<!--
  TYPE: index
  TITLE: The 100x Audio Builder Guide
  CHAPTERS: 44
  PARTS: 8
  APPENDICES: 3
  UPDATED: 2026-04-10
  STRUCTURE: domain-towers-interleaved
  AI_NOTE: Each chapter file contains an HTML comment metadata block with CHAPTER, TITLE, PART, PREREQS, KEY_TOPICS, DIFFICULTY, UPDATED fields. Use these for filtering, prerequisite graphs, and topic search. Files are organized into subdirectories by Part. The guide uses domain towers (self-contained beginner-to-advanced per domain) with interleaved theory-practice within each tower.
-->

# The 100x Audio Builder Guide
### From Ohm's Law to Sound System

A comprehensive, opinionated guide for the software engineer who wants to build audio gear — deeply enough to reason about circuits, design custom systems for any use case, and potentially sell their work. Covers guitar pedals, speakers, amplifiers, and digital audio (Raspberry Pi streaming/DSP).

**44 chapters** · **8 Parts** · **3 Appendices** · **10 build projects** · **~$1,100-2,300 in materials**

```
100x-audio-builder-guide/

Part 0  — Electronics for Audio People              ← Electricity, components, schematics, simulation, safety
Part 1  — Guitar Pedals                             ← Signal theory, gain/clipping, builds: fuzz → overdrive → delay
Part 2  — Speakers                                  ← Acoustics, Thiele-Small, crossovers, cabinets, builds: bookshelf → towers → PA
Part 3  — Amplifiers                                ← Amp theory, solid-state, tubes, safety, builds: chip amp → Class D → tube amp
Part 4  — Digital Audio & The Pi Streamer            ← DAC/ADC, streaming, DSP, builds: streamer → crossover → full hub
Part 5  — PCB Design & Professional Builds           ← KiCad, enclosures, finishing, QC
Part 6  — System Design & Integration               ← Matching, room acoustics, complete system builds
Part 7  — From Hobby to Hustle                      ← Sourcing, costing, branding, scaling

├── appendices/                                      ← Glossary, suppliers, resources
└── README.md                                        ← You are here
```

---

## Who This Guide Is For

You're a software engineer who wants a new creative dimension. You've built systems that handle millions of requests, and now you want to build systems that handle sound waves. Maybe you want a killer home listening setup you designed yourself. Maybe you want a party system that fills a backyard. Maybe you play guitar and want pedals that sound exactly the way you hear them in your head. Maybe you see a future where this becomes a side business — building custom audio gear for people who care about sound.

This guide is for you if:
- You can solder and have basic tools, but you've never applied it to audio
- You want to *understand* electronics, not just follow instructions
- You want to read a schematic and know what it'll sound like before you build it
- You want to use simulation tools the way you use a debugger — to reason about behavior
- You want to speak the industry jargon so you can read datasheets, forums, and specs fluently
- You want professional-grade knowledge, not just hobby-level builds
- You might want to sell what you build someday

---

## The Structure: Domain Towers

This guide is organized as **domain towers** — each Part covers one domain (pedals, speakers, amps, digital) from beginner to advanced. Within each tower, theory chapters come first (so you understand *why* before you build), followed by build chapters that apply everything you learned.

**Every tower is self-contained.** After completing Part 0 (electronics fundamentals), you can read any domain tower independently. Want to just build speakers? Read Part 0 → Part 2. Only interested in guitar pedals? Part 0 → Part 1.

The towers converge in Part 6 (System Design), where you combine speakers + amps + digital into complete sound systems.

---

## Quick Start — Reading Paths

