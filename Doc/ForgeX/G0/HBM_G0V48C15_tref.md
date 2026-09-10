---
toc: true
toc_depth: 3
export_on_save:
  prince: true
---

@import "../../forgex_doc_style.css"

![](../ForgeX.svg)

# HBM_G0V48C15 Technical Reference

> **Project:** ForgeX  
> **Generation:** Gen0  
> **Document:** HBM_G0V48C15 Technical Reference  
> **Author:** Omar Magdy
> **Revision:** Rev. 0  
> **Status:** Development  
> **Date:** September 2026

<div style="page-break-after: always;"></div>

## Table of Contents
[TOC]

<div style="page-break-after: always;"></div>

## 1. Introduction

The **HBM (Half-Bridge Module)** is the localized power-switching
module of the ForgeX platform. It forms the primary power-conversion
building block of the Gen0 three-phase inverter, with each inverter
phase implemented using one HBM.

The module integrates the power switches, gate-drive circuitry,
current and voltage measurement, local protection, and the associated
high-frequency switching infrastructure into a physically localized
power stage.

The **HBM_G0V48C15** is an updated Gen0 HBM implementation based on
the revised electrical requirements. The module is resized from the
original **400 V / 5 A** operating range to **48 V / 15 A**.

The updated power stage uses the **HGN036 MOSFET**, **EG2131 gate driver**,
and **ACS712-20A current sensor**.

### 1.1 Naming

`HBM_G0V48C15` follows the ForgeX module naming convention:

- `HBM` — Half-Bridge Module
- `G0`  — Generation 0
- `V48` — 48 V voltage class
- `C15` — 15 A current class

The `V48C15` designation reflects the updated electrical operating
range of the module, which is resized from the original 400 V / 5 A
design to 48 V / 15 A.

The `C15` designation should not be interpreted as an unconditional
continuous-current rating. The achievable current depends on
operating conditions including cooling, switching frequency, thermal
limits, PCB characteristics, and the applicable electrical and EMI
constraints.

### 1.2 Overview

The HBM implements a single half-bridge power stage intended to be
combined with other HBM modules through the ForgeX PMB and LVP
interfaces.

The HBM_G0V48C15 is designed around the updated electrical operating
range of **48 V / 15 A**, compared with the original 400 V / 5 A
design.

The module is designed around the principle of **electrical locality**:
the high-current switching path, gate-drive loop, local decoupling,
current sensing, and switch-node structures are kept physically close
to one another to minimize parasitic inductance and unwanted coupling.

The principal functions of the HBM are:

- 48 V half-bridge power switching
- Local MOSFET gate drive using the EG2131
- Phase-current measurement using the ACS712-20A
- Switch-node voltage measurement
- Local fault and protection handling
- Interface to the ForgeX PMB power infrastructure
- Low-voltage control interface to the ForgeX LVP

### 1.3 Design Files

The design files for HBM_G0V48C15 will be maintained in the ForgeX
repository.

The following files will be linked here once they are available:

- **PCB & Schematic:** 🛠️
- **Simulation:** 🛠️
- **Manufacturing files:** 🛠️
---

## 2. Interfaces & I/O

> **Note:** The interface diagram is currently under development and will be updated once the final design is completed.

The HBM interfaces with the remainder of the ForgeX system through dedicated power and low-voltage interfaces.

### 2.1 Power Interface

The power interface consists of wide, dedicated copper interfaces designed
to carry the HBM power current. The interfaces can be connected using lugs
or copper spacers. The preceding figure shows the interface dimensions and
their symmetric arrangement.

| Pin Number | Pin Name | Pin Description |
|-----------:|----------|-----------------|
| 1 | `VDC` | Positive DC-link input. Connects to the positive DC bus. |
| 2 | `PHASE` | Half-bridge switched output. Connects to the motor phase or load. |
| 3 | `PGND` | Power ground and negative DC-link return. |

### 2.2 Low-Voltage Interface

The low-voltage interface uses a 2.54 mm-pitch header. The interface is
compatible with standard IDC and Dupont-style connectors.

A long-pin Dupont header may be used when access to the module's top-layer
debug and test points is desired.

