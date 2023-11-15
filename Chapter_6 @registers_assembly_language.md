# Software
Computer programs are abstracted from the hardware via compilers and assemblers, as well as OSes. This allows us to write in high level languages, without needing to handle the complexity of the underlying hardware.

layers of Abstraction
1. High level language(python, java, etc.)
2. Asm language(x86, etc)
3. Machine code(hex instructions, like asm)
4. CPU(micro code)

# Instruction Groups
- Movement Instructions
- Arithmetic Instructions
- Logical Instructions
- Jump Instructions

## Definitions
- 1 byte is 8 bits
- 16 bits / 2 bytes is 1 word(WORD)
- 32 bits / 4 bytes is 1 double word(DWORD)
## Endian
- Little Endian(LE)
	- LSB is stored at a **lower** memory location
	- MSB is stored at a **higher** memory location

This leads to values being stored as such: `0x1234` := `34 12`. Note that this form of reading is unintuitive for us.
- Big Endian(BE)
	- LSB is stored at a **higher** memory location
	- MSB is stored at a **lower** memory location
 
# Registers
Registers are fast, temporary storage for the CPU to perform operations and keep track of data.

Registers help reduce expensive memory accesses, to help keep things fast.
## General registers

| reg | register name       | size    |
| --- | ------------------- | ------- |
| AX  | accumulator         | 16-bits |
| BX  | base                | 16-bits |
| CX  | count               | 16-bits |
| DX  | data                | 16-bits |
For general registers, you can access it's higher or lower bits via smaller, 8-bit registers, that make up the general register.
For example, `AX` is composed of `AL` and `AH`, which accesses the LOW and HIGH segments of the register accordingly.
## Pointer registers

| reg | Pointer registers   | size    |
| --- | ------------------- | ------- |
| SP  | Stack Pointer       | 16-bits |
| BP  | Base pointer        | 16-bits |
| IP  | Instruction Pointer | 16-bits |
IP is inaccessible to programmers, but can still be manipulated with asm instructions.

## Index registers

| reg | Index registers   | size    | 
| --- | ----------------- | ------- |
| SI  | Source Index      | 16-bits |
| DI  | Destination Index | 16-bits |
## Segment registers

| reg | Segment registers | size    | 
| --- | ----------------- | ------- |
| CS  | Code Segment      | 16-bits |
| DS  | Data Segment      | 16-bits |
| SS  | Stack Segment     | 16-bits |
| ES  | Extra Segment     | 16-bits |
These registers are used to address the RAM space, due to RAM space being larger then the 16-bits held by the registers. 
Represented as `SEGMENT:REGISTER`. e.g.: `DS:AX`
### calculating segment offsets
1. Shift left by 4(same effect as multiplying by $2^4$)
2. Add offset
3. profit

## Default segment usage
| offset          | default | usage        |
| --------------- | ------- | ------------ |
| IP              | CS      | Instructions |
| SP              | SS      | Stack        |
| BP              | SS      | Data         |
| BX, SI, DI, etc | DS      | Data         |
### segment override
You can override the segment with instructions like
```x86
MOV AX, ES[SI]
```
## Flags
Flags are used to show what has happened within the processor
- `CF`: Carry flag. Triggered when adding two unsigned numbers that has a carry at MSB. (Can be used as another bit of precision)
- `ZF`: Zero flag. Set to `1` when an operation results in `0`. (Can be used for operations that discard results, like `CMP`(compare))

<sub>EXTRA: `OF`: Overflow flag. Triggers when operating with two signed numbers, and the value rolls over. </sub>
# Addressing modes
| no. | mode                   | asm snippet                                      |
| --- | ---------------------- | ------------------------------------------------ |
| 1   | Register               | `MOV AX, BX`                                     |
| 2   | Immediate              | `MOV AX, 100h`                                   |
| 3   | Direct                 | `MOV AX, [100h]`                                 |
| 4   | Register indirect      | `MOV AX, [BX]`                                   |
| 5   | Indexed                | `MOV AX, [SI]`                                   |
| 6   | Register relative      | `MOV AX, [BX + 4h]` or `MOV AX, 4h[BX]`          |
| 7   | Based indexed          | `MOV AX, [SI + BX]` or `MOV AX, [BX][SI]`        |
| 8   | Relative based indexed | `MOV AX, [SI + BX + 4h]` or `MOV AX, 4h[BX][SI]` | 
REMEMBER: `[AX]` refers to the **ADDRESS** stored in `AX`. If `AX` = `10h`, and address `10h` = `ffh`, `MOV BX, [AX]` will store `ffh` into `BX`. <br>
These addressing modes can be in other assembly instructions, when appropriate
# Assembly
## Arithmetic instructions
- `ADD`: Addition
- `ADC`: Addition(with carry)[^1]
- `SUB`: Subtraction
- `INC`: Increment
- `DEC`: Decrement
- `CMP`: Compare
- `MUL`: Multiply(Unsigned)
- `DIV`: Divide(Unsigned)
- `NEG`: Negate