| Your Goal | Start Here | Then |
|-----------|-----------|------|
| **Learn electronics from scratch** | Part 0: Ch 0 → 1 → 2 → 3 → 4 | Any domain tower |
| **Build guitar pedals** | Part 0, then Part 1: Ch 5 → 6 → 7 → 8 → 9 → 10 | Part 5 (PCB design) |
| **Build speakers** | Part 0, then Part 2: Ch 11 → 12 → 13 → 14 → 15 → 16 → 17 | Part 6 (system design) |
| **Build amplifiers** | Part 0, then Part 3: Ch 18 → 19 → 20 → 21 → 22 → 23 → 24 | Part 6 (system design) |
| **Build a Pi streamer** | Part 0, then Part 4: Ch 25 → 26 → 27 → 28 → 29 → 30 | Part 6 (system design) |
| **Build a complete home hi-fi system** | Part 0 → Part 2 → Part 3 → Part 4 → Part 6: Ch 38 | Done |
| **Build a party PA system** | Part 0 → Part 2 → Part 3 → Part 4 → Part 6: Ch 39 | Done |
| **Go professional / sell your gear** | Complete any domain tower → Part 5 → Part 7 | Done |
| **Quick lookup** | [Glossary](./appendices/appendix-glossary.md) | [Suppliers](./appendices/appendix-suppliers.md) |

---

## [Part 0 — Electronics for Audio People](./part-0-electronics/)
*The shared foundation. Every domain tower builds on this. You'll learn to reason about circuits the way you reason about code.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 0 | [Electricity Through the Lens of Sound](./part-0-electronics/00-electricity-through-sound.md) | Beginner | Voltage as signal, current as flow, impedance, AC vs DC, frequency, phase, power/watts, decibels, Ohm's law and Kirchhoff's laws for audio |
| 1 | [Components and What They Do to Signals](./part-0-electronics/01-components-and-signals.md) | Beg→Inter | Resistors, capacitors, inductors, diodes, transistors, op-amps — each explained through what they do to audio signals |
| 2 | [Reading Schematics and Datasheets](./part-0-electronics/02-reading-schematics-datasheets.md) | Beginner | Schematic symbols, signal flow, power rails, block diagrams, reading datasheets, app notes, identifying standard circuit blocks |
| 3 | [Simulation and Analysis Tools](./part-0-electronics/03-simulation-analysis-tools.md) | Beg→Inter | LTspice, Falstad, KiCad schematic capture, breadboard prototyping, audio probes, signal tracing |
| 4 | [Your Workbench, Tools, and Safety](./part-0-electronics/04-workbench-tools-safety.md) | Beginner | Soldering refinement, desoldering, multimeter deep dive, oscilloscopes, power supplies, mains safety, capacitor discharge |

> **Tower exit state:** You can read a schematic, identify what each block does, simulate the circuit in SPICE, predict its behavior, and prototype it on a breadboard.

---

## [Part 1 — Guitar Pedals](./part-1-guitar-pedals/)
*Your first audio circuits. Low voltage, cheap, fast feedback loops — and you can hear the result immediately.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 5 | [How Effects Shape Sound](./part-1-guitar-pedals/05-how-effects-shape-sound.md) | Beginner | Signal path, gain staging, clipping types, time-based effects, modulation, filtering, buffered vs true bypass, pedal chain impedance |
| 6 | [Gain Stages and Clipping Circuits](./part-1-guitar-pedals/06-gain-stages-clipping-circuits.md) | Intermediate | Transistor gain stages, op-amp gain stages, diode clipping, tone stacks (Fender/Marshall/Baxandall), SPICE simulation |
| 7 | [From Schematic to Breadboard](./part-1-guitar-pedals/07-schematic-to-breadboard.md) | Beg→Inter | Layout translation, signal tracing with audio probe, debugging (no sound, noise, oscillation), voltage checks |
| 8 | [Build: Fuzz and Boost](./part-1-guitar-pedals/08-build-fuzz-and-boost.md) | Beginner | Silicon Fuzz Face clone, clean boost, transistor biasing experiments, component substitution, measuring your build |
| 9 | [Build: Overdrive and Distortion](./part-1-guitar-pedals/09-build-overdrive-distortion.md) | Intermediate | Tube Screamer clone, RAT clone, understanding circuit-level differences, modding for different voices |
| 10 | [Build: Modulation and Time-Based Effects](./part-1-guitar-pedals/10-build-modulation-time-effects.md) | Inter→Adv | Tremolo, PT2399 delay, LFO circuits, bucket brigade devices, designing your own effect |

> **Tower exit state:** You've built 5-6 working pedals, understand how each circuit works at the component level, and can mod or combine circuits to create original effects.

---

