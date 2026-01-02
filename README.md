# Bandgap Reference (BGR) IP Design using Sky130

**Technology Node:** SkyWater 130nm (Sky130)
**Tools:** NgSpice, Magic VLSI, Netgen

## 1. Project Overview
This project focuses on the design and pre-layout simulation of a **Bandgap Voltage Reference (BGR)** circuit. The goal was to achieve a stable reference voltage independent of Process, Voltage, and Temperature (PVT) variations, which is critical for sub-systems such as LDOs, ADCs, and DACs.

The design implements a **Self-Biased Current Mirror (SBCM)** topology to ensure high Power Supply Rejection Ratio (PSRR) and stability, along with a dedicated start-up circuit to prevent degenerate bias points.

## 2. Theory of Operation (Summary)
The BGR operates by summing two voltages with opposing temperature coefficients (TC) to achieve a near-zero TC at the output:
1.  **CTAT (Complementary to Absolute Temperature):** Derived from the Base-Emitter voltage ($V_{BE}$) of a PNP BJT. As temperature rises, $V_{BE}$ decreases.
2.  **PTAT (Proportional to Absolute Temperature):** Derived from the difference in $V_{BE}$ ($\Delta V_{BE}$) between two BJTs with different current densities ($1:N$ ratio). As temperature rises, $\Delta V_{BE}$ increases.

**Governing Equation:**
$$V_{REF} = V_{BE} + K \cdot \Delta V_{BE}$$
*By tuning the gain factor $K$ (via resistor ratios), the positive and negative slopes cancel out.*

## 3. Pre-Layout Simulation (NgSpice)

All circuit simulations were performed using **NgSpice**. The design was rigorously tested across different process corners (TT, SS, FF) and operating conditions to ensure robustness.

### 3.1 Design Components & Sizing
* **Transistors:** 1.8V LVT MOSFETs (`sky130_fd_pr__nfet_01v8_lvt`, `pfet`).
* **BJTs:** PNP (`sky130_fd_pr__pnp_05v5`) configured as diodes.
* **Resistors:** High Poly Resistors (`sky130_fd_pr__res_high_po`) for low TC.

### 3.2 DC Analysis (Temperature Sweep)
**Objective:** Verify temperature independence of $V_{REF}$ from -40°C to 125°C.

* **Corner TT (Typical-Typical):**
    * Achieved Temperature Coefficient: **~22 ppm/°C**
    * The "umbrella" curve indicates successful cancellation of first-order temperature effects.
    ![DC-TT-Plot](Day2/2-35.png)

* **Corner SS (Slow-Slow):**
    * Achieved Temperature Coefficient: **~43 ppm/°C**
    * Validates functionality even with slower process parameters.
    ![DC-SS-Plot](Day2/2-38.png)

* **Corner FF (Fast-Fast):**
    * Achieved Temperature Coefficient: **~10.1 ppm/°C**
    * Best-case performance scenario.
    ![DC-FF-Plot](Day2/2-41.png)

### 3.3 Transient Analysis (Start-up)
**Objective:** Verify the start-up circuit functionality and measure settling time.

* **Start-up Logic:** A "kick" circuit detects if the main branch current is zero and injects current to wake up the core. Once $V_{REF}$ stabilizes, the start-up branch turns off to save power.
* **Settling Time:** The output stabilizes at ~1.2V within **1.2 $\mu$s**, meeting the < 2 $\mu$s specification.

![Transient-Startup](Day2/2-47.png)

## 4. Physical Design (Layout)

The layout was created using **Magic VLSI** with a focus on matching and symmetry.

* **Matching:** Critical BJTs and resistors utilize common-centroid placement to minimize process gradients.
* **LVS Status:** **Clean**. The layout netlist matches the schematic netlist uniquely (Verified via Netgen).

![Magic-Layout-Top](Day2/2-64.png)

## 5. Work in Progress: Post-Layout Verification

* **Current Status:** Layout vs. Schematic (LVS) is clean.
* **Pending:** Parasitic Extraction (PEX) and Post-Layout Simulation (PLS).
* **Next Steps:**
    * Debug parasitic capacitance extraction issues in Magic.
    * Run transient and DC sweeps on the extracted netlist to quantify the impact of interconnect resistance and capacitance on the Temperature Coefficient.
