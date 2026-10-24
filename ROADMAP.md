# Learning Roadmap

A progression of **small, simple circuits** that teach how electronic components work, from the first resistor to a complete microcontroller board. Every board is designed in KiCad, manufactured on a low-cost 2-layer process, and fully documented.

## Ground rules for every project

| Rule | Why |
|---|---|
| One new idea per board | So you can see exactly what each component does |
| About 12 parts or fewer | Small enough to understand every connection |
| Through-hole parts and 0805 SMD only | Hand-solderable, no special equipment |
| 2-layer board, standard KiCad libraries | Cheap to build and easy to reproduce |
| Calculate first, then build, then measure | Theory is checked against reality |

## Every project documents

- Schematic with a parts table and a netlist
- Step-by-step calculations with formulas and worst-case analysis
- **Timing or waveform diagram** wherever signals change over time (oscillators, logic, switching, filters)
- KiCad board setup, layout rules and the reasons for them
- Saved ERC and DRC reports
- Manufacturing files (Gerbers, drill files, BOM)
- A bring-up and test procedure with expected measurements

## The projects

### Level 1: Beginner (meet the components)

| # | Project | Track | New components and ideas | Documentation extras |
|---|---|---|---|---|
| 1 | LED + Push-Button | Basics | Resistor, LED, switch. Ohm's law, current limiting | Calculation tables |
| 2 | 555 LED Blinker | Timing | Capacitor, timer IC. RC time constant, oscillation, decoupling | **Timing diagram** |
| 3 | Voltage Divider, Potentiometer and Light Sensor | Analog | Resistor networks, potentiometer, LDR, test points. Divider formula, loading effect | Transfer curve |
| 4 | NPN Transistor Switch | Analog and power | BJT, base resistor, flyback diode. Transistor as a switch, saturation | Switching waveform |
| 5 | Diode Power Entry and 5 V Linear Regulator | Power | Diode, electrolytic and ceramic capacitors, linear regulator. Reverse-polarity protection, heat dissipation | Voltage and power budget |

### Level 2: Intermediate (analog and digital building blocks)

| # | Project | Track | New components and ideas | Documentation extras |
|---|---|---|---|---|
| 6 | RC Low-Pass Filter | Analog | Passive filter. Cutoff frequency, step response | **Step response and frequency response** |
| 7 | Op-Amp Buffer and Amplifier | Analog | Op-amp, feedback. Gain, single-supply operation, output swing | Input/output transfer curve |
| 8 | Comparator Night-Light | Analog to digital | Comparator, LDR, hysteresis. Turning a sensor into a switch | **Hysteresis diagram** |
| 9 | Logic Gates Trainer | Digital | 74HC gates, DIP switches, LEDs. Truth tables, logic levels, pull-down resistors | Truth tables and **timing diagram** |
| 10 | Switch Debouncer and D Flip-Flop | Digital | SR latch, D flip-flop. Contact bounce, memory, clocked logic | **Bounce and latch timing diagram** |

### Level 3: Advanced (small systems that combine the blocks)

| # | Project | Track | New components and ideas | Documentation extras |
|---|---|---|---|---|
| 11 | LED Chaser (555 + Decade Counter) | Digital | 74HC4017 counter, clock distribution. Sequential logic | **Ten-output timing diagram** |
| 12 | MOSFET Load Switch and PWM Dimmer | Power and analog | N-MOSFET, gate drive, 555 as PWM source. Duty cycle control | **PWM waveform diagram** |
| 13 | Buck Converter | Power | Inductor, switching regulator IC. Efficiency, switching-loop layout | Switching waveforms |
| 14 | Digital Counter with 7-Segment Display | Digital | 555 clock, CD4026 counter and decoder, 7-segment display | **Counting timing diagram** |
| 15 | Microcontroller Starter Board (ATtiny) | Digital, analog and power | MCU, programming header, reset, ADC input. Combines all previous ideas | Pin and signal timing |

## Coverage by track

| Track | Projects |
|---|---|
| Analog | 3, 4, 6, 7, 8, 12, 15 |
| Digital | 2, 9, 10, 11, 14, 15 |
| Power | 4, 5, 12, 13, 15 |

## Components you will have used by the end

Resistor, potentiometer, LDR, capacitor (ceramic and electrolytic), LED, diode, NPN transistor, N-MOSFET, inductor, voltage regulator, 555 timer, op-amp, comparator, logic gates, flip-flop, counter, decoder, switching regulator, 7-segment display, microcontroller.

## Status

| # | Project | Status |
|---|---|---|
| 1 | [LED + Push-Button](01-led-button/) | Done |
| 2 | [555 LED Blinker](02-555-blinker/) | Done |
| 3 | [Voltage Divider, Potentiometer and Light Sensor](03-voltage-divider/) | In progress |
| 4 to 15 | See the tables above | Planned |

The plan may be adjusted as the projects progress.
