# ELS_Interface — User Guide

**ELS_Interface** is an interface board (a *shield*) that plugs into the
**NUCLEO-G474RE** development board from STMicroelectronics. It connects the
microcontroller to everything that lives "outside": the 12 V supply, a display,
the encoders, the command buttons, the isolated outputs for the machine and a
memory that keeps its data when the board is powered off.

This guide is written for whoever assembles, connects and uses the board. It
explains where the supply goes, what every connector is for, how the indicator
LEDs are used and which settings must be made on the Nucleo board. It does not
describe the software.

---

## 1. Power supply

### 1.1 Two ways to power the system

The board can work in two configurations.

**"Machine" configuration — 12 V**

```
   12 V power supply          ELS_Interface board              Nucleo
   ─────────────────          ────────────────────             ──────
   +12 V ──► J3 pin 1 ──► F1 ──► D1 ──► U1 (buck 5 V) ──► E5V ──► CN7-6
   GND   ──► J3 pin 2 ──► common ground
```

**"Bench" configuration — USB cable only**

The Nucleo is powered by the computer through its USB cable. The board takes
its 5 V from that cable (protected by diode D5) and works the same way, with
no 12 V connected.

### 1.2 Protection on the 12 V input

| Component | What it is | What it does |
|---|---|---|
| **F1** | 1.1 A polyfuse | if something short-circuits, it interrupts the current and then resets itself |
| **D1** | protection diode (TVS) SMBJ15A | clips voltage spikes above about 24 V |
| **C1** | electrolytic capacitor 100 uF / 35 V | smooths the input voltage |
| **C3** | capacitor 22 uF / 25 V | fast filter close to the converter |

> **Warning:** **C1 and C5 are polarised.** The positive terminal (marked `+` on
> the board) must face the right side, otherwise the capacitor is damaged and
> may short the board.

### 1.3 From 12 V to 5 V: the converter U1

The heart of the supply is **U1**, a TPS5430 *buck* converter: it takes the
12 V, chops them at high frequency and produces a stable **5.00 V** with up to
3 A.

| Component | Value | What it does |
|---|---|---|
| **U1** | TPS5430 | the converter itself |
| **L1** | 22 uH inductor | stores energy on every cycle |
| **D2** | SS34 diode | carries the current during the "off" phase |
| **R1 / R2** | 10 kOhm / 3.24 kOhm | set the output voltage to **5.00 V** |
| **C5** | 220 uF / 6.3 V tantalum | smooths the output, **watch the polarity** |
| **C15** | 100 nF | filters high-frequency noise |

The converter output is called **E5V** and goes straight to pin **CN7-6**
(J1-6) of the Nucleo: that is what powers the microcontroller.

### 1.4 The board 5 V rail: D4 and D5

Besides the 5 V for the Nucleo, the board has a second 5 V rail called
**V_5V_SHIELD**, which powers the logic display, the level shifters and the
isolated outputs. This rail can receive current from **two different sources**,
and diodes D4 and D5 act as one-way valves: they let the current in and stop it
from going back.

| Diode | Lets current flow from | To | Works when |
|---|---|---|---|
| **D4** | E5V (the converter 5 V) | V_5V_SHIELD | the 12 V is connected |
| **D5** | the Nucleo USB cable | V_5V_SHIELD | the board runs on USB only |

The result is a rail at **4.4–4.7 V** (the difference from 5 V is the diode
drop). Every part on the board accepts this value.

> **Warning:** **never remove D5.** Without that diode the board 5 V would flow
> back into the USB port of the computer.

### 1.5 Display supply

The display needs 12 V. Its branch is protected by **F2** (0.5 A polyfuse) and
**D3** (SS34 diode), with **C6** (10 uF) as a filter. If F2 trips, the rest of
the board keeps working: only the display goes dark.

### 1.6 Voltages you should find

