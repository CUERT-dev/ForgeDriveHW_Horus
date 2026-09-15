# Three-Phase Voltage Source Inverter Power Stage: Component Sizing, Switching Dynamics, and Thermal Analysis

**Hardware Design Report and Technical Sizing Guide (48V/15A Nominal)**

**Author:** Amr khaled fathy & Amr Khaled Abdelhameid


## Abstract

This document provides a comprehensive, rigorous analytical sizing and verification report for a high-efficiency Three-Phase Voltage Source Inverter (VSI) operating at a nominal DC bus voltage of 48 V and an RMS phase current of 15 A. The power stage is driven at a switching frequency of 20 kHz using the HGN036N08S power MOSFET paired with the EG2131 high-voltage half-bridge gate driver. Every critical hardware aspect is addressed step-by-step, including Figure-of-Merit evaluations, precise slew-rate tuning, active Miller shoot-through immunity proofs, bootstrap network dimensioning, power loss distributions, thermal junction estimations, DC-link stabilization, and parasitic resonance snubbing.

---

# 1 System Specifications and Operating Boundaries

The inverter power stage is engineered to meet the following primary electrical and operational requirements:

* **Nominal DC Bus Voltage ($V_{DC}$):** 48 V (Nominal battery voltage, with full-charge operating voltage up to 54.6 V - 58.4 V).

* **Nominal Load Current ($I_{load}$):** 15 A (Continuous RMS / DC equivalent).

* **Peak Phase Current ($I_{peak}$):** $I_{peak} = (1.5...2.0) \times I_{load} \approx 25 \text{ A} - 30 \text{ A}$ (Startup and transient acceleration boundary).

* **PWM Switching Frequency ($f_{sw}$):** 20 kHz (Total switching cycle period $T = \frac{1}{f_{sw}} = 50 \ \mu\text{s}$).

* **Gate Driver Supply Voltage ($V_{drv}$):** 12 V (Regulated auxiliary gate supply voltage).

---

# 2 Component Selection and Technology Rationale

## 2.1 Power MOSFET Selection: HGN036N08S

The primary switching device selected for the $48 \text{ V} / 15 \text{ A}$ inverter power stage is the HGN036N08S N-channel Power MOSFET from Hunteck, housed in a high-density, low-inductance DFN 5 × 6 package.

**Selection Rationale:**

* **Optimal Voltage and Current Ratings:** The device offers an 80 V drain-to-source breakdown voltage ($V_{DSS}$) delivering a 1.67 safety margin above the nominal 48 V DC bus to safely absorb inductive turn-off voltage spikes. Its continuous drain current capability of 83 A at $T_C = 100^{\circ}C$ exceeds the nominal 15 A load current by more than $5\times$ ensuring high thermal reliability.

* **Ultra-Low On-State Resistance:** With a typical on-resistance of $R_{DS(on)} = 3.0 \text{ m}\Omega$ at $V_{GS} = 10 \text{ V}$, conduction losses are kept below 0.55 W per switch under nominal load conditions.

* **Low Parasitic Gate Charge:** A total gate charge of $Q_g = 61 \text{ nC}$ and a Miller charge of $Q_{gd} = 10 \text{ nC}$ allow rapid switching transitions with minimal gate driver power requirements.

* **Advanced DFN 5 × 6 Packaging:** The surface-mount DFN 5 × 6 package features negligible internal lead inductance compared to traditional through-hole packages (such as TO-220), substantially mitigating phase-node ringing and parasitic source inductance degradation.

* **Rugged Reverse Diode Capability:** The integrated body diode features enhanced $dv/dt$ ruggedness and an avalanche single-pulse energy rating ($E_{AS}$) of 320 mJ, protecting the bridge during inductive recirculation.

**Key Datasheet Electrical Parameters:**

* **Breakdown Voltage ($V_{(BR)DSS}$):** 80 V ($V_{GS} = 0 \text{ V}$, $I_D = 250 \ \mu\text{A}$).

* **Continuous Drain Current ($I_D$):** 131 A at $25^{\circ}C$ (Silicon limited), 60 A (Package limited), 83 A at $100^{\circ}C$.

* **Static Drain-Source On-Resistance ($R_{DS(on)}$):** 3.0 m$\Omega$ (Typical), 3.6 m$\Omega$ (Maximum) at $V_{GS} = 10 \text{ V}$, $I_D = 20 \text{ A}$.

* **Gate Threshold Voltage ($V_{GS(th)}$):** $V_{th(min)} = 2.0 \text{ V}$, $V_{th(typ)} = 2.8 \text{ V}$, $V_{th(max)} = 4.0 \text{ V}$.

* **Gate Charge Characteristics ($V_{DD} = 40 \text{ V}$, $I_D = 20 \text{ A}$, $V_{GS} = 10 \text{ V}$):**

  * Total Gate Charge ($Q_g$): 61 nC.
  * Gate-to-Source Charge ($Q_{gs}$): 18 nC.
  * Gate-to-Drain (Miller) Charge ($Q_{gd}$): 10 nC.

* **Internal Gate Resistance ($R_{g(int)}$):** 2.0 $\Omega$ (Typical at $f = 1 \text{ MHz}$).

* **Dynamic Capacitances ($V_{DS} = 40 \text{ V}$, $V_{GS} = 0 \text{ V}$, $f = 1 \text{ MHz}$):**

  * Input Capacitance ($C_{iss}$): 4512 pF.
  * Output Capacitance ($C_{oss}$): 566 pF.
  * Reverse Transfer Capacitance ($C_{rss}$): 31 pF.

* **Thermal Resistance Junction-to-Case ($R_{\theta JC}$):** $1.1^{\circ}C/\text{W}$.

**Figure of Merit (FOM) Evaluation:**

