***All Questions have been answered according to their number e.g (5. How many files are extracted from the tar file?) corresponds to step 5 in the setup.***

**Upgrading Ubuntu**
```bash
ubuntu@os:~$ sudo apt update && sudo apt upgrade && sudo apt autoremove
[sudo] password for ubuntu:
Hit:1 http://us.archive.ubuntu.com/ubuntu focal InRelease
Hit:2 http://ppa.launchpad.net/neovim-ppa/stable/ubuntu focal InRelease
Hit:3 http://us.archive.ubuntu.com/ubuntu focal-updates InRelease
Hit:4 http://us.archive.ubuntu.com/ubuntu focal-backports InRelease
Hit:5 http://us.archive.ubuntu.com/ubuntu focal-security InRelease
Reading package lists... Done
Building dependency tree
Reading state information... Done
4 packages can be upgraded. Run 'apt list --upgradable' to see them.
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages were automatically installed and are no longer required:
  libice6 libluajit-5.1-2 libluajit-5.1-common libmsgpackc2 libsm6 libtermkey1 libtree-sitter0 libunibilium4 libvterm0 libxaw7 libxcursor1 libxfixes3 libxft2 libxkbfile1 libxmu6 libxpm4 libxrender1 libxt6 lua-luv neovim-runtime
  python3-greenlet python3-msgpack python3-neovim python3-pynvim x11-common xbitmaps xclip
Use 'sudo apt autoremove' to remove them.
The following packages will be upgraded:
  bind9-dnsutils bind9-host bind9-libs libvterm0
4 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Need to get 1,377 kB of archives.
After this operation, 7,168 B of additional disk space will be used.
Do you want to continue? [Y/n] y
<SNIP>
```

<div class="page-break" style="page-break-before: always;"></div>

**Disable Auto Updates**
```bash
ubuntu@os:~$ cat /etc/apt/apt.conf.d/20auto-upgrades
APT::Periodic::Update-Package-Lists "0";
APT::Periodic::Download-Upgradeable-Packages "0";
APT::Periodic::AutocleanInterval "0";
APT::Periodic::Unattended-Upgrade "0";
```
**Kernel Version**
```bash
ubuntu@os:~$ uname -r
5.4.0-204-generic
```

**gcc Version**
```bash
ubuntu@os:~$ gcc --version
gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

**Highest linux-5.#.# Version**
![[Pasted image 20250110190516.png]]

**1. Change Directory**
```bash
root@os:/home/ubuntu# cd /usr/src/
```
<div class="page-break" style="page-break-before: always;"></div>

**2. Download Kernel**
```bash
root@os:/usr/src# wget https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/linux-5.19.9.tar.xz
--2025-01-11 00:09:54--  https://mirrors.edge.kernel.org/pub/linux/kernel/v5.x/linux-5.19.9.tar.xz
Resolving mirrors.edge.kernel.org (mirrors.edge.kernel.org)... 147.75.199.223, 2604:1380:45d1:ec00::1
Connecting to mirrors.edge.kernel.org (mirrors.edge.kernel.org)|147.75.199.223|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 131654068 (126M) [application/x-xz]
Saving to: ‘linux-5.19.9.tar.xz’

linux-5.19.9.tar.xz                       100%[====================================================================================>] 125.55M  18.6MB/s    in 10s

2025-01-11 00:10:04 (12.2 MB/s) - ‘linux-5.19.9.tar.xz’ saved [131654068/131654068]

```
**3. Extract Files**
```bash
root@os:/usr/src# tar xvf linux-5.19.9.tar.xz
<SNIP>
```

**4. Confirm New Directory in /usr/src**
```bash
root@os:/home/ubuntu# ls /usr/src/
linux-5.19.9  linux-5.19.9.tar.xz  linux-headers-5.4.0-204  linux-headers-5.4.0-204-generic
```

**5. How Many Files are Extracted from the tar file?**
```bash
root@os:/usr/src/linux-5.19.9# find . -type f | wc -l
76922
```
<div class="page-break" style="page-break-before: always;"></div>
 
 **6. How much disk space is used for kernel source code?**
 ```bash
