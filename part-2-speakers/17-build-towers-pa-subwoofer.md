<!--
  CHAPTER: 17
  TITLE: Build: Floor-Standing Towers, PA Speakers, and Subwoofer
  PART: 2 — Speakers
  PREREQS: Chapter 16
  KEY_TOPICS: floor-standing tower, 2.5-way, 3-way, PA speaker, horn-loaded compression driver, subwoofer, high-excursion driver, system integration
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 17: Build: Floor-Standing Towers, PA Speakers, and Subwoofer

> **Part 2 — Speakers** | Prerequisites: Chapter 16 | Difficulty: Inter→Adv

You built your bookshelf speakers in Chapter 16. They sound great for a nearfield listening setup in a small room. But now you want more. More bass for that floor-standing "anchor" in your living room system. More raw SPL for an outdoor party that does not sound like a phone speaker on a picnic table. And more low-end for the subsonic rumble that no bookshelf speaker can touch.

This chapter covers three builds, each designed for a different purpose and each introducing new concepts beyond the bookshelf build. Build A is a floor-standing tower for home hi-fi -- deeper bass, more authority, and a more complex crossover. Build B is a PA speaker for parties and events -- high SPL, durability, and a horn-loaded compression driver for the first time. Build C is a dedicated subwoofer -- the one driver that only does one thing but does it with authority that shakes the walls.

Each build stands alone, but together with the bookshelf speakers from Chapter 16, they form the components of a complete sound system: towers for critical listening, PA for parties, and a subwoofer for either application. The system design chapter in Part 6 will tie them all together.

### In This Chapter
- BUILD A: Floor-standing tower (2.5-way or 3-way, home hi-fi)
- BUILD B: PA speaker (horn-loaded compression driver + direct-radiator woofer)
- BUILD C: Subwoofer (high-excursion driver, sealed or ported)
- For each: design rationale, driver selection, crossover design, cabinet, construction notes

### Related Chapters
- [Ch 16: Build: Bookshelf Speakers](16-build-bookshelf-speakers.md) -- the foundational build this chapter extends
- [Ch 14: Cabinet Design](14-cabinet-design.md) -- enclosure theory for all three builds
- [Ch 13: Crossover Design](13-crossover-design.md) -- crossover theory for towers and PA
- [Ch 18: Amplifier Matching](../part-3-amplifiers/18-amplifier-matching.md) -- amplifier requirements for each build
- [Ch 27: Active Crossovers and DSP](../part-4-digital-audio/27-active-crossovers-dsp.md) -- DSP integration for subwoofer and PA
- [Ch 35: Power Amplifiers](../part-3-amplifiers/35-power-amplifiers.md) -- amplifier builds for these speakers
- [Ch 38: System Design](../part-6-system-design/38-system-design.md) -- combining these builds into complete systems
- [Ch 39: Live Sound](../part-6-system-design/39-live-sound.md) -- deploying PA speakers at events

---

# BUILD A: Floor-Standing Tower

## A.1 Design Goals

A floor-standing tower speaker for a medium to large living room (25-50 square meters). The tower provides:

- **Deeper bass than the bookshelf** -- target F3 of 35-40 Hz (versus the bookshelf's 75-80 Hz)
- **Higher maximum output** -- bigger woofer, more cone area, more excursion headroom
- **A more authoritative sound** that fills a larger room without strain
- **Floor placement** on spikes or feet (no stands needed)

### A.1.1 Tower Topologies: 2-Way, 2.5-Way, and 3-Way

The bookshelf was a simple 2-way (woofer + tweeter). Towers often use more complex arrangements:

**2-way tower**: Same topology as the bookshelf, just with a larger woofer (or dual woofers) in a bigger cabinet. Simple but limited -- a single large woofer may have breakup issues that make crossing to a tweeter difficult.

**2.5-way tower**: Uses two woofers of the same type, but one handles bass + midrange (same as a 2-way) while the second handles only bass (rolled off below the primary woofer-tweeter crossover frequency). The "half-way" woofer augments bass output and extension without complicating the midrange. This is extremely popular because it gives the bass extension of two woofers without requiring a dedicated midrange driver.

```
2.5-way signal flow:
                          ┌── HP + LP → Upper Woofer (bass + midrange)
Amplifier signal ─── Crossover ─── LP → Lower Woofer (bass only)
                          └── HP → Tweeter
```

**3-way tower**: Uses a dedicated woofer (bass), midrange (mid), and tweeter (treble). The most complex topology but potentially the best performance because each driver operates in its optimal range. Requires two crossover frequencies and more complex crossover networks.

### A.1.2 Our Choice: 2.5-Way Tower

We will build a 2.5-way because:
- It gives deep bass extension (two woofers share the load)
- The midrange is handled by a single driver with no complicated mid crossover
- The crossover is manageable (one main crossover point woofer-to-tweeter, plus a low-pass filter on the bass-only woofer)
- It uses two identical woofers (cost-effective, simpler parts ordering)

---

## A.2 Driver Selection

### A.2.1 Woofer: SB Acoustics SB17NRXC35-4 (6.5")

We will use two of these per speaker:

```
Impedance: 4 ohm | Re: 3.2 ohm
Fs: 37 Hz | Qts: 0.33 | Qes: 0.37 | Qms: 3.05
Vas: 18.5 L | Xmax: 6 mm
BL: 6.7 Tm | Le: 0.35 mH
Sd: 132 cm^2 | Sensitivity: 86 dB (2.83V/m)
```

Why this driver: The low Fs (37 Hz) enables deep bass. The low Qts (0.33) is ideal for a ported alignment, which we will use for the tower to maximize bass extension. Two drivers double the cone area (264 cm^2 total), providing 6 dB more bass capability than a single driver. The smooth frequency response extends well past 3 kHz, giving us crossover flexibility.

### A.2.2 Tweeter: SB Acoustics SB26STAC-C000-4

Same tweeter as the bookshelf build:

```
Impedance: 4 ohm | Fs: 500 Hz
Sensitivity: 92 dB (2.83V/m)
```

We know this tweeter works well. Reusing it simplifies the design and lets us focus the new complexity on the 2.5-way topology.

### A.2.3 Impedance Considerations

Two 4-ohm woofers in parallel give a 2-ohm load -- too low for most amplifiers. Instead, we wire them in **series** for an 8-ohm combined woofer impedance. This is easier on the amplifier and gives us a nominal 8-ohm system when combined with the 4-ohm tweeter circuit.

However, the crossover must be designed for the series-wired woofer pair's combined impedance, not the individual driver impedance. VituixCAD handles this correctly when you define the driver pair as a series connection.

---

## A.3 Enclosure Design

### A.3.1 Ported Alignment

With Qts = 0.33, this driver strongly favors a ported enclosure (EBP = Fs/Qes = 37/0.37 = 100, suggesting ported). In WinISD:

**For two drivers in a single ported enclosure:**
- Box volume: approximately 25-30 liters net
- Port tuning: approximately 32-35 Hz
- F3: approximately 32-36 Hz -- dramatically deeper than the bookshelf
- Vent: a slot port or large-diameter (60-70mm) round port, approximately 25-30 cm long

### A.3.2 Cabinet Dimensions

A tower cabinet is tall and narrow. Typical proportions:

```
External dimensions: 220mm W x 900mm H x 300mm D
Material: 18mm MDF

Internal dimensions: 184mm x 864mm x 264mm
Gross internal volume: 184 * 864 * 264 = 41,941,248 mm^3 ≈ 42 liters

Subtract bracing (4 braces): 1.5 liters
Subtract 2 woofer displacements: 0.8 liters
Subtract 1 tweeter displacement: 0.05 liters
Subtract port volume: 0.8 liters
Subtract crossover: 0.3 liters

Net volume: approximately 38.5 liters
```

This is larger than we need for a 25-30 liter ported design. We can either: (a) reduce the depth to 260mm for approximately 32 liters net, which is closer to optimal, or (b) use the extra volume to tune the port slightly lower for maximum extension. Simulate both options in WinISD and pick the one with the best balance of extension, response flatness, and port velocity.

### A.3.3 Driver Layout

From top to bottom:
1. Tweeter (near the top, ear level when seated)
2. Upper woofer (immediately below the tweeter -- this is the one that handles bass + midrange)
3. Lower woofer (near the bottom -- bass only)
4. Port opening (at the bottom of the front baffle or on the bottom panel)

The upper woofer should be as close to the tweeter as physically possible to minimize inter-driver interference at the crossover frequency. The lower woofer can be further away because it only operates at low frequencies where wavelengths are long and interference effects are minimal.

---

## A.4 Crossover Design

### A.4.1 The 2.5-Way Topology

The crossover has three sections:

1. **Low-pass for the lower woofer**: A low-pass filter that rolls off the lower woofer above approximately 300-500 Hz. This prevents it from contributing to the midrange (where its output would interfere with the upper woofer). This is typically a simple 2nd-order low-pass at around 400 Hz.

2. **Low-pass for the upper woofer**: Same as a standard 2-way crossover -- rolls off the upper woofer below the tweeter's range. Crossover frequency around 2,500 Hz, using LR4 or LR2.

3. **High-pass for the tweeter**: Standard high-pass filter. Crossover frequency at 2,500 Hz, matching the upper woofer's low-pass.

Plus impedance compensation (Zobel networks) on both woofers and an L-pad on the tweeter.

### A.4.2 Design Process in VituixCAD

1. Import FRD/ZMA for the SB17NRXC35-4 (used for both woofers)
2. Import FRD/ZMA for the SB26STAC-C000-4
3. Define the tall, narrow baffle with the driver positions
4. Build the crossover network: LP for lower woofer, LP for upper woofer, HP for tweeter
5. Optimize the upper woofer/tweeter crossover for flat on-axis response
6. Separately optimize the lower woofer's LP filter for smooth bass integration
7. Check the combined response, impedance, and off-axis behavior

The lower woofer's filter is less critical for the on-axis response (bass is omnidirectional) but must be designed carefully to avoid a peak in the 200-500 Hz range where the two woofers overlap.

### A.4.3 Approximate Component Values

These will vary based on optimization, but as a starting point:

```
Lower woofer low-pass (2nd order, 400 Hz, 8 ohm series pair):
  L_low: 4.5 mH (iron-core inductor -- air-core would be enormous)
  C_low: 33 uF

Upper woofer low-pass (4th order LR4, 2500 Hz, 8 ohm series pair):
  L1: 0.72 mH    C1: 5.6 uF
  L2: 0.72 mH    C2: 5.6 uF

Tweeter high-pass (4th order LR4, 2500 Hz, 4 ohm):
  C3: 11.2 uF    L3: 0.36 mH
  C4: 11.2 uF    L4: 0.36 mH

Zobel on each woofer, L-pad on tweeter.
```

Total crossover component count per speaker: approximately 18-22 components. This is a significant crossover -- budget $50-70 per speaker for crossover parts.

---

## A.5 Construction Notes

### A.5.1 Cabinet Construction

The tall, narrow shape of a tower makes it prone to side-panel resonances. Internal bracing is critical:

- Use **four horizontal shelf braces** at approximately equal spacing along the height (one every 18-20 cm)
- Each brace should have a large window cutout (60-70% open area) to allow sound to pass through while stiffening the panels
- Bond the braces firmly to all four cabinet walls with glue

### A.5.2 Floor Coupling

Tower speakers interact with the floor. Options for coupling:
- **Spikes**: Steel spikes that penetrate carpet, coupling the speaker rigidly to the floor. Reduces bass muddiness on carpeted floors.
- **Isolation pads**: Elastomer pads that decouple the speaker from the floor. Reduces floor vibration.
- **Plinths**: A separate base wider than the cabinet that provides stability and houses the spikes. Useful for narrow cabinets that might tip over.

For a 220mm-wide tower, a plinth is recommended for stability. Make it from a separate piece of MDF, 300mm x 300mm x 18mm, attached to the bottom of the cabinet.

### A.5.3 Wiring

The two woofers are wired in series: the upper woofer's negative terminal connects to the lower woofer's positive terminal. The remaining two terminals (upper woofer positive, lower woofer negative) connect to the crossover. Verify polarity carefully -- both woofers should move outward together when a positive voltage is applied.

---

## A.6 Expected Performance

| Spec | Bookshelf (Ch 16) | Tower |
|------|-------------------|-------|
| F3 (bass extension) | ~80 Hz | ~35 Hz |
| Sensitivity | ~84 dB | ~87 dB (two woofers + larger box) |
| Max SPL (clean) | ~95 dB | ~102 dB |
| Impedance | 4 ohm nominal | 4-8 ohm nominal |
| Size | 206 x 306 x 200 mm | 220 x 900 x 300 mm |
| Weight | ~5 kg each | ~18-22 kg each |

The tower plays a full octave deeper in the bass and can play 7 dB louder before distortion. In a medium-to-large room, this is the difference between "nice" and "impressive."

---

# BUILD B: PA Speaker

## B.1 Design Goals

A **PA speaker** for outdoor parties, events, and rehearsal spaces. The design goals are completely different from home hi-fi:

- **Maximum SPL**: Must be loud enough for 50-100 people outdoors (target: 120+ dB peak at 1 meter)
- **Efficiency**: High sensitivity so we do not need a nuclear power plant worth of amplifiers
- **Durability**: Must survive transportation, outdoor weather, and being bumped by drunk partygoers
- **Controlled directivity**: Sound goes to the audience, not to the ceiling and walls
- **Power handling**: Must handle sustained high power without thermal failure

These goals lead to fundamentally different hardware choices than home hi-fi.

---

## B.2 Driver Selection

### B.2.1 Woofer: Eminence Kappa-15LFA (15" PA woofer)

```
Impedance: 8 ohm | Re: 5.36 ohm
Fs: 42 Hz | Qts: 0.39 | Qes: 0.43 | Qms: 4.85
Vas: 154 L | Xmax: 5.2 mm (one-way)
BL: 17.6 Tm | Le: 1.47 mH
Sd: 855 cm^2 | Sensitivity: 99 dB (2.83V/m)
Power handling: 600W continuous
```

Why this driver: Look at that sensitivity -- 99 dB. That is 13-15 dB more than our home woofers. With 100W of amplifier power, this driver produces 119 dB at 1 meter. That is LOUD.

The 15" cone provides massive Sd (855 cm^2 versus 132 cm^2 for the 6.5" home woofer -- over 6x more cone area). The high BL (17.6 Tm) is the reason for the high sensitivity. The 600W power handling means we can push it hard for sustained periods.

