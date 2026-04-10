<!--
  CHAPTER: 29
  TITLE: "Build: Active Crossover and Room Correction"
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 28
  KEY_TOPICS: active crossover, DSP crossover, Linkwitz-Riley, CamillaDSP, multi-channel DAC, room measurement, REW, UMIK-1, impulse response, room correction, FIR convolution, time alignment, per-driver EQ
  DIFFICULTY: Advanced
  UPDATED: 2026-04-10
-->

# Chapter 29: Build — Active Crossover and Room Correction

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 28 | Difficulty: Advanced

In Part 2, you built passive crossovers. You wound inductors, matched capacitors, and made design decisions that were physically permanent -- changing the crossover frequency meant desoldering components and soldering in new ones. The crossover consumed power (insertion loss), interacted unpredictably with the speaker's impedance curve, and could only achieve limited slopes before component tolerances made the response unreliable.

We're going to rip all of that out and replace it with math.

This chapter modifies the Pi streamer from Chapter 28 to add CamillaDSP in the signal path, implementing an active crossover that divides the frequency spectrum in the digital domain before sending each band to a separate amplifier and driver. No inductors. No capacitors in the signal path. No insertion loss. Arbitrarily steep slopes. Perfect Linkwitz-Riley alignment. Time correction down to the microsecond. And if you don't like the crossover frequency, you change a number in a YAML file and reload.

Then we go further: we measure the room with a calibrated microphone, analyze the response, generate correction filters, and load them into the same DSP pipeline. The result is a system where the speakers, the room, and the DSP work together as an integrated whole -- each compensating for the others' limitations.

### In This Chapter
- Why active crossovers beat passive crossovers
- Multi-channel output options for the Pi
- CamillaDSP active crossover configuration
- Linkwitz-Riley crossover implementation
- Time alignment between drivers
- Per-driver EQ
- Room measurement: equipment, procedure, analysis
- Generating room correction filters
- Complete CamillaDSP pipeline configuration
- Before/after verification

### Related Chapters
- [Ch 13: Crossover Design](../part-2-speakers/13-crossover-design.md) -- what we're replacing with DSP
- [Ch 15: Design Tools and Simulation](../part-2-speakers/15-design-tools-simulation.md) -- measurement fundamentals that apply here
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) -- the amplifiers driving each driver
- [Ch 27: DSP Fundamentals for Audio](27-dsp-fundamentals.md) -- the theory behind this build
- [Ch 28: Build: Pi Streamer with Physical Controls](28-build-pi-streamer.md) -- the streamer we're modifying
- [Ch 36: Room Acoustics and Treatment](../part-6-system-design/36-room-acoustics-treatment.md) -- understanding the room problems we're correcting

---

## 1. Why Go Active?

### 1.1 The Passive Crossover's Limitations

Recall from Chapter 13 what a passive crossover does: it sits between the amplifier and the drivers, using inductors and capacitors to divide the frequency spectrum. The amplifier pushes the full-range signal, and the crossover filters it into bands. This approach has inherent problems:

**Insertion loss**: Every component in the signal path has resistance. A woofer inductor might have 0.5-1.5 ohms of DC resistance, which forms a voltage divider with the speaker's impedance. At the crossover frequency, you might lose 1-2 dB of amplifier power to heat in the inductor. That's 20-40% of your amplifier power, wasted.

**Impedance interaction**: The crossover components interact with the driver's impedance curve. The textbook crossover frequency assumes a resistive load, but speakers are reactive -- their impedance varies with frequency. This means the actual crossover frequency and slope differ from the design target, sometimes significantly.

**Component tolerance**: A ±5% tolerance on a capacitor shifts the crossover frequency by ±5%. With multiple components, tolerances stack unpredictably. Matching left and right channels requires hand-selecting components.

**Limited slopes**: Steeper slopes require more components, and each component adds insertion loss and impedance interaction. A fourth-order (24 dB/octave) passive crossover is practical. An eighth-order is theoretically possible but impractical due to accumulated tolerances and losses.

**No time alignment**: Passive crossovers can't delay a signal. If the tweeter's acoustic center is 2 cm closer to the listener than the woofer's, you'd need to physically recess the tweeter or add a stepped baffle. With DSP, you add a 0.058 ms delay to the tweeter and you're done.

### 1.2 What Active Crossovers Change

In an active crossover system:

```
PASSIVE:  Amp → Passive Crossover → Woofer
                                  → Tweeter

ACTIVE:   DSP Crossover → Amp 1 → Woofer (direct connection, no passive components)
                        → Amp 2 → Tweeter (direct connection, no passive components)
```

**No insertion loss**: The amplifier drives the speaker directly. Every watt reaches the driver.

