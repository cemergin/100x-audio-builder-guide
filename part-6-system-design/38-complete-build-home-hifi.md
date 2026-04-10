<!--
  CHAPTER: 38
  TITLE: Complete System Build: Home Hi-Fi
  PART: 6 — System Design & Integration
  PREREQS: Chapter 37
  KEY_TOPICS: complete signal path, Pi streamer, DAC, CamillaDSP active crossover, LM3886 gainclone, bookshelf speakers, preamplifier, cable construction, system grounding, power conditioning, room setup, integration walkthrough
  DIFFICULTY: Advanced
  UPDATED: 2026-04-10
-->

# Chapter 38: Complete System Build: Home Hi-Fi

> **Part 6 — System Design & Integration** | Prerequisites: Chapter 37 | Difficulty: Advanced

This is it. The capstone. Every wire you learned to solder in Part 0, every pedal circuit that taught you about gain staging, every speaker driver you modeled and mounted, every amplifier you biased and tested, every line of CamillaDSP configuration you wrote, every PCB you designed and etched -- all of it converges here, in a complete home hi-fi system built entirely from components you made with your own hands.

We are going to trace the complete signal path from a Spotify stream to your eardrums. Every component. Every connection. Every potential failure point. Not just "connect A to B" -- we will discuss *why* each connection works the way it does, what can go wrong, and how to verify that each stage is doing its job. This is system engineering, not assembly instructions.

The system we build in this chapter is opinionated. It uses an active crossover with DSP because the benefits are substantial and you already built the hardware. But we will also cover the simpler passive crossover path for those who want less complexity. Either way, the signal path is the same from source to amplifier -- only the crossover implementation differs.

### In This Chapter
- The complete signal chain: source to ear
- Two system architectures: active crossover vs passive crossover
- Pi streamer and DAC setup
- CamillaDSP: active crossover + room correction
- Preamplifier options: passive, active, and DSP volume
- LM3886 gainclone amplification
- 2-way bookshelf speakers (or floor-standing towers)
- Cable design and construction (interconnects and speaker cable)
- System-level grounding and eliminating hum
- Power conditioning: what matters and what does not
- Room setup: speaker placement and acoustic treatment
- The integration walkthrough: connect, calibrate, verify, listen

### Related Chapters
- [Ch 16: Build: Bookshelf Speakers](../part-2-speakers/16-build-bookshelf-speakers.md) — the speakers
- [Ch 17: Build: Towers, PA, and Subwoofer](../part-2-speakers/17-build-towers-pa-subwoofer.md) — floor-standing tower alternative
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) — the LM3886 gainclone
- [Ch 28: Build: Pi Streamer with Physical Controls](../part-4-digital-audio/28-build-pi-streamer.md) — the source
- [Ch 29: Build: Active Crossover and Room Correction](../part-4-digital-audio/29-build-active-crossover-room-correction.md) — crossover and room correction
- [Ch 30: Build: The Full Control Hub](../part-4-digital-audio/30-build-full-control-hub.md) — system control and automation
- [Ch 33: Professional Finishing and Aesthetics](../part-5-pcb-professional/33-professional-finishing-aesthetics.md) — enclosure and finish quality
- [Ch 34: Testing, Quality Control, and Documentation](../part-5-pcb-professional/34-testing-qc-documentation.md) — systematic verification
- [Ch 35: Matching Amps to Speakers](35-matching-amps-to-speakers.md) — amp-speaker compatibility verification
- [Ch 36: Room Acoustics and Treatment](36-room-acoustics-treatment.md) — room setup
- [Ch 37: System Tuning and Measurement](37-system-tuning-measurement.md) — final tuning
- [Ch 39: Complete Build: Party PA](39-complete-build-party-pa.md) — the other system

---

## 1. The Signal Chain: Overview

### 1.1 Active Crossover Path (Recommended)

This is the full-featured system. Every stage is a component you built in this guide:

```
Spotify / Tidal / local files (phone, laptop, or NAS)
  │
  ▼
Raspberry Pi Streamer (Ch 28)
  │ — runs Volumio, moOde, or similar
  │ — receives audio via AirPlay, Spotify Connect, DLNA, or local playback
  │
  ▼
DAC HAT on the Pi (Ch 28)
  │ — PCM5122 or ES9038Q2M based HAT
  │ — converts digital audio to analog line-level signal
  │ — output: ~2 Vrms line level, RCA or 3.5mm
  │
  ▼
CamillaDSP running on the Pi (Ch 29)
  │ — volume control (digital domain)
  │ — active crossover: Linkwitz-Riley 4th order at 2.5 kHz
  │ — room correction EQ (parametric or FIR, from Ch 37 measurements)
  │ — time alignment delay on tweeter channel
  │ — output: 4 channels (left woofer, left tweeter, right woofer, right tweeter)
  │
  ▼
Multi-channel DAC (USB audio interface or 4-channel DAC HAT)
  │ — converts 4 DSP output channels to analog
  │ — output: 4 analog line-level signals
  │
  ▼
LM3886 Gainclone Amps × 2 (Ch 23)
  │ — each board has 2 channels
  │ — Amp 1: left woofer + right woofer
  │ — Amp 2: left tweeter + right tweeter
  │ — output: ~50W/channel into 8 ohms
  │
  ▼
2-Way Bookshelf Speakers × 2 (Ch 16)
  │ — NO passive crossover (removed or bypassed)
  │ — woofer terminal connected directly to woofer amp
  │ — tweeter terminal connected to tweeter amp
  │ — safety capacitor in series with tweeter (10 µF, non-polarized)
  │
  ▼
Your Ears
  — via treated room (Ch 36)
```

