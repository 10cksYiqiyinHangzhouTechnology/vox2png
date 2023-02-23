**Project Address**

https://github.com/jemisa/vox2png

**Security Issue Report**

A heap-buffer-overflow issue was discovered in vox2png project in vox2png.c file. The flow allows an attacker to cause a denial of service (abort) via a crafted file.

**OS information**

```
ubuntu@ubuntu:~$ uname -a
Linux ubuntu 5.15.0-58-generic #64~20.04.1-Ubuntu SMP Fri Jan 6 16:42:31 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
```


**Summary**

SUMMARY: AddressSanitizer: heap-buffer-overflow /home/ubuntu/Desktop/vox2png/vox2png.c:247 in main

**Problem Code location**

```
    #0 0x555555562a1c in main /home/ubuntu/Desktop/vox2png/vox2png.c:247
    #1 0x7ffff7266082 in __libc_start_main ../csu/libc-start.c:308
    #2 0x5555555575ad in _start (/home/ubuntu/Desktop/vox2png/asan_vox2png+0x35ad)

```

[Poc file: id11](https://github.com/10cksYiqiyinHangzhouTechnology/vox2png/blob/main/id11)

[asan_vox2png](https://github.com/10cksYiqiyinHangzhouTechnology/vox2png/blob/main/asan_vox2png)

**compile the test case in the source**

```
gcc vox2png.c -o vox2png -lm
./vox2png input.vox output.png
```

**Test Poc**
```
./asan_vox2png id11
```



**ASAN Report**

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

**Use gdb analysis**
```bash
ubuntu@ubuntu:~/Desktop/vox2png$ gdb --args ./vox2png id11 
GNU gdb (Ubuntu 9.2-0ubuntu1~20.04.1) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./vox2png...
gdb-peda$ r id11
Starting program: /home/ubuntu/Desktop/vox2png/vox2png id11
Loaded the .vox file into memory
Found size chunk: [48, 48, 14]
Found voxel chunk: 4104 voxels
No palette chunk found, using the default palette

Program received signal SIGSEGV, Segmentation fault.
[----------------------------------registers-----------------------------------]
RAX: 0x8dc90 
RBX: 0x30 ('0')
RCX: 0x55555556f521 --> 0xff0000 
RDX: 0x3 
RSI: 0x55555556f520 --> 0xff000000 
RDI: 0xffffffff 
RBP: 0x555555572dd1 --> 0x0 
RSP: 0x7fffffffdeb0 --> 0x3000000000 ('')
RIP: 0x555555556e9d (<main+6685>:	mov    DWORD PTR [rsi+rax*4],edi)
R8 : 0x0 
R9 : 0x0 
R10: 0x0 
R11: 0x30 ('0')
R12: 0xe 
R13: 0x2a0 
R14: 0x30 ('0')
R15: 0x555555566920 --> 0xffccffffffffffff
EFLAGS: 0x10206 (carry PARITY adjust zero sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x555555556e93 <main+6675>:	add    eax,r10d
   0x555555556e96 <main+6678>:	movzx  r10d,BYTE PTR [rcx+0x5]
   0x555555556e9b <main+6683>:	cdqe   
=> 0x555555556e9d <main+6685>:	mov    DWORD PTR [rsi+rax*4],edi
   0x555555556ea0 <main+6688>:	movzx  eax,BYTE PTR [rcx+0x6]
   0x555555556ea4 <main+6692>:	mov    edi,DWORD PTR [r15+r9*4]
   0x555555556ea8 <main+6696>:	movzx  r9d,BYTE PTR [rcx+0xb]
   0x555555556ead <main+6701>:	cdq
[------------------------------------stack-------------------------------------]
0000| 0x7fffffffdeb0 --> 0x3000000000 ('')
0008| 0x7fffffffdeb8 --> 0x1 
0016| 0x7fffffffdec0 --> 0x3921 ('!9')
0024| 0x7fffffffdec8 --> 0x555500000030 ('0')
0032| 0x7fffffffded0 --> 0x7fffffffe070 --> 0x7fffffffe3ac ("SHELL=/bin/bash")
0040| 0x7fffffffded8 --> 0x555555565880 (<__libc_csu_init>:	endbr64)
0048| 0x7fffffffdee0 --> 0x555555555480 (<main>:	lea    rsp,[rsp-0x98])
0056| 0x7fffffffdee8 --> 0x0 
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x0000555555556e9d in main (argc=argc@entry=0x2, argv=argv@entry=0x7fffffffe058) at vox2png.c:256
256	        pngData[dataIndex] = currentColor;
```