$$
FOM = R_{DS(on),typ} \times Q_{g,typ}
= 3.0 \text{ m}\Omega \times 61 \text{ nC}
= 183 \text{ m}\Omega\cdot\text{nC}
\tag{1}
$$

This confirms a high-performance silicon figure, yielding low gate drive overhead and minimal switching loss.

## 2.2 Gate Driver Selection: EG2131

The selected gate-driver IC for the $48 \text{ V} / 15 \text{ A}$ power stage is the EG2131 from EGmicro. The device was selected to meet the switching requirements of the HGN036N08S MOSFETS, provide high noise immunity, and deliver reliable high-speed gate driving for the half-bridge power stage.

**Selection Rationale:**

* **Local Availability and Cost:** The EG2131 is widely available from local suppliers at a competitive unit cost, making it a practical choice for prototyping, PCB assembly, maintenance, and replacement.

* **High-Side Floating Channel Capability:** The high-voltage section is rated for operation up to 300 V, providing more than a 6x safety margin over the nominal 48 V DC-link voltage. This additional voltage margin improves immunity to inductive phase-node ringing and transient negative voltage spikes during switching.

* **High Peak Drive Current:** The driver provides high output drive capability, with a typical source current of 1.0 A and sink current of 1.5 A. This allows fast charging and discharging of the HGN036N08S gate, which has a total gate charge of approximately 61 nC. As a result, the turn-on and turn-off transition times are tightly controlled, minimizing dynamic switching losses.

* **Integrated Interlock and Hardware Dead Time:** The driver includes built-in cross-conduction prevention logic and internal dead-time generation ($DT \approx 250 \text{ ns}$). This hardware interlock prevents the high-side and low-side MOSFETs from turning on simultaneously, protecting the half-bridge against shoot-through conditions and potential device failure.

* **External Bootstrap Diode Configuration:** Instead of relying on an integrated bootstrap diode, the design uses a dedicated external ultrafast-recovery diode (ES1J / US1M). This configuration reduces thermal dissipation within the driver IC, minimizes forward-recovery voltage effects, and improves bootstrap capacitor recharge during high-frequency PWM operation.

* **Compact SOP-8 Footprint:** The standard SOP-8 package allows the driver to be placed immediately adjacent to the power MOSFETs. This shortens the gate-drive traces, reduces parasitic inductance in the gate loop, and helps suppress ringing caused by high $di/dt$ switching currents.

**Key Driver Operating Specifications:**

* **Supply Operating Range ($V_{CC}$):** 11 V - 20 V (12 V nominal operational voltage).

* **High-Side Floating Supply ($V_B$):** Up to 300 V absolute maximum.

* **Peak Output Currents:** $I_{O+} = 1.0 \text{ A}$ (Source, $R_{drv(source)} \approx 4.5 \ \Omega$), $I_{O-} = 1.5 \text{ A}$ (Sink, $R_{drv(sink)} \approx 2.5 \ \Omega$).

* **Under-Voltage Lockout (UVLO):** $V_{CC(on)} = 10.3 \text{ V}$, $V_{CC(off)} = 8.6 \text{ V}$.

* **Integrated Dead Time (DT):** 250 ns typical (150 ns - 350 ns).

* **Input Logic Levels:** Compatible with 3.3 V and 5 V logic ($V_{IH} \ge 2.5 \text{ V}$, $V_{IL} \le 1.0 \text{ V}$), with active-high HIN and active-low LIN control inputs.

---

# 3 Slew Rate Dynamics and Switching Analysis

## 3.1 Effective Miller Capacitance

The non-linear gate-to-drain capacitance during the voltage transition is modeled through its effective charge:

$$
C_{gd,eff} = \frac{Q_{gd}}{V_{DC}}
= \frac{10 \text{ nC}}{48 \text{ V}}
\approx 208.33 \text{ pF}
\tag{2}
$$

## 3.2 Gate Driver Impedance (EG2131)

* **Output Pull-Up / Sourcing Current:** $I_{O+} \approx 1.0 \text{ A} \Rightarrow R_{drv(source)} \approx 4.5 \ \Omega$

* **Output Pull-Down / Sinking Current:** $I_{O-} \approx 1.5 \text{ A} \Rightarrow R_{drv(sink)} \approx 2.5 \ \Omega$

## 3.3 Turn-on Slew Rate Target and Gate Resistor Sizing

To maintain an optimal balance between switching dissipation and high-frequency electromagnetic interference (EMI), the turn-on voltage slew rate is constrained to:

$$
\left(\frac{dv}{dt}\right)_{on}
\le 1.2 \text{ V/ns}
\quad
\text{(Recommended Operational Range: } 0.8 \text{ V/ns} - 1.2 \text{ V/ns)}
\tag{3}
$$

The corresponding switch-node voltage fall time ($t_{fall}$) duration is:

$$
t_{fall}
=
\frac{V_{DC}}
{\left(\frac{dv}{dt}\right)_{on}}
\tag{4}
$$

Evaluating the limits of the target range:

* **Upper Slew Rate Limit ($1.2 \text{ V/ns}$):** $t_{fall,min} = \frac{48 \text{ V}}{1.2 \text{ V/ns}} = 40 \text{ ns}$

* **Lower Slew Rate Limit ($0.8 \text{ V/ns}$):** $t_{fall,max} = \frac{48 \text{ V}}{0.8 \text{ V/ns}} = 60 \text{ ns}$

The required average gate charging current during the Miller plateau ($V_{plateau} \approx 4.3 \text{ V}$) is:

$$
I_{gate(on)}
=
\frac{Q_{gd}}{t_{fall}}
\tag{5}
$$

* At $t_{fall} = 40 \text{ ns}$: $I_{gate(on),max} = \frac{10 \text{ nC}}{40 \text{ ns}} = 0.25 \text{ A}$

* At $t_{fall} = 60 \text{ ns}$: $I_{gate(on),min} = \frac{10 \text{ nC}}{60 \text{ ns}} \approx 0.167 \text{ A}$