## [Part 2 — Speakers](./part-2-speakers/)
*Where electronics meets acoustics meets woodworking. From understanding a driver's datasheet to building a complete speaker system.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 11 | [How Speakers Work](./part-2-speakers/11-how-speakers-work.md) | Beginner | Electromagnetic drivers, driver types, enclosure as acoustic system, radiation patterns, frequency response, phase, the room |
| 12 | [The Language of Speakers](./part-2-speakers/12-language-of-speakers.md) | Intermediate | SPL, sensitivity, impedance curves, Thiele-Small parameters (Fs, Qts, Vas, Xmax, BL), frequency response graphs, power handling, datasheets |
| 13 | [Crossover Design](./part-2-speakers/13-crossover-design.md) | Intermediate | Passive crossovers (1st-4th order), Butterworth/Linkwitz-Riley/Bessel, impedance compensation, L-pads, baffle step, Zobel, active crossovers, VituixCAD |
| 14 | [Cabinet Design](./part-2-speakers/14-cabinet-design.md) | Intermediate | Sealed, ported, transmission line, horn-loaded, band-pass, baffle diffraction, bracing, damping, materials, volume calculations |
| 15 | [Design Tools and Simulation](./part-2-speakers/15-design-tools-simulation.md) | Inter→Adv | WinISD, VituixCAD, REW basics, XSim, simulation vs reality, iterative design methodology |
| 16 | [Build: Bookshelf Speakers](./part-2-speakers/16-build-bookshelf-speakers.md) | Intermediate | Full 2-way build: driver selection, crossover design, cabinet construction, finishing, measurement with REW |
| 17 | [Build: Floor-Standing Towers, PA Speakers, and Subwoofer](./part-2-speakers/17-build-towers-pa-subwoofer.md) | Inter→Adv | Floor-standing tower, horn-loaded PA speaker, sealed/ported subwoofer — three builds for three use cases |

> **Tower exit state:** You can design a speaker system for any use case using simulation tools, build it, measure it, and iterate.

---

## [Part 3 — Amplifiers](./part-3-amplifiers/)
*From small signal to big sound. Solid-state and tube amplification, power supplies, and critical safety knowledge.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 18 | [How Amplifiers Work](./part-3-amplifiers/18-how-amplifiers-work.md) | Intermediate | Gain, impedance, distortion, negative feedback, damping factor, slew rate, amp classes (A, AB, B, D, G, H) |
| 19 | [Solid-State Amplifier Design](./part-3-amplifiers/19-solid-state-amplifier-design.md) | Inter→Adv | BJT/MOSFET output stages, Class AB biasing, protection circuits, thermal management, LM3886, TPA3255 |
| 20 | [Tube Amplifier Theory](./part-3-amplifiers/20-tube-amplifier-theory.md) | Advanced | Triodes, pentodes, load lines, operating points, single-ended vs push-pull, output transformers, bias, the "tube sound" |
| 21 | [Power Supply Design](./part-3-amplifiers/21-power-supply-design.md) | Inter→Adv | Linear supplies, regulation, SMPS, dual-rail, tube B+ supply, grounding topologies |
| 22 | [Safety: Working with Mains and High Voltage](./part-3-amplifiers/22-safety-mains-high-voltage.md) | Intermediate | Mains wiring, fusing, isolation, tube amp voltages (250-600V DC), capacitor discharge, dim bulb tester, the one-hand rule |
| 23 | [Build: Chip Amp and Class D Amp](./part-3-amplifiers/23-build-chip-amp-class-d.md) | Intermediate | LM3886 gainclone (hi-fi), TPA3255 Class D (party), power supply builds, listening comparison, measurements |
| 24 | [Build: Tube Guitar Amplifier](./part-3-amplifiers/24-build-tube-guitar-amp.md) | Advanced | 5F1 Champ style build, point-to-point wiring, power-up procedure, safety checklist, bias verification |

> **Tower exit state:** You've built three amplifiers (chip amp, Class D, tube), understand power supply design, and can work safely with mains and high voltage.

---

