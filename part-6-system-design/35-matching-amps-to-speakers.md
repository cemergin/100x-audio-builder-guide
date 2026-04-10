<!--
  CHAPTER: 35
  TITLE: Matching Amplifiers to Speakers
  PART: 6 — System Design & Integration
  PREREQS: Parts 2-3
  KEY_TOPICS: impedance compatibility, power requirements, sensitivity, damping factor, bi-amping, active vs passive systems, amp class selection
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 35: Matching Amplifiers to Speakers

> **Part 6 — System Design & Integration** | Prerequisites: Parts 2-3 | Difficulty: Intermediate

Welcome to Part 6. You have built speakers. You have built amplifiers. You have built a DSP crossover on a Raspberry Pi. You have designed PCBs and finished enclosures that look like they came from a factory. And now comes the question that should have been obvious from the start but somehow nobody asks until everything is already on the bench: will this amp actually work with this speaker?

The word "matching" makes people think of maximum power transfer from RF engineering, where you want the source impedance to equal the load impedance. Forget that entirely. In audio, matching an amp to a speaker is about *compatibility*: can the amplifier safely drive this speaker's impedance curve without overheating, clipping, or sounding terrible? Can it deliver enough power to hit your target volume in your room? And does its output impedance interact with the speaker in a way that helps or hurts the sound?

These are not hand-wavy questions. They have math behind them. And the math is surprisingly simple once you know what numbers matter. This chapter walks you through every calculation you need, ties it back to specific builds from earlier in this guide, and gives you a framework for any amp-speaker combination you will ever encounter.

### In This Chapter
- Impedance compatibility: nominal vs minimum impedance and what your amp can handle
- Power requirements: calculating watts from SPL targets, sensitivity, room size, and headroom
- Why sensitivity matters more than amp power
- Damping factor: what it is, when it matters, and when it does not
- Bi-amping and tri-amping: passive vs active approaches
- Active vs passive speaker systems
- Choosing the right amp class for the job
- Putting it together: matching specific builds from this guide

### Related Chapters
- [Ch 11: How Speakers Work](../part-2-speakers/11-how-speakers-work.md) — driver fundamentals and impedance behavior
- [Ch 18: How Amplifiers Work](../part-3-amplifiers/18-how-amplifiers-work.md) — amp classes, output impedance, damping factor theory
- [Ch 16: Build: Bookshelf Speakers](../part-2-speakers/16-build-bookshelf-speakers.md) — the home hi-fi speaker we will pair with amps
- [Ch 17: Build: Towers, PA, and Subwoofer](../part-2-speakers/17-build-towers-pa-subwoofer.md) — the PA speakers for the party system
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) — the LM3886 gainclone and TPA3255 Class D we built
- [Ch 29: Build: Active Crossover and Room Correction](../part-4-digital-audio/29-build-active-crossover-room-correction.md) — active crossover and room correction
- [Ch 36: Room Acoustics and Treatment](36-room-acoustics-treatment.md) — the room shapes your power requirements

---

## 1. Impedance Compatibility

### 1.1 What "4 Ohms" Actually Means

When a speaker says "4 ohms" on the back, that is the *nominal* impedance. It is not a fixed number -- it is more like an average that the manufacturer chose as a shorthand. The actual impedance of a speaker varies wildly with frequency. Go back to Chapter 12 and look at any impedance curve: there is a massive peak at the driver's resonant frequency (often 40-80 ohms for a woofer), a dip somewhere in the midrange, and a rising slope at high frequencies from the voice coil inductance.

The number that actually matters for your amplifier is the **minimum impedance**. A "4 ohm" speaker might dip to 3.2 ohms at some frequency. A "8 ohm" speaker might dip to 5.5 ohms. That minimum is what your amp has to handle, because that is where the amp has to deliver the most current for a given voltage.

Here is the math. Power delivered to a resistive load:

```
P = V² / R
```

If your amplifier outputs 30 Vrms into 8 ohms:

```
P = 30² / 8 = 900 / 8 = 112.5 W
```

Now the speaker impedance dips to 4 ohms. Same voltage:

```
P = 30² / 4 = 900 / 4 = 225 W
```

The amplifier has to deliver *twice the current* at the impedance dip. If the amp's power supply and output stage cannot source that current, you get one of three outcomes: clipping (audible distortion), thermal shutdown (the amp protects itself), or component failure (the amp does not protect itself).

