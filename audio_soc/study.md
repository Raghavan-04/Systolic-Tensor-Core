

# Technical Specification: Custom RISC-V Audio SoC

## 1. Project Overview

The **Audio SoC** is a vertically integrated System-on-Chip designed for real-time digital audio synthesis. The project encompasses a custom-designed 32-bit RISC-V processor, a memory-mapped Pulse Width Modulation (PWM) peripheral, and a complete simulation-to-verification environment.

## 2. System Architecture

The SoC utilizes a **Harvard-style memory architecture** (split instruction and data paths) connected via a simplified system bus.

### 2.1 Block Diagram

### 2.2 Memory Map

The system uses Memory-Mapped I/O (MMIO) to communicate with peripherals.
| Address Range | Component | Description |
| :--- | :--- | :--- |
| `0x0000_0000` | Instruction ROM | 4KB Boot memory (contains firmware) |
| `0x0000_0400` | Audio PWM | 8-bit Sample Register (Output) |
| `0x0000_0800` | Data SRAM | Working memory for audio buffers |

---

## 3. CPU Core: "The Brain"

The core is a **Single-Cycle RISC-V (RV32I)** implementation, optimized for predictability and low-latency control.

* **Instruction Set:** RV32I (Base Integer).
* **Datapath:** Single-cycle, non-pipelined.
* **Register File:** 32 general-purpose 32-bit registers ( hardwired to zero).
* **ALU:** 32-bit Arithmetic Logic Unit supporting ADD, SUB, AND, OR, XOR, and Logical Shifts.

### 3.1 Instruction Lifecycle

1. **Fetch:** Program Counter (PC) retrieves 32-bit instruction from memory.
2. **Decode:** Opcode, Register addresses, and Immediate values are extracted.
3. **Execute:** The ALU computes the result or effective memory address.
4. **Writeback:** Results are retired to the Register File in the same clock cycle.

---

## 4. Audio Peripheral: "The Voice"

The **Audio PWM** module acts as a Digital-to-Analog bridge.

* **Resolution:** 8-bit (256 duty-cycle levels).
* **Mechanism:** Free-running counter compared against a software-controlled `sample_reg`.
* **Output:** PWM signal designed to drive a standard piezo speaker or low-pass filter.

---

## 5. Software & Firmware

The SoC runs bare-metal assembly/C code.

* **Compiler:** `riscv64-unknown-elf-gcc`
* **Boot Flow:** Upon reset, the CPU vectors to `0x0000_0000` and executes the compiled `firmware.hex`.

---

## 6. Verification Flow (Front-End)

The design was verified using a cycle-accurate simulation flow on macOS.

* **Simulator:** Verilator (Verilog to C++ transcompiler).
* **Testbench:** C++ environment providing clock/reset stimuli and VCD (Value Change Dump) generation.
* **Analysis:** GTKWave used for sub-nanosecond timing analysis of the PWM duty cycle and CPU instruction retirement.

---

