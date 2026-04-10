<!--
  CHAPTER: 21
  TITLE: Power Supply Design
  PART: 3 — Amplifiers
  PREREQS: Chapter 20
  KEY_TOPICS: linear power supplies, transformers, rectification, filter capacitor sizing, voltage regulation, LM317/LM337, SMPS, dual-rail supplies, tube B+ supply, bleeder resistors, PCB layout, grounding topologies
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 21: Power Supply Design

> **Part 3 — Amplifiers** | Prerequisites: Chapter 20 | Difficulty: Inter→Adv

The power supply is the heart of any amplifier. It's also the part that beginners most often underestimate. A beautifully designed amplifier circuit fed by a mediocre power supply will sound mediocre. A simple amplifier circuit fed by a rock-solid supply will sound surprisingly good. The power supply determines how much current the amp can deliver, how much hum it produces, how it responds to transients, and — in the case of tube amps — whether it stores enough energy to kill you hours after being unplugged.

That last point deserves emphasis. The high-voltage section of this chapter (Section 6) deals with **lethal voltages**. A tube amp's B+ supply produces 250-600V DC, stored in capacitors that can hold their charge indefinitely without a bleeder resistor. Before you build anything from this chapter, read Chapter 22 on safety. Then read it again.

This chapter covers the full spectrum of audio power supply design — from the humble linear supply that feeds your chip amp to the high-voltage B+ supply that keeps your tubes glowing. We'll also tackle the perennial headache of grounding, because the difference between a silent amplifier and a humming one often comes down to where you connect the ground wires.

### In This Chapter
- Linear power supplies: transformer, rectifier, filter, regulator
- Voltage regulation: LM317/LM337 and discrete approaches
- Switch-mode power supplies (SMPS) for audio
- Dual-rail supplies for op-amp circuits
- Tube amplifier B+ supply — high voltage, high danger
- Power supply PCB layout principles
- Grounding topologies: star ground, ground plane, hybrid

### Related Chapters
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) — capacitors, diodes, and transformers
- [Ch 19: Solid-State Amplifier Design](19-solid-state-amplifier-design.md) — what the supply feeds
- [Ch 20: Tube Amplifier Theory](20-tube-amplifier-theory.md) — high-voltage supply requirements
- [Ch 22: Safety: Working with Mains and High Voltage](22-safety-mains-high-voltage.md) — CRITICAL for high-voltage work
- [Ch 23: Build: Chip Amp and Class D Amp](23-build-chip-amp-class-d.md) — supply builds for both amps
- [Ch 24: Build: Tube Guitar Amplifier](24-build-tube-guitar-amp.md) — building the B+ supply
- [Ch 31: PCB Design for Audio](../part-5-pcb-professional/31-pcb-design-for-audio.md) — layout techniques for power supplies

---

## 1. Linear Power Supply Fundamentals

The **linear power supply** is the traditional approach for audio amplifiers. It's simple, produces very low noise, and its behavior is well understood. The trade-offs are size, weight, and heat — but for audio, those trade-offs are often worth it.

### 1.1 The Four Stages

Every linear supply follows the same basic chain:

```
AC Mains ──► Transformer ──► Rectifier ──► Filter ──► Regulator ──► DC Output
  (120/240V)   (step up/down)  (AC→DC)     (smooth)   (stabilize)
```

Each stage has a job. Let's walk through them.

### 1.2 The Transformer

The **power transformer** does two things:
1. **Voltage conversion:** Steps the mains voltage (120V or 240V AC) to the voltage your circuit needs.
2. **Isolation:** Galvanically separates your circuit from the mains. This is a safety feature — if something goes wrong in your circuit, the mains current can't flow through it (and through you).

**Transformer types for audio:**

**Laminated EI-core:** The traditional design. Laminated steel core in an E-I shape. Inexpensive, available in many ratings, good performance. Can produce mechanical hum (magnetostriction) at 100/120 Hz. Fine for most builds.

**Toroidal:** Donut-shaped core with windings wrapped around it. Lower magnetic leakage (less EMI), lower mechanical hum, lighter weight for the same power rating, more efficient. More expensive. The preferred choice for quality amplifier builds.

**R-core:** A variation of toroidal with a rectangular core cross-section. Even lower leakage than toroidal. Less common, used in high-end applications.

**Sizing the transformer:**
The VA (volt-ampere) rating must exceed the maximum power draw of your circuit. For a Class AB amplifier, the transformer must supply peak current during signal peaks while maintaining voltage. A rough guideline:

```
VA_rating ≥ 1.5 × Maximum_output_power (for Class AB)
VA_rating ≥ 1.2 × Maximum_output_power (for Class D, regulated supplies)
```

