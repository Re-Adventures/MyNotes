# ARM

* There are a total of 30 32bit General Purpose Registers (GPR).
* The first 16 regs are accessible in user mode while the additional regs are available in priviledged mode only (AMRv6-M & ARMv7-M are exceptions)

## Registers

* R0-R6: General Purpose Registers
* R7: Syscall number
* R8-R10: General Purpose Registers
* R11: Frame Pointer (same as EBP in x86)
* R12: Intra Procedural Call (IP)
* R13: Stack Pointer (SP)
* R14: Link Register (LR)
* R15: Program Counter (same as IP in x86)
* CSPR: Current Program State Registers (same as EFLAGS in x86)


* R7 holds the syscall number
* R0-R3 holds the arguments for function call
* R0 holds the return value of a function
* R0 works as an accumulator for mulitplication/division
* R14(LR) holds the return address, where to return to after the function ends.
* R15(PC) automatically gets incremented by 4 bytes (ARM Mode) or 2 bytes (THUMB Mode). PC stores the address of current instruction +8 bytes (ARM) or +4 (THUMB) i.e. 2 instruction ahead of it. For legacy reasons?


# Instruction Syntax

Syntax: `Mnemonic(S)(Condition) dest, operand1, operand2`

* Example:
1. `add  r0, r1, r2 // r0 = r1 + r2`

2. `add  r3, r4, #4 // r3 = r4 + 4, integers are prefixed by a "#" symbol`

3. `add  r0, r1     // same as r0 = r0 + r1`

4. `adds r0, r1, r2 // r0 = r1 + r2, & sets the cspr flags`

5. `movgt r3, r3, #5 // r3 = 5 if r3 > 5 else nop`

## Running and compiling ARM on x64 based system

* For compiling we would require cross compilers

* For emulating we will use qemu
```sh
sudo apt install qemu
```


## Simple Hello World program

```asm
.global _start

.section .text
_start:
  mov  r7, #4   //syscall for write
  mov  r0, #1   //stdout
  ldr  r1, =msg //string to be printed
  mov  r2, #13  //length of the string
  swi  0        //software interrupt

exit:
  mov  r7, #1   //syscall for write
  mov  r0, #0   //error code
  svc  0        //another way of performing syscall

```


