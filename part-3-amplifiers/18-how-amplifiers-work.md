<!--
  CHAPTER: 18
  TITLE: How Amplifiers Work
  PART: 3 — Amplifiers
  PREREQS: Part 0 (Electronics Fundamentals)
  KEY_TOPICS: voltage gain, current gain, power gain, input/output impedance, frequency response, distortion (THD, IMD, crossover), negative feedback, damping factor, slew rate, amp classes (A, AB, B, D, G/H)
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 18: How Amplifiers Work

> **Part 3 — Amplifiers** | Prerequisites: Part 0 (Electronics Fundamentals) | Difficulty: Intermediate

You have a signal. Maybe it's a few millivolts from a microphone, or a line-level signal from your DAC at roughly a volt. Either way, it's not enough to physically move a loudspeaker cone back and forth with any authority. That's the fundamental job of an amplifier: take a small signal and make it bigger while keeping it as faithful to the original as possible. Simple concept. Surprisingly deep execution.

In this chapter we're going to pull apart what "amplification" actually means, because it's not just "make it louder." We'll look at the different flavors of gain, understand why an amplifier's output isn't a perfect copy of the input, and explore the clever tricks engineers use to get closer to perfection. We'll also survey the major amplifier classes — from the gloriously inefficient Class A to the switching wizardry of Class D — so you can make informed decisions about what to build and why.

If you survived Part 0's coverage of transistors, op-amps, and basic circuit analysis, you're ready. Let's get into it.

### In This Chapter
- Voltage gain, current gain, and power gain
- Input and output impedance — why they matter
- Frequency response and bandwidth
- Distortion: THD, IMD, and crossover distortion
- Negative feedback — the Swiss Army knife of amplifier design
- Damping factor and speaker control
- Slew rate and transient performance
- Amplifier classes: A, AB, B, D, G, and H

### Related Chapters
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) — resistors, capacitors, and transistors refresher
- [Ch 3: Simulation and Analysis Tools](../part-0-electronics/03-simulation-analysis-tools.md) — SPICE techniques for amplifier analysis
- [Ch 6: Gain Stages and Clipping Circuits](../part-1-guitar-pedals/06-gain-stages-clipping-circuits.md) — simpler gain stage examples
- [Ch 12: The Language of Speakers](../part-2-speakers/12-language-of-speakers.md) — the load your amp must drive
- [Ch 19: Solid-State Amplifier Design](19-solid-state-amplifier-design.md) — putting this theory into practice
- [Ch 20: Tube Amplifier Theory](20-tube-amplifier-theory.md) — a different approach to the same problem

---

## 1. What Is Gain?

At its core, an amplifier is a device that takes a small input signal and produces a larger output signal. The relationship between input and output is called **gain**. But "gain" isn't a single number — there are three distinct types, and confusing them is one of the most common mistakes beginners make.

### 1.1 Voltage Gain (Av)

**Voltage gain** is the ratio of output voltage to input voltage:

```
Av = Vout / Vin
```

If you feed in 100 mV and get out 2 V, your voltage gain is 20. Simple enough. We often express voltage gain in **decibels (dB)**:

```
Av(dB) = 20 × log10(Vout / Vin)
```

So a voltage gain of 20 is about 26 dB. A gain of 100 is 40 dB. Every time you double the voltage gain, you add about 6 dB.

Voltage gain is what most people think of first, but here's the thing: a speaker doesn't care about voltage. A speaker cares about *power*.

**Handy dB reference table:**

| Voltage Gain | dB |
|-------------|------|
| 1 (unity) | 0 dB |
| 2 | 6 dB |
| 5 | 14 dB |
| 10 | 20 dB |
| 20 | 26 dB |
| 50 | 34 dB |
| 100 | 40 dB |
| 1000 | 60 dB |

Memorize the first few rows. They come up constantly when talking about amplifiers, attenuators, and signal levels.

### 1.2 Current Gain (Ai)

**Current gain** is the ratio of output current to input current:

```
Ai = Iout / Iin
```

A typical power amplifier might have modest voltage gain (20-40x) but enormous current gain. The input signal might draw microamps from your preamp, but the output needs to deliver *amps* to the speaker. A woofer thumping at high volume can easily draw 5-10 amps from your amp.

### 1.3 Power Gain (Ap) — What Actually Matters

**Power gain** is what we really care about for audio:

```
Ap = Pout / Pin = (Vout × Iout) / (Vin × Iin) = Av × Ai
```

In decibels:

```
Ap(dB) = 10 × log10(Pout / Pin)
```

Notice the factor is 10 for power, not 20 like voltage. That's because power is proportional to voltage *squared* (P = V²/R), so doubling power is only +3 dB, not +6 dB.

Here's the practical takeaway: a power amp's job is to take a low-power signal (microwatts from a preamp or DAC) and deliver *watts* to a speaker. The voltage gain might only be 20-30x, but the power gain is typically 40-60 dB (10,000x to 1,000,000x). The amplifier doesn't create energy from nothing — it uses the power supply as an energy reservoir and the input signal as instructions for how to release that energy into the speaker.

> **What happens if... you only have voltage gain but no current gain?**
> You get a buffer. An op-amp voltage follower has unity voltage gain (Av = 1) but high current gain. It can drive loads that the source couldn't. Some preamps work this way — the signal voltage stays the same, but the preamp can source far more current. Conversely, a transformer can give you voltage gain with no power gain (minus losses) — it just trades voltage for current.

---

## 2. Input and Output Impedance

Impedance matching (or deliberate mismatching) is one of those topics that separates people who build circuits from people who build *good* circuits.

### 2.1 Input Impedance

**Input impedance (Zin)** is the impedance the amplifier presents to whatever is feeding it — your DAC, preamp, or guitar pickup.

