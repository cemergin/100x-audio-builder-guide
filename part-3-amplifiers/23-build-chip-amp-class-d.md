<!--
  CHAPTER: 23
  TITLE: Build: Chip Amp and Class D Amp
  PART: 3 — Amplifiers
  PREREQS: Chapter 22
  KEY_TOPICS: LM3886 gainclone build, component selection, PCB layout, power supply build, TPA3255 Class D build, enclosure, first power-up, testing, listening comparison, measurements
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 23: Build: Chip Amp and Class D Amp

> **Part 3 — Amplifiers** | Prerequisites: Chapter 22 | Difficulty: Intermediate

Time to build. We've spent five chapters on theory, design, power supplies, and safety. Now we turn all of that into two working amplifiers that you can actually listen to, compare, and use.

**Build A** is an LM3886 "Gainclone" — a Class AB chip amplifier for home hi-fi. This is the purist's build: a linear power supply with a toroidal transformer, minimal signal path, and the famously musical LM3886 chip. The result will deliver about 40W per channel into 8 ohms with vanishingly low distortion. It will sound noticeably good.

**Build B** is a TPA3255-based Class D amplifier for party use. Using a pre-made TPA3255 board and an off-the-shelf SMPS, this build prioritizes raw power and efficiency. It can deliver 175W or more per channel — enough to drive a pair of PA speakers to ear-splitting levels with almost no heat generation.

After building both, we'll compare them head-to-head: measurements and listening impressions. The differences are instructive and will deepen your understanding of amplifier design far more than any amount of theory alone.

Both builds involve **mains electricity**. Follow every safety procedure from Chapter 22. Use your dim bulb tester for first power-up. Have your discharge tool ready for the LM3886 build (it uses high-voltage-rated filter capacitors).

### In This Chapter
- BUILD A: LM3886 Gainclone — component selection, power supply, assembly, testing
- BUILD B: TPA3255 Class D — board selection, power supply, assembly, testing
- First power-up procedures for both
- Listening comparison
- Measuring output power, THD, and frequency response

### Related Chapters
- [Ch 19: Solid-State Amplifier Design](19-solid-state-amplifier-design.md) — the theory behind both designs
- [Ch 21: Power Supply Design](21-power-supply-design.md) — supply design principles
- [Ch 22: Safety](22-safety-mains-high-voltage.md) — MANDATORY reading before this chapter
- [Ch 12: Speaker Impedance](../part-2-speakers/12-speaker-impedance.md) — understanding the load
- [Ch 31: PCB Design](../part-5-pcb-professional/31-pcb-design.md) — if you want to design your own boards
- [Ch 35: Matching Amps to Speakers](../part-6-system-design/35-matching-amps-speakers.md) — choosing speakers for your new amps
- [Ch 38: System Builds](../part-6-system-design/38-system-builds.md) — integrating these amps into complete systems

---

# BUILD A: LM3886 Gainclone

## 1. The LM3886 — What We're Working With

Pull up the LM3886 datasheet (Texas Instruments SNAS091C or later revision). You'll refer to it throughout this build. Let's review what makes this chip special.

### 1.1 Key Specifications

| Parameter | Value | Why It Matters |
|-----------|-------|----------------|
| Output power (8 ohm) | 38W (±28V supply) | Enough for most home speakers |
| Output power (4 ohm) | 68W (±28V supply) | Drives less efficient speakers |
| THD+N (1W, 8 ohm) | 0.03% | Essentially inaudible distortion |
| Slew rate | 19 V/us | Handles all audio transients easily |
| Input noise | 2 uV (IHF-A weighted) | Inaudible with any normal speaker |
| PSRR | 120 dB (DC), 85 dB (1 kHz) | Tolerates unregulated supply |
| Protection | Overcurrent, overvoltage, thermal, SPiKe | Hard to destroy |

### 1.2 The Gainclone Topology

We're building the non-inverting configuration with minimal components:

```
                              +Vcc (+28V)
                               │
                             [100nF] C3 (bypass, ceramic)
                               │
                    ┌──────────┤
                    │     ┌────┤ pin 1 (+Vcc)
                    │     │    │
  Input ──[C1]──┬──┤pin 9 │  LM3886  │pin 3 (OUT)──┬──[L1]──┬── Speaker (+)
   1uF    │     │(+in)   │          │            │       │
  film    │     │         │          │          [R3]  [C5+R4]  (Zobel)
          │     │    pin 4│          │          10R    100nF+10R
          │     │   (-in) │          │            │       │
          │     │    │    │          │           GND    GND
          │     │   [R2]  │          │
          │     │   20k   │          │
          │     │    │    │          │
          │     │   [R1]  │          │
          │     │    1k   │          │
          │     │    │    └────┤ pin 7 (-Vcc)
          │     │   GND        │
          │     │            [100nF] C4 (bypass, ceramic)
          │     │              │
         GND   GND           -Vcc (-28V)
```

