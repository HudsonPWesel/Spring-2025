 A regex is a sequence of characters that specifies a match pattern in text
## Questions
1. What is the proxmox identifier name of your machine? (answer on last page)  
 ![[Pasted image 20250109113929.png]]
2. **What is the IP address of your machine?** 
```bash
ubuntu@os:~$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens18: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether bc:24:11:7b:b3:78 brd ff:ff:ff:ff:ff:ff
    inet 10.16.103.150/24 metric 100 brd 10.16.103.255 scope global dynamic ens18
       valid_lft 1126190sec preferred_lft 1126190sec
    inet6 fe80::be24:11ff:fe7b:b378/64 scope link
       valid_lft forever preferred_lft forever

```

3. What version of the kernel are you running?
**OS Version (5.4.0)**
```bash 
ubuntu@os:/proc$ cat /proc/version
Linux version 5.4.0-204-generic (buildd@lcy02-amd64-079) (gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.2)) #224-Ubuntu SMP Thu Dec 5 13:38:28 UTC 2024
ubuntu@os:~$ uname -a
Linux os 5.4.0-204-generic #224-Ubuntu SMP Thu Dec 5 13:38:28 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux

```
4. State the names of the top level directories and files that are related to the current running kernel found in these  
**Kernel File Paths**
```bash
ubuntu@os:~$ cat kernelfiles
 /boot
 /usr/src/
 /usr/lib/modules
```

**Relevant Files**
```bash
ubuntu@os:~$ while IFS= read -r filepath; do echo $filepath; ls $filepath  | grep 5.4.0; echo  ; done < kernelfiles
/boot
config-5.4.0-204-generic
initrd.img-5.4.0-204-generic
System.map-5.4.0-204-generic
vmlinuz-5.4.0-204-generic

/usr/src/
linux-headers-5.4.0-204
linux-headers-5.4.0-204-generic

/usr/lib/modules
5.4.0-204-generic
```
5. What is the version of your linux kernel running (include all numbers)? (answer on last page)
```bash
ubuntu@os:~$ uname -a
Linux os 5.4.0-204-generic #224-Ubuntu SMP Thu Dec 5 13:38:28 UTC 2024 x86_64 x86_64 x86_64 GNU/Linux
```

6. After installing gcc and g++ compilers, write a “Hello World” program in C and C++ on your Proxmox   machine. Once you have them written and they compile, provide a screen shot of a terminal showing that you  can compile and run the hello world programs written in C and C++.
![[Pasted image 20250109113734.png]]