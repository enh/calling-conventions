# Calling Conventions

## ELF

### arm32

TODO

### arm64

TODO

### riscv64

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| x0      | zero     | Reads zero / discards writes          |             |
| x1      | ra       | Return address                        | Caller-save |
| x2      | sp       | Stack pointer                         | Callee-save |
| x3      | gp       | Platform-specific                     |             |
| x4      | tp       | Thread pointer                        |             |
| x5-x7   | t0-t2    | Temporary registers 1                 | Caller-save |
| x8-x9   | s0-s1    | Callee-saved registers 1              | Callee-save |
| x10-x17 | a0-a7    | Argument registers                    | Caller-save |
| x18-x27 | s2-s11   | Callee-saved registers 2              | Callee-save |
| x28-x31 | t3-t6    | Temporary registers 2                 | Caller-save |

*Frame pointer*: `x8` (`s0`) is used as the [frame pointer](https://github.com/riscv-non-isa/riscv-elf-psabi-doc/blob/master/riscv-cc.adoc#frame-pointer-convention).

`x3` (`gp`) is called "global pointer" because embedded systems might use it for that, but it's more likely to be the software shadow stack pointer.

The stack is 16-byte (128-bit) aligned at all times.

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| f0-f7   | ft0-ft7  | FP temporary registers 1              | Caller-save |
| f8-f9   | fs0-fs1  | FP callee-saved registers 1           | Callee-save |
| f10-f17 | fa0-fa7  | FP argument registers                 | Caller-save |
| f18-f27 | fs2-fs11 | FP callee-saved registers 2           | Callee-save |
| f28-f31 | ft8-ft11 | Temporary registers 2                 | Caller-save |

See also: [RISC-V ELF psABI](https://github.com/riscv-non-isa/riscv-elf-psabi-doc/blob/master/riscv-cc.adoc).

### x86

TODO

### x86-64

TODO

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
