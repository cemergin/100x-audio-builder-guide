<!--
  CHAPTER: 31
  TITLE: PCB Design for Audio
  PART: 5 — PCB Design & Professional Builds
  PREREQS: Parts 0-4
  KEY_TOPICS: KiCad workflow, schematic capture, symbol libraries, footprint assignment, PCB layout, design rule check, Gerber generation, ordering, ground planes, star grounding, signal routing, input/output separation, component placement, trace width, thermal relief, via stitching, SMD vs through-hole, Tube Screamer PCB walkthrough
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 31: PCB Design for Audio

> **Part 5 — PCB Design & Professional Builds** | Prerequisites: Parts 0-4 | Difficulty: Inter→Adv

You have built pedals on perfboard. You have wired amps point-to-point. You have soldered crossover components to terminal strips inside speaker cabinets. And every one of those builds works -- but if you are being honest with yourself, they look like prototypes. The wiring is messy, the layouts are inconsistent, and if someone asked you to build ten more identical units, you would dread every minute of it. That is because hand-wired construction is artisan work: it is great for one-offs and learning, but it does not scale, it does not reproduce, and it invites mistakes.

PCB design changes everything. A printed circuit board is the difference between "I built this on my kitchen table" and "this is a product." The layout is fixed, the traces are exact, the ground plane is continuous, and every unit you order from the factory is identical. If you are a software engineer, think of it this way: hand-wiring is writing code in a text editor with no version control. PCB design in KiCad with git is a proper IDE, CI/CD pipeline, and deployment system -- for hardware.

This chapter takes you through the complete KiCad workflow from schematic capture to ordering manufactured boards. You already touched KiCad's schematic editor back in [Ch 3](../part-0-electronics/03-simulation-analysis-tools.md) -- now we go much deeper, into the PCB layout editor where the real magic happens. We will use the Tube Screamer clone from [Ch 9](../part-1-guitar-pedals/09-build-overdrive-distortion.md) as our running example, transforming it from a breadboard prototype into a manufactured PCB. By the end, you will have Gerber files ready to upload to a manufacturer, and you will understand exactly why every trace, via, and copper pour is where it is.

### In This Chapter
- KiCad project structure and workflow overview
- Schematic capture: beyond the basics
- Symbol libraries: built-in, third-party, and custom audio symbols
- Footprint assignment: matching symbols to physical packages
- PCB layout: the core new skill
- Audio-specific layout: ground planes, star grounding, signal routing, separation
- Component placement strategy for audio circuits
- Trace width calculations for current-carrying paths
- Thermal relief and via stitching
- Design rule check (DRC): catching errors before manufacturing
- Gerber file generation and manufacturer requirements
- SMD vs through-hole for audio: when to use each
- Complete walkthrough: Tube Screamer clone PCB

### Related Chapters
- [Ch 3: Simulation and Analysis Tools](../part-0-electronics/03-simulation-analysis-tools.md) — KiCad schematic capture introduction
- [Ch 9: Build: Overdrive and Distortion](../part-1-guitar-pedals/09-build-overdrive-distortion.md) — the Tube Screamer circuit we will lay out
- [Ch 21: Power Supply Design](../part-3-amplifiers/21-power-supply-design.md) — grounding topologies that inform PCB layout
- [Ch 32: Enclosure Design and Fabrication](32-enclosure-design-fabrication.md) — the PCB has to fit inside something
- [Ch 34: Testing, Quality Control, and Documentation](34-testing-qc-documentation.md) — version control for hardware projects
- [Ch 42: Building a Brand and Selling](../part-7-business/42-building-brand-selling.md) — professional PCBs enable scalable production
- [Ch 43: Scaling Up](../part-7-business/43-scaling-up.md) — PCBs are the foundation for batch builds

---

## 1. The KiCad Workflow: From Idea to Manufactured Board

### 1.1 Why KiCad

There are several PCB design tools out there -- Altium (expensive, industry standard), Eagle (acquired by Autodesk, now part of Fusion 360), EasyEDA (web-based, tightly coupled to JLCPCB), and KiCad. We are using KiCad for three reasons:

1. **It is free and open source.** No subscription, no feature-gated tiers, no "you can only have 2 layers on the free plan" nonsense. The full software is the full software.
2. **It is excellent.** KiCad 8+ is a genuinely professional tool. Major companies use it in production. The 3D viewer, interactive router, and DRC engine rival tools costing thousands per seat.
3. **It works with git.** KiCad's file formats are text-based, which means you can track schematic and PCB changes in version control. This is enormous. Hardware version control is a game changer when you are iterating on designs or collaborating.

If you already have Altium or Eagle experience, the concepts in this chapter transfer directly. The KiCad-specific instructions will be slightly different, but the audio layout principles are universal.

### 1.2 The KiCad Project Structure

A KiCad project is a directory containing several files:

```
tube-screamer-pcb/
├── tube-screamer.kicad_pro        # Project file (settings, library paths)
├── tube-screamer.kicad_sch        # Schematic file
├── tube-screamer.kicad_pcb        # PCB layout file
├── tube-screamer.kicad_dru        # Design rules
├── fp-lib-table                   # Footprint library table (project-level)
├── sym-lib-table                  # Symbol library table (project-level)
├── gerbers/                       # Output directory for manufacturing files
│   ├── tube-screamer-F_Cu.gbr     # Front copper layer
│   ├── tube-screamer-B_Cu.gbr     # Back copper layer
│   ├── tube-screamer-F_Mask.gbr   # Front solder mask
│   ├── tube-screamer-B_Mask.gbr   # Back solder mask
│   ├── tube-screamer-F_Silkscreen.gbr
│   ├── tube-screamer-Edge_Cuts.gbr
│   └── tube-screamer.drl          # Drill file
├── bom/                           # Bill of materials exports
│   └── tube-screamer-bom.csv
└── .gitignore                     # Ignore backups and caches
```

Think of this like a project with separate source files for the frontend (schematic) and backend (PCB layout), a build step (Gerber generation), and deployment artifacts (the Gerber zip you upload to the manufacturer).

### 1.3 The Complete Workflow

Here is the workflow from start to finish. Each step gets its own section in this chapter:

```
1. Schematic Capture
   Draw the circuit. Add symbols. Wire them together. Annotate.
       ↓
2. Electrical Rules Check (ERC)
   KiCad checks for unconnected pins, conflicting outputs, power errors.
       ↓
3. Footprint Assignment
   Every schematic symbol gets mapped to a physical footprint (package size/shape).
       ↓
4. Update PCB from Schematic
   KiCad transfers the netlist (connection information) to the PCB editor.
       ↓
5. PCB Layout
   Place components. Route traces. Pour ground planes. This is the art.
       ↓
6. Design Rule Check (DRC)
   KiCad checks for clearance violations, unconnected nets, overlapping pads.
       ↓
7. Gerber Generation
   Export the manufacturing files.
       ↓
8. Order
   Upload to JLCPCB / PCBWay / OSH Park. Wait for delivery.
```