### 1.2 Passive Crossover Path (Simpler)

If you want a simpler system with fewer components:

```
Spotify / Tidal / local files
  │
  ▼
Raspberry Pi Streamer (Ch 28)
  │
  ▼
DAC HAT on the Pi (Ch 28)
  │
  ▼
CamillaDSP (Ch 29)
  │ — volume control
  │ — room correction EQ (optional but recommended)
  │ — stereo output (2 channels)
  │
  ▼
LM3886 Gainclone Amp × 1 (Ch 23)
  │ — 2 channels: left + right
  │ — output: ~50W/channel into 8 ohms
  │
  ▼
2-Way Bookshelf Speakers × 2 (Ch 16)
  │ — WITH passive crossover (as built in Ch 13/16)
  │ — single pair of binding posts per speaker
  │
  ▼
Your Ears
```

This path uses one amp board, two channels, and keeps the passive crossover in the speakers. It is simpler, cheaper, and still sounds very good -- especially with CamillaDSP room correction on top.

### 1.3 Which Path to Choose

**Choose the active path if:**
- You enjoy configuring DSP and want maximum control
- You want the best possible performance from your speakers
- You already have or plan to build two LM3886 amp boards
- You want to experiment with crossover settings without resoldering components

**Choose the passive path if:**
- You want simplicity and reliability
- You built the speakers with a well-designed passive crossover and are happy with them
- You only have one amp board
- You want to hand this system to a family member who will not tweak DSP settings

Both paths are valid. The active path is about 15-20% "better" by measurement (lower distortion at crossover, flatter response, better time alignment). Whether that 15-20% matters to you depends on your ears and your tolerance for complexity.

---

## 2. The Source: Pi Streamer and DAC

### 2.1 The Raspberry Pi Streamer (Ch 28)

Your Pi streamer from Chapter 28 is the source for this entire system. It runs one of the dedicated audio distributions:

- **Volumio**: Most polished UI, good Spotify Connect and AirPlay support, free tier is functional
- **moOde Audio**: More configurable, better CamillaDSP integration, completely free
- **Raspberry Pi OS + custom**: Maximum control, you set up everything manually

The Pi receives audio from your phone, laptop, or NAS over the network. It does not store music locally (though it can if you attach USB storage). Think of it as a headless audio computer -- no screen, no keyboard, controlled via a web interface from your phone.

### 2.2 The DAC HAT

The DAC (Digital-to-Analog Converter) HAT sits on top of the Pi and converts the digital audio stream to an analog signal. We covered the build in Chapter 28. The two common options:

**PCM5122-based HATs** (HiFiBerry DAC+, IQaudio DAC+):
- 32-bit, up to 384 kHz
- Excellent measured performance (THD+N < 0.001%)
- Built-in hardware volume control
- ~$30-50

**ES9038Q2M-based HATs**:
- Even lower measured distortion
- Slightly more complex power supply requirements
- ~$50-80

Either one is overkill for a home system. The DAC is not the bottleneck -- the room and speakers are. Pick whichever you already built.

### 2.3 For the Active Path: Multi-Channel Output

The active crossover system needs four analog output channels. The Pi's single stereo DAC HAT only provides two. Options:

**Option A: USB multi-channel audio interface**
Connect a USB audio interface (like the Behringer UMC404HD, ~$100) to the Pi. CamillaDSP outputs to the USB interface's four channels. Each channel goes to one amp input. This is the most flexible approach and gives you four balanced (TRS) outputs.

**Option B: Two stereo DAC HATs (with I2S multiplexing)**
Some Pi audio setups support two I2S DAC HATs or a quad-channel DAC board. This keeps everything on the Pi without a USB interface. More compact but less common.

**Option C: HDMI to multi-channel DAC**
The Pi's HDMI output supports up to 8 channels of audio. A multi-channel HDMI audio extractor can split this into analog channels. Inexpensive but adds another box.

For most builders, **Option A** (USB audio interface) is the pragmatic choice. The Behringer UMC404HD is widely available, well-supported on Linux, and provides clean balanced outputs.

> **What happens if... your USB audio interface has higher latency than expected?** CamillaDSP adds its own processing latency (typically 5-20 ms depending on buffer settings), and the USB interface adds another 5-10 ms. Total latency might be 15-30 ms. For pure music listening, this is completely irrelevant -- you will never notice it. For video playback, 30 ms of audio delay can cause lip-sync issues. Most video players can compensate with an audio delay setting. If you also use this system for TV/movies, set a negative audio delay in your video player equal to the measured system latency.

---

## 3. CamillaDSP: The Brain of the System

### 3.1 Active Crossover Configuration

CamillaDSP (Chapter 29) runs on the Pi and processes the audio in real-time. For the active crossover path, the configuration includes:

**Input**: Stereo audio from the Pi's audio pipeline (ALSA capture device)