**No impedance interaction**: The crossover operates in the digital domain, before the signal reaches any amplifier or driver. The crossover's behavior is independent of the load.

**Perfect accuracy**: Digital filter coefficients are calculated to arbitrary precision. There's no component tolerance. Left and right channels are mathematically identical.

**Arbitrary slopes**: A Linkwitz-Riley 8th order (48 dB/octave) requires the same CPU effort as a 2nd order. You can implement slopes that would be physically impractical with passive components.

**Time alignment**: A delay filter precisely aligns the acoustic output of each driver at the listening position.

**Per-driver EQ**: Each driver gets its own equalization -- compensating for its specific frequency response deviations without affecting other drivers.

**Infinite adjustability**: Change any parameter by editing a YAML file and reloading. No soldering.

### 1.3 The Tradeoff

Active crossovers need **separate amplifier channels for each driver group**. A stereo two-way system needs four amplifier channels (two woofer, two tweeter). A three-way needs six. This is more hardware and more cost than a single stereo amplifier with passive crossovers.

But: the Class D amplifier modules from Chapter 23 are cheap ($15-30 each), efficient, and small. Two TPA3255 boards give you four channels with 300+ watts total. The cost is comparable to a single good passive crossover pair, and the result is dramatically better.

---

## 2. Multi-Channel Output

### 2.1 The Problem

The Pi streamer from Chapter 28 has a stereo DAC HAT -- two output channels. An active two-way crossover needs four channels (left woofer, right woofer, left tweeter, right tweeter). A three-way needs six. We need more output channels.

### 2.2 Option 1: Multi-Channel USB DAC

The simplest approach for four or more channels is a multi-channel USB DAC:

**miniDSP USBStreamer**: A small USB device with 2-in/8-out channels. It shows up as a standard USB audio device on Linux. ~$100.

```bash
# Verify the USB DAC is recognized
aplay -l
# Should show something like:
# card 2: USBStreamer [miniDSP USBStreamer], device 0: USB Audio [USB Audio]
#   Subdevices: 1/1
#   Subdevice #0: subdevice #0

# Test multi-channel output
speaker-test -D hw:USBStreamer -c 8
```

**Behringer UMC1820 or similar pro audio interface**: More channels (8-18), higher quality preamps (useful if you also want analog input), but larger and more expensive.

**MOTU M4**: 4-output USB audio interface with excellent DAC quality. ~$250. Overkill for crossover duty but you get great measured performance.

### 2.3 Option 2: Multiple I2S DAC HATs

Some HATs can be stacked on the same Pi using different I2S configurations. For example:

- **Two HiFiBerry DAC2 Pro boards**: One on the main I2S bus, one on a secondary bus (requires custom device tree overlay, not straightforward)
- **HiFiBerry DAC8x**: An 8-channel DAC HAT designed for active crossover and multi-room applications. Uses TDM (time-division multiplexing) over I2S. ~$70.

The HiFiBerry DAC8x is purpose-built for this application. It provides 8 analog output channels from a single HAT, with CamillaDSP as the intended processing engine.

### 2.4 Option 3: HDMI Audio to AVR

If you have an AV receiver, the Pi can output multi-channel audio over HDMI. The AVR then provides the DAC conversion and amplification. This is the least "DIY" approach but can work if you already have the hardware.

### 2.5 Recommended Approach

For a two-way active crossover build:

| Budget | Option | Channels | Notes |
|---|---|---|---|
| Minimum (~$100) | miniDSP USBStreamer | 8 out | Well-supported on Linux, plenty of channels |
| Moderate (~$70) | HiFiBerry DAC8x | 8 out | Native I2S, designed for CamillaDSP |
| Maximum flexibility | Pro USB interface (e.g., MOTU M4) | 4 out | Best measured performance, also gives you inputs |

For this chapter, I'll use the **miniDSP USBStreamer** as the reference, since it's widely available and the configuration translates to any multi-channel device.

---

## 3. CamillaDSP Active Crossover Configuration

### 3.1 Setting Up the Signal Path

First, we modify the Pi streamer from Chapter 28 to route audio through CamillaDSP:

```
Music Source (MPD/Spotify/AirPlay)
    ↓
ALSA Loopback (virtual soundcard)
    ↓
CamillaDSP (processing)
    ↓
Multi-Channel DAC (USB or I2S)
    ↓
Channel 1: Left Woofer → Amp → Woofer Driver
Channel 2: Right Woofer → Amp → Woofer Driver
Channel 3: Left Tweeter → Amp → Tweeter Driver
Channel 4: Right Tweeter → Amp → Tweeter Driver
```

### 3.2 ALSA Loopback Configuration

