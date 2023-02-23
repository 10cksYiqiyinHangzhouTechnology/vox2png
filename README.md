**Project Address**

https://github.com/jkriege2/TinyTIFF



**Security Issue Report**

A global-buffer-overflow issue was discovered in TinyTIFF in tinytiffreader.c file. The flow allows an attacker to cause a denial of service (abort) via a crafted file.

**Summary**

AddressSanitizer: global-buffer-overflow (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x4969c6) in __asan_memcpy

**Problem Code location**

```
    #0 0x4969c6 in __asan_memcpy (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x4969c6)
    #1 0x4cdff4 in TinyTIFFReader_readNextFrame /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c
    #2 0x4cb3e9 in TinyTIFFReader_open /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:921:9
    #3 0x4d10ea in main /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:1058:10
    #4 0x7ffff7c4a082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16
    #5 0x41c3bd in _start (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x41c3bd)
```

[Poc file](https://github.com/10cksYiqiyinHangzhouTechnology/Security-Issue-Report-of-TinyTIFF/blob/main/id8)

[asan_tinytiffreader](https://github.com/10cksYiqiyinHangzhouTechnology/Security-Issue-Report-of-TinyTIFF/blob/main/asan_tinytiffreader)

**ASAN Report:**

```bash
ubuntu@ubuntu:~/Desktop/TinyTIFF/src$ ./asan_tinytiffreader out/default/crashes/id\:000008\,sig\:11\,src\:000016+000007\,time\:306221\,execs\:34950\,op\:splice\,rep\:16 
=================================================================
==3817392==ERROR: AddressSanitizer: global-buffer-overflow on address 0x0000004e82c3 at pc 0x0000004969c7 bp 0x7fffffffd890 sp 0x7fffffffd058
READ of size 2082441480 at 0x0000004e82c3 thread T0
    #0 0x4969c6 in __asan_memcpy (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x4969c6)
    #1 0x4cdff4 in TinyTIFFReader_readNextFrame /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c
    #2 0x4cb3e9 in TinyTIFFReader_open /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:921:9
    #3 0x4d10ea in main /home/ubuntu/Desktop/TinyTIFF/src/tinytiffreader.c:1058:10
    #4 0x7ffff7c4a082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16
    #5 0x41c3bd in _start (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x41c3bd)

0x0000004e82c3 is located 61 bytes to the left of global variable '<string literal>' defined in 'tinytiffreader.c:653:13' (0x4e8300) of size 62
0x0000004e82c3 is located 0 bytes to the right of global variable '<string literal>' defined in 'tinytiffreader.c:214:32' (0x4e82c0) of size 3
  '<string literal>' is ascii string 'rb'
SUMMARY: AddressSanitizer: global-buffer-overflow (/home/ubuntu/Desktop/TinyTIFF/src/asan_tinytiffreader+0x4969c6) in __asan_memcpy
Shadow bytes around the buggy address:
  0x000080095000: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x000080095010: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x000080095020: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x000080095030: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x000080095040: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x000080095050: 00 00 00 00 00 00 00 00[03]f9 f9 f9 f9 f9 f9 f9
  0x000080095060: 00 00 00 00 00 00 00 06 f9 f9 f9 f9 00 00 00 00
  0x000080095070: 00 00 f9 f9 f9 f9 f9 f9 00 00 00 00 00 00 00 07
  0x000080095080: f9 f9 f9 f9 00 00 00 00 00 00 00 03 f9 f9 f9 f9
  0x000080095090: 00 00 00 00 00 03 f9 f9 f9 f9 f9 f9 00 00 00 00
  0x0000800950a0: 00 00 00 02 f9 f9 f9 f9 00 00 00 00 00 00 00 00
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
==3817392==ABORTING
```