### 1.2 Reading the Impedance Curve

Every speaker you build (and every commercial speaker worth considering) should come with a measured impedance curve. If you have been following this guide, you measured yours in Chapter 12 using REW and a sense resistor. Here is what to look for:

- **Minimum impedance magnitude**: The lowest point on the curve, usually somewhere in the midrange or just above the bass reflex tuning frequency. This is the number your amp must tolerate.
- **Impedance phase angle**: At frequencies where the impedance is partly reactive (capacitive or inductive), the amp has to deliver current that is out of phase with the voltage. This is harder than driving a pure resistance. An impedance of 4 ohms with a phase angle of -50 degrees is a nastier load than 4 ohms purely resistive.
- **EPDR (Equivalent Peak Dissipation Resistance)**: A modern metric that accounts for both magnitude and phase. An EPDR of 2 ohms means the amp sees current demands equivalent to a 2-ohm resistor at certain points in the music. Some demanding speakers (certain Magnepan models, Wilson speakers) have EPDR values below 2 ohms.

### 1.3 What Your Amp Can Handle

Different amplifier topologies have different current delivery capabilities:

| Amplifier | Min Rated Load | Notes |
|-----------|---------------|-------|
| LM3886 gainclone (Ch 23) | 4 ohms | Stable into 4 ohms, will deliver ~50W. Not rated for 2 ohms. |
| TPA3255 Class D (Ch 23) | 4 ohms | Rated for 4 ohms (2x 175W). Some boards handle 2 ohms in mono-bridged mode -- check the specific board. |
| Typical tube amp (10W) | 4, 8, or 16 ohms via output transformer taps | Must use correct tap. Wrong tap = bad damping, distortion, possible transformer damage. |
| Commercial Class D (e.g., ICEpower) | 2 ohms | Professional modules often rated to 2 ohms. |

**The rule**: your speaker's minimum impedance must be at or above your amplifier's minimum rated load impedance. If your speaker dips to 3.2 ohms and your amp is only rated for 4 ohms, you are in the danger zone. It might work at low volumes but clip or shut down during loud passages where the amp tries to deliver full current at that impedance dip.

> **What happens if... you connect a 2-ohm speaker to an amp rated for 4 ohms minimum?** At low volumes, it works. The amp just delivers more current than intended. As you turn up, the output transistors dissipate more heat than designed for (especially in Class AB, where the output devices handle the difference between supply voltage and speaker voltage as heat). With protection circuitry, the amp shuts down. Without it, the output transistors burn out. In a Class D amp, the MOSFET switches see higher peak currents and may exceed their safe operating area. Either way, you get smoke or silence.

### 1.4 Parallel Speakers Double the Problem

If you connect two 8-ohm speakers in parallel to one amp channel, the combined impedance is 4 ohms. Two 4-ohm speakers in parallel is 2 ohms. This matters for PA systems where you might daisy-chain multiple cabinets. Always calculate the total impedance your amp sees:

```
Z_total = 1 / (1/Z1 + 1/Z2 + ... + 1/Zn)
```

Two 8-ohm cabinets: Z_total = 4 ohms. Three 8-ohm cabinets: Z_total = 2.67 ohms. That third cabinet might push your amp below its minimum load.

---

## 2. Power Requirements

### 2.1 The SPL Budget

This is the most practical calculation in all of audio. You want to know: how many watts do I actually need? Here is the framework:

**Step 1: Define your target SPL at your listening position.**
- Casual home listening: 75-85 dB SPL
- Enthusiastic home listening: 85-95 dB SPL
- Home theater peaks: 105 dB SPL (THX reference)
- House party: 100-105 dB SPL
- Outdoor PA for 50-100 people: 105-110 dB SPL at 10 meters

**Step 2: Note your speaker's sensitivity.**
This is the SPL produced at 1 meter with 1 watt input (or equivalently, 2.83V into 8 ohms). Typical values:
- Small bookshelf speaker: 84-87 dB/W/m
- Large bookshelf / small tower: 87-90 dB/W/m
- PA speaker with horn: 95-100 dB/W/m
- Pro PA with large horn: 100-105 dB/W/m

**Step 3: Account for listening distance.**
SPL drops 6 dB every time you double the distance from the speaker (inverse square law in free field; less in a room due to reflections, but 6 dB is the conservative estimate).

```
Distance loss = 20 × log10(distance in meters / 1 meter)
```

