<!--
  CHAPTER: 1
  TITLE: Components and What They Do to Signals
  PART: 0 — Electronics for Audio People
  PREREQS: Chapter 0
  KEY_TOPICS: resistors, capacitors, inductors, diodes, transistors, op-amps, voltage dividers, filters, clipping, amplification, biasing
  DIFFICULTY: Beg→Inter
  UPDATED: 2026-04-10
-->

# Chapter 1: Components and What They Do to Signals

> **Part 0 — Electronics for Audio People** | Prerequisites: Chapter 0 | Difficulty: Beg→Inter

Every audio circuit -- from the simplest fuzz pedal to a multi-thousand-dollar tube amplifier -- is built from the same handful of fundamental components. Resistors, capacitors, inductors, diodes, transistors, and op-amps. That's basically it. The magic isn't in the parts themselves, it's in how they're combined. But before you can understand combinations, you need to understand each component on its own terms: what it does to a signal, *why* it does that, and where you'll find it in audio circuits.

This chapter is the component catalog for your audio electronics vocabulary. For each part, we'll cover three things: what it *is* (the physics), what it *does to an audio signal* (the behavior), and where you'll *find it in real circuits* (the application). If Chapter 0 gave you the language of electricity, this chapter gives you the actors that speak it.

A note on style: I'm going to treat each component as a character with a personality. Resistors are stubborn and predictable. Capacitors are frequency-selective gatekeepers. Inductors are the capacitor's mirror image. Diodes are one-way doors. Transistors are the workhorses. Op-amps are the overachievers. Get to know them, because you'll be spending a lot of time together.

### In This Chapter
- Resistors: voltage dividers, biasing, gain setting
- Capacitors: coupling, filtering, tone shaping, time constants
- Inductors: speaker crossovers, EMI filtering, wah pedals
- Diodes: clipping, rectification, protection
- Transistors: amplification, switching, BJT vs MOSFET
- Op-amps: gain, buffering, virtual ground, negative feedback
- Common building blocks: gain stages, tone stacks, power supplies

### Related Chapters
- [Ch 0: Electricity Through the Lens of Sound](00-electricity-through-sound.md) -- the electrical fundamentals these components use
- [Ch 2: Reading Schematics and Datasheets](02-reading-schematics-datasheets.md) -- how to read these components on paper
- [Ch 3: Simulation and Analysis Tools](03-simulation-analysis-tools.md) -- how to simulate these components before soldering

---

## 1. Resistors: The Reliable Workhorses

### 1.1 What a Resistor Is

A **resistor** opposes current flow by a fixed amount, regardless of frequency. It converts electrical energy into heat. That's it. It's the simplest component, and you'll use more of them than anything else.

Resistors are specified by three key parameters:

- **Resistance** (in ohms, Ω): How much it opposes current. Common audio values range from 100Ω to 10MΩ.
- **Tolerance** (percentage): How close the actual value is to the stated value. 1% metal film resistors are standard for audio work (a "10kΩ 1%" resistor is actually between 9.9kΩ and 10.1kΩ). Older-style 5% carbon film resistors are cheaper but less precise.
- **Power rating**: How much heat the resistor can dissipate without damage. 1/4W (0.25W) is standard for signal-level circuits. 1/2W to several watts for power applications (speaker crossovers, tube amp circuits, load resistors).

### 1.2 Resistor Value Notation

Common resistor values follow the **E12** or **E24** series -- standardized sequences that cover each decade with evenly spaced values on a logarithmic scale.

E12 values per decade: 10, 12, 15, 18, 22, 27, 33, 39, 47, 56, 68, 82

So available resistors are: 10Ω, 12Ω, 15Ω, ... 100Ω, 120Ω, 150Ω, ... 1kΩ, 1.2kΩ, 1.5kΩ, ... 10kΩ, 12kΩ, and so on.

You can't buy a 500Ω resistor off the shelf; the nearest standard values are 470Ω and 510Ω. This is normal. Audio circuits are tolerant of these small variations -- a 470Ω resistor works just fine where a schematic says 500Ω.

Notation varies by convention:
- **American**: 4.7k, 10k, 1M, 100R (the "R" means ohms)
- **European**: 4k7, 10k, 1M, 100R (the multiplier letter replaces the decimal point)

The European notation (4k7 instead of 4.7k) exists because decimal points can disappear on photocopied schematics. If "4.7k" becomes "47k" due to a bad photocopy, that's a 10x error. "4k7" is unambiguous.

### 1.3 What a Resistor Does to an Audio Signal

By itself, a resistor does nothing interesting to the frequency content of a signal -- it attenuates everything equally. But in combination with other components, resistors are everywhere:

**Voltage divider**: Two resistors in series create a voltage divider -- the output voltage is a fraction of the input. This is how passive volume controls work (though in practice we use potentiometers, which are variable resistors). The formula:

```
Vout = Vin × R2 / (R1 + R2)
```

Where R1 connects to the input, R2 connects to ground, and the output is taken from the junction. A divider with R1 = R2 outputs half the input voltage (-6 dB). With R1 = 9 × R2, the output is 1/10 of the input (-20 dB).

**Biasing**: Resistors set the DC operating point (bias) of transistors and tubes. The choice of bias resistor values determines where the transistor sits on its operating curve, which affects gain, headroom, and clipping symmetry. In a common-emitter amplifier, two resistors form a voltage divider that sets the base voltage, and an emitter resistor stabilizes the operating point.

**Gain setting**: In amplifier circuits, resistors in the feedback network determine gain. An op-amp with a 10kΩ feedback resistor and a 1kΩ input resistor has a gain of 10 (20 dB). Change the feedback resistor to 100kΩ, and the gain jumps to 100 (40 dB). This is one of the most powerful features of op-amp circuits -- the gain is entirely determined by two passive resistors.

**Current limiting**: Every LED in your pedal has a current-limiting resistor. Every input protection circuit uses them. They prevent excessive current from damaging components. Without a current-limiting resistor, an LED will try to draw infinite current and destroy itself (or the power supply).

**Pull-up and pull-down**: In digital circuits and some analog ones, a resistor to the supply rail (pull-up) or to ground (pull-down) ensures a node has a defined voltage when nothing is actively driving it. Useful for switch inputs and logic level setting.

### 1.4 Potentiometers: Variable Resistors

A **potentiometer** ("pot") is a resistor with a sliding or rotating contact (the "wiper") that lets you vary the resistance. It has three terminals: two ends of the resistive element and the wiper.

**Tapers** determine how resistance changes as you turn the knob:
- **Linear (B taper)**: Resistance changes proportionally with rotation. 50% turn = 50% resistance. Used for bias trims and circuits where precise, predictable control is needed.
- **Audio/logarithmic (A taper)**: Resistance changes logarithmically. The first 50% of rotation covers about 10% of the total resistance, with most of the range in the last 50%. This compensates for the logarithmic nature of human loudness perception, making the volume control feel "even" across its range.
- **Reverse audio (C taper)**: The inverse of audio taper. Rarely used in audio.

For volume controls, always use audio taper. A linear taper volume pot feels wrong -- nothing happens for the first half of the turn, then everything happens at once near the top.

### 1.5 Audio Applications