**Gain = 1 + R2/R1 = 1 + 20k/1k = 21 (26.4 dB)**

This is the classic gainclone configuration. The beauty is its simplicity — the signal passes through one input capacitor, two resistors, and the LM3886. Nothing else.

---

## 2. Component Selection

Every component matters in a minimalist circuit. Here's what to buy and why.

### 2.1 Bill of Materials (Per Channel)

| Component | Value | Type/Part Number | Notes |
|-----------|-------|------------------|-------|
| U1 | LM3886TF | TO-220 (11-pin) | Not the LM3886T (different pinout) |
| C1 | 1 uF | Film capacitor (MKP or MKT) | Sets LF rolloff at ~3.4 Hz with 47k Zin |
| R1 | 1k | Metal film, 1%, 1/4W | Gain-setting resistor |
| R2 | 20k | Metal film, 1%, 1/4W | Gain-setting resistor |
| R3 | 10 ohm | Metal film, 1/4W | Damping for output inductor |
| R4 | 10 ohm | Carbon or metal film, 1/2W | Zobel network |
| C3, C4 | 100 nF | Ceramic X7R, 50V | Supply bypass — right at pins |
| C5 | 100 nF | Film (MKP), 100V | Zobel network — must handle high frequency |
| C6, C7 | 1000-4700 uF | Electrolytic, 50V, low ESR | Local reservoir caps per channel |
| L1 | 10 uH | Air-core, hand-wound | 10 turns 18 AWG on 1/2" form |
| | | | Or: buy a pre-made output inductor |
| Misc | | Heat sink (< 5 C/W), mica insulator, thermal paste, mounting hardware, hookup wire |

### 2.2 Component Notes

**C1 — Input capacitor:** Use a film capacitor, not ceramic and not electrolytic. Film caps have low distortion and no polarity. WIMA MKS2, Panasonic ECQ-E series, or Nichicon KP are all good choices. Some builders use 2.2 uF or even 4.7 uF to extend the bass response, but 1 uF gives a -3 dB point of 3.4 Hz — well below audibility.

**R1 and R2 — Gain resistors:** Metal film for low noise and tight tolerance. The gain accuracy depends on these resistors, so 1% tolerance is important. Don't use carbon composition — they're noisy.

**Zobel network (R4 + C5):** This RC network across the output presents a resistive load at high frequencies, preventing oscillation with capacitive loads (like long speaker cables). Use a film capacitor for C5 — ceramic caps at this position can generate microphonic noise. The value isn't critical: 10 ohm + 100 nF is the standard, but 10 ohm + 47 nF works too.

**L1 — Output inductor:** This isolates the amplifier from the speaker cable's capacitance. Wind 10 turns of 18 AWG magnet wire (or solid hookup wire) on a 1/2" (12 mm) diameter former (a drill bit or wooden dowel). Remove the former after winding — it should hold its shape. Alternatively, buy a pre-wound 10 uH air-core inductor rated for 5A+.

**LM3886TF vs LM3886T:** These have **different pinouts**. The TF version is the fully isolated TO-220 package (tab electrically isolated from the heatsink). The T version has the tab connected to -Vcc, requiring an insulator between the chip and heat sink. The TF is easier to work with but slightly more expensive. Either works — just know which one you have and adjust your mounting accordingly.

### 2.3 Where to Buy

- Mouser, Digi-Key, or Newark for the LM3886 and precision components
- Parts Express or DIYAudioStore for audio-specific components
- eBay (with caution — counterfeit LM3886 chips exist; buy from reputable sellers or buy direct from authorized distributors)

---

## 3. Power Supply Build

### 3.1 Power Supply BOM

| Component | Value | Notes |
|-----------|-------|-------|
| Transformer | Toroidal, 2×18V AC, 160VA | Talema, Toroid, or Antek |
| Bridge rectifier | KBPC2510 (25A, 1000V) | Overkill, but robust and cheap |
| C_filter (×4) | 4700-10000 uF / 50V | 2 per rail. Panasonic FR or Nichicon HE |
| NTC thermistor | 5 ohm cold, 5A | Inrush limiter. Epcos B57364 or similar |
| Fuse | 2A slow-blow, 5×20mm | On IEC inlet |
| IEC inlet | C14 with fuse holder | Schurter or Qualtek |
| Power switch | DPST, 250VAC, 5A | Switches both live and neutral |

### 3.2 Assembly

**Step 1: Mount the transformer.**
Bolt the toroidal transformer to the chassis base using the supplied center bolt and rubber washers. The rubber washers prevent mechanical hum from transmitting to the chassis. Place the transformer toward one end of the chassis (away from where the input jacks will be).

**Step 2: Wire the mains input.**
IEC inlet → fuse → power switch (live) → NTC thermistor → transformer primary. Neutral from IEC to transformer primary. Earth from IEC to chassis bolt (star ground point).