At 3 meters: 20 × log10(3) = 9.5 dB loss
At 4 meters: 20 × log10(4) = 12 dB loss
At 10 meters: 20 × log10(10) = 20 dB loss

**Step 4: Add headroom.**
Music has peaks that are 10-20 dB above the average level (this is called crest factor). You need your amp to handle those peaks without clipping. Add 10 dB for typical music, 15-20 dB for very dynamic recordings (classical, jazz).

**Step 5: Do the math.**

```
Required power (dB above 1W) = Target SPL - Sensitivity + Distance Loss + Headroom
Required power (watts) = 10^(required dB / 10)
```

### 2.2 Worked Example: Home Hi-Fi

Let's calculate for the bookshelf speakers from Chapter 16, used in a 30 m² living room.

- Target SPL: 95 dB peaks (enthusiastic listening with some headroom)
- Speaker sensitivity: 87 dB/W/m
- Listening distance: 3 meters
- Headroom: 10 dB (for peaks in pop/rock music)

```
Required dB = 95 - 87 + 9.5 + 10 = 27.5 dB above 1 watt
Required power = 10^(27.5 / 10) = 10^2.75 = 562 watts
```

Wait -- 562 watts? For a living room? That seems insane.

Here is the thing: that 562 watts is the *peak* requirement for the loudest transient in the music. Your *average* power consumption during loud listening will be more like 5-20 watts. But the amp needs to be able to hit those peaks without clipping.

In practice, 100-200 watts per channel handles this scenario well, because:
1. Room gain adds 3-6 dB at lower frequencies (the room reinforces sound, unlike free field)
2. Two speakers playing together add ~3 dB
3. Most people never actually hit 95 dB peaks in their living room
4. Brief clipping on rare transients is not catastrophic

Our LM3886 gainclone from Chapter 23 delivers about 50W into 8 ohms or 68W into 4 ohms. For the 87 dB bookshelf speakers at 3 meters, that gives us:

```
SPL = 87 + 10 × log10(50) - 9.5 = 87 + 17 - 9.5 = 94.5 dB peak
```

That is plenty for a 30 m² room at normal-to-enthusiastic listening levels. You will only run out of headroom if you really crank it with dynamic music. For most people, 50 watts into a reasonably sensitive speaker is more than enough for home hi-fi.

### 2.3 Worked Example: Party PA

Now let's calculate for the PA speakers from Chapter 17 at an outdoor party for 80 people.

- Target SPL: 105 dB at the back of the crowd (10 meters away)
- Speaker sensitivity: 97 dB/W/m (horn-loaded top with compression driver)
- Listening distance: 10 meters
- Headroom: 6 dB (PA music is heavily compressed, less dynamic range)

```
Required dB = 105 - 97 + 20 + 6 = 34 dB above 1 watt
Required power = 10^(34 / 10) = 10^3.4 = 2,512 watts
```

That is per speaker, for peaks. This is why PA systems use serious power. Our TPA3255 Class D amp from Chapter 23 delivers about 175W per channel into 4 ohms. With two tops and two subs (each getting its own amp channel), you have 175W per driver. Let's see what that gives us at 10 meters:

```
SPL (top) = 97 + 10 × log10(175) - 20 = 97 + 22.4 - 20 = 99.4 dB
```

For outdoor use at 10 meters, you are short of the 105 dB target. Options: use more efficient speakers, add more speakers (stacking), use more powerful amps, or accept that you top out at 100 dB at the far edge of the crowd (which is still very loud -- people at 5 meters will be at 106 dB). For 50-80 people in a moderate space, this is workable. For a bigger event, you need more power or more speakers.

### 2.4 The Sensitivity Revelation

Here is the single most important insight in this chapter: **3 dB of speaker sensitivity is worth the same as doubling your amplifier power.**

Why? Because power in dB:

```
10 × log10(2) = 3.01 dB
```

Doubling power gives you exactly 3 dB more SPL. But going from an 87 dB speaker to a 90 dB speaker *also* gives you 3 dB more SPL -- at every power level, with every amp, forever.

Let's make this concrete:

| Speaker Sensitivity | Amp Power Needed for 95 dB at 3m |
|--------------------|---------------------------------|
| 84 dB/W/m | 1,122 W |
| 87 dB/W/m | 562 W |
| 90 dB/W/m | 282 W |
| 93 dB/W/m | 141 W |
| 96 dB/W/m | 71 W |