## [Part 4 — Digital Audio & The Pi Streamer](./part-4-digital-audio/)
*The bridge between the analog world you've been building and the digital world you already know.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 25 | [Digital Audio Fundamentals](./part-4-digital-audio/25-digital-audio-fundamentals.md) | Intermediate | ADC/DAC, Nyquist, bit depth, delta-sigma vs R2R, jitter, I2S/S/PDIF/USB, audio formats |
| 26 | [Streaming Architectures and Protocols](./part-4-digital-audio/26-streaming-architectures-protocols.md) | Intermediate | DLNA, AirPlay, Spotify Connect, Roon, music servers (Volumio, moOde), multi-room concepts |
| 27 | [DSP Fundamentals for Audio](./part-4-digital-audio/27-dsp-fundamentals.md) | Inter→Adv | FIR vs IIR filters, parametric EQ, compression/limiting, convolution, CamillaDSP |
| 28 | [Build: Pi Streamer with Physical Controls](./part-4-digital-audio/28-build-pi-streamer.md) | Intermediate | Pi + DAC HAT, Volumio/moOde, rotary encoder, buttons, OLED display, case, Spotify/AirPlay |
| 29 | [Build: Active Crossover and Room Correction](./part-4-digital-audio/29-build-active-crossover-room-correction.md) | Advanced | CamillaDSP crossover, UMIK-1 measurement, REW analysis, room correction filters, multi-amp output |
| 30 | [Build: The Full Control Hub](./part-4-digital-audio/30-build-full-control-hub.md) | Advanced | Multi-room (Snapcast), touchscreen, source switching, physical master controls, system architecture |

> **Tower exit state:** A streaming system from simple DAC to full DSP hub with room correction and multi-room — with physical controls that never fail.

---

## [Part 5 — PCB Design & Professional Builds](./part-5-pcb-professional/)
*From prototype to product. The tools and techniques that make your builds look and perform like commercial gear.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 31 | [PCB Design for Audio](./part-5-pcb-professional/31-pcb-design-for-audio.md) | Inter→Adv | KiCad full workflow, audio PCB layout (grounding, routing, separation), Gerber generation, ordering PCBs |
| 32 | [Enclosure Design and Fabrication](./part-5-pcb-professional/32-enclosure-design-fabrication.md) | Intermediate | Pedal enclosures, speaker cabinets, amp chassis, 3D printing, laser cutting, CNC |
| 33 | [Professional Finishing and Aesthetics](./part-5-pcb-professional/33-professional-finishing-aesthetics.md) | Intermediate | Powder coating, wood finishing, screen printing, front panel design, the details that make it professional |
| 34 | [Testing, Quality Control, and Documentation](./part-5-pcb-professional/34-testing-qc-documentation.md) | Intermediate | Test procedures, burn-in, specification verification, BOMs, assembly guides, KiCad + git |

> **Tower exit state:** You can design professional PCBs, build polished enclosures, and document your work for reproduction or sale.

---

## [Part 6 — System Design & Integration](./part-6-system-design/)
*Where everything comes together. Complete sound systems for home and party.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 35 | [Matching Amplifiers to Speakers](./part-6-system-design/35-matching-amps-to-speakers.md) | Intermediate | Impedance matching, power requirements, sensitivity, damping factor, bi-amping, active vs passive |
| 36 | [Room Acoustics and Treatment](./part-6-system-design/36-room-acoustics-treatment.md) | Inter→Adv | Room modes, speaker placement, DIY acoustic treatment, measurement-based treatment |
| 37 | [System Tuning and Measurement](./part-6-system-design/37-system-tuning-measurement.md) | Inter→Adv | REW deep dive, frequency/impedance/distortion measurement, target curves, EQ to taste |
| 38 | [Complete System Build: Home Hi-Fi](./part-6-system-design/38-complete-build-home-hifi.md) | Advanced | Pi → DAC → preamp → power amp → speakers, grounding, cable design, room optimization |
| 39 | [Complete System Build: Party PA](./part-6-system-design/39-complete-build-party-pa.md) | Advanced | SPL over fidelity, PA speakers + subs + Class D amps, DSP protection, the complete party rig |

> **Tower exit state:** You can design, build, tune, and deploy a complete sound system — from listening room to outdoor party.

---

## [Part 7 — From Hobby to Hustle](./part-7-business/)
*Turning your craft into a side business.*

