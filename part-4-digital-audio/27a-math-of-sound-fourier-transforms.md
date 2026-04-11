<!--
  CHAPTER: 27a
  TITLE: The Math of Sound — Fourier Transforms and Spectral Analysis
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Chapter 27
  KEY_TOPICS: Fourier transform, DFT, FFT, frequency bins, windowing, spectrogram, spectral analysis, STFT, real-time spectrum analyzer
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 27a: The Math of Sound — Fourier Transforms and Spectral Analysis

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Chapter 27 | Difficulty: Inter→Adv

Chapter 27 handed you a toolbox full of filters -- biquads, FIR convolution, parametric EQ, dynamics processing. You learned to shape frequency content: boost this, cut that, split the signal at a crossover point. But there's a question we haven't answered yet: *how do you see what's inside a signal in the first place?*

When you strum a guitar chord, your ears decompose it instantly. You hear the individual notes, the brightness of the pick attack, the buzz of a fret that isn't pressed cleanly. Your auditory system is performing a kind of frequency analysis in real time, all the time, without you thinking about it. The cochlea in your inner ear is literally a biological spectrum analyzer -- different positions along its length respond to different frequencies.

The Fourier transform is the mathematical version of what your ear does. It takes a waveform -- a sequence of amplitude values over time -- and decomposes it into its constituent frequencies. It tells you *what* frequencies are present and *how much* of each one. It's the single most important tool in all of signal processing, and it underpins nearly everything in this guide: REW measurements, spectrum analyzers, crossover design, room analysis, speaker response curves, even the FFT-based convolution that CamillaDSP uses for room correction.

This chapter teaches you the Fourier transform from the ground up, through working Python code. You already know Python. By the end, you'll have built a real-time spectrum analyzer that captures audio from your microphone, runs an FFT, and displays the frequency content of your guitar signal as a live bar graph. Every concept is connected back to the audio projects in this guide.

### In This Chapter
- The core insight: any sound is a sum of sine waves
- The Discrete Fourier Transform (DFT) -- what it does, how it works
- The Fast Fourier Transform (FFT) -- the same thing, but fast enough to be useful
- Frequency bins and the resolution trade-off
- Windowing: why raw FFTs leak, and how to fix it
- The spectrogram: seeing frequency content change over time
- How FFT connects to every other chapter in this guide
- Build: a real-time spectrum analyzer in Python

### Related Chapters
- [Ch 6: Gain Stages and Clipping](../part-1-guitar-pedals/06-gain-stages-clipping.md) -- distortion creates harmonics visible in the spectrum
- [Ch 13: Crossover Design](../part-2-speakers/13-crossover-design.md) -- crossovers are frequency-domain splits
- [Ch 15: Design Tools and Simulation](../part-2-speakers/15-design-tools-simulation.md) -- REW uses FFT for speaker measurement
- [Ch 25: Digital Audio Fundamentals](25-digital-audio-fundamentals.md) -- sampling, Nyquist, and the digital signals we're analyzing
- [Ch 27: DSP Fundamentals](27-dsp-fundamentals.md) -- filters are frequency-domain operations
- [Ch 28: Build: Pi Streamer with Physical Controls](28-build-pi-streamer.md) -- the hardware running your DSP
- [Ch 29: Build: Active Crossover and Room Correction](29-build-active-crossover-room-correction.md) -- measurement-driven DSP that relies on FFT
- [Ch 36: Room Acoustics and Treatment](../part-6-system-design/36-room-acoustics-treatment.md) -- room modes are standing waves at specific frequencies

---

## 1. The Big Idea: Any Sound Is a Sum of Sine Waves

### 1.1 The Insight That Changed Everything

In 1807, Joseph Fourier made a claim that his contemporaries found outrageous: *any periodic signal can be represented as a sum of sine waves at different frequencies, amplitudes, and phases.* His colleagues at the French Academy of Sciences didn't believe him. It sounded too simple to be true. But it was true, and it became the foundation of modern signal processing, communications, quantum mechanics, medical imaging, and basically every field that deals with signals.

For audio, the implication is direct: every sound you've ever heard -- a guitar chord, a kick drum, a human voice, traffic noise, a symphony orchestra -- is a specific combination of sine waves at different frequencies and amplitudes. A pure sine wave sounds like a tuning fork: clean, featureless, boring. A guitar string vibrating at 440 Hz produces a sine wave at 440 Hz (the fundamental) *plus* sine waves at 880 Hz, 1320 Hz, 1760 Hz, and so on (the harmonics). The specific amplitudes of those harmonics are what make a guitar sound like a guitar instead of a flute or a clarinet playing the same note.

This is not a metaphor. It's not an approximation. It's an exact mathematical identity. If you add up the right sine waves at the right amplitudes and phases, you get the original signal back perfectly.

### 1.2 Seeing It in Code

Let's make this concrete. We'll generate three sine waves at different frequencies, add them together, and see what happens.

```python
import numpy as np
import matplotlib.pyplot as plt

# Parameters
fs = 44100          # Sample rate (CD quality)
duration = 0.01     # 10 milliseconds -- enough to see several cycles
t = np.linspace(0, duration, int(fs * duration), endpoint=False)

# Three sine waves: A4 (440 Hz), E5 (659 Hz), A5 (880 Hz)
# This is an A power chord -- root, fifth, octave
freq1, freq2, freq3 = 440, 659, 880
amp1, amp2, amp3 = 1.0, 0.8, 0.6    # Different amplitudes

sine1 = amp1 * np.sin(2 * np.pi * freq1 * t)
sine2 = amp2 * np.sin(2 * np.pi * freq2 * t)
sine3 = amp3 * np.sin(2 * np.pi * freq3 * t)

# The combined signal
combined = sine1 + sine2 + sine3

# Plot all four
fig, axes = plt.subplots(4, 1, figsize=(12, 10), sharex=True)

axes[0].plot(t * 1000, sine1, color="steelblue")
axes[0].set_ylabel("Amplitude")
axes[0].set_title(f"440 Hz (A4) -- the root")

axes[1].plot(t * 1000, sine2, color="coral")
axes[1].set_ylabel("Amplitude")
axes[1].set_title(f"659 Hz (E5) -- the fifth")

axes[2].plot(t * 1000, sine3, color="seagreen")
axes[2].set_ylabel("Amplitude")
axes[2].set_title(f"880 Hz (A5) -- the octave")

axes[3].plot(t * 1000, combined, color="black", linewidth=1.5)
axes[3].set_ylabel("Amplitude")
axes[3].set_title("Combined -- all three at once")
axes[3].set_xlabel("Time (ms)")

for ax in axes:
    ax.grid(True, alpha=0.3)
    ax.set_xlim(0, duration * 1000)

plt.tight_layout()
plt.savefig("three_sines_combined.png", dpi=150)
plt.show()
```

Run this. The first three plots are clean, simple sine waves. The bottom plot looks like a tangled mess -- a complex waveform with no obvious pattern. But it contains *exactly* three frequencies, no more. The Fourier transform's job is to look at that bottom waveform and tell you: "There's a sine wave at 440 Hz with amplitude 1.0, one at 659 Hz with amplitude 0.8, and one at 880 Hz with amplitude 0.6."

### 1.3 Hearing It

You can also listen to these signals. This makes the abstract concrete:

```python
import sounddevice as sd

# Play each sine wave separately, then the combination
print("Playing 440 Hz (A4)...")
sd.play(sine1.astype(np.float32), samplerate=fs)
sd.wait()

print("Playing 659 Hz (E5)...")
sd.play(sine2.astype(np.float32), samplerate=fs)
sd.wait()

print("Playing 880 Hz (A5)...")
sd.play(sine3.astype(np.float32), samplerate=fs)
sd.wait()

print("Playing all three combined...")
# Normalize to prevent clipping
combined_normalized = combined / np.max(np.abs(combined))
sd.play(combined_normalized.astype(np.float32), samplerate=fs)
sd.wait()
```

Install `sounddevice` with `pip install sounddevice` if you don't have it. The individual sines sound like pure tones -- boring, clinical. The combination sounds like a chord. Your ear separates the frequencies effortlessly. The Fourier transform does the same thing, but gives you exact numbers.

### 1.4 Real Sounds Are More Complex (But the Principle Holds)

A real guitar chord has hundreds of frequency components: the fundamentals of each string, their harmonics, the body resonance of the guitar, the pick attack transient, string-to-string interactions. The waveform looks nothing like a sine wave. But Fourier's theorem still holds. Every one of those frequencies is a sine wave in the sum. The Fourier transform decomposes the mess into a list of frequencies and amplitudes. That list is called the **spectrum**.

