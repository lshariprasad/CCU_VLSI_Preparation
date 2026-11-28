# 6T SRAM Cell — Write, Hold and Read Simulation

![LTspice](https://img.shields.io/badge/Simulator-LTspice-blue)
![Circuit](https://img.shields.io/badge/Cell-6T%20SRAM-orange)
![Analysis](https://img.shields.io/badge/Analysis-Transient-success)
![Model](https://img.shields.io/badge/MOSFET-Generic%20Level--1-lightgrey)

This experiment contains an LTspice transient model of a conventional **six-transistor (6T) SRAM cell** with external bit-line drive and precharge switches. It is intended to demonstrate the write, hold, precharge and read phases at a conceptual circuit level.

> **Model scope:** the netlist uses generic Level-1 MOSFET models and ideal voltage-controlled switches. It is an educational circuit, not a foundry-calibrated SRAM macro or a scaled-node/FinFET implementation.

## Contents

| File | Purpose |
|---|---|
| [6T_SRAM_Write_Read_Hold.cir](6T_SRAM_Write_Read_Hold.cir) | LTspice netlist for write, hold, precharge and read sequence |
| [6T_SRAM_Write_Read_Hold.log](6T_SRAM_Write_Read_Hold.log) | Saved LTspice run log |
| [6T_SRAM_Write_Read_Hold.raw](6T_SRAM_Write_Read_Hold.raw) | Saved LTspice waveform data |
| [SRAM_Waveforms.png](SRAM_Waveforms.png) | Waveform screenshot |
| [Simulation_Observations.txt](Simulation_Observations.txt) | Original experiment observations |

## Circuit architecture

A standard 6T SRAM bit cell contains:

- **M1 and M2:** first CMOS inverter, with output node Q and input QB.
- **M3 and M4:** second CMOS inverter, with output node QB and input Q.
- **M5 and M6:** NMOS access devices controlled by word line WL.
- **BL and BLB:** complementary bit lines connected through the access devices.

The cross-coupled inverters provide regenerative feedback and store complementary values at Q and QB while the access devices are off.

## Netlist parameters

| Parameter | Value in supplied netlist |
|---|---:|
| Supply (VDD) | 1.8 V |
| NMOS W / L | 2 µm / 0.18 µm |
| PMOS W / L | 1 µm / 0.18 µm |
| NMOS model | Level 1, VTO = 0.45 V, KP = 200 µA/V², LAMBDA = 0.02 |
| PMOS model | Level 1, VTO = −0.45 V, KP = 100 µA/V², LAMBDA = 0.02 |
| Switch Ron / Roff | 10 Ω / 1 GΩ |
| Switch threshold / hysteresis | 0.9 V / 0.05 V |
| Transient stop time | 16 ns |
| Maximum time step | 5 ps |
| Bit-line capacitance | 20 fF per bit line |

These values describe this educational model only. They are not process-design-kit parameters.

## Timing sequence encoded in the netlist

The PWL sources in the supplied netlist schedule these phases:

| Approx. interval | WL | WE | PRE | Intended operation |
|---|---:|---:|---:|---|
| 0–1 ns | LOW | LOW | LOW | Initial condition / isolation |
| 1–5 ns | HIGH | HIGH | LOW | Write interval |
| 5–6 ns | LOW | LOW | LOW | Hold interval |
| 6.1–9 ns | LOW | LOW | HIGH | Bit-line precharge interval |
| 9.1–13 ns | HIGH | LOW | LOW | Read interval |
| 13–16 ns | LOW | LOW | LOW | Hold after read |

The write drivers are fixed at BLSRC = 0 V and BLBSRC = 1.8 V. The transient sequence therefore attempts to write Q/QB = 0/1 during the write window, subject to the device model and circuit settling.

**Important review point:** verify the actual Q, QB, BL and BLB waveforms before describing the stored polarity or read differential as successful. The precharge source uses an active-high control in this netlist; the switch model and timing should be checked against the plotted voltages. Do not infer a successful read merely from the presence of a waveform image or a completed simulator log.

## How to run in LTspice

1. Download or clone this repository.
2. Open `6T_SRAM_Write_Read_Hold.cir` in LTspice.
3. Run the transient simulation.
4. Add traces for `V(Q)`, `V(QB)`, `V(BL)`, `V(BLB)`, `V(WL)`, `V(WE)` and `V(PRE)`.
5. Inspect the write, hold, precharge and read windows separately.
6. Export the plotted data if you intend to calculate timing or voltage metrics.

### Reproduce from a terminal

If the LTspice command-line executable is available, run the netlist with the batch option supported by your installed LTspice version. The exact executable path differs by operating system and installation. Keep the generated log and raw data with the experiment.

## What to record

For a reproducible result, record:

- LTspice version and operating system.
- Netlist revision / commit.
- Q and QB values before and after each operation.
- BL and BLB values immediately before and during read.
- Word-line and write-enable timing.
- Whether the transient run converged and any warnings.
- Any parameter changes made from the committed netlist.

A read operation is normally evaluated by the bit-line differential and cell-node disturbance, not only by whether the storage nodes appear complementary.

## Limitations and next steps

- Generic Level-1 MOSFET models are not a substitute for a process design kit.
- The model does not include layout parasitics, mismatch, statistical variation, bit-line coupling or a sense amplifier.
- A transient write/read demonstration is **not** a Static Noise Margin (SNM) extraction.
- To study cell stability, use separate hold-mode and read-mode DC transfer-characteristic methods and document the butterfly construction and maximum-square extraction separately.

## Related experiment

See [02_SRAM_SNM](../02_SRAM_SNM/) for inverter VTC, complementary-curve and SRAM stability experiments.

---
*Educational VLSI circuit study — Hari Prasad L S*