The golden rule: **high input impedance is (almost always) good**. Here's why.

Your source has an output impedance (Zout). Your amplifier has an input impedance (Zin). Together, they form a voltage divider:

```
Vamp_input = Vsource × Zin / (Zout + Zin)
```

If Zin is much larger than Zout, almost all the source voltage reaches the amplifier. If they're comparable, you lose signal. If Zin is lower than Zout, you lose *most* of the signal and likely distort the source.

Typical values:
- Line-level source output impedance: 100-600 ohms
- Power amp input impedance: 10k-100k ohms
- Guitar amp input impedance: 500k-1M ohms (guitar pickups are high impedance)

A power amp with 47k input impedance being fed by a preamp with 100 ohm output impedance? The voltage divider ratio is 47000/(100+47000) = 0.998. You lose 0.2% of the signal. Totally fine.

### 2.2 Output Impedance

**Output impedance (Zout)** is the impedance the amplifier presents to the speaker. This one is more nuanced.

For a solid-state amplifier driving a speaker, **low output impedance is good**. The amplifier and speaker form another voltage divider:

```
Vspeaker = Vamp × Zload / (Zout + Zload)
```

But more importantly, low output impedance means the amplifier can *control* the speaker. When a woofer cone overshoots (because it has mass and momentum), it generates a back-EMF voltage. A low output impedance amplifier can absorb that back-EMF and damp the cone's motion. This is the basis of **damping factor**, which we'll cover in Section 6.

Typical solid-state amp output impedance: 0.01-0.1 ohms. Typical tube amp output impedance: 1-8 ohms. This is one of the fundamental sonic differences between the two technologies.

> **What happens if... you connect a speaker to a high output impedance source?**
> The speaker's impedance varies wildly with frequency (it's not really "8 ohms" — more like "8 ohms nominal, but 40 ohms at resonance and maybe 6 ohms at 200 Hz"). A high output impedance means the voltage across the speaker changes with its impedance, so the frequency response gets mangled. Bass gets boomy and uncontrolled near resonance, and you lose damping. Some guitar players *love* this effect — it's part of why tube amps sound different driving a speaker cab.

---

## 3. Frequency Response and Bandwidth

No amplifier has infinite bandwidth. Every amp has a frequency range over which it works well, and the response rolls off above and below that range. Understanding why — and how to control it — is fundamental to amplifier design.

### 3.1 The Bandwidth Specification

Bandwidth is typically defined as the range of frequencies where the gain stays within -3 dB of the midband gain. If your amp has a midband gain of 26 dB, the bandwidth is the range where gain stays above 23 dB.

For a hi-fi amplifier, you want bandwidth from at least 20 Hz to 20 kHz (the range of human hearing), and most modern solid-state amps extend well beyond this — often 5 Hz to 100 kHz or more within the -3 dB points.

Why extend beyond human hearing? Two reasons: (1) phase shift begins well before the -3 dB point, so having a wide bandwidth keeps the phase response flat within the audio band, and (2) faster rolloff at the bandwidth edges means less transient degradation from group delay.

### 3.2 Why the Response Isn't Flat

Several things limit bandwidth:

**At low frequencies:**
- Coupling capacitors (if present) form high-pass filters with the input impedance
- Output coupling capacitors (common in single-supply designs) limit bass response
- Feedback network capacitors

**At high frequencies:**
- Transistor gain drops with frequency (gain-bandwidth product, or fT)
- Stray capacitances in the circuit and PCB layout
- Compensation capacitors added intentionally for stability (more on this in the feedback section)

### 3.3 The Gain-Bandwidth Product

Every active device has a **gain-bandwidth product (GBP or fT)** — the frequency at which its open-loop gain drops to 1 (0 dB). For most op-amps and transistors, this is fixed:

```
GBP = Gain × Bandwidth
```

An op-amp with a GBP of 10 MHz can provide a gain of 100 up to 100 kHz, or a gain of 10 up to 1 MHz. You trade gain for bandwidth. This is why power amplifiers with modest closed-loop gain (20-30x) can have very wide bandwidth — they're not asking for much gain relative to the open-loop capability.

### 3.4 Phase Response and Group Delay

Frequency response isn't just about amplitude. **Phase response** describes how much the amplifier delays different frequencies relative to each other. Ideally, all frequencies should be delayed by the same amount (linear phase), but in practice, the phase shifts near the bandwidth limits.

**Group delay** is the derivative of phase with respect to frequency — it tells you how much the actual time delay varies across frequencies. Large group delay variations can smear transients. For most well-designed audio amplifiers, group delay is negligible in the audio band, but it becomes important when we talk about feedback stability.

---

## 4. Distortion — The Unwanted Extras

A perfect amplifier would output an exact, scaled copy of the input. Real amplifiers add stuff that wasn't in the original signal. We call this distortion, and understanding its types is essential for designing (or choosing) amplifiers.

### 4.1 Total Harmonic Distortion (THD)

**THD (Total Harmonic Distortion)** measures how much harmonic content the amplifier adds to a pure sine wave input.

Feed in a 1 kHz sine wave. A perfect amp outputs only 1 kHz. A real amp outputs 1 kHz *plus* small amounts of 2 kHz (second harmonic), 3 kHz (third harmonic), 4 kHz, and so on. THD is the ratio of the total harmonic power to the fundamental power:

```
THD = sqrt(V2² + V3² + V4² + ...) / V1
```

Expressed as a percentage. A THD of 0.01% means the harmonics are 80 dB below the fundamental.