| Application | Typical Values | What It Does |
|---|---|---|
| Volume pot (guitar, single coil) | 250kΩ audio taper | Variable voltage divider for signal level |
| Volume pot (guitar, humbucker) | 500kΩ audio taper | Higher value preserves more treble |
| Tone pot (guitar) | 250-500kΩ linear | Controls how much treble bleeds to ground |
| Bias resistors (transistor) | 1kΩ - 1MΩ | Set DC operating point |
| Gain-setting (op-amp) | 1kΩ - 1MΩ | Determine amplification factor |
| Current limiting (LED) | 470Ω - 4.7kΩ | Protect LED from overcurrent |
| Input impedance setting | 1MΩ typical | Ensures high input Z for pedals |
| Mixer summing | 10kΩ - 100kΩ | Combine multiple signals at summing node |
| Feedback network | 1kΩ - 100kΩ | Define gain and frequency response |

### 1.6 Resistor Types and Audio Considerations

| Type | Noise | Tolerance | Audio Use |
|---|---|---|---|
| Metal film | Very low | 1% | Standard for all audio signal paths |
| Carbon film | Low | 5% | Budget alternative, slightly noisier |
| Carbon composition | Moderate (can add audible noise at high gain) | 10-20% | Vintage character, some builders prefer for fuzzes |
| Wire-wound | Very low | 0.1-1% | Speaker crossovers, power applications |
| Thick film SMD | Low | 1% | Modern PCB designs, surface mount |

For signal-path work, metal film resistors are the default. They're cheap ($0.01 each), quiet, stable, and precise. Carbon composition resistors are sometimes specified in vintage pedal builds because they generate a small amount of thermal noise that contributes to the "vintage" character -- but for most purposes, metal film is the right choice.

> **What happens if...** you use the wrong value bias resistor? The transistor's DC operating point shifts. Too little bias and the transistor barely turns on -- it clips the signal prematurely, producing asymmetric distortion. Too much bias and the transistor saturates -- it can't swing high enough, clipping the tops of waveforms. In guitar pedals, deliberately "wrong" biasing is actually a design choice. A "sputtery" dying-battery fuzz sound comes from starving the transistor of bias current. Some Fuzz Face builds include a "bias" trim pot so you can dial in exactly how much the transistor is starved.

---

## 2. Capacitors: The Frequency-Selective Gatekeepers

### 2.1 What a Capacitor Is

A **capacitor** stores energy in an electric field between two conductive plates separated by an insulator (the **dielectric**). It's specified by:

- **Capacitance** (in farads, F): How much charge it stores per volt. You'll almost always work with microfarads (μF, millionths), nanofarads (nF, billionths), and picofarads (pF, trillionths).
- **Voltage rating**: The maximum voltage before the dielectric breaks down and the cap fails (often spectacularly).
- **Type**: The dielectric material determines the capacitor's size, cost, voltage rating, and audio characteristics.

Size reference:
- 1 F = 1,000,000 μF = 1,000,000,000 nF = 1,000,000,000,000 pF
- Typical audio signal path values: 10 pF to 10 μF
- Power supply filter values: 100 μF to 10,000 μF
- A 100 pF ceramic cap is the size of a grain of rice. A 100 μF electrolytic is the size of a small thimble. A 10,000 μF power supply cap is the size of a drink can.

### 2.2 What a Capacitor Does to an Audio Signal

From Chapter 0, you know that a capacitor's impedance decreases with frequency:

```
Zc = 1 / (2π × f × C)
```

This single property makes capacitors the most versatile component in audio. They are, in many ways, the defining component of analog audio design.

**DC blocking / AC coupling**: A capacitor in series with the signal path blocks DC (frequency = 0 Hz, so impedance = infinite) and passes AC (audio frequencies, where impedance is low). This is the coupling capacitor, and it's in virtually every audio circuit between stages. Think of it as a frequency-dependent gate that slams shut for DC and swings wide open for audio.