**Why 1.5x for Class AB?** A Class AB amplifier at full power draws peak currents that are much higher than the average. The transformer must handle these peaks without excessive voltage sag. Additionally, the rectifier and filter caps draw current only during the peak of the AC waveform (when the instantaneous AC voltage exceeds the cap voltage), creating a current waveform with a high peak-to-average ratio. The transformer must supply these high peak currents, and if undersized, it will sag and overheat.

**Worked example:** For a stereo LM3886 amp delivering 2 × 40W into 8 ohms:
- Total output power: 80W
- At ~55% efficiency: DC power needed ≈ 80 / 0.55 = 145W
- With 1.5x safety margin: VA ≥ 145 × 1.5 = 218VA
- Choose a 225VA or 300VA toroidal transformer (standard sizes)

**Choosing the secondary voltage:**
The DC output voltage after rectification and filtering is approximately:

```
Vdc ≈ Vac_rms × √2 - Vdrop_diodes - Vripple/2
```

For a 2×18V secondary through a bridge rectifier:
```
Vdc ≈ 18 × 1.414 - 1.4 - 0.5 ≈ 23.5V
```

This gives ±23.5V rails. For higher power (up to 68W into 4 ohms), use a 2×22V secondary for about ±29V rails — close to the LM3886's sweet spot.

**Regulation (voltage droop under load):**
A transformer's output voltage drops as you draw more current. The amount of droop depends on the transformer's regulation spec (typically 5-15%). A 10% regulation on a 25V AC secondary means: no-load output is 25V, full-load output is about 22.5V. Size your secondary voltage for the loaded condition, not the no-load spec.

**Transformer sourcing:**
- **Antek:** Excellent toroidal transformers at reasonable prices, available direct from their website
- **Talema:** European manufacturer with a wide range, available from Mouser/Digi-Key
- **Hammond:** Wide selection of both EI-core and toroidal transformers
- **Signal Transformer:** Good selection of standard sizes
- **Custom wind:** For tube amp transformers with specific voltage/current requirements, ClassicTone and Heyboer offer standard designs. Mercury Magnetics offers custom winding.

For a stereo LM3886 amp delivering 2 × 50W = 100W: use at least a 150VA transformer.

### 1.3 Rectification

The rectifier converts AC to pulsating DC. Several topologies:

**Full-wave bridge:** Four diodes arranged in a bridge. Uses the entire transformer secondary winding. The most common topology for solid-state amp supplies.

```
        AC in ──┬──[D1]──┬── +DC
                │        │
               [D3]    [D2]
                │        │
        AC in ──┴──[D4]──┴── -DC (ground)
```

Output: full-wave rectified, at 2× the mains frequency (120 Hz for 60 Hz mains, 100 Hz for 50 Hz mains).

Peak DC voltage ≈ Vac_rms × √2 - 2 × Vf_diode

For a 25V AC secondary: Vpeak ≈ 25 × 1.414 - 1.4 = 34 V

**Center-tapped full-wave:** Uses a center-tapped transformer secondary with only two diodes. Each diode conducts on alternate half-cycles. Simpler, but wastes half the secondary winding at any given moment. Used in dual-rail supplies (Section 5).

```
        AC+ ───[D1]──┬── +DC
                      │
        CT ───────────┼── Ground
                      │
        AC- ───[D2]──┴── -DC (through separate rectifier)
```

**Tube rectifiers (for tube amps):** Tubes like the 5Y3, 5U4, and GZ34 serve as rectifiers. They have higher forward voltage drop (15-60V) and higher impedance than silicon diodes. This "soft" rectification contributes to the "sag" character of tube amps — under heavy load, the voltage droops more. Many guitar amp players specifically prefer tube rectification for this dynamic compression.

### 1.4 Filter Capacitors

The rectifier output is pulsating DC — it has a large ripple at 100/120 Hz. The **filter capacitor** smooths this into (nearly) pure DC by storing energy during the voltage peaks and releasing it during the valleys.

**Ripple voltage calculation:**

```
Vripple ≈ Iload / (2 × f × C)
```

Where f is the ripple frequency (100 or 120 Hz) and C is the filter capacitance.

For a 2A load with a 10,000 uF capacitor at 120 Hz:

```
Vripple = 2 / (2 × 120 × 0.01) = 0.83V peak-to-peak
```

That's roughly 0.83V of 120 Hz hum on your supply rail. With 35V DC, that's about 2.4% ripple. Not great for audio — you'd hear that as hum.

**How much capacitance?**

More is generally better, up to a point. Common guidelines:
- 2,000-4,700 uF per amp of load current for basic filtering
- 10,000+ uF per amp for low-hum hi-fi applications

But there's a limit: very large filter caps create high inrush current at power-on (the caps are essentially short circuits until charged). This stresses the rectifier diodes and transformer. Soft-start circuits or inrush current limiters (NTC thermistors) are used to manage this.