**Processing chain (per channel):**
1. **Volume control**: Software volume with dithering to minimize quantization noise at low levels
2. **Room correction EQ**: Parametric filters from your Chapter 37 measurements
3. **Crossover split**: 
   - Low-pass filter → woofer output (Linkwitz-Riley 4th order, 24 dB/octave, at 2.5 kHz)
   - High-pass filter → tweeter output (Linkwitz-Riley 4th order, 24 dB/octave, at 2.5 kHz)
4. **Level adjustment**: Per-driver gain trim (the tweeter is usually more sensitive than the woofer; attenuate it by 2-4 dB to match)
5. **Time alignment**: Delay on the tweeter channel (the tweeter is usually physically in front of the woofer; add delay to align their acoustic output in time). Typical delay: 0.1-0.3 ms (about 3-10 cm of acoustic offset).

**Output**: Four channels to the USB audio interface (or multi-channel DAC)

### 3.2 Why Linkwitz-Riley?

The Linkwitz-Riley (LR) crossover topology is the gold standard for active crossovers because the low-pass and high-pass outputs sum to exactly unity (flat) at all frequencies when the drivers are time-aligned. A Butterworth crossover creates a +3 dB bump at the crossover frequency when both drivers sum. A Bessel crossover does not sum flat either.

LR4 (4th order, 24 dB/octave) is the most common choice:
- Steep enough to protect the tweeter from low frequencies
- Both outputs are in-phase at the crossover point (no polarity inversion needed)
- Provides good power handling distribution (the woofer is not asked to reproduce frequencies far above its comfortable range)

### 3.3 Room Correction EQ

From your measurement session (Chapter 37), you identified room mode peaks and overall tonal deviations from your target curve. These corrections are loaded into CamillaDSP as parametric EQ filters:

```yaml
# Example CamillaDSP parametric EQ section
filters:
  room_mode_68hz:
    type: Biquad
    parameters:
      type: Peaking
      freq: 68
      gain: -8.5
      q: 6.0
  room_mode_103hz:
    type: Biquad
    parameters:
      type: Peaking
      freq: 103
      gain: -5.2
      q: 5.0
  bass_shelf:
    type: Biquad
    parameters:
      type: Lowshelf
      freq: 120
      gain: 2.0
      q: 0.7
  treble_shelf:
    type: Biquad
    parameters:
      type: Highshelf
      freq: 8000
      gain: -2.5
      q: 0.7
```

These filters tame the room modes at 68 Hz and 103 Hz (which survived the bass trap treatment), add a gentle bass shelf to approach the Harman target, and roll off the treble slightly for a natural in-room balance.

### 3.4 The Passive Path: Simpler DSP Configuration

For the passive crossover path, CamillaDSP does not need to do crossover splitting. The configuration is just:
1. Volume control
2. Room correction EQ
3. Stereo output

This is a simpler config file and does not require a multi-channel output interface. The Pi's built-in stereo DAC HAT is sufficient.

---

## 4. Preamplification and Volume Control

### 4.1 The Volume Control Problem

Every system needs volume control. The question is where in the signal chain to put it. There are three approaches, each with trade-offs:

### 4.2 Option 1: DSP Volume in CamillaDSP (Recommended)

CamillaDSP applies volume control in the digital domain before DAC conversion. This is the approach recommended for this system.

**Pros:**
- No additional hardware
- Perfect channel balance at all volume levels
- Controlled via the web interface or a phone app
- Can be integrated with the Pi's GPIO for a physical volume knob (rotary encoder)

**Cons:**
- At very low volumes, you lose digital resolution (fewer bits represent the signal). With a 24-bit DAC, you have about 144 dB of dynamic range. Reducing volume by 40 dB still leaves 104 dB of dynamic range -- more than enough. Reducing by 80 dB leaves 64 dB, which is getting marginal. In practice, you never reduce volume by 80 dB.
- Some purists insist digital volume "degrades" the signal. Below -60 dB of attenuation, they have a point (quantization noise rises relative to the signal). Above that, the degradation is inaudible.

### 4.3 Option 2: Passive Preamp (Just a Pot)

A passive preamp is literally a potentiometer (volume knob) in a box, with input and output RCA jacks. The audio signal passes through the pot, which attenuates it.

**Pros:**
- Absolute simplest possible design
- No power supply needed
- No active components to add noise or distortion
- Transparent (it is just a resistor divider)

**Cons:**
- Output impedance varies with the pot position (at 50%, a 10k pot has 2.5k output impedance). This can affect high-frequency response with long cable runs.
- No gain -- if your source level is low, you cannot boost it
- No source selection (unless you add a switch)

**Build it:** An Alps RK27 10k potentiometer, two pairs of RCA jacks, and a small aluminum enclosure. Wire the input to the top of the pot, the output to the wiper, and ground to the pot's bottom terminal. Total cost: about $20-30.

### 4.4 Option 3: Active Preamp

An active preamp uses an op-amp (or tube) circuit to buffer and optionally amplify the signal, with a pot for volume control.

**Pros:**
- Low output impedance regardless of volume position
- Can add gain if needed (for low-output sources)
- Can include source selection (multiple inputs)
- The op-amp buffer drives long cables without frequency response changes