---

## 2. The Discrete Fourier Transform (DFT)

### 2.1 From Continuous to Discrete

Fourier's original mathematics works with continuous signals -- signals that exist at every instant in time, with infinite precision. Digital audio isn't continuous. It's a sequence of samples, taken at regular intervals (44,100 times per second for CD audio, as you learned in Chapter 25). The **Discrete Fourier Transform** (DFT) is the version of Fourier analysis that works with sampled data.

The DFT takes N samples of a time-domain signal and produces N complex numbers representing the frequency content. Each complex number corresponds to a specific frequency, and its magnitude tells you how much of that frequency is present.

### 2.2 The DFT Equation

Here it is:

```
X[k] = sum from n=0 to N-1 of: x[n] * e^(-j * 2 * pi * k * n / N)
```

Where:
- `x[n]` is the input signal: N samples in the time domain
- `X[k]` is the output: N complex values in the frequency domain
- `n` is the sample index (time)
- `k` is the frequency bin index (frequency)
- `N` is the total number of samples
- `j` is the imaginary unit (sqrt(-1))
- `e^(-j*theta)` is a complex exponential, which is just a compact way of writing `cos(theta) - j*sin(theta)`

Don't let the `e^(-j...)` intimidate you. Here's what the DFT is actually doing: for each frequency bin `k`, it multiplies the input signal by a sine and cosine at that frequency, and sums the result. If the input signal contains energy at that frequency, the multiplication produces a large sum (constructive interference). If the input signal doesn't contain that frequency, the multiplication produces values that cancel out (destructive interference). It's correlation -- you're asking "how much does my signal look like a sine wave at this frequency?"

The complex output `X[k]` encodes two things:
- **Magnitude**: `|X[k]| = sqrt(real^2 + imag^2)` -- how much energy at frequency k
- **Phase**: `angle(X[k]) = atan2(imag, real)` -- the phase offset of that frequency component

For most audio analysis, you only care about the magnitude. Phase matters for filter design and reconstruction, but when you're looking at "what frequencies are in this signal," magnitude is what you want.

### 2.3 Implementing the DFT from Scratch

Here's a naive DFT implementation in Python. It follows the equation directly:

```python
import numpy as np

def dft_naive(x):
    """Compute the DFT of signal x using the direct definition.
    
    This is O(N^2) -- slow, but transparent.
    """
    N = len(x)
    X = np.zeros(N, dtype=complex)
    
    for k in range(N):         # For each frequency bin
        for n in range(N):     # Sum over all time samples
            angle = -2 * np.pi * k * n / N
            X[k] += x[n] * np.exp(1j * angle)
    
    return X
```

Ten lines of code. Two nested loops, each running N times, so the total computation is O(N^2). Let's test it on our three-sine signal:

```python
# Generate a short signal: 3 sines, 1024 samples
N = 1024
fs = 44100
t = np.arange(N) / fs

signal = (1.0 * np.sin(2 * np.pi * 440 * t) +
          0.8 * np.sin(2 * np.pi * 659 * t) +
          0.6 * np.sin(2 * np.pi * 880 * t))

# Compute DFT
X = dft_naive(signal)

# Frequency axis
freqs = np.arange(N) * fs / N

# Plot magnitude spectrum (only first half -- second half is the mirror)
plt.figure(figsize=(12, 5))
plt.plot(freqs[:N//2], np.abs(X[:N//2]) * 2 / N, color="steelblue")
plt.xlabel("Frequency (Hz)")
plt.ylabel("Amplitude")
plt.title("DFT Magnitude Spectrum of Three Sines")
plt.xlim(0, 2000)
plt.grid(True, alpha=0.3)
plt.savefig("dft_three_sines.png", dpi=150)
plt.show()
```

You should see three sharp peaks: one at 440 Hz (amplitude ~1.0), one at 659 Hz (amplitude ~0.8), and one at 880 Hz (amplitude ~0.6). The DFT has decomposed the "complex" waveform back into its constituent frequencies, recovering the exact amplitudes we started with. That's the magic.

### 2.4 Why the Naive DFT Is Useless in Practice

Try running `dft_naive` on a signal with 44,100 samples (1 second at CD sample rate):

```python
import time

N = 44100
signal_long = np.random.randn(N)

start = time.time()
X_slow = dft_naive(signal_long)
elapsed = time.time() - start
print(f"DFT of {N} samples took {elapsed:.1f} seconds")
```

On a typical machine, this takes *minutes*. The O(N^2) complexity means that doubling the number of samples quadruples the computation time. For real-time audio processing at 44.1 kHz, you'd need to process 44,100 DFT points every second. The naive DFT can't even do one per minute. It's a mathematical definition, not a practical algorithm.

This is where the FFT enters the picture.

---

## 3. The Fast Fourier Transform (FFT)

### 3.1 The Cooley-Tukey Algorithm

In 1965, James Cooley and John Tukey published an algorithm that computes the DFT in O(N log N) instead of O(N^2). The trick: they noticed that the DFT of an N-point signal can be broken into two DFTs of N/2-point signals (the even-indexed and odd-indexed samples), each of which can be broken into two DFTs of N/4-point signals, and so on, recursively, until you reach single-point DFTs (which are trivial). This divide-and-conquer approach eliminates the vast majority of redundant multiplications.

The result is called the **Fast Fourier Transform** (FFT). It computes *exactly* the same output as the DFT -- the same N complex numbers, identical to the last bit. It just gets there enormously faster.

How much faster? For N = 1024:
- DFT: 1024 x 1024 = 1,048,576 operations
- FFT: 1024 x log2(1024) = 1024 x 10 = 10,240 operations
- **100x faster**

For N = 65,536 (a typical room correction FIR filter length):
- DFT: ~4.3 billion operations
- FFT: ~1 million operations
- **4000x faster**

This is why CamillaDSP can run 65,536-tap FIR convolution in real time on a Raspberry Pi. Without the FFT, real-time spectral analysis, digital audio processing, and basically all of modern signal processing would be computationally infeasible.

> **What happens if...** you use a non-power-of-2 FFT size? The Cooley-Tukey algorithm works best when N is a power of 2 (512, 1024, 2048, 4096, ...) because each split divides N exactly in half. If N is, say, 1000, numpy's FFT implementation uses a mixed-radix algorithm that works with any size but is fastest for powers of 2. In practice: always use power-of-2 FFT sizes unless you have a specific reason not to.

### 3.2 Using FFT in Python

You don't need to implement the FFT yourself. NumPy's implementation is battle-tested, optimized, and handles all the edge cases:

```python
import numpy as np
import matplotlib.pyplot as plt

# Same signal: three sines
N = 4096
fs = 44100
t = np.arange(N) / fs

signal = (1.0 * np.sin(2 * np.pi * 440 * t) +
          0.8 * np.sin(2 * np.pi * 659 * t) +
          0.6 * np.sin(2 * np.pi * 880 * t))

# Compute FFT
X = np.fft.fft(signal)

# Frequency axis: each bin corresponds to a frequency
freqs = np.fft.fftfreq(N, d=1/fs)

# Take only the positive frequencies (first half)
positive_mask = freqs >= 0
freqs_pos = freqs[positive_mask]
magnitudes = np.abs(X[positive_mask]) * 2 / N   # Scale to get actual amplitude

# Plot
plt.figure(figsize=(12, 5))
plt.plot(freqs_pos, magnitudes, color="steelblue", linewidth=0.8)
plt.xlabel("Frequency (Hz)")
plt.ylabel("Amplitude")
plt.title(f"FFT Magnitude Spectrum (N={N}, fs={fs} Hz)")
plt.xlim(0, 2000)
plt.grid(True, alpha=0.3)

# Annotate the peaks
for freq, amp in [(440, 1.0), (659, 0.8), (880, 0.6)]:
    plt.annotate(f"{freq} Hz", xy=(freq, amp), xytext=(freq + 40, amp + 0.05),
                 fontsize=9, arrowprops=dict(arrowstyle="->", color="gray"))

plt.tight_layout()
plt.savefig("fft_three_sines.png", dpi=150)
plt.show()
```

Compare the speed to the naive DFT:

```python
import time

N = 44100
signal_long = np.random.randn(N)

start = time.time()
X_fast = np.fft.fft(signal_long)
elapsed = time.time() - start
print(f"FFT of {N} samples took {elapsed*1000:.2f} milliseconds")
```

Milliseconds, not minutes. That's the difference between O(N^2) and O(N log N).

### 3.3 Understanding the FFT Output

The FFT produces N complex numbers. Here's what they mean:

**Frequency bins**: Each output value `X[k]` corresponds to a frequency of `k * fs / N`. This means:
- Bin 0 is DC (0 Hz) -- the average value of the signal
- Bin 1 is `fs/N` Hz -- the lowest non-zero frequency
- Bin N/2 is `fs/2` Hz -- the Nyquist frequency

**The Nyquist mirror**: The FFT output is symmetric for real-valued input signals. Bins 0 through N/2 contain the positive frequencies. Bins N/2+1 through N-1 are the mirror image (negative frequencies). They contain the same magnitude information, just conjugated. For audio analysis, you only ever look at the first half. That's why the code above masks to positive frequencies and multiplies by 2 (to account for the energy in the mirror half).

**Magnitude vs. power**: The magnitude `|X[k]|` gives you the amplitude of each frequency component. The power spectrum is `|X[k]|^2`. For audio analysis, people usually display magnitude in decibels:

```python
# Convert to dB (relative to maximum)
magnitude_db = 20 * np.log10(magnitudes / np.max(magnitudes) + 1e-10)

plt.figure(figsize=(12, 5))
plt.plot(freqs_pos, magnitude_db, color="steelblue", linewidth=0.8)
plt.xlabel("Frequency (Hz)")
plt.ylabel("Magnitude (dB)")
plt.title("FFT Magnitude Spectrum in dB")
plt.xlim(20, 20000)
plt.ylim(-80, 5)
plt.xscale("log")
plt.grid(True, which="both", alpha=0.3)
plt.tight_layout()
plt.show()
```

The dB scale is standard for audio because human hearing is logarithmic. A 60 dB difference looks like a factor of 1000 on a linear scale -- unusable. On a dB scale, it's a readable range. This is the same dB scale from Chapter 0 and every chapter since.

### 3.4 Bin Width: The Fundamental Resolution

The **bin width** (also called frequency resolution) is:

```
bin_width = fs / N
```

Where `fs` is the sample rate and `N` is the FFT size (number of samples).

| FFT Size (N) | Sample Rate (fs) | Bin Width | Can resolve... |
|---|---|---|---|
| 256 | 44100 | 172 Hz | octave-scale features |
| 1024 | 44100 | 43 Hz | individual notes above ~200 Hz |
| 4096 | 44100 | 10.8 Hz | individual notes in the bass |
| 16384 | 44100 | 2.7 Hz | fine room mode analysis |
| 65536 | 44100 | 0.67 Hz | extremely fine resolution |

For our three-sine signal (440, 659, 880 Hz), an FFT size of 1024 gives 43 Hz bin width. The nearest bins to 440 Hz are at 430.7 Hz and 473.7 Hz -- close but not exact. The 440 Hz energy spreads across nearby bins. With N=4096 (bin width ~10.8 Hz), the bins land much closer to the actual frequencies, giving sharper peaks. This is the resolution trade-off, and we'll explore it deeply in Section 4.

### 3.5 Zero-Padding: Interpolation, Not Resolution

A common technique is **zero-padding**: appending zeros to the signal before computing the FFT. If you have 1024 samples and pad to 4096, you get 4096 FFT bins with a bin spacing of `fs/4096` instead of `fs/1024`.

```python
# Original signal: 1024 samples
N_original = 1024
signal_short = np.sin(2 * np.pi * 440 * np.arange(N_original) / fs)

# Zero-pad to 4096
N_padded = 4096
signal_padded = np.zeros(N_padded)
signal_padded[:N_original] = signal_short

# FFT both
X_short = np.fft.fft(signal_short)
X_padded = np.fft.fft(signal_padded)

freqs_short = np.fft.fftfreq(N_original, 1/fs)
freqs_padded = np.fft.fftfreq(N_padded, 1/fs)

fig, axes = plt.subplots(2, 1, figsize=(12, 8))

axes[0].plot(freqs_short[:N_original//2],
             np.abs(X_short[:N_original//2]) * 2 / N_original,
             "o-", markersize=4, color="steelblue")
axes[0].set_title(f"FFT of {N_original} samples (bin width = {fs/N_original:.1f} Hz)")
axes[0].set_xlim(300, 600)
axes[0].set_ylabel("Amplitude")
axes[0].grid(True, alpha=0.3)

axes[1].plot(freqs_padded[:N_padded//2],
             np.abs(X_padded[:N_padded//2]) * 2 / N_original,  # Note: normalize by original N
             "o-", markersize=2, color="coral")
axes[1].set_title(f"Zero-padded to {N_padded} (bin width = {fs/N_padded:.1f} Hz)")
axes[1].set_xlim(300, 600)
axes[1].set_xlabel("Frequency (Hz)")
axes[1].set_ylabel("Amplitude")
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("zero_padding.png", dpi=150)
plt.show()
```

The zero-padded version has more points in the spectrum -- it's a smoother curve. But the *actual frequency resolution* hasn't improved. You still only have 1024 samples of information. Zero-padding interpolates between the coarse bins, giving you a prettier picture, but it can't resolve two frequencies that were too close together in the original 1024 samples.

Think of it like this: if you take a low-resolution photo and upscale it, you get more pixels but no more detail. Zero-padding is the FFT equivalent of image upscaling.

**When is zero-padding useful?** When the true frequency doesn't align with a bin center. With 1024 points, the nearest bin to 440 Hz might be at 430.7 Hz. Zero-padding gives you intermediate bins that land closer to 440 Hz, making the peak position more accurate in the plot. It's cosmetic -- the information was already there, you just couldn't see it at the coarser bin spacing.

---

## 4. Frequency Bins and Resolution

### 4.1 The Fundamental Trade-Off

This is the most important concept in practical spectral analysis: **you cannot have perfect frequency resolution and perfect time resolution simultaneously.** Improving one degrades the other. This is not a limitation of the FFT algorithm or your computer's processing power. It's a fundamental mathematical constraint, often called the **uncertainty principle of signal processing** (yes, it's related to Heisenberg's uncertainty principle in physics -- same math).

Here's why. The FFT analyzes a *window* of N samples. That window represents a duration of N/fs seconds. To resolve two frequencies that are close together, you need enough cycles of each frequency to tell them apart. If the window is short (few samples), you see very few cycles, and closely spaced frequencies blur together. If the window is long (many samples), you see many cycles, and closely spaced frequencies resolve into distinct peaks.

But a long window means you're averaging over a longer time period. If the frequency content changes during that window (a note bending, a transient attack, a drum hit), the change gets smeared out. You know *what* frequencies were present, but not *when* they appeared or disappeared.

### 4.2 Short Window vs. Long Window

Let's make this concrete. We'll create a signal where the frequency changes halfway through: 440 Hz for the first half, 880 Hz for the second half. Then we'll analyze it with different FFT sizes.

```python
import numpy as np
import matplotlib.pyplot as plt

fs = 44100
duration = 0.1   # 100 ms total
t = np.arange(int(fs * duration)) / fs
N_total = len(t)

# Signal: 440 Hz for first half, 880 Hz for second half
midpoint = N_total // 2
signal = np.zeros(N_total)
signal[:midpoint] = np.sin(2 * np.pi * 440 * t[:midpoint])
signal[midpoint:] = np.sin(2 * np.pi * 880 * t[midpoint:])

# Analyze with different FFT sizes (using the whole signal as one block)
fig, axes = plt.subplots(3, 1, figsize=(12, 10))
fft_sizes = [256, 1024, 4096]

for ax, N_fft in zip(axes, fft_sizes):
    # Take the first N_fft samples (or zero-pad if signal is shorter)
    chunk = signal[:N_fft] if N_fft <= N_total else np.pad(signal, (0, N_fft - N_total))
    
    X = np.fft.fft(chunk)
    freqs = np.fft.fftfreq(N_fft, 1/fs)
    positive = freqs >= 0
    
    magnitude = np.abs(X[positive]) * 2 / N_fft
    
    ax.plot(freqs[positive], magnitude, color="steelblue", linewidth=0.8)
    ax.set_xlim(0, 2000)
    ax.set_ylabel("Amplitude")
    ax.set_title(f"FFT size = {N_fft} | bin width = {fs/N_fft:.1f} Hz | "
                 f"window duration = {N_fft/fs*1000:.1f} ms")
    ax.grid(True, alpha=0.3)

axes[-1].set_xlabel("Frequency (Hz)")
plt.tight_layout()
plt.savefig("resolution_tradeoff.png", dpi=150)
plt.show()
```

