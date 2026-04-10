<!--
  CHAPTER: 27
  TITLE: DSP Fundamentals for Audio
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 26
  KEY_TOPICS: FIR filters, IIR filters, parametric EQ, graphic EQ, shelving filters, all-pass filters, compression, limiting, expansion, gating, convolution, sample rate conversion, CamillaDSP, signal flow
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 27: DSP Fundamentals for Audio

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 26 | Difficulty: Inter→Adv

In Part 2, you built passive crossovers from inductors and capacitors. Each component had a physical reality: the inductor's wire gauge limited its current handling, the capacitor's tolerance affected the crossover frequency, and every component added insertion loss that ate away at your amplifier's power. If you wanted to change the crossover frequency, you desoldered parts and soldered in new ones. If you wanted to add time alignment between drivers, you physically moved the tweeter forward on the baffle. If you wanted to correct a room resonance, you built a bass trap.

DSP (Digital Signal Processing) replaces all of that with math. A crossover becomes a filter equation. Time alignment becomes a delay line. Room correction becomes a convolution with the room's inverse impulse response. And changing any parameter means editing a number in a configuration file, not heating up a soldering iron.

This chapter covers the DSP concepts you'll need to turn your Pi streamer into a serious audio processor. We're not doing a graduate-level DSP course -- we're covering exactly what you need to understand CamillaDSP configurations, design active crossovers, implement room correction, and build parametric EQ presets. The math is real, but we'll focus on intuition and application over proofs.

### In This Chapter
- Digital filters: FIR and IIR, when to use each
- Parametric EQ: the workhorse of audio DSP
- Graphic EQ, shelving filters, and all-pass filters
- Dynamic range processing: compression, limiting, expansion, gating
- Convolution: room correction and speaker correction
- Sample rate conversion
- CamillaDSP: architecture and configuration
- Signal flow in a DSP pipeline

### Related Chapters
- [Ch 0: Electricity Through the Lens of Sound](../part-0-electronics/00-electricity-through-sound.md) -- the analog equivalent of what we're doing digitally
- [Ch 13: Passive Crossover Design](../part-2-speakers/13-passive-crossover-design.md) -- what DSP crossovers replace
- [Ch 15: Speaker Measurement](../part-2-speakers/15-speaker-measurement.md) -- measurements that feed into DSP corrections
- [Ch 25: Digital Audio Fundamentals](25-digital-audio-fundamentals.md) -- the digital signal we're processing
- [Ch 28: Build: Pi Streamer with Physical Controls](28-build-pi-streamer.md) -- the hardware running this DSP
- [Ch 29: Build: Active Crossover and Room Correction](29-build-active-crossover-room-correction.md) -- applying these concepts
- [Ch 36: Room Acoustics](../part-6-system-design/36-room-acoustics.md) -- the room problems DSP can (and can't) fix

---

## 1. Digital Filters: The Foundation

### 1.1 What a Digital Filter Does

A digital filter modifies the frequency content of a digital signal. It's the digital equivalent of the RC and LC filter networks from Part 0, but with arbitrary precision and no component tolerance issues.

Every digital filter takes input samples and produces output samples according to a mathematical relationship. The two fundamental types -- FIR and IIR -- differ in how they compute the output.

### 1.2 FIR Filters (Finite Impulse Response)

An FIR filter computes each output sample as a weighted sum of the current input sample and a fixed number of previous input samples:

```
y[n] = b₀·x[n] + b₁·x[n-1] + b₂·x[n-2] + ... + bₘ·x[n-M]
```

Where:
- `y[n]` is the current output sample
- `x[n]` is the current input sample
- `x[n-1], x[n-2], ...` are previous input samples
- `b₀, b₁, b₂, ...` are the filter coefficients (also called "taps")
- `M` is the filter order (number of taps minus one)

**The key insight**: an FIR filter only uses *input* samples. It has no feedback -- the output doesn't feed back into the calculation. This makes FIR filters inherently stable (they can't oscillate or blow up).

**Properties of FIR filters**:

| Property | Characteristic |
|---|---|
| Stability | Always stable (no feedback) |
| Phase response | Can be made perfectly linear phase (symmetric coefficients) |
| Latency | Higher -- delay is (M/2) samples for linear-phase FIR |
| Computation | More expensive -- requires many taps for steep filters |
| Typical use | Room correction convolution, mastering-quality EQ, crossovers where phase matters |

**Linear phase**: This is the killer feature of FIR filters. A linear-phase filter delays all frequencies by exactly the same amount. There's no phase distortion -- the shape of the waveform is preserved, just delayed. Analog filters (and IIR digital filters) can't do this; they always introduce frequency-dependent phase shift.

The cost of linear phase is latency. A linear-phase FIR crossover at 2 kHz with a steep slope might need 4096 taps at 48 kHz, which means 4096/48000 ≈ 85ms of delay. For music playback, this is irrelevant (you just hear the music 85ms later). For live monitoring or video sync, it's a problem.

**How many taps?** The steepness and frequency of the filter determine the tap count:

| Filter Type | Typical Taps at 48 kHz | Latency |
|---|---|---|
| Gentle EQ correction | 256-1024 | 2.7-10.7 ms |
| Crossover at 2 kHz | 2048-4096 | 21-43 ms |
| Room correction (full-range) | 16384-65536 | 170-683 ms |
| Sub-bass crossover at 80 Hz | 16384+ | 170+ ms |

Lower crossover frequencies need more taps because you need more samples to represent one cycle of a low-frequency wave.

### 1.3 IIR Filters (Infinite Impulse Response)

An IIR filter uses both input samples *and* previous output samples:

```
y[n] = b₀·x[n] + b₁·x[n-1] + b₂·x[n-2] - a₁·y[n-1] - a₂·y[n-2]
```