**ESR matters:**

**ESR (Equivalent Series Resistance)** is the internal resistance of the capacitor. High ESR reduces the cap's ability to smooth ripple and can cause heating. For power supply filters, use **low-ESR** electrolytic capacitors. Panasonic FR, Nichicon HE, and Kemet ALC70 are excellent choices.

### 1.5 The Complete Unregulated Supply

Putting it together for an LM3886 amplifier (±28V rails, 3A peak per channel):

```
Mains ──[Fuse]──[Switch]──► Toroidal Transformer (2×20V AC, 160VA)
                                    │
                            ┌───────┴───────┐
                            │               │
                     [Bridge Rect.]   [Bridge Rect.]
                            │               │
                     ┌──[10000uF]──┐  ┌──[10000uF]──┐
                     │     │       │  │     │        │
                    +28V  GND    -28V +28V  GND    -28V
                     │     │       │  │     │        │
                   Ch.1   Ch.1   Ch.1 Ch.2  Ch.2    Ch.2
```

(In practice, you can use a single power supply for both channels, with adequate filter capacitance.)

---

## 2. Voltage Regulation

An unregulated supply's output voltage changes with load current (due to transformer impedance, rectifier drops, and ripple). For some circuits (especially Class AB power amps), this is acceptable — the output stage can tolerate some supply variation. For preamp circuits, op-amps, and digital circuits, regulation is essential.

### 2.1 LM317 and LM337 — The Workhorses

The **LM317** (positive) and **LM337** (negative) are adjustable three-terminal voltage regulators. They're the Swiss Army knives of power supply design.

```
        Unregulated DC ──► [LM317] ──► Regulated DC
                             │
                            [R1] (240Ω, from output to adjust pin)
                             │
                           Adjust pin
                             │
                            [R2] (sets output voltage)
                             │
                            GND
```

Output voltage:

```
Vout = 1.25V × (1 + R2/R1) + Iadj × R2
```

Since Iadj is tiny (50-100 uA), the simplified formula is:

```
Vout ≈ 1.25 × (1 + R2/R1)
```

For R1 = 240 ohm, R2 = 4.87k: Vout ≈ 1.25 × (1 + 20.3) = 26.6V.

**Key specs:**
- Output current: up to 1.5A (with adequate heat sinking)
- Dropout voltage: ~2V minimum (the input must be at least 2V higher than the desired output)
- Ripple rejection: 65-80 dB (at 120 Hz)
- Output noise: 30-50 uV typical

The LM317's ripple rejection alone transforms a mediocre unregulated supply into a clean, quiet rail. For preamp supplies, op-amp circuits, and relay logic, LM317/LM337 regulation is highly recommended.

### 2.2 Fixed Regulators: 78xx and 79xx

For standard voltages (5V, 12V, 15V), the 78xx (positive) and 79xx (negative) series are even simpler — no external resistors needed. The 7815/7915 pair gives you a clean ±15V supply for op-amp circuits with just two capacitors each.

### 2.3 Discrete Regulators

For high-current applications (>1.5A) or high-voltage applications (tube amp supplies), discrete regulators using pass transistors are necessary. A basic series pass regulator:

```
     Unregulated ──┬──► [Pass transistor (e.g., TIP41C)] ──► Regulated Out
                   │          │ (base)
                   │    [Error Amp / Zener reference]
                   │          │
                   │    [Feedback from output]
                   │
                  GND
```

The pass transistor handles the full load current, and the error amplifier adjusts its base drive to maintain constant output voltage. For tube amp B+ supplies, a MOSFET pass element can handle the high voltages involved (see Section 6).

### 2.4 When to Regulate (and When Not To)

**Regulate:**
- Preamp circuits (sensitive to supply noise)
- Op-amp rails (need stable, clean voltage)
- Digital circuits (absolutely need regulation)
- Tube preamp B+ (reduces hum in high-gain stages)

