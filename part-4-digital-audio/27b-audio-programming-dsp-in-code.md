<!--
  CHAPTER: 27b
  TITLE: Audio Programming — Writing DSP Effects in Code
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 27a
  KEY_TOPICS: real-time audio, delay line, reverb, distortion, filters, guitar tuner, convolution, synthesizer
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 27b: Audio Programming — Writing DSP Effects in Code

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 27a | Difficulty: Inter→Adv

You've spent the last two chapters learning the *theory* of DSP. You know what an FIR filter is, what convolution does, how the Fourier transform decomposes a signal into frequencies. That knowledge lives in your head as math -- equations, diagrams, maybe some intuitive understanding of what "convolving with an impulse response" actually means.

This chapter is where that math becomes *sound*.

We're going to write audio effects in Python -- real, working, play-your-guitar-through-them effects. Distortion, delay, reverb, chorus, tremolo, filters, a guitar tuner, convolution reverb, and a basic synthesizer. Every section has code you can run *right now*. Plug your guitar into an audio interface, run the script, and hear the effect. Tweak a number, hear the change. Break something, hear *that* too.

This is the most fun chapter in the guide. You've built analog tremolo circuits with LFOs and resistors (Chapter 10). You've built distortion pedals with diode clipping networks (Chapter 9). Now you're going to build the *same effects* in 20 lines of Python and realize: oh, that's all it is. The math is the circuit. The circuit is the math. They're the same thing, just expressed in different substrates.

By the end of this chapter, you'll have written every major category of audio effect from scratch. You'll understand why they sound the way they do, because you'll have controlled every parameter. And you'll have the foundation to explore serious DSP platforms -- JUCE, Faust, Rust audio, embedded DSP on hardware -- with the confidence that comes from having built it all yourself first.

### In This Chapter
- Real-time audio processing in Python with `sounddevice`
- Distortion: hard clipping, soft clipping, asymmetric clipping
- Delay: circular buffers, feedback, dry/wet mix
- Reverb: Schroeder architecture from comb and allpass filters
- Tremolo and chorus: LFO-modulated effects
- FIR and IIR filters: low-pass, band-pass, sweepable wah
- Guitar tuner: autocorrelation-based pitch detection
- Convolution reverb: your guitar in a cathedral
- Simple synthesizer: oscillators, FM synthesis, ADSR envelopes
- Where to go deeper: JUCE, Faust, Rust, embedded DSP

### Related Chapters
- [Ch 6: Gain Stages and Clipping Circuits](../part-1-guitar-pedals/06-gain-stages-clipping-circuits.md) -- the analog version of the distortion effect you'll code
- [Ch 10: Build: Modulation and Time-Based Effects](../part-1-guitar-pedals/10-build-modulation-time-effects.md) -- the analog version of tremolo and chorus you'll code
- [Ch 13: Crossover Design](../part-2-speakers/13-crossover-design.md) -- crossovers are filters; you'll write filters in code
- [Ch 27: DSP Fundamentals for Audio](27-dsp-fundamentals.md) -- theory of FIR/IIR/convolution, now implemented
- [Ch 27a: Fourier Transforms](27a-fourier-transforms.md) -- the math behind the FFT you're using everywhere
- [Ch 29: Build: Active Crossover and Room Correction](29-build-active-crossover-room-correction.md) -- CamillaDSP uses convolution, just like your convolution reverb

---

## 1. Real-Time Audio in Python

### 1.1 The Audio Callback Model

Here's the fundamental concept of real-time audio programming, regardless of language or platform: the audio system calls *your* function with a buffer of samples, you process them, and you hand them back. That's it. That's the entire model.

The library we'll use is `sounddevice`, a Python wrapper around PortAudio (the same library that powers Audacity, SuperCollider, and dozens of other audio applications). The flow looks like this:

```
Audio Input (mic/guitar)
    ↓
Soundcard captures N samples into a buffer
    ↓
sounddevice calls YOUR callback function
    ↓
Your function processes the buffer (distortion, delay, whatever)
    ↓
Your function writes the result to the output buffer
    ↓
Soundcard plays the output buffer through speakers/headphones
    ↓
Repeat forever, thousands of times per second
```

Your callback function must finish processing the buffer before the next buffer is ready. If it takes too long, you get an audible glitch -- a click, a pop, a dropout. This is the real-time constraint, and it's the same whether you're writing Python, C++, or assembly.

### 1.2 Buffer Size vs Latency

The buffer size determines your latency -- the time between playing a note on your guitar and hearing it in your headphones.

| Buffer Size (samples) | Latency at 44.1 kHz | Latency at 48 kHz | Notes |
|---|---|---|---|
| 64 | 1.5 ms | 1.3 ms | Extremely tight; may glitch on Python |
| 128 | 2.9 ms | 2.7 ms | Aggressive; works in C++, risky in Python |
| 256 | 5.8 ms | 5.3 ms | Good for Python with simple effects |
| 512 | 11.6 ms | 10.7 ms | Safe for Python; noticeable but usable |
| 1024 | 23.2 ms | 21.3 ms | Very safe; slight delay feeling |
| 2048 | 46.4 ms | 42.7 ms | Too much for live playing; fine for experimenting |

For playing guitar through effects, you want latency under about 10 ms. Above that, you start to feel a disconnect between your pick hitting the string and the sound arriving in your headphones. We'll use 512 samples as our default -- it's a safe choice for Python on most machines, and the ~11 ms latency is tolerable for noodling.

Professional audio plugins in C++ run at 64 or 128 samples. Python will never compete on latency, but it's unbeatable for rapid prototyping and learning. We're here to understand the DSP, not to ship a product.

### 1.3 Setup

Install the dependencies:

```bash
pip install sounddevice numpy scipy
```

You'll need an audio interface with an instrument input (any cheap USB interface works -- a Focusrite Scarlett Solo, a Behringer UMC22, whatever you have). Plug your guitar into the input, plug headphones into the output.

Check that `sounddevice` can see your interface:

```python
import sounddevice as sd
print(sd.query_devices())
```

This prints a list of available audio devices. Find your interface and note its index number. You'll use it to set the default device:

```python
sd.default.device = [1, 1]  # [input_device_index, output_device_index]
sd.default.samplerate = 44100
sd.default.channels = 1
```

### 1.4 The Simplest Effect: Passthrough

Before you write any DSP, verify that audio goes in and comes out. This is the "hello world" of audio programming:

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

def callback(indata, outdata, frames, time, status):
    if status:
        print(status)
    outdata[:] = indata

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print("Passthrough running. Press Enter to stop.")
    input()
```

That's 15 lines. Run it, play your guitar, hear your guitar in the headphones. If you hear yourself, everything is wired up correctly. If you don't, check `sd.query_devices()` and make sure you've set the right device.

The `callback` function receives `indata` (a numpy array of input samples), and you copy it directly to `outdata`. That's passthrough -- input equals output. Every effect we build from here modifies what happens between `indata` and `outdata`.

> **What happens if...** you set `BUFFER_SIZE = 64`? On most machines running Python, you'll get frequent audio glitches -- tiny clicks and pops as the callback occasionally takes too long. The `status` parameter will print `input underflow` or `output underflow` messages. Increase the buffer size until the glitches stop. This is the latency/reliability trade-off in action.

---

## 2. Write a Distortion Effect

### 2.1 Hard Clipping

In Chapter 6, you learned that distortion pedals work by amplifying the signal until it exceeds the voltage limits of the circuit, then clipping the peaks. Diodes to ground (shunt clipping) or in the feedback loop of an op-amp create the ceiling. The signal hits the diode's forward voltage and goes no further.

In code, this is one line:

```python
clipped = np.clip(signal * gain, -threshold, threshold)
```

That's it. `gain` amplifies the signal (like the op-amp stage), and `np.clip` enforces the ceiling (like the diodes). Everything above `+threshold` gets flattened to `threshold`. Everything below `-threshold` gets flattened to `-threshold`.

### 2.2 Soft Clipping

Hard clipping sounds harsh and buzzy -- it's the RAT, the HM-2, the "saw the waveform flat" sound. For a smoother distortion, use a function that *gradually* rounds off the peaks instead of chopping them abruptly. The hyperbolic tangent (`tanh`) is the classic choice:

```python
soft_clipped = np.tanh(gain * signal)
```

`tanh` maps any input to the range (-1, +1). For small inputs, it's nearly linear (clean signal). As the input increases, it gradually compresses toward the limits. The transition is smooth, generating lower-order harmonics -- closer to tube saturation than transistor clipping.

This is the software version of what happens in a Tube Screamer's feedback clipping network (Chapter 6, Section 2.4). The diodes in the feedback loop create a similar gradual compression curve. Different diodes (silicon, germanium, LED) have different knee shapes; `tanh` is the mathematical idealization.

### 2.3 Asymmetric Clipping

Real circuits often clip asymmetrically -- the positive and negative peaks hit different ceilings. In analog, this happens when you use different diode types (or different numbers of diodes) for each polarity. In a single-transistor gain stage (Chapter 6, Section 1), asymmetry is inherent because the transistor's transfer curve isn't symmetric around the bias point.

Asymmetric clipping generates even-order harmonics (2nd, 4th), which sound "warm" and "musical." Symmetric clipping generates only odd-order harmonics (3rd, 5th, 7th), which sound "hollow" or "aggressive" at high gain.

```python
def asymmetric_clip(signal, gain, pos_threshold, neg_threshold):
    amplified = signal * gain
    clipped = np.where(amplified > pos_threshold, pos_threshold, amplified)
    clipped = np.where(clipped < -neg_threshold, -neg_threshold, clipped)
    return clipped