Use appropriately rated wire (18 AWG minimum for mains). Solder and/or use crimp terminals. All mains connections must be mechanically secure — a loose mains connection can arc and start a fire.

**Detailed mains wiring path:**
```
IEC inlet pin L (live) ──► Fuse holder ──► Power switch terminal 1
Power switch terminal 2 ──► NTC thermistor terminal 1
NTC thermistor terminal 2 ──► Transformer primary terminal 1
IEC inlet pin N (neutral) ──► Transformer primary terminal 2
IEC inlet pin E (earth) ──► Star ground bolt on chassis
```

Use ring terminals or spade terminals with heat-shrink tubing for all mains connections. Verify that no bare conductor is exposed. The power switch should be DPST (Double Pole Single Throw) so it disconnects both live AND neutral — this is important for safety.

**Step 3: Wire the rectifier and filter caps.**
Connect the transformer's secondary leads (the 18V AC wires) to the bridge rectifier AC inputs. Connect the bridge rectifier DC outputs to the filter capacitors. Observe polarity carefully — electrolytics connected backwards will fail violently.

For a dual-rail supply using a center-tapped transformer: the center tap is ground. Each secondary half is rectified separately (two half-wave rectifiers, or a bridge per rail) to produce +V and -V.

**Capacitor polarity — how to verify:**
Electrolytic capacitors have the negative terminal marked with a stripe (usually with minus signs "-" printed along the stripe). The positive terminal is typically the longer lead on new capacitors. Before soldering:
1. Identify the positive and negative terminals on the cap.
2. Verify: the positive terminal connects to the positive rectifier output; the negative terminal connects to ground.
3. For the negative rail: polarity is reversed — the negative terminal connects to the negative rectifier output; the positive terminal connects to ground.
4. Double-check. A reversed electrolytic cap at 30V will bulge, vent, and potentially explode within seconds of power-on.

**Step 4: Verify the supply before connecting the amplifier.**
Power up through the dim bulb tester with *nothing* connected to the DC outputs. Measure:
- +V rail to ground: should read approximately ±25 to ±28V DC (18V × √2 = 25.5V minus diode drops)
- Ripple: connect an oscilloscope (AC coupled) to see the ripple waveform. Should be a few hundred mV at 120 Hz.
- Both rails should be approximately equal in magnitude.

---

## 4. Amplifier Assembly

### 4.1 Layout Approach

For a first build, point-to-point construction on a small piece of perfboard (or even dead-bug style) works well. The circuit is simple enough that a PCB isn't strictly necessary, though it does make things neater.

If you prefer a PCB: multiple open-source LM3886 PCB designs are available on the DIYAudio forums and GitHub. The Neurochrome LM3886 board is a professionally designed option if you want a kit.

### 4.2 Wiring the LM3886

Refer to the LM3886TF datasheet pinout. The pin numbering is printed on the chip. Key connections:

| Pin | Function | Connect To |
|-----|----------|------------|
| 1   | +Vcc     | +28V rail through C3 bypass |
| 2   | +Vcc     | +28V rail (parallel with pin 1) |
| 3   | Output   | Output inductor L1 → speaker |
| 4   | -Input   | Feedback network (R2/R1 junction) |
| 5   | N/C      | No connection |
| 6   | N/C      | No connection |
| 7   | -Vcc     | -28V rail through C4 bypass |
| 8   | -Vcc     | -28V rail (parallel with pin 7) |
| 9   | +Input   | Input capacitor C1 |
| 10  | Mute     | +Vcc through 10k resistor (un-muted) |
| 11  | GND      | Signal ground |

**Critical:** The 100 nF ceramic bypass caps (C3, C4) must be physically as close to the power pins as possible — within 10 mm. Long leads on bypass caps are nearly useless.

### 4.3 Heat Sink Mounting

The LM3886 must be mounted to a heat sink. From the thermal calculations in Chapter 19:

- For ±28V rails and 8-ohm speaker: worst-case dissipation ≈ 10W per chip
- Required heat sink: 5-6 C/W or better

**Mounting procedure (for LM3886T with non-isolated tab):**
1. Apply thin, even layer of thermal paste to the chip's tab.
2. Place mica insulator on the heat sink.
3. Apply thin layer of thermal paste to the other side of the mica insulator.
4. Position the LM3886 on the mica insulator.
5. Secure with mounting screw and insulating shoulder washer (to isolate the screw from the heat sink).
6. **Verify isolation:** With a multimeter on continuity mode, check that there is NO continuity between the LM3886 tab (accessible at the screw) and the heat sink. If there is continuity, your insulation has failed — redo it.

**For LM3886TF (isolated tab):** No insulator needed. Apply thermal paste directly and bolt down. Much simpler.

### 4.4 Star Ground Implementation

