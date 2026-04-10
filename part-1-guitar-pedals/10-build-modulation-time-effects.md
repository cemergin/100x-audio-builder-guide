<!--
  CHAPTER: 10
  TITLE: Build: Modulation and Time-Based Effects
  PART: 1 — Guitar Pedals
  PREREQS: Chapter 9
  KEY_TOPICS: tremolo, LFO circuits, low-frequency oscillator, waveform shapes, PT2399 delay, delay time, feedback repeats, bucket brigade devices, BBD, MN3007, MN3207, designing your own effect, chorus from LFO + delay, digital effects platforms, Daisy Seed
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 10: Build: Modulation and Time-Based Effects

> **Part 1 — Guitar Pedals** | Prerequisites: Chapter 9 | Difficulty: Inter→Adv

Everything we've built so far — fuzz, boost, overdrive, distortion — operates on the signal's amplitude and harmonic content. We've been sculpting the *shape* of the waveform. Now we're going to manipulate the signal in time, and that opens up an entirely new dimension of effects.

This chapter introduces two fundamentally new circuit concepts: the **Low-Frequency Oscillator (LFO)**, which produces a slow, periodic control signal, and the **delay line**, which stores the audio signal and plays it back later. The LFO gives us tremolo, and the delay line gives us echo. But here's the moment that makes this chapter special: when you combine the LFO with the delay line — using the LFO to modulate the delay time — you get chorus, vibrato, and flanger. One plus one equals five.

By the end of this chapter, you'll have built a tremolo and a delay, combined them into a chorus, and understood the architecture of modulation effects well enough to design your own. You'll also understand why some effects sound "analog" and others sound "digital," and you'll get a preview of the digital platforms that take effect design into the software domain.

### In This Chapter
- Build 5: Tremolo — LFO circuit, amplitude modulation, speed and depth controls
- Build 6: PT2399 Delay — the PT2399 chip, delay time, feedback, noise management
- Bucket brigade devices (BBDs): what they are and why they matter
- Combining LFO + delay = chorus/vibrato/flanger
- Introduction to digital effects platforms
- Designing your own effect

### Related Chapters
- [Ch 5: How Effects Shape Sound](05-how-effects-shape-sound.md) — Modulation and time-based effect theory
- [Ch 6: Gain Stages and Clipping Circuits](06-gain-stages-clipping-circuits.md) — Op-amp circuits used in LFOs
- [Ch 8: Build: Fuzz and Boost](08-build-fuzz-and-boost.md) — Transistor stages (used in the tremolo)
- [Ch 9: Build: Overdrive and Distortion](09-build-overdrive-distortion.md) — Previous builds
- [Ch 27: DSP Fundamentals for Audio](../part-4-digital-audio/27-dsp-fundamentals.md) — Digital implementations of these same effects
- [Ch 31: PCB Design for Audio](../part-5-pcb-professional/31-pcb-design-for-audio.md) — PCB layout for these circuits

---

## 1. The Low-Frequency Oscillator (LFO)

### 1.1 What an LFO Does

An LFO is simply an oscillator that runs at very low frequencies — typically 0.5 Hz to 15 Hz, far below the audible range. You can't hear an LFO. Instead, you use its output as a **control signal** to modulate some parameter of the audio path: volume (tremolo), delay time (chorus/flanger), filter cutoff (auto-wah), or pitch (vibrato).

Think of the LFO as a robot hand slowly turning a knob back and forth. The LFO frequency determines how fast the hand turns, and the LFO amplitude determines how far it turns.

### 1.2 LFO Waveform Shapes

The shape of the LFO waveform determines the character of the modulation:

**Sine wave:** The most common LFO shape. Smooth, organic, and natural-sounding. The parameter changes gradually and symmetrically. Think of the gentle pulsing of a classic Fender tremolo.

**Triangle wave:** Similar to sine but with linear (straight-line) transitions instead of curved ones. Slightly more "pointed" modulation — the parameter ramps up linearly, reverses, and ramps down. Very similar to sine in practice; most listeners can't tell the difference.

**Square wave:** The parameter jumps abruptly between two values — no transition, just on/off. In tremolo, this creates a choppy, stuttering effect. In chorus, it creates a jarring, artificial pitch shift. Useful for specific effects but not the default choice.

**Sawtooth (ramp):** The parameter ramps up linearly and then drops abruptly (or vice versa). Less common in guitar effects, but useful for certain auto-wah and filter sweep effects.

### 1.3 Building an LFO: The Op-Amp Oscillator

There are many ways to build an LFO. We'll use a simple and effective approach: an op-amp integrator with hysteresis that produces a triangle/square wave pair.

**The circuit concept:** An op-amp comparator (with hysteresis) generates a square wave. This square wave feeds an op-amp integrator, which converts it into a triangle wave. The integration "smooths" the square wave's abrupt transitions into linear ramps.

```
  Square wave output ←── Op-Amp A (comparator with hysteresis)
         |                    |
         |                    ← feedback from triangle output
         |
         ↓
  Op-Amp B (integrator) ──→ Triangle wave output
         |
         ↓
  Speed pot + Cap set the frequency
```

**Op-Amp A — Comparator with hysteresis (Schmitt trigger):**
```
              R_hyst (100k)
  ┌─────────/\/\/──────────────┐
  |                             |
  ├──── (+) Op-Amp A (output) ─┤
  |                             |
  R_threshold                   |
  |                             |
  Vbias ────────────────────────┘

  (-) ← receives triangle wave from Op-Amp B
```

This creates a square wave that flips between high and low output whenever the triangle wave crosses the upper or lower threshold.

**Op-Amp B — Integrator:**
```
              C_int (varies: 100nF-1uF)
         ┌────||────────────────┐
         |                       |
  Square wave ─── R_speed (Speed pot + R) ──┤(-) Op-Amp B (output)──── Triangle wave
                                             |
                                        ┌────┤(+)
                                        |
                                      Vbias
```