The `a₁·y[n-1]` and `a₂·y[n-2]` terms are *feedback* -- the filter's own output feeds back into the calculation. This feedback is what gives IIR filters their efficiency: a second-order IIR filter (just 5 coefficients) can achieve a sharp resonant peak or a steep roll-off that would require hundreds of FIR taps.

**Properties of IIR filters**:

| Property | Characteristic |
|---|---|
| Stability | Can be unstable if poorly designed (feedback can blow up) |
| Phase response | Minimum phase -- introduces frequency-dependent phase shift, just like analog filters |
| Latency | Very low -- essentially zero added latency for low-order filters |
| Computation | Very efficient -- steep filters with few coefficients |
| Typical use | Parametric EQ, crossovers, dynamics processing, real-time applications |

**Minimum phase**: IIR filters behave like analog filters in terms of phase. A low-pass filter introduces phase lag that increases with frequency. A high-pass filter introduces phase lead. This is the same behavior you saw with passive crossovers in Part 2 -- the phase shift at the crossover frequency is what causes the characteristic lobing pattern when woofer and tweeter overlap.

**Stability**: Because IIR filters use feedback, they can become unstable (output grows without bound) if the coefficients put the filter's poles outside the unit circle in the z-plane. In practice, if you're using well-known filter topologies (biquad sections, as CamillaDSP does), stability is guaranteed by the design equations. You'd have to deliberately mis-design a filter to make it unstable.

### 1.4 The Biquad: The Building Block of Audio DSP

Almost all IIR audio filters are built from cascaded **biquad** (bi-quadratic) sections. A biquad is a second-order IIR filter with the transfer function:

```
H(z) = (b₀ + b₁·z⁻¹ + b₂·z⁻²) / (1 + a₁·z⁻¹ + a₂·z⁻²)
```

That's 5 coefficients (b₀, b₁, b₂, a₁, a₂). From these 5 numbers, you can create:

- Low-pass filter (any frequency, any Q)
- High-pass filter (any frequency, any Q)
- Band-pass filter
- Band-reject (notch) filter
- Peaking EQ (boost or cut at a specific frequency with adjustable bandwidth)
- Low shelf
- High shelf
- All-pass filter

The **Robert Bristow-Johnson Audio EQ Cookbook** (search for "audio eq cookbook") provides the formulas to calculate biquad coefficients for all these filter types. It's one of the most-referenced documents in audio DSP. CamillaDSP uses these formulas internally when you specify a filter type, frequency, gain, and Q.

### 1.5 FIR vs IIR: When to Use Each

| Scenario | Recommended | Why |
|---|---|---|
| Parametric EQ bands | IIR (biquad) | Efficient, low latency, minimum phase matches analog behavior |
| Crossover filters | IIR for live/low-latency; FIR for critical playback | IIR is simpler; FIR gives linear phase if desired |
| Room correction | FIR (convolution) | Complex response requires many correction points; FIR handles arbitrary shapes |
| Real-time processing (live sound) | IIR | Latency matters; IIR adds essentially zero delay |
| Bass management (sub crossover) | IIR | Low-frequency FIR needs huge tap counts |
| Speaker correction (compensating driver response) | Either | FIR if phase matters; IIR if you need low latency |

> **What happens if...** you try to implement a steep low-frequency crossover (say, 80 Hz Linkwitz-Riley 4th order) as a linear-phase FIR filter? At 48 kHz, you'd need on the order of 32,768 taps to get accurate behavior at 80 Hz. That's 682ms of latency. For music playback, this is fine -- the music just starts 0.7 seconds after you press play. For lip-synced video or live monitoring, it's unusable. Use IIR for low-frequency crossovers unless you have a specific reason to need linear phase.

---

## 2. Parametric EQ: The Workhorse

### 2.1 What Parametric EQ Is

A **parametric equalizer** is a filter with three adjustable parameters per band:

- **Frequency (f)**: The center frequency of the filter, in Hz
- **Gain (G)**: How much to boost or cut at that frequency, in dB
- **Q (Quality factor)**: The bandwidth of the filter -- higher Q means narrower band

This is the most versatile EQ type. With enough parametric bands, you can shape any frequency response to match any target. CamillaDSP, REW, and most pro audio EQs use parametric filters.

### 2.2 Understanding Q

Q is the parameter people struggle with most. Intuitively:

- **Low Q (0.5-1)**: Wide, gentle curve affecting a broad frequency range. Use for overall tonal shaping ("a little more warmth" = low-Q boost around 200 Hz).
- **Medium Q (1-4)**: Moderate bandwidth. Use for general frequency response correction ("there's a broad bump at 3 kHz").
- **High Q (4-20+)**: Narrow, surgical. Use for targeting specific resonances ("there's a sharp room mode at 63 Hz").

The mathematical relationship: bandwidth (in octaves) = 2 / (Q × √2) approximately. A Q of 1.41 gives roughly 1 octave bandwidth. A Q of 4.3 gives roughly 1/3 octave bandwidth.

| Q Value | Bandwidth (octaves) | Use Case |
|---|---|---|
| 0.67 | ~2 octaves | Broad tonal shaping |
| 1.0 | ~1.4 octaves | Wide correction |
| 1.41 | ~1 octave | General purpose |
| 2.87 | ~0.5 octave | Moderate correction |
| 4.32 | ~1/3 octave | Narrow correction |
| 10+ | < 1/6 octave | Surgical notch for resonances |

### 2.3 Parametric EQ in CamillaDSP

Here's how you define parametric EQ bands in CamillaDSP's YAML configuration:

```yaml
filters:
  bass_boost:
    type: Biquad
    parameters:
      type: Peaking
      freq: 80
      gain: 3.0
      q: 0.8

  presence_cut:
    type: Biquad
    parameters:
      type: Peaking
      freq: 3000
      gain: -2.5
      q: 2.0

  room_mode_notch:
    type: Biquad
    parameters:
      type: Notch
      freq: 63
      q: 8.0
```

Each filter is a single biquad section -- five coefficients, near-zero latency, minimal CPU usage. You can cascade as many as you need.

### 2.4 The Art of EQ: Cut Before You Boost

A principle from studio engineering that applies equally to your Pi streamer: **prefer cutting over boosting**. If something sounds too bright, don't boost the bass -- cut the treble. If a frequency range sounds too prominent, cut it rather than boosting everything else.

Why?
- Cutting reduces the signal level, which gives headroom and reduces the chance of clipping
- Boosting increases the signal level, which can cause clipping downstream
- The ear is better at detecting boosts than cuts -- cuts sound more natural
- If you boost everything except the offending frequency, you've effectively cut it, but you've also raised the overall level and introduced more processing

**Practical approach for room/speaker correction**:
1. Measure the frequency response (Chapter 29)
2. Identify peaks (room modes, driver resonances)
3. Cut the peaks with narrow-Q parametric bands
4. Leave the dips alone (or address them with room treatment, not EQ -- boosting into a dip just wastes amplifier power)
5. Apply gentle broad-Q shaping for overall tonal balance
6. Reduce the overall level by the amount of your largest boost (headroom management)

---

## 3. Other Filter Types

### 3.1 Graphic EQ

A **graphic equalizer** divides the frequency spectrum into fixed bands (typically 1/3 octave -- 31 bands from 20 Hz to 20 kHz) with a slider for each. You can boost or cut each band, but you can't change the center frequency or bandwidth.

Graphic EQ is simpler to understand but less precise than parametric. It's common on consumer receivers and PA systems. In the Pi streamer context, parametric EQ is strictly more capable -- anything a graphic EQ can do, parametric can do better with fewer bands. CamillaDSP doesn't have a "graphic EQ" mode, but you can simulate one by creating 31 parametric bands at the standard 1/3-octave frequencies with Q ≈ 4.32.

### 3.2 Shelving Filters

A **shelf** filter boosts or cuts all frequencies above (high shelf) or below (low shelf) a specified frequency. Think of it as a tilt: a low shelf at 200 Hz with +3 dB gain boosts everything below 200 Hz by 3 dB and leaves everything above unchanged.

Shelving filters are the digital equivalent of the bass and treble knobs on a stereo amplifier. In Chapter 0, you learned that a simple RC network creates a shelf. The digital version is a biquad with shelf coefficients.

```yaml
# CamillaDSP shelving filter examples
filters:
  bass_shelf:
    type: Biquad
    parameters:
      type: Lowshelf
      freq: 200
      gain: 3.0
      q: 0.707   # Q of 0.707 gives a smooth Butterworth-like shelf

  treble_shelf:
    type: Biquad
    parameters:
      type: Highshelf
      freq: 8000
      gain: -2.0
      q: 0.707
```

### 3.3 All-Pass Filters

An **all-pass filter** passes all frequencies at equal amplitude but changes the phase relationship. It doesn't boost or cut anything -- it shifts the phase of certain frequencies relative to others.