All grounds connect to a single point (a bolt on the chassis near the filter caps):

1. Power supply ground (filter cap negative terminals)
2. LM3886 pin 11 (signal ground)
3. Input jack ground
4. Speaker jack ground

Run separate wires from each of these to the star ground point. Do not daisy-chain.

### 4.5 The Mute Pin

The LM3886 has a mute function on pin 10. When pin 10 is pulled below about 1V (relative to the -Vcc rail), the output is muted. When pulled above about 3V, the output is active.

For the simplest implementation: connect pin 10 to +Vcc through a 10k resistor. This keeps the chip permanently unmuted.

For a more sophisticated build, you can add a mute circuit that:
1. Mutes the output at power-on (by delaying the pin 10 voltage rise with an RC circuit)
2. Unmutes after 2-3 seconds (once the power supply has stabilized)
3. This eliminates the "thump" that can occur at power-on

A simple implementation: connect pin 10 to +Vcc through a 47k resistor, with a 47uF capacitor from pin 10 to -Vcc (or signal ground). The RC time constant (47k × 47uF ≈ 2.2 seconds) provides the turn-on delay. Some builders add a transistor that pulls pin 10 low on power-down for instant muting.

### 4.6 Input Configuration Options

**Direct connection (purist approach):** Wire RCA jacks directly to the LM3886 input. No volume control, no source selection. The source (preamp, DAC, etc.) controls the volume. This is the classic gainclone approach — minimum components in the signal path.

**With volume control:** Add a 10k-50k audio-taper potentiometer between the input jack and the LM3886 input. Wire it as a voltage divider: input to top lug, wiper to the amplifier input through C1, bottom lug to ground. The pot value should be much lower than the amplifier's input impedance (which is set by R1 + R2 = 21k in our configuration) to avoid loading effects. A 10k pot works well.

**With input buffer:** For driving long cable runs or using a passive preamp, add a unity-gain buffer (OPA2134 or similar audio op-amp) between the input jack and the amplifier. This provides a low output impedance to drive the gain-setting network without affecting the source.

For your first build, the direct connection is recommended. Add complexity later if needed.

---

## 5. First Power-Up — LM3886

Follow this procedure exactly. Refer to the Chapter 22 safety checklist.

### 5.1 Pre-Power Checks

- [ ] Visual inspection: all wiring matches schematic
- [ ] Mains wiring verified: live, neutral, earth correct
- [ ] Earth continuity from plug to chassis: < 0.5 ohm
- [ ] No shorts between power rails and ground
- [ ] No shorts between + and - rails
- [ ] LM3886 mounted with correct insulation (if T version)
- [ ] Speaker NOT connected
- [ ] No input signal connected

### 5.2 Power-Up Through Dim Bulb Tester

1. Connect the amplifier to the dim bulb tester.
2. Turn on. The bulb should flash briefly (cap charging) then go nearly dark.
3. If the bulb stays bright: **power off immediately.** You have a fault.
4. If the bulb goes dim/dark: measure supply rails. Should be ±25 to ±28V.

### 5.3 Amplifier Checks (No Signal)

1. **DC offset at output:** Measure the DC voltage at the speaker output terminal relative to ground. It should be less than ±50 mV. If it's more than ±100 mV, something is wrong. **Do not connect a speaker until DC offset is within spec.** DC at the output will damage a speaker.

2. **Quiescent current:** Measure the voltage across one of the power supply feed wires (insert a 1-ohm, 5W resistor in series with one supply rail temporarily). The quiescent current should be about 30-50 mA. V = I × R, so 30 mV across 1 ohm = 30 mA.

3. **Thermal check:** Let it idle for 10 minutes. The LM3886 and heat sink should be warm but not hot. If the heat sink is too hot to hold your hand on after 10 minutes (> 50C), investigate.

### 5.4 First Audio Test

1. Remove the dim bulb tester (connect directly to mains through a fused outlet).
2. Connect a speaker (8 ohm, rated for at least 50W).
3. Connect a signal source (phone, DAC, etc.) through a volume-controlled preamp or with the source volume at minimum.
4. Slowly increase the volume. You should hear clean, distortion-free audio.
5. Listen for hum or buzz at zero signal. A well-built gainclone should be dead silent — any hum audible with your ear near the speaker suggests a grounding issue.

---

# BUILD B: TPA3255 Class D Amplifier

## 6. Board Selection

Building a TPA3255 from scratch requires a precisely laid-out PCB with controlled impedances and tight component placement. For a first build, this is impractical. Instead, we'll use a pre-assembled TPA3255 board.

### 6.1 Recommended Boards

Several manufacturers offer TPA3255 boards at reasonable prices:

- **TI TPA3255EVM** — The official evaluation module from Texas Instruments. The reference design. Excellent performance but relatively expensive ($50-80).
- **Aiyima A07** — A popular consumer TPA3255 board. Inexpensive ($30-50), well-documented in the DIY community, and good measured performance. Available on Amazon and AliExpress.
- **Various AliExpress/eBay boards** — Many options at various price points. Look for boards that use the genuine TPA3255 chip (counterfeits exist) and have decent output filter inductors.

For this build, we'll assume a board like the Aiyima A07 or equivalent — a stereo TPA3255 board that accepts a single 24-48V DC input and outputs to speaker terminals.

### 6.2 What's on the Board

A typical TPA3255 board includes:
- The TPA3255 chip
- Input filtering (RC low-pass)
- Output LC filters (inductors + film capacitors)
- Input connectors (RCA or 3.5mm)
- Speaker terminals
- Power input connector (DC barrel jack or screw terminal)
- Indicator LED(s)

You provide: the power supply, the enclosure, and the wiring.

---

## 7. Power Supply for Class D

### 7.1 The SMPS

The TPA3255 operates from a single DC supply, typically 12-48V. Higher voltage = more power output. For maximum performance:

**Recommended:** 48V / 10A SMPS
- Mean Well LRS-350-48 (350W, 48V, 7.3A) — reliable, well-regarded, and available everywhere
- Or similar: Mean Well RSP-320-48, etc.

The Mean Well LRS-350-48 is an open-frame supply (no enclosure) intended for installation inside equipment. It accepts 100-240V AC input and outputs 48V DC.

### 7.2 Power Budget

The TPA3255 at 48V into 4 ohms per channel (stereo, both channels driven):
- Maximum output: ~175W × 2 = 350W
- At 92% efficiency: DC input power ≈ 380W
- Plus SMPS losses: total from mains ≈ 420W

A 350W SMPS is sufficient for typical use (music has a high crest factor, so average power is much less than peak). For sustained full-power testing, a larger supply (500W+) provides more headroom.

### 7.3 Input Filtering

Add a Pi filter between the SMPS output and the TPA3255 board:

```
SMPS +48V ──[100uF/63V electrolytic]──[ferrite bead]──[100uF/63V electrolytic]──► Board +Vin
SMPS GND  ──────────────────────────────────────────────────────────────────────► Board GND
```

This helps suppress any switching noise from the SMPS that might affect the TPA3255's modulator.

---

## 8. Enclosure and Assembly — Class D

### 8.1 Enclosure Selection

The TPA3255 generates minimal heat, so thermal management is not a major concern. Choose an enclosure based on:
- Size: large enough for the TPA3255 board + SMPS + wiring
- Material: aluminum preferred for shielding and modest heat sinking
- Ventilation: some airflow for the SMPS (which does generate heat)

A standard 1U rack enclosure or a DIY aluminum project box works well.

### 8.2 Wiring

1. **Mains input:** IEC C14 inlet → fuse (5A slow-blow) → SMPS AC input. Follow all mains wiring procedures from Chapter 22.
2. **SMPS output → TPA3255 board:** Use 16 AWG or heavier wire. Keep the run short. Include the Pi filter from Section 7.3.
3. **Signal input:** RCA jacks on the back panel → shielded cable → board input. Keep the signal wiring away from the SMPS and its AC input wiring.
4. **Speaker output:** Board output → speaker binding posts on the back panel. Use 16 AWG or heavier wire.
5. **Earth ground:** Connect the chassis to the mains earth. Connect the SMPS earth (if present) to the chassis. The TPA3255 board ground should connect to the chassis at one point only (star ground principle, even in a Class D build).

### 8.3 Safety Note

The SMPS is a mains-connected device with **exposed mains terminals on its input side**. Mount it securely inside the enclosure so that no exposed mains connections are accessible when the enclosure is closed. Cover the mains input terminals with heat-shrink tubing or insulating boots. The SMPS should not be accessible without tools.

---

## 9. SMPS Safety — Important Details

Open-frame SMPS units like the Mean Well LRS-350-48 deserve special attention because they're not enclosed — the mains terminals, components, and high-voltage internals are all exposed.

### 9.1 SMPS Hazards

**Mains voltage on the input terminals.** The screw terminals where you connect 120/240V AC are exposed metal. Even after the SMPS is wired, the terminals are accessible if not covered.

**Internal high-voltage DC bus.** The SMPS internally rectifies the mains to create a high-voltage DC bus (about 160V for 120V input, 340V for 240V input). This bus voltage is stored in internal capacitors and can persist after power is removed — similar to the tube amp B+ hazard, though usually with smaller capacitance and built-in discharge.

**Output voltage present.** The 48V output, while not immediately lethal, can deliver very high current (10A+). A short circuit on the output can cause sparks, wire heating, and fire. Fuse the output.

### 9.2 SMPS Safety Practices

