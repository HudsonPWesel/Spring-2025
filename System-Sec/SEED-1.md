## 2. 1 Task 1: Manipulating Environment Variables

**Printing ENV**
```
[01/21/25]seed@VM:~$ export MYVAR=var
[01/21/25]seed@VM:~$ env
SHELL=/bin/bash
MYVAR=var
PWD=/home/seed
LOGNAME=seed
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/seed
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
LC_TERMINAL=iTerm2
SSH_CONNECTION=192.168.254.4 62513 10.16.103.201 22
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=screen-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
LC_TERMINAL_VERSION=3.5.11
SHLVL=1
XDG_SESSION_ID=635
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=192.168.254.4 62513 22
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
SSH_TTY=/dev/pts/2
_=/usr/bin/env
```

<div class="page-break" style="page-break-before: always;"></div>

**Export & Unset**
```bash
[01/15/25]seed@VM:~/Labsetup$ export MAYVAR=32
[01/15/25]seed@VM:~/Labsetup$ echo $MAYVAR
32
[01/15/25]seed@VM:~/Labsetup$ unset MAYVAR
[01/15/25]seed@VM:~/Labsetup$ echo $MAYVAR
```

<div class="page-break" style="page-break-before: always;"></div>

**Modified Printenv**
```bash
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

extern char **environ;

void printenv()
{
  int i = 0;
  while (environ[i] != NULL) {
     printf("%s\n", environ[i]);
     i++;
  }
}

void main()
{
  pid_t childPid;
  switch(childPid = fork()) {
    case 0:  /* child process */
      //printenv();
      exit(0);
    default:  /* parent process */
       printenv();
      exit(0);
  }
}
```

<div class="page-break" style="page-break-before: always;"></div>

**Diff (No difference)**
```bash
[01/15/25]seed@VM:~/Labsetup$ diff file file2
```
### 2.3
**Modified myenv.c**
```bash
#include <unistd.h>

extern char **environ;

int main()
{
  char *argv[2];

  argv[0] = "/usr/bin/env";
  argv[1] = NULL;

  execve("/usr/bin/env", argv, environ);

  return 0 ;
}
```

<div class="page-break" style="page-break-before: always;"></div>

**Myenv.c** The program gets its env variables from the current process
```bash
[01/15/25]seed@VM:~/Labsetup$ gcc myenv.c -o myenv; ./myenv
[01/15/25]seed@VM:~/Labsetup$ vim myenv.c
[01/15/25]seed@VM:~/Labsetup$ gcc myenv.c -o myenv; ./myenv
SHELL=/bin/bash<div class="page-break" style="page-break-before: always;"></div>

PWD=/home/seed/Labsetup
LOGNAME=seed
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/seed
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
LC_TERMINAL=iTerm2
SSH_CONNECTION=192.168.254.2 58125 10.16.103.201 22
LESSCLOSE=/usr/bin/lesspipe %s %s
XDG_SESSION_CLASS=user
TERM=screen-256color
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
LC_TERMINAL_VERSION=3.5.11
SHLVL=1
XDG_SESSION_ID=352
XDG_RUNTIME_DIR=/run/user/1000
SSH_CLIENT=192.168.254.2 58125 22
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
SSH_TTY=/dev/pts/1
OLDPWD=/home/seed
_=./myenv

```


<div class="page-break" style="page-break-before: always;"></div>

## 2.4

```bash
[01/15/25]seed@VM:~/Labsetup$ cat system.c
#include <stdio.h>
#include <stdlib.h>
int main()
{
        system("/usr/bin/env");
        return 0 ;
}

[01/15/25]seed@VM:~/Labsetup$ gcc -o system system.c; ./system
LESSOPEN=| /usr/bin/lesspipe %s
USER=seed
SSH_CLIENT=192.168.254.2 58125 22
XDG_SESSION_TYPE=tty
SHLVL=1
MOTD_SHOWN=pam
HOME=/home/seed
OLDPWD=/home/seed
SSH_TTY=/dev/pts/1
DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus
LC_TERMINAL_VERSION=3.5.11
LOGNAME=seed
_=./system
XDG_SESSION_CLASS=user
TERM=screen-256color
XDG_SESSION_ID=352
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:.
XDG_RUNTIME_DIR=/run/user/1000
LANG=en_US.UTF-8
LS_COLORS=rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:
SHELL=/bin/bash
LESSCLOSE=/usr/bin/lesspipe %s %s
LC_TERMINAL=iTerm2
PWD=/home/seed/Labsetup
SSH_CONNECTION=192.168.254.2 58125 10.16.103.201 22
XDG_DATA_DIRS=/usr/local/share:/usr/share:/var/lib/snapd/desktop

```


<div class="page-break" style="page-break-before: always;"></div>

### 2.5
**SUID** ( the env variabled I defined previously are not there becuase the program is running as root)
```bash
[01/15/25]seed@VM:~/Labsetup$ sudo chown root myprintenv
[01/15/25]seed@VM:~/Labsetup$ sudo chmod 4755 myprintenv
[01/21/25]seed@VM:~$ export NEW_NAME="Hudson"
[01/15/25]seed@VM:~/Labsetup$ ./myprintenv | grep NEW_NAME
[01/21/25]seed@VM:~$ env | grep NEW_NAME
NEW_NAME=Hudson
[01/21/25]seed@VM:~$ sudo su -
root@VM:~# env | grep NEW_NAME

```
### 2.7 The LD PRELOAD Environment Variable and Set-UID Progra