**Don't necessarily regulate:**
- Class AB power amp rails (the output stage needs the current capacity that regulation often limits, and moderate ripple is rejected by the amp's PSRR)
- Tube power amp B+ (some sag is desirable for guitar amps, and the current demands are high)
- Heater supplies (tubes are tolerant of modest heater voltage variation)

---

## 3. Switch-Mode Power Supplies (SMPS)

**Switch-mode power supplies** convert AC to DC using high-frequency switching and a transformer, rather than the bulky 50/60 Hz transformer of a linear supply.

### 3.1 How They Work

```
AC Mains ──► Rectifier ──► High-voltage DC ──► High-frequency switch (50-500 kHz)
                                                        │
                                                  [HF Transformer]
                                                        │
                                              Rectifier + Filter ──► DC Output
                                                        │
                                              Feedback controller ◄── Output sense
```

Because the transformer operates at 50-500 kHz instead of 50/60 Hz, it can be *much* smaller and lighter for the same power rating. A 500W SMPS might weigh 1 kg; a 500W linear transformer alone weighs 5-10 kg.

### 3.2 Advantages for Audio

- **Small and light:** Critical for portable PA, powered speakers, and rack-mount amps
- **Efficient:** 85-95% typical, meaning less waste heat
- **Universal voltage:** Most SMPS accept 100-240V AC input, so your amp works worldwide
- **Tight regulation:** Active feedback loop maintains output voltage under varying loads
- **Lower cost** at high power levels

### 3.3 Disadvantages for Audio

- **Switching noise:** The high-frequency switching generates EMI that can couple into sensitive analog circuits. Good SMPS designs use extensive filtering and shielding, but it's always a concern.
- **Common-mode noise:** The primary-to-secondary capacitance of the HF transformer can couple mains noise to the output. This is particularly problematic for ground-referenced audio circuits.
- **Transient response:** Some SMPS have oscillatory transient behavior under sudden load changes. Audio amplifiers with their dynamic current demands can trigger this.
- **Audible artifacts:** Poorly designed SMPS can produce audible "birdie" noise — high-frequency whining or whistling. Good designs avoid this, but cheap ones don't.

### 3.4 When to Use SMPS for Audio

**Good applications:**
- Class D amplifiers (already switching at high frequency, so the SMPS noise is less problematic)
- Subwoofer amps (limited bandwidth, so HF noise is filtered by the low-pass crossover)
- Digital circuits (DSP, DAC digital section)
- Professional PA amps (weight and size matter, and the controlled environment reduces noise concerns)

**Avoid for:**
- Sensitive analog preamps and phono stages (the noise floor is too important)
- Tube amplifier B+ supply (the switching noise will couple through the high-impedance tube circuits)
- Headphone amps (the listener is inches from the transducer, any noise is magnified)

For the Class D build in Chapter 23, we'll use a 48V SMPS — it's the standard approach and works excellently with the TPA3255. For the LM3886 build, we'll use a linear supply with a toroidal transformer for the cleanest possible rails.

---

## 4. Ripple, Noise, and Power Supply Rejection

### 4.1 PSRR — Power Supply Rejection Ratio

Every amplifier has a **PSRR (Power Supply Rejection Ratio)** — the ability to ignore noise on its supply rails. PSRR is expressed in dB:

```
PSRR = 20 × log10(Vsupply_ripple / Voutput_ripple)
```

A PSRR of 80 dB means that 1V of supply ripple produces only 0.1 mV of ripple at the output. The LM3886 has a PSRR of about 120 dB at low frequencies — exceptional. This is why unregulated supplies work well for power amp output stages.

### 4.2 What You Hear

Any ripple that makes it through the PSRR and output to the speaker becomes audible hum. At 50/60 Hz, it's a low-pitched hum. At 100/120 Hz (full-wave rectified ripple), it's a slightly higher-pitched buzz.

The rule of thumb: ripple at the amplifier output should be at least 80 dB below the maximum signal level to be inaudible. For a 50W amp into 8 ohms (Vrms = 20V), this means output ripple below 200 uV. Achievable with decent filtering and reasonable PSRR.

### 4.3 Reducing Ripple

**More filter capacitance:** Direct approach. Double the capacitance, halve the ripple.

**RC filtering:** A resistor followed by a capacitor forms an additional low-pass filter. Used in tube amps to clean up the B+ for preamp stages:

```
B+ (raw) ──[R, 1-10k]──┬── B+ (filtered for preamp)
                        │
                    [C, 22-100uF]
                        │
                       GND
```

**LC filtering (choke input):** An inductor (called a "choke" in power supply parlance) followed by a capacitor. The inductor's impedance increases with frequency, providing excellent ripple attenuation. Common in high-end tube amp supplies.

```
B+ (raw) ──[Choke, 5-10H]──┬── B+ (filtered)
                            │
                        [C, 22-100uF]
                            │
                           GND
```

**CRC and CLC filtering:** Cascading multiple RC or LC filter sections for progressively cleaner B+ at each stage. In a tube guitar amp, the B+ might pass through 2-3 RC filter sections, with the cleanest supply feeding the first preamp stage (which has the most gain ahead of it and thus amplifies any remaining ripple the most).

**Voltage regulation:** As described in Section 2, an LM317 or discrete regulator provides 60-80 dB of additional ripple rejection.

---

## 5. Dual-Rail Supplies for Op-Amp Circuits

Op-amps and many linear ICs (including the LM3886) require bipolar power supplies: a positive rail (+V), a negative rail (-V), and a ground reference at the midpoint.

### 5.1 Center-Tapped Transformer Approach

The cleanest way to generate dual rails:

```
              ┌──[D1]──[D2]──┬──[C+]──► +V
              │               │         │
    Mains ──[Transformer]   GND       [Bypass caps]
              │               │         │
              └──[D3]──[D4]──┴──[C-]──► -V
                (center tap = GND)
```

A center-tapped transformer with two bridge rectifiers (or two pairs of diodes in a full-wave configuration) produces symmetrical positive and negative rails with the center tap as ground.

For the LM3886 build: a 2×20V center-tapped toroidal transformer produces approximately ±28V DC after rectification — ideal for driving 8-ohm speakers to rated power.

### 5.2 Virtual Ground (Rail Splitter)

If you only have a single positive supply (e.g., from an SMPS), you can create a "virtual ground" at the midpoint using a rail splitter:

```
     +V ──────────┬──── +V/2 (positive rail relative to virtual ground)
                  │
              [TLE2426] or [op-amp buffer with R-R divider]
                  │
             Virtual GND
                  │
              [TLE2426] or [buffer]
                  │
     GND ─────────┴──── -V/2 (negative rail relative to virtual ground)
```

The TLE2426 is a dedicated rail-splitter IC that creates a low-impedance midpoint from any supply. It's limited in current (20 mA), so it's suitable for small-signal op-amp circuits but not power amplifiers.

For power amplifiers, always use a real dual-rail supply with a center-tapped transformer. The current demands are too high for a virtual ground to handle.

---

## 6. Tube Amplifier B+ Supply

> **WARNING: This section deals with LETHAL voltages. A tube amplifier's B+ supply produces 250-600V DC. This voltage is stored in filter capacitors and remains present even after the amplifier is unplugged. Contact with these voltages can cause fatal cardiac arrest. Read Chapter 22 before building anything described here. No exceptions.**

### 6.1 The B+ Supply Chain

A tube amp's high-voltage supply follows the same basic chain as a low-voltage supply, but everything is scaled up:

```
Mains ──[Fuse]──[Switch]──► Power Transformer
                                │
                          HV Secondary (250-350V AC)
                                │
                          [Rectifier] (tube or silicon)
                                │
                          [Filter Cap] (first filter, 22-47uF, 450V+)
                                │
                          [Choke or R] (ripple filter)
                                │
                          [Filter Cap] (second filter, 22-47uF, 450V+)
                                │
                          B+ Rail (to output stage plates)
                                │
                          [R] (further RC filtering)
                                │
                          [Filter Cap] (preamp B+)
                                │
                          B+2 Rail (to preamp stages)
```

### 6.2 Capacitor Selection

**Voltage rating is critical.** If your B+ is 380V, you need capacitors rated for at least 450V. Using a 400V capacitor with a 380V B+ is asking for failure — the voltage can spike above 380V during no-load conditions or mains surges. Always leave at least 20% margin.

**Capacitance values** are smaller than in solid-state supplies because the current demands are lower (tubes draw milliamps, not amps). Typical values: 22-100 uF per filter section.

**Electrolytic capacitor safety:** At these voltages, a capacitor failure can be violent. Electrolytic caps that exceed their voltage rating can vent, leak, or explode. Always use caps rated for the voltage. Mount them so that if they do vent, the electrolyte doesn't contact other components. Some builders use axial-lead caps mounted with the vent facing away from the circuit board.

### 6.3 Slow-Start (Inrush Current Protection)

When a tube amp is first powered on, the filter capacitors are completely discharged — effectively short circuits. The rectifier must supply a very large current to charge them. This **inrush current** stresses the rectifier and can trip breakers.

**Solutions:**

**Tube rectifiers:** Inherently slow-start. They don't conduct until the cathode heats up (20-45 seconds), by which time the rest of the amp has warmed up too. This is one reason tube rectifiers are still popular in guitar amps — they're a natural soft-start mechanism.

**NTC thermistor:** A negative-temperature-coefficient resistor in series with the primary. Cold, it has high resistance (5-50 ohms), limiting inrush current. As it heats up from the current flow, its resistance drops to near zero. Simple and cheap, but it doesn't limit inrush on a quick power cycle (the thermistor is still hot).

**Relay-based soft start:** A resistor in series with the primary at power-on, bypassed by a relay after a delay (1-3 seconds). More reliable than NTC for repeated power cycling.

### 6.4 Bleeder Resistors — Passive Safety

> **This is a safety feature, not optional. Every tube amp must have bleeder resistors.**

A **bleeder resistor** is a high-value resistor connected across each filter capacitor. Its purpose: to discharge the capacitors when the amp is turned off.

Without bleeder resistors, the filter capacitors in a tube amp can hold lethal voltage **for hours or even days** after the amp is unplugged. The only discharge path is the leakage current of the capacitors themselves, which can be incredibly low.

**Sizing:**

```
R_bleeder = V² / P_discharge
```

Where P_discharge is the power you're willing to waste (1-5W is typical). For a 400V supply with 5W bleeder:

```
R_bleeder = 400² / 5 = 32k ohms
```

Use a resistor rated for at least 2× the calculated power (so 10W for a 5W calculation). The discharge time constant is:

```
τ = R × C
```

For 32k and 47uF: τ = 1.5 seconds. After 5τ (7.5 seconds), the voltage has dropped to less than 1% of its initial value.

**Even with bleeder resistors, always verify that capacitors are discharged before working inside the amp.** Bleeder resistors can fail open. Measure with a multimeter. See Chapter 22 for the complete safety procedure.

### 6.5 Voltage Doubler Circuits

Some tube amp designs use a **voltage doubler** to achieve higher B+ from a lower-voltage transformer secondary:

```
     AC ──[D1]──┬──[C1]──┐
          │     │         │
          │    GND        ├── +2V_peak (doubled output)
          │     │         │
     AC ──[D2]──┴──[C2]──┘
```

The Cockcroft-Walton voltage multiplier extends this principle to higher multiples. Voltage doublers are used when the required B+ exceeds what's conveniently available from standard transformers, or to reduce transformer cost. The trade-off: higher output impedance and more ripple compared to a standard full-wave rectifier at the same voltage.

---

## 7. Power Supply PCB Layout

The physical layout of a power supply is as important as the schematic. Poor layout causes hum, oscillation, and noise that no amount of filtering can fix.

### 7.1 Keep High-Current Loops Small

Current flowing in a loop creates a magnetic field proportional to the loop area. This field can induce hum in nearby signal wiring. The cure: minimize the area of high-current loops.

**The rectifier loop:** The path from the transformer secondary, through the rectifier diodes, through the filter capacitor, and back to the transformer should be as physically small as possible. Place the rectifier diodes right next to the filter cap, and use short, thick wires (or wide PCB traces) for the connections.

**The output loop:** The path from the filter capacitor, through the amplifier, through the speaker, and back should be as tight as possible. Place bypass capacitors right at the amplifier's supply pins.

### 7.2 Input/Output Separation

Keep the mains input wiring and transformer as far from the signal input as possible. In a chassis, the standard layout is:

```
[Mains inlet + fuse] ─── [Transformer] ─── [Rectifier/Caps] ─── [Amp circuit] ─── [Output jacks]
     LEFT side                                                                    RIGHT side
```

The signal input should enter the chassis at the opposite end from the mains transformer. This maximizes the distance between the largest source of magnetic hum (the transformer) and the most sensitive point in the circuit (the signal input).

### 7.3 Bypass Capacitors

Every power rail should have local bypass capacitors at the point of use:

- **100 nF ceramic:** Right at each IC's power pins. Handles high-frequency decoupling. Use X7R or C0G dielectric.
- **10-100 uF electrolytic or tantalum:** Near each IC or stage. Handles mid-frequency decoupling and local energy storage.
- **Bulk filter caps:** At the supply board. Handles low-frequency ripple and provides the main energy reservoir.

The combination of different capacitor values at different distances creates a distributed filtering network that's effective across a wide frequency range.

---

## 8. Grounding Topologies

Grounding is where good engineers are separated from great engineers. In audio, improper grounding is the number-one cause of hum, buzz, and noise. The goal: ensure that currents flowing through ground wires don't create voltage drops that add to the audio signal.

### 8.1 The Problem

Ground is not zero volts. It's a wire (or trace) with resistance. Current flowing through that resistance creates a voltage drop (Ohm's law: V = I × R). If a high-current ground return (from the power supply or output stage) shares a wire with a low-level signal ground (from the input stage), the voltage drop from the high current appears as noise in the signal path.

