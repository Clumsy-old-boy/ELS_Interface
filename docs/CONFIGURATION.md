# ELS_Interface — Hardware Configuration

This document explains **how the board is wired**: which microcontroller pin
goes where, which connectors carry the signals to the outside world and at
which voltages everything works.

It contains no software: it describes only the physical connection between the
components. It is meant for whoever writes the program (to know which pin to
use), for whoever connects external equipment, and for whoever has to find a
fault.

**Board:** NUCLEO-G474RE with the **STM32G474RET6** microcontroller (LQFP64
package). The shield sits on the two morpho headers.

---

## 1. How to read this document

Before the tables, three names that come up all the time:

| Term | Meaning | Example |
|---|---|---|
| **PAn / PBn / PCn** | name of a microcontroller pin | **PA0** = pin 0 of port A |
| **J1, J2, J3, J5, J7, J8** | name of a connector on the board | **J5** = the 2x10 field connector |
| **Net** | name given to an electrical connection | **E5V** = the 5 V line of the converter |

When a table says "PA0 → J1-28" it means: pin PA0 of the microcontroller is
connected to **pin 28 of connector J1**, so the signal is also available at the
connector.

---

## 2. Pins and peripherals

### 2.1 Digital outputs

| MCU pin | Connected to | What it does | Note |
|---|---|---|---|
| **PA0** | R19 (47 Ohm) → LED1 | lights the green LED | also available on J1-28 |
| **PA1** | R18 (100 Ohm) → LED2 | lights the red LED | also available on J1-30 |
| **PA5** | LD2 LED of the Nucleo | LED of the Nucleo board | no component on the shield |
| **PA8** | connector J1 | free output | available on the morpho |
| **PA9** | connector J1 | free output | available on the morpho |

All of these work at **3.3 V** and can source about 8 mA each.

**Practical example:** to light the green LED just drive PA0 high; the LED stays
off while PA0 is low.

### 2.2 Serial lines (towards the display)

| MCU pin | Signal | Connected to | Level | Note |
|---|---|---|---|---|
| **PB6** | DWIN_TX | J7 pin 5 | 5 V | transmits towards the display |
| **PA10** | DWIN_RX | J7 pin 4 | 5 V | receives from the display through **R4 (100 Ohm)** |
| PA2 | — | Nucleo board | — | Nucleo debug console |
| PA3 | — | Nucleo board | — | Nucleo debug console |

PA10 and PB6 are **5 V tolerant** pins: the display can drive them directly,
with no level shifter. R4 is essential: it limits the current spikes along the
cable.

### 2.3 EEPROM memory (I2C bus)

| Signal | MCU pin | Memory pin | Components | Voltage |
|---|---|---|---|---|
| **SCL** (clock) | **PC8** | 6 | **R16 = 4.7 kOhm** to 3.3 V | 3.3 V |
| **SDA** (data) | **PC11** | 5 | **R17 = 4.7 kOhm** to 3.3 V | 3.3 V |
| WC (write protect) | — | 7 | connected to ground | writing always allowed |
| Supply | — | 8 | 3.3 V + **C14 (100 nF)** | 3.3 V |
| Address A0-A1-A2 | — | — | all to ground | address **0x50** |

The memory is an **M24M01E** of 1 Mbit: it keeps its data when the board is
powered off (for example machine parameters or positions).

> **Open point:** on the schematic the memory is wired to **PC8** and **PC11**.
> Before using the bus, confirm that the microcontroller I2C peripheral can be
> routed to this pin pair; otherwise the bus has to be driven as two ordinary
> digital lines.

### 2.4 Encoders

| Signal | MCU pin | Channel | Connector | Note |
|---|---|---|---|---|
| JOG_ENC_A | **PA6** | TIM3_CH1 | J5-8 | manual (jog) encoder |
| JOG_ENC_B | **PA4** | TIM3_CH2 | J5-9 | second channel of the same encoder |
| PC0 | **PC0** | TIM1_CH1 | J5-3 | spindle encoder, channel A |
| SPINDLE_ENC_B | **PC1** | TIM1_CH2 | J5-4 | spindle encoder, channel B |

