<!--
  CHAPTER: 4
  TITLE: Your Workbench, Tools, and Safety
  PART: 0 — Electronics for Audio People
  PREREQS: Chapter 3
  KEY_TOPICS: soldering technique, desoldering, multimeter, oscilloscope, signal generator, power supply, mains voltage safety, capacitor discharge, tube amp voltage, one-hand rule, safe habits
  DIFFICULTY: Beginner
  UPDATED: 2026-04-10
-->

# Chapter 4: Your Workbench, Tools, and Safety

> **Part 0 — Electronics for Audio People** | Prerequisites: Chapter 3 | Difficulty: Beginner

You can design the most brilliant circuit in the world, simulate it perfectly in LTspice, and have a flawless schematic in KiCad -- but at some point, you have to sit down with a soldering iron and make it real. This chapter is about the physical side of audio electronics: the skills, tools, and -- critically -- the safety practices that keep you alive and productive.

I'm putting safety in this chapter rather than at the very beginning of the guide because I wanted you to understand *what* you're working with before I tell you *how* to be careful with it. Now that you understand voltage, current, power, and how circuits work, the safety guidelines will make intuitive sense rather than feeling like arbitrary rules. But make no mistake: the safety section of this chapter is the most important content in the entire guide. You can learn electronics wrong and waste some money. You can't learn safety wrong -- the consequences are permanent.

That said, the good news is that with proper knowledge and habits, building audio electronics is extremely safe. Millions of hobbyists do it every day without incident. The dangers are specific, well-understood, and avoidable. Let's make sure you know where they are and how to handle them.

### In This Chapter
- Soldering technique refinement for through-hole PCBs
- Drag soldering for headers and multi-pin components
- Flux usage and tip maintenance
- Desoldering: solder braid, solder sucker, hot air
- Multimeter deep dive: every function explained for audio work
- When you need an oscilloscope (and which one)
- Signal generators for testing
- Power supply options for different projects
- Safety: mains voltage, capacitor discharge, tube amps, the one-hand rule
- Building safe habits

### Related Chapters
- [Ch 0: Electricity Through the Lens of Sound](00-electricity-through-sound.md) -- the electrical quantities you'll be measuring
- [Ch 3: Simulation and Analysis Tools](03-simulation-analysis-tools.md) -- the simulation tools that complement physical measurement
- [Ch 1: Components and What They Do to Signals](01-components-and-signals.md) -- the components you'll be soldering and testing

---

## 1. Soldering Technique Refinement

### 1.1 A Note on Assumed Knowledge

You already know how to solder. You've held a soldering iron, melted solder, and made joints. This section isn't "how to solder for the first time" -- it's the refinements and best practices that make the difference between joints that work and joints that work reliably for years under vibration, temperature cycling, and handling.

Audio circuits are sensitive to bad connections: a cold solder joint in a signal path can cause crackling, intermittent cutouts, or added noise. A cold joint in a power supply can cause hum. A solder bridge between adjacent pads can short a signal to ground (silence) or short a power rail to a signal pin (smoke). Getting your soldering consistently right is the difference between a build you're proud of and one that frustrates you.

### 1.2 Through-Hole Soldering for PCBs

Most DIY audio builds (pedals, amp kits, crossover networks) use through-hole components on PCBs. The technique, refined:

**Step 1: Insert the component**
Push the component leads through the correct holes from the top side of the PCB. The component body sits on the top; the leads protrude from the bottom (solder side). Bend the leads slightly outward (about 15-30 degrees from vertical) to hold the component in place while you flip the board. Don't bend them flat against the board -- you may need to remove the component later.

For resistors and small capacitors, bend the leads to match the hole spacing before insertion. A lead-bending jig (or just a pair of needle-nose pliers and a ruler) makes this fast and consistent.

**Step 2: Heat the pad and lead simultaneously**
Place the soldering iron tip so it touches BOTH the PCB pad (the copper ring around the hole) and the component lead at the same time. This is the single most important step: both surfaces need to be hot enough to accept solder. If you heat only the pad, the solder won't flow onto the lead (cold joint on the lead side). If you heat only the lead, the solder won't flow onto the pad (cold joint on the pad side).

The ideal tip position: press the flat face of a chisel tip against the pad surface with the tip also contacting the component lead. This maximizes heat transfer to both surfaces simultaneously.

**Step 3: Apply solder to the junction (not the iron tip)**
Touch the solder wire to the junction of the pad and lead -- NOT to the iron tip itself. The solder should melt on contact with the heated joint, flowing smoothly onto both the pad and the lead by capillary action. Apply enough solder to fill the through-hole and form a small, slightly concave fillet around the lead.

Typical timing: 1-3 seconds of heating, then 0.5-1 second of solder application. Total time per joint: 2-4 seconds. If it takes longer than 5 seconds, you're either not transferring heat efficiently (wrong tip size, poor contact, old tip) or the pad is large and thermally connected to a ground plane (which sinks heat away -- you may need a higher temperature or a larger tip for those joints).

**Step 4: Remove solder wire, then remove iron**
Pull the solder wire away first, then lift the iron cleanly. Don't "drag" the iron off the pad -- this can pull solder into a spike or create a bridge to an adjacent pad.

**Step 5: Inspect**
A good solder joint has these qualities:
- **Shiny** surface (leaded solder) or slightly satin/matte (lead-free solder -- this is normal for lead-free)
- **Smooth** contours, no cracks or grainy texture
- **Concave** fillet shape -- the solder curves inward from the pad to the lead, like a gentle funnel
- **Complete wetting**: solder has flowed onto both the pad and the lead with no gaps
- **Appropriate volume**: enough solder to fill the hole and form a fillet, but not so much that it forms a large blob

A bad solder joint may look:
- **Dull and grainy**: Cold joint -- wasn't hot enough when the solder solidified. Reheat and add fresh solder.
- **Blobby**: Too much solder, possibly bridging to adjacent pads. Remove excess with solder wick.
- **Spiky or pointed**: Solder was pulled away while still molten. Usually functional but cosmetically poor.
- **Cracked or ring-shaped**: Joint moved while solidifying. Reheat, let it solidify without movement.

### 1.3 Common Through-Hole Mistakes and Fixes

| Mistake | What It Looks Like | Audio Consequence | Fix |
|---|---|---|---|
| Cold joint | Dull, grainy, rough surface | Intermittent connection, crackle, added noise | Reheat and add a touch of fresh solder |
| Insufficient solder | Pin visible, thin solder coating | Weak joint, prone to cracking under vibration | Add more solder while reheating |
| Excessive solder | Large blob that may bridge to adjacent pad | Short circuit between unrelated connections | Remove excess with solder wick |
| Overheated pad | Pad lifts from PCB, turns dark brown/black | Permanent PCB damage; trace may be broken | Bridge with wire jumper if pad lifts completely |
| Solder bridge | Two adjacent pads connected by a solder blob | Short circuit -- could be catastrophic depending on what's connected | Remove with solder wick, verify with continuity test |
| Component not seated | Component body floats above the PCB surface | Mechanical weakness, longer leads act as antennas | Desolder, reseat, re-solder |
| Wrong component value | Correct type but wrong value (47k instead of 4.7k) | Circuit misbehaves in predictable ways | Desolder, replace with correct value. Measure components BEFORE soldering. |