The integrator converts the square wave's constant voltage into a linearly changing voltage. When the square wave is high, the triangle ramps in one direction. When it flips low, the triangle ramps in the other direction.

**The Speed pot** controls the integration rate — how fast the triangle wave ramps. Larger resistance (or larger capacitor) = slower LFO. Smaller = faster.

**Approximating a sine wave:** A true sine LFO requires more circuitry (a Wien bridge oscillator or similar). For tremolo purposes, a triangle wave sounds nearly identical to a sine wave — the difference is barely perceptible. If you want a closer approximation to sine, you can "round off" the triangle wave using a pair of diodes (soft clipping the triangle peaks, just like we soft-clipped the guitar signal in the Tube Screamer). This converts the sharp triangle peaks into smooth curves.

> **What happens if...** you use a square wave LFO for tremolo instead of triangle/sine? The volume jumps abruptly between full and quiet, with no transition. This creates a stuttering, on/off effect — think of a fast rhythmic chop. Some players love it for rhythmic textures. The tremolo in some vintage Vox amps actually uses a roughly square-wave LFO, which is part of their distinctive character.

---

## 2. Build 5: Tremolo

### 2.1 How Tremolo Works

Tremolo = LFO modulating the signal's amplitude. When the LFO is at its peak, the signal passes at full volume. When the LFO is at its trough, the signal is attenuated.

We need two things:
1. An **LFO** to generate the control signal (we just designed one)
2. A **Voltage-Controlled Amplifier (VCA)** — a gain stage whose gain is controlled by the LFO

For a simple transistor tremolo, we can skip the formal VCA and instead use the LFO to directly modulate a transistor's bias, which varies its gain. This is how many classic tremolos work — the LFO wobbles the operating point of a gain stage.

### 2.2 The Circuit

Our tremolo uses a JFET as the variable-gain element. A JFET's drain-source resistance varies with the gate voltage — apply the LFO to the gate, and the JFET acts as a voltage-controlled resistor, forming a voltage divider with a fixed resistor.

**Full circuit overview:**

```
  Audio Input → Cin → [Audio path through JFET attenuator] → Cout → Output

  LFO (triangle/square oscillator) → Depth pot → JFET gate
```

**Audio path (JFET as voltage-controlled attenuator):**
```
  Input ── Cin (100nF) ──┬── Rload (10k) ── Cout (10uF) ── Output (to volume pot)
                          |
                      JFET (2N5457)
                      Drain = signal node
                      Source = GND
                      Gate ← LFO through Depth circuit
```

When the LFO drives the JFET gate toward pinch-off (more negative voltage relative to source), the JFET's resistance increases, attenuating the signal. When the LFO drives the gate toward 0V, the JFET's resistance decreases, passing the signal.

**LFO circuit (dual op-amp triangle/square oscillator):**

Using the second half of a TL072 (or a second TL072):

| Component | Value | Function |
|---|---|---|
| R_speed + Speed pot | 10k + 500k pot (in series) | LFO frequency (speed) control |
| C_int | 1uF | Integration capacitor (sets frequency range) |
| R_hyst1 | 100 kohm | Hysteresis feedback resistor |
| R_hyst2 | 100 kohm | Hysteresis feedback resistor |
| R_threshold | 10 kohm | Threshold setting |

**Depth control:**
```
  Triangle LFO output ── Depth pot (100k) ── JFET gate
                              |
                          R_bias (1M) ── Vbias or GND
```

The Depth pot controls how much of the LFO signal reaches the JFET gate. At zero depth, the gate voltage is constant (no modulation, no tremolo). At full depth, the full LFO swing reaches the gate, creating maximum volume variation.

### 2.3 Component List

| Reference | Value | Type | Notes |
|---|---|---|---|
| IC1 | TL072 | Dual op-amp, DIP-8 | One half for LFO, one for buffer if desired |
| Q1 | 2N5457 | N-channel JFET | VCA element |
| Cin | 100nF | Film capacitor | Input coupling |
| Cout | 10uF | Electrolytic, 25V | Output coupling |
| Rload | 10 kohm | Metal film | Audio path load resistor |
| R_speed | 10 kohm | Metal film | In series with Speed pot |
| Speed pot | 500 kohm | Linear (B) taper | LFO speed |
| C_int | 1uF | Film capacitor (non-polarized) | LFO integrator |
| R_hyst1, R_hyst2 | 100 kohm each | Metal film | Hysteresis network |
| R_threshold | 10 kohm | Metal film | Comparator threshold |
| Depth pot | 100 kohm | Linear (B) taper | Modulation depth |
| R_bias_gate | 1 Mohm | Metal film | JFET gate bias |
| Volume pot | 100 kohm | Audio (A) taper | Output level |
| R_bias1, R_bias2 | 10 kohm each | Metal film | Vbias divider |
| C_bias | 10uF | Electrolytic | Vbias bypass |
| C_pwr | 100uF + 100nF | Electrolytic + ceramic | Power decoupling |

**Total cost:** About $15-25.

### 2.4 Breadboard Build Steps

**Step 1: Build the power supply and Vbias.** Same as previous builds — voltage divider, bypass cap, power rail decoupling.

**Step 2: Build the LFO.**

Install the TL072. Use Op-Amp A (pins 1, 2, 3) for the comparator and Op-Amp B (pins 5, 6, 7) for the integrator.

**Comparator (Op-Amp A):**
- Pin 3 (+): Connect through R_threshold (10k) to Vbias. Also connect R_hyst1 (100k) from Pin 1 (output) back to Pin 3 (positive feedback for hysteresis).
- Pin 2 (-): Connect to the triangle wave output (Op-Amp B output, Pin 7).
- Pin 1 (output): Square wave output.

**Integrator (Op-Amp B):**
- Pin 5 (+): Connect to Vbias.
- Pin 6 (-): Connect through R_speed (10k) + Speed pot (500k) to the square wave (Op-Amp A output, Pin 1). Also connect C_int (1uF) from Pin 7 (output) to Pin 6 (integration capacitor in feedback).
- Pin 7 (output): Triangle wave output.