```bash
# Load the ALSA loopback module
sudo modprobe snd-aloop

# Make it persistent
echo "snd-aloop" | sudo tee -a /etc/modules

# Configure MPD to output to the loopback
# In moOde: System Config → Audio → MPD Output → set to "CamillaDSP"
# Or manually in /etc/mpd.conf:
```

```
audio_output {
    type        "alsa"
    name        "CamillaDSP"
    device      "hw:Loopback,0"
    format      "44100:32:2"
    mixer_type  "none"
}
```

### 3.3 Linkwitz-Riley Crossover: The Theory

We'll implement a **Linkwitz-Riley fourth-order (LR4)** crossover at 2 kHz. This is the gold standard for active crossovers because:

- At the crossover frequency, both the low-pass and high-pass outputs are at -6 dB
- When summed acoustically, they produce a flat response (0 dB) at the crossover point
- Both outputs are in phase at the crossover frequency -- no cancellation
- The roll-off is 24 dB/octave -- steep enough to keep the woofer out of the tweeter's range and vice versa

An LR4 is implemented as two cascaded second-order Butterworth filters (each Butterworth is a biquad with Q = 0.707).

### 3.4 Complete CamillaDSP Configuration

Here's the full YAML configuration for a stereo two-way active crossover at 2 kHz:

```yaml
---
devices:
  samplerate: 44100
  chunksize: 1024
  queuelimit: 4
  capture:
    type: Alsa
    channels: 2
    device: "hw:Loopback,1"
    format: S32LE
  playback:
    type: Alsa
    channels: 4
    device: "hw:USBStreamer"
    format: S32LE

# --- FILTERS ---
filters:
  # Master gain: headroom for EQ boosts
  headroom:
    type: Gain
    parameters:
      gain: -6.0
      inverted: false

  # --- Crossover: Low-pass for woofer (LR4 at 2000 Hz) ---
  # Two cascaded Butterworth 2nd-order = Linkwitz-Riley 4th-order
  xover_lp_1:
    type: Biquad
    parameters:
      type: LowpassQ
      freq: 2000
      q: 0.707

  xover_lp_2:
    type: Biquad
    parameters:
      type: LowpassQ
      freq: 2000
      q: 0.707

  # --- Crossover: High-pass for tweeter (LR4 at 2000 Hz) ---
  xover_hp_1:
    type: Biquad
    parameters:
      type: HighpassQ
      freq: 2000
      q: 0.707

  xover_hp_2:
    type: Biquad
    parameters:
      type: HighpassQ
      freq: 2000
      q: 0.707

  # --- Per-driver EQ ---
  # Woofer: gentle presence roll-off (example -- adjust based on measurement)
  woofer_eq_1:
    type: Biquad
    parameters:
      type: Peaking
      freq: 1200
      gain: -2.0
      q: 1.0

  # Tweeter: compensate for rising response (example)
  tweeter_eq_1:
    type: Biquad
    parameters:
      type: Highshelf
      freq: 8000
      gain: -1.5
      q: 0.707

  # --- Time alignment ---
  # Tweeter is 2 cm closer to listener than woofer
  # Speed of sound: ~343 m/s → 2 cm = 0.058 ms delay
  tweeter_delay:
    type: Delay
    parameters:
      delay: 0.058
      unit: ms

# --- MIXER ---
# Route stereo input to 4 output channels
mixers:
  stereo_to_4ch:
    channels:
      in: 2
      out: 4
    mapping:
      - dest: 0    # Left Woofer
        sources:
          - channel: 0
            gain: 0
            inverted: false
      - dest: 1    # Right Woofer
        sources:
          - channel: 1
            gain: 0
            inverted: false
      - dest: 2    # Left Tweeter
        sources:
          - channel: 0
            gain: 0
            inverted: false
      - dest: 3    # Right Tweeter
        sources:
          - channel: 1
            gain: 0
            inverted: false

# --- PIPELINE ---
pipeline:
  # Step 1: Apply headroom to input channels
  - type: Filter
    channel: 0
    names:
      - headroom
  - type: Filter
    channel: 1
    names:
      - headroom

  # Step 2: Expand stereo to 4 channels
  - type: Mixer
    name: stereo_to_4ch

  # Step 3: Crossover filters (per channel)
  # Channel 0: Left Woofer — low-pass
  - type: Filter
    channel: 0
    names:
      - xover_lp_1
      - xover_lp_2
      - woofer_eq_1

  # Channel 1: Right Woofer — low-pass
  - type: Filter
    channel: 1
    names:
      - xover_lp_1
      - xover_lp_2
      - woofer_eq_1

  # Channel 2: Left Tweeter — high-pass + delay
  - type: Filter
    channel: 2
    names:
      - xover_hp_1
      - xover_hp_2
      - tweeter_eq_1
      - tweeter_delay

  # Channel 3: Right Tweeter — high-pass + delay
  - type: Filter
    channel: 3
    names:
      - xover_hp_1
      - xover_hp_2
      - tweeter_eq_1
      - tweeter_delay
```

