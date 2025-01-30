### Sample Program 1:
```C
#include <stdio.h>

int main() {
    char buffer[10];
    gets(buffer);
    printf("You entered: %s\n", buffer);
    return 0;
}
```

**Question:**  
- Which memory bug is present in this program?  stack-buffer-overflow
- **Justify your answer:**

**ASAN Sanitizer**
```bash
ubuntu@ubuntu:~$ cat simple.c
#include <stdio.h>

int main() {
        char buffer[10];
        gets(buffer);
        printf("You entered: %s\n", buffer);
        return 0;
}
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
simple.c: In function ‘main’:
simple.c:5:9: warning: implicit declaration of function ‘gets’; did you mean ‘fgets’? [-Wimplicit-function-declaration]
    5 |         gets(buffer);
      |         ^~~~
      |         fgets
/usr/bin/ld: /tmp/ccZqucwk.o: in function `main':
/home/ubuntu/simple.c:5: warning: the `gets' function is dangerous and should not be used.

```

**Execute** 
```bash
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277115==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7fffffffe36a at pc 0x7ffff745df89 bp 0x7fffffffe1f0 sp 0x7fffffffd968
READ of size 28 at 0x7fffffffe36a thread T0
    #0 0x7ffff745df88 in printf_common ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors_format.inc:553
    #1 0x7ffff745e6cc in __interceptor_vprintf ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:1660
    #2 0x7ffff745e7c6 in __interceptor_printf ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:1718
    #3 0x5555555552ac in main /home/ubuntu/simple.c:6
    #4 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #5 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #6 0x555555555144 in _start (/home/ubuntu/simple+0x1144)

Address 0x7fffffffe36a is located in stack of thread T0 at offset 42 in frame
    #0 0x555555555218 in main /home/ubuntu/simple.c:3

  This frame has 1 object(s):
    [32, 42) 'buffer' (line 4) <== Memory access at offset 42 overflows this variable
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
      (longjmp and C++ exceptions *are* supported)
SUMMARY: AddressSanitizer: stack-buffer-overflow ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors_format.inc:553 in printf_common
Shadow bytes around the buggy address:
  0x10007fff7c10: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c20: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c30: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c40: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x10007fff7c60: 00 00 00 00 00 00 00 00 f1 f1 f1 f1 00[02]f3 f3
  0x10007fff7c70: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7ca0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7cb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==277115==ABORTING
ubuntu@ubuntu:~$

```

### Simple Program 2
*heap-buffer-overflow*

```bash
ubuntu@ubuntu:~$ cat simple.c
#include <stdlib.h>
#include <string.h>

int main() {
        char *buffer = malloc(10);
        strcpy(buffer, "This is a very long string");
        printf("Buffer content: %s\n", buffer);
        free(buffer);
        return 0;
}

```

**ASAN Sanitizer**
```c
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
simple.c: In function ‘main’:
simple.c:7:9: warning: implicit declaration of function ‘printf’ [-Wimplicit-function-declaration]
    7 |         printf("Buffer content: %s\n", buffer);
      |         ^~~~~~
simple.c:3:1: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
    2 | #include <string.h>
  +++ |+#include <stdio.h>
    3 |
simple.c:7:9: warning: incompatible implicit declaration of built-in function ‘printf’ [-Wbuiltin-declaration-mismatch]
    7 |         printf("Buffer content: %s\n", buffer);
      |         ^~~~~~
simple.c:7:9: note: include ‘<stdio.h>’ or provide a declaration of ‘printf’
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277153==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x60200000001a at pc 0x7ffff743a2c3 bp 0x7fffffffe380 sp 0x7fffffffdb28
WRITE of size 27 at 0x60200000001a thread T0
    #0 0x7ffff743a2c2 in __interceptor_memcpy ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827
    #1 0x55555555525d in main /home/ubuntu/simple.c:6
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #3 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #4 0x555555555164 in _start (/home/ubuntu/simple+0x1164)

0x60200000001a is located 0 bytes to the right of 10-byte region [0x602000000010,0x60200000001a)
allocated by thread T0 here:
    #0 0x7ffff74b4887 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x55555555523e in main /home/ubuntu/simple.c:5
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

SUMMARY: AddressSanitizer: heap-buffer-overflow ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827 in __interceptor_memcpy
Shadow bytes around the buggy address:
  0x0c047fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c047fff8000: fa fa 00[02]fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8010: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8020: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==277153==ABORTING
ubuntu@ubuntu:~$

```

### Simple Program 3
*Null Pointer Dereference*
```bash
ubuntu@ubuntu:~$ cat simple.c
#include <stdio.h>

int main() {
        int *ptr = NULL;
        *ptr = 5;
        printf("Value: %d\n", *ptr);
        return 0;
}
```