| Measurement point | Expected value | Where you measure |
|---|---|---|
| 12 V input | 12.0 V | J3 pin 1 |
| Converter output **E5V** | **5.00 V +/- 0.15 V** | L1 pad 2 |
| Board rail **V_5V_SHIELD** | **4.4–4.7 V** | C12 pad 1 |
| Display 12 V | 12.0 V | J7 pin 1 |
| 3.3 V (from the Nucleo) | 3.30 V | J1 pin 12 |

---

## 2. Connectors

### 2.1 J3 — 12 V supply input

Two-pole screw terminal, 3.5 mm pitch.

| Pin | Signal | What it is for |
|---|---|---|
| **1** | +12 V | the positive wire of the supply |
| **2** | GND | ground (the negative wire) |

### 2.2 J1 and J2 — the two sockets for the Nucleo

These are the sockets that hold the Nucleo board (morpho headers, 2x19 pins,
2.54 mm pitch). They are mounted on the **bottom** side of the board. Only a
few pins are actually used by the shield:

| Socket | Pin | Signal | What it is for |
|---|---|---|---|
| **J1** (CN7) | **6** | E5V | brings the 5 V to the Nucleo: this is the MCU supply |
| J1 | **28** | PA0 | lights LED1 |
| J1 | **30** | PA1 | lights LED2 |
| J1 | 2 | PC11 | SDA data line of the EEPROM memory |
| J1 | 12, 16 | 3.3 V | reference supply and pull-up resistors |
| J1 | 8, 19, 20, 22 | GND | ground |
| **J2** (CN10) | **8** | USB VBUS | the 5 V of the USB cable (input of diode D5) |
| J2 | 2 | PC8 | SCL clock line of the EEPROM memory |
| J2 | 9, 20 | GND | ground |

> **Warning:** before inserting the Nucleo check that **pin 1** of J1 and J2 is
> aligned. Always insert the board with the power off.

### 2.3 J5 — field inputs and outputs

Ribbon (IDC) connector, 2x10 pins. Encoders, pushbuttons and the emergency
stop are connected here.

| Pin | Signal | What it is for |
|---|---|---|
| **1** | 5 V (board rail) | supply for external devices |
| 2 | GND | spindle encoder ground |
| 3 | PC0 — spindle encoder A | first channel of the spindle encoder |
| 4 | PC1 — spindle encoder B | second channel of the spindle encoder |
| 5 | PC4 | free auxiliary signal |
| 6 | GND | jog encoder ground |
| **7** | 5 V (board rail) | second supply output |
| 8 | PA6 — jog encoder A | first channel of the manual (jog) encoder |
| 9 | PA4 — jog encoder B | second channel of the jog encoder |
| 10 | GND | pushbutton ground |
| **11** | PB7 — EMERGENCY | the emergency stop (closed to ground) |
| 12 | — | not connected |
| **13** | PB10 — RESET button | reset pushbutton |
| **14** | PB12 — MODE button | mode selection pushbutton |
| **15** | PB14 — JOG + button | manual feed pushbutton (plus) |
| **16** | PA15 — JOG − button | manual feed pushbutton (minus) |
| **17** | GND | ground |
| 18–20 | — | not connected |

All these signals pass through the level shifter **U2 (TXB0104)**, which adapts
the 3.3 V of the microcontroller to the 5 V of the outside world.

### 2.4 J7 — serial display

8-pole connector, 2.00 mm pitch, for the DWIN display.

| Pin | Signal | What it is for |
|---|---|---|
| **1, 2** | display 12 V | display supply (pin 2 in parallel to carry more current) |
| 3 | — | not connected |
| **4** | DWIN_RX | data coming **from** the display to the board (through R4, 100 Ohm) |
| **5** | DWIN_TX | data going **from** the board to the display |
| 6 | — | not connected |
| **7, 8** | GND | ground |

### 2.5 J8 — isolated outputs

5-pole terminal. The first three outputs pass through an optocoupler (PC817):
the signal crosses a layer of light, so the board and the machine are
**electrically separated** — a surge in the machine cannot damage the
microcontroller.