The trade-off: Xmax is only 5.2mm, which is modest for a 15" driver. This is a high-efficiency design, not a high-excursion one. It works best above 60-80 Hz in a ported enclosure and should not be asked to produce deep sub-bass.

### B.2.2 High-Frequency: Eminence PSD-2002S-8 (Compression Driver) + Horn

For the first time in our builds, we use a compression driver on a horn instead of a dome tweeter:

```
Compression driver: Eminence PSD-2002S-8
Impedance: 8 ohm
Frequency range: 1,200 Hz - 20,000 Hz
Sensitivity: 107 dB (1W/1m)
Power handling: 80W continuous
Diaphragm: 1.4" titanium
```

```
Horn: A 90x60 degree constant-directivity horn
Throat: 1.4" (matching the compression driver)
Frequency range: 1,200 Hz+
Coverage: 90 degrees horizontal x 60 degrees vertical
```

Why a compression driver and horn: At 107 dB sensitivity, this combination is 8 dB more sensitive than even the beefy woofer. In a PA system, you cannot use a dome tweeter -- at the power levels involved, a dome tweeter would melt in seconds. Compression drivers are built for this environment with large voice coils, powerful magnets, and diaphragms designed to handle extreme thermal loads.

The horn provides two critical functions:
1. **Efficiency**: The horn's acoustic impedance matching gives us the extreme sensitivity
2. **Controlled directivity**: The 90x60 degree pattern means the sound goes to the audience, not to the ceiling or behind the speakers. This reduces feedback risk (when using microphones) and reduces wasted energy.