**Cons:**
- More complex (needs a power supply)
- Adds noise and distortion (though both are negligible with a good op-amp like the OPA2134 or LME49720)

For this system, **Option 1 (DSP volume)** is the recommendation. You already have CamillaDSP running. Adding a physical preamp is an extra box, extra cables, and extra cost for negligible sonic benefit. If you want a physical volume knob, wire a rotary encoder to the Pi's GPIO and map it to CamillaDSP volume -- Chapter 30 covers this kind of control interface.

---

## 5. The Power Amplifier: LM3886 Gainclone

### 5.1 System Configuration

The LM3886 gainclone from Chapter 23 is a National Semiconductor (now TI) LM3886 chip amp in a simple, high-quality circuit. For the active crossover system, you need two boards (four channels total):

| Amp Board | Channel 1 | Channel 2 |
|-----------|----------|----------|
| Board 1 | Left woofer | Right woofer |
| Board 2 | Left tweeter | Right tweeter |

For the passive crossover path, you need one board (two channels):

| Amp Board | Channel 1 | Channel 2 |
|-----------|----------|----------|
| Board 1 | Left (full range) | Right (full range) |

### 5.2 Gain Structure

The gain structure of the entire system matters. You want each stage to operate at its optimal signal level -- not so low that noise dominates, not so high that clipping occurs.

**Typical signal levels in the chain:**
- CamillaDSP output at 0 dBFS: the DAC outputs its maximum voltage (~2 Vrms for most DAC HATs)
- Typical listening level: CamillaDSP volume at -20 to -30 dB, so DAC output is ~0.07-0.2 Vrms
- LM3886 gain: typically set to 27 dB (voltage gain of ~22x)
- Speaker sensitivity: 87 dB/W/m

At a CamillaDSP volume of -20 dB, the DAC outputs 0.2 Vrms. The LM3886 amplifies this to 4.4 Vrms. Into an 8-ohm speaker, that is:

```
P = V² / R = 4.4² / 8 = 2.4 watts
SPL at 1m = 87 + 10 × log10(2.4) = 87 + 3.8 = 90.8 dB
SPL at 3m = 90.8 - 9.5 = 81.3 dB
```

That is a comfortable listening level. Turn up to -10 dB on CamillaDSP, and you are at 91 dB at 3 meters -- enthusiastic listening. Full volume (0 dBFS) drives the LM3886 to its rated output of ~50W, producing 104 dB at 1 meter (94.5 dB at 3 meters). You have plenty of headroom.

### 5.3 Power Supply Considerations

Each LM3886 board needs a dual-rail power supply (typically ±28V for 8-ohm speakers, ±22V for 4-ohm speakers). The two boards can share a power supply if it has sufficient current capacity, but separate supplies reduce crosstalk and ensure each amp has unlimited access to current during transients.

For the home hi-fi system, a single toroidal transformer (2 × 22 VAC, 160 VA or larger) with rectification and filtering provides clean power for both boards. The transformer should be mounted in a separate enclosure or at least on the opposite side of the chassis from the signal wiring to minimize hum coupling.

> **What happens if... you use an undersized power supply?** During loud, bass-heavy passages, the woofer amp draws large current pulses. If the power supply cannot deliver this current, the rail voltage sags. The amp clips -- not because the signal is too large, but because the power supply ran out of juice. This sounds different from signal clipping: it is a "dynamic compression" where the loud parts are slightly squashed. You might not hear it as overt distortion, but the system loses impact and dynamics. Size your power supply for the maximum output: 50W per channel × 2 channels / 0.6 efficiency = 167W minimum transformer rating. A 200W or larger transformer provides comfortable headroom.

---

## 6. The Speakers

### 6.1 For the Active Path: Modified Bookshelf Speakers (Ch 16)

Your 2-way bookshelf speakers from Chapter 16 were built with a passive crossover. For the active path, you need to modify them:

1. **Remove or bypass the passive crossover.** If the crossover is on a separate board inside the speaker (which it should be, per the build instructions), simply disconnect it. Wire the woofer binding posts directly to the woofer terminals. Wire the tweeter binding posts directly to the tweeter terminals.

2. **Add a safety high-pass capacitor on the tweeter.** Solder a 10 µF non-polarized film capacitor in series with the tweeter's positive lead. This creates a first-order high-pass at about 2 kHz (for an 8-ohm tweeter). It does not interfere with the DSP crossover (which crosses at 2.5 kHz with a 24 dB/octave slope), but it protects the tweeter from bass if the DSP ever fails or misconfigures.

3. **Add separate binding post pairs.** If the speaker only has one pair of binding posts, add a second pair. Drill two holes for the additional binding posts and wire one pair to the woofer, one pair to the tweeter. Label clearly.

### 6.2 For the Passive Path: Unmodified Bookshelf Speakers (Ch 16)

No modifications needed. Use the speakers exactly as built, with the passive crossover intact and a single pair of binding posts.

### 6.3 Floor-Standing Towers as an Upgrade

For a larger room (40+ m²), the bookshelf speakers may run out of bass extension and output capability. The floor-standing tower design from Chapter 17 uses larger woofers in a bigger enclosure, extending bass response to 35-40 Hz and adding 3-5 dB of sensitivity. The same active/passive crossover options apply.