If you are a software person, step 5 is the equivalent of writing the implementation after designing the interface. Steps 1-4 are your spec and type system. Steps 6-7 are your linter and build. Step 8 is deployment. Let's go through each one.

---

## 2. Schematic Capture: Beyond the Basics

### 2.1 What You Already Know

Back in [Ch 3](../part-0-electronics/03-simulation-analysis-tools.md), you learned to use KiCad's schematic editor to draw circuits. You placed symbols, wired them together, and used it alongside simulation tools. That was schematic capture for understanding circuits. Now we are doing schematic capture for *manufacturing* -- which means we need to be more rigorous.

For manufacturing, your schematic must be:
- **Complete**: every component that will physically exist on the board is in the schematic. No "I'll just solder a wire" mental notes.
- **Annotated**: every component has a unique reference designator (R1, R2, C1, C2, U1, etc.) and a value.
- **Power-explicit**: power connections must be shown clearly. VCC, GND, V+, V- -- all labeled with power symbols.
- **Connector-explicit**: every off-board connection (input jack, output jack, power jack, pots, switches, LEDs) gets a connector symbol in the schematic.

### 2.2 Schematic Organization for Audio Circuits

Organize your schematic so it reads like the signal flow. For a guitar pedal:

```
Left side                    Center                     Right side
─────────                    ──────                     ──────────
Input Jack  →  Input Buffer  →  Gain Stage  →  Tone  →  Output Buffer  →  Output Jack
                                                                               ↑
Power Jack  →  Reverse Polarity Protection  →  Voltage Regulation  →  Power Rail
                                                                               ↑
                                     Bypass Switching (3PDT / relay)  ─────────┘
```

Put the signal path across the top of the schematic, flowing left to right. Put the power supply section at the bottom. Put the bypass switching in its own area. This is not just cosmetic -- it directly maps to how you will lay out the PCB, and it makes the schematic readable by anyone who picks it up.

Use **hierarchical sheets** if the circuit is complex. For a simple pedal, one sheet is fine. For an amplifier with a preamp, power amp, power supply, and protection circuit, use one sheet per section. KiCad handles hierarchical sheets well -- think of them as separate source files that get linked at build time.

### 2.3 Adding Power Symbols and Connectors

The most common mistake in going from "schematic for understanding" to "schematic for manufacturing" is forgetting the connectors. Your schematic needs explicit symbols for:

- **Input and output jacks**: Use a `Conn_01x02` or a dedicated audio jack symbol (like `AudioJack3` for a TRS jack).
- **Potentiometers**: Use the `R_Potentiometer` symbol -- it has three pins (CW, wiper, CCW).
- **Switches**: Use `SW_DPDT` for a toggle, `SW_3PDT` for a true-bypass footswitch, etc.
- **LEDs**: Include the series resistor on the schematic.
- **Power jack**: A `Barrel_Jack` or `Conn_01x02` for the DC power input.

Every wire that leaves the PCB must go through a connector symbol. No exceptions.

### 2.4 Net Labels and Global Labels

For readability, you do not need to draw wires across the entire schematic. Use **net labels** to connect things logically. A wire labeled `AUDIO_IN` on the left side of the schematic is electrically connected to any other wire labeled `AUDIO_IN` on the same sheet.

For connections between hierarchical sheets, use **global labels**. These work across all sheets in the project.

Convention for audio projects:

| Label | Meaning |
|-------|---------|
| `AUDIO_IN` | Signal input from input jack |
| `AUDIO_OUT` | Signal output to output jack |
| `FX_IN` | Signal entering the effect circuit (after bypass switch) |
| `FX_OUT` | Signal leaving the effect circuit (before bypass switch) |
| `VCC` or `V+` | Positive supply rail |
| `GND` | Ground |
| `VBIAS` or `VREF` | Half-supply bias voltage (common in single-supply op-amp circuits) |

---

## 3. Symbol Libraries

### 3.1 Built-In Libraries

KiCad ships with extensive symbol libraries covering most standard components. For audio work, you will use symbols from:

- **Device**: Resistors (`R`), capacitors (`C`, `C_Polarized`), inductors (`L`), diodes (`D`), LEDs (`LED`), potentiometers (`R_Potentiometer`)
- **Transistor_BJT**: `2N3904`, `2N3906`, `2SC1815`, etc.
- **Transistor_FET**: `2N7000`, `BS170`, `J201` (the legendary JFET for audio)
- **Amplifier_Operational**: `TL072`, `LM358`, `NE5532`, `OPA2134` -- the audio op-amps
- **Diode**: `1N4148` (signal), `1N4001` (power), `1N5817` (Schottky)
- **Connector**: `Conn_01x02`, `AudioJack3`, `Barrel_Jack`

### 3.2 Third-Party and Custom Symbols

Some audio-specific parts are not in KiCad's default libraries. You have options:

**SnapEDA and Ultra Librarian**: These services provide free KiCad symbols, footprints, and 3D models for millions of parts. Search for your part number, download the KiCad library files, and import them into your project. This is the fastest path for weird ICs.

**Creating custom symbols**: For parts that do not exist anywhere, you can create your own. The KiCad Symbol Editor is straightforward:
1. Create a new symbol library for your project (e.g., `my-audio-parts`).
2. Create a new symbol. Draw the outline, add pins with correct names and electrical types.
3. Pin electrical types matter for ERC: `Input`, `Output`, `Bidirectional`, `Passive`, `Power Input`, `Power Output`.

Audio parts you might need custom symbols for:
- **PT2399** (echo/delay IC from [Ch 10](../part-1-guitar-pedals/10-build-modulation-time-effects.md))
- **JRC4558** (the classic Tube Screamer op-amp -- may not be in default lib under that name)
- **CD4049UBE** (unbuffered hex inverter, used in some distortion circuits)
- **LM13700** (operational transconductance amplifier for modulation effects)
- **TDA7293** / **TDA7294** (audio power amplifier ICs)

### 3.3 Symbol Best Practices for Audio

When creating or choosing symbols:
- **Use the actual part number**, not a generic symbol, when the specific part matters. A generic "OpAmp" symbol works for simulation but for manufacturing you want `TL072` or `NE5532` so the BOM is unambiguous.
- **Include pin numbers**. The symbol must map to the physical IC pin numbers. A TL072 with pin 1 = Output A, pin 2 = Inverting A, pin 3 = Non-Inverting A, etc.
- **Add the datasheet URL** in the symbol properties. Future-you will thank present-you.

---

