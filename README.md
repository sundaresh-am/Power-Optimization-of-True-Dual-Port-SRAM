# Design and Power Optimization of True Dual Port SRAM

## Overview

This project presents the complete design, implementation, simulation, and power optimization of a 12-Transistor (12T) True Dual Port (TDP) SRAM using Cadence Virtuoso in gpdk090 90nm CMOS technology.

Modern VLSI systems such as multi-core processors, networking ASICs, AI accelerators, and embedded SoCs contain large SRAM arrays that consume a major portion of total chip power. As SRAM array sizes increase, continuous switching of long wordlines and bitlines causes high dynamic power dissipation.

This project addresses these challenges by implementing:
- A fully functional 12T True Dual Port SRAM cell
- Simultaneous dual-port read/write operation
- Disturbance-free read operation using a decoupled read path
- Dynamic power optimization using:
  - Wordline Segmentation
  - Clock Gating

The complete hierarchy from the transistor-level 12T SRAM bit cell up to a 1024×16 SRAM array was designed and simulated using Cadence Spectre transient analysis.

---

# Objectives

The primary objectives of this project are:

- To design a 12T True Dual Port SRAM cell capable of simultaneous dual-port operation
- To eliminate read disturbance using a dedicated decoupled read path
- To construct a scalable SRAM hierarchy from single bit cell to 1024×16 array
- To reduce dynamic power consumption in large SRAM arrays
- To implement wordline segmentation to reduce switching capacitance
- To implement clock gating for disabling inactive memory segments
- To compare baseline and optimized SRAM power consumption using transient simulations

---

# 12T SRAM Cell Architecture

The proposed SRAM cell uses a 12-transistor topology consisting of:

## 1. Cross-Coupled Storage Core

The storage core consists of two CMOS inverters connected back-to-back:

- Inverter 1 → M1 (PMOS) + M3 (NMOS)
- Inverter 2 → M2 (PMOS) + M4 (NMOS)

These inverters create a positive feedback latch that stores one bit of data at internal nodes:
- Q
- QB

---

## 2. Dual Write Ports

The SRAM cell contains two independent write ports:

### Port A Write Access
- M5
- M6

### Port B Write Access
- M7
- M8

These transistors allow simultaneous and independent write operations from both ports without interference.

---

## 3. Decoupled Read Path

The read operation is implemented using dedicated read transistors:

### Port A Read Path
- M9
- M10

### Port B Read Path
- M11
- M12

Unlike conventional 6T SRAM cells, the read bitline is not directly connected to the storage node. The read transistors sense the stored value through gate control only, completely eliminating read disturbance.

This improves:
- Stability
- Reliability
- Read robustness
- Low-voltage operation

---

# SRAM Design Hierarchy

The complete SRAM architecture was designed hierarchically.

```text
SRAM_12T_Cell
    └── SRAM_Row_16
            └── SRAM_256x16
                    ├── SRAM_1024x16 (Baseline)
                    └── SRAM_1024x16_OPT (Optimized)
                                └── CLOCK_GATE
```

---

## Hierarchy Description

### SRAM_12T_Cell
Basic transistor-level SRAM bit cell.

### SRAM_Row_16
One row containing 16 SRAM cells connected horizontally.

### SRAM_256x16
256 rows of SRAM_Row_16 assembled into one segmented memory block.

### SRAM_1024x16
Complete baseline SRAM array containing:
- 1024 rows
- 16 columns
- 16,384 SRAM cells
- 196,608 transistors

### SRAM_1024x16_OPT
Optimized SRAM architecture implementing:
- Wordline segmentation
- Clock gating

---

# Power Optimization Techniques

## 1. Wordline Segmentation

In conventional SRAM arrays, a single wordline spans the entire array width. This results in:
- High capacitive loading
- Large switching power
- Increased delay

To solve this problem, the 1024-row array was divided into four independent segments:

```text
Segment 0 → Rows 0–255
Segment 1 → Rows 256–511
Segment 2 → Rows 512–767
Segment 3 → Rows 768–1023
```

Only one segment is activated during a memory access while all other segments remain inactive.

### Benefits
- Reduced switching capacitance
- Lower dynamic power
- Improved scalability
- Reduced unnecessary switching activity

---

## 2. Clock Gating

Clock gating was implemented using AND-gate based CLOCK GATE cells.

### Logic Used

```text
GWL = EN AND WL
```

Where:
- EN = Segment enable signal
- WL = Original wordline
- GWL = Gated wordline

If:

```text
EN = 0
```

Then:

```text
GWL = 0
```

This completely disables switching activity in inactive segments.

### Benefits
- Reduced dynamic power
- Elimination of unnecessary transitions
- Lower switching losses
- Improved energy efficiency

---

# Simulation Environment

The entire project was implemented and simulated using industry-standard Cadence EDA tools.

| Component | Details |
|---|---|
| EDA Tool | Cadence Virtuoso IC617 |
| Simulator | Cadence Spectre |
| Simulation Environment | Cadence ADE L |
| Process Technology | gpdk090 (90nm CMOS) |
| Transistor Models | BSIM3v3 |
| Supply Voltage | 1.2V |
| Operating Frequency | 25 MHz |
| Operating System | Linux / CentOS |