1. **Cover all mains terminals** with heat-shrink tubing, insulating boots, or hot-glue after wiring. They should not be touchable.
2. **Mount the SMPS securely** to the chassis. Use the mounting holes provided. Don't let it rattle around.
3. **Add strain relief** to all wires connected to the SMPS terminals. A wire pulling loose from the mains terminal could contact the chassis or output wiring — with catastrophic results.
4. **Allow ventilation.** SMPS units generate heat and need airflow. Don't enclose them in a sealed space.
5. **Fuse the output.** A 10A fast-blow fuse on the 48V output line protects against wiring faults in your amplifier section.
6. **Don't modify the SMPS.** The internal components are selected for safety certification. Modifying an SMPS voids its safety certification and can create hazards.

---

## 10. First Power-Up — TPA3255

### 9.1 Pre-Power Checks

- [ ] Visual inspection: all wiring correct
- [ ] Mains wiring to SMPS verified
- [ ] Earth continuity to chassis
- [ ] No shorts on DC power to board
- [ ] Speaker NOT connected
- [ ] No signal input

### 9.2 Power-Up

1. Connect through dim bulb tester.
2. Turn on. The SMPS may cause a brief bright flash (inrush). The bulb should then go nearly dark.
3. If the TPA3255 board has an indicator LED, it should light up.
4. Measure the SMPS output: should be close to 48V DC.
5. Measure the voltage at the speaker output terminals: should be approximately 0V DC (within ±50 mV). Any significant DC offset indicates a fault.

### 9.3 First Audio Test

1. Remove dim bulb tester, connect directly to mains through fused outlet.
2. Connect speakers (4 or 8 ohm, rated for the expected power).
3. Connect signal source at minimum volume.
4. Slowly increase volume. The TPA3255 should produce clean, powerful audio.
5. Listen for switching noise (high-frequency hiss or whine). A small amount may be audible with ear pressed to the speaker — this is normal for Class D and inaudible at listening distance. Excessive noise suggests a grounding or filtering problem.

---

## 10. Listening Comparison

With both amplifiers built and working, it's time for the most revealing test: direct comparison.

### 10.1 Setup

Use the **same speakers, same source, and same tracks** for both amplifiers. Ideally, set both amps to the same output level for a fair comparison (use a smartphone SPL meter or a multimeter to measure output voltage with a test tone).

Suggested test tracks (use lossless files — FLAC or CD, not MP3):
- **Bass control:** Something with deep, tight bass (Massive Attack - "Angel," or Billie Eilish - "Bad Guy"). Listen for how well the amp controls the woofer on the deep bass notes. Does the bass stay tight and defined, or does it become loose and boomy?
- **Transients:** Percussion-heavy material (Steely Dan - "Aja," or any well-recorded drum solo). Listen for the snap and attack of each drum hit. Are the transients sharp and precise, or slightly softened?
- **Vocals:** A solo voice with minimal instrumentation (Diana Krall, Leonard Cohen). Listen for naturalness, presence, and any sibilance (harsh "s" sounds). A good amp makes vocals sound like a person in the room.
- **Full orchestral:** Something with wide dynamic range (Mahler Symphony No. 2, or a film score). Listen for how the amp handles the transition from quiet passages to loud climaxes. Does it feel effortless, or does it strain?
- **Guitar:** Your own playing, through a clean preamp or direct. You know what your guitar sounds like — does the amp reproduce it faithfully?

**Level matching is critical for fair comparison.** Our ears perceive louder as "better." A difference of even 1 dB can make an amplifier seem brighter, more detailed, and more dynamic. Use an SPL meter (smartphone app is adequate) to match levels between the two amps within 0.5 dB before making any sonic judgments.

### 10.2 What You'll Likely Hear

**LM3886 Gainclone:**
- Slightly "warmer" or "fuller" character, especially in the midrange
- Bass that's tight and controlled (high damping factor) but with a "rounded" quality
- Smooth, fatigue-free treble
- An analog, cohesive presentation — things "flow" together
- At very high volumes, clipping is relatively soft and graceful

**TPA3255 Class D:**
- "Cleaner" and more transparent — less of its own character imposed on the music
- Bass with more "slam" and impact — partly due to the higher available power
- Treble that's detailed and extended, possibly slightly "sharper" than the LM3886
- A more dynamic, "lively" presentation — transients feel faster
- At very high volumes, significantly more headroom before clipping

The differences are subtle at moderate volumes. They become more apparent at higher volumes and with demanding material. Neither is "better" — they represent different philosophies and different trade-offs.

### 10.3 Why They Sound Different

The LM3886 is a Class AB amplifier with negative feedback within the chip. Its character comes from:
- The BJT-based output stage inside the chip
- The analog feedback loop topology
- The linear power supply (no switching artifacts, but the supply sags slightly under dynamic peaks)

The TPA3255 is a switching amplifier with a completely different signal path. Its character comes from:
- PWM modulation and demodulation (the output LC filter is in the signal path)
- The closed-loop digital feedback architecture
- The SMPS power supply (tightly regulated, no sag, but with HF switching artifacts)