## 4. Footprint Assignment

### 4.1 What Footprints Are

A symbol is the logical representation of a component. A footprint is the physical representation -- the copper pads, silkscreen outline, and courtyard that define where and how the component sits on the board. The same symbol (say, a 10kohm resistor) can have many possible footprints:

| Footprint | Package | Size | Use Case |
|-----------|---------|------|----------|
| `R_Axial_DIN0207_L6.3mm_D2.5mm_P10.16mm_Horizontal` | Through-hole axial | 10.16mm pitch | Easy hand soldering |
| `R_0805_2012Metric` | SMD 0805 | 2.0 x 1.25 mm | Good balance of size and hand-solderability |
| `R_0603_1608Metric` | SMD 0603 | 1.6 x 0.8 mm | Smaller, still hand-solderable with practice |
| `R_0402_1005Metric` | SMD 0402 | 1.0 x 0.5 mm | Tiny. Needs a microscope or machine assembly |

### 4.2 Choosing Footprints for Audio Projects

For your first audio PCBs, I strongly recommend a **hybrid approach**:

- **Through-hole for**: Electrolytic capacitors, potentiometers, switches, jacks, connectors, any component you might want to swap or experiment with, large power resistors, tube sockets.
- **SMD for**: Resistors (0805 is beginner-friendly, 0603 with practice), small ceramic capacitors, ICs that only come in SMD packages, voltage regulators, small signal transistors.

Why hybrid? Through-hole components are easier to solder by hand, easier to debug (you can clip a probe right onto the lead), and easier to replace. But they are larger and limit how compact your board can be. SMD components let you shrink the board and are required for parts that simply do not come in through-hole anymore.

For the Tube Screamer PCB, we will use:
- Through-hole for: electrolytic caps, the audio jacks, the power jack, the 3PDT footswitch wiring, the LED
- 0805 SMD for: resistors and small ceramic caps
- DIP-8 through-hole for: the JRC4558 op-amp (because swapping op-amps is part of the Tube Screamer mystique)

### 4.3 The Assignment Process in KiCad

In KiCad, you assign footprints in the Schematic Editor:

1. Open your schematic.
2. Go to **Tools → Assign Footprints** (or click the footprint assignment icon).
3. The Footprint Assignment Tool opens with three panes: component list, footprint libraries, and available footprints.
4. For each component, select the appropriate footprint. Double-click to assign.
5. Use the footprint viewer (right-click → View Footprint) to see the actual pad layout before committing.

**Pro tip**: Set up footprint filters. In the middle pane, you can filter by library (e.g., `Resistor_SMD`) and by pin count. This narrows the list dramatically.

### 4.4 Footprint Gotchas

**Pad size vs hole size**: For through-hole components, the pad diameter should be at least 1.5x the hole diameter. KiCad's default footprints handle this, but if you create custom footprints, check this.

**Pin 1 orientation**: IC footprints have a pin 1 indicator (dot, notch, or bar). Make sure the symbol's pin numbering matches the footprint's pin numbering. This is where most first-timer errors happen. If pin 1 of your op-amp symbol is "Output A" but pin 1 of your footprint corresponds to a different physical pin, your board will not work.

**Courtyard**: Every footprint has a courtyard -- an invisible boundary that defines the keep-out area around the component. KiCad's DRC checks for courtyard overlaps. Respect them, especially for taller components that might collide in 3D space.

> **What happens if...** you assign the wrong footprint to a component? Best case: the pads do not line up with the component's leads, and you have to bodge-wire it (ugly but functional). Worst case: you get the IC orientation backward, apply power in reverse, and release the magic smoke. Always double-check footprints before ordering boards. Use KiCad's 3D viewer (View → 3D Viewer) to visually inspect component placement -- it catches footprint errors that are invisible in 2D.

---

## 5. PCB Layout: The Core New Skill

### 5.1 Transferring the Schematic to the PCB Editor

Once your schematic is complete, annotated, ERC-clean, and footprints assigned:

1. In the Schematic Editor, run **Tools → Update PCB from Schematic** (or press F8).
2. KiCad generates a netlist and opens the PCB Editor with all your components piled in a heap outside the board outline.
3. You will see a "rat's nest" -- thin lines connecting pads that should be electrically connected. Your job is to arrange the components and replace every rat's nest line with a copper trace.

This is the moment where PCB design goes from "following a procedure" to "practicing a craft." Layout is part engineering, part spatial puzzle, part art.

### 5.2 Drawing the Board Outline

First, define the board shape. Switch to the **Edge.Cuts** layer and draw the outline:

- For a pedal PCB that fits in a Hammond 1590B: the interior is roughly 56mm x 100mm, but you need to account for standoffs and the curvature of the walls. A board of about **48mm x 94mm** with rounded corners fits well.
- For a pedal PCB in a 125B: about **56mm x 115mm**.
- For other projects, measure the enclosure interior and subtract clearance for mounting hardware.

Use the grid. Set it to 1mm or 0.5mm for board outline work. Rounded corners (arc tool) are both aesthetically pleasing and structurally stronger.

### 5.3 Setting Up Design Rules

Before placing components, configure your design rules under **File → Board Setup → Design Rules**:

| Rule | Recommended Minimum | Notes |
|------|---------------------|-------|
| Track width (signal) | 0.25mm (10 mil) | Fine for audio signal traces |
| Track width (power) | 0.5mm - 1.0mm | Depends on current; see section 8 |
| Clearance | 0.2mm (8 mil) | Space between copper features |
| Via hole | 0.3mm | Standard for most manufacturers |
| Via outer diameter | 0.6mm | Annular ring = (0.6 - 0.3) / 2 = 0.15mm |
| Minimum annular ring | 0.13mm | Check your manufacturer's capabilities |

These values work for all major budget PCB manufacturers (JLCPCB, PCBWay, OSH Park). When in doubt, check the manufacturer's capability page -- they all publish their minimums.

### 5.4 Layer Stack

For most audio projects, a **2-layer board** (front copper + back copper) is all you need. The typical layer stack:

```
Top Silkscreen (F.SilkS)      ← Component labels, logos, text
Top Solder Mask (F.Mask)       ← Lacquer that covers copper, with openings at pads
Top Copper (F.Cu)              ← Signal traces, component pads
FR4 Substrate                  ← The fiberglass board material (1.6mm standard)
Bottom Copper (B.Cu)           ← Ground plane, some routing
Bottom Solder Mask (B.Mask)    ← Same as top mask but on bottom
Bottom Silkscreen (B.SilkS)   ← Assembly notes
```

For audio circuits, the go-to strategy is: **signal traces on the top layer, ground plane on the bottom layer.** This gives you a continuous, low-impedance ground reference directly beneath every signal trace, which reduces noise and improves shielding. We will dig into this in the next section.