```

### 2.4 Complete Real-Time Distortion Effect

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
GAIN = 20.0          # How much to amplify before clipping (1 = clean, 50 = wall of fuzz)
MODE = "soft"        # "hard", "soft", or "asymmetric"
THRESHOLD = 0.3      # Clipping threshold for hard/asymmetric modes
ASYM_POS = 0.4       # Positive threshold for asymmetric mode
ASYM_NEG = 0.2       # Negative threshold for asymmetric mode
OUTPUT_LEVEL = 0.3   # Master volume (clipping makes things loud)
# --------------------

def callback(indata, outdata, frames, time, status):
    if status:
        print(status)

    signal = indata[:, 0]

    if MODE == "hard":
        processed = np.clip(signal * GAIN, -THRESHOLD, THRESHOLD)
    elif MODE == "soft":
        processed = np.tanh(signal * GAIN)
    elif MODE == "asymmetric":
        amplified = signal * GAIN
        processed = np.where(amplified > ASYM_POS, ASYM_POS, amplified)
        processed = np.where(processed < -ASYM_NEG, -ASYM_NEG, processed)
    else:
        processed = signal

    outdata[:, 0] = processed * OUTPUT_LEVEL

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Distortion running: mode={MODE}, gain={GAIN}")
    print("Press Enter to stop.")
    input()
```

Run this. Play guitar. Now change `MODE` to `"hard"`, restart, play the same riff. Hear the difference? Soft clipping rounds off the attack; hard clipping adds fizz and high-frequency content. Change `GAIN` from 5 to 50 -- hear how the character transforms from "a little grit" to "sustained fuzz." This is the same progression you heard going from a clean boost to a Fuzz Face in Chapter 8, but now you control every parameter explicitly.

> **What happens if...** you set `GAIN = 1.0` and `THRESHOLD = 1.0`? No clipping happens -- the signal passes through clean. Distortion only occurs when the amplified signal exceeds the threshold. This is exactly what happens in an analog circuit too: the gain stage must push the signal past the diodes' forward voltage before clipping begins. Below that voltage, the pedal is basically a clean boost.

---

## 3. Write a Delay Effect

### 3.1 The Circular Buffer (Ring Buffer)

A delay effect needs memory. You play a note, the effect stores it, and plays it back later. The most efficient data structure for this is a **circular buffer** (also called a ring buffer): a fixed-size array with a write pointer that wraps around to the beginning when it reaches the end.

```
Buffer:  [s0] [s1] [s2] [s3] [s4] [s5] [s6] [s7]
                     ↑ read                ↑ write

Write pointer: where new samples go in
Read pointer: where delayed samples come out
Distance between them: the delay time
```

When the write pointer reaches the end of the buffer, it wraps back to position 0 and starts overwriting the oldest samples. This gives you a continuously flowing delay line with no memory allocation -- just pointer arithmetic.

### 3.2 Delay Time in Samples

If you want a 300ms delay at 44.1 kHz:

```
delay_samples = delay_time_seconds * sample_rate
delay_samples = 0.3 * 44100 = 13230 samples
```

Your buffer needs to be at least 13,230 samples long. For flexibility (in case you want longer delays), make it bigger -- say, 2 seconds' worth (88,200 samples).

### 3.3 Feedback

A single echo is just a delay. Multiple repeating echoes require **feedback** -- you mix the delayed signal back into the buffer, so it gets delayed again, and again, getting quieter each time.

The feedback amount (0.0 to just below 1.0) controls how many repeats you hear. At 0.0, one echo. At 0.5, the echo repeats many times, each at half the volume of the previous. At 0.99, the echoes go on for a very long time. At 1.0 or above, the signal never decays -- infinite repeats that eventually clip and turn into noise. (This is "oscillation" in analog delay pedals. Same principle.)

### 3.4 Complete Delay Effect

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
DELAY_TIME = 0.35     # Delay time in seconds
FEEDBACK = 0.45       # Feedback amount (0.0 to 0.95 -- don't go above 0.95)
MIX = 0.5             # Dry/wet mix (0.0 = all dry, 1.0 = all wet)
# --------------------

MAX_DELAY = 2.0  # Maximum delay time in seconds (sets buffer size)
buffer_length = int(MAX_DELAY * SAMPLE_RATE)
delay_buffer = np.zeros(buffer_length)
write_pos = 0

delay_samples = int(DELAY_TIME * SAMPLE_RATE)

def callback(indata, outdata, frames, time, status):
    global write_pos

    if status:
        print(status)

    for i in range(frames):
        # Read from the delay buffer
        read_pos = (write_pos - delay_samples) % buffer_length
        delayed = delay_buffer[read_pos]

        # Current input sample
        dry = indata[i, 0]

        # Write input + feedback into the delay buffer
        delay_buffer[write_pos] = dry + delayed * FEEDBACK

        # Mix dry and wet signals
        outdata[i, 0] = dry * (1.0 - MIX) + delayed * MIX

        # Advance write pointer
        write_pos = (write_pos + 1) % buffer_length

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Delay running: time={DELAY_TIME}s, feedback={FEEDBACK}, mix={MIX}")
    print("Press Enter to stop.")
    input()
```

Play guitar. Hear the echoes. Crank `FEEDBACK` to 0.85 and play a single note -- hear it cascade into a wash of repeats, each slightly quieter than the last. Set `MIX` to 1.0 and you hear only the echoes, not the original note (this is useful for effects sends in a mixer). This is exactly what the PT2399 chip does in the analog delay from Chapter 10 -- it stores samples in a tiny memory and reads them back later. The principle is identical.

> **What happens if...** you set `DELAY_TIME = 0.005` (5 milliseconds)? Instead of hearing distinct echoes, you hear a metallic, hollow sound. This is **comb filtering** -- the delayed signal interferes with the original, reinforcing some frequencies and canceling others. The pattern of reinforcement/cancellation creates a "comb" shape in the frequency spectrum (evenly spaced peaks and notches). Set the delay to 0.001 (1ms) and it starts to sound like a short resonant tube. This is the basis of **flanging** -- a short, slowly varying delay time that creates a sweeping comb filter. You've accidentally stumbled into the chorus/flanger territory we'll explore properly in Section 5.

---

## 4. Write a Reverb (Schroeder Reverb)

### 4.1 What Reverb Is

When you clap in a cathedral, the sound bounces off every surface -- walls, floor, ceiling, pillars, pews -- and arrives at your ears as thousands of reflections, each with a slightly different delay and amplitude. Your brain interprets this dense wash of reflections as "reverb," and uses it to unconsciously estimate the size and character of the space.

A digital reverb simulates this by generating many delayed copies of the signal at different times, blending them together. The question is: how do you generate thousands of delays efficiently?

### 4.2 The Schroeder Architecture

In 1962, Manfred Schroeder at Bell Labs proposed a reverb algorithm that remains the foundation of most algorithmic reverbs today. The architecture uses two building blocks you already know:

1. **Comb filters** (parallel): These are delay lines with feedback -- exactly what you built in Section 3. Multiple comb filters with different delay times create a dense field of echoes.

2. **Allpass filters** (series): These pass all frequencies at equal amplitude but diffuse the timing of the echoes, breaking up the regularity and making the reverb sound more natural.

The signal flow:

```
Input → [Comb 1] ─┐
      → [Comb 2] ─┤
      → [Comb 3] ─┼─→ Sum → [Allpass 1] → [Allpass 2] → Output
      → [Comb 4] ─┘