---

# Simulation Setup

## Baseline Testbench

The baseline SRAM testbench was designed to simulate worst-case power consumption conditions.

### Configuration
- All wordlines switch continuously
- Both ports active simultaneously
- Read bitlines precharged using 10kΩ resistors
- 1pF decoupling capacitor connected to VDD
- Simulation duration = 200ns

### Power Measurement

```text
average(1.2 × IT("/V10/MINUS"))
```

---

## Optimized Testbench

The optimized testbench activates only one segment at a time using SEG_EN signals.

### Segment Activation

```text
SEG_EN<0> → 0–50ns
SEG_EN<1> → 50–100ns
SEG_EN<2> → 100–150ns
SEG_EN<3> → 150–200ns
```

This reduces total switching activity significantly.

---

# Results

| Parameter | Baseline | Optimized |
|---|---|---|
| Technology | gpdk090 90nm CMOS | gpdk090 90nm CMOS |
| Supply Voltage | 1.2V | 1.2V |
| Clock Frequency | 25 MHz | 25 MHz |
| Array Size | 1024 × 16 | 1024 × 16 |
| Total Average Power | 78.82 mW | 9.619 mW |
| Power Saved | — | 69.201 mW |
| Power Reduction | — | ~87% |

---

# Design Images

## 12T SRAM Cell
![12T Cell](Power%20Optimization%20of%20TDP%20SRAM/design/12T_Cell.jpeg)

---

## Baseline 1024x16 SRAM Design
![Baseline SRAM](Power%20Optimization%20of%20TDP%20SRAM/design/Baseline%201024x16%20SRAM%20design.jpeg)

---

## Block Diagram
![Block Diagram](Power%20Optimization%20of%20TDP%20SRAM/design/Block%20diagram.jpeg)

---

## Clock Gate using 2-input AND Gate
![Clock Gate](Power%20Optimization%20of%20TDP%20SRAM/design/Clock%20gate%20using%202%20input%20AND%20gate%20design.jpeg)

---

## 256x16 SRAM Design
![256x16 SRAM](Power%20Optimization%20of%20TDP%20SRAM/design/Design%20of%20256x16%20SRAM.jpeg)

---

## Optimized 1024x16 SRAM Design
![Optimized SRAM](Power%20Optimization%20of%20TDP%20SRAM/design/Optimized%20design%20of%201024x16%20SRAM.jpeg)

---

# Testbench Images

## 12T Cell Testbench
![12T Testbench](Power%20Optimization%20of%20TDP%20SRAM/testbench/12T_Cell%20Testbench.jpeg)

---

## Baseline 1024x16 SRAM Testbench
![Baseline Testbench](Power%20Optimization%20of%20TDP%20SRAM/testbench/Baseline%201024x16%20SRAM%20design%20Testbench.jpeg)

---

## Optimized SRAM Testbench
![Optimized Testbench](Power%20Optimization%20of%20TDP%20SRAM/testbench/Optimized%20design%20Testbench.jpeg)

---

# Simulation Waveforms

## 12T Cell Waveform
![12T Waveform](Power%20Optimization%20of%20TDP%20SRAM/waveforms/12T_Cell%20waveform.jpeg)

---

## Baseline 1024x16 SRAM Waveform
![Baseline Waveform](Power%20Optimization%20of%20TDP%20SRAM/waveforms/Baseline%201024x16%20SRAM%20Waveform.jpeg)

---

## Optimized 1024x16 SRAM Waveform
![Optimized Waveform](Power%20Optimization%20of%20TDP%20SRAM/waveforms/Optimized%201024x16%20SRAM%20waveform.jpeg)

---

# Power Analysis

## Baseline Power Result
![Baseline Power](Power%20Optimization%20of%20TDP%20SRAM/results/Baseline_Power.jpeg)

---

## Optimized Power Result
![Optimized Power](Power%20Optimization%20of%20TDP%20SRAM/results/Optimized_Power.jpeg)

The optimized SRAM architecture achieved approximately 87% reduction in total power consumption compared to the baseline design.

---

# Repository Structure

```text
docs/        -> Project report
design/      -> SRAM schematics and architecture
testbench/   -> Testbench screenshots
waveforms/   -> Simulation waveforms
results/     -> Power analysis results
```

---

# Future Enhancements

Several improvements can be implemented in future versions of this project:

- Sense amplifier integration
- Row decoder implementation
- Post-layout simulations
- PVT corner analysis
- Layout optimization
- Larger SRAM array scaling
- Low-voltage SRAM operation
- Advanced leakage reduction techniques

---

# Authors

- Sundaresh A. M.
- Arunansh Arya
- M. Naveen Krishnan
- Sindhu R. G.

---

# Conclusion

This project successfully demonstrates the design and power optimization of a 12T True Dual Port SRAM using wordline segmentation and clock gating techniques.

The optimized architecture significantly reduced dynamic power consumption while maintaining reliable simultaneous dual-port SRAM operation. The design was fully implemented and verified using Cadence Virtuoso and Spectre simulations in gpdk090 90nm CMOS technology.

The project establishes an effective low-power SRAM architecture suitable for modern VLSI memory systems and provides a strong foundation for future SRAM optimization research.