Encoder signals are **two square waves 90 degrees apart**: from the sequence
the microcontroller knows how far and in which direction the axis is turning.
Pins PA6, PA4 and PC1 are not 5 V tolerant: they reach J5 through the level
shifter **U2**.

### 2.5 Buttons and emergency stop

| Signal | MCU pin | Connector | Components | Behaviour |
|---|---|---|---|---|
| BTN_JOG_PLUS | **PB14** | J5-15 | internal pull-up | **active low** |
| BTN_JOG_MINUS | **PA15** | J5-16 | internal pull-up | **active low** |
| BTN_MODE | **PB12** | J5-14 | internal pull-up | **active low** |
| BTN_RESET | **PB10** | J5-13 | internal pull-up | **active low** |
| **EMERGENCY** | **PB7** | J5-11 | **R15 = 47 kOhm** to 3.3 V | **active low** |

**What "active low" means:** at rest the pin sits at 3.3 V; when you press the
button the pin drops to 0 V. A button wired to ground is therefore normal.

> **Warning:** **PB7 is dedicated to the emergency stop** and must not be
> reassigned: it is the pin that guarantees the immediate stop.

### 2.6 Pins reserved for programming

| Pin | Signal | Note |
|---|---|---|
| PA13 | SWDIO | reserved for programming, do not use for anything else |
| PA14 | SWCLK | reserved for programming |
| PB3 | SWO | reserved for the debug trace |

### 2.7 Supplies available on the connectors

| Rail | Connector | Voltage | Max current |
|---|---|---|---|
| **E5V** | J1-6 | 5.00 V | **500 mA** (Nucleo limit) |
| **V_5V_SHIELD** | J5-1 and J5-7 | 4.4–4.7 V | about 100 mA in total |
| **Display 12 V** | J7-1 and J7-2 | 12 V | 0.5 A (F2 limit) |
| **3.3 V** | J1-12 and J1-16 | 3.30 V | supplied by the Nucleo |

---

## 3. Hardware to connector map

The master table, to use when you need to find "where this signal ends up".

| Signal | MCU pin | Components on the board | Connector | Voltage |
|---|---|---|---|---|
| +12 V input | — | F1, D1, C1, C3 | J3-1 | 12 V |
| Ground | — | — | J3-2, J8-5, J7-7/8 | 0 V |
| **E5V** | — | L1, C5, C15, D4 anode | **J1-6** | 5.00 V |
| V_5V_SHIELD | — | D4, D5, C12 | J5-1, J5-7 | 4.4–4.7 V |
| USB VBUS | — | D5 anode | J2-8 | 5 V |
| Display 12 V | — | F2, D3, C6 | J7-1, J7-2 | 12 V |
| Green LED | **PA0** | R19 47 Ohm → LED1 | J1-28 | 3.3 V |
| Red LED | **PA1** | R18 100 Ohm → LED2 | J1-30 | 3.3 V |
| DWIN_TX | **PB6** | — | J7-5 | 5 V |
| DWIN_RX | **PA10** | R4 100 Ohm | J7-4 | 5 V |
| Memory SCL | **PC8** | R16 4.7 kOhm, C14 | J2-2 | 3.3 V |
| Memory SDA | **PC11** | R17 4.7 kOhm | J1-2 | 3.3 V |
| Jog encoder A | **PA6** | U2 (A1→B1) | J5-8 | 3.3 → 4.6 V |
| Jog encoder B | **PA4** | U2 (A2→B2) | J5-9 | 3.3 → 4.6 V |
| Spindle encoder A | **PC0** | — | J5-3 | 3.3 V |
| Spindle encoder B | **PC1** | U2 (A3→B3) | J5-4 | 3.3 → 4.6 V |
| Auxiliary signal | **PC4** | — | J5-5 | 3.3 V |
| Emergency | **PB7** | R15 47 kOhm | J5-11 | 3.3 V, active low |
| Reset button | **PB10** | internal pull-up | J5-13 | active low |
| Mode button | **PB12** | internal pull-up | J5-14 | active low |
| Jog + button | **PB14** | internal pull-up | J5-15 | active low |
| Jog − button | **PA15** | internal pull-up | J5-16 | active low |
| Isolated output 1 | via U3 | R12 220 Ohm, PC817 | J8-1 | open collector |
| Isolated output 2 | via U4 | R13 220 Ohm, PC817 | J8-2 | open collector |
| Isolated output 3 | via U5 | R14 220 Ohm, PC817 | J8-3 | open collector |
| Direct output | **PC9** | — | J8-4 | 3.3 V |
| +3.3 V | — | R16, R17, C13, C14 | J1-12, J1-16 | 3.3 V |