Not all harmonics are created equal perceptually:
- **Even harmonics (2nd, 4th, 6th...)** are musically related to the fundamental (octaves, etc.). Low levels of even harmonics are often perceived as "warm" or "rich." This is a big part of the tube amp sound.
- **Odd harmonics (3rd, 5th, 7th...)** are less musical and can sound harsh or "buzzy" at even moderate levels. Crossover distortion in Class B amps produces primarily odd harmonics, which is why it sounds bad even at low levels.

THD is usually specified at a particular power level because distortion increases as you approach the amplifier's maximum output. A spec like "0.01% THD at 1W, 0.1% THD at rated power" is common.

### 4.2 Intermodulation Distortion (IMD)

**IMD (Intermodulation Distortion)** is arguably more important than THD for perceived sound quality, yet it's less commonly specified. Here's why it matters.

Feed in two sine waves simultaneously — say 60 Hz and 7 kHz (the SMPTE standard test). A nonlinear amplifier doesn't just add harmonics of each tone. It creates *sum and difference frequencies*: 7060 Hz, 6940 Hz, 14060 Hz, and so on. These intermodulation products are *not* harmonically related to either input tone, so they sound dissonant and unpleasant.

This is particularly nasty with complex music. A bass note and a cymbal playing simultaneously can create IM products that sit in the midrange where our ears are most sensitive. You might not consciously hear "distortion," but the music sounds muddy, fatiguing, or "closed in."

IMD is measured as the ratio of the IM products to the original signal level, typically expressed as a percentage.

### 4.3 Crossover Distortion

**Crossover distortion** is the artifact specific to Class B and Class AB amplifiers. It occurs at the "zero crossing" — the point where the signal transitions between the positive-going and negative-going output devices.

In a Class B output stage, the NPN transistor handles the positive half of the waveform and the PNP transistor handles the negative half. But transistors don't turn on until their base-emitter voltage reaches about 0.6 V. So there's a "dead zone" around zero where neither device is conducting, and the output goes flat.

This dead zone creates a kink in the transfer function at exactly zero volts. The resulting distortion is predominantly odd-order harmonics, and it's particularly audible at low signal levels (where the signal spends proportionally more time near zero). This is why pure Class B sounds terrible for audio — it's fine for RF, where the signal never crosses zero, but awful for music.

Class AB biases both devices to conduct a small "quiescent current" even with no signal, eliminating the dead zone. We'll cover the biasing techniques in Chapter 19.

> **What happens if... you listen to a pure Class B amp at low volume?**
> It sounds *terrible*. Thin, gritty, harsh. The crossover distortion is a larger percentage of the signal at low levels. Turn it up and it sounds better (the signal spends less relative time in the dead zone). This is the opposite of what you want — distortion that gets worse as you turn *down* is deeply objectionable. It's why Class B is essentially never used for audio power amplifiers.

### 4.4 Transient Intermodulation Distortion (TIM)

**TIM (Transient Intermodulation Distortion)** — also called SID (Slew-Induced Distortion) — occurs when a fast transient exceeds the amplifier's slew rate. The amplifier literally can't keep up, and during the time it's slewing, the feedback loop is broken because the output can't track the input. This creates a burst of distortion on fast transients like percussion hits, plucked strings, and vocal consonants.

TIM was a hot topic in the 1970s-80s and led to amplifier designs with wider bandwidth, lower feedback, and faster slew rates. We'll revisit this when we discuss slew rate in Section 7.

---

## 5. Negative Feedback — The Engineer's Best Friend (Usually)

Negative feedback is the single most important concept in amplifier design. Almost every practical amplifier uses it. Understanding what it does — and what it costs — is essential.

### 5.1 What Negative Feedback Does

The basic idea: take a fraction of the output signal, invert it, and add it back to the input. The amplifier then amplifies the *difference* between the input and the fed-back output.

```
                    ┌──────────────┐
  Vin ──(+)──►│   Open-loop   │──► Vout
        │       │  Gain = A     │    │
        │       └──────────────┘    │
        │                            │
        └────(-)────[ β ]◄──────────┘
                  (feedback
                   network)
```

If the open-loop gain is A and the feedback fraction is β, the closed-loop gain is:

```
Acl = A / (1 + Aβ)
```

When Aβ >> 1 (which is the normal case), this simplifies to:

```
Acl ≈ 1 / β
```

This is remarkable: the closed-loop gain depends almost entirely on the feedback network (β), not on the amplifier's open-loop gain. Since β is set by passive components (resistors), the gain is stable and predictable.

**Worked example:** Consider the LM3886, which has an open-loop gain of about 125 dB (approximately 1,780,000 times). If we configure it for a closed-loop gain of 20 (26 dB), then β = 1/20 = 0.05.

```
Feedback factor = 1 + Aβ = 1 + (1,780,000 × 0.05) = 89,001
```

The closed-loop gain: 1,780,000 / 89,001 = 20.0002. Essentially exactly 20, regardless of whether the open-loop gain varies by 20% due to temperature or device variation. This is the power of negative feedback.

That feedback factor of 89,001 (about 99 dB) also means: distortion is reduced by 99 dB, output impedance is reduced by 99 dB, and frequency response variations are flattened by 99 dB compared to the open-loop performance. Massive improvements from two resistors.

### 5.2 Benefits of Negative Feedback

**Reduces distortion** by the feedback factor (1 + Aβ). If your amplifier has 1% THD open-loop and a feedback factor of 100, closed-loop THD is about 0.01%.

**Flattens frequency response.** The open-loop response might roll off at 20 Hz and 20 kHz, but if there's enough gain margin, the closed-loop response stays flat across the audio band.

**Reduces output impedance** by the feedback factor. If open-loop output impedance is 10 ohms and feedback factor is 100, closed-loop output impedance is 0.1 ohms. This is how solid-state amps achieve damping factors of 100+.

**Reduces noise and hum** originating within the amplifier (though not noise at the input).