### 3.5 Understanding the Pipeline

Let's trace a signal through:

1. **Stereo music** enters from the ALSA loopback (2 channels)
2. **Headroom gain** (-6 dB) is applied to prevent clipping from subsequent EQ boosts
3. **Mixer** copies each input channel to two output channels: left → left woofer + left tweeter, right → right woofer + right tweeter
4. **Crossover filters**:
   - Woofer channels get two cascaded Butterworth low-pass filters (= LR4 low-pass) → only frequencies below 2 kHz pass
   - Tweeter channels get two cascaded Butterworth high-pass filters (= LR4 high-pass) → only frequencies above 2 kHz pass
5. **Per-driver EQ** corrects each driver's individual response characteristics
6. **Time alignment** delays the tweeter by 0.058 ms to align acoustic centers
7. **4-channel output** goes to the USB DAC → 4 amplifier channels → 4 speakers

### 3.6 Adjusting the Crossover

To change the crossover frequency, just change the `freq` value in all four crossover filters (both LP and both HP must match). To change the slope:

| Slope | Implementation | Biquads per filter |
|---|---|---|
| 2nd order (12 dB/oct) — LR2 | One Butterworth (Q=0.5) | 1 |
| 4th order (24 dB/oct) — LR4 | Two Butterworth (Q=0.707) | 2 |
| 8th order (48 dB/oct) — LR8 | Four Butterworth (Q values: 0.541, 1.307) | 4 |

For LR8, the Q values of the four cascaded biquads are 0.541 and 1.307 (each used twice, alternating). This gives an extremely steep 48 dB/octave slope.

> **What happens if...** you set different crossover frequencies for the LP and HP filters? You create a gap or overlap in the frequency response. A gap means no driver is reproducing those frequencies -- a hole in the response. An overlap means both drivers are reproducing the same frequencies, which might cause interference patterns. Always use the same frequency for both the LP and HP filters in a crossover pair.

---

## 4. Room Measurement

### 4.1 Equipment

You need:

| Equipment | Recommendation | Cost | Notes |
|---|---|---|---|
| Measurement microphone | miniDSP UMIK-1 | ~$80 | USB, calibrated, comes with calibration file. The standard for DIY room measurement. |
| Measurement software | REW (Room EQ Wizard) | Free | Runs on Windows, macOS, Linux. The gold standard. |
| Microphone stand | Any boom stand | $15-30 | Needs to hold the mic at ear height at the listening position |
| Computer | Your laptop/desktop | — | Runs REW and connects to the UMIK-1 |

The **UMIK-1** is a calibrated measurement microphone -- it comes with a calibration file that tells REW exactly how to compensate for the microphone's own frequency response deviations. This is important: an uncalibrated mic gives you uncalibrated results.

### 4.2 REW Setup

