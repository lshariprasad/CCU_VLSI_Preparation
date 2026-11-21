# Experiment 2D – Hold Static Noise Margin (HSNM) Analysis of a 6T SRAM Cell Using LTspice

![LTspice](https://img.shields.io/badge/Simulation-LTspice-blue)
![VLSI](https://img.shields.io/badge/Domain-VLSI-green)
![Circuit](https://img.shields.io/badge/Circuit-6T%20SRAM-orange)
![Analysis](https://img.shields.io/badge/Analysis-Hold%20SNM-purple)

## 1. Project Overview

This project focuses on the simulation and analysis of a **6-Transistor (6T) Static Random Access Memory (SRAM) cell** using LTspice.

The main objective is to study the stability of an SRAM cell during hold operation and determine its Hold Static Noise Margin (HSNM) using inverter Voltage Transfer Characteristics (VTC) and the butterfly curve method.

The experiment includes:

- CMOS inverter DC voltage transfer characteristic analysis.
- 6T SRAM transistor-level circuit implementation.
- Hold-mode operation verification.
- Butterfly curve construction using mirrored inverter characteristics.
- Hold Static Noise Margin calculation.
- Analysis using generic Level-1 NMOS and PMOS models.

---

## 2. Objectives

1. Understand the working principle of a 6T SRAM cell.
2. Design and simulate a CMOS inverter using LTspice.
3. Obtain the inverter Voltage Transfer Characteristic (VTC).
4. Implement a complete 6T SRAM cell.
5. Verify the stored logic state during hold operation.
6. Construct the SRAM butterfly curve.
7. Calculate the Hold Static Noise Margin (HSNM).
8. Understand the effect of transistor characteristics on SRAM stability.

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
4. The inverter VTC is used to construct the butterfly curve.

### VTC Result

![Inverter VTC](04_Hold_SNM_Inverter_VTC.png)

*Note: Rename the original `04_Hold_SNM_Butterfly.png` file to `04_Hold_SNM_Inverter_VTC.png`. It shows the inverter VTC, not the final butterfly curve.*

---

# 7. Experiment 2D - Part 2: Complete 6T SRAM Hold Verification

A complete 6T SRAM cell was implemented using two cross-coupled CMOS inverters and two NMOS access transistors.

### Hold Mode Conditions

| Signal | Voltage |
|---|---:|
| VDD | 1.8 V |
| WL | 0 V |
| BL | 1.8 V |
| BLB | 1.8 V |
| Initial Q | 1.8 V |
| Initial QB | 0 V |

When WL is LOW, both access transistors are OFF.

The cross-coupled inverter pair retains the previously stored logic state.

### Initial Conditions

```spice
.ic V(Q)=1.8 V(QB)=0
```

### Transient Analysis

```spice
.tran 0 20n 0 10p
```

### Expected Hold State

| Storage Node | Expected Voltage | Logic State |
|---|---:|---|
| Q | Approximately 1.8 V | Logic 1 |
| QB | Approximately 0 V | Logic 0 |

### Simulation Result

![6T SRAM Hold Verification](05_6T_SRAM_Hold_Verification.png)

The transient simulation verifies that the SRAM cell maintains its complementary storage-node voltages during the 20 ns hold interval.

---

# 8. Experiment 2D - Part 3: Butterfly Curve Construction

The SRAM butterfly curve is constructed using the inverter Voltage Transfer Characteristics.

For two identical cross-coupled inverters, the second inverter characteristic can be represented by reflecting the first inverter VTC across the diagonal line:

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

### Second Inverter Characteristic

\[
X=V(QB)
\]

\[
Y=V(QIN)
\]

The two characteristics form the butterfly plot.

### Butterfly Curve

![6T SRAM Hold Butterfly Curve](06_Hold_SNM_Butterfly_Curve.png)

The largest inscribed square inside the smaller butterfly lobe is used to determine the Hold Static Noise Margin.

**Methodological assumption:** The second inverter VTC is assumed to be identical to the extracted first inverter VTC. Its characteristic is therefore generated by exchanging the X and Y coordinates.

---

# 9. Hold Static Noise Margin Calculation

The Hold Static Noise Margin represents the maximum DC noise voltage that can be tolerated by an SRAM cell while preserving its stored data under hold conditions.

The SNM is extracted using the largest-square method on the butterfly curve.

### Calculated Result

| Parameter | Value |
|---|---:|
| Supply Voltage | 1.8 V |
| VTC Data Points | 1801 |
| Input Sweep Step | 1 mV |
| Calculated Hold SNM | 0.6442 V |
| Rounded Hold SNM | **0.644 V** |

### Calculation Method

The maximum-square extraction is performed in rotated coordinates.

The second inverter characteristic is obtained by mirroring the original VTC.

The largest square side length is then calculated from the butterfly characteristics.

### Result

\[
\boxed{HSNM \approx 0.6442\ V}
\]

This result is based on the extracted inverter VTC and the assumption of identical cross-coupled inverters.

It is a simulation result using generic Level-1 MOSFET models.

---

# 10. Observations

1. A 6T SRAM cell consists of six MOSFET transistors.
2. Two cross-coupled CMOS inverters provide bistable data storage.
3. Both access transistors remain OFF during hold operation.
4. The word line is maintained at 0 V during the hold condition.
5. Both bitlines are maintained at 1.8 V.
6. The transient simulation verifies the stored logic state.
7. The inverter VTC is extracted using a DC sweep.
8. The butterfly curve is constructed using the original and mirrored inverter characteristics.
9. The largest inscribed square is used to calculate the Hold Static Noise Margin.

---

# 11. Project Results

The 6T SRAM hold-mode circuit was successfully simulated using LTspice.

The stored logic state was verified through transient analysis:

- Q remains approximately 1.8 V.
- QB remains approximately 0 V.
- Both storage nodes maintain their complementary logic states during the hold interval.

The inverter VTC was extracted using DC sweep analysis.

The butterfly curve was constructed using the mirrored-inverter VTC assumption.

The calculated Hold Static Noise Margin is approximately:

\[
\boxed{HSNM = 0.6442\ V}
\]

---

# 12. Limitations

- The simulation uses generic Level-1 NMOS and PMOS models.
- The MOSFET models are not calibrated to a specific semiconductor fabrication process.
- The simulation does not represent an actual 16-nm FinFET technology.
- The butterfly curve assumes identical cross-coupled inverter characteristics.
- The calculated HSNM is based on the mirrored single-inverter VTC approximation.
- The results should not be interpreted as silicon measurement data.

---

# 13. Repository Structure

```text
Experiment_2D_Hold_SNM/
│
├── README.md
│
├── 04_Hold_SNM_Inverter_VTC.png
├── 04_Hold_SNM_Observations.txt
├── 04_Hold_SNM_VTC.cir
├── 04_Hold_SNM_VTC.log
├── 04_Hold_SNM_VTC.txt
│
├── 05_6T_SRAM_Hold_Verification.cir
├── 05_6T_SRAM_Hold_Verification.log
├── 05_6T_SRAM_Hold_Verification.png
│
├── 06_Hold_SNM_Butterfly_Curve.png
├── 06_Hold_SNM_Butterfly_Data.csv
└── 06_Hold_SNM_Results.txt
```

LTspice `.raw` and `.op.raw` files are generated simulation files. They may be retained locally but are not required in the GitHub repository when the netlists, exported data, logs and plots are included.

---

# 14. Future Work

The next stage of this project is to study the stability of a 6T SRAM cell under read conditions.

Planned experiments:

- Experiment 2E: Read Static Noise Margin (RSNM) Analysis.
- Experiment 2F: Write Static Noise Margin (WSNM) Analysis.
- Experiment 2G: SRAM Read and Write Operation Verification.
- Experiment 2H: Transistor Sizing and SNM Comparison.

---

## Conclusion

This experiment provides a transistor-level introduction to 6T SRAM operation and stability analysis using LTspice.

The work covers CMOS inverter VTC extraction, SRAM hold-state verification, butterfly curve construction and Hold Static Noise Margin calculation.

It establishes a foundation for further exploration of SRAM design, memory stability, transistor sizing and low-power VLSI circuit design.

---

**Project Area:** VLSI Design | CMOS Circuits | SRAM Design | LTspice Simulation

**Experiment:** 2D - Hold Static Noise Margin Analysis of a 6T SRAM Cell
