# KiCad Learning Path

![KiCad 10](https://img.shields.io/badge/KiCad-10.x-314CB0?logo=kicad&logoColor=white)
![Projects](https://img.shields.io/badge/projects-1%20of%206-informational)
![License: MIT](https://img.shields.io/badge/license-MIT-blue)

> My hands-on journey from complete beginner to advanced PCB design with KiCad.
> Every project introduces new electronics concepts and PCB design skills, and is documented with schematics, calculations, layout rules, verification reports and manufacturing files.

## Projects

| #   | Project                                 | Preview                                                                                    | Concepts and skills                                                 | Status  |
| --- | --------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------------------------------------------------------- | ------- |
| 1   | [LED + Push-Button](01-led-button/)     | <img src="01-led-button/docs/images/pcb-3d-top.png" width="160" alt="Project 1 3D render"> | Ohm's law, LED current limiting, full KiCad workflow, Gerber export | Done    |
| 2   | 555 Timer LED Blinker                   |                                                                                            | Capacitors, RC timing, ICs, decoupling, ground pour                 | Planned |
| 3   | Transistor / MOSFET Driver              |                                                                                            | Switching, flyback diodes, power traces, net classes                | Planned |
| 4   | USB-C to 3.3 V Power Board              |                                                                                            | Regulators, protection, custom footprints, BOM                      | Planned |
| 5   | Microcontroller Board (RP2040 or ESP32) |                                                                                            | Crystals, USB data, hierarchical sheets, 4-layer stackup            | Planned |
| 6   | Advanced Capstone                       |                                                                                            | Impedance control, EMI, thermal design, DFM, assembly files         | Planned |

## What every project includes

- **README**: overview, specifications, images, bill of materials, netlist, testing and status
- **Design notes**: electronics theory, step-by-step calculations, KiCad configuration, layout rules
- **Verification**: saved ERC and DRC reports and a Gerber viewer check
- **Manufacturing files**: Gerbers, drill files and BOM, ready for a low-cost fab

## Repository structure

```
kicad-learning-path/
├── README.md                 this index
├── LICENSE
├── .gitignore
├── 01-led-button/
│   ├── README.md
│   ├── *.kicad_pro / *.kicad_sch / *.kicad_pcb
│   ├── fabrication/          Gerbers, drill files, BOM
│   └── docs/                 design notes, PDF, images, reports
├── 02-555-blinker/           (coming next)
└── ...
```

## Tools

| Tool       | Version                                       |
| ---------- | --------------------------------------------- |
| KiCad      | 10.x                                          |
| Libraries  | Standard KiCad libraries only                 |
| Target fab | Low-cost 2-layer service (for example JLCPCB) |

## How to open a project

1. Install [KiCad](https://www.kicad.org/download/).
2. Clone or download this repository.
3. Open the `.kicad_pro` file inside any project folder.

## License

Released under the [MIT License](LICENSE).

Author: **Nitish Ghadage** · [GitHub](https://github.com/nitu2126)