1. Download REW from [roomeqwizard.com](https://www.roomeqwizard.com/)
2. Install and launch
3. Go to **Preferences → Soundcard**:
   - Set **Output** to your Pi streamer (route the measurement signal through the same path the music takes)
   - Set **Input** to the UMIK-1
4. Load the UMIK-1 calibration file: **Preferences → Mic/Meter → Browse** → select the `.txt` calibration file that came with your UMIK-1
5. Set **Level** to a moderate volume -- you want the measurement signal to be clearly audible above room noise but not painfully loud

### 4.3 Measurement Procedure

**Microphone placement**: Put the UMIK-1 at your primary listening position, at ear height when seated. Point it straight up (for omnidirectional measurement of the room's contribution) or straight forward (for a measurement that includes the direct speaker response more heavily). Standard practice is straight up for room correction.

**Measurement sequence**:

1. In REW, click **Measure**
2. Select **Sweep** as the signal type
3. Set sweep range to 20 Hz - 20,000 Hz
4. Set sweep length to at least 256k samples (gives good frequency resolution, especially at low frequencies)
5. Click **Start**
6. REW plays a sine sweep through your speakers and records what the microphone picks up
7. The result appears as a frequency response curve

**Take multiple measurements**: Move the microphone to several positions within a 30 cm radius of the primary listening position (this captures the average response across a "head-sized" area rather than one precise point). REW can average multiple measurements.

### 4.4 Analyzing the Measurement

The measurement shows you three important things:

**Frequency response**: The magnitude (in dB) at each frequency. A flat response means every frequency is reproduced at equal level. In reality, rooms and speakers create peaks and dips.

Typical room problems:
- **Room modes** (standing waves): Sharp peaks and nulls at low frequencies, typically 30-150 Hz. These are caused by sound bouncing between parallel surfaces. A room 4 meters long has a primary mode at ~43 Hz (speed of sound / 2 × room dimension).
- **Speaker response irregularities**: Broader bumps and dips across the spectrum, caused by the drivers' own frequency response deviations, diffraction off the baffle, crossover interactions.
- **Room reflections**: Comb filtering patterns at mid-to-high frequencies from strong early reflections off walls, floor, and ceiling.

**RT60 (Reverberation Time)**: How long it takes the sound to decay by 60 dB. Too high (>0.5s for a listening room) means the room is too reverberant -- absorptive treatment is needed. Too low (<0.2s) means the room is "dead" and unpleasant. DSP can't fix reverberation -- that requires physical room treatment (Chapter 36).

**Waterfall plot**: A 3D view showing frequency response over time. It reveals where energy "rings" -- a room mode at 63 Hz that takes 500ms to decay appears as a ridge extending along the time axis at 63 Hz. These ringing modes are what makes bass sound "boomy" and indistinct.

### 4.5 What Measurement Tells You (and What It Doesn't)

**What measurement reveals that your ears might miss**:
- Exact frequencies of room modes
- Cancellation nulls (your brain partially fills these in perceptually)
- The severity of frequency response deviations in dB
- Time-domain behavior (ringing, late reflections)

**What measurement doesn't capture**:
- How good the music actually sounds (measurements are necessary but not sufficient)
- Off-axis response from your listening position (a single measurement captures one point)
- How the room sounds when you move your head

> **What happens if...** you measure at only one position and create correction filters for that exact point? The correction will be precise at that point but may make things worse at nearby positions. Room modes have nodes (nulls) and antinodes (peaks) at different positions -- correcting a peak at one position might boost a null at a nearby position. This is why averaging multiple positions is important, and why aggressive correction of narrow dips is counterproductive.

---

## 5. Generating Room Correction Filters

### 5.1 Approach 1: Parametric EQ from REW

REW has a built-in EQ function that analyzes your measurement and generates parametric EQ bands to correct the response.

1. In REW, open your (averaged) measurement
2. Click **EQ** → Set **Target** to your desired response curve (flat, or a gentle house curve with +2-3 dB at low frequencies)
3. Select **Generic** as the equalizer type
4. Set the number of bands (8-12 is typical)
5. Click **Match Response to Target**
6. REW generates frequency, gain, and Q for each band
7. Note down the values and enter them into your CamillaDSP configuration

Example REW output:

```
Band 1:  Freq 42 Hz,   Gain -8.5 dB,  Q 5.2   (room mode)
Band 2:  Freq 63 Hz,   Gain -4.2 dB,  Q 3.8   (room mode)
Band 3:  Freq 125 Hz,  Gain -3.0 dB,  Q 2.1   (broad bump)
Band 4:  Freq 250 Hz,  Gain +1.5 dB,  Q 1.2   (slight dip)
Band 5:  Freq 2.8 kHz, Gain -2.0 dB,  Q 1.5   (speaker peak)
Band 6:  Freq 6 kHz,   Gain +1.0 dB,  Q 2.0   (speaker dip)
```

Translated to CamillaDSP:

```yaml
filters:
  room_eq_1:
    type: Biquad
    parameters:
      type: Peaking
      freq: 42
      gain: -8.5
      q: 5.2

  room_eq_2:
    type: Biquad
    parameters:
      type: Peaking
      freq: 63
      gain: -4.2
      q: 3.8

  room_eq_3:
    type: Biquad
    parameters:
      type: Peaking
      freq: 125
      gain: -3.0
      q: 2.1

  room_eq_4:
    type: Biquad
    parameters:
      type: Peaking
      freq: 250
      gain: 1.5
      q: 1.2

  room_eq_5:
    type: Biquad
    parameters:
      type: Peaking
      freq: 2800
      gain: -2.0
      q: 1.5

  room_eq_6:
    type: Biquad
    parameters:
      type: Peaking
      freq: 6000
      gain: 1.0
      q: 2.0
```

### 5.2 Approach 2: FIR Convolution from REW

For more precise correction, you can generate an FIR filter (impulse response) in REW:

1. In REW, after running the EQ match, go to **Filters** → **Export filter impulse response as WAV**
2. Select the sample rate matching your CamillaDSP configuration (44100 or 48000)
3. Select the number of taps (16384 is a good starting point for full-range correction)
4. Export as a WAV or raw file

Convert and load into CamillaDSP:

```bash
# Convert WAV to raw float32 for CamillaDSP
sox room_correction.wav -t f32 -c 1 room_correction_left.raw
# (Do the same for the right channel if measured separately)
```

```yaml
filters:
  room_fir_left:
    type: Conv
    parameters:
      type: Raw
      filename: /etc/camilladsp/room_correction_left.raw
      format: FLOAT32LE

  room_fir_right:
    type: Conv
    parameters:
      type: Raw
      filename: /etc/camilladsp/room_correction_right.raw
      format: FLOAT32LE
```

### 5.3 Approach 3: AutoEQ

**AutoEQ** is a Python tool that can generate parametric or FIR correction filters from measurement data. It's particularly good for headphone correction but works for room correction too.

```bash
pip3 install autoeq
# Import your REW measurement and generate correction
autoeq --input-file measurement.txt --output-dir ./correction --parametric-eq --max-filters 10
```

### 5.4 Correction Philosophy: Less is More

The temptation is to flatten the response perfectly. Resist it. Here are the guidelines:

**Do correct**:
- Room mode peaks (narrow boosts at low frequencies): Cut these. They're the biggest improvement you'll hear.
- Broad speaker response deviations: Gentle EQ to smooth out wide bumps.
- High-frequency anomalies from the listening position.

**Don't correct (or correct gently)**:
- Room mode nulls (dips at low frequencies): Boosting into a null wastes amplifier power. The room is cancelling the sound at that point. No amount of EQ fixes a cancellation. Physical room treatment (bass traps, Chapter 36) is the real solution.
- Narrow dips above the bass range: These are often position-dependent and may sound worse if boosted.
- Anything below 20 Hz or above 16 kHz: Outside the range that matters for most listeners.

**Target curve**: Consider targeting a gentle downward slope rather than ruler-flat. Many listeners prefer a response that's +3-5 dB at 40 Hz relative to 4 kHz, rolling off above 10 kHz. This "house curve" sounds more natural than clinical flatness. The Harman target curve (based on extensive preference research) is a well-studied starting point.

---

## 6. The Complete Pipeline

### 6.1 Integrating Crossover and Room Correction

Here's the complete CamillaDSP configuration combining the active crossover from Section 3 with room correction from Section 5:

```yaml
---
devices:
  samplerate: 44100
  chunksize: 1024
  queuelimit: 4
  capture:
    type: Alsa
    channels: 2
    device: "hw:Loopback,1"
    format: S32LE
  playback:
    type: Alsa
    channels: 4
    device: "hw:USBStreamer"
    format: S32LE

filters:
  # --- Headroom ---
  headroom:
    type: Gain
    parameters:
      gain: -9.0    # Extra headroom for room correction boosts
      inverted: false

  # --- Room correction (applied to stereo input before crossover) ---
  room_eq_1:
    type: Biquad
    parameters:
      type: Peaking
      freq: 42
      gain: -8.5
      q: 5.2

  room_eq_2:
    type: Biquad
    parameters:
      type: Peaking
      freq: 63
      gain: -4.2
      q: 3.8

  room_eq_3:
    type: Biquad
    parameters:
      type: Peaking
      freq: 125
      gain: -3.0
      q: 2.1

  room_eq_4:
    type: Biquad
    parameters:
      type: Peaking
      freq: 250
      gain: 1.5
      q: 1.2

  room_eq_5:
    type: Biquad
    parameters:
      type: Peaking
      freq: 2800
      gain: -2.0
      q: 1.5

  # --- Crossover: LR4 at 2000 Hz ---
  xover_lp_1:
    type: Biquad
    parameters:
      type: LowpassQ
      freq: 2000
      q: 0.707

  xover_lp_2:
    type: Biquad
    parameters:
      type: LowpassQ
      freq: 2000
      q: 0.707

  xover_hp_1:
    type: Biquad
    parameters:
      type: HighpassQ
      freq: 2000
      q: 0.707

  xover_hp_2:
    type: Biquad
    parameters:
      type: HighpassQ
      freq: 2000
      q: 0.707

  # --- Per-driver EQ ---
  woofer_eq:
    type: Biquad
    parameters:
      type: Peaking
      freq: 800
      gain: -1.5
      q: 1.2

  tweeter_eq:
    type: Biquad
    parameters:
      type: Highshelf
      freq: 10000
      gain: -2.0
      q: 0.707

  # --- Time alignment ---
  tweeter_delay:
    type: Delay
    parameters:
      delay: 0.058
      unit: ms

  # --- Level matching ---
  # If the tweeter is more sensitive than the woofer, pad it down
  tweeter_level:
    type: Gain
    parameters:
      gain: -3.0
      inverted: false

mixers:
  stereo_to_4ch:
    channels:
      in: 2
      out: 4
    mapping:
      - dest: 0
        sources:
          - channel: 0
            gain: 0
            inverted: false
      - dest: 1
        sources:
          - channel: 1
            gain: 0
            inverted: false
      - dest: 2
        sources:
          - channel: 0
            gain: 0
            inverted: false
      - dest: 3
        sources:
          - channel: 1
            gain: 0
            inverted: false

pipeline:
  # --- Input processing (stereo) ---
  - type: Filter
    channel: 0
    names:
      - headroom
      - room_eq_1
      - room_eq_2
      - room_eq_3
      - room_eq_4
      - room_eq_5

  - type: Filter
    channel: 1
    names:
      - headroom
      - room_eq_1
      - room_eq_2
      - room_eq_3
      - room_eq_4
      - room_eq_5

  # --- Expand to 4 channels ---
  - type: Mixer
    name: stereo_to_4ch

  # --- Channel 0: Left Woofer ---
  - type: Filter
    channel: 0
    names:
      - xover_lp_1
      - xover_lp_2
      - woofer_eq

  # --- Channel 1: Right Woofer ---
  - type: Filter
    channel: 1
    names:
      - xover_lp_1
      - xover_lp_2
      - woofer_eq

  # --- Channel 2: Left Tweeter ---
  - type: Filter
    channel: 2
    names:
      - xover_hp_1
      - xover_hp_2
      - tweeter_eq
      - tweeter_delay
      - tweeter_level

  # --- Channel 3: Right Tweeter ---
  - type: Filter
    channel: 3
    names:
      - xover_hp_1
      - xover_hp_2
      - tweeter_eq
      - tweeter_delay
      - tweeter_level
```

### 6.2 Signal Flow Diagram

```
Music (2ch stereo)
  │
  ▼
┌─────────────────────────────────────────────────┐
│  HEADROOM: -9 dB                                │
│  ROOM EQ: 5 parametric bands                    │
│  (applied to both channels identically)         │
└──────────────────────┬──────────────────────────┘
                       │
                 ┌─────┴─────┐
                 │  MIXER    │
                 │  2ch → 4ch │
                 └──┬──┬──┬──┬┘
                    │  │  │  │
       ┌────────────┘  │  │  └────────────┐
       │               │  │               │
       ▼               ▼  ▼               ▼
   ┌───────┐    ┌───────┐┌───────┐    ┌───────┐
   │ LPF   │    │ LPF   ││ HPF   │    │ HPF   │
   │ 2kHz  │    │ 2kHz  ││ 2kHz  │    │ 2kHz  │
   │ LR4   │    │ LR4   ││ LR4   │    │ LR4   │
   ├───────┤    ├───────┤├───────┤    ├───────┤
   │Woofer │    │Woofer ││Tweeter│    │Tweeter│
   │  EQ   │    │  EQ   ││  EQ   │    │  EQ   │
   ├───────┤    ├───────┤├───────┤    ├───────┤
   │       │    │       ││ Delay │    │ Delay │
   │       │    │       ││ Level │    │ Level │
   └───┬───┘    └───┬───┘└───┬───┘    └───┬───┘
       │            │        │            │
       ▼            ▼        ▼            ▼
     Ch 0         Ch 1     Ch 2         Ch 3
    L Woof       R Woof   L Tweet      R Tweet
       │            │        │            │
       ▼            ▼        ▼            ▼
     Amp 1       Amp 2    Amp 3        Amp 4
       │            │        │            │
       ▼            ▼        ▼            ▼
     Woofer      Woofer   Tweeter     Tweeter
     (Left)      (Right)  (Left)      (Right)
```

---

## 7. Before/After Verification

### 7.1 The Measure-Correct-Verify Loop

After configuring and activating the DSP:

1. **Measure again** with the UMIK-1 at the listening position, with the correction active
2. **Compare** the corrected measurement to the uncorrected measurement in REW
3. **Verify** that peaks have been reduced and the overall response is smoother
4. **Listen** -- does it sound better? If the correction makes things sound worse, recheck your filter configuration

### 7.2 What to Expect

**At low frequencies (20-200 Hz)**: The most dramatic improvement. Room modes that were +10-15 dB peaks are now tamed to ±3 dB. Bass should sound tighter, more defined, and less "boomy."

**At mid frequencies (200-2000 Hz)**: Subtle improvement. Speaker response irregularities are smoothed. Vocals and instruments may sound more natural and present.

**At high frequencies (2000-20000 Hz)**: The crossover transition should be seamless. No audible "hole" or excess brightness at the crossover frequency. Time alignment should produce a stable stereo image.

### 7.3 Iterating

Room correction is iterative. Your first attempt won't be perfect. The process:

1. Measure
2. Generate correction
3. Apply
4. Measure again (with correction active)
5. Adjust filters based on the corrected measurement
6. Repeat until satisfied

Each iteration gets closer to your target. Three or four iterations is typical to dial in a room correction that sounds genuinely good.

### 7.4 A/B Listening

CamillaDSP's websocket API lets you bypass all filters in real time:

```bash
# Toggle all processing on/off via the CamillaDSP websocket
# (Requires the camilladsp Python library or a websocket client)
pip3 install camilladsp

python3 -c "
from camilladsp import CamillaClient
client = CamillaClient('127.0.0.1', 1234)
client.connect()
# Check if muted
if client.volume.mute():
    client.volume.set_mute(False)
else:
    client.volume.set_mute(True)
client.disconnect()
"
```

Or load a "bypass" configuration (no filters) versus your correction configuration. The difference should be immediately obvious -- especially in the bass, where room modes dominate.

> **What happens if...** the corrected measurement looks great but the system sounds worse? Several possibilities: (1) You've boosted into a null, which wastes amplifier power and causes distortion without fixing the problem. (2) Your microphone position wasn't representative of where you actually listen. (3) You've over-corrected -- the response is flat but sounds clinical. Try a target curve with a gentle bass tilt (+3 dB at 40 Hz) and treble roll-off (-2 dB above 8 kHz) instead of pure flatness. (4) The correction fights the room's natural acoustics in a way that feels unnatural. Some room character is pleasant -- don't try to eliminate it entirely.

---

## 8. Advanced Topics

### 8.1 Three-Way Crossover

Extending to a three-way (woofer, midrange, tweeter) requires 6 output channels and two crossover points:

```yaml
# Example: 3-way at 500 Hz and 3500 Hz
# Woofer: LPF at 500 Hz (LR4)
# Midrange: HPF at 500 Hz (LR4) + LPF at 3500 Hz (LR4)  (bandpass)
# Tweeter: HPF at 3500 Hz (LR4)
```

The midrange channel is a bandpass -- cascaded high-pass and low-pass. Each crossover point is an LR4 pair. The mixer expands 2 channels to 6.

### 8.2 Subwoofer Integration

Adding a subwoofer channel is straightforward:

```yaml
# In the mixer, add a mono sub output:
# Sub = (Left + Right) / 2, low-passed at 80 Hz
mixers:
  with_sub:
    channels:
      in: 2
      out: 5    # L_woof, R_woof, L_tweet, R_tweet, Sub
    mapping:
      # ... (previous 4 mappings)
      - dest: 4    # Subwoofer (mono sum)
        sources:
          - channel: 0
            gain: -6    # -6 dB for mono sum (prevent clipping)
            inverted: false
          - channel: 1
            gain: -6
            inverted: false

filters:
  sub_lpf_1:
    type: Biquad
    parameters:
      type: LowpassQ
      freq: 80
      q: 0.707
  sub_lpf_2:
    type: Biquad
    parameters:
      type: LowpassQ
      freq: 80
      q: 0.707
```

### 8.3 Saving Presets

Create multiple CamillaDSP configuration files for different scenarios:

- `config_flat.yml`: No correction, basic crossover only
- `config_corrected.yml`: Full room correction
- `config_night.yml`: Room correction + loudness compensation for low-volume listening
- `config_party.yml`: Room correction + bass boost + limiter on the output

Switch between them via the CamillaDSP websocket API or a button on your physical interface.

---

## Summary

An active DSP crossover replaces passive inductors and capacitors with digital filters computed in CamillaDSP, running on the Pi streamer from Chapter 28. The signal path routes music through an ALSA loopback to CamillaDSP, which applies headroom management, room correction EQ, crossover filters (Linkwitz-Riley fourth-order implemented as cascaded biquads), per-driver equalization, and time alignment, then outputs to a multi-channel DAC (miniDSP USBStreamer or HiFiBerry DAC8x) feeding separate amplifiers for each driver.

Room measurement with a calibrated microphone (miniDSP UMIK-1) and REW reveals the room's frequency response, modes, and decay characteristics. Correction filters -- either parametric EQ bands or FIR convolution -- are generated from the measurements and loaded into CamillaDSP. The correction targets room mode peaks while leaving nulls alone, and uses a slightly tilted target curve rather than ruler-flat for the most natural sound.

The result is a speaker system where every parameter -- crossover frequency, slope, per-driver EQ, time alignment, room correction -- is software-defined and infinitely adjustable. The improvement over passive crossovers is not subtle: tighter bass, smoother midrange, a seamless crossover transition, and a listening experience that's optimized for your specific room and speakers. The measure-correct-verify loop lets you iterate toward a result that no passive crossover could achieve.