root@os:/usr/src/linux-5.19.9# du -sh .
1.4G    .
```

**Install Make **
```bash
root@os:/usr/src/linux-5.19.9# apt install make
Reading package lists... Done
Building dependency tree
Reading state information... Done
Suggested packages:
  make-doc
The following NEW packages will be installed:
  make
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 162 kB of archives.
After this operation, 393 kB of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu focal/main amd64 make amd64 4.2.1-1.2 [162 kB]
Fetched 162 kB in 0s (596 kB/s)
Selecting previously unselected package make.
(Reading database ... 78249 files and directories currently installed.)
Preparing to unpack .../make_4.2.1-1.2_amd64.deb ...
Unpacking make (4.2.1-1.2) ...
Setting up make (4.2.1-1.2) ...
Processing triggers for man-db (2.9.1-1) ...
```
<div class="page-break" style="page-break-before: always;"></div>

**install ncurses**
```bash
root@os:/usr/src/linux-5.19.9# sudo apt install libncurses-dev
Reading package lists... Done
Building dependency tree
Reading state information... Done
Suggested packages:
  ncurses-doc
The following NEW packages will be installed:
  libncurses-dev
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 340 kB of archives.
After this operation, 2,398 kB of additional disk space will be used.
Get:1 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 libncurses-dev amd64 6.2-0ubuntu2.1 [340 kB]
Fetched 340 kB in 0s (1,117 kB/s)
Selecting previously unselected package libncurses-dev:amd64.
(Reading database ... 78265 files and directories currently installed.)
Preparing to unpack .../libncurses-dev_6.2-0ubuntu2.1_amd64.deb ...
Unpacking libncurses-dev:amd64 (6.2-0ubuntu2.1) ...
Setting up libncurses-dev:amd64 (6.2-0ubuntu2.1) ...
Processing triggers for man-db (2.9.1-1) ...

```

**Confirm GCC Install**
```bash
root@os:/usr/src/linux-5.19.9# apt list | grep "^gcc" | grep installed

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

gcc-10-base/focal-updates,focal-security,now 10.5.0-1ubuntu1~20.04 amd64 [installed,automatic]
gcc-9-base/focal-updates,focal-security,now 9.4.0-1ubuntu1~20.04.2 amd64 [installed,automatic]
gcc-9/focal-updates,focal-security,now 9.4.0-1ubuntu1~20.04.2 amd64 [installed,automatic]
gcc/focal,now 4:9.3.0-1ubuntu2 amd64 [installed]

```
<div class="page-break" style="page-break-before: always;"></div>

**Install GCC Modules**
```bash
root@os:/usr/src/linux-5.19.9# sudo apt install libelf-dev libssl-dev flex bison
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libfl-dev m4 zlib1g-dev
Suggested packages:
  bison-doc build-essential flex-doc libssl-doc m4-doc
The following NEW packages will be installed:
  bison flex libelf-dev libfl-dev libssl-dev m4 zlib1g-dev
0 upgraded, 7 newly installed, 0 to remove and 0 not upgraded.
Need to get 2,977 kB of archives.
After this operation, 12.4 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://us.archive.ubuntu.com/ubuntu focal/main amd64 m4 amd64 1.4.18-4 [199 kB]
Get:2 http://us.archive.ubuntu.com/ubuntu focal/main amd64 flex amd64 2.6.4-6.2 [317 kB]
<SNIP>
root@os:/usr/src/linux-5.19.9#

```

**8. Copy Config File**
```bash
root@os:/usr/src/linux-5.19.9# cp /boot/config-5.4.0-204-generic .config
```

**Confirm Config File Exists**
```bash
root@os:/usr/src/linux-5.19.9# ls /usr/src/linux-5.19.9/.config
/usr/src/linux-5.19.9/.config
```
<div class="page-break" style="page-break-before: always;"></div>

**9. Make menuconfig**
![[Pasted image 20250110193155.png]]

**10. Kernel Name**
![[Pasted image 20250110193258.png]]

**Confirm**
![[Pasted image 20250110193441.png]]

**11. Disable Kernel Debugging**
![[Pasted image 20250110193641.png]]

**12. Save Kernel Config**
![[Pasted image 20250110193757.png]]
```bash
root@os:/usr/src/linux-5.19.9# make menuconfig
<SNIP>
configuration written to .config

