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

* For compiling we would require cross compilers because I am on x86 machine & this is arm code. For installing arm cross compiler
```sh
sudo apt install gcc-5-arm-linux-gnueabi
```

* After the installation, we first need to assemble the assembly file & convert it into an object file
```sh
arm-linux-gnueabihf-as prog.asm -o prog.o
```

* Now we need to link this object file to make it an executable file
```sh
arm-linux-gnueabihf-ld prog.o -o a.out
```

* For emulating we will use qemu
```sh
sudo apt install qemu
```

* Now we can just run it
```sh
qemu-arm ./a.out
# or just
./a.out
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
  mov  r7, #1   //syscall for exit
  mov  r0, #0   //error code
  svc  0        //another way of performing syscall

```

## Finding strlen of a string

* A string in C ends with a null byte (\0), so we can just get characters from string byte by byte & increment the counter unless we encounter a null byte.

```asm
.global _start

.section .data
  msg:  .ascii  "Hello World\n", "\0"

.section .text
_start:
  ldr  r0, =msg // Passing the string for calculating strlen
  bl   strlen

  mov  r2, r0 // strlen
  mov  r7, #4
  mov  r0, #1
  ldr  r1, =msg
  swi  0
  bl   exit

strlen:
  eor  r1, r1  //r1 will store the strlen

strlen_loop:
  ldrb r2, [r0]    //load a byte from string into r2
  cmp  r2, #0      //If it is a null byte then stop the loop
  beq  strlen_end
  add  r1, #1      //Increment the counter
  add  r0, #1
  b    strlen_loop

strlen_end:
  mov  r0, r1
  bx   lr  // return

exit:
  mov  r7, #1
  eor  r0, r0
  swi  0
```

