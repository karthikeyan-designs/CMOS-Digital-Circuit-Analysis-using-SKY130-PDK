# CMOS Digital Circuit Analysis using SKY130 PDK

This repository presents the design, simulation, layout, and characterization of CMOS digital circuits using the SKY130 open-source PDK.  
The work includes device-level MOSFET analysis, CMOS inverter characterization, layout verification, and post-layout simulations using open-source VLSI tools.

---

## 📌 Objectives
- Perform NMOS & PMOS I–V characterization  
- Design and simulate a CMOS inverter  
- Extract VTC, noise margins, rise/fall times, and propagation delays  
- Measure power consumption  
- Create layout using Magic  
- Extract the netlist from the layout  
- Perform LVS (Layout vs Schematic) using Netgen  

---

## 🛠 Tools Used
- **Xschem** – schematic design  
- **Ngspice** – circuit simulation  
- **Magic VLSI** – layout & extraction  
- **Netgen** – LVS verification  
- **SKY130 PDK**

---

# 1️⃣ NMOS & PMOS Characterization


MOSFET I–V characteristics help understand device behavior in linear, saturation, and subthreshold regions.

---

### **Procedure**
- Designed NMOS and PMOS characterization schematics in Xschem  
- Performed DC sweep of **VGS (0–1.8 V)** at fixed **VDS**  
- Simulated using Ngspice  
- Extracted **ID–VGS** and **ID–VDS** curves  

---

---

### **Plots**

#### **NMOS**
**Schematic:**  
![NMOS Schematic](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/nmos_sch.png)

**I–V Characteristics NMOS:**  
![NMOS I–V Plot](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/nmos_wave.png)

**Explanation:**  
In the *ID–VGS* curve, the current stays almost zero when **VGS < VTH** — this is the **cutoff region**, where no channel is formed.  
Once **VGS crosses VTH**, the channel begins to form and the drain current increases; this corresponds to the **linear/ohmic region/Resistive **.  
When the condition **VDS ≥ VGS – VTH** is met, the MOSFET enters **saturation**, visible as the flatter portion of the curve.  
A slight upward slope even in saturation is due to **channel-length modulation**, where ID increases slightly with VDS.

---

#### **PMOS**
**Schematic:**  
![PMOS Schematic](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/pmos3_sch.png)

**I–V Characteristics PMOS:**  
![PMOS I–V Plot](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/pmos_wave.png)

**Explanation:**  
In the *ID–VGS (or ID–VSG)* curve for PMOS, the drain current is nearly zero when **VSG < |VTP|**, indicating the **cutoff region**.  
When **VSG exceeds |VTP|**, a channel forms and the current increases in the negative direction (typical PMOS behavior).  
The device reaches **saturation** when **VSD ≥ VSG – |VTP|**, shown by the flat region in the plot.  
PMOS currents are generally lower than NMOS due to **lower hole mobility**.

### Ngspice Commands (Interactive)