*** End of the configuration.
*** Execute 'make' to start the build or try 'make help'.
```

**13. Confirm Config Update**
```bash
root@os:/usr/src/linux-5.19.9# cat .config | grep CSC4210
CONFIG_LOCALVERSION="CSC4210"
```

<div class="page-break" style="page-break-before: always;"></div>

**14. make localmodconfig**
```bash
root@os:/usr/src/linux-5.19.9# make localmodconfig
  HOSTCC  scripts/kconfig/conf.o
  HOSTLD  scripts/kconfig/conf
using config: '.config'
glue_helper config not found!!
bochs_drm config not found!!
System keyring enabled but keys "debian/canonical-certs.pem" not found. Resetting keys to default value.
*
* Restart config...
*
*
* PCI GPIO expanders
*
AMD 8111 GPIO driver (GPIO_AMD8111) [N/m/y/?] n
BT8XX GPIO abuser (GPIO_BT8XX) [N/m/y/?] (NEW) y
OKI SEMICONDUCTOR ML7213 IOH GPIO support (GPIO_ML_IOH) [N/m/y/?] n
ACCES PCI-IDIO-16 GPIO support (GPIO_PCI_IDIO_16) [N/m/y/?] n
ACCES PCIe-IDIO-24 GPIO support (GPIO_PCIE_IDIO_24) [N/m/y/?] n
RDC R-321x GPIO support (GPIO_RDC321X) [N/m/y/?] n
#
# configuration written to .config
#

```

<div class="page-break" style="page-break-before: always;"></div>

**Confirm 4 Processors**
```bash
root@os:/usr/src/linux-5.19.9# cat /proc/cpuinfo | grep process
processor       : 0
processor       : 1
processor       : 2
processor       : 3

```

**Compile (Cert Error)**
```bash
root@os:/usr/src/linux-5.19.9# make -j4 bzImage
  <SNIP>
  HOSTCC  certs/extract-cert
  COPY    certs/x509.genkey
make[1]: *** No rule to make target 'debian/canonical-revoked-certs.pem', needed by 'certs/x509_revocation_list'.  Stop.
make[1]: *** Waiting for unfinished jobs....
  CC      certs/blacklist.o
  CC      arch/x86/entry/syscall_64.o
  CC      arch/x86/entry/common.o
make: *** [Makefile:1846: certs] Error 2
make: *** Waiting for unfinished jobs....
<SNIP>
```

**Confirm edit of .config**
```bash
root@os:/usr/src/linux-5.19.9# cat .config | grep CONFIG_SYSTEM_REVOCATION_KEYS
CONFIG_SYSTEM_REVOCATION_KEYS=""
```

<div class="page-break" style="page-break-before: always;"></div>

**15. make -j4 bzImage**
```bash
root@os:/usr/src/linux-5.19.9# make -j4 bzImage
  SYNC    include/config/auto.conf.cmd
  DESCEND objtool
  CALL    scripts/atomic/check-atomics.sh
  CALL    scripts/checksyscalls.sh
  CHK     include/generated/compile.h
  CERT    certs/x509_certificate_list
  GENKEY  certs/signing_key.pem
Generating a RSA private key
..............................................................................  CC      mm/filemap.o
++++
.....................................................................++++
writing new private key to 'certs/signing_key.pem'

<SNIP>