### B.2.3 Horn Selection

Choosing or building a horn is its own area of expertise. For this build, we recommend purchasing a commercial horn:

- **90 x 60 degree pattern**: 90 degrees horizontal covers a wide audience area. 60 degrees vertical is narrower, focusing the sound downward toward the audience (if the speaker is elevated on a stand).
- **1.4" throat**: Matches our compression driver
- **Cutoff around 1,200 Hz**: This determines the lowest frequency the horn can effectively radiate

Horn options include the B&C ME90 or Selenium HM17-25 (both available from parts distributors). You can also build a horn from MDF or fiberglass, though this requires careful CNC work or a mold.

---

## B.3 Enclosure Design

### B.3.1 A Ported Woofer Cabinet

The Kappa-15LFA in WinISD:

```
Ported, ~80 liters net, tuned to 50 Hz:
- F3: approximately 50 Hz
- F6: approximately 42 Hz
- Port: slot port, approximately 4" x 14" opening, 8" deep
- Max SPL: 125+ dB with 600W input (before excursion limiting)
```

PA speakers prioritize output over extension. We tune the port at 50 Hz (not 35 Hz like the home tower) because:
- The woofer's modest Xmax means it runs out of excursion at low frequencies
- Most music content and DJ material has little energy below 50 Hz
- If you need sub-bass, add a subwoofer (Build C)

### B.3.2 Cabinet Dimensions

