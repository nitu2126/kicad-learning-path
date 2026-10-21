# Design Notes: LED + Push-Button PCB

Revision 1.0 · KiCad 10.0.x · Last updated: 2026-10-19

This document records every engineering decision behind the board: the electronics theory, the calculations, the KiCad configuration, the layout rules, the verification results and the manufacturing settings.

## Contents

1. [Requirements](#1-requirements)
2. [Electronics background](#2-electronics-background)
3. [Calculations](#3-calculations)
4. [Schematic](#4-schematic)
5. [KiCad configuration](#5-kicad-configuration)
6. [Layout](#6-layout)
7. [Verification](#7-verification)
8. [Manufacturing outputs](#8-manufacturing-outputs)
9. [Bring-up and testing](#9-bring-up-and-testing)
10. [Lessons learned](#10-lessons-learned)
11. [References](#11-references)

---

## 1. Requirements

| #   | Requirement                                                              |
| --- | ------------------------------------------------------------------------ |
| R1  | Powered from a 5 V DC source through a 2-pin header                      |
| R2  | The LED lights only while the push-button is pressed                     |
| R3  | LED current between 5 mA and 10 mA, safely below the LED's rated maximum |
| R4  | Resistor and switch operate well within their ratings                    |
| R5  | Hand-solderable parts (0805 and through-hole)                            |
| R6  | 2-layer board that a low-cost fab can build without special options      |
| R7  | Zero ERC errors and zero DRC errors                                      |

---

## 2. Electronics background

### 2.1 The three basic quantities

| Quantity   | Symbol | Unit       | Water analogy              |
| ---------- | ------ | ---------- | -------------------------- |
| Voltage    | V      | volt (V)   | Pressure pushing the water |
| Current    | I      | ampere (A) | Flow rate of the water     |
| Resistance | R      | ohm (Ω)    | Narrowness of the pipe     |

### 2.2 Ohm's law and power

$$V = I \times R \qquad I = \frac{V}{R} \qquad R = \frac{V}{I}$$

$$P = V \times I = I^2 R = \frac{V^2}{R}$$

Power is the rate at which electrical energy turns into heat or light, measured in watts (W).

### 2.3 Series circuits and Kirchhoff's voltage law

In a series loop, **the same current flows through every component**, and the voltage drops around the loop add up to the supply voltage:

$$V_S = V_{SW} + V_R + V_{LED}$$

A closed push-button has a contact resistance of well under 0.1 Ω, so $V_{SW}$ is below 1 mV at 9 mA and is treated as 0 V.

### 2.4 Why an LED needs a resistor

An LED is a diode. Once the voltage across it reaches its **forward voltage** $V_F$ (about 1.8 V to 2.2 V for red, about 3.0 V for white or blue), its current rises **exponentially** with a tiny increase in voltage. A resistor in series fixes the current: the resistor takes whatever the LED does not, and Ohm's law then sets the current. Without it, the LED would draw excessive current and fail.

---

## 3. Calculations

### 3.1 Inputs

| Symbol | Meaning                       | Value | Source                                                         |
| ------ | ----------------------------- | ----- | -------------------------------------------------------------- |
| $V_S$  | Supply voltage                | 5.0 V | Design requirement R1                                          |
| $V_F$  | LED forward voltage (typical) | 2.0 V | Typical red LED, check the datasheet of the part you buy       |
| $I_F$  | Target LED current            | 10 mA | Bright indicator, below the typical 20 mA maximum of 0805 LEDs |

### 3.2 Resistor value

$$R = \frac{V_S - V_F}{I_F} = \frac{5.0\ \text{V} - 2.0\ \text{V}}{0.010\ \text{A}} = 300\ \Omega$$

The nearest standard values are 300 Ω (E24 series) and 330 Ω (E12 series). **330 Ω was chosen** because it is the more commonly stocked value and gives a slightly lower, more conservative current.

### 3.3 Actual current

$$I = \frac{V_S - V_F}{R} = \frac{3.0\ \text{V}}{330\ \Omega} = 9.09\ \text{mA}$$

### 3.4 Voltage drops and power

| Element | Voltage | Current | Power   | Rating                                                    | Margin        |
| ------- | ------- | ------- | ------- | --------------------------------------------------------- | ------------- |
| `R1`    | 3.0 V   | 9.09 mA | 27.3 mW | about 125 mW (0805)                                       | 22 % utilised |
| `D1`    | 2.0 V   | 9.09 mA | 18.2 mW | check datasheet                                           | well within   |
| `SW1`   | ≈ 0 V   | 9.09 mA | ≈ 0 mW  | typically about 50 mA (6 mm tact switch, check datasheet) | well within   |
| Total   | 5.0 V   | 9.09 mA | 45.5 mW |                                                           |               |

Resistor power from the formula: $P_R = I^2 R = (0.00909)^2 \times 330 = 27.3\ \text{mW}$.

### 3.5 Worst-case analysis

Assumptions: supply 5 V ± 5 % (4.75 V to 5.25 V), resistor 330 Ω ± 1 % (326.7 Ω to 333.3 Ω), LED forward voltage 1.8 V to 2.2 V.

| Case            | $V_S$  | $V_F$ | $R$     | Current  | Resistor power |
| --------------- | ------ | ----- | ------- | -------- | -------------- |
| Nominal         | 5.00 V | 2.0 V | 330.0 Ω | 9.09 mA  | 27.3 mW        |
| Maximum current | 5.25 V | 1.8 V | 326.7 Ω | 10.56 mA | 36.4 mW        |
| Minimum current | 4.75 V | 2.2 V | 333.3 Ω | 7.65 mA  | 19.5 mW        |

The LED current stays between 7.65 mA and 10.56 mA. Even at the maximum, the resistor dissipates 36.4 mW, which is under 30 % of an 0805 resistor's rating.

### 3.6 Alternative resistor values (nominal $V_S$ = 5 V, $V_F$ = 2 V)

| R         | Current     | Brightness          |
| --------- | ----------- | ------------------- |
| 220 Ω     | 13.6 mA     | Very bright         |
| **330 Ω** | **9.09 mA** | **Bright (chosen)** |
| 470 Ω     | 6.38 mA     | Medium              |
| 1 kΩ      | 3.00 mA     | Dim indicator       |

---

## 4. Schematic

### 4.1 Symbols and footprints

| Ref   | Library symbol                                | Value          | Footprint                                                    |
| ----- | --------------------------------------------- | -------------- | ------------------------------------------------------------ |
| `J1`  | `Connector:Conn_01x02_Pin`                    | Conn_01x02_Pin | `Connector_PinHeader_2.54mm:PinHeader_1x02_P2.54mm_Vertical` |
| `SW1` | `Switch:SW_Push`                              | SW_Push        | `Button_Switch_THT:SW_PUSH_6mm`                              |
| `R1`  | `Device:R`                                    | 330            | `Resistor_SMD:R_0805_2012Metric`                             |
| `D1`  | `Device:LED`                                  | RED            | `LED_SMD:LED_0805_2012Metric`                                |
| Power | `power:+5V`, `power:GND`, `power:PWR_FLAG` ×2 | n/a            | n/a                                                          |

### 4.2 Nets

| Net             | Connections            |
| --------------- | ---------------------- |
| `+5V`           | `J1`-1, `SW1`-1        |
| `Net-(R1-Pad1)` | `SW1`-2, `R1`-1        |
| `Net-(D1-A)`    | `R1`-2, `D1`-A (pad 2) |
| `GND`           | `D1`-K (pad 1), `J1`-2 |

### 4.3 Power flags and ERC

The header pins are electrically "passive", so KiCad cannot tell that the `+5V` and `GND` nets are driven by an external supply. A `PWR_FLAG` symbol on each net declares that, and removes the ERC error _"Input Power pin not driven by any Output Power pins"_.

### 4.4 Schematic conventions

- Signal flows left to right, positive supply at the top, ground at the bottom.
- Reference and value fields are visible and not overlapping.
- Schematic grid: 1.27 mm (50 mil), the KiCad default.
- Every part has a unique reference (Tools → Annotate Schematic) and an assigned footprint.

---

## 5. KiCad configuration

### 5.1 Environment

| Setting                | Value                                               |
| ---------------------- | --------------------------------------------------- |
| KiCad version          | 10.0.x (Help → About KiCad shows the exact patch)   |
| Units                  | Millimetres                                         |
| Schematic grid         | 1.27 mm                                             |
| PCB grid (recommended) | 0.5 mm for placement, 0.25 mm or 0.1 mm for routing |
| Libraries              | Standard KiCad libraries only                       |

### 5.2 Board stackup (File → Board Setup → Board Stackup)

| Item            | Value                       |
| --------------- | --------------------------- |
| Copper layers   | 2 (`F.Cu`, `B.Cu`)          |
| Board thickness | 1.6 mm                      |
| Copper weight   | 1 oz (35 µm) on both layers |
| Material        | FR-4                        |

### 5.3 Design constraints (Board Setup → Design Rules → Constraints)

| Setting                      | Value     | Reason                                                    |
| ---------------------------- | --------- | --------------------------------------------------------- |
| Minimum clearance            | 0.2 mm    | Fab minimum is 0.127 mm; this adds margin                 |
| Minimum track width          | 0.2 mm    | Fab minimum is 0.127 mm                                   |
| Minimum connection width     | 0.2 mm    | Prevents thin copper necks in pours                       |
| Minimum annular width        | 0.15 mm   | Fab minimum is 0.13 mm                                    |
| Minimum via diameter         | 0.6 mm    | Fab minimum is 0.5 mm; 0.6 mm is the safe standard        |
| Copper to hole clearance     | 0.3 mm    | Fab minimum is about 0.25 mm                              |
| Copper to edge clearance     | 0.5 mm    | Fab minimum is 0.2 mm; extra margin for routing tolerance |
| Minimum drill size           | 0.3 mm    | Fab minimum for 2-layer boards                            |
| Hole to hole clearance       | 0.5 mm    | Fab requirement between holes on different nets           |
| Silk: minimum item clearance | 0.15 mm   | Keeps text away from pads                                 |
| Silk: minimum text height    | 1.0 mm    | Smaller text prints as a blur                             |
| Silk: minimum text thickness | 0.15 mm   | Thinner strokes do not print reliably                     |
| uVia settings                | Unchanged | Microvias are not used on 2-layer boards                  |

### 5.4 Net classes (Board Setup → Design Rules → Net Classes)

| Class     | Clearance | Track width | Via diameter | Via drill | Assigned nets |
| --------- | --------- | ----------- | ------------ | --------- | ------------- |
| `Default` | 0.2 mm    | 0.25 mm     | 0.6 mm       | 0.3 mm    | all others    |
| `Power`   | 0.2 mm    | 0.5 mm      | 0.8 mm       | 0.4 mm    | `+5V`, `GND`  |

### 5.5 Pre-defined sizes (Board Setup → Design Rules → Pre-defined Sizes)

- Track widths: 0.25 mm, 0.5 mm
- Vias: 0.6 mm / 0.3 mm, 0.8 mm / 0.4 mm

### 5.6 Why "constraints" and "net classes" are different

The **constraints** are the _minimum floor_: DRC flags anything below them. The **net classes** are what you actually _draw with_. Keeping the floor at the fab's capability (plus margin) and drawing above it gives manufacturable boards with a safety buffer.

Fab capability values were taken from the JLCPCB capability pages (see [References](#11-references)). Always re-check your fab's current limits before ordering, as they change.

---

## 6. Layout

### 6.1 Placement strategy

- `J1` at the board edge where the power connection is made.
- Components placed in signal order: `J1` → `SW1` → `R1` → `D1`.
- Short, direct traces and no crossing nets.
- Polarity-sensitive parts (`J1` pin 1, `D1` cathode) are clearly marked on the silkscreen.

### 6.2 Routing rules

| Rule                             | Value                                       |
| -------------------------------- | ------------------------------------------- |
| Signal track width               | 0.25 mm                                     |
| Power track width (`+5V`, `GND`) | 0.5 mm                                      |
| Corner angles                    | 45° or curved, never 90°                    |
| Via count                        | 0 (single-layer routing with a ground pour) |
| Copper to board edge             | at least 0.5 mm                             |

### 6.3 Trace width calculation (IPC-2221)

For an external layer:

$$I = k \cdot \Delta T^{0.44} \cdot A^{0.725}$$

where $I$ is current in amps, $\Delta T$ is the allowed temperature rise in °C, $A$ is the cross-section area in mil², and $k = 0.048$ for external layers (0.024 for internal layers). Rearranged:

$$A = \left(\frac{I}{k \cdot \Delta T^{0.44}}\right)^{1/0.725} \qquad w = \frac{A}{t}$$

with copper thickness $t = 1.378$ mil for 1 oz copper (1 mil = 0.0254 mm).

**Minimum width for this circuit** ($I$ = 9.09 mA, $\Delta T$ = 10 °C):

$$A = \left(\frac{0.00909}{0.048 \times 10^{0.44}}\right)^{1.379} = 0.0249\ \text{mil}^2 \quad\Rightarrow\quad w = \frac{0.0249}{1.378} \approx 0.018\ \text{mil} \approx 0.0005\ \text{mm}$$

The electrical requirement is far below what any fab can manufacture, so **manufacturing limits decide the width, not current**.

**Current capacity of the chosen widths** (1 oz, external layer, 10 °C rise, IPC-2221 estimate):

| Width   | Approximate capacity |
| ------- | -------------------- |
| 0.25 mm | 0.9 A                |
| 0.5 mm  | 1.4 A                |

### 6.4 Copper pour

A `GND` zone fills unused copper area. It gives a low-resistance ground return, reduces etched copper, and is connected to pads through thermal reliefs so the parts remain solderable. Refill zones (press `B`) before every DRC run and before plotting.

### 6.5 Silkscreen

- Reference designators, polarity marks, and `+5V` / `GND` labels on the connector.
- Text height at least 1.0 mm and stroke at least 0.15 mm.
- No text over pads, and kept at least 0.5 mm from the board edge.

### 6.6 Board outline

A rectangle on `Edge.Cuts` with rounded corners, size 18.750 × 11.480 mm. The outline is a single closed shape with no gaps.

---

## 7. Verification

| Check            | How                                                         | Result                                                  |
| ---------------- | ----------------------------------------------------------- | ------------------------------------------------------- |
| ERC              | Schematic editor → Inspect → Electrical Rules Checker       | 0 errors                                                |
| DRC              | PCB editor → Inspect → Design Rules Checker                 | 0 errors, 0 unrouted                                    |
| Board statistics | PCB editor status bar                                       | 10 pads, 4 nets, 17 track segments, 0 vias              |
| 3D view          | PCB editor → View → 3D Viewer (Alt+3)                       | Footprints and orientation correct                      |
| Gerber check     | Third-party viewer (KiCad Gerber Viewer, Gerbv, tracespace) | Outline closed, holes aligned, silkscreen clear of pads |

Saved reports are in [`reports/`](reports/).

---

## 8. Manufacturing outputs

### 8.1 Gerber plot settings (File → Fabrication Outputs → Gerbers)

| Setting                             | Value                                                                                                 |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------- |
| Output directory                    | `fabrication/gerbers`                                                                                 |
| Layers                              | `F.Cu`, `B.Cu`, `F.Paste`, `B.Paste`, `F.Silkscreen`, `B.Silkscreen`, `F.Mask`, `B.Mask`, `Edge.Cuts` |
| Use Protel filename extensions      | On                                                                                                    |
| Subtract soldermask from silkscreen | On                                                                                                    |
| Check zone fills before plotting    | On                                                                                                    |
| Coordinate format                   | 4.6, unit mm                                                                                          |
| Use extended X2 format              | On                                                                                                    |
| Include netlist attributes          | On                                                                                                    |
| Disable aperture macros             | Off                                                                                                   |
| Use drill/place file origin         | Off                                                                                                   |
| Plot drawing sheet                  | Off                                                                                                   |

### 8.2 Drill file settings

| Setting                    | Value                    |
| -------------------------- | ------------------------ |
| Drill file format          | Excellon                 |
| Oval holes drill mode      | Use alternate drill mode |
| Drill origin               | Absolute                 |
| Drill units                | Millimetres              |
| Zeros format               | Decimal format           |
| Map file format (optional) | Gerber                   |

### 8.3 Suggested fab order options

| Option              | Value                         |
| ------------------- | ----------------------------- |
| Layers              | 2                             |
| Thickness           | 1.6 mm                        |
| Outer copper weight | 1 oz                          |
| Surface finish      | HASL (lead-free if available) |
| Quantity            | Fab minimum                   |

Verify the generated files in a Gerber viewer, zip the `gerbers` folder, and upload the zip.

---

## 9. Bring-up and testing

### 9.1 Before powering

1. Inspect the solder joints and orientation of `D1` (cathode towards `GND`).
2. With a multimeter in continuity mode, confirm there is **no short** between `+5V` and `GND` with the button released.

### 9.2 Powered test

1. Apply 5 V to `J1` (pin 1 = +5 V, pin 2 = GND). A current-limited bench supply is ideal.
2. Press `SW1`: the LED must light. Release: it must turn off.
3. Measure the voltages in the table below, and compute the current as $I = V_R / 330\ \Omega$.

| Measurement            | Expected                       | Measured |
| ---------------------- | ------------------------------ | -------- |
| Supply voltage at `J1` | 5.0 V                          |          |
| Voltage across `R1`    | about 3.0 V (2.8 V to 3.2 V)   |          |
| Voltage across `D1`    | about 2.0 V (1.8 V to 2.2 V)   |          |
| Calculated current     | about 9 mA (7.6 mA to 10.6 mA) |          |

---

## 10. Lessons learned

- Run ERC early: it catches missing power flags and unconnected pins before layout.
- Set the design constraints and net classes **before** routing, not after.
- Silkscreen text must be scaled to the board, not left at default sizes.
- Use a sensible grid (0.25 mm or 0.5 mm) so parts and traces align cleanly.
- Fill the title block of the schematic before exporting the PDF.
- A manufacturing-ready design is verified in a Gerber viewer, not only in KiCad.

---

## 11. References

- Ohm's law and basic circuit theory: any introductory electronics textbook
- IPC-2221, _Generic Standard on Printed Board Design_ (trace width and clearance guidance)
- KiCad documentation: <https://docs.kicad.org>
- JLCPCB capabilities: <https://jlcpcb.com/capabilities/pcb-capabilities>
- JLCPCB guide to generating Gerber and drill files in KiCad: <https://jlcpcb.com/help/article/how-to-generate-gerber-and-drill-files-in-kicad-7>