**Test the LFO before proceeding:** Set your multimeter to DC voltage and touch the probe to Pin 7 (triangle output). You should see the voltage slowly swinging above and below Vbias. If you have an oscilloscope (or use your amp as a rough audio indicator by connecting through a capacitor), you can see or hear the LFO. At the lowest speed setting, it should cycle once every few seconds. At the highest speed, several times per second.

**Step 3: Build the audio path.**

- Install Q1 (2N5457 JFET): Drain in one row, Gate in the next, Source in the next. (Check pinout — for 2N5457: D, G, S left to right looking at flat face.)
- Connect Source to GND.
- Connect Drain to the signal node — where Cin arrives and Rload connects.
- Install Rload (10k) from the signal node to the Cout and output.
- Install Cin (100nF) from the input jack to the signal node.
- Install Cout (10uF) from the other side of Rload to the Volume pot.

**Step 4: Connect LFO to JFET gate.**

- Depth pot: Lug 3 to triangle wave output (Pin 7). Lug 1 to Vbias (or GND — experiment with both). Lug 2 (wiper) through R_bias_gate (1M) to the JFET gate.
- The 1M resistor isolates the JFET gate from the LFO circuit and provides a path for the gate leakage current.

**Step 5: Output wiring.**
Volume pot to output jack, same as previous builds.

### 2.5 Testing the Tremolo

**LFO check:**
1. Power up. Measure the triangle wave output (Pin 7) with your multimeter on DC. Watch the voltage. It should slowly oscillate around 4.5V — maybe swinging from 3V to 6V.
2. Adjust the Speed pot. The oscillation rate should change — slower at one extreme, faster at the other.

**Audio check:**
1. Plug in your guitar, connect to amp.
2. Set Depth to about halfway, Speed to about 2-3 Hz (moderate speed).
3. Strum a chord and hold it. You should hear the volume pulsing rhythmically — louder, softer, louder, softer.
4. Turn Depth up: the pulsing becomes more dramatic (the quiet parts get quieter).
5. Turn Depth down: the pulsing becomes subtle.
6. Turn Speed up: faster pulsing. Turn Speed down: slower, more dramatic sweeping.

**Waveform character:**
If you used the triangle/square LFO, the tremolo should sound smooth and organic (the triangle wave creates a smooth volume change). If you want to hear the square wave version, try connecting the *square wave* output (Pin 1) to the Depth pot instead of the triangle. The tremolo becomes choppy and stuttery — a completely different character from the same circuit.

> **What happens if...** you increase C_int from 1uF to 4.7uF? The LFO runs slower at all speed settings — the minimum speed drops well below 1 Hz, creating a slow, dramatic volume swell. Decrease C_int to 0.1uF and the LFO runs faster — up to 10+ Hz, entering the range where tremolo starts to sound almost like a ring modulator. The audible "sweet spot" for tremolo speed is usually 2-8 Hz.

---

## 3. Build 6: PT2399 Delay

### 3.1 The PT2399 Chip

The PT2399 is a remarkable little IC — a complete digital delay system on a single chip. It contains an analog-to-digital converter, 1024 stages of digital memory, a digital-to-analog converter, and internal clock generation. Despite being technically digital, the PT2399 has a distinctly "lo-fi" character because of its limited bit depth and sampling rate, which introduces noise and degradation that many players find musically pleasing.

**Key specifications:**
- Maximum delay time: About 300ms (usable), up to 600ms (noisy)
- Sample rate: Variable (controlled by an external resistor), typically 30-50 kHz
- Bit depth: ~16 bits (effectively lower due to noise)
- Supply voltage: 5V (needs a regulator from our 9V supply)
- Pins: 16-pin DIP

The PT2399 is the go-to chip for DIY analog-style delay pedals. It's cheap (under $2), readily available, and simple to use. It's found in dozens of commercial delay pedals.

### 3.2 The Minimum Viable Delay Circuit

The PT2399 datasheet provides an application circuit, but let's understand what each section does:

**Clock section:** An external resistor (connected between specific pins) sets the clock frequency, which determines the delay time. Lower resistance = faster clock = shorter delay. Higher resistance = slower clock = longer delay. A potentiometer lets you vary the delay time continuously.

**Input section:** The audio signal enters through the PT2399's internal ADC. The chip expects a signal centered around its internal bias (about 2.5V at 5V supply) and relatively small in amplitude. An attenuator resistor divider before the input ensures the signal isn't too hot.

**Output section:** The delayed signal exits through the PT2399's internal DAC. It needs filtering (a simple RC low-pass) to remove clock noise and aliasing artifacts.

**Feedback section:** To get repeating echoes, some of the output signal is fed back to the input. More feedback = more repeats. At high feedback, the repeats build up and eventually self-oscillate — a wall of cascading echoes that's either musical chaos or uncontrollable noise, depending on your perspective.

### 3.3 The Schematic

```
                        +5V (regulated from 9V)
                         |
           ┌─────────────┤
           |             PT2399
  Input ───┤             ┌──────────────────┐
  (through  attenuator)  │ 16: Vcc  1: Vss  │ → GND
           |             │ 15: CG   2: Vin  │ ← Audio input
           └─────────────│ 14: RE   3: Vtx  │
                         │ 13: RFO  4: GND  │ → GND
    Feedback ←───────────│ 12: RFI  5: Delay│ ← R_delay (delay time)
                         │ 11: CPI  6: CP   │ ── C (clock filter)
    Output ←─────────────│ 10: OP   7: Vref │
                         │  9: CP2  8: CPI2 │
                         └──────────────────┘
```

(Pin numbers and functions vary slightly between PT2399 datasheets from different manufacturers. Always verify against *your* datasheet.)

**Critical external components:**