What you'll see:
- **N=256 (5.8 ms window, 172 Hz bin width)**: Two very broad bumps somewhere near 440 Hz and 880 Hz. The short window captured only a few cycles of each frequency, so they're poorly defined. But if you ran this FFT on the first half and then the second half *separately*, you'd know *when* the frequency changed.
- **N=1024 (23.2 ms window, 43 Hz bin width)**: Clearer peaks at 440 Hz and 880 Hz. Better frequency resolution, but the 23 ms window spans both halves of the signal, so you see both frequencies blurred together.
- **N=4096 (92.9 ms window, 10.8 Hz bin width)**: Very sharp peaks. You can precisely identify both frequencies. But the window covers almost the entire 100 ms signal -- you have no time resolution at all. You know what frequencies were present, but not when.

### 4.3 The Uncertainty Principle in Practice

The product of time resolution and frequency resolution has a lower bound:

```
delta_t * delta_f >= 1
```

Where `delta_t = N/fs` (the window duration in seconds) and `delta_f = fs/N` (the bin width in Hz). Their product is always exactly 1 for the rectangular window, and slightly larger for other window types.

What this means for audio work:

| Application | Need | Recommended FFT Size at 44.1 kHz |
|---|---|---|
| Onset detection (drum hits) | Good time resolution | 256-512 (5.8-11.6 ms) |
| Real-time spectrum display | Balance of both | 2048-4096 (46-93 ms) |
| Room mode identification | Good frequency resolution | 8192-16384 (186-372 ms) |
| Speaker impedance measurement | Very fine frequency resolution | 32768-65536 (743-1486 ms) |

The spectrogram (Section 6) is the practical solution: instead of choosing one FFT size and living with the trade-off, you chop the signal into overlapping windows and run an FFT on each window. You get a time-frequency picture that shows both what frequencies are present and when they appear.

> **What happens if...** you need to identify a room mode at exactly 63 Hz? The bin width must be small enough to resolve it. At 44.1 kHz, an FFT size of 4096 gives ~10.8 Hz bins, so the nearest bins are at ~53.8 Hz and ~64.5 Hz. That might be close enough to see the mode. But if you need to distinguish between a mode at 63 Hz and one at 67 Hz, you need bins smaller than 4 Hz, which requires N > 11,025 -- use N=16,384 (bin width 2.7 Hz). This is exactly what REW does when it runs a high-resolution room measurement.

---

## 5. Windowing

### 5.1 The Problem: Spectral Leakage

There's a catch with the FFT that we've been ignoring until now. The DFT assumes that your N-sample block is one period of a *periodic* signal -- that is, the signal repeats endlessly with the same N samples. If the signal's frequency happens to produce an exact integer number of cycles within your N samples, everything is fine. The start and end of the block join seamlessly.

But if the frequency doesn't divide evenly into N samples (and for real-world signals, it almost never does), there's a discontinuity at the edges. The last sample of your block doesn't smoothly connect to the first sample of the (assumed) next repetition. This discontinuity creates a sharp edge in the signal, and sharp edges have energy spread across all frequencies -- the same way a square wave has harmonics that extend to infinity.

This spreading is called **spectral leakage**. Energy from the true frequency "leaks" into neighboring bins, creating broad skirts around each peak instead of a sharp spike. The worse the discontinuity at the edges, the worse the leakage.

Let's see it:

```python
import numpy as np
import matplotlib.pyplot as plt

fs = 44100
N = 1024

# Case 1: Frequency lands exactly on a bin (no leakage)
# Bin spacing = 44100/1024 = 43.07 Hz
# 440 Hz / 43.07 = ~10.21 -- not integer! Let's pick one that IS integer.
exact_freq = 10 * fs / N   # = 430.66 Hz, exactly bin 10
signal_exact = np.sin(2 * np.pi * exact_freq * np.arange(N) / fs)

# Case 2: Frequency falls between bins (leakage)
leaky_freq = 440   # Doesn't land on a bin
signal_leaky = np.sin(2 * np.pi * leaky_freq * np.arange(N) / fs)

# FFT both
X_exact = np.fft.fft(signal_exact)
X_leaky = np.fft.fft(signal_leaky)
freqs = np.fft.fftfreq(N, 1/fs)
positive = freqs >= 0

fig, axes = plt.subplots(2, 1, figsize=(12, 8))

axes[0].plot(freqs[positive], 20 * np.log10(np.abs(X_exact[positive]) / N + 1e-10),
             color="steelblue", linewidth=0.8)
axes[0].set_title(f"Frequency exactly on bin ({exact_freq:.1f} Hz) -- no leakage")
axes[0].set_ylabel("Magnitude (dB)")
axes[0].set_xlim(200, 700)
axes[0].set_ylim(-80, 0)
axes[0].grid(True, alpha=0.3)

axes[1].plot(freqs[positive], 20 * np.log10(np.abs(X_leaky[positive]) / N + 1e-10),
             color="coral", linewidth=0.8)
axes[1].set_title(f"Frequency between bins ({leaky_freq} Hz) -- leakage!")
axes[1].set_ylabel("Magnitude (dB)")
axes[1].set_xlabel("Frequency (Hz)")
axes[1].set_xlim(200, 700)
axes[1].set_ylim(-80, 0)
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("spectral_leakage.png", dpi=150)
plt.show()
```

The on-bin frequency produces a single sharp spike with the rest of the spectrum at essentially negative infinity dB. The off-bin frequency shows a main peak surrounded by "skirts" that extend well above the noise floor. Those skirts are the leaked energy. In a real signal with multiple frequencies at different levels, the leakage from a strong component can mask a weak component nearby. This is a real problem.

### 5.2 The Solution: Window Functions

A **window function** is a shape that you multiply your signal by *before* running the FFT. The window tapers the signal smoothly to zero at both edges, eliminating the discontinuity. No discontinuity means no sharp edge, which means dramatically reduced leakage.

The simplest window is no window at all -- the **rectangular window**, which multiplies every sample by 1.0. This is what we've been using so far. The most common windows for audio work are:

**Hann window** (sometimes incorrectly called "Hanning"):
```python
w = 0.5 * (1 - np.cos(2 * np.pi * np.arange(N) / N))
```
Tapers smoothly to zero at both ends. Good all-around choice. The most commonly used window in audio analysis.

**Hamming window**:
```python
w = 0.54 - 0.46 * np.cos(2 * np.pi * np.arange(N) / N)
```
Similar to Hann but doesn't quite reach zero at the edges. Slightly narrower main lobe than Hann, but higher sidelobes.

**Blackman window**:
```python
w = 0.42 - 0.5 * np.cos(2 * np.pi * np.arange(N) / N) + 0.08 * np.cos(4 * np.pi * np.arange(N) / N)
```
More aggressive tapering. Lower sidelobes than Hann (better at suppressing leakage from strong signals), but wider main lobe (slightly worse frequency resolution).

**Flat-top window**: Specialized for amplitude accuracy. The main lobe is very flat on top, so even if your frequency doesn't land exactly on a bin center, the measured amplitude is accurate. Used when you care about precise amplitude measurement (calibration, distortion measurement).

### 5.3 Seeing the Difference

```python
import numpy as np
import matplotlib.pyplot as plt

fs = 44100
N = 1024
t = np.arange(N) / fs

# Signal: single sine at a frequency that doesn't land on a bin
freq = 440
signal = np.sin(2 * np.pi * freq * t)

# Windows
windows = {
    "Rectangular (no window)": np.ones(N),
    "Hann": np.hanning(N),
    "Hamming": np.hamming(N),
    "Blackman": np.blackman(N),
}

fig, axes = plt.subplots(len(windows), 1, figsize=(12, 12), sharex=True)

for ax, (name, window) in zip(axes, windows.items()):
    windowed_signal = signal * window
    X = np.fft.fft(windowed_signal)
    freqs = np.fft.fftfreq(N, 1/fs)
    positive = freqs >= 0
    
    mag_db = 20 * np.log10(np.abs(X[positive]) / np.max(np.abs(X[positive])) + 1e-10)
    
    ax.plot(freqs[positive], mag_db, color="steelblue", linewidth=0.8)
    ax.set_title(name)
    ax.set_ylabel("dB (normalized)")
    ax.set_xlim(200, 700)
    ax.set_ylim(-100, 5)
    ax.grid(True, alpha=0.3)

axes[-1].set_xlabel("Frequency (Hz)")
plt.tight_layout()
plt.savefig("window_comparison.png", dpi=150)
plt.show()
```