Going from 87 dB to 93 dB sensitivity cuts your power requirement by a factor of *four*. That is the difference between needing a massive power amp and being perfectly fine with a 50-watt gainclone.

This is why horn-loaded PA speakers (95-100+ dB sensitivity) can hit deafening levels with relatively modest amplifiers. And it is why tiny sealed bookshelf speakers (83-85 dB sensitivity) need beefy amps to play loud in a big room.

> **What happens if... you pair a very insensitive speaker (83 dB) with a low-power tube amp (8 watts)?** You get background music at best. At 3 meters: 83 + 9 - 9.5 = 82.5 dB maximum. That is about the level of a normal conversation. You will be turning the volume knob all the way up and still wishing for more. This combination only works in a near-field setup (1 meter or less) in a quiet room.

---

## 3. Damping Factor

### 3.1 What It Is

Damping factor is the ratio of the speaker's impedance to the amplifier's output impedance:

```
DF = Z_speaker / Z_output
```

A solid-state amp with an output impedance of 0.04 ohms driving an 8-ohm speaker:

```
DF = 8 / 0.04 = 200
```

A tube amp with an output impedance of 1 ohm driving the same speaker:

```
DF = 8 / 1 = 8
```

### 3.2 What It Does

When a speaker cone moves, it generates a voltage (back-EMF) -- the motor works in reverse. That voltage tries to push current back into the amplifier. If the amp has very low output impedance (high damping factor), this back-EMF current flows easily and the cone is quickly brought under control. The amp acts like a short circuit across the speaker, "braking" the cone.

If the amp has high output impedance (low damping factor), the back-EMF current cannot flow as easily, and the cone is free to ring or overshoot. This is most audible at the speaker's resonant frequency, where the cone *wants* to ring like a bell.

In practice:
- **Bass tightness**: High damping factor = tighter bass. The woofer cone stops when the signal says stop, rather than continuing to wobble.
- **Transient accuracy**: High damping factor = better transient response. Kick drums sound punchy instead of mushy.
- **Frequency response deviation**: Low damping factor means the speaker's impedance curve influences the frequency response. At frequencies where impedance is high (like the bass resonance peak), less power is delivered, creating a dip in response. At the impedance minimum, more power flows, creating a bump.

### 3.3 When It Matters and When It Does Not

**Damping factor matters most when:**
- You have a woofer in a sealed or ported box (bass reflex), especially sealed boxes where the driver's resonance is more exposed
- You listen to bass-heavy music where woofer control is critical
- Your speaker has a high-Q resonance that needs electrical damping

**Damping factor matters less when:**
- You are using horn-loaded speakers (the horn itself controls cone motion through acoustic loading)
- You are using PA speakers at high SPL (acoustic output dominates, back-EMF effects are relatively small)
- Your speaker already has very low Q (high mechanical damping)

**Here is the nuance most people miss:** damping factor is calculated at the speaker terminals, but between the amp and the speaker there is *cable*. A 10-meter run of 16 AWG speaker cable has about 0.26 ohms of resistance. If your amp has an output impedance of 0.04 ohms, the cable resistance dominates:

```
Effective DF = 8 / (0.04 + 0.26) = 8 / 0.30 = 26.7
```

Your damping factor of 200 just became 27 because of the cable. This is why for home hi-fi, keeping cable runs short and using adequate gauge matters more than obsessing over amplifier damping factor specs. And it is why the difference between an amp with DF = 200 and DF = 500 is completely irrelevant in practice -- the cable swamps both.

For tube amps with DF of 5-20, the cable resistance is a smaller fraction of the already-high output impedance, so it matters less.

> **What happens if... you use a tube amp with DF = 8 on a ported bookshelf speaker?** The bass around the port tuning frequency will be looser and more resonant than with a high-DF solid-state amp. Some people love this -- it adds a warmth and fullness that tube amp lovers swear by. Others find it muddy. Critically, some speakers are designed *expecting* a high damping factor (their Qtc is tuned for it). Running those on a tube amp changes the system Q and can make the bass response peaky or boomy.

### 3.4 Practical Recommendations

| Scenario | Minimum DF | Why |
|----------|-----------|-----|
| Sealed bookshelf, home hi-fi | 50+ | Controls resonance, tight bass |
| Ported tower, home hi-fi | 20+ | Less critical, port provides damping near tuning |
| PA horn-loaded top | 10+ | Horn acoustic loading dominates |
| PA subwoofer | 20+ | You want tight bass, but cable runs are long |
| Guitar cab with tube amp | 5-20 | Part of the sound; low DF is expected |