**Normal User no SUID**
```bash
[01/15/25]seed@VM:~/Labsetup$ vim mylib.c
[01/15/25]seed@VM:~/Labsetup$ gcc -fPIC -g -c mylib.c
[01/15/25]seed@VM:~/Labsetup$ gcc -shared -o libmylib.so.1.0.1 mylib.o -lc
[01/15/25]seed@VM:~/Labsetup$ export LD_PRELOAD=./libmylib.so.1.0.1
[01/15/25]seed@VM:~/Labsetup$ vim myprog.c
[01/15/25]seed@VM:~/Labsetup$ gcc myprog.c -o myprog; ./myprog
I am not sleeping!

```

<div class="page-break" style="page-break-before: always;"></div>

**Normal User SUID -> USER**
```bash
[01/15/25]seed@VM:~/Labsetup$ sudo chown seed myprog
[01/15/25]seed@VM:~/Labsetup$ sudo chmod 4755 myprog
[01/15/25]seed@VM:~/Labsetup$ ./myprog
I am not sleeping!

```

**Normal User SUID -> ROOT**
```bash
[01/15/25]seed@VM:~/Labsetup$ ./myprog
I am not sleeping!

```
## 2.8 Task 8: Invoking External Programs Using system() versus execve()
```bash
[01/15/25]seed@VM:~/Labsetup$ gcc catall.c -o catall; sudo chown root catall; sudo chmod 4755 catall; ./catall '/etc/shadow;id'
/bin/cat: /etc/shadow: Permission denied
uid=1000(seed) gid=1000(seed) groups=1000(seed),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),136(docker)

```
**Running system()**
```bash
[01/15/25]seed@VM:~/Labsetup$ gcc catall.c -o catall; sudo chown root catall; sudo chmod 4755 catall; ./catall '/etc/shadow;id'
/bin/cat: /etc/shadow: Permission denied
uid=1000(seed) gid=1000(seed) groups=1000(seed),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),136(docker)
[01/15/25]seed@VM:~/Labsetup$ vim catall.c

```

<div class="page-break" style="page-break-before: always;"></div>

**Running execve()**
```bash
[01/15/25]seed@VM:~/Labsetup$ vim catall.c
[01/15/25]seed@VM:~/Labsetup$ gcc catall.c -o catall; sudo chown root catall; sudo chmod 4755 catall; ./catall '/etc/shadow'
root:!:18590:0:99999:7:::
daemon:*:18474:0:99999:7:::
bin:*:18474:0:99999:7:::
sys:*:18474:0:99999:7:::
sync:*:18474:0:99999:7:::
games:*:18474:0:99999:7:::
man:*:18474:0:99999:7:::
lp:*:18474:0:99999:7:::
mail:*:18474:0:99999:7:::
news:*:18474:0:99999:7:::
uucp:*:18474:0:99999:7:::
proxy:*:18474:0:99999:7:::
www-data:*:18474:0:99999:7:::
backup:*:18474:0:99999:7:::
list:*:18474:0:99999:7:::
irc:*:18474:0:99999:7:::
gnats:*:18474:0:99999:7:::
nobody:*:18474:0:99999:7:::
systemd-network:*:18474:0:99999:7:::
systemd-resolve:*:18474:0:99999:7:::
systemd-timesync:*:18474:0:99999:7:::
messagebus:*:18474:0:99999:7:::
syslog:*:18474:0:99999:7:::
_apt:*:18474:0:99999:7:::
tss:*:18474:0:99999:7:::
uuidd:*:18474:0:99999:7:::
tcpdump:*:18474:0:99999:7:::
avahi-autoipd:*:18474:0:99999:7:::
usbmux:*:18474:0:99999:7:::
rtkit:*:18474:0:99999:7:::
dnsmasq:*:18474:0:99999:7:::
cups-pk-helper:*:18474:0:99999:7:::
speech-dispatcher:!:18474:0:99999:7:::
avahi:*:18474:0:99999:7:::
kernoops:*:18474:0:99999:7:::
saned:*:18474:0:99999:7:::
nm-openvpn:*:18474:0:99999:7:::
hplip:*:18474:0:99999:7:::
whoopsie:*:18474:0:99999:7:::
colord:*:18474:0:99999:7:::
geoclue:*:18474:0:99999:7:::
pulse:*:18474:0:99999:7:::
gnome-initial-setup:*:18474:0:99999:7:::
gdm:*:18474:0:99999:7:::
seed:$6$n8DimvsbIgU0OxbD$YZ0h1EAS4bGKeUIMQvRhhYFvkrmMQZdr/hB.Ofe3KFZQTgFTcRgoIoKZdO0rhDRxxaITL4b/scpdbTfk/nwFd0:18590:0:99999:7:::
systemd-coredump:!!:18590::::::
telnetd:*:18590:0:99999:7:::
ftp:*:18590:0:99999:7:::
sshd:*:18590:0:99999:7:::
```

<div class="page-break" style="page-break-before: always;"></div>

## 2.9 Capability Leak
```bash
[01/15/25]seed@VM:~/Labsetup$ gcc cap_leak.c -o cap_leak; sudo chown root cap_leak; sudo chmod 4755 cap_leak; ./cap_leak
Cannot open /etc/zzz
[01/15/25]seed@VM:~/Labsetup$ cat cap_leak.c
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <fcntl.h>

void main()
{
  int fd;
  char *v[2];

  /* Assume that /etc/zzz is an important system file,
   * and it is owned by root with permission 0644.
   * Before running this program, you should create
   * the file /etc/zzz first. */
  fd = open("/etc/zzz", O_RDWR | O_APPEND);
  if (fd == -1) {
     printf("Cannot open /etc/zzz\n");
     exit(0);
  }

  // Print out the file descriptor value
  printf("fd is %d\n", fd);

  // Permanently disable the privilege by making the
  // effective uid the same as the real uid
  setuid(getuid());

  // Execute /bin/sh
  v[0] = "/bin/sh"; v[1] = 0;
  execve(v[0], v, 0);
}

```