**ASAN Sanitizer**
```bash
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
AddressSanitizer:DEADLYSIGNAL
=================================================================
==277176==ERROR: AddressSanitizer: SEGV on unknown address 0x000000000000 (pc 0x555555555238 bp 0x7fffffffe3a0 sp 0x7fffffffe390 T0)
==277176==The signal is caused by a WRITE memory access.
==277176==Hint: address points to the zero page.
    #0 0x555555555238 in main /home/ubuntu/simple.c:5
    #1 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #2 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #3 0x555555555124 in _start (/home/ubuntu/simple+0x1124)

AddressSanitizer can not provide additional info.
SUMMARY: AddressSanitizer: SEGV /home/ubuntu/simple.c:5 in main
==277176==ABORTING
```

### Simple Program 4
*heap-use-after-free*

```C
#include <stdio.h>
#include <stdlib.h>

int main() {
        char *buffer = malloc(10);
        free(buffer);
        strcpy(buffer, "Hello!");
        printf("%s\n", buffer);
        return 0;
}
```

**ASAN Sanitizer**
```bash
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
simple.c: In function ‘main’:
simple.c:7:9: warning: implicit declaration of function ‘strcpy’ [-Wimplicit-function-declaration]
    7 |         strcpy(buffer, "Hello!");
      |         ^~~~~~
simple.c:3:1: note: include ‘<string.h>’ or provide a declaration of ‘strcpy’
    2 | #include <stdlib.h>
  +++ |+#include <string.h>
    3 |
simple.c:7:9: warning: incompatible implicit declaration of built-in function ‘strcpy’ [-Wbuiltin-declaration-mismatch]
    7 |         strcpy(buffer, "Hello!");
      |         ^~~~~~
simple.c:7:9: note: include ‘<string.h>’ or provide a declaration of ‘strcpy’
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277207==ERROR: AddressSanitizer: heap-use-after-free on address 0x602000000010 at pc 0x7ffff743a2c3 bp 0x7fffffffe380 sp 0x7fffffffdb28
WRITE of size 7 at 0x602000000010 thread T0
    #0 0x7ffff743a2c2 in __interceptor_memcpy ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827
    #1 0x555555555269 in main /home/ubuntu/simple.c:7
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #3 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #4 0x555555555164 in _start (/home/ubuntu/simple+0x1164)

0x602000000010 is located 0 bytes inside of 10-byte region [0x602000000010,0x60200000001a)
freed by thread T0 here:
    #0 0x7ffff74b4537 in __interceptor_free ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:127
    #1 0x55555555524e in main /home/ubuntu/simple.c:6
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

previously allocated by thread T0 here:
    #0 0x7ffff74b4887 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x55555555523e in main /home/ubuntu/simple.c:5
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

SUMMARY: AddressSanitizer: heap-use-after-free ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827 in __interceptor_memcpy
Shadow bytes around the buggy address:
  0x0c047fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c047fff8000: fa fa[fd]fd fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8010: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8020: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==277207==ABORTING
ubuntu@ubuntu:~$
```

### Simple Program 5
*double-free*
**simple.c**
```C
#include <stdio.h>
#include <stdlib.h>

int main() {
        int *buffer = malloc(10 * sizeof(int));
        free(buffer);
        free(buffer);
        return 0;
}
```

**ASAN Sanitizer**
```bash
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277232==ERROR: AddressSanitizer: attempting double-free on 0x604000000010 in thread T0:
    #0 0x7ffff74b4537 in __interceptor_free ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:127
    #1 0x5555555551da in main /home/ubuntu/simple.c:7
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #3 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #4 0x5555555550e4 in _start (/home/ubuntu/simple+0x10e4)

0x604000000010 is located 0 bytes inside of 40-byte region [0x604000000010,0x604000000038)
freed by thread T0 here:
    #0 0x7ffff74b4537 in __interceptor_free ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:127
    #1 0x5555555551ce in main /home/ubuntu/simple.c:6
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

previously allocated by thread T0 here:
    #0 0x7ffff74b4887 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x5555555551be in main /home/ubuntu/simple.c:5
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

SUMMARY: AddressSanitizer: double-free ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:127 in __interceptor_free
==277232==ABORTING

```
### Simple Program 6
**Format String Vulnerability**
```bash
ubuntu@ubuntu:~$ cat simple.c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main() {
        char *buffer = malloc(10);
        free(buffer);
        buffer = malloc(20);
        strcpy(buffer, "Hello, World!");
        printf("%s\n", buffer);
        return 0;
}
```


### Simple Program 7
*stack-buffer-overflow*
```bash
ubuntu@ubuntu:~$ vim simple.c
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277329==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7fffffffe368 at pc 0x7ffff743a2c3 bp 0x7fffffffe330 sp 0x7fffffffdad8
WRITE of size 22 at 0x7fffffffe368 thread T0
    #0 0x7ffff743a2c2 in __interceptor_memcpy ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827
    #1 0x55555555529b in main /home/ubuntu/simple.c:6
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #3 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #4 0x555555555144 in _start (/home/ubuntu/simple+0x1144)

Address 0x7fffffffe368 is located in stack of thread T0 at offset 40 in frame
    #0 0x555555555218 in main /home/ubuntu/simple.c:4

  This frame has 1 object(s):
    [32, 40) 'buffer' (line 5) <== Memory access at offset 40 overflows this variable
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
      (longjmp and C++ exceptions *are* supported)
SUMMARY: AddressSanitizer: stack-buffer-overflow ../../../../src/libsanitizer/sanitizer_common/sanitizer_common_interceptors.inc:827 in __interceptor_memcpy
Shadow bytes around the buggy address:
  0x10007fff7c10: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c20: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c30: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c40: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x10007fff7c60: 00 00 00 00 00 00 00 00 f1 f1 f1 f1 00[f3]f3 f3
  0x10007fff7c70: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7c90: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7ca0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x10007fff7cb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==277329==ABORTING
ubuntu@ubuntu:~$

```


