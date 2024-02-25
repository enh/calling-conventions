# Calling Conventions

## Linux system calls

The "return value" is either a negative error number (which libc negates and stores in `errno`) or the actual result. See [bionic's riscv64 syscall(2)](https://android.googlesource.com/platform/bionic/+/main/libc/arch-riscv64/bionic/syscall.S#45) for an example implementation.

See also [man 2 syscall](https://man7.org/linux/man-pages/man2/syscall.2.html) for architectures not covered here.

### arm32
On entry:  
  r0-r6 - arguments  
  r7 - system call number

On exit:  
  r0 - return value

Note: arm32 splits 64-bit arguments across register pairs, but each pair must start with an even-numbered register.

## arm64
On entry:  
  x0-x5 - arguments  
  w8 - system call number

On exit:  
  x0 - return value

## riscv64
On entry:  
  a0-a5 - arguments  
  a7 - system call number

On exit:  
  a0 - return value

## x86
On entry:  
  ebx,ecx,edx,esi,edi,ebp - arguments  
  eax - system call number

On exit:  
  eax - return value

## x86-64
On entry:  
  rdi,rsi,rdx,r10,r8,r9 - arguments  
  rax - system call number

On exit:  
  rax - return value