**Stabilizes gain** against component aging, temperature changes, and device-to-device variations.

### 5.3 The Costs of Negative Feedback

Nothing is free:

**Stability.** Negative feedback becomes *positive* feedback if the phase shift around the loop reaches 180 degrees at a frequency where the loop gain is still above unity. The amp oscillates. This is the fundamental stability problem, and it's why amplifier compensation (intentionally rolling off gain at high frequencies) is necessary. You trade bandwidth for stability.

**Transient response.** Heavy feedback can cause ringing on fast transients if the phase margin is inadequate. The amp is stable in the steady-state sense, but overshoots and rings on step inputs.

**TIM susceptibility.** If the input stage slew rate is limited, large feedback can make TIM worse because the error signal (input minus feedback) can become very large on fast transients, overloading the input stage.

**The "negative feedback sound" debate.** Some audiophiles and many tube amp designers argue that high feedback ratios change the character of the sound — making it "clinical," "sterile," or "lifeless." The technical reality is nuanced: *well-applied* feedback unambiguously improves measurable performance. *Poorly applied* feedback (too much feedback with inadequate bandwidth, poor compensation, or slow input stages) can create problems that show up on some measurements (like TIM) and may be audible. The key is good engineering, not avoiding feedback.

### 5.4 Feedback Topologies

There are four basic feedback topologies depending on how you sample the output (voltage or current) and how you apply the feedback to the input (series or shunt):

- **Series-shunt (voltage-voltage):** Most common in audio. Samples output voltage, applies to input as voltage. Reduces output impedance. This is what a non-inverting op-amp configuration uses.
- **Shunt-shunt (voltage-current):** Samples output voltage, applies to input as current. This is the inverting op-amp configuration.
- **Series-series (current-current):** Samples output current (via an emitter/source resistor), applies to input as voltage. Increases output impedance. Used when you want a current source output.
- **Shunt-series (current-voltage):** Samples output current, applies to input as current.

For audio power amplifiers, series-shunt is dominant because we want low output impedance to control the speaker.

---

## 6. Damping Factor

**Damping factor (DF)** is the ratio of the speaker's nominal impedance to the amplifier's output impedance:

```
DF = Zload / Zout
```

An 8-ohm speaker driven by an amp with 0.08 ohm output impedance has a damping factor of 100.

### 6.1 Why Damping Factor Matters

Your speaker is a motor. It also works in reverse — as a generator. When the cone moves (because the amplifier told it to), it generates a voltage (back-EMF). If the amplifier has low output impedance, that back-EMF is effectively short-circuited, and the resulting current opposes the cone's motion. This is electromagnetic braking, and it's what keeps the woofer from bouncing around after a bass transient.

Without adequate damping:
- Bass sounds "boomy," "flabby," or "one-note"
- The woofer rings at its resonant frequency instead of stopping cleanly
- Transient response suffers — the leading edge of a kick drum is followed by mushy overhang

A damping factor above 20 is generally considered adequate for most speakers. Above 100 is excellent. Most solid-state amps achieve 100-1000+. Most tube amps are in the range of 2-20.

### 6.2 A Worked Example — Why Damping Factor Matters for Woofers

Consider a 12-inch woofer with a resonant frequency of 40 Hz and a Qms (mechanical Q) of 5.0. At resonance, the woofer's impedance is about 40 ohms (the impedance peak at resonance).

**With a solid-state amp (Zout = 0.05 ohm, DF = 160):**
The amp holds the voltage across the speaker constant regardless of the impedance peak. The woofer's electrical damping is maximum. The effective total Q (Qtc in a sealed box) is determined almost entirely by the speaker and box parameters. The amp is in firm control.

**With a tube amp (Zout = 4 ohm, DF = 2):**
At resonance, the speaker impedance is 40 ohms. The amplifier output impedance is 4 ohms. The voltage divider effect: the speaker sees 40/(40+4) = 91% of the amp's output voltage at resonance, but at 200 Hz (where speaker impedance might be 6 ohms), it sees only 6/(6+4) = 60%. The frequency response is no longer flat — there's a peak near resonance. Bass near resonance is boosted, bass above resonance is cut. The result: colored, resonant bass that may sound "warm" and "big" or "boomy" and "loose" depending on the speaker, the box, and your taste.

This is why matching amplifiers to speakers matters — covered in depth in [Ch 35](../part-6-system-design/35-matching-amps-to-speakers.md).

### 6.3 The Practical Reality

Here's the dirty secret: the amplifier's output impedance is not the only impedance in the path. Speaker cables have resistance too. A 10-foot run of 16 AWG speaker cable adds about 0.08 ohms. If your amplifier has an output impedance of 0.01 ohms, the cable just made it 0.09 ohms — dropping your effective damping factor from 800 to 89.

This is why thick speaker cables matter more than exotic amplifier specs. Use 12 AWG or heavier for any run over a few feet.

### 6.3 Damping Factor and Speaker Design

Some speakers are designed to be driven by tube amps and actually *expect* a higher source impedance. If you drive a speaker designed for tube amps with a high-damping-factor solid-state amp, the bass might sound thin and overdamped. Conversely, driving a modern hi-fi speaker (designed for solid-state) with a tube amp can produce flabby bass.

This interaction between amplifier output impedance and speaker design is explored further in [Ch 35: Matching Amplifiers to Speakers](../part-6-system-design/35-matching-amps-to-speakers.md).

---

## 7. Slew Rate

**Slew rate** is the maximum rate at which the amplifier's output voltage can change, measured in volts per microsecond (V/us).

### 7.1 Why Slew Rate Matters

A 20 kHz sine wave at 40V peak requires:

```
Maximum dV/dt = 2π × f × Vpeak = 2π × 20000 × 40 = 5.03 V/us
```

