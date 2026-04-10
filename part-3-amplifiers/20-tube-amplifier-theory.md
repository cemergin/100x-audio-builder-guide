<!--
  CHAPTER: 20
  TITLE: Tube Amplifier Theory
  PART: 3 — Amplifiers
  PREREQS: Chapter 19
  KEY_TOPICS: vacuum tube physics, triodes, plate characteristics, mu/rp/gm, load lines, pentodes, operating points, single-ended Class A, push-pull Class AB, output transformers, cathode bias vs fixed bias, negative feedback, tube sound
  DIFFICULTY: Advanced
  UPDATED: 2026-04-10
-->

# Chapter 20: Tube Amplifier Theory

> **Part 3 — Amplifiers** | Prerequisites: Chapter 19 | Difficulty: Advanced

There is something profoundly satisfying about a vacuum tube. It glows. It's warm to the touch. You can *see* the electrons doing their thing — the faint blue haze of current flow inside a beam-power tube running near its limit is genuinely beautiful. And when you plug a guitar into a tube amp and dig in, there's a responsiveness and musicality that's hard to explain and harder to replicate with transistors.

But tube amplifiers also run at voltages that can kill you. The power supply in a typical guitar tube amp produces 300-400V DC. A big hi-fi amp might hit 500-600V. These voltages are stored in filter capacitors that hold their charge long after you pull the plug. We'll cover safety extensively in Chapter 22, but let this be your first warning: **tube amplifier work requires respect for lethal voltages. Every. Single. Time.**

This chapter covers the theory — how tubes work, how to analyze them, and the circuit topologies that make them sing. Chapter 24 puts this theory into practice with a complete guitar amp build. Let's start at the beginning: a hot piece of metal in a vacuum.

### In This Chapter
- How vacuum tubes work physically
- Triode characteristics: mu, rp, gm, and their relationships
- Drawing and reading load lines — the graphical method
- Pentodes and beam-power tubes
- Choosing operating points for different goals
- Single-ended Class A — the simplest tube amplifier
- Push-pull Class AB — more power, different character
- Output transformers — why tubes need them
- Cathode bias vs fixed bias
- Negative feedback in tube amplifiers
- The "tube sound" — what's actually happening

### Related Chapters
- [Ch 18: How Amplifiers Work](18-how-amplifiers-work.md) — amplifier classes and distortion theory
- [Ch 19: Solid-State Amplifier Design](19-solid-state-amplifier-design.md) — contrast with transistor approaches
- [Ch 6: Gain Stages in Pedals](../part-1-guitar-pedals/06-gain-stages.md) — simpler single-tube-like gain stages
- [Ch 21: Power Supply Design](21-power-supply-design.md) — the high-voltage B+ supply
- [Ch 22: Safety](22-safety-mains-high-voltage.md) — CRITICAL reading before building anything
- [Ch 24: Build: Tube Guitar Amp](24-build-tube-guitar-amp.md) — applying this theory

---

## 1. How Vacuum Tubes Work

A vacuum tube is remarkably simple in concept. Take a glass bottle, pump out the air, put some metal structures inside, and use heat to liberate electrons from one of them. Then use electric fields to control where those electrons go.

### 1.1 The Cathode — Electron Source

The **cathode** is a metal electrode coated with materials that emit electrons when heated (thermionic emission). In most modern tubes, the cathode is a nickel tube coated with barium/strontium oxide, heated by a separate heater filament (called an "indirectly heated cathode"). Some tubes, especially rectifiers and some power tubes, use the heater wire itself as the cathode ("directly heated" or "filament" type).

The heater typically runs at 6.3V AC or 12.6V AC/DC, drawing 0.3-1.5A depending on the tube type. Those orange glowing filaments you see? That's the heater doing its job — getting the cathode hot enough (~800C) to boil electrons off the surface.

### 1.2 The Plate (Anode) — Electron Collector

The **plate** (also called the anode) is a metal structure surrounding the cathode, connected to a high positive voltage (the B+ supply, typically 150-500V). The positive voltage attracts the electrons emitted by the cathode. Current flows from cathode to plate through the vacuum — and through the external circuit back to the cathode via the power supply.

The plate dissipates significant power as heat (the electrons slam into it at high velocity). Plate dissipation ratings range from 1W for small-signal tubes to 25-40W for common power tubes, and hundreds of watts for transmitting tubes.

### 1.3 The Grid — The Control Element

Between the cathode and plate sits the **control grid** — a fine wire mesh or helix. When the grid is at the same voltage as the cathode (0V bias), electrons pass through the grid mesh freely on their way to the plate.

When the grid is made *negative* relative to the cathode, the negative electric field repels electrons back toward the cathode, reducing the plate current. At a sufficiently negative voltage (the **cutoff voltage**), plate current drops to essentially zero.

When the grid is made *positive* relative to the cathode, it attracts electrons — some electrons are captured by the grid itself, causing **grid current** to flow. This is generally undesirable for voltage amplification because it loads the source and causes distortion. Normal operation keeps the grid negative (in the linear region, at least).

The key insight: a *small* voltage change on the grid causes a *large* change in plate current. This is amplification. The tube converts a small signal voltage into a large current change, which becomes a large voltage change when that current flows through a load resistor.

### 1.4 The Complete Triode