The total turn-on loop resistance is derived via Ohm's law:

$$
R_{g,total(on)}
=
\frac{V_{drv} - V_{plateau}}{I_{gate(on)}}
=
\frac{12 \text{ V} - 4.3 \text{ V}}{I_{gate(on)}}
=
\frac{7.7 \text{ V}}{I_{gate(on)}}
\tag{6}
$$

* At $I_{gate(on)} = 0.25 \text{ A} \Rightarrow R_{g,total(on),min} = \frac{7.7 \text{ V}}{0.25 \text{ A}} = 30.8 \ \Omega$

* At $I_{gate(on)} = 0.167 \text{ A} \Rightarrow R_{g,total(on),max} = \frac{7.7 \text{ V}}{0.167 \text{ A}} \approx 46.1 \ \Omega$

Subtracting the driver sourcing resistance ($R_{drv(source)} \approx 4.5 \ \Omega$) and the internal gate resistance ($R_{g(int)} \approx 2.0 \ \Omega$) yields the external turn-on resistor bounds:

$$
R_{g(ext,on)}
=
R_{g,total(on)}
-
R_{drv(source)}
-
R_{g(int)}
=
R_{g,total(on)} - 6.5 \ \Omega
\tag{7}
$$

$$
24.3 \ \Omega
\le
R_{g(ext,on)}
\le
39.6 \ \Omega
\tag{8}
$$

**Selected Standard Values:** $R_{g(ext,on)} = 27 \ \Omega - 33 \ \Omega$ (SMD 1206 package, providing $0.95 \text{ V/ns} - 1.1 \text{ V/ns}$).

## 3.4 Turn-off Slew Rate Target and Gate Resistor Sizing

A slightly faster transition is permitted during turn-off to minimize turn-off switching losses while maintaining safe drain-to-source voltage overshoot margins:

$$
\left(\frac{dv}{dt}\right)_{off}
\le 1.6 \text{ V/ns}
\quad
\text{(Recommended Operational Range: } 1.2 \text{ V/ns} - 1.6 \text{ V/ns)}
\tag{9}
$$

The corresponding switch-node voltage rise time ($t_{rise}$) duration is:

$$
t_{rise}
=
\frac{V_{DC}}
{\left(\frac{dv}{dt}\right)_{off}}
\tag{10}
$$

Evaluating the limits of the target range:

* **Upper Slew Rate Limit ($1.6 \text{ V/ns}$):** $t_{rise,min} = \frac{48 \text{ V}}{1.6 \text{ V/ns}} = 30 \text{ ns}$

* **Lower Slew Rate Limit ($1.2 \text{ V/ns}$):** $t_{rise,max} = \frac{48 \text{ V}}{1.2 \text{ V/ns}} = 40 \text{ ns}$

The required gate discharging sink current during the Miller plateau transition is:

$$
I_{gate(off)}
=
\frac{Q_{gd}}{t_{rise}}
\tag{11}
$$

* At $t_{rise} = 30 \text{ ns}$: $I_{gate(off),max} = \frac{10 \text{ nC}}{30 \text{ ns}} \approx 0.333 \text{ A}$

* At $t_{rise} = 40 \text{ ns}$: $I_{gate(off),min} = \frac{10 \text{ nC}}{40 \text{ ns}} = 0.25 \text{ A}$

During turn-off, the gate discharges from the plateau voltage ($V_{plateau} \approx 4.3 \text{ V}$) down toward ground (0 V):

$$
R_{g,total(off)}
=
\frac{V_{plateau} - 0 \text{ V}}
{I_{gate(off)}}
=
\frac{4.3 \text{ V}}
{I_{gate(off)}}
\tag{12}
$$

* At $I_{gate(off)} = 0.333 \text{ A} \Rightarrow R_{g,total(off),min} = \frac{4.3 \text{ V}}{0.333 \text{ A}} \approx 12.91 \ \Omega$

* At $I_{gate(off)} = 0.25 \text{ A} \Rightarrow R_{g,total(off),max} = \frac{4.3 \text{ V}}{0.25 \text{ A}} = 17.2 \ \Omega$

Subtracting the driver sinking resistance ($R_{drv(sink)} \approx 2.5 \ \Omega$) and the internal gate resistance ($R_{g(int)} \approx 2.0 \ \Omega$) gives:

$$
R_{g(ext,off)}
=
R_{g,total(off)}
-
R_{drv(sink)}
-
R_{g(int)}
=
R_{g,total(off)} - 4.5 \ \Omega
\tag{13}
$$

$$
8.41 \ \Omega
\le
R_{g(ext,off)}
\le
12.7 \ \Omega
\tag{14}
$$

**Selected Standard Values:** $R_{g(ext,off)} = 8.2 \ \Omega - 10 \ \Omega$ in series with the turn-off Schottky diode (or direct anti-parallel diode clamp for maximum Miller sink capability).

---

# 4 Miller Shoot-Through Immunity and Gate Clamping Verification

When the high-side MOSFET turns on at the maximum design slew rate $\left(\left(\frac{dv}{dt}\right)_{on,max} = 1.2 \text{ V/ns}\right)$, the switch-node voltage rises abruptly from 0 V to 48 V. This dynamic voltage step injects a displacement current through the parasitic Miller capacitance ($C_{gd}$) of the low-side switch into its gate terminal.

## 4.1 Displacement Current Injection

$$
i_{inj}
=
C_{gd,eff}
\times
\left(\frac{dv}{dt}\right)_{on,max}
=
208.33 \text{ pF}
\times
1.2 \text{ V/ns}
=
0.25 \text{ A}
\tag{15}
$$

Over the operational slew rate range ($0.8 \text{ V/ns} - 1.2 \text{ V/ns}$):

$$
0.167 \text{ A}
\le
i_{inj}
\le
0.25 \text{ A}
\tag{16}
$$