```
     High-current return (amps)
          │
          ├── Shared ground wire ──►  This section has V = I × R noise
          │                            that the input stage sees as signal
     Low-level signal return (microamps)
```

### 8.2 Star Ground

In a **star ground** topology, all ground returns are routed independently to a single common point (the "star point"). No ground wire carries current for more than one circuit.

```
                      ┌── Input stage ground
                      │
     Star point ──────┼── Preamp stage ground
     (one bolt         │
      on chassis)     ├── Power amp ground
                      │
                      └── Power supply ground ◄── This carries the heavy current
```

The star point is typically a bolt on the chassis near the power supply filter capacitors. Each circuit's ground wire runs independently to this point.

**Advantages:**
- Prevents ground loops between stages
- High-current returns don't pollute signal grounds
- Conceptually simple

**Disadvantages:**
- Lots of wires running to one point — can be messy
- Works best for point-to-point wired or through-hole PCB designs
- Not practical for complex multi-board systems

**Star ground is the standard for tube amps and discrete solid-state amps.** The Champ-style build in Chapter 24 uses a star ground.

### 8.3 Ground Plane

In a **ground plane** topology, the entire bottom layer (or a large area) of a PCB is a continuous copper pour connected to ground. All ground connections go to this plane.

**Advantages:**
- Very low impedance ground return at all frequencies
- Excellent for RF bypass and high-frequency decoupling
- Simpler PCB routing (just via to ground anywhere)
- Provides shielding for signal traces