If your amplifier's slew rate is only 3 V/us, it literally cannot reproduce a full-power 20 kHz sine wave without distorting. The output becomes a triangle wave at the frequency extremes — this is slew-rate limiting.

But music isn't just sine waves. Transients — the attack of a snare drum, the pluck of a guitar string, the consonants in speech — contain very fast edges that can demand much higher instantaneous slew rates than a steady-state sine wave.

### 7.2 Typical Values

- Basic op-amps (741 era): 0.5 V/us — terrible
- LM3886 (audio power IC): 19 V/us — very good for audio
- Good discrete Class AB amp: 20-100 V/us
- Fast Class D amp: effectively very high (switching nature)
- Tube amps: 5-15 V/us typically (limited by output transformer)

### 7.3 How Much Is Enough?

A common rule of thumb: the slew rate should be at least 5-10 times the minimum needed for full-power output at 20 kHz. This ensures plenty of headroom for transients and keeps TIM at bay.

For a 100W amp into 8 ohms (peak voltage = 40V):
- Minimum needed: ~5 V/us
- Comfortable target: 25-50 V/us

Most modern amplifier designs easily exceed this. Slew rate was a bigger concern in the 1970s when op-amps and amplifier topologies were slower.

---

## 8. Amplifier Classes — How They Use Power Devices

Now we get to the fun part. Amplifier "class" describes the *operating strategy* of the output devices — how much of the signal cycle each device conducts for, and how the power supply energy is converted to output signal.

### 8.1 Class A

**How it works:** The output device(s) conduct for the *entire* 360 degrees of the signal cycle. There's always a large standing current flowing through the output device, and the signal modulates this current up and down.

**Efficiency:** Terrible. Theoretical maximum 25% for a single-ended stage, 50% for push-pull Class A. In practice, 15-25%. Most of the power supply energy is dissipated as heat.

