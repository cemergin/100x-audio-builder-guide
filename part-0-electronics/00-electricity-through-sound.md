<!--
  CHAPTER: 0
  TITLE: Electricity Through the Lens of Sound
  PART: 0 — Electronics for Audio People
  PREREQS: None
  KEY_TOPICS: voltage, current, resistance, impedance, AC vs DC, frequency, wavelength, phase, power, watts, decibels, Ohm's law, Kirchhoff's laws
  DIFFICULTY: Beginner
  UPDATED: 2026-04-10
-->

# Chapter 0: Electricity Through the Lens of Sound

> **Part 0 — Electronics for Audio People** | Prerequisites: None | Difficulty: Beginner

You already know what sound *sounds* like. You've twisted a volume knob and heard the result get louder. You've turned a tone knob and heard the brightness change. You've plugged a guitar into a pedal, the pedal into an amp, and heard music come out the other end. What you might not know is that every one of those experiences has a precise electrical explanation -- and once you understand that explanation, you stop being someone who follows instructions and start being someone who *designs*.

This chapter is the foundation for everything else in this guide. We're going to talk about voltage, current, resistance, impedance, frequency, phase, power, and decibels -- but we're going to do it through the lens of audio. Every concept will be tied to something you can hear, measure, or build. No water pipe analogies. No abstract physics lectures. Just electricity as it relates to sound.

If you've ever wondered what *actually* changes when you turn up the volume, or why a bass guitar needs a different cable strategy than a microphone, or what "impedance mismatch" really means when someone on a forum throws it around -- this is where those answers live.

### In This Chapter
- Voltage as signal amplitude
- Current as flow
- Resistance vs impedance
- AC vs DC and why audio is AC
- Frequency, wavelength, and the audible spectrum
- Phase and why it matters
- Power: watts, real vs apparent
- Decibels: dB, dBV, dBu, dBSPL
- Ohm's law applied to audio
- Kirchhoff's laws in real circuits

### Related Chapters
- [Ch 1: Components and What They Do to Signals](01-components-and-signals.md) -- how individual parts shape the electrical signals described here
- [Ch 2: Reading Schematics and Datasheets](02-reading-schematics-datasheets.md) -- how these electrical concepts appear on paper
- [Ch 4: Your Workbench, Tools, and Safety](04-workbench-tools-safety.md) -- how to measure the quantities described here

---

## 1. Voltage: The Size of the Signal

### 1.1 What Voltage Actually Is

**Voltage** (measured in **volts**, symbol **V**) is the difference in electrical potential between two points. Formally, it's the amount of energy per unit charge available to push electrons through a circuit. Colloquially, it's *electrical pressure* -- how hard electrons are being pushed.

Here's what matters for audio: **voltage is amplitude**. When you look at a waveform on an oscilloscope or in your DAW, the vertical axis is voltage. A louder signal is a higher-voltage signal. A quieter signal is a lower-voltage signal. That's it. When you turn up the volume knob on a preamp, you are increasing the voltage of the audio signal.

This connection between voltage and loudness is so fundamental that it's worth pausing on. Every piece of audio equipment you've ever used -- every mixer fader, every amp gain knob, every compressor threshold -- is ultimately manipulating voltage. The entire discipline of audio electronics is the discipline of controlling voltage in precise, frequency-dependent ways. Master this idea and everything else follows.

### 1.2 Voltage Levels Across the Audio World

Let's put some numbers on this:

| Signal Source | Typical Voltage (peak) | Context |
|---|---|---|
| Electric guitar pickup | 100-500 mV | Before any pedals or amp |
| Dynamic microphone (SM57) | 1-10 mV | Extremely quiet -- needs a preamp |
| Condenser microphone (with phantom power) | 5-50 mV | Still quiet, but more output than dynamic |
| Piezo pickup (acoustic guitar) | 50-500 mV | High impedance, needs a buffered preamp |
| Line-level signal (pro) | 1.228 V (+4 dBu) | Balanced connections between gear |
| Line-level signal (consumer) | 0.316 V (-10 dBV) | Unbalanced, like your home stereo |
| Headphone amplifier output | 1-5 V | Driving headphones |
| Speaker amplifier output | 10-40 V | Driving a speaker cabinet |
| Phantom power | 48 V DC | Powering condenser microphones |
| Tube amp plate voltage | 250-600 V DC | Will kill you. Respect it. |

Notice the enormous range. A microphone signal is *millivolts* -- thousandths of a volt. A speaker amplifier signal is *tens of volts*. The entire signal chain in audio is the story of taking a tiny voltage and making it bigger, shaping it along the way.

This range also explains why audio equipment has distinct "levels" -- mic level, instrument level, line level, speaker level. These aren't arbitrary categories; they're voltage ranges that different equipment expects to see. Plug a mic-level signal into a line-level input and it's too quiet. Plug a line-level signal into a mic input and you'll overdrive (distort) it. Understanding voltage levels is the first step toward understanding gain staging, which is how professionals keep their signal chains clean and noise-free.

### 1.3 AC Voltage vs DC Voltage

A 9V battery provides **DC** (direct current) voltage -- it's a steady 9V, constant, unchanging. DC is the power supply: it keeps your circuits alive.

An audio signal is **AC** (alternating current) voltage -- it swings positive and negative, oscillating above and below a reference point. When a guitar string vibrates, the pickup converts that physical vibration into an AC voltage that swings positive and negative at the same frequency as the vibration. A 440 Hz "A" note produces a voltage that swings positive and negative 440 times per second.

