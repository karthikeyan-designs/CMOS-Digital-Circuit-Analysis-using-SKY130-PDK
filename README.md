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

### **Concept**
MOSFET I–V characteristics help understand device behavior in linear, saturation, and subthreshold regions.

---

### **Procedure**
- Designed NMOS and PMOS characterization schematics in Xschem  
- Performed DC sweep of **VGS (0–1.8 V)** at fixed **VDS**  
- Simulated using Ngspice  
- Extracted **ID–VGS** and **ID–VDS** curves  

---

### **Results**  
(Replace values accordingly)

| Device | Vth (approx) | Idsat |
|--------|--------------|--------|
| NMOS   | XXX V        | XXX µA |
| PMOS   | XXX V        | XXX µA |

---

### **Plots**

#### **NMOS**
**Schematic:**  
![NMOS Schematic](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/nmos_sch.png)

**I–V Characteristics NMOS:**  
![NMOS I–V Plot](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/nmos_wave.png)

---

#### **PMOS**
**Schematic:**  
![PMOS Schematic](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/pmos3_sch.png)

**I–V Characteristics PMOS:**  
![PMOS I–V Plot](CMOS_DIGITAL_ANALYSIS_SCREENSHOTS/pmos_wave.png)


# 2️⃣ CMOS Inverter Analysis

### **Concept**
The CMOS inverter is the basic digital logic element.  
Important parameters:
- Voltage Transfer Curve (VTC)  
- Switching threshold (VM)  
- Noise margins (NMH, NML)  
- Rise time, fall time  
- Propagation delay (tpHL, tpLH)  

### **Procedure**
- Designed inverter using sky130_fd_pr models  
- Performed DC sweep for VTC  
- Applied pulse input for transient simulation  

### **Results**  
(Replace values)

- Switching threshold, VM ≈ **XXX V**  
- Noise Margins:  
  - NMH = **XXX V**  
  - NML = **XXX V**  
- Propagation Delay:  
  - tpHL = **XXX ps**  
  - tpLH = **XXX ps**  
- Rise time = **XXX ns**  
- Fall time = **XXX ns**

**Waveforms:**  
_Add VTC and transient images here_

---

# 3️⃣ Layout, Extraction & LVS

### **Procedure**
- Designed inverter layout in Magic  
- Ensured DRC clean  
- Extracted netlist using `ext2spice`  
- Performed LVS with Netgen  

### **Files Generated**
- `inverter.mag` – layout  
- `extracted.spice` – extracted netlist  
- `lvs_report.log` – LVS comparison  

**Screenshots:**  
_Add layout, DRC, and LVS images here_

---

# 4️⃣ Post-Layout Simulation

### **Procedure**
- Simulated extracted netlist including parasitics  
- Ran transient and VTC analysis  

### **Pre vs Post Layout Comparison**
(Replace values)

| Parameter | Pre-Layout | Post-Layout |
|----------|------------|-------------|
| tpHL     | XXX ps     | XXX ps      |
| tpLH     | XXX ps     | XXX ps      |
| Rise time | XXX ns    | XXX ns      |
| Fall time | XXX ns    | XXX ns      |

**Plots:**  
_Add post-layout waveform images here_

---

## 📌 Conclusion
This project demonstrates a complete open-source digital design flow using the SKY130 PDK, covering device-level MOSFET behavior, CMOS inverter analysis, layout verification, and post-layout performance evaluation.

---

## 📁 Repository Structure (Suggested)