### 1.4 Drag Soldering for Headers and Pin Arrays

When soldering multi-pin headers (IC sockets, connector headers, pin strips), drag soldering is faster than soldering each pin individually:

1. Solder the first pin to tack the header in place. Verify it's straight and flush.
2. Solder the last pin (diagonal from the first) to lock alignment.
3. Apply a thin coat of flux paste along the remaining pins.
4. Load a small amount of solder onto the iron tip (a "solder bead").
5. Touch the tip to the first pin, making contact with both pin and pad.
6. Slowly drag the tip along the row of pins at a steady speed. The solder flows to each pad/pin junction by capillary action, pulled by the flux.
7. If bridges form (solder connecting adjacent pins), don't panic -- clean with solder wick. Bridges are expected and easily fixed.

Drag soldering works best with a chisel-style tip (flat edge for maximum thermal contact area), good flux, and a steady hand. Practice on scrap headers before doing it on your build.

### 1.5 Flux: The Unsung Hero

**Flux** is a chemical that cleans metal surfaces (removes oxide layers) and promotes solder flow. Solder wire has flux in its core (that's the "rosin core" label on good quality solder), but adding extra flux makes difficult joints easier and drag soldering possible.

**When to add extra flux**:
- Reworking (re-soldering) joints: the original flux has been consumed
- Desoldering: helps solder wick absorb molten solder faster
- Soldering to oxidized or tarnished pads (old PCBs, recycled components)
- Drag soldering headers
- Any time solder isn't flowing well despite adequate heat

**Types of flux**:
- **Rosin flux (RMA/R type)**: Mild activity, leaves amber residue that's non-corrosive and generally doesn't need to be cleaned (but cleaning improves cosmetics). The default choice for audio electronics.
- **No-clean flux**: Very mild, leaves minimal clear residue. Designed for automated manufacturing where cleaning isn't practical.
- **Water-soluble flux (OA type)**: More active (cleans oxides better), but leaves corrosive residue that MUST be cleaned with water after soldering. Best for difficult joints on oxidized surfaces, but don't forget to clean.

**Application**: Flux pens are the most convenient for hobbyist work -- just click and brush. Liquid flux in a small squeeze bottle with a needle tip also works well. Flux paste (gel) in a syringe is ideal for drag soldering and rework.

### 1.6 Tip Maintenance

Your soldering iron tip is a precision tool. Its condition directly affects your soldering quality:

- **Tin the tip** before storing: apply a generous coating of solder to the tip before turning the iron off. This protects the tip's iron plating from oxidation at high temperature.
- **Clean frequently**: Use a brass wire sponge (preferred) to wipe the tip between joints. The brass sponge removes oxide and excess solder without cooling the tip. A damp sponge works too but cools the tip by 50-100 C each time you wipe, which slows down your work.
- **Don't use the tip as a scraper**: Scraping flux residue or stuck components damages the plating.
- **Don't file the tip**: Modern plated tips (iron-plated over copper) must not be filed. Filing removes the iron plating, and the exposed copper dissolves rapidly in molten solder.
- **Replace when worn**: Tips are consumable. When a tip is pitted, won't hold solder (solder beads off instead of wetting the surface), or takes much longer to heat a joint, replace it. Budget tips cost $2-5 each.
- **Use tip tinner**: If a tip becomes oxidized and won't accept solder, dip it in tip tinner (a mix of flux and solder powder) while hot. This can restore a tip that seems dead.

### 1.7 Solder Choice

**Leaded solder (63/37 or 60/40 tin-lead)**: Easier to work with, lower melting point (183 C for 63/37 eutectic), shiny joints, excellent wetting. The standard for hobby electronics. 63/37 is preferred over 60/40 because it's eutectic -- it transitions from solid to liquid instantly (no "pasty" intermediate state that can cause cold joints if the board moves during cooling).

**Lead-free solder (SAC305 or similar)**: Required for commercial products in the EU (RoHS compliance). Higher melting point (~217-227 C), duller appearance (normal, not a cold joint indicator), requires more heat and flux. Harder to work with but necessary if you plan to sell in regulated markets.

**Wire diameter**: 0.8 mm (0.031") is ideal for through-hole work. 0.5 mm for fine work and small SMD. 1.0 mm+ for large joints (speaker crossover terminals, heavy ground connections).

**Health note**: Leaded solder contains lead, which is toxic if ingested. Wash your hands after soldering. Don't eat at your soldering bench. The solder fumes you see are rosin flux smoke (irritating to lungs over time), not lead vapor -- lead doesn't vaporize at soldering temperatures. Good ventilation or a fume extractor is recommended for extended soldering sessions.

### 1.8 Recommended Soldering Stations

For audio electronics work, you want a **temperature-controlled soldering station** (not a fixed-wattage pencil iron from the hardware store):

| Level | Model | Approx. Price | Notes |
|---|---|---|---|
| Budget portable | Pinecil V2 | $25-35 | USB-C or DC powered, excellent for the price, open-source firmware, travel-friendly |
| Budget station | KSGER T12 station | $40-60 | T12 cartridge tips (fast heating), good temperature control |
| Mid-range | Hakko FX-888D | $100 | The workhorse. Reliable, well-documented, huge tip selection |
| Professional | JBC CD-2BC | $300+ | Instant heat recovery, incredible for production soldering -- overkill for hobby but a joy to use |

Temperature setting for leaded solder: 315-345 C (600-650 F). For lead-free: 350-385 C (660-725 F). Start at the lower end and increase only if joints are taking more than 3-4 seconds.

---

## 2. Desoldering: Removing Components and Fixing Mistakes

### 2.1 Why Desoldering Matters

You will make mistakes. You will put a component in the wrong place, solder a diode backwards, install the wrong value resistor, or need to replace a failed part. Desoldering is the skill that lets you recover from errors without destroying the board.

Good desoldering is actually harder than good soldering. The goal is to remove solder completely from a through-hole so you can pull the old component out and insert the new one cleanly. Incomplete desoldering means forcing the component out, which risks lifting pads and destroying traces.

### 2.2 Solder Wick (Solder Braid)

**What it is**: A flat braid of fine copper wire, often pre-fluxed. When placed on a solder joint and heated, the braid absorbs (wicks up) the molten solder by capillary action.

**Technique**:
1. Apply fresh flux to the joint (even if using pre-fluxed wick).
2. Place the braid flat on the joint.
3. Press the soldering iron tip firmly on top of the braid, directly over the joint. The iron heats through the braid to melt the solder.
4. Wait 2-3 seconds for the solder to melt and wick into the braid. You'll see the braid darken as it absorbs solder.
5. Remove the iron and braid together in one smooth motion. Do NOT pull the braid while the solder is cold -- the braid will be soldered to the pad, and pulling it will lift the pad.
6. Cut off the used (solder-filled) portion of the braid and repeat if needed.

**Width selection**: Use 1.5-2 mm wide braid for most through-hole work. Narrower (0.8-1 mm) for fine pitch. Wider (2.5-3 mm) for large pads and ground planes.

**Best for**: Removing solder bridges, cleaning pads, desoldering two-pin components (resistors, caps, diodes).

### 2.3 Solder Sucker (Desoldering Pump)

**What it is**: A spring-loaded vacuum pump. You heat the joint with an iron, then trigger the sucker to pull molten solder away through a PTFE tip.

**Technique**:
1. Prime the solder sucker (push the plunger down until it latches).
2. Heat the joint with the soldering iron until the solder is fully molten.
3. Position the solder sucker's PTFE nozzle right next to the molten solder (touching the pad if possible).
4. While maintaining heat with the iron, trigger the sucker with a quick press of the release button.
5. The vacuum pulls molten solder into the sucker.
6. Repeat if needed. Add a small amount of fresh solder to the joint and re-melt if the old solder isn't flowing well (fresh solder re-activates old flux).

**Best for**: Clearing through-holes for component removal. Faster than wick for multi-pin components.

**Quality matters**: A $3 solder sucker from the dollar store barely works. The **Engineer SS-02** ($15-20) is dramatically better -- tighter seal, stronger vacuum, replaceable PTFE tip. It's the standard recommendation in the hobby community.

### 2.4 Hot Air Rework

A **hot air rework station** uses a controlled stream of hot air (200-500 C) to heat an area uniformly. This melts all the solder at once, letting you lift a component off.

For through-hole work, hot air is useful for removing multi-pin components (IC sockets, connectors, switches). For surface-mount work, hot air is essential.

You don't need a hot air station for basic through-hole audio work. If you get into surface-mount PCBs or need to rework multi-pin connectors, a basic hot air station ($50-100 for a Quick 861DW or similar) is worthwhile.

### 2.5 Fixing Lifted Pads

If a pad lifts (separates from the PCB) during desoldering:

1. **Assess the damage**: Is the trace still intact under the solder mask, or is it torn? You can check with continuity test from the pad's intended connection to the nearest via or component that shares the trace.

2. **If the trace is intact**: You can often press the pad back down with the iron while applying a tiny amount of solder. It won't be as strong as before, but it'll work. Secure with a drop of epoxy over the top.

3. **If the trace is broken**: Scrape back the solder mask on the trace near the lifted pad to expose bare copper. Solder a thin wire (30 AWG wire-wrap wire works well) from the component lead to the exposed trace. Secure the wire with epoxy to prevent mechanical stress.

4. **If the pad is completely gone**: Solder the wire directly from the component lead to wherever the trace was going (another component's pad, a via, etc.). This is an "air wire" or "bodge wire" and is common in professional rework.

Prevention is better than repair: use adequate heat (don't heat a joint forever trying to melt cold solder -- add fresh solder to improve heat transfer), don't apply excessive mechanical force while desoldering, and don't try to pull components through holes that still have solder in them.

---

## 3. The Multimeter: Your Primary Measurement Tool

### 3.1 What a Multimeter Measures

A **digital multimeter** (DMM) measures voltage, current, resistance, and often capacitance, frequency, and diode forward voltage. It's the single most important test instrument for audio electronics. You can build and debug any audio circuit with just a multimeter and an audio probe (Chapter 3). An oscilloscope is nice to have, but a multimeter is essential.

### 3.2 Continuity Test

**What it does**: Applies a small current between the probes and measures resistance. If the resistance is below a threshold (typically 20-50 Ω), the meter beeps.

**Audio applications**:
- **Verifying solder joints**: Probe from the component lead on the top side to the PCB trace on the bottom side. Beep = good joint. No beep = bad joint, cold solder, or cracked trace.
- **Checking cables**: Probe the tip contact at one end and the tip contact at the other end. Beep = cable is good. Check sleeve to sleeve as well. Also verify there's NO continuity between tip and sleeve (no short).
- **Finding solder bridges**: If two adjacent pads that shouldn't be connected show continuity, you have a bridge.
- **Tracing PCB traces**: When you can't visually follow a trace on a complex multi-layer PCB, use continuity to determine where it goes.
- **Checking switch contacts**: Verify that the switch connects and disconnects the correct terminals.

**Important**: Always do continuity tests with the circuit **powered off** and any large capacitors discharged. Active circuits can give misleading readings, and in some cases, continuity test current can interact with active components.

### 3.3 DC Voltage Measurement

**What it does**: Measures the steady-state voltage between two points.

**Audio applications**:
- **Checking power supply voltage**: Is your 9V supply actually 9V? A fresh alkaline battery is about 9.5V. A "dead" battery might read 6-7V -- enough to power the circuit but not enough headroom for clean operation.
- **Checking bias points**: Measure the DC voltage at transistor collectors, bases, and emitters. Compare to expected values from the schematic.
  - In a 9V pedal with virtual ground at 4.5V, the collector of a properly biased transistor should be somewhere between 2V and 7V (depending on the design).
  - The base-emitter voltage of a conducting silicon transistor should be about 0.6V.
  - The op-amp output in a single-supply circuit should sit at about Vcc/2 (4.5V for a 9V supply).
- **Diagnosing dead circuits**: If the power supply voltage reads 0V at the circuit, you have a wiring error, blown fuse, or open protection diode.
- **Finding shorts**: If a node that should be at a specific voltage is at 0V or Vcc, something is likely shorted.

**Technique**: Set the meter to DC voltage (V with a straight line or "DC" label). Place the black probe on the circuit's ground reference, and the red probe on the node you're measuring. Read the display.

### 3.4 AC Voltage Measurement

**What it does**: Measures the RMS value of an AC voltage.

**Audio applications**:
- Measuring signal levels at various points in the circuit (with a test signal applied)
- Checking power supply ripple (switch to AC mode while probing the DC power rail -- the reading shows the AC component riding on the DC, which should be very small)
- Measuring the output voltage of an amplifier to calculate power: P = V²/R

**Limitations**: Most DMMs measure AC voltage accurately only at low frequencies (50/60 Hz for basic meters, up to a few kHz for better ones). At higher audio frequencies (especially above 5-10 kHz), cheap meters become inaccurate. Don't trust a budget DMM's AC voltage reading for precision audio measurements above a few hundred hertz. An oscilloscope is the right tool for AC signal measurement across the audio band.

**True RMS vs average-sensing**: Cheap meters measure the average of the rectified waveform and apply a correction factor that assumes a sine wave. This gives correct readings for sine waves but wrong readings for complex waveforms (music, square waves, distorted signals). A **True RMS** meter computes the actual RMS value regardless of waveform shape. For audio work, True RMS is preferred.

### 3.5 Resistance Measurement

**What it does**: Applies a small known current through the component and measures the resulting voltage to calculate resistance (Ohm's law in reverse).

**Audio applications**:
- **Pre-flight component verification**: Before soldering, measure each resistor to confirm its value. This catches picking errors -- grabbing a 47k instead of a 4.7k from the parts drawer. They look almost identical (the color bands differ by one band). This 30-second check prevents hours of debugging.
- **Checking potentiometers**: Measure the total resistance between the two end terminals (should be close to the rated value). Then measure from the wiper to each end while slowly turning the shaft -- resistance should change smoothly. A scratchy pot (common in old gear) shows jumpy, erratic readings.
- **Measuring speaker DC resistance (DCR)**: An "8 ohm" speaker typically reads 5.5-7.0 Ω DCR. This is normal and expected -- the nameplate impedance is the impedance at a specific frequency (usually 1 kHz or the impedance minimum), not the DC resistance.
- **Checking coils**: An inductor or transformer winding should measure as a low resistance (typically under 100 Ω for audio inductors). An open reading indicates a broken wire inside the coil.

**Critical rule**: Measure resistance with the component **disconnected from the circuit** (or at least with power off and caps discharged). Other components in parallel will affect the reading, giving misleading results. If you can't remove the component, the in-circuit reading will always be lower than or equal to the component's actual value (because parallel resistance is always less than the smallest individual resistance).

### 3.6 Capacitance Measurement

**What it does**: Uses a charge-discharge timing technique to determine capacitance.

**Audio applications**:
- **Verifying capacitor values**: Especially important for unmarked caps (ceramic discs often have only a three-digit code that's easy to misread) and electrolytics pulled from old equipment.
- **Checking aging electrolytics**: Electrolytic capacitors dry out over time and lose capacitance. A 100 μF cap that measures 40 μF has degraded significantly and should be replaced. This is a common failure mode in vintage audio equipment (30+ years old).
- **Matching capacitors for crossovers**: Speaker crossover capacitors that need to be closely matched between left and right channels can be measured and selected. Matching to within 2-3% is good practice for stereo crossovers.

**Limitations**: Most DMMs can measure capacitance up to about 100-1000 μF. For very large values, you need a dedicated LCR meter. Accuracy is typically 2-5% -- good enough for verification but not for precision matching. For tight matching, measure a batch of capacitors and select pairs that read within 1% of each other.

### 3.7 Diode Test

**What it does**: Applies a small current through the component and displays the forward voltage drop.

**Audio applications**:
- **Verifying diode orientation**: The meter reads forward voltage in one direction (typically 0.5-0.8V for silicon) and "OL" (over-limit, open circuit) in the other. If it reads a voltage in both directions, the diode is shorted. If it reads OL in both directions, the diode is open (failed).
- **Identifying diode type**: Silicon reads ~0.6V, germanium ~0.25-0.35V, Schottky ~0.15-0.25V, LED ~1.5-3.0V (depending on color). This helps you identify unmarked diodes in salvaged circuits.
- **Checking transistor junctions**: A BJT is effectively two back-to-back diodes sharing a common terminal. In diode test mode:
  - NPN: Base-to-Collector reads ~0.6V, Base-to-Emitter reads ~0.6V, Collector-to-Emitter reads OL in both directions (if the transistor is good)
  - If any junction reads OL in both directions: that junction is open (transistor damaged)
  - If Collector-to-Emitter reads low: transistor is shorted (damaged)

### 3.8 Recommended Multimeters

| Level | Model | Approx. Price | Key Features for Audio |
|---|---|---|---|
| Minimum viable | UNI-T UT33C+ or similar | $15-25 | Continuity, DC voltage, resistance -- covers 70% of needs |
| Recommended | UNI-T UT61E+ or Brymen BM235 | $50-80 | True RMS, capacitance, duty cycle, reliable build quality |
| For mains work | Fluke 117 or 87V | $150-400 | CAT III/IV rated, robust, reliable for high-voltage measurements |
| Bench meter | Keysight 34461A | $1000+ | 6.5 digits, data logging, GPIB/USB -- overkill for hobby, great for product development |

**CAT ratings matter for safety**. If you ever work on mains-connected equipment (amplifiers, powered speakers, power supplies), your meter needs proper **CAT III** (distribution level, up to 600V) or **CAT IV** (utility entrance) safety ratings. A meter that fails during a mains voltage measurement can arc, blow out, and injure you. Cheap unbranded meters from AliExpress often have fake safety ratings -- the protection circuits (fuses, MOVs) are either absent or inadequate.

For 9V pedal work, any meter is safe. For amplifier and power supply work involving mains voltage, buy a reputable brand: Fluke, Brymen, UNI-T (higher-end models), Keysight, or Siglent.

---

## 4. When You Need an Oscilloscope

### 4.1 What a Multimeter Can't Do

A multimeter gives you a single number: the voltage *now*, the resistance, the capacitance. But audio signals change thousands of times per second. A multimeter can't show you:

- The **waveform shape**: Is it a clean sine, a clipped square, a sawtooth? Is the clipping symmetric?
- **Distortion details**: Are the peaks rounded (soft clipping) or flat (hard clipping)?
- **Noise characteristics**: Is the noise mains hum (60/120 Hz), RF interference (MHz range), or broadband hiss?
- **Timing**: Is there ringing after a transient? Is a square wave overshoot present? How fast is the rise time?
- **Frequency content**: Most scopes have built-in FFT for spectral analysis.
- **Two-signal comparison**: What does the output look like relative to the input? Is there phase shift?

An oscilloscope shows you voltage as a function of time on a continuously updating display. For audio work, it's the difference between knowing "there's a signal" and understanding "this is exactly what the signal looks like."

### 4.2 When to Buy One

You can do a surprising amount of audio work without a scope. The audio probe (Chapter 3) substitutes for many scope functions by letting you *hear* the signal character. A multimeter covers DC verification. LTspice simulation covers waveform prediction. If you're on a budget, these three tools get you very far.

Buy an oscilloscope when:
- You're designing amplifiers and need to see clipping behavior, crossover distortion, and oscillation
- You're debugging persistent noise issues where seeing the noise waveform identifies the source
- You're working on digital audio circuits (I2S, SPI, I2C signals are invisible without a scope)
- You're designing or troubleshooting power supplies (switching noise, ripple, regulation transients)
- You want to verify simulation results quantitatively
- You're developing products for sale and need to do proper testing

### 4.3 Recommended Oscilloscopes for Audio

| Level | Model | Approx. Price | Key Features |
|---|---|---|---|
| Budget portable | Hantek DSO2D10 or FNIRSI-1014D | $80-150 | 2 channels, 100 MHz, basic but functional |
| Standard hobby | Rigol DS1054Z | $350-400 | 4 channels, 50 MHz (hackable to 100 MHz), enormous user community |
| Recommended | Siglent SDS1104X-E | $400-500 | 4 channels, 100 MHz, better UI and decoder options, built-in signal generator |
| Portable pro | Siglent SDS2104X Plus | $700-900 | 4 channels, 100 MHz, 12-bit ADC (better for audio -- 16x more voltage resolution than 8-bit scopes) |

For audio work, you need at least **2 channels** (to compare input and output simultaneously) and a bandwidth of at least **20 MHz** (far more than audio needs, but you want margin for seeing high-frequency oscillation and power supply switching noise). The Rigol DS1054Z remains the default recommendation for hobbyists -- well-documented, huge community, and capable. The Siglent SDS1104X-E is the slightly more polished alternative.

If you care about audio signal fidelity (seeing low-level distortion, noise floors), consider a **12-bit oscilloscope** (like the Siglent SDS2104X Plus). Standard scopes are 8-bit, which gives only 256 voltage levels -- that's about 48 dB of dynamic range, barely enough to see distortion details in an audio signal. A 12-bit scope gives 4096 levels and ~72 dB of dynamic range, much better for audio analysis.

### 4.4 Basic Oscilloscope Usage for Audio

1. **Connect the probe ground clip** to the circuit's ground. Important: scope probe grounds are typically connected to mains earth through the scope's power cord. Never connect the scope ground to any node that isn't ground -- this creates a short through the scope's ground wire, potentially destroying components or creating a safety hazard.

2. **Set the vertical scale** to match expected signal levels. Guitar pedal signals range from 10 mV to 5V depending on the point in the circuit. Start at 1V/div and adjust.

3. **Set the timebase** to show a few complete cycles. For 1 kHz: one period = 1 ms, so set to 0.5 ms/div to see ~2 cycles.

4. **Set the trigger** to the channel you're viewing, edge trigger, auto mode. This stabilizes the display.

5. **What to look for**:
   - Clean sine wave: circuit is in its linear operating region
   - Flat tops/bottoms: clipping. Symmetric = both rails. Asymmetric = one rail.
   - Staircase or notch at zero crossing: crossover distortion in push-pull output stage -- a bias problem
   - High-frequency fuzz riding on the signal: oscillation -- check bypass caps
   - 50/60 Hz sine wave superimposed: mains hum -- grounding or filtering issue

---

## 5. Signal Generators for Testing

### 5.1 Why You Need a Known Test Signal

When debugging or measuring, you need a signal that's consistent, repeatable, and known. Playing guitar introduces too many variables. A signal generator provides exactly what you need.

### 5.2 Options from Free to Full-Featured

**Smartphone app + cable** ($0): A tone generator app outputs a sine wave through the headphone jack or USB-C audio. Output is typically line level (~300 mV to 1V). Useful for quick tests. Limitations: no amplitude control below the phone's minimum volume, limited frequency accuracy.

**Software + audio interface** ($0 if you already have a DAW and interface): Your DAW or a free tool like REW (Room EQ Wizard) can generate precise test signals -- sine waves, sweeps, noise, multi-tone signals -- and output them through your audio interface. Very flexible, very precise.

**Dedicated function generator** ($50-150): Standalone instruments like the FY6900 or Siglent SDG1032X produce sine, square, triangle, and arbitrary waveforms at adjustable frequency (0.01 Hz to 20+ MHz) and amplitude. Useful for bench work where you need quick, precise signal generation without a computer.

**Oscilloscope built-in generator**: Some scopes (Siglent SDS1104X-E, Rigol DS1054Z with hack) include a built-in function generator. Very convenient -- generate and measure with one instrument.

### 5.3 Essential Test Signals

| Signal | Frequency | Use |
|---|---|---|
| Sine wave | 1 kHz | Universal audio test. Gain measurement, distortion analysis, noise measurement. |
| Sine wave | 100 Hz | Low-frequency behavior: coupling cap cutoff, bass response, transformer response. |
| Sine wave | 10 kHz | High-frequency behavior: bandwidth, slew rate, high-frequency distortion. |
| Sine wave | 20 Hz | Very low frequency: tests coupling caps, checks for subsonic artifacts. |
| Frequency sweep | 20 Hz - 20 kHz | Maps the full frequency response of a circuit or speaker. |
| Square wave | 1 kHz | Transient response test: overshoot, ringing, and rise time reveal stability issues. |
| Pink noise | Broadband | Equal energy per octave. Reveals frequency response anomalies by ear. |
| White noise | Broadband | Equal energy per Hz. Used for measurement and speaker break-in. |
| Two-tone (1 kHz + 1.5 kHz) | Two frequencies | Intermodulation distortion test: reveals nonlinearity products. |

---

## 6. Power Supply Options

### 6.1 For Pedals: 9V Battery or Adapter

Most guitar pedals run on 9V DC. Options:

**9V alkaline battery**: Simple, zero noise (batteries don't produce electrical noise), finite life (~25 hours at 20 mA draw). Best for performance and critical recording where absolute silence matters. The "dying battery" sound (voltage sagging below 9V) is actually sought after for some fuzz and distortion circuits.

**9V DC wall adapter**: Must be regulated (steady 9V regardless of load) and correct polarity (**center-negative** is the guitar pedal standard -- the tip of the 2.1mm barrel connector is negative, the sleeve is positive). This is the opposite of most consumer electronics and catches newcomers constantly. An unregulated adapter can output 12-14V with a light load, potentially exceeding component voltage ratings.

**Isolated pedal power supply**: Units like the Voodoo Lab Pedal Power, Strymon Zuma, or Cioks DC7 provide multiple isolated 9V outputs. Isolation prevents ground loops between pedals (which cause buzz). Worth the investment if you have more than 3-4 pedals.

### 6.2 For Bench Work: Adjustable Bench Supply

A **bench power supply** provides adjustable voltage and current with displays showing exactly what the circuit draws. Invaluable for development:

- **Current limiting**: Set the limit to slightly above expected draw (e.g., 100 mA for a pedal). If a wiring error causes excessive current, the supply limits it before components are damaged. This feature alone justifies owning a bench supply.
- **Current monitoring**: Watch the display while powering up. Normal draw for a typical pedal: 5-50 mA. If you see 500 mA, something is shorted.
- **Voltage adjustment**: Test circuits at different voltages (7V, 9V, 12V, 18V) to explore headroom and behavior.

**Recommendations**:
- Budget: KORAD KA3005D ($50-70). 30V/5A single output, digital display, current limiting.
- Mid-range: Rigol DP832 ($350-400). Triple output (30V/3A × 2, 5V/3A × 1), programmable.
- Quick-and-dirty: USB-C PD trigger board ($5-10) with a USB-C phone charger. Outputs 5V, 9V, 12V, 15V, or 20V at the push of a button. No current limiting but surprisingly useful for bench work.

### 6.3 For Amplifier Work: Variac and Isolation Transformer

When working on mains-powered amplifiers (especially tube amps), two additional tools are important:

**Variac** (variable autotransformer): Lets you slowly bring up the mains voltage from 0V to 120/240V. This is critical for first power-on of a new build or repair -- if something is wrong (short, incorrect wiring), the slow ramp gives you time to spot problems (rising current, smoke, incorrect voltages) before full power is applied. A dim-bulb tester (a light bulb in series with the mains) is a cheaper alternative -- the bulb limits current if there's a short.

**Isolation transformer**: Isolates the circuit's ground from mains earth ground. This prevents lethal shock if you accidentally touch a live chassis while grounded (standing on a concrete floor, touching a grounded instrument, etc.). Essential for working on "hot chassis" designs -- some vintage amplifiers have the chassis directly connected to one side of the mains. Without isolation, touching the chassis while grounded is exactly like touching a live mains wire.

---

## 7. Safety: The Section That Keeps You Alive

### 7.1 The Voltage Danger Spectrum

Not all voltages are equally dangerous. Here's the hierarchy:

| Voltage Range | Source | Danger Level | Where You'll Encounter It |
|---|---|---|---|
| 0-12V DC | Batteries, USB, wall adapters | **Minimal** | Pedals, Raspberry Pi, most DIY projects |
| 12-48V DC | Phantom power, automotive | **Low** (startling but rarely injures) | Condenser mics, some active electronics |
| 48-100V DC | Some preamps, industrial | **Moderate** (can cause muscle contraction, burns) | Low-voltage tube circuits, PoE |
| 120-240V AC | Mains power | **LETHAL** | Amplifiers, powered speakers, PSU internals |
| 250-600V DC | Tube amp plate supply | **LETHAL** (stored in caps, persists after power off) | Tube power amps, vintage receivers |

**The critical threshold**: Below about 50V DC, the risk of lethal shock is very low for healthy adults with dry skin. Above 50V, and especially at mains voltage (120/240V AC) or tube amp plate voltage (250-600V DC), electrocution is a real possibility.

Why is AC more dangerous than DC at the same voltage? Because AC at mains frequencies (50/60 Hz) is particularly effective at causing cardiac fibrillation. The alternating current causes the heart muscle to contract and release rapidly, disrupting its normal rhythm. DC is more likely to cause a single sustained contraction that, while painful, is less likely to fibrillate the heart. However, DC can also kill -- especially at the voltages found in tube amplifiers.

### 7.2 Mains Voltage: 120/240V AC Will Kill You

This isn't hyperbole. Mains voltage regularly kills people -- the US sees roughly 400 electrocution deaths per year. The current needed to cause fatal cardiac fibrillation is as low as **30 milliamps** across the chest -- less than the current drawn by a single LED. Your household outlet can supply thousands of amps, limited only by the circuit breaker, which trips in milliseconds to seconds -- enough time for lethal damage.

**Rules for mains work**:

1. **Never work on a mains-connected circuit while it's plugged in** unless you are experienced, using proper tools (insulated probes, isolation transformer), and have a specific reason that requires live testing.

2. **Use an isolation transformer** when live testing is required. This limits fault current and prevents shock paths through earth ground.

3. **Use a variac or dim-bulb tester** for first power-on. Bring up voltage slowly and monitor current. If current is excessive (the dim bulb lights brightly), power off immediately and find the fault.

4. **Verify the circuit is dead before touching anything inside**. Unplug it. THEN verify with a multimeter that there's no voltage present at the power supply filter caps and the chassis. Don't assume unplugging made it safe -- capacitors store charge (see next section).

5. **Install proper fuses**. A fuse protects against fire (it opens when current exceeds its rating, usually from a short circuit). It does NOT protect against shock -- lethal current (30 mA) is far below any fuse rating. But fuses prevent a shorted component from starting a fire.

6. **Use proper strain relief and insulation on mains wiring**. Mains wires should be routed away from signal wiring, secured with cable clamps or grommets at chassis entry points, and insulated with heat-shrink tubing at all terminations.

7. **Ground the chassis**. In any mains-powered device with a metal chassis, the chassis MUST be connected to mains earth ground through the three-prong plug's ground pin. This ensures that if a live wire contacts the chassis (from insulation failure, a loose wire, or a component fault), the circuit breaker or GFCI trips immediately instead of leaving the chassis energized and waiting for someone to touch it.

8. **Consider a GFCI outlet**: A Ground Fault Circuit Interrupter detects imbalanced current between hot and neutral (indicating current is leaking through an unintended path -- possibly through a person) and trips in about 5 milliseconds. Using a GFCI outlet for your bench power reduces the risk of lethal shock during mains work.

### 7.3 Capacitor Discharge: The Danger After Power Off

This catches people by surprise because it's counterintuitive -- **the equipment is off and unplugged, but it can still kill you**. Capacitors in power supplies store energy as an electric charge. In a tube amplifier with 400V plate supply and 100 μF of filter capacitance, the stored energy is:

```
E = 0.5 × C × V² = 0.5 × 0.0001 × 400² = 8 joules
```

Eight joules at 400V is more than enough to cause lethal cardiac fibrillation. And depending on the circuit (bleeder resistors may be present but may have failed open), this charge can persist for **minutes to hours** after the amp is turned off and unplugged. Some amplifier designs intentionally omit bleeder resistors to avoid their power dissipation -- in those designs, the caps retain charge essentially indefinitely.

**Capacitor discharge procedure** (MEMORIZE THIS):

1. **Turn off the device and unplug it** from the wall.
2. **Wait 5 minutes** minimum for any bleeder resistors to partially discharge the caps.
3. **Verify voltage with a multimeter** across each filter capacitor (positive terminal to negative terminal, or positive terminal to chassis ground). The meter should read well below 50V before you proceed.
4. **If voltage is still high, manually discharge each cap**: Use a discharge resistor tool -- a high-wattage resistor (e.g., 1kΩ / 10W for high-voltage circuits, 100Ω / 5W for lower voltages) connected via insulated clip leads. Connect the resistor across each capacitor. The resistor converts the stored energy to heat safely.
   - **Never discharge with a screwdriver**. The instantaneous current from a short circuit can weld the screwdriver to the cap terminal, damage the capacitor, and spray molten metal.
   - **Discharge slowly with a resistor**. A 1kΩ resistor across a 100 μF cap gives a time constant of 0.1 seconds -- fast enough to be practical, slow enough to be safe.
5. **Verify again with the multimeter** after discharge. All filter caps should read less than 1V.
6. **Only then** touch anything inside the chassis.

**Build a discharge tool**: Solder a 1kΩ 10W wirewound resistor between two insulated alligator clip leads. Use silicone-insulated wire. Mark the tool clearly with a label: "HV DISCHARGE TOOL -- 1kΩ 10W." Keep it on your bench permanently.

### 7.4 Tube Amplifier Voltage Warnings

Tube amps are the highest-voltage devices you're likely to work on as an audio builder. A typical guitar tube amp has:

- **B+ (plate supply)**: 300-450V DC
- **Screen grid supply**: 250-400V DC (sometimes the same as B+, sometimes a separate tap)
- **Bias supply**: -30 to -60V DC (negative, but still not trivial at high current)
- **Heater supply**: 6.3V AC or 12.6V DC (not dangerous, but the wires carry 1-3A and run close to high-voltage nodes)
- **Power transformer secondary**: 300-400V AC RMS before rectification (peak voltage is 1.414x higher: 424-566V!)

These voltages are present on exposed PCB pads, tube socket pins, component leads, transformer terminals, and tag strips. They are lethal.

**Additional rules for tube amp work**:

1. **Use the one-hand rule** (section 7.5) whenever measuring inside a live tube amp.
2. **Never assume a tube amp is safe because it's off**. ALWAYS discharge capacitors. Every single time. Even if you discharged them an hour ago and haven't turned it on.
3. **Replace the back panel before powering on** during testing. If a tube fails catastrophically (glass breaking, arc flash), the panel contains debris.
4. **Know the schematic and mark the high-voltage nodes**. Before opening the chassis, study the schematic and know exactly where the dangerous voltages are. Consider marking HV nodes with red nail polish or labels.
5. **Have someone nearby** when doing live-chassis work. Not to intervene (pulling someone off a high-voltage source without disconnecting power first makes two victims), but to call emergency services if something goes wrong.
6. **Consider a "chicken stick"** -- an insulated stick (fiberglass, wooden dowel) for manipulating things inside a live chassis without getting your hands close to HV nodes.

### 7.5 The One-Hand Rule

When you must probe a live, high-voltage circuit (and sometimes this is necessary for diagnostic measurements):

**Keep one hand behind your back or in your pocket. Use only one hand for the probe.**

Why? The most dangerous shock path is **hand to hand** across the chest. Current flowing across the chest passes through the heart at as little as 30 mA, potentially causing fatal cardiac fibrillation. Current from one hand to the same arm (not crossing the chest) is painful and can cause burns, but is much less likely to be fatal.

This isn't a suggestion. This is how professional engineers work on live high-voltage equipment, and it's how you should work too. Build the habit now, at 9V, so it's automatic when you encounter 400V.

Practical implementation: Use a probe with an insulated handle (like a proper test probe, not a bare wire). Clip the meter's ground lead to the chassis ground before you start -- don't hold both probes. Then use one hand to touch the signal probe to each measurement point while your other hand is safely away from the circuit.

### 7.6 Building Safe Habits Before You Need Them

Safety isn't about memorizing rules for specific situations. It's about building habits that protect you automatically, even in situations you haven't anticipated.

**Habit 1: Assume it's live.** Treat every circuit as if it's energized until you've verified otherwise with a meter. Even if you unplugged it. Even if you turned it off. Even if you discharged the caps yesterday. Verify.

**Habit 2: Check your meter first.** Before using a multimeter to verify that a circuit is de-energized, verify that the meter itself works by measuring a known live source (like a wall outlet, carefully). A meter with dead batteries or a blown fuse reads 0V on everything -- including a live 400V rail. This "live-dead-live" test methodology (test on a known live source, test the circuit, test the known live source again) is standard in professional electrical work.

**Habit 3: Discharge before touching.** Always discharge filter capacitors before working inside any power supply or amplifier. Make it a ritual. Every time.

**Habit 4: Don't work tired, rushed, or impaired.** More accidents happen at 2 AM when someone is trying to "just finish this one thing." High-voltage work demands your full attention. If you're tired, stop. The amp will still be there tomorrow.

**Habit 5: Keep your workspace organized.** A cluttered bench with exposed wires, stacked equipment, and metal tools scattered near a live chassis is an accident waiting to happen. Keep the work area clear. Put tools you're not using away from the live circuit.

**Habit 6: Know what to do if someone gets shocked.** Don't touch them while they're in contact with the source -- you'll become a second victim. Disconnect the power source first (unplug it, hit the breaker, use a non-conductive object to knock them away from the source). If they're unresponsive, call emergency services immediately. If they're not breathing, begin CPR. If an AED is available, use it.

### 7.7 First Aid at the Electronics Bench

Keep these items within reach:
- Burn gel or aloe vera (soldering iron burns are inevitable -- mild burns happen to everyone)
- Bandages/plasters (component leads are sharp and cuts are common)
- Eye wash solution or saline (flux can splash; lead-free solder is more prone to splattering)
- Safety glasses (wear them! Clipped component leads fly, flux spatters, and solder can pop)
- A charged phone (for calling emergency services)

> **What happens if...** you ignore the safety section because "I'm just building a guitar pedal at 9V"? At 9V, you're absolutely right -- there's no shock hazard. Even shorting a 9V battery just makes it warm. But the habits you build at 9V carry over to 450V. If you routinely reach into circuits without checking voltage, you'll do it reflexively when working on an amplifier. If you never learn to discharge caps because your pedals don't have dangerous filter caps, you won't think to do it when you open a tube amp. The safety practices in this chapter cost you nothing at 9V (a few seconds to check the meter, a moment to think before touching) and save your life at 450V. Build them now. Make them automatic. Your future self will thank you.

---

## 8. Setting Up Your Workbench

### 8.1 Minimum Viable Audio Electronics Workbench

To complete the projects in this guide, you need at minimum:

| Item | Budget Recommendation | Approx. Price | Purpose |
|---|---|---|---|
| Soldering station | Pinecil V2 with DC supply | $30-40 | All soldering work |
| Solder wire | 63/37 leaded, 0.8mm, rosin core | $10-15 | Standard solder for through-hole |
| Solder wick | 2mm width, flux-impregnated | $3-5 | Desoldering and corrections |
| Desoldering pump | Engineer SS-02 | $15-20 | Through-hole component removal |
| Multimeter | UNI-T UT61E+ or similar True RMS | $50-80 | All measurement tasks |
| Wire strippers | Self-adjusting type | $10-15 | Preparing hookup wire |
| Flush cutters | Hakko CHP-170 or similar | $5-8 | Trimming leads after soldering |
| Needle-nose pliers | 5" precision type | $5-10 | Bending leads, holding components |
| Breadboard | Standard full-size (830 tie points) | $5-10 | Prototyping circuits |
| Hookup wire kit | 22 AWG solid core, 6+ colors | $10-15 | Breadboard jumpers, point-to-point wiring |
| Audio probe | Build one per Ch 3 | $3-5 | Signal tracing and debugging |
| Helping hands / PCB holder | Spring-arm type or Omnivise | $10-20 | Holding boards while soldering |
| Safety glasses | ANSI Z87.1 rated | $5-10 | Eye protection during soldering |
| Flux pen | Kester 951 or similar no-clean | $10-15 | Improving solder flow for rework |

**Total: approximately $170-270**

This covers everything you need for pedal building, preamp construction, and breadboard prototyping. You can start building projects from Part 1 (Guitar Pedals) with this setup.

### 8.2 Recommended Additions (as Budget Allows)

| Item | Recommendation | Approx. Price | When to Add |
|---|---|---|---|
| Bench power supply | KORAD KA3005D | $50-70 | When you're building more than 1-2 projects |
| Oscilloscope | Rigol DS1054Z or Siglent SDS1104X-E | $350-500 | When you start amplifier work or persistent debugging |
| Component tester | TC1 or similar LCR tester | $20-30 | For identifying unknown components |
| Magnification | Head-mounted magnifier or USB microscope | $15-50 | For inspecting small joints and reading component markings |
| Anti-static mat | Grounded ESD mat | $15-25 | When working with sensitive ICs (CMOS, MOSFET) |
| Fume extractor | Desktop activated carbon filter | $30-50 | For extended soldering sessions |
| Component storage | Multi-drawer cabinet or parts binder | $15-30 | When your parts collection grows beyond a few projects |
| Third hand / vise | Panavise 366 or similar | $40-80 | For holding odd-shaped workpieces and enclosures |

### 8.3 Organizing Components

As you buy components for projects, you'll accumulate a collection. Organize from day one -- time spent searching for parts is time not spent building:

**Resistors**: Sort by value. Options include:
- Labeled pages in a binder (small pockets or tape strips on paper, one value per section)
- Small compartment organizers (fishing tackle boxes, craft bead organizers) with values labeled
- Labeled bags in a drawer (lowest cost but slowest to search)

**Capacitors**: Separate by type first (ceramic, film, electrolytic), then by value. Electrolytics also need voltage rating notation.

**Semiconductors**: Separate by type (transistors, diodes, ICs, voltage regulators), then by part number. Keep them in anti-static bags or foam.

**Hardware**: Separate bins for: screws/nuts/washers, standoffs, 1/4" jacks, 3.5mm jacks, DC power jacks, switches (SPDT, 3PDT), knobs, pots, LED holders, enclosures.

**Incoming inventory**: When components arrive from a supplier, immediately sort them into your storage system. Don't leave them in the shipping bag "for later" -- that bag becomes the junk drawer that consumes an hour of searching when you need that one 4.7k resistor.

### 8.4 The Bench Environment

**Lighting**: Good lighting is essential for inspecting solder joints and reading component markings. A desk lamp with a daylight-temperature (5000-6500K) LED bulb positioned to illuminate your work area without creating shadows.

**Ventilation**: Solder flux fumes are irritating to the respiratory system with prolonged exposure. If possible, work near an open window or use a small desktop fume extractor with an activated carbon filter. At minimum, don't lean directly over the solder joint and breathe the rising smoke.

**Grounding**: If you're working with ESD-sensitive components (CMOS ICs, MOSFETs), use an anti-static wrist strap connected to the bench ground. For most through-hole audio work with BJTs and standard op-amps, ESD is a minor concern, but it's good practice.

**Power**: Have a power strip with a switch for your bench equipment. Being able to cut power to everything with one switch is both convenient and a safety feature.

---

## 9. Exercises: Measurement and Safety Practice

### 9.1 Multimeter Familiarization

If you have a multimeter available, practice these measurements:

1. **Continuity**: Test a length of hookup wire -- you should get a beep. Test an open space (probes not touching anything) -- no beep. Test a resistor -- it should beep if the resistance is below the threshold (varies by meter, usually 20-50Ω).

2. **Resistance**: Measure five different resistors. Read the color code (or marking) first, then measure. How close is the measured value to the marked value? For 1% metal film resistors, you should be within 1% of the marked value.

3. **DC voltage**: Measure a fresh 9V battery. It should read 9.2-9.6V (fresh alkaline batteries are above their nominal voltage). Measure an old battery -- how much has the voltage dropped?

4. **Diode test**: Find a silicon diode (1N4148, 1N4001, or similar). Measure in diode test mode:
   - Red probe on anode, black on cathode: should read ~0.5-0.7V
   - Reverse the probes: should read "OL" (open/over-limit)
   - If you have an LED, test it too: the LED may light dimly during the forward-bias test.

5. **Capacitance** (if your meter has this function): Measure a few capacitors. Start with large values (100 μF electrolytics are easy to measure). Note: wait for the reading to stabilize, especially for larger values.

### 9.2 Safety Checklist Exercise

Before your first high-voltage project (amplifier, tube circuit, mains-connected power supply), go through this checklist and ensure you can answer "yes" to every item:

- [ ] I have a multimeter rated for the voltages I'll encounter (CAT III for mains work)
- [ ] I have a discharge tool (1kΩ 10W resistor with insulated clip leads)
- [ ] I know the schematic and have identified all high-voltage nodes
- [ ] I have an isolation transformer (for live-chassis work) or a dim-bulb tester
- [ ] I understand the one-hand rule and will practice it
- [ ] Someone knows I'm working on high-voltage equipment and can reach emergency services
- [ ] My workspace is clear and well-lit
- [ ] I am not tired, rushed, or impaired
- [ ] I have reviewed the capacitor discharge procedure and will follow it every time
- [ ] I know the location of the circuit breaker for my bench outlet

If any answer is "no," address it before proceeding.

### 9.3 Soldering Practice

Before soldering your first real project, practice on scrap:

1. **Get a scrap PCB**: Old electronics at thrift stores, dead computer boards, or purpose-built practice PCBs (available cheaply online as "soldering practice kits").

2. **Practice through-hole soldering**: Solder 10 resistors. Inspect each joint. Rate them: good (shiny, concave, fully wetted), acceptable (slightly imperfect but electrically sound), or bad (cold, blobby, bridged). If more than 2 out of 10 are bad, adjust your technique and do another 10.

3. **Practice desoldering**: Remove the resistors you just soldered using solder wick. Then re-solder them. This builds the muscle memory for rework.

4. **Practice IC socket soldering**: Solder an 8-pin DIP socket. Verify there are no solder bridges between adjacent pins using continuity test. If there are bridges, remove them with solder wick.

5. **Practice drag soldering**: Solder a header strip. Clean up any bridges. This skill saves time on builds with many headers.

### 9.4 Build Your First Test Tool

Before starting any project from Part 1 onward, build these two simple tools:

**Tool 1: Audio Probe** (from Chapter 3)
- 10 nF film capacitor
- 10kΩ resistor
- 1/4" mono jack
- Probe tip (stiff wire)
- Ground clip lead

**Tool 2: Test Cable Set**
- 1/4" TS to 1/4" TS cable (for connecting signal generators to pedal inputs)
- 1/4" TS to 3.5mm cable (for connecting phones/computers to circuits)
- Alligator clip leads (at least 4, in different colors, for power supply connections)

These tools cost under $15 total and will be used constantly throughout the guide.

---

## 10. Quick Reference: Essential Numbers

### Voltage Levels

| Signal | Level |
|---|---|
| Microphone (dynamic) | 1-10 mV |
| Guitar pickup | 100-500 mV |
| Consumer line (-10 dBV) | 316 mV RMS |
| Pro line (+4 dBu) | 1.228 V RMS |
| Headphone amp | 1-5 V RMS |
| Speaker amp (100W/8Ω) | 28.3 V RMS |

### Power and Battery Life

| Battery Type | Capacity | 20 mA Draw | 100 mA Draw |
|---|---|---|---|
| 9V alkaline | ~500 mAh | 25 hours | 5 hours |
| 9V lithium | ~1200 mAh | 60 hours | 12 hours |
| AA alkaline (×6 = 9V) | ~2800 mAh | 140 hours | 28 hours |

### Standard Resistor Values (E12 Series)

Per decade: 10, 12, 15, 18, 22, 27, 33, 39, 47, 56, 68, 82

Common audio values: 100Ω, 470Ω, 1kΩ, 4.7kΩ, 10kΩ, 47kΩ, 100kΩ, 470kΩ, 1MΩ

### Standard Capacitor Values

Signal path (film): 100pF, 220pF, 470pF, 1nF, 2.2nF, 4.7nF, 10nF, 22nF, 47nF, 100nF, 220nF, 470nF, 1μF

Power supply (electrolytic): 1μF, 4.7μF, 10μF, 47μF, 100μF, 220μF, 470μF, 1000μF

### Safety Thresholds

| Parameter | Value |
|---|---|
| Lethal current (across chest) | As low as 30 mA |
| "Generally safe" voltage (dry skin) | Below ~50V DC |
| Capacitor discharge time (5τ) | 5 × R × C |
| Typical tube amp B+ | 300-450V DC |
| Mains voltage (North America) | 120V AC RMS (170V peak) |
| Mains voltage (Europe/Asia) | 230-240V AC RMS (325-340V peak) |

---

## Key Takeaways

1. **Good soldering technique** is the physical foundation of reliable builds. Heat both pad and lead simultaneously, apply solder to the junction, inspect every joint.
2. **Desoldering** is a core skill. Solder wick for bridges and small corrections; desoldering pump for through-hole removal. The Engineer SS-02 is the standard recommendation.
3. **The multimeter** is your primary measurement tool. Continuity, DC voltage, and resistance cover 80% of debugging needs. Invest in a True RMS meter.
4. **An oscilloscope** lets you see waveforms and is essential for amplifier design. The Rigol DS1054Z is the standard budget choice.
5. **Mains voltage (120/240V AC) is lethal.** Never work on live mains circuits without proper training, tools (isolation transformer, insulated probes), and the one-hand rule.
6. **Capacitors store lethal charge** after power is removed -- sometimes for hours. ALWAYS discharge filter capacitors before touching anything inside an amplifier or power supply.
7. **Tube amplifiers operate at 250-600V DC.** Treat them with the utmost respect: discharge caps, one-hand rule, know the schematic, have someone nearby.
8. **Build safe habits at 9V** so they're automatic at 400V. The habits cost nothing now and save everything later.
9. **Organize your workspace and components from day one.** A clean, well-lit bench with organized parts makes building faster and safer.

---

*Previous: [Chapter 3 -- Simulation and Analysis Tools](03-simulation-analysis-tools.md)*
*Next: [Part 1 -- Guitar Pedals](../part-1-guitar-pedals/) (coming soon)*