The trade-off: floor-standing speakers take up more floor space, require more amplifier power to take advantage of their larger driver capability, and are harder to position (they are less flexible about distance from walls because of their deeper bass response).

---

## 7. Cable Design and Construction

### 7.1 Interconnects (RCA Cables)

The cables between the DAC, preamp, and amplifier carry line-level signals (~0.1-2 Vrms). At these signal levels, the cable needs to:
- Shield the signal from electromagnetic interference (hum from nearby power cables, RF from phones, etc.)
- Have low enough resistance to not attenuate the signal (trivial for any reasonable cable)
- Have low enough capacitance to not roll off high frequencies (matters for long runs with high-impedance sources)

**Build your own RCA interconnects:**

**Materials:**
- Mogami 2534 or Canare L-4E6S star-quad microphone cable (~$1-2 per foot). These are professional-grade shielded cables with low capacitance and excellent rejection of interference.
- Neutrik Rean NYS373 RCA plugs (~$3-5 each). Gold-plated, quality construction, easy to solder.

**Construction:**
1. Cut the cable to length. For home hi-fi, keep runs under 2 meters (6 feet). Shorter is better.
2. Strip back the outer jacket by about 15 mm.
3. Unwind and twist the braided shield. This becomes the ground connection.
4. Strip the inner conductors by about 5 mm. For star-quad cable, there are four inner conductors -- pair them: two for signal (twist and solder together) and two for return (twist with the shield and solder to ground).
5. Slide the RCA plug's barrel over the cable before soldering.
6. Solder the signal pair to the center pin of the RCA plug.
7. Solder the return pair + shield to the ground lug.
8. Assemble the plug barrel. Add heat shrink for strain relief.

**Total cost per 1-meter pair:** about $10-15. Equivalent commercial cables cost $30-100+.

### 7.2 Speaker Cable: What Actually Matters

Speaker cables carry high-current signals (amps, not milliamps). The physics is simple:

**Resistance matters.** Speaker cable resistance should be less than 5% of the speaker impedance to avoid power loss and damping factor degradation. For an 8-ohm speaker, cable resistance should be under 0.4 ohms (total, both conductors).

| AWG | Resistance per meter (both conductors) | Max Length for <0.4 Ω |
|-----|---------------------------------------|----------------------|
| 18 AWG | 0.042 Ω/m | 9.5 m |
| 16 AWG | 0.026 Ω/m | 15.4 m |
| 14 AWG | 0.017 Ω/m | 23.5 m |
| 12 AWG | 0.010 Ω/m | 40.0 m |

For home hi-fi with cable runs under 5 meters, 16 AWG is more than adequate. 14 AWG gives additional margin and costs almost nothing extra.

**What does NOT matter:**
- Cable material beyond copper (silver-plated cables have marginally lower resistance, but the difference at audio frequencies is irrelevant for runs under 10 meters)
- Cable geometry (twisted, braided, coaxial -- all irrelevant at audio frequencies where the wavelength is kilometers long)
- Cable capacitance (only matters for very long runs, >20 meters)
- Price above about $1 per foot

The math is unambiguous here. A 3-meter run of 14 AWG copper cable has a resistance of 0.051 ohms. Even the most exotic $100/foot audiophile cable cannot be significantly less than this, because the resistance is determined by the conductor cross-section and material, and copper is copper. The insulation, geometry, and marketing copy do not change the physics.

**Use:** standard 14 or 16 AWG stranded copper speaker wire from any hardware store. Terminate with banana plugs or spade lugs for clean connections. Total cost for a 3-meter pair: about $5.

> **What happens if... you use lamp cord (zip cord) as speaker cable?** It works perfectly fine, as long as it is adequate gauge. 16 AWG lamp cord is electrically identical to 16 AWG "speaker cable" -- same copper, same resistance, same performance. The only real difference is that dedicated speaker cable usually has clearer polarity marking (one conductor is marked) and more flexible insulation. In a blind test, nobody has ever reliably distinguished lamp cord from exotic speaker cable. This has been tested repeatedly by audio publications and engineering groups.

---

## 8. System-Level Grounding

### 8.1 Ground Loops: The Number One Cause of Hum

A ground loop occurs when there are multiple paths to ground in a system. Current flows through these paths, and because the paths have different impedances, voltages develop across them. If an audio cable's ground carries even a tiny ground loop current, that current creates a voltage that appears as a 50/60 Hz hum in the audio.

In a typical home hi-fi system with separate components (source, preamp, amp), each component plugs into the wall outlet and has its own path to ground through the power cord. The audio cables between components create additional ground connections. Now you have a loop: ground flows through the power cord to the outlet, through the building wiring to the breaker panel, back through another power cord to the next component, and through the audio cable back to the first component.

### 8.2 The Star Grounding Approach

The solution is to ensure there is only one ground path between components. In practice:

1. **Plug all audio components into the same power strip or outlet.** This minimizes the voltage difference between their ground references.

2. **Use the audio cable shield as the only signal ground connection between components.** If a component has a ground lift switch, use it (this disconnects the chassis ground from the audio ground, breaking the loop).

