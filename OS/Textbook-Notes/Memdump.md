Syscalls 
- Communication
	- socket
	- signal
- Process Management
	- fork
	- exec
	- wait
	- exit
- Information Maintenance
	- dump
	- time
	- date
- File Management, Device Management (Really similar so we use similar systcalls)
	- Open | Request
	- Read
	- Write
	- move | Reposition
	- Close | release
- Protection
	- Deny_user()
	- allow_user()

Syscalls are leveraged via an API, which implements each syscall

RTE allows for program execution (Compilers, Linkers, Loaded, Libraries, Syscall-Call-Interface (link between user program and OS to fufill syscall))

**System Services** (Tools/services for program development)
- File modification and creation
	- VI Editor 
	- FS
- Status info
	- Disk usage
	- CPU Execution Time
- Programming langauage support
	- Compilers
	- Linkers
	- Interpreteries
	- *Assemblers*
- Executing Programs
	- Loaders 
- Communication
	- Browsers 
	- ssh
	- mail clients
- Daemons (Background services/processes)
	- Process schedulers 
	- Error monitoring 
Why programs are OS specific 
- Syscall implementation (which also depend on the various services the OS provides)
*Workarounds*
- Interpreter (Python)
	- Reads each line and translates (eventually) to proper instructions on the native instruction set
- VM 
	- JVM 
- Global API ubiquitous across on type of OS
	- Win32 API
	- UNIX POSIX 
Design an OS  (How to layer it)
- Monolthic Structure (tightly-coupled system) (Kernel is compiled into a single exectuable and runs in a single VA space)
	- Leverages modules to help modularize this 
- Other implementations 
	- Strict layered (each layer has a specific function)
		- Network Protocols (TCP/IP) but not OSs
		- *Problems*
			- Latency 
			- Hard to define what each layer does
	- Microkernel (Everything that's not an essential kernel function is a user program (file hdnling, device drivers) and runs in their own user-level process)
		- Darwin (macOS & iOS)
		- RTOSs, QNX (Embedded Systems)
		- *Problems* 
			- Because each process is running separately, the process makes a copy of its data that it wants to send to another process (message copying) and then asks the kernel to copy the message into a buffer, context switch into process B, and paste it into process B's memory
Mechanism -> How
Policy -> What
C -> assembly -> assembled -> linked -> executable -> loaded 
*Separated so one doesn't impact the other*
- User goals -> I want a GUI, tools, etc.
- System Goals -> I want the system to be easily extendible, maintained, etc.

Linux is 100% Monolithic structure HOWEVER, it has modules which allows the kernel to be modified @ runtime 
- These modules can be linked in dynamically as runtime 
*Booting*
- UEFI -- Finds boot block from bootable storages -> Initial Grub (full GRUB can't fit in one block size)  --> Loads Grub -- Loads initrd & Kernel (Kernel needs drivers to access root FS and drivers are in root FS) to get everything for kernel loading) -->  Kernel Loads Itself using initrd
*Failure Analysis*
- If a process fails the OS will take a **core dump** which is a capture/snapshot of memoery of th process and stores it in a file (memory was referred to as the "core" in early days), which can be opened in a debugger.
- A failure in the kernel is a **crash** and error info (memory snapshot) is saved to a crash dump log file
*Tracing*

BCC (BPF Compiler Collection) set of tools for tracing various aspects about the OS without interrupting it (verified the through a **verifier** before being inserted into the running kernel) which is compiled in to eBPF instructions and inserts them it into the kernel using probes or tracepoints

you can write your own custom BCC tool in Python or C
![[Pasted image 20250130091322.png]]