| Component | Value | Function |
|---|---|---|
| R_delay + Delay pot | 10k + 50k pot | Delay time control (shorter delay = lower R) |
| C_clock filter | 100nF | Filters clock noise on the clock pins |
| C_input | 100nF | Input coupling |
| R_input | 10 kohm | Input attenuator |
| R_feedback + Repeats pot | 10k + 50k pot | Feedback amount (echo repeats) |
| C_output_filter | 1nF-4.7nF | Output low-pass filter |
| R_output_filter | 10 kohm | Output filter resistor |
| Voltage regulator | 78L05 (or LM7805) | Drops 9V to 5V for the PT2399 |

### 3.4 The 5V Power Supply

The PT2399 runs on 5V, but our pedal supply is 9V. We need a voltage regulator:

```
  +9V ── 78L05 ── +5V
          |
         GND
```

The 78L05 (low-power 5V regulator) takes 9V in and outputs a stable 5V. Add a 10uF electrolytic cap on the input and a 10uF + 100nF on the output for stability.

```
  +9V ──┤ 10uF ├── 78L05 IN ── 78L05 OUT ──┬── 10uF ──┬── 100nF ── GND
         |          |                        |           |
        GND        GND                      +5V        GND
```

### 3.5 Component List

| Reference | Value | Type | Notes |
|---|---|---|---|
| IC1 | PT2399 | Delay IC, DIP-16 | The delay chip |
| VR1 | 78L05 | 5V voltage regulator, TO-92 | Power regulation |
| Delay pot | 50 kohm | Linear (B) taper | Delay time |
| R_delay | 10 kohm | Metal film | Minimum delay time resistor |
| Repeats pot | 50 kohm | Linear (B) taper | Feedback/repeats |
| R_feedback_min | 10 kohm | Metal film | Prevents runaway oscillation |
| R_input | 10 kohm | Metal film | Input level setting |
| Cin | 100nF | Film | Input coupling |
| Cout | 10uF | Electrolytic, 25V | Output coupling |
| C_clock1, C_clock2 | 100nF each | Ceramic | Clock filter caps |
| C_out_filter | 2.2nF | Ceramic or film | Output low-pass filter |
| R_out_filter | 10 kohm | Metal film | Output filter |
| Mix pot | 50 kohm | Linear (B) taper | Wet/dry blend |
| Volume pot | 100 kohm | Audio (A) taper | Output level |
| C_reg_in | 10uF | Electrolytic | Regulator input filter |
| C_reg_out | 10uF + 100nF | Electrolytic + ceramic | Regulator output filter |
| Various | 10k, 100k | Resistors | Biasing, as per PT2399 datasheet |

**Total cost:** About $20-35.

### 3.6 Breadboard Build Steps

**Step 1: Build the 5V power supply.**
Install the 78L05 regulator. Check pinout — for TO-92 package (looking at flat face): typically Input, GND, Output (but verify!). Connect 9V to input, GND to ground rail, output to a dedicated +5V rail (use a separate bus on the breadboard or a row that you dedicate to +5V). Add filter caps.

Test: Measure the +5V rail. Should read 4.9-5.1V.

**Step 2: Install the PT2399.**
This is a 16-pin DIP — straddle the center gap, Pin 1 at one end (identified by notch/dot).

Connect power: Vcc (Pin 16) to +5V, GND (Pins 1 and 4) to ground rail.

**Step 3: Clock circuit.**
Connect R_delay (10k) in series with the Delay pot (50k). One end to Pin 5 (or Pin 6 — check your specific datasheet), other end to GND or +5V (datasheet specifies). This RC combination sets the internal clock frequency.

Connect clock filter caps (100nF) to the appropriate pins (Pins 9/10 or 6/11 — varies by datasheet version).

**Step 4: Audio input.**
Guitar signal → Cin (100nF) → R_input (10k voltage divider, center tap to Pin 2 or the designated input pin). The signal needs to be centered around the PT2399's internal reference voltage (approximately Vcc/2 = 2.5V).

**Step 5: Audio output.**
From the PT2399's output pin (Pin 10 or designated), connect through the RC low-pass filter (R_out_filter 10k + C_out_filter 2.2nF to ground). The filtered output goes to the wet/dry mix control.

**Step 6: Feedback path.**
From the filtered output, connect through the Repeats pot (50k) + R_feedback_min (10k) back to the PT2399's feedback input pin. The R_feedback_min prevents total feedback (which would cause runaway oscillation — though some players do this intentionally by holding down a momentary switch that shorts R_feedback_min).

**Step 7: Wet/dry mix.**
Mix the dry (undelayed) signal with the wet (delayed) signal. Simple approach: a pot with the dry signal on one outer lug, wet signal on the other, and the wiper going to the output. More properly, use a resistor summing network.

**Step 8: Output and audio connections.**
Volume pot → output jack, as usual.

### 3.7 Testing the Delay

**Power check:**
- +5V rail: Should read ~5V
- PT2399 power pins: Verify 5V and 0V on the correct pins

**Audio test:**
1. Set Delay to about middle position, Repeats to minimum, Mix to about 50/50.
2. Play a single staccato note. You should hear the original note followed by a single echo after a noticeable delay (maybe 100-200ms).
3. Turn Repeats up. The echo should repeat multiple times, each repeat slightly quieter than the last. At high repeat settings, the echoes should persist for several seconds.
4. Turn Repeats near maximum. The echoes start to build up — each repeat is nearly as loud as the last. Push it further and the delay self-oscillates — the repeats grow instead of decay, creating a wash of cascading echoes. This is musically useful in small doses.
5. Sweep the Delay pot. Short delay: slapback echo (rockabilly). Medium: rhythmic delay. Long: ambient, spacious echoes. At maximum delay time, you may notice increased noise — this is normal for the PT2399 at long delay settings.

**Noise management:** The PT2399 is inherently noisier than high-end digital delay chips. At long delay times, the clock rate drops, and the signal-to-noise ratio degrades. Strategies:
- Keep the input signal level as hot as possible without clipping the PT2399's input
- The output low-pass filter (R_out_filter + C_out_filter) removes clock noise. If you hear a high-pitched whine, increase C_out_filter (try 4.7nF or 10nF)
- Add a second PT2399 in series for longer delay times — each chip handles half the total delay at a higher (less noisy) clock rate