4-layer boards (with internal power and ground planes) are available but overkill for most audio projects and more expensive. Save them for complex digital-analog hybrid designs or dense SMD layouts.

---

## 6. Audio-Specific PCB Layout

This is where audio PCB design diverges from general electronics PCB design. Audio signals are small (millivolts to a few volts), sensitive to noise, and the listener can hear problems that would be totally irrelevant in a digital circuit. A few microvolts of noise on a data bus? Nobody cares. A few microvolts of noise on an audio bus? That is audible hiss.

### 6.1 Ground Planes

**Rule number one of audio PCB layout: pour a ground plane on the bottom layer.**

A ground plane is a continuous copper fill connected to GND that covers most or all of the bottom layer. It provides:

- **Low-impedance ground return**: Current always flows in a loop. The signal goes out on the top-layer trace and returns through the ground plane directly beneath it. A continuous plane means the return path is always the shortest possible path directly under the signal trace.
- **Shielding**: The ground plane acts as a shield between your circuit and external interference (and between the top and bottom of the board).
- **Heat dissipation**: Copper conducts heat well. A ground plane helps spread heat from power components.
- **Reduced crosstalk**: When two signal traces run parallel on the top layer, the ground plane between them (on the bottom layer) reduces capacitive coupling.

In KiCad, creating a ground plane is simple:
1. Switch to the B.Cu (back copper) layer.
2. Select the zone tool (or press the hotkey).
3. Draw a rectangle covering the entire board.
4. Assign it to the `GND` net.
5. Set zone properties: clearance 0.3mm, minimum width 0.2mm, pad connection "Thermal Relief" (more on this in section 9).
6. Press B to fill zones.

The ground plane should be as unbroken as possible. Every trace you route on the bottom layer cuts a gap in the ground plane. Minimize bottom-layer routing to keep the plane intact.

### 6.2 Star Grounding on a PCB

In [Ch 21](../part-3-amplifiers/21-power-supply-design.md), we discussed star grounding -- the principle of connecting all ground returns to a single point so that current from one section does not flow through another section's ground path. On a breadboard or point-to-point build, star grounding is done with physical wires. On a PCB, it is done by controlling where and how sections connect to the ground plane.

The PCB star ground strategy:

```
                    ┌──── Input stage ground connections
                    │
    GND star ───────┼──── Gain stage ground connections
    point           │
                    ├──── Output stage ground connections
                    │
                    └──── Power supply ground connections
```

In practice on a 2-layer board with a ground plane:
1. **Keep the ground plane continuous** under the signal path sections.
2. **Do not route signal traces on the bottom layer** through the signal path area -- this would cut the ground plane and force ground return current to detour around the gap.
3. **Place the power supply section** in its own area of the board, connected to the main ground plane through a single bottleneck point. You can achieve this by making the power section's ground plane connection narrow (a single trace width connecting two zone areas).
4. **Digital and analog grounds**: If your design has both (like the PT2399 delay, which has digital and analog sections), keep separate ground plane areas that connect at one point near the power entry.

This is not about routing GND traces -- the ground plane handles all GND connections. It is about *not breaking* the ground plane and controlling how different sections share it.

### 6.3 Signal Routing

Audio signal routing has a few firm rules:

**Keep input traces away from output traces.** In a gain circuit like the Tube Screamer, the output signal is 100-1000x larger than the input signal. If input and output traces run parallel, the output can capacitively couple back into the input, creating a feedback path that causes oscillation or changes the tone in unpredictable ways.

Practical approach: Route the input traces on one side of the board and the output traces on the other. In the Tube Screamer layout, the input jack is on the right, the output jack is on the left (or vice versa), and the signal flows in one direction across the board.

**Keep analog away from digital.** If your design has digital components (microcontrollers, LED drivers, switching regulators), keep them physically separated from the sensitive analog audio path. Digital signals have fast edges that radiate electromagnetic interference (EMI). The PT2399 delay chip from [Ch 10](../part-1-guitar-pedals/10-build-modulation-time-effects.md) is a good example -- its internal clock generates noise that can couple into the audio path if layout is careless.

**Keep signal away from power.** Power traces carry higher currents and can couple noise into signal traces. Route power traces away from sensitive signal paths. Use the ground plane between them.

**Minimize trace length for sensitive nodes.** The inverting input of an op-amp is a high-impedance node -- it picks up noise like an antenna. Keep traces to high-impedance nodes as short as possible. Place the feedback resistor physically close to the op-amp.

**Avoid running traces under sensitive components.** A trace running under a high-gain amplifier stage can couple unwanted signals into that stage through the PCB substrate.

### 6.4 Input/Output Separation

This deserves its own subsection because it is the single most impactful layout decision for audio.

**Physically separate input and output connectors.** In a pedal layout, the input jack should be at one end of the board and the output jack at the other. This maximizes the physical distance between the weakest signal (input) and the strongest signal (output).

For the Tube Screamer layout:

```
┌─────────────────────────────────────────┐
│  INPUT    →  Buffer  →  Clipping  →     │
│  JACK        Stage      Stage      │    │
│                                    ↓    │
│             POWER      Tone  ←────┘    │
│             SECTION    Stage            │
│                         ↓               │
│  OUTPUT   ←  Output  ←─┘               │
│  JACK        Buffer                     │
└─────────────────────────────────────────┘
```

Input on the top-left. Output on the bottom-left. Signal flows in a U-shape or L-shape. Power supply is off to the side, away from both the input and the output. The input and output jacks have maximum physical separation.

> **What happens if...** you put the input and output jacks right next to each other? You might get oscillation (the dreaded squeal) in high-gain circuits. Even if it does not oscillate, you will likely have higher noise than necessary because the output signal couples back into the input through proximity. In a Tube Screamer (which has moderate gain), you might get away with it. In a high-gain distortion pedal? You will have problems.

---

## 7. Component Placement Strategy

### 7.1 Signal Flow Left-to-Right

Place components so that the signal flows left-to-right (or in a clear directional pattern) across the board. This is not just for readability -- it naturally creates physical separation between early stages (small signals) and late stages (large signals).

For the Tube Screamer:
1. **Input section** (left): Input jack connector, input buffer components (R1, C1, Q1 or U1A input section)
2. **Gain/clipping section** (center-left): Op-amp gain stage components, clipping diodes
3. **Tone section** (center-right): Tone control components, pot connections
4. **Output section** (right): Output buffer, output jack connector
5. **Power supply** (bottom or edge): Power jack, reverse polarity diode, voltage regulator, bypass caps

### 7.2 Power Supply in Its Own Area

The power supply section generates the most noise (switching, rectification, regulation) and handles the most current. Keep it physically separated from the signal path:

- Place it along one edge of the board (bottom or right side).
- Place bulk decoupling capacitors (100uF+) near the power input.
- Place local decoupling capacitors (100nF ceramic) directly next to each IC's power pins -- as close as physically possible. These are the most important capacitors on the board. If a 100nF cap is more than 5mm from the IC power pin, move it closer.

### 7.3 Sensitive Components Away from Noise Sources

Some components are more sensitive to electromagnetic interference than others:

- **High-impedance nodes**: Op-amp inverting inputs, JFET gates. Keep traces short, components close.
- **Low-level signal paths**: The input buffer area. Keep it away from power components and switching elements.
- **Precision components**: Voltage references, bias networks. Keep them away from heat sources (which cause drift).

Some components are noise *sources*:
- **Switching regulators**: If you use one, keep it far from the audio path and use a separate ground plane area.
- **Relay bypass switching**: Relay coils generate voltage spikes. Include a flyback diode and keep the relay away from the input stage.
- **LEDs**: LED switching generates minor transients. Include a series resistor and decouple the LED's power connection.
- **3PDT footswitches**: These carry signal through their contacts. Route the switch connections carefully.

### 7.4 The 3D Check

After placing components, use KiCad's **3D Viewer** (View → 3D Viewer or Alt+3) to visually inspect the board. This catches:

- Components that overlap in 3D (tall caps under a low-profile enclosure lid)
- Components that will not physically fit (a tall electrolytic cap too close to the enclosure wall)
- Wrong footprint sizes (the footprint says "10mm diameter" but the actual cap is 12.5mm)
- Components that will interfere with mounting hardware (standoffs, screws)

This is your visual integration test. Do it before routing.

---

## 8. Trace Width for Current

### 8.1 Why Width Matters

A PCB trace is just a flat wire. Like wire, it has resistance that depends on its cross-sectional area. A thin trace carrying too much current will heat up, potentially to the point of delaminating from the board (trace burn). A wide trace has lower resistance and handles more current.

For audio, the currents involved are generally small -- milliamps for signal paths, maybe a few hundred milliamps for power. But speaker output traces and power supply traces can carry amps.

### 8.2 The Trace Width Calculator

Do not memorize trace width tables. Use a calculator. KiCad has one built in: **Calculators → PCB Calculator → Track Width**. You can also use the Saturn PCB Toolkit or any online trace width calculator.

Input parameters:
- **Current**: How much current will flow through this trace?
- **Copper thickness**: Standard is 1 oz/ft2 = 35 micrometers. Budget PCBs default to this.
- **Temperature rise**: How much hotter than ambient you will accept (10C is conservative).
- **Trace length**: Does not affect width calculation, but affects total resistance.

Results for 1 oz copper, 10C rise:

| Current | Minimum Trace Width | Audio Application |
|---------|--------------------|--------------------|
| 100 mA | 0.15 mm | Signal traces (even thinner is fine for milliamp signals) |
| 250 mA | 0.25 mm | IC power traces, LED supply |
| 500 mA | 0.5 mm | Power supply input, regulated output |
| 1 A | 0.8 mm | Higher-current power delivery |
| 2 A | 1.5 mm | Speaker output traces (from amp to connector) |
| 5 A | 3.5 mm | High-power speaker lines, power supply mains |

### 8.3 Trace Width by Function in Audio Projects

For practical audio PCB layout, use these conventions:

- **Signal traces** (audio path): 0.25mm - 0.3mm. More than enough for the milliamps of signal current. You could go thinner, but this is easy to visually inspect and route.
- **Power traces**: 0.5mm minimum, 1.0mm preferred. Even if the IC only draws 10mA, wider power traces reduce voltage drops and noise.
- **Ground traces** (if routing manually instead of using a plane): 0.5mm minimum, but you should be using a ground plane instead.
- **Speaker output traces**: 1.5mm - 3.0mm or wider. If your amplifier PCB delivers 2A to the speaker, use wide traces. Better yet, use a copper pour (fill zone) for the speaker output path.
- **LED traces**: 0.3mm is fine. LEDs draw 10-20mA.

> **What happens if...** you use the default 0.25mm trace width for everything, including the power traces? For a 9V pedal drawing 30mA, honestly nothing bad happens -- the trace can handle it. But for an amplifier board delivering 2A to a speaker through a thin trace, the trace becomes a resistor (and a fuse). You get voltage drop across the trace, heat, and in extreme cases, the trace burns open.

---

## 9. Thermal Relief and Via Stitching

### 9.1 Thermal Relief: Why You Need It

When a through-hole pad is connected directly to a large copper ground plane, the plane acts as a massive heat sink. This sounds good for thermal management, but it is terrible for soldering. The ground plane wicks heat away from the pad so fast that your soldering iron cannot get the pad hot enough to melt solder. You end up with cold joints -- the number one defect in hand-soldered PCBs.

**Thermal relief** solves this by connecting the pad to the ground plane through thin spokes instead of a solid connection:

```
Without thermal relief:          With thermal relief:
┌──────────────┐                 ┌──────────────┐
│ █████████████│                 │  ███   ███   │
│ █████████████│                 │ █  ██████  █ │
│ ████ PAD ████│                 │ █  █ PAD █  █│
│ █████████████│                 │ █  ██████  █ │
│ █████████████│                 │  ███   ███   │
└──────────────┘                 └──────────────┘
Pad merges into plane            Pad connects through 4 spokes
Heat escapes everywhere          Heat is contained during soldering
Impossible to hand solder        Solders easily
```

In KiCad, thermal relief is set in the zone properties:
- **Pad connection**: "Thermal Relief" (this is the default, keep it)
- **Thermal relief gap**: 0.5mm (space between pad and plane)
- **Thermal relief spoke width**: 0.5mm (width of the connecting spokes)

Use thermal relief for all pads connected to the ground plane. The only exception is if you need a very low-impedance connection for a high-current path -- in that case, use "Solid" connection and accept that you will need more heat (a bigger iron tip or a preheater).

### 9.2 Via Stitching

Via stitching means placing additional vias throughout the ground plane to connect the top-layer ground copper to the bottom-layer ground plane. This provides multiple parallel paths between the top and bottom ground, reducing impedance and improving shielding.

For audio PCBs, via stitching is most important:

- **Around the board perimeter**: A ring of ground vias around the edge creates a shielding fence.
- **Near IC ground pads**: Additional vias near IC ground connections reduce the ground impedance at the IC.
- **Between sections**: Vias between the input and output sections reinforce the ground plane continuity.

Spacing: one via every 5-10mm around the perimeter is a good starting point. You do not need to go overboard -- this is not a multi-gigahertz RF board. But a dozen extra ground vias cost nothing and can measurably reduce noise floor.