This distinction is critical. In almost every audio circuit, you have *both*: DC power keeping the circuit running, and AC audio signals flowing through it. A huge part of circuit design is keeping those two separate -- letting the AC signal pass through while blocking the DC. (Spoiler: that's what coupling capacitors do. Chapter 1 covers them in detail.)

If you're a software engineer, think of DC as the runtime environment (JVM, Node, OS) and AC as the data flowing through your application. The runtime needs to be there and stable, but it shouldn't leak into the data stream. When it does leak (a DC offset on your audio signal), you get problems -- speaker cone displacement, headroom loss, and asymmetric clipping.

### 1.4 Peak, Peak-to-Peak, and RMS

When we say a signal is "1 volt," we need to be specific about *which* 1 volt we mean:

- **Peak voltage (Vpk)**: The maximum excursion from zero. If a sine wave swings from -1V to +1V, its peak voltage is 1V.
- **Peak-to-peak voltage (Vpp)**: The full swing. That same wave has a Vpp of 2V.
- **RMS voltage (Vrms)**: The "equivalent DC" value -- the DC voltage that would deliver the same power. For a sine wave, Vrms = Vpk / sqrt(2), which is approximately Vpk x 0.707. So our 1V peak sine wave has an RMS voltage of about 0.707V.

**RMS is what you'll use most often** in audio work. When someone says a line-level signal is "+4 dBu," they mean the RMS voltage is 1.228 Vrms. When an amplifier is rated at "100 watts into 8 ohms," they (usually) mean RMS watts.

The RMS concept is important because real audio signals aren't constant -- they're always varying. The RMS value tells you the "effective" value, the one that determines how much power the signal delivers. It's the equivalent of the "average" in statistics, but computed as the square root of the mean of the squared values (hence "root mean square") because audio signals swing both positive and negative and a simple average would be zero.

> **What happens if...** you confuse peak and RMS? You'll be off by about 3 dB -- which is roughly the smallest volume difference most people can reliably notice. In amplifier design, confusing peak and RMS power means your "100 watt" amp is really a 50 watt amp (or vice versa). Some manufacturers exploit this ambiguity in their marketing -- they'll quote "200 watts peak" which sounds impressive but is only 100 watts RMS. Now you know better.

### 1.5 Headroom: The Space Between the Signal and the Ceiling

**Headroom** is the difference between the current signal level and the maximum level the circuit can handle before clipping (distorting). It's measured in dB.

If your circuit runs on a 9V supply with a virtual ground at 4.5V, the maximum signal swing is approximately +/-4.5V peak (in practice a bit less due to transistor/op-amp limitations -- maybe +/-3.5V). If your current signal is 1V peak, you have about 10.9 dB of headroom (20 x log10(3.5/1)).

Why does headroom matter? Because real music has transients -- brief peaks that are much louder than the average level. A snare drum hit, a hard guitar pick attack, a vocal consonant -- these can be 10-20 dB above the average level. If your circuit doesn't have enough headroom, these transients get clipped. Sometimes that clipping is intentional and desirable (hello, overdrive pedals). But in a preamp, mixer, or power amp, unintended clipping sounds bad and can damage speakers.

This is why professional audio equipment runs on higher supply voltages (+/-15V or +/-18V) than consumer gear or guitar pedals (+/-4.5V or +9V single supply). Higher supply voltage means more headroom means more dynamic range means a cleaner signal.

> **What happens if...** you run a pedal at 18V instead of 9V (assuming the components are rated for it)? You double the available voltage swing, which gives you 6 dB more headroom. The pedal stays cleaner at higher input levels, and the character of the clipping changes when you do push it. This is why some boutique pedal builders design their circuits for 18V operation. The Tube Screamer at 18V is a noticeably different beast than at 9V -- more dynamic, more headroom, harder to push into saturation.

---

## 2. Current: The Flow

### 2.1 What Current Is

**Current** (measured in **amperes** or **amps**, symbol **A**) is the rate of electron flow through a conductor. If voltage is the pressure, current is the flow rate. Formally, one ampere is one coulomb of charge passing a point per second.

In daily life, we rarely think about current directly. We think about voltage (the label on the battery, the setting on the power supply). But current is what actually does work -- it's current through a speaker's voice coil that moves the cone, current through a heating element that produces heat, current through an LED that produces light.

### 2.2 Where Current Matters in Audio

Current matters in specific, predictable places:

- **Speaker drivers**: A speaker is essentially a motor. It converts electrical current into physical cone movement. More current = more cone excursion = more air movement = more volume. This is why amplifiers that drive speakers need to supply significant current -- often several amps peak. A power amplifier's ability to deliver current into low-impedance loads (4 ohms, 2 ohms) is one of the key differentiators between budget and high-end designs.

- **Headphone amplifiers**: Headphones vary wildly in their current demands. A pair of sensitive IEMs (in-ear monitors) at 16 ohms might need fractions of a milliamp. A pair of planar magnetic headphones at 300 ohms might need 100+ mA to reach satisfying volume. The headphone amp's current capability determines which headphones it can drive properly.

- **Power supplies**: Every active component in your circuit -- every op-amp, every LED, every transistor -- draws current from the power supply. If your power supply can't deliver enough current, your circuit starves and misbehaves. The symptoms are sneaky: the power supply voltage droops under load, causing increased distortion, reduced headroom, and often audible noise as the circuit fights for current during signal peaks.

- **LED brightness**: LEDs are current-controlled devices. The brightness of an indicator LED in your pedal is determined by the current flowing through it, which is set by the current-limiting resistor. More current = brighter, up to the LED's maximum rating.

### 2.3 Signal Current vs Power Current

It helps to think of current in two categories:

**Signal current** is the tiny AC current that constitutes the audio signal itself. In a guitar pedal, signal currents are typically in the microamp range (millionths of an amp). You almost never think about signal current directly -- you think about voltage, and the current follows from the circuit's impedance (I = V/Z).

**Power current** (or "bias current," "supply current") is the DC current drawn from the power supply to keep the circuit operating. A typical guitar pedal might draw 5-50 mA from a 9V battery. A tube amplifier might draw 200+ mA from its high-voltage supply. This is the current that drains your batteries and determines the size of your power supply.

Here's a practical rule: if you're building a pedal that draws 20 mA from a 9V battery, and a standard alkaline 9V battery has roughly 500 mAh of capacity, your battery life is approximately 500/20 = 25 hours. Digital pedals with displays and microcontrollers can draw 100-200 mA, cutting battery life to 2.5-5 hours -- which is why most digital pedals practically require an external power supply.

> **What happens if...** your power supply can't deliver enough current? The supply voltage drops ("sags"). In some contexts this is desirable -- tube amp power supply sag is a sought-after characteristic that creates a compressed, touch-sensitive feel. In other contexts it's a problem: a digital pedal that needs 150 mA running from a power supply that can only deliver 100 mA per output will glitch, reset, or produce digital artifacts. Always check the current draw of your circuit against the current capacity of your supply.

---

## 3. Resistance and Impedance

### 3.1 Resistance: The DC Story

**Resistance** (measured in **ohms**, symbol **Ω**) is opposition to current flow. It's constant -- a 1kΩ resistor is 1kΩ whether you apply DC, 60 Hz AC, or 20 kHz AC. Ohm's law describes the relationship:

```
V = I × R

Voltage (volts) = Current (amps) × Resistance (ohms)
```

Rearranged: `I = V / R` and `R = V / I`.

This is the single most important equation in electronics. If you know any two of voltage, current, and resistance, you can find the third. It's the `F = ma` of electrical engineering.

**Audio example**: You have a 9V battery and a 1kΩ resistor. The current through the resistor is 9V / 1000Ω = 9 mA. The power dissipated is 9V x 0.009A = 81 mW. Simple, predictable, frequency-independent.

### 3.2 Impedance: The AC Story (and Why Audio People Care)

**Impedance** (also measured in **ohms**, also symbol **Ω**, sometimes **Z**) is opposition to AC current flow -- and here's the critical difference: **impedance changes with frequency**.

If resistance is a brick wall (the same height no matter what), impedance is a fence that's tall for some people and short for others, depending on their "frequency." That's a terrible analogy, but the point stands: impedance is frequency-selective, and that selectivity is the foundation of all audio signal processing.

A resistor has impedance equal to its resistance at all frequencies. But capacitors and inductors (which we'll meet in Chapter 1) have impedance that depends on the frequency of the signal passing through them:

- **Capacitor**: impedance *decreases* as frequency *increases*. A capacitor blocks low frequencies and passes high frequencies. This is why a tone knob works -- it's a capacitor bleeding high frequencies to ground.
- **Inductor**: impedance *increases* as frequency *increases*. An inductor passes low frequencies and blocks high frequencies. This is why inductors are used in speaker crossover networks to send bass to the woofer.

The formulas (don't memorize them -- internalize the *direction*):

```
Capacitor impedance:  Zc = 1 / (2π × f × C)
Inductor impedance:   Zl = 2π × f × L
```

Where `f` is frequency in Hz, `C` is capacitance in farads, and `L` is inductance in henrys.

Let's put real numbers on these:

A 100 nF (0.1 μF) capacitor:
- At 20 Hz: Zc = 1/(2π × 20 × 0.0000001) = 79,577 Ω ≈ 80 kΩ (very high -- blocks bass)
- At 1 kHz: Zc = 1/(2π × 1000 × 0.0000001) = 1,592 Ω ≈ 1.6 kΩ (moderate)
- At 20 kHz: Zc = 1/(2π × 20000 × 0.0000001) = 80 Ω (low -- passes treble easily)

See the pattern? The same capacitor is essentially an open circuit for bass and a short circuit for treble. That's a filter, built from a single component.

**This is the single most important concept in audio electronics.** Every tone control, every crossover, every filter, every EQ -- they all work because impedance varies with frequency. If impedance were constant like resistance, we couldn't shape tone at all. We'd just have volume knobs.

### 3.3 Impedance in the Signal Chain

Impedance also governs how audio devices interact with each other. Every audio source has an **output impedance**, and every audio input has an **input impedance**. The relationship between them determines how much signal voltage transfers from one device to the next.

The general rule in modern audio: **low output impedance driving high input impedance**. If you're a software engineer, think of it like an API contract: the source should be able to deliver the signal without breaking a sweat (low output impedance), and the receiver should accept it without loading down the source (high input impedance). The ratio should be at least 1:10, and ideally 1:50 or higher.

Typical ratios:

| Connection | Source Output Z | Destination Input Z | Ratio |
|---|---|---|---|
| Guitar → pedal input | 7-15 kΩ (pickup) | 500 kΩ - 1 MΩ | ~1:50+ |
| Pedal output → next pedal | 1-10 kΩ | 500 kΩ - 1 MΩ | ~1:100+ |
| Preamp → power amp | 100-600 Ω | 10-47 kΩ | ~1:50+ |
| Power amp → speaker | 0.01-0.1 Ω | 4-16 Ω | ~1:100+ |
| Microphone → preamp | 150-600 Ω | 1.5-10 kΩ | ~1:10+ |
| DAC output → amp input | 50-200 Ω | 10-50 kΩ | ~1:100+ |

The "bridging" approach (very high input impedance) is standard in modern audio because it maximizes voltage transfer and minimizes frequency-dependent loading effects. But there's a historical exception: in the telephone era, audio systems used "matched" impedances (source and load both at 600Ω) to maximize *power* transfer. This is the origin of the 600Ω reference in the dBu measurement standard. Modern audio equipment doesn't actually use 600Ω impedance matching, but the standard persists.

> **What happens if...** the impedance ratio is wrong? If you plug a guitar directly into a line-level input (say, the AUX input on a home stereo with 10 kΩ input impedance), the relatively high output impedance of the guitar pickup (7-15 kΩ) loads down against the low input impedance. The result: you lose high frequencies, the signal gets quiet and dull. This is called "impedance mismatch," and it's the reason guitar buffers and DI boxes exist. They present a high impedance to the guitar (keeping it happy) and a low impedance to the next device (keeping *it* happy).

### 3.4 Impedance is Complex (Literally)

One more thing, and this one takes some getting used to: impedance has both a **magnitude** (how much opposition, in ohms) and a **phase angle** (how much the current is shifted in time relative to the voltage, in degrees).

Resistors have zero phase angle -- voltage and current are in sync. Capacitors shift current 90 degrees ahead of voltage (current "leads"). Inductors shift current 90 degrees behind voltage (current "lags"). Real-world components (like speakers) have impedance that's a complicated mix of resistance, capacitance, and inductance -- which is why a speaker's "8 ohm" rating is a gross simplification.

A typical 8Ω woofer might have this impedance profile:
- At 50 Hz: 30Ω (the resonant peak -- impedance spikes here)
- At 200 Hz: 7Ω (the minimum, near the rated impedance)
- At 1 kHz: 8Ω (close to the rating)
- At 5 kHz: 15Ω (rising due to voice coil inductance)
- At 20 kHz: 40Ω+ (voice coil inductance dominates)

This means the amplifier sees a very different load depending on what frequency it's trying to reproduce. At the impedance minimum (200 Hz in this example), the amplifier has to deliver the most current. At the resonant peak (50 Hz), the speaker draws relatively little current. The amplifier has to handle all of these conditions gracefully. We'll revisit speaker impedance extensively in Part 2.

### 3.5 The Impedance Thread

Impedance isn't just a Chapter 0 concept -- it's a thread that runs through the entire guide:

| Chapter | Impedance Application |
|---|---|
| Ch 1 | Component impedance: how caps, inductors filter by impedance |
| Ch 5-10 | Pedal input/output impedance, buffer circuits |
| Ch 12-13 | Speaker impedance curves, crossover design |
| Ch 18-20 | Amplifier output impedance, damping factor |
| Ch 35 | System impedance matching from source to speaker |

Every time you see a concept that involves frequency-dependent behavior, impedance is behind it.

---

## 4. AC vs DC and Why Audio Lives in AC

### 4.1 DC: The Power Rail

Direct current flows in one direction, at a constant voltage. In audio circuits, DC serves two roles:

1. **Power supply**: The +9V, +15V, +48V (phantom power), +250V (tube amps) that keep circuits alive. Without DC power, active components (transistors, op-amps, microcontrollers) don't work.

2. **Bias voltages**: The carefully chosen DC voltages that set transistors and tubes at their operating points. When someone says a transistor is "biased," they mean DC voltages have been arranged so the transistor sits at the right spot on its operating curve, ready to amplify the AC signal riding on top. Think of it as the "idle speed" of an engine -- the car is running, ready to respond to the gas pedal, but not moving yet. The bias is the idle; the AC audio signal is the gas pedal.

### 4.2 AC: The Signal

Audio signals are AC because sound is AC. A sound wave is a periodic variation in air pressure -- pressure goes up, pressure goes down, repeat. The electrical representation of that sound is a voltage that goes up and goes down at the same rate. A 100 Hz bass note is a voltage oscillating 100 times per second. A 10 kHz cymbal shimmer is a voltage oscillating 10,000 times per second.

Most real audio signals aren't pure sine waves -- they're complex waveforms made up of many sine waves at different frequencies and amplitudes (Fourier taught us this). A guitar's "A" note at 440 Hz contains energy at 440 Hz (the **fundamental**), 880 Hz (the **second harmonic**), 1320 Hz (the **third harmonic**), and so on up into the kilohertz range. The relative strength of these harmonics is what makes a guitar sound like a guitar and not a flute -- both might play the same fundamental, but the harmonic structure (the **timbre**) differs.

When we shape tone -- with EQ, with distortion, with filtering -- we're changing the relative amplitudes of these harmonics. A tone knob that rolls off treble is reducing the amplitude of higher harmonics, making the sound warmer and darker. A distortion pedal that clips the waveform is *adding* harmonics that weren't in the original signal, making it richer and more complex (and louder, and eventually harsh).

### 4.3 The Coupling Capacitor: Keeping AC and DC Separate

In most audio circuits, the AC signal rides on top of a DC offset. A transistor amplifier might have its output sitting at 4.5V DC, with the AC audio signal swinging +/-0.5V around that point. You don't want that 4.5V DC reaching the next stage or your speaker -- you only want the AC signal.

The solution is a **coupling capacitor** (also called a **DC-blocking capacitor**). Because capacitors pass AC and block DC, placing one between stages strips off the DC and passes only the audio signal. You'll see coupling caps in virtually every audio circuit. We'll cover the details in Chapter 1.

### 4.4 The Signal Riding the DC Rail

Here's a concrete picture of how AC and DC coexist:

Imagine a transistor amplifier powered by 9V. The transistor's collector sits at 4.5V DC (the bias point). When a guitar signal arrives, it modulates that 4.5V:

- During a positive peak: the collector might swing to 7V (4.5V + 2.5V)
- During a negative peak: the collector might swing to 2V (4.5V - 2.5V)
- During silence: the collector sits at 4.5V, doing nothing

The audio signal is the *variation* around 4.5V. The 4.5V itself is just the operating point -- it contains no audio information. The coupling capacitor at the output strips away the 4.5V and outputs just the variation: +2.5V to -2.5V, centered on 0V.

If the coupling cap is missing or fails open, the next stage receives 4.5V DC plus the audio signal. At best, this misbiases the next stage. At worst (if driving a speaker), the DC current through the speaker voice coil causes heating and displacement that can damage the driver. This is one reason why amplifiers have protection circuits that detect DC on the output.

> **What happens if...** the bias point isn't at the midpoint of the supply? If the transistor's collector sits at 7V instead of 4.5V, it can only swing 2V positive (up to 9V) but 7V negative (down to 0V). The waveform clips asymmetrically -- the positive peaks get clipped sooner than the negative peaks. This produces a characteristic "lopsided" distortion with a mix of even and odd harmonics. Some guitar amp designers deliberately set asymmetric bias points for their tonal character.

---

## 5. Frequency, Wavelength, and the Audible Spectrum

### 5.1 Frequency

**Frequency** (measured in **hertz**, symbol **Hz**) is how many times a periodic signal completes one full cycle per second. Human hearing spans roughly **20 Hz to 20,000 Hz (20 kHz)**, though most adults past age 25 have lost sensitivity above 15-16 kHz. Don't feel bad about this -- it's normal. And most of the musically important content is below 10 kHz anyway.

Some landmarks:

| Frequency | Musical/Audio Context |
|---|---|
| 20-60 Hz | Sub-bass. You feel it more than hear it. Kick drum thump, bass drops, organ pedal tones. |
| 60-250 Hz | Bass. Bass guitar fundamentals, warmth, "body" of most instruments. Male voice fundamental range. |
| 250-500 Hz | Low midrange. Can sound "boxy" or "muddy" when overemphasized. |
| 500-2000 Hz | Midrange. Voice fundamentals, guitar body, most of the "information" in music. This is where intelligibility lives. |
| 2-4 kHz | Presence. Vocal intelligibility, guitar "bite." Human ear sensitivity peak (~3-4 kHz). Telephone bandwidth tops out here. |
| 4-6 kHz | Upper presence. "Edge" and clarity. Can become harsh and fatiguing if overemphasized. |
| 6-10 kHz | Brilliance. Cymbal attack, sibilance in vocals, "air" in acoustic instruments. |
| 10-20 kHz | Extreme treble / "air." Cymbal shimmer, the very top of the harmonic series. Most adults can't hear above 15 kHz. |

Everything we do in audio -- EQ, crossovers, tone knobs, filter effects -- operates on this frequency spectrum. Understanding which frequencies correspond to which sonic characteristics is foundational. When a guitarist says "this amp is too dark," they mean there's insufficient energy above 3-5 kHz. When a vocalist says "the PA sounds harsh," they mean there's too much energy around 2-6 kHz. When a bass player says "I need more punch," they're usually talking about 700-1500 Hz. These words map to specific frequency ranges, and once you internalize that mapping, you can translate subjective audio complaints into specific electronic adjustments.

### 5.2 Wavelength

**Wavelength** (symbol **λ**, "lambda") is the physical length of one complete cycle of a wave. For sound in air:

```
λ = speed of sound / frequency ≈ 343 m/s / f
```

At 20 Hz, the wavelength is about 17 meters (56 feet). At 20 kHz, it's about 1.7 centimeters (0.7 inches). This matters enormously for speaker and room design:

- A woofer needs to be physically large enough to move a meaningful volume of air at its operating wavelengths. A 6" woofer struggles to produce significant output below 60 Hz because the cone's excursion can't displace enough air relative to the wavelength.
- You can't effectively absorb a bass frequency with an acoustic panel that's thinner than about 1/4 of the wavelength. To absorb 100 Hz (λ ≈ 3.4m), you need treatment about 0.85 meters (2.8 feet) deep. This is why bass trapping is hard and expensive.
- Room modes (standing waves) occur at frequencies whose half-wavelengths fit evenly between opposing walls. A room that's 3.4 meters wide has a fundamental standing wave at about 50 Hz.

For electrical signals in cables, wavelength is much longer (because electrical signals travel at 60-90% the speed of light), so cable length is almost never an issue for audio frequencies. If someone tells you they can hear the difference between a 1-meter and 2-meter guitar cable at audio frequencies, the physics disagrees -- the wavelength of a 20 kHz signal in a cable is roughly 10 kilometers. Cable *capacitance* is what affects guitar tone, not wavelength effects. We'll discuss this when we talk about guitar cables and buffer circuits.

### 5.3 Octaves and Decades

Audio people think in **octaves** and **decades**:

- An **octave** is a doubling of frequency. 100 Hz to 200 Hz is one octave. 1 kHz to 2 kHz is one octave. 10 kHz to 20 kHz is one octave. The audible range spans about 10 octaves (20 Hz to 20 kHz).
- A **decade** is a tenfold increase. 100 Hz to 1 kHz is one decade. The audible range spans about 3 decades.

This logarithmic thinking maps directly to human perception -- we hear equal *ratios* of frequency as equal musical intervals. The jump from 100 Hz to 200 Hz sounds like the same "interval" as 1000 Hz to 2000 Hz, even though the latter spans 10x more hertz. This is why frequency response plots use a logarithmic x-axis, and why we measure things in decibels (logarithmic scale) rather than raw voltage ratios.

This also explains filter specifications. When we say a filter has a slope of "-6 dB per octave" (or equivalently "-20 dB per decade"), we mean that for each doubling of frequency beyond the cutoff, the output drops by 6 dB (half the voltage). A filter with "-12 dB per octave" drops by 12 dB per doubling (quarter the voltage). Steeper slopes mean sharper filtering -- a more abrupt transition between the frequencies that pass and those that don't.

### 5.4 Musical Notes and Frequencies

For audio builders, it's handy to know where common musical notes live:

| Note | Frequency | Context |
|---|---|---|
| Low E (bass guitar) | 41 Hz | The lowest standard bass note |
| Low E (guitar, standard tuning) | 82 Hz | Open 6th string |
| A (tuning reference) | 440 Hz | The universal reference pitch |
| Middle C (piano) | 261.6 Hz | Approximate center of the piano |
| High E (guitar, 24th fret, 1st string) | 1319 Hz | Top of the standard guitar range (fundamental) |
| Soprano voice top | ~1000 Hz | Fundamental; harmonics extend much higher |

Note that the *fundamental* of a guitar string might only go up to about 1.3 kHz, but the *harmonics* extend much higher -- up to 5-10 kHz for the prominent ones. This is why a guitar still sounds different through a speaker that rolls off at 5 kHz versus one that extends to 10 kHz, even though no fundamental is above 1.3 kHz.

---

## 6. Phase: Why Timing Matters

### 6.1 What Phase Is

**Phase** describes where a signal is in its cycle at a given moment in time, measured in degrees (0-360) or radians (0-2π). Two signals at the same frequency can be "in phase" (peaks align, troughs align) or "out of phase" (one's peak aligns with the other's trough).

Think of two people on swings. If they swing in sync (both forward at the same time), they're in phase. If one is forward when the other is backward, they're 180 degrees out of phase. If one is always slightly ahead, they have a phase difference somewhere between 0 and 180 degrees.

**In phase (0 degrees)**: The signals add together. Two 1V signals in phase produce a 2V combined signal. This is constructive interference. +6 dB boost.

**Out of phase (180 degrees)**: The signals cancel. Two identical 1V signals 180 degrees out of phase produce *silence* -- 0V. This is destructive interference. Complete cancellation (in theory; in practice, perfect cancellation is rare because signals are never perfectly identical).

**Partially out of phase (anywhere between)**: You get something in between -- partial reinforcement at some frequencies, partial cancellation at others. This creates the "comb filter" effect -- a hollow, phasey sound with regular dips and peaks across the frequency spectrum.

### 6.2 Where Phase Matters in Audio

Phase shows up everywhere:

- **Speaker crossovers**: When a crossover splits the signal between a woofer and a tweeter, the filter circuits introduce phase shifts. If the woofer and tweeter are out of phase at the crossover frequency, you get a notch -- a dip in response right where the two drivers are supposed to blend seamlessly. Getting the phase right at crossover is one of the most important parts of speaker design. (Chapters 12-13 cover this in detail.)

- **Multi-microphone recording**: Two mics picking up the same source at different distances will receive the signal at different times, creating phase differences that vary with frequency. This is why the "3:1 rule" exists in recording (for every unit of distance from source to near mic, the far mic should be at least 3 units away).

- **Effects pedals**: Phaser and flanger pedals work by splitting the signal, shifting the phase of one copy, and mixing it back. The constantly changing phase relationship creates that sweeping, swooshing sound. The difference between a phaser and a flanger is the mechanism of the phase shift (all-pass filters vs time delay), which produces different comb-filter patterns.

- **Feedback in circuits**: In amplifier design, phase determines whether feedback is *negative* (stabilizing, reducing distortion) or *positive* (destabilizing, causing oscillation). An amplifier that's supposed to have negative feedback at all frequencies but accidentally has positive feedback at some high frequency will oscillate -- it becomes an unintended oscillator instead of an amplifier. This is a real problem that shows up regularly in DIY builds.

- **Balanced audio connections**: Balanced cables carry two copies of the signal, one inverted (180 degrees out of phase). At the receiving end, the inverted copy is flipped back and added to the original. Any noise picked up along the cable (which is the same on both copies) cancels out. This is called "common-mode rejection" and it's why balanced connections can run long distances without picking up hum or interference.

### 6.3 Phase Shift in Filters

Every filter (low-pass, high-pass, bandpass) introduces phase shift. A simple first-order RC low-pass filter introduces up to 90 degrees of phase shift. A second-order filter introduces up to 180 degrees. This phase shift is *inherent* to the filtering process -- you can't have one without the other in analog circuits. (Digital filters can be designed with zero phase shift using FIR techniques, which is one advantage of DSP-based crossovers. We'll explore this in Part 4.)

Here's the phase shift profile of a first-order RC low-pass filter:
- Well below cutoff: ~0 degrees (no shift)
- At the cutoff frequency: -45 degrees
- Well above cutoff: approaching -90 degrees

And a first-order high-pass:
- Well below cutoff: approaching +90 degrees
- At the cutoff frequency: +45 degrees
- Well above cutoff: ~0 degrees (no shift)

### 6.4 Phase and Polarity: Don't Confuse Them

**Polarity** is a simple inversion: flip the signal upside down. Swap + and - throughout. It's an instantaneous, frequency-independent operation.

**Phase shift** is a time delay that corresponds to different angular offsets at different frequencies. A 1 ms delay produces 360 degrees of phase shift at 1 kHz (one complete cycle), but only 36 degrees at 100 Hz, and 3600 degrees (10 complete cycles) at 10 kHz.

When someone says a speaker driver is wired "out of phase," they usually mean "inverted polarity" -- the + and - wires are swapped. The fix is simple: swap them back. Actual phase shift (from filters, distance, or room reflections) is frequency-dependent and can't be fixed by swapping wires.

> **What happens if...** you cascade multiple filter stages without thinking about phase? Each stage adds its own phase shift. Four stages of 90-degree phase shift gives you 360 degrees -- which is the same as 0 degrees, but the signal has been delayed. In feedback systems, accumulated phase shift is the primary cause of instability and oscillation. This is why amplifier designers obsess over "phase margin" -- the amount of phase shift remaining before the feedback loop reaches 360 degrees (positive feedback) at a frequency where the loop gain is still above unity.

---

## 7. Power and Watts

### 7.1 What Power Is

**Power** (measured in **watts**, symbol **W**) is the rate at which energy is consumed or transferred. In DC circuits:

```
P = V × I
```

Power (watts) = Voltage (volts) x Current (amps). Combined with Ohm's law, this gives us two more incredibly useful forms:

```
P = V² / R
P = I² × R
```

These are the equations you'll use constantly. Want to know how much power a speaker is dissipating? You need the voltage across it and its impedance. Want to know how much power a resistor is handling (so you can pick one that won't burn up)? Same equations.

### 7.2 Power Dissipation in Components

Every component that has voltage across it and current through it dissipates power as heat. This matters for component selection:

**Resistors**: A 1kΩ resistor with 10V across it dissipates P = V²/R = 100/1000 = 0.1W. A standard 1/4W (0.25W) resistor handles this easily. But that same 1kΩ resistor with 20V across it dissipates 0.4W -- and a 1/4W resistor will overheat, potentially burning the PCB or catching fire. Always check power dissipation and use appropriately rated components.

**Transistors and ICs**: Active components dissipate power internally. A voltage regulator that converts 12V to 9V at 100 mA drops 3V at 100 mA = 0.3W. That energy becomes heat. Higher current or larger voltage drops mean more heat, which may require heatsinks.

**LEDs**: An LED drawing 15 mA with 2V forward voltage dissipates 0.03W. Trivial. But the current-limiting resistor dropping the remaining 7V at 15 mA dissipates 0.105W, which is fine for a 1/4W resistor but worth checking.

### 7.3 Real Power vs Apparent Power

In DC circuits, power is straightforward. In AC circuits with reactive components (capacitors, inductors, speakers), it gets more nuanced.

**Real power** (watts, W): The power that actually does work -- moves a speaker cone, heats a resistor, makes sound. This is what you pay for in electricity and what matters for thermal design.

**Reactive power** (volt-amps reactive, VAR): Power that flows back and forth between the source and reactive components. It doesn't do useful work but it does load the amplifier.

**Apparent power** (volt-amps, VA): The product of RMS voltage and RMS current, regardless of phase. In a circuit with reactive components, current and voltage can be out of phase, meaning some power flows back and forth without doing useful work.

**Power factor**: The ratio of real power to apparent power. A purely resistive load (like a heater) has a power factor of 1.0. A speaker, being partly reactive, might have a power factor of 0.7 at some frequencies.

For most audio work, you can treat speaker loads as roughly resistive and use P = V²/R with the nominal impedance. But when designing amplifier power supplies or choosing wire gauges, the reactive component matters -- the amplifier has to supply the apparent power even though only the real power produces sound.

### 7.4 Power in the Audio Signal Chain

Power levels span an enormous range in audio:

| Stage | Typical Power Level |
|---|---|
| Guitar pickup output | ~0.0001 W (0.1 mW) |
| Line-level signal | ~0.001 W (1 mW) |
| Headphone amplifier | 0.01 - 1 W |
| Guitar amp (practice, bedroom) | 1-5 W |
| Guitar amp (gigging) | 15-50 W (tube); 30-100 W (solid state) |
| Hi-fi stereo amplifier | 20-200 W per channel |
| PA / live sound power amp | 500-2000 W per channel |
| Concert PA system (total) | 10,000-100,000 W |

Notice: going from a guitar pickup to a concert PA system is a power increase of roughly one *billion* times (about 90 dB). The audio signal chain is fundamentally a chain of amplification.

### 7.5 Power and Speaker Sensitivity: The Most Important Relationship

Here's a practical power fact that surprises most people: **doubling the power gives you only 3 dB more volume** -- barely noticeable. To sound "twice as loud" (a subjective +10 dB), you need about **10 times the power**. This is why a 100W amp doesn't sound "ten times louder" than a 10W amp -- it only sounds roughly twice as loud.

This has profound implications:

| Power | dB above 1W | Perception (relative to 1W) |
|---|---|---|
| 1 W | 0 dB | Reference |
| 2 W | +3 dB | Just noticeable louder |
| 5 W | +7 dB | Clearly louder |
| 10 W | +10 dB | "Twice as loud" |
| 100 W | +20 dB | "Four times as loud" |
| 1000 W | +30 dB | "Eight times as loud" |

This also means speaker sensitivity matters enormously. A speaker rated at 90 dB/W/m (90 dB of sound at 1 watt, measured at 1 meter) needs 100 watts to produce 110 dB. A more sensitive speaker at 96 dB/W/m needs only 25 watts for the same 110 dB. The 6 dB sensitivity advantage is worth a 4x reduction in amplifier power. We'll come back to this math extensively in the speaker chapters.

This is also why tube guitar amps seem "louder" than their solid-state counterparts at the same wattage rating. It's not magic -- tube amps clip differently (more gradually, with more even harmonics), which keeps the perceived loudness high even when the amp is being driven into distortion. A solid-state amp clips more abruptly and sounds harsh at the same power level, so players turn it down sooner.

> **What happens if...** you connect an 8Ω speaker to an amp rated for 4Ω? The amp delivers less power (because higher impedance means less current). If the amp delivers 100W into 4Ω, it'll deliver about 50W into 8Ω. That's only 3 dB quieter -- barely noticeable. Going the other direction (4Ω speaker on an amp rated for 8Ω) makes the amp work harder (more current). If the amp can handle it, you get more power. If it can't, it overheats or clips. Always check the amp's minimum impedance rating.

---

## 8. Decibels: The Universal Audio Unit

### 8.1 Why Decibels?

The **decibel** (dB) is a logarithmic ratio. We use it in audio because:

1. Human hearing is logarithmic -- we perceive equal *ratios* of intensity as equal steps in loudness. Doubling the power (a 2:1 ratio) sounds like the same "step" whether you go from 1W to 2W or from 50W to 100W.

2. The dynamic range of hearing (threshold of hearing to threshold of pain) spans about **120 dB** -- a power ratio of 1,000,000,000,000 to 1 (one trillion). Writing that as a linear number is absurd. In dB, it's a manageable number.

3. Logarithmic math turns multiplication into addition. A preamp with 20 dB gain feeding an amp with 30 dB gain gives 50 dB total gain. Much easier than multiplying voltage ratios (10 x 31.6 = 316, then converting back to dB).

4. The industry has standardized on dB for everything. Signal levels, noise floors, frequency response, gain, loss -- all in dB. Once you're fluent in dB, you speak the universal language of audio.

### 8.2 dB as a Ratio

In its purest form, dB is a ratio -- it compares two values:

**For power:** `dB = 10 × log10(P1 / P2)`

**For voltage:** `dB = 20 × log10(V1 / V2)`

(The factor is 20 for voltage because power is proportional to voltage squared, and log of a square is 2x the log.)

Some key dB values to memorize:

| dB | Power Ratio | Voltage Ratio | Perception |
|---|---|---|---|
| 0 dB | 1:1 | 1:1 | No change |
| +1 dB | 1.26:1 | 1.12:1 | Imperceptible (in most contexts) |
| +3 dB | 2:1 | 1.41:1 | Just noticeable louder |
| +6 dB | 4:1 | 2:1 | Clearly louder |
| +10 dB | 10:1 | 3.16:1 | "Twice as loud" (subjective) |
| +20 dB | 100:1 | 10:1 | Much louder |
| +40 dB | 10,000:1 | 100:1 | Enormously louder |
| -3 dB | 1:2 | 1:1.41 | Just noticeable quieter |
| -6 dB | 1:4 | 1:2 | Clearly quieter |
| -10 dB | 1:10 | 1:3.16 | "Half as loud" |
| -20 dB | 1:100 | 1:10 | Very quiet relative to original |

The -3 dB point is special in filter design: it's the cutoff frequency, where the power is halved. In frequency response plots, the -3 dB points define the "bandwidth" of the system.

### 8.3 dB with References: dBV, dBu, dBSPL, dBFS

Raw dB is a ratio with no absolute meaning. To express absolute levels, we attach a reference:

**dBV** -- referenced to 1 volt RMS.
- 0 dBV = 1.0 Vrms
- Consumer line level is typically -10 dBV = 0.316 Vrms
- Used in consumer audio specifications.
- Quick conversion: dBV = 20 × log10(Vrms / 1.0)

**dBu** -- referenced to 0.7746 volts RMS (the voltage that produces 1 mW across 600 ohms -- a historical telephone standard).
- 0 dBu = 0.7746 Vrms
- Professional line level is typically +4 dBu = 1.228 Vrms
- Used in professional audio specifications.
- Quick conversion: dBu = 20 × log10(Vrms / 0.7746)
- Relationship to dBV: dBu = dBV + 2.21 (always)

**dBSPL** -- referenced to 20 micropascals (the nominal threshold of human hearing at 1 kHz).
- 0 dBSPL = the quietest sound most people can hear
- Quiet room at night ≈ 30 dBSPL
- Normal conversation ≈ 60-70 dBSPL
- Loud rock concert ≈ 110-120 dBSPL
- Threshold of pain ≈ 130 dBSPL
- Used for acoustic (in-air) sound levels.

**dBFS** -- referenced to "full scale" in a digital system.
- 0 dBFS = the maximum level a digital system can represent
- Everything is negative (you can't exceed 0 dBFS without clipping)
- A well-recorded track might peak at -6 to -3 dBFS with an average around -18 dBFS
- Used in digital audio (DAWs, digital mixers, ADCs/DACs).

**dBW** -- referenced to 1 watt. Used for amplifier power ratings.
- 0 dBW = 1 W
- 10 dBW = 10 W
- 20 dBW = 100 W

> **What happens if...** you mix up dBV and dBu? The difference between them is about 2.2 dB (because the reference voltages differ by a factor of 1.228). If you connect consumer gear (-10 dBV, which equals -7.8 dBu) to pro gear expecting +4 dBu, the pro gear receives a signal about 12 dB too quiet. The signal will work, but you'll have to crank the gain up, which also raises the noise floor. Going the other direction (pro into consumer) can overdrive the consumer input, causing clipping. This is the most common "why does my home studio sound noisy?" problem, and the fix is simple: match your levels.

### 8.4 Useful dB Conversions

Here's a cheat sheet you'll use constantly:

- **Voltage doubles** → +6 dB
- **Voltage halves** → -6 dB
- **Voltage × 10** → +20 dB
- **Voltage × 100** → +40 dB
- **Power doubles** → +3 dB
- **Power halves** → -3 dB
- **Power × 10** → +10 dB
- **Perceived "twice as loud"** → +10 dB (roughly)

If someone says their amplifier has "40 dB of gain," you instantly know the voltage gain is 100x (because +20 dB = 10x, and +40 dB = 10 × 10 = 100x). If a filter is described as "rolling off at -6 dB per octave," you know the voltage halves each time the frequency doubles (or halves, depending on the filter type).

### 8.5 dB Mental Math

You can compose dB values to handle non-obvious conversions:

- **Voltage × 3**: That's close to +10 dB (9.54 dB precisely). Why? 3 ≈ 10/3.16, and +20 dB = ×10, -10 dB = ÷3.16. So ×3 ≈ +10 dB. Close enough for quick estimates.
- **Voltage × 5**: That's +20 dB - 6 dB = +14 dB. Because ×10 is +20 dB, and ÷2 is -6 dB, so ×5 is +14 dB.
- **Voltage × 0.1**: That's -20 dB (just the inverse of ×10).

With practice, you'll do these conversions in your head as naturally as a software engineer converts between KB, MB, and GB.

---

## 9. Ohm's Law Applied to Audio

### 9.1 The Three Forms

We already introduced Ohm's law: V = I × R. Its three forms (and the equivalent power forms) cover almost every circuit calculation you'll ever do:

```
V = I × R       I = V / R       R = V / I
P = V × I       P = V² / R     P = I² × R
```

These six equations are your Swiss Army knife. Let's apply them to real audio situations.

### 9.2 Worked Audio Examples

**Example 1: Guitar Volume Pot**

A guitar's volume control is a **potentiometer** (variable resistor) used as a **voltage divider**. With a 500kΩ pot (typical for humbuckers), the signal from the pickup is divided between the pot's output and ground. At full volume, essentially all the voltage appears at the output. As you turn down, more voltage is dropped across the portion of the pot connected to ground, and less reaches the output.

If your pickup produces 200 mV peak and the volume pot is at 50%:
- Output voltage ≈ 200 mV × 0.5 = 100 mV
- That's -6 dB (voltage halved)

Why 500kΩ for humbuckers and 250kΩ for single coils? The pot's resistance acts as a load on the pickup. A lower value bleeds more treble to ground (because the pickup's inductance and the pot form a low-pass filter). Humbuckers already have lower resonant peaks (less treble), so a higher pot value preserves more brightness. Single coils have more treble, so a lower pot value tames it slightly. Change the pot value and you change the guitar's tonal character -- this is one of the simplest and most effective guitar mods.

**Example 2: Speaker Power**

Your amplifier puts out 28.3 Vrms into an 8Ω speaker (this is a 100W amplifier):
- Current: I = V / R = 28.3 / 8 = 3.54 Arms
- Power: P = V² / R = 28.3² / 8 = 800.9 / 8 ≈ 100 W
- Or equivalently: P = V × I = 28.3 × 3.54 ≈ 100 W

Same amplifier into a 4Ω speaker (if it can supply the current):
- Current: I = 28.3 / 4 = 7.07 Arms (doubled!)
- Power: P = 28.3² / 4 = 200 W (doubled!)

This is why speaker impedance matters so much. Halving the impedance doubles the current draw and doubles the power. If your amplifier can't supply the extra current, it clips -- producing distortion and potentially damaging the speaker with DC-offset waveforms.

**Example 3: LED Current Limiting**

You want to add a power indicator LED to your pedal. The LED needs about 10 mA to light up, and it has a forward voltage drop of about 2V. Your power supply is 9V. What resistor do you need?

- Voltage across the resistor: 9V - 2V = 7V
- Required current: 10 mA = 0.01 A
- R = V / I = 7 / 0.01 = 700Ω
- Nearest standard value: 680Ω (slightly brighter) or 750Ω (slightly dimmer)
- Power dissipated in the resistor: P = V × I = 7 × 0.01 = 70 mW (a standard 1/4W resistor handles this easily)

**Example 4: Headroom Calculation**

You're designing a pedal circuit running on 9V with a virtual ground at 4.5V. The op-amp can swing within about 1V of each rail. What's the maximum undistorted output signal?

- Maximum positive swing: 9V - 1V = 8V
- Maximum negative swing: 0V + 1V = 1V
- Maximum symmetrical swing around 4.5V: ±3.5V peak
- Maximum Vrms (sine wave): 3.5 / 1.414 = 2.47 Vrms
- In dBu: 20 × log10(2.47 / 0.7746) = +10.1 dBu
- That's a maximum clean output of about +10 dBu. If your circuit has gain and the input signal pushes the output beyond this, the signal clips.

> **What happens if...** you forget to account for the op-amp's output swing limitation? You design a gain stage with 40 dB of gain (100×) expecting it to handle a 100 mV input signal. 100 mV × 100 = 10V peak -- but your supply is only 9V, and the op-amp can't swing past 8V. The output clips at 8V, producing severe distortion. You needed to either reduce the gain, increase the supply voltage, or attenuate the input. This is a gain staging error, and it's one of the most common design mistakes in pedal building.

---

## 10. Kirchhoff's Laws: What Happens at Junctions and in Loops

### 10.1 Kirchhoff's Current Law (KCL)

**The current entering a junction equals the current leaving it.** Current doesn't appear out of nowhere or vanish -- it's conserved at every node.

Think of it like a software load balancer: if 100 requests per second arrive, and you have three backend servers, those 100 requests per second are split among the three. The total in equals the total out.

**Audio application**: In a mixer circuit, multiple signal paths converge at a summing node. KCL governs how the currents from each input channel add together. A simple passive mixer uses resistors from each input to a common node -- the currents add (following KCL), and the combined current creates the mixed output voltage.

Consider a simple 4-input passive mixer with 10kΩ mixing resistors:
- Input 1 at 1V produces 0.1 mA into the summing node
- Input 2 at 0.5V produces 0.05 mA into the summing node
- Input 3 at 0V produces 0 mA
- Input 4 at -0.3V drains 0.03 mA from the summing node

Total current into the node: 0.1 + 0.05 + 0 - 0.03 = 0.12 mA. KCL is satisfied. This current flows through the feedback resistor of the summing op-amp to produce the mixed output.

### 10.2 Kirchhoff's Voltage Law (KVL)

**The sum of all voltage drops around any closed loop in a circuit equals zero.** Every volt provided by the power supply is consumed by the components in the loop.

Think of it like a hiking trail: if you go up 500 meters and then down 500 meters, you end up at the same elevation. Voltage works the same way around a loop.

**Audio application**: Consider a simple transistor amplifier stage powered by 9V:
- The 9V supply provides +9V
- Some voltage is dropped across the collector resistor (say 4.5V)
- Some voltage appears across the transistor (say 4.5V)
- Total: +9V - 4.5V - 4.5V = 0V. KVL is satisfied.

If you change the collector resistor, the voltage distribution changes -- but the sum always equals the supply voltage. This is the basis for setting the **bias point** of a transistor amplifier, which determines its gain, headroom, and clipping behavior. If the transistor "sits" at 4.5V (halfway), it has maximum symmetric swing before clipping. If it sits at 7V, it clips asymmetrically -- which some guitarists actually prefer for its tonal character.

### 10.3 KVL in a Real Pedal Power Supply

Let's trace a voltage loop in a pedal with a voltage regulator:

- 12V wall adapter provides 12V DC
- Reverse-polarity protection diode drops 0.5V (KVL: 12V - 0.5V = 11.5V remaining)
- 7809 voltage regulator drops the remaining excess: 11.5V - 9V = 2.5V dropped across the regulator
- The circuit receives 9V

KVL is satisfied: 12V (source) = 0.5V (diode) + 2.5V (regulator) + 9V (circuit). If you measure 9V at the circuit, you've confirmed KVL.

If you measure 7V at the circuit instead, something is wrong. KVL says the missing 2V must be somewhere -- maybe the adapter is only providing 10V (dying batteries?), maybe the regulator is damaged, maybe there's excess resistance in a connector. KVL gives you a framework for finding the fault.

### 10.4 Combining the Laws

KCL and KVL together let you analyze any circuit, no matter how complex. In practice, you'll use them most when:

- Designing **voltage dividers** (for biasing, for attenuation)
- Analyzing **feedback networks** (in op-amp circuits)
- Understanding **power supply distribution** (what voltage reaches each part of your circuit)
- Debugging (if the voltages don't add up around a loop, something is wrong -- you've found the fault)
- Calculating **node voltages** in complex networks (needed for bias point calculations in discrete amplifier design)

In software terms, KCL is like the principle of conservation in a data pipeline (every byte that enters a process either stays or leaves -- none are created or destroyed). KVL is like energy conservation in a computation (the total work done by a process equals the energy input). Both are bookkeeping tools that let you verify your design or find errors.

---

## 11. Putting It All Together: Signal Flow

### 11.1 The Audio Signal Chain as an Electrical Story

Let's trace a signal from guitar to speaker, using everything we've covered:

1. **Guitar pickup**: A vibrating string creates a changing magnetic field, which induces an AC voltage in the pickup coil (Faraday's law of electromagnetic induction). Output: ~200 mV peak, high impedance (7-15 kΩ at 1 kHz), frequency content from about 80 Hz to 5 kHz with harmonics extending higher.

2. **Guitar cable**: The cable has capacitance (~30 pF per foot). This capacitance forms a low-pass filter with the pickup's high output impedance, rolling off high frequencies. A 20-foot cable (600 pF) with a 10kΩ source creates a filter with a cutoff around f = 1/(2π × 10000 × 600e-12) ≈ 26.5 kHz. That's above audibility, but the roll-off starts well below the cutoff, and multiple cables and effects loops compound the loss. (This is why some players prefer shorter cables or buffers.)

3. **Pedal input**: The pedal presents a high input impedance (1 MΩ typical) so it doesn't load down the guitar. The signal enters the circuit through a coupling capacitor that blocks any external DC.

4. **Pedal circuit**: The signal is amplified (voltage increased by transistor or op-amp gain stages), filtered (capacitors and resistors shape the frequency response), and potentially clipped (diodes or transistor saturation create distortion by flattening the peaks of the waveform). Throughout, DC bias voltages keep the active components in their operating range, while coupling capacitors keep the DC bias from leaking between stages.

5. **Pedal output**: The processed signal exits through another coupling capacitor at low impedance (typically 1-10 kΩ), ready to drive the next device without losing high frequencies to cable capacitance.

6. **Amplifier preamp**: More gain, more tone shaping. The volume and tone controls are potentiometers and capacitors creating variable voltage dividers and filters. Each stage follows Ohm's law, each junction follows KCL, each loop follows KVL.

7. **Amplifier power stage**: The signal is amplified to high voltage (tens of volts) and high current (amps) to drive the speaker. Power transistors or tubes handle this heavy lifting. Real power (watts) is being delivered.

8. **Speaker**: The amplified AC voltage drives current through the speaker's voice coil, which sits in a magnetic field. The current creates a force (electromagnetism), moving the cone back and forth, pushing air, creating sound waves that reach your ears. The speaker's frequency-dependent impedance means different frequencies draw different amounts of current from the amplifier.

Every connection in this chain involves impedance matching. Every stage involves voltage gain (or intentional attenuation). Every filter involves frequency-dependent impedance. Phase shifts accumulate through the chain. Power levels increase at each amplification stage. Every concept in this chapter is at work, simultaneously, all the time.

### 11.2 The Gain Staging Perspective

Looking at the same chain from a gain staging perspective:

| Stage | Voltage Level | Gain (dB) | Cumulative Gain |
|---|---|---|---|
| Guitar pickup | 200 mV pk | -- | -- |
| After cable loss | 195 mV pk | -0.2 dB | -0.2 dB |
| Pedal input buffer | 195 mV pk | 0 dB | -0.2 dB |
| Pedal gain stage | 2V pk | +20 dB | +19.8 dB |
| Pedal clipping | 1V pk | -6 dB | +13.8 dB |
| Pedal volume (50%) | 0.5V pk | -6 dB | +7.8 dB |
| Pedal output buffer | 0.5V pk | 0 dB | +7.8 dB |
| Amp preamp gain | 5V pk | +20 dB | +27.8 dB |
| Amp tone stack loss | 1V pk | -14 dB | +13.8 dB |
| Amp volume (50%) | 0.5V pk | -6 dB | +7.8 dB |
| Amp power stage | 25V pk | +34 dB | +41.8 dB |
| Speaker (acoustic output) | 100 dBSPL | -- | -- |

This is a simplified, illustrative example, but it shows how the signal gets amplified, attenuated, amplified again, attenuated, and finally amplified to speaker-driving levels. Good gain staging means each stage has enough headroom, the noise floor stays low, and no stage clips unintentionally.

### 11.3 Where to Go From Here

You now have the vocabulary and conceptual framework to understand any audio circuit. The next chapter introduces the actual components -- resistors, capacitors, inductors, diodes, transistors, and op-amps -- and shows you exactly how each one shapes the audio signal. The concepts from this chapter (especially impedance, frequency response, and phase) will be the lens through which we examine every component.

---

## 12. Exercises: Testing Your Understanding

These exercises are designed to build your confidence with the concepts from this chapter. Work through them with a calculator or in your head -- the goal is fluency, not perfection.

### 12.1 Voltage and dB Conversions

1. A guitar pickup produces 200 mV peak. Convert this to:
   - Peak-to-peak voltage: _____ mV (Answer: 400 mV)
   - RMS voltage (assuming a sine wave): _____ mV (Answer: 141 mV)
   - dBV: _____ (Answer: 20 × log10(0.141/1.0) = -17 dBV)
   - dBu: _____ (Answer: -17 + 2.21 = -14.8 dBu)

2. A microphone preamp has 60 dB of gain. What is the voltage gain?
   - Answer: 20 dB = 10x, 40 dB = 100x, 60 dB = 1000x. So 60 dB = 1000× voltage gain.

3. If the preamp in question 2 has a 2 mV input signal, what is the output voltage?
   - Answer: 2 mV × 1000 = 2000 mV = 2V.

4. You have a 50W amplifier driving an 8Ω speaker. What is the output voltage (RMS)?
   - Answer: P = V²/R, so V = √(P × R) = √(50 × 8) = √400 = 20 Vrms.

5. What current does the speaker in question 4 draw?
   - Answer: I = V/R = 20/8 = 2.5 Arms.

### 12.2 Impedance Calculations

1. A 47 nF capacitor at 1 kHz: what is its impedance?
   - Zc = 1/(2π × 1000 × 0.000000047) = 1/(0.000295) = 3,386 Ω ≈ 3.4 kΩ

2. The same 47 nF capacitor at 100 Hz:
   - Zc = 1/(2π × 100 × 0.000000047) = 33,862 Ω ≈ 34 kΩ

3. A 10 mH inductor at 1 kHz:
   - Zl = 2π × 1000 × 0.01 = 62.8 Ω

4. The same inductor at 100 Hz:
   - Zl = 2π × 100 × 0.01 = 6.28 Ω

Notice the pattern: the capacitor has 10x more impedance at 100 Hz than at 1 kHz (it blocks bass more). The inductor has 10x less impedance at 100 Hz than at 1 kHz (it passes bass more easily).

### 12.3 RC Filter Cutoff Frequencies

1. R = 10kΩ, C = 100 nF: f_c = 1/(2π × 10000 × 0.0000001) = 159 Hz
2. R = 1MΩ, C = 100 nF: f_c = 1/(2π × 1000000 × 0.0000001) = 1.59 Hz (a good coupling cap value)
3. R = 470kΩ, C = 22 nF: f_c = 1/(2π × 470000 × 0.000000022) = 15.4 Hz
4. R = 250kΩ, C = 22 nF: f_c = 1/(2π × 250000 × 0.000000022) = 29 Hz (a guitar tone pot at full)

### 12.4 Power Calculations

1. Your pedal draws 25 mA from a 9V supply. What power does it consume?
   - P = V × I = 9 × 0.025 = 0.225W (225 mW).

2. How long will a 9V alkaline battery (approximately 500 mAh capacity) last?
   - Time = Capacity / Current = 500 mAh / 25 mA = 20 hours.

3. A 100W amplifier into 8Ω: what is the current? What is the voltage?
   - V = √(100 × 8) = 28.3 Vrms. I = 28.3/8 = 3.54 Arms.

4. Same amplifier into 4Ω: voltage stays the same (28.3V), but current doubles (7.07A) and power doubles (200W). Does your amplifier's power supply handle this?

### 12.5 Kirchhoff's Laws Applied

1. A 9V supply powers three LEDs in parallel, each with its own 680Ω current-limiting resistor and a 2V forward drop. What current does each LED draw? What total current does the supply provide?
   - Per LED: I = (9-2)/680 = 10.3 mA. Total: 3 × 10.3 = 30.9 mA. (KCL: current into the node equals current out.)

2. A simple transistor amplifier has: 9V supply, 4.7kΩ collector resistor, transistor Vce = 4.2V, 1kΩ emitter resistor. What is the voltage across the emitter resistor? What is the collector current?
   - By KVL: 9 = V_Rc + V_ce + V_Re. V_Re = 9 - (I × 4700) - 4.2 = ... This needs one more variable. The trick: current through Rc equals current through Re (series circuit). So: 9 = I×4700 + 4.2 + I×1000 = I×5700 + 4.2. I = 4.8/5700 = 0.84 mA. V_Re = 0.84 × 1000 = 0.84V. V_Rc = 0.84 × 4700 = 3.96V. Check: 3.96 + 4.2 + 0.84 = 9.0V. KVL satisfied.

---

## 13. A Note on Intuition vs Precision

Throughout this chapter, I've given you equations and precise calculations. But in real audio work, you'll develop an intuition that often matters more than exact numbers. You'll look at a 100 nF cap and 1 MΩ resistor and think "that's a coupling cap -- cutoff is down around a hertz or two, way below audio, it's fine." You won't do the math every time.

That intuition comes from doing the math repeatedly until the patterns are second nature. The exercises above are one way to build it. Another is to simulate (Chapter 3) and observe how changing values shifts the response. A third is to build circuits and listen to what changes when you swap components.

The goal isn't to memorize formulas. The goal is to *think in dB, think in impedance, think in frequency*. When someone says "this amp has 20 dB of gain and the tone stack has 15 dB of insertion loss," you should instantly know: the net gain of that section is 5 dB, and the signal is about 1.8× louder after those two stages combined. When someone says "the crossover frequency is 2.5 kHz," you should immediately think: that's the boundary between midrange and treble, the woofer handles everything below, the tweeter handles everything above.

That fluency is what separates someone who follows schematics from someone who designs circuits. This chapter gave you the vocabulary. The rest of the guide will give you the practice.

---

## Key Takeaways

1. **Voltage is signal amplitude**. Louder = higher voltage. The entire audio chain is about manipulating voltage.
2. **Current does the physical work** -- moving speaker cones, lighting LEDs, powering circuits. Ensure your supply can deliver enough.
3. **Impedance is frequency-dependent resistance**. It's the reason tone controls, crossovers, and filters exist. It's the most important concept in audio electronics.
4. **Audio signals are AC**; power supplies are DC. Coupling capacitors keep them separate. Headroom is the space between your signal and the supply rails.
5. **Frequency maps to pitch**, and octaves/decades are the logarithmic units we think in. The audible spectrum is 20 Hz to 20 kHz.
6. **Phase matters** at crossovers, in feedback circuits, and whenever signals combine. Phase shift is inherent to filtering.
7. **Decibels are logarithmic ratios**. +6 dB = voltage doubles. +3 dB = power doubles. +10 dB ≈ "sounds twice as loud."
8. **Ohm's law (V = IR) and Kirchhoff's laws (KCL/KVL)** are the tools you use to analyze every circuit.
9. **The signal chain** is a story of impedance matching, gain staging, and frequency shaping from source to speaker.
10. **Every concept in this chapter appears in every chapter that follows.** This is the foundation.

---

*Next: [Chapter 1 -- Components and What They Do to Signals](01-components-and-signals.md)*
