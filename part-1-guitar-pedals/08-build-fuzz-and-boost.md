<!--
  CHAPTER: 8
  TITLE: Build: Fuzz and Boost
  PART: 1 — Guitar Pedals
  PREREQS: Chapter 7
  KEY_TOPICS: Silicon Fuzz Face clone, clean boost, JFET boost, transistor hFE, component substitution, breadboard build, bias voltage, audio probe measurement
  DIFFICULTY: Beginner
  UPDATED: 2026-04-10
-->

# Chapter 8: Build: Fuzz and Boost

> **Part 1 — Guitar Pedals** | Prerequisites: Chapter 7 | Difficulty: Beginner

This is the chapter where you stop reading about circuits and start hearing them. We're building two pedals: a Silicon Fuzz Face clone and a clean boost. Together, they'll cost you about $30-50 in components, and you'll have them working on a breadboard within an afternoon.

The Fuzz Face is one of the most iconic circuits in guitar history — Jimi Hendrix, David Gilmour, Eric Johnson. It's also one of the simplest: two transistors, a handful of resistors and capacitors, and that's it. About ten components total. But within that simplicity lies surprising depth. The transistor gain, the bias point, the input capacitor — every component changes the character of the fuzz. This is where you start developing your ear for how component choices translate to sound.

The clean boost is even simpler — a single transistor or op-amp that makes your guitar louder without (intentionally) distorting it. It's a utility pedal that's genuinely useful: put it before an overdrive to push it harder, put it after everything to boost your solo volume, or use it as a buffer to drive a long cable run. And it's a great warm-up build before tackling the fuzz.

### In This Chapter
- Build 1: Silicon Fuzz Face clone — full build with component values
- Build 2: JFET clean boost — simple, useful, educational
- Component substitution experiments: 3 transistors compared
- Measuring your build with a multimeter and audio probe
- "What happens if..." explorations for every key component

### Related Chapters
- [Ch 5: How Effects Shape Sound](05-how-effects-shape-sound.md) — Clipping types and the theory behind fuzz
- [Ch 6: Gain Stages and Clipping Circuits](06-gain-stages-clipping-circuits.md) — Common emitter stages and clipping theory
- [Ch 7: From Schematic to Breadboard](07-schematic-to-breadboard.md) — The debugging methodology you'll use
- [Ch 9: Build: Overdrive and Distortion](09-build-overdrive-distortion.md) — Next builds: Tube Screamer and RAT
- [Ch 31: PCB Design for Audio](../part-5-pcb-professional/31-pcb-design-for-audio.md) — Making these into permanent PCBs
- [Ch 40: Sourcing and Supply Chain](../part-7-business/40-sourcing-supply-chain.md) — Where to buy components

---

## 1. Build 2: JFET Clean Boost (Start Here)

We're starting with the simpler build — yes, Build 2 before Build 1. The clean boost is fewer components, lower stakes, and teaches you the breadboard workflow before you tackle the more finicky fuzz circuit.

### 1.1 What This Circuit Does

