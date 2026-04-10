<!--
  CHAPTER: 5
  TITLE: How Effects Shape Sound
  PART: 1 — Guitar Pedals
  PREREQS: Part 0 (Chapters 0-4)
  KEY_TOPICS: audio signal path, gain staging, frequency spectrum, harmonics, clipping types, time-based effects, modulation effects, filtering and EQ, buffered vs true bypass, impedance in pedal chain
  DIFFICULTY: Beginner
  UPDATED: 2026-04-10
-->

# Chapter 5: How Effects Shape Sound

> **Part 1 — Guitar Pedals** | Prerequisites: Part 0 (Chapters 0-4) | Difficulty: Beginner

Welcome to the first domain tower. You made it through the electronics fundamentals, and now we're going to do something with them that you can actually *hear*. Guitar pedals are the perfect starting point for audio electronics because they're low voltage (usually 9V), cheap to prototype, quick to build, and the feedback loop is instant: plug in, strum, listen. You'll know in a second whether what you built is working.

Before we touch a single component, though, we need to understand what effects actually do to your signal. Not in a "this pedal makes your guitar sound crunchy" way — in a "here's the math and physics of what's happening to the electrical waveform" way. Because once you understand that, you can design effects instead of just copying them.

This chapter is your map of the territory. We'll trace the signal from the moment your pick hits the string to the moment sound comes out of your amp, and we'll see how every category of effect — distortion, delay, modulation, filtering — manipulates that signal in a specific, predictable, and designable way. By the end, you'll look at a pedalboard and see not a chain of mystery boxes, but a signal processing pipeline where each stage has a clear electrical function.

### In This Chapter
- The audio signal path from guitar pickup to amplifier
- Gain staging and what happens when you chain effects
- Frequency spectrum, harmonics, and timbre
- Clipping types: soft, hard, diode, MOSFET
- Time-based effects: delay, reverb, echo
- Modulation effects: tremolo, chorus, phaser, flanger
- Filtering and EQ: low-pass, high-pass, band-pass, shelving
- Buffered vs true bypass
- Impedance in the pedal chain and why order matters

### Related Chapters
- [Ch 0: Electricity Through the Lens of Sound](../part-0-electronics/00-electricity-through-sound.md) — Voltage, impedance, frequency fundamentals
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) — Resistors, caps, diodes, transistors
- [Ch 6: Gain Stages and Clipping Circuits](06-gain-stages-clipping-circuits.md) — Deep dive into the circuits behind gain and clipping
- [Ch 12: The Language of Speakers](../part-2-speakers/12-language-of-speakers.md) — Impedance concepts revisited for speakers
- [Ch 18: How Amplifiers Work](../part-3-amplifiers/18-how-amplifiers-work.md) — Impedance and gain staging at amplifier scale

---

## 1. The Audio Signal Path

### 1.1 From String to Speaker

Let's trace the complete journey of your guitar signal, because understanding this chain is the key to understanding where effects fit in and what they're actually operating on.

**The chain:**

```
Guitar String Vibration
    → Pickup (magnetic → electrical conversion)
        → Guitar Cable (transmission line)
            → Pedal Input Jack (impedance boundary)
                → Effect Circuit (signal processing)
            → Pedal Output Jack (impedance boundary)
        → Amp Cable
    → Amplifier Input
        → Preamp (voltage gain)
            → Tone Stack (EQ)
                → Power Amp (current gain)
                    → Speaker (electrical → mechanical → acoustic)
                        → Your Ears (acoustic → neural)
```

Each arrow represents a point where something happens to the signal. Some of these are intentional (the effect circuit), some are unavoidable (cable capacitance), and some are both (tube amp saturation). As a pedal designer, you care about every stage from the guitar cable onward.

### 1.2 What the Guitar Actually Outputs

Your guitar pickup is an electromagnetic transducer — a fancy way of saying it converts one form of energy (vibrating metal string in a magnetic field) into another (a tiny alternating voltage on a wire). Here's what matters about that signal:

- **Voltage level:** Roughly 100mV to 1V peak-to-peak for a typical humbucker, less for single coils. This is a *tiny* signal. For reference, a standard "line level" audio signal is about 1.7V peak-to-peak. Your guitar is much quieter than what most audio equipment expects.
- **Frequency range:** The fundamental of the lowest note on a standard-tuned guitar (low E) is about 82 Hz. The highest fundamental (24th fret, high E) is about 1,319 Hz. But the *harmonics* extend much higher — up to around 5-6 kHz for the musically useful ones, with some content out to 10 kHz or beyond.
- **Impedance:** A typical single-coil pickup has an output impedance of around 6-8 kilohms (kohm). A humbucker is typically 8-16 kohm. This is *high* compared to most audio sources, and it matters enormously for pedal design.
- **Waveform:** Not a sine wave. Not even close. A plucked guitar string produces a complex waveform rich in harmonics, and the pickup's inductance and capacitance further shape that waveform. It's this complexity that makes guitar tone so varied and interesting.

### 1.3 The Cable: Your First (Unintentional) Filter

Here's something most guitarists don't think about: your guitar cable is a low-pass filter. Every cable has capacitance — typically 15-40 picofarads per foot (pF/ft). A 20-foot cable at 30 pF/ft gives you 600 pF of capacitance. Combined with your pickup's inductance, this forms a resonant LC circuit that rolls off high frequencies.

The longer the cable, the more high-frequency loss. This is why some players swear their tone is "better" with shorter cables — they're hearing less high-frequency rolloff, which sounds brighter and more present. It's not magic; it's a simple RC filter formed by the cable capacitance and the pickup impedance.

This is also why buffered pedals exist, but we'll get to that in section 8.