---

## 4. Logic levels

The board works with **three different voltages**, and this is where most
mistakes happen. Every "family" of signals is separated from the others.

| Domain | Voltage | What lives there |
|---|---|---|
| **Microcontroller** | **3.3 V** | Nucleo, EEPROM, I2C pull-ups, LEDs, opto series resistors |
| **Board (internal rail)** | **4.4–5.0 V** | supply of shifter U2, opto collectors, 5 V pins of J5 |
| **Field (external)** | **5 V** supplied by you | buttons, encoders, output loads |
| **Display** | **12 V** power + **5 V** signals | connector J7 |

**The bridge between 3.3 V and 5 V** is the level shifter **U2 (TXB0104)**: it
takes a signal on one side and repeats it on the other with the right voltage.
Its main pins:

| U2 pin | Function | Connected to |
|---|---|---|
| 1 and 8 | VCCA (3.3 V side) | 3.3 V + C13 (100 nF) |
| 14 | VCCB (5 V side) | V_5V_SHIELD + C12 (100 nF) |
| 2, 3, 4 | inputs A1, A2, A3 | PA4, PA6, PC1 |
| 11, 12, 13 | outputs B1, B2, B3 | J5 pins |

> **Rule of thumb:** a signal going from the microcontroller (3.3 V) to the
> outside (5 V) **must** pass through U2. A 5 V signal entering a
> non-tolerant pin damages it.

---

## 5. Design notes

| # | Note |
|---|---|
| **N1** | **R1 and R2** (10 kOhm and 3.24 kOhm) set the converter output to 5.00 V. If you change them, E5V changes too and the 4.75–5.25 V window required by the Nucleo must be re-verified |
| **N2** | **D4 and D5 are in SOD-123** packages (pads 0.9 x 1.2 mm). A SOD-123FL part does not fit: it is not a compatible substitute |
| **N3** | The thermal vias under **U1** have a 0.150 mm annular ring, against the 0.180 mm recommended by JLCPCB for manufacturing. They are still above the 0.130 mm absolute minimum, but they are the most critical point of the board |
| **N4** | The **V_5V_SHIELD** rail always sits between 4.4 and 4.7 V because of the diode drop: devices connected to J5 must accept this voltage |
| **N5** | The **PC8/PC11** I2C pair is the only design point still to be confirmed (see §2.3) |

---

## 6. Design verification

The configuration described here was checked with the **`kicad-mcp-pro`** MCP
server (KiCad 10.0.6) and with `kicad-cli`:

| Check | Result |
|---|---|
| ERC — electrical rules check of the schematic | **0 violations** |
| DRC — design rules check of the PCB | **0 active violations**, **0 unconnected items** |
| Netlist | exported correctly, **56 components** |
| Schematic / PCB parity | **56 = 56**, no missing component |
| 3D models | **56 of 56** resolved |
| DFM (JLCPCB) | 2 layers, minimum track 0.25 mm, via 0.40/0.80 mm |