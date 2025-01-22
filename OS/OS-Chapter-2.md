## 2.3.2
![[Pasted image 20250121102845.png]]
**In addition to syscalls is the program's run time environment and it features a syscall-interface to intercept **

![[Pasted image 20250121103027.png]]

Depending on the syscall there may need to be additional params. There are three ways the OS might do this (they're all implemented)
- pass the parameters in registers (what if there are more params than are registers?) (only used when there are 5 or less params)
- Given the above problem, we may store params in stored in a block, or table, in memory, and the address of the block is passed as a parameter in a register
-  Params are stored on the stack
![[Pasted image 20250121103852.png]]

**• Process control**
	◦ create process, terminate process
	◦ load, execute
	◦ get process attributes, set process attributes
	◦ wait event, signal event
	◦ allocate and free memory
**• File management**
	◦ create file, delete file
	◦ open, close
	◦ read, write, reposition
	◦ get file attributes, set file attributes
**• Device management**
	◦ request device, release device
	◦ read, write, reposition
	◦ get device attributes, set device attributes
	◦ logically attach or detach devices
**• Information maintenance**
	◦ get time or date, set time or date
	◦ get system data, set system data
	◦ get process, file, or device attributes
	◦ set process, file, or device attributes
**• Communications**
	◦ create, delete communication connection
	◦ send, receive messages
	◦ transfer status information
	◦ attach or detach
![[Pasted image 20250121104401.png]]

**If two processes want to share data the OS will lock it to ensure no other process can access the data**

Aurdino is a single-tasking system where only one sketch (pre-compiled program) can be and loaded onto it at one time via the bootloader.

## 2.4 _System Services

Linker -> After C files are compiled into O files, the linker links those object files, with C library files to create on binary executable. After everything is linked the loader loads the object into memory
DLLs Instead, the library is conditionally linked and is loaded if it is required during program run time.

get/set_num_threads()
get_thread_num()