## 4.2 Low-Side Gate Clamping Path Impedance

When utilizing an ultra-fast Schottky diode (BAT54/1N4148) in anti-parallel with $R_{g(ext,on)}$, the external turn-off impedance during clamping is minimized ($R_{g(ext,off)} \approx 0 \ \Omega$). The total effective low-side pull-down impedance is:

$$
R_{g,total(off)}
=
R_{drv(sink)}
+
R_{g(int)}
=
2.5 \ \Omega
+
2.0 \ \Omega
=
4.5 \ \Omega
\tag{17}
$$

## 4.3 Induced Gate Voltage, Safety Margin, and Absolute Limits

The maximum peak transient voltage induced at the low-side gate terminal is:

$$
V_{GS,induced}
=
i_{inj,max}
\times
R_{g,total(off)}
=
0.25 \text{ A}
\times
4.5 \ \Omega
=
1.125 \text{ V}
\tag{18}
$$

For the operational range ($0.8 \text{ V/ns} - 1.2 \text{ V/ns}$):

$$
0.751 \text{ V}
\le
V_{GS,induced}
\le
1.125 \text{ V}
\tag{19}
$$

**Safety Verification and Critical Boundaries:**

$$
V_{GS,induced,max}
=
1.125 \text{ V}
\ll
V_{th(min)}
=
2.0 \text{ V}
\tag{20}
$$

$$
\Delta V_{margin}
=
V_{th(min)}
-
V_{GS,induced,max}
=
2.0 \text{ V}
-
1.125 \text{ V}
=
0.875 \text{ V}
\tag{21}
$$

**Theoretical Stability Limits:**

* **Maximum Allowable Sinking Path Resistance ($R_{g,total(off),crit}$):**

$$
R_{g,total(off),crit}
<
\frac{V_{th(min)}}{i_{inj,max}}
=
\frac{2.0 \text{ V}}{0.25 \text{ A}}
=
8.0 \ \Omega
\Rightarrow
R_{g(ext,off)} < 3.5 \ \Omega
\tag{22}
$$

* **Maximum Critical Slew Rate Limit ($\left(\frac{dv}{dt}\right)_{crit}$ with $R_{g,total(off)} = 4.5 \ \Omega$):**

$$
\left(\frac{dv}{dt}\right)_{crit}
=
\frac{V_{th(min)}}
{C_{gd,eff} \times R_{g,total(off)}}
=
\frac{2.0 \text{ V}}
{208.33 \text{ pF} \times 4.5 \ \Omega}
\approx
2.13 \text{ V/ns}
\tag{23}
$$

Because the maximum operational slew rate ($1.2 \text{ V/ns}$) remains well below the critical threshold ($2.13 \text{ V/ns}$), spurious cross-conduction (Shoot-Through) is prevented across all manufacturing and temperature variations.

---

# 5 Bootstrap Circuit Design

## 5.1 Total Consumed Charge per Cycle ($Q_{total}$)

Under maximum duty cycle conditions ($D_{max} = 95\% \Rightarrow t_{on,max} = 47.5 \ \mu\text{s}$ at 20 kHz):

$$
Q_{total}
=
Q_{g(12V)}
+
Q_{ls}
+
(I_{qbs} + I_{gs,leak} + I_{boot,diode,leak})
\times
t_{on,max}
+
Q_{rr,diode}
\tag{24}
$$

Evaluating parametric worst-case values:

* $Q_{g(12V)} \approx 70 \text{ nC}$

* $Q_{ls} \approx 2 \text{ nC}$

* $Q_{leak} \approx (100 \ \mu\text{A} + 0.1 \ \mu\text{A} + 10 \ \mu\text{A}) \times 47.5 \ \mu\text{s} \approx 5.23 \text{ nC}$

* $Q_{rr,diode} \approx 10 \text{ nC}$

$$
Q_{total}
=
70 \text{ nC}
+
2 \text{ nC}
+
5.23 \text{ nC}
+
10 \text{ nC}
=
87.23 \text{ nC}
\tag{25}
$$

## 5.2 Bootstrap Capacitor Sizing

Allowing a maximum allowable ripple of $\Delta V_{boot} \le 0.5 \text{ V}$:

$$
C_{boot,min}
=
\frac{Q_{total}}{\Delta V_{boot}}
=
\frac{87.23 \times 10^{-9} \text{ C}}
{0.5 \text{ V}}
\approx
174.46 \text{ nF}
\tag{26}
$$

Accounting for the DC-bias capacitance degradation in Class-II ceramics (X7R), a design multiplier of $\ge 5\times$ is applied:

$$
C_{boot}
=
1.0 \ \mu\text{F}
\quad
\text{(Ceramic X7R, Rated Voltage } \ge 50 \text{ V)}
\tag{27}
$$

## 5.3 Bootstrap Diode and Current-Limiting Resistor

* **Diode ($D_{boot}$):** Ultra-fast recovery diode ES1J or US1M ($V_{RRM} = 600 \text{ V}$, $I_F = 1 \text{ A}$, $t_{rr} \le 35 \text{ ns}$).

* **Series Resistor ($R_{boot}$):** $R_{boot} = 2.2 \ \Omega - 4.7 \ \Omega$ (SMD 1206, 0.5 W) to suppress peak charging current spikes and limit high-frequency noise injection.

---

# 6 Dead-Time Optimization

The minimum dead time required by the power stage dynamics is:

$$
t_{dead,min}
\ge
(t_{d(off),max} + t_{f,max})
-
(t_{d(on),min} + t_{r,min})
+
t_{margin}
\tag{28}
$$

Using datasheet timings ($t_{d(off)} = 54 \text{ ns}$, $t_f = 17 \text{ ns}$, $t_{d(on)} = 15 \text{ ns}$, $t_r = 11 \text{ ns}$) and an engineering margin $t_{margin} = 80 \text{ ns}$:

$$
t_{dead,min}
\ge
(54 \text{ ns} + 17 \text{ ns})
-
(15 \text{ ns} + 11 \text{ ns})
+
80 \text{ ns}
=
125 \text{ ns}
\tag{29}
$$

The EG2131 gate driver features an integrated internal hardware dead time of:

$$
DT_{internal}
=
250 \text{ ns}
\quad
\text{(Range: } 150 \text{ ns} - 350 \text{ ns)}
\tag{30}
$$

Since $250 \text{ ns} > 125 \text{ ns}$, hardware cross-conduction is prevented while occupying only 0.5% of the total PWM switching cycle (50 $\mu$s).

---

# 7 Power Loss Distribution and Thermal Analysis

To provide an accurate thermal model and properly size the heatsink, switching dissipation is analyzed using two distinct methodologies: a first-order empirical transition model, followed by a rigorous piecewise gate-charge and Miller plateau analysis.

## 7.1 Method 1: First-Order Switching Loss Calculation

Assuming linear simultaneous transitions of drain voltage and drain current across the full switching duration:

$$
P_{SW(1st)}
\approx
\frac{1}{2}
V_{DS}
I_D
(t_{on} + t_{off})
f_{sw}
\tag{31}
$$

For $V_{DS} = 48 \text{ V}$, $I_D = 15 \text{ A}$, $f_{sw} = 20 \text{ kHz}$, with target durations $t_{on} \approx 40 \text{ ns}$ and $t_{off} \approx 30 \text{ ns}$:

$$
P_{SW(1st)}
\approx
0.5
\times
48
\times
15
\times
(40 + 30)
\times
10^{-9}
\times
20
\times
10^3
=
0.504 \text{ W}
\tag{32}
$$

This is a first-order estimate of the voltage-current overlap loss and does not include other switching-related losses such as $E_{OSS}$, body-diode reverse-recovery losses, gate-drive losses, or additional losses associated with switch-node ringing.

## 7.2 Method 2: Piecewise Gate-Charge & Miller Plateau Analysis (Rigorous Model)

This method models the actual physical charge transfer into the gate capacitances ($C_{gs}$ and $C_{gd}$) using datasheet charge curves and gate loop impedances.

### 7.2.1 Extracted Device Parameters (HGN036N08S & EG2131)

* **Gate drive supply voltage:** $V_{CC} = 12 \text{ V}$

* **Gate plateau voltage at $I_D = 15 \text{ A}$:** $V_{plat} \approx 4.3 \text{ V}$

* **Gate threshold voltage:** $V_{th} \approx 2.8 \text{ V}$

* **Gate-to-drain (Miller) charge:** $Q_{gd} = 10 \text{ nC}$

* **Post-threshold gate-to-source charge:**

$$
Q_{gs2}
=
Q_{gs}
\times
\left(
1 - \frac{V_{th}}{V_{plat}}
\right)
\approx
18 \text{ nC}
\times
\left(
1 - \frac{2.8 \text{ V}}{4.3 \text{ V}}
\right)
\approx
6.28 \text{ nC}
\tag{33}
$$

* **Output capacitance charge (48 V):** $Q_{oss} \approx 23 \text{ nC}$ ($C_{oss,eff} \approx 0.48 \text{ nF}$)

* **Reverse recovery charge:** $Q_{rr} \approx 158 \text{ nC}$

* **Total gate loop resistance** (using selected $R_{G(ext,on)} = 27 \ \Omega$ and $R_{G(ext,off)} \approx 0 \ \Omega$ with Schottky clamp):

  * Internal MOSFET gate resistance: $R_{G,int} = 2.0 \ \Omega$

  * Driver internal pull-up resistance: $R_{drv,on} = 4.5 \ \Omega$

  * Driver internal pull-down resistance: $R_{drv,off} = 2.5 \ \Omega$

  * $R_{tot,on} = R_{drv,on} + R_{G(ext,on)} + R_{G,int} = 4.5 + 27 + 2.0 = 33.5 \ \Omega$

  * $R_{tot,off} = R_{drv,off} + R_{G(ext,off)} + R_{G,int} = 2.5 + 0 + 2.0 = 4.5 \ \Omega$

### 7.2.2 Dynamic Drive Current and Crossover Timing

During active switching, current and voltage overlap only during the current rise/fall ($t_{ri} / t_{fi}$) and voltage fall/rise ($t_{fv} / t_{rv}$) phases:

**Turn-On Transition:**

$$
I_{G,on(plat)}
=
\frac{V_{CC} - V_{plat}}{R_{tot,on}}
=
\frac{12 \text{ V} - 4.3 \text{ V}}
{33.5 \ \Omega}
\approx
0.230 \text{ A}
\tag{34}
$$

$$
t_{ri}
=
\frac{Q_{gs2}}{I_{G,on(plat)}}
=
\frac{6.28 \text{ nC}}
{0.230 \text{ A}}
\approx
27.3 \text{ ns}
\quad
\text{(Current Rise Time)}
\tag{35}
$$

$$
t_{fv}
=
\frac{Q_{gd}}{I_{G,on(plat)}}
=
\frac{10 \text{ nC}}
{0.230 \text{ A}}
\approx
43.5 \text{ ns}
\quad
\text{(Voltage Fall Time)}
\tag{36}
$$

$$
t_{cross,on}
=
t_{ri}
+
t_{fv}
=
27.3 \text{ ns}
+
43.5 \text{ ns}
=
70.8 \text{ ns}
\tag{37}
$$

**Turn-Off Transition:**

$$
I_{G,off(plat)}
=
\frac{V_{plat}}{R_{tot,off}}
=
\frac{4.3 \text{ V}}
{4.5 \ \Omega}
\approx
0.956 \text{ A}
\tag{38}
$$

