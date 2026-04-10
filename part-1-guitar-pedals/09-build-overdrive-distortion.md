<!--
  CHAPTER: 9
  TITLE: Build: Overdrive and Distortion
  PART: 1 — Guitar Pedals
  PREREQS: Chapter 8
  KEY_TOPICS: Tube Screamer clone, TS808, TS9, RAT clone, op-amp clipping, feedback clipping, shunt clipping, tone circuit, mid hump, diode swapping, modding pedals, LTspice comparison
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 9: Build: Overdrive and Distortion

> **Part 1 — Guitar Pedals** | Prerequisites: Chapter 8 | Difficulty: Intermediate

The fuzz you built in the last chapter is a blunt instrument — it takes your guitar signal and crushes it into a thick, saturated wall of harmonics. Beautiful for some things, but not exactly subtle. Now we're building two pedals that represent the more refined end of the gain spectrum: the Tube Screamer (overdrive) and the RAT (distortion).

These two pedals are worth studying side by side because they use the same basic ingredient — an op-amp and clipping diodes — but they arrange them differently, and the difference in arrangement produces dramatically different sounds. The Tube Screamer puts the clipping diodes in the op-amp's feedback loop (soft clipping). The RAT puts them after the op-amp output to ground (hard clipping). This is exactly the distinction we studied in [Chapter 6, section 3](06-gain-stages-clipping-circuits.md), and now you'll hear it with your own ears.

More importantly, once you've built both circuits, you'll have the knowledge to mod them. Want more bass in your Tube Screamer? Change one capacitor. Want softer clipping in your RAT? Move the diodes into the feedback loop. Want asymmetric harmonics? Mix diode types. Every mod we'll discuss maps directly to a circuit change you understand, not just a "try this" recipe from an internet forum.

### In This Chapter
- Build 3: Tube Screamer clone (TS808/TS9 topology)
- Build 4: RAT clone
- Circuit-level analysis of what makes these sound different
- LTspice comparison of both clipping topologies
- Modding guide: diode swaps, gain range, tone stack modifications
- Each mod explained by what it does electrically

### Related Chapters
- [Ch 5: How Effects Shape Sound](05-how-effects-shape-sound.md) — Clipping types, soft vs hard
- [Ch 6: Gain Stages and Clipping Circuits](06-gain-stages-clipping-circuits.md) — Op-amp stages, feedback vs shunt clipping, tone stacks
- [Ch 7: From Schematic to Breadboard](07-schematic-to-breadboard.md) — Debugging methodology
- [Ch 8: Build: Fuzz and Boost](08-build-fuzz-and-boost.md) — Previous builds, transistor gain stages
- [Ch 10: Build: Modulation and Time-Based Effects](10-build-modulation-time-effects.md) — Next build: tremolo and delay
- [Ch 31: PCB Design for Audio](../part-5-pcb-professional/31-pcb-design-for-audio.md) — PCB layout for these circuits
- [Ch 41: Costing and Pricing](../part-7-business/41-costing-pricing.md) — What these cost to build vs sell

---

## 1. Build 3: Tube Screamer Clone

### 1.1 The Circuit That Defined Overdrive

The Ibanez Tube Screamer (TS808 and TS9) is arguably the most cloned, modded, and discussed guitar pedal in history. Players like Stevie Ray Vaughan, John Mayer, and The Edge have made it central to their sound. Hundreds of boutique pedals are variations on its core circuit.

But here's the thing: the Tube Screamer circuit isn't complex. It's an op-amp gain stage with diodes in the feedback loop, sandwiched between an input buffer and an output buffer, with a simple tone control. About 20-25 components total. The genius is in the *specific component values* and how they shape the frequency response around the clipping stage.

The circuit breaks down into four stages:
1. **Input buffer** — isolates the guitar from the rest of the circuit
2. **Gain/clipping stage** — the heart of the overdrive sound
3. **Tone control** — a variable low-pass filter
4. **Output buffer** — drives the next pedal or amp

### 1.2 Stage-by-Stage Analysis

**Stage 1: Input Buffer**

```
  Input ── Cin (0.047uF) ── (+) Op-Amp A ── Output
                              |
                         (-) connected to output (unity gain)
```

A non-inverting buffer (voltage follower). Gain = 1. Its job is to present a high input impedance (~500k, set by the bias resistor) to the guitar so the pickup doesn't get loaded, and to provide a low output impedance to drive the clipping stage. The 0.047uF input coupling cap forms a high-pass filter with the input impedance — this is the first frequency-shaping element, and it rolls off bass below about 720 Hz.

**This bass rolloff is deliberate and critical.** It's a major part of why the Tube Screamer sounds "mid-humpy." Bass frequencies are reduced before they reach the clipping stage, so they stay relatively clean while the midrange gets distorted. The result: tight, focused overdrive without muddy bass.

**Stage 2: Gain/Clipping Stage**

```
              Drive pot (500k) + Rmin (4.7k)
         ┌──────/\/\/──────────────┐
         |                          |
         |    D1 (1N4148) →         |
         ├────|>|──────────────────┤
         |                          |
         |    ← D2 (1N4148)        |
         ├────|<|──────────────────┤
         |                          |
         |    Cf (0.047uF)         |
         ├────||───────────────────┤
         |                          |
  In ─── Ri (4.7k) ───┤(-) Op-Amp B (output)─── Out
                       |
                  ┌────┤(+)
                  |
                Vbias
```