```

Four comb filters run in *parallel* (each processes the input independently, and their outputs are summed). Then two allpass filters run in *series* (the output of the first feeds the input of the second). The comb filters create the echo density; the allpass filters smooth out the metallic quality.

### 4.3 The Comb Filter

A comb filter is literally the delay effect from Section 3. Input goes in, the output is the input plus a delayed, attenuated version of the output:

```
y[n] = x[n] + g * y[n - M]
```

Where `M` is the delay in samples and `g` is the feedback gain (controls the decay time). You already built this.

### 4.4 The Allpass Filter

An allpass filter is more subtle. It passes all frequencies at equal amplitude (unlike a low-pass or high-pass), but it shifts their phase. In the reverb context, this "smears" the timing of reflections without coloring the frequency balance:

```
y[n] = -g * x[n] + x[n - M] + g * y[n - M]
```

The feedforward term (`-g * x[n]`) is what makes it allpass rather than just a comb filter. It cancels the amplitude coloration while preserving the phase diffusion.

### 4.5 Choosing Delay Times

The delay times for the comb filters should be **mutually prime** (no common factors). If two comb filters have delay times that are multiples of each other, their echo patterns align and create a metallic, "ringy" quality. Mutually prime delays ensure maximum echo density and a more natural sound.

Schroeder's original delay times (in milliseconds) at 44.1 kHz:

| Comb Filter | Delay (ms) | Delay (samples at 44.1kHz) |
|---|---|---|
| Comb 1 | 29.7 | 1310 |
| Comb 2 | 37.1 | 1636 |
| Comb 3 | 41.1 | 1813 |
| Comb 4 | 43.7 | 1927 |

| Allpass Filter | Delay (ms) | Delay (samples at 44.1kHz) |
|---|---|---|
| Allpass 1 | 5.0 | 221 |
| Allpass 2 | 1.7 | 75 |

### 4.6 Complete Schroeder Reverb

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
REVERB_MIX = 0.35       # Dry/wet (0.0 = dry, 1.0 = all reverb)
DECAY = 0.8             # Reverb decay (0.0 = dead room, 0.95 = cathedral)
# --------------------

# Comb filter delay times in samples (mutually prime lengths)
COMB_DELAYS = [1310, 1636, 1813, 1927]
COMB_GAINS = [DECAY] * 4

# Allpass filter delay times in samples
ALLPASS_DELAYS = [221, 75]
ALLPASS_GAIN = 0.7

# Allocate buffers
comb_buffers = [np.zeros(d + BUFFER_SIZE) for d in COMB_DELAYS]
comb_positions = [0] * 4

allpass_buffers = [np.zeros(d + BUFFER_SIZE) for d in ALLPASS_DELAYS]
allpass_positions = [0] * 2


def comb_filter(input_sample, buf, delay, gain, pos_index):
    """Single comb filter: y[n] = x[n] + g * y[n - M]"""
    pos = comb_positions[pos_index]
    read_pos = (pos - delay) % len(buf)
    delayed = buf[read_pos]
    output = input_sample + gain * delayed
    buf[pos % len(buf)] = output
    comb_positions[pos_index] = (pos + 1) % len(buf)
    return output


def allpass_filter(input_sample, buf, delay, gain, pos_index):
    """Single allpass filter: y[n] = -g*x[n] + x[n-M] + g*y[n-M]"""
    pos = allpass_positions[pos_index]
    read_pos = (pos - delay) % len(buf)
    delayed = buf[read_pos]
    output = -gain * input_sample + delayed + gain * delayed
    # A more precise implementation stores the output:
    buf[pos % len(buf)] = input_sample + gain * delayed
    allpass_positions[pos_index] = (pos + 1) % len(buf)
    return output


def callback(indata, outdata, frames, time, status):
    if status:
        print(status)

    for i in range(frames):
        dry = indata[i, 0]

        # Four parallel comb filters, sum their outputs
        wet = 0.0
        for c in range(4):
            wet += comb_filter(dry, comb_buffers[c],
                               COMB_DELAYS[c], COMB_GAINS[c], c)
        wet *= 0.25  # Average the four comb outputs

        # Two series allpass filters
        for a in range(2):
            wet = allpass_filter(wet, allpass_buffers[a],
                                ALLPASS_DELAYS[a], ALLPASS_GAIN, a)

        # Mix dry and wet
        outdata[i, 0] = dry * (1.0 - REVERB_MIX) + wet * REVERB_MIX

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Schroeder Reverb running: decay={DECAY}, mix={REVERB_MIX}")
    print("Press Enter to stop.")
    input()
```

Play your guitar through this. You built a reverb. From scratch. With math.

Set `DECAY = 0.85` and play a single note -- hear it ring out, the reflections decaying naturally over several seconds. Set `DECAY = 0.5` and it sounds like a small room. Set `DECAY = 0.95` and you're in a cathedral with stone walls. (Don't set it above 0.98 or the reverb will never decay and eventually clip.)

This reverb is crude compared to commercial algorithms (Valhalla, Lexicon, Strymon), which add modulated delay times, pre-delay, early reflections, high-frequency damping, and more sophisticated diffusion networks. But the *principle* is identical. Those algorithms are refinements of Schroeder's 1962 architecture.

> **What happens if...** you change the comb filter delays to be exact multiples of each other (e.g., 1000, 2000, 3000, 4000)? The reverb develops a strong metallic, "ringy" quality -- you can hear a distinct pitch in the reverb tail. This is because the echo patterns align and reinforce at specific frequencies. Mutually prime delay times prevent this alignment, creating a denser, more natural-sounding decay. Try it both ways -- the difference is dramatic.

---

## 5. Write a Tremolo and Chorus

### 5.1 Tremolo: LFO Modulates Amplitude

In Chapter 10, you built a hardware tremolo by using an LFO (op-amp oscillator) to modulate the gain of a signal path through a transistor or optocoupler. The LFO output was a slow sine or triangle wave that pushed the gain up and down, creating the pulsing effect.

In code, tremolo is multiplication:

```python
output = signal * (1.0 - depth + depth * lfo)
```

Where `lfo` is a sine wave oscillating between 0 and 1, and `depth` controls how much the volume varies. At `depth = 0`, no modulation (clean signal). At `depth = 1`, the signal goes from silence to full volume on each LFO cycle.

### 5.2 Complete Tremolo Effect

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
RATE = 4.0       # LFO speed in Hz (0.5 = slow pulse, 8 = helicopter)
DEPTH = 0.7      # Modulation depth (0.0 = no effect, 1.0 = full mute to full volume)
WAVEFORM = "sine" # "sine", "triangle", or "square"
# --------------------

phase = 0.0
phase_increment = RATE / SAMPLE_RATE

def callback(indata, outdata, frames, time, status):
    global phase

    if status:
        print(status)

    for i in range(frames):
        # Generate LFO sample
        if WAVEFORM == "sine":
            lfo = 0.5 * (1.0 + np.sin(2.0 * np.pi * phase))
        elif WAVEFORM == "triangle":
            lfo = 1.0 - 2.0 * abs(2.0 * (phase % 1.0) - 1.0)
            lfo = 0.5 * (1.0 + lfo)
        elif WAVEFORM == "square":
            lfo = 1.0 if (phase % 1.0) < 0.5 else 0.0
        else:
            lfo = 1.0

        # Modulate amplitude
        gain = 1.0 - DEPTH + DEPTH * lfo
        outdata[i, 0] = indata[i, 0] * gain

        phase += phase_increment

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Tremolo running: rate={RATE}Hz, depth={DEPTH}, waveform={WAVEFORM}")
    print("Press Enter to stop.")
    input()
