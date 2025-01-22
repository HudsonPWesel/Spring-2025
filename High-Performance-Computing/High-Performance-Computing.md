## Lecture 1
**TODOs**
- [ ] First Chapter
- [ ] Write a program to perform matrix multiplication in C/C++
- [ ] Dive into systems

# Dive Into Systems
## 14.1
 A multicore processor increases the **throughput** of process execution, or the number of processes that can complete in a given period of time. Thus, while the CPU time of an individual process remains unchanged, its wall-clock time may decrease.
## 14.1.2. Expediting Process Execution with Threads
![[Pasted image 20250121120027.png]]
All threads share the same program data  but have their own call stack memory and the OS 

1 thead = 1 core. Thus the speed up of the program should be runtime * 1/c (c = number of cores) but this linear speedup is not realistic because of the resource contention between all processes on the system

## 14.1.3 Hello POSIX

Joining a thread that has terminated frees the thread’s execution context and resource 
- attempting to join a thread that _hasn’t_ terminated blocks the caller until the thread terminates, similar to the semantics of the [wait function for processes](https://diveintosystems.org/singlepage/#_exit_and_wait).
-

## 14.1.4 Race Conditions

Given Counting Sort does two things
- Counts the frequences of ints in a range (0-9)
- Uses the frequences to write to an array the should be values
An attempt to paralelize the frequency count. function, thread 0 on core 0 and thread 1 on core 1 have the following times.
*Notice that by the time thread 1 reads counts[1] (it's still the original value) because the new value in the register has not been written to counts[1] yet. Thus this will cause the element to lose a count *

Say counts[i] = 60
i+1 Core 0 register = 61 BUT the value stored in counts[i] = 60, so when thread 1 reads the value, it stores the value 60 into Core1's register. Thus Core1 = Core0 registers are equal, which shoudln't happen the value should be 62, but the acutal value is 61. Note two values can read and it's all good but it's only when we have a read-modify-write that causes problems. This is known as a **data race**  A synchronization construct like mutual exclusion is needed to ensure that there are no data races, when these non-atomic operations are being performed.

![[Pasted image 20250121125549.png]]

**Solution**
![[Pasted image 20250121130107.png]]
In this execution sequence, Thread 1 does not read from `counts[1]` until after Thread 0 updates it with its new value (61). The end result is that Thread 1 reads the value 61 from `counts[1]` and places it into Core 1’s register during time step _i+3_, and writes the value 62 to `counts[1]` in time step _i+5_.

To fix a data race, we must first isolate the **critical section**, or the subset of code that must execute **atomically** (in isolation) blocks of code that update a shared resource are typically identified to be critical sections. Since the fundamental problem in `countElems` is the simultaneous access of `counts` by multiple threads, a mechanism is needed to force the threads to enter the critical section sequentially.

### 14.3.1 Mutual Exclusion
a mutex is a synchronoization rimitive that ensures only one thread executes the critical section at a time (sequentially )
On startup, the critical section is usable by any thread. To enter the critical section a thread must acquire the lock via pthread_mutex_lock. After the thread has the lock, no other thread can have it (enter the critical section) until the previous thread releases it. If another thread tries to get the lock via pthread_mutex_lock, the thread will block (wait) until the thread releases the lock. This is similar to how processes enter the locked state when waiting for an I/O operation, in that they can't be scheduled by the CPU for execution. **When a thread exits the critical section it must call the `pthread_mutex_unlock` function to release the mutex, making it available for another thread.**

It is better to use a system call like `gettimeofday`, which allows a user to accurately measure the wall-clock time of a particular section of code. 

*It's extremely important to understand where you're placing the pthread_mutex_lock function.* If you put it at the top of a for-loop then the entire loop body is blocking which effectively serielizes the program again.

*In addition locking and unlocking a mutex are expensive operations. Recall what was covered in the discussion on [function call optimizations](https://diveintosystems.org/singlepage/#_function_inlining): calling a function repeatedly (and needlessly) in a loop can be a major cause of slowdown in a program*

**to efficiently minimize a critical section, use local variables to collect intermediate values. After the hard work requiring parallelization is over, use a mutex to safely update any shared variable(s).**

**Deadlock**  threads have dependencies on one another ince both threads are blocked on each other, they are in deadlock. Processes can also get deadlocked.
![[Pasted image 20250121132327.png]]