$$
t_{rv}
=
\frac{Q_{gd}}{I_{G,off(plat)}}
=
\frac{10 \text{ nC}}
{0.956 \text{ A}}
\approx
10.5 \text{ ns}
\quad
\text{(Voltage Rise Time)}
\tag{39}
$$

$$
t_{fi}
=
\frac{Q_{gs2}}{I_{G,off(plat)}}
=
\frac{6.28 \text{ nC}}
{0.956 \text{ A}}
\approx
6.6 \text{ ns}
\quad
\text{(Current Fall Time)}
\tag{40}
$$

$$
t_{cross,off}
=
t_{rv}
+
t_{fi}
=
10.5 \text{ ns}
+
6.6 \text{ ns}
=
17.1 \text{ ns}
\tag{41}
$$

### 7.2.3 Comprehensive Switching Loss Formulation

The total dynamic power dissipation per MOSFET accounts for crossover losses, capacitive energy discharge, and body-diode recovery:

$$
P_{cross}
=
0.5
\times
V_{DS}
\times
I_D
\times
(t_{cross,on} + t_{cross,off})
\times
f_{sw}
\tag{42}
$$

$$
P_{cross}
=
0.5
\times
48
\times
15
\times
(70.8 + 17.1)
\times
10^{-9}
\times
20
\times
10^3
\approx
0.633 \text{ W}
\tag{43}
$$

$$
P_{oss}
=
0.5
\times
C_{oss,eff}
\times
(V_{DS})^2
\times
f_{sw}
=
0.5
\times
(0.48 \times 10^{-9})
\times
(48)^2
\times
20
\times
10^3
\approx
0.011 \text{ W}
\tag{44}
$$

$$
P_{rr}
=
Q_{rr}
\times
V_{DS}
\times
f_{sw}
=
158 \times 10^{-9}
\times
48
\times
20
\times
10^3
\approx
0.152 \text{ W}
\tag{45}
$$

$$
P_{SW,total}
=
P_{cross}
+
P_{oss}
+
P_{rr}
=
0.633 \text{ W}
+
0.011 \text{ W}
+
0.152 \text{ W}
\approx
0.796 \text{ W}
\tag{46}
$$

## 7.3 Comparative Evaluation: Why Method 2 is More Accurate

| Evaluation Criterion     | Method 1: Slew-Rate / Target Model                                          | Method 2: Piecewise Gate-Charge Model                                            |
| ------------------------ | --------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **Physical Basis**       | Empirical assumption based on arbitrary $dv/dt$ limits                      | Electrostatic charge conservation ($Q = \int i,dt$) across non-linear junctions  |
| **Miller Effect**        | Ignored; assumes linear transition through the entire turn-on window        | Directly modeled via $Q_{gd}$ and Miller Plateau voltage clamp ($V_{plat}$)      |
| **Gate Impedance**       | Ignores driver pull-up/down resistance and internal silicon gate resistance | Models total loop resistance ($R_{drv} + R_{G,ext} + R_{G,int}$)                 |
| **Delay Discrimination** | Blurs turn-on delay ($t_{d(on)}$) with true power crossover ($t_{cross}$)   | Separates zero-loss turn-on delay from active $V \times I$ overlap               |
| **Secondary Losses**     | Excludes $E_{OSS}$ dissipation and body diode reverse recovery ($Q_{rr}$)   | Integrates $P_{cross}$, $P_{oss}$, and $P_{rr}$ into total switching dissipation |

## 7.4 Conduction Losses ($P_{cond}$)

For a sinusoidal 3-phase modulation scheme, the RMS current per switch is $I_{rms,switch} = I_{load} \times \sqrt{0.5} = 15 \text{ A} \times 0.707 \approx 10.606 \text{ A}$. At an elevated junction temperature of $100^{\circ}C$, the normalized on-resistance increases by a factor of $1.55\times$:

$$
R_{DS(on)(hot)}
=
1.55
\times
3.0 \text{ m}\Omega
=
4.65 \text{ m}\Omega
\tag{47}
$$

$$
P_{cond}
=
I_{rms,switch}^2
\times
R_{DS(on)(hot)}
=
(10.606 \text{ A})^2
\times
0.00465 \ \Omega
\approx
0.523 \text{ W}
\tag{48}
$$

## 7.5 Body Diode Dead-Time Losses ($P_{diode}$)

During the 250 ns dead-time interval, load current circulates through the integrated body diode:

$$
P_{diode}
=
2
\times
(V_{SD}
\times
I_{load}
\times
t_{dead}
\times
f_{sw})
\tag{49}
$$

$$
P_{diode}
=
2
\times
(0.9 \text{ V}
\times
15 \text{ A}
\times
250 \text{ ns}
\times
20000 \text{ Hz})
\approx
0.135 \text{ W}
\tag{50}
$$

## 7.6 Total Losses and Inverter Efficiency

The total power dissipation per MOSFET combining conduction, rigorous dynamic switching, and dead-time diode conduction is:

$$
P_{total}
=
P_{cond}
+
P_{SW,total}
+
P_{diode}
=
0.523 \text{ W}
+
0.796 \text{ W}
+
0.135 \text{ W}
\approx
1.454 \text{ W}
\quad
\text{per MOSFET}
\tag{51}
$$

For all 6 switches in the three-phase inverter bridge:

$$
P_{inverter,total}
=
6
\times
1.454 \text{ W}
\approx
8.72 \text{ W}
\tag{52}
$$

Inverter electrical efficiency at nominal output power ($P_{out} = 48 \text{ V} \times 15 \text{ A} = 720 \text{ W}$):

$$
\eta
=
\frac{720 \text{ W}}
{720 \text{ W} + 8.72 \text{ W}}
\times
100\%
\approx
98.80\%
\tag{53}
$$

## 7.7 Junction Temperature Estimation ($T_j$)

