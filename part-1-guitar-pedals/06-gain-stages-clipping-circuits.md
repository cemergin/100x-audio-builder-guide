<!--
  CHAPTER: 6
  TITLE: Gain Stages and Clipping Circuits
  PART: 1 — Guitar Pedals
  PREREQS: Chapter 5
  KEY_TOPICS: single-transistor gain stage, common emitter, op-amp gain stages, inverting/non-inverting, diode clipping networks, feedback vs shunt clipping, symmetrical vs asymmetrical clipping, frequency-dependent clipping, tone stacks, Fender/Marshall/Baxandall, SPICE simulation
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 6: Gain Stages and Clipping Circuits

> **Part 1 — Guitar Pedals** | Prerequisites: Chapter 5 | Difficulty: Intermediate

In the last chapter, you learned what effects *do* to sound — the categories, the concepts, the big picture. Now we're going to look inside the box and see *how* they do it. This chapter is about the fundamental circuits that appear in almost every guitar pedal: gain stages and clipping networks.

If Chapter 5 was the architectural overview, this chapter is the structural engineering. You'll learn why a common emitter transistor stage sounds different from an op-amp stage, why diodes in a feedback loop create a different distortion character than diodes to ground, and why the Tube Screamer has that mid-humpy character while the RAT sounds flat and aggressive. More importantly, you'll simulate these circuits yourself and *see* the differences — in waveforms, frequency response, and harmonic content.

By the end of this chapter, you'll be able to look at any pedal schematic and immediately identify: "That's a non-inverting gain stage with about 40 dB of gain, soft-clipping with silicon diodes in the feedback loop, feeding a modified Baxandall tone control." And you'll know exactly what it's going to sound like before you plug it in.

### In This Chapter
- Single-transistor gain stages (common emitter)
- Op-amp gain stages (inverting and non-inverting)
- Diode clipping networks: feedback loop vs shunt to ground
- Symmetrical vs asymmetrical clipping
- Frequency-dependent clipping and pre/de-emphasis
- Tone stacks: Fender, Marshall, and Baxandall
- SPICE simulation walkthroughs for key circuits

### Related Chapters
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) — Transistor and op-amp fundamentals
- [Ch 3: Simulation and Analysis Tools](../part-0-electronics/03-simulation-analysis-tools.md) — LTspice and Falstad basics
- [Ch 5: How Effects Shape Sound](05-how-effects-shape-sound.md) — The conceptual framework we're now implementing
- [Ch 7: From Schematic to Breadboard](07-schematic-to-breadboard.md) — Building these circuits physically
- [Ch 18: How Amplifiers Work](../part-3-amplifiers/18-how-amplifiers-work.md) — These same gain stage concepts at amp scale

---

## 1. Single-Transistor Gain Stages

### 1.1 The Common Emitter Configuration

The common emitter (CE) amplifier is the most fundamental gain stage in electronics and the heart of many classic pedal designs — including the Fuzz Face, Tone Bender, and Rangemaster. If you understand this one circuit deeply, you can read half the pedal schematics ever drawn.

Here's the basic topology:

```
        Vcc (+9V)
         |
         Rc (collector resistor)
         |
         ├──── Output (via coupling cap)
         |
  Input ──┤ (base)
  (via     |
  coupling Q1 (NPN transistor)
  cap)     |
         Re (emitter resistor)
         |
        GND
```

And the biasing network (which sets the DC operating point):

```
        Vcc (+9V)
         |
        R1 (top bias resistor)
         |
         ├──── Base of Q1
         |
        R2 (bottom bias resistor)
         |
        GND
```

**How it works:** The bias resistors (R1 and R2) set a DC voltage at the base, which establishes a DC current through the transistor. This "quiescent" current determines where on the transistor's characteristics the circuit operates — the **bias point** (also called the **Q-point** or **operating point**).

When an AC signal (your guitar) arrives at the base (through a coupling capacitor that blocks the DC bias voltage), it modulates the base current. Because of the transistor's current gain (hFE or beta), a small change in base current produces a large change in collector current, which creates a large voltage swing across the collector resistor Rc. That voltage swing is your amplified output.

### 1.2 Biasing: The Art of Setting the Operating Point

Getting the bias right is the single most important thing in a transistor gain stage. If the bias is wrong, the circuit either doesn't amplify, distorts asymmetrically, or oscillates.

**What "correct" bias means:** For a linear (clean) amplifier, you want the collector voltage to sit at roughly half the supply voltage (about 4.5V for a 9V supply). This gives the signal equal room to swing both up and down before clipping against the supply rail or ground.

**For a fuzz or distortion circuit:** You might deliberately mis-bias the transistor — setting the collector voltage higher or lower than center. This means the signal clips asymmetrically (it hits one rail before the other), producing even-order harmonics and a different distortion character.

The bias point depends on:
- **R1 and R2** (the bias divider): These set the base voltage
- **Re** (the emitter resistor): This provides DC feedback for stability
- **Rc** (the collector resistor): This, combined with the collector current, determines the collector voltage
- **hFE of the transistor**: This is the current gain — and it varies between individual transistors, even of the same type

That last point is critical. Two 2N3904 transistors can have hFE values anywhere from 100 to 300. If your circuit is sensitive to hFE, two "identical" builds will sound different. This is why vintage fuzz pedals are so variable — the Fuzz Face circuit is extremely sensitive to transistor hFE, and builders would hand-select transistors for the right sound.

### 1.3 Gain Calculation

The voltage gain of a common emitter stage (with emitter resistor Re) is approximately:

```
Av ≈ -Rc / Re
```