---

## 11. Measurements

If you have access to measurement equipment (or even a PC with a decent sound card and Room EQ Wizard / REW software), quantify the differences:

### 11.1 Output Power into Various Loads

**Test procedure:**
1. Connect a power resistor (8 ohm/100W or 4 ohm/200W non-inductive) as the load.
2. Feed a 1 kHz sine wave at the input.
3. Increase the input level while monitoring the output voltage on an oscilloscope or AC voltmeter.
4. Record the output voltage at visible clipping (where the waveform starts to flatten).
5. Calculate power: P = Vrms² / Rload.

**Expected results:**

| Amplifier | 8 ohm load | 4 ohm load |
|-----------|-----------|-----------|
| LM3886 (±28V) | 35-42W | 50-68W |
| TPA3255 (48V) | 130-175W | 175-250W |

### 11.2 THD at Various Power Levels

Using a PC sound card and REW (or a dedicated audio analyzer if available):
1. Connect the amplifier output to the sound card input through a resistive attenuator (to bring the voltage down to line level — do NOT connect a power amp output directly to a sound card input).
2. Sweep input level from 0.1W to rated power.
3. Record THD+N at each level.

**Expected results:**
- LM3886: 0.01-0.03% THD+N from 1W to 30W, rising to 0.1-1% near clipping
- TPA3255: 0.003-0.01% THD+N from 1W to 100W, rising near clipping

### 11.3 Frequency Response

Sweep a sine wave from 20 Hz to 20 kHz at a constant 1W output level. Record the output voltage at each frequency.

**Expected results:**
- LM3886: ±0.2 dB from 20 Hz to 20 kHz (limited by input cap at low end)
- TPA3255: ±0.5 dB from 20 Hz to 20 kHz (limited by output LC filter at high end — response depends on load impedance due to the filter interaction with the speaker impedance)

---

## 12. Troubleshooting

### 12.1 Common LM3886 Issues

**Oscillation (high-frequency squealing or hiss):**
- Check that bypass caps are right at the power pins
- Verify the Zobel network is connected
- Check for excessively long wiring runs
- Add a 10 ohm + 100 nF snubber from output to ground if needed

**Hum (50/60 Hz or 100/120 Hz):**
- Grounding problem. Check star ground.
- Input cable too close to transformer — reroute.
- Try grounding the input jack shield directly to the chassis.

**DC offset at output > 100 mV:**
- Check that the gain-setting resistors (R1, R2) are the correct values and properly connected.
- Verify pin 4 (-input) is connected to the R1/R2 junction.
- Check for a solder bridge between pins.

**Thermal shutdown under load:**
- Heat sink undersized. Check calculations from Ch 19.
- Thermal paste not applied properly.
- Insulator between chip and heat sink creating too much thermal resistance.

### 12.2 Common TPA3255 Issues

**No output / very quiet:**
- Check that the SMPS is outputting the correct voltage.
- Check that the board's power LED is on.
- Some boards have a standby mode — check if there's an enable pin or button.

**Distorted output at all levels:**
- DC power polarity reversed (may have damaged the chip).
- Output LC filter components dislodged or incorrect.

**Buzzing or whining:**
- Ground loop between SMPS and TPA board. Try connecting board ground to chassis at one point only.
- SMPS switching noise coupling into the audio. Add input filtering (Pi filter). Move signal cables away from SMPS.
- Inductor buzz (magnetostriction in the output filter inductors at high power). This is a design characteristic of some cheaper boards.

---

## 13. Enclosure Design — LM3886

The enclosure is more than a box — it's a functional part of the amplifier. It provides shielding, heat sinking, and protection.

### 13.1 Chassis Selection

For the LM3886 build, a good enclosure provides:
- **Aluminum construction** for good thermal conductivity and EMI shielding
- **Adequate internal volume** for the transformer, filter caps, and two amplifier boards (approximately 300mm × 200mm × 80mm minimum for a stereo build)
- **Heat sink capability** — either an external heat sink mounted to the chassis or a chassis thick enough to serve as a heat sink itself

Good options:
- **Pre-made hi-fi enclosure:** Many aluminum enclosures designed for DIY audio are available from AliExpress vendors, Modushop, and hifi2000. These come with heatsink fins on the sides and pre-machined panels. They look professional and work well.
- **Hammond 1455 series:** An industrial aluminum extrusion with finned sides that can serve as heat sinks. Available from Mouser/Digi-Key.
- **DIY from sheet aluminum:** For those who enjoy metalwork. 2mm aluminum sheet can be cut, bent, and drilled. Use self-tapping screws and aluminum angle for assembly.

### 13.2 Panel Layout

**Rear panel:**
- IEC inlet with fuse holder
- Power switch
- Two pairs of speaker binding posts (left/right)
- Two RCA input jacks (left/right) — or XLR if you prefer balanced input

