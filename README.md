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

### Program Counter

Program counter is a 8 bit loadable counter with clear input.

![PC in CPU](https://github.com/AntoninPvr/8bit_cpu/blob/main/images/PC_in_CPU.png?raw=true)