```spice
.dc vgs 0 1.8 1m vds 0 1.8
plot  vgs 
plot  vds
````

# 2️⃣ CMOS Inverter Analysis

The CMOS inverter is the fundamental building block of digital logic.  
Key parameters characterized:

- **Voltage Transfer Curve (VTC)**
- **Switching Threshold (VM)**
- **Noise Margins (NMH, NML)**
- **Rise and Fall Time**
- **Propagation Delays (tpHL, tpLH)**

---

## **Design**
- Inverter designed using **sky130_fd_pr** devices  
- **NMOS width = 1 µm**, **PMOS width = 2 µm**  
- Custom **inverter symbol** created in *Xschem*  
- Testbench prepared for DC sweep and transient analysis  

**Inverter Schematic:**  
![Inverter Schematic](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/inverter_schematic.png)

**Inverter Symbol:**  
![Inverter Symbol](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/SYMBOL_INVERTER.png)

---

## **Procedure**

### **1. VTC (Voltage Transfer Characteristics)**
- Performed **DC sweep** of VIN from 0 V to VDD  
- Used NGSpice measurement:
```spice
.lib
.dc vin 0 2 1m
.save all
.end
````
- Used NGSpice commands
```spice
meas dc vm when vin=vout
````
## **Waveforms**

### **1. VTC (Voltage Transfer Curve)**
![VTC Waveform](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/inv_vtcwave.png)

### **VTC Analysis**

The Voltage Transfer Curve (VTC) characterizes the static behavior of the CMOS inverter.  
It shows how the output voltage (Vout) changes with respect to the input voltage (Vin).  
A key parameter extracted from the VTC is the **switching threshold (VM)**, the point where:

\[
V_{in} = V_{out}
\]

### **Effect of PMOS/NMOS Sizing on VM**

To study device sizing impact, the PMOS width was varied while keeping the NMOS width fixed at **1 µm**:

| NMOS Width | PMOS Width | VM (Measured) |
|------------|------------|---------------|
| 1 µm       | 2 µm       | 0.8698 V      |
| 1 µm       | 4 µm       | 0.8930 V      |

### **Observation**

- Increasing the **PMOS width** shifts the **VM slightly upward**.
- A stronger PMOS increases the pull-up drive, causing the inverter to switch at a **higher input voltage**.

### **Switching Threshold (VM)**

The **switching threshold (VM)** is the input voltage at which the inverter output is exactly at the same voltage level as the input:

\[
V_{in} = V_{out}
\]

At this point, both NMOS and PMOS are partially ON, and their drain currents are equal:

\[
I_{D,n} = I_{D,p}
\]

VM determines the **logic switching point** of the inverter:
- If Vin < VM → Output is HIGH  
- If Vin > VM → Output is LOW  

A properly chosen VM improves noise margins and creates a symmetrical switching characteristic.

The measured values:

- **VM = 0.8698 V** (PMOS = 2 µm)  
- **VM = 0.8930 V** (PMOS = 4 µm)

These results show how **device sizing directly influences the inverter’s switching point**.  
A wider PMOS strengthens the pull-up network, requiring a slightly higher Vin for the NMOS to dominate and switch the output LOW.  
This upward shift is expected and confirms correct transistor behavior.



### **VM Measurement (NGSpice Output)**
![VM Measurement Window](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/vtc_vm.png)

---
## **Noise Margin Analysis**

### **Noise Margin Plot**
![Noise Margin Plot](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/noise_margin.png)

The noise margin defines how much unwanted noise a logic signal can tolerate without causing a wrong output.  
From the VTC, three important points divide the curve into **three operating regions**:

- **VIL = 0.7855 V**  
- **VIH = 1.03344 V**  
- \( VOL \approx 0\,V \), \( VOH \approx 1.8\,V \)

---

### **Three Input Regions of the CMOS Inverter**

#### **1️⃣ Region 1 — Stable Logic LOW (Vin < VIL)**  
When the input voltage is less than **VIL**, the NMOS is almost OFF and the PMOS is ON.  
Example:  
- If **Vin = 0.3 V**, this is well below VIL = 0.7855 V.  
- Therefore, the inverter output is driven strongly HIGH:

\[
V_{out} = VOH \approx 1.8\,V
\]

This region is noise-tolerant to ground-related noise.

---

#### **2️⃣ Region 2 — Transition Region(Indeterminate Region) (VIL < Vin < VIH)**  
Between **0.7855 V** and **1.03344 V**, both PMOS and NMOS conduct.  
- The inverter is switching.  
- A small change in input causes a large change in output (high gain region).  

This region must be avoided in digital logic because noise here can flip the output.

---

#### **3️⃣ Region 3 — Stable Logic HIGH (Vin > VIH)**  
When the input voltage exceeds **1.03344 V**, NMOS is ON and PMOS is OFF.  
Example:  
- If **Vin = 1.4 V**, this is above VIH.  
- Output is pulled strongly LOW:

\[
V_{out} = VOL \approx 0\,V
\]

This region is noise-tolerant to supply-related noise.

---

### **Noise Margins**

Using the standard formulas:

\[
NMH = VOH - VIH = 1.8 - 1.03344 = 0.76656\,V
\]

\[
NML = VIL - VOL = 0.7855 - 0 = 0.7855\,V
\]

So the inverter can tolerate:
- **Up to 0.7855 V of noise on logic LOW**  
- **Up to 0.7666 V of noise on logic HIGH**

These values indicate a robust switching behavior for the designed inverter.

### **Noise Margin Calculation (NGSpice Window)**
![Noise Margin Values](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/nm_values.png)

**Noise Margins:**

### **1. NMH (Noise Margin High)**  
Using the formula:  
\[
NMH = VOH - VIH
\]

Given:  
- \( VOH = 1.8\,V \)  
- \( VIH = 1.03344\,V \)

\[
NMH = 1.8 - 1.03344 = 0.76656\,V
\]

---

### **2. NML (Noise Margin Low)**  
Using the formula:  
\[
NML = VIL - VOL
\]

Given:  
- \( VIL = 0.7855\,V \)  
- \( VOL = 0\,V \)

\[
NML = 0.7855 - 0 = 0.7855\,V
\]

---

### **Final Noise Margins**
- **NML = 0.7855 V**  
- **NMH = 0.76656 V**


---
## **Propagation Delay**

### **Propagation Delay Values (NGSpice Output)**
![Propagation Delay](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/PD_values.png)

### **Propagation Delay Analysis**

Propagation delay defines **how fast a CMOS inverter responds** to a change in its input.  
It is measured between the **50% point of the input transition** and the **50% point of the output transition**.

The two propagation delays are:

- **TPHL** – Output transition **High → Low**
- **TPLH** – Output transition **Low → High**

The **average propagation delay** is:

\[
t_p = \frac{t_{PHL} + t_{PLH}}{2}
\]

---

### **Rise Time (tr) & Fall Time (tf)**

These measure how long the output waveform takes to complete its transitions.

- **Rise Time (tr):** output transition from **10% → 90% of VDD**  
  \[
  t_r = t_{90\%} - t_{10\%}
  \]

- **Fall Time (tf):** output transition from **90% → 10% of VDD**  
  \[
  t_f = t_{90\%} - t_{10\%}
  \]

---

### **Ngspice Measurement Commands**

#### ** TPHL (High → Low output transition)**

```spice
.meas tran vin50  WHEN vin=0.9  RISE=2
.meas tran vout50 WHEN vout=0.9 FALL=2

