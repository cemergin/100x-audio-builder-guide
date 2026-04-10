<!--
  CHAPTER: 24
  TITLE: Build: Tube Guitar Amplifier
  PART: 3 — Amplifiers
  PREREQS: Chapter 23
  KEY_TOPICS: Fender 5F1 Champ inspired build, chassis prep, turret board, point-to-point wiring, power supply build and test, bias verification, first power-up, safety verification, common mods
  DIFFICULTY: Advanced
  UPDATED: 2026-04-10
-->

# Chapter 24: Build: Tube Guitar Amplifier

> **Part 3 — Amplifiers** | Prerequisites: Chapter 23 | Difficulty: Advanced

---

> **SAFETY WARNING**
>
> This chapter involves building a circuit that produces **300-400V DC** — voltages that are **immediately lethal on contact.** These voltages are present on exposed components inside the chassis during operation and can persist in filter capacitors **long after the amplifier is unplugged.**
>
> Before proceeding:
> - You MUST have read and understood Chapter 22 (Safety) in its entirety.
> - You MUST have a dim bulb tester built and tested.
> - You MUST have a capacitor discharge tool built and tested.
> - You MUST have a GFCI-protected outlet on your workbench.
> - You SHOULD have another person present (or at least aware) during high-voltage testing.
>
> If you are not fully comfortable with high-voltage safety procedures, build the solid-state amps from Chapter 23 first and gain more experience before attempting this project. There is no shame in waiting until you are ready. **The amp will still be here when you are.**

---

We're building a guitar amplifier. Not just any tube amp — a circuit inspired by the **Fender 5F1 Champ**, one of the most important guitar amplifiers ever made. The original Champ was introduced in 1955 and has been used on more recordings than any amp its size should reasonably claim. Neil Young's "Harvest," Joe Walsh's clean tones, Eric Clapton's "Layla" sessions — the little Champ punches absurdly far above its weight.

Why the 5F1? Because it's the perfect learning platform. One 12AX7 dual-triode preamp tube. One 6V6GT beam-power output tube. One 5Y3GT tube rectifier (or silicon diodes if you prefer). About 5 watts of output — enough to record with, enough to gig in small venues with a microphone on it, and enough to learn the fundamentals of tube amplifier construction without building a beast that could power a stadium.

The 5F1 uses cathode bias (self-adjusting, forgiving), has no negative feedback loop (simpler to build, rawer tone), and has a straightforward power supply. It is, in many ways, the "Hello World" of tube amp building. And it sounds *glorious*.

### In This Chapter
- What we're building: the 5F1-inspired circuit
- Chassis preparation and layout
- Turret board construction
- Point-to-point wiring technique
- Building and testing the power supply FIRST
- Completing the audio circuit
- Bias verification
- First power-up procedure
- Safety verification checklist
- Testing with a guitar
- Common mods and tweaks

### Related Chapters
- [Ch 20: Tube Amplifier Theory](20-tube-amplifier-theory.md) — the theory behind every circuit in this chapter
- [Ch 21: Power Supply Design](21-power-supply-design.md) — understanding the B+ supply
- [Ch 22: Safety: Working with Mains and High Voltage](22-safety-mains-high-voltage.md) — MANDATORY. Read before building. Read again before testing.
- [Ch 6: Gain Stages and Clipping Circuits](../part-1-guitar-pedals/06-gain-stages-clipping-circuits.md) — simpler gain stage concepts
- [Ch 35: Matching Amplifiers to Speakers](../part-6-system-design/35-matching-amps-to-speakers.md) — speaker cab pairing
- [Ch 39: Complete System Build: Party PA](../part-6-system-design/39-complete-build-party-pa.md) — integrating this amp into a rig

---

## 1. What We're Building

### 1.1 The Circuit Overview

Our 5F1-inspired amp has three functional blocks:

```
Guitar Input → [Preamp (V1, 12AX7)] → [Power Amp (V2, 6V6GT)] → [Output Transformer] → Speaker
                                                                        ↑
                                                          [Power Supply (V3, 5Y3GT)]
                                                          B+ = ~310V, heaters = 6.3V
```

**V1 — 12AX7 Preamp:** Two triode gain stages in series. The first stage provides voltage gain (~35-50x). A volume control between the stages sets the drive level. The second stage provides additional gain (~35-50x) and drives the power tube's grid.

**V2 — 6V6GT Power Tube:** A single beam-power tube operating in Class A, cathode-biased. It takes the preamp signal and delivers about 4-5 watts to the output transformer.

**V3 — 5Y3GT Rectifier:** A tube rectifier that converts the high-voltage AC from the power transformer secondary into DC for the B+ supply. Its "soft" rectification contributes to the amp's dynamic feel.

**Output Transformer:** Transforms the 6V6's high-impedance output to drive an 8-ohm speaker.

### 1.2 Complete Bill of Materials

> **Note:** The Fender 5F1 schematic is well-documented and freely available. Search for "Fender 5F1 Champ schematic" and use it as your reference alongside this chapter. We describe our build based on the standard 5F1 values with a few modern improvements noted.

**Tubes:**
| Qty | Type | Function | Notes |
|-----|------|----------|-------|
| 1 | 12AX7 (ECC83) | Preamp | JJ, Sovtek, or Electro-Harmonix — all good |
| 1 | 6V6GT | Power amp | JJ 6V6S is robust and readily available |
| 1 | 5Y3GT | Rectifier | Or substitute silicon diodes (see note) |