```
External dimensions: 480mm W x 680mm H x 430mm D (approximately 19" x 27" x 17")
Material: 18mm Baltic birch plywood (stronger, lighter than MDF for a portable cabinet)

Internal volume (approximate): 80-85 liters
```

### B.3.3 Horn Mounting

The horn mounts in the top section of the cabinet, above the woofer. Cut the front baffle to accommodate the horn's rectangular face (dimensions per the horn manufacturer's spec). The compression driver bolts to the back of the horn from inside the cabinet.

Important: the horn opening should be above the woofer and oriented so the 90-degree horizontal axis is parallel to the ground (widest coverage horizontally, narrower coverage vertically).

---

## B.4 Crossover Design

### B.4.1 Crossover Frequency

The crossover frequency for a PA speaker with a compression driver is typically **1,200-1,500 Hz**, limited by the horn's low-frequency cutoff. We will cross at 1,400 Hz.

This is lower than our home speakers' 2,500 Hz crossover. The 15" woofer is beaming severely at 1,400 Hz (wavelength = 245mm, woofer diameter = 380mm -- the woofer is much larger than the wavelength). This is acceptable because:
1. In a PA application, the audience is typically far from the speakers, so the narrow woofer pattern at 1,400 Hz is less of an issue
2. The horn takes over directivity control at and above the crossover frequency
3. Crossing any higher would require the compression driver to play below its comfortable range

### B.4.2 Passive or Active?

For PA speakers, **active crossovers** are strongly recommended:
- The power levels involved make passive crossover components enormous (a series inductor for a 600W, 8-ohm woofer at 1,400 Hz would need to handle significant current)
- Active crossovers allow you to adjust the crossover and EQ to the venue
- Most PA amplifiers have DSP built in, or you can use a standalone DSP processor (miniDSP, Behringer DCX2496, dbx DriveRack)

For a passive design (simpler, no DSP needed):

```
Woofer low-pass (2nd order, 1400 Hz, 8 ohm):
  L: 1.8 mH (iron-core, high-current rated, low DCR)
  C: 5.6 uF (film capacitor, high voltage rated)

Compression driver high-pass (2nd order, 1400 Hz, 8 ohm):
  C: 14 uF (film capacitor)
  L: 0.91 mH (air-core)

L-pad on compression driver: approximately 8-10 dB attenuation
  R1: 5.6 ohm, 50W wire-wound
  R2: 12 ohm, 50W wire-wound
```

Note the enormous power ratings on the L-pad resistors. At 600W program power, the L-pad dissipates significant heat. Use high-power wire-wound resistors mounted on aluminum heatsinks, or better yet, use an active crossover that eliminates the L-pad entirely.

### B.4.3 Protection Circuits

PA speakers need **protection circuits** that home speakers do not:
- **PTC (Positive Temperature Coefficient) thermistor** in series with the compression driver: If the driver overheats, the PTC's resistance increases, reducing power delivery. This protects against thermal failure during sustained high-power use.
- **Light bulb limiter**: A 75-100W incandescent light bulb in series with the compression driver acts as a self-resetting fuse. At low power, the filament has low resistance. At high power, it heats up, increases resistance, and limits current. Old-school but effective.

---

## B.5 Ruggedized Construction

PA cabinets must survive transportation, stacking, rain, and abuse. Key differences from home construction:

### B.5.1 Material

18mm Baltic birch plywood is the standard for PA cabinets. It is stronger than MDF, lighter, and resists damage better. The edges do not crumble like MDF when bumped.

### B.5.2 Hardware

- **Steel or aluminum corner protectors**: Metal corners on all eight cabinet corners prevent damage during transport.
- **Bar handles**: Recessed handles on two sides for carrying. A 15" PA speaker weighs 20-30 kg -- you NEED handles.
- **Pole mount socket**: A 35mm socket on the bottom of the cabinet (or top, for underslung mounting) that accepts a standard speaker stand pole. This allows the speaker to be elevated above the audience for better coverage.
- **Rubber feet**: Large rubber feet on the bottom for floor placement.
- **Speaker grill**: A steel or aluminum grill over the woofer and horn to protect them from impacts, fingers, and beer. Use perforated steel sheet or wire mesh.
- **Speakon connectors**: The standard connector for professional audio. Use NL4 (4-pole) Speakon jacks, not binding posts (which can short if they come loose).

### B.5.3 Finishing

