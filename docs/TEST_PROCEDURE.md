# ELS_Interface — Test Procedure

This procedure is used to **verify that the board works** after assembly, before
installing it on the machine. It is written for a bench technician: every test
states what to measure, how to measure it, which value is expected and leaves a
space to note the result.

**What you need:** the assembled ELS_Interface board, a NUCLEO-G474RE, a 12 V
bench power supply, a multimeter, two 1 kOhm resistors. An oscilloscope is
recommended but not mandatory.

**How to read it:** the tests are numbered **T1** to **T18** and must be run
**in order**. If a test does not pass, stop and find the cause before going on:
the following tests assume the previous ones are correct.

---

## 1. Prerequisites

Before powering anything, check the following.

| # | What to check | Done |
|---|---|---|
| **P1** | Board **not powered**: resistance between +12 V and ground must be **above 10 kOhm** | |
| **P2** | Board not powered: resistance between **E5V** and ground must be **above 1 kOhm** (below 100 Ohm there is a short: do not power up) | |
| **P3** | Visual inspection: no solder bridge, no rotated component | |
| **P4** | **C1** (100 uF electrolytic) and **C5** (220 uF tantalum) mounted with the correct polarity | |
| **P5** | **F1** is the 1.1 A polyfuse and **F2** the 0.5 A one (not swapped) | |
| **P6** | The **Nucleo is not inserted** for tests T1 to T7 | |
| **P7** | The power supply is set to **12 V with a 300 mA current limit** | |
| **P8** | Every instrument has its ground connected to a single point (common ground) | |

---

## 2. The tests

### T1 — Power-up with no load

| Field | Content |
|---|---|
| **What it verifies** | that the board has no short circuits and that the converter starts |
| **Procedure** | 1. Connect the supply to **J3** (pin 1 = +12 V, pin 2 = ground). · 2. Set the voltage to 12.0 V. · 3. Read the current on the supply display. |
| **Expected result** | current **below 50 mA** |
| **Measured result** | |

### T2 — Converter output voltage (E5V)

| Field | Content |
|---|---|
| **What it verifies** | that converter U1 produces the correct 5 V |
| **Procedure** | 1. Board powered at 12 V. · 2. Put the positive probe on **L1 pad 2** (or C5 pad 1). · 3. Put the negative probe on ground. · 4. Read the voltage. |
| **Expected result** | **5.00 V +/- 0.15 V** |
| **Measured result** | |

*If the value is wrong:* check the solder joint of the U1 thermal pad, the
R1/R2 divider and the L1 and D2 solder joints.

### T3 — Voltage after the protection

| Field | Content |
|---|---|
| **What it verifies** | that F1 and D1 do not introduce abnormal drops |
| **Procedure** | 1. Board powered. · 2. Measure the voltage on **C1 pad 1**. |
| **Expected result** | **12.0 V** with a drop on F1 below 50 mV |
| **Measured result** | |

### T4 — Board rail (V_5V_SHIELD)

| Field | Content |
|---|---|
| **What it verifies** | that diode D4 feeds the internal rail correctly |
| **Procedure** | 1. Board powered at 12 V. · 2. Measure the voltage on **C12 pad 1**. |
| **Expected result** | **4.4 – 4.7 V** (the 5 V minus the diode drop) |
| **Measured result** | |

### T5 — Ripple on the converter output

| Field | Content |
|---|---|
| **What it verifies** | that the supply is "clean" and does not disturb the microcontroller |
| **Procedure** | 1. Connect the oscilloscope probe (10x, ground spring) to **L1 pad 2**. · 2. Enable the 20 MHz bandwidth limit. · 3. Read the peak-to-peak amplitude. |
| **Expected result** | **below 50 mV peak-to-peak** |
| **Measured result** | |

### T6 — Thermal check

| Field | Content |
|---|---|
| **What it verifies** | that nothing gets too hot |
| **Procedure** | 1. Leave the board powered for **5 minutes**. · 2. Measure the temperature of U1, L1 and D2 (contact thermometer or thermal camera). |
| **Expected result** | no component above **50 deg C**; U1 and L1 warm to the touch |
| **Measured result** | |

