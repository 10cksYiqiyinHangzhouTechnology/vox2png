**Project Address**

https://github.com/jemisa/vox2png



**Security Issue Report**

A **heap-buffer-overflow** issue was discovered in vox2png project in **vox2png.c** file. The flow allows an attacker to cause a denial of service (abort) via a crafted file.

**Summary**

SUMMARY: AddressSanitizer: heap-buffer-overflow /home/ubuntu/Desktop/vox2png/vox2png.c:247 in main

**Problem Code location**

```
    #0 0x555555562a1c in main /home/ubuntu/Desktop/vox2png/vox2png.c:247
    #1 0x7ffff7266082 in __libc_start_main ../csu/libc-start.c:308
    #2 0x5555555575ad in _start (/home/ubuntu/Desktop/vox2png/asan_vox2png+0x35ad)

```

[Poc file](https://github.com/10cksYiqiyinHangzhouTechnology/vox2png/blob/main/id11)

[asan_tinytiffreader](https://github.com/10cksYiqiyinHangzhouTechnology/vox2png/blob/main/asan_vox2png)

**ASAN Report:**

```bash
ubuntu@ubuntu:~/Desktop/vox2png$ ./asan_vox2png out/default/crashes/id\:000011\,sig\:11\,src\:000000+000158\,time\:115193\,execs\:9320\,op\:splice\,rep\:4 
Loaded the .vox file into memory
Found size chunk: [48, 48, 14]
Found voxel chunk: 4104 voxels
No palette chunk found, using the default palette
=================================================================
==2583824==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x629000004281 at pc 0x555555562a1d bp 0x7fffffffdde0 sp 0x7fffffffddd0
READ of size 4 at 0x629000004281 thread T0
    #0 0x555555562a1c in main /home/ubuntu/Desktop/vox2png/vox2png.c:247
    #1 0x7ffff7266082 in __libc_start_main ../csu/libc-start.c:308
    #2 0x5555555575ad in _start (/home/ubuntu/Desktop/vox2png/asan_vox2png+0x35ad)

0x629000004281 is located 0 bytes to the right of 16513-byte region [0x629000000200,0x629000004281)
allocated by thread T0 here:
    #0 0x7ffff7690808 in __interceptor_malloc ../../../../src/libsanitizer/asan/asan_malloc_linux.cc:144
    #1 0x555555561fbb in main /home/ubuntu/Desktop/vox2png/vox2png.c:151
    #2 0x7ffff7266082 in __libc_start_main ../csu/libc-start.c:308

SUMMARY: AddressSanitizer: heap-buffer-overflow /home/ubuntu/Desktop/vox2png/vox2png.c:247 in main
Shadow bytes around the buggy address:
  0x0c527fff8800: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8810: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8820: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8830: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x0c527fff8840: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
=>0x0c527fff8850:[01]fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8860: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8870: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8880: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff8890: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c527fff88a0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
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
==2583824==ABORTING

```