Kernel: arch/x86/boot/bzImage is ready  (#1)

```

**16. Make Modules**
```bash
root@os:/usr/src/linux-5.19.9# make modules
  CALL    scripts/checksyscalls.sh
  CALL    scripts/atomic/check-atomics.sh
  DESCEND objtool
  CHK     include/generated/compile.h
```

<div class="page-break" style="page-break-before: always;"></div>

**17. make modules_install**
```bash
root@os:/usr/src/linux-5.19.9# make modules_install
  <SNIP>
  DEPMOD  /lib/modules/5.19.9CSC4210

```
**Confirm Install**
```bash
root@os:/usr/src/linux-5.19.9# ls /lib/modules/5.19.9CSC4210/
build   modules.alias      modules.builtin            modules.builtin.bin      modules.dep      modules.devname  modules.softdep  modules.symbols.bin
kernel  modules.alias.bin  modules.builtin.alias.bin  modules.builtin.modinfo  modules.dep.bin  modules.order    modules.symbols  source

```

<div class="page-break" style="page-break-before: always;"></div>

**18. Make Install**
```bash
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

```

<div class="page-break" style="page-break-before: always;"></div>

**19. Confirm Install**
```bash
root@os:/usr/src/linux-5.19.9# ls -l /boot | grep 'config-\|vmlinuz\|System.map\|initrd'
-rw-r--r-- 1 root root   161612 Jan 11 01:12 config-5.19.9CSC4210
-rw-r--r-- 1 root root   161612 Jan 11 01:07 config-5.19.9CSC4210.old
-rw-r--r-- 1 root root   237778 Dec  5 11:35 config-5.4.0-204-generic
lrwxrwxrwx 1 root root       24 Jan 11 01:07 initrd.img -> initrd.img-5.19.9CSC4210
-rw-r--r-- 1 root root 26611728 Jan 11 01:12 initrd.img-5.19.9CSC4210
-rw-r--r-- 1 root root 87831392 Jan  2 19:59 initrd.img-5.4.0-204-generic
-rw-r--r-- 1 root root  5774317 Jan 11 01:12 System.map-5.19.9CSC4210
-rw-r--r-- 1 root root  5774317 Jan 11 01:07 System.map-5.19.9CSC4210.old
-rw------- 1 root root  4762473 Dec  5 11:35 System.map-5.4.0-204-generic
lrwxrwxrwx 1 root root       21 Jan 11 01:12 vmlinuz -> vmlinuz-5.19.9CSC4210
-rw-r--r-- 1 root root 13342624 Jan 11 01:12 vmlinuz-5.19.9CSC4210
-rw-r--r-- 1 root root 13342624 Jan 11 01:07 vmlinuz-5.19.9CSC4210.old
-rw------- 1 root root 13705992 Dec  5 11:37 vmlinuz-5.4.0-204-generic
lrwxrwxrwx 1 root root       25 Jan 11 01:12 vmlinuz.old -> vmlinuz-5.19.9CSC4210.old

```

**Backup Grub Conf File**
```bash
root@os:/usr/src/linux-5.19.9# cp /etc/default/grub ./grub-bak
```

**Edit grub config**
```bash
root@os:/usr/src/linux-5.19.9# cat /etc/default/grub | grep GRUB_DEFAULT
GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 5.4.0-204-generic"
```
<div class="page-break" style="page-break-before: always;"></div>

**20. update-grub**
```bash
root@os:/usr/src/linux-5.19.9# update-grub
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

```

**Reboot**
```bash
root@os:/usr/src/linux-5.19.9# reboot
```

**21. Confirm Version**
```bash
ubuntu@os:~$ cat /proc/version
Linux version 5.4.0-204-generic (buildd@lcy02-amd64-079) (gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.2)) #224-Ubuntu SMP Thu Dec 5 13:38:28 UTC 2024
```
**grub-reboot**
```bash
root@os:/home/ubuntu# grub-reboot "Advanced options for Ubuntu>Ubuntu, with Linux 5.19.9CSC4210"
```
**22. reboot**
```bash
root@os:/home/ubuntu# reboot
```
<div class="page-break" style="page-break-before: always;"></div>

**Confirm Kernel Version **
```bash
ubuntu@os:~$ cat /proc/version
Linux version 5.19.9CSC4210 (root@os) (gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #2 SMP PREEMPT_DYNAMIC Sat Jan 11 01:00:45 UTC 2025

```

**23. Update Grub Default**
```bash
root@os:/home/ubuntu# cat /etc/default/grub | grep GRUB_DEFAULT
GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 5.19.9CSC4210"

```
**Confirm Update**
```bash
root@os:/home/ubuntu# update-grub
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
```
**reboot**
```bash
root@os:/home/ubuntu# reboot

```

<div class="page-break" style="page-break-before: always;"></div>

**Test New Config**
```bash
ubuntu@os:~$ cat /proc/version
Linux version 5.19.9CSC4210 (root@os) (gcc (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0, GNU ld (GNU Binutils for Ubuntu) 2.34) #2 SMP PREEMPT_DYNAMIC Sat Jan 11 01:00:45 UTC 2025
```