### T7 — Display branch

| Field | Content |
|---|---|
| **What it verifies** | that the display 12 V arrives and that F2 has not tripped |
| **Procedure** | 1. Board powered. · 2. Measure the voltage between **J7 pin 1** and ground. |
| **Expected result** | **12.0 V**, F2 not tripped |
| **Measured result** | |

### T8 — Drop across diode D4

| Field | Content |
|---|---|
| **What it verifies** | that D4 conducts correctly |
| **Procedure** | 1. Board powered at 12 V. · 2. Measure the voltage between the anode (E5V side) and the cathode (rail side) of D4. |
| **Expected result** | **0.35 – 0.45 V** |
| **Measured result** | |

### T9 — Diode D5 blocks the back-feed

| Field | Content |
|---|---|
| **What it verifies** | **the most important safety check of the board**: that the 5 V cannot enter the computer USB port |
| **Procedure** | 1. Power the board at 12 V (Nucleo not inserted). · 2. Insert a **1 Ohm shunt** in series with pin **CN10-8 (J2-8)**. · 3. Measure the voltage across the shunt and derive the current (I = V / 1 Ohm). |
| **Expected result** | current **below 10 uA** (practically zero) |
| **Measured result** | |

*This test must be repeated every time the supply circuit is modified.*

### T10 — Nucleo insertion

| Field | Content |
|---|---|
| **What it verifies** | that the shield powers the Nucleo board correctly |
| **Procedure** | 1. With the board **off**, insert the Nucleo checking pin 1. · 2. Set **JP5 on pins 5-6 (E5V)**. · 3. Power at 12 V. · 4. Measure the **3.3 V** on J1 pin 12. |
| **Expected result** | the Nucleo turns on; **3.28 – 3.32 V** |
| **Measured result** | |

### T11 — Green LED chain

| Field | Content |
|---|---|
| **What it verifies** | that LED1 and resistor R19 are fitted and working |
| **Procedure** | 1. Board **off**. · 2. Apply **3.3 V through a 1 kOhm resistor** on the **R19** pad on the J1-28 side. · 3. Look at the LED and measure the drop across R19. |
| **Expected result** | LED1 lights (dim); drop across R19 **0.32 – 0.42 V** |
| **Measured result** | |

### T12 — Red LED chain

| Field | Content |
|---|---|
| **What it verifies** | that LED2 and R18 are fitted correctly |
| **Procedure** | 1. Board off. · 2. Apply 3.3 V through 1 kOhm on the **R18** pad on the J1-30 side. |
| **Expected result** | LED2 lights; drop across R18 **0.7 – 1.5 V** |
| **Measured result** | |

### T13 — Optocoupler U3

| Field | Content |
|---|---|
| **What it verifies** | that the first isolated output works |
| **Procedure** | 1. Connect a test load (LED + 1 kOhm to an external +5 V) on **J8 pin 1**. · 2. Apply **5 V through 1 kOhm** on the **R12** pad. · 3. Measure the voltage between J8-1 and the rail. |
| **Expected result** | the load turns on; saturation voltage **below 0.4 V** |
| **Measured result** | |

### T14 — Optocouplers U4 and U5

| Field | Content |
|---|---|
| **What it verifies** | that the second and third isolated outputs work independently |
| **Procedure** | Repeat test T13 on the **R13** (J8-2) and **R14** (J8-3) pads. |
| **Expected result** | same behaviour, independent outputs, saturation **< 0.4 V** |
| **Measured result** | |

### T15 — Opto outputs off

| Field | Content |
|---|---|
| **What it verifies** | that the optos do not conduct when they are not driven |
| **Procedure** | 1. Remove the test supply used in T13/T14. · 2. Measure the residual current in the load. |
| **Expected result** | load off; residual current **below 10 uA** |
| **Measured result** | |

### T16 — Supplies and buttons on J5

