# Experiment 2D – 6T SRAM Hold-Mode Simulation and Preliminary VTC-Based SNM Estimate Using LTspice

![LTspice](https://img.shields.io/badge/Simulation-LTspice-blue)
![VLSI](https://img.shields.io/badge/Domain-VLSI-green)
![Circuit](https://img.shields.io/badge/Circuit-6T%20SRAM-orange)
![Analysis](https://img.shields.io/badge/Analysis-Hold%20Mode-purple)

## 1. Project Overview

This experiment documents a 6T SRAM hold-mode simulation and an educational estimate derived from a mirrored single-inverter voltage-transfer characteristic (VTC).

The repository contains a complete 6T SRAM hold-transient netlist. Separately, the value previously recorded as 0.6442 V comes from mirroring one inverter VTC under an identical-inverter assumption. **It is not a validated cell-level 6T SRAM Hold Static Noise Margin (HSNM) result.**

All simulations use generic educational Level-1 MOSFET models. They are not foundry-calibrated models and do not represent a 16-nm FinFET process.

The experiment currently includes:

- CMOS inverter DC VTC extraction.
- A complete 6T SRAM hold-mode transient netlist.
- A mirrored single-inverter VTC approximation for learning the butterfly-curve concept.
- A clearly labelled preliminary estimate, with full-cell HSNM extraction still requiring validation.

## 2. Objectives

1. Understand the working principle of a 6T SRAM cell.
2. Design and simulate a CMOS inverter using LTspice.
3. Obtain the inverter Voltage Transfer Characteristic (VTC).
4. Implement a complete 6T SRAM hold-mode circuit.
5. Set up transient analysis for the stored state during hold operation.
6. Explore how a mirrored inverter VTC forms an educational butterfly-like approximation.
7. Distinguish a VTC-based estimate from a validated full-cell HSNM extraction.
8. Identify the additional methodology and evidence needed for cell-level stability results.

---

## 3. Software and Tools

| Tool | Purpose |
|---|---|
| LTspice 26.0.1 | Circuit simulation |
| Windows | Simulation environment |
| CSV / Text data | Numerical result storage |
| Python | Butterfly curve generation and numerical analysis |

---

## 4. 6T SRAM Cell Architecture

A conventional 6T SRAM cell consists of:

- Two cross-coupled CMOS inverters.
- Two NMOS access transistors.
- Two complementary bitlines (BL and BLB).
- One word line (WL).

### Transistor Configuration

| Transistor | Type | Function |
|---|---|---|
| M1 | NMOS | Pull-down transistor |
| M2 | PMOS | Pull-up transistor |
| M3 | NMOS | Pull-down transistor |
| M4 | PMOS | Pull-up transistor |
| M5 | NMOS | Access transistor |
| M6 | NMOS | Access transistor |

The two cross-coupled inverters store complementary logic values at nodes Q and QB.

The access transistors connect the storage nodes to the bitlines when the word line is enabled.

---

## 5. Simulation Parameters

| Parameter | Value |
|---|---|
| SRAM Architecture | 6T SRAM |
| Supply Voltage (VDD) | 1.8 V |
| NMOS Width | 2 µm |
| NMOS Length | 0.18 µm |
| PMOS Width | 1 µm |
| PMOS Length | 0.18 µm |
| NMOS Threshold Voltage | 0.45 V |
| PMOS Threshold Voltage | -0.45 V |
| NMOS Model | Generic Level-1 |
| PMOS Model | Generic Level-1 |
| Operating Mode | Hold Mode |

### MOSFET Model Parameters

```spice
.model NM NMOS (LEVEL=1 VTO=0.45 KP=200u LAMBDA=0.02)

.model PM PMOS (LEVEL=1 VTO=-0.45 KP=100u LAMBDA=0.02)
```

These are generic educational MOSFET models and are not foundry-calibrated process models.

---

# 6. Experiment 2D - Part 1: Inverter VTC Analysis

The first part of the experiment extracts the Voltage Transfer Characteristic of a CMOS inverter.

### Simulation Conditions

| Parameter | Value |
|---|---|
| Input Sweep | 0 V to 1.8 V |
| Step Size | 1 mV |
| Word Line | 0 V |
| Bitline (BL) | 1.8 V |
| Complementary Bitline (BLB) | 1.8 V |
| Analysis | DC Sweep |

The input voltage is swept from 0 V to 1.8 V, and the corresponding inverter output voltage is observed.

The access transistors remain OFF because the word line is maintained at 0 V.

### Netlist

File: `04_Hold_SNM_VTC.cir`

The LTspice netlist is included in this repository.

### Observations

1. The inverter output remains HIGH for a LOW input voltage.
2. As the input voltage increases, the output voltage decreases.
3. A sharp transition occurs near the inverter switching region.
4. A mirrored copy of the inverter VTC can illustrate the butterfly-curve concept, but it is only an approximation and is not a full-cell 6T SRAM extraction.

### VTC Result

![Inverter VTC](02_SRAM_SNM/Experiment%202D%20Hold%20Static%20Noise%20Margin%20(SNM)%20Analysis%20of%20a%206T%20SRAM%20Cell%20Using%20LTspice/04_Hold_SNM_Butterfly.png)

*Repository note: the tracked image retains the legacy filename `04_Hold_SNM_Butterfly.png`, but this experiment section identifies it as the inverter VTC. The filename does not mean it is the final butterfly plot.*

---

# 7. Experiment 2D - Part 2: Complete 6T SRAM Hold-Mode Transient Setup

The file `05_6T_SRAM_Hold_Verification.cir` contains two cross-coupled CMOS inverters and two NMOS access transistors, forming a complete 6T SRAM cell.

### Hold Mode Conditions

| Signal | Voltage |
|---|---:|
| VDD | 1.8 V |
| WL | 0 V |
| BL | 1.8 V |
| BLB | 1.8 V |
| Initial Q | 1.8 V |
| Initial QB | 0 V |

When WL is LOW, both access transistors are intended to be OFF.

### Initial Conditions

```spice
.ic V(Q)=1.8 V(QB)=0
```

### Transient Analysis

```spice
.tran 0 20n 0 10p
```

### Simulation Artifact

![6T SRAM Hold Verification](02_SRAM_SNM/Experiment%202D%20Hold%20Static%20Noise%20Margin%20(SNM)%20Analysis%20of%20a%206T%20SRAM%20Cell%20Using%20LTspice/05_6T_SRAM_Hold_Verification.png)

The netlist and simulation artifacts are committed. The LTspice log records successful operating-point convergence. Inspect the transient waveform and rerun the netlist locally before quoting exact node voltages or treating the plot as an independently validated result.

# 8. Mirrored-Inverter VTC Construction (Educational Approximation)

For two identical cross-coupled inverters, the second inverter characteristic can be approximated by reflecting the first inverter VTC across the diagonal:

\[
Y=X
\]

### First Inverter Characteristic

\[
X=V(QIN)
\]

\[
Y=V(QB)
\]

### Mirrored Characteristic

\[
X=V(QB)
\]

\[
Y=V(QIN)
\]

**Artifact status:** The existing `04_Hold_SNM_Butterfly.png` is referenced above as the inverter VTC. A separate full-cell 6T SRAM hold butterfly plot is not documented as an exported artifact in this experiment folder.

**Important limitation:** Mirroring one inverter VTC assumes identical inverter transfer characteristics. It does not replace extracting both sides of the butterfly curve from the complete 6T cell under the correct hold conditions. Any value calculated from this approximation must be labelled an estimate, not a validated 6T SRAM HSNM.

# 9. Preliminary Mirrored-VTC Estimate (Not Validated 6T SRAM HSNM)

The following value is retained as a preliminary educational estimate from the extracted single-inverter VTC and its mirrored characteristic. It must not be reported as a validated cell-level 6T SRAM HSNM.

| Parameter | Value |
|---|---:|
| Supply Voltage | 1.8 V |
| VTC Data Points | 1801 |
| Input Sweep Step | 1 mV |
| Mirrored-inverter VTC estimate | 0.6442 V |

### Method and assumptions

- The source VTC is extracted using a DC sweep.
- The second characteristic is generated by exchanging the X and Y coordinates of the same VTC.
- This assumes identical cross-coupled inverter characteristics.
- The estimate is based on generic Level-1 educational MOSFET models.

**Status:** The full-cell 6T SRAM HSNM has not been validated by a butterfly curve extracted from the complete cell. Do not describe 0.6442 V as a measured, foundry-calibrated, or validated SRAM HSNM result.

# 10. Observations

1. A conventional 6T SRAM cell contains six MOSFETs.
2. Two cross-coupled CMOS inverters provide bistable data storage.
3. Both access transistors are intended to remain OFF when WL is 0 V.
4. The complete 6T hold-mode transient netlist is present in the repository.
5. The inverter VTC is extracted separately using a DC sweep.
6. A mirrored single-inverter VTC can illustrate the butterfly-curve concept.
7. The mirrored-VTC estimate depends on the identical-inverter assumption.
8. A full-cell butterfly extraction and validated largest-square result are still required before reporting cell-level HSNM.

# 11. Project Results and Validation Status

The repository contains a complete 6T SRAM hold-mode netlist and a 20 ns transient-analysis setup. Its LTspice log records successful operating-point convergence. The transient waveform should be inspected locally before reporting exact storage-node values.

The separately extracted inverter VTC contains 1801 sweep points at a 1 mV step. A value of 0.6442 V was previously recorded from a mirrored-single-inverter VTC calculation.

**That 0.6442 V value is a preliminary VTC-based estimate, not a validated 6T SRAM HSNM result.** Full-cell HSNM remains to be extracted and checked using the complete SRAM cell's hold-mode butterfly characteristics and a documented largest-square procedure.

# 12. Limitations

- The simulation uses generic Level-1 NMOS and PMOS models.
- The MOSFET models are not calibrated to a specific semiconductor fabrication process.
- The simulation does not represent an actual 16-nm FinFET technology.
- The 0.6442 V value comes from a mirrored single-inverter VTC approximation.
- A full-cell 6T SRAM hold butterfly curve and validated HSNM extraction are not yet documented.
- The available files do not establish silicon measurement data or foundry-calibrated results.

# 13. Repository Structure

The Experiment 2D files are stored in:

`02_SRAM_SNM/Experiment 2D Hold Static Noise Margin (SNM) Analysis of a 6T SRAM Cell Using LTspice/`

Key files in that directory:

- `04_Hold_SNM_VTC.cir` - decoupled inverter VTC extraction setup.
- `04_Hold_SNM_VTC.log` and `04_Hold_SNM_VTC.raw` - associated LTspice simulation artifacts.
- `04_Hold_SNM_Butterfly.png` - the image referenced above as the inverter VTC.
- `04_Hold_SNM_Observations.txt` - scope and limitations of the preliminary estimate.
- `05_6T_SRAM_Hold_Verification.cir` - complete 6T hold-mode transient netlist.
- `05_6T_SRAM_Hold_Verification.log` and `.raw` - associated LTspice artifacts.
- `05_6T_SRAM_Hold_Verification.png` and `05_6T_SRAM_Hold_New_Verification.png` - exported plots.

# 14. Future Work

### Experiment 2E - Read Static Noise Margin (RSNM)

The repository contains an initial read-mode netlist and a results template. The numerical RSNM field is blank, and the extraction methodology has not been independently validated. Treat this experiment as in progress; do not report an RSNM value yet.

### Planned experiments

- Experiment 2F: Write Static Noise Margin (WSNM) Analysis.
- Experiment 2G: SRAM Read and Write Operation Verification.
- Experiment 2H: Transistor Sizing and SNM Comparison.

## Conclusion

This experiment provides an educational introduction to CMOS inverter VTC extraction and 6T SRAM hold-mode simulation using LTspice.

A mirrored-single-inverter VTC estimate is retained with explicit assumptions, but it is not presented as a validated full-cell HSNM result. Full-cell hold-SNM extraction and the read/write stability studies remain work to be completed and verified.

---

**Project Area:** VLSI Design | CMOS Circuits | SRAM Design | LTspice Simulation

**Experiment:** 2D - 6T SRAM Hold-Mode Simulation and Preliminary VTC-Based Estimate