**Distortion character:** Very low distortion, especially even-order harmonics. No crossover distortion (there's no crossover — the device never turns off). The transfer function is smooth and continuous.

**Heat generation:** Enormous. A 25W Class A amplifier wastes 75-100W as heat *continuously*, whether playing music or sitting idle. The heat sink requirements are massive.

**When to use it:** Headphone amps (low power, so the inefficiency doesn't matter), first-watt amplifiers for high-sensitivity speakers, and anywhere you prioritize absolute signal purity over practicality. Nelson Pass has built an empire on Class A amplifiers, and they sound magnificent — if you don't mind the electric bill.

**The Class A efficiency problem in numbers:** Let's say you want 25W of Class A power. The theoretical maximum efficiency of a single-ended Class A stage is 25% (for a push-pull Class A stage, it's 50%, but let's consider the common SE case). To deliver 25W to the speaker, the amplifier must draw 25W / 0.25 = 100W from the power supply *at all times*. The remaining 75W is heat. And here's the painful part: at idle (no signal), the amplifier still draws 100W, all of which is heat. You're running a space heater that occasionally makes music.

A practical 25W Class A amp with a push-pull topology (50% max efficiency) needs a power supply capable of at least 50W and heat sinks rated for 25W continuous dissipation per channel. That's a large, heavy, hot amplifier for 25 watts.

**SPICE tip:** Simulate a single-ended Class A stage in LTSpice to see how the operating point moves along the load line. Watch the power dissipation in the output device — it's maximum at idle and *decreases* as signal increases. This is the opposite of every other class. Plot the instantaneous power in the output device: `P(Q1)` in LTSpice. You'll see it decrease during signal peaks — the energy is going to the speaker instead of being wasted as heat.

> **What happens if... you build a high-power Class A amplifier?**
> It's been done — Nelson Pass's First Watt F7 delivers 25W in pure Class A. It's a magnificent amplifier. It also draws about 100W continuously, runs hot enough to warm a small room, and requires massive heat sinks. Krell used to make 200W+ Class A amplifiers that weighed 100+ pounds and could literally heat a living room in winter. The sonic benefits are real (no crossover distortion, smooth transfer function), but the practical costs are substantial. For most applications, Class AB with generous bias current gets you 90% of the sonic benefit at a fraction of the heat and power consumption.

### 8.2 Class B

**How it works:** Two complementary output devices (NPN + PNP, or N-channel + P-channel) each conduct for exactly half the signal cycle (180 degrees). The NPN handles the positive half, the PNP handles the negative half.

**Efficiency:** Much better — theoretical maximum 78.5%. In practice, 50-65%.

**Distortion character:** Crossover distortion at the zero-crossing point where control passes from one device to the other. As discussed in Section 4.3, this produces odd-order harmonics that are particularly objectionable at low levels.

**Heat generation:** Low at idle (devices are off with no signal), moderate at full power.

**When to use it:** Almost never for audio. It's used in some PA systems where efficiency matters more than sound quality, and in RF amplifiers where the signal never crosses zero. For audio, Class AB is almost always a better choice.

### 8.3 Class AB

**How it works:** Both output devices are biased to conduct for *more* than half the cycle but *less* than the full cycle — typically 181-200 degrees. A small quiescent (idle) current flows through both devices, keeping them in their linear region near the zero crossing.

**Efficiency:** Between Class A and Class B. Typical 50-65%. Depends on the amount of bias current — more bias = more Class A operation = lower efficiency but lower distortion.

**Distortion character:** Very low crossover distortion (if biased properly). The quiescent current fills in the dead zone that plagues Class B. Higher-order harmonics are well-suppressed. THD typically 0.01-0.1% at rated power.

**Heat generation:** Moderate. The heat sinks are substantial but not absurd.

**When to use it:** This is the workhorse of audio amplification. The vast majority of solid-state power amplifiers from the 1960s through today are Class AB. It's the right balance of sound quality, efficiency, and practicality for most applications.

**The Class A vs AB debate:** At low power levels (the first few watts), a well-biased Class AB amplifier operates *in Class A* because both devices are still conducting. Since most music listening happens at a few watts average (even with a 100W amp), a Class AB amp with generous bias spends most of its time in Class A operation anyway. This is why specs like "Class A operation up to 10W" are meaningful.

### 8.4 Class D

**How it works:** Completely different from A/AB/B. Class D is a *switching* amplifier. The output devices are either fully ON or fully OFF — they switch at a high frequency (typically 300 kHz to 1 MHz), and the duty cycle of the switching is modulated by the audio signal (PWM — Pulse Width Modulation). A low-pass filter on the output recovers the audio signal from the switching waveform.

```
Audio in ──► Modulator ──► Gate Driver ──► MOSFETs (switching) ──► LC Filter ──► Speaker
                 ▲                                                        
                 │                                                        
              Triangle/                                                    
              Sawtooth                                                    
              Oscillator                                                  
```

**Efficiency:** Excellent. 85-95% in practice. Since the output devices are either fully on (low resistance) or fully off (no current), very little power is wasted in the devices themselves.

**Distortion character:** Early Class D amps had a reputation for harsh, "digital" sound. Modern designs (like the TPA3255 we'll build in Chapter 23) have largely overcome this. THD can be below 0.01% in well-designed implementations. The remaining distortion tends to be from the output filter, dead-time distortion (a brief period where both devices are off during switching transitions), and power supply modulation.

**Heat generation:** Very low. A 200W Class D amp might need only a small heat sink or none at all. This is transformative for portable and high-power applications.

**When to use it:** Subwoofer amps (efficiency matters, and the low-pass nature of the output filter is fine since we're only reproducing bass), portable/battery-powered systems, PA systems where you need lots of power in a small package, and increasingly for hi-fi where modern Class D designs rival or exceed traditional Class AB in measured performance.

**The "Class D is digital" myth:** Class D is *not* digital audio. It uses analog PWM modulation. The switching is analog — the pulse width varies continuously, not in discrete steps. Some Class D amps do accept digital input and use a digital modulator, but the amplification itself is an analog switching process. Don't let anyone tell you it's "digital amplification."

> **What happens if... you forget the output filter on a Class D amp?**
> Your speaker sees the full switching waveform — a high-frequency square wave modulated by the audio signal. The speaker inductance provides some natural filtering, and you'll hear the audio, but: (1) you'll also radiate massive EMI at the switching frequency that will interfere with everything nearby, (2) the speaker will waste energy trying to reproduce the ultrasonic switching, and (3) some tweeters might overheat from the high-frequency energy. The LC output filter is not optional.

### 8.5 Class D — Open-Loop vs Closed-Loop

A critical distinction in Class D amplifier design that affects sound quality:

**Open-loop Class D:** The modulator generates a PWM signal from the input, and the output follows. Any errors from the switching process (dead-time, power supply variation, output filter nonlinearity) go directly to the speaker uncorrected. Early Class D amps were open-loop, and they had mediocre THD (0.1-1%) and poor PSRR. Some very high-end designs (e.g., the Hypex Ncore) use a sophisticated open-loop topology with careful compensation that achieves excellent results, but they're the exception.

**Closed-loop Class D:** A feedback loop from the output (after the LC filter) back to the modulator corrects for all errors in the chain. The TPA3255 is a closed-loop design. The feedback loop reduces distortion, flattens frequency response, and improves PSRR just like feedback in a Class AB amp. Closed-loop Class D is what made modern Class D competitive with Class AB for audio quality.

**Filterless Class D:** Some Class D amplifiers (e.g., those using the TPA3116 or similar) rely on the speaker's inductance as the output filter rather than using discrete LC components. This works for short cable runs and standard speakers but can cause EMI issues with long cables and doesn't work well with capacitive loads. For serious applications, a proper LC output filter is recommended.

### 8.6 Class G

**How it works:** Class G uses multiple power supply rails. At low signal levels, the output devices run from a lower supply voltage. When the signal demands more voltage swing, the supply automatically switches to a higher rail.

Think of it as a Class AB amp with a two-tier power supply:

```
     +50V ─────────┐
                    │ (switches in for peaks)
     +25V ─────┐   │
               │   │
           Output Stage
               │   │
     -25V ─────┘   │
                    │
     -50V ─────────┘
```

**Efficiency:** Better than Class AB, worse than Class D. Typically 55-75%. The improvement comes from reducing the voltage drop across the output devices at low signal levels.

**When to use it:** Professional PA amplifiers (Crown, QSC) where high power output and reasonable efficiency are needed but the design team doesn't want to deal with Class D's EMI and filter challenges.

### 8.6 Class H

**How it works:** Similar concept to Class G, but instead of discrete supply rails, Class H uses a continuously variable supply voltage that tracks the audio signal envelope. The supply voltage is always just slightly above the signal voltage, minimizing wasted power.

**Efficiency:** Can approach Class D levels in practice (70-85%).

**When to use it:** High-power professional amplifiers. Many high-end PA amps are Class H. It combines the sonic characteristics of Class AB with the efficiency advantages of Class D.

### 8.7 Comparison Table

| Parameter         | Class A  | Class AB | Class B  | Class D  | Class G/H |
|-------------------|----------|----------|----------|----------|-----------|
| Conduction angle  | 360°     | 181-359° | 180°     | Switching| AB-based  |
| Max efficiency    | 25-50%   | 50-65%   | 78.5%    | 90-95%   | 55-85%    |
| Crossover dist.   | None     | Very low | Bad      | N/A*     | Very low  |
| Heat at idle      | Maximum  | Moderate | None     | Very low | Moderate  |
| Output impedance  | Low      | Very low | Low      | Low†     | Very low  |
| Complexity        | Simple   | Moderate | Simple   | High     | High      |
| Cost for power    | High     | Moderate | Low      | Low      | Moderate  |

*Class D has its own switching artifacts instead of crossover distortion.
†Class D output impedance depends on the output filter design and feedback topology.

---

## 9. Amplifier Specifications — Reading Between the Lines

Before we look at what makes a good amplifier for different applications, let's talk about how to read amplifier specifications critically. Manufacturers can spec their products in ways that look impressive but obscure the real-world performance.

### 9.1 Power Ratings — The Biggest Lie in Audio

Amplifier power ratings are a minefield of deception, especially in consumer and PA gear. Here's what to watch for:

**"Peak power" vs "RMS power" vs "Continuous power":**
- **Continuous (RMS) power** is the only honest rating. It's the power the amp can deliver continuously, measured with a sine wave into a resistive load, without exceeding its rated distortion. This is the number that matters.
- **"Peak power"** is the instantaneous peak of the waveform, which is 2x the RMS power. A 50W RMS amp will be marketed as "100W peak." Technically true, completely misleading.
- **"PMPO" (Peak Music Power Output):** A meaningless marketing term that can be 4-10x the actual RMS power. If you see PMPO on a product, distrust all their specs.

**Rated at what distortion?**
A "100W" amp rated at 10% THD is basically in clipping — you wouldn't actually want to listen to it at that level. A "50W" amp rated at 0.1% THD is delivering 50 clean watts. The second amp is more useful despite the lower number.

**Rated into what impedance?**
An amp that's "200W into 2 ohms" might only be 50W into 8 ohms. Always check the impedance. For home speakers, 8 ohms is standard. For PA, 4 ohms is common.

**Both channels driven?**
Many stereo amp specs list power with only one channel driven. With both channels driven from the same power supply, the supply sags more, and the per-channel power drops — sometimes by 20-30%.

### 9.2 THD — Context Matters

A THD spec of 0.001% at 1W is meaningless if the amp produces 1% THD at its rated power. Always look at THD as a function of power (the THD vs. power curve in a datasheet or review).

Also note the measurement frequency. THD at 1 kHz is easier to achieve than THD at 20 kHz. Good specs give THD across the full audio bandwidth (20 Hz - 20 kHz).

### 9.3 Signal-to-Noise Ratio (SNR)

**SNR** tells you how far the noise floor is below the maximum signal level. An SNR of 100 dB means the noise is 100 dB below full power output.

Watch out for **A-weighting** on SNR specs. A-weighted measurements filter out low-frequency noise (below ~500 Hz) and high-frequency noise (above ~10 kHz), where our hearing is less sensitive. A-weighted SNR always reads 3-8 dB better than unweighted. Both are legitimate measurements, but compare like with like.

### 9.4 Channel Separation (Crosstalk)

How much of the left channel bleeds into the right (and vice versa). For a stereo amplifier, 60 dB of separation is good, 80 dB is excellent. This matters for soundstage and imaging in a hi-fi system.

Crosstalk is usually worse at high frequencies due to capacitive coupling between channels. A spec of "80 dB at 1 kHz" might be only "50 dB at 20 kHz."

> **What happens if... you only read the front page specs?**
> You might buy a "500W" amplifier that actually delivers 100W of usable power. Or a "0.001% THD" amp that measures that only at 1W/1kHz into an open circuit. The full datasheet or a qualified independent review (ASR - Audio Science Review is excellent for measured performance) tells the real story. Always dig deeper than the marketing bullet points.

---

## 10. SPICE Simulation for Amplifier Circuits

Before building any amplifier circuit, simulate it. SPICE simulation lets you verify your design, explore trade-offs, and catch mistakes that would be expensive to discover on the bench.

### 10.1 What to Simulate

For a power amplifier design, simulate:

1. **DC operating point:** Verify all bias voltages and currents are correct.
2. **AC small-signal response:** Run a .AC analysis from 1 Hz to 1 MHz. Check gain, bandwidth, and phase margin.
3. **Transient response:** Apply a square wave and watch for ringing, overshoot, and recovery.
4. **Clipping behavior:** Drive the input to beyond full power and observe how the output clips. Is it symmetric? Soft or hard?
5. **Output impedance:** Apply a load step (switch the load between 4 and 8 ohms) and see how the output voltage changes.
6. **Stability under reactive loads:** Add a realistic speaker impedance model (not just a resistor) and verify no oscillation.

### 10.2 Free SPICE Tools

- **LTSpice (now from Analog Devices):** The gold standard for free SPICE. Excellent for analog circuits. Includes many TI/AD components. Available for Windows and Mac.
- **QSPICE:** A newer, faster SPICE tool from the creator of LTSpice. Still maturing but very capable.
- **NgSpice:** Open-source, cross-platform. More setup required but very powerful.

### 10.3 A Simple Exercise

Open LTSpice and simulate a basic Class AB output stage:
1. Use a complementary pair (MMBT3904 / MMBT3906 or similar small-signal transistors for concept validation)
2. Add a Vbe multiplier bias circuit
3. Apply a 1 kHz sine wave at the input
4. Observe the output waveform
5. Adjust the bias and watch crossover distortion appear and disappear

This 30-minute exercise will teach you more about Class AB operation than hours of reading. See [Ch 3: Simulation and Analysis Tools](../part-0-electronics/03-simulation-analysis-tools.md) for detailed SPICE setup and techniques.

---

## 11. Putting It All Together — What Makes a Good Amplifier?

After all this theory, what actually matters for building a great amp?

### 11.1 For Hi-Fi (Fidelity First)

- Low THD and IMD (< 0.1%, preferably < 0.01%)
- Flat frequency response (20 Hz - 20 kHz, ±0.5 dB)
- High damping factor (> 50) for speaker control
- Adequate slew rate (> 10 V/us)
- Low noise floor (> 90 dB signal-to-noise ratio)
- Sufficient power for your speakers (more on this in Part 6)
- Low output impedance for consistent response across the speaker's impedance curve
- Stability into reactive loads (real speakers are not resistors)

The hi-fi design philosophy: the amplifier should be a "straight wire with gain." It should add nothing, remove nothing, and impose no character of its own. Whether that's achievable or even desirable is debatable, but it's the design target.

### 11.2 For Party/PA (Power and Reliability First)

- Enough power to achieve target SPL (often 100W+ per channel)
- High efficiency (your electric bill and heat sinks will thank you)
- Robust protection circuits (people will do stupid things to PA gear)
- Thermal stability (it'll run hard for hours)
- Reasonable distortion (< 1% THD at rated power is fine for live sound)
- Rugged construction (road vibration, temperature extremes, beer spills)
- Clip indication (so the operator knows when they're pushing too hard)
- Bridgeable for mono use when you need maximum power to a single sub

PA amps live a harder life than hi-fi amps. They run at higher duty cycles (parties last hours), face more hostile loads (speakers in parallel, long cable runs), and are operated by people who don't know what clipping sounds like. Protection circuits aren't a luxury — they're survival gear.

### 11.3 For Guitar Amplification (Character First)

- Controlled distortion at desired power levels (distortion *is* the feature)
- Interaction with speaker impedance (high output impedance = tonal variety)
- Power supply sag under load (compression, dynamic response)
- Specific harmonic content (even-order from single-ended stages, etc.)
- Feel and touch sensitivity (how the amp responds to playing dynamics)
- Appropriate power level for the use case (5W for recording, 15-30W for gigging, 50-100W for large stages without PA support)
- Speaker matching (the speaker is part of the instrument, not just a transducer)

Guitar amplifiers are musical instruments, not laboratory equipment. The "best" guitar amp has the most inspiring and expressive character for the player's style, not the lowest THD or the flattest frequency response. A 1% THD spec would be considered excessive in hi-fi; in a guitar amp, 5-10% THD might be exactly the sweet spot that players seek.

These three use cases lead to very different design choices, which is why we cover solid-state designs (Ch 19), tube theory (Ch 20), and build both types (Ch 23-24).

---

## 12. Amplifier Myths and Misconceptions

Before we move on, let's clear up some persistent myths that circulate in audio forums and gear reviews.

### 12.1 "Watts Are Watts"

Not exactly. Different amplifier classes deliver watts differently. A Class A amplifier delivering 10W is always dissipating 30-40W of heat. A Class D amp delivering 10W barely breaks a sweat. The listening experience at 10W is similar, but the engineering reality is different. Also, 10W from a "stiff" regulated power supply sounds different from 10W with a sagging tube supply — the dynamic behavior differs.

### 12.2 "You Can't Hear the Difference Between 50W and 100W"

This is both true and misleading. The difference between 50W and 100W is only 3 dB — barely perceptible in sustained level. But amplifiers don't operate at constant power. Music has transients — short peaks that demand burst power. A 100W amplifier has 3 dB more headroom for those peaks compared to a 50W amplifier, and it clips less often. Whether this is audible depends on your speakers, your listening level, and the music. But the *feel* of an amplifier with adequate headroom is noticeably different from one that's constantly running near its limits.

### 12.3 "Class D Sounds Digital"

As we covered in Section 8.4, Class D is an analog switching topology, not a digital one. Early Class D implementations had audible issues (poor output filter design, dead-time distortion, inadequate feedback), but modern designs like the TPA3255 measure as well as or better than the best Class AB amplifiers. Listen with your ears, not with your preconceptions.

### 12.4 "Negative Feedback Is Bad"

This myth originated from the early tube hi-fi era when designers applied too much global feedback to amplifiers with inadequate bandwidth. The result was ringing, TIM, and a "nervous" sound on transients. The solution wasn't to eliminate feedback — it was to design amplifiers with adequate bandwidth and slew rate to support the feedback. Virtually every commercially successful amplifier uses negative feedback. The question is how much and how well it's implemented.

### 12.5 "Burn-In Changes the Sound"

The claim that amplifiers need 100+ hours of "burn-in" to sound their best is not supported by measurement. Electronic components do settle in the first few minutes of operation (capacitor dielectric absorption, thermal stabilization), but beyond that, measurable changes are negligible. What *does* change is the listener's perception — you adjust to the sound over time. This is psychoacoustic adaptation, not electronics.

---

## 13. Chapter Summary

An amplifier's job is straightforward — make a small signal bigger — but doing it well requires managing a web of interrelated parameters. The key takeaways:

1. **Power gain** is what matters for driving speakers. Voltage gain and current gain are components of it.
2. **High input impedance** avoids loading the source. **Low output impedance** controls the speaker.
3. **Negative feedback** is the primary tool for reducing distortion, flattening response, and lowering output impedance — but demands careful compensation for stability.
4. **Damping factor** determines how well the amp controls the speaker, especially in the bass.
5. **THD** gets the headlines, but **IMD** is often more audible with real music.
6. **Class AB** is the default choice for most audio amplifiers. **Class D** is increasingly competitive and dominates where efficiency matters. **Class A** is the luxury choice for small-signal purity.

In the next chapter, we'll take these principles and turn them into actual circuits — starting with solid-state output stages, biasing, protection, and practical IC-based designs you can build.

---

*Next: [Chapter 19: Solid-State Amplifier Design](19-solid-state-amplifier-design.md)*
*Previous: Part 2 — Speakers*