A tube with three elements — cathode, grid, and plate — is a **triode**. The 12AX7 (ECC83 in European nomenclature) is the most famous audio triode, containing two triode sections in one glass envelope. It's the standard preamp tube in guitar amplifiers and has been in continuous production since the 1950s.

```
        Plate (Anode)
         ┌─────┐
         │     │
    ─────┤ Grid├─────  ← Control grid (fine wire mesh)
         │     │
         └──┬──┘
            │
        Cathode
         [heater filament inside]
```

---

## 2. Triode Characteristics

To design circuits with triodes, we need to understand their electrical behavior quantitatively. Three interrelated parameters define a triode's behavior.

### 2.1 The Three Parameters

**mu (μ) — Amplification Factor**

The amplification factor is the ratio of the change in plate voltage to the change in grid voltage needed to produce the same change in plate current:

```
μ = -ΔVp / ΔVg   (at constant Ip)
```

For a 12AX7: μ ≈ 100. This means a 1V change on the grid has the same effect on plate current as a 100V change on the plate (but in opposite direction). High mu = high potential voltage gain.

**rp — Plate Resistance (or AC plate resistance)**

The plate resistance is the ratio of change in plate voltage to the resulting change in plate current:

```
rp = ΔVp / ΔIp   (at constant Vg)
```

For a 12AX7: rp ≈ 62.5k ohms. This is the tube's output impedance for AC signals. It's high — much higher than a transistor's output impedance. This is a fundamental characteristic of tubes and explains why they need output transformers.

**gm — Transconductance**

Transconductance is the ratio of change in plate current to the change in grid voltage:

```
gm = ΔIp / ΔVg   (at constant Vp)
```

For a 12AX7: gm ≈ 1.6 mA/V (1600 micromhos). This tells you how effectively the grid voltage controls the plate current.

### 2.2 The Fundamental Relationship

These three parameters are not independent. They're related by:

```
μ = gm × rp
```

For the 12AX7: 100 = 0.0016 × 62500. Check.

This relationship is crucial for circuit analysis. If you know any two parameters, you can calculate the third.

### 2.3 Plate Characteristics — The Tube's Personality

The **plate characteristic curves** are a family of curves plotting plate current (Ip) versus plate voltage (Vp) for various grid voltages (Vg). They're the tube equivalent of a transistor's collector curves, and they're essential for graphical analysis.

```
Ip (mA)
 │
 │   Vg=0V  Vg=-1V  Vg=-2V  Vg=-3V  Vg=-4V
 │   /       /       /        /        /
 3 ─/───────/───────/────────/────────/─────
 │ /       /       /        /        /
 2/───────/───────/────────/────────/───────
 │       /       /        /        /
 1──────/───────/────────/────────/─────────
 │     /       /        /        /
 0────/───────/────────/────────/───────────
 └──────────────────────────────────────── Vp (V)
   0   50   100  150  200  250  300
```

(This is a simplified sketch — real curves are published in tube datasheets and available at sites like tubecad.com and duncanamps.com.)

**Reading the curves:**
- Pick a grid voltage (say Vg = -2V)
- Follow that curve across — for each plate voltage, you can read the plate current
- The spacing between curves for equal grid voltage steps tells you about linearity. Evenly spaced = linear. Crowded at one end = nonlinear (distortion)

---

## 3. Load Lines — The Graphical Method

The **load line** is the most powerful tool for analyzing tube circuits. It's a graphical technique that overlays the circuit's constraints on the tube's characteristics to find the operating point and predict gain, distortion, and output swing.

### 3.1 Drawing a DC Load Line

For a simple triode gain stage with a plate resistor (Ra):

```
         +B (e.g., 300V)
          │
         [Ra] (plate load resistor, e.g., 100k)
          │
     ┌────┤ Plate
     │    │
     │    Tube
     │    │
     │    ┤ Grid ◄── Input signal + bias
     │    │
     └────┤ Cathode
          │
         [Rk] (cathode resistor for bias)
          │
         GND
```

The plate voltage and current are constrained by Ohm's law:

```
Vp = B+ - Ip × Ra
```

This is a straight line on the plate curves:
- When Ip = 0: Vp = B+ = 300V (the x-intercept)
- When Vp = 0: Ip = B+ / Ra = 300V / 100k = 3 mA (the y-intercept)

Draw this line on the plate curves. The operating point (Q point) is where the load line intersects the curve for the chosen grid bias voltage.

### 3.2 Finding the Operating Point

For a 12AX7 with B+ = 300V, Ra = 100k, and cathode bias resistor Rk = 1.5k:

1. Draw the DC load line from (300V, 0 mA) to (0V, 3 mA).
2. The cathode resistor develops a voltage Vk = Ip × Rk. Since the grid is at ground potential (through the grid leak resistor), Vgk = -Vk = -Ip × Rk.
3. The operating point is where the load line crosses the grid voltage curve that satisfies Vg = -Ip × Rk simultaneously.
4. This typically lands around Ip = 1.0-1.2 mA, Vp = 180-190V, Vg = -1.5 to -1.8V for these values.

### 3.3 Reading Gain from the Load Line

Once you have the load line and Q point:

1. Choose a signal swing on the grid — say ±1V from the bias point.
2. Find where the load line crosses the Vg = (bias - 1V) and Vg = (bias + 1V) curves.
3. Read the corresponding plate voltages.
4. The voltage gain is the change in plate voltage divided by the change in grid voltage.