$$
T_j
=
T_a
+
P_{total}
\times
(R_{\theta JC} + R_{\theta CS} + R_{\theta SA})
\tag{54}
$$

Assuming maximum ambient temperature $T_a = 40^{\circ}C$, $R_{\theta JC} = 1.1^{\circ}C/\text{W}$ (DFN5 × 6), $R_{\theta CS} = 0.5^{\circ}C/\text{W}$ (thermal interface material), and a conservative heatsink $R_{\theta SA} = 8.0^{\circ}C/\text{W}$:

$$
T_j
=
40^{\circ}C
+
1.454 \text{ W}
\times
(1.1 + 0.5 + 8.0)^{\circ}C/\text{W}
=
40
+
(1.454 \times 9.6)
\approx
53.96^{\circ}C
\tag{55}
$$

The calculated junction temperature ($53.96^{\circ}C$) maintains a massive safety margin below the silicon thermal limit ($150^{\circ}C$).

---

# 8 DC-Link Capacitor Network Design

## 8.1 RMS Ripple Current Requirement

$$
I_{C(rms)}
\approx
0.55
\times
I_{load}
\approx
0.55
\times
15 \text{ A}
\approx
8.25 \text{ A}_{rms}
\tag{56}
$$

## 8.2 Minimum Bus Capacitance

Allowing a peak-to-peak voltage ripple $\Delta V_{pp} \le 1\% \times 48 \text{ V} = 0.48 \text{ V}$:

$$
C_{min}
=
\frac{I_{load}}
{4 \times f_{sw} \times \Delta V_{pp}}
=
\frac{15 \text{ A}}
{4 \times 20000 \text{ Hz} \times 0.48 \text{ V}}
\approx
390.6 \ \mu\text{F}
\tag{57}
$$

## 8.3 Capacitor Implementation Strategy

* **Bulk Electrolytic Bank:** $2 \times 470 \ \mu\text{F} / 80 \text{ V}$ Low-ESR capacitors in parallel, distributing ripple current ($4.12 \text{ A}_{rms}$ each) and halving equivalent series resistance.

* **High-Frequency Decoupling MLCCs:** $1.0 \ \mu\text{F} - 2.2 \ \mu\text{F} / 100 \text{ V}$ (X7R) ceramic capacitors placed immediately adjacent to each half-bridge leg to minimize parasitic power loop inductance ($L_{loop}$).

---

# 9 RC Snubber Design

## 9.1 Component Calculations

At $V_{DS} = 48 \text{ V}$, the output capacitance is $C_{oss} \approx 480 \text{ pF}$.

* **Snubber Capacitance ($C_{snub}$):**

$$
C_{snub}
\approx
2...3
\times
C_{oss}
\approx
1.0 \text{ nF}
-
1.5 \text{ nF}
\quad
\text{(Ceramic SMD / 100 V)}
\tag{58}
$$

* **Snubber Resistance ($R_{snub}$):** Assuming a parasitic loop inductance $L_{\sigma} \approx 15 \text{ nH}$:

$$
R_{snub}
=
\sqrt{
\frac{L_{\sigma}}
{C_{oss} + C_{snub}}
}
=
\sqrt{
\frac{15 \times 10^{-9} \text{ H}}
{0.48 \text{ nF} + 1.0 \text{ nF}}
}
\approx
3.18 \ \Omega
\approx
3.3 \ \Omega - 4.7 \ \Omega
\tag{59}
$$

* **Power Dissipation in Snubber Resistor ($P_{R,snub}$):**

$$
P_{R,snub}
=
C_{snub}
\times
V_{DC}^2
\times
f_{sw}
=
(1.0 \times 10^{-9} \text{ F})
\times
(48 \text{ V})^2
\times
20000 \text{ Hz}
\approx
0.046 \text{ W}
\tag{60}
$$

A standard SMD 1206/2512 non-inductive resistor rated at $\ge 0.5 \text{ W}$ is selected to handle repetitive pulse currents.

## 10 Current Sensing

Since galvanic isolation is not enforced between the power and logic domains, low-side shunt current sensing is employed to provide a cost-effective, high-accuracy, and compact current measurement solution. The shunt is placed in the low-side current return path of each half-bridge, allowing the resulting differential voltage drop to be amplified with respect to the local logic ground.

The **INA199A1** bidirectional current-sense amplifier from Texas Instruments is employed for this function.

**Datasheet:** INA199A1

**Selection rationale:**

*   **Local availability and cost:** The device is widely stocked and readily available from local component distributors at a low unit cost, making it highly practical for prototyping, production, and field maintenance.
*   **Common-mode voltage range:** The amplifier operates over a common-mode voltage range of -0.1 V to 26 V independent of the supply voltage, easily accommodating inductive ground bounce and negative switching transients occurring on the low-side source node.
*   **High CMRR:** A typical common-mode rejection ratio (CMRR) of 120 dB provides exceptional rejection of high-frequency common-mode noise and switching transients appearing across the shunt.
*   **Precision accuracy and low offset:** Features a low typical gain error of ±0.5% (max ±1%) and an ultra-low input offset voltage ($V_{OS} \le \pm 150 \ \mu\text{V}$), ensuring accurate current measurement for Field Oriented Control (FOC) without necessitating complex calibration routines.
*   **Bandwidth:** The 80 kHz small-signal bandwidth is well-matched to the 20 kHz PWM fundamental frequency, providing clean tracking of phase currents while rejecting high-frequency switching hash.
*   **High fixed gain:** The 50 V/V fixed precision gain allows the use of an ultra-low-value shunt resistor, minimizing $I^2R$ power dissipation in the current path while delivering a robust signal swing for the 3.3 V microcontroller ADC.

### Shunt Resistor Selection

The shunt resistance is selected as an optimal compromise between ADC dynamic range utilization, signal-to-noise ratio (SNR), and thermal power dissipation. For the updated **15 A nominal current** power stage, a low-inductance **2 mΩ metal-alloy shunt resistor** is selected.

