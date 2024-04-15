# Worst 8bit CPU

This is a von Neumann 8bit CPU and has a tiny instruction set.

## Specification

* 7 not powerfull instructions: `NOT`, `OR`, `AND`, `ADD`, `JMP`, `SEL`, `CC`
* 3 internal 8bit registers
* 256 Bytes of RAM
* 256 Bytes of ROM for programs
* 8 bit parallel output

## Instruction set

### Instructions

|Instruction| Operand | Description | Cycles |
|---|---|---|---|
| `NOT` | `A` | Not A | 1 |
| `OR` | `A` | Or A | 1 |
| `AND` | `A` |And A | 1 |
| `ADD` | `A` | Full 8 bit adder | 1 |
| `JMP` | `A` | Absolute jump to A address in ROM | 1 |
| `SEL` | `A` | Expose address A of RAM | 1 |
| `CC` | `A` | Clear Carry flag | 1 |

All instructions are 1 byte long and the operand is a 1 byte. How compute "A OR B"?

By using the accumulator modifier that allow to use the accumulator as operand. Let's define "new" instructions:
|Instruction| Operand | Cycles |
|---|---|---|
| `NOT_a` | `A` | 1 |
| `OR_a` | `A` | 1 |
| `AND_a` | `A` | 1 |
| `ADD_a` | `A` | 1 |

So `A OR B` is:
```asm
AND_a 0x00; // clear the accumulator
OR_a A;
OR_a B;
```
Therefore in practice `A OR B` take 3cycles.

### OPCODE
|Instruction| Opcode |
|---|---|
| `NOT` | 0x00 |
| `OR` | 0x01 |
| `AND` | 0x02 |
| `ADD` | 0x03 |
| `JMP` | 0x20 |
| `SEL` | 0x08 |
| `CC` | 0x10 |
| `NOT_a` | 0x04 |
| `OR_a` | 0x05 |
| `AND_a` | 0x06 |
| `ADD_a` | 0x07 |

For `JMP` the operand is the address to jump to. Value must be even. (if odd it can execute data as instruction, that can be dangerous...)

For `SEL` the operand is the address of the RAM to expose.

## Architecture

Von Neumann architecture with 3 internal registers and 256 Bytes RAM and 256 Bytes ROM.

### CPU

![PC in CPU](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/CPU.png?raw=true)

### Program Counter

Program counter is a 8 bit loadable counter with clear input. The overflow output has no purpose is this version. If the program is designed correctly, the program counter will either never overflow, or design to seamlessly from 0xFF address 0x00.

![PC in CPU](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/PC_in_CPU.png?raw=true)

![PC](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/PC.png?raw=true)

Counter use JKFlipFlop.

![PC details](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/PC_details.png?raw=true)

### Fetcher

The fetcher read ROM and alternatively write in instruction or data register. Even address are for instructions and odd for data.

![Fetcher](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/Fetcher.png?raw=true)

### Instruction Decoder

The instruction decoder decode the instruction and set the control signals. Is divided in two part, one for ALU and one for flow control.

![Instruction_decoder](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/Instruction_decoder.png?raw=true)

This is an heterogenous instruction decoder. This helps to alleviate the overwhelming aspects of the design. Only ALU use demux decoder, other control flow use One-hot decoder.

![ALU_Instruction_decoder](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/ALU_instruction_decoder.png?raw=true)

### Compute unit

This unit is the heart of the CPU. It is composed of the ALU, internal register and the RAM.

![compute_unit](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/compute_unit.png?raw=true)

### ALU

The ALU is a simple 8 bit ALU with 4 operations: NOT, OR, AND, ADD.
ALU instruction decoder is directly connected to control logic, It is a 4 to 1 demux.

![ALU](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/ALU.png?raw=true)

### Logic Unit

Logic unit is responsible for `NOT`, `OR` and `AND`.

![LU](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/LU.png?raw=true)

### Full 8bit Adder

This is a 8 bit adder with carry in and carry out. No more, no less.

![AU](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/AU.png?raw=true)

### RAM

Ram with 8 bit address register on the left.

![RAM](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/RAM.png?raw=true)