For a 12AX7 with Ra = 100k, you'll typically find a gain of about 55-65 (out of the theoretical maximum μ = 100, reduced by the plate load resistor forming a divider with rp).

The formula for voltage gain of a triode stage:

```
Av = -μ × Ra / (Ra + rp)
```

For μ = 100, Ra = 100k, rp = 62.5k:

```
Av = -100 × 100k / (100k + 62.5k) = -61.5
```

The negative sign indicates phase inversion — the plate voltage goes down when the grid voltage goes up.

### 3.4 What the Load Line Tells You About Distortion

Look at the spacing of the grid curves along the load line. If the spacing is even, the stage is linear. If the curves are closer together at one end, the stage produces asymmetric distortion — it clips differently on positive and negative grid swings.

For triodes, the curves are typically more crowded at the high-current end (near the top of the load line) and more spread out at the low-current end. This means the positive half of the output waveform is compressed relative to the negative half. This asymmetric clipping generates predominantly **even-order harmonics** (2nd, 4th, etc.), which is a major component of the "tube sound."

> **What happens if... you choose a very high plate resistor?**
> The load line becomes steeper, the voltage gain increases (closer to μ), but the output impedance goes up and the available voltage swing shrinks. For a 12AX7 with Ra = 220k (a common high-gain value), you might get gain of 70+ but with a smaller clean output swing before clipping. This is exactly the trade-off guitar amp designers play with — more gain versus more headroom.

---

## 4. Pentodes and Beam-Power Tubes

Triodes are elegant but limited. Their relatively low gain (especially for power tubes) and high plate resistance led to the development of tubes with additional grid elements.

### 4.1 The Pentode

A **pentode** adds two more grids between the control grid and the plate:

**Screen grid (G2):** Placed between the control grid and the plate, connected to a positive voltage (usually via a resistor from B+). The screen grid accelerates electrons toward the plate and *shields* the plate from the control grid. This dramatically reduces the plate-to-grid capacitance (Miller capacitance), increasing the tube's high-frequency capability and its amplification factor.

**Suppressor grid (G3):** Placed between the screen grid and the plate, usually connected to the cathode. Its job is to prevent **secondary emission** — when high-energy electrons hit the plate, they knock loose additional electrons that would be attracted back to the positive screen grid, causing a current dip. The suppressor grid's negative potential (relative to the plate) repels these secondary electrons back to the plate.

### 4.2 Beam-Power Tubes

**Beam-power tubes** (like the 6V6, 6L6, EL34) achieve suppression of secondary emission differently — through beam-forming plates that focus the electron stream into beams. The concentrated electron beam creates a space charge between the screen and plate that acts like a virtual suppressor grid.