A clean boost amplifies your guitar signal without adding distortion (until you push it into the next stage's clipping threshold, at least). It's the audio equivalent of a volume knob that goes past 10. The circuit is a single JFET transistor in a common-source configuration — the JFET equivalent of the common emitter stage from [Chapter 6](06-gain-stages-clipping-circuits.md).

Why a JFET? JFETs (Junction Field-Effect Transistors) have very high input impedance — similar to an op-amp — which means they don't load down your guitar pickup. They also have a naturally smooth overdrive characteristic when pushed into clipping, which is why many boutique pedals use them.

### 1.2 The Schematic

```
              +9V
               |
              Rd (6.8k)
               |
               ├───── Cout (10uF) ──── Vol pot (100kA) ──── Output
               |
  Input ─ Cin (100nF) ─── Gate
                           |
                          Q1 (2N5457 JFET)
                           |
                          Source
                           |
                          Rs (2.2k)
                           |
                      ┌── Cs (47uF) ── GND
                      |
                     GND
```

**The idea:** The input signal arrives at the gate through a coupling cap. The JFET amplifies it, and the amplified signal appears at the drain. The output coupling cap blocks DC, and a volume pot controls the output level.

### 1.3 Component List

| Reference | Value | Type | Notes |
|---|---|---|---|
| Q1 | 2N5457 | N-channel JFET, TO-92 | Can sub J201 or 2N5458 |
| Rd | 6.8 kohm | 1/4W metal film resistor | Drain resistor |
| Rs | 2.2 kohm | 1/4W metal film resistor | Source resistor |
| Cin | 100nF (0.1uF) | Ceramic or film capacitor | Input coupling |
| Cout | 10uF | Electrolytic capacitor, 25V | Output coupling |
| Cs | 47uF | Electrolytic capacitor, 25V | Source bypass |
| Vol | 100 kohm | Audio taper (A) pot | Output volume |
| C_pwr | 100uF | Electrolytic, 16V+ | Power filtering |

**Total cost:** Under $10 for all components.

### 1.4 Breadboard Build Steps

**Step 1: Install Q1 (2N5457).**

Check the datasheet for pinout. For the 2N5457 in a TO-92 package, looking at the flat face with leads pointing down: **Drain, Gate, Source** (left to right).

Place Q1 with:
- Drain in row 10
- Gate in row 10 (next column — same row group but different hole)
- Source in row 10

Wait — that can't be right. The three pins of a TO-92 are in a line, and they span three adjacent holes in one row group. Actually, they span across three columns in the same row *or* across the center gap. Let me be more specific:

Place Q1 straddling three rows:
- Drain lead in row 8, column e
- Gate lead in row 9, column e
- Source lead in row 10, column e

(The actual row numbers don't matter — what matters is that each pin is in a separate row so they can be independently connected to other components.)

**Step 2: Install Rd (6.8k).**
One lead in row 8 (same row as Drain), other lead in + power rail.

**Step 3: Install Rs (2.2k).**
One lead in row 10 (same row as Source), other lead in - power rail (GND).

**Step 4: Install Cs (47uF electrolytic).**
Positive lead in row 10 (Source), negative lead in - rail. This capacitor bypasses Rs for AC, increasing the AC gain.

**Step 5: Install Cin (100nF).**
One lead in row 5 (input node), other lead in row 9 (Gate).

**Step 6: Install Cout (10uF electrolytic).**
Positive lead in row 8 (Drain), negative lead in row 14 (output node).

**Step 7: Install volume pot.**
Use alligator clips: Lug 3 to row 14, Lug 2 (wiper) to the output jack tip, Lug 1 to GND.

**Step 8: Power and audio connections.**
- 9V battery: red to + rail, black to - rail
- C_pwr (100uF): across the power rails for filtering
- Input jack tip: wire to row 5
- Input jack ground: wire to - rail
- Output jack tip: wire from vol pot wiper
- Output jack ground: wire to - rail

### 1.5 Testing the Boost

**DC voltage checks (before audio):**

| Point | Expected | Your Measurement |
|---|---|---|
| Drain (row 8) | 3-7V (varies with JFET) | _______ |
| Gate (row 9) | ~0V (through input cap) | _______ |
| Source (row 10) | 0.5-2V | _______ |
| +9V rail | ~9V | _______ |

JFETs have wide parameter variation — the drain voltage can vary significantly between individual devices. As long as the drain isn't at 0V (shorted) or 9V (JFET not conducting), you're in the active region.

**Audio test:**
1. Plug in your guitar, connect the output to your amp (clean channel, moderate volume).
2. Play a chord. You should hear your guitar, noticeably louder.
3. Adjust the volume pot. At full, the signal should be significantly louder than bypassing the circuit (pulling the input wire out of the breadboard and connecting directly to the output).
4. Listen critically: the tone should be very similar to your dry guitar tone, just louder. If it sounds significantly different (much brighter or darker), a component value may be wrong.

**Audio probe verification:**
- Probe row 5 (input): Clean guitar
- Probe row 9 (gate): Clean guitar (should sound the same — the gate draws no current)
- Probe row 8 (drain): Louder guitar — this is the amplified signal
- Probe row 14 (output, after Cout): Same as drain, but DC-blocked

Congratulations — your first audio circuit is working. Now let's build something with more personality.

### 1.6 Boost Variations and Experiments

Now that the basic boost is working, try these variations to develop your ear for how circuit changes affect sound:

**Variation A: Remove the source bypass cap (Cs).**
Pull the 47uF cap out of the breadboard. The JFET source resistor now provides full AC negative feedback. Gain drops from ~15 dB to maybe 5-6 dB — a subtle lift rather than a strong push. The tonal character also changes: without the bypass cap, the gain is more uniform across the frequency range (the bypass cap was boosting the gain at *all* frequencies equally, but its removal reveals the JFET's natural frequency response more clearly). Some players prefer this subtler boost as an "always on" tone sweetener.

**Variation B: Change the drain resistor (Rd).**
Replace 6.8k with 4.7k: The drain voltage increases (less voltage drop across a smaller resistor). The gain decreases slightly, but the headroom increases — the circuit can handle hotter signals before clipping.

Replace 6.8k with 15k: The drain voltage decreases, gain increases, headroom decreases. At some point (very high Rd), the drain voltage drops so low that the JFET is no longer in its active region. The signal starts to clip — your "clean" boost is now a "dirty" boost. This can sound great as a mild overdrive.

**Variation C: Add a bass cut (make it a treble booster).**
Replace the input coupling cap (Cin, 100nF) with a much smaller cap: 1nF to 4.7nF. This forms a high-pass filter that cuts bass and low mids, passing only the treble through the boost. You've just built a circuit conceptually similar to the famous Dallas Rangemaster treble booster, used by Brian May and Tony Iommi.

With a 1nF input cap and the gate bias resistor providing maybe 1 Mohm to ground, the cutoff frequency is:

```
f = 1 / (2 * pi * 1M * 1nF) = 159 Hz
```

Everything below 159 Hz is progressively attenuated. The upper strings get boosted; the lower strings get only a mild lift. Into a slightly overdriven amp, this produces a cutting, aggressive lead tone because you're pushing the amp's distortion in the midrange and treble where it sounds best, while keeping the bass clean and tight.

> **What happens if...** you replace the JFET (2N5457) with a different JFET, like the J201? JFETs have even wider parameter variation than bipolar transistors — the pinch-off voltage and IDSS (drain saturation current) vary enormously. A J201 might have a different drain voltage, different gain, and different clipping behavior than the 2N5457, even though the circuit is identical. You may need to adjust Rd and Rs to re-bias the circuit for the new JFET. This is the JFET equivalent of the transistor selection game we'll play with the Fuzz Face — different devices, different characters.

> **What happens if...** you remove Cs (the source bypass cap)? Without the bypass, Rs provides AC negative feedback, which reduces the gain. The boost becomes much gentler — maybe only 3-6 dB instead of 15-20 dB. The tone also becomes more neutral (less coloration). Some players actually prefer this as a subtle "always on" boost. Try it both ways.

---

## 2. Build 1: Silicon Fuzz Face Clone

### 2.1 The Circuit and Its History

The Fuzz Face was introduced in 1966 by Arbiter Electronics. The original used germanium transistors (NKT275 or AC128), which are now rare and expensive. Dallas Semiconductor later made versions with silicon transistors (BC108, BC109), which are cheap, widely available, and consistent. We're building the silicon version because you can actually source the parts without selling a kidney.

The circuit has only two transistors and about ten total components. It's deceptively simple — the behavior depends critically on the interaction between the two transistors, and the biasing is sensitive enough that different transistors produce noticeably different sounds.

### 2.2 The Schematic

```
                        +9V
                         |
                        Rc2 (8.2k)
                         |
           Rc1 (33k)    ├──── Output ── Cout (10uF) ── Vol (500kA) ── Out Jack
            |            |
            ├── Rb (100k)──── Base Q2
            |            |
  In ─ Cin (2.2uF) ─── Base Q1       Q2 (NPN, e.g., BC108)
                         |            |
                   Q1 (NPN, e.g.,    Emitter Q2
                    BC108)            |
                         |           Re2 (470 ohm)
                     Emitter Q1       |
                         |           GND
                        GND
                         |
                        Fuzz pot (1k lin) between Q1 emitter and GND
```

Let me redraw this more carefully — the Fuzz Face is a two-transistor feedback amplifier:

```
                   +9V ────────────┬──────────────┐
                                   |              |
                                 Rc1 (33k)     Rc2 (8.2k)
                                   |              |
                                   ├── Rb (100k) ─┤
                                   |              |
              Cin                  |              |
  Input ───||──── Q1 base          Q2 base        ├─── Cout ──||── Vol ── Output
             2.2uF   |                 |          |     10uF    500kA
                      Q1 (NPN)         Q2 (NPN)   |
                      |                 |          |
                      Q1 emitter       Q2 emitter  Q2 collector
                      |                 |
                      Fuzz pot (1kB)   Re2 (390Ω)  ← Note: Some versions omit this
                      |                 |
                     GND              GND
```

Actually, let me describe the circuit topology clearly in words, since ASCII schematics of the Fuzz Face are notoriously hard to draw:

**Q1** (first transistor, NPN):
- Collector connects to Vcc through Rc1 (33k)
- Base receives the input signal through Cin (2.2uF)
- Emitter connects to GND through the Fuzz pot (1k linear)

**Q2** (second transistor, NPN):
- Collector connects to Vcc through Rc2 (8.2k) — this is also the audio output point
- Base connects to Q1's collector (through Rb, 100k, in some versions; directly in others)
- Emitter connects to GND (some versions have a small Re2 of 100-470 ohms)

**Feedback path:** Q2's collector connects back to Q1's base through a 100k resistor. This negative feedback stabilizes the bias and affects the fuzz character.

**Output:** Q2's collector → Cout (10uF) → Volume pot (500k audio taper) → Output jack.

### 2.3 Component List

| Reference | Value | Type | Notes |
|---|---|---|---|
| Q1 | BC108 (or 2N3904, see section 4) | NPN transistor, TO-92 | First stage |
| Q2 | BC108 (or 2N3904, see section 4) | NPN transistor, TO-92 | Second stage |
| Rc1 | 33 kohm | 1/4W metal film | Q1 collector resistor |
| Rc2 | 8.2 kohm | 1/4W metal film | Q2 collector resistor |
| Rb | 100 kohm | 1/4W metal film | Feedback/bias resistor |
| Fuzz pot | 1 kohm | Linear taper (B) pot | Controls fuzz amount |
| Cin | 2.2uF | Electrolytic, 25V | Input coupling |
| Cout | 10uF | Electrolytic, 25V | Output coupling |
| Vol | 500 kohm | Audio taper (A) pot | Output volume |
| C_pwr | 100uF | Electrolytic, 16V+ | Power supply filtering |

**Total cost:** About $10-15 for all components (less if you already have the transistors from the boost build).

### 2.4 Understanding the Circuit

Before we build, let's understand what's happening. This is a **two-stage common emitter amplifier with feedback:**

**Q1 (first stage):** Amplifies the guitar signal. The Fuzz pot controls the emitter resistance — at maximum resistance (1k), Q1 has moderate gain with AC degeneration. At zero resistance (emitter directly to ground), Q1 has maximum gain and the signal clips heavily against the supply rails.

**Q2 (second stage):** Further amplifies and clips the signal from Q1. Because Q2's collector resistor (8.2k) is relatively low, Q2 has high gain and drives the signal hard into clipping.

**The feedback path:** The connection from Q2's collector back to Q1's base (through the 100k resistor) provides negative feedback that:
- Stabilizes the DC bias point (making the circuit less sensitive to transistor hFE variations)
- Affects the frequency response (the feedback has some frequency dependence)
- Interacts with the guitar's volume knob (this is why Fuzz Faces clean up so well when you roll down the guitar volume)

**Why it sounds like a fuzz:** Both stages are driven into clipping. The combination of two stages of distortion, plus the feedback interaction, creates a rich, complex harmonic structure that's more "musical" than simple diode clipping. The signal is heavily compressed and saturated — sustain for days, with a thick, woolly character.

### 2.5 Breadboard Build Steps

**Step 1: Plan your layout.** Assign nodes to rows:

| Row | Node | Connections |
|---|---|---|
| 3 | Input | Input jack tip, Cin (+) |
| 5 | Q1 Base | Cin (-), Q1 base, Rb (one end) |
| 7 | Q1 Collector | Rc1 (one end), Q2 base connection |
| 6 | Q1 Emitter | Q1 emitter, Fuzz pot wiper wire |
| 10 | Q2 Base | Q2 base, wire from row 7 (or Rb) |
| 12 | Q2 Collector | Rc2 (one end), Cout (+), Rb (other end) |
| 11 | Q2 Emitter | Q2 emitter, GND (or Re2 to GND) |
| 15 | Output | Cout (-), Vol pot lug 3 |
| + rail | Vcc | Rc1, Rc2, battery + |
| - rail | GND | Fuzz pot, Q2 emitter path, battery - |

**Step 2: Install transistors.** This is where you need to be very careful about pinouts.

For **BC108** (TO-18 metal can): Check the datasheet. The tab on the can indicates the emitter. Looking at the bottom with the tab at the top, the pins (clockwise from the tab) are typically E, B, C. But verify for your specific batch.

For **2N3904** (TO-92 plastic): Flat face toward you, leads down: E, B, C (left to right).

Install Q1 with its three pins in separate rows as planned. Install Q2 the same way.

**Step 3: Install resistors.** Connect each resistor between the rows specified in your plan.

**Step 4: Install capacitors.** Mind the polarity on electrolytics. Cin (2.2uF): positive side toward the input (row 3), negative toward the base (row 5). Cout (10uF): positive toward the collector (row 12), negative toward the output (row 15).

**Step 5: Connect the Fuzz pot.** Use alligator clips: one outer lug to Q1's emitter row (row 6), the wiper to GND rail, the other outer lug also to GND. (Or: one outer lug to the emitter row, the other outer lug to GND. The wiper and one outer lug are both at ground. Turning the pot varies the resistance between the emitter and ground.)

**Step 6: Connect the Volume pot.** Lug 3 to row 15, Lug 2 (wiper) to output jack tip, Lug 1 to GND.

**Step 7: Power and audio connections.** Same as the boost build — battery, input jack, output jack, power filtering cap.

### 2.6 Testing the Fuzz

**DC voltage checks (no audio):**

| Point | Expected | Trouble Sign |
|---|---|---|
| Q1 Collector | 1-5V (depends on hFE and Fuzz pot) | 0V = saturated; 9V = cut off |
| Q2 Collector | 4-6V (should be near Vcc/2) | 0V or 9V = bias is wrong |
| Q1 Base | 0.5-0.7V above Q1 Emitter | No voltage = Rb not connected |
| Q2 Base | 0.5-0.7V above Q2 Emitter | Should be close to Q1 collector |

The Q2 collector voltage is the most critical. For the best fuzz tone, Q2's collector should be around 4-5V — roughly half the supply. If it's much higher or lower, the fuzz will sound thin and gated (cutting off abruptly when notes decay). Adjusting Rc2 (try values from 4.7k to 15k) lets you tune this bias point.

**Audio test:**
1. Set the Fuzz pot to about halfway. Set Volume to about halfway.
2. Plug in and play a single note. You should hear a thick, sustained, distorted tone.
3. Turn the Fuzz pot up (toward maximum resistance). The fuzz should get *less* intense — more clean signal, less saturation.
4. Turn the Fuzz pot down (toward minimum resistance). Maximum fuzz — thick, compressed, saturated.

Wait, that might seem backwards. In the Fuzz Face, increasing the emitter resistance *decreases* the gain. So "more resistance" = "less fuzz." Some builders wire the pot so that clockwise = more fuzz (minimum resistance). The naming is a convention, not a rule.

**The volume knob trick:** Roll your guitar's volume down to about 7 or 8. The fuzz should clean up dramatically — going from thick fuzz to a light, dynamic overdrive. This is one of the Fuzz Face's most musical characteristics, and it comes from the interaction between the guitar's output impedance (which changes with the volume pot position) and Q1's input circuit. This is also why Fuzz Faces sound different with a buffer in front of them — the buffer changes this interaction (see [Chapter 5, section 9](05-how-effects-shape-sound.md)).

**Audio probe walkthrough:**
- Input (row 3): Clean guitar
- Q1 Base (row 5): Slightly loaded guitar signal
- Q1 Collector (row 7): First stage of amplification — louder, starting to clip
- Q2 Base (row 10): Same as Q1 collector
- Q2 Collector (row 12): Full fuzz — clipped, compressed, harmonically rich
- Output (row 15): Same fuzz sound, DC-blocked

### 2.7 If It Doesn't Work

Refer to the debugging methodology in [Chapter 7](07-schematic-to-breadboard.md), but here are Fuzz Face-specific issues:

**No sound at all:**
- Check that both transistors are in the right orientation. This is the #1 cause.
- Verify that Cin and Cout are connected (not open).
- Check that the Fuzz pot is actually varying resistance (measure with multimeter).

**Sound but no fuzz (just a clean, quiet signal):**
- Q2 might not be biased correctly. Check Q2 collector voltage. If it's at 9V, Q2 isn't conducting — check Q2 base voltage and the bias path from Q1's collector.
- The feedback resistor (Rb) might be wrong value or disconnected.

**Gated, "sputtery" fuzz (sound cuts off abruptly when notes decay):**
- Q2 collector voltage is too far from Vcc/2. The transistor is biased too close to cutoff. Try a lower value for Rc2 (e.g., 4.7k instead of 8.2k) or select a transistor with different hFE.

**Oscillation (squealing):**
- Add a 100nF cap across the power rails close to the circuit. The Fuzz Face can oscillate if the power supply impedance is too high (weak battery or long wires).
- Shorten all wires. Keep the circuit compact.

**Motorboating (low-frequency putt-putt-putt oscillation):**
- This is a power supply feedback problem. The circuit's current draw varies with the audio signal, which causes the supply voltage to fluctuate. If those fluctuations feed back through the circuit, you get a low-frequency oscillation. Fix: increase the power supply filter cap (try 220uF or 470uF across the rails). Also try powering from a bench supply instead of a battery — if motorboating stops, the battery's internal resistance is too high (replace the battery).

**Temperature sensitivity (tone drifts as circuit warms up):**
- The Fuzz Face is somewhat temperature-sensitive because the transistor bias point drifts with temperature. Q2's collector voltage may shift by 0.5-1V as the circuit warms up over the first few minutes. This is normal and is part of the "vintage" character. In a final build, the enclosed environment will stabilize after a few minutes of playing. If it bothers you, increase the feedback resistor (Rb) to increase the negative feedback, which makes the bias point more temperature-stable.

### 2.8 Understanding the Bias Point in Depth

The Fuzz Face's magic — and its frustration — comes from the sensitivity of Q2's bias point. Let's dig into why this matters so much.

**What Q2's collector voltage tells you:**

| Q2 Collector Voltage | Behavior | Sound |
|---|---|---|
| 0-1V | Deep saturation | Gated, sputtery, "dying battery" |
| 1-3V | Moderate saturation | Heavy compression, thick fuzz |
| 3-5V | Active region center | Full fuzz, good dynamics, classic tone |
| 5-7V | Near cutoff | Lighter fuzz, more dynamics, cleans up well |
| 7-9V | Cutoff / barely conducting | Very light effect, thin, possibly gated |

The "sweet spot" for most players is 4-5V — center of the active region, with equal room for the signal to swing both up (toward 9V) and down (toward 0V). But this is a preference. Some builders deliberately bias Q2 lower (2-3V) for a thicker, more compressed fuzz, accepting the trade-off of more gating.

**The relationship between hFE and bias:**
Higher hFE transistors draw more collector current (for the same base voltage), which means more voltage drop across Rc2, which means a *lower* collector voltage. If you plug in a transistor with hFE=500, Q2's collector might sit at 1V — way too low for good fuzz. You'd need to *decrease* Rc2 to compensate (less resistance = less voltage drop = higher collector voltage).

Conversely, a low-hFE transistor (say, hFE=80) draws less current, less voltage drop across Rc2, and the collector sits at maybe 7V. You'd *increase* Rc2 to pull the bias down.

This is why the Fuzz Face is a "hand-selected transistor" circuit. The component values assume a specific hFE range, and transistors outside that range need adjusted resistors. In contrast, the Tube Screamer ([Chapter 9](09-build-overdrive-distortion.md)) uses an op-amp where the gain depends only on external resistors, making it immune to this type of variation.

**A trimmer mod for easy biasing:** Replace Rc2 (8.2k) with a 20k or 25k trimmer pot. Now you can adjust Q2's collector voltage to exactly the value you want, regardless of which transistors you're using. Set it to 4.5V for the "textbook" bias, or dial it up or down to taste. This is a common mod that makes the Fuzz Face much more practical for variable transistor selections.

---

## 3. Measuring Your Build

### 3.1 Multimeter Measurements

Now that both circuits are working, let's take some measurements that connect the theory to practice.

**Gain measurement (boost circuit):**
1. Set your multimeter to AC voltage.
2. Play a steady, consistent note (or use a signal generator if you have one — a phone app playing a sine wave works).
3. Measure the AC voltage at the input (row 5 of the boost). Let's say it reads 50mV.
4. Measure the AC voltage at the output (drain, row 8). Let's say it reads 300mV.
5. Gain = 300/50 = 6, or about 15.6 dB. That matches our expectation for a JFET boost with a bypassed source resistor.

**Bias current estimation (fuzz circuit):**
1. Measure the DC voltage across Rc2 (between +9V rail and Q2 collector).
2. If Rc2 = 8.2k and the voltage across it is 4V, then by Ohm's law: I = V/R = 4V / 8.2k = 0.49 mA. That's Q2's collector current.
3. This current, combined with the transistor's hFE, tells you about the operating point.

### 3.2 Audio Probe Comparative Listening

Here's an exercise that builds your ear:

1. Build both circuits (boost and fuzz) on the same breadboard.
2. Connect the boost output to the fuzz input (boost → fuzz cascade).
3. Use the audio probe at each stage:
   - Guitar input (clean)
   - After boost (louder, clean)
   - After Q1 of fuzz (louder, starting to clip)
   - After Q2 of fuzz (full fuzz)

Listen to how the signal transforms at each stage. This is the signal chain in action — each stage adds gain and changes the character. This multi-stage cascade is exactly how amplifiers work, just on a smaller scale. The same concept scales up to [Part 3](../part-3-amplifiers/18-how-amplifiers-work.md), where multiple gain stages drive a power amp.

---

## 4. Component Substitution Experiments

### 4.1 Three Transistors, Three Sounds

This is the experiment that makes the Fuzz Face circuit come alive. You're going to swap the transistors and hear how each one changes the character of the fuzz. This isn't subtle — you'll hear real, meaningful differences.

**The candidates:**

| Transistor | Typical hFE | Character |
|---|---|---|
| 2N3904 | 100-300 | Clean, bright, moderate gain |
| 2N2222 | 100-300 | Similar to 2N3904, slightly different voicing |
| BC108 | 110-800 | Higher gain available, "classic" silicon fuzz |

**The procedure:**
1. Start with your working Fuzz Face using one pair of transistors.
2. Note the Q2 collector voltage and listen to the fuzz character. Record your impressions.
3. Power off. Swap both Q1 and Q2 for the next transistor type.
4. Power on. Measure Q2 collector voltage again. If it's shifted significantly (below 2V or above 7V), you may need to adjust Rc2 to re-center the bias. Listen to the fuzz character. Record your impressions.
5. Repeat for the third transistor type.

**What you'll likely notice:**

- **2N3904:** The most "polite" fuzz of the three. Moderate gain means less sustain but more note definition. Brighter character. Cleans up well with the guitar volume.

- **2N2222:** Very similar to the 2N3904 in many cases (they have similar specifications). You might hear subtle differences in the high-frequency character due to different internal capacitances.

- **BC108:** If you get one with high hFE (300+), it produces a thicker, more sustained fuzz with more compression. The higher gain means the circuit clips harder and sustains longer. Some BC108s will require a different Rc2 value to keep the bias centered.

**Why they sound different:** The primary factors are:
1. **hFE (current gain):** Higher hFE means more gain, which means harder clipping. More sustain, more compression, thicker fuzz.
2. **fT (transition frequency):** The frequency at which the transistor's gain drops to 1. Higher fT means the transistor amplifies higher frequencies better, which translates to a brighter or more present fuzz.
3. **Internal capacitances (Cbc, Cbe):** These affect the high-frequency rolloff (Miller effect). Different transistors have different capacitances, which subtly change the treble character.

### 4.2 Measuring hFE

If you have a multimeter with an hFE measurement function (many do — look for a socket labeled "hFE" or "transistor test"):

1. Measure the hFE of each transistor before installing it.
2. Record the value alongside your listening impressions.
3. You'll start to correlate: "This 2N3904 with hFE=180 sounded cleaner, while that BC108 with hFE=450 sounded thicker and more compressed."

For the Fuzz Face, the sweet spot for Q2's hFE is generally between 100 and 300. Below 100, the fuzz sounds thin and gated. Above 400-500, it can get muddy and overly compressed. Q1 is less critical — values of 70-150 work well.

This is why vintage fuzz pedal builders hand-selected transistors. Now you can too.

> **What happens if...** you use mismatched transistors — say, a 2N3904 (hFE ~150) for Q1 and a BC108 (hFE ~400) for Q2? The first stage provides moderate amplification, and the second stage has very high gain, driving hard into clipping. The result is often a good-sounding combination: Q1 preserves some dynamics and note clarity, while Q2 provides the thick saturation. Many builders prefer mismatched pairs for this reason.

---

## 5. Power Supply Considerations

### 5.1 Battery vs Adapter

For breadboard prototyping, a 9V battery is the simplest power source. But batteries have limitations:

- **Voltage sag:** A fresh alkaline 9V battery actually outputs about 9.5V. As it discharges, it drops to 9V, then 8.5V, then lower. Guitar circuits are designed to work across this range, but the tone changes as the voltage drops — less headroom, earlier clipping, different bias points.
- **Internal resistance:** A fresh battery has low internal resistance (~1 ohm), but as it discharges, the internal resistance increases. High internal resistance means the supply voltage dips when the circuit draws current (during loud transients), causing a form of "sag" or "compression" that some players find musical.
- **Limited lifespan:** A typical pedal draws 5-20mA. A 9V battery has about 500mAh of capacity. At 10mA draw, the battery lasts about 50 hours. With multiple pedals or higher-current circuits (like the PT2399 delay), battery life drops significantly.

For final pedal builds, most players use a **DC power adapter** (a.k.a. "wall wart" or "power brick"). The standard is 9V DC, center-negative (the center pin of the barrel jack is negative, and the outer ring is positive). This is the Boss standard, and virtually all pedal power supplies follow it.

**Caution:** Some cheap adapters have significant ripple (AC noise on the DC output). This ripple can inject hum into your audio circuit. If you hear a 60 Hz or 120 Hz buzz that goes away when you switch to battery power, the adapter's ripple is the culprit. The fix: add more capacitance on the power rail (220uF or 470uF), or use a higher-quality regulated adapter.

### 5.2 Positive Ground vs Negative Ground

Most modern pedals use **negative ground** — the negative terminal of the battery connects to the circuit's ground (0V reference). But some classic circuits — notably the original germanium Fuzz Face and many vintage fuzzes — use **positive ground**, where the positive terminal is the ground reference and the circuit sees -9V as its supply.

Why does this matter? If you try to power a positive-ground pedal from the same power supply as your other negative-ground pedals, you'll create a short circuit. The grounds are at different potentials.

Our silicon Fuzz Face build uses NPN transistors with standard negative ground, so it's compatible with all modern power supplies. If you ever build a germanium PNP Fuzz Face (the original design), you'll need isolated power or a battery.

### 5.3 Decoupling: Why Every Circuit Needs It

We've mentioned decoupling caps (100nF ceramic + 100uF electrolytic on the power rails) in every build. Here's why they're non-negotiable:

When a circuit draws current from the power supply (which happens constantly as the audio signal drives the transistors or op-amp), the supply voltage dips momentarily due to the impedance of the battery, cables, and wiring. These dips propagate through the entire circuit as noise.

Decoupling caps act as tiny local reservoirs of charge. When the circuit needs a sudden burst of current, the caps provide it locally without pulling from the distant power supply. The 100uF electrolytic handles low-frequency supply noise (the "reservoir" function). The 100nF ceramic handles high-frequency noise (fast transient demands that the large electrolytic can't respond to quickly due to its internal inductance).

Without decoupling, high-gain circuits will oscillate, hum, or have audible noise. With proper decoupling, the circuit sees a clean, stable supply voltage. Always include both caps, as close to the active devices (transistors, op-amps) as possible.

---

## 6. "What Happens If..." Explorations

These are experiments to try on your working breadboard. Each one changes the fuzz character in a specific, predictable way. Try them one at a time, and always return to the stock circuit between experiments so you have a consistent reference point.

### 6.1 Change the Input Cap (Cin)

**Stock value:** 2.2uF

**Try:** 100nF (0.1uF)

**What happens:** The input coupling cap forms a high-pass filter with the circuit's input impedance. The stock 2.2uF is large enough to pass bass frequencies down to about 7 Hz — well below the guitar's range. Reducing it to 100nF raises the cutoff to about 160 Hz, which rolls off the low E string's fundamental and some bass content.

**How it sounds:** Thinner, tighter, more focused on midrange. Less "woofy" bass in the fuzz. Some players prefer this for single-note lead playing because it reduces the muddiness that fuzz can add to low notes.

**Try:** 10uF

**What happens:** Even more bass into the fuzz. With a 10uF input cap, bass frequencies pass through without any attenuation at all. 

**How it sounds:** Fatter, more bass-heavy. Can sound impressive on its own but might be too boomy in a band context. This is a case where the technically "better" choice (flat bass response) isn't necessarily the musically better choice.

### 6.2 Change the Collector Resistor (Rc2)

**Stock value:** 8.2 kohm

**Try:** 4.7 kohm

**What happens:** The collector resistor, combined with the transistor's collector current, determines the collector voltage and the gain. Lowering Rc2 from 8.2k to 4.7k raises the collector voltage (Q2 is less "loaded") and reduces the voltage gain. The signal clips less.

**How it sounds:** Less gain, more headroom. The fuzz is lighter, with more dynamics. Notes are less compressed. If the stock circuit sounded "gated" (cutting off abruptly), this fix might help — it re-centers the bias point.

**Try:** 15 kohm

**What happens:** More gain, less headroom. Q2's collector voltage drops, and the signal clips harder. More sustain, more compression.

**How it sounds:** Thicker, more saturated, more sustained. But if the collector voltage drops too low (below ~1V), the fuzz starts to sound "starved" — thin and sputtery, like a dying battery effect. Some players like this (it's actually a sought-after sound called "dying battery fuzz"), but it's not the classic Fuzz Face tone.

### 6.3 Change the Feedback Resistor (Rb)

**Stock value:** 100 kohm

**Try:** 47 kohm

**What happens:** More negative feedback from Q2's collector to Q1's base. This lowers the overall gain and stabilizes the bias point. The fuzz cleans up more, and the circuit becomes less sensitive to transistor variations.

**How it sounds:** Less wild, more controlled fuzz. Better cleanup with guitar volume. Some of the "raw" character is tamed. More predictable.

**Try:** 220 kohm

**What happens:** Less negative feedback. Higher overall gain, more sensitivity to transistor parameters, wilder fuzz character.

**How it sounds:** More aggressive, less controlled, more "vintage" chaos. The fuzz is more touch-sensitive but also more prone to bias drift and temperature sensitivity.

### 6.4 Add an Output Filter

**Stock circuit has no tone control.** Many Fuzz Face builds add a simple tone control at the output:

```
  Q2 Collector ── Cout ── 10k resistor ──┬── Output
                                          |
                                        Cap (try 1nF to 22nF)
                                          |
                                         GND
```

This is a passive low-pass filter. The cap value sets the cutoff frequency: 1nF gives a cutoff around 16 kHz (barely audible), while 22nF gives a cutoff around 720 Hz (very dark). A 4.7nF cap gives a cutoff around 3.4 kHz, which tames the fizzy high-frequency harmonics while preserving the fundamental fuzz character.

**How it sounds:** The fizzy, scratchy top end is reduced. The fuzz sounds smoother, warmer, and more "vintage." A variable version (pot + cap) is essentially the tone control found in many commercial fuzz pedals.

> **What happens if...** you run the fuzz on a nearly dead 9V battery (say, 6V instead of 9V)? Less supply voltage means less headroom, earlier clipping, and a "starved" sound. The fuzz becomes gated and sputtery — notes die off abruptly instead of sustaining. This is the famous "dying battery" fuzz sound that some players (and some pedals, like the ZVex Fuzz Factory) deliberately seek. Electrically, Q2's collector swing is reduced, and the bias point shifts as the supply drops. You can simulate this by using a variable power supply instead of a battery.

---

## 7. Germanium vs Silicon: A Brief History and Comparison

### 7.1 Why the Fuzz Face Exists in Two Flavors

The original 1966 Fuzz Face used **PNP germanium transistors** (NKT275, AC128, or similar). Germanium transistors were the first commercial transistors and were the only option when the circuit was designed. When silicon transistors became widely available in the late 1960s, manufacturers switched — silicon was cheaper, more consistent, and more temperature-stable.

But the two types sound different, and the debate between germanium and silicon fuzz has raged for decades. Here's what's actually going on:

**Germanium transistors:**
- Lower gain (hFE typically 70-120)
- Softer clipping characteristics (the transistor enters saturation more gradually)
- Temperature-sensitive (the bias drifts as the transistor warms up, sometimes dramatically)
- Leakage current increases with temperature (can cause the bias to shift into gated territory on a hot day)
- PNP polarity (requires positive ground — incompatible with standard power supplies)
- Inconsistent between devices (even within the same batch)
- Sound description: "warm," "woolly," "organic," "vintage"

**Silicon transistors:**
- Higher gain available (hFE 100-500+)
- Sharper clipping (the transistor saturates more abruptly)
- Temperature-stable (consistent tone regardless of temperature)
- Minimal leakage current
- Available in NPN (standard negative ground) or PNP
- Consistent between devices (modern manufacturing)
- Sound description: "bright," "aggressive," "articulate," "cutting"

### 7.2 Can You Get Germanium Tone from Silicon?

Partially, yes. The "warmth" of germanium comes partly from the softer clipping and partly from the lower gain (which means less saturation and more dynamics). You can approximate the germanium character in a silicon circuit by:

1. **Selecting low-hFE silicon transistors** (around 80-120 instead of 300+)
2. **Adding a small emitter resistor** to Q2 (100-470 ohms) to reduce the gain and soften the clipping
3. **Increasing the feedback resistor** (Rb) to increase negative feedback, reducing the overall gain
4. **Using slightly larger coupling caps** (more bass, matching the germanium's frequency balance)

You won't get an exact replica of germanium tone because the underlying physics of the junctions are different. But you can get close enough that most listeners wouldn't tell the difference in a blind test.

### 7.3 What We Built and Why

We built the silicon NPN version because:
- Components are cheap and readily available
- The circuit is temperature-stable
- It uses standard negative ground (compatible with all power supplies)
- You can experiment with a wide range of hFE values easily
- The silicon Fuzz Face has its own classic legacy (Hendrix used both types)

If you want to explore germanium territory later, the circuit is the same topology with PNP transistors and reversed polarity. The [appendix on suppliers](../appendices/appendix-suppliers.md) lists sources for germanium transistors.

---

## 8. From Breadboard to Pedal

### 8.1 When to Move Off the Breadboard

Your breadboard fuzz and boost are working. You've experimented with component values. You've found the transistors and settings you like. Now what?

If you want to actually use these pedals (gigging, jamming, recording), you need to move to a permanent, enclosed build. Breadboards are great for prototyping but terrible for real use — components fall out, connections are intermittent, and there's no shielding against noise.

Your options, in order of increasing effort and quality:

1. **Stripboard (Veroboard):** Solder the circuit onto a copper-striped board. Cut traces where you need to break connections. Cheap, fast, suitable for one-off builds. Many pedal builders use this exclusively.

2. **Perfboard:** Similar to stripboard but with individual pads instead of strips. More wiring, more flexible layout.

3. **PCB:** Design and order a custom printed circuit board. Professional quality, repeatable, sellable. This is covered in [Part 5, Chapter 31](../part-5-pcb-professional/31-pcb-design-for-audio.md).

For now, keep your breadboard builds. We have more pedals to build in [Chapter 9](09-build-overdrive-distortion.md) and [Chapter 10](10-build-modulation-time-effects.md), and you can reuse the same breadboard by carefully removing the current circuit and building the next one.

### 8.2 Enclosure Considerations (Preview)

When you do move to a permanent build, you'll put the circuit in an aluminum enclosure (typically a Hammond 1590B or 125B). The enclosure provides:

- **Shielding:** The metal box blocks electromagnetic interference.
- **Durability:** Stomping on a breadboard doesn't end well.
- **Professional appearance:** It looks like a real pedal because it is one.

Enclosure drilling, finishing, and wiring are covered in [Part 5, Chapter 32](../part-5-pcb-professional/32-enclosure-design-fabrication.md). For now, focus on getting the circuit right. The enclosure is the easy part.

### 8.3 What You've Learned By Building

By completing these two builds, you've practiced:

- Translating a schematic to a breadboard layout
- Checking DC bias voltages to verify proper operation
- Using an audio probe to trace the signal through a multi-stage circuit
- Understanding how individual component values affect the sound
- Comparing different transistors in the same circuit
- Debugging common issues (biasing, orientation, connections)

These skills transfer directly to every build in this guide — and to any audio circuit you'll ever build. The specific circuits change, but the methodology is the same: understand the schematic, build it carefully, verify with measurements, and listen critically.

---

## Chapter Summary

- The **JFET clean boost** (2N5457, ~6 components) is the simplest useful pedal circuit. It amplifies the guitar signal without adding distortion, with gain set by the drain and source resistors. The source bypass capacitor controls whether the gain applies to all frequencies or just treble.

- The **Silicon Fuzz Face** (~10 components, 2 NPN transistors) is a two-stage common emitter amplifier with feedback. The Fuzz pot controls emitter degeneration on Q1 (more resistance = less fuzz). The feedback resistor from Q2 collector to Q1 base stabilizes the bias and enables volume-knob cleanup.

- **Q2's collector voltage** is the critical bias measurement for the Fuzz Face. It should be roughly half the supply voltage (4-5V for a 9V supply). If it's off, adjust Rc2 or try a transistor with different hFE.

- **Transistor hFE** dramatically affects fuzz character: lower hFE (100-200) gives a brighter, more dynamic fuzz; higher hFE (300+) gives a thicker, more compressed, more sustained fuzz. The "sweet spot" depends on personal preference, but 100-300 for Q2 is a good starting range.

- **Component substitution experiments** teach you more than theory alone: changing Cin affects bass content, changing Rc2 affects gain and headroom, changing Rb affects feedback and cleanup behavior, and adding an output filter tames high-frequency fizz.

- The **audio probe** traces the signal through each stage: clean guitar at the input → amplified at Q1 collector → fully fuzzed at Q2 collector → DC-blocked at the output. If the signal disappears at any stage, the problem is in that stage.

- **Estimated cost:** $30-50 total for both builds including components, breadboard, and connectors. Individual component cost is under $15 per pedal.

---

*Next: [Chapter 9 — Build: Overdrive and Distortion](09-build-overdrive-distortion.md), where we build a Tube Screamer clone and a RAT clone — and finally hear the difference between feedback and shunt clipping with our own ears.*