---

## 4. Bi-Amping and Tri-Amping

### 4.1 What and Why

In a typical speaker system, one amplifier drives all the drivers through a passive crossover network. The crossover splits the full-range signal, sending lows to the woofer and highs to the tweeter. This works. Millions of speakers do it this way. But it has limitations:

- The passive crossover sits between the amp and the drivers, adding resistance (insertion loss), inductors that store energy, and components that can interact with the amp's output impedance.
- One amp must deliver all the current for all drivers. A bass transient pulls current that could affect the tweeter signal (intermodulation).
- You cannot independently adjust the level of each driver without swapping crossover components.

**Bi-amping** uses two amplifier channels -- one for the woofer(s), one for the tweeter. **Tri-amping** adds a third for the midrange. This can be done two ways:

### 4.2 Passive Bi-Amping

The speaker keeps its passive crossover, but the woofer section and tweeter section have separate input terminals (many speakers have bi-wire/bi-amp terminals with removable jumpers). You connect one amp channel to the woofer terminals and another to the tweeter terminals.

**What you gain:**
- Each amp only drives one section, reducing intermodulation
- More total power available (two amps instead of one)
- Psychological satisfaction

**What you do not gain:**
- The passive crossover components are still in the signal path
- The crossover points and slopes are still fixed
- The frequency response is still determined by the passive crossover design

Honestly? Passive bi-amping is the least impactful upgrade you can make. The benefits are real but subtle. If your single amp has enough power, passive bi-amping will not transform your sound. Save your money for better speakers or room treatment.

### 4.3 Active Bi-Amping

This is where it gets interesting. You remove the passive crossover entirely. The crossover is done *before* the amplifiers, in the electronic domain -- either with an analog active crossover circuit or (much better) with DSP.

The signal chain becomes:

```
Source → DAC → DSP Crossover (CamillaDSP from Ch 29)
          ├── Low-pass filter → Amp 1 → Woofer
          └── High-pass filter → Amp 2 → Tweeter
```

**What you gain:**
- Eliminate passive crossover losses (0.5-2 dB of insertion loss, gone)
- Complete control over crossover frequency, slope, type (Butterworth, Linkwitz-Riley, etc.)
- Independent level adjustment per driver (just change a number in CamillaDSP)
- Independent EQ per driver
- Time alignment between drivers (add delay to the tweeter to align acoustic centers)
- The amplifiers drive the raw driver impedance, not the impedance modified by crossover components
- Lower distortion: each amp only reproduces its frequency band

**What you give up:**
- Simplicity (more amps, more cables, more configuration)
- You need a DSP crossover (but you built one in Chapter 29)
- Getting the crossover settings wrong can damage drivers (send full-range to a tweeter and it dies)

### 4.4 Active Tri-Amping

Same concept, three ways:

```
Source → DAC → DSP Crossover
          ├── Low-pass → Amp 1 → Woofer
          ├── Band-pass → Amp 2 → Midrange
          └── High-pass → Amp 3 → Tweeter
```

This is standard in professional PA systems and high-end home audio. Every driver gets exactly the frequencies it can reproduce, amplified by a dedicated amp optimized for that frequency range. The PA system we build in Chapter 39 uses this approach with a separate sub amp, top woofer amp, and compression driver amp.

### 4.5 A Warning About Active Crossovers and Tweeters

When you remove the passive crossover and wire an amp directly to a tweeter, there is no capacitor protecting the tweeter from low frequencies. If your DSP crashes, resets to default settings, or you accidentally load a wrong configuration, full-range signal goes straight to the tweeter. A tweeter can handle maybe 1/10 of a second of full-power bass before the voice coil smokes.

**Safeguards:**
1. Always put a small inline capacitor (non-polarized, film type) in series with the tweeter as a safety high-pass. Even a simple first-order at 2 kHz (for a 8-ohm tweeter: C = 1 / (2 * pi * 2000 * 8) = 10 uF) protects against catastrophe while being high enough in value to not significantly affect the DSP crossover above 3-4 kHz.
2. Test your DSP configuration at low volume before cranking it.
3. Save a known-good configuration as a fallback in CamillaDSP.

---

## 5. Active vs Passive Speaker Systems