**Disadvantages:**
- High-current returns and signal returns share the same plane
- Ground current distribution can create localized voltage gradients
- Requires careful placement of high-current components to avoid routing return currents under sensitive areas

**Ground planes are standard for PCB-based designs,** especially those using SMD ICs. The LM3886 and TPA3255 evaluation boards use ground planes.

### 8.4 Hybrid Approach

The best practical approach for most audio builds combines elements of both:

1. Use a ground plane on the amplifier PCB.
2. Separate high-current power ground from signal ground at the PCB level (split ground plane or strategic routing).
3. Connect the PCB ground to the chassis at one point (star connection to chassis ground).
4. Connect the chassis ground to the mains earth.

This gives you the low impedance of a ground plane for on-board connections and the loop-prevention benefits of a star ground for board-to-board and board-to-chassis connections.

### 8.5 Common Grounding Mistakes

**Ground loops between chassis and signal ground:** If the signal ground connects to the chassis at multiple points, mains-frequency currents flowing through the chassis create hum. Solution: connect signal ground to chassis at one point only.

**Long ground wires:** A ground wire is an inductor. A long ground wire has significant impedance at audio frequencies, creating voltage drops. Keep ground wires short and thick.

**Daisy-chained grounds:** Don't run a single ground wire from input jack to preamp to power amp to speaker jack. Each stage's ground current adds to the wire, and the later stages' current creates voltage drops that the earlier stages see as noise. Use star or bus grounding instead.