PA cabinets are typically finished with one of:
- **Carpet (Ozite)**: Traditional PA finish. Cheap, durable, hides scratches. Ugly but effective. Applied with spray adhesive.
- **Textured spray coating**: Truck bed liner or Duratex creates a tough, textured surface. More attractive than carpet. Apply with a roller or spray gun.
- **Polyurea coating**: The professional standard. Sprayed on, creates an extremely durable rubberized coating. Requires spray equipment.

### B.5.4 Internal Construction

- **Heavy bracing**: At least two full-width braces connecting front and back panels
- **No damping material**: PA cabinets do not typically use internal damping -- every bit of energy needs to reach the audience, and the high SPL levels inside the cabinet would compress lightweight damping material anyway
- **Sealed sub-enclosure for the horn**: The compression driver's rear chamber should be sealed from the woofer's internal volume to prevent the woofer's back wave from reaching the compression driver

---

## B.6 Expected Performance

| Spec | Value |
|------|-------|
| Frequency range | 50 Hz - 20 kHz |
| Sensitivity | 99 dB (woofer) / 107 dB (horn), 99 dB system |
| Max SPL (continuous) | 127 dB at 1 meter |
| Max SPL (peak) | 130+ dB at 1 meter |
| Coverage | 90 x 60 degrees |
| Power handling | 600W continuous / 1200W program |
| Weight | ~25-30 kg |
| Impedance | 8 ohms nominal |

With a pair of these and a 500W per channel amplifier, you can comfortably cover an outdoor party of 50-100 people. Add subwoofers (Build C) for the low end and you have a complete PA system.

---

# BUILD C: Subwoofer

## C.1 Design Goals

A dedicated subwoofer for frequencies below 80-120 Hz. The subwoofer complements either the tower speakers (filling in below 35 Hz) or the bookshelf speakers (filling in below 80 Hz). It can also serve as the bass foundation for the PA system.

Design goals:
- **Deep bass extension**: Target F3 of 25-30 Hz (below which there is very little musical content)
- **High output**: Enough SPL to pressurize a medium-to-large room or supplement a PA system
- **Clean bass**: Low distortion down to the extension limit
- **Flexible integration**: Works with passive or active crossovers

### C.1.1 Sealed vs. Ported for a Subwoofer

The sealed-vs-ported debate is particularly relevant for subwoofers:

**Sealed subwoofer**:
- Tighter, more controlled bass
- Gradual 12 dB/octave rolloff (room gain compensates well)
- Smaller cabinet
- Better transient response
- Maximum excursion occurs at the lowest frequencies (where content is rare)
- **Best for music listening**

**Ported subwoofer**:
- Deeper extension for a given driver and box size
- Higher maximum output near the tuning frequency
- 24 dB/octave rolloff below tuning -- drops off rapidly
- Cone unloading below tuning frequency (need subsonic protection)
- More group delay
- **Best for home theater and maximum output**

For this build, we will present **both options** and let you choose based on your application.

---

## C.2 Driver Selection

### C.2.1 The Priority: Excursion

For subwoofers, **Xmax is the most important parameter.** At low frequencies, the driver must move huge volumes of air, and the only way to do that with a given cone area is to move the cone a long distance. A subwoofer driver with 3mm Xmax is useless; you need 10-20mm or more.

### C.2.2 Suggested Driver: Dayton Audio RSS315HF-4 (12" subwoofer driver)

```
Impedance: 4 ohm | Re: 3.3 ohm
Fs: 24 Hz | Qts: 0.50 | Qes: 0.54 | Qms: 7.69
Vas: 82 L | Xmax: 13 mm (one-way)
BL: 13.5 Tm | Le: 1.25 mH
Sd: 507 cm^2 | Sensitivity: 87 dB (2.83V/m)
Power handling: 350W RMS
```

Why this driver: Xmax of 13mm is excellent -- this is a true long-excursion driver designed for subwoofer duty. The Fs of 24 Hz means it naturally resonates at a very low frequency. The Qts of 0.50 works in either sealed or ported. The sensitivity of 87 dB is typical for a subwoofer driver (you sacrifice sensitivity for excursion capability -- there is always a trade-off).