| Pin | Signal | What it is for |
|---|---|---|
| **1** | U3 opto output | load driven by optocoupler 1 |
| **2** | U4 opto output | load driven by optocoupler 2 |
| **3** | U5 opto output | load driven by optocoupler 3 |
| **4** | PC9 | direct digital output, **not** isolated |
| **5** | GND | common ground for the loads |

The load must be supplied externally and returned to the output pin.

---

## 3. Debug LEDs

The board has two LEDs that show what the system is doing without any
instrument.

| LED | Colour | MCU pin | Resistor | Current |
|---|---|---|---|---|
| **LED1** | green | **PA0** (from J1-28) | R19 = 47 Ohm | 4–18 mA |
| **LED2** | red | **PA1** (from J1-30) | R18 = 100 Ohm | 7–15 mA |

**How they work:** the microcontroller pin goes **high** (3.3 V), the current
flows through the resistor, lights the LED and returns to ground. A **low** pin
(0 V) means the LED is off.

**Practical examples:**

- To light the green LED, drive **PA0** high.
- To light the red LED, drive **PA1** high.
- To make them blink, alternate high and low with a delay.
- If an LED stays dark, check the polarity: the **cathode** (the side with the
  notch) goes to ground, the **anode** towards the resistor.

> **Note:** the green LED has a high forward voltage (2.6–3.1 V) and stays
> clearly visible with 47 Ohm. If more light is needed it can go down to
> 33 Ohm, without exceeding 25 mA.

The Nucleo also carries a third LED (LD2, connected to PA5): that one belongs
to the Nucleo board, not to the shield.

---

## 4. Jumper on the Nucleo: JP5

The **Nucleo** board (not the shield) carries a 4x2 jumper header **JP5** that
decides **where the Nucleo takes its 5 V from**. It is the only manual setting
of the system.

| JP5 position | 5 V source | When to use it |
|---|---|---|
| 1-2 | Nucleo USB cable | bench use, USB only |
| 3-4 | VIN | supply from the VIN pin |
| **5-6** | **E5V (from the ELS_Interface board)** | **normal use with the 12 V connected** |
| 7-8 | VBUS direct | special use, not recommended |

**Practical example:** you are powering the board from 12 V and want the Nucleo
powered too, so set JP5 on pins **5-6** (the Nucleo silkscreen says "E5V").

**Practical example:** you are at the bench with only the USB cable and no
12 V, so set JP5 on pins **1-2** ("5V_STLK").

With JP5 on 5-6 the programming section of the Nucleo stays powered from the
shield, so the microcontroller can be programmed **even with the 12 V
connected**.

---

## 5. Things you must not do

> **1. Do not power from 12 V with JP5 on 1-2 or 7-8.**
> You would put two 5 V sources on the same MCU rail. The jumper must be on
> **5-6**.

> **2. Do not remove D5.**
> It is the diode that stops the board 5 V from entering the computer USB port.

> **3. Do not exceed 500 mA on the E5V pin (CN7-6).**
> That is the limit of the regulator inside the Nucleo.

> **4. Respect the polarity of C1 and C5.**
> A reversed electrolytic or tantalum capacitor is damaged and may short.

> **5. Do not bypass F1, F2, D1, D3.**
> They are the protection of the board and of the Nucleo: they matter exactly
> when something goes wrong.

> **6. Do not connect inductive loads (relays, motors, solenoid valves) to J5.**
> Loads belong on the isolated outputs of **J8**.

> **7. Always insert the Nucleo with the board powered off,**
> checking that pin 1 is aligned.

> **8. The 12 V supply must be EN 62368-1 certified.**
> It is an ST requirement for the E5V input.

---

## 6. Design verification

The board described in this guide was checked with the **`kicad-mcp-pro`** MCP
server (KiCad 10.0.6) and with `kicad-cli`:

| Check | Result |
|---|---|
| ERC — electrical rules check of the schematic | **0 violations** |
| DRC — design rules check of the PCB | **0 active violations**, **0 unconnected items** |
| Netlist | exported correctly, **56 components** |
| Schematic / PCB parity | **56 = 56**, no missing component |
| 3D models | **56 of 56** resolved |