3. **If hum persists:** Disconnect all audio cables. Connect them one at a time. When you connect the cable that introduces hum, that cable is the ground loop path. Solutions:
   - Use a ground loop isolator (a small audio transformer that breaks the galvanic connection while passing the audio signal). These cost $10-20 and work well.
   - Use balanced connections (XLR or TRS) instead of unbalanced (RCA). Balanced connections reject ground loop hum by design (the balanced receiver subtracts the common-mode noise, including ground loop hum).

### 8.3 Balanced vs Unbalanced Connections

Our system uses RCA (unbalanced) connections between the DAC and the amp. This is standard for home hi-fi. For short cable runs (under 3 meters) in a quiet domestic environment, unbalanced works fine.

If you experience ground loop hum that cannot be resolved with a common outlet and ground lift, switch to balanced connections. The Behringer UMC404HD (used for multi-channel output in the active system) has balanced TRS outputs. The LM3886 gainclone can accept balanced input by adding a differential input stage (an op-amp configured as a differential amplifier -- a simple circuit addition covered in many LM3886 design guides).

### 8.4 The Pi's Ground

The Raspberry Pi is powered by a 5V USB-C power supply. This supply has its own ground reference, which may differ from the power amplifier's ground. The audio connection between the DAC HAT and the amp creates a ground link.

In the active system using a USB audio interface, the USB connection between the Pi and the interface creates a ground link, and the interface's analog outputs connect to the amp. If hum appears, try:
1. Powering the Pi from a linear power supply instead of a switching supply (switching supplies can inject high-frequency noise into the ground)
2. Using a USB isolator between the Pi and the USB audio interface
3. Using the balanced outputs of the USB interface

---

## 9. Power Conditioning

### 9.1 Does It Actually Matter?

The audio industry sells power conditioners ranging from $50 filtered power strips to $5,000 regenerators. Here is the practical reality:

**Surge protection: Yes, absolutely.** A power surge from a lightning strike or grid fault can destroy your electronics. Use a surge-protected power strip. Cost: $20-40. Non-negotiable.

**EMI/RFI filtering: Sometimes helpful.** If you live near a radio transmitter, airport, or industrial facility, your power line may carry high-frequency noise that gets into your audio. A filtered power strip (like the Furman SS-6B, ~$50) includes common-mode and differential-mode filters that clean this up. In most homes, this makes no audible difference. In some environments, it is a night-and-day improvement.

**Voltage regulation: Rarely needed.** If your wall voltage sags or fluctuates (common in areas with old wiring or during peak demand), a voltage regulator keeps the supply steady. The LM3886's power supply has its own regulation via the filter capacitors, so minor voltage sags are already handled. Only in extreme cases (voltage dropping below 100V on a 120V line) would a regulator be necessary.

**Power regeneration: Almost never needed.** These devices convert AC to DC and back to AC, generating a "perfect" sine wave. They work. They are also $1,000+ and provide no benefit in 99% of home installations. Save your money for better speakers.

### 9.2 The Recommendation

Buy a quality surge-protected power strip. Plug all your audio components into it. Plug the power strip into a single wall outlet. Total cost: $30-50. This handles surge protection and ensures all components share a common ground reference (reducing ground loop potential).

If you hear noise (buzzing, crackling, or a radio station in your speakers), then consider a filtered power strip. If that does not fix it, investigate the specific noise source (often a dimmer switch, LED light driver, or appliance on the same circuit).

---

## 10. Room Setup

### 10.1 Applying Chapter 36

You have already covered room acoustics and treatment in depth. Here is the checklist for the home hi-fi system specifically:

1. **Speaker placement**: Equilateral stereo triangle with the listening position. Speakers at least 30-50 cm from the front wall (more if possible). The 38% rule for distance from the front wall if your room allows it.

2. **Toe-in**: Start with speakers aimed to cross slightly behind your head. Adjust by ear.

3. **Subwoofer** (if using one): Use the subwoofer crawl technique. Cross the sub over to the main speakers at 80-100 Hz using CamillaDSP.

4. **Bass traps**: Floor-to-ceiling in at least the two front corners. All four corners if possible.

5. **First reflection panels**: At the mirror-trick points on both side walls. Ceiling cloud above the listening position.

6. **Rear wall**: Diffusion panel or a mix of diffusion and absorption.

7. **Listening position**: Not against the rear wall (that puts you at a bass mode pressure maximum). Pull the listening position at least 0.5-1 meter from the rear wall.

### 10.2 The Listening Chair

This seems trivial, but your listening chair affects the sound. A high-backed chair reflects sound from behind your head, creating comb filtering at frequencies where the reflected path differs from the direct path by odd multiples of half wavelengths. A low-backed chair or an open chair lets sound pass freely.

Does this matter? For casual listening, not really. For critical listening and evaluation, it can. Many audiophiles prefer a low-backed chair or even a simple stool for critical evaluation. Your call.

---

## 11. The Integration Walkthrough

### 11.1 Step-by-Step Connection

Here is the exact sequence for assembling the active crossover system. Do each step in order.

**Step 1: Speakers in position**
Place the speakers on stands at ear height (tweeter at seated ear level, approximately 90-100 cm from the floor). Position per section 10. Do not connect any cables yet.

**Step 2: Amp placement**
Place the LM3886 amp(s) near the speakers. Short speaker cable runs are better than short interconnects (because speaker cables carry high current and are more affected by resistance). If you must choose between a short speaker cable or a short interconnect, choose the short speaker cable.

