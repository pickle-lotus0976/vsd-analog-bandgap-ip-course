# Bandgap Reference (BGR) IP Design using Sky130

**Technology Node:** SkyWater 130nm (Sky130)
**Tools:** NgSpice, Magic VLSI, Netgen

## 1. Project Overview
This project focuses on the design and pre-layout simulation of a **Bandgap Voltage Reference (BGR)** circuit. The goal was to achieve a stable reference voltage independent of Process, Voltage, and Temperature (PVT) variations, which is critical for sub-systems such as LDOs, ADCs, and DACs.

The design implements a **Self-Biased Current Mirror (SBCM)** topology to ensure high Power Supply Rejection Ratio (PSRR) and stability, along with a dedicated start-up circuit to prevent degenerate bias points.

## 2. Theory of Operation
The BGR operates by summing two voltages with opposing temperature coefficients (TC) to achieve a near-zero TC at the output:
1.  **CTAT:** Derived from the Base-Emitter voltage ($V_{BE}$) of a PNP BJT (Negative TC).
2.  **PTAT:** Derived from the $\Delta V_{BE}$ between two BJTs with different current densities (Positive TC).

**Governing Equation:**
$$V_{REF} = V_{BE} + K \cdot \Delta V_{BE}$$

## 3. Simulation Results Gallery
All circuit simulations were performed using **NgSpice**. The table below summarizes the design performance across different process corners and operating conditions.

| Test Case | Objective & Outcome | Plot |
| :--- | :--- | :--- |
| **DC Analysis (Corner TT)** | **Objective:** Verify temp independence.<br>**Outcome:** Exhibits characteristic "umbrella" curve, confirming first-order compensation. | <img src="Images/Screenshot_2026-01-02_15-00-22.png" width="800"> |
| **DC Analysis (Corner SS)** | **Objective:** Verify stability at slow process.<br>**Outcome:** $V_{REF}$ remains stable; slight negative slope indicates CTAT dominance. | <img src="Images/Screenshot_2026-01-02_14-54-42.png" width="800"> |
| **DC Analysis (Corner FF)** | **Objective:** Verify stability at fast process.<br>**Outcome:** $V_{REF}$ remains stable; positive slope indicates PTAT dominance. | <img src="Images/Screenshot_2026-01-02_14-46-51.png" width="800"> |
| **Start-up Check** | **Objective:** Verify circuit wake-up.<br>**Outcome:** Circuit snaps out of zero-current state and stabilizes at ~1.2V. | <img src="Images/Screenshot_2026-01-02_14-58-52.png" width="800"> |

## 4. Physical Design (Layout)
The layout was created using **Magic VLSI** with a focus on matching and symmetry.

* **Status:** Work in Progress (LVS Pending).
![Magic-Layout-Top](Images/Screenshot_2026-01-02_18-48-05.png)
