

1. Who is the owner of these two files (syslog)
```bash
ubuntu@os:~$ ls -l /var/log/syslog /var/log/kern.log
-rw-r----- 1 syslog adm    0 Jan 12 00:00 /var/log/kern.log
-rw-r----- 1 syslog adm 8761 Jan 16 15:04 /var/log/syslog

```
**Viewing Syslog User**
```bash
ubuntu@os:~$ cat /etc/passwd | grep syslog
syslog:x:104:110::/home/syslog:/usr/sbin/nologin

```
2. What is the group assignment of these two files? (adm)
```bash
ubuntu@os:~$ ls -l /var/log/syslog /var/log/kern.log
-rw-r----- 1 syslog adm    0 Jan 12 00:00 /var/log/kern.log
-rw-r----- 1 syslog adm 8761 Jan 16 15:04 /var/log/syslog

```
3. Try to access these files. 
   *I can read these files because the group owner is adm and I am a member of that group*
```bash
ubuntu@os:~$ id
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),117(lxd)

ubuntu@os:~$ cat /var/log/syslog /var/log/kern.log
Jan 16 00:00:10 os systemd[1]: logrotate.service: Succeeded.
Jan 16 00:00:10 os systemd[1]: Finished Rotate log files.
<SNIP>
```
a. Can ubuntu (as a normal user) use the **tail** command to display the last few lines of these files? 
Yes. (see above)
b. Log into spock, repeat 3 again. Can you **cd** into /var/log or **tail** /var/log/syslog or kern.log? 
No
c. Can you explain the difference? HINT: Use the **id** command as ubuntu and when you log into spock.
I'm not a member of the adm group on spock

4. What is the include file needed for the **read** and **write** system call? _______________________.
```bash
unistd.h
```

5. Assuming that you have relocated to the /usr/include directory. What **find/grep** command combo would locate the unistd_32.h file inside the /usr/include directory? Use your **find/grep** command to find the full path to the unistd_32.h file, what is the path?
	**/usr/include/x86_64-linux-gnu/asm/unistd_32.h**
```bash
ubuntu@os:/usr/include$ find . -print | grep -e 'unistd_32.h\|unistd_64.h'
./x86_64-linux-gnu/asm/unistd_32.h
./x86_64-linux-gnu/asm/unistd_64.h
```

6. Once you locate unistd_32.h and unistd_64.h, using an editor (vi) to look through the file to identify the number associated with the following system calls in 32 and 64 bit linux. 

```bash
ubuntu@os:/usr/include$ cat ./x86_64-linux-gnu/asm/unistd_32.h | grep -e ' __NR_read \| __NR_write \|__NR_open \|__NR_close \|__NR_mkdir \|__NR_fork \|__NR_exit \|__NR_dup \|__NR_pipe '
#define __NR_exit 1
#define __NR_fork 2
#define __NR_read 3
#define __NR_write 4
#define __NR_open 5
#define __NR_close 6
#define __NR_mkdir 39
#define __NR_dup 41
#define __NR_pipe 42

ubuntu@os:/usr/include$ cat ./x86_64-linux-gnu/asm/unistd_64.h | grep -e ' __NR_read \| __NR_write \|__NR_open \|__NR_close \|__NR_mkdir \|__NR_fork \|__NR_exit \|__NR_dup \|__NR_pipe '
#define __NR_read 0
#define __NR_write 1
#define __NR_open 2
#define __NR_close 3
#define __NR_pipe 22
#define __NR_dup 32
#define __NR_fork 57
#define __NR_exit 60
#define __NR_mkdir 83

```
7. Check the man pages on write (remember, man write is not the system call write, use man 2 write to get the correct man page). Using the 64-bit version, **what** are the arguments (parameters) for the write system call and **what** registers should the arguments be placed?
```bash
ssize_t write(int fd, const void *buf, size_t count);
```

8/9. I’ll do a **visual check** on the two programs on your machine, during the class, on the due date of assignment.

___ 10. From the base directory, search through the files in the **arch** subdirectory for the file named syscall_64.tbl. That should get the directory (location) of the syscall_64.tbl. What find command will help you find the file? What is the directory?
**Directory: syscalls**
```bash

ubuntu@os:/usr/src/linux-5.19.9$ find arch/ -print | grep syscall_64.tbl
arch/x86/entry/syscalls/syscall_64.tbl

```
11. What is the first number that follows the last 400 number in the file? ___________
512

 12. From the base directory remember the base directory is as noted at the top of this page,
```bash
root@os:/usr/src/linux-5.19.9# grep helloworld arch/x86/entry/syscalls/syscall_64.tbl
469     64      helloworld              sys_helloworld

```

**edit syscalls.h**
```bash
root@os:/usr/src/linux-5.19.9/include/linux# tail -n2 /usr/src/linux-5.19.9/include/linux/syscalls.h
asmlinkage long sys_helloworld(void);
#endif
```
 13. Provide Syscall code From the base directory
 ```bash
 root@os:/usr/src/linux-5.19.9/include/linux# cat helloworld.c
#include <linux/linkage.h>
#include <linux/kernel.h>

asmlinkage long __x64_sys_helloworld(void)
{
        printk(KERN_INFO "Hello World!\n");
        return 1;
}
```

cd kernel