> **What happens if...** you turn the Delay pot all the way down (minimum delay time, say 20-30ms)? The echo becomes so short that you can't hear it as a separate repeat. Instead, the delayed signal combines with the dry signal and creates **comb filtering** — a hollow, flanging sound. With the Repeats pot turned up, you get a strong comb filter with resonant peaks. You've accidentally built a flanger. This is the same physics described in [Chapter 5, section 6.4](05-how-effects-shape-sound.md).

---

## 4. Bucket Brigade Devices (BBDs)

### 4.1 What BBDs Are

Before the PT2399 and digital delay chips, analog delay effects used **Bucket Brigade Devices (BBDs)**. These are analog chips that pass the signal through a long chain of capacitor stages — each stage holds a tiny "bucket" of voltage and passes it to the next stage on each clock pulse, like a line of people passing buckets of water.

The most famous BBDs:
- **MN3005:** 4096 stages. Used in the Boss DM-2 and Electro-Harmonix Deluxe Memory Man. Long delays (300ms+), warm character.
- **MN3007:** 1024 stages. Used in many classic chorus and flanger pedals. Shorter delays.
- **MN3207:** 1024 stages. The "budget" BBD, still used in many DIY and boutique pedals.
- **MN3102:** Not a BBD itself but a clock driver chip designed to pair with the MN3207.

### 4.2 Why BBDs Sound "Analog"

BBDs degrade the signal with each stage transfer. High frequencies are lost progressively (each capacitor stage acts as a low-pass filter), and noise accumulates. The result: each repeat of a BBD delay is slightly darker and noisier than the previous one.

This degradation is the "analog delay sound" — repeats that naturally decay in both volume *and* brightness, like real acoustic echoes. Digital delays (including the PT2399) can reproduce the signal more faithfully, but many players find perfect reproduction less musical than the BBD's gradual degradation.

The PT2399 sits between a true BBD and a true digital delay. It's technically digital (it samples and reconstructs the signal), but its limited resolution and clock noise give it a character closer to analog than to a modern high-resolution digital delay.

### 4.3 BBD Circuit Considerations

Building a BBD delay is more complex than a PT2399 delay because BBDs require:
- An external clock oscillator (the MN3102 provides this for the MN3207)
- Anti-aliasing filters on the input (to prevent clock frequency artifacts)
- Reconstruction filters on the output (to remove clock noise)
- Companding (compression before the BBD, expansion after) to improve the signal-to-noise ratio

We won't build a BBD delay in this guide because the component cost is higher and the PT2399 achieves a similar character more easily. But understanding BBDs is important because they explain *why* analog delay sounds the way it does, and many digital algorithms attempt to model BBD behavior.

### 4.4 Analog vs Digital vs PT2399: A Character Comparison

| Parameter | True Analog (BBD) | PT2399 (Digital, Lo-Fi) | Modern Digital (DSP) |
|---|---|---|---|
| Maximum delay | 300-600ms | 300-600ms | Unlimited (limited by memory) |
| Repeat degradation | Significant (darker each pass) | Moderate (some degradation) | None (unless modeled) |
| Noise floor | High | Moderate | Very low |
| Clock noise | Present (requires filtering) | Present (less severe) | None |
| Component count | High (BBD + clock + compander) | Low (single IC + support) | Moderate (MCU + codec) |
| Cost | $5-20 (if you can find them) | $1-2 | $10-30 (for the processor) |
| Character | "Warm," naturally degrading | "Lo-fi," slight grit | "Clean," precise |
| Modulation (chorus) | Smooth, organic | Adequate, slightly stepped | Perfect, smooth |

For your first delay build, the PT2399 is the right choice — it's cheap, simple, and sounds good. If you later want true analog delay character, explore BBDs. If you want pristine, long delays with presets and tap tempo, you'll need a digital platform (covered in [Part 4](../part-4-digital-audio/25-digital-audio-fundamentals.md)).

### 4.5 Stacking PT2399s for Longer Delay

One PT2399 maxes out at about 300ms of clean delay (you can push it to 600ms, but the noise becomes objectionable). To get longer delays while maintaining quality, you can cascade two PT2399s:

```
  Input → [PT2399 #1: 200ms] → [PT2399 #2: 200ms] → Output
                                                       Total: 400ms
```

Each chip handles half the total delay at a higher clock rate, which means better signal-to-noise ratio than running a single chip at its maximum delay time. Many commercial PT2399-based pedals use two or even three chips in series for this reason.

The trade-off: more components, more board space, and more noise (each chip adds its own noise floor to the signal). A proper analog mixing network with level matching between the chips keeps the noise manageable.

> **What happens if...** you cascade three PT2399s at 200ms each for a 600ms total delay? The signal passes through three sets of ADC/DAC conversions, each adding a bit of degradation and noise. The repeats start to degrade noticeably — getting grittier and darker with each pass. This is actually similar to the natural degradation of a BBD delay, and some builders use cascaded PT2399s specifically to get that "analog" degradation character. The line between "limitation" and "feature" depends on your musical intent.

---

## 5. The "Aha" Moment: LFO + Delay = Chorus

### 5.1 Combining What You've Built

Here's the key insight of this chapter, and one of the most important concepts in modulation effects: **if you modulate the delay time with an LFO, you get chorus (or vibrato or flanger, depending on the parameters).**

You already have:
- An LFO circuit (from the tremolo build, section 2)
- A delay circuit (from the PT2399 build, section 3)

To turn the delay into a chorus, you need to use the LFO to vary the delay time. On the PT2399, the delay time is set by a resistor. If you replace that fixed resistor with a **voltage-controlled resistor** (controlled by the LFO), the delay time will sweep up and down with the LFO.

### 5.2 How Chorus Works (Circuit-Level)

When the delay time increases (LFO sweeping toward longer delay), the delayed signal is being "stretched" — its pitch drops slightly. When the delay time decreases (LFO sweeping toward shorter delay), the delayed signal is "compressed" — its pitch rises slightly. The result is a subtle pitch modulation.