Beam-power tubes are the workhorses of guitar and audio power amplification. The 6V6 (5W plate dissipation, used in the Fender Champ we'll build in Chapter 24), the 6L6 (23W, used in Fender Twin Reverb), and the EL34 (25W, used in Marshall amplifiers) are among the most iconic audio tubes ever made.

### 4.3 Pentode Characteristics vs Triode

Compared to triodes, pentodes and beam-power tubes have:

- **Much higher mu:** 100-500+ (vs 10-100 for power triodes)
- **Much higher rp:** 20k-100k+ (vs 1k-10k for power triodes)
- **Higher gm:** 3-11 mA/V (vs 2-6 mA/V for power triodes)
- **Flatter plate curves:** The plate current is nearly independent of plate voltage, making the plate curves almost horizontal. This means higher gain but also higher output impedance.

The flat plate curves of a pentode look very different from a triode's:

```
Ip (mA)                  Pentode plate curves
 │
 │   Vg=0V ──────────────────────────────────
 │
 │   Vg=-5V ─────────────────────────────────
 │
 │   Vg=-10V ────────────────────────────────
 │
 │   Vg=-15V ────────────────────────────────
 │
 │   Vg=-20V ────────────────────────────────
 │
 └──────────────────────────────────────── Vp (V)
   0   100  200  300  400  500
```

The nearly horizontal curves mean: changing the plate voltage barely affects the current, while changing the grid voltage has a large effect. This is why pentodes have such high gain — and such high output impedance.

### 4.4 Pentode vs Triode — A Sonic Comparison

The choice between pentode and triode operation in a power stage has profound sonic implications:

**Pentode mode:**
- Higher output power (utilizes the tube more efficiently)
- Higher output impedance → less speaker damping → more "lively" interaction with the speaker
- Distortion character: more complex harmonics, including stronger odd-order harmonics
- Tone: "aggressive," "raw," "cutting" — the Marshall sound
- Better for rhythm guitar, cutting through a band mix

**Triode mode:**
- Lower output power (typically 30-40% of pentode power)
- Lower output impedance → better speaker damping → tighter bass
- Distortion character: predominantly even-order harmonics, smoother
- Tone: "warm," "round," "sweet" — more hi-fi-like, often preferred for jazz and clean tones
- Softer dynamic response

Many modern tube amps include a pentode/triode switch so the player can choose on the fly. This is accomplished by switching the screen grid connection between B+ (pentode mode) and the plate (triode mode).

### 4.5 Ultralinear Operation — The Compromise

The **ultralinear** connection taps the screen grid at a point partway along the output transformer primary (typically 40-43% from the B+ end). This provides characteristics between pentode and triode:
- Higher power than triode mode
- Lower distortion than pentode mode
- Moderate output impedance
- Excellent for hi-fi applications

Ultralinear operation was invented by Hafler and Keroes in 1951 and became the standard for hi-fi tube amplifiers. It's less common in guitar amps (which usually want the full pentode power and character), but excellent for home audio.

### 4.6 Triode-Connected Pentodes

You can connect a pentode as a triode by tying the screen grid to the plate. This reduces the gain and output impedance to triode-like values while retaining the pentode's higher power handling. Many guitar amps use this as a switchable option — "pentode mode" for more power and a different distortion character, "triode mode" for lower power and a smoother, more triode-like sound.

---

## 5. Choosing Operating Points

The operating point (Q point) determines the tube's behavior — how much gain you get, what the distortion character looks like, how much headroom you have, and how much power the tube dissipates.

### 5.1 For Maximum Clean Headroom (Hi-Fi)

Center the Q point in the middle of the load line, equidistant from cutoff and saturation. This gives the maximum symmetrical swing before clipping in either direction. The distortion at moderate levels will be lowest here.

### 5.2 For Guitar Preamp — Musical Clipping

Many guitar amp designs deliberately set the Q point off-center:

- **Biased cold (closer to cutoff):** The tube clips asymmetrically — it clips the negative swing (on the plate) sooner than the positive swing. This produces more even-order harmonics and a particular kind of "crunchy" breakup.
- **Biased hot (more current):** More headroom before clipping, but when it does clip, it's more symmetrical (more odd harmonics). "Cleaner" at moderate levels.

### 5.3 For Power Tubes — Staying within Dissipation Limits

Power tubes have a maximum plate dissipation rating. The Q point must be chosen so that:

```
Pd = Vp × Ip ≤ Pd_max
```

This defines a hyperbolic curve on the plate characteristics (Ip = Pd_max / Vp). The Q point must be below this curve. For a 6V6 with Pd_max = 14W at a plate voltage of 300V: Ip_max = 14W / 300V = 46.7 mA.

### 5.4 SPICE Simulation

You can simulate tube circuits in LTSpice using tube models. The Koren models and the "Improved Koren" models are available for most common audio tubes. Download them, add them to your LTSpice library, and you can:

- Plot plate characteristic curves
- Draw load lines
- Simulate complete amplifier stages
- Predict distortion character
- Test different operating points quickly

This is enormously useful before committing to a build. See [Ch 3: Simulation](../part-0-electronics/03-simulation.md) for SPICE setup.

---

## 6. Single-Ended Class A

The **single-ended (SE) Class A** topology is the simplest power amplifier circuit. One tube, one output transformer — and a sound that has inspired devotion for nearly a century.

### 6.1 How It Works

A single power tube (or paralleled tubes) handles the *entire* audio signal. The tube is biased to conduct continuously, with the signal modulating the plate current around the Q point.

```
         +B+ (300-400V)
          │
    ┌─────┤
    │     │
    │  [Output Transformer Primary]
    │     │
    │     ┤ Plate
    │     │
    │    Tube (e.g., 6V6)
    │     │
    │     ┤ Grid ◄── Audio input (from preamp)
    │     │
    │     ┤ Cathode
    │     │
    │    [Rk] (cathode bias resistor)
    │     │
    │    GND
    │
    │   [Output Transformer Secondary] ──── Speaker
    │     │
    └─────┘
```

The output transformer converts the high-impedance, high-voltage plate signal to a low-impedance, lower-voltage signal suitable for driving a speaker (more on transformers in Section 8).

### 6.2 The Sound of Single-Ended

Single-ended amplifiers have an inherently asymmetric transfer function. The tube clips differently on the positive and negative halves of the signal because the plate curves are not equally spaced. This asymmetry produces predominantly **even-order harmonics** (2nd, 4th, 6th...).

Even-order harmonics are musically consonant — the 2nd harmonic is one octave up, the 4th is two octaves up. At low levels, they add "warmth" and "richness." At higher levels (when the tube is being pushed into clipping), they produce a smooth, compressive distortion that guitar players describe as "creamy," "fat," or "singing."

Additionally, the output transformer saturates at high levels, which adds its own harmonic content and compressive behavior. The power supply sags under heavy load (the B+ voltage drops), reducing available headroom and creating a natural dynamic compression that responds to playing dynamics.

### 6.3 Limitations

- **Low power:** A single 6V6 produces about 4-5W. A single EL34 about 8-10W. For hi-fi, this requires high-sensitivity speakers (> 93 dB/W/m). For guitar, 5W is plenty for home practice and recording.
- **Low efficiency:** Class A is inherently inefficient. The tube dissipates maximum power at idle. Heat is significant.
- **High output impedance:** Without feedback, the output impedance is relatively high (determined by the transformer ratio and the tube's rp), giving a low damping factor. Great for guitar, potentially problematic for hi-fi speakers.

---

## 7. Push-Pull Class AB

The **push-pull** topology uses two (or more) tubes working together, each handling one half of the signal. This is the dominant topology for tube amplifiers over about 15 watts.

### 7.1 How It Works

The audio signal is split into two out-of-phase signals by a **phase splitter** (or an input transformer). Each signal drives one tube of a matched pair. The output transformer has a center-tapped primary — each tube drives one half of the primary in alternation.

```
         +B+ (400-500V)
          │
    ┌─────┤ Center tap
    │     │
    │  [Primary     │      Primary]
    │   half 1      │       half 2
    │     │         │          │
    │     ┤ Plate   │   Plate ┤
    │     │         │          │
    │    Tube 1     │       Tube 2
    │   (e.g.,      │      (e.g.,
    │    6V6)       │       6V6)
    │     │         │          │
    │     ┤ Grid    │   Grid  ┤
    │     │         │          │
    │    Signal(+)  │   Signal(-)
    │               │
    │         [Output Transformer Secondary] ──── Speaker
    │               │
    └───────────────┘
```

### 7.2 Advantages of Push-Pull

**Cancellation of even harmonics.** Because the two tubes operate in antiphase, even-order harmonics (and DC magnetization of the transformer core) cancel in the output. This makes push-pull amps inherently lower-distortion than single-ended — the dominant remaining distortion is odd-order.

**More power.** Two tubes share the work, and the push-pull configuration is more efficient with the output transformer (no DC core magnetization means the transformer can be smaller for the same power).

**Better power supply rejection.** B+ ripple (hum) appears equally on both plates, so it cancels in the output transformer. Push-pull amps are much less sensitive to power supply quality than single-ended amps.

### 7.3 The Phase Splitter

To drive the push-pull output stage, you need two signals that are equal in amplitude but opposite in phase. Several phase splitter topologies exist:

**Cathodyne (split-load):** A single triode with equal plate and cathode resistors. One output is taken from the plate (inverted) and one from the cathode (non-inverted). Simple but doesn't provide gain, and the two outputs have different output impedances.

**Long-tailed pair (differential amplifier):** Two triodes sharing a common cathode resistor (the "tail"). One grid gets the signal, the other gets ground (or a local feedback signal). Provides gain and well-matched outputs. This is the standard in most high-quality designs.

**Paraphase:** A triode gain stage followed by another triode that inverts and matches the signal. Simple but can have balance issues.

### 7.4 Biasing Push-Pull Tubes

For push-pull Class AB, the bias determines the quiescent (idle) plate current and affects both distortion and power output:

- **Hotter bias (more idle current):** More Class A operation, smoother transition between tubes, lower crossover distortion, but more heat and reduced tube life.
- **Colder bias (less idle current):** More efficiency, less heat, but more crossover distortion as the tubes begin to produce notch distortion near zero crossing.

Most guitar amps are biased in the "cool to moderate" range for reliability and tube longevity, while hi-fi amps may be biased hotter for lower distortion.

> **What happens if... the bias drifts in a push-pull amp?**
> If one tube's bias drifts so it conducts more than the other, the asymmetry un-cancels the even harmonics and can DC-magnetize the output transformer core. The amp sounds muddy and distorted, and the transformer may saturate prematurely. This is why matched output tubes and proper bias adjustment matter. In a guitar amp, some players like a slightly mismatched pair for added even-harmonic coloration, but taken too far, it's just sloppy.

---

## 8. Output Transformers

The **output transformer** is arguably the most critical (and most expensive) component in a tube amplifier. It's the interface between the tube's high-impedance, high-voltage world and the speaker's low-impedance, low-voltage world.

### 8.1 Why Tubes Need Output Transformers

A typical power tube like the 6V6 has a plate resistance (rp) of about 52k ohms. An 8-ohm speaker has... 8 ohms. If you connected them directly, almost all the signal voltage would drop across the tube's plate resistance, and essentially no power would reach the speaker. Maximum power transfer occurs when the load impedance matches the source impedance, but we can't make a tube with 8 ohms of plate resistance.

The output transformer solves this with **impedance transformation**. A transformer with a turns ratio of N:1 transforms impedances by N²:

```
Zreflected = Zload × N²
```

To make an 8-ohm speaker look like 5000 ohms (a typical load for a 6V6): N² = 5000/8 = 625, so N = 25:1 turns ratio.

### 8.2 Impedance Transformation Ratio

The "correct" load impedance for a power tube is specified in the datasheet (or derived from the plate curves using load line analysis). Common values:

| Tube | Typical Plate-to-Plate Load (Push-Pull) | Single-Ended Primary Z |
|------|------------------------------------------|----------------------|
| 6V6  | 8000-10000 ohms                          | 5000 ohms            |
| 6L6  | 6600 ohms                                | 4000-5000 ohms       |
| EL34 | 6600 ohms                                | 3500-4500 ohms       |
| EL84 | 8000 ohms                                | 5000-7000 ohms       |

The secondary winding typically has taps for 4, 8, and 16 ohm speakers.

### 8.3 Transformer Quality and Sound

The output transformer is the most significant source of coloration in a tube amplifier. Its limitations include:

- **Bandwidth:** A cheap transformer might roll off below 80 Hz and above 10 kHz. A good one extends from 20 Hz to 40 kHz. For guitar amps, limited bandwidth is actually *desirable* — it rolls off the harsh high-frequency content of distortion.
- **Core saturation:** At high signal levels and low frequencies, the transformer core saturates, compressing the signal. This is part of the tube amp "sag" character.
- **Leakage inductance:** Imperfect magnetic coupling between windings limits high-frequency response and affects transient response.
- **DCR (DC resistance):** The copper windings have resistance, which wastes power and increases output impedance.

For guitar amps, transformers from manufacturers like Hammond, Heyboer, Mercury Magnetics, and ClassicTone are proven choices. For hi-fi, companies like Hashimoto, Tamura, Lundahl, and Edcor make excellent units.

> **What happens if... you use the wrong output transformer impedance?**
> If the reflected impedance is too high (e.g., using a 16-ohm speaker on an 8-ohm tap), the load line is steeper, the voltage swing increases, and the power decreases. If the reflected impedance is too low (8-ohm speaker on a 16-ohm tap), the load line is shallower, the current swing increases, and you can exceed the tube's plate dissipation rating. Too low a load is more dangerous — it can overheat the tubes and transformer. Always match the speaker to the correct tap.

---

## 9. Cathode Bias vs Fixed Bias

There are two fundamental approaches to biasing the power tubes in a tube amp, each with distinct characteristics.

### 9.1 Cathode Bias (Self-Bias)

In **cathode bias**, a resistor between the cathode and ground develops a positive voltage on the cathode. Since the grid is at 0V DC (through the grid leak resistor), the grid is negative *relative to the cathode* by an amount equal to the cathode voltage. The bias is self-adjusting — if the tube tries to draw more current, the cathode voltage increases, making the grid more negative, reducing the current. Negative feedback at work.

```
     Grid (0V DC) ──┤
                     │
                    Tube
                     │
                 Cathode
                     │
                    [Rk] ──┬── GND
                           │
                         [Ck] (bypass capacitor)
```

The bypass capacitor Ck prevents the cathode voltage from varying at audio frequencies (which would reduce the gain due to negative feedback). For full bypass: Ck > 1/(2π × f_low × Rk). For a 1.5k cathode resistor and 20 Hz low-frequency limit: Ck > 5.3 uF. In practice, 22-47 uF is typical.

**Advantages:**
- Self-adjusting — tube aging and replacement doesn't require bias adjustment
- Simpler circuit — no separate bias supply
- Safer — can't accidentally bias the tubes into a destructive state
- Each tube finds its own operating point, so matched tubes are less critical

**Disadvantages:**
- Wastes some B+ voltage (typically 15-30V dropped across Rk)
- Slightly less efficient (the cathode resistor dissipates power)
- The self-biasing action reduces available output power slightly

The Fender 5F1 Champ (which we'll build in Chapter 24) uses cathode bias. So do most small to medium tube amps.

### 9.2 Fixed Bias

In **fixed bias**, a separate negative voltage supply provides the grid bias voltage directly. "Fixed" is slightly misleading — the bias is adjustable via a potentiometer, but it doesn't self-adjust with tube aging.

```
                    Bias supply (-30 to -50V)
                         │
                        [Pot] (bias adjustment)
                         │
     Grid ◄──────────────┤
                         │
                       [100k] (grid stopper)
                         │
                        Tube
                         │
                     Cathode ── GND (directly)
```

**Advantages:**
- Full B+ voltage available for output swing (no voltage dropped across cathode resistor)
- Higher output power
- Can be optimized precisely for each tube or pair

**Disadvantages:**
- Requires a separate bias supply (more complexity)
- Needs readjustment when tubes are replaced
- No self-correction — if the bias drifts, the tubes can be damaged (running too hot) or produce crossover distortion (too cold)
- Matched tubes are important for push-pull amps

Most large Fender amps (Twin Reverb, Deluxe Reverb, Bassman) and Marshall amps use fixed bias for the power stage.

### 9.3 Which to Choose?

For your first tube amp build: **cathode bias**. It's forgiving, self-correcting, and one less thing to get wrong. The Champ-style amp in Chapter 24 uses cathode bias for exactly these reasons.

For maximum power output and fine-tuning capability: fixed bias. But this requires more expertise and a commitment to periodic maintenance (checking and adjusting bias, especially after tube changes).

---

## 10. Negative Feedback in Tube Amps

Many tube amplifiers use a global negative feedback loop from the output transformer secondary back to the input of the amplifier (typically to the cathode of the phase-splitter stage or to a feedback point in the preamp).

### 10.1 What It Does

The same benefits as solid-state feedback (Chapter 18, Section 5):
- Reduces distortion
- Flattens frequency response (compensating for output transformer limitations)
- Reduces output impedance (increases damping factor)
- Makes the amp less sensitive to tube variations

### 10.2 The Debate: Feedback vs No Feedback

In the hi-fi world, feedback is almost universally used in tube amps. It tames the output transformer's frequency response deviations and reduces distortion to respectable levels.

In the guitar amp world, it's more controversial. The Fender 5F1 Champ has *no* global negative feedback. The Fender Deluxe Reverb has moderate feedback. The Fender Twin Reverb has heavy feedback. Each sounds distinctly different, and the amount of feedback is a significant part of that character.

Without feedback:
- Higher output impedance → more speaker interaction → more "lively" tone
- More harmonic distortion → "warmer" character
- Less controlled frequency response → transformer coloration shows through
- More susceptible to blocking distortion (where the tube is driven past cutoff and takes time to recover)

With feedback:
- Lower output impedance → tighter bass → more "controlled" tone
- Less distortion → "cleaner" headroom
- Flatter response → less coloration
- Better transient response

For the Champ-style build in Chapter 24, we won't use global feedback — keeping it simple and allowing the natural tube character to come through. It's also easier to add feedback later as a mod than to design it in from the start.

### 10.3 Feedback and Stability

Tube amps with output transformers have a particular stability challenge. The output transformer introduces phase shift at high frequencies (due to leakage inductance) and low frequencies (due to core magnetization). If the feedback loop has enough gain at frequencies where this phase shift approaches 180 degrees, the amp will oscillate.

To check stability, look at the phase margin — the difference between the actual phase shift at unity loop gain and 180 degrees. At least 45 degrees of phase margin is recommended. In practice, this means limiting the feedback ratio to what the transformer can support, typically 6-15 dB of feedback for guitar amps, up to 20 dB for hi-fi amps with premium transformers.

---

## 11. The "Tube Sound" — What's Actually Happening

Let's demystify the "tube sound" with actual engineering:

### 11.1 Soft Clipping

When a tube is overdriven, the onset of clipping is gradual. The signal is compressed (rounded) before it's hard-clipped. This is because the tube's transfer function curves gently toward saturation rather than hitting a brick wall. The result: the distortion onset is "musical" rather than harsh.

Solid-state devices (especially op-amps with feedback) clip abruptly — the signal is clean right up to the clipping point, then suddenly flat-topped. The harmonics generated by hard clipping are higher-order and less musical.

### 11.2 Even-Order Harmonics

As discussed, single-ended stages produce predominantly even-order harmonics due to their asymmetric transfer function. These are musically consonant and add perceived "warmth" without making the sound feel "distorted" in the unpleasant sense.

### 11.3 Output Transformer Saturation

At high levels, the output transformer core saturates, compressing the signal further and adding its own harmonics. This is particularly prominent at low frequencies (where the core must handle more flux). The result is a compression of bass notes that many players describe as "sag" — the amp compresses dynamically in response to playing, creating an elastic feel.

### 11.4 Power Supply Sag

Tube amp power supplies are typically not tightly regulated (deliberately). When the amp is driven hard, the current draw increases, the B+ voltage drops (sags), and the available headroom decreases. This creates a dynamic compression effect that responds to playing dynamics — play harder, the amp compresses more; play softer, the headroom opens up. Players describe this as the amp "breathing" or being "touch-responsive."

### 11.5 Microphonics and Physical Interaction

Tubes are mechanical devices. Vibrations can modulate the electron flow (microphonics), creating a subtle physical interaction between the speaker and the amplifier. In extreme cases this is a problem (feedback howl), but in mild form it adds a "liveness" to the sound.

### 11.6 Putting It Together

The "tube sound" isn't one thing — it's the convergence of:
- Soft clipping → gradual distortion onset
- Even-order harmonics → musical coloration
- Transformer saturation → bass compression
- Power supply sag → dynamic compression
- High output impedance → speaker interaction
- Limited bandwidth → natural high-frequency rolloff

Each of these can be simulated individually in the solid-state domain, but the *combination* of all of them, interacting nonlinearly, is what makes a real tube amp feel the way it does. It's not magic — it's engineering. But it's very complex engineering that emerges from the physics of the devices.

---

## 12. Common Audio Tube Types — A Reference

Here's a practical reference for the tubes you'll encounter most often in audio amplifiers. This isn't exhaustive — there are hundreds of tube types — but these are the ones that matter for the circuits in this guide.

### 12.1 Small-Signal / Preamp Tubes (9-pin Noval)

| Tube | European | mu | rp (k) | gm (mA/V) | Typical Use |
|------|----------|-----|---------|------------|-------------|
| 12AX7 | ECC83 | 100 | 62.5 | 1.6 | High-gain preamp, standard guitar amp preamp |
| 12AT7 | ECC81 | 60 | 11 | 5.5 | Phase splitter, reverb driver, moderate-gain preamp |
| 12AU7 | ECC82 | 17 | 7.7 | 2.2 | Low-gain stages, cathode follower, audio line driver |
| 12AY7 | 6072 | 44 | 26 | 1.7 | Original Fender tweed preamp tube, very dynamic |

All of these are dual triodes (two independent triode sections in one envelope) and share the same 9-pin noval pinout for the heater and shield. The audio sections have the same pin functions but different characteristics. You can substitute any of them in most circuits — the amp will work, but the gain and headroom change.

### 12.2 Power Tubes (Octal)

| Tube | Type | Pd_max | Typical Power (SE/PP) | Character |
|------|------|--------|----------------------|-----------|
| 6V6GT | Beam power | 14W | 4-5W SE, 14W PP | Sweet, compressed, classic Fender |
| 6L6GC | Beam power | 30W | 8-10W SE, 40-60W PP | Big, clean, "American" tone |
| EL34 | Pentode | 25W | 8-10W SE, 50W PP | Aggressive, "British" (Marshall) |
| EL84 | Pentode (noval) | 12W | 4-5W SE, 15W PP | Bright, chimey (Vox AC30, AC15) |
| 6550 | Beam power | 35W | 12W SE, 100W PP | High power, clean, hi-fi and bass amps |
| KT88 | Beam power | 42W | 15W SE, 100W+ PP | Premium 6550 equivalent, hi-fi favorite |

**Note on pin compatibility:** 6V6, 6L6, 6550, and KT88 share the same octal pin arrangement for the most part, but **bias requirements differ significantly.** Do not swap power tubes without verifying the bias is appropriate for the new tube. An amp biased for a 6V6 will over-dissipate a 6V6 tube that happens to draw more current, and the same amp with a 6L6 may run cold (insufficient bias current). Always check and adjust.

### 12.3 Rectifier Tubes

| Tube | Type | Max DC mA | Voltage Drop | Character |
|------|------|-----------|--------------|-----------|
| 5Y3GT | Full-wave | 125 mA | ~60V | Most sag, softest, most compression |
| 5U4GB | Full-wave | 275 mA | ~45V | Moderate sag, higher current |
| GZ34 / 5AR4 | Full-wave | 250 mA | ~15V | Least sag, stiffest, closest to silicon |

The rectifier tube choice has a significant effect on the amp's feel and dynamics. The 5Y3 in our Champ build provides maximum sag — when you hit a hard chord, the B+ voltage drops noticeably, compressing the signal. The GZ34 barely sags at all, producing a tighter, more controlled feel. Guitar players argue endlessly about rectifier tube preferences.

### 12.4 Where to Buy Tubes

Current production tubes are manufactured primarily in Russia (Sovtek, Electro-Harmonix, Tung-Sol reissue), Slovakia (JJ Electronic), and China (Shuguang, Psvane). Quality varies but all manufacturers produce usable tubes.

**New production:** JJ Electronic and Electro-Harmonix are generally regarded as consistent and reliable. They're available from tube dealers like Tube Depot, Eurotubes, Doug's Tubes, and thetubestore.com.

**NOS (New Old Stock):** Vintage tubes that were manufactured decades ago but never used. Brands like Mullard, RCA, GE, Telefunken, and Amperex command premium prices. NOS tubes can sound genuinely different from current production (different manufacturing processes, different materials), but the prices are often absurd and counterfeit NOS is rampant. For a first build, stick with current production.

---

## 13. Designing Your Own Tube Amplifier

Once you've built the 5F1 in Chapter 24 and understand how it works, you might want to design your own circuit. Here are the key considerations:

### 13.1 Start with the Output Stage

The output tube(s) and the output transformer define the amp's power output and fundamental character. Choose these first, then design backward to the input.

1. **Choose the output tube** based on desired power and character.
2. **Choose the output transformer** with the correct primary impedance for your tube(s) and secondary impedance for your speaker.
3. **Determine the B+ voltage** — typically specified in the tube datasheet for various operating conditions.
4. **Set the bias point** using load line analysis.

### 13.2 Then Design the Preamp

Work backward from the output tube's grid requirements:
1. How much voltage swing does the output tube need at its grid for full power?
2. How much gain does the preamp need to provide that swing from the guitar signal (~100-500 mV peak)?
3. How many gain stages? Each 12AX7 triode provides ~35-60x voltage gain. One stage may be enough; two give you more gain and earlier breakup.
4. What tone shaping do you want between stages? (Passive tone stacks, coupling cap values, cathode bypass values.)

### 13.3 Then Design the Power Supply

1. What B+ voltage and current does the output stage need?
2. What heater current do all the tubes draw?
3. Choose a power transformer that provides these voltages with adequate current capacity.
4. Design the filter network for acceptable ripple.
5. Don't forget bleeder resistors.

### 13.4 Reference Designs

Before designing from scratch, study existing amps that are close to what you want:
- **Fender Champ (5F1):** Our build circuit. 5W, simple, single-ended.
- **Fender Deluxe (5E3):** 15W push-pull, the "next step" from the Champ.
- **Fender Princeton (5F2-A):** 5W with a tone control. Close to the Champ but with more voicing options.
- **Marshall 18 Watt:** Push-pull EL84, the "baby Marshall." A classic next build.
- **Vox AC15:** Push-pull EL84, the British counterpart to Fender.

Schematics for all of these are freely available online. Study them. Compare them. Understand why each designer made the choices they did.

---

## 14. Chapter Summary

This chapter covered the theoretical foundation of tube amplifier design:

1. **Vacuum tubes** are voltage-controlled current sources. The control grid modulates electron flow from cathode to plate.
2. **Triode parameters** (mu, rp, gm) are interrelated by μ = gm × rp and fully characterize the tube's small-signal behavior.
3. **Load line analysis** is the graphical tool for determining operating points, gain, and distortion character. Learn it — it's the most intuitive way to understand tube circuit behavior.
4. **Pentodes and beam-power tubes** offer higher gain and power than triodes, at the cost of higher output impedance.
5. **Single-ended Class A** is simple and harmonically rich (even harmonics), but limited in power.
6. **Push-pull Class AB** delivers more power and cancels even harmonics, but requires a phase splitter and matched tubes.
7. **Output transformers** are essential for matching the tube's high impedance to the speaker's low impedance. Their quality profoundly affects the sound.
8. **Cathode bias** is self-adjusting and forgiving. **Fixed bias** offers more power and control but requires maintenance.
9. **The "tube sound"** is a combination of soft clipping, even-order harmonics, transformer saturation, power supply sag, and speaker interaction.

Next up: the power supply that keeps all of this running — and the high voltages that make it dangerous.

---

*Next: [Chapter 21: Power Supply Design](21-power-supply-design.md)*
*Previous: [Chapter 19: Solid-State Amplifier Design](19-solid-state-amplifier-design.md)*