**Front panel (optional):**
- Power LED indicator
- Volume potentiometer (if adding one — the purist gainclone has no volume control, relying on the preamp or source)

### 13.3 Internal Layout

The layout should follow the signal flow and minimize coupling between the mains/supply section and the signal section:

```
     REAR PANEL
     ┌─────────────────────────────────┐
     │ [IEC] [Switch]   [RCA L] [RCA R]│
     │                                  │
     │ [Transformer]  [Filter    ]      │
     │                [Caps      ]      │
     │                                  │
     │ [LM3886 L mounted to left wall] │
     │ [LM3886 R mounted to right wall]│
     │                                  │
     │ [Speaker L]        [Speaker R]   │
     └─────────────────────────────────┘
     FRONT PANEL
```

Mount each LM3886 on a separate section of the heat-sinking chassis wall. Keep the signal wiring (RCA jacks to the amplifier boards) as short as possible and away from the transformer.

### 13.4 Wiring Tips for the Enclosure

- **Use shielded cable** for the input signal (RCA jack to amplifier input). The shield connects to ground at the RCA jack end only (to avoid ground loops).
- **Use heavy gauge wire** (14-16 AWG) for speaker outputs and power supply connections.
- **Keep mains wiring** physically separated from signal wiring. If they must cross, cross at 90 degrees.
- **Label everything.** When you open this amp in 5 years for maintenance, you'll be glad you labeled the wires.

---

## 14. Bridged and Mono Configurations

### 14.1 Bridged TPA3255

Most TPA3255 boards support **BTL (Bridge-Tied Load)** operation, which is the default stereo configuration. For even more power, some boards support **PBTL (Parallel Bridge-Tied Load)** — both channels driving a single speaker in parallel. This can double the current capability (and roughly double the power into a given load).

Check your specific board's documentation for PBTL support and the procedure to enable it. Not all boards support this, and incorrect configuration can damage the chip.

### 14.2 LM3886 Paralleled

Two LM3886 chips can be paralleled to double the current capability and halve the output impedance. This requires matching the gain carefully and using small ballast resistors (0.1-0.5 ohm) on each output to ensure current sharing. The Neurochrome Modulus-86 is a well-known paralleled LM3886 design that achieves about 80W into 8 ohms with extremely low distortion.

---

## 15. Where to Go from Here

These two amplifiers are starting points, not endpoints. Here are natural next steps:

**For the LM3886 build:**
- Add a preamp with volume control, source selection, and tone controls
- Build a headphone amplifier version (lower supply voltage, lower power)
- Add a subwoofer output with a low-pass filter
- Build a multi-channel version for surround sound
- Connect it to a DSP crossover for an active speaker system ([Ch 29](../part-4-digital-audio/29-dsp-digital.md))

**For the TPA3255 build:**
- Build a dedicated subwoofer amp with integrated crossover
- Create a multi-amplifier PA rack with multiple TPA3255 boards
- Add DSP-based speaker processing ([Ch 29](../part-4-digital-audio/29-dsp-digital.md))
- Build a powered speaker by mounting the amp inside a speaker cabinet ([Ch 38](../part-6-system-design/38-system-builds.md))

**For deeper understanding:**
- Design a discrete Class AB amplifier from scratch using the principles from [Ch 19](19-solid-state-amplifier-design.md)
- Design your own PCB for the LM3886 ([Ch 31](../part-5-pcb-professional/31-pcb-design.md))
- Measure your amps with more sophisticated equipment (Audio Precision, QuantAsylum QA401)

---

## 16. Chapter Summary

You've built two working amplifiers:

1. **The LM3886 Gainclone** — a Class AB chip amplifier that demonstrates that simplicity and quality go hand in hand. With a clean linear power supply, minimal signal path, and proper grounding, it delivers hi-fi performance that rivals amplifiers costing many times more.

2. **The TPA3255 Class D** — a modern switching amplifier that shows how far Class D has come. Enormous power, nearly unmeasurable distortion, and a heat sink you barely need. It's the rational choice for any application where power and efficiency matter.

The comparison between the two amps teaches more about amplifier design than any amount of reading. You can hear the trade-offs — the warmth of analog versus the precision of switching, the character of a linear supply versus the grip of a regulated SMPS, the soft clipping of Class AB versus the hard limits of Class D.

Both are excellent amplifiers. Both are worth keeping and using. The LM3886 is your reference for late-night listening at home. The TPA3255 is your weapon for parties and outdoor events. Together, they cover most of the solid-state amplifier world.

Next up: we leave the semiconductor world behind and build something that glows.

---

*Next: [Chapter 24: Build: Tube Guitar Amplifier](24-build-tube-guitar-amp.md)*
*Previous: [Chapter 22: Safety: Working with Mains and High Voltage](22-safety-mains-high-voltage.md)*