Why would you want to change phase without changing amplitude? **Time alignment.** If your woofer and tweeter are physically offset (the tweeter is recessed or the woofer's acoustic center is behind the baffle), their outputs arrive at the listening position at slightly different times. An all-pass filter can delay one driver's signal to align them.

In CamillaDSP, you more commonly use a `Delay` filter for time alignment (which adds a fixed time delay to a channel), but all-pass filters are useful for more subtle phase corrections within crossover regions.

```yaml
# CamillaDSP delay for time alignment
filters:
  tweeter_delay:
    type: Delay
    parameters:
      delay: 0.29   # milliseconds
      unit: ms
```

> **What happens if...** you forget time alignment in an active crossover? The woofer and tweeter outputs arrive at the listening position at slightly different times, causing a cancellation notch at the crossover frequency. The severity depends on the offset distance and the crossover frequency. At 2 kHz (wavelength ~17 cm), even a 2 cm offset creates noticeable interference. You'll hear a "hole" in the response right where the two drivers are supposed to blend smoothly. Measurement (Chapter 29) makes this obvious.

---

## 4. Dynamic Range Processing

### 4.1 Why Dynamics Processing Matters

Dynamic range processors change the relationship between input level and output level. They're essential for:
- **PA systems**: Limiters protect speakers from damage; compressors keep music sounding good at high volume in noisy environments
- **Headphone listening**: Compression can reduce dynamic range for listening in noisy environments (commuting, gym)
- **System protection**: A brick-wall limiter at the end of your DSP chain prevents your amplifier from seeing a signal that would damage your speakers

### 4.2 Compression

A **compressor** reduces the dynamic range of a signal by attenuating loud parts. When the signal exceeds a **threshold**, the compressor reduces the gain by a **ratio**.

Parameters:

| Parameter | What it Does | Typical Range |
|---|---|---|
| **Threshold** | The level above which compression begins | -40 to 0 dBFS |
| **Ratio** | How much the signal is reduced above threshold (e.g., 4:1 means 4 dB of input above threshold becomes 1 dB of output) | 1:1 (off) to ∞:1 (limiter) |
| **Attack** | How quickly the compressor responds to a signal exceeding the threshold | 0.1-100 ms |
| **Release** | How quickly the compressor stops compressing after the signal drops below threshold | 10-1000 ms |
| **Knee** | Hard knee (abrupt onset) vs soft knee (gradual onset) | 0-20 dB |
| **Makeup gain** | Gain applied after compression to restore average level | 0-20 dB |

**Attack and release** shape the character of compression more than any other parameters. Fast attack catches transients (drum hits, plucks) -- useful for limiting but can make music sound flat and lifeless. Slow attack lets transients through, preserving punch while compressing the sustained part. Fast release follows the signal closely (can cause "pumping" artifacts). Slow release provides smoother, more transparent gain reduction.

### 4.3 Limiting

A **limiter** is a compressor with a very high ratio (typically 10:1 to ∞:1) and very fast attack. Its purpose is to prevent the signal from exceeding a ceiling level. Limiters are essential for:

- **Speaker protection**: Setting a hard limit at the level where your amplifier clips or your speaker excursion limit is reached
- **Broadcast standards**: Ensuring audio doesn't exceed a maximum level
- **Mastering**: Setting the final peak level of a recording (the loudness war tool)

For your Pi streamer's active crossover, a limiter on each output channel protects your drivers:

```yaml
# Conceptual -- CamillaDSP doesn't have a built-in dynamics processor,
# but external processors or plugins can provide this.
# For speaker protection, you'd typically set this in the amplifier
# or use a hardware limiter.
```

Note: CamillaDSP (as of 2026) does not include built-in dynamics processing (compression/limiting). If you need dynamics on the Pi, you'd use an external tool in the signal chain (like LADSPA plugins via ALSA, or a separate processing step). For most home listening, you don't need compression. Limiting for speaker protection is better handled at the amplifier stage.

### 4.4 Expansion and Gating

**Expansion** is the opposite of compression -- it makes quiet parts quieter, increasing dynamic range. A signal below the threshold is attenuated by the ratio.

**Gating** is extreme expansion -- a signal below the threshold is silenced completely. Gates are used in live sound to cut microphone bleed (the drum mic gate closes between hits, rejecting the other instruments bleeding into it).

For the Pi streamer, expansion and gating are rarely needed. They're more relevant in live sound and recording contexts. But if you're building a PA system with the Pi (feeding the amplifiers from Part 3), a noise gate on the input can prevent amplifying hiss during silent periods.

### 4.5 The Signal Chain for Dynamics

When combining EQ and dynamics processing, order matters:

```
Input → EQ (shape tone) → Compressor (control dynamics) → Limiter (protect output) → Output
```

If you compress before EQ, the compressor reacts to the unequalized signal, which might not be what you want (a low-frequency peak would trigger compression even if you're about to cut it). If you EQ first, the compressor sees the signal you've already shaped.

The limiter always goes last -- it's the safety net that prevents anything downstream from seeing excessive levels.

---

## 5. Convolution: The Power Tool

### 5.1 What Convolution Is

**Convolution** is the process of applying an **impulse response** (IR) to a signal. Mathematically, it's the sum of the signal multiplied by a time-reversed, shifted copy of the impulse response. In practice, it's implemented using FFT (Fast Fourier Transform) for efficiency.

An impulse response captures the complete behavior of a system -- a room, a speaker, a reverb unit, anything that transforms audio. If you clap your hands in a cathedral, the echo pattern you hear is the room's impulse response. If you record that echo, you can apply it to any audio via convolution, and it will sound like it was played in the cathedral.

### 5.2 Convolution for Room Correction

This is the application that matters for your Pi streamer. The process:

1. **Measure the room's impulse response**: Place a calibrated measurement microphone at the listening position. Play a test signal (log sine sweep) through your speakers. Record the result. The difference between what you played and what you recorded is the room's impulse response.

2. **Invert the impulse response**: Calculate the mathematical inverse -- a filter that, when convolved with the room's response, produces a flat result. This is your correction filter.

3. **Apply the correction in CamillaDSP**: Load the correction filter as an FIR convolution. Every audio sample is convolved with the correction, compensating for the room's frequency response peaks and dips.

```yaml
# CamillaDSP convolution filter
filters:
  room_correction:
    type: Conv
    parameters:
      type: Raw
      filename: /home/pi/dsp/room_correction.raw
      format: FLOAT32LE
```

**What convolution can fix**: Frequency response peaks caused by room modes (standing waves between parallel surfaces), broad coloration from room reflections, some aspects of speaker response irregularity.

**What convolution can't fix**: Acoustic null points (where a room mode cancels, there's no energy to correct), reverberation/decay issues (convolution changes the frequency response at the listening position but doesn't change how the room rings), and problems that vary significantly from the measurement position (move your head 6 inches and the room response changes).

### 5.3 Convolution for Speaker Correction

You can also use convolution to correct your speaker's response independent of the room. Measure the speaker in a free-field (or quasi-anechoic) condition, generate an inverse filter, and apply it. This is particularly useful for correcting driver response irregularities that passive crossover design couldn't fully address.

### 5.4 Convolution Efficiency

Convolution is computationally expensive. An FIR filter with 65,536 taps applied naively requires 65,536 multiplications and additions per output sample. At 48 kHz stereo, that's ~6.3 billion operations per second.

**FFT-based convolution** (also called "fast convolution") makes this tractable. Instead of convolving in the time domain, the signal and the impulse response are both transformed to the frequency domain (via FFT), multiplied element-by-element (which is much simpler), and transformed back to the time domain. CamillaDSP uses this approach.

On a Pi 4, CamillaDSP can handle convolution filters with tens of thousands of taps without breaking a sweat. A Pi 5 is even more capable. You won't run into CPU limits for typical room correction use cases.

---

## 6. Sample Rate Conversion

### 6.1 When SRC Happens

**Sample rate conversion** (SRC, also called resampling) is necessary when audio at one sample rate needs to play through a system running at a different rate. Common scenarios:

- Your FLAC library is 44.1 kHz, but your DAC is running at 48 kHz
- You're mixing sources at different rates (Spotify at 44.1 kHz, a podcast at 48 kHz)
- Your DSP pipeline runs at a fixed rate and all input must be converted

### 6.2 How Good SRC Works

Changing sample rate is not as simple as dropping or inserting samples. Proper SRC involves:

1. **Upsampling**: Insert zeros between existing samples (zero-stuffing) to increase the sample rate by an integer factor
2. **Low-pass filtering**: Apply an anti-aliasing/anti-imaging filter to remove artifacts from the upsampling
3. **Downsampling**: Remove excess samples to reach the target rate

When the ratio isn't an integer (like 44.1 kHz → 48 kHz, which is a ratio of 160/147), the process uses **polyphase filtering** -- a highly efficient technique that combines upsampling, filtering, and downsampling into a single operation.

### 6.3 SRC Quality Matters

Bad SRC introduces aliasing and imaging artifacts. Good SRC is transparent. The quality spectrum:

| Quality | Implementation | Artifacts |
|---|---|---|
| Terrible | Nearest-neighbor (just drop/repeat samples) | Audible distortion, aliasing |
| Poor | Linear interpolation | Reduced high-frequency content, mild aliasing |
| Good | Polyphase with moderate filter | Transparent for most content |
| Excellent | libsamplerate "Best" (SRC_SINC_BEST_QUALITY) | Unmeasurable artifacts |
| Transparent | SOX "Very High Quality" | Reference-quality, inaudible artifacts |

CamillaDSP can perform sample rate conversion using its built-in resampler (based on libsamplerate). moOde Audio also has SRC options in its configuration.

```yaml
# CamillaDSP resampler configuration
devices:
  samplerate: 48000
  resampler:
    type: Synchronous
    profile: Balanced  # Options: Fast, Balanced, Accurate
```

**Practical advice**: Configure your DAC to run at the native sample rate of your most common content (44.1 kHz for CD-ripped libraries, 48 kHz if you have a mix). If your player software supports it, use "native" sample rate mode where the DAC switches to match each track's rate. This avoids SRC entirely -- the best conversion is no conversion.

> **What happens if...** your system does SRC from 44.1 to 48 kHz using a poor algorithm? You'll get subtle but measurable aliasing -- frequency content just below the Nyquist frequency (22.05 kHz) will alias to audible frequencies. In practice, most music has very little energy above 16 kHz, so the artifacts may be inaudible. But why introduce any degradation when the alternative (running the DAC at native rate or using good SRC) is trivial?

---

## 7. CamillaDSP: The Tool We'll Use

### 7.1 What CamillaDSP Is

**CamillaDSP** is an open-source, cross-platform audio DSP engine written in Rust. It's designed for exactly our use case: real-time audio processing on Linux (specifically on the Pi). It sits in the audio path between your music player and your DAC, processing every sample in real time.

CamillaDSP processes audio as 64-bit floating point internally, so you have essentially unlimited headroom for cascading multiple filters without worrying about internal clipping.

### 7.2 Architecture

```
Audio Source (MPD/Spotify/AirPlay)
    ↓
ALSA Loopback Device (virtual soundcard)
    ↓ (capture)
CamillaDSP
    ├── Input: reads from ALSA loopback
    ├── Processing Pipeline:
    │   ├── Filter step 1 (e.g., parametric EQ)
    │   ├── Filter step 2 (e.g., crossover HPF)
    │   ├── Filter step 3 (e.g., crossover LPF)
    │   ├── Filter step 4 (e.g., delay)
    │   └── Filter step 5 (e.g., convolution)
    └── Output: writes to real DAC via ALSA
    ↓
DAC HAT (I2S → analog)
    ↓
Amplifier → Speakers
```

The **ALSA loopback** is key. It's a virtual soundcard built into the Linux kernel. Music player software outputs to the loopback device (thinking it's a real soundcard). CamillaDSP captures from the loopback, processes the audio, and outputs to the real DAC hardware. The music player never knows the DSP is there.

### 7.3 Configuration Format

CamillaDSP uses YAML configuration files. A configuration defines:

1. **Devices**: Input (capture) and output (playback) ALSA devices, sample rate, buffer sizes
2. **Filters**: Individual filter definitions (biquads, convolution, delay, gain, dither)
3. **Mixers** (optional): Channel routing (e.g., stereo to 4-channel for an active crossover)
4. **Pipeline**: The ordered sequence of processing steps applied to each channel

Here's a minimal example -- a two-band parametric EQ:

```yaml
---
devices:
  samplerate: 44100
  chunksize: 1024
  capture:
    type: Alsa
    channels: 2
    device: "hw:Loopback,1"
    format: S32LE
  playback:
    type: Alsa
    channels: 2
    device: "hw:sndrpihifiberry"
    format: S32LE

filters:
  low_shelf:
    type: Biquad
    parameters:
      type: Lowshelf
      freq: 120
      gain: 2.0
      q: 0.707

  presence_cut:
    type: Biquad
    parameters:
      type: Peaking
      freq: 2800
      gain: -3.0
      q: 1.5

pipeline:
  - type: Filter
    channel: 0
    names:
      - low_shelf
      - presence_cut
  - type: Filter
    channel: 1
    names:
      - low_shelf
      - presence_cut
```

### 7.4 Setting Up ALSA Loopback

The ALSA loopback module needs to be loaded:

```bash
# Load the loopback module
sudo modprobe snd-aloop

# Make it persistent across reboots
echo "snd-aloop" | sudo tee -a /etc/modules

# The loopback creates two devices:
# hw:Loopback,0 — the "playback" side (music player outputs here)
# hw:Loopback,1 — the "capture" side (CamillaDSP reads from here)
```

Then configure your music player (MPD) to output to the loopback:

```bash
# In /etc/mpd.conf, change the audio output:
audio_output {
    type        "alsa"
    name        "CamillaDSP"
    device      "hw:Loopback,0"
    format      "44100:32:2"
    mixer_type  "none"
}
```

### 7.5 Running CamillaDSP

```bash
# Install CamillaDSP (download the ARM binary from GitHub releases)
wget https://github.com/HEnquist/camilladsp/releases/download/v2.0.3/camilladsp-linux-aarch64.tar.gz
tar xzf camilladsp-linux-aarch64.tar.gz
sudo mv camilladsp /usr/local/bin/

# Run it
camilladsp /path/to/config.yml

# Or as a systemd service
sudo tee /etc/systemd/system/camilladsp.service << 'EOF'
[Unit]
Description=CamillaDSP audio processor
After=sound.target

[Service]
Type=simple
ExecStart=/usr/local/bin/camilladsp -p 1234 /etc/camilladsp/config.yml
Restart=on-failure
User=pi

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable --now camilladsp
```

The `-p 1234` flag enables the websocket control port, which lets you change the configuration (including filter coefficients) in real time without restarting. This is useful for the CamillaDSP web UI (a separate project) and for your own control scripts.

### 7.6 CamillaDSP Web GUI

The **CamillaDSP GUI** is a separate web application that provides a browser-based interface for editing CamillaDSP configurations. It connects via websocket to CamillaDSP's control port and lets you:

- Adjust filter parameters with sliders and see the response curve
- View the combined frequency response of all filters
- Monitor signal levels and clipping indicators
- Switch between different configuration presets

```bash
# Install the CamillaDSP GUI
git clone https://github.com/HEnquist/camillagui-backend.git
cd camillagui-backend
pip3 install -r requirements.txt
python3 main.py --port 5000 --camilla-host 127.0.0.1 --camilla-port 1234
# Access at http://pi-ip:5000
```

moOde Audio includes CamillaDSP integration in its UI, which simplifies setup considerably.

---

## 8. Signal Flow: The Order of Operations

### 8.1 Why Order Matters

In a DSP pipeline, the order in which you apply filters affects the result. This is especially critical for active crossovers:

```
CORRECT ORDER:
Input → Volume/Gain → Input EQ → Crossover → Per-Driver EQ → Time Alignment → Limiter → Output

WRONG ORDER:
Input → Crossover → Volume → EQ → Output
(Why wrong: Volume after crossover means you can't adjust the level without changing the crossover behavior.
EQ after crossover but not per-driver means you're EQing the wrong frequency range.)
```

### 8.2 The Reference Pipeline

For an active two-way crossover with room correction (what we'll build in Chapter 29):

```
┌─────────────────────────────────────────────────────────────────┐
│                    CamillaDSP Pipeline                          │
│                                                                 │
│  Stereo Input (2 channels)                                      │
│       │                                                         │
│       ▼                                                         │
│  [1] Master Gain (-6 dB headroom)                               │
│       │                                                         │
│       ▼                                                         │
│  [2] Room Correction EQ (parametric or convolution)             │
│       │                                                         │
│       ▼                                                         │
│  [3] Mixer: 2ch → 4ch (duplicate L/R for woofer and tweeter)   │
│       │                                                         │
│       ├── Ch 0: Left Woofer                                     │
│       │    ├── [4a] Crossover LPF (2kHz LR4)                   │
│       │    ├── [5a] Per-driver EQ                               │
│       │    └── [6a] Delay (time alignment)                      │
│       │                                                         │
│       ├── Ch 1: Right Woofer                                    │
│       │    ├── [4b] Crossover LPF (2kHz LR4)                   │
│       │    ├── [5b] Per-driver EQ                               │
│       │    └── [6b] Delay (time alignment)                      │
│       │                                                         │
│       ├── Ch 2: Left Tweeter                                    │
│       │    ├── [4c] Crossover HPF (2kHz LR4)                   │
│       │    ├── [5c] Per-driver EQ                               │
│       │    └── [6c] Delay (time alignment)                      │
│       │                                                         │
│       └── Ch 3: Right Tweeter                                   │
│            ├── [4d] Crossover HPF (2kHz LR4)                   │
│            ├── [5d] Per-driver EQ                               │
│            └── [6d] Delay (time alignment)                      │
│                                                                 │
│  4-Channel Output → Multi-Channel DAC → Separate Amps          │
└─────────────────────────────────────────────────────────────────┘
```

Each numbered step is a filter or mixer stage in the CamillaDSP pipeline. The YAML configuration for this full pipeline is covered in detail in Chapter 29.

### 8.3 Headroom Management

When you apply EQ boosts, the signal level can exceed 0 dBFS (digital full scale). In fixed-point digital audio, this causes clipping -- hard, ugly distortion. In CamillaDSP's 64-bit float processing, internal clipping isn't possible, but the output stage converts back to the DAC's format (typically 24-bit or 32-bit integer), and *that* can clip.

**Solution**: Add a negative gain stage at the beginning of your pipeline. If your maximum EQ boost is +6 dB, add -6 dB of gain before the EQ stage. This gives you headroom for the boosts without ever clipping the output.

```yaml
filters:
  headroom:
    type: Gain
    parameters:
      gain: -6.0
      inverted: false

pipeline:
  - type: Filter
    channel: 0
    names:
      - headroom
      - eq_band_1
      - eq_band_2
      # ... rest of pipeline
```

> **What happens if...** you skip headroom management and apply a +10 dB boost at 50 Hz for bass correction? Music with strong bass content will clip at the DAC output. The clipping happens only on bass peaks (since that's where the boost pushes the signal over 0 dBFS), so it manifests as harsh distortion on kick drums and bass notes. CamillaDSP shows a red clipping indicator in its web GUI. Add -10 dB of headroom gain before the boost, and the problem disappears.

---

## 9. Practical DSP Recipes

### 9.1 Simple Two-Band Tonal Shaping

"I want a bit more bass warmth and slightly less treble bite."

```yaml
filters:
  warm_bass:
    type: Biquad
    parameters:
      type: Lowshelf
      freq: 150
      gain: 2.5
      q: 0.707

  smooth_treble:
    type: Biquad
    parameters:
      type: Highshelf
      freq: 6000
      gain: -1.5
      q: 0.707
```

### 9.2 Room Mode Notch

"My room has a booming resonance at 42 Hz."

```yaml
filters:
  room_mode_42:
    type: Biquad
    parameters:
      type: Peaking
      freq: 42
      gain: -8.0
      q: 6.0
```

### 9.3 Loudness Compensation (Low-Volume Bass Boost)

Human hearing is less sensitive to bass at low volumes (Fletcher-Munson curves). A "loudness" contour boosts bass at low listening levels to compensate:

```yaml
filters:
  loudness_bass:
    type: Biquad
    parameters:
      type: Lowshelf
      freq: 100
      gain: 6.0
      q: 0.5

  loudness_presence:
    type: Biquad
    parameters:
      type: Peaking
      freq: 3000
      gain: 2.0
      q: 0.8
```

Apply this when listening quietly; bypass it at normal volumes. CamillaDSP's websocket API lets your control scripts toggle filters dynamically -- tie this to your volume knob so the loudness contour automatically scales with volume level.

### 9.4 Subwoofer Integration (Bass Management)

Route everything below 80 Hz to a subwoofer output, everything above to the main speakers:

```yaml
filters:
  sub_lpf:
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
  # Two cascaded 2nd-order = 4th-order Linkwitz-Riley

  main_hpf:
    type: Biquad
    parameters:
      type: HighpassQ
      freq: 80
      q: 0.707
  main_hpf_2:
    type: Biquad
    parameters:
      type: HighpassQ
      freq: 80
      q: 0.707
```

---

## 10. Crossover Types: A Deeper Look

### 10.1 Why Linkwitz-Riley?

In Chapter 29, we'll implement a Linkwitz-Riley crossover. Here's why it's the standard for active crossovers, compared to the alternatives:

**Butterworth crossover**: A first-order Butterworth crossover sums to flat, but it only has 6 dB/octave slopes -- far too gentle for protecting a tweeter from bass energy. Higher-order Butterworth crossovers don't sum flat: they create a +3 dB bump at the crossover frequency because both outputs are at -3 dB at the crossover point, and they're in phase.

**Linkwitz-Riley (LR)**: Named after Siegfried Linkwitz and Russ Riley, who published the design in 1976. LR crossovers are constructed by cascading Butterworth sections. The key property: at the crossover frequency, both the LP and HP outputs are at -6 dB. When summed, they produce 0 dB -- perfectly flat. And both outputs are in phase at the crossover point, so there's no cancellation.

| Crossover Type | At Fc | Sum at Fc | In Phase? | Slope |
|---|---|---|---|---|
| Butterworth 1st order | -3 dB each | +3 dB bump | Yes | 6 dB/oct |
| Butterworth 2nd order | -3 dB each | +3 dB bump | No (180 phase) | 12 dB/oct |
| Linkwitz-Riley 2nd (LR2) | -6 dB each | 0 dB (flat) | Yes | 12 dB/oct |
| Linkwitz-Riley 4th (LR4) | -6 dB each | 0 dB (flat) | Yes | 24 dB/oct |
| Linkwitz-Riley 8th (LR8) | -6 dB each | 0 dB (flat) | Yes | 48 dB/oct |
| Bessel | -3 dB each | Varies | Varies | 12-24 dB/oct |

**LR4 (24 dB/octave)** is the sweet spot for most active crossovers. It's steep enough to protect the tweeter from low-frequency energy, sums flat, and maintains phase coherence at the crossover point. LR2 is usable but gentler. LR8 is steeper but rarely needed.

### 10.2 Choosing the Crossover Frequency

The crossover frequency depends on your drivers:

- **Tweeter lower limit**: Most dome tweeters can handle down to ~1.5-2.5 kHz. The crossover frequency should be at least 1-2 octaves above the tweeter's resonant frequency (Fs) to avoid driving it at resonance.
- **Woofer upper limit**: Woofers begin to beam (radiate in a narrowing pattern) as the frequency increases. The rule of thumb: crossover when the woofer's cone diameter equals the wavelength. For a 6.5" woofer, that's roughly 2 kHz. For an 8" woofer, roughly 1.7 kHz.
- **Power handling**: The higher the crossover frequency, the more power the woofer handles (since it reproduces a wider band). The lower the crossover, the more the tweeter must handle.

For a typical 2-way with a 6.5" woofer and 1" dome tweeter, 2-2.5 kHz is the standard range. This is what we'll use in Chapter 29.

### 10.3 Crossover and Phase: Why Active Wins

In a passive crossover, the phase shift through the inductors and capacitors interacts with the drivers' own phase response (which varies with frequency and physical position). The result is unpredictable phase behavior at the crossover region, leading to off-axis lobing and uneven power response.

In a DSP active crossover, you control the phase precisely. You can add all-pass filters or delay to align the phase of both drivers at the crossover frequency. You can measure the combined response at the listening position and adjust until the crossover is seamless. This level of control is simply impossible with passive components.

> **What happens if...** you use a Butterworth crossover instead of Linkwitz-Riley in your active system? You'll get a +3 dB bump at the crossover frequency (because both drivers are at -3 dB and in phase, their sum is +3 dB). That bump means the crossover region is louder than the rest of the spectrum -- a coloration in the upper midrange that makes voices sound forward and aggressive. LR eliminates this by design.

---

## 11. Visualizing Filter Responses

### 11.1 Frequency Response Plots

When designing filters, you need to see their effect before applying them. CamillaDSP's web GUI shows the combined frequency response of all filters. But for learning, it's useful to plot individual filters.

Python makes this easy with `scipy.signal`:

```python
#!/usr/bin/env python3
"""Plot the frequency response of a biquad filter."""

import numpy as np
from scipy.signal import freqz
import matplotlib.pyplot as plt

def biquad_peaking(freq, gain_db, q, fs=48000):
    """Calculate biquad coefficients for a peaking EQ filter."""
    A = 10 ** (gain_db / 40)
    w0 = 2 * np.pi * freq / fs
    alpha = np.sin(w0) / (2 * q)

    b0 = 1 + alpha * A
    b1 = -2 * np.cos(w0)
    b2 = 1 - alpha * A
    a0 = 1 + alpha / A
    a1 = -2 * np.cos(w0)
    a2 = 1 - alpha / A

    return [b0/a0, b1/a0, b2/a0], [1, a1/a0, a2/a0]

# Example: peaking filter at 1 kHz, +6 dB, Q=2
b, a = biquad_peaking(1000, 6.0, 2.0)
w, h = freqz(b, a, worN=8192, fs=48000)

plt.figure(figsize=(10, 4))
plt.semilogx(w, 20 * np.log10(np.abs(h)))
plt.xlabel("Frequency (Hz)")
plt.ylabel("Gain (dB)")
plt.title("Peaking EQ: 1 kHz, +6 dB, Q=2")
plt.xlim(20, 20000)
plt.ylim(-10, 10)
plt.grid(True, which="both", alpha=0.3)
plt.axhline(y=0, color="gray", linewidth=0.5)
plt.show()
```

This script uses the same Bristow-Johnson formulas that CamillaDSP uses internally. Being able to visualize filters helps you understand what each parameter does before you apply it to your system.

### 11.2 Phase Response

Every IIR filter introduces phase shift. The `freqz` function returns complex values -- the phase is `np.angle(h)`:

```python
plt.figure(figsize=(10, 4))
plt.semilogx(w, np.degrees(np.angle(h)))
plt.xlabel("Frequency (Hz)")
plt.ylabel("Phase (degrees)")
plt.title("Phase Response: Peaking EQ at 1 kHz")
plt.xlim(20, 20000)
plt.grid(True, which="both", alpha=0.3)
plt.show()
```

Understanding phase response is essential for active crossover design. When you cascade crossover filters with driver EQ, the combined phase response determines whether the drivers sum constructively or destructively at the crossover point. Measurement and iteration (Chapter 29) are how you verify that the theory matches reality.

---

## 12. DSP Processing Power on the Pi

### 12.1 How Much Can the Pi Handle?

CamillaDSP is written in Rust and optimized for real-time audio processing. The Pi's ARM processor handles typical audio DSP workloads with ease:

| Processing Task | CPU Usage (Pi 4) | CPU Usage (Pi 5) |
|---|---|---|
| 10 biquad filters (5 per channel, stereo) | ~1% | <1% |
| 30 biquad filters (active crossover + EQ) | ~3% | ~1% |
| FIR convolution, 4096 taps, stereo | ~5% | ~2% |
| FIR convolution, 65536 taps, stereo | ~15% | ~6% |
| Full active crossover + room correction + EQ | ~10-20% | ~5-10% |

The Pi 4 has a quad-core Cortex-A72 at 1.5 GHz. CamillaDSP uses SIMD (NEON) instructions on ARM for FFT-based convolution, which dramatically accelerates the computation. You have significant headroom for complex processing pipelines.

The Pi 5 (Cortex-A76 at 2.4 GHz) is roughly 2-3x faster for DSP workloads. If you plan to do heavy FIR convolution (very long room correction filters) or process many channels (three-way crossover = 6 channels), the Pi 5 is the better choice.

### 12.2 Buffer Size and Latency Tradeoff

CamillaDSP's `chunksize` parameter controls the buffer size. Larger buffers mean more latency but more efficient processing (fewer context switches). Smaller buffers mean less latency but higher CPU usage.

| Chunk Size | Latency at 48 kHz | CPU Efficiency | Use Case |
|---|---|---|---|
| 256 | 5.3 ms | Lower | Live monitoring, low-latency applications |
| 1024 | 21.3 ms | Good | Standard music playback (recommended) |
| 4096 | 85.3 ms | Highest | Heavy convolution, maximum efficiency |

For music playback, 1024 is the sweet spot. The 21ms latency is completely imperceptible -- you'd need a stopwatch to measure the delay between pressing "play" and hearing the first note.

---

## Summary

Digital audio processing replaces passive analog circuits with mathematical operations. The two fundamental filter types -- FIR (no feedback, can be linear phase, higher latency) and IIR (uses feedback, minimum phase like analog, very efficient) -- are the building blocks. Parametric EQ (frequency, gain, Q) is the workhorse for tonal shaping and frequency response correction. Dynamic range processing (compression, limiting) is essential for PA systems and speaker protection. Convolution applies an impulse response to the signal, enabling room correction and speaker correction with arbitrary precision.

CamillaDSP is the open-source tool we'll use on the Pi. It sits in the audio path via an ALSA loopback device, processes audio in 64-bit float, and supports biquad filters, FIR convolution, delays, gain, and channel mixing. Its YAML configuration defines the complete processing pipeline, and its websocket API enables real-time control.

The order of operations in a DSP pipeline matters: gain/headroom first, then input EQ, then crossover, then per-driver EQ, then time alignment, then limiting. Getting this order right is the difference between a system that sounds correct and one that sounds wrong in subtle, hard-to-diagnose ways.

In Chapter 28, we build the Pi streamer hardware and get it playing music. In Chapter 29, we add CamillaDSP to implement an active crossover with room correction -- putting everything in this chapter into practice.
