<!--
  CHAPTER: 19
  TITLE: Solid-State Amplifier Design
  PART: 3 — Amplifiers
  PREREQS: Chapter 18
  KEY_TOPICS: BJT output stages, MOSFET output stages, Class AB biasing, Vbe multiplier, thermal runaway, protection circuits, thermal management, LM3886, TPA3255, power calculations
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 19: Solid-State Amplifier Design

> **Part 3 — Amplifiers** | Prerequisites: Chapter 18 | Difficulty: Inter→Adv

Now we turn theory into transistors. Chapter 18 gave you the vocabulary — gain, distortion, feedback, damping factor, amplifier classes. This chapter puts those concepts to work in real circuits. We'll start with the output stage (because that's where the power lives), work through biasing and thermal management, add protection circuits that keep your amp and speakers alive, and then look at two integrated solutions that let you build excellent amplifiers without designing from scratch.

The progression here is deliberate. We start with discrete transistor stages so you understand what's happening inside those IC packages. Then we move to the LM3886 and TPA3255 — two chips that pack decades of analog design into a single component. By the time you build the amps in Chapter 23, you'll understand *why* the circuits work, not just *that* they work.

Fair warning: this chapter gets into the weeds. We'll be reading datasheets, calculating thermal resistance, and debating the merits of Darlington versus Sziklai pairs. If that sounds like your idea of a good time, let's go.

### In This Chapter
- BJT output stage topologies: complementary symmetry, Darlington, Sziklai
- MOSFET output stages: advantages and gotchas
- Class AB biasing and the Vbe multiplier
- Thermal runaway: what it is and how to prevent it
- Protection circuits: DC offset, overcurrent, speaker relay
- Thermal management and heat sink calculations
- LM3886 — the audiophile's favorite chip amp
- TPA3255 — Class D powerhouse for high-power applications
- Power output calculations

### Related Chapters
- [Ch 18: How Amplifiers Work](18-how-amplifiers-work.md) — the theory behind everything here
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) — transistor fundamentals refresher
- [Ch 3: Simulation and Analysis Tools](../part-0-electronics/03-simulation-analysis-tools.md) — simulate these circuits before building
- [Ch 21: Power Supply Design](21-power-supply-design.md) — what feeds the amplifier
- [Ch 23: Build: Chip Amp and Class D Amp](23-build-chip-amp-class-d.md) — building the amps we design here
- [Ch 31: PCB Design for Audio](../part-5-pcb-professional/31-pcb-design-for-audio.md) — layout considerations for amp circuits

---

## 1. BJT Output Stages

The **Bipolar Junction Transistor (BJT)** has been the backbone of audio power amplification since the 1960s. Its ability to handle high currents, its well-understood behavior, and the availability of complementary NPN/PNP pairs make it ideal for output stages.

### 1.1 Complementary Symmetry

The simplest Class AB output stage uses a complementary pair — one NPN and one PNP power transistor:

```
         +Vcc
          │
          │
     ┌────┤ NPN (e.g., MJL3281A)
     │    │ collector to +Vcc
     │    │ emitter to output
     │    │
Input──┤    ├──── Output to Speaker
     │    │
     │    │ PNP (e.g., MJL1302A)
     │    └────┤ collector to -Vcc
     │         │ emitter to output
     │         │
          │
         -Vcc
```

The NPN transistor sources current to the speaker (positive half-cycle), and the PNP sinks current (negative half-cycle). In Class AB, both are biased to have a small quiescent current flowing at idle.

The key parameters for selecting output transistors:
- **Vceo (collector-emitter voltage):** Must exceed your total supply voltage with margin. For ±35V rails, choose transistors rated for at least 100V.
- **Ic (continuous collector current):** Must handle the peak current into your lowest expected load impedance. For 100W into 4 ohms: Ipeak = sqrt(2P/R) = sqrt(200/4) = 7.07A. Choose transistors rated for at least 10A.
- **hFE (current gain):** Higher is better for a simple complementary stage. Typical power transistors: 20-80. This is often not enough for the driver stage to control directly, which leads us to...
- **fT (transition frequency):** Higher = faster switching. Audio power transistors typically 2-30 MHz.

### 1.2 Darlington Pair

A **Darlington pair** is two transistors in cascade where the emitter of the first drives the base of the second:

```
             Base ──┤ Q1 (driver)
                    │
                    └──► Emitter of Q1
                         │
                         ├──┤ Q2 (output)
                         │  │
                         │  └──► Emitter of Q2 ──── Output
                         │
                    Collector of both ──── Rail
```

The total current gain is hFE1 x hFE2. If each transistor has a gain of 50, the pair has a gain of 2500. This means the *driver* stage needs to provide very little current.

**Advantages:**
- Very high current gain — easy to drive
- Simple circuit
- Many Darlington pairs available in single packages (TIP141/TIP146)