The results tell the story:
- **Rectangular**: Narrow main peak, but sidelobes only 13 dB below the peak. A signal 14 dB quieter than the main tone would be hidden.
- **Hann**: Slightly wider main peak, but sidelobes drop to -31 dB. Much better.
- **Hamming**: Similar to Hann with sidelobes at -42 dB. Better suppression.
- **Blackman**: Widest main peak, but sidelobes below -58 dB. Excellent leakage suppression.

### 5.4 Which Window to Use

| Situation | Recommended Window | Why |
|---|---|---|
| General music analysis | Hann | Best balance of resolution and leakage suppression |
| Measuring single tones (THD, IMD) | Blackman or flat-top | Need to see harmonics 60+ dB below fundamental |
| Transient analysis (drum hits) | Hann or rectangular | Preserve time-domain shape |
| Overlap-add processing (STFT) | Hann with 50% overlap | Satisfies constant-overlap-add (COLA) condition |
| Speaker frequency response measurement | Hann | Standard for REW and most measurement tools |
| Measuring SNR of a DAC | Blackman-Harris | Very low sidelobes to see noise floor accurately |

If you're unsure, use Hann. It's the default in virtually every audio analysis tool for good reason. REW uses a Hann window for its FFT analysis. CamillaDSP uses it internally. Matplotlib's `specgram()` uses it by default.

### 5.5 The Cost of Windowing

Windowing isn't free. By tapering the signal to zero at the edges, you're effectively discarding data near the edges of your window. This has two consequences:

1. **Wider main lobe**: The frequency resolution gets slightly worse. A Hann window makes the effective main lobe about 2x wider than rectangular. In practice, this means your effective frequency resolution is roughly `2 * fs / N` instead of `fs / N`.

2. **Reduced amplitude accuracy**: The taper reduces the total energy, so the measured amplitude is lower than the true amplitude. Each window type has a correction factor. For Hann, it's a factor of 2 (multiply your magnitudes by 2 to compensate).

The overlap technique in spectrograms (Section 6) recovers the data lost at the edges: consecutive windows overlap by 50-75%, so the edges of one window are the center of the adjacent windows.

---

## 6. The Spectrogram (Time-Frequency Analysis)

### 6.1 The Limitation of a Single FFT

A single FFT gives you the frequency content of a chunk of audio, but it tells you nothing about *when* things happened. For a sustained chord, that's fine -- the frequency content is roughly constant. But for music, speech, or any sound that evolves over time, you need both dimensions: what frequencies are present *and* when they appear.

### 6.2 The Short-Time Fourier Transform (STFT)

The solution is the **Short-Time Fourier Transform**: chop the signal into short, overlapping windows, apply a window function to each, compute the FFT of each windowed chunk, and stack the results side by side. Each column in the resulting 2D matrix represents the spectrum at one moment in time.

The parameters:
- **Window size (N)**: Determines frequency resolution (bin width = fs/N) and time resolution (window duration = N/fs)
- **Hop size**: How many samples to advance between consecutive windows. Typical: N/2 (50% overlap) or N/4 (75% overlap)
- **Window function**: Usually Hann for audio

```python
import numpy as np
import matplotlib.pyplot as plt

def stft_simple(signal, window_size, hop_size, fs):
    """Compute a basic STFT."""
    window = np.hanning(window_size)
    num_windows = (len(signal) - window_size) // hop_size + 1
    
    stft_matrix = np.zeros((window_size // 2 + 1, num_windows), dtype=complex)
    
    for i in range(num_windows):
        start = i * hop_size
        chunk = signal[start:start + window_size] * window
        spectrum = np.fft.rfft(chunk)
        stft_matrix[:, i] = spectrum
    
    return stft_matrix

# Create a chirp signal (frequency sweep from 200 Hz to 2000 Hz)
fs = 44100
duration = 2.0
t = np.linspace(0, duration, int(fs * duration), endpoint=False)
chirp = np.sin(2 * np.pi * (200 + (2000 - 200) * t / (2 * duration)) * t)

# Compute STFT
window_size = 2048
hop_size = 512
S = stft_simple(chirp, window_size, hop_size, fs)

# Convert to dB
magnitude_db = 20 * np.log10(np.abs(S) + 1e-10)

# Time and frequency axes
time_axis = np.arange(S.shape[1]) * hop_size / fs
freq_axis = np.linspace(0, fs / 2, S.shape[0])

plt.figure(figsize=(14, 6))
plt.pcolormesh(time_axis, freq_axis, magnitude_db, shading="gouraud",
               cmap="magma", vmin=-60, vmax=0)
plt.colorbar(label="Magnitude (dB)")
plt.xlabel("Time (s)")
plt.ylabel("Frequency (Hz)")
plt.ylim(0, 3000)
plt.title("Spectrogram of a Frequency Chirp (200 Hz to 2000 Hz)")
plt.tight_layout()
plt.savefig("spectrogram_chirp.png", dpi=150)
plt.show()
```

You should see a diagonal line sweeping from 200 Hz at the left to 2000 Hz at the right. The spectrogram simultaneously shows *what* frequency is present and *when* -- something no single FFT can do.

### 6.3 Using scipy and matplotlib (The Easy Way)

You don't need to implement the STFT from scratch for practical work. Both scipy and matplotlib have optimized implementations:

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import spectrogram

fs = 44100
duration = 3.0
t = np.linspace(0, duration, int(fs * duration), endpoint=False)

# Simulate a guitar chord being strummed:
# Strings hit one at a time over ~100ms, then ring out
# Low E (82 Hz), A (110 Hz), D (147 Hz), G (196 Hz), B (247 Hz), high E (330 Hz)
guitar_freqs = [82.4, 110.0, 146.8, 196.0, 246.9, 329.6]
signal = np.zeros_like(t)

for i, freq in enumerate(guitar_freqs):
    # Each string starts with a slight delay (strum takes ~15ms per string)
    delay = i * 0.015
    mask = t >= delay
    
    # Exponential decay (strings ring out)
    envelope = np.exp(-(t - delay) * 2.0) * mask
    
    # Fundamental + harmonics (2nd, 3rd, 4th)
    for harmonic in range(1, 5):
        amplitude = 1.0 / harmonic   # Harmonics get quieter
        signal += amplitude * envelope * np.sin(2 * np.pi * freq * harmonic * t)

# Normalize
signal = signal / np.max(np.abs(signal))

# Compute spectrogram using scipy
f, t_spec, Sxx = spectrogram(signal, fs, nperseg=4096, noverlap=3072,
                              window="hann")

plt.figure(figsize=(14, 7))
plt.pcolormesh(t_spec, f, 10 * np.log10(Sxx + 1e-10), shading="gouraud",
               cmap="inferno", vmin=-80, vmax=-10)
plt.colorbar(label="Power (dB)")
plt.xlabel("Time (s)")
plt.ylabel("Frequency (Hz)")
plt.ylim(0, 2000)
plt.title("Spectrogram of a Simulated Guitar Chord (E major, open)")
plt.tight_layout()
plt.savefig("spectrogram_guitar.png", dpi=150)
plt.show()
```

In the spectrogram, you should see:
- Horizontal lines at each string's fundamental frequency, appearing left to right (the strum)
- Additional horizontal lines at the harmonics of each string (2x, 3x, 4x the fundamental)
- All lines fading from left to right as the strings decay
- The strum onset visible as a slight left-to-right stagger of the line starts

This is exactly what you'd see in a real guitar recording analyzed with REW, Audacity, or any DAW's spectral view.

### 6.4 Spectrogram Parameters and Their Effects

```python
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import spectrogram, chirp as scipy_chirp

# Generate a test signal with clear time and frequency features
fs = 44100
duration = 2.0
t = np.linspace(0, duration, int(fs * duration), endpoint=False)

# Three tones: one steady, one pulsed, one sweeping
signal = (0.5 * np.sin(2 * np.pi * 500 * t) +                       # Steady 500 Hz
          0.5 * np.sin(2 * np.pi * 1000 * t) * (np.sin(2 * np.pi * 2 * t) > 0) +  # 1000 Hz, pulsing at 2 Hz
          0.3 * np.sin(2 * np.pi * (200 + 600 * t / duration) * t))  # Sweep 200-800 Hz

fig, axes = plt.subplots(3, 1, figsize=(14, 12))
window_sizes = [256, 2048, 8192]
titles = [
    "Short window (256) -- good time resolution, poor frequency resolution",
    "Medium window (2048) -- balanced",
    "Long window (8192) -- poor time resolution, good frequency resolution"
]

