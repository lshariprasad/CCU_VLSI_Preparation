# SRAM Voltage-Transfer Characteristics and Static Noise Margin Studies

![LTspice](https://img.shields.io/badge/Simulator-LTspice-blue)
![Domain](https://img.shields.io/badge/Domain-VLSI-green)
![Cell](https://img.shields.io/badge/Cell-6T%20SRAM-orange)
![Methods](https://img.shields.io/badge/Methods-VTC%20%7C%20Butterfly%20%7C%20SNM-purple)

This directory collects a sequence of LTspice exercises that move from a CMOS inverter VTC to complementary curves and then to 6T SRAM hold- and read-mode stability studies.

The experiments use generic educational MOSFET models. Treat the results as learning artifacts unless the extraction method, operating point, data and numerical calculation have been independently checked.

## Experiment map

| Experiment | Directory | Main subject | Evidence currently present |
|---|---|---|---|
| 2A | [CMOS inverter VTC](Experiment%202A%20DC%20Analysis%20of%20CMOS%20Inverter%20Voltage%20Transfer%20Characteristics%20(VTC)%20Using%20LTspice/) | DC transfer characteristic of one CMOS inverter | Netlist, log, raw data, PNG and observations |
| 2B | [Complementary curves](Experiment%202B%20%E2%80%94%20SRAM%20Butterfly%20Curve%20Generation/) | Behavioural complementary inverter-like curves | Behavioural netlist, log, raw data, PNG and observations |
| 2C | [Transistor-level SRAM butterfly study](Experiment%202C%20-%20Actual%20SRAM%20Butterfly%20Curve/) | Transistor-based curve-generation attempt | Netlist, log, raw data, PNG and observations |
| 2D | [Hold-mode SNM study](Experiment%202D%20Hold%20Static%20Noise%20Margin%20(SNM)%20Analysis%20of%20a%206T%20SRAM%20Cell%20Using%20LTspice/) | Hold-mode 6T-cell study and VTC data | Netlists, log, raw data, plots and observations |
| 2E | [Read-mode stability / RSNM study](Experiment%202E%20%E2%80%94%20Read%20Static%20Noise%20Margin%20(RSNM)%20Analysis%20of%20a%206T%20SRAM%20Cell%20Using%20LTspice/) | Read-mode DC sweep setup | Netlist, log, raw data, plot and result notes |

## Correct interpretation of the experiments

### 2A — CMOS inverter VTC

The netlist sweeps the inverter input from 0 V to 1.8 V in 1 mV steps and saves the input and output node voltages. This is a single-inverter DC transfer characteristic.

### 2B — Behavioural complementary curves

The netlist uses behavioural voltage sources with logistic expressions to create idealised falling and rising curves. The observation file explicitly describes these as a demonstration, **not an actual SRAM butterfly curve and not an SNM measurement**. Use this experiment to understand curve reflection and plotting only.

### 2C — Transistor-based butterfly attempt

This folder contains a transistor-level netlist and saved plot/data artifacts. A valid 6T SRAM butterfly requires the two cross-coupled inverter transfer characteristics under the correct SRAM bias condition, with one characteristic transformed into the common coordinate system. Inspect the netlist and plotted axes before treating the result as a validated butterfly. The folder title and PNG alone do not prove a correct SNM extraction.

### 2D — Hold-mode stability

The folder contains a complete 6T cell hold-verification transient netlist and a separate DC-sweep/VTC data set. A hold butterfly should represent the cross-coupled cell's transfer characteristics with WL LOW and the bit lines held at the defined hold bias. The maximum square that can be fitted between the two butterfly lobes gives the hold SNM, provided the curves and axes are constructed correctly.

**Do not report the earlier 0.6442 V mirrored-single-inverter estimate as a validated cell-level HSNM.** The root experiment notes identify that number as an educational approximation under an identical-inverter assumption, not a verified six-transistor-cell measurement.

### 2E — Read-mode stability

The read-mode netlist sets WL HIGH and both bit lines HIGH, then sweeps node Q. The saved result note currently states that no verified numerical RSNM has been recorded. Keep that status until the butterfly construction, operating point and maximum-square extraction are reviewed.

## Key concepts

### Voltage-transfer characteristic (VTC)

A CMOS inverter VTC is the relationship between its input voltage and output voltage, (V_{out}=f(V_{in})), obtained here with a DC sweep.

### SRAM butterfly curve

The butterfly plot overlays the voltage-transfer characteristics of the two cross-coupled inverters in a common coordinate system. The second characteristic must be transformed appropriately; simply plotting two arbitrary complementary curves does not establish a physical SRAM butterfly.

### Static Noise Margin (SNM)

SNM is commonly interpreted geometrically as the side length of the largest square that can fit between the two lobes of the butterfly curve.

- **HSNM:** hold static noise margin, with the cell in hold mode (word line disabled).
- **RSNM:** read static noise margin, with the cell in read mode (word line enabled and bit lines precharged).
- **WSNM:** write noise margin is a different writeability metric and is not obtained by re-labelling a hold/read butterfly.

For a defensible numerical extraction, state the device model, supply voltage, transistor sizing, bias condition, curve transformation, interpolation method and square-fitting algorithm.

## Common model used in several experiments

Several supplied netlists use these generic Level-1 models:

```spice
.model NM NMOS (LEVEL=1 VTO=0.45 KP=200u LAMBDA=0.02)
.model PM PMOS (LEVEL=1 VTO=-0.45 KP=100u LAMBDA=0.02)
```

Typical dimensions in the current exercises are NMOS W/L = 2 µm / 0.18 µm and PMOS W/L = 1 µm / 0.18 µm, with VDD = 1.8 V. Check each experiment's own netlist because not every exercise uses the same circuit or bias conditions.

These models are for educational exploration only. They are not foundry models and cannot substantiate a claim about a specific technology node such as 16 nm.

## Recommended workflow

1. Open the experiment's `.cir` file in LTspice.
2. Read the source comments and confirm the bias conditions before running.
3. Run the analysis and inspect the simulator log for errors and warnings.
4. Plot the required node voltages with clearly labelled axes.
5. Export raw data to a documented text/CSV format.
6. Construct the butterfly in a reproducible script or documented LTspice plot setup.
7. Extract the maximum square using interpolation and record the algorithm.
8. Cross-check the numerical result against the plotted geometry.
9. Commit the updated plot, exported data, script and method notes together.

## Reporting checklist

- [ ] LTspice version and operating system recorded.
- [ ] VDD, transistor dimensions and model parameters stated.
- [ ] Correct hold or read bias documented.
- [ ] Both transfer curves use a consistent voltage scale.
- [ ] Curve reflection / coordinate transformation explained.
- [ ] Butterfly plot and source data committed.
- [ ] Maximum-square extraction method documented.
- [ ] Numerical SNM cross-checked against the plot.
- [ ] Model limitations stated; no unsupported process-node claims.

## Related experiment

[01_6T_SRAM](../01_6T_SRAM/) contains the write, hold, precharge and read transient demonstration.

---
*Educational VLSI study notes — Hari Prasad L S*
