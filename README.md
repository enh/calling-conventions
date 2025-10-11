# Calling Conventions

## ELF

### arm32

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| r0-r3   |          | Arguments                             | Caller-save |
| r4-r11  |          | Temporaries                           | Callee-save |
| r12     | ip       | Intra-procedural scratch register     |             |
| r13     | sp       | Stack pointer                         |             |
| r14     | lr       | Link register                         |             |

`r12` (`ip`) is used (and clobbered) by the GOT/PLT stubs.

### arm64

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| x0-r7   |          | Arguments                             |             |
| x8      | xr       | Indirect result / temporary           | Caller-save |
| x9-x15  |          | Temporaries (caller-save)             | Caller-save |
| x16-x17 | ip0/ip1  | Intra-procedural scratch registers    |             |
| x18     |          | Platform-specific                     |             |
| x19-x28 |          | Temporaries (callee-save)             | Callee-save |
| x29     | fp       | Frame pointer                         |             |
| x30     | lr       | Link register                         |             |
| x31     | sp/xzr   | Stack pointer/zero register           |             |

`x8` is used to return values too large to be returned in registers. Since that doesn't happen often, it's effectively an extra caller-save temporary.

`x16` and `x17` (`ip0` and `ip1`) are used (and clobbered) by the GOT/PLT stubs.

`x18` is typically used for the software shadow stack pointer.

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| v0-v7   |          | Arguments                             |             |
| v8-v15  |          | Temporaries (callee-save)             | Callee-save |
| v16-v31 |          | Temporaries (caller-save)             | Caller-save |

### riscv64

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| x0      | zero     | Reads zero / discards writes          |             |
| x1      | ra       | Return address                        |             |
| x2      | sp       | Stack pointer                         |             |
| x3      | gp       | Platform-specific                     |             |
| x4      | tp       | Thread pointer                        |             |
| x5-x7   | t0-t2    | Temporary registers 1                 | Caller-save |
| x8-x9   | s0-s1    | Callee-saved registers 1              | Callee-save |
| x10-x17 | a0-a7    | Argument registers                    | Caller-save |
| x18-x27 | s2-s11   | Callee-saved registers 2              | Callee-save |
| x28-x31 | t3-t6    | Temporary registers 2                 | Caller-save |

`x8` (`s0`) is used as the [frame pointer](https://github.com/riscv-non-isa/riscv-elf-psabi-doc/blob/master/riscv-cc.adoc#frame-pointer-convention).

`x3` (`gp`) is called "global pointer" because embedded systems might use it for that, but it's more likely to be the software shadow stack pointer.

The stack is 16-byte (128-bit) aligned at all times.

Temporary and callee-saved registers are each broken into two blocks so the C extension can directly access the first block of each.

| Number  | Name     | Use                                   | Type        |
| ------- | -------- | ------------------------------------- | ----------- |
| f0-f7   | ft0-ft7  | FP temporary registers 1              | Caller-save |
| f8-f9   | fs0-fs1  | FP callee-saved registers 1           | Callee-save |
| f10-f17 | fa0-fa7  | FP argument registers                 | Caller-save |
| f18-f27 | fs2-fs11 | FP callee-saved registers 2           | Callee-save |
| f28-f31 | ft8-ft11 | Temporary registers 2                 | Caller-save |

See also: [RISC-V ELF psABI](https://github.com/riscv-non-isa/riscv-elf-psabi-doc/blob/master/riscv-cc.adoc).

### x86

All arguments passed on the stack, in right-to-left order (!), with the caller responsible for stack cleanup. `eax` used for the return value.

| Name                          | Use                       | Type        |
| ----------------------------- | ------------------------- | ----------- |
| eax                           | Return value              | Caller-save |
| eax, ecx, edx                 | Temporaries (caller-save) | Caller-save |
| esi, edi, ebx, ebp            | Temporaries (callee-save) | Callee-save |
| esp                           | Stack pointer             |             |

### x86-64

| Name                          | Use                       | Type        |
| ----------------------------- | ------------------------- | ----------- |
| rax                           | Return value              | Caller-save |
| rdi, rsi, rdx, rcx, r8, r9    | Arguments                 | Caller-save |
| rax, r10, r11                 | Temporaries (caller-save) | Caller-save |
| rbp, rbx, r12, r13, r14, r15  | Temporaries (callee-save) | Callee-save |
| rsp                           | Stack pointer             |             |

Because x86 has so few registers, frame pointers have typically been omitted, but `rbp` is the frame pointer when used.

## Linux system calls

The "return value" is either a negative error number (which libc negates and stores in `errno`) or the actual result. See [bionic's riscv64 syscall(2)](https://android.googlesource.com/platform/bionic/+/main/libc/arch-riscv64/bionic/syscall.S#45) for an example implementation.

See also [man 2 syscall](https://man7.org/linux/man-pages/man2/syscall.2.html) for architectures not covered here.

| Architecture | Arguments                    | System call number | Return value |
| ------------ | ---------------------------- | ------------------ | ------------ |
| arm32        | r0-r6                        | r7                 | r0           |
| arm64        | x0-x5                        | w8                 | x0           |
| riscv64      | a0-a5                        | a7                 | a0           |
| x86          | ebx, ecx, edx, esi, edi, rbp | eax                | eax          |
| x86-64       | rdi, rsi, rdx, r10, r8, r9   | rax                | rax          |

Note: arm32 splits 64-bit arguments across register pairs, but each pair must start with an even-numbered register.