| Pin Number | Pin Name | Pin Description |
|-----------:|----------|-----------------|
| 1 | `VCC` | Low-voltage supply input for the gate-driver and interface circuitry. A 12 V supply is recommended. The supply is referenced to `GND`. |
| 2 | `GND` | Low-voltage ground reference. `GND` is connected to `PGND` at a designated system-level grounding point; the HBM does not provide galvanic isolation between these domains. |
| 3 | `HIN` | High-side gate-drive control input. |
| 4 | `LIN` | Low-side gate-drive control input. |
| 5 | `NC` | No connection. Reserved for future use or left electrically unconnected. |
| 9 | `VSENSE` | Voltage-sense output proportional to the switch-node voltage. |
| 10 | `ISENSE` | Current-sense output proportional to the measured phase current. |

---

## 3. Design Requirements

Requirements are what the HBM itself is supposed to achieve:

- 48 V DC-link operation
- 15 A class power handling
- Efficient switching operation from 8–20 kHz with manageable conduction and switching losses
- Controlled switch-node ringing and voltage overshoot within component and system limits
- Current measurement bandwidth targeted at approximately 1–1.2 kHz
- Robust operation in the presence of switching noise and transients
- Controlled conducted and radiated EMI to minimize interference with other system components

---

## 4. Constraints

The Gen0 HBM design is subject to several practical constraints.

A significant constraint is the limited local supply chain, with
component availability primarily governed by vendors accessible in
Egypt. Component selection therefore considers availability and
replacement options in addition to electrical performance.

PCB fabrication is constrained by the DFM rules applicable to the
available fabrication process:

- Maximum number of layers: 2
- Minimum track width: 0.25 mm
- Minimum clearance: 0.25 mm
- Minimum polygon-pour clearance: 0.3 mm
- Minimum via diameter: 0.9 mm
- Minimum via hole diameter: 0.4 mm
- Minimum annular ring: 0.5 mm
- Maximum single-board size: 38 cm × 28 cm
- Maximum double-board size: 28 cm × 22 cm

The Gen0 implementation additionally prioritizes:

- Cost effectiveness
- Component availability
- Simplicity
- Reliability
- Ease of debugging and modification
- Maintainability during development

---

## 5. Sizing and Component Selection

### 5.1 MOSFET

The selected MOSFET for the HBM power stage is the `HGN036N08S`.
The device was selected based on its electrical characteristics,
high-current capability, low conduction resistance, high-speed
switching performance, package construction, and suitability for the
intended 48 V / 15 A switching conditions.