When this pitch-modulated delayed signal is mixed with the dry signal, you hear the characteristic chorus shimmer — two signals at slightly different pitches creating beating and interference patterns.

**Key parameters for chorus:**
- Delay time: 5-30ms (short — you shouldn't hear the delay as a distinct echo)
- LFO rate: 0.5-5 Hz (slow, gentle modulation)
- LFO depth: Small (just enough to create audible pitch variation)
- Mix: About 50/50 wet/dry

### 5.3 Practical Implementation

**Option A: Voltage-controlled resistor using a JFET or LDR**

Use the LFO to control a JFET (as we did in the tremolo) or an LED/LDR (Light-Dependent Resistor) optocoupler. The JFET or LDR's resistance varies with the LFO, and this variable resistance replaces the delay time pot.

**LED/LDR approach (simplest):**
```
  LFO triangle output → current-limiting resistor (1k) → LED
                                                          |
                                                         LDR (physically coupled to LED)
                                                          |
                                                    Connected where the
                                                    Delay pot was
```

An LED and an LDR are physically placed facing each other (inside a piece of heat-shrink tubing to block ambient light). When the LFO makes the LED brighter, the LDR's resistance drops (shorter delay). When the LED dims, the LDR resistance rises (longer delay). This is called a **vactrol** or **optocoupler** — a crude but effective voltage-controlled resistor.

**Option B: Use the PT2399's VCO input**

Some PT2399 application circuits show a method to modulate the delay time via a control voltage. Check your specific datasheet — some versions have a pin that accepts an external modulation signal.

### 5.4 From Chorus to Vibrato to Flanger

By changing the parameters, the same LFO + delay architecture produces three different effects:

| Effect | Delay Time | LFO Rate | LFO Depth | Mix | Feedback |
|---|---|---|---|---|---|
| **Chorus** | 5-30ms | 0.5-5 Hz | Small | 50/50 wet/dry | None or minimal |
| **Vibrato** | 5-30ms | 0.5-5 Hz | Small-medium | 100% wet (no dry) | None |
| **Flanger** | 1-10ms | 0.1-5 Hz | Medium-large | 50/50 wet/dry | High (regeneration) |

**Vibrato** is chorus without the dry signal — you only hear the pitch-modulated delayed signal. Without the dry reference, the pitch variation sounds like an actual pitch wobble rather than a shimmering doubling.

**Flanger** uses shorter delay times and adds feedback (feeding the delayed output back to the input). The shorter delay creates more closely spaced comb filter notches, and the feedback makes those notches deeper and more resonant, creating the characteristic metallic sweep.

**The design realization:** Chorus, vibrato, and flanger are not three different circuits. They're the same circuit with different control settings. If you build a delay with an LFO, a mix control, and a feedback control, you have all three effects in one pedal. Add a few switches to select preset parameter ranges and you've designed a multi-modulation pedal.

> **What happens if...** you increase the LFO rate above 15-20 Hz on a chorus? The pitch modulation becomes fast enough to enter the audible range — you start to hear the modulation as a separate pitch, creating a ring modulator-like effect. Strange, metallic, and atonal. Not for everyone, but definitely a creative territory worth exploring.

---

## 6. Designing Your Own Effect

### 6.1 The Building Blocks

After building five pedals (boost, fuzz, overdrive, distortion, tremolo) and a delay, you now have a library of circuit building blocks:

| Building Block | What It Does | Where You Learned It |
|---|---|---|
| JFET buffer/boost | High-Z input, gain, low-Z output | Ch 8 (boost) |
| Common emitter gain stage | Voltage amplification | Ch 6, Ch 8 (fuzz) |
| Op-amp gain stage | Precise, controllable amplification | Ch 6, Ch 9 (TS, RAT) |
| Feedback clipping | Soft, dynamic distortion | Ch 9 (TS) |
| Shunt clipping | Hard, aggressive distortion | Ch 9 (RAT) |
| Passive tone control | Variable low-pass filter | Ch 9 (TS, RAT) |
| LFO (triangle/square) | Slow periodic control signal | Ch 10 (tremolo) |
| JFET VCA | Voltage-controlled attenuation | Ch 10 (tremolo) |
| PT2399 delay | Time-domain signal storage | Ch 10 (delay) |
| LED/LDR optocoupler | Voltage-controlled resistance | Ch 10 (chorus) |

### 6.2 Example: Auto-Wah (Envelope-Controlled Filter)

Let's design an original effect using these blocks. An auto-wah sweeps a band-pass filter in response to your playing dynamics — pick hard and the filter opens (bright), let it decay and the filter closes (dark).

**Block diagram:**
```
  Input → [Envelope follower] → Control voltage
    |
    └─→ [Voltage-controlled filter] → Output
```

**Envelope follower:** Rectify the guitar signal (diode + capacitor) to create a DC voltage that follows the signal's amplitude. This is the "control voltage" — it represents how hard you're playing.

**Voltage-controlled filter:** Use the envelope voltage to control a filter's cutoff frequency. A JFET (like in our tremolo) can serve as the variable element in an RC filter. When you play hard, the envelope voltage changes the JFET's resistance, shifting the filter cutoff up. As the note decays, the filter cutoff sweeps back down.

This is the same architecture as the tremolo — a control signal modulating a variable element — but instead of the LFO as the control source, we use the signal's own envelope. The circuit topology is identical; only the source of the control voltage changes.

### 6.3 Example: Modulated Distortion

Take the Tube Screamer clipping stage and modulate the clipping threshold with the LFO. Use LEDs as the clipping diodes, and modulate the LED brightness (and thus its forward voltage) with the LFO.

When the LFO makes the LED brighter, the effective forward voltage changes slightly, altering the clipping character. The result: a distortion that subtly pulses and breathes — a combination of overdrive and tremolo in one circuit.

This kind of creative combination is how new and original effects are born. You're not copying a schematic from the internet — you're combining building blocks in new ways based on your understanding of how they work.

### 6.4 Example: Delay with Degrading Repeats (Lo-Fi Delay)

What if each repeat of your delay got more distorted, not just quieter? Take the PT2399 delay and put a soft-clipping stage (Tube Screamer topology, low gain) in the feedback path:

```
  Input → [PT2399 delay] → Output
                ↑                |
                |                ↓
         [Soft clipping stage] ← Feedback
```

Each time the signal passes through the feedback loop, it gets slightly clipped. The first repeat is clean. The second has a bit of overdrive. The third is more distorted. By the fifth or sixth repeat, the signal has been through the clipping stage multiple times and is heavily saturated. The repeats don't just fade out — they *degrade* into warm, fuzzy ghosts of the original note.

This is a real effect design that you can prototype right now using circuits you've already built. The PT2399 delay provides the time-domain storage, and the Tube Screamer clipping stage provides the degradation. Combined, they create something that neither circuit does on its own.

### 6.5 Example: Tremolo-Delay (Multi-Effect)

Route your guitar signal through the tremolo first, then into the delay:

```
  Guitar → [Tremolo (LFO × amplitude)] → [PT2399 Delay] → Output
```

The delay captures the tremolo-modulated signal and repeats it. The echoes pulse at the tremolo rate, creating a rhythmically complex pattern. Now try it the other way:

```
  Guitar → [PT2399 Delay] → [Tremolo] → Output
```

Now the tremolo modulates *both* the dry signal and the echoes. The echoes don't pulse on their own — they're modulated by the same LFO as the dry signal. Different sound, same two circuits, different order.

This "pedal order" exploration is the physical version of [Chapter 5, section 2](05-how-effects-shape-sound.md) on gain staging. The order of effects changes the result because each effect operates on the signal as modified by the previous effect. Designing multi-effect pedals means choosing the order carefully.

### 6.6 The Design Process

1. **Start with the sound you want.** What should it sound like? Describe it in words. "A distortion that wobbles." "A delay that gets dirtier with each repeat." "A filter that responds to my playing."

2. **Decompose it into building blocks.** Which blocks from your library do you need? A gain stage? A filter? An LFO? A delay?

3. **Define the signal flow.** What's the audio path? What's the control path? Draw a block diagram.

4. **Choose component values.** Use the theory from Chapters 5 and 6 to calculate initial values. Use LTspice to simulate and refine.

5. **Prototype on the breadboard.** Build it. Listen. Adjust. Iterate. The breadboard is your playground.

6. **Document what works.** When you find a combination you like, record the component values, take a photo of the breadboard, and save the LTspice simulation. This is the design package you'll use when you move to PCB in [Part 5](../part-5-pcb-professional/31-pcb-design-for-audio.md).

7. **Name it.** Once you have something original that sounds good, give it a name. You're not copying a Tube Screamer anymore — you've designed an original effect. When you move to [Part 7](../part-7-business/42-building-brand-selling.md), that name becomes your product.

> **What happens if...** you use the envelope follower (from the auto-wah example) to control the PT2399's delay time instead of a fixed pot? When you play softly, the delay time is long (spacious echoes). When you play hard, the delay time shortens (tight slapback). The delay responds to your playing dynamics — something no standard delay pedal does. This kind of "responsive" design is where the building-block approach shines: you're combining standard circuits in non-standard ways.

---

## 7. Introduction to Digital Effects Platforms

### 7.1 The Analog-Digital Boundary

Everything we've built in Part 1 has been analog — the audio signal stays in the voltage domain throughout. But as effects become more complex (multi-tap delays, pitch shifting, reverb algorithms, real-time audio processing), analog circuits become impractical. The amount of circuitry required for a high-quality reverb, for example, would fill a large enclosure and cost hundreds of dollars in components. A digital implementation runs on a $10 microcontroller.

This doesn't mean analog is obsolete — far from it. Analog circuits have a character and simplicity that digital can't easily replicate. But digital platforms offer capabilities that analog can't match.

### 7.2 Platforms Worth Knowing About

**Electrosmith Daisy Seed:**
A microcontroller board specifically designed for audio effects. Based on the STM32 ARM processor with a built-in audio codec (ADC + DAC). You write effects in C++ using their audio library. A Daisy Seed costs about $30 and can run complex algorithms: reverb, pitch shifting, granular synthesis, multi-band processing.

**Arduino + Audio Shield:**
A more general-purpose platform. The Teensy (an Arduino-compatible board) with the Teensy Audio Shield provides decent audio I/O and a library of audio processing blocks you can connect in software. Lower audio quality than the Daisy Seed, but the Arduino ecosystem is huge and well-documented.

**Raspberry Pi + Audio HAT:**
The most powerful option — a full Linux computer with audio I/O. Can run professional DSP software (CamillaDSP, PureData, FAUST). This is the platform we'll build with in [Part 4](../part-4-digital-audio/25-digital-audio-fundamentals.md), where we create a Pi-based audio streamer and DSP processor.

**FV-1 Spin Semiconductor:**
A dedicated digital audio effects IC — kind of like the PT2399 but for complex algorithms. Programs are written in a simple assembly-like language. 8 user programs stored in an external EEPROM. Used in many commercial boutique effects. Simpler to use than a full microcontroller but more limited.

### 7.3 When to Use Digital

Use analog when:
- The effect is simple (distortion, fuzz, boost, simple filtering)
- You want the analog "character" (component-level variation, warmth, interactivity)
- You want to understand every component in the signal path
- The circuit is low-cost and low-power

Use digital when:
- The effect requires storage (delay longer than 600ms, looping, sampling)
- The effect requires complex algorithms (reverb, pitch shifting, harmonization)
- You want parameter recall and presets
- You want to update or change the effect without hardware modification

Many modern pedals combine both: analog input buffering and output driving with digital processing in the middle. The best of both worlds.

### 7.4 Where This Goes Next

We've covered analog effects in depth across Part 1. The digital audio path picks up in [Part 4: Digital Audio & The Pi Streamer](../part-4-digital-audio/25-digital-audio-fundamentals.md), where you'll learn about ADC/DAC technology, digital signal processing fundamentals, and build complete digital audio systems.

But before that, Parts 2 and 3 cover speakers and amplifiers — where the signal from your pedals eventually goes. Understanding impedance matching between your pedals and your amp ([Chapter 18](../part-3-amplifiers/18-how-amplifiers-work.md)) and how your amp drives your speakers ([Chapter 12](../part-2-speakers/12-language-of-speakers.md)) completes the picture.

---

## 8. Part 1 Wrap-Up: What You've Built and Where You're Going

### 8.1 The Pedals You've Built

| Build | Chapter | Type | Components | Cost |
|---|---|---|---|---|
| JFET Clean Boost | Ch 8 | Gain | ~6 | $5-10 |
| Silicon Fuzz Face | Ch 8 | Distortion | ~10 | $10-15 |
| Tube Screamer Clone | Ch 9 | Overdrive | ~20 | $15-25 |
| RAT Clone | Ch 9 | Distortion | ~15 | $15-20 |
| Tremolo | Ch 10 | Modulation | ~15 | $15-25 |
| PT2399 Delay | Ch 10 | Time-based | ~20 | $20-35 |

**Total: 6 working pedals, $40-80 in components** (many components are shared between builds if you reuse the breadboard).

### 8.2 What You Now Understand

- How a guitar signal is generated, transmitted, and processed through a pedal chain
- Gain staging and why pedal order matters electrically
- Transistor and op-amp gain stages — biasing, gain calculation, frequency response
- Soft vs hard clipping, symmetric vs asymmetric, frequency-dependent clipping
- Tone stacks and passive/active filtering
- LFO generation and its use as a modulation control source
- Delay circuits (PT2399) and the concept of bucket brigade devices
- How modulation effects (chorus, vibrato, flanger) are all variations of LFO + delay
- Systematic breadboard debugging methodology
- How to measure and characterize your builds with a multimeter and audio probe
- How changing individual component values changes the sound — and why

### 8.3 The Skills That Transfer

Everything you've learned in Part 1 applies beyond guitar pedals:

- **Gain stages and feedback** reappear in amplifier design ([Part 3](../part-3-amplifiers/18-how-amplifiers-work.md))
- **Impedance matching** is critical for speakers ([Part 2, Ch 12](../part-2-speakers/12-language-of-speakers.md)) and amp-speaker interfaces ([Part 6, Ch 35](../part-6-system-design/35-matching-amps-to-speakers.md))
- **Filtering and frequency response** are the foundation of crossover design ([Part 2, Ch 13](../part-2-speakers/13-crossover-design.md)) and DSP ([Part 4, Ch 27](../part-4-digital-audio/27-dsp-fundamentals.md))
- **Breadboard prototyping and debugging** apply to every circuit you'll ever build
- **SPICE simulation** is used throughout all domains

### 8.4 Next Steps

**To go deeper into pedals:**
- [Part 5, Ch 31](../part-5-pcb-professional/31-pcb-design-for-audio.md): Turn your breadboard pedals into professional PCBs
- [Part 5, Ch 32](../part-5-pcb-professional/32-enclosure-design-fabrication.md): Build proper enclosures
- [Part 7](../part-7-business/40-sourcing-supply-chain.md): Turn your pedal designs into a product line

**To explore other domains:**
- [Part 2](../part-2-speakers/11-how-speakers-work.md): Build the speakers your amp drives
- [Part 3](../part-3-amplifiers/18-how-amplifiers-work.md): Build the amp your pedals feed into
- [Part 4](../part-4-digital-audio/25-digital-audio-fundamentals.md): Build the digital systems that extend your analog designs

You've completed the first domain tower. Six pedals, hundreds of hours of potential experimentation, and a foundation that makes every other domain in this guide more approachable. Go plug in and play.

---

## Chapter Summary

- The **LFO (Low-Frequency Oscillator)** produces a slow periodic control signal used to modulate audio circuit parameters. A dual op-amp triangle/square oscillator is simple and effective. Waveform shape (sine/triangle = smooth, square = choppy) determines the modulation character.

- The **tremolo** effect uses an LFO to modulate signal amplitude through a JFET voltage-controlled attenuator. Speed controls LFO rate, Depth controls LFO amplitude reaching the JFET gate.

- The **PT2399** is a single-chip digital delay with analog character. It requires a 5V regulated supply, external clock resistor (sets delay time), input attenuation, output filtering, and a feedback path for repeats. Maximum usable delay is about 300ms.

- **Bucket brigade devices (BBDs)** are analog delay chips that naturally degrade the signal with each repeat (high-frequency loss, noise accumulation). This degradation is the "analog delay sound." The PT2399 approximates this character digitally.

- **LFO + delay = chorus/vibrato/flanger.** Modulating the delay time with an LFO creates pitch modulation. With dry signal mixed in, it's chorus. Without dry signal, it's vibrato. With short delay and feedback, it's flanger. Same architecture, different parameters.

- **Designing your own effects** means combining building blocks: gain stages, clipping networks, filters, LFOs, VCAs, and delay lines. The design process: describe the sound → decompose into blocks → define signal flow → choose values → simulate → prototype → iterate.

- **Digital platforms** (Daisy Seed, Arduino, Raspberry Pi, FV-1) extend effect design into territory that analog circuits can't practically reach: complex reverb, pitch shifting, presets, and real-time DSP.

- **Part 1 total cost:** $40-80 for all six pedal builds. The skills transfer directly to amplifier design, speaker crossovers, DSP, and PCB layout in later Parts.

---

*You've completed Part 1 — Guitar Pedals. Next domain tower: [Part 2 — Speakers](../part-2-speakers/11-how-speakers-work.md), or continue to [Part 5 — PCB Design](../part-5-pcb-professional/31-pcb-design-for-audio.md) to turn these breadboard pedals into professional products.*