In KiCad, you can manually place vias on the GND net, or use the via stitching plugin (available in KiCad 8+).

---

## 10. Design Rule Check (DRC)

### 10.1 What DRC Catches

DRC is your linter. Run it before generating Gerbers. It catches:

- **Clearance violations**: Traces or pads too close to each other. This can cause short circuits during manufacturing.
- **Unrouted connections**: Rat's nest lines that you forgot to route. The board will have missing connections.
- **Minimum width violations**: Traces narrower than your design rules or the manufacturer's capabilities.
- **Minimum drill violations**: Holes too small for the manufacturer to drill.
- **Courtyard overlaps**: Components that physically overlap.
- **Pad-to-pad clearance**: Pads from different nets too close together.
- **Silkscreen on pads**: Text or graphics covering exposed copper.

### 10.2 Running DRC in KiCad

In the PCB Editor: **Inspect → Design Rules Checker** (or the DRC button in the toolbar).

- Click **Run DRC**.
- Review every violation. Fix them. Do not ignore violations unless you have a very specific reason.
- Run DRC again until you get zero violations.

**Zero violations. Not "a few that are probably fine."** DRC errors are like compiler warnings that you turn into errors -- they exist for a reason, and the one you ignore is the one that bites you.

### 10.3 Common DRC Fixes for Audio PCBs