### 5.1 The Passive Approach

A passive speaker contains drivers and a passive crossover network. You feed it with an external amplifier. This is the traditional approach and what we built in Chapters 16 and 17.

**Pros:**
- Simple: one cable from amp to speaker
- Flexible: use any amp you want, upgrade the amp without touching the speaker
- Familiar: most home speakers work this way
- Repairable: amp dies, replace just the amp

**Cons:**
- Passive crossover losses (0.5-2 dB)
- Crossover design compromises (covered in Chapter 13)
- One amp drives everything

### 5.2 The Active Approach

An active speaker contains its own amplifier(s) and an electronic crossover (analog or DSP). You feed it line-level signal. Think studio monitors (JBL LSR, Genelec, Adam Audio) or powered PA speakers (QSC, EV, JBL PRX).

**Pros:**
- Designer optimized the amp, crossover, and drivers as a system
- DSP crossover eliminates passive component limitations
- Built-in protection (limiter, thermal management)
- Often includes room correction or EQ presets
- Fewer external components (no amp rack, fewer cables)

**Cons:**
- Amp dies = whole speaker is down (harder to repair in the field)
- No amp choice or upgrade path
- Heat from the amp is inside the speaker enclosure (thermal management required)
- Heavier (speaker + amp in one box)
- More expensive per unit (but often cheaper than separate amp + passive speaker)

### 5.3 When to Go Active

**Go active when:**
- You want the best possible sound quality and are willing to configure DSP (home hi-fi with CamillaDSP)
- You are building a PA system where DSP processing is essential (limiter, crossover, delay)
- You want independent control over each driver
- You are building a studio monitor where accuracy matters