| Ch | Title | Difficulty | What You'll Learn |
|----|-------|-----------|-------------------|
| 40 | [Sourcing and Supply Chain](./part-7-business/40-sourcing-supply-chain.md) | Beginner | Mouser, Digikey, Parts Express, bulk pricing, substitution, building a supplier list |
| 41 | [Costing and Pricing](./part-7-business/41-costing-pricing.md) | Intermediate | BOM calculation, labor costing, pricing strategies, real market prices for custom gear |
| 42 | [Building a Brand and Selling](./part-7-business/42-building-brand-selling.md) | Intermediate | Photography, social media, marketplaces, custom orders, warranty, legal basics |
| 43 | [Scaling Up](./part-7-business/43-scaling-up.md) | Inter→Adv | Batch production, jigs, outsourcing, inventory, craft vs throughput |

> **Tower exit state:** You understand sourcing, costing, selling, and scaling — and can decide whether and how to turn this into revenue.

---

## [Appendices](./appendices/)

| Appendix | Title | What's Inside |
|----------|-------|--------------|
| A | [Glossary](./appendices/appendix-glossary.md) | 200+ terms from impedance to Thiele-Small to slew rate, cross-referenced to chapters |
| B | [Component Suppliers](./appendices/appendix-suppliers.md) | Organized by category with notes on strengths, pricing, and shipping |
| C | [Recommended Reading, Channels, and Communities](./appendices/appendix-resources.md) | Books, YouTube, forums, courses — annotated, not just links |

---

## Cross-Cutting Concepts

These concepts thread through multiple domain towers, building richer understanding each time:

```
IMPEDANCE     Ch 0 → Ch 5 → Ch 12 → Ch 13 → Ch 18 → Ch 20 → Ch 35
              concept → pedal chain → speaker curves → crossover comp → amp output → transformers → matching

FREQ RESPONSE Ch 0 → Ch 5 → Ch 6 → Ch 11 → Ch 13 → Ch 15 → Ch 18 → Ch 27 → Ch 37
              concept → effects → tone stacks → drivers → crossovers → simulation → amp bandwidth → DSP EQ → measurement

SIGNAL PATH   Ch 5 → Ch 11 → Ch 18 → Ch 25 → Ch 35 → Ch 38/39
              guitar chain → electrical→acoustic → gain stages → digital pipeline → complete chain → system builds

POWER         Ch 0 → Ch 8 → Ch 17 → Ch 18 → Ch 21 → Ch 23 → Ch 35
              P=VI → 9V pedals → speaker limits → amp ratings → supply design → builds → system budget

SAFETY        Ch 4 → Ch 21 → Ch 22 → Ch 24 → Ch 42
              basics → mains voltage → tube amp HV → safe build procedure → selling legally

SIM→BUILD→    Ch 3 → Ch 7 → Ch 15 → Ch 19 → Ch 29 → Ch 31 → Ch 37
MEASURE       SPICE → breadboard → WinISD/VituixCAD → datasheet→build → CamillaDSP → KiCad → REW

PRO QUALITY   Ch 7 → Ch 16 → Ch 32 → Ch 33 → Ch 34 → Ch 42
              clean builds → cabinet finishing → fabrication → finishing → QC → brand/photos
```

---

## Build Projects

| Ch | Project | Cost | Domain |
|----|---------|------|--------|
| 8 | Fuzz + Boost pedals | $30-50 | Pedals |
| 9 | Overdrive + Distortion pedals | $40-70 | Pedals |
| 10 | Tremolo + Delay pedals | $40-80 | Pedals |
| 16 | 2-way Bookshelf Speakers (pair) | $150-250 | Speakers |
| 17 | Floor-standing Towers / PA + Sub | $300-800 | Speakers |
| 23 | LM3886 Chip Amp + TPA3255 Class D | $80-200 | Amps |
| 24 | Tube Guitar Amplifier | $200-400 | Amps |
| 28 | Pi Streamer with Physical Controls | $80-150 | Digital |
| 29 | Active Crossover + Room Correction | $50-100 | Digital |
| 30 | Full Control Hub | $100-200 | Digital |

**Total: ~$1,100-2,300 across all projects**