> **What happens if...** you run a 50-foot cable from your guitar to your amp with no buffer? You'd have roughly 1,500 pF of cable capacitance. With a humbucker at 10 kohm output impedance, the -3 dB cutoff frequency drops noticeably — you'd hear a distinctly darker, less articulate tone. Some players actually prefer this (Brian May's long cable run is part of his sound), but it should be a choice, not an accident.

---

## 2. Gain Staging

### 2.1 What Gain Actually Means

**Gain** is the ratio of output signal amplitude to input signal amplitude. If a circuit takes a 100mV signal and turns it into a 1V signal, it has a voltage gain of 10 (or 20 dB). That's it. No mystery.

In audio, we usually express gain in **decibels (dB)** because our ears perceive loudness logarithmically. Quick reference from [Ch 0](../part-0-electronics/00-electricity-through-sound.md):

| Voltage Gain | dB |
|---|---|
| 1 (unity) | 0 dB |
| 2 | +6 dB |
| 10 | +20 dB |
| 100 | +40 dB |
| 0.5 | -6 dB |
| 0.1 | -20 dB |

Every pedal in your chain has a gain — and it might not be greater than 1. A passive tone control has a gain less than 1 (it only attenuates). A wah pedal boosts some frequencies and cuts others. A fuzz might have a gain of 100 or more.

It's worth noting that gain can be *frequency-dependent*. A tone control that passes bass at full volume but cuts treble by 10 dB has a gain of 1 (0 dB) at low frequencies and a gain of 0.32 (-10 dB) at high frequencies. An equalizer is literally a device that has different gain at different frequencies. When people say a Tube Screamer has a "mid hump," they mean its gain is highest in the midrange — maybe 40 dB at 1 kHz, but only 20 dB at 100 Hz and 20 dB at 5 kHz. Same circuit, frequency-dependent gain.

### 2.2 Chaining Effects: The Cascade

When you chain pedals, the gains multiply. If Pedal A has a gain of 10 and Pedal B has a gain of 5, the total gain is 50 (not 15). In dB, gains *add*: 20 dB + 14 dB = 34 dB.

This is why pedal order matters *electrically*, not just sonically. Consider two scenarios:

**Scenario A: Boost (gain = 10) → Overdrive (clips at 1V)**
- Guitar outputs 100mV
- Boost amplifies to 1V
- Overdrive receives 1V — right at its clipping threshold
- Result: Heavy overdrive, the boost is "pushing" the overdrive harder

**Scenario B: Overdrive → Boost**
- Guitar outputs 100mV
- Overdrive receives 100mV — well below its clipping threshold
- Overdrive outputs maybe 200mV with slight clipping
- Boost amplifies to 2V
- Result: Louder signal with only slight overdrive character

Same two pedals, completely different sound. The difference isn't subjective taste — it's the physics of where in the chain the signal exceeds the clipping threshold.

### 2.3 Headroom and the Noise Floor

Every analog circuit has two boundaries:

1. **The noise floor** — the level below which your signal gets lost in the circuit's inherent noise (thermal noise from resistors, shot noise from semiconductors, etc.). Typically -90 dB to -60 dB below the maximum signal level.
2. **The clipping threshold** — the level above which the circuit can't faithfully reproduce the signal and starts distorting. In a 9V pedal circuit biased at 4.5V, the maximum clean swing is roughly 4V peak-to-peak before the signal clips against the supply rails.

The distance between these two boundaries is your **dynamic range** or **headroom**. A 9V pedal powered by a battery has less headroom than an 18V pedal. This is why some pedals (like the Klon Centaur) use an internal charge pump to double the supply voltage — more headroom means cleaner operation before clipping.

> **What happens if...** you power a pedal designed for 9V with 18V? If the components can handle it (check the op-amp's maximum supply voltage!), you get roughly twice the headroom — 6 dB more clean swing before clipping. The overdrive character changes: it stays cleaner longer and clips more abruptly. Some players prefer this. Others don't. The point is you understand *why* it sounds different.

### 2.4 Real-World Signal Levels

Here's a practical reference for the signal levels you'll encounter at each point in the guitar-to-amp chain:

| Point in Chain | Typical Signal Level | Level in dBV |
|---|---|---|
| Single-coil pickup (normal playing) | 50-200 mV peak | -26 to -14 dBV |
| Humbucker pickup (normal playing) | 100-500 mV peak | -20 to -6 dBV |
| Active pickup (EMG, Fishman) | 200-1000 mV peak | -14 to 0 dBV |
| After a clean boost (+15 dB) | 500-3000 mV peak | -6 to +10 dBV |
| After a fuzz/distortion (clipped) | ~600 mV peak (limited by diodes) | -4 dBV |
| After a Tube Screamer (clipped) | ~600 mV peak | -4 dBV |
| Line level (pro audio standard) | 1230 mV peak (+4 dBu) | +2 dBV |
| Amplifier clipping threshold | Varies (2-4V typical for input stage) | +6 to +12 dBV |

Understanding these levels helps you design pedals with appropriate gain. If your guitar outputs 100mV and you want to hit a Tube Screamer's clipping diodes at 600mV, you need a gain of at least 6 (about 16 dB) before the diodes. If the TS has a maximum gain of 100 (40 dB), it'll hit the diodes with plenty of headroom at any drive setting above about 40%.

### 2.5 Gain Staging Exercise in Falstad

Open the Falstad circuit simulator and build this simple chain:

1. An AC voltage source (100mV peak, 440 Hz) representing your guitar
2. An amplifier stage with gain of 10
3. A pair of anti-parallel diodes to ground (representing clipping)
4. A second amplifier stage with gain of 5

Observe the waveform at each stage:
- After stage 1: A clean, amplified 1V peak sine wave
- After the diodes: A clipped waveform limited to ~600mV (silicon diode Vf)
- After stage 2: The clipped waveform, amplified 5x to ~3V peak

Now rearrange: put both amplifiers before the diodes (total gain of 50, then clip). The signal hits the diodes at 5V, far above the 600mV threshold. The clipping is much harder — the diodes chop off a larger percentage of the waveform. This sounds more distorted, more compressed, and more harmonically rich than the first arrangement.

Same total gain. Different gain staging. Different sound. This is why the order of stages inside a pedal matters as much as the order of pedals on your board.

---

## 3. Frequency Spectrum and Harmonics

### 3.1 Why a Guitar Doesn't Sound Like a Flute

When you pluck the low E string on your guitar, the fundamental frequency is 82.4 Hz. But you're not hearing a pure 82.4 Hz sine wave — if you were, it would sound like a tuning fork or a sine wave generator. Bland. What makes your guitar sound like a guitar is the **harmonics** (also called **overtones** or **partials**): additional frequencies that ring out simultaneously.

The harmonics of 82.4 Hz are:
- 2nd harmonic: 164.8 Hz (one octave up)
- 3rd harmonic: 247.2 Hz (an octave and a fifth)
- 4th harmonic: 329.6 Hz (two octaves)
- 5th harmonic: 412.0 Hz
- ...and so on, getting progressively quieter

The specific *mixture* of these harmonics — their relative amplitudes — is what gives an instrument its **timbre** (pronounced "TAM-ber"). A flute playing the same note has different harmonic amplitudes. A trumpet has yet another set. Your guitar's timbre depends on the pickup type, pickup position, string material, pick attack, and more.

### 3.2 Why This Matters for Effects

Every effect you design will alter the harmonic content of the signal. This is fundamentally what "shaping sound" means:

- **Distortion/overdrive** adds new harmonics that weren't in the original signal (harmonic distortion — we'll explore this deeply in section 4)
- **EQ and filtering** change the relative levels of existing harmonics
- **Modulation** shifts harmonics in time or pitch
- **Delay/reverb** repeat the entire harmonic structure at different times

Understanding harmonics is the key to understanding why two distortion pedals with the same gain level sound completely different. It's about *which* harmonics they add and in what proportions.

### 3.3 Even vs Odd Harmonics

This distinction comes up constantly in audio design:

- **Even harmonics** (2nd, 4th, 6th...): Generally perceived as "warm," "musical," "pleasant." Tube amps are famous for producing predominantly even-order harmonic distortion. The 2nd harmonic is an octave above the fundamental — it sounds consonant and reinforcing.
- **Odd harmonics** (3rd, 5th, 7th...): In small amounts, they add "presence" and "edge." In large amounts, they sound "harsh," "buzzy," or "aggressive." The 3rd harmonic is an octave and a fifth — still fairly consonant. But the 7th and above start sounding dissonant.

When we get to clipping circuits in [Chapter 6](06-gain-stages-clipping-circuits.md), you'll see exactly why symmetrical clipping produces odd harmonics and asymmetrical clipping produces even harmonics. It's a direct consequence of the math — Fourier analysis of the clipped waveform.

### 3.4 Seeing Harmonics: A SPICE Exercise

You can visualize harmonics in LTspice or Falstad. Here's a revealing exercise:

1. **Create a pure sine wave** at 440 Hz (A4). Run a transient simulation and look at the waveform — it's a smooth, single-frequency curve.

2. **Run a Fourier analysis** (in LTspice: `.four 440 V(out)`). You'll see a single spike at 440 Hz and nothing else. This is a "pure" tone — no harmonics.

3. **Now clip it.** Add a pair of diodes (1N4148, anti-parallel) from the output to ground. Run the transient again — the waveform now has flat tops and bottoms. Run the Fourier analysis — suddenly you see spikes at 440 Hz, 1320 Hz (3rd harmonic), 2200 Hz (5th harmonic), 3080 Hz (7th harmonic), etc. The clipping *created* those harmonics. They didn't exist in the original signal.

4. **Compare soft and hard clipping.** Put the diodes in an op-amp feedback loop (soft clipping) and note the harmonic levels. Then move them to ground (hard clipping). The hard-clipped version has significantly more energy in the higher harmonics (5th, 7th, 9th). This is the physical basis for "hard clipping sounds harsher."

This exercise makes the abstract concept of harmonics concrete and visible. The harmonics aren't metaphorical — they're real frequency components that appear in the signal when you clip it. Your ear hears them as "distortion character," but a spectrum analyzer shows them as distinct peaks at integer multiples of the fundamental.

### 3.5 Intermodulation Distortion: The Ugly Cousin

When you play a single note through a distortion pedal, the harmonics are all mathematically related to the fundamental (integer multiples). They sound "musical" because they correspond to notes in the harmonic series.

But when you play a chord — two or more notes simultaneously — something less pleasant happens. The distortion creates **intermodulation products**: frequencies that are sums and differences of the input frequencies and their harmonics. For example, if you play A (440 Hz) and E (660 Hz) through a distortion:

- Sum: 440 + 660 = 1100 Hz
- Difference: 660 - 440 = 220 Hz
- And combinations involving harmonics: 880 + 660 = 1540 Hz, etc.

These intermodulation products are *not* harmonically related to the input notes. They sound dissonant and "muddy." This is why:

- Heavy distortion sounds great on single notes and power chords (notes a fifth apart have simple harmonic relationships)
- Heavy distortion sounds terrible on complex chords (the intermodulation products clash with the chord tones)
- The Tube Screamer's mid hump *helps* with this — by rolling off bass before clipping, it reduces the number of low-frequency notes entering the distortion, which reduces intermodulation

Understanding intermodulation is the difference between designing a distortion that sounds "musical" on chords and one that sounds like a swarm of angry bees.

> **What happens if...** you play a full open C chord through a high-gain fuzz with lots of bass? The low E (82 Hz), C (131 Hz), E (165 Hz), G (196 Hz), C (262 Hz), and E (330 Hz) all intermodulate with each other, creating dozens of non-harmonic sum and difference frequencies. The result: a dense, indistinct wall of noise. Now play the same chord through a Tube Screamer with its bass-cut input — the 82 Hz and 131 Hz fundamentals are attenuated, dramatically reducing the intermodulation products. The chord sounds cleaner and more defined, even though it's still distorted. This is frequency-dependent clipping at work.

---

## 4. Clipping Types and How They Sound

### 4.1 What Clipping Actually Is

Clipping occurs when a circuit tries to produce an output voltage that exceeds what it's physically capable of producing. If an op-amp is powered from a 9V supply (biased at 4.5V), it can swing maybe 3.5V above and 3.5V below the bias point. Any signal that would need to go beyond that gets "clipped" — flattened at the maximum voltage.

Imagine drawing a sine wave and then cutting off the tops and bottoms with scissors. That's clipping. The sharper the cut, the more harmonics you add. The shape of the cut determines *which* harmonics.

### 4.2 Soft Clipping

**Soft clipping** happens when the signal is gradually compressed as it approaches the maximum. Instead of hitting a hard wall, the waveform gets gently rounded off. Think of it like squeezing a sine wave through a tube that narrows gradually — the peaks get smoother, not abruptly chopped.

**How it sounds:** Smooth, warm, "tube-like" overdrive. The compression is gradual, so the transition from clean to distorted is musical. Think Tube Screamer, Blues Driver, most "overdrive" pedals.

**Electrically:** Soft clipping often comes from diodes placed in the *feedback loop* of an op-amp, or from the natural saturation characteristics of a vacuum tube or transistor. When the signal is small, the diodes don't conduct and the circuit amplifies normally. As the signal grows, the diodes begin to conduct progressively, reducing the effective gain and rounding off the peaks.

**Harmonic content:** Predominantly lower-order harmonics (2nd, 3rd). The gradual onset means less high-order harmonic content, which translates to a smoother sound.

### 4.3 Hard Clipping

**Hard clipping** happens when the signal hits an abrupt ceiling. The peaks are sheared off flat. Instead of a gradual rounding, you get a sharp transition from the waveform to a flat line at the maximum voltage.

**How it sounds:** Aggressive, buzzy, "in your face." More obvious distortion. Think ProCo RAT, MXR Distortion+, most "distortion" pedals (as opposed to "overdrive").

**Electrically:** Hard clipping often comes from diodes connected to *ground* (not in the feedback loop). The signal is first amplified, then the diodes shunt everything above their forward voltage to ground. It's a brick wall: the signal goes up, hits the diode's forward voltage (~0.6V for silicon, ~0.3V for germanium), and stops.

**Harmonic content:** Rich in higher-order harmonics, including both odd and even depending on symmetry. The sharp transitions in the waveform create a lot of harmonic energy.

### 4.4 Diode Clipping

Diodes are the workhorse of guitar pedal clipping. Different diode types have different forward voltages and different curves:

| Diode Type | Forward Voltage | Clipping Character |
|---|---|---|
| Silicon (1N4148, 1N914) | ~0.6V | Medium threshold, moderate hardness |
| Germanium (1N34A, OA91) | ~0.3V | Low threshold, soft onset — "vintage" |
| LED (various) | ~1.8-2.2V | High threshold, more headroom, "open" |
| Schottky (BAT41, 1N5817) | ~0.2-0.4V | Very low threshold, early onset |
| MOSFET (as a clipping element) | Varies | Asymmetric, tube-like characteristics |

The forward voltage determines *how much* signal gets through before clipping starts. Lower forward voltage = earlier clipping = more distortion at lower input levels. Higher forward voltage = more clean signal before distortion kicks in = more "headroom."

### 4.5 MOSFET Clipping

MOSFET transistors can be used as clipping elements, and they produce a transfer characteristic that resembles tube amplifier distortion more closely than simple diode clipping. This is because the MOSFET's drain current has an asymmetric relationship with gate voltage — it clips differently on positive and negative swings.

The result is **asymmetric clipping** — one half of the waveform is clipped differently than the other. As we discussed, this produces even-order harmonics (the "warm" ones). Several pedals use MOSFETs specifically for this quality.

### 4.6 Symmetrical vs Asymmetrical Clipping

If both halves of the waveform are clipped identically (same diodes, same threshold for positive and negative swings), the clipping is **symmetrical**. This produces predominantly **odd harmonics**.

If one half clips more than the other (different diodes for each polarity, or different thresholds), the clipping is **asymmetrical**. This adds **even harmonics** to the mix.

Many classic pedals use asymmetric clipping: the Klon Centaur uses a germanium and a silicon diode in anti-parallel (different forward voltages), and the Marshall Bluesbreaker uses different diode counts in each direction. The result is a blend of even and odd harmonics that many players find more "amp-like."

> **What happens if...** you take a Tube Screamer clone (symmetrical silicon diodes) and replace one of the clipping diodes with an LED? Now one side clips at 0.6V and the other at ~1.8V. The result: the signal clips heavily on one polarity and barely at all on the other. This adds strong even-order harmonics and changes the compression feel. The distortion becomes noticeably asymmetric in both sound and in the waveform you'd see on an oscilloscope (or in LTspice).

---

## 5. Time-Based Effects

### 5.1 Delay

The concept is simple: take the input signal, store a copy, and play it back after a specified time. The result is an echo — you hear the original note followed by a repeat.

**Key parameters:**
- **Delay time:** How long before the repeat. Short (20-50ms) creates slapback. Medium (100-400ms) creates rhythmic echoes. Long (500ms+) creates ambient washes.
- **Feedback (repeats):** How much of the delayed signal gets fed back into the delay input. Zero feedback = one repeat. Some feedback = multiple decaying repeats. Too much feedback = runaway oscillation (which some players do intentionally).
- **Mix (wet/dry):** The ratio of original signal to delayed signal.

**Analog vs digital delay:** Early delays used **bucket brigade devices (BBDs)** — analog chips that pass the signal through hundreds of tiny capacitor stages, each holding a sample of the voltage. Modern delays use digital conversion (ADC → memory → DAC) or dedicated delay chips like the PT2399. We'll build a PT2399 delay in [Chapter 10](10-build-modulation-time-effects.md).

The sonic difference: analog delays degrade slightly with each repeat (high frequencies roll off, noise accumulates), which many players find more natural. Digital delays can repeat perfectly forever.

### 5.2 Reverb

Reverb is conceptually similar to delay, but instead of one or two discrete echoes, it simulates the thousands of reflections that occur when sound bounces around a physical space.

Think about clapping your hands in a cathedral versus a closet. The cathedral has long, complex reverb — sound reflects off distant walls, ceiling, floor, pillars, each reflection arriving at different times and from different directions. The closet has virtually no reverb.

**Types of reverb in pedals:**
- **Spring reverb:** A metal spring physically vibrates, creating a real mechanical reverb. Still found in Fender amps. Distinctive "drip" sound.
- **Plate reverb:** A metal plate vibrates. Used in studios since the 1950s.
- **Digital reverb:** Algorithms simulate room reflections. Can simulate any space.
- **Convolution reverb:** Uses a recorded "impulse response" of a real space to process your signal. The most realistic digital approach.

At the circuit level, a simple spring reverb pedal drives a spring tank (a small metal box with actual springs inside) and mixes the wet return signal with the dry input. We won't build one in this guide, but understanding the concept is important.

### 5.3 Echo and Looping

"Echo" is often used interchangeably with "delay," but technically, echo implies a single distinct repeat (think shouting into a canyon), while delay is the more general circuit term. A **looper** takes delay to the extreme: it records your playing into memory and plays it back continuously, allowing you to layer parts on top.

From a circuit perspective, the difference between delay, echo, and looping is just the delay time and the feedback structure. The fundamental technology is the same: store the signal and play it back later.

### 5.4 Delay Time and Its Musical Effects

The delay time determines the character of the effect dramatically:

| Delay Time | Effect | Musical Use |
|---|---|---|
| 0-5 ms | Comb filtering | Flanger, thickening |
| 5-30 ms | Chorus/doubling | Chorus, ADT (automatic double tracking) |
| 30-100 ms | Slapback echo | Rockabilly, surf, early rock and roll |
| 100-300 ms | Rhythmic echo | Edge-style dotted eighth, ambient |
| 300-600 ms | Distinct echoes | Spacious leads, atmospheric |
| 600+ ms | Long delay/looping | Ambient, experimental, live looping |

Notice how the same physical mechanism — storing and replaying a signal — creates completely different musical effects depending on a single parameter (delay time). When you build the PT2399 delay in [Chapter 10](10-build-modulation-time-effects.md), you'll experience this continuum firsthand by sweeping the delay time pot from minimum to maximum.

### 5.5 Simulating Delay in Falstad

Delay is hard to simulate in LTspice (which is optimized for analog circuits, not signal storage), but Falstad's circuit simulator has a built-in delay element. Try this:

1. Create a signal source (sine wave, 440 Hz)
2. Add a delay element set to 50ms
3. Add a summing node that combines the original and delayed signals
4. Observe the frequency response — you'll see a comb filter pattern with notches spaced at 1/delay_time = 20 Hz apart

This is the fundamental mechanism behind flanger and chorus. The notch spacing changes as you vary the delay time, and if you sweep the delay time with an LFO, the notches sweep through the spectrum, creating the characteristic whooshing sound.

> **What happens if...** you set the feedback on a delay to exactly 100%? Each repeat is exactly as loud as the previous one — the echoes never decay. In theory, they continue forever, and the signal builds up until the circuit clips. In practice, the slight noise added by each pass through the delay means the signal eventually becomes pure noise. But before that, you get a hypnotic, infinite-sustain effect that some ambient guitarists exploit deliberately. The thin line between "controlled feedback" and "runaway oscillation" is one of the most expressive parameters in delay pedal design.

---

## 6. Modulation Effects

Modulation effects all share one thing in common: they use a **Low-Frequency Oscillator (LFO)** to continuously vary some parameter of the audio signal. The LFO produces a slow, regular waveform — typically between 0.1 Hz and 10 Hz — that controls a circuit parameter over time.

### 6.1 Tremolo

**What it does:** Varies the signal's *amplitude* (volume) up and down rhythmically.

**How it works:** An LFO (a slow oscillator producing a waveform at maybe 2-8 Hz) controls a voltage-controlled amplifier (VCA) or a simple transistor-based gain stage. When the LFO is at its peak, the signal passes at full volume. When the LFO is at its trough, the signal is attenuated.

**Controls:**
- **Speed (rate):** The LFO frequency — how fast the volume goes up and down.
- **Depth:** How much the volume changes — from subtle pulsing to full on/off chopping.

**LFO waveform shapes:**
- **Sine wave:** Smooth, organic pulsing. The classic tremolo sound.
- **Triangle wave:** Similar to sine but with slightly sharper transitions.
- **Square wave:** Abrupt on/off — a choppy, stuttering effect.

Tremolo is one of the simplest modulation effects to build, which is why it's our first modulation build in [Chapter 10](10-build-modulation-time-effects.md).

### 6.2 Chorus

**What it does:** Makes one guitar sound like two (or more) playing the same thing slightly out of tune and time with each other.

**How it works:** The signal is split. One copy goes straight through (dry). The other is fed through a short delay line (typically 5-30ms) whose delay time is being slowly modulated by an LFO. This means the delayed copy is constantly being sped up and slowed down very slightly, creating subtle pitch variations. When mixed with the dry signal, you get a shimmering, thickened sound.

**Why it works:** When two signals of slightly different pitch are combined, they create **beating** — periodic constructive and destructive interference. Your ear interprets this as two separate sources, even though it's one guitar processed electronically.

### 6.3 Phaser

**What it does:** Creates a sweeping, swooshing sound — like a jet plane whooshing past.

**How it works:** The signal is split. One copy goes through a series of **all-pass filters** (circuits that shift the phase of the signal at specific frequencies without changing the amplitude). An LFO sweeps the frequencies at which these phase shifts occur. When the phase-shifted copy is mixed with the dry signal, frequencies where the two copies are out of phase cancel out, creating moving **notches** in the frequency spectrum.

**Key concept:** An all-pass filter doesn't boost or cut any frequency — it only changes the phase relationship. It's only when the all-pass output is *mixed* with the original that you hear any tonal change, because the phase cancellations create the characteristic notches.

### 6.4 Flanger

**What it does:** Similar to phasing but more dramatic and metallic — a deeper swoosh with a hollow, resonant quality.

**How it works:** Very similar to chorus — a modulated short delay — but with a much shorter delay time (typically 1-10ms) and a **feedback path** that sends some of the delayed output back to the delay input. The shorter delay time creates more closely spaced notches (like a comb — this is called a **comb filter** effect), and the feedback makes those notches deeper and more resonant.

**Phaser vs Flanger:** Both create sweeping notches, but phasers have irregularly spaced notches (determined by the all-pass filter stages), while flangers have evenly spaced notches (determined by the delay time). Flangers tend to sound more dramatic and metallic. Phasers tend to sound smoother and more organic.

> **What happens if...** you take a chorus circuit and shorten the delay time from 20ms to 5ms while adding feedback? You've just turned it into a flanger. These effects are all variations on the same underlying architecture: LFO-modulated delay with different parameters.

### 6.5 Ring Modulation

Ring modulation is a special case of modulation that's less common in standard guitar setups but worth understanding because it reveals a fundamental principle.

A **ring modulator** multiplies two signals together. If you multiply your guitar signal (say, 440 Hz) by a carrier oscillator (say, 1000 Hz), you get two new frequencies: the sum (1440 Hz) and the difference (560 Hz). Crucially, the original frequencies (440 Hz and 1000 Hz) *disappear* — you only hear the sum and difference.

The result sounds metallic, atonal, and alien — like a robot playing guitar. It's the sound of Dalek voices in Doctor Who and the bell-like tones in some Tony Iommi solos.

Ring modulation is mathematically simple (multiplication) but produces musically complex and often dissonant results. It's the extreme end of the modulation spectrum: tremolo multiplies by a slowly varying signal (the LFO), creating gentle volume changes. Ring modulation multiplies by a fast oscillator in the audio range, creating entirely new frequencies.

### 6.6 The Modulation Family Tree

Here's how all modulation effects relate to each other:

```
                        Modulation Effects
                              |
              ┌───────────────┼───────────────┐
              |               |               |
        Amplitude        Time-based        Frequency
        Modulation       Modulation        Modulation
              |               |               |
          Tremolo    ┌───────┼────────┐    Ring Mod
          (LFO ×     |       |        |    (Audio ×
          volume)  Chorus  Flanger  Vibrato  Audio)
                   (LFO ×  (LFO ×  (LFO ×
                   delay,  short    delay,
                   wet+dry delay+  wet only)
                          feedback)
```

Every modulation effect is fundamentally an **oscillator** controlling a **variable parameter** in the audio path. The oscillator frequency, the controlled parameter, and the mixing strategy determine which effect you get. Master this concept and you can design any modulation effect from first principles.

> **What happens if...** you use an audio-rate oscillator (say, 200 Hz) instead of an LFO (2 Hz) to modulate a tremolo? At 2 Hz, you hear volume pulsing. At 20 Hz, the pulsing is so fast that you start to hear it as a "buzz" or a rough texture — the modulation frequency is entering the audible range. At 200 Hz, you're in ring modulation territory — new frequencies appear and the effect sounds metallic and bizarre. The boundary between "tremolo" and "ring modulator" is just the modulation frequency.

---

## 7. Filtering and EQ

### 7.1 Filters: The Most Important Effect You Don't Think About

Here's a secret: almost every pedal contains a filter, even if it's not labeled as one. The tone knob on your guitar? A low-pass filter. The tone control on your fuzz pedal? A low-pass filter. The "mid hump" that defines the Tube Screamer sound? A band-pass filter formed by the input and output filtering. Even the coupling capacitors at the input and output of every pedal form high-pass filters.

Filters are everywhere because they determine the *frequency balance* — which is one of the most important aspects of tone.

### 7.2 Filter Types

**Low-pass filter (LPF):** Passes frequencies below a cutoff frequency and attenuates frequencies above it. The universal "tone" control in guitar pedals — turn it one way and you roll off highs (darker tone), turn it the other way and you let everything through (brighter tone).

**High-pass filter (HPF):** The opposite — passes frequencies above the cutoff and attenuates those below. The coupling capacitors at the input and output of most pedal circuits are high-pass filters. They block DC voltage while passing the audio signal. But if the capacitor is too small, it also blocks low frequencies — this is why some pedals sound "thin." Increasing the coupling cap value lowers the HPF cutoff frequency and lets more bass through.

**Band-pass filter (BPF):** Passes a range of frequencies and attenuates everything above and below. A wah pedal is a variable band-pass filter — the pedal position sweeps the center frequency of the BPF up and down, creating that iconic "wah" sound.

**Band-reject (notch) filter:** The opposite of band-pass — it removes a narrow range of frequencies. Not common as a standalone guitar effect, but notch filters are exactly what phasers create (as we discussed in section 6.3).

**Shelving filter:** Instead of a sharp cutoff, a shelving filter boosts or cuts all frequencies above (high shelf) or below (low shelf) a certain frequency by a fixed amount. Think of it as raising or lowering a shelf. The bass and treble controls on most stereo equipment are shelving filters.

### 7.3 Filter Cutoff Frequency

The cutoff frequency of an RC filter (the most common type in pedals) is:

```
f_cutoff = 1 / (2 * pi * R * C)
```

Where R is in ohms and C is in farads. This formula appears constantly in pedal design. Here are some practical examples:

| R | C | Cutoff Frequency | Typical Use |
|---|---|---|---|
| 10 kohm | 100nF | 159 Hz | Bass-heavy tone rolloff |
| 10 kohm | 22nF | 723 Hz | Mid-focused tone rolloff (Tube Screamer territory) |
| 10 kohm | 4.7nF | 3,386 Hz | Treble-only rolloff |
| 10 kohm | 1nF | 15,915 Hz | Subtle "air" rolloff |
| 1 Mohm | 100nF | 1.6 Hz | Input coupling (passes all audio) |
| 100 kohm | 47nF | 33.9 Hz | Input coupling (passes most bass) |

When you see a coupling capacitor on a schematic, use this formula to check what frequency it starts rolling off. That tells you whether the circuit passes full bass or is deliberately thin-sounding.

### 7.4 Tone Controls in Pedals

Most guitar pedals use a simple passive tone control: a potentiometer and a capacitor forming a variable low-pass filter. Turn the knob one way, and you shunt high frequencies to ground through the cap. Turn it the other way, and the signal passes unfiltered.

More sophisticated pedals (and amplifiers) use **tone stacks** — combinations of filters that shape the frequency response in more complex ways. We'll dive deep into the Fender, Marshall, and Baxandall tone stacks in [Chapter 6](06-gain-stages-clipping-circuits.md).

### 7.4 EQ as a Creative Tool

When you think about EQ not as "corrective" but as "creative," you realize that *every* tonal decision in effect design is an EQ decision:

- The coupling capacitor value at the input sets the bass rolloff point
- The feedback capacitor in an op-amp circuit sets the treble rolloff point
- The clipping diodes combined with a capacitor create frequency-dependent clipping
- The output filter determines the final tonal balance

The Tube Screamer's famous "mid hump" isn't created by a dedicated mid-boost circuit. It's created by the combination of the input high-pass filter (rolling off bass before clipping), the feedback network (rolling off treble), and the output low-pass filter. Three simple filters, and together they create one of the most recognizable sounds in guitar history.

> **What happens if...** you change the input coupling capacitor of a Tube Screamer from 0.047uF to 0.47uF? The high-pass cutoff frequency drops by a factor of 10 — from about 720 Hz to about 72 Hz. Suddenly, much more bass gets into the clipping stage. The "mid hump" becomes a "mid-and-bass hump." The pedal sounds fatter but potentially muddier. This is a popular mod called the "bass boost mod," and now you know exactly why it works.

---

## 8. Buffered vs True Bypass

### 8.1 The Problem: Tone Suck

You've built an amazing pedalboard with 12 pedals. But even with all pedals bypassed (turned off), your tone sounds duller and quieter than when you plug straight into the amp. What gives?

The culprit is **tone suck**, and it's an impedance problem.

Remember from section 1.3 that your guitar cable's capacitance interacts with your pickup's impedance to create a low-pass filter. Now imagine your signal going through 12 pedals, each with input and output jacks and internal wiring. Even when bypassed, each pedal adds a tiny bit of cable length and capacitance. More importantly, if the pedals use **true bypass** switching (a hard-wired bypass that connects input directly to output), your guitar sees the combined capacitance of *all* cables and wiring in the chain.

With 12 true-bypass pedals and the associated patch cables, you might have the equivalent of 30-40 feet of cable capacitance. Your guitar's high-impedance output is trying to drive all of that capacitance, and the result is significant high-frequency loss.

### 8.2 True Bypass

**True bypass** means that when the pedal is off, the input jack is connected directly to the output jack through a mechanical switch (usually a 3PDT footswitch). The signal doesn't pass through any of the pedal's circuitry.

**Pros:**
- No active components in the signal path when bypassed (no noise, no coloration)
- Simple to implement
- "What you hear is what your guitar sounds like"

**Cons:**
- Doesn't buffer the signal — your guitar still sees all the cable capacitance
- With many pedals in a chain, cumulative capacitance causes high-frequency loss
- Switching can produce a pop (especially with circuits that have DC offset at the output)

### 8.3 Buffered Bypass

**Buffered bypass** means the pedal contains a buffer circuit — a unity-gain amplifier (gain of 1, so it doesn't change the signal level) with high input impedance and low output impedance.

**What the buffer does:** It converts the guitar's high-impedance, capacitance-sensitive signal into a low-impedance, capacitance-resistant signal. The buffer "drives" the cable and downstream pedals without the high-frequency loss that the raw guitar signal would experience.

**Pros:**
- Preserves high-frequency content regardless of cable length
- Signal is robust against capacitive loading from other pedals
- One good buffer at the start of your chain can protect the signal through the entire chain

**Cons:**
- Adds active components to the signal path (potential for noise, coloration, battery drain)
- Slightly changes the interaction between guitar and first pedal (some players prefer the "unbuffered" feel, especially with fuzz pedals)

### 8.4 When Each Matters

**Use true bypass when:**
- You have a short signal chain (3-4 pedals)
- You're using short patch cables
- You want zero coloration when bypassed
- The pedal is a fuzz that interacts with guitar volume knob (fuzzes often sound better driven directly by a high-impedance source)

**Use a buffer when:**
- You have a long signal chain (5+ pedals)
- You're running long cables
- You want consistent tone regardless of pedal configuration

**The practical solution:** Many players use one buffered pedal at the start of their chain (or a dedicated buffer pedal) and true bypass for the rest. The first buffer "fixes" the impedance for the entire chain. Boss pedals, for example, are always buffered — having one Boss pedal on your board effectively provides this buffer.

### 8.5 Building a Simple Buffer

A buffer is one of the easiest circuits to build and one of the most useful. We'll build one as part of the clean boost in [Chapter 8](08-build-fuzz-and-boost.md), but here's the concept:

```
  Input ── Cin (100nF) ── (+) Op-Amp ── Output
                            |
                       (-) connected to Output (100% feedback = unity gain)
```

Input impedance: ~1 Mohm (determined by the bias resistor from the input to the bias voltage). Output impedance: a few ohms (the op-amp drives the output directly). Gain: exactly 1 (0 dB) — the signal comes out the same amplitude it went in, just impedance-converted.

You can also build a buffer with a single JFET (which is what our clean boost from Chapter 8 becomes at unity gain) or a single bipolar transistor in emitter follower configuration.

### 8.6 The "Bypass Switching" Problem

Beyond the buffered vs true bypass decision, there's the mechanical question of *how* you switch a pedal on and off. The standard approach is a 3PDT (three-pole, double-throw) footswitch that mechanically switches the input, output, and LED simultaneously. This gives you true bypass when the pedal is off.

For buffered bypass, you need an electronic switching circuit — typically a JFET-based switch or a CMOS analog switch (CD4066) controlled by a flip-flop triggered by the footswitch. The footswitch toggles a flip-flop, and the flip-flop controls the electronic switches that route the signal through or around the circuit.

Electronic switching eliminates the "pop" that mechanical switches can produce when switching pedals with DC offset at the output. It also enables "soft" switching — relay-based true bypass, or microcontroller-based preset switching. This is how high-end and multi-channel pedals work.

We won't build a complete switching circuit in the breadboard builds (we'll just plug and unplug from the breadboard), but when you move to enclosure builds in [Part 5](../part-5-pcb-professional/32-enclosure-design-fabrication.md), switching becomes important.

---

## 9. Impedance in the Pedal Chain

### 9.1 Why Pedal Order Matters Electrically

We touched on pedal order in the gain staging section (section 2), but impedance gives us another reason why order matters — and this one is more subtle.

The fundamental rule of signal transfer, which you learned in [Chapter 0](../part-0-electronics/00-electricity-through-sound.md): **the source impedance should be much lower than the load impedance** for efficient voltage transfer. This is called "bridging" — the load draws negligible current from the source, so the full signal voltage is preserved.

Your guitar has a high output impedance (6-16 kohm). Most pedal inputs have an impedance of 500 kohm to 1 Mohm. That's a good ratio — the pedal's input impedance is much higher than the guitar's output impedance, so the signal transfers efficiently.

But here's where it gets interesting: **some effects interact with the source impedance.**

### 9.2 Fuzz Pedals and Input Impedance

Classic fuzz pedals (like the Fuzz Face and Tone Bender) have relatively low input impedance — sometimes as low as 5-10 kohm. They were designed in the 1960s to be plugged directly into a guitar, and their circuit behavior depends on the interaction between the guitar's pickup impedance and the fuzz's input impedance.

When you put a buffer (low output impedance, maybe 100 ohms) before a vintage fuzz, you're feeding the fuzz from a very different source than it was designed for. The result: the fuzz sounds different — often thinner, less dynamic, and with different cleanup behavior when you roll down the guitar volume.

This is why many experienced pedalboard builders put fuzz pedals *first* in the chain, before any buffered pedals.

### 9.3 The Practical Impedance Chain

Here's a reasonable pedal order with impedance in mind:

```
Guitar (high Z out: ~10 kohm)
  → Fuzz (low Z in: ~10 kohm — wants to see high Z source)
    → Wah (moderate Z in: ~100 kohm — also benefits from direct guitar)
      → Overdrive/Distortion (high Z in: ~1 Mohm — buffered input)
        → Modulation (high Z in: ~1 Mohm)
          → Delay (high Z in: ~1 Mohm)
            → Reverb (high Z in: ~1 Mohm)
              → Amp (high Z in: ~1 Mohm)
```

After the first buffered pedal (usually the overdrive), the signal has been converted to low impedance and all downstream pedals see a strong, consistent signal. The effects that care about source impedance (fuzz, sometimes wah) go before the buffer.

### 9.4 Output Impedance and Driving Cables

The other side of the coin: a pedal's output impedance determines how well it can drive the next stage and the cable between them. Most modern pedals have low output impedance (a few hundred ohms or less), which means they can drive long cables and capacitive loads without high-frequency loss.

But some vintage-style pedals (especially those using transistors without a buffer stage at the output) might have higher output impedance. If such a pedal is driving a long cable to the amp, you might lose highs — exactly the same problem as the unbuffered guitar signal from section 1.3.

> **What happens if...** you place a germanium Fuzz Face after a Boss tuner pedal? The Boss tuner has a buffered bypass with very low output impedance (~100 ohms). The Fuzz Face, which was designed to see your guitar's ~10 kohm output impedance, now sees 100 ohms. The fuzz's biasing shifts, the input transistor operates differently, and the fuzz sounds fizzy and thin instead of fat and responsive. The fix: either put the fuzz before the tuner, or look for a "buffer-friendly" fuzz design.

---

## 10. Putting It All Together: Thinking Like a Designer

### 10.1 Effects as Signal Processing Blocks

Now that you understand the categories, step back and see the pattern. Every guitar effect is one (or a combination) of these signal processing operations:

| Operation | Examples | Key Circuit Element |
|---|---|---|
| **Amplification** | Boost, preamp | Transistor, op-amp gain stage |
| **Clipping** | Overdrive, distortion, fuzz | Diodes, transistor saturation |
| **Filtering** | Tone controls, wah, EQ | RC networks, LC networks |
| **Delay** | Delay, reverb, chorus, flanger | BBD, PT2399, digital memory |
| **Modulation** | Tremolo, chorus, phaser, flanger | LFO + VCA, LFO + delay, LFO + all-pass |
| **Mixing** | Wet/dry blend, parallel effects | Summing amplifier, resistor mixer |

A Tube Screamer is: **amplification** + **clipping** + **filtering**.
A chorus is: **delay** + **modulation** + **mixing**.
A wah is: **filtering** (with foot-controlled sweep).
A reverb is: many **delays** + **mixing** + **filtering**.

When you start thinking about effects this way — as combinations of basic operations — you can design your own. What would happen if you took the LFO from a tremolo and used it to sweep a filter cutoff instead of volume? You'd get an auto-wah. What if you modulated the clipping threshold? You'd get a dynamically varying distortion texture.

### 10.2 From Theory to Build

In the next five chapters, you'll go from these abstract concepts to real, working circuits:

- [Chapter 6](06-gain-stages-clipping-circuits.md) dives deep into the specific circuit topologies for gain stages, clipping, and tone controls — the building blocks you'll use in every pedal.
- [Chapter 7](07-schematic-to-breadboard.md) shows you how to take a schematic and turn it into a working breadboard prototype, with systematic debugging when things go wrong.
- [Chapters 8](08-build-fuzz-and-boost.md), [9](09-build-overdrive-distortion.md), and [10](10-build-modulation-time-effects.md) are full builds: fuzz, boost, overdrive, distortion, tremolo, and delay. Each one applies the theory from this chapter and the circuits from Chapter 6.

And looking further ahead: once you've built and understood these pedals on a breadboard, [Part 5, Chapter 31](../part-5-pcb-professional/31-pcb-design-for-audio.md) will show you how to turn them into professional PCBs. [Part 7](../part-7-business/40-sourcing-supply-chain.md) covers turning your builds into a product line you can sell.

### 10.3 Your Ears Are the Final Test

One last thing before we move on. All the theory in the world won't replace your ears. When you build these circuits, *listen*. Compare. Swap a component and listen again. The theory tells you *why* something sounds the way it does, but your ears tell you *whether it sounds good*.

You're a guitar player. You already have trained ears — you know what sounds good to you. The electronics knowledge you're building is giving you the power to *create* those sounds deliberately instead of shopping for them.

Let's get into the circuits.

---

## Chapter Summary

- The guitar signal path extends from pickup through cables, pedals, and amp — each stage can modify the signal.
- **Gain staging** matters because gains multiply through the chain: the order of pedals determines where clipping occurs and how much headroom each stage has.
- **Harmonics** define timbre. Effects shape sound by adding, removing, or shifting harmonics.
- **Soft clipping** (feedback diodes, tube saturation) sounds smooth; **hard clipping** (diodes to ground) sounds aggressive. Symmetrical clipping produces odd harmonics; asymmetrical produces even harmonics.
- **Time-based effects** (delay, reverb) store and replay the signal. Analog delays use BBDs; digital delays use ADC/DAC and memory.
- **Modulation effects** all use an LFO to vary a parameter: tremolo varies amplitude, chorus varies delay time (pitch), phaser varies phase, flanger varies short delay with feedback.
- **Filters and EQ** are in every pedal — coupling caps, tone controls, and frequency-shaping networks all shape the spectral balance.
- **Buffered bypass** preserves high frequencies in long chains by converting the guitar's high-impedance signal to low impedance. **True bypass** is simpler but suffers from capacitive loading.
- **Impedance** determines pedal order: fuzz pedals want to see the guitar's high impedance directly, while downstream pedals benefit from a buffered (low impedance) source.
- Every effect is a combination of basic operations: amplification, clipping, filtering, delay, modulation, and mixing. Understanding these building blocks lets you *design* effects, not just copy them.

---

*Next: [Chapter 6 — Gain Stages and Clipping Circuits](06-gain-stages-clipping-circuits.md), where we build the actual circuits behind everything we've discussed.*