**Low-pass filtering**: A capacitor from signal to ground bleeds high frequencies to ground (because the cap's impedance is low at high frequencies) while leaving low frequencies alone (impedance is high). This is your guitar's tone knob -- a capacitor from the signal to ground, with a pot controlling how much of the signal reaches the cap. It's also the principle behind RF filtering (keeping radio interference out of your audio).

**High-pass filtering**: A capacitor in series with the signal blocks low frequencies (impedance is high) and passes high frequencies (impedance is low). Coupling capacitors are technically high-pass filters -- they pass everything above a certain frequency and block everything below.

**Timing / smoothing**: In power supplies, large capacitors smooth out rectified AC into steady DC. The capacitor charges during voltage peaks and discharges during voltage dips, filling in the gaps. In pedal circuits, a capacitor across the power rail (called a "bypass cap" or "decoupling cap") absorbs brief current demands from the circuit and rejects power supply noise.

### 2.3 The Time Constant (RC)

When you pair a resistor and a capacitor, you get a **time constant** (symbol τ, "tau"):

```
τ = R × C
```

Where R is in ohms and C is in farads. The time constant tells you how quickly the capacitor charges or discharges through the resistor. After one time constant, the capacitor has charged to 63% of the final voltage. After 5 time constants, it's essentially fully charged (>99%).

More importantly for audio, the time constant defines the **cutoff frequency** (also called the **-3 dB point** or **corner frequency**) of an RC filter:

```
f_cutoff = 1 / (2π × R × C) ≈ 1 / (6.28 × R × C)
```

This is the frequency at which the filter starts having a significant effect. Below the cutoff, a high-pass filter attenuates; above it, signals pass through. For a low-pass filter, it's the reverse.

### 2.4 Worked Filter Examples

**Coupling capacitor selection**: A coupling capacitor of 100 nF (0.1 μF) in series with a 1 MΩ input impedance:
- f_cutoff = 1 / (2π × 1,000,000 × 0.0000001) = 1 / (0.628) ≈ 1.6 Hz
- This filter passes everything above ~1.6 Hz (well below the audible range) -- so it blocks DC without affecting the audio signal. Good design.

What if you used a 1 nF capacitor instead?
- f_cutoff = 1 / (2π × 1,000,000 × 0.000000001) = 159 Hz
- Now you're cutting bass below 159 Hz! You've accidentally made a treble booster. This is a real mistake people make when choosing coupling cap values -- always calculate the cutoff.

**Guitar tone knob**: A 22 nF capacitor (typical for Stratocaster) with the tone pot at various settings (pot resistance to ground):
- Tone at 10 (pot = 250kΩ to ground): f_cutoff = 1 / (2π × 250000 × 0.000000022) = 29 Hz. Almost no audible filtering -- everything passes.
- Tone at 5 (pot ≈ 125kΩ): f_cutoff = 58 Hz. Still below most guitar content -- subtle warmth.
- Tone at 1 (pot ≈ 25kΩ): f_cutoff = 290 Hz. Significant treble roll-off -- "woman tone," dark and mellow.
- Tone at 0 (pot ≈ 0Ω, but there's always some residual resistance): Very low cutoff -- maximum treble bleed, very dark sound.

This is why the capacitor value matters so much for guitar tone knobs. A 47 nF cap (typical for Les Paul humbuckers) produces cutoff frequencies about half those of a 22 nF cap, which means darker overall character at the same pot position.

**Amp tone stack capacitor**: In a classic Fender tone stack, the treble cap is typically 250 pF and the bass cap is 100 nF. The treble cap is 400x smaller than the bass cap, which means its filtering effect operates at 400x higher frequencies. This wide ratio is what gives the tone stack separate control over bass and treble ranges.

### 2.5 Capacitor Types and Audio Quality

Different capacitor constructions have different characteristics:

| Type | Typical Range | Audio Character | Common Use |
|---|---|---|---|
| Ceramic (C0G/NP0) | 1 pF - 10 nF | Low distortion, very stable, tight tolerance | Precision filtering, high-frequency compensation |
| Ceramic (X7R, Y5V) | 100 pF - 10 μF | Voltage-dependent capacitance (!), microphonic | Power supply decoupling ONLY -- avoid in signal path |
| Film (polyester/Mylar) | 1 nF - 10 μF | Clean, neutral, good all-rounder | General audio signal path, coupling, tone |
| Film (polypropylene) | 100 pF - 1 μF | Very low distortion, very low loss | Tone circuits, speaker crossovers, high-end pedals |
| Film (polystyrene) | 10 pF - 10 nF | Extremely low distortion | Precision filters, sample-and-hold circuits |
| Electrolytic (aluminum) | 1 μF - 10,000 μF | Higher distortion, polarized, leakage current | Power supply filtering, large coupling caps |
| Tantalum | 0.1 μF - 100 μF | Compact, polarized, can fail catastrophically | Power supply decoupling where space is tight |

**Why ceramic X7R in the signal path is bad**: X7R and Y5V ceramic capacitors have a property called "voltage coefficient" -- their capacitance changes with applied voltage. An X7R cap rated at 1 μF might actually be 0.6 μF when 50% of its rated voltage is applied. This varying capacitance means the filter cutoff frequency changes with signal level, which is literally *amplitude-dependent distortion*. For power supply decoupling (where the DC voltage is constant), this doesn't matter. For audio signals (where the voltage is constantly changing), it introduces measurable distortion.

C0G/NP0 ceramics don't have this problem -- their capacitance is voltage-independent. But they're only available in small values (up to about 10 nF).

**The "mojo" debate**: Guitar pedal communities debate endlessly about capacitor types and their sonic character. Here's the reality: in signal path positions where the capacitor is acting as a filter (tone caps, EQ circuits), the capacitor type *can* matter because the dielectric material's loss characteristics affect the filter's Q factor and high-frequency behavior. In coupling positions (where the cap is just blocking DC and passing everything above a few Hz), the type barely matters because the cap's impedance is so low at audio frequencies that its losses are negligible. Don't spend $5 on a "boutique" coupling capacitor. Do spend thoughtfully on tone-critical filter positions if you want to experiment.

> **What happens if...** you put an electrolytic capacitor in backwards? Electrolytic caps are **polarized** -- they have a positive and negative terminal. The oxide layer that forms the dielectric is created by an electrochemical process that only works in one polarity. Reversing polarity causes the oxide layer to break down, which causes heating, gas generation, and eventual failure -- ranging from a quiet leak to a loud pop and a shower of electrolyte. Always check polarity when working with electrolytics. The negative terminal is marked with a stripe and minus signs on through-hole parts. On the PCB, the positive hole is usually marked with a + symbol.

### 2.6 Audio Applications Summary

| Application | Typical Cap Value | Paired With | Effect |
|---|---|---|---|
| Coupling / DC blocking | 100 nF - 10 μF | Input impedance of next stage | High-pass filter below ~1-20 Hz |
| Guitar tone knob (Strat) | 22 nF | 250kΩ tone pot | Bleeds treble to ground |
| Guitar tone knob (LP) | 47 nF | 500kΩ tone pot | Bleeds treble to ground, darker |
| Treble bleed (guitar vol) | 100-150 pF | Volume pot | Preserves highs when turning down |
| Power supply filtering | 100 μF - 1000 μF | Power supply rail | Smooths DC, absorbs ripple |
| Bypass / decoupling | 100 nF | Per IC, on power pin | Provides instant local current, blocks power supply noise |
| Tone stack (amp) | 250 pF - 100 nF | Resistor network | Shapes mid, bass, treble response |
| Emitter bypass | 10 μF - 100 μF | Emitter resistor in BJT amp | Increases AC gain while maintaining DC bias stability |

---

## 3. Inductors: The Capacitor's Mirror

### 3.1 What an Inductor Is

An **inductor** is a coil of wire that stores energy in a magnetic field. When current flows through the coil, it creates a magnetic field; when the current changes, the magnetic field resists the change (this is **Lenz's law**). It's specified by:

- **Inductance** (in henrys, H): How strongly it resists current changes. Typical audio values are millihenrys (mH) for speaker crossovers and microhenrys (μH) for EMI filters.
- **DC resistance (DCR)**: The resistance of the wire itself. Lower is generally better, but zero-DCR inductors don't exist. In speaker crossovers, DCR matters because it wastes power as heat.
- **Saturation current**: The current at which the magnetic core saturates and the inductor stops behaving like an inductor. Beyond saturation, the inductor becomes a plain wire. This matters in power applications.
- **Core type**: Air core (no saturation, linear but large), ferrite core (compact but can saturate), iron core (high inductance, used in transformers and speaker crossovers).

Inductors are physically larger, heavier, and more expensive than capacitors at equivalent "filtering power." This is why audio circuits use capacitors wherever possible and reserve inductors for applications where only an inductor will do.

### 3.2 What an Inductor Does to an Audio Signal

An inductor's impedance *increases* with frequency -- exactly the opposite of a capacitor:

```
Zl = 2π × f × L
```

At low frequencies, an inductor's impedance is low (it passes bass). At high frequencies, its impedance is high (it blocks treble). Let's compute real values for a 1 mH inductor:

- At 20 Hz: Zl = 2π × 20 × 0.001 = 0.126 Ω (essentially transparent -- passes bass freely)
- At 1 kHz: Zl = 2π × 1000 × 0.001 = 6.28 Ω (starting to have some effect)
- At 20 kHz: Zl = 2π × 20000 × 0.001 = 126 Ω (significant impedance -- blocks treble)

This complementary behavior to capacitors is why inductors and capacitors are paired in crossover and filter networks. The inductor handles the low-pass duty, the capacitor handles the high-pass, and together they split the frequency spectrum.

### 3.3 Audio Applications

**Speaker crossovers**: A series inductor in front of a woofer is the most natural way to build a low-pass filter for bass -- the inductor passes low frequencies (low impedance) and blocks high frequencies (high impedance). This is far more common than using capacitors for the same job because the inductor handles the power levels involved in speaker driving (tens or hundreds of watts) without needing any active electronics. Typical crossover inductors range from 0.5 mH to 5 mH, wound with thick wire (14-18 AWG) to minimize DCR losses.

Chapters 12-13 cover crossover design in depth, including the trade-offs between air-core inductors (lower distortion, larger, more expensive) and iron-core inductors (more compact, but can saturate and distort at high power).

**Wah pedals**: The classic Dunlop Cry Baby wah pedal is built around an inductor (the "wah inductor" or "fasel" inductor, named after the Italian manufacturer). The inductor forms a resonant bandpass filter with capacitors. The foot pedal sweeps the resonant frequency by varying a resistance, creating the characteristic wah sound. The inductor's value (approximately 500 mH) and quality significantly affect the sound -- which is why vintage wah pedals with original fasel inductors are sought after, and why the modern replacement inductors sound different.

**EMI filtering**: Inductors (often called "chokes" in this context) block high-frequency electromagnetic interference from entering or leaving a circuit. A ferrite bead on a cable is a tiny inductor (typically a few μH) that absorbs RF interference. In power supplies, a choke filters high-frequency switching noise. The bead's impedance is negligible at audio frequencies but significant at RF frequencies (above 1 MHz), so it removes interference without affecting the audio.

**Guitar pickup resonance**: A guitar pickup IS an inductor -- a coil of several thousand turns of fine wire wrapped around magnets. A typical single-coil pickup has an inductance of 2-3 H and a DC resistance of 5-7 kΩ. A humbucker might have 4-8 H and 7-15 kΩ. This inductance, combined with the capacitance of the cable and the guitar's volume and tone pots, creates a resonant circuit with a peak somewhere between 2-5 kHz. This resonant peak is a major factor in the pickup's tonal character -- it's the "presence" or "sparkle" frequency. Different pickup designs target different resonant peak frequencies to achieve different tones.

### 3.4 LC Resonance

When an inductor and capacitor are combined, they form a resonant circuit that peaks at a specific frequency:

```
f_resonance = 1 / (2π × √(L × C))
```

At resonance, the impedances of the inductor and capacitor are equal and opposite, and they cancel. In a series LC circuit, impedance drops to a minimum at resonance (the circuit passes that frequency easily). In a parallel LC circuit, impedance peaks at resonance (the circuit blocks that frequency).

This resonance is the basis of wah pedals, certain guitar pickup characteristics, band-pass filters, and notch filters. It's also the mechanism behind LC speaker crossovers, where the resonant behavior of the inductor-capacitor pair determines the crossover frequency and slope.

> **What happens if...** you substitute a capacitor-based filter for an inductor-based filter in a speaker crossover? Technically, you can achieve a similar frequency response using active circuits (op-amps with capacitors). But passive crossovers in speakers traditionally use inductors because: (1) they handle high power without needing amplification, (2) they don't require a power supply, (3) they're simpler and more reliable (no ICs to fail), and (4) the inductor's behavior with real speaker loads (which are partly inductive themselves) can be more predictable than an active filter's behavior with the same load. Active crossovers (covered in Part 4) avoid inductors entirely by doing the filtering before amplification, splitting the signal, and using separate amplifiers for each driver.

---

## 4. Diodes: One-Way Doors and Waveform Sculptors

### 4.1 What a Diode Is

A **diode** is a semiconductor device that conducts current in one direction (forward bias) and blocks it in the other (reverse bias). It has two terminals: **anode** (+) and **cathode** (-). Current flows from anode to cathode when the forward voltage exceeds the diode's **forward voltage drop** (about 0.6V for silicon, 0.3V for germanium, 0.2V for Schottky diodes).

Think of a diode as a one-way valve with a minimum pressure requirement. Current flows only in one direction, and only when the voltage exceeds a threshold.

The transition from "not conducting" to "conducting" isn't a hard switch -- it's a gradual curve. The "softness" of this curve (called the "knee") varies by diode type and affects the harmonic character of clipping distortion. This is why different diode types sound different in overdrive pedals.

### 4.2 What a Diode Does to an Audio Signal

**Clipping / distortion**: This is the big one for audio. When an audio signal's voltage exceeds the diode's forward voltage drop, the diode conducts and "clips" the top of the waveform flat. By placing diodes in the feedback loop of an op-amp or from the signal to ground, you create controlled distortion. This is the basis of most overdrive and distortion pedals.

Different diode types clip differently:

| Diode Type | Forward Voltage | Clipping Character | Famous Circuits |
|---|---|---|---|
| Silicon (1N4148) | ~0.6V | Hard, aggressive clipping | Boss DS-1, ProCo RAT |
| Germanium (1N34A) | ~0.3V | Softer clipping, earlier onset | Vintage Tube Screamer mods |
| LED (red) | ~1.7V | Very clean until clipping, then hard | DOD OD-250, Bluesbreaker |
| LED (green) | ~2.1V | Even more headroom before clipping | Custom builds |
| Schottky (BAT41) | ~0.2V | Earliest clipping, relatively soft | Boutique low-gain overdrives |
| MOSFET (body diode) | ~0.7V | Soft, gradual onset | Some modern overdrives |

**Symmetrical vs asymmetrical clipping**: Two identical diodes in opposite directions (antiparallel) clip both the positive and negative peaks identically -- **symmetrical clipping**, which produces predominantly odd harmonics (3rd, 5th, 7th). This sounds more aggressive and "square-wave-like."

Using different diode types for each direction (e.g., one silicon, one germanium) clips unevenly -- **asymmetrical clipping**, which produces a mix of even harmonics (2nd, 4th, 6th) and odd harmonics. Even harmonics are generally considered warmer and more "musical" because they're musically related to the fundamental (the 2nd harmonic is one octave up, the 4th is two octaves up).

This is not voodoo -- it's math. Fourier analysis of an asymmetrically clipped waveform shows even harmonics that are absent in the symmetrically clipped version. You can verify this in LTspice using FFT analysis (Chapter 3).

### 4.3 Clipping Placement: In-Feedback vs To-Ground

Where you place the clipping diodes matters as much as which diodes you use:

**In the feedback loop** of an op-amp (like the Tube Screamer): The diodes limit the maximum voltage across the feedback resistor, which limits the gain. The op-amp's feedback mechanism means the clipping is "soft" -- the transition from clean to clipped is gradual because the op-amp's feedback linearizes the diode's behavior. The result is a warm, compressed overdrive.

**To ground** from the signal path (like the Boss DS-1 and MXR Distortion+): The diodes hard-clip the signal after amplification. There's no feedback mechanism to soften the transition. The clipping is more abrupt, which produces a more aggressive, buzzy distortion with stronger harmonic content.

These two approaches produce dramatically different sounds even with the same diode types. The Tube Screamer's in-feedback clipping gives it that smooth, mid-humped overdrive character. The MXR Distortion+'s to-ground clipping gives it a more raw, edgy sound. Same diodes, different topology, different tone.

### 4.4 Rectification

In power supplies, diodes convert AC (from a transformer) into DC (which your circuit needs). Four diodes arranged in a **bridge rectifier** convert both halves of the AC waveform into positive DC. This is followed by filter capacitors to smooth the result.

**Half-wave rectification** uses a single diode -- only the positive halves of the AC waveform pass through. This is less efficient but simpler. In tube amplifiers, a **tube rectifier** (like a GZ34 or 5AR4) performs the same function as a diode bridge but adds "sag" -- the rectifier tube's internal resistance causes the supply voltage to drop under heavy current demand, compressing the signal in a musically pleasing way. This is one of the reasons tube amp players prefer tube rectification over silicon diode rectification.

### 4.5 Protection

Diodes protect circuits from reversed polarity (plug the battery in backwards and the diode blocks current instead of frying your circuit) and voltage spikes (TVS diodes and Zener diodes clamp dangerous transients).

**Reverse polarity protection** is the simplest protection: a diode in series with the power input. If the voltage is correct polarity, the diode conducts (dropping about 0.5V). If reversed, the diode blocks. The downside is the 0.5V voltage drop -- on a 9V supply, you lose 5.5% of your headroom. An alternative is a P-channel MOSFET protection circuit that drops only about 50 mV.

### 4.6 Zener Diodes and Voltage Regulation

A **Zener diode** is designed to conduct in reverse at a specific voltage (the **Zener voltage**). When reverse-biased beyond its Zener voltage, it maintains a nearly constant voltage across its terminals, regardless of the current flowing through it (within its ratings). This makes it useful as a simple voltage reference or regulator.

In some distortion circuits, Zener diodes are used for clipping because their reverse breakdown is more controlled and their forward voltage is lower than their Zener voltage, giving the designer more clipping threshold options. A 4.7V Zener clips at 4.7V in one direction and ~0.6V in the other -- inherently asymmetric, producing that desirable even-harmonic content.

> **What happens if...** you swap silicon diodes for germanium in a distortion pedal? The clipping threshold drops from ~0.6V to ~0.3V, meaning distortion kicks in at lower signal levels. The result is more distortion at quieter playing levels, a "squishier" feel, and a softer clipping knee (germanium diodes have a softer knee characteristic than silicon). This is why germanium-diode overdrives (like the classic Tube Screamer mods) sound and feel different from stock silicon versions. It's not magic -- it's physics and math.

---

## 5. Transistors: The Amplifiers and Switches

### 5.1 What a Transistor Is

A **transistor** is a semiconductor device that uses a small current or voltage to control a much larger current. It has three terminals and comes in two main families:

**BJT (Bipolar Junction Transistor)**: Has a **base**, **collector**, and **emitter**. A small current into the base controls a much larger current from collector to emitter. The current gain (β or hFE) is typically 50-500x. BJTs are current-controlled devices. They come in NPN (most common) and PNP polarities.

**FET (Field-Effect Transistor)**: Has a **gate**, **drain**, and **source**. A voltage on the gate controls current from drain to source. The gate draws virtually no current (or extremely little), making FETs easier to drive. FETs come in several sub-types:
- **MOSFET**: The most common modern FET. N-channel and P-channel varieties.
- **JFET**: Junction FET. Behaves similarly to vacuum tubes -- high input impedance, gradual clipping, pleasing harmonics. Beloved in audio for buffer circuits and tube-emulation pedals.

### 5.2 The Common-Emitter Amplifier: Transistor Gain 101

The **common emitter** amplifier is the most fundamental transistor audio circuit. Understanding it unlocks every other transistor topology. Here's how it works conceptually:

1. A BJT transistor is connected with its emitter to ground (through a resistor, Re), its collector to the positive supply (through another resistor, Rc), and the audio signal applied to the base through a coupling capacitor.

2. DC bias resistors (a voltage divider from the supply to ground, connected to the base) set the base voltage so the transistor is "on" but not saturated -- sitting in its linear region where small input changes produce proportional output changes.

3. The AC audio signal on the base causes small variations in base current.

4. These small base current variations cause large collector current variations (multiplied by the transistor's current gain, β).

5. The large collector current variations create large voltage swings across the collector resistor (V = I × Rc).

6. The output voltage swing at the collector is an amplified (and inverted -- the phase flips 180 degrees) version of the input.

**Voltage gain** of a common emitter stage (simplified): `Av ≈ -Rc / Re` (collector resistor divided by emitter resistor, with phase inversion).

With Rc = 10kΩ and Re = 1kΩ, gain is about -10 (20 dB). The negative sign means the output is inverted -- when the input goes positive, the output goes negative.

**Bypassing the emitter resistor**: If you put a capacitor across Re (called an "emitter bypass cap"), the AC impedance of the emitter drops to near zero at audio frequencies, while the DC resistance stays the same (maintaining bias stability). This dramatically increases the AC gain -- from Rc/Re to approximately β × Rc. A 10kΩ Rc with a transistor having β = 200 gives a gain of up to 200 × ... wait, that's too much. In practice, the gain is limited by other factors. But the point is: the emitter bypass cap is a powerful gain control, and many pedal designs use it (or omit it) to set the gain level.

### 5.3 BJT vs MOSFET vs JFET in Audio

| Characteristic | BJT | MOSFET | JFET |
|---|---|---|---|
| Control mechanism | Current (into base) | Voltage (on gate) | Voltage (on gate) |
| Input impedance | Moderate (1-10 kΩ) | Very high (>1 MΩ) | Very high (>1 MΩ) |
| Clipping character | Softer onset, gradual | Harder, more abrupt | Gradual, tube-like |
| Noise | Lower with low-Z sources | Lower with high-Z sources | Low, especially at low frequencies |
| Classic audio use | Fuzz, discrete preamps | Power amps, switching | Buffers, preamps, tube emulation |
| Famous circuits | Fuzz Face, Rangemaster | Most modern power amps | JFET buffer, Fetzer Valve |

Both BJTs and JFETs distort interestingly when pushed. BJT distortion tends to produce a mix of even and odd harmonics with a gradual onset -- this is the "fuzzy" character of a Fuzz Face. JFET distortion is often described as "tube-like" because JFETs share similar transfer characteristics with vacuum tubes (specifically, the square-law relationship between input voltage and output current). Neither is "better" -- they're different tools for different sonic goals.

### 5.4 The Transistor as a Switch

When a transistor is driven fully on (saturated) or fully off (cutoff), it acts as a switch. This is how:

- **True bypass switching** works in pedals: JFET or MOSFET transistors electronically switch the signal path, avoiding the mechanical wear and noise of physical switches.
- **Relay drivers** work: A small logic signal triggers a transistor that powers a relay coil, which then switches a high-current or high-voltage circuit.
- **LED indicators** work: A transistor switches the LED on and off under microcontroller control.

### 5.5 JFETs: A Special Case Worth Knowing

**JFETs** (Junction Field-Effect Transistors) deserve special attention for audio builders. They have characteristics similar to vacuum tubes: high input impedance, gradual clipping, and a pleasing harmonic structure when driven into distortion.

The **J201**, **2N5457**, and **2SK170** are legendary audio JFETs:
- J201: A depletion-mode N-channel JFET. Used in many pedal designs as a gain stage. Operates in its linear region with a simple self-biasing resistor.
- 2N5457: Another popular audio JFET, slightly different characteristics. Common in buffer circuits.
- 2SK170: An ultra-low-noise JFET. Used in high-end preamp input stages where noise performance is critical.

Unfortunately, many classic JFETs have been discontinued or are in short supply. The J201 was discontinued by ON Semiconductor, and while equivalents exist (and new production has resumed), availability and authenticity are ongoing concerns. This is a real practical challenge discussed in later chapters, along with substitution strategies.

### 5.6 Germanium Transistors: The Vintage Sound

Germanium transistors (like the OC44, AC128, 2N527) predate silicon technology and have distinct characteristics:
- Lower gain (typically 30-120 hFE)
- Higher leakage current (they "leak" even when off)
- Temperature sensitivity (gain and bias point shift with temperature)
- Softer clipping (more gradual transition into saturation)

These "flaws" create the sonic character of classic fuzz pedals like the Fuzz Face and Tonebender. The temperature sensitivity is real -- a germanium Fuzz Face literally sounds different on a hot stage versus a cold one. Modern builders sometimes add bias trim pots to compensate, or they select and match transistors for specific gain ranges.

> **What happens if...** you change the bias point of a transistor in a fuzz pedal? In a Fuzz Face circuit, the bias point is critical and sensitive. The classic Fuzz Face has a single bias resistor whose value determines the collector voltage of Q2. The "sweet spot" is usually around 4.5V (half the supply), but many players prefer it slightly off-center for asymmetric clipping character. Some Fuzz Face builds include a bias trim pot so you can dial in the exact flavor of fuzz. Turn it too far one way and the sound becomes gated and sputtery (transistor almost off). Turn it too far the other way and it gets mushy and undefined (transistor saturated). The sweet spot in between is what makes a great fuzz.

---

## 6. Op-Amps: The Swiss Army Knives

### 6.1 What an Op-Amp Is

An **operational amplifier** (op-amp) is an integrated circuit (IC) that contains a complete high-gain differential amplifier on a single chip. It has two inputs -- **inverting (-)** and **non-inverting (+)** -- and one output. It amplifies the *difference* between the two inputs by a very large factor (typically 100,000x or more in open-loop gain).

In practice, you never use an op-amp at its open-loop gain (that would be insanely sensitive -- a 1 μV difference at the inputs would try to produce 100V at the output, which is impossible, so it just slams to the supply rail). Instead, you use **negative feedback** -- connecting a portion of the output back to the inverting input -- to set a precise, controlled gain.

Think of an op-amp as a programmable amplifier building block. By changing the feedback network (just resistors and capacitors), you can configure it as a voltage amplifier, buffer, filter, mixer, oscillator, comparator, or dozens of other useful circuits. It's the standard library of analog electronics -- high-level, versatile, and well-documented.

### 6.2 The Key Op-Amp Concepts

**Negative feedback**: When you connect the output to the inverting input through a resistor, you create a self-correcting loop. If the output goes too high, the feedback pulls the inverting input up, which reduces the difference between the inputs, which reduces the output. This stabilizes the gain, reduces distortion, flattens frequency response, and makes the circuit behavior predictable and repeatable.

In software terms, negative feedback is a PID controller, a servo loop, or a thermostat. It continuously adjusts the output to minimize the error between the desired and actual values. The "desired" value is set by the non-inverting input, and the "actual" value is reported back through the feedback network.

**Virtual ground** (also called "virtual short"): When negative feedback is active, the op-amp forces the voltage at the inverting input to (very nearly) equal the voltage at the non-inverting input. If the non-inverting input is at 0V (ground), the inverting input *acts* as if it's at ground -- even though it's not directly connected to ground. This "virtual ground" is a powerful simplification that makes analyzing op-amp circuits straightforward.

**Gain-bandwidth product (GBW)**: An op-amp has a fixed gain-bandwidth product. If the GBW is 3 MHz, you can have a gain of 100 (40 dB) up to 30 kHz, or a gain of 10 (20 dB) up to 300 kHz. For audio (20 Hz - 20 kHz), most op-amps have plenty of bandwidth even at high gains. But for high-gain stages operating at the top of the audio band, or for fast transients, GBW can start to matter.

**Slew rate**: How fast the output voltage can change, measured in V/μs. A slew rate of 13 V/μs (TL072) means the output can change by 13 volts in one microsecond. For a 10V peak sine wave at 20 kHz, the maximum rate of change is 2π × 20000 × 10 = 1.26 V/μs. The TL072 has 10x margin. For an op-amp with a slew rate of only 0.5 V/μs (like the LM741), 20 kHz at 10V peak exceeds the slew rate, causing "slew-rate distortion" -- the waveform tips get rounded off. This is one reason why the LM741 is a poor choice for audio.

### 6.3 Common Op-Amp Audio Circuits

**Non-inverting amplifier**:
```
        ┌─── Rf ───┐
        │           │
Vin ──(+)   [Op-Amp]──── Vout
        (-)─── Rg ──┘
              │
             GND

Gain = 1 + (Rf / Rg)
```
Input goes to the non-inverting (+) input. Feedback resistor (Rf) and ground resistor (Rg) set the gain. Input impedance is extremely high (the op-amp's own input impedance, often >1 MΩ for JFET-input types). Output impedance is very low (<1Ω with feedback). Phase: non-inverting (0 degrees).

**Inverting amplifier**:
```
        ┌─── Rf ───┐
        │           │
Vin ── Rin ──(-)  [Op-Amp]──── Vout
              (+)
               │
              GND (or Vref)

Gain = -(Rf / Rin)
```
Input goes through Rin to the inverting (-) input. Non-inverting input goes to ground (or a voltage reference). Gain is set by Rf/Rin. Input impedance equals Rin (because the inverting input is at virtual ground, Rin is effectively between the input signal and ground). Phase: inverting (180 degrees).

**Buffer (voltage follower)**:
```
Vin ──(+)   [Op-Amp]──── Vout
        (-)───────────┘

Gain = 1 (0 dB)
```
Output connected directly to inverting input. Gain is exactly 1 -- no amplification. But the buffer converts a high-impedance source to a low-impedance output. This is the heart of a guitar buffer pedal: it takes the high-impedance guitar signal and makes it low-impedance, immune to cable capacitance and loading effects. The signal voltage doesn't change, but the signal's ability to drive loads improves dramatically.

**Summing amplifier (mixer)**:
```
V1 ── R1 ──┐
V2 ── R2 ──┤──(-)  [Op-Amp]──── Vout
V3 ── R3 ──┘  (+)      │
               │     ┌─Rf─┘
              GND    │
```
Multiple inputs through separate resistors to the inverting input. The output is the inverted, weighted sum of all inputs. If all input resistors are equal and Rf equals the input resistors, the output is -1 times the sum of all inputs. This is how audio mixing consoles work at the summing bus.

### 6.4 Active Filters with Op-Amps

By putting capacitors in the feedback network, you create frequency-dependent gain -- filters:

- **Low-pass**: A capacitor in parallel with Rf makes the feedback impedance decrease at high frequencies, reducing gain. Result: treble rolls off.
- **High-pass**: A capacitor in series with Rin blocks low frequencies from reaching the inverting input. Result: bass rolls off.
- **Band-pass**: Combine both techniques to pass a specific band.
- **Band-reject (notch)**: The inverse -- reject a specific band (useful for removing 60 Hz hum).

Active filters are more versatile than passive RC filters because the op-amp provides gain, buffering, and isolation. A second-order active filter (Sallen-Key or multiple-feedback topology) can achieve -12 dB/octave roll-off with controlled resonance, which is useful for speaker crossovers, graphic EQs, and parametric EQs.

### 6.5 Popular Audio Op-Amps

| Op-Amp | Supply Range | Noise (nV/√Hz) | Character | Common Use |
|---|---|---|---|---|
| LM386 | 4-12V (single) | High | Gritty, lo-fi, noisy but fun | Small guitar amps, lo-fi builds |
| TL072 / TL074 | ±5 to ±18V | 18 | Clean, JFET input, the workhorse | General pedals, preamps, mixers |
| NE5532 / NE5534 | ±5 to ±22V | 5 | Very low noise, can drive headphones | Preamps, headphone amps, pro audio |
| OPA2134 | ±2.5 to ±18V | 8 | Very low distortion, JFET input | Hi-fi preamps, DAC output stages |
| LM4562 | ±2.5 to ±17V | 2.7 | Ultra-low noise and distortion | Reference-grade audio, measurement |
| JRC4558 (RC4558) | ±4 to ±18V | 8 | The Tube Screamer op-amp, warm | Overdrive pedals, the legend |
| OPA1612 | ±2.25 to ±18V | 1.1 | State-of-the-art low noise | Professional microphone preamps |

For most pedal and preamp work, the TL072 is the default starting point. It's cheap ($0.50), widely available, has JFET inputs (high impedance, low bias current), adequate noise performance, and well-documented behavior. If noise is critical (microphone preamp), step up to the NE5532 or OPA1612.

### 6.6 Single-Supply Operation and Virtual Ground

Most op-amps are designed for dual-supply operation (e.g., +9V and -9V, giving an 18V total swing centered on 0V ground). But guitar pedals typically run from a single 9V supply. The solution: create a **virtual ground** at half the supply voltage (4.5V) using a voltage divider and buffer.

The standard circuit:
1. Two equal resistors (typically 100kΩ each) from +9V to ground, creating a 4.5V voltage divider.
2. A 10-100 μF capacitor from the 4.5V node to ground, providing a low-impedance AC ground reference.
3. Optionally, an op-amp buffer on the 4.5V node to provide even lower impedance.

The audio signal swings above and below this 4.5V reference instead of 0V. Coupling capacitors on the input and output strip off the 4.5V DC offset so the signal enters and exits at true ground reference.

This "biasing to half-supply" is so common in pedal design that it's almost universal. When you see a circuit with a 4.5V reference created by two equal resistors and a bypass capacitor, that's the virtual ground. It appears in the Tube Screamer, the Big Muff, the RAT, the Blues Driver, and hundreds of other circuits.

> **What happens if...** you run an op-amp on a single 9V supply without creating a virtual ground reference? The op-amp's output can only swing between the supply rails (0V and 9V). If the input is referenced to ground (0V), the op-amp can only swing positive -- it clips the entire negative half of the waveform. You get severe distortion and only half the signal comes through. The virtual ground at 4.5V lets the signal swing ±3.5V (limited by the op-amp's rail-to-rail capability), giving you the full available headroom.

---

## 7. Putting Components Together: Common Building Blocks

### 7.1 The Gain Stage

A gain stage is any circuit that increases signal voltage. It's the fundamental unit of amplification. Every audio device -- from a microphone preamp to a guitar amp -- is built from one or more gain stages chained together.

A single common-emitter transistor stage might provide 20-40 dB of gain. A single op-amp stage can provide anywhere from 0 to 60+ dB depending on the feedback resistors. By cascading multiple stages, you can achieve any total gain you need. Between stages, coupling capacitors block DC and prevent one stage's bias from disrupting another's.

The designer's challenge: each gain stage amplifies not only the signal but also any noise present at its input. This means the first gain stage in the chain is the most critical for noise performance -- noise introduced there gets amplified by every subsequent stage. This is why high-quality audio preamps (microphone preamps, phono preamps) use premium, ultra-low-noise components in the first stage.

### 7.2 The Tone Stack

A tone stack is a passive (or active) network of resistors and capacitors (sometimes inductors) that shapes the frequency response. The classic guitar amp tone stack (used in Fender, Marshall, and Vox amplifiers with slight variations) is a network of three potentiometers and two or three capacitors that provides interactive bass, mid, and treble control.

Understanding a tone stack requires everything from this chapter:
- Resistors (the pots) form voltage dividers
- Capacitors create frequency-dependent paths to ground
- The combination creates a complex frequency response that changes as you turn the knobs
- KVL and KCL govern the voltages and currents at each node

The Fender/Marshall tone stack has a characteristic behavior: it introduces significant signal loss (typically 15-25 dB of attenuation even with all knobs at maximum). This attenuation is compensated by the gain stages before and after the tone stack. It also means the "midrange" control on these amps is really a "mid scoop" control -- at minimum, mids are scooped; at maximum, they're less scooped but still not boosted.

We'll simulate actual tone stacks in Chapter 3 using LTspice, where you can see the frequency response change as you sweep the virtual knobs.

### 7.3 The Power Supply

Every active circuit needs DC power. A basic power supply for a guitar pedal is just a 9V battery or wall adapter. But amplifiers need more sophisticated supplies:

1. **Transformer**: Steps mains voltage (120/240V AC) down (or up, for tube amps) to the needed voltage.
2. **Rectifier**: Diodes (or a tube rectifier) convert AC to pulsating DC.
3. **Filter capacitors**: Large electrolytic caps smooth the pulsating DC into steady DC. The ripple voltage (the AC component remaining after filtering) should be less than 1% of the DC voltage for audio applications.
4. **Voltage regulator**: An IC (78xx series for positive, 79xx for negative, LM317/337 for adjustable) or discrete circuit that maintains a constant output voltage regardless of load variations and mains voltage fluctuations.

The quality of the power supply directly affects the audio quality. A noisy power supply creates hum and buzz in the audio signal (120 Hz hum from full-wave rectification, 60 Hz from half-wave). A power supply that can't deliver enough current causes distortion when the signal demands peak current. This is why serious audio builds pay careful attention to power supply design -- it's not glamorous, but it's foundational.

### 7.4 The Output Stage

The output stage is the final amplification stage that drives the load (speaker, headphones, or the next device's input). Output stages must deliver significant current (unlike voltage-gain stages, which deal in microamps).

Common output stage topologies:
- **Emitter follower** (BJT) or **source follower** (MOSFET/JFET): Gain of ~1 (unity voltage gain), but converts high impedance to low impedance. Used in buffers and headphone amps.
- **Push-pull**: Two transistors (or tubes), one handling the positive half of the waveform and one handling the negative half. More efficient and powerful. Used in most amplifiers.
- **Class A**: The output device conducts during the entire signal cycle. Low distortion, low efficiency (25-50%), runs hot. Prized in some hi-fi circles and in certain guitar amp designs.
- **Class AB**: A compromise between Class A (low distortion) and Class B (high efficiency). The output devices conduct for slightly more than half the cycle. This is the most common topology for audio power amplifiers.
- **Class D**: A switching amplifier. Very efficient (>90%), compact, cool-running. Used in modern powered speakers, subwoofer amps, and portable devices. The audio signal modulates a high-frequency (several hundred kHz) switching waveform, and a low-pass filter recovers the audio at the output.

---

## 8. Component Selection for Audio: A Practical Guide

### 8.1 The Decision Framework

When building or modifying an audio circuit, here's how to think about component selection:

**Resistors**: Use metal film (1% tolerance) for the signal path. They're cheap ($0.01 each), quiet (low thermal noise), and precise. Carbon film or carbon composition resistors are noisier but some builders prefer their character in certain vintage-style circuits (the noise adds a subtle "warmth" that's really just noise and nonlinearity -- fine if that's the sound you want). For power applications (load resistors in speaker crossovers, bleeder resistors in power supplies), use wire-wound or metal oxide resistors rated for the expected power dissipation with at least 2x margin.

**Capacitors**: Use film caps (polyester or polypropylene) in the signal path where possible. Use ceramics (C0G/NP0 only!) for small values in high-frequency circuits. Avoid X7R/Y5V ceramics in the signal path. Use electrolytics only for power supply filtering and large-value coupling caps where film would be impractically large (anything above about 1 μF in a small enclosure). Always use voltage ratings of at least 2x the expected voltage for reliability margin.

**Diodes**: For clipping, choose based on the clipping threshold and character you want (see section 4.2). For power supply rectification, any standard silicon diode rated for the voltage and current will work (1N4001-1N4007 series for low power, bridge rectifiers for higher power). For protection, use a diode rated for the expected fault current.

**Transistors**: For preamp gain stages, choose based on the circuit design and the desired tonal character. For switching, any small-signal transistor that can handle the current is fine. For power amplification, choose based on the voltage, current, and power dissipation requirements, with attention to safe operating area (SOA) curves in the datasheet.

**Op-amps**: Start with the TL072 (dual) or TL074 (quad) for general audio work. Upgrade to NE5532 if noise is critical. Use the LM386 only for intentionally lo-fi builds or when single-supply, simple amplification is the goal. For microphone preamps, consider the OPA1612 or equivalent ultra-low-noise type.

### 8.2 Component Sourcing

Buy components from reputable distributors:
- **Mouser** and **Digikey**: The two dominant US-based electronics distributors. Huge selection, genuine parts, datasheets for everything.
- **Tayda Electronics**: Budget-friendly, popular in the pedal-building community. Good for common parts; limited selection for specialty components.
- **Amazon/eBay/AliExpress**: Risky for semiconductors. Counterfeit transistors and op-amps are rampant on these platforms. A "2N3904" from AliExpress might be relabeled scrap. Buy passive components (resistors, caps) from anywhere, but buy semiconductors from authorized distributors.

> **What happens if...** you build a "high-end" pedal with the most expensive components available? You spend a lot of money and the pedal sounds... basically the same as one built with standard quality parts, as long as the standard parts are appropriate for the application. A $3 polypropylene capacitor in a guitar pedal's tone circuit won't sound meaningfully different from a $0.10 polyester film cap to 99% of listeners in a blind test. Spend your money on design quality, proper PCB layout, and good mechanical construction -- not on capacitor brands. (Speaker crossovers are one exception where component quality genuinely matters, because the components carry significant power and the tolerances directly affect the crossover frequency and driver integration.)

---

## 9. Exercises: Building Component Intuition

### 9.1 Filter Design Exercises

1. **Design a coupling capacitor**: You need a coupling cap between a guitar buffer (output impedance 1kΩ) and a gain stage (input impedance 1MΩ). The cutoff frequency should be below 10 Hz. What minimum capacitor value do you need?
   - The impedance the cap "sees" is roughly 1kΩ (output impedance dominates when much smaller than input impedance)
   - f_c = 1/(2π × R × C), so C = 1/(2π × R × f_c) = 1/(2π × 1000 × 10) = 15.9 μF
   - That's large. But wait -- the relevant impedance is really the parallel combination of 1kΩ and 1MΩ, which is essentially 1kΩ. For a 10 Hz cutoff into 1kΩ, you need ~16 μF.
   - However, if the source impedance is lower (say 100Ω from a good op-amp buffer), you'd need 10× more: 160 μF. That's impractical.
   - The real lesson: the relevant resistance is the *total* impedance the cap sees. In practice, a 1 μF coupling cap works when the next stage has high input impedance (1MΩ): f_c = 1/(2π × 1M × 1μ) = 0.16 Hz. Well below audible.

2. **Design a tone control**: You want a simple low-pass filter (capacitor to ground) that starts rolling off at approximately 3 kHz. The source impedance at that point in the circuit is 10kΩ. What capacitor value?
   - C = 1/(2π × 10000 × 3000) = 5.3 nF. Nearest standard value: 4.7 nF (cutoff at 3.4 kHz) or 5.6 nF (cutoff at 2.8 kHz). Either works.

3. **Predict the effect**: A guitar player changes the tone cap from 22 nF to 47 nF. With a 250kΩ tone pot at a 50% setting (125kΩ to ground):
   - Old: f_c = 1/(2π × 125000 × 0.000000022) = 58 Hz
   - New: f_c = 1/(2π × 125000 × 0.000000047) = 27 Hz
   - The cutoff moved lower, but at the same pot position, the 47 nF cap will roll off more aggressively at any given frequency above its cutoff. The guitar will sound darker at the same tone pot setting.

### 9.2 Gain Calculations

1. An inverting op-amp amplifier has Rf = 100kΩ and Rin = 10kΩ. What is the gain in dB?
   - Gain = -Rf/Rin = -100k/10k = -10. |Gain| = 10. In dB: 20 × log10(10) = 20 dB. The output is inverted.

2. You want 40 dB of gain from a non-inverting amplifier. If Rg = 1kΩ, what value of Rf do you need?
   - 40 dB = 100× voltage gain. For non-inverting: Gain = 1 + Rf/Rg = 100. So Rf/Rg = 99. Rf = 99 × 1kΩ = 99kΩ. Nearest standard: 100kΩ (gain = 101, or 40.1 dB -- close enough).

3. A common-emitter transistor stage has Rc = 10kΩ and Re = 470Ω. What is the approximate voltage gain?
   - Av ≈ Rc/Re = 10000/470 = 21.3, or about 26.5 dB. With the emitter resistor bypassed by a capacitor, gain increases to approximately β × Rc/(β × Re_ac + re), where re is the transistor's internal emitter resistance (~26mV/Ic). For Ic = 1mA, re ≈ 26Ω. Without Re in the AC path: Av ≈ Rc/re = 10000/26 ≈ 385 (about 52 dB). Much more gain but also much less stable.

### 9.3 Clipping Threshold Exercise

A Tube Screamer-style circuit has a gain stage with clipping diodes in the feedback loop. The diodes are standard silicon (1N4148, Vf ≈ 0.6V). 

1. At what output voltage does clipping begin?
   - The diodes start conducting when the voltage across them exceeds ~0.6V. Since they're in the feedback loop, the output clips at approximately ±0.6V (two antiparallel diodes clip both halves at 0.6V).
   - Actually, in the TS circuit, the clipping voltage is the *sum* of the diode drop and the voltage across the feedback resistor at the clipping point. But in simplified terms: the signal at the inverting input is at virtual ground, so the voltage across the diode is approximately the output voltage divided by the feedback network. The exact clipping level depends on the gain setting.

2. If you replace the silicon diodes with LEDs (Vf ≈ 1.7V), what happens?
   - Clipping threshold increases from ~0.6V to ~1.7V. The signal stays clean at much higher levels. When it does clip, the transition is harder (LEDs have a sharper knee). The result: more headroom, less compression, harder clipping when it occurs. This is the DOD OD-250 / Bluesbreaker character.

### 9.4 Quick Component Identification

For each description, identify the component and its role:

1. A 100 nF ceramic cap from an op-amp's pin 8 to ground: **Bypass/decoupling capacitor**. Provides local high-frequency current to the IC and filters power supply noise.

2. Two 100kΩ resistors in series from +9V to ground, with a 10 μF cap from the midpoint to ground: **Virtual ground generator**. Creates a 4.5V reference for single-supply op-amp biasing.

3. A 1MΩ resistor from input to ground, before the coupling cap: **Input pull-down resistor**. Prevents "popping" when plugging in (drains any charge on the input coupling cap) and defines the input impedance.

4. A diode in series with the positive power supply lead, cathode toward the circuit: **Reverse polarity protection**. Blocks current if the power supply is connected backwards.

5. A 47 nF capacitor in series with a 500kΩ pot, from the signal path to ground: **Guitar tone control**. The pot varies the effective resistance to ground, controlling how much treble is bled off.

---

## Key Takeaways

1. **Resistors** oppose current equally at all frequencies. They set gain, bias, and signal levels. Metal film is the default for audio.
2. **Capacitors** pass high frequencies and block low frequencies (and DC). They're in every coupling, filtering, and tone-shaping circuit. The cutoff frequency is f = 1/(2π×R×C).
3. **Inductors** pass low frequencies and block high frequencies -- the mirror of capacitors. They're essential in speaker crossovers and wah pedals.
4. **Diodes** clip signals for distortion (silicon, germanium, LED, Schottky all sound different), rectify AC for power supplies, and protect circuits from damage.
5. **Transistors** amplify signals and act as switches. BJTs, MOSFETs, and JFETs each have distinct audio characteristics. Germanium transistors add vintage character.
6. **Op-amps** are versatile amplifier building blocks configured by external feedback networks. The TL072 is the audio workhorse.
7. **Common building blocks** repeat everywhere: gain stage, tone stack, power supply, output stage. Learn to recognize and design each one.
8. **Component selection** matters, but topology and design matter more. Don't over-spend on exotic parts for marginal gains.

---

*Previous: [Chapter 0 -- Electricity Through the Lens of Sound](00-electricity-through-sound.md)*
*Next: [Chapter 2 -- Reading Schematics and Datasheets](02-reading-schematics-datasheets.md)*