**Stay passive when:**
- You want simplicity (one amp, one cable, done)
- You already have a great amp you want to use
- You are building a guitar cabinet (the whole point is the amp's character into a passive speaker)
- You are building speakers for a non-technical user who does not want to configure DSP

### 5.4 The Hybrid Approach We Use in This Guide

For the home hi-fi system in Chapter 38, we use a hybrid approach:
- CamillaDSP on the Pi handles the active crossover and room correction
- Two channels of LM3886 gainclone amplification
- Speakers with no passive crossover (or a minimal safety high-pass on the tweeter)

This gives us all the benefits of active crossover design with separate, repairable, auditable amplifier hardware. It is more complex than a single-amp passive setup, but the sonic benefits are substantial and the configuration is done once.

---

## 6. Choosing the Right Amp Class

### 6.1 A Quick Class Review

We covered amp classes thoroughly in Chapter 18. Here is the practical summary for system design:

| Class | Efficiency | Sound Character | Heat | Weight | Best For |
|-------|-----------|----------------|------|--------|----------|
| Class A | 15-25% | Smooth, low crossover distortion | Extreme | Heavy | Ultra-fi headphone amps, small monitors |
| Class AB | 50-70% | Clean, well-understood | Moderate | Moderate | Home hi-fi, studio monitors |
| Class D | 85-95% | Clean (modern designs) | Minimal | Light | PA, subwoofers, anywhere efficiency matters |
| Tube (Class A/AB) | 15-50% | Rich harmonics, low DF | High | Heavy | Guitar amps, SET audiophile systems |

### 6.2 Class D for PA and Party (Power + Efficiency)

The math is simple. At a party, you need 200-600 watts of amplifier power. A Class AB amp at 60% efficiency delivering 400W dissipates about 267W as heat. That is a lot of heat sinks, a lot of weight, and a lot of power from the wall.

A Class D amp at 90% efficiency delivering 400W dissipates 44W as heat. It runs cooler, weighs less, and draws less power. For a battery-powered or generator-powered outdoor PA, this efficiency difference is the difference between running all night and running out of juice at midnight.

Our TPA3255 build from Chapter 23 is Class D. It runs barely warm while delivering 175W per channel. Two of these boards, four amp channels, powering a complete PA system. They fit in a 2U rack case. A Class AB equivalent would need a 4U case with massive heat sinks and a transformer the size of a brick.

### 6.3 Class AB for Home Hi-Fi (Perceived Quality)

The LM3886 gainclone from Chapter 23 is Class AB. At 50W into 8 ohms, it is more than enough for most home listening. Class AB remains the default recommendation for home hi-fi because:

- The technology is mature and well-understood
- Distortion characteristics are benign (primarily low-order harmonics)
- Output impedance is very low (high damping factor)
- It sounds "right" to most people -- transparent and controlled

Could you use a Class D amp for home hi-fi? Absolutely. Modern Class D amplifiers from manufacturers like Purifi, Hypex, and ICEpower measure as well as or better than the best Class AB designs. The old prejudice against Class D for critical listening is fading fast. But the LM3886 is easier to build, requires no specialized modules, and sounds excellent.

### 6.4 Tube for Guitar (Character)

We covered this in Part 1 and Part 3, but it bears repeating in the context of system design: tube amplifiers are not about fidelity. They are about *character*. The low damping factor, the soft clipping, the harmonic richness -- these are features when the goal is to make an electric guitar sound exciting.

You would not use a tube amp for a PA system (too heavy, too fragile, wrong distortion characteristics at high power). You would not typically use one for a reference monitoring system (too much coloration). But for a guitar rig running into a 1x12 cab? There is no substitute.

> **What happens if... you use a Class D amp for a guitar cabinet?** It works, and many modern guitar amps do exactly this (the Boss Katana, for example). But the distortion character is different -- Class D clips hard and ugly, not soft and musical like tubes. Guitar players using Class D amps typically rely on digital amp modeling (which simulates tube distortion in DSP) rather than driving the amp itself into clipping. The Class D just acts as a clean power stage.

---

## 7. Putting It All Together: Specific Build Pairings

### 7.1 Home Hi-Fi: LM3886 + Bookshelf Speakers (Ch 16)

- **Amp**: LM3886 gainclone, 50W/8 ohms, 68W/4 ohms
- **Speaker**: 2-way bookshelf, 87 dB/W/m sensitivity, 6 ohm nominal, 4.5 ohm minimum
- **Impedance check**: 4.5 ohm minimum > LM3886's 4 ohm minimum. Safe.
- **Power check at 3m**: 87 + 17 - 9.5 = 94.5 dB peak. Solid for a living room.
- **Damping factor**: LM3886 output impedance ~0.05 ohms. DF = 6/0.05 = 120. Excellent.
- **Verdict**: Well-matched for rooms up to 30-35 m². For bigger rooms or very dynamic listening, consider the active bi-amp path with two LM3886 boards.

### 7.2 Party PA: TPA3255 + PA Speakers (Ch 17)

- **Amp**: TPA3255 Class D, 175W/4 ohms per channel
- **Speakers**: Horn-loaded top (97 dB/W/m) + ported subwoofer (95 dB/W/m)
- **Impedance check**: Both speakers 4 ohms nominal. TPA3255 handles 4 ohms. One speaker per channel only -- do not parallel two cabs on one channel.
- **Power check at 10m**: Top: 97 + 22.4 - 20 = 99.4 dB. Sub adds reinforcement below 120 Hz.
- **Coverage**: For 50 people in a 200 m² space (indoor), adequate. For 100 people outdoor, consider adding a second pair of tops.
- **Verdict**: Efficient, lightweight, loud. The DSP handles crossover between subs and tops (Chapter 29), plus limiter protection. Two TPA3255 boards = four channels = two tops + two subs.

### 7.3 The Upgrade Path

Once your basic system is running, the upgrade path is clear:

1. **More sensitivity first**: Upgrade speakers before adding more amp power. A 3 dB sensitivity improvement is equivalent to doubling your amp power.
2. **Active crossover second**: Move from passive crossover to CamillaDSP active crossover. This is free if you already have the Pi running and an extra amp channel.
3. **More power third**: Add amplifier power only after you have maximized sensitivity and optimized the crossover.
4. **Room treatment alongside everything**: This is covered in Chapter 36, but it belongs in every upgrade path. Treating the room might give you a perceived improvement equivalent to doubling your entire system budget.

---

## 8. Summary

The amp-speaker matching process boils down to four questions:

1. **Can the amp drive the speaker's impedance?** Check minimum impedance against amp's minimum rated load.
2. **Is there enough power for the room?** Calculate from target SPL, sensitivity, distance, and headroom.
3. **Is the damping factor appropriate?** High DF for tight bass in hi-fi, less critical for PA and horns.
4. **What topology fits the application?** Class D for PA power and efficiency, Class AB for home hi-fi, tubes for guitar.

Answer those four questions and you will never have a mismatched system. In the next chapter, we will tackle the component that has the most influence on what you hear and the least to do with your electronics: the room.