To support bidirectional phase-current sensing (monitoring both motoring and regenerative braking currents), the reference pin of the amplifier is biased at the midpoint of the 3.3 V ADC range:

$$
V_{bias}
=
\frac{3.3 \text{ V}}{2}
=
1.65 \text{ V}
\tag{61}
$$

This establishes the zero-current operating point at exactly 1.65 V, enabling symmetric positive and negative current measurement.

For a 2 mΩ shunt and a fixed amplifier gain of 50 V/V, the sensed output voltage is:

$$
V_{signal}
=
I
\times
\left( 50 \times 0.002 \right) + 1.65 \text{ V}
\tag{62}
$$

Substituting the selected shunt resistance ($R_{shunt} = 2 \text{ m}\Omega = 0.002 \text{ }\Omega$):

$$
V_{signal}
=
I
\times
\left(
50
\times
0.002
\frac{\text{V}}{\text{A}}
\right)
+
1.65 \text{ V}
\tag{63}
$$

Thus, the current-sensing path provides an overall transfer gain of *$0.10 \frac{\text{V}}{\text{A}}$ ($100 \frac{\text{mV}}{\text{A}}$)*.

The theoretical 0 V to 3.3 V ADC input span corresponds to:

$$
I_{max}
=
\frac{(3.3 \text{ V} - 1.65 \text{ V})}
{0.10 \frac{\text{V}}{\text{A}}}
=
+16.5 \text{ A}
\tag{64}
$$

$$
I_{min}
=
\frac{(0.0 \text{ V} - 1.65 \text{ V})}
{0.10 \frac{\text{V}}{\text{A}}}
=
-16.5 \text{ A}
\tag{65}
$$

This yields a full-scale bidirectional measurement range of:

$$
\text{Range}
=
\pm 16.5 \text{ A}
\tag{66}
$$

> *Design Note:* If higher transient peak current headroom is required during aggressive acceleration or motor stall conditions, a 1 mΩ shunt can be substituted, which doubles the measurable dynamic range to ±33 A with a sensitivity of 50 mV/A.

In practice, the usable linear range is slightly constrained by the amplifier output stage rail-to-rail swing limits (typically 30 mV to 50 mV from rails) and ADC conversion margins.

## 11 Voltage Sensing

For the 48 V / 15 A power stage, galvanic isolation is not enforced between the power and logic domains, and PGND and LGND are tied at a single star-ground point. This allows the switch-node (phase) voltage to be sensed directly with respect to LGND using a precision resistive voltage divider.

The divider ratio is selected such that the maximum expected switch-node voltage (including battery charge headroom and regenerative braking voltage rise up to 60 V) is mapped cleanly into the valid 0 to 3.3 V range of the microcontroller ADC:

$$
V_{signal}
=
V_{sw}
\times
\left(
\frac{3.3 \text{ k}}
{60 \text{ k} + 3.3 \text{ k}}
\right)
\tag{67}
$$

where the upper leg is implemented as a series combination of three 20 kΩ SMD resistors (total 60 kΩ), and the lower leg is a 3.3 kΩ resistor.

This gives the following nominal full-scale mapping:

$$
60 \text{ V}
\rightarrow
3.128 \text{ V}
$$

*Full-scale saturation limit:* 63.3 V → 3.300 V

The resulting scaling makes effective use of the available ADC dynamic range while retaining a safe margin below the 3.3 V rail during transient voltage overshoots. The upper divider is implemented as a series string of resistors to distribute voltage stress across multiple SMD footprints (0603 package size) and to prevent dielectric breakdown.

### Power Dissipation

The power dissipated by the divider under the nominal 48 V DC switch-node condition is approximately:

$$
P_{divider}
=
\frac{(48)^2}
{(60 \text{ k} + 3.3 \text{ k})}
=
\frac{2304}{63300}
\approx
0.0364 \text{ W}
\quad
(36.4 \text{ mW})
\tag{68}
$$

This minimal power dissipation is distributed across the series resistor string rather than concentrated in a single component, resulting in negligible self-heating and minimal thermal drift error.

### Low-Pass Filtering (Anti-Aliasing & Noise Rejection)

To suppress high-frequency PWM switching hash and switch-node ringing, a *47 nF ceramic capacitor* is placed in parallel with the 3.3 kΩ lower divider resistor (forming the Phase_Cnd conditioning node).

The equivalent Thevenin resistance seen by the filter capacitor is:

$$
R_{eq}
=
60 \text{ k}\Omega
\parallel
3.3 \text{ k}\Omega
=
\frac{(60 \times 3.3)}
{(60 + 3.3)}
\approx
3.13 \text{ k}\Omega
\tag{69}
$$

The resulting low-pass filter cutoff frequency is:

$$
f_c
=
\frac{1}
{2 \times \pi \times R_{eq} \times C_{filter}}
\tag{70}
$$

$$
f_c
=
\frac{1}
{2 \times 3.1416 \times 3130 \text{ }\Omega \times 47 \times 10^{-9} \text{ F}}
\approx
1.08 \text{ kHz}
\tag{71}
$$

This low cutoff frequency effectively attenuates the 20 kHz PWM carrier frequency and high-frequency switching harmonics, providing a clean, smoothed analog representation of the fundamental phase voltage for the ADC.

### Operational Purpose

The conditioned phase voltage signal (Phase_Cnd) is primarily utilized for:

*   **High-Impedance Phase Monitoring:** Sensing the phase voltage when both high-side and low-side MOSFETs are turned OFF (tri-state), enabling Back-EMF zero-crossing detection for sensorless motor commutation and rotor position estimation.
*   **Fault Diagnostics & Protection:** Real-time monitoring of phase-node state, open-phase detection, and regenerative bus over-voltage tracking during dynamic deceleration.