**Datasheet:** [HGN036N08S](https://www.alldatasheet.net/view_datasheet.jsp?Searchword=HGN036)

**Selection rationale:**

* **80 V drain-source rating:** The device has an 80 V maximum `VDS`
  rating, providing voltage margin above the nominal 48 V DC-link
  voltage. This margin provides tolerance to switching-node
  overshoot and transient voltage spikes.
* **High current capability:** The HGN036N08S is specified with a
  continuous drain current of up to 60 A under the package-limited
  condition at `TC = 25°C`, providing substantial current capability
  relative to the HBM's 15 A class operating current.
* **Low `RDS(on)`:** The device has a typical `RDS(on)` of 3.0 mΩ at
  `VGS = 10 V` and `ID = 20 A`. The low on-state resistance helps
  reduce conduction losses during normal operation.
* **Low gate charge:** The total gate charge `QG` is 61 nC, while the
  gate-to-drain (Miller) charge `QGD` is 18 nC. These parameters are
  considered in the gate-drive and switching-speed calculations.
* **High-speed switching:** The datasheet specifies the device for
  high-speed power switching and hard-switching applications,
  making it suitable for the HBM's intended switching-frequency
  range.
* **Enhanced body-diode dv/dt capability:** The device includes
  enhanced body-diode dv/dt capability, which is beneficial in the
  switching environment of a half-bridge power stage.
* **Package:** The MOSFET is provided in a compact DFN5×6 package,
  supporting a physically localized power stage and short
  high-current switching paths.
  
### 5.2 Gate Drive

The selected gate-driver IC for the HBM is the `EG2131` from EG Micro.

**Datasheet:** [EG2131](https://www.lcsc.com/datasheet/C193777.pdf)

**Selection rationale:**

* **Local availability and cost:** The device is readily available from local suppliers at a relatively low unit cost, making it practical for prototyping, production, and replacement.
* **300 V high-side operating capability:** The high-side floating section is rated for operation up to **300 V**, providing sufficient voltage margin for the HBM's nominal **48 V DC-link**.
* **High gate-drive current:** The driver provides up to **1 A source current** and **1.5 A sink current**, providing sufficient drive capability for the selected MOSFET at the intended switching frequencies.
* **Integrated interlock and deadtime:** The device incorporates half-bridge interlock logic and internal deadtime generation, with a typical deadtime of approximately **250 ns**. This provides protection against simultaneous turn-on of the high-side and low-side MOSFETs.
* **Bootstrap high-side drive:** The high-side driver uses a floating bootstrap supply to drive the high-side MOSFET. The bootstrap supply is implemented using an external bootstrap diode and capacitor, eliminating the need for a separate isolated high-side gate-drive supply and simplifying the gate-drive implementation.

### 5.3 External Components

The external components of the gate-drive circuit were selected based on the required switching speed, gate-drive current, bootstrap supply requirements, and supply decoupling.

### 5.3.1 Gate Resistor
The external gate resistor is treated as a design variable used to establish the required switching speed
while providing a practical compromise between switching losses, switch-node `dv/dt`,
voltage overshoot, ringing, and electromagnetic interference.

For the HGN036N08S, the datasheet specifies an external gate resistance of:

$$
R_{G,EXT}=10\,\Omega
$$

under the test conditions:

$$
V_{DD}=40\,V,\qquad I_D=20\,A,\qquad V_{GS}=10\,V
$$

The 10 Ω value is a **datasheet test condition rather than a recommended final design value**.
 It defines the external gate-drive condition used by the manufacturer to characterize
  the MOSFET's switching behavior and identify its switching characteristics.

Therefore, the switching times obtained under this test condition cannot be directly transferred to the HBM design. 
The actual switching time depends on the selected gate driver, gate-drive voltage, total gate resistance, MOSFET gate characteristics,
 operating current and voltage, temperature, and PCB parasitics.

The same datasheet specifies a total gate charge of:

$$
Q_G=61\,nC
$$

and a gate-to-drain (Miller) charge of:

$$
Q_{GD}=18\,nC
$$

**First-Order Switching Loss Estimate**

For the HBM operating point, a first-order estimate of the voltage-current overlap switching loss of a single MOSFET is:

$$
P_{SW}\approx\frac{1}{2}V_{DS}I_D(t_{on}+t_{off})f_{SW}
$$

For a 48 V DC-link, 15 A operating current, and 20 kHz switching frequency, initial switching-time targets of approximately 100 ns turn-on and 80 ns turn-off are used only as preliminary design targets:

$$
P_{SW}\approx
\frac{1}{2}\times48\times15\times(100+80)
\times10^{-9}\times20\times10^3
$$

$$
P_{SW}\approx1.296\,W
$$

This value is a first-order estimate of the voltage-current overlap loss only. It does not include output-capacitance energy, body-diode reverse-recovery losses, gate-drive losses, or additional losses caused by switch-node ringing.

The 100 ns and 80 ns values are **initial design targets and are not taken from the MOSFET datasheet**. They are used to establish an initial switching-speed target for the HBM. The final switching times shall be obtained from switching simulation and hardware measurements after selecting the actual gate resistance.

If the final gate resistance produces substantially faster or slower switching, the switching losses, `dv/dt`, `di/dt`, and parasitic-inductance voltage shall all be recalculated using the validated switching times.

**Gate Current and Gate Resistor**

The required gate current during the Miller transition can be estimated from:

$$
I_G\approx\frac{Q_{GD}}{t_{Miller}}
$$

For the selected HGN036N08S:

$$
Q_{GD}=18\,nC
$$

**Turn-On**

For an initial target Miller transition time of approximately 100 ns:

$$
I_{G,on}=
\frac{18\,nC}{100\,ns}
=180\,mA
$$

The EG2131 provides a specified gate-drive source current capability of approximately 1.0 A. Therefore, the estimated Miller current is within the driver's specified source-current capability.

**Turn-Off**

For an initial target turn-off transition of approximately 80 ns:

$$
I_{G,off}=
\frac{18\,nC}{80\,ns}
=225\,mA
$$

The EG2131 provides a specified gate-drive sink current capability of approximately 1.5 A, which is also sufficient for the calculated initial gate-current target.

**Gate Resistor Selection**

The 10 Ω resistance specified in the HGN036N08S datasheet is therefore used only as a reference for the manufacturer's switching characterization and as an indicator of the MOSFET's switching behavior. It is not directly adopted as the final HBM gate-resistor value.

The external gate resistor is selected according to the required switching speed and gate-drive current. In first-order form:

$$
I_G\approx
\frac{V_{DRV}-V_{PL}}
{R_{G,EXT}+R_{G,int}+R_{DRV}}
$$

where $V_{DRV}$ is the gate-drive voltage, $V_{PL}$ is the Miller plateau voltage, $R_{G,int}$ is the MOSFET internal gate resistance, and $R_{DRV}$ represents the effective driver output resistance.

A lower external gate resistance generally produces faster switching and lower voltage-current overlap losses, but increases `dv/dt`, `di/dt`, ringing, voltage overshoot, and EMI. A higher resistance reduces these transient stresses at the expense of increased switching losses.

Consequently, the final gate-resistor value shall be determined through switching simulation and hardware measurements rather than directly copied from the MOSFET datasheet test circuit. Separate turn-on and turn-off resistors may also be used if asymmetric switching control is required.

**`dv/dt` Constraints**

The selected switching speed shall be evaluated against the principal `dv/dt` constraints associated with the MOSFET, gate driver, and half-bridge configuration.

**Miller-Induced False Turn-On**

When the complementary MOSFET switches, the switch-node `dv/dt` couples through the gate-drain capacitance of the MOSFET that is in the OFF state.

The resulting Miller current can be approximated by:

$$
I_{Miller}=C_{GD}(V_{DS})\frac{dV_{DS}}{dt}
$$

A faster switching transition increases `dv/dt` and consequently the Miller current, increasing the possibility of false turn-on. The actual behavior depends on the nonlinear $C_{GD}(V_{DS})$ characteristic, gate-loop impedance, temperature, and PCB parasitics.

The intended switch-node slew rate shall therefore be verified through simulation and experimental measurements.

**Gate-Driver `dv/dt` Capability**

The EG2131 is specified as a half-bridge high-side/low-side gate driver with a high-side floating supply capability of up to 300 V and gate-drive output capability of approximately 1.0 A source and 1.5 A sink.

The EG2131 also includes internal dead-time control with a typical dead time of approximately 250 ns:

$$
DT_{typ}=250\,ns
$$

No separate numerical `dv/dt` immunity limit is assumed here because such a value has not been established from the selected EG2131 datasheet data.

**`di/dt` and Parasitic-Inductance Voltage**

The current slew rate through parasitic inductance generates an additional voltage according to:

$$
V_L=L_{par}\frac{di}{dt}
$$

For an initial 15 A current transition occurring over approximately 100 ns:

$$
\frac{di}{dt}\approx
\frac{15\,A}{100\,ns}
=0.15\,A/ns
$$

For an illustrative switching-loop inductance of 20 nH:

$$
V_{L,Power}\approx
20\,nH\times0.15\,A/ns
\approx3.0\,V
$$

If a source inductance of 7 nH is considered:

$$
V_{L,Source}=
7\,nH\times0.15\,A/ns
\approx1.05\,V
$$

The combined first-order inductive voltage contribution is therefore:

$$
\boxed{
V_{L,Total}\approx
3.0\,V+1.05\,V
\approx4.05\,V
}
$$

These inductance values are illustrative design assumptions and shall be replaced by extracted or measured PCB parasitic values when the final layout is available.

The calculated inductive voltage is directly dependent on the actual switching time. For example, reducing the switching time substantially increases `di/dt` and therefore increases the voltage generated across the parasitic inductance.

Accordingly, the final calculations for `di/dt`, parasitic-inductance voltage, and switching losses shall use the validated turn-on and turn-off switching times obtained with the final gate-resistor network.

The final gate-resistor value and switching performance shall therefore be validated through simulation and hardware testing under the target 48 V / 15 A operating conditions.

### 5.3.2 Bootstrap Capacitor
The bootstrap capacitor provides the local energy reservoir required to drive the high-side MOSFET. In the `EG2131` implementation, the high-side floating supply is generated using an external bootstrap diode and bootstrap capacitor connected between `VB` and `VS`. During the low-side conduction interval, the bootstrap capacitor is recharged from the `VCC` supply.

The bootstrap capacitor must be sufficiently large to limit the voltage drop caused by the MOSFET gate charge and the charge consumed by the high-side gate-driver circuitry.

A first-order estimate of the voltage drop due to the MOSFET gate charge is:

$$
\Delta V_{BOOT}=\frac{\Delta Q}{C_{BOOT}}
$$

For the selected HGN036N08S:

$$
Q_G\approx61\,nC
$$

Using a preliminary bootstrap capacitance of:

$$
C_{BOOT}=1\,\mu F
$$

the voltage drop due to one MOSFET gate-charge event is approximately:

$$
\Delta V_{BOOT}\approx
\frac{61\,nC}{1\,\mu F}
\approx61\,mV
$$

This represents only the MOSFET gate-charge contribution. The actual bootstrap-voltage droop will be higher because the gate driver also consumes charge and because of losses in the bootstrap diode and capacitor.

The selected bootstrap capacitor is therefore:

$$
\boxed{C_{BOOT}=1\,\mu F}
$$

The 1 µF value provides substantial charge reserve and keeps the gate-charge-induced voltage variation small. The final value shall be verified considering the gate-driver quiescent and switching current, maximum high-side on-time, bootstrap-diode characteristics, capacitor tolerance, temperature, and allowable bootstrap-voltage ripple.

The selected value should therefore be understood as a preliminary hold-up and robustness choice rather than a datasheet-mandated minimum. Final validation shall confirm that the bootstrap voltage remains within the required operating range at the maximum intended duty cycle and switching conditions.

### 5.3.3 VCC Decoupling Capacitor
The gate-driver supply requires local decoupling to provide the high-frequency current demanded by the gate-drive output stage and to minimize voltage sag and supply-loop inductance during switching transitions.

The `EG2131` datasheet specifies a high-frequency 0.1 µF bypass capacitor between `VCC` and `GND`. A larger local capacitor is also used to provide additional bulk energy storage.

For the selected HBM implementation, a 10 µF capacitor is used together with a 0.1 µF ceramic bypass capacitor in parallel:

$$
\boxed{C_{VCC}=10\,\mu F}
$$

$$
\boxed{C_{HF}=0.1\,\mu F}
$$

The 10 µF capacitor provides local bulk energy storage, while the 0.1 µF ceramic capacitor provides a low-impedance path for high-frequency current components during gate-drive transitions.

Both capacitors should be placed directly adjacent to the `EG2131` `VCC` and `GND` pins using short and wide PCB connections to minimize parasitic inductance.

The `EG2131` VCC supply operates within:

$$
11\,V\leq V_{CC}\leq20\,V
$$

Therefore, the selected capacitors shall be appropriately rated for the actual VCC supply voltage and operating conditions.

The final decoupling network shall be verified through switching simulation and hardware testing to ensure stable driver-supply voltage during high-current switching transitions.

### 5.3.4 RC Snubber
A first-pass RC snubber was estimated to damp the high-frequency switch-node resonance associated with the switching-loop inductance and MOSFET output capacitance.

Using an estimated switching-loop inductance of:

$$
L_0=20\,nH
$$

and MOSFET output capacitance:

$$
C_{oss}\approx565\,pF
$$

an initial snubber capacitance can be selected as approximately four times the MOSFET output capacitance:

$$
C_{snub}\approx4C_{oss}
$$

$$
C_{snub}\approx
4\times565\,pF
\approx2.26\,nF
$$

The damping resistance can be estimated from the characteristic impedance of the parasitic LC network:

$$
R_{snub}\approx
\sqrt{\frac{L_0}{C_{oss}}}
$$

Therefore:

$$
R_{snub}\approx
\sqrt{\frac{20\,nH}{565\,pF}}
\approx5.95\,\Omega
$$

Standard nominal values are therefore selected as:

$$
\boxed{C_{snub}=2.2\,nF}
$$

$$
\boxed{R_{snub}=6.2\,\Omega}
$$

The average power dissipated by the snubber resistor can be estimated as:

$$
P_{snub}\approx
C_{snub}V_{DS}^{2}f_{SW}
$$

For a 48 V DC-link and 20 kHz switching frequency:

$$
P_{snub}\approx
2.2\,nF\times(48\,V)^2\times20\,kHz
\approx0.101\,W
$$

These values represent an initial damping network based on estimated parasitic parameters. The final snubber values shall be optimized using switching-waveform simulation and hardware measurements by evaluating voltage overshoot, ringing, switching losses, and snubber power dissipation under the target operating conditions.

### 5.4 Current Sensing
A Hall-effect-based current sensor is employed for phase-current monitoring. The use of a Hall-effect sensor provides galvanic isolation between the current-conduction path and the signal circuitry while allowing bidirectional current measurement.

The **ACS712** fully integrated Hall-effect current sensor IC from Allegro MicroSystems is selected for this function.

**Datasheet:** [ACS712 Datasheet](https://www.sparkfun.com/datasheets/Breakouts/ACS712-datasheet.pdf)

**Selection Rationale**

* **Galvanic Isolation:** The integrated copper conduction path provides up to $2.1,\mathrm{kV_{RMS}}$ galvanic isolation between the sensed current path and the signal circuitry.
* **Low Internal Resistance:** The internal conductor resistance is approximately $1.2,\mathrm{m\Omega}$, resulting in low conduction loss.
* **Bidirectional Sensing:** The output is biased around $V_{CC}/2$, allowing measurement of both positive and negative phase current.
* **Analog Proportional Output:** The output voltage is proportional to the sensed current.

**Sensitivity and Output Characteristics**

The zero-current output voltage is approximately:

$$
V_{OUT(Q)}=\frac{V_{CC}}{2}
$$

For:

$$
V_{CC}=5.0\,V
$$

the nominal zero-current output is:

$$
V_{bias}=2.5\,V
$$

For the target operating conditions, the **ACS712-20A** variant is selected. Its nominal sensitivity is:

$$
\boxed{\text{Sensitivity}=100\,\mathrm{mV/A}}
$$

The current-to-voltage transfer function is:

$$
V_{OUT}=
\left(I\times0.100\,\mathrm{V/A}\right)+2.5\,V
$$

**Measurement Range and ADC Interface**

The nominal bidirectional measurement range is:

$$
\boxed{I_{range}=\pm20\,A}
$$

The corresponding output voltages are:

$$
V_{OUT,max}
=20\,A\times0.100\,V/A+2.5\,V
=4.5\,V
$$

$$
V_{OUT,min}
=-20\,A\times0.100\,V/A+2.5\,V
=0.5\,V
$$

Since the maximum sensor output exceeds the 3.3 V ADC input range, an external resistor divider is used.

For:

$$
R_1=10\,k\Omega,\qquad R_2=20\,k\Omega
$$

the attenuation factor is:

$$
\frac{R_2}{R_1+R_2}
=
\frac{20}{10+20}
=
\frac{2}{3}
$$

Thus:

$$
V_{ADC}=V_{OUT}\times\frac{2}{3}
$$

and the zero-current level becomes:

$$
V_{ADC,bias}
=
2.5\,V\times\frac{2}{3}
\approx1.667\,V
$$

The full sensor output range is mapped to approximately:

$$
0.333\,V\leq V_{ADC}\leq3.0\,V
$$

which remains within the 3.3 V ADC range.

**Internal Conductor Power Dissipation**

The sensor conduction loss is estimated from:

$$
P_{sensor}=I_{RMS}^{2}R_{primary}
$$

For a 15 A peak current:

$$
I_{RMS}=
\frac{15}{\sqrt{2}}
\approx10.61\,A
$$

Using:

$$
R_{primary}\approx1.2\,m\Omega
$$

gives:

$$
P_{sensor}
=
(10.61\,A)^2\times1.2\,m\Omega
\approx0.135\,W
$$

$$
\boxed{P_{sensor}\approx135\,mW}
$$

**Noise Filtering and Bandwidth Adjustment**

The current-sensor bandwidth is intentionally reduced to attenuate PWM-related components while retaining sufficient bandwidth for phase-current feedback and higher electrical-frequency operation, including High-Frequency Injection (HFI) techniques.

For the selected filter network, the cutoff frequency is approximated by:

$$
f_{-3dB}\approx
\frac{1}{2\pi\times20\,k\Omega\times C_F}
$$

To target approximately 1 kHz bandwidth:

$$
C_F\approx
\frac{1}
{2\pi(20\,k\Omega)(1\,kHz)}
\approx7.96\,nF
$$

A standard capacitor value of:

$$
\boxed{C_F=8.2\,nF}
$$

is therefore selected, giving:

$$
f_{-3dB}\approx
\frac{1}
{2\pi(20\,k\Omega)(8.2\,nF)}
\approx971\,Hz
$$

This bandwidth is approximately one order of magnitude below the minimum 20 kHz PWM frequency. It therefore provides substantial attenuation of PWM-related components while maintaining a higher measurement bandwidth than the previous lower-bandwidth configuration, supporting phase-current feedback and higher electrical-frequency operation such as HFI.

### 5.5 Voltage Sensing
For the `HBM_G0V48C15`, galvanic isolation is not enforced between the power and logic domains. `PGND` and `LGND` are therefore connected at a defined point within the system, allowing the switch-node and DC-link voltages to be sensed with respect to `LGND` using a resistive divider.

The divider is designed to measure the nominal 48 V DC-link voltage while tolerating transient voltages up to approximately 60 V.

The divider transfer function is:

$$
V_{signal}
=
V_{sw}
\frac{R_2}{R_1+R_2}
$$

Using:

$$
R_1=180\,k\Omega,\qquad
R_2=10\,k\Omega
$$

gives:

$$
V_{signal}
=
V_{sw}
\frac{10\,k\Omega}
{180\,k\Omega+10\,k\Omega}
=
\frac{V_{sw}}{19}
$$

Therefore:

$$
60\,V\rightarrow3.158\,V
$$

$$
48\,V\rightarrow2.526\,V
$$

and the scaling factor is:

$$
\boxed{\text{Scaling Factor}=0.0526\,V/V}
$$

The selected divider therefore keeps the sensed voltage below the 3.3 V ADC limit while retaining a small margin for transient events.

**Divider Power and ADC Source-Impedance Trade-Off**

The divider current at the nominal 48 V operating voltage is:

$$
I_{divider}
=
\frac{48\,V}
{180\,k\Omega+10\,k\Omega}
\approx0.253\,mA
$$

and the total divider power is:

$$
P_{divider}
=
\frac{(48\,V)^2}
{190\,k\Omega}
\approx12.1\,mW
$$

$$
\boxed{P_{divider}\approx12.1\,mW}
$$

However, the high resistor values result in a relatively high Thevenin source impedance at the ADC input:

$$
R_{TH}
=
R_1\parallel R_2
$$

$$
R_{TH}
=
180\,k\Omega\parallel10\,k\Omega
\approx9.47\,k\Omega
$$

A lower-resistance alternative such as $47,k\Omega+2.7,k\Omega$ provides a significantly lower ADC source impedance:

$$
R_{TH,alt}
=
47\,k\Omega\parallel2.7\,k\Omega
\approx2.55\,k\Omega
$$

However, the lower-resistance network increases continuous power dissipation. At 48 V:

$$
P_{alt}
=
\frac{(48\,V)^2}
{47\,k\Omega+2.7\,k\Omega}
\approx46.3\,mW
$$

Therefore, the comparison is:

| Divider Network          | Thevenin Source Impedance |    Power at 48 V |
| ------------------------ | ------------------------: | ---------------: |
| $180,k\Omega+10,k\Omega$ |     $\approx9.47,k\Omega$ | $\approx12.1,mW$ |
| $47,k\Omega+2.7,k\Omega$ |     $\approx2.55,k\Omega$ | $\approx46.3,mW$ |

The lower-resistance option reduces the ADC source impedance by approximately 73%, making ADC sampling easier and reducing the required acquisition time. However, it increases continuous divider power by approximately 3.8 times.

The selected $180,\mathrm{k\Omega}+10,\mathrm{k\Omega}$ network therefore provides a reasonable compromise between low power consumption and ADC source impedance. The ADC sampling time shall be verified against the actual ADC input requirements. If the required acquisition time cannot be achieved with the selected ADC configuration, the lower-resistance divider option may be considered.

The upper resistor $R_1$ may be implemented as a series string where required to distribute the voltage stress across multiple components and satisfy the resistor voltage-rating, creepage, and clearance requirements of the PCB layout.

### 5.6 References
* [TI — Bootstrap Circuitry Selection for Half-Bridge Configurations](https://www.ti.com/lit/an/slua887a/slua887a.pdf)
* [Infineon — Using Monolithic High-Voltage Gate Drivers](https://www.infineon.com/row/public/documents/24/42/infineon-using-monolithic-high-voltage-gate-drivers-applicationnotes-en.pdf)
* [Seminar 1400 Topic 2 APDX Estimating MOSFET Parameters from the Data Sheet](https://www.ti.com/lit/ml/slup170/slup170.pdf?ts=1786803369734)

---
<div style="page-break-after: always;"></div>

Note: This section is currently under development.
 Simulation models and hardware validation results are being updated for the latest module revision 
 and will be finalized upon complete testing.

<div style="page-break-after: always;"></div>