edit the Makefile
```bash
root@os:/usr/src/linux-5.19.9/kernel# head -n15 Makefile
# SPDX-License-Identifier: GPL-2.0
#
# Makefile for the linux kernel.
#

obj-y     = fork.o exec_domain.o panic.o \
            cpu.o exit.o softirq.o resource.o \
            sysctl.o capability.o ptrace.o user.o \
            signal.o sys.o umh.o workqueue.o pid.o task_work.o \
            extable.o params.o platform-feature.o \
            kthread.o sys_ni.o nsproxy.o \
            notifier.o ksysfs.o cred.o reboot.o \
            async.o range.o smpboot.o ucount.o regset.o \
            helloworld.o

```

___ 14. From the base directory, 

```bash
root@os:/usr/src/linux-5.19.9# make -j4 bzImage
  DESCEND objtool
  <SNIP>
  CC      kernel/helloworld.o
  <SNIP>
Kernel: arch/x86/boot/bzImage is ready  (#3)
root@os:/usr/src/linux-5.19.9# make install
  INSTALL /boot
run-parts: executing /etc/kernel/postinst.d/initramfs-tools 5.19.9CSC4210 /boot/vmlinuz-5.19.9CSC4210
update-initramfs: Generating /boot/initrd.img-5.19.9CSC4210
run-parts: executing /etc/kernel/postinst.d/unattended-upgrades 5.19.9CSC4210 /boot/vmlinuz-5.19.9CSC4210
run-parts: executing /etc/kernel/postinst.d/update-notifier 5.19.9CSC4210 /boot/vmlinuz-5.19.9CSC4210
run-parts: executing /etc/kernel/postinst.d/xx-update-initrd-links 5.19.9CSC4210 /boot/vmlinuz-5.19.9CSC4210
run-parts: executing /etc/kernel/postinst.d/zz-update-grub 5.19.9CSC4210 /boot/vmlinuz-5.19.9CSC4210
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/init-select.cfg'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.19.9CSC4210
Found initrd image: /boot/initrd.img-5.19.9CSC4210
Found linux image: /boot/vmlinuz-5.19.9CSC4210.old
Found initrd image: /boot/initrd.img-5.19.9CSC4210
Found linux image: /boot/vmlinuz-5.4.0-204-generic
Found initrd image: /boot/initrd.img-5.4.0-204-generic
done
root@os:/usr/src/linux-5.19.9#
root@os:/usr/src/linux-5.19.9# ls /boot
config-5.19.9CSC4210      config-5.4.0-204-generic  initrd.img                initrd.img-5.4.0-204-generic  System.map-5.19.9CSC4210      System.map-5.4.0-204-generic  vmlinuz-5.19.9CSC4210      vmlinuz-5.4.0-204-generic
config-5.19.9CSC4210.old  grub                      initrd.img-5.19.9CSC4210  lost+found                    System.map-5.19.9CSC4210.old  vmlinuz                       vmlinuz-5.19.9CSC4210.old  vmlinuz.old
root@os:/usr/src/linux-5.19.9# grep -Ei 'submenu|menuentry ' /boot/grub/grub.cfg | sed -re "s/(.? )'([^']+)'.*/\1 \2/"
menuentry  Ubuntu
submenu  Advanced options for Ubuntu
        menuentry  Ubuntu, with Linux 5.19.9CSC4210
        menuentry  Ubuntu, with Linux 5.19.9CSC4210 (recovery mode)
        menuentry  Ubuntu, with Linux 5.19.9CSC4210.old
        menuentry  Ubuntu, with Linux 5.19.9CSC4210.old (recovery mode)
        menuentry  Ubuntu, with Linux 5.4.0-204-generic
        menuentry  Ubuntu, with Linux 5.4.0-204-generic (recovery mode)
root@os:/usr/src/linux-5.19.9# update-grub "Advanced options for Ubuntu>Ubuntu, with Linux 5.19.9CSC4210"
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/init-select.cfg'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.19.9CSC4210
Found initrd image: /boot/initrd.img-5.19.9CSC4210
Found linux image: /boot/vmlinuz-5.19.9CSC4210.old
Found initrd image: /boot/initrd.img-5.19.9CSC4210
Found linux image: /boot/vmlinuz-5.4.0-204-generic
Found initrd image: /boot/initrd.img-5.4.0-204-generic
done
root@os:/usr/src/linux-5.19.9# reboot
Connection to osubuntu closed by remote host.
Connection to osubuntu closed.

```


__ 15. Create the system call test program as a normal user.
```bash
ubuntu@os:~$ cat hello_SCT.c
#include<stdio.h>
#include<stdlib.h>
#include<error.h>
#include<unistd.h>
#include<sys/syscall.h>
#include<linux/unistd.h>

int main(void)
{
        syscall(469);
        return 0;
}

```
16. Run the code. What should happen? Look back at code for the system call, Any output to screen? Did it run?
No output to scrren, but that's okay as we used **printk**
```bash
ubuntu@os:~$ ./helloworld-syscall
```

18. Where should I look to find out if the system call did it's job? (/var/log/syslog)
```bash
ubuntu@os:~$ ./helloworld-syscall
ubuntu@os:~$ tail /var/log/syslog
<SNIP>
Jan 16 17:22:05 os kernel: [  356.245648] Hello World!
```