**Step 3: Speaker cables**
Connect speaker cables from each amp channel to the appropriate speaker terminal. For the active system:
- Amp 1, Ch 1 → Left woofer binding posts
- Amp 1, Ch 2 → Right woofer binding posts
- Amp 2, Ch 1 → Left tweeter binding posts
- Amp 2, Ch 2 → Right tweeter binding posts

Double-check polarity: red to red, black to black. Reversed polarity on one driver creates a phase cancellation at the crossover frequency that sounds hollow and wrong.

**Step 4: Interconnects**
Connect the USB audio interface outputs to the amp inputs:
- Interface Out 1 → Amp 1, Ch 1 (left woofer)
- Interface Out 2 → Amp 1, Ch 2 (right woofer)
- Interface Out 3 → Amp 2, Ch 1 (left tweeter)
- Interface Out 4 → Amp 2, Ch 2 (right tweeter)

**Step 5: USB connection**
Connect the USB audio interface to the Raspberry Pi via USB.

**Step 6: Power connections**
Plug all components into the same surge-protected power strip:
- Pi power supply
- Amp 1 power supply
- Amp 2 power supply
- USB audio interface power (if external)

Do not turn anything on yet.

### 11.2 Power-On Sequence

Turn on in this order to avoid sending transients through the speakers:

1. **Pi first**: Let it boot fully (30-60 seconds). CamillaDSP starts automatically.
2. **USB audio interface**: Should initialize automatically when the Pi boots.
3. **Amplifiers last**: Turn on both amp boards. You should hear a very faint click from the speakers as the amp's output relay engages (if your LM3886 build includes a relay mute circuit, which it should).

Listen for hum. With no music playing, put your ear close to each driver. You should hear silence (or very faint hiss that is inaudible from the listening position). If you hear 50/60 Hz hum, go to section 8 (grounding).

### 11.3 Initial DSP Configuration

Open the CamillaDSP web interface from your phone or laptop. Verify:

1. **Volume is at a low level** (-40 dB or lower). Do not start at full volume.
2. **Crossover filters are active**: LR4 low-pass at 2.5 kHz on woofer channels, LR4 high-pass at 2.5 kHz on tweeter channels.
3. **Channel routing is correct**: Play a test tone or music and verify that the woofer moves when low frequencies play and the tweeter produces high frequencies. If the woofer is playing highs, your channel routing is swapped.
4. **Polarity is correct**: Play a voice recording. If the voice sounds hollow, thin, or like it is coming from inside a barrel, one driver may be wired in reverse polarity. Check connections, or flip the polarity of one driver's channel in CamillaDSP.

### 11.4 Level Setting and Calibration

1. Play pink noise at a moderate level.
2. Mute the tweeter channels in CamillaDSP. Adjust the woofer channel level so the woofer produces about 75 dB SPL at the listening position (use the REW SPL meter or a phone app for a rough check).
3. Unmute the tweeter channels. Mute the woofer channels. Adjust the tweeter channel level to match -- 75 dB SPL at the listening position.
4. Unmute both. The combined system should produce about 78 dB SPL (3 dB addition from two uncorrelated sources).
5. Fine-tune: play familiar music. If the sound is too bright, reduce the tweeter level by 0.5-1 dB. If it is dull, increase it slightly. This is where personal preference enters.

### 11.5 Room Correction

Follow the measurement workflow from Chapter 37:
1. Measure the in-room response at 5 positions around the listening area
2. Average the measurements
3. Compare to your target curve (Harman or personal house curve)
4. Create parametric EQ filters for room mode peaks and tonal corrections
5. Load into CamillaDSP
6. Measure again with corrections active
7. Verify improvements

### 11.6 Final Listening Evaluation

With everything connected, calibrated, and corrected, sit down and listen. Not to test tones or pink noise -- to *music*. Music you know intimately. Music that exposes flaws. Here is a suggested evaluation playlist:

- **Bass control**: A track with deep, articulate bass (Massive Attack - "Angel"). The bass should be deep but not boomy, with distinct notes rather than one amorphous rumble.
- **Vocal clarity**: A track with a solo voice (Eva Cassidy - "Songbird"). The voice should be centered, natural, and free from harshness or sibilance.
- **Stereo imaging**: A track with instruments placed across the soundstage (Pink Floyd - "Money"). Each instrument should have a specific location. Close your eyes and point to the saxophone. If you can, the imaging is working.
- **Dynamic range**: A track with soft-to-loud transitions (Beethoven Symphony No. 7, 2nd movement). The quiet passages should be clear and detailed. The loud passages should be powerful without compression or distortion.
- **Fatigue test**: Listen to an album you enjoy for 45-60 minutes. If you can listen for an hour without wanting to turn it down or take a break, the tonal balance is right. If you find yourself reaching for the volume knob after 20 minutes, something is too bright, too harsh, or too fatiguing -- revisit the treble EQ.

---

## 12. The Complete Signal Path: Spotify to Eardrum

Let's trace a single sound -- a kick drum hit in a song -- from the streaming service to your cochlea. Every transformation, every component, every potential failure point:

1. **Spotify server**: The song is stored as an Ogg Vorbis file (~320 kbps) on Spotify's CDN. The kick drum sample was originally a 24-bit, 48 kHz WAV file that was mixed, mastered, and encoded to lossy format.