This is the core. A non-inverting op-amp configuration where the feedback network contains:
- The **Drive pot** (500k) in series with a minimum resistor (4.7k): Sets the gain. At minimum drive, the gain is roughly 1 + 4.7k/4.7k = 2 (~6 dB). At maximum drive, the gain is roughly 1 + 504.7k/4.7k = 108 (~41 dB).
- **Clipping diodes** (two 1N4148 silicon diodes, anti-parallel): When the output exceeds ~0.6V, the diodes conduct and limit the output swing. Because they're in the feedback loop, the clipping is *soft* — the gain gradually reduces as the signal clips.
- **Cf** (0.047uF capacitor in the feedback): This is crucial. It creates a frequency-dependent gain: at low frequencies, the cap's impedance is high, so it doesn't affect the gain. At high frequencies, the cap's impedance drops, providing an alternative feedback path that reduces the gain. This means high frequencies are amplified *less* than midrange frequencies — another contributor to the mid-focused character.

**Stage 3: Tone Control**

```
  Input ──── R_tone (10k) ──┬── Tone pot (20k) ──┬── Output
                             |                     |
                           C_tone (0.22uF)        |
                             |                     |
                            GND              Mixing point
```

A simple variable low-pass filter. The Tone pot blends between the filtered signal (darker, with highs rolled off by the RC filter) and the unfiltered signal (brighter). Fully counterclockwise: dark, all highs removed. Fully clockwise: bright, the RC filter is bypassed.

**Stage 4: Output Buffer**

Another voltage follower, identical to the input buffer. Provides low output impedance to drive cables and downstream pedals.

### 1.3 Component List

| Reference | Value | Type | Notes |
|---|---|---|---|
| IC1 | JRC4558D (or NE5532, TL072) | Dual op-amp, DIP-8 | Both op-amps in one chip |
| D1, D2 | 1N4148 | Silicon signal diodes | Clipping pair |
| Cin | 0.047uF (47nF) | Film capacitor | Input coupling / bass cut |
| Cf | 0.047uF (47nF) | Film capacitor | Feedback — frequency shaping |
| Ri | 4.7 kohm | Metal film | Input to inverting input |
| Rmin | 4.7 kohm | Metal film | Minimum gain resistor |
| Drive | 500 kohm | Linear taper (B) pot | Gain control |
| R_tone | 10 kohm | Metal film | Tone filter resistor |
| C_tone | 0.22uF | Film capacitor | Tone filter cap |
| Tone | 20 kohm | Linear taper (B) pot | Tone control |
| R_vol | — | — | Volume pot: 100k Audio (A) taper |
| R_bias1, R_bias2 | 10 kohm each | Metal film | Vbias voltage divider |
| C_bias | 10uF | Electrolytic, 25V | Vbias bypass |
| Cout | 10uF | Electrolytic, 25V | Output coupling |
| C_pwr | 100uF + 100nF | Electrolytic + ceramic | Power supply decoupling |

**Total cost:** About $15-25 for all components.

### 1.4 Breadboard Build Steps

**Step 1: Power supply and biasing.**
- Connect 9V to + rail, GND to - rail.
- Install the Vbias divider: two 10k resistors from + rail to a row (Vbias), and from that row to - rail. Add the 10uF bypass cap from the Vbias row to GND. This creates your 4.5V reference.
- Install power decoupling: 100uF electrolytic + 100nF ceramic across the power rails.

**Step 2: Install the op-amp IC.**
Place the JRC4558D (or substitute) straddling the center gap of the breadboard. Pin 1 (identified by the notch or dot) should be at the top-left.

For a JRC4558D / NE5532 / TL072 (standard dual op-amp pinout):
```
        ┌──── notch ────┐
  Pin 1 │ 1-OUT    Vcc 8 │ Pin 8
  Pin 2 │ 1-IN(-)  2-OUT 7 │ Pin 7
  Pin 3 │ 1-IN(+)  2-IN(-) 6 │ Pin 6
  Pin 4 │ GND     2-IN(+) 5 │ Pin 5
        └───────────────┘
```

Connect Pin 8 to + rail (Vcc). Connect Pin 4 to - rail (GND).

**Step 3: Build the input buffer (Op-Amp A = pins 1, 2, 3).**
- Pin 3 (+): Connect through Cin (0.047uF) to the input jack. Also connect Pin 3 to Vbias through a 510k resistor (input bias — sets the input impedance).
- Pin 2 (-): Connect directly to Pin 1 (output). This creates the unity-gain buffer.
- Pin 1 (output): This is the buffer output, feeding the clipping stage.

**Step 4: Build the clipping stage (Op-Amp B = pins 5, 6, 7).**
- Pin 5 (+): Connect to Vbias.
- Pin 6 (-): Connect through Ri (4.7k) to the input buffer output (Pin 1).
- Feedback network (between Pin 7 and Pin 6): Install the Drive pot (500k) in series with Rmin (4.7k). In parallel with this series combination, install the two 1N4148 diodes (anti-parallel — one pointing each direction). Also in parallel, install Cf (0.047uF).
- Pin 7 (output): This is the clipping stage output.

For the breadboard, the feedback network can be built as follows:
- Row A: Connected to Pin 6
- Row B: Connected to Pin 7
- Drive pot: Lug 1 to Row A (through Rmin 4.7k), Lug 2 (wiper) to Row B. Or: use a 500k trim pot between Row A and Row B, with Rmin (4.7k) in series.
- D1: Anode in Row A, Cathode in Row B
- D2: Cathode in Row A, Anode in Row B (anti-parallel to D1)
- Cf: One lead in Row A, other in Row B