**Transformers:**
| Qty | Part | Suggested Source |
|-----|------|-----------------|
| 1 | Power transformer, 5F1 spec | ClassicTone 40-18010, Hammond 290AX |
| 1 | Output transformer, 5F1 spec (8k:8 ohm) | ClassicTone 40-18011, Hammond 125A |

**Tube sockets:**
| Qty | Type | Notes |
|-----|------|-------|
| 2 | 9-pin noval (for 12AX7 and 6V6) | Belton or ceramic. Chassis-mount. |
| 1 | Octal (for 5Y3) | Belton or ceramic. Chassis-mount. |

**Capacitors:**

> **VOLTAGE RATINGS ARE CRITICAL. Do not substitute lower voltage rated capacitors. These caps see 300V+ DC.**

| Ref | Value | Voltage | Type | Notes |
|-----|-------|---------|------|-------|
| C1 | 25 uF | 25V | Electrolytic | Cathode bypass, V1a |
| C2 | 0.02 uF (20 nF) | 400V | Film (Orange Drop) | Coupling, V1a to V1b |
| C3 | 25 uF | 25V | Electrolytic | Cathode bypass, V1b |
| C4 | 0.02 uF (20 nF) | 400V | Film (Orange Drop) | Coupling, V1b to V2 grid |
| C5 | 25 uF | 50V | Electrolytic | Cathode bypass, V2 |
| C6 | 20 uF | 450V | Electrolytic | First B+ filter |
| C7 | 20 uF | 450V | Electrolytic | Second B+ filter |
| C_input | 0.05 uF (50 nF) | 400V | Film | Input coupling (optional — some 5F1s have this, some don't) |

**Resistors (all 1/2W carbon composition or metal film unless noted):**

| Ref | Value | Power | Notes |
|-----|-------|-------|-------|
| R1 | 68k | 1/2W | Grid leak, V1a |
| R2 | 1.5k | 1/2W | Cathode, V1a |
| R3 | 100k | 1W | Plate load, V1a |
| R4 | 1M | 1/2W | Volume pot to grid leak, V1b |
| R5 | 1.5k | 1/2W | Cathode, V1b |
| R6 | 100k | 1W | Plate load, V1b |
| R7 | 220k | 1/2W | Grid leak / stopper, V2 |
| R8 | 470 | 5W | Cathode, V2 (power rating matters!) |
| R9 | 10k | 5W | B+ dropping resistor (between C6 and C7) |
| R_bleeder1 | 220k | 5W | Bleeder across C6 — SAFETY COMPONENT |
| R_bleeder2 | 220k | 5W | Bleeder across C7 — SAFETY COMPONENT |

**Potentiometer:**
| Qty | Value | Type |
|-----|-------|------|
| 1 | 1M audio taper | 16mm or 24mm pot |

**Miscellaneous:**
- Chassis: 5F1 size aluminum or steel (available from Mojotone, Watts Tube Audio, etc.)
- Turret board or eyelet board: pre-drilled for 5F1 layout
- IEC C14 inlet with fuse holder (or hardwired mains with panel-mount fuse holder)
- Fuse: 1A slow-blow (120V mains) or 0.5A slow-blow (240V mains)
- Jewel light (pilot lamp indicator)
- 1/4" input jack (Switchcraft 11)
- Speaker jack (1/4" Switchcraft 11 or Cliff jack)
- Hookup wire: 22 AWG solid core for signal, 18 AWG stranded for heaters and power
- Grommets, cable ties, hardware

### 1.3 Silicon Diode Rectifier Option

If you want to skip the 5Y3 tube rectifier (saves a tube socket, a tube, and the 5V heater winding), you can substitute two silicon diodes (1N4007 or UF4007) in a full-wave configuration, with a 100-ohm/5W resistor in series to simulate the tube rectifier's sag. The B+ will be slightly higher and stiffer with silicon — the amp will have slightly more headroom and less "sag" character. Both approaches work; the tube rectifier is more traditional.

---

## 2. Chassis Preparation

### 2.1 Layout Planning

Before you drill a single hole, plan the layout on paper or in a CAD program. The 5F1 layout has been optimized over decades:

```
    BACK OF CHASSIS (component side up)
    ┌──────────────────────────────────────────────────┐
    │  [Power        [5Y3       [6V6       [12AX7     │
    │   Trans.]       socket]    socket]    socket]    │
    │                                                   │
    │  [Output                                          │
    │   Trans.]      [Turret board area]                │
    │                                                   │
    └──────────────────────────────────────────────────┘
         ↑                                        ↑
    [IEC inlet,                           [Input jack,
     fuse, switch,                         Volume pot]
     speaker jack]
```

**Key layout principles:**
- Power transformer on the far left, input jack on the far right — maximum separation between the largest noise source and the most sensitive point.
- Output transformer near the power transformer (convenient wiring to power tube plate).
- 12AX7 (preamp) farthest from transformers to minimize hum pickup.
- Turret board in the center, accessible from underneath.

### 2.2 Hole Drilling and Punching

**Tube socket holes:** These are the trickiest. 9-pin noval sockets need a 7/8" (22 mm) hole. Octal sockets need 1-1/8" (29 mm) holes.

**Methods for punching socket holes:**
- **Chassis punch (Greenlee type):** The professional tool. A hardened steel punch and die with a bolt that draws them together. Clean, precise holes every time. Worth buying if you plan to build more than one amp. Available for common socket sizes.
- **Step drill bit (Unibit):** A conical, stepped drill bit that cuts progressively larger holes. Works on aluminum but not ideal for thick steel.
- **Hole saw:** Works but can wander. Clamp the chassis securely and go slowly.
- **Drill and file:** Drill a pilot hole, then enlarge with a round file. Tedious but requires no special tools.

**Transformer mounting holes:** Drill to match the transformer bolt pattern. Use the transformer's included template or measure carefully.

**Back panel holes:** For the IEC inlet, speaker jack, etc. Rectangular cutouts for the IEC inlet can be made by drilling corner holes and filing to shape, or using a nibbler tool.

**All holes should be deburred** with a deburring tool or a larger drill bit twisted by hand. Sharp edges inside a chassis will cut wires over time, eventually causing shorts — possibly to the chassis, possibly catastrophically.

### 2.3 Mounting Components to the Chassis

**Tube sockets:** Mount from the top of the chassis (the component side). The socket body sits on top; the pins project downward into the chassis. Use the socket's mounting ring and screws. Ensure the socket is oriented correctly — the key slot or pin 1 indicator should be positioned so that the wiring runs are shortest and signal pins are away from heater pins. Most 5F1 layouts have the sockets oriented with the key slot facing toward the back of the chassis.

**Transformers:** The power transformer is the heaviest single component. Mount it with bolts through the chassis, using rubber washers to decouple mechanical vibration. The output transformer can be mounted similarly.

**Orientation matters:** The power transformer and output transformer should be mounted at 90 degrees to each other. This minimizes magnetic coupling between them, which reduces hum. If both transformers are oriented with their cores in the same direction, the power transformer's stray magnetic field induces voltage directly into the output transformer, causing hum that's nearly impossible to eliminate.

**IEC inlet, switches, and jacks:** Mount to the chassis panels. Use lock washers to prevent loosening from vibration. Verify that the IEC inlet mounts securely — it carries mains power and must not become loose.

### 2.4 Grounding and Safety

**The chassis is your safety ground.** The earth wire from the mains inlet connects to the chassis via a bolt with a lock washer and star washer (to bite through paint or anodizing and make reliable contact with the metal).

The star washer (also called a tooth washer) is important. If your chassis is painted, anodized, or powder-coated, the coating is an insulator. The star washer's teeth cut through the coating to make metal-to-metal contact with the underlying aluminum or steel. Without it, your earth connection might have high resistance — which defeats its purpose as a safety ground.

Before proceeding, verify: mains earth to chassis — less than 0.5 ohms. This connection is what protects you if a live wire contacts the chassis. If the reading is above 0.5 ohms, scrape the paint away from the contact area and re-mount with a fresh star washer.

> **What happens if... the chassis earth connection fails?**
> If a live wire touches the chassis (from a loose connection, vibration, or component failure), the chassis becomes energized at mains voltage. Without an earth connection, there's no low-impedance path to trip the fuse or breaker. The chassis sits at mains potential, silently waiting for someone to touch it and a grounded surface simultaneously. With a proper earth connection, the massive current flow through the low-resistance earth path trips the fuse in milliseconds, de-energizing the chassis. **Earth ground is not optional.**

---

## 3. Turret Board or Eyelet Board

### 3.1 What They Are

A **turret board** is a flat insulating board (typically fiberglass, phenolic, or fiber) with metal turrets pressed into holes at regular intervals. Components are soldered to the turrets, and wires connect the turrets to form the circuit.

An **eyelet board** is similar but uses hollow metal eyelets instead of solid turrets. Components leads are threaded through the eyelets and soldered.

Both provide a neat, serviceable, and durable construction method that has been used in professional tube amps for 70+ years. The turret board gives the amp a "professional" look that point-to-point spaghetti wiring does not.

### 3.2 Getting a Board

For a 5F1 build, the easiest approach is to buy a **pre-drilled, pre-populated turret board** laid out for the 5F1 circuit. Several vendors offer these:
- Mojotone 5F1 turret board
- Watts Tube Audio 5F1 board
- Various vendors on eBay

Alternatively, buy a blank turret board and press your own turrets based on a layout you design. Many 5F1 turret layouts are available online.

### 3.3 Soldering Technique for Turret Boards

Soldering to turrets is different from soldering on PCBs. The turrets have much more thermal mass, so you need more heat and more time.

**Tools needed:**
- Soldering iron: 40-60W with a chisel tip (not a fine conical tip — you need thermal mass). Temperature-controlled stations (Hakko FX-888D or similar) are ideal, set to 700-750F (370-400C).
- Solder: 60/40 or 63/37 tin-lead rosin-core, 0.032" or 0.040" diameter. Lead-free solder is harder to work with and not recommended for hand-soldering turret boards.
- Flux: Additional liquid or paste flux for stubborn joints.

**Technique:**
1. Pre-tin the turret: Apply the iron and solder to the turret until a thin coating of solder covers the contact surface. This makes subsequent connections easier.
2. Thread the component lead through or around the turret.
3. Apply the iron to both the turret and the lead simultaneously. Feed solder into the joint until it flows smoothly around the connection.
4. Remove the solder, then the iron. The joint should be shiny and concave (a "fillet" shape). A dull, blobby joint is a "cold joint" — it has high resistance and will cause problems.
5. Trim excess lead length after the joint cools.

**Common mistakes:**
- Not enough heat: The solder doesn't flow onto the turret, creating a cold joint that looks like a blob sitting on top rather than flowing into the connection.
- Too much solder: Creates bridges between adjacent turrets or makes it difficult to disassemble for servicing.
- Moving the joint before the solder solidifies: Creates a "disturbed" joint with a crystalline, frosty appearance. These have high resistance. Reheat and reflow.

### 3.4 Component Installation

**Ground bus:** Run a bare tinned copper wire along one edge of the turret board, soldered to the ground turrets. This is your signal ground bus. Use 20 AWG solid tinned copper. The bus should make a clean, low-resistance connection to each ground turret.

**Component mounting:** Lead dress matters in a tube amp. The order of operations:
1. Install the ground bus wire first.
2. Install resistors — bend leads to length, thread through turrets, solder. Keep leads short but not so short that the component body touches the board (leave 3-5mm clearance for ventilation on power resistors).
3. Install capacitors — observe polarity on electrolytics. Film capacitors have no polarity.
4. Install wire jumpers between turrets as needed.
5. After all components are installed, visually inspect every joint. Reheat any that look dull or have poor wetting.

> **VOLTAGE RATINGS: Every capacitor connected to or near the B+ rail must be rated for at least 450V. The coupling capacitors (C2, C4) see the full plate voltage. Using a 200V rated cap where a 400V is needed will result in cap failure — possibly violent, possibly causing a fire. Double-check every capacitor's voltage rating before soldering.**

### 3.5 Component Choices — What Matters and What Doesn't

In the tube amp world, there's an enormous amount of folklore about component choices affecting tone. Here's the engineer's perspective:

**Resistors:** Carbon composition resistors were used in original Fender amps and have a certain mystique. They're noisier than metal film and less precise, but some builders claim they contribute to the "vintage" tone. In reality, the difference is inaudible in most positions. Use whatever you prefer. Metal film is quieter and more consistent. Carbon composition is more "authentic." Both work.

**Coupling capacitors (C2, C4):** These are in the signal path and their choice matters more. Orange Drop (Sprague/Vishay/CDE) polyester film caps are the standard for tube amps. Mallory 150 series are another popular choice. The important thing is the value and voltage rating — the brand is secondary. Avoid ceramic capacitors in coupling positions — they can be microphonic and have nonlinear capacitance with applied voltage.

**Electrolytic caps (filter caps, cathode bypass):** Use quality brands: JJ Electronic (specifically designed for tube amp use), F&T, Sprague Atom, or Nichicon. The voltage rating is critical. The exact capacitance value matters less — 20uF and 22uF and 33uF will all work; the sound difference is in the amount of bass and the degree of power supply sag.

**The output transformer:** This is the component where quality matters most. A good output transformer from Hammond, ClassicTone, Mercury Magnetics, or Heyboer will sound significantly better than a cheap import transformer. The output transformer defines the amp's bandwidth, power handling, and tonal character. Don't skimp here.

---

## 4. Point-to-Point Wiring

### 4.1 Wire Types

- **22 AWG solid-core hookup wire:** For signal connections (grid, plate, coupling). Use PVC or PTFE insulated wire rated for at least 600V.
- **18 AWG stranded wire:** For heater wiring and power supply connections. Stranded wire is more flexible and handles vibration better.
- **Shielded cable:** For the input jack to the first grid. This run picks up every bit of interference it can — shielding it is important.

### 4.2 Wiring Best Practices

**Keep input wiring away from output wiring.** The 12AX7 input grid wire carries microvolt-level signals. The 6V6 plate wire carries signals at hundreds of volts. If they run parallel, the plate signal will couple into the input, potentially causing oscillation or adding noise.

**Twist the heater wires.** The 6.3V heater supply carries AC current that creates a magnetic field. Twisting the two heater wires together (about 2-3 twists per inch) causes the fields from each wire to cancel. This dramatically reduces heater hum coupling into signal circuits.

**Dress wires flat against the chassis** where possible. Wires sticking up into the air act as antennas. Route them along the chassis walls, close to grounded surfaces.

**Grounding strategy — star ground:**
All ground connections terminate at a single star ground point — a bolt on the chassis near the filter capacitors. The ground connections, in order of priority:
1. Filter capacitor grounds
2. Input jack ground
3. Turret board ground bus
4. Heater center-tap or bias point (if using DC heaters or heater elevation)

**Do NOT ground anything to random chassis bolts.** Every ground goes to the star point.

### 4.3 Wire Routing Order

Wire the amp in this order:
1. **Tube socket pin identification:** Before wiring anything, mark which socket pin is which, looking at the bottom of the chassis (the pin numbering is the mirror image of what you see from the top). Use the socket manufacturer's diagram. Getting a pin wrong is the most common wiring mistake in tube amp building.
2. **Heater wiring first.** Run the twisted heater pair from the transformer heater secondary to each tube socket. Solder at each socket's heater pins.
3. **Ground bus and ground connections.**
4. **Power supply wiring** (B+ from rectifier through filters to turret board).
5. **Power tube connections** (plate to output transformer, cathode, grid).
6. **Preamp connections** (plate loads, coupling caps, grid connections).
7. **Input jack and volume pot wiring** (last, to keep the most sensitive wiring away from the work area as long as possible).

---

## 5. Power Supply — Build and Test FIRST

> **THIS IS THE MOST IMPORTANT STEP IN THE BUILD. Build and test the power supply before connecting any audio circuitry.**

### 5.1 Why Test the Supply First?

If there's a wiring error in the power supply, it will destroy every tube and component connected to it the moment you power on. By testing the supply in isolation, you verify correct voltages before anything expensive is at risk.

### 5.2 Power Supply Wiring

**Step 1:** Wire the mains input to the power transformer primary.
- IEC inlet → fuse (1A slow-blow) → power switch → transformer primary
- Connect earth from IEC inlet to chassis ground bolt

**Step 2:** Wire the rectifier.
For the 5Y3GT tube rectifier:
- Transformer HV secondary leads to pins 4 and 6 of the 5Y3 octal socket
- Pin 8 (cathode/plate) of the 5Y3 is the B+ output (rectified DC)
- Pin 2 is connected to pin 8 (both are cathode connections in the 5Y3)
- Transformer 5V rectifier heater winding to pins 2 and 8 of the 5Y3 (the 5Y3 is directly heated — the heater IS the cathode)

**Correction: 5Y3GT pin connections:**
- Pins 4, 6: Plates (connect to HV secondary ends)
- Pins 2, 8: Cathode/heater (connect to 5V winding AND this is the B+ output)
- Verify with the 5Y3GT datasheet. Pin numbering varies — always check.

**Step 3:** Wire the filter capacitors and bleeder resistors.
- B+ from rectifier → C6 (20uF/450V) positive terminal
- C6 negative terminal → chassis ground (star point)
- R_bleeder1 (220k/5W) across C6
- C6 positive → R9 (10k/5W dropping resistor) → C7 (20uF/450V) positive
- C7 negative → chassis ground (star point)
- R_bleeder2 (220k/5W) across C7

**Step 4:** Wire the heater supply.
- 6.3V secondary from transformer to the 12AX7 and 6V6 heater pins
- Twist the heater wires

### 5.3 Power Supply Test Procedure

> **FOLLOW CHAPTER 22 SAFETY PROCEDURES. This test produces lethal voltages.**

**With NO tubes installed and NO audio circuit connected:**

1. **Visual check:** Inspect all wiring against the schematic. Check twice.
2. **Continuity checks (unpowered):**
   - Mains earth to chassis: < 0.5 ohm
   - No short between B+ and ground
   - No short between mains live and chassis
   - Fuse installed and correct value
3. **Connect the dim bulb tester.**
4. **Install ONLY the 5Y3GT rectifier tube** (or if using silicon diodes, they're already wired in).
5. **Power on through dim bulb tester.** Stand back.
   - The dim bulb should glow briefly (filter cap charging) then dim.
   - If the bulb stays bright: **power off immediately.** You have a short or wiring error.
6. **Measure B+ voltage (ONE HAND RULE — clip leads first, then power on):**
   - B+ at C6: should read approximately 350-380V DC (with no load, the voltage will be higher than the loaded value)
   - B+ at C7 (after dropping resistor): should read approximately 300-340V DC
   - **These are LETHAL voltages. Keep your hands out of the chassis.**
7. **Measure heater voltage:**
   - Across the 6.3V heater winding: should read approximately 6.3V AC (±10%)
8. **Measure for AC ripple on B+ (oscilloscope, AC-coupled):**
   - Should be less than a few volts of 120 Hz ripple. Large ripple suggests a filter cap is open or wrong polarity.
9. **Power off.**
10. **Discharge filter caps** using your discharge tool. Measure to verify < 50V before touching anything.

> **If all voltages are correct, your power supply is working. This is a significant milestone. Take a moment to appreciate that you've built a working high-voltage power supply. Now discharge those caps again before proceeding.**

---

## 6. Complete the Audio Circuit

With the power supply verified, you can now wire the rest of the amplifier.

### 6.1 Output Stage (6V6GT)

**Connections:**
- Pin 3 (plate): to output transformer primary (the lead marked "B+" or "plate")
- Pin 4 (screen grid): to B+ at C6 (through a 470-ohm/1W screen resistor in some builds — the stock 5F1 connects the screen directly to B+, but a screen resistor adds protection)
- Pin 5 (grid 1 / control grid): to coupling cap C4, through R7 (220k grid stopper)
- Pin 8 (cathode): to R8 (470 ohm/5W) to ground, bypassed by C5 (25uF/50V)
- Pins 2, 7 (heater): already connected in Step 5

**The output transformer:** The primary connects between B+ and the 6V6 plate. The secondary goes to the speaker jack. Polarity matters if you later add a feedback loop — for now, either orientation works.

### 6.2 Preamp (12AX7)

The 12AX7 contains two triodes. In the 5F1:

**First triode (V1a) — pins 1 (plate), 2 (grid), 3 (cathode):**
- Pin 2 (grid): from input jack through R1 (68k grid leak) to ground, and through the input coupling cap (if used)
- Pin 1 (plate): to R3 (100k plate load) to B+ at C7, and through C2 (0.02 uF coupling cap) to the volume pot
- Pin 3 (cathode): to R2 (1.5k) to ground, bypassed by C1 (25 uF/25V)

**Second triode (V1b) — pins 6 (plate), 7 (grid), 8 (cathode):**
- Pin 7 (grid): from volume pot wiper through R4 (1M grid leak to ground)
- Pin 6 (plate): to R6 (100k plate load) to B+ at C7, and through C4 (0.02 uF coupling cap) to V2 grid
- Pin 8 (cathode): to R5 (1.5k) to ground, bypassed by C3 (25 uF/25V)

**Pins 4, 5 (heater), pin 9 (heater CT):** Already wired for heaters. Pin 9 can be left unconnected or tied to ground (grounding the center tap can reduce heater hum in some cases).

### 6.3 Input Jack and Volume Pot

- **Input jack:** The tip connector goes through shielded wire to the grid circuit (pin 2 of V1a, through the grid leak resistor). The sleeve (ground) goes to the star ground point. Use a Switchcraft 11 — the standard for guitar amps.
- **Volume pot (1M audio taper):** Wiper goes to V1b grid. One outer lug goes to V1a plate coupling cap (C2). The other outer lug goes to ground.

### 6.4 Final Visual Inspection

Before powering on the complete amp, do one more thorough visual check:

- [ ] Every wire goes where the schematic says it goes
- [ ] No solder bridges between adjacent turrets or socket pins
- [ ] All electrolytic capacitors have correct polarity
- [ ] All high-voltage caps are rated 400V+
- [ ] Bleeder resistors are connected across both filter caps
- [ ] Star ground point has all ground wires connected
- [ ] No bare wire touching the chassis unintentionally
- [ ] All solder joints are clean and shiny (no cold joints)
- [ ] Heater wires are twisted
- [ ] Input wiring is separated from output wiring

---

## 7. Bias Verification

Before the first full power-up, we'll verify the operating point of the 6V6 power tube.

### 7.1 What We're Checking

In the 5F1 circuit, the 6V6 is cathode-biased with a 470-ohm cathode resistor. The cathode voltage tells us the plate current:

```
Ik = Vk / Rk
```

The plate dissipation is approximately:

```
Pd ≈ (B+ - Vk) × Ik
```

For a 6V6GT with a maximum plate dissipation of 14W, we want to run at about 70-80% of maximum for longevity:

```
Target Pd ≈ 10-11W
```

### 7.2 Measurement Procedure

> **HIGH VOLTAGE. One-hand rule. Clip leads before power on. Discharge before touching.**

1. **With the amp powered off and discharged,** clip a multimeter lead to the 6V6 cathode (pin 8 — the junction of R8 and C5) and another lead to ground.
2. Install all three tubes.
3. **Connect a speaker** (or a suitable dummy load — 8 ohm/10W power resistor). Never operate a tube amp without a load connected — the output transformer can generate destructive voltage spikes without a load.
4. Power on through the dim bulb tester. No input signal.
5. **Wait 1-2 minutes** for the tubes to warm up and stabilize.
6. Read the cathode voltage. Expected: 15-20V DC.

**Interpreting the reading:**

For a cathode voltage of 18V:
```
Ik = 18V / 470 ohm = 38 mA
Pd = (320V - 18V) × 0.038A = 11.5W
```

This is a reasonable operating point — about 82% of the 6V6's 14W maximum. The tube will run warm but within safe limits.

If the cathode voltage is:
- **Below 10V:** The tube is biased cold. Check all connections. The tube might be defective.
- **Above 25V:** The tube is biased hot. Plate dissipation may exceed the maximum. Check the cathode resistor value (it should be 470 ohms). Try a different 6V6 tube.

### 7.3 Adjusting the Bias

The 5F1's cathode bias is fixed by the resistor value. If you want to adjust it:
- **Higher R8 (e.g., 560 ohms):** Runs the tube cooler, more headroom, "cleaner"
- **Lower R8 (e.g., 390 ohms):** Runs hotter, earlier breakup, "warmer"

For your first build, stick with 470 ohms. It's the stock value and works well with most 6V6 tubes.

---

## 8. First Full Power-Up

### 8.1 The Procedure

1. **Dim bulb tester connected.**
2. All tubes installed.
3. Speaker connected (MANDATORY — never power a tube amp without a load).
4. Volume pot at minimum (fully counterclockwise).
5. No guitar connected.
6. Power on.
7. **Wait 30-60 seconds.** The tubes need to heat up. You should see the heaters glowing inside the tubes (orange glow visible through the glass). The 5Y3 takes the longest to start conducting.
8. **Check the dim bulb.** It should glow dimly (heater and quiescent current). If it's bright, power off and investigate.
9. **Listen.** With your ear near the speaker (amp is at minimum volume, no input), you should hear... essentially nothing. A very faint hum is normal for a tube amp. Loud hum suggests a grounding problem or a microphonic tube.
10. **Slowly turn up the volume** with no input. You should hear a gradual increase in background hiss (preamp tube noise — this is normal). If you hear motorboating (a rhythmic "putt-putt-putt" sound), the amp is oscillating — power off immediately and check for feedback paths (output wiring too close to input wiring, grounding issues).

### 8.2 First Guitar Signal

1. Remove the dim bulb tester. **Connect directly to mains through a fused, GFCI-protected outlet.**
2. **Connect a guitar** (or any instrument with a 1/4" output).
3. Volume at minimum. Play a chord. Slowly increase volume.
4. You should hear your guitar — clean at low volume, gradually breaking up as you increase the volume.
5. At full volume, a 5W tube amp will be **LOUD**. Louder than you expect from 5 watts. Seriously. Start quietly and work up.

### 8.3 What It Should Sound Like

A well-built 5F1 has a characteristic voice:
- **Clean (low volume):** Clear, chimey, with a slight sweetness. The single-ended 6V6 adds a gentle warmth even at clean levels.
- **Edge of breakup (medium volume):** Notes have a bloom and sustain. Chords are full and rich. Your picking dynamics control the amount of grit — play softly for clean, dig in for crunch.
- **Full overdrive (high volume):** Fat, compressed, singing distortion. Even-order harmonics from the single-ended stage give it a "thick" quality. The power supply sags with the tube rectifier, adding a bouncy, dynamic compression. This is the sound that has graced countless recordings.

---

## 9. Safety Verification Checklist — Complete Build

> **Go through this checklist before considering the build complete.**

### Electrical Safety

- [ ] Earth continuity: IEC inlet earth pin to chassis — less than 0.5 ohm
- [ ] Earth continuity: IEC inlet earth pin to every exposed metal surface — less than 1 ohm
- [ ] All chassis screws tight (loose screws can interrupt earth continuity)
- [ ] Fuse installed, correct type and rating
- [ ] No live or B+ wires can contact the chassis
- [ ] All wire insulation intact — no nicks, cuts, or melted spots
- [ ] Mains wiring mechanically secure (strain-relieved, crimped or soldered, not just twisted)
- [ ] Bleeder resistors installed and verified functional (measure voltage decay after power off)

### Thermal Safety

- [ ] Adequate ventilation around tubes (at least 2" clearance above 6V6 and 5Y3)
- [ ] No combustible material near tubes or power resistors
- [ ] Power resistors (R8, R9, bleeders) mounted with clearance from other components and from the chassis if they're inside the enclosure

### Operational Safety

- [ ] Speaker connected before power-on (verified every time)
- [ ] Volume pot functional (smooth rotation, zero signal at minimum)
- [ ] No unusual noises (oscillation, motorboating, excessive hum)
- [ ] Amp powers up and shuts down cleanly (no pops or thumps)

---

## 10. Troubleshooting — When Things Don't Work Right

Even with careful construction, problems happen. Here's a systematic approach to diagnosing tube amp issues.

> **SAFETY REMINDER: All troubleshooting inside a tube amp chassis requires following the safety procedures from Chapter 22. Discharge caps. Measure before touching. One-hand rule when probing live circuits. Every time.**

### 10.1 No Sound at All

**Systematic check:**
1. Is the fuse blown? Check by removing and testing continuity. If blown, do NOT simply replace it — find out why it blew first.
2. Are the tubes glowing? If the heaters aren't lit, check the heater winding voltage and wiring.
3. Is B+ present? Measure at C6. If zero, check the rectifier (5Y3 or silicon diodes) and the HV secondary winding.
4. Is there B+ at the plates? Measure at V1a plate (pin 1) and V2 plate (pin 3). If B+ is present at C6 but not at the plates, check the dropping resistors and plate load resistors.
5. Signal path check: Use a signal tracer (or an oscilloscope) and inject a signal at the input. Follow it through each stage: V1a grid → V1a plate → volume pot → V1b grid → V1b plate → V2 grid → V2 plate → output transformer → speaker. The signal should grow larger at each stage. Where it disappears is where your problem is.

### 10.2 Loud Hum (50/60 Hz or 100/120 Hz)

**50/60 Hz hum (mains frequency):** Usually caused by magnetic coupling from the power transformer to the signal wiring or circuit. Solutions:
- Reorient the power transformer (rotating it 90 degrees can dramatically change the hum field)
- Move signal wiring away from the transformer
- Add a mu-metal shield around the transformer (expensive but effective)
- Check for a broken heater wire (one side of the heater circuit disconnected causes massive hum)

**100/120 Hz hum (double mains frequency):** Usually power supply ripple getting into the audio. Solutions:
- Check filter capacitor values and polarity (a reversed electrolytic will not filter at all)
- Check the rectifier — a failed diode in a full-wave circuit produces half-wave ripple, which is much harder to filter
- Increase filter capacitance
- Check grounding — is the star ground intact? Is any ground wire disconnected?

**Hum that changes with volume control position:** The hum is entering before the volume control (input stage or input wiring). Check the input jack wiring, the shielded cable to the grid, and the grounding of the input jack.

**Hum that doesn't change with volume:** The hum is entering after the volume control or in the power supply. Check B+ filtering and grounding.

### 10.3 Oscillation (Squealing, Motorboating)

**High-frequency squealing:** The amplifier is oscillating at a high frequency. Common causes:
- Output wiring running parallel to input wiring (move them apart)
- Missing or incorrect grid stopper resistors
- Excessive lead length on components
- A "cold" solder joint creating a parasitic oscillator

**Low-frequency motorboating (rhythmic "putt-putt-putt"):** The amplifier is oscillating at a sub-audio or low-audio frequency. Usually caused by inadequate B+ filtering — the amplifier's current draw modulates the B+ voltage, which feeds back into the amplifier. Solutions:
- Increase filter capacitor values (try 47uF instead of 20uF)
- Add more RC filtering stages
- Check that the B+ dropping resistor (R9) is the correct value

### 10.4 Microphonic Tubes

If tapping the chassis or a tube with a pencil produces a ringing or "boing" sound through the speaker, one of the tubes is microphonic. The 12AX7 preamp tube is most susceptible because it has the highest gain.

**Solution:** Replace the microphonic tube. If multiple 12AX7s exhibit microphonics, try a different brand. Some brands are inherently more microphonic than others. Tube damper rings (silicone rings that fit over the tube) can help.

### 10.5 Excessive Noise (Hiss)

Some hiss is normal in a tube amp — tubes are inherently noisier than semiconductors. But if the hiss is excessive:
- Try a different 12AX7. Some individual tubes are much noisier than others. "Low noise" selected tubes from tube vendors are worth the small premium.
- Check for dirty or corroded tube socket pins. Clean with DeoxIT contact cleaner.
- Check that all grid resistors are connected properly. An open grid (no DC path to ground) will produce maximum noise.

> **What happens if... the 6V6 plate glows red?**
> **POWER OFF IMMEDIATELY.** A red-plating power tube is dissipating far more power than it's designed for. This can be caused by: (1) a shorted or leaky coupling capacitor (C4) that puts positive DC on the grid, driving the tube into maximum conduction, (2) a failed cathode resistor (R8) that eliminates the self-biasing, (3) excessive B+ voltage, or (4) a defective tube. Leaving a red-plating tube running will destroy the tube and can damage the output transformer. Diagnose and fix the cause before reinserting any tube.

---

## 11. Common Mods and Tweaks

Once your amp is working and you've spent some time playing through it, you may want to customize the tone. The 5F1 is one of the most-modded circuits in existence. Here are proven modifications:

### 10.1 Different Preamp Tubes

The first stage (V1a) has the most impact on tone. Try:
- **12AX7 (stock):** Highest gain (mu = 100). Maximum overdrive.
- **12AT7:** Lower gain (mu = 60). More headroom, cleaner at higher volumes. Nice for recording when you want less distortion.
- **12AY7:** Even lower gain (mu = 44). The original Fender tweed amps used 12AY7s. Very dynamic and touch-sensitive.
- **12AU7:** Lowest gain (mu = 17). Essentially a clean amp — very little breakup. Good for pedal platforms.

These are all pin-compatible and can be swapped without any circuit changes.

### 10.2 Negative Feedback (Adding It)

The stock 5F1 has no global negative feedback. Adding a feedback loop from the output transformer secondary to the V1b cathode:

- **Reduces distortion** at moderate levels
- **Tightens bass** response
- **Increases headroom** before breakup
- **Changes the amp's character** from "raw and dynamic" to "polished and controlled"

To add feedback: connect a 22k resistor from the output transformer secondary (speaker side) to pin 8 (cathode) of the 12AX7's second triode. If the amp oscillates, reverse the secondary connections (you have the feedback polarity wrong).

Experiment with resistor value: 22k = moderate feedback, 47k = light feedback, 10k = heavy feedback.

### 10.3 Tone Cap Changes

The coupling capacitors (C2, C4) affect the tonal character:
- **Smaller values (0.01 uF):** Less bass, brighter tone, earlier treble breakup
- **Stock values (0.02 uF):** Balanced tone
- **Larger values (0.047 uF):** More bass, fuller tone, can sound "muddy" with some speakers

The cathode bypass capacitors (C1, C3) affect gain at low frequencies:
- **Removing them entirely:** Drops the gain significantly and evens out the frequency response. Very clean, less compressed.
- **Smaller values (1 uF):** Bypasses only higher frequencies, producing a boost in the upper mids and treble.
- **Stock values (25 uF):** Full bypass across the audio band. Maximum gain.

### 10.4 Power Tube Alternatives

The 6V6 socket can accept several other octal-based tubes with different characteristics:
- **6V6GT (stock):** Sweet, compressed, classic Fender tone
- **6L6 (with appropriate rebiasing and B+ adjustment):** More headroom, cleaner, bigger sound — but requires checking that the power transformer can handle the increased current draw
- **EL84 (with socket adapter):** Different pin configuration but can be adapted. Brighter, more aggressive character. Used in Vox amps.

Tube rolling the power tube requires verifying that the plate dissipation stays within the tube's rating. Always check bias after a tube swap.

---

## 11. Maintenance and Tube Life

### 11.1 Tube Lifespan

Tubes don't last forever:
- **12AX7 preamp tubes:** 5,000-10,000+ hours. They tend to get noisy (microphonic or hissy) rather than fail outright.
- **6V6 power tubes:** 1,000-3,000 hours. Output tubes work harder and wear faster. As they age, output power drops and distortion changes.
- **5Y3 rectifier:** 2,000-5,000 hours. Rectifier failure usually means loss of B+ (amp goes silent).

### 11.2 Signs of Worn Tubes

- Loss of output power
- Increased noise or hum
- Microphonic behavior (tapping the tube produces sound through the speaker)
- Red-plating (the 6V6 plate glows visibly red — this is an emergency, power off immediately)
- Intermittent crackling or popping

### 11.3 Maintenance Schedule

Every 6 months or 500 hours of use:
- Check and retorque chassis bolts (they can loosen from vibration and thermal cycling)
- Verify earth ground continuity
- Check tube pins for corrosion (remove tubes, inspect sockets)
- Check all solder joints visually (reflow any that look dull or cracked)
- Measure bias to verify the operating point hasn't drifted significantly

> **ALWAYS discharge the filter caps before performing any maintenance. Every time. No exceptions.**

---

## 12. Chapter Summary

You've built a working tube guitar amplifier. This is a significant achievement — you've:

1. **Prepared a chassis** with precision-drilled holes for tube sockets, transformers, and hardware.
2. **Built a turret board** with the amplifier circuit, using voltage-rated components and proper lead dress.
3. **Wired the power supply first** and tested it in isolation — verifying B+ voltages before risking any other components. This disciplined approach prevents expensive mistakes.
4. **Completed the audio circuit** with careful attention to wiring routing, grounding, and component placement.
5. **Verified the bias** of the 6V6 power tube, confirming safe operating conditions.
6. **Powered up safely** through a dim bulb tester, following the complete safety checklist from Chapter 22.
7. **Played guitar through your own tube amplifier.** That's a feeling that never gets old.

The 5F1-inspired amp is your platform for understanding tube amplifier design. The mods in Section 10 give you a roadmap for years of experimentation. And the safety habits you've built here will serve you well if you ever tackle larger, more powerful (and more dangerous) tube amp projects.

You now have three working amplifiers: the LM3886 for hi-fi listening, the TPA3255 for high-power applications, and a tube guitar amp that sounds like rock and roll history. Part 3 is complete.

---

*Previous: [Chapter 23: Build: Chip Amp and Class D Amp](23-build-chip-amp-class-d.md)*
*Up: Part 3 — Amplifiers*
*Next: [Part 4 — Digital Audio](../part-4-digital-audio/)*
