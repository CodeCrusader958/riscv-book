# Chapter 6 – RV32I Non-Pipelined Processor IP

## Objective

The objective of this chapter is to design a complete **RV32I Non-Pipelined Processor** using AMD Vitis HLS. Unlike Chapter 5, which only implemented the Fetch stage, this design integrates instruction fetching, decoding, execution, memory access, and program control into a single hardware IP.

The generated processor is synthesized into RTL and exported as a Vivado-compatible IP that can be integrated into a Zynq-based FPGA system.

---

# Design Flow

```text
Instruction Memory
        │
        ▼
     Fetch
        │
        ▼
     Decode
        │
        ▼
 Immediate Generation
        │
        ▼
    Execute
        │
        ▼
Register / Memory Update
        │
        ▼
Program Counter Update
        │
        ▼
Repeat until RET
```

---

# Project Structure

```text
rv32i_npp_ip
│
├── rv32i_npp_ip.cpp
├── fetch.cpp
├── decode.cpp
├── immediate.cpp
├── execute.cpp
├── emulate.cpp
├── disassemble.cpp
├── type.cpp
├── testbench
└── solution1
```

| File | Description |
|------|-------------|
| `rv32i_npp_ip.cpp` | Top-level HLS processor module |
| `fetch.cpp` | Reads instructions from instruction memory |
| `decode.cpp` | Decodes opcode, registers, function fields and immediate values |
| `immediate.cpp` | Generates immediates for I, S, B, U and J instruction formats |
| `execute.cpp` | Executes ALU, branch, load/store and PC update operations |
| `disassemble.cpp` | Prints assembly instructions during C Simulation (Debug only) |
| `emulate.cpp` | Prints register/memory updates during C Simulation (Debug only) |
| `type.cpp` | Determines the instruction format (R/I/S/B/U/J) |

---

# Top-Level Design (`rv32i_npp_ip.cpp`)

The `rv32i_npp_ip()` function is synthesized as the top hardware module.

Unlike Chapter 5, the processor now contains:

- Program Counter (PC)
- Register File (32 Registers)
- Instruction Memory
- Data Memory
- Instruction Counter

The register file is initialized before execution, and the processor starts execution from the supplied `start_pc`.

---

## Processor Execution Flow

```text
Initialize Register File
        │
        ▼
Load start_pc
        │
        ▼
Fetch Instruction
        │
        ▼
Decode Instruction
        │
        ▼
Execute Instruction
        │
        ▼
Update Instruction Count
        │
        ▼
RET ?
   │
 ┌─┴─────────────┐
 │               │
No              Yes
 │               │
 ▼               ▼
Repeat        Stop
```

The processor executes instructions until the termination condition is satisfied (`RET` instruction with PC equal to zero). :contentReference[oaicite:1]{index=1}

---

# Hardware Interfaces

The processor exposes its interfaces using HLS pragmas.

```cpp
#pragma HLS INTERFACE s_axilite port=start_pc
#pragma HLS INTERFACE s_axilite port=code_ram
#pragma HLS INTERFACE s_axilite port=data_ram
#pragma HLS INTERFACE s_axilite port=nb_instruction
#pragma HLS INTERFACE s_axilite port=return
```

These directives automatically generate an AXI4-Lite Slave interface, allowing software running on the Processing System (PS) to:

- Initialize the Program Counter
- Load the instruction memory
- Access the data memory
- Read the total number of executed instructions

---

# Pipeline Configuration

Inside the processor loop, the following pragma is applied:

```cpp
#pragma HLS PIPELINE II=7
```

This instructs Vitis HLS to pipeline the processor loop with an **Initiation Interval (II) of 7**, meaning a new iteration can begin every seven clock cycles. The design is still considered **non-pipelined at the processor architecture level**, as instructions complete sequentially; the pragma optimizes the generated hardware schedule. :contentReference[oaicite:2]{index=2}

---

# Source Code

> **Screenshot Placeholder**

```text
[ Insert rv32i_npp_ip.cpp Screenshot ]
```

---

# C Simulation

C Simulation verifies the functional correctness of the processor before RTL generation.

During simulation:

- Instructions are fetched from `code_ram`
- Each instruction is decoded
- The corresponding operation is executed
- Registers and memory are updated
- Execution continues until a `RET` instruction is encountered

When debug macros are enabled, the simulator can additionally:

- Print the disassembled instruction sequence
- Display register updates
- Display memory updates

These debug features are active only during C Simulation and are excluded from hardware synthesis. 

---

## C Simulation Output

> **Screenshot Placeholder**

```text
[ Insert C Simulation Screenshot ]
```

---

# C Synthesis

C Synthesis converts the complete RV32I processor into synthesizable RTL.

During synthesis, Vitis HLS performs:

- Scheduling
- Resource Binding
- Register Allocation
- FSM Generation
- RTL Generation
- AXI Interface Generation

Compared to Chapter 5, the synthesized hardware is significantly larger because it includes instruction decoding, immediate generation, ALU operations, memory access, branch logic, and register file management in addition to the Fetch stage.

---

## C Synthesis

> **Screenshot Placeholder**

```text
[ Insert C Synthesis Screenshot ]
```

---

# Key Observations

- A complete RV32I processor was implemented using C++.
- Register file, instruction memory, and data memory are integrated into the design.
- Instruction execution continues until the RET instruction terminates the program.
- Debug modules (`disassemble` and `emulate`) are used only during simulation and are excluded from synthesized hardware.
- The generated design is exported as a reusable Vivado IP for FPGA integration.

---

# Function Call Graph

The Function Call Graph represents the hierarchy of hardware modules generated from the C++ implementation. Each major function is synthesized as an independent hardware block while preserving the execution order defined in the top-level function.

The generated hierarchy is shown below.