**Step 5: Build the tone control.**
- From Pin 7 (clipping output), connect R_tone (10k) to a new row.
- From that row, connect C_tone (0.22uF) to GND.
- Connect the Tone pot: Lug 1 to the row with C_tone junction, Lug 3 to Pin 7 (clipping output), Lug 2 (wiper) is the tone-controlled output.

**Step 6: Output stage.**
- Connect the Tone pot wiper through Cout (10uF) to the Volume pot.
- Volume pot: Lug 3 to Cout, Lug 2 (wiper) to output jack, Lug 1 to GND.

**Step 7: Audio connections.**
- Input jack tip to Cin
- Input jack ground to - rail
- Output jack tip to Volume pot wiper
- Output jack ground to - rail

### 1.5 Testing

**DC voltage checks:**

| Point | Expected |
|---|---|
| Pin 8 (Vcc) | ~9V |
| Pin 4 (GND) | 0V |
| Pin 3 (+, buffer) | ~4.5V (Vbias) |
| Pin 1 (buffer output) | ~4.5V |
| Pin 5 (+, clipping) | ~4.5V |
| Pin 6 (-. clipping) | ~4.5V |
| Pin 7 (clipping output) | ~4.5V |
| Vbias node | ~4.5V |

All the DC voltages in this circuit should be at approximately 4.5V (the virtual ground). If any op-amp output is at 0V or 9V, it's "railing" — check the feedback connections and bias.

**Audio test:**
1. Set Drive to about halfway, Tone to about halfway, Volume to about halfway.
2. Play a chord. You should hear a warm, mid-focused overdrive. Not as extreme as the fuzz — more like a slightly crunchy amp pushed into breakup.
3. Turn the Drive up. The overdrive gets more saturated, more compressed, with more sustain. At full drive, it should sound thick and singing.
4. Turn the Drive down. The overdrive cleans up. At minimum drive, it should be nearly clean — just a hint of warmth.
5. Sweep the Tone control. Full counterclockwise: dark, muffled. Full clockwise: bright, present, potentially harsh at high drive settings.

**The "mid hump" test:** Compare the Tube Screamer to your bypass signal (pull the input from the circuit and connect directly to the output). The TS should sound noticeably more midrange-focused. Bass will feel tighter (less boomy), and there will be a characteristic presence in the upper-midrange (1-3 kHz). This is the famous Tube Screamer voicing.

**Audio probe walkthrough:**
- Input jack: Clean guitar
- Pin 3 (buffer input): Clean guitar, bass slightly reduced (Cin filtering)
- Pin 1 (buffer output): Same — unity gain
- Pin 6 (clipping stage input): Same signal, fed through Ri
- Pin 7 (clipping stage output): Distorted! The overdrive character is here
- After tone control (pot wiper): Distorted, with treble shaped by the tone setting
- Output jack: Final signal

### 1.6 Understanding the "Mid Hump"

Now that you've heard it, let's trace exactly where the mid hump comes from:

1. **Cin (0.047uF) + input impedance (~510k):** HPF cutoff at ~6.8 Hz. This barely affects the audio range — most of the bass filtering happens later.

2. **The clipping stage's input network:** The 4.7k resistor (Ri) combined with the capacitor at the non-inverting input creates a high-pass characteristic. Frequencies below about 720 Hz see progressively less gain.

3. **Cf (0.047uF) in the feedback:** At high frequencies, this capacitor reduces the feedback impedance, reducing gain. The gain peaks in the midrange and rolls off above about 3-4 kHz.

4. **C_tone (0.22uF) + R_tone (10k):** The tone control further rolls off treble, with the pot setting determining how much.

The combined effect of items 2, 3, and 4: bass is reduced before clipping (so it stays clean and tight), mids are amplified and clipped (creating the overdrive character), and harsh high-frequency harmonics are filtered out after clipping (keeping it smooth). The peak gain — the "hump" — sits in the 700 Hz to 3 kHz range, which is exactly where the guitar lives in a band mix. This is why the Tube Screamer is such an effective "cut through the mix" pedal.

---

## 2. Build 4: RAT Clone

### 2.1 A Different Philosophy

The ProCo RAT (1978) takes the opposite approach to the Tube Screamer. Where the TS uses soft clipping and frequency shaping to create a smooth, mid-focused overdrive, the RAT uses hard clipping and relatively flat gain to create a raw, aggressive distortion.

The RAT circuit has three stages:
1. **Op-amp gain stage** — inverting configuration, very high gain available
2. **Hard clipping** — diodes to ground after the gain stage
3. **Filter control** — a variable low-pass filter (not a "tone" control in the traditional sense)

### 2.2 Stage-by-Stage Analysis

**Stage 1: Op-Amp Gain Stage**

```
         Rf (variable: Distortion pot 100k + fixed 47 ohm)
  ┌────/\/\/───────────┐
  |                     |
  |    Cf (3.3nF)      |
  ├────||──────────────┤
  |                     |
  Input ── Cin (0.022uF) ── Rin (1k) ──┤(-) Op-Amp (LM308 or OP07)
                                        |
                                   ┌────┤(+)
                                   |
                                 Vbias
```