```

Play guitar through this. Set `RATE = 3.0` and `DEPTH = 0.5` for a classic surf-rock tremolo. Switch `WAVEFORM` to `"square"` for a choppy, stuttering effect. This is *exactly* what the analog circuit from Chapter 10 does -- the LFO waveform shape determines the modulation character, the rate pot sets the frequency, the depth pot sets how far the LFO swings the gain. Same concept, different substrate.

### 5.3 Chorus: LFO Modulates Delay Time

Chorus takes the delay effect from Section 3 and modulates the delay time with an LFO. Instead of a fixed echo, the delay time wobbles slowly back and forth. This creates slight pitch variations (because changing the delay time is equivalent to resampling the signal at a varying rate), which makes a single guitar sound like two slightly detuned guitars playing together -- the "chorus" effect.

The delay times are very short (5-30 ms, typically centered around 15 ms) and the modulation depth is small (a few milliseconds of variation). The ear doesn't hear the delay as a distinct echo; it hears the pitch/timing smearing as "thickness" or "shimmer."

### 5.4 Complete Chorus Effect

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
RATE = 1.5           # LFO speed in Hz
DEPTH_MS = 3.0       # Modulation depth in milliseconds
CENTER_DELAY_MS = 15  # Center delay time in milliseconds
MIX = 0.5            # Dry/wet mix
# --------------------

MAX_DELAY = 0.05  # 50ms max delay
buffer_length = int(MAX_DELAY * SAMPLE_RATE)
delay_buffer = np.zeros(buffer_length)
write_pos = 0
phase = 0.0
phase_increment = RATE / SAMPLE_RATE

center_delay_samples = CENTER_DELAY_MS / 1000.0 * SAMPLE_RATE
depth_samples = DEPTH_MS / 1000.0 * SAMPLE_RATE

def callback(indata, outdata, frames, time, status):
    global write_pos, phase

    if status:
        print(status)

    for i in range(frames):
        # Write input to delay buffer
        delay_buffer[write_pos] = indata[i, 0]

        # Calculate modulated delay time
        lfo = np.sin(2.0 * np.pi * phase)
        current_delay = center_delay_samples + depth_samples * lfo

        # Read from delay buffer with linear interpolation
        read_pos = write_pos - current_delay
        read_index = int(read_pos) % buffer_length
        frac = read_pos - int(read_pos)
        next_index = (read_index + 1) % buffer_length

        # Linear interpolation for smooth pitch shifting
        delayed = delay_buffer[read_index] * (1.0 - frac) + \
                  delay_buffer[next_index] * frac

        # Mix
        outdata[i, 0] = indata[i, 0] * (1.0 - MIX) + delayed * MIX

        write_pos = (write_pos + 1) % buffer_length
        phase += phase_increment

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Chorus running: rate={RATE}Hz, depth={DEPTH_MS}ms, delay={CENTER_DELAY_MS}ms")
    print("Press Enter to stop.")
    input()
```

Note the linear interpolation when reading from the delay buffer. Because the modulated delay time is a floating-point number (not an integer), we need to interpolate between adjacent samples. Without interpolation, you'd hear a zipper-like artifact as the delay jumps between integer sample positions. This is the same problem that arises in hardware delay circuits -- the BBD (bucket brigade device) in Chapter 10's chorus section uses analog charge packets that are inherently smooth, giving it that "lush" character.

> **What happens if...** you increase `RATE` to 8 Hz? The slow, gentle pitch wobble turns into a fast vibrato -- your guitar sounds like it's being played through a Leslie speaker. Push it to 20 Hz and you enter **ring modulation** territory -- the pitch variations become so fast they create new frequencies (sum and difference tones), and the sound becomes metallic and robotic. The boundary between "chorus," "vibrato," "ring modulation," and "weird alien sound" is just the LFO rate. One parameter, a continuous spectrum of effects.

---

## 6. Write FIR and IIR Filters

### 6.1 FIR Filter: Convolution with a Kernel

Chapter 27 explained that an FIR filter computes each output sample as a weighted sum of input samples -- this is convolution with a set of coefficients (the "kernel" or "taps"). The `scipy.signal` module can generate these coefficients for us, and `numpy` can apply them.

### 6.2 Low-Pass FIR Filter

```python
import numpy as np
from scipy.signal import firwin, lfilter

# Design a low-pass FIR filter
SAMPLE_RATE = 44100
CUTOFF_HZ = 2000       # Cut everything above 2 kHz
NUM_TAPS = 101          # Filter order (more taps = steeper rolloff)

# Generate the filter coefficients
fir_coeffs = firwin(NUM_TAPS, CUTOFF_HZ, fs=SAMPLE_RATE)

# Apply to a signal (offline, for now)
# filtered_signal = lfilter(fir_coeffs, 1.0, input_signal)
```

The `firwin` function generates a set of coefficients that, when convolved with a signal, attenuate all frequencies above `CUTOFF_HZ`. The `NUM_TAPS` parameter controls the steepness of the rolloff -- more taps means a sharper transition between the passband and the stopband, at the cost of more computation and latency.

### 6.3 Real-Time Low-Pass Filter

```python
import sounddevice as sd
import numpy as np
from scipy.signal import firwin

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
CUTOFF_HZ = 1500     # Cutoff frequency (200 = very muffled, 5000 = subtle)
NUM_TAPS = 101       # Filter length
# --------------------

fir_coeffs = firwin(NUM_TAPS, CUTOFF_HZ, fs=SAMPLE_RATE)
# Overlap buffer for continuity between callbacks
overlap_buffer = np.zeros(NUM_TAPS - 1)

def callback(indata, outdata, frames, time, status):
    global overlap_buffer

    if status:
        print(status)

    signal = indata[:, 0]

    # Prepend overlap from last buffer for continuity
    extended = np.concatenate([overlap_buffer, signal])
    filtered = np.convolve(extended, fir_coeffs, mode='valid')

    # Store overlap for next callback
    overlap_buffer = extended[-(NUM_TAPS - 1):]

    # Write output (filtered may be slightly shorter; pad if needed)
    out_len = min(len(filtered), frames)
    outdata[:out_len, 0] = filtered[:out_len]
    if out_len < frames:
        outdata[out_len:, 0] = 0.0

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Low-pass FIR filter: cutoff={CUTOFF_HZ}Hz, taps={NUM_TAPS}")
    print("Press Enter to stop.")
    input()
```

Play guitar through this with `CUTOFF_HZ = 1500`. It sounds muffled -- like turning down the tone knob on your guitar. That tone knob is a passive RC low-pass filter (Chapter 1); you've just built its digital equivalent. Set `CUTOFF_HZ = 500` and it sounds like your guitar is in another room. Set it to `8000` and the effect is subtle -- just a slight dulling of the high end.

### 6.4 IIR Filter: The Biquad

IIR filters use feedback -- the output samples feed back into the calculation. This makes them far more efficient than FIR filters for sharp resonant peaks and steep rolloffs, but they can't achieve linear phase. In Chapter 27, you learned that the biquad is the universal building block of IIR audio filters. Let's use one.

### 6.5 Sweepable Band-Pass (Instant Wah-Wah)

A wah pedal is fundamentally a band-pass filter with a resonant peak that you sweep up and down in frequency by rocking the treadle. In code, we can sweep the frequency automatically for an auto-wah, or in response to the signal's envelope for a dynamic wah.