```text
rv32i_npp_ip
        │
        ▼
rv32i_npp_ip_Pipeline_VITIS_LOOP
        ├── fetch()
        ├── decode()
        ├── execute()
        ├── statistic_update()
        └── running_cond_update()
```

### Module Description

| Module | Function |
|---------|----------|
| `rv32i_npp_ip` | Top-level processor generated by Vitis HLS |
| `fetch()` | Reads an instruction from instruction memory |
| `decode()` | Extracts opcode, register fields, instruction type, and immediate value |
| `execute()` | Performs ALU, branch, load/store and PC update operations |
| `statistic_update()` | Updates the executed instruction counter |
| `running_cond_update()` | Checks whether execution should continue |

Unlike Chapter 5, the processor now contains multiple functional stages instead of only the Fetch stage, resulting in a larger hardware hierarchy.

---

## Function Call Graph

> **Screenshot Placeholder**

```text
[ Insert Function Call Graph Screenshot ]
```

---

# Schedule Viewer

The Schedule Viewer displays how Vitis HLS schedules the generated hardware operations across clock cycles.

Unlike Chapter 5, the scheduler now executes multiple processor stages inside the loop.

The scheduled execution sequence is:

```text
Read Program Counter
        │
        ▼
Fetch Instruction
        │
        ▼
Decode Instruction
        │
        ▼
Execute Instruction
        │
        ▼
Update Instruction Counter
        │
        ▼
Check Running Condition
        │
        ▼
Repeat
```

### Schedule Analysis

The Schedule Viewer contains:

- **Blue Bar** – Represents the processor loop generated by HLS.
- **Green Bars** – Represent synthesized hardware functions (`fetch`, `decode`, `execute`, etc.).
- **Gray Blocks** – Register reads, writes, branch decisions and control operations.
- **Purple Dependency Arrows** – Show data dependencies between operations, ensuring an operation starts only after the required data from the previous stage is available.

The execution order is preserved through these dependencies:

```text
Fetch
   │
   ▼
Decode
   │
   ▼
Execute
   │
   ▼
Instruction Counter Update
   │
   ▼
Running Condition Check
```

The processor loop is synthesized with:

```cpp
#pragma HLS PIPELINE II=7
```

Therefore, Vitis schedules loop iterations with an **Initiation Interval (II) of 7**, allowing a new loop iteration to begin every seven clock cycles while maintaining sequential instruction execution.

---

## Schedule Viewer

> **Screenshot Placeholder**

```text
[ Insert Schedule Viewer Screenshot ]
```

---

# Synthesis Summary

The Synthesis Summary provides timing estimation, scheduling information, and FPGA resource utilization for the generated processor.

## Timing Analysis

| Parameter | Value |
|-----------|-------|
| Target Clock | **10.00 ns** |
| Estimated Delay | **10.358 ns** |
| Clock Uncertainty | **2.70 ns** |

The estimated delay exceeds the target clock period, resulting in a timing violation.

This indicates that the processor cannot meet the specified **100 MHz** timing constraint without further optimization.

---

## Resource Utilization

> **Insert Screenshot Here**

```text
[ Insert Synthesis Summary Screenshot ]
```

The synthesis report also provides:

- LUT utilization
- Flip-Flop utilization
- BRAM utilization
- DSP utilization
- Pipeline information
- Loop latency
- Initiation Interval (II)

These values provide an estimate of the FPGA resources required by the generated processor.

---

# Hardware Interfaces

The generated IP exposes an AXI4-Lite Slave interface for software control.

The generated control registers include:

- AP_START
- AP_DONE
- AP_IDLE
- AP_READY
- GIER
- IP_IER
- IP_ISR

The following software arguments are automatically mapped into hardware interfaces.

| Software Argument | Hardware Interface |
|-------------------|-------------------|
| `start_pc` | AXI4-Lite Register |
| `code_ram` | AXI Memory Interface |
| `data_ram` | AXI Memory Interface |
| `nb_instruction` | AXI Register |

This enables software running on the Zynq Processing System to initialize the processor, load instructions, access data memory, and retrieve the total number of executed instructions.

---

# Vivado Integration

After successful RTL generation, the processor is exported as a **Vivado IP**.

The generated IP is integrated into a Zynq Block Design consisting of:

```text
ZYNQ Processing System
          │
          ▼
    AXI Interconnect
          │
          ▼
     rv32i_npp_ip
```

The Processing System communicates with the custom RV32I processor through the AXI4-Lite interface generated by Vitis HLS.

---

## Vivado Block Design

> **Screenshot Placeholder**

```text
[ Insert Vivado Block Design Screenshot ]
```

---

# RTL Export

The generated RTL is packaged as a reusable Vivado IP.

Exporting RTL automatically generates:

- Synthesizable RTL (Verilog/VHDL)
- IP Packaging Files
- AXI Interface Description
- Driver Metadata
- Vivado Project Support Files

The packaged IP can be instantiated directly inside Vivado IP Integrator for FPGA implementation.

---

## RTL Export

> **Screenshot Placeholder**

```text
[ Insert RTL Export Screenshot ]
```

---

# Conclusion

This chapter extends the simple Fetch IP developed in Chapter 5 into a complete **RV32I Non-Pipelined Processor**.

The processor integrates instruction fetching, decoding, execution, register management, data memory access, and program control within a single hardware IP generated using AMD Vitis HLS.

The design was successfully:

- ✔ Verified using C Simulation
- ✔ Synthesized into RTL
- ✔ Analyzed for timing and scheduling
- ✔ Packaged as a reusable Vivado IP
- ✔ Integrated into a Zynq-based Vivado Block Design

This processor serves as the foundation for the architectural enhancements introduced in the subsequent chapters, including multicycle execution and pipelined processor design.