**Disadvantages:**
- **Higher Vce(sat):** The pair drops about 1.2-2V at saturation (two Vbe drops), wasting voltage headroom and generating more heat in the output devices.
- **Slower switching:** The base charge of Q2 must be removed through Q1, making turn-off sluggish. This can degrade high-frequency performance and increase crossover distortion.
- **Higher distortion** at the crossover region compared to other topologies.

### 1.3 Sziklai Pair (Complementary Feedback Pair)

The **Sziklai pair** (pronounced "SHIK-lie") is a clever alternative to the Darlington:

```
             Base ──┤ Q1 (driver, e.g., NPN)
                    │
                    └──► Collector of Q1
                         │
                    ┌────┤ Q2 (output, e.g., PNP)
                    │    │
         Output ◄───┤    └──► Emitter of Q2 ──── Rail
                    │
                    Base of Q2 ◄── Emitter of Q1
```

Q1 is an NPN driving a PNP (or vice versa). The combination behaves like a super-NPN with high gain, but with a key advantage: only one Vbe drop between base and emitter of the compound device.

**Advantages:**
- **Lower Vce(sat)** than Darlington — only one Vbe drop instead of two. More output voltage swing, less wasted power.
- **Faster turn-off** than Darlington — Q1 actively pulls charge from Q2's base.
- **Lower crossover distortion** — the feedback inherent in the topology linearizes the transfer function.

**Disadvantages:**
- Can be less stable — the internal feedback can cause oscillation if not carefully designed.
- Requires a speed-up capacitor across Q2's base-emitter in some implementations.

The Sziklai pair is used in many high-quality amplifier designs and is arguably the best discrete output topology for audio. If you're designing a discrete amp, start here.

> **What happens if... you mix Darlington on one side and Sziklai on the other?**
> Some classic amplifier designs do exactly this — Darlington NPN on the positive side, Sziklai PNP on the negative side (or vice versa) to compensate for the fact that PNP power transistors are generally slower and have lower gain than their NPN counterparts. The asymmetry can be tuned to produce a more symmetrical overall transfer function. The Hitachi MOSFET amps famously used asymmetric topologies to good effect.

---

## 2. MOSFET Output Stages

**MOSFETs (Metal-Oxide-Semiconductor Field-Effect Transistors)** offer a different set of trade-offs for output stages.

### 2.1 Advantages of MOSFETs

**No thermal runaway.** This is the big one. BJTs have a positive temperature coefficient for current — as they heat up, they conduct *more* current, which makes them hotter, which makes them conduct more... a potentially destructive positive feedback loop (see Section 4). MOSFETs have a negative temperature coefficient above a certain current level — as they heat up, they conduct *less*. They're inherently self-limiting.

**Simpler biasing.** Because of the thermal stability, you can use simpler bias circuits without the paranoid thermal tracking that BJT amps require.

**Very fast switching.** MOSFETs switch faster than BJTs, making them ideal for Class D and for linear amps where slew rate matters.