2. **Network**: The Pi's WiFi or Ethernet receives the Ogg Vorbis stream via Spotify Connect protocol. The stream is buffered in the Pi's RAM.

3. **Software decode**: Volumio/moOde decodes the Ogg Vorbis to PCM (uncompressed digital audio). The kick drum is now a sequence of 16-bit or 24-bit samples at 44.1 or 48 kHz.

4. **CamillaDSP processing**: The PCM audio enters CamillaDSP. The kick drum -- primarily energy between 50-150 Hz -- passes through the volume control (attenuated to listening level), through the room correction EQ (a mode at 68 Hz is attenuated by 8.5 dB), and then through the crossover. The low-pass filter passes the kick drum's energy to the woofer channels. The high-pass filter blocks it from the tweeters (though the attack transient of the kick has some energy above 2.5 kHz that does reach the tweeters).

5. **DAC conversion**: The processed digital samples are sent to the USB audio interface, which converts them to analog voltage. The kick drum is now a brief burst of voltage -- a rapid positive excursion followed by a damped oscillation, at about 0.1-0.5 Vrms depending on the volume setting.

6. **Amplification**: The LM3886 receives the analog voltage on its input pin. Its internal error amplifier compares the input to a fraction of its output (negative feedback). The output stage swings its output to match the input, multiplied by the gain (22x). The kick drum is now a 2-10 Vrms burst, with the amplifier sourcing 0.5-2.5 amps from the power supply into the speaker.

7. **Speaker**: The voltage arrives at the woofer voice coil. Current flows through the coil, creating a magnetic field that interacts with the permanent magnet. The coil and attached cone are pushed forward. The cone pushes air. A pressure wave radiates outward.

8. **Room**: The pressure wave travels directly to your ears (3 meters, ~9 ms). It also bounces off the walls, floor, and ceiling. The first reflections arrive 15-25 ms later. The room modes at 68 Hz ring, but the DSP correction reduced the stored energy so the ringing is controlled. The bass traps in the corners absorb some of the mode energy on each reflection.

9. **Your ears**: The direct sound arrives at your eardrums. The tympanic membrane vibrates. The ossicles (hammer, anvil, stirrup) amplify the vibration mechanically and transmit it to the cochlea. Hair cells in the cochlea transduce the mechanical vibration into nerve impulses. Your auditory cortex processes the impulses and says: "kick drum."

Total latency from the kick drum hit on the recording to your perception: about 50-100 ms (network buffer + DSP processing + DAC conversion + sound travel time + neural processing). You do not notice, because every other sound in the recording has the same latency.

Total number of components the signal passed through: approximately 30 active components (op-amps, transistors, DAC chips, Pi SoC) and dozens of passive ones (resistors, capacitors, inductors in the crossover filters and power supplies). Any one of them, failing or misbehaving, would change what you hear. That they all work together seamlessly is what makes this system satisfying to build and even more satisfying to listen to.

---

## 13. Troubleshooting the Integrated System

### 13.1 Common Problems and Solutions

| Problem | Likely Cause | Solution |
|---------|-------------|----------|
| No sound at all | Pi not booted, CamillaDSP not running, amp not on | Check power and boot status of each component in order |
| Sound from one channel only | Cable disconnected, CamillaDSP channel routing wrong | Check cables; verify DSP routing |
| Constant hum (50/60 Hz) | Ground loop | See section 8; try common outlet, ground lift, balanced connections |
| Sound is thin/hollow | Polarity reversed on one driver | Check wiring polarity; flip polarity in CamillaDSP |
| Bass is boomy | Room modes untreated, sub crossover too high | Add bass traps; lower crossover; apply room correction EQ |
| Treble is harsh/fatiguing | Tweeter level too high, no room correction | Reduce tweeter level 1-2 dB; apply high-shelf EQ cut |
| Clicks or dropouts | Pi CPU overloaded, buffer too small | Increase CamillaDSP buffer size; close unnecessary Pi services |
| Distortion at moderate volume | Amp clipping due to too much DSP gain | Reduce CamillaDSP output level; check gain structure |

### 13.2 The Diagnostic Approach

When something sounds wrong, isolate the problem systematically (as we practiced in Chapter 34):

1. **Is it in one channel or both?** If one, the problem is in that channel's signal path.
2. **Is it constant or only at certain frequencies?** Constant = electrical problem. Frequency-dependent = acoustic or crossover problem.
3. **Does it change when you move?** If yes, it is a room interaction. If no, it is in the electronics.
4. **Does it change with volume?** If it gets worse with volume, it is distortion (amp or speaker). If it is the same at all volumes, it is noise (ground loop, interference).

---

## 14. Summary

You now have a complete, high-fidelity audio system built from components you understand at every level. No black boxes. No mystery components. Every resistor, every capacitor, every line of DSP code is there because you put it there, and you know why.

The system is not just assembled -- it is measured, tuned, and optimized for your specific room and preferences. It will sound better than most commercial systems at several times the price, not because the components are magical, but because you took the time to optimize the one thing most people ignore: the interaction between the speakers, the room, and the listener.

In the next chapter, we take the same engineering mindset and apply it to a very different problem: making 80 people dance in a field.