for ax, nperseg, title in zip(axes, window_sizes, titles):
    f, t_spec, Sxx = spectrogram(signal, fs, nperseg=nperseg,
                                  noverlap=nperseg * 3 // 4, window="hann")
    ax.pcolormesh(t_spec, f, 10 * np.log10(Sxx + 1e-10), shading="gouraud",
                  cmap="magma", vmin=-60, vmax=-10)
    ax.set_ylabel("Frequency (Hz)")
    ax.set_ylim(0, 2000)
    ax.set_title(title)

axes[-1].set_xlabel("Time (s)")
plt.tight_layout()
plt.savefig("spectrogram_window_sizes.png", dpi=150)
plt.show()
```

Run this and study the three spectrograms carefully:
- **Short window (256)**: You can clearly see the 1000 Hz tone pulsing on and off. The pulsing is crisp in time. But the 500 Hz tone is a broad, blurry band. Frequency resolution is poor.
- **Medium window (2048)**: The 500 Hz tone is a sharp line. The pulsing is visible but the on/off transitions are slightly blurred. The sweep is clear. This is the sweet spot for most music analysis.
- **Long window (8192)**: The 500 Hz tone is razor-sharp. The sweep is a thin, precise line. But the pulsing of the 1000 Hz tone is smeared -- you can barely tell it's pulsing. Time resolution is poor.

This is the uncertainty principle in action, visualized.

### 6.5 Waterfall Plots

A **waterfall plot** is a 3D version of the spectrogram. Instead of encoding magnitude as color, it encodes it as height on a 3D surface. Waterfall plots are common in room acoustics analysis -- REW generates them to show how sound decays at different frequencies after an impulse (Chapter 37). Room modes show up as ridges that decay slowly compared to the rest of the spectrum.

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
from scipy.signal import spectrogram

# Simulate a room impulse response with a strong mode at 63 Hz
fs = 44100
duration = 0.5
t = np.linspace(0, duration, int(fs * duration), endpoint=False)

# Broadband impulse that decays quickly, plus a room mode that rings
impulse = np.random.randn(len(t)) * np.exp(-t * 30)         # Broadband, fast decay
room_mode = np.sin(2 * np.pi * 63 * t) * np.exp(-t * 5)     # 63 Hz, slow decay
room_mode2 = np.sin(2 * np.pi * 125 * t) * np.exp(-t * 8)   # 125 Hz, medium decay
signal = impulse + 3 * room_mode + 1.5 * room_mode2

f, t_spec, Sxx = spectrogram(signal, fs, nperseg=2048, noverlap=1536, window="hann")

# 3D waterfall plot
fig = plt.figure(figsize=(14, 8))
ax = fig.add_subplot(111, projection="3d")

# Downsample time axis for clearer wireframe
step = max(1, len(t_spec) // 40)
T, F = np.meshgrid(t_spec[::step], f)
Z = 10 * np.log10(Sxx[:, ::step] + 1e-10)

ax.plot_surface(T * 1000, F, Z, cmap="viridis", alpha=0.8,
                rstride=5, cstride=1, linewidth=0.1)
ax.set_xlabel("Time (ms)")
ax.set_ylabel("Frequency (Hz)")
ax.set_zlabel("Power (dB)")
ax.set_ylim(20, 300)
ax.set_title("Waterfall Plot -- Room Impulse Response with Modes at 63 Hz and 125 Hz")
ax.view_init(elev=25, azim=-60)
plt.tight_layout()
plt.savefig("waterfall_room_modes.png", dpi=150)
plt.show()
```

The ridges that persist long after the initial impulse has died away are room modes. In Chapter 37, you'll use REW to generate exactly this kind of measurement, and the waterfall plot will show you which frequencies your room traps or reinforces. The math underneath is the same STFT we just built.

---

## 7. Why This Matters for Everything in the Guide

The FFT isn't just a mathematical curiosity. It's the engine driving nearly every measurement, analysis, and processing tool in audio engineering. Here's how it connects to the rest of this guide:

### 7.1 Crossovers (Chapter 13)

A crossover splits a signal into frequency bands: low frequencies to the woofer, high frequencies to the tweeter. In the frequency domain, this is trivially clear -- a low-pass filter keeps the low-frequency FFT bins and attenuates the high ones. When you look at a crossover's frequency response plot, you're looking at the magnitude of its transfer function, which is computed via the FFT.

In Chapter 29, you'll design active crossovers with CamillaDSP. You'll verify the crossover behavior by measuring the output of each driver with a microphone, running an FFT on the measurement, and comparing the result to the target response. The FFT is how you *see* whether your crossover is working correctly.

### 7.2 EQ (Chapter 27)

Parametric EQ boosts or cuts specific frequency ranges. In the frequency domain, it's multiplication: the signal's spectrum multiplied by the filter's frequency response. A +6 dB boost at 3 kHz means multiplying the FFT bins around 3 kHz by a factor of 2. When REW auto-generates EQ corrections for your room, it's comparing the measured FFT to a target curve and computing the difference.

### 7.3 Distortion (Chapter 6)

When you clip a guitar signal in a gain stage (overdrive, distortion, fuzz), you add harmonics that weren't in the original signal. A clean 440 Hz sine wave through a hard-clipping distortion pedal comes out with energy at 880 Hz, 1320 Hz, 1760 Hz, and beyond. The FFT shows this clearly: run a clean signal through the distortion algorithm, take the FFT of the output, and you can *see* every harmonic the distortion created. This is how THD (Total Harmonic Distortion) is measured -- the FFT separates the fundamental from the harmonics, and you compute the ratio.

```python
import numpy as np
import matplotlib.pyplot as plt

fs = 44100
N = 8192
t = np.arange(N) / fs

# Clean sine wave
clean = np.sin(2 * np.pi * 440 * t)

# Hard-clipped version (simulating a distortion pedal)
clipped = np.clip(clean * 3.0, -1.0, 1.0)  # Gain of 3, clip at +/- 1

# FFT both
freqs = np.fft.fftfreq(N, 1/fs)
positive = freqs >= 0

X_clean = np.fft.fft(clean * np.hanning(N))
X_clipped = np.fft.fft(clipped * np.hanning(N))

fig, axes = plt.subplots(2, 1, figsize=(12, 8))

for ax, X, title, color in [
    (axes[0], X_clean, "Clean 440 Hz sine", "steelblue"),
    (axes[1], X_clipped, "Hard-clipped 440 Hz (distortion)", "coral")
]:
    mag_db = 20 * np.log10(np.abs(X[positive]) / np.max(np.abs(X[positive])) + 1e-10)
    ax.plot(freqs[positive], mag_db, color=color, linewidth=0.8)
    ax.set_xlim(0, 5000)
    ax.set_ylim(-80, 5)
    ax.set_ylabel("Magnitude (dB)")
    ax.set_title(title)
    ax.grid(True, alpha=0.3)
    
    # Mark harmonics
    for h in range(1, 11):
        ax.axvline(x=440 * h, color="gray", alpha=0.3, linestyle="--")

axes[-1].set_xlabel("Frequency (Hz)")
plt.tight_layout()
plt.savefig("distortion_harmonics.png", dpi=150)
plt.show()
```

The clean signal has one peak at 440 Hz. The clipped signal has peaks at 440, 880, 1320, 1760, 2200 Hz, and beyond -- every harmonic generated by the clipping. The odd harmonics (3rd, 5th, 7th) are stronger than the even harmonics, which is characteristic of symmetric hard clipping. Asymmetric clipping (like tube saturation) emphasizes even harmonics, which sounds "warmer." You can *see* the difference in the FFT, and that difference is what makes different distortion circuits sound different.

### 7.4 Room Modes (Chapter 36)

A rectangular room has standing waves at frequencies determined by its dimensions. A room that's 5 meters long has a primary axial mode at about 34 Hz (the speed of sound divided by twice the room length). These modes show up as peaks and dips in the room's frequency response, which you measure by playing a test signal, recording it with a calibrated microphone, and running an FFT.

REW's room measurement is literally: play a known signal (usually a logarithmic sine sweep), record the result, compute the FFT of the recording, divide by the FFT of the original signal, and the result is the room's transfer function. Every peak is a room mode or reflection reinforcement. Every dip is a cancellation. The FFT makes invisible acoustic phenomena visible and measurable.

### 7.5 Speaker Response (Chapter 15)

A speaker's frequency response -- the graph that shows how loud it plays at each frequency -- is an FFT magnitude plot. You play a test signal into the speaker, record the output with a microphone at a specific distance and angle, compute the FFT, and normalize it. The result tells you everything about the speaker's performance: where its response is flat, where it rolls off, where resonances create peaks, where the crossover causes a dip.

Every speaker datasheet graph you've ever seen is the output of an FFT. Understanding what the FFT actually computes helps you read those graphs with real comprehension instead of just pattern matching.

### 7.6 FFT-Based Convolution (Chapter 27)

Here's one that brings everything full circle. In Chapter 27, you learned that FIR convolution with long impulse responses (room correction filters with 65,536 taps) would be impossibly expensive as direct computation. CamillaDSP solves this using **FFT-based convolution**: instead of convolving in the time domain (which is O(N * M) where M is the filter length), it transforms both the signal and the filter to the frequency domain with FFT, multiplies them element-by-element, and transforms back with an inverse FFT. The total cost is O(N log N) -- fast enough for real-time.

This works because of the **convolution theorem**: convolution in the time domain equals multiplication in the frequency domain. The FFT makes the domain transformation cheap, turning an expensive O(N * M) convolution into a cheap O(N log N) multiplication. This single insight is why real-time room correction is possible on a Raspberry Pi.

---

## 8. Build: Real-Time Spectrum Analyzer

Now we put everything together. We'll build a real-time spectrum analyzer in Python that captures audio from your microphone, computes the FFT, and displays the result as a live updating plot. You can play guitar into it and see your signal's frequency content in real time.

### 8.1 Dependencies

```bash
pip install numpy sounddevice matplotlib
```

- `numpy`: FFT computation
- `sounddevice`: Cross-platform audio input from your microphone
- `matplotlib`: Display (we'll use its animation framework for real-time updates)

### 8.2 The Core Code

```python
"""
Real-Time Spectrum Analyzer
----------------------------
Captures audio from your default input device, runs an FFT on each block,
and displays the magnitude spectrum as a live updating plot.

Play guitar, sing, clap, or play music through speakers -- 
watch the frequency content change in real time.

Usage: python spectrum_analyzer.py
Press Ctrl+C to stop.
"""

import numpy as np
import sounddevice as sd
import matplotlib.pyplot as plt
import matplotlib.animation as animation
import sys

# ============================================================
# Configuration -- tweak these to change behavior
# ============================================================
SAMPLE_RATE = 44100          # Hz -- match your audio interface
FFT_SIZE = 4096              # Samples per FFT block (power of 2)
HOP_SIZE = 2048              # How often to update (samples between frames)
FREQ_MIN = 20                # Hz -- lowest frequency to display
FREQ_MAX = 20000             # Hz -- highest frequency to display
DB_MIN = -80                 # dB -- bottom of the display range
DB_MAX = 0                   # dB -- top of the display range
DEVICE = None                # None = default input device
# ============================================================

# Compute frequency axis
freqs = np.fft.rfftfreq(FFT_SIZE, 1.0 / SAMPLE_RATE)
freq_mask = (freqs >= FREQ_MIN) & (freqs <= FREQ_MAX)
freqs_display = freqs[freq_mask]

# Window function
window = np.hanning(FFT_SIZE)

# Shared buffer for audio data (ring buffer approach)
audio_buffer = np.zeros(FFT_SIZE, dtype=np.float32)


def audio_callback(indata, frames, time_info, status):
    """Called by sounddevice for each audio block."""
    global audio_buffer
    if status:
        print(f"Audio status: {status}", file=sys.stderr)
    # Shift old data left and append new data
    mono = indata[:, 0]  # Take first channel if stereo
    if len(mono) >= FFT_SIZE:
        audio_buffer[:] = mono[-FFT_SIZE:]
    else:
        audio_buffer = np.roll(audio_buffer, -len(mono))
        audio_buffer[-len(mono):] = mono


def compute_spectrum():
    """Compute the FFT magnitude spectrum of the current buffer."""
    windowed = audio_buffer * window
    spectrum = np.fft.rfft(windowed)
    magnitude = np.abs(spectrum)
    
    # Normalize and convert to dB
    magnitude = magnitude / (FFT_SIZE / 2)  # Scale to amplitude
    magnitude_db = 20 * np.log10(magnitude + 1e-10)
    
    return magnitude_db[freq_mask]


# ============================================================
# Set up the plot
# ============================================================
fig, ax = plt.subplots(figsize=(14, 6))
fig.patch.set_facecolor("#1a1a2e")
ax.set_facecolor("#16213e")

# Create the line plot
(line,) = ax.plot(freqs_display, np.full(len(freqs_display), DB_MIN),
                  color="#00d4ff", linewidth=1.0, alpha=0.9)

# Fill under the curve
fill = ax.fill_between(freqs_display, DB_MIN,
                        np.full(len(freqs_display), DB_MIN),
                        color="#00d4ff", alpha=0.15)

ax.set_xscale("log")
ax.set_xlim(FREQ_MIN, FREQ_MAX)
ax.set_ylim(DB_MIN, DB_MAX)
ax.set_xlabel("Frequency (Hz)", color="white", fontsize=12)
ax.set_ylabel("Magnitude (dB)", color="white", fontsize=12)
ax.set_title("Real-Time Spectrum Analyzer", color="white", fontsize=14,
             fontweight="bold")
ax.tick_params(colors="white")
ax.grid(True, which="both", alpha=0.2, color="white")

# Mark musical reference frequencies
reference_freqs = {
    "E2": 82.4, "A2": 110, "E3": 164.8, "A3": 220,
    "E4": 329.6, "A4": 440, "E5": 659.3, "A5": 880,
    "1k": 1000, "5k": 5000, "10k": 10000
}
for name, f in reference_freqs.items():
    if FREQ_MIN <= f <= FREQ_MAX:
        ax.axvline(x=f, color="white", alpha=0.1, linewidth=0.5)
        ax.text(f, DB_MAX - 2, name, color="white", alpha=0.4,
                fontsize=7, ha="center", va="top")


# Peak hold
peak_hold = np.full(len(freqs_display), DB_MIN)
peak_decay_rate = 0.3  # dB per frame

(peak_line,) = ax.plot(freqs_display, peak_hold,
                        color="#ff6b6b", linewidth=0.5, alpha=0.6)


def update(frame):
    """Update function called by matplotlib animation."""
    global fill, peak_hold
    
    spectrum_db = compute_spectrum()
    
    # Update main line
    line.set_ydata(spectrum_db)
    
    # Update fill
    fill.remove()
    fill = ax.fill_between(freqs_display, DB_MIN, spectrum_db,
                            color="#00d4ff", alpha=0.15)
    
    # Update peak hold (slow decay)
    peak_hold = np.maximum(spectrum_db, peak_hold - peak_decay_rate)
    peak_line.set_ydata(peak_hold)
    
    return line, peak_line


# ============================================================
# Start audio stream and animation
# ============================================================
print(f"Starting spectrum analyzer...")
print(f"  Sample rate: {SAMPLE_RATE} Hz")
print(f"  FFT size: {FFT_SIZE} ({FFT_SIZE/SAMPLE_RATE*1000:.1f} ms window)")
print(f"  Frequency resolution: {SAMPLE_RATE/FFT_SIZE:.1f} Hz")
print(f"  Display range: {FREQ_MIN}-{FREQ_MAX} Hz, {DB_MIN} to {DB_MAX} dB")
print(f"Press Ctrl+C to stop.")

try:
    stream = sd.InputStream(
        samplerate=SAMPLE_RATE,
        blocksize=HOP_SIZE,
        channels=1,
        dtype="float32",
        callback=audio_callback,
        device=DEVICE,
    )
    stream.start()
    
    ani = animation.FuncAnimation(fig, update, interval=30, blit=False,
                                   cache_frame_data=False)
    plt.tight_layout()
    plt.show()
    
except KeyboardInterrupt:
    print("\nStopped.")
except Exception as e:
    print(f"Error: {e}")
    print("\nTroubleshooting:")
    print("  - Check that a microphone is connected")
    print("  - Run: python -c \"import sounddevice; print(sounddevice.query_devices())\"")
    print("  - Try setting DEVICE to a specific device index")
finally:
    if "stream" in dir():
        stream.stop()
        stream.close()
```

### 8.3 Running It

Save the code as `spectrum_analyzer.py` and run it:

```bash
python spectrum_analyzer.py
```

You should see a window with a logarithmic frequency axis from 20 Hz to 20 kHz, updating in real time. The blue line shows the current spectrum, the red line is peak hold (slowly decays, showing the maximum level each frequency has reached).

Now play guitar into it:
- Strum an open E chord -- you'll see peaks at 82 Hz (low E), 110 Hz (A), 164 Hz (E), etc., plus their harmonics
- Play a single note and bend it -- watch the peak slide up in frequency
- Hit the strings hard vs. soft -- watch the high-frequency content change (harder pick attack = more treble)
- Tap a harmonic at the 12th fret -- see a single peak with very few harmonics (almost a pure sine wave)

This is exactly what commercial tools like REW, Smaart, and hardware spectrum analyzers do. The only differences are calibration (REW compensates for your microphone's frequency response), averaging (REW averages multiple measurements for stability), and professional UI polish.

### 8.4 Extending the Analyzer

Here are some modifications you can make to enhance the tool:

**Octave banding**: Instead of showing every FFT bin, group bins into octave or 1/3-octave bands. This gives a smoother, more readable display similar to a 31-band graphic EQ.

```python
def octave_bands(spectrum_db, freqs, bands_per_octave=3):
    """Group FFT bins into fractional-octave bands."""
    # Standard 1/3-octave center frequencies
    f_center = 1000 * 2 ** (np.arange(-20, 14) / bands_per_octave)
    f_center = f_center[(f_center >= FREQ_MIN) & (f_center <= FREQ_MAX)]
    
    band_levels = []
    for fc in f_center:
        f_low = fc / 2 ** (1 / (2 * bands_per_octave))
        f_high = fc * 2 ** (1 / (2 * bands_per_octave))
        mask = (freqs >= f_low) & (freqs < f_high)
        if np.any(mask):
            # RMS average of bins in this band (in linear domain, then back to dB)
            linear = 10 ** (spectrum_db[mask] / 20)
            rms = np.sqrt(np.mean(linear ** 2))
            band_levels.append(20 * np.log10(rms + 1e-10))
        else:
            band_levels.append(DB_MIN)
    
    return f_center, np.array(band_levels)
```

**A-weighting**: Human hearing isn't flat -- we're less sensitive to low frequencies and very high frequencies. A-weighting applies a correction curve that approximates human perception, so the display shows "perceived loudness" rather than raw amplitude. This is the standard weighting for noise measurements (dBA).

```python
def a_weight(freqs):
    """Compute A-weighting correction in dB for given frequencies."""
    f2 = freqs ** 2
    # A-weighting transfer function (IEC 61672)
    numerator = 12194 ** 2 * f2 ** 2
    denominator = ((f2 + 20.6 ** 2) *
                   np.sqrt((f2 + 107.7 ** 2) * (f2 + 737.9 ** 2)) *
                   (f2 + 12194 ** 2))
    a_weight_db = 20 * np.log10(numerator / (denominator + 1e-10) + 1e-10)
    # Normalize so 1 kHz = 0 dB
    ref = 20 * np.log10(12194 ** 2 * 1000 ** 4 /
                         ((1000 ** 2 + 20.6 ** 2) *
                          np.sqrt((1000 ** 2 + 107.7 ** 2) * (1000 ** 2 + 737.9 ** 2)) *
                          (1000 ** 2 + 12194 ** 2)) + 1e-10)
    return a_weight_db - ref
```

**Smoothing**: For a cleaner display, average several consecutive FFT frames:

```python
# Keep a rolling average
NUM_AVERAGES = 4
spectrum_history = []

def compute_averaged_spectrum():
    spectrum_db = compute_spectrum()
    spectrum_history.append(spectrum_db)
    if len(spectrum_history) > NUM_AVERAGES:
        spectrum_history.pop(0)
    return np.mean(spectrum_history, axis=0)
```

### 8.5 Comparing to REW

Your Python spectrum analyzer and REW are doing fundamentally the same thing: FFT of the audio input, magnitude displayed on a dB scale with a logarithmic frequency axis. REW adds:

- **Calibrated microphone compensation**: REW loads a calibration file for your specific measurement microphone and subtracts its frequency response, giving you a flat reference
- **Gating**: REW can window out room reflections to isolate the direct sound from the speaker (anechoic approximation)
- **Smoothing options**: 1/48, 1/24, 1/12, 1/6, 1/3 octave smoothing
- **Target curve overlay**: Compare your measurement to a target response
- **Export**: Save measurements for later comparison or import into CamillaDSP

But the core engine is the same FFT you just built. When you measure your speakers with REW in Chapter 29, you'll know exactly what's happening under the hood.

---

## 9. Pulling It All Together

### 9.1 The Fourier Transform Is a Lens

Think of the Fourier transform as a lens that lets you see sound differently. In the time domain, you see amplitude changing over time -- useful for understanding dynamics, transients, and waveform shape. In the frequency domain, you see how much energy is at each frequency -- useful for understanding tone, harmonics, resonances, and the behavior of filters and rooms.

Neither view is "more real" than the other. They're two representations of the same signal, each revealing different information. The FFT converts between them, and the inverse FFT converts back. No information is lost in either direction.

### 9.2 The Key Equations to Remember

Here are the equations from this chapter that you'll use repeatedly:

**Bin width (frequency resolution)**:
```
delta_f = fs / N
```

**Frequency of bin k**:
```
f_k = k * fs / N
```

**Number of bins for a given frequency range**:
```
bins = freq_range / (fs / N)
```

**Window duration (time resolution)**:
```
delta_t = N / fs
```

**Uncertainty principle**:
```
delta_t * delta_f >= 1
```

### 9.3 What's Next

Chapter 27b continues with more audio programming projects, building on the FFT foundation you've learned here. You'll write audio effects in Python, implement your own filters, and build tools that process real audio files. Chapter 29 puts all of this into practice: you'll measure your speakers with a sweep signal, use FFT to compute the frequency response, and feed the results into CamillaDSP for room correction.

The path from here to a fully corrected, optimized playback system is: measure (FFT) -> analyze (spectral analysis) -> correct (DSP filters) -> verify (FFT again). It's FFTs all the way down.

---

## Summary

The Fourier transform decomposes any signal into a sum of sine waves at different frequencies and amplitudes. The DFT is the discrete version for sampled signals. The FFT computes the DFT in O(N log N) instead of O(N^2), making real-time spectral analysis practical.

The FFT output is a set of frequency bins, each representing a narrow frequency band. The bin width (fs/N) determines your frequency resolution, and there's an unavoidable trade-off between frequency resolution and time resolution. Windowing (Hann, Blackman, etc.) eliminates spectral leakage caused by the FFT's assumption that the signal is periodic.

The spectrogram combines multiple FFTs to show how frequency content evolves over time -- it's the practical tool for analyzing music, speech, and any time-varying signal. Waterfall plots are the 3D version used in room acoustics.

Every measurement tool in this guide -- REW, CamillaDSP, Audacity's spectrum view, hardware analyzers -- is built on the FFT. Understanding how it works transforms these tools from black boxes into transparent instruments. When REW shows you a frequency response plot, you know exactly what it computed, what the limitations are, and what the numbers mean.

The real-time spectrum analyzer you built in Section 8 is the bridge between theory and practice. Play guitar into it. Listen to music through your speakers and watch the spectrum dance. See how a chord resolves, how a pick attack splashes high-frequency energy across the spectrum, how a bass note fills the low-frequency bins. This is the math of sound, made visible.

---

## Exercises

1. **Frequency resolution experiment**: Generate two sine waves at 440 Hz and 445 Hz. Try different FFT sizes (256, 1024, 4096, 16384) and find the smallest FFT size that can clearly resolve both peaks as separate. Verify this matches the bin width formula.

2. **Window function shootout**: Generate a signal with a strong 1 kHz sine wave and a weak 1.1 kHz sine wave at -40 dB relative to the strong one. Compare rectangular, Hann, and Blackman windows. Which window lets you see the weak tone? What happens if the weak tone is at -60 dB?

3. **Distortion spectrum**: Implement soft clipping (y = tanh(gain * x)) instead of hard clipping. Compare the harmonic spectrum to hard clipping (np.clip). Observe how soft clipping produces a different harmonic distribution. Relate this to why tube amps and solid-state amps sound different when driven into distortion.

4. **Guitar string analysis**: Record yourself playing a single guitar string (use Audacity or sounddevice). Compute the FFT and identify the fundamental and harmonics. How many harmonics can you see above the noise floor? How does the harmonic content change between the pick attack and the sustained note? (Hint: use a spectrogram.)

5. **Room mode finder**: Record a hand clap in your room using your microphone. Compute the FFT of the decay tail (skip the initial transient). Identify peaks that correspond to room modes. Measure your room dimensions and calculate the theoretical axial modes: f = c / (2 * L) where c = 343 m/s and L is the room dimension. Do the measured modes match the predicted ones?

6. **Build a tuner**: Modify the spectrum analyzer to find the peak frequency and display the nearest musical note name and how many cents sharp or flat it is. The formula for note number from frequency is: n = 12 * log2(f / 440) + 49, where n=49 is A4. Test it with your guitar.