* tphl = vout50 - vin50
```

### ** . TPLH (Low → High output transition)**
```spice
.meas tran vin50  WHEN vin=0.9  FALL=1
.meas tran vout50 WHEN vout=0.9 RISE=1

* tplh = vout50 - vin50
```


- **tpHL = 3.64e-11 s**  
- **tpLH = 2.7381e-11 s**  
- **Propagation Delay = 3.192e-11 s**

---

## **Rise and Fall Times Measurement**
```spice
.meas tran tr10 WHEN vout=0.16 RISE=1
.meas tran tr90 WHEN vout=1.44 RISE=1

* trise = tr90 - tr10

.meas tran tf10 WHEN vout=0.16 FALL=1
.meas tran tf90 WHEN vout=1.44 FALL=1

* tfall = tf90 - tf10

```

- **Rise Time (trise) = 5.0647e-11 s**  
- **Fall Time (tfall) = 4.046e-11 s**

---
### **Propagation Delay Dependency**
Propagation delay (**tpHL**, **tpLH**) is **not an independent parameter**.  
It depends directly on the **input transition shape**, because the delay is measured from the moment the input crosses **50% of VDD** to the moment the output crosses **50% of VDD**.


#### **Propagation Delay Depends on the Input**
- Slow input transition (low slew rate) → **larger delay**
- Fast input transition (high slew rate) → **smaller delay**
- Delay is affected by:
  - Input slew rate  
  - Load capacitance (CL)  
  - PMOS/NMOS drive strength  
---

### **Rise/Fall Time Independence**

Rise time (**tr**) and fall time (**tf**) are mostly **independent of the input**.  
They depend on the **output node characteristics**, such as:

- Output load capacitance (**CL**)  
- Transistor drive strength  
- Output RC path  



## **Transient Response**
![Transient Waveform](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/transient.png)

# Transient Response Analysis

The transient response characterizes the dynamic switching behavior of the CMOS inverter under time-varying input conditions.

## Key Observations

### 1. Clean Logic Transitions
- The output (red) successfully inverts the input (blue), switching between **0V (logic low)** and **~1.8V (logic high)**
- Sharp transition edges indicate good switching performance

### 2. Propagation Delays
- **tpHL (High-to-Low delay)**: Output falls faster due to stronger NMOS pull-down
- **tpLH (Low-to-High delay)**: Output rises slower due to weaker PMOS pull-up
- This asymmetry is expected given the NMOS/PMOS sizing ratio (1µm vs 2µm/4µm)

### 3. Rise and Fall Times
- **Fall time (tf)**: Sharper/faster transition when output goes low
- **Rise time (tr)**: Slightly slower transition when output goes high
- This confirms the mobility difference between electrons (NMOS) and holes (PMOS)

### 4. Overshoot/Undershoot
Minor spikes at transitions are typical due to:
- Parasitic capacitances and inductances
- Gate charge injection during switching
- These are generally acceptable in digital circuits if within noise margins


## Waveform Analysis

The transient simulation validates:
- Proper inverter operation with full rail-to-rail swing
- Fast switching response suitable for digital applications
- Asymmetric delays consistent with device sizing
- Minimal overshoot/undershoot within acceptable limits

## Interpretation

The transient response shows typical CMOS switching behavior with minor overshoot/undershoot at transition edges. These artifacts result from parasitic capacitances and charge injection during MOSFET switching, and remain well within acceptable noise margins for digital operation.

  


---

## Power Consumption Analysis

Power consumption is calculated by integrating the current drawn from the supply over time and multiplying by the supply voltage.

## Measurement Commands
```spice
.meas tran current_inte integ vdd#branch from=0n to=20n
```

This command integrates the current flowing through the VDD supply over the simulation period (0ns to 20ns).

## Calculation Results

### Current Integration
```
current_inte = -3.12007e-14 A·s
```

**Note:** The negative sign indicates current flowing **into** the supply node. In SPICE convention, current flows from the positive terminal of the voltage source through the circuit. The negative value simply reflects the measurement direction and is expected behavior.

### Power Calculation
```spice
let power_int = current_inte * 1.8
print power_int
```

**Result:**
```
power_int = -5.61612e-14 W·s (or Joules)
```

### Average Power Consumption

To find the average power over the simulation period:
```
Pavg = |power_int| / time_period
Pavg = 5.61612e-14 / 20e-9
Pavg = 2.808 nW
```

## Interpretation

| Parameter | Value | Unit |
|-----------|-------|------|
| **Current Integral** | 3.12007e-14 | A·s |
| **Energy Consumed** | 5.61612e-14 | J (Joules) |
| **Average Power** | ~2.81 | nW |
| **Supply Voltage** | 1.8 | V |

### Key Points

- The **negative sign** in the current measurement is due to SPICE's current direction convention (current flows from the voltage source)
- The **absolute value** is used for power calculations
- The extremely low power consumption (~2.81 nW) indicates:
  - Efficient CMOS operation with minimal static current
  - Power is primarily consumed during switching transitions
  - Negligible leakage current in this technology node

### Power Components

For a CMOS inverter:
```
Ptotal = Pstatic + Pdynamic
Pdynamic = α × CL × VDD² × f
```

Where:
- **Pstatic**: Leakage power (very small in this simulation)
- **Pdynamic**: Switching power (dominant component)
- **α**: Switching activity factor
- **CL**: Load capacitance
- **f**: Switching frequency**

# 3️⃣ Layout, Extraction & LVS

### **Procedure**
- Designed inverter layout in Magic  
- Ensured DRC clean  
- Extracted netlist using `ext2spice`  
- Performed LVS with Netgen  

### **Files Generated**
- `inverter.mag` – layout  
- `extracted.spice` – extracted netlist  
- `comp.out` – LVS comparison report (viewed using less comp.out) 

**Screenshots:**  
_Add layout, DRC, and LVS images here_

---

# 3️⃣ Layout, Extraction & LVS

## Procedure

1. **Layout Design**
   - Designed CMOS inverter layout in **Magic VLSI**
   - Followed DRC (Design Rule Check) guidelines for the 180nm technology
   - Placed NMOS and PMOS transistors with proper spacing and contacts

2. **DRC Verification**
   - Ensured the layout is **DRC clean** (zero violations)
   - Verified metal spacing, poly spacing, and diffusion rules

3. **Netlist Extraction**
   - Extracted parasitic-aware netlist using Magic's `ext2spice` command
   - Includes parasitic capacitances and resistances from interconnects

4. **LVS (Layout vs. Schematic)**
   - Performed LVS check using **Netgen**
   - Verified that the layout matches the schematic netlist
   - Ensured all connections and device parameters are correct

## Commands Used (Magic + Netgen)

# Open Magic with SKY130 PDK
magic -rcfile /usr/local/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc

# Extract layout netlist
extract all
ext2spice lvs
ext2spice

# Run Netgen LVS
netgen -batch lvs INVERTER.spice layout_inv3.spice \
/usr/local/share/pdk/sky130A/libs.tech/netgen/sky130A_setup.tcl

# Check device names
grep -n "sky130_fd_pr__nfet_01v8" INVERTER.spice layout_inv3.spice

# View LVS result
less comp.out


## Files Generated

| File | Description |
|------|-------------|
| `inverter.mag` | Magic layout file |
| `extracted.spice` | Extracted netlist with parasitics |
| `comp.out` | LVS comparison report (viewed using less comp.out)|



## Layout Design

![CMOS Inverter Layout](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/INV_layout.png)

*Figure: CMOS inverter layout designed in Magic VLSI showing NMOS, PMOS, power rails, and metal interconnects*

---

# Post-Layout Simulation

## Overview

Post-layout simulation includes the effects of parasitic capacitances and resistances extracted from the physical layout. This provides a more realistic analysis compared to pre-layout (schematic-level) simulation.

## Procedure

1. **Setup**
   - Used the extracted netlist (`extracted.spice`) from Magic
   - Included parasitic capacitances and resistances in the simulation

2. **Simulations Performed**
   - **Transient Analysis**: Dynamic switching behavior
   - **DC Sweep (VTC)**: Voltage Transfer Characteristics
   - **Power Analysis**: Energy consumption calculation

3. **Comparison**
   - Compared post-layout results with pre-layout simulation
   - Analyzed the impact of parasitics on delay and power

## Simulation Results

### 1. Transient Response

![Post-Layout Transient Waveform](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/post_tran.png)

*Figure: Post-layout transient response showing input (blue) and output (red) waveforms with parasitic effects*

**Observations:**
- Output correctly inverts the input signal
- Transition edges are slightly slower due to parasitic capacitances
- Clean rail-to-rail switching maintained

### 2. Propagation Delay

![Propagation Delay Measurements](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/Layout_prop.png)

*Figure: Terminal output showing propagation delay measurements (tpHL, tpLH, tpd)*

**Key Metrics:**
- **tpHL**: High-to-Low propagation delay
- **tpLH**: Low-to-High propagation delay  
- **tpd**: Average propagation delay = (tpHL + tpLH) / 2

**Impact of Parasitics:**
- Post-layout delays are higher than pre-layout due to:
  - Wire capacitance
  - Contact resistance
  - Interconnect RC delays

### 3. Voltage Transfer Characteristics (VTC)

![VTC Waveform](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/Layout_tran.png)

*Figure: Post-layout VTC curve showing the DC transfer characteristic*

![VTC Measurement Window](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/layout_vm.png)

*Figure: ngspice window showing VTC analysis and switching threshold (Vm)*

**Analysis:**
- Switching threshold (Vm) indicates the symmetry of the inverter
- Sharp transition region confirms good noise margins
- VOH and VOL remain close to VDD and GND respectively

### 4. Power Consumption

![Power Consumption Analysis](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/layout_power.png)

*Figure: Power consumption calculation from post-layout simulation*

**Power Analysis:**
- Includes both dynamic and static power dissipation
- Parasitic capacitances increase dynamic power consumption
- Energy per switching cycle accounts for layout parasitics

## Post-Layout vs Pre-Layout Comparison

| Parameter | Pre-Layout | Post-Layout | Impact |
|-----------|------------|-------------|--------|
| **tpHL** | Lower | Higher | +Δt due to parasitics |
| **tpLH** | Lower | Higher | +Δt due to parasitics |
| **Power** | Lower | Higher | +ΔP due to parasitic C |
| **Vm** | Nominal | Slightly shifted | Asymmetry from parasitics |

## Conclusion

The post-layout simulation demonstrates:
- ✅ **Functional correctness** with parasitic effects included
- ✅ **Realistic performance metrics** accounting for physical layout
- ✅ **Increased delays** due to interconnect parasitics (expected behavior)
- ✅ **Higher power consumption** from parasitic capacitances

The layout is verified to be **DRC clean** and **LVS matched**, ready for fabrication.

---

## 📌 Conclusion
This project demonstrates a complete open-source digital design flow using the SKY130 PDK, covering device-level MOSFET behavior, CMOS inverter analysis, layout verification, and post-layout performance evaluation.

---

## 📁 Repository Structure (Suggested)