Alternative option: the Dayton Audio UM12-22 or UM15-22 (15" version) if you want even more output.

---

## C.3 Enclosure Design

### C.3.1 Option 1: Sealed Subwoofer

In WinISD with the RSS315HF-4:

```
Sealed, 42 liters net:
- Qtc: 0.707 (Butterworth)
- Fc: 38 Hz
- F3: 38 Hz (Butterworth F3 = Fc)
- F6: 28 Hz
```

With room gain in a typical room, the effective F3 drops to approximately 28-30 Hz. This is excellent subwoofer performance.

**Cabinet dimensions (sealed)**:
```
External: 380mm W x 450mm H x 400mm D (15" x 18" x 16")
Material: 18mm MDF (double-thick front baffle: 36mm)
Internal volume: approximately 47 liters gross (42 liters net after displacement)
```

### C.3.2 Option 2: Ported Subwoofer

```
Ported, 70 liters net, tuned to 22 Hz:
- F3: approximately 22 Hz -- extremely deep
- F6: approximately 19 Hz
- Port: slot port, 4" x 12", approximately 18" long
```

The ported option extends a full octave deeper than sealed but requires a much larger cabinet (about 70 vs 42 liters) and has the steep 24 dB/octave rolloff below 22 Hz plus the excursion concern below tuning.

**Cabinet dimensions (ported)**:
```
External: 420mm W x 550mm H x 450mm D (16.5" x 21.7" x 17.7")
Material: 18mm MDF (double-thick front baffle: 36mm)
```

### C.3.3 Construction Notes

Subwoofer cabinets experience enormous internal pressures. Construction must be robust:

- **Double-thick front baffle**: Two layers of 18mm MDF (36mm total) for the panel that holds the driver. The large woofer cutout weakens the baffle, and the high excursion forces create significant vibration.
- **Heavy bracing**: At least two internal braces connecting front and back panels. Shelf braces are ideal.
- **Generous damping (sealed only)**: Fill with polyfill to about 60% density. This also increases the effective volume slightly.
- **Large port (ported only)**: Use a slot port with generous cross-sectional area. The ported subwoofer moves enormous volumes of air through the port, and undersized ports create unacceptable noise levels.
- **Gasket the driver carefully**: The high excursion and internal pressure will find any air leak. Use a proper foam gasket and seal with silicone.

---

## C.4 Low-Pass Filtering and Integration

### C.4.1 The Low-Pass Filter

The subwoofer needs a low-pass filter to roll off its output above the crossover frequency (typically 60-120 Hz, depending on the main speakers). Options:

**Option 1: Active crossover in the subwoofer's plate amplifier**
Most subwoofer plate amplifiers (a dedicated amplifier built into the subwoofer cabinet) include a variable low-pass filter, typically adjustable from 40-150 Hz. This is the simplest and most common approach.

Suggested plate amplifier: Dayton Audio SPA250DSP (250W with DSP, including variable low-pass filter, phase adjustment, and parametric EQ). Approximately $170. Mounts in a cutout in the back panel.

**Option 2: External active crossover**
Use a miniDSP 2x4 HD or similar DSP unit between the preamp and amplifiers. This provides precise crossover control and allows you to EQ the subwoofer and main speakers independently.

**Option 3: Passive low-pass filter**
A simple passive low-pass filter (large inductor in series with the woofer) can work but is less flexible and introduces losses. At subwoofer power levels, the inductor must be massive (10+ mH for 80 Hz, 4-ohm load). This is generally not recommended.

### C.4.2 Integration with Main Speakers

Setting up the subwoofer with the main speakers is a topic in itself (covered fully in Part 6, system design). The key parameters to adjust:

- **Crossover frequency**: Set the low-pass frequency to overlap with the main speakers' natural rolloff. For the bookshelf speakers (F3 ≈ 80 Hz), set the subwoofer's low-pass to 80 Hz. For the towers (F3 ≈ 35 Hz), set it to 40-50 Hz.
- **Phase**: Adjust the subwoofer's phase (0 or 180 degrees, or continuously variable on DSP-equipped subs) so that the subwoofer and main speakers reinforce at the crossover frequency rather than cancel.
- **Level**: Adjust the subwoofer level so bass sounds natural and integrated, not boomy or disconnected.
- **Placement**: Subwoofer placement in the room dramatically affects the bass response due to room modes. Corner placement gives maximum output but can excite modes. Try multiple positions and measure with REW to find the smoothest response at the listening position.

---

## C.5 Expected Performance

| Spec | Sealed | Ported |
|------|--------|--------|
| F3 | ~38 Hz (28 Hz with room gain) | ~22 Hz |
| Max SPL (at F3) | ~108 dB | ~112 dB |
| Cabinet volume | 42 L net | 70 L net |
| Group delay at F3 | ~12 ms | ~25 ms |
| Best for | Music, accuracy | Home theater, maximum extension |

Either option produces a subwoofer that will handle any music genre and most home theater content. The ported version reaches earthquake territory (22 Hz is felt more than heard) while the sealed version prioritizes musical accuracy.

---

# Putting It All Together

## System Configurations

These three builds, combined with the bookshelf speakers from Chapter 16, give you a complete toolkit:

### Configuration 1: Home Hi-Fi
- **Tower speakers (Build A)** + **Subwoofer (Build C, sealed)**
- The towers handle 35-20,000 Hz. The subwoofer extends to 25 Hz.
- Amplifier: 100-200W per channel for the towers. Plate amp in the sub.
- For serious music listening in a medium to large room.

### Configuration 2: Small Room / Desktop
- **Bookshelf speakers (Chapter 16)** + **Subwoofer (Build C, sealed)**
- The bookshelves handle 80-20,000 Hz. The subwoofer handles 25-80 Hz.
- Amplifier: 50-100W per channel for the bookshelves. Plate amp in the sub.
- For bedroom, office, or desktop listening.

### Configuration 3: Party / Event PA
- **PA speakers (Build B)** on stands + **Subwoofer (Build C, ported)**
- The PA speakers handle 50-20,000 Hz. The subwoofer handles 22-50 Hz.
- Amplifier: 500W per channel for the PA speakers. Separate power amp for the sub.
- Active crossover (miniDSP or similar) for system management.
- For outdoor parties of 50-150 people.

### Configuration 4: Full Range Home + Party
- **All of the above.** Tower speakers in the living room for daily listening. Bookshelf speakers in the bedroom. PA speakers and sub in storage for events. Subwoofer serves double duty.

---

## Bill of Materials (All Three Builds)

### Build A: Tower (per pair)
| Component | Cost |
|-----------|------|
| SB17NRXC35-4 woofers (4 total) | $180-200 |
| SB26STAC-C000-4 tweeters (2 total) | $50-60 |
| Crossover components | $100-140 |
| 18mm MDF panels | $40-60 |
| Finishing materials | $20-30 |
| Hardware (spikes, binding posts, etc.) | $20-30 |
| **Total** | **$410-520** |

### Build B: PA Speaker (per pair)
| Component | Cost |
|-----------|------|
| Eminence Kappa-15LFA woofers (2) | $180-220 |
| Eminence PSD-2002S-8 comp. drivers (2) | $100-120 |
| Horns (2) | $80-120 |
| Crossover components (passive) | $60-80 |
| 18mm Baltic birch plywood | $60-80 |
| Hardware (corners, handles, pole mount, Speakon, grill) | $60-80 |
| Finishing (Duratex or carpet) | $30-40 |
| **Total** | **$570-740** |

### Build C: Subwoofer (each)
| Component | Cost |
|-----------|------|
| Dayton Audio RSS315HF-4 driver | $100-120 |
| SPA250DSP plate amplifier | $160-180 |
| 18mm MDF panels | $30-40 |
| Damping material | $10-15 |
| Finishing materials | $10-20 |
| **Total** | **$310-375** |

---

## Chapter Summary and What Comes Next

In this chapter, you built three speakers that cover the entire range of audio reproduction:

**Build A (Tower)** taught you:
- 2.5-way crossover topology and woofer-in-series wiring
- Ported enclosure optimization for maximum bass extension
- Floor-standing cabinet construction with heavy bracing
- The performance leap from bookshelf to tower

**Build B (PA Speaker)** taught you:
- Compression driver and horn basics: efficiency and controlled directivity
- PA-specific design priorities (SPL over flatness, durability over aesthetics)
- Ruggedized construction with plywood, corners, handles, and pole mounts
- Protection circuits for high-power operation
- Why PA is a fundamentally different design domain from home hi-fi

**Build C (Subwoofer)** taught you:
- High-excursion driver selection (Xmax is king)
- Sealed vs. ported trade-offs specifically for subwoofer duty
- Integration with main speakers via active crossover
- Plate amplifier selection and DSP integration

You now have the skills and knowledge to design, build, and tune any speaker for any application. In Part 3, we turn to the other half of the equation: the amplifiers that drive these speakers. The amplifier and speaker are a system -- the amplifier's output impedance, power delivery, and damping factor all interact with the speaker's impedance and sensitivity. Understanding that interaction is the next step on the path to 100x audio building.
