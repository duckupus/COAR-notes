RISC computers are very different from CISC computers

CISC - Complex Instruction Set Computer
- Large number of addressing modes
- Different instruction versions for different operands
- Execution time depends on instruction executed
- Small amount of processor registers
- Microcoding used to implement more complex instructions
- Emphasizes **hardware** complexity
- Extensive instruction set
- machine commands are Microcoded
- In the past, when programs were written in assembly, improvements to the speed of instructions would improve the speed of the general program as a whole.

RISC - Reduced Instruction Set Computer
- One instruction per clock cycle
- Access memory using deliciated instructions
- Lesser addressing modes
- e.g.: ARM, SPARC, RISC-V
- Emphasizes **software** complexity
- Simple design - no complex instructions(like `MUL`, `DIV`, etc.).
- Simple addressing modes(reg+reg and reg+intermid const)
- No [Microcoding](#Microcoding) used, all hard-wired.
- More registers(hundreds). Named R0-R111, etc.
- Load-store architecture

# CISC Design
CISC simplifies compiler design, hardware complexity increases due to more complex addressing modes created.

However, more complex instructions may not be used often. Which means there is less observable impact on performance.
## Microcoding
Instructions sets in the past were hard wired. More complex operations CISC created made it harder and harder to implement using hard-wiring. Hence, Microcoding was created to make a control unit. Using subroutines instead of fixed hardware, it would be easier to design and modify even late into the CPU designing process.

However, Microcoding will require much more clock cycles to finish an instruction as compared to hardwired circuitry.
# RISC Design
20% of instructions used 80% of the time in CISC designs. Unlike CISC, RISC has fixed instruction sizes, are fetched in 1 cycle, and are all easily decoded.
## Instruction frequency
| Inst              | Freq  |
| ----------------- | ----- |
| Move              | 33%   |
| Branch            | 16%   |
| compare           | 11%   |
| add, sub, AND, OR | 12%   |
| multiply          | 0.58% |
| divide            | 0.13% | 
## Load Store Architecture
The Load Store Architecture means the instructions that can access memory are limited. This means that instructions like arithmetic instructions will not be able to access memory.

## Registers
With **over 100 registers** in RISC architecture, from both a hardware and software perspective, there's improvements in performance. In hardware, less read/write traffic to/fro memory leads to faster programs, and smaller code sizes. Compilers that are tuned to work with registers will also see a increase in execution speed of compiled code. This improves memory traffic and code density.

Because they have so much registers, RISC usually uses **3 operands**, instead of 2 used in CISC. Usually one being the register that stores the value, and the other two being the registers that are used to perform the operation.
```arm
ADD R0, R1, R2 ; R0 = R1 + R2
```

## Compiler Optimization
In CISC, although there are many complex instructions available, compilers usually focus on a small set of the fastest instructions, and make routines out of them. This leads to many unused assembly instructions.

Modern compilers have quite a few different optimizations. Re-arranging branches, improving register use, etc.

### Copy propagation
[Copy propagation](https://en.wikipedia.org/wiki/Copy_propagation) is usually run after other optimizations to improve performance. It reduces instructions used by replacing "redundant" operations, where the value is already stored elsewhere.
### Loop unrolling
[Loop unrolling](https://en.wikipedia.org/wiki/Loop_unrolling) unrolls loops that can be hard coded. This is as loops have some "overhead" in order to setup. This is used for loops that have upper and lower limits predefined, and saves time without the programmer needing to unroll it themselves.

If the loop is massive and cannot be completely removed, reducing the amount of iterations the loop goes through can speed up the program too.
```c
for(int i=0; i < 3; i++) {
	x[i] = x[i] ^ 0x22;
}
```
becomes
```c
x[0] = x[0] ^ 0x22;
x[1] = x[1] ^ 0x22;
x[2] = x[2] ^ 0x22;
```
Note that both upper and lower limits are numbers: `0`, and `3`. And not runtime variables.

### Other optimizations
#### Inlining
Changes subroutine calls and inlines them into code. Reduces function call overhead.
#### mul/div replacement
When multiplying or dividing by an (unsigned)integer by a power of `2`, Compilers can optimize them to left/right shifts.
```c
x = y * 2; //same
x = y << 1; //same

x = y / 4; //same
x = y >> 2; //same
```

Divisions can also be changed to multiplication as such: $$\frac{x}{y} = x\cdot{y^{-1}}$$
# ARM Processors
ARM is some CPUs that are based on the RISC architecture, developed by Advanced RISC Machines(ARM). ARM Processors are used a lot in phones, tablets, smartwatches, etc.

Because ARM has an reduced instruction set, the transistors used also decrease. This leads to reduced die sizes, and smaller ICs. It also consumes less power. Making it suitable for smaller devices.
## Processor modes
- User - Unprivileged mode. Most tasks run here
- FIQ - High priority interrupt(aka fast)
- IRQ - Low priority interrupt(aka normal)
- Supervisor - On reset and when software interrupt instruction is executed
- Abort - handle memory violations
- Undef - handle undefined instructions
- System - privileged mode. Uses same registers as User mode

Each mode has access to their own stack space and unique subset of registers. Some operations are only executable in privileged modes. Only User mode is unprivileged.

## ARM Register Set
ARM has a total of 37 registers. They are 32-bits long. These consist of
- 1 program counter
- 1 program status register
- 5 saved program status registers
- 30 general registers

These are not accessible in every mode, however. Generally, each mode has access to:
- A set of r0-r12 registers
- r13(Stack Pointer), r14(Link Register), r15(program counter)
- CPSR(Current Program Status Register)
- SPSR(Saved Program Status Register - Only in privileged mode)
## ARM Cache Features
- Harvard implementation of L1 Caches(Split into Instruction and data caches).
- Cache lockdown - prevents line eviction
- Pesudo-random/[Round-Robin](https://en.wikipedia.org/wiki/Round-robin_scheduling) strategies for replacement
- Non-blocking data cache
- Critical Word First Priority - Send Word to processor first, then finish reading rest of the data into the cache.
- ECC(Error Correcting Code) or parity checking to check for bit-flips in cache.

Level 1 memory system includes L1-caches. Inner cache attributes define the behavior of L1 caches
Level 2 memory system onwards depends on the design of the system. Outer cache attributes define the behavior of L2 caches onward.

## Cortex-A9
- ARMv7-A Arch
	- Thumb[^1]-2, ThumbEE
	- TrustZone
- Variable length Multi-issue pipeline(simultaneous instruction execution)
- Register renaming(helps mitigate data dependencies)
- Branch Prediction
- Return stack
- 64-bit Advanced eXtensible Interface(AXI) and data interfaces
- L1 Data and Instruction caches - 16-64kb, 4-way set-associative

[^1]: Thumb is a 16-bit instruction set thats a subset of ARM

Other features
- PTM instruction trace interface
- IEM power saving support
- Full Jazelle saving support
- VFPv3-D16 Floating-Point Unit (FPU) or NEON™ media processing engine

## Complete ARM-Based system
<!-- and let the copypasta begin! -->
### Processing Core Complex
- Dual ARM Cortex A9
- NEON Extensions
- FPU
- Up to 1GHz

### High Bandwidth Memory
- 56KB OCM
- 32KB L1 / 512KB L2 Cache
- Memory Controllers
	- DDR3/L, DDR2, LPDDR2
	- 2xQSPI, NOR, NAND

### AMBA AXI
- High Bandwidth between PS-PL
- ACP Port
	- Hardware Acceleration

### Integrated Memory Mapped Peripherals
- 2x USB 2.0 OTG w/DMA (High Speed 480Mbps)
- 2x TEMAC w/DMA
- 2x SD/SDIO w/DMA
- 2x UART, 2x CAN, 2xI2C, 2x SPI, GPIO