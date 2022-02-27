# Unicorn Engine

*Unicorn is a lightweight multi-platform, multi-architecture CPU emulator framework*

* Basically Unicorn is a emulator capable of emulating various machine architectures.

* Unicorn is written in C & has bindings for various languages including Python & Rust

* We can download unicorn from [here](https://www.unicorn-engine.org/) or if we install **pwntools** & unicorn is installed automatically with it.

## Emulating some code
Lets try to emulate some x64 code using python

* First writing some x64 code that we need to emulate. I will use nasm syntax

```asm
[bits 64]
section .text
inc  r8
inc  r9
inc  r10
```

We will assemble this using the following command
`nasm file.asm -o code.bin`

* Writing the emuation in python

1. First we will start by import unicorn & reading the bytecode that we want to emulate

```py
from unicorn import *
from unicorn.unicorn.x86_const import * # this has registers

with open("code.bin", "rb") as fp:
  bytecode = fp.read()

# This address can be anything
# On this address the emulation will proceed
ADDR = 0x100000
```

2. Next we will initilize the unicorn engine in x64 mode, initilize memory for emulation & copy the bytecode in the memory allocated

```py
# Initializing the emulator for x86 64bit mode
E = Uc(UC_ARCH_X86, UC_MODE_64)

# Allocating 4MB of Memory for emulation
E.mem_map(ADDR, 4 * 1024 * 1024)

# Writing the bytecode we want to emulate in the memory
E.mem_write(ADDR, bytecode)
```

3. Before starting the emulation we will initialize some registers
```py
# Initializing r8 = 1, r9 = 3, r10 = 5
E.reg_write(UC_X86_REG_R9, 3)
E.reg_write(UC_X86_REG_R8, 1)
E.reg_write(UC_X86_REG_R10, 5)
```

4. Starting the emulation
```py
# We need to provide emulation start & end addr
E.emu_start(ADDR, ADDR + len(bytecode))
```

5. Checking the registers values to confirm that the emulation was successful
```py
print(f"R8: {E.reg_read(UC_X86_REG_R8)}")
print(f"R9: {E.reg_read(UC_X86_REG_R9)}")
print(f"R10: {E.reg_read(UC_X86_REG_R10)}")
```

Running the script gives the following result
```
user@server:~/Emulation/unicorn$ python basic.py
R8: 10
R9: 11
R10: 12
```

This confirms that our emulation was successful

The full script is as follows
```py
from unicorn import *
from unicorn.x86_const import *

with open("code", "rb") as fp:
  code = fp.read()

address = 0x1000000

E = Uc(UC_ARCH_X86, UC_MODE_64)

E.mem_map(address, 4 * 1024 * 1024)

E.mem_write(address, code)

E.reg_write(UC_X86_REG_R8, 9)
E.reg_write(UC_X86_REG_R9, 10)
E.reg_write(UC_X86_REG_R10, 11)

E.emu_start(address, address + len(code))

print(f"R8: {E.reg_read(UC_X86_REG_R8)}")
print(f"R9: {E.reg_read(UC_X86_REG_R9)}")
print(f"R10: {E.reg_read(UC_X86_REG_R10)}")
```


