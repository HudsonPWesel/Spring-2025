Problems 1-8
2.1 What is the purpose of system calls?
2.2 What is the purpose of the command interpreter? Why is it usually
separate from the kernel?
2.3 What system calls have to be executed by a command interpreter or shell
in order to start a new process on a UNIX system?
2.4 What is the purpose of system programs?
2.5 What is the main advantage of the layered approach to system design?
What are the disadvantages of the layered approach?
2.6 List five services provided by an operating system, and explain how each
creates convenience for users. In which cases would it be impossible for
user-level programs to provide these services? Explain your answer.
2.7 Why do some systems store the operating system in firmware, while
others store it on disk?
2.8 How could a system be designed to allow a choice of operating systems
from which to boot?What would the bootstrap program need to do?

10-13, 15, 16, 20

10 Describe three general methods for passing parameters to the operating system.
- Stack 
- Registers _however, there may be more parameters than registers_
-  Directly in memory & address of the block is passed as a parameter in a register
2.11 Describe how you could obtain a statistical profile of the amount of time a program spends executing different sections of its code. Discuss the
importance of obtaining such a statistical profile.
- gettimeofday()
2.12 What are the advantages and disadvantages of using the same systemcall interface for manipulating both files and devices?

2.13 Would it be possible for the user to develop a new command interpreter using the system-call interface provided by the operating system?
Yes
2.15 What are the two models of interprocess communication? What are the strengths and weaknesses of the two approaches?
shared memory, message passing, signals
2.16 Contrast and compare an application programming interface (API) and
an application binary interface (ABI).
2.20 What are the advantages of using loadable kernel modules?