The minus sign means the output is *inverted* — when the input goes up, the output goes down. This doesn't matter for audio (your ear doesn't detect polarity inversion), but it matters when you're chaining stages or analyzing waveforms.

If Rc = 10 kohm and Re = 1 kohm, the gain is about -10 (20 dB).

**Bypassed emitter resistor:** Many designs place a capacitor across Re. At AC frequencies, this capacitor shorts out Re, effectively making Re = 0 for AC signals. This dramatically increases the gain (limited now only by the transistor's internal emitter resistance, re ≈ 26mV/Ic). But it also makes the gain dependent on temperature and transistor parameters, which is why the tone can be less predictable.

**Partially bypassed emitter:** Some designs put the bypass cap in series with a small resistor, giving maximum gain above a certain frequency but lower gain at low frequencies. This is a form of frequency-dependent gain — an EQ built into the gain stage itself.

### 1.4 Frequency Response

A single CE stage doesn't have flat frequency response. Several factors shape it:

- **Input coupling capacitor + base impedance:** Forms a high-pass filter. Below the cutoff frequency, bass is attenuated. Larger cap = more bass.
- **Output coupling capacitor + load impedance:** Another high-pass filter at the output.
- **Emitter bypass capacitor:** Determines the low-frequency rolloff of the gain boost. Small bypass cap = gain boost only at high frequencies. Large bypass cap = gain boost at all audio frequencies.
- **Miller capacitance:** The transistor's internal collector-base capacitance, amplified by the gain, creates an effective capacitance at the input that rolls off high frequencies. This is one reason single-transistor stages sound "warmer" than op-amp stages — they naturally roll off treble.

> **What happens if...** you increase the emitter bypass capacitor from 1uF to 47uF? The low-frequency gain increases. With 1uF and a 1 kohm emitter resistor, the bypass is effective above about 160 Hz — so the gain is lower for bass frequencies. With 47uF, the bypass is effective above about 3.4 Hz — essentially all audio frequencies get maximum gain. The pedal sounds bassier and higher-gain. This is a common mod for the Rangemaster treble booster circuit.

### 1.5 SPICE Simulation: Common Emitter Stage

Let's simulate a basic common emitter gain stage in LTspice. Open LTspice and create this circuit:

**Component values:**
- Q1: 2N3904 (NPN)
- R1 (Vcc to base): 470 kohm
- R2 (base to GND): 100 kohm
- Rc (Vcc to collector): 10 kohm
- Re (emitter to GND): 2.2 kohm
- Ce (across Re): 10uF electrolytic
- Cin (input coupling): 100nF
- Cout (output coupling): 100nF
- Vcc: 9V DC
- Input: AC voltage source, 100mV peak, 1 kHz

**Simulations to run:**

1. **DC Operating Point (.op):** Check the collector voltage. You should see approximately 3-5V — confirming the transistor is biased in the active region. If the collector is at 0V or 9V, the transistor is saturated or cut off — the biasing is wrong.

2. **Transient Analysis (.tran 10m):** Apply a 1 kHz, 100mV sine wave at the input. Look at the output waveform. You should see an amplified, inverted sine wave. Measure the peak-to-peak voltage of the output and divide by the input to get the gain. It should be roughly Rc/Re ≈ 10k/2.2k ≈ 4.5. (The bypass cap Ce increases this for AC.)

3. **AC Analysis (.ac dec 100 10 100k):** Sweep from 10 Hz to 100 kHz. Plot the output magnitude in dB. You'll see the frequency response: gain rolls off at low frequencies (due to coupling caps) and at high frequencies (due to Miller capacitance and transistor bandwidth). Note the midband gain.

4. **Now experiment:** Change Re from 2.2 kohm to 470 ohms. The gain increases dramatically. Look at the transient output — does it clip? Change Ce from 10uF to 100nF. Watch how the low-frequency gain drops while high-frequency gain stays the same. This is how you shape the frequency response of a gain stage.

**Detailed LTspice steps for those new to the tool:**

If this is your first LTspice simulation, here's the step-by-step:

1. Open LTspice. Go to File → New Schematic.
2. Place components: Click the component button (looks like an AND gate) or press "P" for passive components. Search for "npn" to find the 2N3904, "res" for resistors, "cap" for capacitors, "voltage" for voltage sources.
3. Wire them together: Press "W" to draw wires. Click on two component terminals to connect them.
4. Set values: Right-click on each component to edit its value.
5. Add ground: Place a ground symbol (press "G") and connect it to the bottom of your circuit.
6. For the input source: Right-click the voltage source and set it to `SINE(0 0.1 1000)` for a 100mV, 1kHz sine wave. (For the biased version: `SINE(0.7 0.1 1000)` where 0.7V is the approximate base bias voltage.)
7. Add the simulation command: Right-click empty space and choose "Edit Simulation Cmd." Select "Transient" and enter a stop time of 10m (10 milliseconds).
8. Run: Click the Run button (or press the play icon). A waveform viewer opens.
9. Click on a wire in the schematic to plot its voltage. Click on a component to plot current through it.

For the AC analysis, edit the simulation command and select "AC Analysis." Set the sweep type to "Decade," number of points per decade to 100, start frequency to 10, stop frequency to 100k. Change the input source to `AC 0.1` (instead of SINE). Run and plot the output — you'll see the frequency response in dB.

**What you should see in the AC analysis:** A roughly flat gain region (the "midband") from about 100 Hz to 20 kHz. Below 100 Hz, the gain rolls off (high-pass filter from the coupling caps). Above 20 kHz, the gain rolls off (transistor bandwidth and Miller capacitance). The midband gain should be roughly Rc/(Re || (1/jwCe)), which at frequencies where Ce is effective is approximately Rc × gm, where gm is the transistor's transconductance.

Don't worry if the numbers don't match exactly — component models in SPICE are more detailed than our simplified formulas, and the transistor's internal parameters add complexity. The point is to see the *trends*: how gain varies with frequency, how changing a component shifts those curves.

### 1.6 Two Transistors in Series: The Darlington Pair

Before we leave transistor stages, one more configuration worth knowing: the **Darlington pair**. Two transistors are connected so that the first transistor's emitter drives the second transistor's base. The combined current gain is hFE1 × hFE2 — which can be enormous (10,000 or more).

```
  Input ── Q1 base
            |
           Q1 emitter ── Q2 base
                           |
                          Q2 emitter ── Re ── GND
                           |
                          Q2 collector ── Rc ── Vcc
                           |
                          Output
```

Darlington pairs are used when you need very high input impedance and very high gain. Some fuzz and booster circuits use them. The MPSA13 is a Darlington transistor in a single package.

The downside: the two base-emitter junctions mean the minimum Vce is about 1.2V (two diode drops) instead of 0.6V. This reduces the available output swing and changes the clipping behavior.

---

## 2. Op-Amp Gain Stages

### 2.1 Why Op-Amps Changed Everything

Transistor gain stages are powerful but finicky. The gain depends on transistor parameters that vary between individual components. Biasing requires careful resistor selection. Frequency response is hard to control precisely.

Op-amps (operational amplifiers) solved all of these problems. An op-amp is an integrated circuit that provides very high gain (typically 100,000 or more), very high input impedance, and very low output impedance. By wrapping it in a **negative feedback** network (connecting the output back to the inverting input through resistors), you can set the gain precisely with just two resistors. The gain depends almost entirely on the external resistors, not on the op-amp's internal parameters.

For guitar pedals, the most common op-amps are:
- **JRC4558 (NJM4558):** The Tube Screamer chip. Dual op-amp, moderate bandwidth, slightly "soft" character.
- **TL072/TL071:** JFET-input op-amps. High input impedance, low noise, widely used.
- **LM741:** The classic — but noisy and slow by modern standards. Still found in vintage designs.
- **LM386:** A power amplifier IC, not a standard op-amp. Used for small amps and high-gain circuits.

### 2.2 Non-Inverting Configuration

```
                    Rf (feedback resistor)
         ┌────────/\/\/────────┐
         |                      |
  Input ─┤(+)                   |
         |      Op-Amp     (output)──── Output
    ┌────┤(-)                   |
    |    |                      |
    Rg   └──────────────────────┘
    |
   GND (or Vbias for single-supply)
```

**Gain formula:**

```
Av = 1 + (Rf / Rg)
```

If Rf = 100 kohm and Rg = 1 kohm, the gain is 1 + 100 = 101 (about 40 dB). If Rf = 0 (a wire), the gain is 1 — a voltage follower (buffer).

**Key properties:**
- Output is **in phase** with the input (non-inverting)
- **Very high input impedance** — essentially the op-amp's input impedance, typically 1 Mohm or more. Great for interfacing with guitar pickups.
- Gain is set precisely by Rf and Rg, independent of the op-amp's characteristics
- Gain is always at least 1 (you can't attenuate with a non-inverting stage)

This is the topology used in the Tube Screamer's gain stage. The "drive" pot varies Rf (actually a combination of a fixed resistor and a potentiometer), changing the gain and therefore the amount of distortion.

### 2.3 Inverting Configuration

```
         Rf (feedback resistor)
  ┌────/\/\/────────┐
  |                  |
  |              ┌───┤
  |              |   |
  Input ──Rin──┤(-) Op-Amp (output)──── Output
               |   |
          ┌────┤(+)|
          |    └───┘
         Vbias (Vcc/2 for single supply)
```

**Gain formula:**

```
Av = -(Rf / Rin)
```

If Rf = 100 kohm and Rin = 10 kohm, the gain is -10 (inverting, about 20 dB).

**Key properties:**
- Output is **inverted** relative to input
- Input impedance is approximately equal to Rin (not the op-amp's internal impedance) — this can be a drawback if Rin is low
- Gain can be less than 1 (an attenuator) if Rf < Rin
- The inverting input is a **virtual ground** — it's held at the same voltage as the non-inverting input by the feedback

The RAT distortion pedal uses an inverting op-amp gain stage. The "distortion" pot varies the feedback resistance Rf, changing the gain.

### 2.4 Single-Supply Operation

Op-amps naturally want a dual power supply (positive and negative, like +9V and -9V). But guitar pedals typically run from a single 9V battery. The solution: create a **virtual ground** at half the supply voltage.

```
        +9V
         |
        R (typically 10k-100k)
         |
         ├──── Vbias (+4.5V)
         |
        R (same value)
         |
        GND
```

A voltage divider creates a +4.5V reference. A bypass capacitor (10-100uF) on this node provides a low-impedance AC ground. The op-amp's non-inverting input is biased to this voltage, and the output swings above and below 4.5V — effectively using the range from 0V to 9V as if it were -4.5V to +4.5V.

All coupling capacitors in the circuit block this 4.5V DC bias and pass only the AC audio signal.

### 2.5 Bandwidth and Slew Rate

Op-amps aren't perfect, and their limitations matter for audio:

- **Gain-bandwidth product (GBW):** The product of gain and bandwidth is approximately constant. A JRC4558 has a GBW of about 3 MHz. At a gain of 100, the bandwidth is only 30 kHz. At a gain of 1000, the bandwidth drops to 3 kHz — potentially audible high-frequency rolloff. For guitar pedals, this is usually acceptable (you rarely need response above 10 kHz), but it contributes to the "soft" character of high-gain op-amp circuits.

- **Slew rate:** The maximum rate at which the output voltage can change, measured in V/microsecond. A low slew rate means the op-amp can't follow fast transients faithfully — it "rounds off" sharp edges. The JRC4558 has a slew rate of about 1 V/us, while a TL072 has about 13 V/us. This difference contributes to the audible character of each chip.

> **What happens if...** you replace the JRC4558 in a Tube Screamer with a TL072? The TL072 has a faster slew rate and higher bandwidth. The result is slightly more high-frequency content in the distortion — some players describe it as "brighter" or "crisper." Neither is better or worse; it's a design choice. And now you know the electrical reason for the difference.

---

## 3. Diode Clipping Networks

### 3.1 Feedback Clipping (Tube Screamer Topology)

This is the defining circuit of the Tube Screamer and countless derivatives. Clipping diodes are placed in the **feedback loop** of the op-amp, in parallel with the feedback resistor:

```
           Rf
  ┌───────/\/\/────────┐
  |                     |
  |    D1 →   D2 ←     |
  ├────|>|────|<|───────┤
  |                     |
  ├─────────────────(output)
  |                     |
  Input ──Rin──┤(-)  Op-Amp
               |
          ┌────┤(+)
          |
         Vbias
```

**How it works:** When the output signal is small, the diodes don't conduct (they need about 0.6V forward voltage for silicon). The gain is set normally by Rf/Rin. As the output signal grows and exceeds the diode forward voltage, the diodes begin to conduct, effectively reducing the feedback resistance. This reduces the gain, which limits how large the output can get.

The key insight: the clipping is **gradual**. As the signal increases, the diodes conduct more and more, progressively reducing the gain. This creates **soft clipping** — the waveform peaks are gently rounded rather than sharply chopped.

**Why it sounds the way it does:**
- Smooth, compressed overdrive
- The onset of distortion is gradual — play softly and it's clean, play harder and it breaks up
- Dynamic response — your playing dynamics control the amount of distortion
- Lower harmonic content compared to hard clipping

### 3.2 Shunt Clipping (RAT Topology)

In this topology, the op-amp first amplifies the signal cleanly, then diodes connected to ground clip the amplified signal:

```
  Input ──┤ Op-Amp (high gain) ├──┬──── Output
                                   |
                              D1 → | ← D2
                              |>|──┤──|<|
                                   |
                                  GND
```

**How it works:** The op-amp amplifies the signal with high gain (set by its feedback resistors, no diodes in the feedback loop). The amplified signal then encounters diodes connected to ground. When the signal exceeds the diode forward voltage, the diodes conduct and clamp the signal — any voltage above ~0.6V (for silicon) is shunted to ground.

The key difference: the clipping is **abrupt**. The signal ramps up cleanly, then hits the diode threshold and is chopped flat. This creates **hard clipping** — the waveform peaks are flattened sharply.

**Why it sounds the way it does:**
- Aggressive, "in your face" distortion
- Less dynamic — once you're past the clipping threshold, more input just means the same flat-topped output
- Rich in higher-order harmonics due to the sharp transitions
- More "buzz" and "grind" in the character

### 3.3 Comparing the Two Topologies

| Characteristic | Feedback Clipping (TS) | Shunt Clipping (RAT) |
|---|---|---|
| Clipping character | Soft (gradual) | Hard (abrupt) |
| Dynamic response | High (clean up with volume) | Lower (more on/off) |
| Harmonic content | Lower order, fewer harmonics | Higher order, more harmonics |
| Output level | Self-limiting (gain reduces) | Clamped at diode voltage |
| Noise | Lower (gain reduces in clipping) | Higher (full gain always, diodes add noise) |
| Typical sound | Smooth overdrive | Aggressive distortion |

### 3.4 SPICE Simulation: Feedback vs Shunt Clipping

Let's simulate both topologies side by side in LTspice. This is one of the most revealing simulations you can do for pedal design.

**Circuit 1: Feedback Clipping (Tube Screamer style)**

Build a non-inverting op-amp stage:
- Op-amp: Use an ideal op-amp or a JRC4558 model
- Vcc: +9V, Vbias: +4.5V (resistor divider with bypass cap)
- Rin (non-inverting input to Vbias): 10 kohm
- Rf (feedback): 500 kohm (represents the TS drive pot at maximum)
- Rg (to Vbias): 4.7 kohm
- D1, D2: 1N4148 silicon diodes, anti-parallel, across Rf
- Cin: 47nF input coupling cap
- Input: 1 kHz sine wave, sweep amplitude from 10mV to 200mV

**Circuit 2: Shunt Clipping (RAT style)**

Build an inverting op-amp stage:
- Same op-amp and power supply
- Rin: 1 kohm
- Rf: 100 kohm (gain of -100)
- D1, D2: 1N4148, anti-parallel from output to ground
- Same input conditions

**What to observe:**

1. **Transient analysis (.tran 5m):** Set the input to 100mV, 1 kHz. Look at the output waveforms of both circuits. The feedback-clipped output should have smoothly rounded peaks. The shunt-clipped output should have flat-topped peaks that look like the tops were sliced off with a straight edge.

2. **Harmonic content:** Run a .four analysis (Fourier analysis at 1 kHz) on both outputs. Compare the harmonic amplitudes. The shunt-clipped circuit will show significantly more energy in the 3rd, 5th, and higher harmonics.

3. **Dynamic response:** Sweep the input amplitude from 10mV to 200mV (use a .step param or run multiple transient analyses). Watch how each circuit responds:
   - Feedback clipping: Output amplitude increases gradually, then compresses. The clipping onset is smooth.
   - Shunt clipping: Output amplitude increases linearly until it hits the diode threshold, then stays flat. The clipping onset is abrupt.

4. **Try different diodes:** Replace the 1N4148s with:
   - 1N34A germanium diodes (Vf ≈ 0.3V): Earlier clipping, softer knee
   - LEDs (Vf ≈ 1.8V): Later clipping, more headroom before distortion
   - One silicon + one LED (asymmetric): Different clipping on positive vs negative half — watch for even harmonics in the Fourier analysis

---

## 4. Symmetrical vs Asymmetrical Clipping

### 4.1 The Math of Harmonic Generation

When you clip a waveform symmetrically (both halves clipped equally), the resulting waveform has **odd symmetry** — flipping it upside down produces the same shape. Fourier analysis tells us that odd-symmetric waveforms contain only odd harmonics (3rd, 5th, 7th...).

When you clip asymmetrically (one half clipped more than the other), the waveform loses its odd symmetry. Now it contains both even and odd harmonics. The greater the asymmetry, the more prominent the even harmonics.

This is not just theory — it's directly audible:
- **Odd harmonics only:** Brighter, edgier, more "present." Think square wave buzziness.
- **Even + odd harmonics:** Warmer, fuller, more "musical." Often described as "tube-like" because tube amps naturally produce asymmetric clipping (the positive and negative halves of the tube's transfer function are different).

### 4.2 Creating Asymmetric Clipping

Several techniques:

**Different diode types in anti-parallel:**
```
  D1 (silicon, Vf=0.6V) →
  ──|>|──┬──|<|──
         |       D2 (germanium, Vf=0.3V) ←
```
Positive half clips at 0.6V, negative half clips at 0.3V. Asymmetric.

**Different number of diodes in each direction:**
```
  2x silicon →
  ──|>|──|>|──┬──|<|──
              |       1x silicon ←
```
Positive half clips at ~1.2V (two diode drops), negative half clips at ~0.6V (one diode drop). The Klon Centaur uses a variation of this (one germanium vs two silicon).

**Single diode:**
```
  ──|>|──┬──
         |
        GND
```
Only one polarity is clipped. The other passes freely. Extreme asymmetry.

### 4.3 The Klon Centaur's Clever Trick

The Klon Centaur uses a unique clipping arrangement: a germanium diode in one direction and a pair of silicon diodes in the other. But here's the clever part — the clipping section is blended with the clean signal using a dual-ganged pot. At low drive settings, you mostly hear the clean signal with just a touch of clipping. At high drive settings, the clipped signal dominates.

This "clean blend" architecture is why the Klon sounds open and transparent — it preserves the unclipped dynamics underneath the distortion. It's an insight you can apply to your own designs: mixing clean and clipped signals is a powerful technique.

---

## 5. Frequency-Dependent Clipping

### 5.1 Pre-Emphasis and De-Emphasis

This is the secret sauce of the Tube Screamer — and once you understand it, you'll see it everywhere.

**Pre-emphasis:** Boosting certain frequencies *before* the clipping stage. The Tube Screamer rolls off bass before the clipping diodes, so the midrange and treble hit the diodes harder while the bass stays relatively clean. This is why the TS sounds "mid-humpy" — the mids are distorted more than the bass.

**De-emphasis:** Cutting certain frequencies *after* the clipping stage. The Tube Screamer's tone control is a low-pass filter after the clipping, rolling off the harsh high-frequency harmonics created by the distortion.

The combination: bass is rolled off before clipping (so bass notes stay clean and tight), mids are amplified and clipped (creating the characteristic crunch), and harsh treble harmonics are rolled off after clipping (keeping it smooth). It's a deliberate frequency-shaping strategy, not an accident.

### 5.2 The Tube Screamer's Frequency Shaping

Let's trace the frequency-dependent behavior through the TS circuit:

1. **Input buffer:** Unity gain buffer with high input impedance. Flat frequency response. Sets up a low-impedance drive for the next stage.

2. **Input high-pass filter:** The coupling cap (0.047uF) and the resistor to the virtual ground form a high-pass filter with a cutoff around 720 Hz. Bass below this frequency is attenuated before it reaches the clipping stage.

3. **Gain/clipping stage:** Non-inverting op-amp with diodes in feedback. But note: the feedback network isn't just a resistor and diodes. There's a capacitor (0.047uF) in series with the drive pot. This cap, combined with the drive resistance, creates a frequency-dependent gain: low frequencies see less gain than high frequencies. At full drive, the gain at 1 kHz might be 100x, while the gain at 100 Hz might be only 10x.

4. **Tone control:** A simple low-pass filter with a variable cutoff. Turning the tone down rolls off treble; turning it up lets everything through.

5. **Output buffer:** Another unity gain buffer driving the output jack at low impedance.

The total effect: a mid-focused distortion with tight bass and controllable treble. Every component value was chosen to create this specific frequency balance.

### 5.3 Frequency Shaping in the RAT

The RAT takes a different approach. Its gain stage is relatively flat across frequencies (less frequency-dependent pre-emphasis). The "filter" knob is a low-pass filter *after* the clipping diodes that controls how much high-frequency harmonic content reaches the output.

At full filter (counterclockwise), the RAT sounds dark and wooly — most of the harmonics are rolled off. At minimum filter (clockwise), it sounds bright and aggressive — all the distortion harmonics come through. The filter control is essentially controlling the harshness of the distortion.

This gives the RAT a different *type* of tonal control than the Tube Screamer. The TS shapes the frequency content *before* clipping (changing what gets distorted). The RAT shapes it *after* clipping (changing what you hear of the distortion).

> **What happens if...** you add a 0.047uF capacitor in series with the input resistor of a RAT clone? You've just added pre-emphasis — the RAT will now roll off bass before clipping, similar to a Tube Screamer. The bass will be tighter, but you'll lose some of the RAT's characteristic "wall of sound" bass distortion. This is a real mod that some builders do, and now you understand exactly what it does and why.

---

## 6. Tone Stacks

### 6.1 What Is a Tone Stack?

A **tone stack** is a passive filter network that shapes the frequency response using potentiometers, resistors, and capacitors. The name comes from amplifier design, where these circuits are literally "stacked" in the signal path.

Most guitar pedals use simple tone controls (a single pot and cap forming a variable low-pass filter), but amplifiers — and some more sophisticated pedals — use full tone stacks with bass, mid, and treble controls. Understanding these circuits is important because they appear in amp-in-a-box pedals, parametric EQ pedals, and any design where you want more tonal control than a single knob provides.

All passive tone stacks have one thing in common: **insertion loss**. Because they're made of passive components (no gain), they can only cut — never boost. A typical tone stack attenuates the signal by 10-20 dB even with all knobs at maximum. This is why tone stacks are usually followed by a "recovery" gain stage to bring the signal back up to useful levels.

### 6.2 The Fender Tone Stack

The Fender tone stack (used in the Twin Reverb, Deluxe Reverb, and many other Fender amps) is the most common tone stack in guitar amplification. It uses three controls: Treble, Bass, and Mid.

```
  Input ──┬── C1 ──┬──── Treble pot ────┬── Output
          |        |                     |
          |        R1                    |
          |        |                     |
          |        ├── Mid pot ──── C3 ──┘
          |        |
          └── Bass pot ──── C2 ──┬── GND
                                  |
                                 R2
                                  |
                                 GND
```

(Simplified — the actual implementation varies between Fender models.)

**Character:** The Fender tone stack has a distinctive **mid scoop** — even with all controls at noon (center), it attenuates midrange frequencies by about 10-15 dB relative to bass and treble. This is the "Fender clean sound" — scooped mids, sparkly highs, full lows.

**Interaction:** The three controls interact significantly. Changing the bass control affects the mid response. Changing the treble affects the mid. This is because they share a common signal path and ground reference. It's not three independent filters; it's one interconnected network.

### 6.3 The Marshall Tone Stack

The Marshall tone stack is actually a derivative of the Fender design — Jim Marshall and Ken Bran started with the Fender Bassman circuit and modified it. The component values are different, giving the Marshall a different frequency balance:

- Less mid scoop than Fender (the mid control is more effective)
- More emphasis on upper-mid and lower-treble frequencies
- The "presence" of Marshalls comes partly from this tonal balance

The topology is essentially the same as the Fender stack, but with different resistor and capacitor values that shift the center frequencies and change the interaction between controls.

### 6.4 The Baxandall Tone Control

The **Baxandall tone control** (named after Peter Baxandall, who published it in 1952) takes a fundamentally different approach. Instead of the mid-scooped Fender/Marshall topology, it uses an op-amp-based active circuit with **shelving** EQ for bass and treble.

```
  Input ──┤ Bass network ├──┤(-) Op-Amp ├──┤ Treble network ├── Output
                             |          |
                             └──────────┘
                              (feedback)
```

**Character:** Because it's an active circuit (built around an op-amp), the Baxandall can **boost** as well as cut. With the controls at center, the response is roughly flat — no inherent mid scoop. Bass and treble can be independently boosted or cut by 10-15 dB.

**Used in:** Many hi-fi amplifiers, the Boss SD-1 (modified version), the Blackstar HT series, and many pedals that want a more "neutral" or "hi-fi" tonal character.

**Advantages over Fender/Marshall stacks:**
- Flat response at center position (no insertion loss when flat)
- Independent bass and treble control (minimal interaction)
- Can boost frequencies, not just cut
- Predictable, well-behaved frequency response

**Disadvantages:**
- Requires an op-amp (active, needs power)
- Doesn't have the interactive, "musical" interaction of the Fender stack that many guitarists love
- Some players find it too clinical or hi-fi sounding

### 6.5 SPICE Simulation: Comparing Tone Stacks

This is a fantastic simulation to run because you can *see* the difference between tone stack topologies.

**Setup:** In LTspice, build a Fender-style tone stack and a Baxandall tone stack. Use an AC analysis (.ac dec 200 20 20k) to sweep from 20 Hz to 20 kHz.

**Fender tone stack (approximate values from Fender Twin Reverb):**
- C1 (treble cap): 250pF
- C2 (bass cap): 100nF
- C3 (mid cap): 22nF
- R1 (slope resistor): 100 kohm
- R2 (ground reference): 56 kohm
- Treble pot: 250 kohm linear
- Bass pot: 250 kohm linear
- Mid pot: 25 kohm linear (reverse audio taper in actual amp)

**What to look for in the simulation:**

1. Set all pots to 50% (center). Plot the frequency response. You'll see the characteristic mid scoop — a dip of 10-15 dB in the 300 Hz to 1 kHz range, with relatively higher response at bass and treble.

2. Now sweep the bass pot from 0% to 100% while keeping the others at center. Watch how the bass response changes — but also notice how the mid response shifts. This is the interaction effect.

3. Do the same for the mid and treble controls. Note that the mid control has a relatively narrow range of effect and primarily affects the depth of the mid scoop.

4. Compare with the Baxandall: set both tone stacks to "flat" (all at center). The Baxandall should be roughly flat (0 dB across the spectrum, minus a few dB of insertion loss from the resistors). The Fender will show its mid scoop. This visual comparison explains why Fender amps sound "scooped" even with all tone controls at noon.

**Experiment:** Try changing the Fender tone stack's mid cap (C3) from 22nF to 47nF. The mid scoop shifts to a lower frequency. This is a common mod in Marshall-style amps to change the voicing. Every component value in a tone stack corresponds to a specific frequency — change the cap, change the frequency.

---

## 7. How Component Values Change Character

### 7.1 The Philosophy: One Component at a Time

Here's a principle that will serve you throughout this guide: **change one component at a time, and listen** (or simulate). If you change three things at once and it sounds different, you don't know which change caused it.

In SPICE simulation, this is trivially easy — duplicate the circuit, change one value, and compare the two outputs. On a breadboard, it's almost as easy — just swap the component. This is one of the great advantages of breadboard prototyping, which we'll explore in [Chapter 7](07-schematic-to-breadboard.md).

### 7.2 A Component Value Cheat Sheet

Here's a quick reference for how changing specific components affects the sound of a typical pedal circuit. These are generalizations — the exact effect depends on the circuit context — but they hold true more often than not:

| Component | Increase Value | Decrease Value |
|---|---|---|
| **Input coupling cap** | More bass into circuit | Less bass (thinner) |
| **Output coupling cap** | More bass to output | Less bass |
| **Emitter bypass cap** | More low-freq gain (bassier) | Less low-freq gain (brighter) |
| **Feedback resistor (Rf)** | More gain, more distortion | Less gain, cleaner |
| **Gain resistor (Rg)** | Less gain, cleaner | More gain, more distortion |
| **Collector resistor (Rc)** | Higher gain, less headroom | Lower gain, more headroom |
| **Emitter resistor (Re)** | Lower gain, more stable | Higher gain, less stable |
| **Tone cap (in RC filter)** | Lower cutoff freq, darker | Higher cutoff freq, brighter |
| **Tone resistor** | Higher cutoff freq, brighter | Lower cutoff freq, darker |
| **Clipping diode (higher Vf)** | More headroom, less clipping | More clipping, more distortion |

### 7.3 A Practical Exercise: Modify a Tube Screamer

In LTspice (or on a breadboard, once you've built one in [Chapter 9](09-build-overdrive-distortion.md)), try these modifications to a Tube Screamer circuit and note the results:

1. **Change the input coupling cap (C1) from 0.047uF to 0.47uF:** The bass cutoff frequency before the clipping stage drops from ~720 Hz to ~72 Hz. Much more bass enters the clipping stage. The pedal sounds fatter but potentially muddier.

2. **Change the clipping diodes from 1N4148 to LEDs:** Clipping threshold jumps from ~0.6V to ~1.8V. The pedal has much more clean headroom, clips later, and sounds more "open" and less compressed. This is the basis of the "LED mod."

3. **Increase the drive pot range from 500k to 1M:** Maximum gain increases. More distortion available at full drive. The tone may become more compressed and saturated.

4. **Change the tone cap from 0.22uF to 0.1uF:** The tone control's range shifts. Less high-frequency rolloff available, so the darkest setting is brighter than stock.

5. **Add asymmetric clipping (1N4148 + 1N34A):** One silicon diode (Vf ~0.6V) and one germanium diode (Vf ~0.3V). The positive and negative halves clip at different thresholds. The waveform becomes asymmetric, adding even harmonics. The distortion sounds warmer and less buzzy.

Each of these mods is a single component change, and each has a clear, predictable effect on the circuit's behavior. That predictability is the reward for understanding the underlying circuit theory.

---

## 8. Design Patterns and Building Blocks

### 8.1 The Universal Pedal Architecture

Almost every guitar pedal follows this pattern:

```
  Input Jack
    → Input Buffer (high Z in, low Z out)
      → Gain/Effect Stage (the core of the pedal)
        → Tone Control (frequency shaping)
          → Volume Control (output level)
            → Output Buffer (low Z out to drive cable)
  Output Jack
```

Some pedals omit stages (a simple fuzz has no buffer or tone control). Some add stages (a multi-band EQ has several filter stages). But this basic architecture is the template.

### 8.2 Common Sub-Circuits You'll See Everywhere

**Voltage divider bias supply:**
```
  9V ── 10k ──┬── 10k ── GND
              |
             10-100uF cap to GND
              |
              Vbias (4.5V)
```

**Input protection (against phantom power or static):**
```
  Input ──┬── 1M resistor ── Vbias (bleeds DC to the bias voltage)
          |
         Circuit input
```

**LED indicator with current-limiting resistor:**
```
  9V ── 4.7k ── LED ── GND (through switching circuit)
```

**Power supply decoupling:**
```
  9V ──┬── 100uF electrolytic ──┬── 100nF ceramic ── GND
       |                         |
      Vcc to circuit            Vcc to circuit
```

The large electrolytic handles low-frequency supply noise. The small ceramic handles high-frequency noise. Together, they provide a clean, stable supply to the circuit. Every audio circuit should have both.

### 8.3 Cascading Gain Stages

Many pedals (and virtually all amplifiers) use multiple gain stages in series. Understanding how stages interact is crucial for designing more complex circuits.

**Two stages in series:**
```
  Input → [Stage 1: Gain = 10] → [Stage 2: Gain = 10] → Output
                                         Total gain = 100 (40 dB)
```

But the stages don't simply multiply in isolation. The output impedance of Stage 1 interacts with the input impedance of Stage 2. If Stage 1 has a high output impedance and Stage 2 has a low input impedance, you lose signal at the junction. This is why the Tube Screamer uses a buffer between the input and the clipping stage — the buffer's low output impedance drives the clipping stage without signal loss.

**Interstage coupling:** The coupling capacitor between stages forms a high-pass filter with the input impedance of the next stage. If the cap is too small, you lose bass between stages. If it's plenty large, the stages couple fully.

**Where to clip in a multi-stage design:** You have choices:
- Clip early (after stage 1): The clipped signal is then amplified further. This adds "texture" to the distortion — the second stage's frequency response shapes the harmonics created by the first stage's clipping.
- Clip late (after stage 2): The first stage adds gain cleanly, and all clipping happens at the end. This produces a more "straightforward" distortion character.
- Clip at both stages: Maximum saturation and harmonic complexity. This is essentially what the Fuzz Face does — both transistors clip.

The choice of where to place clipping in a multi-stage architecture is one of the most powerful design decisions available to you. It's how pedal designers create unique voices from the same basic building blocks.

### 8.4 Op-Amp Substitution: A Listening Guide

When you swap op-amps in a pedal, you're changing the gain-bandwidth product, slew rate, input bias current, and noise characteristics. Here's a practical guide to how common op-amp substitutions affect tone:

| Op-Amp | GBW | Slew Rate | Input Type | Sonic Character |
|---|---|---|---|---|
| JRC4558D | 3 MHz | 1 V/us | Bipolar | Warm, smooth, the classic TS sound |
| RC4558 | 3 MHz | 1 V/us | Bipolar | Very similar to JRC4558 (different manufacturer) |
| NE5532 | 10 MHz | 9 V/us | Bipolar | Cleaner, more detailed, less "colored" |
| TL072 | 3 MHz | 13 V/us | JFET | Brighter, more defined transients, lower noise |
| TL071 | 3 MHz | 13 V/us | JFET | Single version of TL072 |
| LM741 | 1 MHz | 0.5 V/us | Bipolar | Vintage, "dirty," high noise, very limited |
| LM308 | 1 MHz | 0.3 V/us | Bipolar | The RAT op-amp, very slow, softens highs |
| OP07 | 0.6 MHz | 0.3 V/us | Bipolar | Similar to LM308, common substitute |
| OPA2134 | 8 MHz | 20 V/us | JFET | Very clean, hi-fi, minimal coloration |
| OPA2604 | 20 MHz | 25 V/us | JFET | Extremely clean, fast, "transparent" |

**The rule of thumb:** Slower op-amps (lower slew rate, lower GBW) tend to sound "warmer" and "softer" because they naturally roll off high frequencies and can't follow fast transients faithfully. Faster op-amps sound "cleaner" and "brighter" because they reproduce the full bandwidth and transient detail.

Neither is inherently better. The JRC4558's warmth is a feature of the Tube Screamer, not a limitation. The LM308's slowness is part of the RAT's character. And the OPA2134's transparency is ideal for a clean preamp or a hi-fi audio circuit.

> **What happens if...** you put an OPA2134 (very fast, very clean) in a Tube Screamer? The overdrive becomes noticeably brighter and more detailed. The clipping sounds crisper — you can hear more of the harmonic detail. Some players love this because it's more "present" and cuts through a mix better. Others miss the warmth of the JRC4558. There's no wrong answer — but now you understand the *reason* for the tonal difference, and you can choose deliberately rather than guessing.

### 8.5 Looking Ahead

In the next chapter, you'll take these circuit building blocks and translate them from schematics and simulations to physical breadboard prototypes. Then in Chapters 8, 9, and 10, you'll build complete pedals using the gain stages, clipping networks, and tone controls you've studied here.

And when you eventually move to PCB layout in [Part 5, Chapter 31](../part-5-pcb-professional/31-pcb-design-for-audio.md), every decision about where to route traces, where to place the ground plane, and how to separate analog and digital sections will be informed by the circuit theory from this chapter.

---

## Chapter Summary

- **Common emitter transistor stages** are the foundation of many classic pedals. Biasing sets the operating point, Rc/Re sets the gain, and the bypass capacitor on Re controls frequency-dependent gain. Transistor hFE variation means individual transistors affect the sound.
- **Op-amp gain stages** provide precise, predictable gain. The non-inverting configuration (Av = 1 + Rf/Rg) is used in the Tube Screamer. The inverting configuration (Av = -Rf/Rin) is used in the RAT. Single-supply operation requires a virtual ground at Vcc/2.
- **Feedback clipping** (diodes in the op-amp feedback loop) produces soft, gradual clipping — smooth overdrive. **Shunt clipping** (diodes to ground after the gain stage) produces hard, abrupt clipping — aggressive distortion.
- **Symmetrical clipping** generates odd harmonics (edgy, buzzy). **Asymmetrical clipping** adds even harmonics (warmer, "tube-like"). Asymmetry comes from using different diode types or different numbers of diodes in each direction.
- **Frequency-dependent clipping** (pre-emphasis/de-emphasis) shapes which frequencies get distorted. The Tube Screamer's "mid hump" comes from rolling off bass before clipping and rolling off treble after clipping.
- **Tone stacks** shape the overall frequency response. The Fender stack has a characteristic mid scoop. The Marshall stack is a variation with less scoop. The Baxandall is an active design with flat response at center and independent boost/cut.
- **SPICE simulation** lets you see and compare all these effects before building. Change one component at a time and observe the result — this is the fastest way to develop circuit intuition.

---

*Next: [Chapter 7 — From Schematic to Breadboard](07-schematic-to-breadboard.md), where we turn these circuits into physical prototypes you can plug in and play.*