<sub>NOTE: `IMUL` and `IDIV` are the signed versions of said instructions</sub>

| Instruction | Usage           | Operation                                            |
| ----------- | --------------- | ---------------------------------------------------- |
| `ADD`       | `ADD dest, src` | dest := dest + src                                   |
| `ADC`       | `ADC dest, src` | dest := dest + src + CF                              |
| `SUB`       | `SUB dest, src` | dest := dest - src                                   |
| `INC`       | `INC opr`       | opr := opr + 1                                       |
| `DEC`       | `DEC opr`       | opr := opr - 1                                       |
| `CMP`       | `CMP dst, src`  | dst - src                                            |
| `MUL`       | `MUL opr`       | AX := AL $\times$ opr                                |
| `DIV`       | `DIV opr`       | AX := AL $\div$ opr; AL := quotient, AH := remainder | 
| `NEG`       | `NEG opr`       | opr := -opr                                          |

[^1]: No, it's not Analog Digital Conversion, stop thinking about MAPP
## Logical instructions
- `AND`: AND bits in one operand with accordance to the other operand; `AND dest, src`; `dest &= src`
- `OR`: OR bits in one operand with accordance to the other operand; `OR dest, src`; `dest |= src`
- `NOT`: NOT bits in operand; `NOT opr`; `opr = ~opr`
- `XOR`: XOR bits in one operand with accordance to another operand; `XOR dest, src`; `dest ^= src`
- `SHR`: Shift Right. Shift bits in one operand right, with accordance to the other operand; `SHR dest, sh`; `dest >>= sh`
- `SHL`: Shift Left. Shift bits in one operand left, with accordance to the other operand; `SHL dest, sh`; `dest <<= sh`
- `ROR`: Rotate Right. Rotate bits in one operand right, with accordance to the other operand; `ROR dest, sh`;
- `ROL`: Rotate Left. Rotate bits in one operand left, with accordance to the other operand; `ROL dest, sh`;

## Control instructions
- `CALL`: Calls a function
- `RET`: Returns to callee function
- `JMP`: Jumps to another address to continue execution
- `LOOP`: Decrements `CX`(in 16 bit systems), checks if `CX` = `0h`. If so, continue. Otherwise, does a JMP to offset specified in operand

## Conditional Jumps
Hey... wait a minute
- `JE`/`JZ`: Jumps if Equal/Zero
- `JNE`/`JNZ`: Jumps if not Equal/not Zero
- `JA`: Jump Above. Jumps if 1st operand is > to 2nd operand (unsigned); `CF==0` && `ZF==0`
- `JAE`: Jump Above Equal. Jumps if 1st operand is >= to 2nd operand (unsigned); `CF==0`
- `JB`: Jump Below. Jumps if 1st operand is < to 2nd operand (unsigned); `CF==1`
- `JBE`: Jump Below Equal. Jumps if 1st operand is <= to 2nd operand (unsigned); `CF==1` || `ZF==1`

<sub> Reference: https://www.felixcloutier.com/x86/jcc</sub>
`XCHG`: eXCHanGe. Exchanges values of both operands
## Input/Output Instructions
Instructions used in 8086 to interact with external devices
- `IN`: Input from port; `IN AL, PORT`
- `OUT`: Output to port; `OUT PORT, AX`


`PORT` is from `0x00-0xff`

NOTE: AH is not allowed in `IN` and `OUT`, but `AL` and `AX` are. <!-- why? no clue! --> <br>