### Simple Program 8
*Heap buffer overflow*
```bash
ubuntu@ubuntu:~$ vim simple.c
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277346==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x603000000054 at pc 0x555555555238 bp 0x7fffffffe380 sp 0x7fffffffe370
WRITE of size 4 at 0x603000000054 thread T0
    #0 0x555555555237 in main /home/ubuntu/simple.c:7
    #1 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #2 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #3 0x555555555104 in _start (/home/ubuntu/simple+0x1104)

0x603000000054 is located 0 bytes to the right of 20-byte region [0x603000000040,0x603000000054)
allocated by thread T0 here:
    #0 0x7ffff74b4887 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x5555555551de in main /home/ubuntu/simple.c:5
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

SUMMARY: AddressSanitizer: heap-buffer-overflow /home/ubuntu/simple.c:7 in main
Shadow bytes around the buggy address:
  0x0c067fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c067fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c067fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c067fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c067fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c067fff8000: fa fa 00 00 00 fa fa fa 00 00[04]fa fa fa fa fa
  0x0c067fff8010: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c067fff8020: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c067fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c067fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c067fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==277346==ABORTING
ubuntu@ubuntu:~$

```

### Simple Program 9
*heap-use-after-free*
```bash
ubuntu@ubuntu:~$ vim simple.c
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
=================================================================
==277364==ERROR: AddressSanitizer: heap-use-after-free on address 0x602000000010 at pc 0x5555555552e7 bp 0x7fffffffe380 sp 0x7fffffffe370
READ of size 4 at 0x602000000010 thread T0
    #0 0x5555555552e6 in main /home/ubuntu/simple.c:8
    #1 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58
    #2 0x7ffff7029e3f in __libc_start_main_impl ../csu/libc-start.c:392
    #3 0x555555555184 in _start (/home/ubuntu/simple+0x1184)

0x602000000010 is located 0 bytes inside of 4-byte region [0x602000000010,0x602000000014)
freed by thread T0 here:
    #0 0x7ffff74b4537 in __interceptor_free ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:127
    #1 0x5555555552af in main /home/ubuntu/simple.c:7
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

previously allocated by thread T0 here:
    #0 0x7ffff74b4887 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cpp:145
    #1 0x55555555525e in main /home/ubuntu/simple.c:5
    #2 0x7ffff7029d8f in __libc_start_call_main ../sysdeps/nptl/libc_start_call_main.h:58

SUMMARY: AddressSanitizer: heap-use-after-free /home/ubuntu/simple.c:8 in main
Shadow bytes around the buggy address:
  0x0c047fff7fb0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7fe0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c047fff7ff0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c047fff8000: fa fa[fd]fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8010: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8020: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8030: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8040: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fff8050: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==277364==ABORTING
ubuntu@ubuntu:~$
```


### Simple Program 10
*Use of uninitialized variable*
```bash
ubuntu@ubuntu:~$ cat simple.c
#include <stdio.h>

int main() {
        char *buffer;
        printf("Buffer content: %s\n", buffer);
        return 0;
}

```
```bash
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
Buffer content: (null)
ubuntu@ubuntu:~$
```


### Simple Program 11
```C
ubuntu@ubuntu:~$ cat simple.c
#include <stdio.h>

int main() {
        int x = 2147483647;
        int y = x + 1;
        printf("y = %d\n", y);
        return 0;
}
```

```bash
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
y = -2147483648
```

### Simple Program 12
```bash
ubuntu@ubuntu:~$ cat simple.c
#include <stdio.h>

int main() {
        char buffer[100];
        int user_input = 12343333333333333333333333333333333333333333;
        sprintf(buffer, "User input: %x", user_input);
        printf(buffer);
        return 0;
}
ubuntu@ubuntu:~$ gcc -g -fsanitize=address -fno-stack-protector -z execstack simple.c -o simple
simple.c: In function ‘main’:
simple.c:5:26: warning: integer constant is too large for its type
    5 |         int user_input = 12343333333333333333333333333333333333333333;
      |                          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
simple.c:5:26: warning: overflow in conversion from ‘long int’ to ‘int’ changes value from ‘5293051541640795477’ to ‘1431655765’ [-Woverflow]
simple.c:7:16: warning: format not a string literal and no format arguments [-Wformat-security]
    7 |         printf(buffer);
      |                ^~~~~~
ubuntu@ubuntu:~$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | ./simple
User input: 55555555ubuntu@ubuntu:~$

```