| DRC Error | Likely Cause | Fix |
|-----------|-------------|-----|
| Clearance violation | Traces too close to ground plane or pads | Increase ground plane clearance or reroute |
| Unrouted net | Forgot to connect something | Route the connection (check rat's nest) |
| Courtyard overlap | Two components placed too close | Move one component |
| Silkscreen on pad | Reference designator overlaps a pad | Move the text |
| Minimum width | A trace is narrower than spec | Widen the trace |
| Thermal relief error | Pad too close to zone boundary | Adjust zone or pad position |

---

## 11. Gerber File Generation

### 11.1 What Gerbers Are

Gerber files are the manufacturing standard for PCBs. They are like PDFs for circuit boards -- every PCB manufacturer on earth can read them. Each Gerber file describes one layer of the board as a set of geometric shapes (traces, pads, fills).

You also need drill files (Excellon format) to tell the manufacturer where to drill holes and what diameter.

### 11.2 Generating Gerbers in KiCad

In the PCB Editor: **File → Fabrication Outputs → Gerbers (.gbr)**

Select these layers:

| Layer | Gerber Layer | Description |
|-------|-------------|-------------|
| F.Cu | Front Copper | Top traces and pads |
| B.Cu | Back Copper | Bottom traces and ground plane |
| F.Mask | Front Solder Mask | Top mask openings |
| B.Mask | Back Solder Mask | Bottom mask openings |
| F.SilkS | Front Silkscreen | Top text and graphics |
| B.SilkS | Back Silkscreen | Bottom text (optional) |
| Edge.Cuts | Board Outline | The board shape |
| F.Paste | Front Paste | Only needed if using SMD stencil assembly |

Click **Plot** to generate the Gerber files.

Then generate drill files: **File → Fabrication Outputs → Drill Files (.drl)**
- Format: Excellon
- Oval holes: Route command
- Map file: none needed
- Click **Generate Drill File**

### 11.3 Verifying Gerbers

Before uploading to a manufacturer, verify your Gerbers. Use the **KiCad Gerber Viewer** or an online viewer like the one on the JLCPCB website:

1. Open each Gerber file in the viewer.
2. Check that all layers align correctly.
3. Verify the board outline looks right.
4. Verify all drill holes are present.
5. Look for any anomalies -- missing traces, odd artifacts, text that did not render.

This is your final visual inspection before committing money to manufacturing. Treat it like reviewing a diff before merging to main.

### 11.4 Manufacturer-Specific Requirements

Each manufacturer has slightly different expectations:

**JLCPCB** (cheapest, fast, good quality):
- Upload a zip file containing all Gerbers + drill files.
- Their website auto-detects layers. Verify the auto-detection is correct.
- Standard options: 2-layer, 1.6mm FR4, 1oz copper, HASL (Hot Air Solder Leveling) surface finish, green solder mask.
- Cost: ~$2-5 for 5 boards (yes, five boards for the price of a coffee). Shipping adds $5-15.
- Turnaround: 2-5 days production + shipping.

**PCBWay** (more options, slightly higher quality):
- Similar upload process.
- More surface finish options (ENIG gold for better soldering and aesthetics).
- Slightly more expensive than JLCPCB.
- Good customer service and support.

**OSH Park** (US-made, distinctive purple boards):
- Upload directly -- they accept KiCad .kicad_pcb files in addition to Gerbers.
- Famous purple solder mask with gold (ENIG) surface finish.
- More expensive ($5/in2 for 2-layer boards).
- Slower turnaround.
- Great for when you want your boards to look gorgeous.

For your first order, start with JLCPCB. Order 5 boards (the minimum). The cost is negligible, and having spare boards means you can experiment or recover from a soldering mistake.

---

## 12. SMD vs Through-Hole for Audio

### 12.1 The Through-Hole Case

Through-hole components have leads that pass through holes in the PCB and are soldered on the opposite side. Arguments in favor:

- **Easy hand soldering**: You can solder through-hole with a basic iron and no magnification. Every skill you built in Parts 1-4 applies directly.
- **Easy debugging**: You can clip a scope probe or multimeter lead directly onto a component lead.
- **Easy replacement**: Desoldering a through-hole component is straightforward with a solder sucker or wick.
- **Component availability**: Many audio-specific components (film caps, electrolytic caps, potentiometers, jacks, tube sockets) are only available in through-hole.
- **Tradition and tone**: Some audio designers (especially in guitar pedal and tube amp communities) insist that through-hole construction sounds better. The technical argument is weak, but the cultural argument is strong if you are selling to those communities.

### 12.2 The SMD Case

Surface-mount components sit on pads on one side of the board and are soldered directly to the surface. Arguments in favor:

- **Size**: An 0805 resistor is tiny compared to an axial through-hole resistor. This lets you make more compact boards.
- **Manufacturing**: SMD components can be machine-placed and reflow-soldered. If you want to have boards assembled by a manufacturer (JLCPCB offers SMT assembly starting at remarkably low cost), SMD is the way.
- **Performance**: Shorter leads = lower parasitic inductance. This matters at radio frequencies but is generally irrelevant at audio frequencies.
- **Availability**: Many modern ICs are SMD-only. Some op-amps, voltage regulators, and specialty audio ICs do not come in DIP packages.
- **Cost**: SMD components are generally cheaper than their through-hole equivalents in bulk.

### 12.3 The Hybrid Approach (Recommended)

Use both. There is no rule that says a board has to be all one technology. The hybrid approach gives you the best of both worlds:

| Component Type | Recommended Package | Why |
|---------------|--------------------|----|
| Resistors | 0805 SMD | Small, cheap, easy enough to hand solder |
| Small ceramic caps | 0805 SMD | Same as resistors |
| Film caps | Through-hole (box, radial) | Usually not available in SMD at audio values |
| Electrolytic caps | Through-hole (radial) | SMD electrolytic caps exist but have worse specs |
| Op-amps (DIP available) | DIP-8 through-hole | Socketed for easy swapping and debugging |
| Op-amps (SMD only) | SOIC-8 SMD | Still hand-solderable with a fine tip |
| Voltage regulators | Through-hole (TO-220) or SOT-223 SMD | TO-220 if heat sinking needed, SOT-223 otherwise |
| Transistors | Through-hole (TO-92) or SOT-23 SMD | TO-92 if you want to experiment, SOT-23 if final |
| Potentiometers | Through-hole | Always through-hole -- they are mechanical components |
| Jacks, switches | Through-hole | Always through-hole -- mechanical components |
| LEDs | Through-hole (5mm) | Easier to position in enclosure |
| Power diodes | Through-hole (DO-41) | Easier to handle and heat sink |

This approach keeps the board compact where it matters (passives) and hand-serviceable where it matters (ICs, mechanical parts).

---

## 13. Complete Walkthrough: Tube Screamer Clone PCB

Let's put it all together. We are going to design a PCB for the Tube Screamer (TS-808) clone from [Ch 9](../part-1-guitar-pedals/09-build-overdrive-distortion.md). This is a real design exercise -- follow along in KiCad.

### 13.1 Schematic Recap

The Tube Screamer circuit (simplified for PCB purposes):

```
Signal Path:
  Input Jack → R_input (510k to GND) → C_input (0.047uF coupling) → 
    Op-Amp A (JRC4558, non-inverting gain stage with clipping diodes in feedback) →
      Tone Control (passive network with pot) →
        Op-Amp B (JRC4558, output buffer) → C_output (coupling) → Output Jack

Power:
  9V DC Jack → Reverse polarity diode (1N5817) → 
    C_bulk (47uF) → C_bypass (100nF) → VCC rail
  Voltage divider → VBIAS (4.5V) → C_bias (47uF) → VBIAS rail

Bypass:
  3PDT footswitch wired for true bypass
  LED indicator with series resistor
```

### 13.2 Schematic in KiCad

1. **Create new project**: `tube-screamer-pcb`
2. **Add all components** with correct values and reference designators.
3. **Add connector symbols**: `J1` (Input AudioJack), `J2` (Output AudioJack), `J3` (DC Barrel Jack), `SW1` (3PDT footswitch -- use a 3x `Conn_01x02` or a custom 9-pin symbol).
4. **Wire the circuit** following signal flow left-to-right.
5. **Add power flags** on VCC and GND.
6. **Add decoupling caps**: 100nF directly on U1 pins 4 and 8 to GND.
7. **Run ERC**. Fix any errors.

### 13.3 Footprint Assignment

| Ref | Component | Footprint |
|-----|-----------|-----------|
| R1-R15 | Resistors | `R_0805_2012Metric` (SMD) |
| C1-C6 | Ceramic/film caps | `C_0805_2012Metric` (SMD for ceramic) or `C_Disc_D5.0mm` (through-hole for film) |
| C7-C9 | Electrolytic caps | `CP_Radial_D5.0mm_P2.50mm` (through-hole) |
| U1 | JRC4558 | `DIP-8_W7.62mm_Socket` (DIP with socket) |
| D1-D2 | Clipping diodes (1N914) | `D_DO-35_SOD27_P7.62mm_Horizontal` (through-hole) |
| D3 | Reverse polarity (1N5817) | `D_DO-41_SOD81_P10.16mm_Horizontal` (through-hole) |
| LED1 | Status LED | `LED_D5.0mm` (through-hole) |
| J1, J2 | Audio jacks | Depends on your chosen jack -- use a custom footprint if needed |
| J3 | DC power jack | `BarrelJack_Horizontal` or custom |

### 13.4 PCB Layout Step-by-Step

**Step 1: Board outline.** Draw a 48mm x 94mm rectangle with 1mm corner radius on Edge.Cuts. This fits a Hammond 1590B.

**Step 2: Place connectors first.** Position J1 (input) at the top edge, J2 (output) at the bottom edge, J3 (power) on the side. This establishes the physical constraints.

**Step 3: Place the op-amp.** U1 goes in the center of the board. It is the heart of the circuit.

**Step 4: Place input stage components** near J1. R_input, C_input, and the surrounding components cluster near the op-amp's input pins.

**Step 5: Place clipping/gain components** near U1's first op-amp section. The clipping diodes and feedback resistor/cap go as close to U1 pins 1-3 as possible.

**Step 6: Place tone components** between the two op-amp sections. The tone pot connector goes nearby.

**Step 7: Place output stage components** near U1's second op-amp section and J2.

**Step 8: Place power supply** along the bottom or side edge. D3, C_bulk, C_bypass in a cluster near J3.

**Step 9: Place decoupling caps** immediately adjacent to U1 pins 4 (GND) and 8 (VCC). I cannot stress this enough -- within 3mm of the pin.

**Step 10: Route signal traces** on the top layer. Follow the signal flow. Use 0.3mm trace width for signal paths. Keep input-related traces on one side of U1 and output-related traces on the other.

**Step 11: Route power traces** on the top layer. Use 0.5mm width for VCC and VBIAS. Route from the power section to U1 pin 8, then to any other powered components.

**Step 12: Pour ground plane** on the bottom layer. Assign to GND net. Fill zones. Inspect for any isolated copper islands (delete them -- they serve no purpose and may cause manufacturing issues).

**Step 13: Add via stitching.** Place GND vias around the perimeter and near U1's ground connections.

**Step 14: Add silkscreen labels.** Reference designators (R1, C1, etc.), board name, version, your name/logo, pin 1 indicators, polarity marks for electrolytic caps and diodes.

**Step 15: 3D viewer check.** Verify everything fits.

**Step 16: Run DRC.** Fix all violations. Run again. Zero errors.

**Step 17: Generate Gerbers and drill files.** Verify in the Gerber viewer.

**Step 18: Order.** Upload to JLCPCB. Green solder mask, white silkscreen, HASL finish, 1.6mm, 1oz copper. Order 5 boards. Total cost: under $10 including shipping.

### 13.5 Wait, Then Build

Your boards arrive in a week or two. When they do:

1. **Visual inspect** each board. Look for manufacturing defects (missing copper, scratches, misaligned silkscreen).
2. **Continuity check**: Use a multimeter to verify there is no short between VCC and GND.
3. **Solder the lowest-profile components first**: SMD resistors and caps, then IC socket, then through-hole components, then connectors.
4. **Insert the IC last**, after powering up the board and verifying correct supply voltages at the socket.
5. **Test** following the procedures we will cover in [Ch 34](34-testing-qc-documentation.md).

---

## 14. Iteration and Version Control

### 14.1 Your First Board Will Have Issues

Accept this now: your first PCB revision will have something wrong. A footprint that is slightly off. A trace that should have been wider. A component that would work better in a different position. This is normal and expected. Professional hardware designers budget for 2-3 board revisions before production.

Label your boards with version numbers: `TS-Clone v1.0` on the silkscreen. When you revise, increment: `v1.1` for minor fixes, `v2.0` for major layout changes.

### 14.2 KiCad + Git

Your KiCad project belongs in a git repository. KiCad's file formats are text-based (S-expression format), which means git can track meaningful diffs.

```bash
# Initialize the repo
git init tube-screamer-pcb
cd tube-screamer-pcb

# Create .gitignore
cat > .gitignore << 'EOF'
# KiCad backup files
*.kicad_sch-bak
*.kicad_pcb-bak
*-backups/
fp-info-cache
# Gerbers (regenerate from source)
gerbers/
# 3D model cache
*.3d_cache/
EOF

# Initial commit
git add .
git commit -m "Initial Tube Screamer PCB layout v1.0"

# After making changes
git add .
git commit -m "v1.1: Moved decoupling caps closer to U1, widened power traces"

# Tag releases (versions you actually ordered)
git tag -a v1.0 -m "First board order - sent to JLCPCB 2026-04-10"
```

This is hardware version control. When you order a batch of boards for [Ch 43: Scaling Up](../part-7-business/43-scaling-up.md), you will tag the exact commit that corresponds to that production run. If a customer reports an issue, you can check out that tag and see exactly what they have.

We will expand on this in [Ch 34](34-testing-qc-documentation.md) when we discuss full build documentation.

### 14.3 Schematic Review Checklist

Before moving from schematic to layout, run through this checklist:

- [ ] Every component has a reference designator and value
- [ ] Every off-board connection has a connector symbol
- [ ] Power connections are explicit (VCC, GND symbols)
- [ ] Decoupling caps on every IC power pin (100nF minimum)
- [ ] ERC passes with zero errors
- [ ] Footprints assigned to all components
- [ ] Datasheet URLs in component properties
- [ ] Schematic reads left-to-right following signal flow

### 14.4 Layout Review Checklist

Before generating Gerbers:

- [ ] Board outline matches enclosure dimensions (with clearance)
- [ ] Input and output connectors are physically separated
- [ ] Signal flow follows a clear directional pattern
- [ ] Power supply section is in its own area
- [ ] Decoupling caps are within 3mm of IC power pins
- [ ] Ground plane is continuous (no unnecessary cuts)
- [ ] Trace widths appropriate for current (signal: 0.25mm+, power: 0.5mm+)
- [ ] Thermal relief on ground-connected pads
- [ ] Via stitching around perimeter and near ICs
- [ ] Silkscreen labels are readable and not on pads
- [ ] 3D viewer shows no physical conflicts
- [ ] DRC passes with zero violations
- [ ] Gerbers verified in viewer

---

## 15. Beyond the Pedal: PCB Design for Other Audio Projects

### 15.1 Speaker Crossover PCBs

The crossovers you built in [Ch 16](../part-2-speakers/16-build-bookshelf-speakers.md) on terminal strips can be designed as PCBs too. Crossover PCBs need:

- **Wide traces**: Crossover components carry the full amplifier output current. Use 2mm+ traces or copper pours.
- **Large pads**: Crossover inductors and capacitors are physically large. Custom footprints may be needed.
- **Mounting holes**: The PCB mounts inside the speaker cabinet, so include mounting holes at the corners.
- **Generous spacing**: Crossover components are large and need room. Board space is not a constraint inside a speaker cabinet.

### 15.2 Amplifier PCBs

The LM3886 chip amp from [Ch 23](../part-3-amplifiers/23-build-chip-amp-class-d.md) is a great candidate for a PCB:

- **Heat sink considerations**: The LM3886 needs a heat sink. The PCB footprint must align with heat sink mounting.
- **Ground plane is critical**: Amplifiers are high-gain circuits. Ground plane continuity is paramount.
- **Wide power and output traces**: The LM3886 can deliver several amps to the speaker.
- **Kelvin connections**: For the output stage, separate the high-current paths from the sensing paths.

### 15.3 Pi Streamer Interface Boards

The Pi streamer from [Ch 28](../part-4-digital-audio/28-build-pi-streamer.md) used off-the-shelf HATs. But you can design a custom PCB that:

- **Combines multiple functions**: DAC, rotary encoder interface, button inputs, OLED connector -- all on one board.
- **Matches the Pi header**: Use a 2x20 2.54mm pin header footprint to mate with the Raspberry Pi GPIO.
- **Separates digital and analog grounds**: The DAC section needs analog ground isolation from the Pi's digital ground. Connect them at a single point near the DAC.

### 15.4 Tube Amp Boards

For the tube amplifier from [Ch 24](../part-3-amplifiers/24-build-tube-guitar-amp.md), PCBs are less common (point-to-point is traditional), but they exist:

- **High voltage clearances**: Tube amp B+ can be 400V+. Standard 0.2mm clearance is dangerously inadequate. Use 3mm+ clearance between high-voltage traces and other nets. Check IPC-2221 for voltage-based clearance tables.
- **Large pads**: Tube sockets, terminal strips, turret boards all need large, robust pads.
- **Thick copper**: Consider 2oz copper for power supply paths.

> **What happens if...** you design a tube amp PCB with standard clearances? At 400V, an arc can jump across a 0.2mm gap, especially if there is any contamination (flux residue, dust, moisture). At best, the board fails. At worst, someone gets shocked. High-voltage PCB design has different rules -- respect them or use point-to-point construction.

---

## 16. Summary

PCB design is the bridge between prototype and product. The workflow -- schematic capture, footprint assignment, layout, DRC, Gerber generation, ordering -- is a learnable process with concrete rules. The audio-specific knowledge -- ground planes, star grounding, signal separation, trace width management -- is what turns a functional PCB into a quiet, professional-sounding one.

Your first board order will feel momentous. You designed something on your computer, uploaded a file, and a factory on the other side of the world manufactured your exact design in copper and fiberglass. When you hold that board in your hand and solder the Tube Screamer components onto it, and it works -- that is the moment you transition from "builder" to "designer."

Next up: [Chapter 32](32-enclosure-design-fabrication.md) covers what you put that PCB *inside* -- enclosures for pedals, speakers, amps, and everything in between.