**Higher input impedance.** The gate draws essentially zero DC current (it's a capacitor), making the driver stage design easier.

### 2.2 Disadvantages of MOSFETs

**Higher on-resistance (Rds_on).** A MOSFET in its linear operating region drops more voltage than a saturated BJT. This means more power dissipated in the output device for a given current.

**Gate capacitance.** While MOSFETs draw no DC current, their gates are capacitive loads. Charging and discharging these capacitances at audio frequencies requires a driver with good transient current capability. Gate capacitance can be 1000-5000 pF for power MOSFETs.

**Transfer function linearity.** The Vgs-to-Id transfer function of a MOSFET is square-law (Id proportional to Vgs²), while a BJT is exponential. The MOSFET's transfer function is arguably *less* linear for small-signal Class A/AB operation, though this depends on the operating point.

**Lateral vs. vertical MOSFETs.** Lateral MOSFETs (like the legendary Hitachi 2SK135/2SJ50) have better linearity and a more graceful failure mode but are largely obsolete and expensive. Vertical MOSFETs (like IRFP240/IRFP9240) are cheap and available but have a nasty nonlinearity in their transfer function that can increase distortion in Class AB operation.

### 2.3 When to Choose MOSFETs vs BJTs

**Choose MOSFETs when:**
- Building Class D (they're essentially the only choice for switching amps)
- Thermal stability is paramount (outdoor PA, harsh environments)
- You want simpler biasing
- You're designing for high slew rate

**Choose BJTs when:**
- Designing a classic Class AB amp with the lowest possible distortion
- You need every last volt of supply rail utilization (lower Vce(sat))
- Using a proven topology that was designed around BJTs

In practice, both can produce excellent results. The BJT vs MOSFET "sound quality" debate has been raging since the 1970s and has no definitive answer — good engineering matters more than device choice.

### 2.4 MOSFET Biasing Differences

Biasing a MOSFET output stage differs from BJT biasing in important ways:

**Gate voltage:** MOSFETs need 2-5V of Vgs to conduct (the threshold voltage), compared to ~0.6V Vbe for a BJT. The bias spreader must produce more voltage for a MOSFET output stage.

**No base current:** A BJT requires continuous base current proportional to the collector current (Ic/hFE). A MOSFET gate draws only capacitive charging current during signal transitions. This means the driver stage doesn't need to supply DC current — but it must be capable of supplying transient current to charge the gate capacitance quickly.

**Temperature coefficient:** Above a certain current level (typically 100-500 mA depending on the device), MOSFETs have a positive temperature coefficient of Rds_on — as they heat up, resistance increases and current decreases. This is inherently stable. Below that current, the coefficient is negative (like a BJT), so at very low quiescent currents, MOSFETs can still exhibit thermal issues. Set quiescent current above the crossover point.

**Matched pairs:** MOSFET matching is less critical than BJT matching because the temperature coefficient provides self-balancing. However, for best performance, match the Vgs(th) (threshold voltage) of the N-channel and P-channel devices to within 0.5V.

> **What happens if... you use BJT biasing techniques for a MOSFET output stage?**
> The Vbe multiplier used for BJT biasing produces ~1.2-1.4V for a complementary pair. MOSFETs need 4-10V of total gate-to-gate bias. If you use the same Vbe multiplier circuit, the MOSFETs won't turn on and you'll get massive crossover distortion. You need to either: (1) use a modified Vbe multiplier with a higher multiplication ratio, (2) stack multiple Vbe multipliers, or (3) use a dedicated MOSFET bias circuit with a pot and a source follower.

---

## 3. Biasing for Class AB

Getting the bias right is the most critical part of Class AB amplifier design. Too little bias current and you get crossover distortion. Too much and you waste power, generate excess heat, and risk thermal runaway (in BJT designs).

### 3.1 The Vbe Multiplier (Bias Spreader)

The **Vbe multiplier** is the standard biasing circuit for Class AB output stages. It's a transistor configured as an adjustable voltage source:

```
         From VAS (voltage amp stage)
              │
              │
         ┌────┤ Collector
         │    │
    R1   │    Q_bias
         │    │
         ├────┤ Base
         │    │
    R2   │    │
         │    └──── Emitter
         │         │
         └─────────┘
              │
         To output stage
```

The voltage across the Vbe multiplier is:

```
Vbias = Vbe × (1 + R1/R2)
```

For a complementary BJT output stage, you need about 2 × Vbe (about 1.2-1.4V) to bias both output transistors into conduction. Set R1/R2 to achieve this, and add a trimpot for fine adjustment.

**The adjustment procedure:**
1. Set the trimpot to minimum bias (minimum R1 or maximum R2, depending on topology).
2. Power up the amplifier with no input signal.
3. Measure the voltage across one of the emitter resistors (Re, typically 0.22-0.47 ohms).
4. Calculate quiescent current: Iq = Vre / Re.
5. Adjust the trimpot until Iq reaches the design target (typically 20-100 mA for a power amp).
6. Let the amp warm up for 15-30 minutes and readjust if needed.

### 3.2 Thermal Tracking

Here's the critical detail: the Vbe of a BJT *decreases* with temperature — about -2.2 mV per degree Celsius. As the output transistors heat up, their Vbe drops, meaning they need less base voltage to conduct. If the bias voltage stays constant, the quiescent current increases, generating more heat, causing more Vbe drop, causing more current...

The solution: **mount the bias transistor (Q_bias) on the heat sink** next to the output transistors. As the heat sink warms up, Q_bias warms up too, its Vbe drops, the bias voltage drops, and the quiescent current self-corrects. This thermal coupling is essential for stable operation.

Some designs use a thermistor in the bias network instead of or in addition to the Vbe multiplier. Others use a dedicated thermal sensor IC. The principle is always the same: the bias must track temperature.

### 3.3 How Much Quiescent Current?

This is a design judgment call:

- **10-20 mA:** Minimum for Class AB operation. Some crossover distortion will remain, but it'll be suppressed by the global feedback loop. Fine for PA amplifiers.
- **50-100 mA:** A good compromise for hi-fi. Essentially eliminates crossover distortion below moderate power levels.
- **200+ mA:** Entering Class A territory for the first few watts of output. Sonically excellent, but runs hot.

For most builds, 40-80 mA is the sweet spot. Measure it, don't guess it.

---

## 4. Thermal Runaway in BJTs

**Thermal runaway** is the failure mode that keeps BJT amplifier designers up at night. It's worth understanding thoroughly because it can destroy your output transistors (and speaker) in seconds.

### 4.1 The Mechanism

1. BJT conducts current → power dissipated as heat → junction temperature rises.
2. Higher junction temperature → lower Vbe for the same current → more current flows.
3. More current → more heat → higher temperature → even more current.
4. Positive feedback loop → current increases until the transistor exceeds its SOA (Safe Operating Area) and fails.

This can happen in microseconds under fault conditions (shorted output) or over minutes with inadequate thermal design.

### 4.2 Prevention Methods

**Emitter resistors (Re):** Small resistors (0.1-0.47 ohms) in series with each output transistor's emitter provide local negative feedback. If current increases, the voltage across Re increases, reducing the effective Vbe and limiting current. These resistors also help balance current sharing between paralleled output devices.

**Thermal tracking** (as described in Section 3.2): The bias voltage tracks the output device temperature, reducing bias as temperature increases.

**SOA protection:** Active circuits that monitor the simultaneous voltage across and current through the output devices and reduce drive if the operating point approaches the SOA boundary. This is particularly important for reactive (speaker) loads where voltage and current can be out of phase, putting the transistor in a high-voltage, high-current state simultaneously.

**Adequate heat sinking:** The simplest defense. If the junction temperature never gets too high, thermal runaway can't start. See Section 6.

> **What happens if... thermal runaway occurs?**
> The output transistor fails short-circuit. The full supply rail voltage (±35V or whatever you're using) is now applied directly to your speaker. The speaker voice coil, which is designed for perhaps 20V RMS, sees a DC voltage it cannot handle. The voice coil overheats, the adhesive melts, and the coil either opens (best case) or welds itself to the magnet gap (worst case). You've destroyed both the amp and the speaker. This is why DC protection and speaker relays exist — see Section 5.

---

## 5. Protection Circuits

A power amplifier without protection circuits is like a car without seatbelts. It might work fine until something goes wrong, and then the consequences are catastrophic.

### 5.1 DC Offset Protection

If the amplifier fails or develops a fault, it may output a DC voltage instead of (or in addition to) the audio signal. DC applied to a speaker is destructive — the voice coil can't dissipate the heat because it's not moving (no cooling airflow from cone motion).

A DC detection circuit monitors the amplifier output. If DC exceeds a threshold (typically ±1-2V) for more than a brief period (typically 100-500 ms, to avoid false triggering on bass transients), the circuit disconnects the speaker via a relay.

A simple implementation uses an RC low-pass filter on the output to extract any DC component, followed by a comparator or window detector:

```
Amp Output ──[100k]──┬──[10uF]── GND    (RC filter, τ = 1 second)
                      │
                      ├── Comparator (+) ──► Relay Driver
                      │
               ±1.5V thresholds
```

### 5.2 Overcurrent / Short-Circuit Protection

If someone shorts the speaker terminals (or connects a 2-ohm load to an amp designed for 8 ohms), the output transistors will attempt to deliver unlimited current. They will fail.

**Simple current limiting** uses the emitter resistors as current sensors. When the voltage across Re exceeds about 0.6V (one Vbe drop), a small transistor turns on and diverts base drive from the output device:

```
         +Vcc
          │
     Base Drive ──┤ Q_output (NPN)
                   │
                   Re (0.22Ω)
                   │
                   ├── Output
                   │
              ┌────┤ Q_limit (NPN)
              │    │
         Base ◄─── Across Re (when V_Re > 0.6V, Q_limit turns on
         Drive      and steals base current from Q_output)
```

Current limit = 0.6V / Re = 0.6 / 0.22 = 2.7A per device.

**SOA (Safe Operating Area) protection** is more sophisticated. It accounts for the fact that the transistor can handle more current at low Vce than at high Vce. The protection curve follows the SOA boundary of the output transistor rather than a flat current limit. This gives better performance with reactive loads.

### 5.3 Speaker Protection Relay

The **speaker relay** is the last line of defense. It's a relay in series with the speaker output that:

1. **Delays turn-on** by 2-5 seconds after power-up, allowing all circuits to stabilize and any turn-on thump to pass.
2. **Immediately disconnects** on any fault condition (DC offset, overcurrent, overtemperature).
3. **Disconnects on power-down** before the supply rails sag asymmetrically, which could cause a DC thump.

The relay should be rated for the full output current and voltage of the amplifier. Use a relay with contacts rated for at least 10A at the supply voltage. Gold-plated contacts are a nice touch for minimal contact resistance.

A basic protection circuit with turn-on delay and DC detection can be built around a single op-amp or comparator, a timing capacitor, and a relay driver transistor. Several proven designs are available — the Rod Elliott (ESP) protection circuit is an excellent starting point.

### 5.4 Thermal Protection

A thermistor or thermal switch mounted on the heat sink triggers a warning (LED) or shutdown if the heat sink temperature exceeds a safe limit (typically 70-80C). This protects against blocked ventilation, overdriving into low-impedance loads, or operation in hot environments.

The LM3886 (Section 7) has thermal protection built in — one less thing to worry about.

---

## 6. Thermal Management

Heat is the enemy of reliability. Every watt of power that doesn't reach the speaker becomes heat in the amplifier. Getting that heat out of the transistor junction and into the room is a critical engineering task.

### 6.1 The Thermal Resistance Chain

Heat flows from the transistor junction to the ambient air through a chain of thermal resistances:

```
Junction ──[Rθ_jc]──► Case ──[Rθ_cs]──► Heat Sink ──[Rθ_sa]──► Ambient Air

Rθ_jc = junction-to-case (from datasheet)
Rθ_cs = case-to-sink (depends on mounting: thermal paste, insulator)
Rθ_sa = sink-to-ambient (from heat sink datasheet or calculation)
```

Total thermal resistance:
```
Rθ_ja = Rθ_jc + Rθ_cs + Rθ_sa
```

The junction temperature:
```
Tj = Ta + (Pd × Rθ_ja)
```

Where Ta is ambient temperature and Pd is power dissipated in the device.

### 6.2 Worked Example

Let's size a heat sink for an LM3886 delivering 50W into 8 ohms from ±28V rails.

**Power dissipation calculation:**
At full power into 8 ohms, the LM3886 dissipates approximately:

```
Pd = (Vcc × Ipeak / π) - (Pout / 2)
```

For Class AB with ±28V rails and 50W output:
- Ipeak = sqrt(2 × 50 / 8) = 3.54A
- Pd ≈ (28 × 3.54 / π) - (50 / 2) = 31.5 - 25 = 6.5W per device

But that's at full power. The *worst-case* dissipation occurs at a lower power level, roughly when Vout_peak = Vcc × 2/π:

```
Pd_max = Vcc² / (π² × Rload) = 28² / (9.87 × 8) = 9.9W
```

**Thermal resistance budget:**
- Tj_max = 150C (from LM3886 datasheet)
- Ta = 40C (assume warm room / enclosed space)
- Rθ_jc = 1.0 C/W (from LM3886 datasheet)
- Rθ_cs = 0.4 C/W (with thermal paste and mica insulator)

Required Rθ_sa:
```
Rθ_sa = (Tj_max - Ta) / Pd_max - Rθ_jc - Rθ_cs
       = (150 - 40) / 9.9 - 1.0 - 0.4
       = 11.1 - 1.4
       = 9.7 C/W
```

A heat sink rated at 9.7 C/W or lower will keep the junction below 150C. In practice, aim for 50-70% of the maximum — a 5-6 C/W heat sink gives comfortable margin.

### 6.3 Thermal Paste Application

**Do:**
- Clean both surfaces with isopropyl alcohol first.
- Apply a thin, even layer — just enough to fill microscopic air gaps. Think "spread, not blob."
- Use silicone-based thermal compound (Arctic MX-4 or similar). Thermal conductivity: 4-8 W/mK.

**Don't:**
- Use too much paste. A thick layer is *worse* than a thin layer because the paste itself is a poorer conductor than metal-to-metal contact.
- Forget the electrical insulator if the device case is connected to a circuit node (the LM3886 tab is connected to -Vcc). A mica or Kapton insulator between the device and heat sink adds Rθ_cs but prevents short circuits.
- Reuse old paste. Clean and reapply if you ever remove the device.

### 6.4 Heat Sink Selection

For enclosed chassis builds, use a heat sink with:
- Sufficient thermal resistance (calculated above)
- Fins oriented vertically for convective airflow
- Enough surface area for your power dissipation
- Mounting holes that match your device package

For high-power builds, consider using the chassis itself as the heat sink. Aluminum chassis with 2-3mm wall thickness can work well for moderate power (< 50W dissipation).

Forced-air cooling (a fan) can reduce the effective Rθ_sa by 2-3x, but adds noise. In a hi-fi amp, you usually want passive cooling. In a PA amp, fans are standard.

---

## 7. The LM3886 — The Audiophile's Chip Amp

The **LM3886** from Texas Instruments (originally National Semiconductor) is one of the most beloved audio ICs ever made. It's a complete 68W power amplifier in a single TO-220 package (11 pins).

### 7.1 Why the LM3886?

- **68W into 4 ohms, 38W into 8 ohms** (from ±28V supply)
- **0.03% THD** at typical listening levels
- **Integrated protection:** overcurrent, overvoltage, thermal shutdown, speaker mute
- **High slew rate:** 19 V/us
- **Wide bandwidth:** 2 MHz (open loop)
- **Low noise:** 2 uV input-referred
- **Remarkably musical sound** — the "gainclone" community swears by it

The term **"gainclone"** comes from the audiophile world. In 1999, a DIYer named Peter Daniel built a minimal amplifier around the LM3886 — non-inverting configuration, almost no external components — and declared it sounded as good as $5000 amplifiers. The resulting "47 Lab Gaincard clone" movement spawned thousands of builds and furious debate. Whatever you think of the audiophile hyperbole, the LM3886 genuinely sounds excellent.

### 7.2 Typical Application Circuit

The standard non-inverting "gainclone" configuration:

```
                         +Vcc (e.g., +28V)
                          │
                        [100nF] bypass cap
                          │
               ┌──────────┤
               │          │
Input ──[1uF]──┤(+)  LM3886  (OUT)──┬──[10uH]──┬── Speaker
               │          │         │           │
               │   (-) ───┤         [R3=10R]  [100nF] Zobel
               │    │     │         │           │
               │   [R2]   │         GND       [Speaker]
               │    │     │                     │
               │   [R1]   │                    GND
               │    │     │
               │   GND    │
               │          │
               │        [100nF] bypass cap
               │          │
               │         -Vcc (e.g., -28V)
```

**Component values:**
- **Gain:** Av = 1 + R2/R1. For Av = 21 (26.4 dB): R1 = 1k, R2 = 20k.
- **Input capacitor:** 1 uF film capacitor. Sets the low-frequency -3 dB point with the input impedance: f = 1/(2π × 1µF × 47k) = 3.4 Hz. Plenty of bass extension.
- **Zobel network:** 10 ohm + 100 nF in series, across the output. Stabilizes the amp when driving reactive (speaker) loads by presenting a resistive load at high frequencies.
- **Output inductor:** 10 uH (wound from 10 turns of 18 AWG on a 1/2" air-core form), in series with the output. Isolates the amplifier from the speaker cable's capacitance. A small resistor (10 ohm) in parallel with the inductor damps any resonance.
- **Bypass capacitors:** 100 nF ceramic right at the power pins, plus 1000-4700 uF electrolytics on each rail.

### 7.3 Power Output Calculations

The maximum output voltage swing of the LM3886 is limited by the supply rails minus dropout voltage (about 2V each side):

```
Vpeak = Vcc - 2V = 28 - 2 = 26V
Vrms = Vpeak / sqrt(2) = 18.4V
```

Power into various loads:
```
P(8Ω) = Vrms² / R = 18.4² / 8 = 42W
P(4Ω) = 18.4² / 4 = 84W (but current-limited — see datasheet)
```

The datasheet thermal limits and SOA will determine the actual achievable power for your heat sink and rail voltages. Always check the safe operating curves.

### 7.4 Reading the LM3886 Datasheet

The datasheet is your bible for this chip. Key sections to study:

- **Absolute Maximum Ratings** (p.3): Don't exceed ±42V supply. Don't exceed 11.5A output current.
- **Thermal Information** (p.4): Rθ_jc = 1 C/W. You need a heat sink.
- **Typical Application** (p.10): The reference circuit. Start here.
- **SPiKe Protection** (p.12): The chip's built-in SOA protection. It will shut down if pushed too hard.
- **Power Dissipation vs. Output Power** curves: Find your worst-case dissipation for heat sink sizing.

---

## 8. The TPA3255 — Class D Powerhouse

The **TPA3255** from Texas Instruments is a Class D amplifier chip that delivers astounding power from a simple design.

### 8.1 Why the TPA3255?

- **Up to 315W per channel** in bridge-tied load (BTL) configuration
- **Up to 600W in mono PBTL** (parallel bridge-tied load)
- **Efficiency: 92%** typical — almost no heat
- **THD+N: 0.005%** at moderate power — yes, really
- **Operates from a single 48V supply** — no dual rails needed
- **Minimal external components** — the modulator, gate drivers, and output stage are all internal

### 8.2 How the TPA3255 Works

The TPA3255 uses a closed-loop Class D architecture:

1. The audio input is compared against a triangle wave oscillator (switching frequency ~600 kHz).
2. The comparator output is a PWM signal whose duty cycle represents the audio.
3. The PWM signal drives the internal H-bridge output MOSFETs.
4. An external LC filter recovers the audio.
5. A feedback network from the output back to the input corrects for errors.

The closed-loop feedback is what makes modern Class D sound so good. It corrects for power supply variations, output filter nonlinearity, and other errors that plagued early open-loop Class D designs.

### 8.3 Typical Application

For the TPA3255, the recommended approach for builders is to use an **evaluation module** or a well-designed breakout board rather than building from scratch. The reason: the TPA3255's PCB layout is *extremely* critical. The switching currents are large and fast, and poor layout causes EMI, oscillation, and degraded performance. TI's reference layout is the product of extensive engineering — use it.

Key external components:
- **LC output filter:** Typically 10-22 uH inductor + 0.68-1 uF film capacitor per channel. The inductor must handle the full output current without saturating.
- **Input filter:** RC low-pass to limit input bandwidth and reduce noise.
- **Power supply decoupling:** Multiple ceramic capacitors (10 uF, 100 nF) at the supply pins, plus bulk electrolytics (2200+ uF).

### 8.4 Power Output Calculations

For the TPA3255 in BTL mode from a 48V supply:

```
Vpeak (each half-bridge) ≈ Vcc - 2V = 46V
BTL Vpeak = 2 × 46 = 92V
BTL Vrms = 92 / sqrt(2) = 65V

P(8Ω) = 65² / 8 = 528W (theoretical max)
P(4Ω) = 65² / 4 = 1056W (theoretical, current-limited in practice)
```

In practice, the TPA3255 is rated for 315W per channel into 4 ohms in BTL mode, limited by internal current capability and thermal considerations.

Even at "only" 315W, this is enormously powerful from a single chip. For a party system driving efficient PA speakers (95+ dB sensitivity), this is thunderous.

### 8.5 LM3886 vs TPA3255 — When to Use Each

| Parameter        | LM3886         | TPA3255         |
|------------------|----------------|-----------------|
| Topology         | Class AB       | Class D         |
| Max power (8Ω)   | 68W            | 175W (BTL)      |
| Max power (4Ω)   | 135W*          | 315W (BTL)      |
| THD (1W/8Ω)      | 0.03%          | 0.005%          |
| Efficiency       | ~55%           | ~92%            |
| Heat sink needed | Large          | Minimal/none    |
| Power supply     | Dual rail      | Single rail     |
| Sonic character  | "Warm," analog | Clean, dynamic  |
| Best for         | Home hi-fi     | PA, party, sub  |
| Build difficulty | Easy           | Moderate†       |

*Limited by thermal dissipation.
†PCB layout critical; using a pre-made board is recommended.

---

## 9. Power Output — What Do You Actually Need?

Before choosing or designing an amplifier, you need to know how much power your application requires. This is one of the most misunderstood topics in audio.

### 9.1 The Basics

Speaker sensitivity is rated in dB SPL at 1W/1m. A speaker rated at 87 dB/W/m produces 87 dB SPL with 1 watt of input, measured at 1 meter.

Each doubling of power adds 3 dB SPL. Each doubling of distance costs 6 dB SPL (inverse square law).

```
SPL = Sensitivity + 10 × log10(Power) - 20 × log10(Distance/1m)
```

### 9.2 Worked Examples

**Home listening (3 meters, 90 dB peak SPL target):**
Speaker sensitivity: 87 dB/W/m

```
90 = 87 + 10 × log10(P) - 20 × log10(3)
90 = 87 + 10 × log10(P) - 9.5
12.5 = 10 × log10(P)
P = 10^1.25 = 17.8W per channel
```

18 watts. A 50W amp gives you 4.5 dB of headroom above your target — plenty for dynamic peaks.

**Party use (10 meters, 105 dB peak SPL target):**
Speaker sensitivity: 97 dB/W/m (typical PA speaker)

```
105 = 97 + 10 × log10(P) - 20 × log10(10)
105 = 97 + 10 × log10(P) - 20
28 = 10 × log10(P)
P = 10^2.8 = 631W per channel
```

631 watts to hit peak levels at 10 meters with a single speaker. Now you see why PA amplifiers are so powerful, and why Class D's efficiency matters.

### 9.3 Headroom

Always design for more power than you think you need. Music has a **crest factor** (peak-to-average ratio) of 10-20 dB. An amp that clips on peaks sounds terrible. Having 10 dB of headroom (10x the average power) keeps the amp operating in its linear region virtually all the time.

This is also why amplifier power ratings are somewhat misleading for hi-fi use. A 100W amp operating with normal music at comfortable levels is typically delivering 1-5W average, with peaks to 20-50W. The amplifier spends most of its time idling.

---

## 10. The Complete Amplifier — Block Diagram

Now that we've covered individual blocks, let's see how they fit together in a complete discrete Class AB amplifier. This is the "canonical" topology used in countless designs from the 1970s onward:

```
                    ┌─────────────────────────────────────────────────────┐
                    │                                                     │
Input ──[R_in]──┬──┤(+)  Differential    ──► VAS (Voltage    ──► Output  ├──► Speaker
                │  │      Input Stage          Amplifier         Stage   │
                │  │      (Long-tailed         Stage)          (Class AB)│
                │  │       pair)               (Common          with     │
                │  │                            emitter)        emitter  │
                │  │(-) ◄── Feedback           with             resistors│
                │  │        network            compensation              │
                │  │                           cap (Miller)              │
                │  └─────────────────────────────────────────────────────┘
                │              ↑
                │         [Vbe multiplier
                │          on heat sink]
                │
               GND
```

### 10.1 Input Stage — The Differential Pair

The **long-tailed pair** (differential amplifier) is the standard input topology. It subtracts the feedback signal from the input signal, amplifies the difference, and rejects common-mode noise (like power supply ripple). Using matched transistors (or a monolithic dual like the MAT12) gives the lowest offset and best common-mode rejection.

Current-source biasing of the tail improves CMRR (Common-Mode Rejection Ratio) and linearity. A simple current mirror or a cascode current source works well.

### 10.2 Voltage Amplifier Stage (VAS)

The **VAS** provides the bulk of the voltage gain. It's typically a common-emitter stage with a current-source load for maximum gain. The **Miller compensation capacitor** (typically 30-100 pF) connects from collector to base, creating a dominant pole that ensures stability with negative feedback.

The VAS is also where the signal transitions from the small-signal world (milliamps) to the pre-driver level (tens of milliamps). It must have enough voltage swing to drive the output stage to full power.

### 10.3 Pre-Driver and Driver

Between the VAS and the output devices, a **pre-driver** (or buffer) stage provides current gain. The driver transistors (e.g., MJE15032/MJE15033) handle the base current requirements of the output devices while maintaining linearity.

### 10.4 Output Stage

The complementary output stage we covered in Sections 1-3, with the Vbe multiplier for biasing and emitter resistors for stability.

### 10.5 Protection and Output Network

DC protection, overcurrent limiting, speaker relay, Zobel network, and output inductor — all as described in Section 5.

### 10.6 The Elegance of the Whole

The beauty of this topology is that each block can be analyzed and optimized independently, then combined. The negative feedback loop wraps around the entire chain, correcting for the imperfections of each individual stage. The compensation cap ensures stability. The result is an amplifier with performance that far exceeds what any single stage could achieve on its own.

If you want to dive deeper into discrete amplifier design, Bob Cordell's *Designing Audio Power Amplifiers* and Douglas Self's *Audio Power Amplifier Design Handbook* are the definitive references. Both are dense but rewarding.

---

## 11. Practical Design Tips

### 11.1 Start with a Proven Topology

Do not try to invent a new amplifier topology for your first discrete design. The topologies described above have been refined over 50+ years by thousands of engineers. Start with a well-documented design (Rod Elliott's ESP project pages are an excellent free resource), understand it thoroughly, and then modify it incrementally.

### 11.2 Simulate Before You Solder

Build the complete circuit in LTSpice before committing to hardware. Verify:
- DC operating point (all voltages and currents)
- AC gain and bandwidth
- Step response (apply a 1V step, look for ringing)
- Output power into 4 and 8 ohm loads
- Clipping behavior
- Response to a short circuit on the output (does the protection work?)

### 11.3 Build the Power Supply First

As with tube amps (Chapter 24), build and test your power supply independently before connecting the amplifier. Verify voltages, ripple, and regulation under load.

### 11.4 Thermal Derating

Design for worst-case conditions: highest ambient temperature, lowest load impedance, continuous full power. Your amp might live in a sealed cabinet next to a hot wall, driving 4-ohm speakers at a summer party. Design for that scenario, and everything else is easy.

### 11.5 Use Quality Passives in the Signal Path

The few components in the signal path (input resistor, feedback resistors, coupling caps if any) should be quality parts: metal film resistors (1% tolerance, low noise), film capacitors (MKP or C0G ceramic), and low-ESR electrolytics for the power supply. The money you spend on premium passive components in the signal path has a far higher return than money spent on exotic cables or vibration-dampening feet.

---

## 12. Chapter Summary

This chapter covered the practical building blocks of solid-state amplifier design:

1. **BJT output stages:** Complementary symmetry is the foundation. Sziklai pairs offer lower saturation voltage and faster operation than Darlington pairs.
2. **MOSFETs** offer thermal self-protection and simpler biasing, but with higher on-resistance.
3. **The Vbe multiplier** sets Class AB quiescent current. Thermal coupling to the heat sink is essential for stability.
4. **Thermal runaway** is the catastrophic failure mode of BJT designs — prevented by emitter resistors, thermal tracking, and adequate heat sinking.
5. **Protection circuits** (DC detection, overcurrent, speaker relay) are not optional — they save your speakers and your reputation.
6. **Heat sink sizing** follows a thermal resistance chain calculation. Always design with margin.
7. **The LM3886** is a complete, excellent-sounding Class AB power amplifier. The "gainclone" topology is dead simple to build.
8. **The TPA3255** is a Class D monster. Enormous power with tiny heat sinks. Use a pre-made board for best results.

In the next chapter, we leave semiconductors behind and enter the world of glowing glass bottles — vacuum tube amplifier theory.

---

*Next: [Chapter 20: Tube Amplifier Theory](20-tube-amplifier-theory.md)*
*Previous: [Chapter 18: How Amplifiers Work](18-how-amplifiers-work.md)*