```python
import sounddevice as sd
import numpy as np
from scipy.signal import iirfilter, sosfilt

SAMPLE_RATE = 44100
BUFFER_SIZE = 512

# --- Tweak these ---
WAH_MIN_HZ = 400      # Bottom of the sweep range
WAH_MAX_HZ = 3000     # Top of the sweep range
WAH_RATE = 1.0         # Sweep speed in Hz (auto-wah LFO rate)
WAH_Q = 5.0            # Resonance (higher = more pronounced "wah")
# --------------------

phase = 0.0
phase_increment = WAH_RATE / SAMPLE_RATE

# We'll store filter state across callbacks
sos_state = None

def make_bandpass(center_freq, q, fs):
    """Create a second-order band-pass filter (biquad) as SOS."""
    low = center_freq / (q ** 0.5)
    high = center_freq * (q ** 0.5)
    # Clamp to valid range
    low = max(low, 20)
    high = min(high, fs / 2 - 1)
    if low >= high:
        low = high * 0.5
    sos = iirfilter(1, [low, high], btype='band', fs=fs, output='sos')
    return sos

def callback(indata, outdata, frames, time, status):
    global phase, sos_state

    if status:
        print(status)

    # Calculate current center frequency from LFO
    lfo = 0.5 * (1.0 + np.sin(2.0 * np.pi * phase * SAMPLE_RATE))
    center_freq = WAH_MIN_HZ + (WAH_MAX_HZ - WAH_MIN_HZ) * \
                  (0.5 * (1.0 + np.sin(2.0 * np.pi * phase * SAMPLE_RATE)))

    # Use the LFO at block rate (update once per buffer, not per sample)
    t = phase * SAMPLE_RATE
    center_freq = WAH_MIN_HZ + (WAH_MAX_HZ - WAH_MIN_HZ) * \
                  (0.5 * (1.0 + np.sin(2.0 * np.pi * WAH_RATE * t / SAMPLE_RATE)))

    sos = make_bandpass(center_freq, WAH_Q, SAMPLE_RATE)

    signal = indata[:, 0]
    if sos_state is None:
        sos_state = np.zeros((sos.shape[0], 2))

    filtered, sos_state = sosfilt(sos, signal, zi=sos_state)
    outdata[:, 0] = filtered * 3.0  # Boost output (band-pass reduces level)

    phase += frames / SAMPLE_RATE

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Auto-wah running: range={WAH_MIN_HZ}-{WAH_MAX_HZ}Hz, Q={WAH_Q}")
    print("Press Enter to stop.")
    input()
```

Play guitar through this. You'll hear the classic "wah-wah" sweep as the band-pass filter moves up and down in frequency. Crank `WAH_Q` higher for a more aggressive, resonant peak. This is exactly what an analog wah pedal does -- the inductor/capacitor network in a Cry Baby creates a resonant band-pass, and the treadle moves the resonant frequency. Same filter, different implementation.

Connect this to Chapter 13 (Crossover Design): a speaker crossover is fundamentally the same thing -- a filter that passes certain frequencies and blocks others. The crossover is a low-pass and high-pass filter pair. The wah is a band-pass. The math is the same.

---

## 7. Build a Guitar Tuner

### 7.1 The Problem: Finding the Fundamental Frequency

A guitar tuner needs to determine the pitch of the note you're playing. This means finding the **fundamental frequency** -- the lowest and loudest frequency component of the string's vibration. The standard tuning for a guitar is:

| String | Note | Frequency |
|---|---|---|
| 6 (low E) | E2 | 82.41 Hz |
| 5 | A2 | 110.00 Hz |
| 4 | D3 | 146.83 Hz |
| 3 | G3 | 196.00 Hz |
| 2 | B3 | 246.94 Hz |
| 1 (high E) | E4 | 329.63 Hz |

### 7.2 Method 1: FFT Peak Detection (and Why It's Not Great)

Your first instinct from Chapter 27a is to use the FFT: transform the signal to the frequency domain, find the bin with the highest magnitude, and read off the frequency. Let's see why this is harder than it sounds.

At 44.1 kHz with a buffer of 4096 samples, the FFT frequency resolution is:

```
resolution = sample_rate / buffer_size = 44100 / 4096 ≈ 10.77 Hz
```

That means each FFT bin is about 10.8 Hz wide. For tuning, you need accuracy better than about 1 Hz (a cent of pitch at A440 is about 0.26 Hz). The FFT is 40 times too coarse. You could increase the buffer size (32768 samples gives ~1.35 Hz resolution), but that means 0.74 seconds of audio per analysis -- too slow for responsive tuning.

You *can* improve FFT resolution with zero-padding and parabolic interpolation around the peak. This works, but there's a simpler and more robust method.

### 7.3 Method 2: Autocorrelation

Autocorrelation finds the **period** of a signal by correlating the signal with delayed versions of itself. If the signal has a period of T samples, then the signal and a version delayed by T samples will be very similar -- the autocorrelation will have a peak at lag T.

The fundamental frequency is then:

```
frequency = sample_rate / T
```

Why is this better than FFT for tuning?

- The resolution is proportional to the sample rate, not the buffer size. At 44.1 kHz, you can detect frequency differences of about 1 sample period = 44100/(T^2) Hz at frequency f, which is much finer than FFT bins for the typical guitar range.
- It naturally finds the *fundamental* frequency, not the strongest harmonic. (A guitar's second harmonic is often louder than the fundamental -- the FFT would pick the wrong peak. Autocorrelation finds the period, which corresponds to the fundamental.)
- It works well with relatively short buffers (2048-4096 samples).

### 7.4 Complete Guitar Tuner

```python
import sounddevice as sd
import numpy as np

SAMPLE_RATE = 44100
BUFFER_SIZE = 4096  # Larger buffer for pitch detection accuracy

# Note names and frequencies for display
NOTE_NAMES = ['C', 'C#', 'D', 'D#', 'E', 'F',
              'F#', 'G', 'G#', 'A', 'A#', 'B']

def freq_to_note(freq):
    """Convert frequency to note name, octave, and cents offset."""
    if freq <= 0:
        return None, None, None
    # MIDI note number (A4 = 440 Hz = MIDI 69)
    midi = 12 * np.log2(freq / 440.0) + 69
    midi_rounded = round(midi)
    cents = (midi - midi_rounded) * 100

    note_name = NOTE_NAMES[midi_rounded % 12]
    octave = (midi_rounded // 12) - 1
    return note_name, octave, cents

def autocorrelate(signal, fs, fmin=60, fmax=1000):
    """Find fundamental frequency using autocorrelation."""
    # Only search within plausible guitar range
    min_lag = int(fs / fmax)
    max_lag = int(fs / fmin)

    # Normalize
    signal = signal - np.mean(signal)
    if np.max(np.abs(signal)) < 0.01:
        return 0  # Signal too quiet

    # Autocorrelation via numpy
    correlation = np.correlate(signal, signal, mode='full')
    correlation = correlation[len(correlation) // 2:]  # Take positive lags only

    # Normalize
    correlation = correlation / correlation[0]

    # Find the first peak after the initial decline
    # (Skip lag 0, which is always 1.0)
    search_region = correlation[min_lag:max_lag]
    if len(search_region) == 0:
        return 0

    peak_index = np.argmax(search_region) + min_lag

    # Parabolic interpolation for sub-sample accuracy
    if 0 < peak_index < len(correlation) - 1:
        alpha = correlation[peak_index - 1]
        beta = correlation[peak_index]
        gamma = correlation[peak_index + 1]
        if alpha + gamma - 2 * beta != 0:
            peak_index += 0.5 * (alpha - gamma) / (alpha + gamma - 2 * beta)

    if peak_index == 0:
        return 0

    return fs / peak_index

def callback(indata, outdata, frames, time, status):
    if status:
        print(status, flush=True)

    signal = indata[:, 0]
    outdata[:, 0] = 0.0  # Silence output (tuner doesn't need sound)

    freq = autocorrelate(signal, SAMPLE_RATE)

    if freq > 0:
        note, octave, cents = freq_to_note(freq)
        if note is not None:
            # Build a visual tuning meter
            meter_width = 40
            center = meter_width // 2
            offset = int(cents / 50.0 * center)  # Scale cents to meter width
            offset = max(-center, min(center, offset))

            meter = ['-'] * meter_width
            meter[center] = '|'  # Center mark
            pos = center + offset
            pos = max(0, min(meter_width - 1, pos))
            meter[pos] = '*'

            sharp_flat = ""
            if cents > 2:
                sharp_flat = f" (+{cents:.0f} cents, sharp)"
            elif cents < -2:
                sharp_flat = f" ({cents:.0f} cents, flat)"
            else:
                sharp_flat = " (in tune!)"

            print(f"\r  {note}{octave}  {freq:7.1f} Hz  "
                  f"[{''.join(meter)}]{sharp_flat}        ",
                  end='', flush=True)

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print("Guitar Tuner running. Play a single note.")
    print("Press Enter to stop.\n")
    input()
    print()  # Clean newline on exit
```

This is a fully functional guitar tuner in about 70 lines. Play a single open string -- you'll see the note name, frequency, and how many cents sharp or flat you are. The visual meter shows you which direction to turn the tuning peg.

The autocorrelation approach finds the period of the waveform, which directly gives you the fundamental frequency. Parabolic interpolation around the autocorrelation peak provides sub-sample accuracy, getting you well within the 1-cent precision needed for tuning.

> **What happens if...** you strum a full chord instead of a single note? The tuner flickers between readings or shows a wrong note. This is because a chord is a sum of multiple frequencies with no single dominant period -- the autocorrelation sees multiple competing periodicities and picks one semi-randomly. This is why guitar tuners (hardware and software) always say "play one string at a time." It's not a limitation of this implementation; it's a fundamental property of pitch detection on polyphonic signals. (Polyphonic pitch detection is possible but requires significantly more sophisticated algorithms -- harmonic product spectrum, CREPE neural networks, etc.)

---

## 8. Convolution Reverb from Scratch

### 8.1 The Concept

The Schroeder reverb from Section 4 *simulates* a room using an algorithm. Convolution reverb does something more direct: it captures the actual acoustic character of a real space and applies it to your signal.

Here's how it works:

1. Go to an interesting space (a cathedral, a parking garage, a tiled bathroom, a stairwell).
2. Make a very short, loud sound (a clap, a starter pistol, a popped balloon). This is approximately an "impulse" -- a signal with energy at all frequencies.
3. Record what you hear. The recording captures the room's response to the impulse -- the pattern of reflections, their timing, their frequency content, their decay. This recording is the **impulse response (IR)**.
4. Convolve your guitar signal with this impulse response. Convolution "stamps" the room's acoustic character onto your signal. Your guitar now sounds like it's being played in that cathedral.

This is mathematically the same operation as FIR filtering (Section 6). An impulse response *is* a set of FIR filter coefficients. A very, very large set -- a 2-second IR at 44.1 kHz is 88,200 taps. You wouldn't implement this with direct convolution (that would require 88,200 multiply-adds per *sample*). Instead, you use FFT-based convolution, which is dramatically faster for long filters.

### 8.2 Where to Find Impulse Responses

You don't need to travel to a cathedral with a starter pistol (though that's a fun weekend project). Free impulse response libraries are available online:

- **OpenAIR** (openairlib.net): Academic collection of real spaces -- concert halls, churches, tunnels, outdoor environments. High-quality recordings with metadata.
- **EchoThief** (echothief.com): Guerrilla impulse responses recorded in public spaces around the world -- subways, warehouses, stadiums, caves. Raw and interesting.
- **Voxengo** (voxengo.com/impulses): Free collection of studio and hall impulse responses.
- **Fokke van Saane**: Various impulse responses of classic reverb hardware (Lexicon, EMT plate reverbs).

Download a WAV file. Any IR will do -- start with a large hall or cathedral for maximum drama.

### 8.3 Complete Convolution Reverb

```python
import sounddevice as sd
import numpy as np
from scipy.io import wavfile
from scipy.signal import fftconvolve

SAMPLE_RATE = 44100
BUFFER_SIZE = 1024  # Slightly larger buffer for convolution processing

# --- Set this to your impulse response file ---
IR_FILE = "cathedral.wav"  # Path to your IR WAV file
MIX = 0.4                 # Dry/wet mix
# --------------------

# Load impulse response
ir_rate, ir_data = wavfile.read(IR_FILE)

# Convert to float and mono if needed
if ir_data.dtype == np.int16:
    ir_data = ir_data.astype(np.float32) / 32768.0
elif ir_data.dtype == np.int32:
    ir_data = ir_data.astype(np.float32) / 2147483648.0

if ir_data.ndim > 1:
    ir_data = ir_data[:, 0]  # Take left channel only

# Resample if IR sample rate doesn't match
if ir_rate != SAMPLE_RATE:
    from scipy.signal import resample
    new_length = int(len(ir_data) * SAMPLE_RATE / ir_rate)
    ir_data = resample(ir_data, new_length)

# Normalize IR
ir_data = ir_data / np.max(np.abs(ir_data))

print(f"Loaded IR: {len(ir_data)} samples ({len(ir_data)/SAMPLE_RATE:.2f}s)")

# For real-time convolution, we use overlap-save method
# Accumulate input and process in chunks
IR_LEN = len(ir_data)
FFT_SIZE = 1  # Will be set to next power of 2
while FFT_SIZE < BUFFER_SIZE + IR_LEN - 1:
    FFT_SIZE *= 2

# Pre-compute IR FFT (this is the expensive part -- done once)
IR_FFT = np.fft.rfft(ir_data, FFT_SIZE)

# Overlap buffer for continuous output
overlap = np.zeros(FFT_SIZE)
input_buffer = np.zeros(FFT_SIZE)

def callback(indata, outdata, frames, time, status):
    global overlap

    if status:
        print(status)

    signal = indata[:, 0]

    # Zero-pad input to FFT size
    padded_input = np.zeros(FFT_SIZE)
    padded_input[:frames] = signal

    # Convolution in frequency domain: multiply FFTs
    signal_fft = np.fft.rfft(padded_input)
    convolved_fft = signal_fft * IR_FFT
    convolved = np.fft.irfft(convolved_fft, FFT_SIZE)

    # Add overlap from previous buffer
    convolved[:len(overlap)] += overlap

    # Output the first 'frames' samples
    wet = convolved[:frames]

    # Save the rest as overlap for next callback
    overlap = np.zeros(FFT_SIZE)
    overlap[:FFT_SIZE - frames] = convolved[frames:FFT_SIZE]

    # Mix dry and wet
    outdata[:, 0] = signal * (1.0 - MIX) + wet * MIX * 0.3  # Scale wet down

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    print(f"Convolution reverb running: IR={IR_FILE}, mix={MIX}")
    print("Press Enter to stop.")
    input()
```

Play your guitar through a cathedral impulse response. Play through a parking garage. Play through a tiled bathroom. Each IR stamps its acoustic fingerprint onto your signal. This is not a *simulation* of reverb -- it's the *actual acoustic behavior* of that real space, captured and applied mathematically.

This is **exactly** what CamillaDSP does for room correction in Chapter 29. The room correction workflow is:

1. Measure the room's impulse response
2. Compute the *inverse* impulse response (the mirror image that cancels the room's coloration)
3. Convolve all audio through that inverse IR

Same operation, different purpose. Convolution reverb *adds* a room's character. Room correction *removes* it. The math is identical.

> **What happens if...** you load a very short IR (say, 0.01 seconds -- just 441 samples)? Instead of reverb, you hear a timbral change -- the IR acts like a short FIR filter, coloring the frequency balance of your guitar. This is the boundary between "reverb" and "EQ/filtering." A very long IR (several seconds) is reverb. A very short IR is a filter. There's no sharp dividing line -- it's a continuum, and convolution handles the entire range.

---

## 9. Simple Synthesizer

### 9.1 Oscillators: Sound from Math

Every sound a synthesizer makes starts with an oscillator -- a function that generates a repeating waveform. Unlike the previous sections where we processed an incoming guitar signal, here we're *generating* the audio from scratch. No input needed.

### 9.2 The Four Classic Waveforms

```python
import numpy as np

def sine_wave(freq, t, sample_rate):
    """Pure tone -- single frequency, no harmonics."""
    return np.sin(2 * np.pi * freq * t / sample_rate)

def sawtooth_wave(freq, t, sample_rate):
    """All harmonics (1st, 2nd, 3rd, ...) with decreasing amplitude.
    Bright, buzzy. Think: analog synth leads."""
    phase = (freq * t / sample_rate) % 1.0
    return 2.0 * phase - 1.0

def square_wave(freq, t, sample_rate):
    """Odd harmonics only (1st, 3rd, 5th, ...).
    Hollow, clarinet-like. Think: NES music."""
    phase = (freq * t / sample_rate) % 1.0
    return np.where(phase < 0.5, 1.0, -1.0)

def triangle_wave(freq, t, sample_rate):
    """Odd harmonics with rapid amplitude decrease.
    Soft, mellow. Think: flute-like tones."""
    phase = (freq * t / sample_rate) % 1.0
    return 4.0 * np.abs(phase - 0.5) - 1.0
```

Each waveform has a distinct harmonic structure, which determines its timbre:

| Waveform | Harmonics | Character | Analog Circuit |
|---|---|---|---|
| Sine | Fundamental only | Pure, clean | Oscillator with heavy filtering |
| Sawtooth | All harmonics (1/n amplitude) | Bright, rich, buzzy | Ramp generator |
| Square | Odd harmonics only (1/n amplitude) | Hollow, woody | Schmitt trigger / comparator |
| Triangle | Odd harmonics (1/n² amplitude) | Soft, mellow | Integrator from square wave |

The LFO circuit from Chapter 10 generates square and triangle waves -- the same shapes you're now using as audio oscillators, just at much higher frequencies (tens of Hz for an LFO, hundreds of Hz for an audio oscillator).

### 9.3 Additive Synthesis

You can build complex timbres by *adding* harmonics together. This is **additive synthesis** -- the inverse of the Fourier transform. In Chapter 27a, you learned that any periodic signal can be decomposed into sine waves. Here, you go the other direction: combine sine waves to build a target timbre.

```python
def organ_tone(freq, t, sample_rate):
    """Simulate a drawbar organ by mixing harmonic sine waves."""
    # Drawbar levels (0-8 scale, like a real Hammond organ)
    drawbars = [8, 6, 8, 7, 4, 0, 2, 0, 3]  # 888740203
    harmonic_ratios = [0.5, 1, 2, 3, 4, 5, 6, 8, 10]  # Footage mapping

    signal = np.zeros_like(t, dtype=np.float64)
    for level, ratio in zip(drawbars, harmonic_ratios):
        if level > 0:
            amplitude = level / 8.0
            signal += amplitude * np.sin(2 * np.pi * freq * ratio * t / sample_rate)

    return signal / np.max(np.abs(signal) + 1e-10)  # Normalize
```

### 9.4 FM Synthesis

FM (Frequency Modulation) synthesis, invented by John Chowning at Stanford in 1967 and commercialized by Yamaha in the legendary DX7, generates complex, evolving timbres by using one oscillator to modulate the *frequency* of another.

- **Carrier**: the oscillator you hear
- **Modulator**: the oscillator that modulates the carrier's frequency

```python
def fm_synth(carrier_freq, mod_freq, mod_index, t, sample_rate):
    """FM synthesis: carrier frequency modulated by modulator.

    mod_index controls the 'brightness' / harmonic complexity.
    Higher mod_index = more harmonics = brighter, more metallic sound.
    """
    modulator = mod_index * np.sin(2 * np.pi * mod_freq * t / sample_rate)
    carrier = np.sin(2 * np.pi * carrier_freq * t / sample_rate + modulator)
    return carrier
```

The `mod_index` parameter is the magic knob. At `mod_index = 0`, you hear a pure sine wave (no modulation). At `mod_index = 1`, a few harmonics appear. At `mod_index = 5`, a rich, bright, bell-like tone. At `mod_index = 20`, a harsh, metallic clang. By varying the modulation index over time (using an envelope), you can create sounds that start bright and fade to warmth -- like a struck bell, or a plucked string.

### 9.5 ADSR Envelope

A raw oscillator plays forever at constant volume. Real instruments don't work that way -- a guitar string has a sharp attack, a brief peak, a gradual decay, and eventually silence. The **ADSR envelope** (Attack, Decay, Sustain, Release) shapes the volume over time:

```
Level
  ^
  |     /\
  |    /  \________
  |   /    \       \
  |  /      \       \
  | /   D    \ S     \
  |/ A        \       \ R
  +----+--+----+------+--→ Time
       A  D    S      R
```

- **Attack**: time from silence to peak (short = percussive, long = swelling pad)
- **Decay**: time from peak to sustain level
- **Sustain**: the level held while the key is pressed (0.0-1.0)
- **Release**: time from key release to silence

```python
def generate_adsr(total_samples, attack_ms, decay_ms, sustain_level,
                  release_ms, sample_rate, gate_off_sample=None):
    """Generate an ADSR envelope.

    gate_off_sample: when the key is released (if None, key is held for entire duration)
    """
    attack_samples = int(attack_ms / 1000 * sample_rate)
    decay_samples = int(decay_ms / 1000 * sample_rate)
    release_samples = int(release_ms / 1000 * sample_rate)

    if gate_off_sample is None:
        gate_off_sample = total_samples - release_samples

    envelope = np.zeros(total_samples)

    for i in range(total_samples):
        if i < attack_samples:
            # Attack: ramp from 0 to 1
            envelope[i] = i / attack_samples
        elif i < attack_samples + decay_samples:
            # Decay: ramp from 1 to sustain_level
            decay_progress = (i - attack_samples) / decay_samples
            envelope[i] = 1.0 - (1.0 - sustain_level) * decay_progress
        elif i < gate_off_sample:
            # Sustain: hold at sustain_level
            envelope[i] = sustain_level
        else:
            # Release: ramp from sustain_level to 0
            release_progress = (i - gate_off_sample) / release_samples
            release_progress = min(release_progress, 1.0)
            envelope[i] = sustain_level * (1.0 - release_progress)

    return envelope
```

### 9.6 Putting It All Together: A Playable FM Synth

```python
import sounddevice as sd
import numpy as np
import threading

SAMPLE_RATE = 44100
BUFFER_SIZE = 256  # Low latency for synth responsiveness

# --- Tweak these ---
CARRIER_FREQ = 440.0    # A4
MOD_RATIO = 2.0         # Modulator freq = carrier * ratio
MOD_INDEX = 3.0         # Modulation depth (0 = pure sine, 5+ = metallic)
ATTACK_MS = 10
DECAY_MS = 200
SUSTAIN_LEVEL = 0.6
RELEASE_MS = 300
# --------------------

# Shared state
note_on = False
note_phase = 0.0
envelope_value = 0.0
envelope_stage = 'off'  # 'attack', 'decay', 'sustain', 'release', 'off'

# Pre-compute rates
attack_rate = 1.0 / (ATTACK_MS / 1000 * SAMPLE_RATE) if ATTACK_MS > 0 else 1.0
decay_rate = (1.0 - SUSTAIN_LEVEL) / (DECAY_MS / 1000 * SAMPLE_RATE) if DECAY_MS > 0 else 1.0
release_rate = SUSTAIN_LEVEL / (RELEASE_MS / 1000 * SAMPLE_RATE) if RELEASE_MS > 0 else 1.0

# Simple keyboard mapping (computer keyboard -> note frequencies)
KEY_MAP = {
    'a': 261.63,  # C4
    'w': 277.18,  # C#4
    's': 293.66,  # D4
    'e': 311.13,  # D#4
    'd': 329.63,  # E4
    'f': 349.23,  # F4
    't': 369.99,  # F#4
    'g': 392.00,  # G4
    'y': 415.30,  # G#4
    'h': 440.00,  # A4
    'u': 466.16,  # A#4
    'j': 493.88,  # B4
    'k': 523.25,  # C5
}

current_freq = 440.0

def callback(indata, outdata, frames, time, status):
    global note_phase, envelope_value, envelope_stage

    if status:
        print(status)

    output = np.zeros(frames)

    for i in range(frames):
        # Update envelope
        if envelope_stage == 'attack':
            envelope_value += attack_rate
            if envelope_value >= 1.0:
                envelope_value = 1.0
                envelope_stage = 'decay'
        elif envelope_stage == 'decay':
            envelope_value -= decay_rate
            if envelope_value <= SUSTAIN_LEVEL:
                envelope_value = SUSTAIN_LEVEL
                envelope_stage = 'sustain'
        elif envelope_stage == 'sustain':
            envelope_value = SUSTAIN_LEVEL
        elif envelope_stage == 'release':
            envelope_value -= release_rate
            if envelope_value <= 0.0:
                envelope_value = 0.0
                envelope_stage = 'off'
        else:
            envelope_value = 0.0

        # FM synthesis
        mod_freq = current_freq * MOD_RATIO
        modulator = MOD_INDEX * np.sin(2 * np.pi * mod_freq * note_phase / SAMPLE_RATE)
        carrier = np.sin(2 * np.pi * current_freq * note_phase / SAMPLE_RATE + modulator)

        output[i] = carrier * envelope_value * 0.3  # Master volume
        note_phase += 1

    outdata[:, 0] = output

def keyboard_listener():
    """Listen for keyboard input (simple blocking approach)."""
    global note_on, envelope_stage, current_freq, note_phase

    print("\nKeyboard map (like a piano):")
    print("  W E   T Y U")
    print(" A S D F G H J K")
    print(" C D E F G A B C")
    print("\nPress a key to play. Press 'q' to quit.\n")

    import sys
    import tty
    import termios

    fd = sys.stdin.fileno()
    old_settings = termios.tcgetattr(fd)
    try:
        tty.setraw(fd)
        while True:
            ch = sys.stdin.read(1)
            if ch == 'q':
                break
            elif ch in KEY_MAP:
                current_freq = KEY_MAP[ch]
                note_phase = 0
                envelope_value = 0.0
                envelope_stage = 'attack'
                note_on = True
            elif ch == ' ':
                # Space = note off
                envelope_stage = 'release'
                note_on = False
    finally:
        termios.tcsetattr(fd, termios.TCSADRAIN, old_settings)

with sd.Stream(samplerate=SAMPLE_RATE,
               blocksize=BUFFER_SIZE,
               channels=1,
               callback=callback):
    keyboard_listener()
```

You've built a synthesizer. From scratch. It has FM synthesis with adjustable modulation, an ADSR envelope, and keyboard control. Press 'a' and hear a note. Press 'd' and hear a higher note. Press space to release. Change `MOD_INDEX` from 0 to 10 and hear the timbre transform from a pure sine to a bright, bell-like tone to a harsh metallic clang.

The signal chain is: oscillator (FM) -> envelope (ADSR) -> output. This is the same signal flow as every hardware synthesizer ever made -- Moog, Buchla, Eurorack modules -- just at a higher level of abstraction. The oscillator generates raw tone. The envelope shapes its dynamics. If you added a filter between them (take the IIR band-pass from Section 6), you'd have the classic subtractive synthesis architecture used by virtually every analog synth since the 1960s.

> **What happens if...** you set `MOD_RATIO` to a non-integer value like 1.414 (the square root of 2)? The resulting harmonics become *inharmonic* -- they don't fall on integer multiples of the fundamental. This creates bell-like or metallic tones, because real metal objects (bells, gongs, metal bars) have inharmonic overtones. Integer ratios sound "musical" and harmonic; non-integer ratios sound "metallic" and percussive. The DX7's ability to dial in precise FM ratios is what made it the defining sound of 1980s pop music.

---

## 10. Where to Go Deeper

You've now written every major category of audio DSP effect in Python: distortion, delay, reverb, modulation, filtering, pitch detection, convolution reverb, and synthesis. You understand the math because you implemented it. You understand the sound because you heard it. You understand the connection to analog circuits because you've built those too.

Python is the right tool for learning and prototyping, but it's not the right tool for shipping audio products. The per-sample Python loops in this chapter are orders of magnitude slower than native code, and the garbage collector can cause random latency spikes. For production audio DSP, you need faster languages and more specialized frameworks.

Here's where to go:

### 10.1 JUCE (C++)

**What**: The industry-standard framework for audio plugin development (VST, AU, AAX). Used by every major plugin company -- Native Instruments, FabFilter, Arturia, Valhalla DSP.

**Why**: If you want to build audio plugins that run in a DAW (Ableton, Logic, Pro Tools), JUCE is the path. It handles the plugin format wrapping, GUI rendering, parameter management, and cross-platform audio I/O. You focus on the DSP.

**Learning curve**: Moderate. You need to know C++, which is the real barrier. The JUCE API itself is well-designed and well-documented.

**Key insight**: The `processBlock()` method in a JUCE plugin is *exactly* the `callback` function you've been writing all chapter. Same concept, same model, just faster and with more infrastructure.

### 10.2 Faust

**What**: A functional programming language specifically for audio DSP. Compiles to C++, WebAssembly, LLVM, and more. Developed at GRAME in Lyon.

**Why**: The fastest path from DSP idea to working implementation. A distortion effect in Faust is about 5 lines. It compiles to highly optimized native code. The online IDE lets you prototype and test in the browser.

**Learning curve**: Easy syntax, different paradigm. Faust describes signal flow as a block diagram -- inputs flow through processing blocks to outputs. If you understand the signal flow diagrams in this chapter, you already think in Faust.

```
// Faust hard clipping distortion
gain = hslider("Gain", 10, 1, 50, 0.1);
threshold = hslider("Threshold", 0.3, 0.01, 1, 0.01);
process = *(gain) : min(threshold) : max(-threshold);
```

### 10.3 Rust Audio (cpal + dasp)

**What**: Audio programming in Rust using the `cpal` crate (cross-platform audio I/O, equivalent to PortAudio) and `dasp` (digital audio signal processing primitives).

**Why**: Memory safety without garbage collection. Rust guarantees no null pointer dereferences, no buffer overflows, no data races -- exactly the kinds of bugs that cause audio glitches and crashes in C++ code. The real-time audio community is increasingly moving to Rust.

**Learning curve**: Steep. Rust's borrow checker and ownership model take time to internalize. But once you do, you write code that is both safe and fast.

### 10.4 Daisy Seed

**What**: An embedded DSP platform -- a tiny circuit board with an ARM Cortex-M7 processor, a stereo audio codec, and GPIO pins. You write DSP code in C++, flash it to the board, and it runs your effect in real time with microsecond-level latency.

**Why**: This is the bridge between software DSP and hardware audio. You can build a physical guitar pedal that runs *your* C++ DSP code. The Daisy ecosystem includes a pedal form factor (Daisy Petal), a desktop synth form factor (Daisy Pod), and a Eurorack module form factor (Daisy Patch).

**The connection**: Everything you learned in this chapter translates directly. Your distortion callback becomes a Daisy `AudioCallback`. Your delay buffer becomes a Daisy `DelayLine`. Your FM synth becomes a Daisy `Oscillator` plus `Fm2`. The concepts are identical; the platform is different.

### 10.5 Pure Data / Max MSP

**What**: Visual programming environments for audio. You connect boxes with virtual patch cables to build signal flows. Pure Data (Pd) is free and open-source; Max MSP is commercial and more polished.

**Why**: Instant feedback. You literally see the signal flow as a diagram and can change it in real time. Excellent for experimentation and live performance. Many experimental electronic musicians perform with Pd or Max.

### 10.6 Web Audio API

**What**: Audio DSP in the browser using JavaScript. Every modern browser includes an audio processing engine accessible through JavaScript.

**Why**: Zero-install distribution. Build a web page with your audio effect, share the URL, anyone can use it. Great for educational tools, interactive demos, and browser-based instruments.

**Limitation**: JavaScript is garbage-collected (latency spikes), and the Web Audio API has a minimum buffer size that's browser-dependent. Not suitable for professional low-latency audio, but perfect for demos and toys.

---

## Summary

Here's what you built in this chapter:

| Section | Effect | Key Concept | Analog Equivalent |
|---|---|---|---|
| 1 | Passthrough | Audio callback model | Patch cable |
| 2 | Distortion | Clipping functions | Diode clipping (Ch 6) |
| 3 | Delay | Circular buffer, feedback | PT2399 chip (Ch 10) |
| 4 | Reverb | Schroeder architecture | Spring reverb tank |
| 5 | Tremolo | LFO × amplitude | LFO + optocoupler (Ch 10) |
| 5 | Chorus | LFO × delay time | LFO + BBD (Ch 10) |
| 6 | FIR filter | Convolution with kernel | RC low-pass (Ch 1) |
| 6 | IIR wah | Biquad band-pass sweep | Inductor/cap wah (Ch 5) |
| 7 | Guitar tuner | Autocorrelation pitch detection | Strobe tuner |
| 8 | Convolution reverb | FFT-based convolution | Acoustic space + mic |
| 9 | FM synthesizer | FM synthesis + ADSR | Yamaha DX7 |

Every effect in this table is fundamentally the same operation: take samples in, do math on them, put samples out. The math determines whether you hear distortion, delay, reverb, or a synthesized bell. The substrate -- Python, C++, analog components, hardware DSP -- is just the medium. The math is the music.

You started this guide by building a fuzz pedal with two transistors and a handful of resistors. Now you can build the same effect in 10 lines of code. The transistor clips the signal when it hits its saturation voltage. `np.clip()` clips the signal when it hits a threshold value. Same physics. Same math. Same sound. Different tools.

In Chapter 28, you'll build the Pi streamer that runs CamillaDSP -- which uses these same DSP algorithms (FIR convolution, IIR biquads, delay lines) to process audio in real time. In Chapter 29, you'll configure it as an active crossover and room correction engine. The foundation you built in this chapter is the foundation those builds stand on.

Now go plug your guitar in and break something.