> **What happens if... you get the grounding wrong?**
> You hear a 50/60 Hz hum (or 100/120 Hz buzz) that no amount of filtering, shielding, or component swapping will fix. It's maddening. The fix is almost always a grounding issue. Lift grounds one at a time. Try different star points. Check for unintended chassis contact. Grounding problems are detective work, and the answers are always logical — current is flowing somewhere it shouldn't, creating a voltage where it shouldn't.

---

## 9. Practical Power Supply Recipes

### 9.1 Supply for LM3886 Stereo Amp

```
Transformer: Toroidal, 2×18V or 2×22V, 160VA
Rectifier: 35A bridge rectifier (KBPC3510 or similar)
Filter caps: 2 × 10,000 uF / 50V per rail (4 total for dual mono)
Bypass: 100 nF ceramic at each LM3886 power pin
Fuse: 2A slow-blow on primary

Expected output: ±25 to ±31V DC (depending on transformer voltage)
```

### 9.2 Supply for TPA3255 Class D Amp

```
Power supply: 48V / 10A SMPS (off-the-shelf, e.g., Mean Well LRS-350-48)
Input filter: Pi filter (100 uF + ferrite bead + 100 uF) at amp board input
Bypass: 10 uF + 100 nF ceramic at each TPA3255 power pin

Expected output: 48V DC, regulated
```

### 9.3 Supply for 5F1-Style Tube Guitar Amp

```
Transformer: Fender 5F1 replacement (ClassicTone 40-18010 or equivalent)
  - Primary: 120V (or 240V for international)
  - HV secondary: 325-0-325V (center-tapped)
  - Heater: 6.3V @ 2A
  - Rectifier heater: 5V @ 2A (if using 5Y3 tube rectifier)
Rectifier: 5Y3GT tube (or silicon diodes with dropping resistor for sag)
Filter caps: 20uF/450V, 20uF/450V (original spec — can upgrade to 22-47uF)
Bleeder: 220k / 5W across each HV cap
Choke or resistor: 5H choke (or 1k-2k/5W resistor for budget)

Expected B+: ~360V (no load), ~310-340V (loaded, with tube rectifier)
Heater: 6.3V AC (or DC with rectifier for lower noise)
```

---

## 10. Power Supply Measurements and Verification

Once you've built a power supply, you need to verify it works correctly before connecting it to your amplifier. Here's a systematic measurement procedure.

### 10.1 No-Load Measurements

With the supply powered on but *nothing* connected to the DC outputs:

1. **DC output voltage:** Measure with a multimeter. For a 2×18V transformer, expect ±25.5V DC (18 × √2) minus diode drops (~1.4V) = ±24.1V. Under no load, the voltage is higher than the nominal spec because there's no voltage drop across transformer impedance.

2. **Voltage symmetry (dual rail):** Both rails should be within 1V of each other. If they're not, check that both secondary windings are the same voltage and that both rectifier paths are working.