An inverting gain stage. The Distortion pot (100k) is the feedback resistor — turning it up increases the gain. The gain range is approximately -Rf/Rin, from about -47 (the fixed 47 ohm minimum, giving about 0.05 gain — well, this doesn't quite work out. Actually the RAT uses a different arrangement):

Let me correct: The RAT's gain stage uses an inverting configuration with:
- Rin: 1 kohm (some versions use 2.2 kohm)
- Rf: Distortion pot (100 kohm log taper) — sets the feedback resistance
- Cf: A small capacitor (3.3nF in some versions, 30pF in others) across the feedback, rolling off gain at high frequencies

The gain ranges from about -1 (minimum distortion, Rf shorted by the pot) to about -100 (maximum distortion, full pot resistance). At maximum gain, the signal is amplified 100x before hitting the clipping diodes — far past the clipping threshold, creating heavy distortion.

The LM308 op-amp used in the original RAT has a relatively low slew rate (0.3 V/us) and low bandwidth. The compensation cap (Cf, 30pF in the original) further limits the bandwidth. This means the op-amp itself "softens" the high-frequency content of the distortion — a characteristic of the original RAT that is changed when you substitute a faster op-amp.

**Stage 2: Hard Clipping**

```
  Op-Amp Output ──┬── Resistor ──── Filter stage
                   |
                  D1 →     ← D2
                  |>| ──┬── |<|
                        |
                       GND
```

Two 1N4148 silicon diodes, anti-parallel, from the op-amp output to ground. Any signal above ~0.6V (positive or negative) is clamped. The amplified signal from the op-amp easily exceeds this threshold, so the diodes clip hard, creating a flat-topped waveform rich in harmonics.

**Stage 3: Filter Control**

```
  Clipped signal ── Filter pot (100k) ──┬── Output buffer
                                         |
                                       C_filter (3.3nF)
                                         |
                                        GND
```

A variable low-pass filter. Unlike the Tube Screamer's tone control (which mixes filtered and unfiltered signals), the RAT's filter is a true variable-cutoff LPF. Turning the Filter knob counterclockwise increases the resistance, which lowers the cutoff frequency, making the sound darker. Turning it clockwise decreases the resistance (higher cutoff), letting more high-frequency harmonics through — making the sound brighter and more aggressive.

### 2.3 Component List

| Reference | Value | Type | Notes |
|---|---|---|---|
| IC1 | LM308N (or OP07, TL071) | Single op-amp, DIP-8 | LM308 is "original"; OP07/TL071 are substitutes |
| D1, D2 | 1N4148 | Silicon signal diodes | Clipping pair (to ground) |
| Cin | 0.022uF (22nF) | Film capacitor | Input coupling |
| Rin | 1 kohm | Metal film | Input resistor (sets gain denominator) |
| Cf | 30pF (or 3.3nF — see note) | Ceramic capacitor | Feedback frequency shaping |
| Distortion | 100 kohm | Log taper (A) pot | Gain/distortion control |
| R_filter | 100 kohm | Log taper (A) pot | Filter/tone control |
| C_filter | 3.3nF | Film or ceramic | Filter capacitor |
| Volume | 100 kohm | Audio taper (A) pot | Output level |
| R_bias1, R_bias2 | 1 Mohm each | Metal film | Vbias divider |
| C_bias | 4.7uF | Electrolytic, 25V | Vbias bypass |
| Cout | 1uF | Film or electrolytic | Output coupling |
| C_pwr | 100uF + 100nF | Electrolytic + ceramic | Power decoupling |

**Note on Cf:** The original RAT uses a 30pF cap and an LM308 with specific compensation characteristics. If you're using an OP07 or TL071, a larger cap (3.3nF) may be needed to keep the circuit stable. Start with 30pF and increase if you hear oscillation.

**Total cost:** About $15-20 for all components.

### 2.4 Breadboard Build Steps

**Step 1: Power supply and biasing.** Same concept as the Tube Screamer, but the RAT uses higher-value bias resistors (1M each). Install the Vbias divider and bypass cap. Install power decoupling caps.

**Step 2: Install the op-amp.** The LM308N (or substitute) is a single op-amp in a DIP-8 package:
```
        ┌──── notch ────┐
  Pin 1 │ (comp)   Vcc 8 │ Pin 8  → NOT USED on most substitutes
  Pin 2 │ IN(-)   OUT  7 │ Pin 7
  Pin 3 │ IN(+)  GND  4 │ Pin 4  → Wait, check the specific pinout!
        └───────────────┘
```

**Important:** The LM308's pinout is different from a standard single op-amp (LM741, TL071). Pin 1 and 5 are compensation pins, and Pin 8 is not Vcc. Check the datasheet for your specific op-amp. For TL071 / OP07:
- Pin 2: IN(-)
- Pin 3: IN(+)
- Pin 4: V- (GND)
- Pin 6: Output
- Pin 7: V+ (Vcc)

Connect power pins to the appropriate rails.

**Step 3: Build the gain stage.**
- Pin 3 (+): Connect to Vbias.
- Pin 2 (-): Connect through Rin (1k) to the input (via Cin, 22nF, from the input jack).
- Feedback: Connect the Distortion pot (100k) between Pin 2 and Pin 6 (output). Add Cf (30pF) in parallel with the Distortion pot.

**Step 4: Clipping diodes.**
From Pin 6 (or Pin 7, depending on your op-amp — the output pin), install D1 and D2 anti-parallel to ground:
- D1: Anode to output pin row, cathode to GND rail
- D2: Cathode to output pin row, anode to GND rail

**Step 5: Filter control.**
From the clipped output, connect the Filter pot (100k): one outer lug to the clipped signal, wiper to the next stage, other outer lug through C_filter (3.3nF) to GND. (Or: series resistor pot with cap to ground.)

**Step 6: Output and volume.**
Volume pot: Connect the filtered output through Cout (1uF) to Lug 3 of the Volume pot. Wiper to output jack. Lug 1 to GND.

**Step 7: Audio connections.** Input jack, output jack, grounds — same as before.

### 2.5 Testing

**DC voltage checks:**

| Point | Expected |
|---|---|
| Op-amp output | ~4.5V (Vbias) |
| Pin 3 (+) | ~4.5V |
| Pin 2 (-) | ~4.5V (virtual ground, held by feedback) |
| Vcc pin | ~9V |
| GND pin | 0V |

**Audio test:**
1. Set Distortion to minimum, Filter to middle, Volume to middle.
2. Play. You should hear a clean or lightly driven signal. At minimum distortion, the RAT should be nearly clean.
3. Turn Distortion up to about 3 o'clock. You should hear a significant increase in distortion — more aggressive and raw than the Tube Screamer.
4. Turn Distortion to maximum. Heavy distortion — thick, compressed, lots of sustain.
5. Sweep the Filter control. Counterclockwise: dark, wooly, all the harsh harmonics are gone. Clockwise: bright, aggressive, buzzy — all the harmonics come through.

**Compare with the Tube Screamer:** Play the same riff through both pedals at similar gain settings. The differences should be immediately obvious:
- TS: Smoother, more compressed, mid-focused, cleans up with dynamics
- RAT: More aggressive, more "edge," flatter frequency response, less dynamic cleanup

This is soft clipping vs hard clipping. This is feedback vs shunt topology. You've read about it, simulated it, and now you're hearing it.

### 2.6 Stacking the TS and RAT

If you have both circuits on separate breadboards (or sequentially on the same breadboard), try stacking them — running one into the other. This is a time-honored technique among pedalboard players, and the results are instructive:

**Tube Screamer → RAT:**
The TS pre-shapes the signal with its mid hump before feeding it into the RAT. The RAT then distorts a mid-focused signal. Result: The RAT's distortion is tighter in the bass, more focused in the mids, and smoother in the treble than the RAT alone. Many players use this combination for "tight high-gain" tones. The TS at low drive acts as a mid-boost EQ pedal feeding the RAT's gain stage.

**RAT → Tube Screamer:**
The RAT provides aggressive, full-range distortion, and the TS then smooths it with soft clipping and its tone control. Result: A smoother, more compressed version of the RAT's character. The TS's soft clipping rounds off the RAT's sharp edges.

**Key insight from stacking:** The first pedal sets the *character* (which frequencies are emphasized, how the dynamics behave), and the second pedal modifies that character. This is gain staging in practice — and it's the same principle used inside multi-stage amplifiers, just spread across separate pedals.

> **What happens if...** you set the Tube Screamer to minimum drive and maximum volume, and feed it into the RAT? The TS acts as a clean mid-boost — no clipping, just the mid-hump EQ and 6-10 dB of gain. The RAT then receives a hotter, mid-focused signal. This is arguably the most common way to use a TS in practice: not as an overdrive, but as a clean boost and EQ in front of a higher-gain pedal. Now you know why it works — you're using the TS's frequency shaping (bass cut + mid emphasis) without its clipping.

### 2.7 If the RAT Doesn't Work

**Specific RAT debugging tips:**

**Oscillation at high distortion settings:** The RAT's high gain (up to 100x / 40dB) makes it prone to oscillation on a breadboard. If you hear a high-pitched squeal when the Distortion pot is turned up high:
- Add a 100nF ceramic cap directly across the op-amp's power pins (as close to the IC as possible)
- Try increasing Cf from 30pF to 100pF (this limits the high-frequency gain, reducing the tendency to oscillate)
- Shorten all jumper wires, especially the feedback path from the op-amp output to the inverting input

**No distortion at any setting:** Verify the diodes are connected from the output node to ground, not from the output to Vbias. Also verify the diodes are anti-parallel (one in each direction). If both diodes face the same way, only one polarity of the signal is clipped, and the other passes through — the distortion may be barely audible.

**Volume is very quiet:** Check the Filter pot wiring. If the filter pot and cap are wired so that the signal is being shunted entirely to ground (instead of partially), you'll lose most of the signal. The wiper of the Filter pot should connect to the next stage (volume control), not to ground.

---

## 3. Understanding the Differences at Circuit Level

### 3.1 Drawing Both Clipping Topologies

Let's put them side by side as simplified block diagrams:

**Tube Screamer (feedback clipping):**
```
  Input → [Buffer] → [Op-Amp with diodes IN feedback] → [Tone] → [Volume] → Output
                            ↕
                      (diodes limit the GAIN)
```

**RAT (shunt clipping):**
```
  Input → [Op-Amp with high gain] → [Diodes to GND] → [Filter] → [Volume] → Output
                                          ↕
                                    (diodes clip the OUTPUT)
```

The fundamental difference: in the Tube Screamer, the diodes are *inside* the feedback loop, so they affect the gain dynamically. As the signal tries to exceed the diode threshold, the diodes start conducting, which effectively reduces the gain. The output is self-limiting and the clipping is gradual.

In the RAT, the diodes are *outside* the feedback loop. The op-amp amplifies to full gain regardless of the signal level, producing a large signal. Then the diodes ruthlessly chop anything above their forward voltage. The clipping is abrupt and doesn't affect the gain stage's behavior at all.

### 3.2 Simulating Both in LTspice

Set up both circuits in LTspice and run a transient analysis with a 1 kHz, 50mV sine wave input:

**What you'll see:**

- **TS output waveform:** Smooth, rounded peaks. The transition from the linear part of the waveform to the clipped part is gradual — like a sine wave that's been gently compressed. The peak amplitude is about 0.5-0.6V (one diode drop above Vbias).

- **RAT output waveform:** Sharp, flat-topped peaks. The waveform rises linearly, hits the diode threshold (~0.6V above ground), and stops abruptly. The flat parts are truly flat — it looks like a clipped sine wave drawn with a ruler.

**Harmonic analysis (.four directive):**

Run a Fourier analysis on both outputs at 1 kHz. Compare the harmonic content:

| Harmonic | TS (feedback clipping) | RAT (shunt clipping) |
|---|---|---|
| Fundamental (1 kHz) | Strong | Strong |
| 3rd (3 kHz) | Moderate | Strong |
| 5th (5 kHz) | Weak | Moderate |
| 7th (7 kHz) | Very weak | Moderate |
| 9th (9 kHz) | Negligible | Weak |
| THD (total harmonic distortion) | ~10-20% | ~30-50% |

The RAT produces significantly more high-order harmonics because of the sharp transitions in its clipped waveform. The TS produces fewer harmonics because the rounded clipping contains less high-frequency energy. This is the mathematical basis for "the RAT sounds harsher" and "the TS sounds smoother."

### 3.3 Frequency Response Comparison

Run an AC analysis (.ac dec 200 20 20k) on both circuits:

- **TS:** The gain peaks in the 700 Hz to 3 kHz range (the "mid hump") and rolls off above and below. The frequency response looks like a bandpass filter centered on the upper midrange.

- **RAT:** The gain is relatively flat from 100 Hz up to the filter control's cutoff frequency. No inherent mid hump or bass cut (though the input cap does roll off sub-bass). The frequency response looks like a broad, flat-ish amplifier with a variable high-frequency cutoff.

This frequency response difference is equally as important as the clipping difference in defining their sound. Even if you made both circuits use the same clipping topology, they'd still sound different because of their different frequency shaping.

### 3.4 LTspice Simulation Walkthrough: Building Both Circuits

Here is a step-by-step guide to simulating both circuits in LTspice for direct comparison.

**Setting up the Tube Screamer clipping stage:**

1. Open LTspice. Create a new schematic.
2. Place an op-amp (use the "UniversalOpamp2" for simplicity, or download a JRC4558 model from the internet).
3. Power the op-amp: +9V on V+ pin, 0V on V- pin.
4. Create the Vbias: Two 10k resistors in series from +9V to GND. Tap the midpoint (4.5V) for the bias. Add a 10uF cap from the midpoint to ground.
5. Connect the non-inverting input (+) to Vbias.
6. Inverting input (-): Connect through a 4.7k resistor (Ri) to the signal source.
7. Feedback: From the output to the inverting input, place:
   - A 500k resistor (Rf, representing the Drive pot at maximum)
   - Two 1N4148 diodes (anti-parallel) — place one forward and one reverse between the same two nodes
   - A 47nF capacitor (Cf) — also between the same two nodes
8. Signal source: A voltage source with `SINE(4.5 0.1 1000)` — this creates a 100mV amplitude sine wave at 1 kHz, centered on the 4.5V bias. Connect through a 47nF input coupling cap.
9. Output: Tap the op-amp output node.

**Setting up the RAT clipping stage (on the same schematic, offset to the right):**

1. Place a second op-amp.
2. Same power supply and Vbias.
3. Non-inverting input (+) to Vbias.
4. Inverting input (-): Connect through a 1k resistor (Rin) to the same signal source (or a copy).
5. Feedback: A 100k resistor from output to inverting input. In parallel, a 30pF capacitor.
6. Clipping: From the output node, place two 1N4148 diodes anti-parallel to ground.
7. Output: Tap the node between the op-amp output and the diodes.

**Simulation commands:**

Add these directives to your schematic:

```
.tran 10m
.four 1000 V(ts_out) V(rat_out)
```

The `.tran 10m` runs a 10ms transient simulation. The `.four` directive calculates the Fourier components at 1 kHz for both outputs.

**What to observe in the waveform viewer:**

1. Plot both output voltages on the same graph. The TS output will show smooth, rounded peaks staying close to 4.5V +/- 0.6V (one diode drop). The RAT output will show sharp, flat-topped peaks at exactly 4.5V +/- 0.6V above/below ground (where the shunt diodes clip).

2. Right-click on the waveform plot and select "View → FFT." This shows the frequency spectrum. Compare the harmonic content: the RAT's spectrum will have stronger peaks at the 3rd, 5th, 7th, and 9th harmonics.

3. Now try this: change the TS's Rf from 500k to 50k (lower drive). The clipping becomes less severe — the peaks are still rounded but less compressed. Run the FFT again — the harmonic content drops. The TS at low drive is almost clean.

4. Try the same with the RAT: change Rf from 100k to 10k. The gain drops, and the signal may no longer exceed the diode threshold at all. The RAT at low distortion is *clean* — there's an abrupt transition from "clean" to "distorted" as you increase the pot. The TS has a smooth, gradual transition.

**Save this simulation.** You'll reference it when modding these circuits on the breadboard — simulate the change first, predict the effect, then verify with your ears.

### 3.5 Real-World vs Simulation

A few things you'll notice when building these that the simulation doesn't fully capture:

- **Noise:** Real circuits have noise from resistors (thermal noise), transistors/op-amps (shot noise), and external sources (mains hum, RF interference). The simulation is silent in comparison.
- **Component tolerance:** Your 4.7k resistor might actually be 4.6k or 4.8k. This matters less for pedals (the tone difference is inaudible) than for precision circuits.
- **Power supply sag:** A 9V battery under load doesn't deliver exactly 9V — it might be 8.5V, dropping further as the battery ages. This changes the headroom and bias points.
- **Temperature:** Semiconductor properties drift with temperature. The simulation runs at a fixed 27 degrees C by default.

None of these invalidate the simulation — it's still an excellent prediction tool. But they explain why your breadboard might sound slightly different from your SPICE model.

---

## 4. Modding for Different Voices

This is where it gets creative. Each mod is a single component change with a clear electrical explanation.

### 4.1 Diode Swaps (Both Pedals)

The simplest and most dramatic mod: change the clipping diodes.

**Silicon (1N4148) → LED (any color):**

| Parameter | Silicon | LED |
|---|---|---|
| Forward voltage | ~0.6V | ~1.8-2.2V |
| Clipping threshold | Low | High |
| Headroom | Less | More |
| Sound | More distorted, earlier onset | More open, later onset, "louder clean" |

In the TS: LED clipping gives a more "transparent" overdrive — the guitar's natural tone comes through more because the signal has more room to swing before clipping. The distortion is less compressed.

In the RAT: LED clipping gives a more "crunchy" distortion — the signal clips at a higher voltage, so the flat parts of the waveform are at a higher level. More output volume available.

**Silicon → Germanium (1N34A, OA91):**

| Parameter | Silicon | Germanium |
|---|---|---|
| Forward voltage | ~0.6V | ~0.3V |
| Clipping threshold | Moderate | Low |
| Headroom | More | Less |
| Sound | Standard | Softer, more "vintage," earlier onset |

Germanium diodes have a softer knee — the transition from non-conducting to conducting is more gradual than silicon. This softens the clipping further, especially in the RAT where the shunt clipping is normally abrupt. Some builders say germanium diodes in a RAT make it sound more "tube-like."

**Asymmetric: Silicon + LED (one each direction):**
One direction clips at 0.6V, the other at ~1.8V. The positive and negative halves of the waveform are distorted differently. This adds even-order harmonics (see [Chapter 6, section 4](06-gain-stages-clipping-circuits.md)). The sound is warmer and more complex than symmetric clipping.

**Asymmetric: 1x Silicon + 2x Silicon:**
One direction: one diode (clips at 0.6V). Other direction: two diodes in series (clips at 1.2V). More headroom in one direction, more clipping in the other. Subtle asymmetry with lower even harmonics than the silicon/LED combination.

### 4.2 Gain Range Modifications

**Tube Screamer — increase maximum gain:**
Replace the Drive pot (500k) with a 1M pot. Maximum gain roughly doubles. Alternatively, decrease Rmin (4.7k) to 2.2k — this increases the maximum gain and also increases the *minimum* gain (the pedal never gets fully clean).

**Tube Screamer — decrease minimum gain (true clean at minimum drive):**
Increase Rmin to 10k. At minimum drive, the gain is closer to unity, and the pedal is essentially bypassed (but buffered). This gives a wider usable range on the Drive knob.

**RAT — change the distortion range:**
Replace the Distortion pot (100k) with a higher value (250k, 500k) for more maximum gain. Or replace Rin (1k) with a higher value (2.2k, 4.7k) for less gain at all settings.

### 4.3 Tone/Filter Modifications

**Tube Screamer — bass boost mod:**
Change Cin from 0.047uF to 0.1uF or 0.22uF. More bass enters the clipping stage. The pedal loses some of its "tight" character but gains a fatter, fuller bottom end. This is one of the most popular TS mods.

**Tube Screamer — change the mid hump:**
The mid hump frequency is determined by the RC network in the clipping stage. Changing Cf from 0.047uF to 0.022uF shifts the gain peak higher in frequency — the mid hump becomes an "upper-mid hump." The sound is more aggressive and nasal. Changing Cf to 0.1uF shifts the peak lower — the sound becomes thicker and warmer.

**RAT — change filter range:**
Increase C_filter from 3.3nF to 10nF: The filter becomes darker overall. Even at full clockwise, some treble is rolled off. The RAT sounds warmer and less abrasive.

Decrease C_filter to 1nF: The filter barely affects the treble. The RAT is brighter at all settings. Better for players who always run the filter fully clockwise anyway.

**RAT — add a "flat" mid control:**
Add a second variable filter in series: a pot (50k) with a cap (10nF-47nF) to ground, between the clipping diodes and the existing filter. This creates a crude parametric mid control. This is not a standard mod, but it illustrates how adding a simple RC filter stage changes the frequency balance — the same principle applies whether you're modding a RAT or designing an original pedal.

### 4.4 Topology Swap: The Ultimate Experiment

Here's the most educational mod of all: **move the RAT's clipping diodes into the feedback loop**, converting it from shunt clipping to feedback clipping.

Instead of diodes from the op-amp output to ground, place them in parallel with the Distortion pot (across the feedback network), exactly like the Tube Screamer.

What happens? The RAT's character changes dramatically — it becomes smoother, more compressed, and more dynamic. It loses some of its aggressive edge. You've essentially turned a RAT into something that behaves like a Tube Screamer (but with the RAT's flat frequency response and higher gain range).

Now do the reverse: take your TS clone and **move the clipping diodes from the feedback loop to ground** after the output. The TS becomes harsher, more aggressive, with less dynamic response. You've turned a Tube Screamer into something that behaves like a RAT (but with the TS's mid-humped frequency response).

These experiments prove that the clipping topology and the frequency shaping are independent dimensions of pedal design. You can combine any clipping topology with any frequency response to create different flavors.

> **What happens if...** you remove the clipping diodes entirely from the Tube Screamer? Without diodes, the op-amp amplifies the signal cleanly — no clipping (until the signal hits the op-amp's output voltage limit, which is close to the supply rails). With the Drive pot at maximum, you get up to 41 dB of clean gain. The TS becomes a clean boost with the mid-humped frequency response. Some players actually run a TS with the diodes lifted as a mid-boost to push their amp into natural distortion. This is called the "mid boost mod" or "no-clipping mod."

---

## 5. Building Confidence: From Clone to Original

### 5.1 You Now Have a Design Vocabulary

After building and modding these two pedals, you have a practical vocabulary of pedal design:

- **Gain stage topology** (inverting vs non-inverting, transistor vs op-amp)
- **Clipping topology** (feedback vs shunt)
- **Clipping element** (silicon, germanium, LED, MOSFET, symmetric, asymmetric)
- **Frequency shaping** (pre-emphasis, de-emphasis, mid hump, flat)
- **Tone control** (variable LPF, blending network)

Any commercial overdrive or distortion pedal is a specific combination of choices from these categories. The Klon Centaur? Non-inverting gain stage + germanium/silicon asymmetric clipping + clean blend + Baxandall-ish tone. The Boss DS-1? Non-inverting gain + transistor shunt clipping + mid-scoop tone stack. The Fulltone OCD? MOSFET gain stage + MOSFET clipping + toggle between two voicings.

### 5.2 Designing Your Own Overdrive

You have enough knowledge now to design an original overdrive. Here's a framework:

1. **Choose your gain stage:** Non-inverting op-amp is the safe default. Inverting if you want the virtual-ground input characteristics.
2. **Choose your clipping:** Feedback for smooth, shunt for aggressive. Or both (some pedals have switchable topologies).
3. **Choose your diodes:** Silicon for standard, LED for more headroom, germanium for softer, asymmetric for warmth.
4. **Choose your frequency shaping:** Add pre-emphasis (bass cut before clipping) for a tight, focused sound. Leave it flat for a full-range sound. Add a mid-boost cap in the feedback for a specific voicing.
5. **Choose your tone control:** Simple variable LPF, blending network, or a full Baxandall.

Prototype on your breadboard. Swap components. Listen. The breadboard is your laboratory.

### 5.3 What's Next

In [Chapter 10](10-build-modulation-time-effects.md), we leave the world of gain and clipping and enter time-based and modulation effects: a tremolo (amplitude modulation with an LFO) and a PT2399 delay. These use completely different circuit building blocks — oscillators, voltage-controlled amplifiers, and delay chips — and they'll round out your pedal design toolkit.

When you're ready to take these breadboard pedals off the bench and into an enclosure, [Part 5](../part-5-pcb-professional/31-pcb-design-for-audio.md) covers PCB layout and enclosure design. And if you want to sell them, [Part 7](../part-7-business/40-sourcing-supply-chain.md) covers sourcing, costing, and building a brand.

---

## Chapter Summary

- The **Tube Screamer** uses a non-inverting op-amp stage with clipping diodes in the feedback loop (soft clipping). The 0.047uF input cap and 0.047uF feedback cap create the "mid hump" by shaping the gain to peak in the 700 Hz - 3 kHz range. The tone control is a variable low-pass filter.

- The **RAT** uses an inverting op-amp stage with high gain, followed by clipping diodes to ground (hard clipping). The frequency response is relatively flat, and the Filter control is a variable low-pass filter that controls how much high-frequency harmonic content reaches the output.

- **Feedback clipping** (TS): soft, gradual, dynamic, less THD, smoother character. **Shunt clipping** (RAT): hard, abrupt, less dynamic, more THD, more aggressive character. These differences are visible in LTspice waveforms and Fourier analysis.

- **Diode swaps** change the clipping threshold and character: LEDs give more headroom and transparency, germanium gives softer onset and "vintage" character, asymmetric combinations add even harmonics for warmth.

- **Frequency shaping mods** are equally important as clipping mods: changing Cin affects bass content, changing Cf shifts the mid hump, changing tone/filter cap values change the overall brightness.

- **Topology swaps** (moving diodes from feedback to shunt or vice versa) prove that clipping topology and frequency shaping are independent design choices that can be combined in any configuration.

- **Estimated cost:** $40-70 total for both builds. Individual builds are $15-25 each.

---

*Next: [Chapter 10 — Build: Modulation and Time-Based Effects](10-build-modulation-time-effects.md), where we build a tremolo and a PT2399 delay, combine them into a chorus, and enter the world of LFOs and time-domain effects.*