| Field | Content |
|---|---|
| **What it verifies** | that the 5 V reaches the field connector and that the pull-up resistors are present |
| **Procedure** | 1. Measure between **J5-1** and ground: it must give the rail (4.4–4.7 V). · 2. Measure between **J5-11** (emergency) and ground. · 3. Short J5-11 to ground and measure again. · 4. Repeat on J5-15, 16, 14, 13 with the buttons. |
| **Expected result** | J5-1 = rail; J5-11 = **3.3 V at rest** and **< 0.3 V** when shorted to ground; buttons 3.3 V at rest, 0 V pressed |
| **Measured result** | |

### T17 — Level shifter U2 levels

| Field | Content |
|---|---|
| **What it verifies** | that the 3.3 V to 5 V level shifter works |
| **Procedure** | 1. Measure **U2 pin 1** (3.3 V side) and **U2 pin 14** (5 V side). · 2. Apply 3.3 V on U2 pin 2 (input A1). · 3. Measure the corresponding B1 side. |
| **Expected result** | pin 1 = 3.30 V; pin 14 = 4.4–4.7 V; B1 = about 0.95 x VCCB = **4.3 – 4.5 V** |
| **Measured result** | |

### T18 — Ground continuity

| Field | Content |
|---|---|
| **What it verifies** | that all grounds are really connected together |
| **Procedure** | 1. Board off. · 2. With the multimeter in continuity mode, check between the ground pins of all connectors: J1 (8, 19, 20, 22), J2 (9, 20), J3-2, J5-17, J7 (7, 8), J8-5. |
| **Expected result** | resistance **below 0.5 Ohm** between any pair of ground pins |
| **Measured result** | |

---

## 3. Acceptance criteria

The board is considered **tested** only if all of these points are satisfied.

| # | Criterion | Tolerance | Test |
|---|---|---|---|
| 1 | E5V voltage | 5.00 V +/- 0.15 V | T2 |
| 2 | Internal rail voltage | 4.4 – 4.7 V | T4 |
| 3 | Ripple on the converter output | < 50 mV peak-to-peak | T5 |
| 4 | **No back-feed into the USB port** | < 10 uA | T9 |
| 5 | Drop across diode D4 | 0.35 – 0.45 V | T8 |
| 6 | Temperature after 5 minutes | no component above 50 deg C | T6 |
| 7 | Saturation of the three isolated outputs | < 0.4 V each | T13, T14 |
| 8 | Residual current with the optos off | < 10 uA | T15 |
| 9 | Ground continuity | < 0.5 Ohm | T18 |

---

## 4. Registration sheet

Fill in this table during the test.

| Test | Description | Measured value | Result (OK / KO) |
|---|---|---|---|
| T1 | Power-up with no load | | |
| T2 | E5V voltage | | |
| T3 | Voltage after the protection | | |
| T4 | Internal rail | | |
| T5 | Ripple | | |
| T6 | Thermal check | | |
| T7 | Display branch | | |
| T8 | Drop across D4 | | |
| T9 | Back-feed blocked | | |
| T10 | Nucleo insertion | | |
| T11 | Green LED | | |
| T12 | Red LED | | |
| T13 | Opto U3 | | |
| T14 | Opto U4 and U5 | | |
| T15 | Optos off | | |
| T16 | Supplies and buttons J5 | | |
| T17 | Shifter U2 levels | | |
| T18 | Ground continuity | | |

---

## 5. Final outcome

| | |
|---|---|
| Test date | |
| Technician | |
| Board (serial number or revision) | |
| Tests passed | _____ / 18 |
| Acceptance criteria met | yes / no |
| Anomalies found | |
| Signature | |

---

## 6. Electrical design verification

Before the physical test, the design was checked with the **`kicad-mcp-pro`**
MCP server (KiCad 10.0.6) and with `kicad-cli`:

| Check | Result |
|---|---|
| ERC — electrical rules check of the schematic | **0 violations** |
| DRC — design rules check of the PCB | **0 active violations**, **0 unconnected items**, **3 excluded findings** (Local override) |
| Netlist | exported correctly, **56 components** |
| Schematic / PCB parity | **56 = 56**, no missing component |
| 3D models | **56 of 56** resolved |

> **Note:** this table covers the physical board. Until a bench test is run, the
> measured values stay empty.