3. **Ripple (oscilloscope):** Set your scope to AC coupling and look at the DC output. You should see very low ripple (< 50 mV) under no load. The ripple frequency should be 100/120 Hz (double mains frequency for full-wave rectification). If you see 50/60 Hz ripple, you have a half-wave rectifier problem — one diode may be bad.

### 10.2 Load Testing

Connect a power resistor load that draws approximately the expected current:

For a ±28V supply intended for an LM3886 (expected load ~2A per rail):
- Use an 8 ohm / 100W power resistor per rail (draws about 3.5A at 28V — slightly more than typical, which is a good stress test)
- Or use the amplifier itself as the load, but only after the no-load tests pass

**Measurements under load:**
1. **DC voltage:** Will be lower than no-load by 1-5V depending on transformer regulation.
2. **Ripple:** Will increase proportionally with load current. At 2A load with 10,000 uF per rail: Vripple ≈ 2 / (2 × 120 × 0.01) = 0.83V. This is fine — the LM3886's PSRR will reject it completely.
3. **Temperature:** Let the transformer run under load for 15-30 minutes. It should be warm but not too hot to touch (below 60C surface temperature). If it's excessively hot, it's undersized for the load.

### 10.3 Common Power Supply Problems

**Output voltage too low:**
- Transformer VA rating too low (voltage sags excessively under load)
- Excessive voltage drop across rectifier (check diodes)
- Filter caps have degraded (ESR increased)

**Output voltage too high:**
- Mains voltage higher than expected (especially common in areas with 240V nominal that actually delivers 250V)
- Load is drawing less current than designed for (the unregulated voltage rises when load decreases)

**Excessive ripple:**
- Filter capacitor(s) have failed or are wrong value
- One or more rectifier diodes have failed
- Capacitor connected with wrong polarity (won't filter properly)

**Hum from the transformer:**
- Mechanical (magnetostriction): normal for EI-core transformers, usually worse at the exact mains frequency. Tighten mounting bolts. Use rubber washers for isolation.
- DC offset on the mains (common with dimmer switches on the same circuit): A small DC component magnetizes the core asymmetrically. Can be fixed with a DC blocker circuit.

---

## 11. Advanced Topics: Power Supply for DSP and Digital Circuits

If your amplifier project includes digital components (DSP crossovers, digital inputs from Chapter 29 onward), you'll need a clean digital supply in addition to the analog power rails.

### 11.1 Separate Digital and Analog Supplies

Digital circuits generate high-frequency switching noise that can contaminate analog circuits. The golden rule: **never power digital and analog circuits from the same regulator.** Use separate regulators (or separate secondary windings) for digital and analog sections.

### 11.2 Digital Supply Requirements

Most DSP chips and microcontrollers need:
- 3.3V or 5V DC, regulated
- Clean high-frequency bypassing (100 nF ceramic + 10 uF per IC)
- Low-dropout (LDO) regulators for best noise performance (e.g., AMS1117-3.3 for 3.3V, L7805 for 5V)

### 11.3 Clock Jitter and Power Supply Noise

In digital audio, power supply noise can modulate the clock signal, causing **jitter** — timing variation that degrades audio quality. This is particularly important for DACs and ADCs. If your project includes digital audio conversion, use a separate, well-filtered supply for the clock oscillator.

This topic intersects with [Ch 29: Build: Active Crossover and Room Correction](../part-4-digital-audio/29-build-active-crossover-room-correction.md), where we'll explore digital audio power supply requirements in more detail.

---

## 12. Chapter Summary

Power supply design is where the rubber meets the road:

1. **Linear supplies** (transformer + rectifier + filter) are the standard for quality audio. Heavy, hot, but clean and reliable.
2. **Filter capacitor sizing** determines ripple. More capacitance = less ripple, but watch for inrush current.
3. **Voltage regulation** (LM317/LM337) is essential for preamp supplies and op-amp circuits. Power amp rails often run unregulated.
4. **SMPS** are lightweight and efficient — great for Class D and PA, but keep them away from sensitive analog circuits.
5. **Tube amp B+ supplies** produce lethal voltages. **Bleeder resistors are mandatory.** Always verify caps are discharged.
6. **Grounding** is the most common source of audio noise. Star ground for point-to-point builds, ground planes for PCBs, hybrid for complex systems.
7. **Layout matters.** Keep high-current loops small, separate input from output, and bypass locally.

The power supply connects directly to the mains — which means we need to talk about safety. The next chapter is the most important one in this entire section.

---

*Next: [Chapter 22: Safety: Working with Mains and High Voltage](22-safety-mains-high-voltage.md)*
*Previous: [Chapter 20: Tube Amplifier Theory](20-tube-amplifier-theory.md)*
