<!--
  CHAPTER: 25
  TITLE: Digital Audio Fundamentals
  PART: 4 — Digital Audio & The Pi Streamer
  PREREQS: Part 0
  KEY_TOPICS: sampling, quantization, aliasing, Nyquist-Shannon, bit depth, dynamic range, DAC architectures, delta-sigma, R2R, jitter, I2S, S/PDIF, USB Audio, AES/EBU, PCM, DSD, lossy compression, loudness war
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 25: Digital Audio Fundamentals

> **Part 4 — Digital Audio & The Pi Streamer** | Prerequisites: Part 0 | Difficulty: Intermediate

You've spent the first three parts of this guide in the analog world. You've wound inductors, soldered op-amps, and pushed air with speaker cones. Every signal has been a continuous voltage -- smoothly varying, infinitely divisible, existing in the real physical world of electrons and magnetic fields. Now we're going to do something that would have seemed absurd to the engineers who designed the first audio amplifiers: we're going to chop that beautiful continuous signal into a stream of numbers, move those numbers around, do math on them, and then reconstruct a continuous signal on the other end. And it's going to sound identical -- or, depending on your implementation, *better* than the original.

Digital audio is not a compromise. It's not a lesser version of analog. It's a mathematically rigorous transformation that, when done correctly, preserves the original signal with provable perfection. The key phrase there is "when done correctly." Understanding *what* correct means -- what the theory guarantees, where the real-world pitfalls hide, and how different hardware architectures trade off against each other -- is what separates someone who buys a DAC based on marketing from someone who builds a system based on engineering.

This chapter is the theoretical backbone of everything we'll build in Part 4. By the end of it, you'll understand exactly what happens when an analog signal becomes digital, what happens when it comes back out, and why most of the arguments on audio forums are about things that don't matter.

### In This Chapter
- Sampling: taking snapshots of analog signals at regular intervals
- Quantization: mapping each snapshot to the nearest digital value
- Aliasing and the Nyquist-Shannon sampling theorem
- Bit depth, dynamic range, and the noise floor
- DAC architectures: delta-sigma vs R2R
- Jitter: timing errors and their audible consequences
- Digital audio interfaces: I2S, S/PDIF, USB Audio, AES/EBU
- Audio formats: PCM, DSD, lossy compression
- The loudness war and dynamic range destruction

### Related Chapters
- [Ch 0: Electricity Through the Lens of Sound](../part-0-electronics/00-electricity-through-sound.md) -- the analog signals we're about to digitize
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) -- the passive components in analog filter stages of DACs
- [Ch 26: Streaming Architectures and Protocols](26-streaming-architectures-protocols.md) -- how digital audio moves across networks
- [Ch 27: DSP Fundamentals for Audio](27-dsp-fundamentals.md) -- what you can do with audio once it's digital
- [Ch 28: Build: Pi Streamer with Physical Controls](28-build-pi-streamer.md) -- putting this theory into practice

---

## 1. Sampling: Snapshots of a Continuous World

### 1.1 What Sampling Does

An analog audio signal is a continuously varying voltage. At any instant, it has a precise value. Between any two instants, it passes through an infinite number of intermediate values. A digital system can't store infinity, so it does something clever: it takes measurements at regular intervals and stores only those measurements.

Each measurement is called a **sample**. The number of samples taken per second is the **sample rate**, measured in hertz (Hz) or more commonly kilohertz (kHz). A CD uses 44,100 samples per second -- 44.1 kHz. Professional audio typically uses 48 kHz. "High-resolution" audio goes to 96 kHz or 192 kHz.

Think of it like this: if you filmed a spinning wheel at 30 frames per second, you'd capture its motion adequately as long as it wasn't spinning too fast. If it spins faster than your frame rate can capture, you get artifacts -- the wheel appears to spin backward or freeze. Audio sampling works the same way, and the artifacts are just as real (and just as avoidable).

### 1.2 The ADC: Analog-to-Digital Converter

The hardware that performs sampling is the **ADC** (analog-to-digital converter). An ADC takes an analog voltage at its input, measures it at precise intervals determined by a clock signal, and outputs a stream of digital numbers representing those measurements.

The quality of an ADC depends on:

- **How precisely it measures** (resolution, determined by bit depth)
- **How regularly it measures** (clock accuracy, related to jitter -- Section 5)
- **How cleanly it handles the analog input** (noise floor, distortion)
- **How well it prevents aliasing** (anti-aliasing filter design -- next section)

In a recording chain, the ADC is the last analog component and the first digital one. It's the bridge. In a playback chain (which is what we're building with the Pi streamer), the DAC is the bridge going the other way.

### 1.3 Sample Rate: How Fast is Fast Enough?

This brings us to the most important theorem in digital audio.

---

## 2. The Nyquist-Shannon Sampling Theorem

### 2.1 The Theorem

In 1949, Claude Shannon proved something that underpins all of digital audio (and digital communications, and radar, and basically the entire modern world):

> **A bandlimited signal can be perfectly reconstructed from its samples if the sampling rate is at least twice the highest frequency present in the signal.**

Read that again. *Perfectly reconstructed*. Not approximately. Not "pretty close." Mathematically, provably, identically reconstructed. The reconstructed signal is not a staircase of discrete steps -- it's a smooth, continuous waveform identical to the original.

The minimum sampling rate is called the **Nyquist rate**: twice the highest frequency. The highest frequency that can be represented at a given sample rate is called the **Nyquist frequency**: half the sample rate.

| Sample Rate | Nyquist Frequency | Can Represent |
|---|---|---|
| 44.1 kHz | 22.05 kHz | Full audible range (human hearing tops out ~20 kHz) |
| 48 kHz | 24 kHz | Full audible range + small margin |
| 96 kHz | 48 kHz | Well beyond audible range |
| 192 kHz | 96 kHz | Way beyond audible range |
| 384 kHz | 192 kHz | Absurdly beyond audible range |

### 2.2 Why CD is 44.1 kHz

Human hearing extends from roughly 20 Hz to 20 kHz (and that upper limit drops with age -- if you're over 30, you've probably lost everything above 16-17 kHz). To capture the full audible spectrum, you need a sample rate of at least 40 kHz.

CD's 44.1 kHz was chosen for a specific practical reason: it was derived from video equipment used in early digital recording (the Sony PCM-1600 used a modified video recorder, and 44.1 kHz fit neatly into the video frame structure). The extra 4.1 kHz above the minimum 40 kHz gives the anti-aliasing filter room to roll off -- a practical necessity we'll discuss in a moment.

Professional audio standardized on 48 kHz because it divides evenly into common video frame rates (24, 25, 30 fps), making it cleaner for film and broadcast work.

### 2.3 Does Higher Sample Rate Sound Better?

For playback: **no**. 44.1 kHz captures everything you can hear. 48 kHz gives a tiny bit more filter headroom. Going to 96 kHz or 192 kHz for playback is, from a Shannon perspective, recording frequencies that no human ear can detect.

For recording and production: **there are arguments**. Higher sample rates give you more headroom for processing. Non-linear operations (like saturation plugins) can generate harmonics above the Nyquist frequency, and working at a higher sample rate prevents those from aliasing back into the audible range. Some engineers also argue that the anti-aliasing filter can be gentler at higher sample rates, reducing phase artifacts in the audible band.

For your Pi streamer playback system: 44.1 kHz and 48 kHz are all you need. If your source files are 96 kHz or 192 kHz, you'll play them fine, but the extra bandwidth isn't buying you audible improvement. Focus your budget on DAC quality, not sample rate bragging rights.

### 2.4 What Happens Below the Nyquist Rate: Aliasing

Here's where things go wrong. If you sample a signal that contains frequencies above the Nyquist frequency, those frequencies don't just disappear -- they fold back into the audible range as **aliases**. A 23 kHz tone sampled at 44.1 kHz would alias to 21.1 kHz (44.1 - 23 = 21.1). A 30 kHz tone would alias to 14.1 kHz -- right in the middle of the audible range, sounding like a harsh, inharmonic tone that wasn't in the original signal.

This is why every ADC has an **anti-aliasing filter**: a low-pass filter placed before the sampling stage that removes everything above the Nyquist frequency. On a DAC (the playback side), a corresponding **reconstruction filter** (also called an anti-imaging filter) removes the high-frequency artifacts created by the digital-to-analog process.

The quality of these filters matters. An ideal filter would be a perfect "brick wall" -- passing everything below Nyquist and blocking everything above with infinite steepness. Real filters can't do this (they need some transition bandwidth to roll off), which is why the sample rate is set slightly above twice 20 kHz, giving the filter room to work.

> **What happens if...** your ADC's anti-aliasing filter is poorly designed? You get aliasing artifacts -- phantom tones that weren't in the original signal. These sound harsh, metallic, and wrong. Early digital audio (1980s) suffered from this, which gave digital audio its reputation for sounding "cold" and "harsh." Modern ADCs with well-designed filters have solved this problem completely. If someone tells you "digital sounds harsh," they're either listening to 1985 hardware or they've convinced themselves of something that doesn't survive a blind test.

### 2.5 Reconstruction: How Samples Become Continuous Again

There's a common misconception that digital audio is a "staircase" -- that the DAC outputs a stepped waveform where each sample is a flat plateau at the sample's voltage. This is wrong, and understanding why is key to understanding why digital audio works so well.

The Shannon reconstruction theorem says that a bandlimited signal is *perfectly* reconstructed from its samples by a process called **sinc interpolation**. Each sample point generates a sinc function (sin(x)/x), and the sum of all these sinc functions produces a smooth, continuous waveform identical to the original.

In practice, a DAC approximates this using a reconstruction filter. A delta-sigma DAC (Section 4) oversamples the signal and uses a simple analog low-pass filter for reconstruction. The result is a smooth, continuous analog waveform -- not a staircase.

If you put an oscilloscope on the output of a modern DAC playing a 1 kHz sine wave, you'll see a smooth sine wave. Not steps. Not staircase artifacts. A perfect sine.

### 2.6 The Staircase Myth and Why It Persists

YouTube videos and even some textbooks show digital audio as a staircase with steps at each sample point. This visualization is a **zero-order hold** -- the simplest possible DAC implementation, which holds each sample value until the next sample arrives. It's how a *naive* DAC would work if you had no reconstruction filter.

No real DAC works this way. The zero-order hold creates imaging artifacts (copies of the signal at multiples of the sample rate) that the reconstruction filter removes. What comes out the other end is the smooth original waveform.

Why does the staircase myth persist? Because it's visually intuitive. It's easy to draw. And it supports the narrative that digital audio is inherently inferior to analog's "smooth" waveforms. But it's wrong. Digital audio, when reconstructed properly, *is* smooth. The math proves it. The measurements confirm it.

---

## 3. Quantization: Mapping to Numbers

### 3.1 What Quantization Does

Sampling tells us *when* to measure. Quantization determines *how precisely* we can represent each measurement. When the ADC takes a sample, it maps the measured voltage to the nearest value in a fixed grid of possible values. The number of possible values is determined by the **bit depth**.

- **16-bit**: 2^16 = 65,536 possible values
- **24-bit**: 2^24 = 16,777,216 possible values
- **32-bit float**: enormous range (more on this in a moment)

Think of it like rounding. If you can only represent numbers to the nearest integer, the value 3.7 becomes 4, and the value 3.2 becomes 3. The difference between the true value and the rounded value is called **quantization error**. When applied to audio, quantization error manifests as **quantization noise** -- a low-level noise added to the signal by the rounding process.

### 3.2 Bit Depth and Dynamic Range

Each additional bit of depth doubles the number of possible values, which adds approximately 6 dB of dynamic range:

| Bit Depth | Dynamic Range | Practical Meaning |
|---|---|---|
| 8-bit | 48 dB | Telephone quality -- audible noise floor |
| 16-bit | 96 dB | CD quality -- noise floor below audibility in any real listening environment |
| 24-bit | 144 dB | Studio quality -- noise floor below the thermal noise of any real microphone or analog circuit |
| 32-bit float | ~1528 dB | Effectively infinite -- used for processing headroom, not for representing real signals |

Let's put 96 dB in perspective. A quiet living room has an ambient noise floor of about 30 dB SPL. The threshold of pain is about 130 dB SPL. That's a 100 dB range. Sixteen bits gives you 96 dB of range, which means CD-quality audio has enough dynamic range to go from "quieter than your room" to "painfully loud" with room to spare. Nobody is listening in a room quiet enough to hear the 16-bit noise floor.

Twenty-four bits gives 144 dB -- more dynamic range than the entire span from the threshold of hearing (0 dB SPL) to the threshold of pain (130 dB SPL), with 14 dB left over. No physical microphone has a noise floor low enough to use all 24 bits of dynamic range. The extra depth is useful for recording (headroom for levels, room for processing) but doesn't improve playback quality.

### 3.3 Dither: Making Quantization Noise Behave

Here's a subtle but important concept. Without dither, quantization error is correlated with the signal -- it's not random noise, it's *distortion*. On very quiet signals (where only the lowest few bits are active), this distortion becomes audible as harsh, granular artifacts.

**Dither** is the deliberate addition of a tiny amount of random noise before quantization. It sounds counterintuitive -- adding noise to improve quality? -- but it works because the random noise decorrelates the quantization error from the signal, transforming correlated distortion into uncorrelated noise. The result is a slightly higher noise floor but one that sounds natural (like gentle hiss) rather than artifactual.

When you truncate from 24-bit to 16-bit for CD distribution, applying dither (specifically **TPDF dither** -- triangular probability density function) is essential. Without it, quiet passages can exhibit audible distortion. With it, you get a clean 96 dB of dynamic range with a benign noise floor.

> **What happens if...** you don't dither when truncating from 24-bit to 16-bit? Quiet passages -- the fade-out at the end of a song, a gentle acoustic guitar intro -- will have a grainy, harsh quality. The quantization distortion is most audible exactly where the music is quietest and your ear is most sensitive. This was a real problem in early CD mastering before engineers understood the importance of dither.

### 3.4 32-Bit Float: The Processing Format

You'll encounter 32-bit floating point audio in DAWs and DSP pipelines (including CamillaDSP, which we'll use in Chapter 29). Floating point represents numbers as a mantissa and exponent (like scientific notation), which gives enormous dynamic range. You essentially can't clip a 32-bit float signal internally -- the format can represent values far beyond the range of any physical system.

This makes it ideal for processing: you can apply gain, EQ, dynamics processing, and mixing without ever worrying about internal overflow. The conversion to a fixed-point format (16-bit or 24-bit) for output is where you set your final level and apply dither if needed.

---

## 4. DAC Architectures: Turning Numbers Back into Sound

### 4.1 The DAC's Job

The **DAC** (digital-to-analog converter) does the reverse of the ADC: it takes a stream of digital numbers and reconstructs a continuous analog voltage. This is the component that matters most in your Pi streamer, because it's the last thing the signal touches before it enters your analog amplification chain.

A DAC needs to do three things well:

1. **Accurately convert each digital value to the correct analog voltage** (linearity)
2. **Do so at precisely the right time** (low jitter -- Section 5)
3. **Output a clean analog signal** (low noise, low distortion, good reconstruction filter)

There are two fundamentally different approaches to step 1, and the debate between them is one of the more interesting conversations in audio engineering.

### 4.2 Delta-Sigma (ΔΣ) DACs

**How they work**: A delta-sigma DAC doesn't directly convert an N-bit value to a voltage using N-bit-precise components. Instead, it oversamples the signal to a much higher rate (often 128x or 256x the original sample rate), reduces the bit depth to just a few bits (sometimes 1 bit), and uses a high-speed switching circuit to produce a pulse stream. A simple analog low-pass filter then smooths this pulse stream into a continuous analog signal.

The clever part is **noise shaping**: the quantization noise from reducing to a few bits is mathematically shifted ("shaped") out of the audible band and into the ultrasonic range, where the output filter removes it. The result is a high-precision analog signal derived from a very simple (and therefore very accurate) digital-to-analog element.

**Common delta-sigma DAC chips you'll encounter**:

| Chip | Manufacturer | Used In | Notes |
|---|---|---|---|
| PCM5122 | Texas Instruments | HiFiBerry DAC+ Pro, many Pi HATs | Excellent price/performance, 112 dB SNR |
| ES9038PRO | ESS Technology | High-end standalone DACs | "SABRE" series, 140 dB DNR claimed |
| ES9038Q2M | ESS Technology | Topping, SMSL budget DACs | Mobile version of ES9038, still excellent |
| AK4499EX | AKM (Asahi Kasei) | Premium DACs | Current-output, very low distortion |
| CS43198 | Cirrus Logic | Apple dongles, some DAPs | Low power, great performance |
| PCM1794A | Texas Instruments | Pro audio, Benchmark DAC3 | Older but excellent, used in many studio DACs |

**Advantages of delta-sigma**:
- Extremely low measured distortion (THD+N numbers that would have been unimaginable 20 years ago)
- Excellent linearity across the entire dynamic range
- The simple output stage is cheap and reliable
- Modern implementations are phenomenally good
- Very well-understood and mature technology

**Disadvantages**:
- The oversampling process requires a digital filter, which introduces pre-ringing on transients (the filter "rings" before an impulse, which doesn't happen in nature). Whether this is audible is debated.
- The digital filter's characteristics affect the sound -- many modern DACs offer selectable filters (sharp, slow, minimum phase, apodizing) to let you choose your tradeoff.

### 4.3 R2R / Multibit DACs

**How they work**: An R2R DAC uses a precision resistor ladder network to directly convert each bit of the digital word into a proportional current or voltage. Each bit controls a switch connected to a resistor. The resistors are weighted in a binary ratio (R, 2R, 4R, 8R...) so that each bit contributes its correct proportion to the output voltage.

It's a conceptually simple and elegant approach: the digital code directly maps to an analog voltage through Ohm's law and Kirchhoff's current law -- the fundamentals from Chapter 0.

**The catch**: every resistor needs to be precise. For a 16-bit DAC, the most significant bit's resistor and the least significant bit's resistor need to maintain a ratio accuracy of 1 in 65,536 (about 0.0015%). For 24 bits, that ratio is 1 in 16,777,216. This is extremely difficult to achieve in manufacturing, which is why true high-bit-count R2R DACs are expensive.

**Companies making R2R DACs**:
- **Schiit** (Yggdrasil, Bifrost 2, Gungnir): American manufacturer, one of the most visible R2R advocates
- **Denafrips** (Ares, Pontus, Venus, Terminator): Chinese manufacturer, well-regarded R2R lineup
- **Holo Audio** (Spring, May): Also Chinese, uses proprietary R2R modules
- **MSB Technology**: Ultra-high-end, discrete R2R modules, five-figure prices

**Advantages of R2R**:
- No oversampling digital filter means no pre-ringing (this is the main argument R2R proponents make)
- Some listeners describe the sound as more "natural," "analog," or "organic"
- Each sample is independently converted -- no dependency on previous or future samples

**Disadvantages**:
- Manufacturing precision is difficult and expensive
- Measured performance (THD+N, SINAD) is typically worse than good delta-sigma designs
- Component aging can affect accuracy over time
- Genuinely high-resolution R2R (beyond 16-bit precision) is very hard to achieve

### 4.4 The "Sound Character" Debate

Here's where engineering meets opinion. On measurements, modern delta-sigma DACs objectively outperform R2R DACs. An ES9038PRO measures better on every metric (SINAD, THD+N, dynamic range) than any R2R DAC at any price.

But some experienced listeners prefer the R2R sound. They describe it as more natural on transients, more relaxed, more "analog." Delta-sigma proponents argue this is confirmation bias -- that sighted listening tests are unreliable and that the measured differences between a good delta-sigma and a good R2R are below the threshold of audibility.

My take: for your Pi streamer build, a delta-sigma DAC HAT like the HiFiBerry DAC2 Pro (which uses the PCM5142) is the right choice. It measures excellently, costs $45, plugs directly into the Pi's GPIO header via I2S, and sounds indistinguishable from DACs costing 10x more in level-matched blind tests. Save the R2R vs delta-sigma soul-searching for when you're building a dedicated standalone DAC -- and even then, let your ears and your measurements be the guides, not forum arguments.

> **What happens if...** you spend $2,000 on an R2R DAC when a $45 DAC HAT measures identically within the audible band? You get a nicer-looking piece of equipment on your shelf and the satisfaction of owning a precision instrument. Both are valid reasons to buy something. Just don't confuse aesthetics and pride of ownership with audible superiority, unless you've confirmed it in a blind, level-matched test.

---

## 5. Jitter: When the Clock Lies

### 5.1 What Jitter Is

Remember that sampling happens at regular intervals determined by a clock signal. The clock is supposed to tick at *exactly* 44,100 times per second (for 44.1 kHz audio). In reality, no clock is perfect. Each tick arrives slightly early or slightly late. This timing uncertainty is called **jitter**, measured in picoseconds (trillionths of a second).

Jitter during DAC conversion matters because it means each sample is converted at slightly the wrong time. If a sample is played 100 picoseconds early or late, the reconstructed voltage at that instant is slightly wrong. The difference between the correct voltage and the actual voltage is an error -- and that error manifests as noise and distortion.

### 5.2 How Jitter Sounds

At very high levels (nanoseconds, thousands of picoseconds), jitter is audible as:
- Loss of stereo image precision ("smearing" -- instruments lose their specific position)
- Loss of fine detail in complex passages
- A general "hardness" or "glassiness" to the sound
- Degraded sense of space and depth

At the levels found in any decent modern DAC (low hundreds of picoseconds or less), jitter is below the threshold of audibility. The human ear's ability to detect timing errors corresponds to jitter levels far above what modern clocks produce. A 2012 AES paper by Benjamin and Gannon found that jitter becomes audible only above about 10 nanoseconds (10,000 picoseconds) for typical music signals. Modern DACs typically have jitter below 100 picoseconds.

### 5.3 Sources of Jitter

**Clock jitter**: The oscillator itself isn't perfectly periodic. Crystal oscillators are very good (10-100 ps jitter is typical). Temperature-compensated crystal oscillators (TCXOs) are better. Oven-controlled crystal oscillators (OCXOs) are even better but are overkill for audio.

**Interface-induced jitter**: This is where things get interesting for your Pi streamer. Different digital audio interfaces handle clocking differently:

- **I2S**: The clock signal travels on a dedicated wire alongside the data. If the DAC is physically close to the source (like a HAT mounted directly on the Pi), the clock signal arrives cleanly. This is one reason HAT DACs can sound excellent -- the clock path is short and clean.

- **USB Audio**: The data is packetized and sent over a serial bus. The timing relationship between the data and the DAC's clock is indirect. In **adaptive mode** (UAC1), the DAC derives its clock from the USB data rate, which can introduce significant jitter. In **asynchronous mode** (UAC2), the DAC uses its own high-quality clock and requests data from the host as needed -- much better.

- **S/PDIF**: The clock is embedded in the data stream (encoded into the transitions between bits). The receiving DAC must extract the clock from this data using a PLL (phase-locked loop), which can introduce jitter depending on PLL quality and the signal path.

### 5.4 Solutions: Reclocking and Asynchronous Operation

**Reclocking** means taking a jittery digital signal and re-timing it against a low-jitter local clock before feeding it to the DAC. This is what asynchronous USB does -- the DAC has its own excellent clock and reclocks the incoming data against it.

**FIFO buffering**: Some DACs include a memory buffer (FIFO -- first in, first out) that stores incoming data and reads it out under the control of a local clock. This completely isolates the DAC's conversion timing from the source's timing jitter.

For your Pi streamer with a HAT DAC: the DAC chip's own clock (on the HAT board) is what matters. The I2S connection from the Pi is clocked by this local oscillator. Jitter is not a concern you need to worry about -- it's handled by the hardware design.

> **What happens if...** you use a cheap USB DAC with adaptive (UAC1) mode instead of a proper I2S HAT? The DAC derives its clock from the USB bus, which can have significant jitter. You might hear a subtle degradation in stereo imaging and detail retrieval compared to the same DAC chip fed by a clean I2S clock. This is why we recommend HAT DACs for the Pi build.

---

## 6. Digital Audio Interfaces

### 6.1 I2S (Inter-IC Sound)

**I2S** (pronounced "I-squared-S" or "I-two-S") is the interface your Pi uses to talk to its DAC HAT. It was developed by Philips in 1986 for communicating between digital audio ICs (chips) on the same circuit board.

I2S uses three (or four) signal lines:

| Signal | Name | Purpose |
|---|---|---|
| SCK (or BCLK) | Serial Clock / Bit Clock | Clocks each bit of data |
| WS (or LRCLK) | Word Select / Left-Right Clock | Indicates left or right channel |
| SD (or DOUT/DIN) | Serial Data | The actual audio data |
| MCLK | Master Clock (optional) | A higher-frequency reference clock (typically 256x or 512x the sample rate) |

I2S is a board-level interface -- it's designed for chips talking to each other over short PCB traces, not for cables running between equipment. That's why it's perfect for a HAT mounted directly on the Pi's GPIO header: the traces are a few centimeters long, and the signal integrity is excellent.

The Pi's GPIO header exposes I2S on specific pins. When you configure a DAC HAT in the Pi's device tree overlay, you're telling the kernel to route audio data out these pins as I2S. The DAC chip on the HAT receives this I2S stream and converts it to analog.

### 6.2 S/PDIF (Sony/Philips Digital Interface)

S/PDIF is the consumer standard for digital audio between separate pieces of equipment. It carries stereo PCM audio (up to 24-bit/192 kHz) over either:

- **Coaxial**: A single RCA cable carrying a 0.5 Vpp signal at 75 ohms impedance. Looks like an analog RCA connection but is digital. Use a proper 75-ohm digital cable, not a random analog interconnect.
- **Optical (TOSLINK)**: A fiber optic cable carrying the same data as light pulses. Immune to electrical interference but limited to ~96 kHz sample rate in practice (some receivers do 192 kHz, but it's not guaranteed).

S/PDIF embeds the clock in the data stream using biphase mark coding. The receiver extracts the clock using a PLL. This clock recovery process is the main source of jitter in S/PDIF connections.

For your Pi streamer: some DAC HATs include S/PDIF output (both coax and optical), which lets you feed a separate outboard DAC or an AV receiver. Some Pi HATs are S/PDIF *input* boards, letting you digitize audio from CD players or other digital sources.

### 6.3 USB Audio Class (UAC1 and UAC2)

When you plug a USB DAC into a computer (or a Pi), it communicates using the USB Audio Class protocol. There are two versions:

**UAC1 (USB Audio Class 1.0)**:
- Supports up to 24-bit/96 kHz
- Uses **adaptive** or **synchronous** timing -- the DAC adapts to the host's clock
- Natively supported by every OS without drivers
- Higher jitter potential

**UAC2 (USB Audio Class 2.0)**:
- Supports up to 32-bit/384 kHz (and beyond)
- Supports **asynchronous** mode -- the DAC uses its own clock and controls the data flow
- Natively supported on Linux and macOS. Windows needed drivers until Windows 10 (version 1703+).
- Lower jitter due to asynchronous operation

For the Pi streamer: if you use a USB DAC instead of a HAT DAC, make sure it supports UAC2 asynchronous mode. Linux has excellent UAC2 support. But again, a HAT DAC over I2S is the preferred approach for this build.

### 6.4 AES/EBU (AES3)

The professional version of S/PDIF. AES/EBU carries the same types of data but over a balanced connection using XLR connectors at 110 ohms impedance. The balanced connection provides better noise immunity over longer cable runs -- up to 100 meters versus S/PDIF's practical limit of about 10 meters.

You'll encounter AES/EBU in professional studios and PA systems. For the home Pi streamer, you're unlikely to need it, but it's good to know it exists. Some high-end DACs accept AES/EBU input and prefer it over S/PDIF for its lower jitter characteristics (the balanced signal is more resistant to external interference that can increase jitter).

### 6.5 Interface Comparison

| Interface | Connection | Max Resolution | Clocking | Typical Use |
|---|---|---|---|---|
| I2S | PCB traces / GPIO | Limited by DAC chip | Master/slave | Pi ↔ HAT DAC |
| S/PDIF Coax | RCA cable (75Ω) | 24-bit/192 kHz | Embedded (PLL recovery) | Consumer equipment |
| S/PDIF Optical | TOSLINK fiber | 24-bit/96 kHz (practical) | Embedded (PLL recovery) | Consumer, electrical isolation |
| USB Audio (UAC2) | USB cable | 32-bit/384 kHz+ | Asynchronous | Computer ↔ DAC |
| AES/EBU | XLR cable (110Ω) | 24-bit/192 kHz | Embedded (PLL recovery) | Professional studios |

---

## 7. Audio Formats: How Digital Audio is Stored

### 7.1 PCM (Pulse Code Modulation)

**PCM** is the standard encoding for digital audio. It's what we've been describing throughout this chapter: a sequence of samples, each representing an amplitude value, at a fixed sample rate and bit depth. Every DAC expects PCM data (or converts to PCM internally).

PCM data needs a container format to be stored as a file:

**Uncompressed PCM**:
- **WAV** (Waveform Audio File Format): Microsoft/IBM format. The workhorse. Dead simple: a short header followed by raw PCM data. Maximum file size is 4 GB (due to 32-bit size field), extended by RF64/BWF for larger files.
- **AIFF** (Audio Interchange File Format): Apple's version. Same quality as WAV, different header format. No practical advantage over WAV; just a historical fork.

**Lossless compression** (smaller file, bit-for-bit identical to original when decoded):
- **FLAC** (Free Lossless Audio Codec): Open source, universally supported, typically 50-60% of original size. This is what you should use for your music library.
- **ALAC** (Apple Lossless Audio Codec): Apple's version. Now open source. Supported natively on Apple devices, supported by most players. Same quality as FLAC, slightly less efficient compression.
- **WavPack**: Open source, supports hybrid lossy/lossless mode. Less common but technically excellent.

### 7.2 DSD (Direct Stream Digital)

DSD takes a radically different approach from PCM. Instead of multi-bit samples at a moderate rate, DSD uses **1-bit samples at a very high rate** (2.8224 MHz for DSD64, 5.6448 MHz for DSD128, and so on up to DSD512).

Each DSD sample is just a single bit: "the signal went up" or "the signal went down" since the last sample. It's essentially the output of a delta-sigma modulator *before* decimation (conversion to multi-bit PCM). DSD was developed by Sony and Philips for the Super Audio CD (SACD) format.

**The DSD debate**:
- DSD proponents argue it's a more "natural" representation (no brick-wall anti-aliasing filter, simpler conversion process)
- DSD critics point out that it has rising noise above 20 kHz (inherent to 1-bit delta-sigma), that it's essentially a delta-sigma bitstream (so delta-sigma DACs are already "native DSD" in a sense), and that you can't do DSP on DSD without converting to PCM first
- Most "DSD DACs" convert DSD to PCM internally for processing, then convert back to analog

For your Pi streamer: most music is available in PCM. If you have DSD files (from SACD rips or DSD recordings on sites like NativeDSD), your player software will either play them natively (if the DAC supports DSD-over-PCM or native DSD) or convert them to PCM on the fly. Don't choose your DAC based on DSD support unless you have a large DSD library.

### 7.3 Lossy Compression

Lossy codecs reduce file size by discarding information deemed inaudible through **perceptual coding** -- psychoacoustic models that predict what your brain won't miss.

| Codec | Typical Bitrate | Quality | Notes |
|---|---|---|---|
| MP3 (LAME) | 128-320 kbps | Good at 256+, transparent at 320 | The original. V0 or 320 kbps is genuinely hard to distinguish from lossless. |
| AAC | 128-256 kbps | Better than MP3 at same bitrate | Apple's preferred format. Excellent quality at 256 kbps. |
| Opus | 96-256 kbps | Best current lossy codec | Open source, designed for both music and voice. Transparent at lower bitrates than MP3/AAC. |
| Ogg Vorbis | 128-320 kbps | Comparable to AAC | Open source, used by Spotify (320 kbps for Premium). |

**Are lossy formats audibly inferior?** At their highest quality settings (MP3 320, AAC 256, Opus 128+), the differences from lossless are inaudible to virtually all listeners in controlled blind tests. The ABX test archives are full of failed attempts to distinguish high-bitrate lossy from lossless. Spotify Premium at 320 kbps Ogg Vorbis is, for all practical purposes, transparent.

That said, if storage is cheap (and it is -- a 1TB NAS drive costs $40), there's no reason not to store your library in FLAC. You get bit-perfect archival quality with no generation loss if you ever need to transcode.

### 7.4 Container vs Codec

Don't confuse the container (file format) with the codec (compression algorithm):

- **.flac** is both a container and a codec
- **.mp3** is both a container and a codec
- **.m4a** is a container (MPEG-4 Part 14) that can contain AAC, ALAC, or other codecs
- **.ogg** is a container that typically contains Vorbis or Opus audio
- **.wav** is a container for uncompressed PCM (though it can technically contain compressed data)
- **.mka** (Matroska audio) can contain almost anything

Your player software handles all of this transparently. You don't need to worry about containers vs codecs unless you're debugging why a specific file won't play.

---

## 8. The Loudness War and Dynamic Range

### 8.1 What the Loudness War Is

The **loudness war** is the decades-long trend of mastering music at progressively higher average levels, achieved by applying aggressive compression and limiting. The goal: make the track sound louder than the competition when played on the radio or in a playlist, because human perception equates loudness with quality (the Fletcher-Munson effect).

The result: modern masters often have only 4-8 dB of dynamic range (the difference between the quietest and loudest moments). A well-mastered recording from the 1970s or 1980s might have 15-20 dB of dynamic range. Classical music can have 30+ dB.

### 8.2 Why It Sounds Fatiguing

When everything is loud all the time, your ear has no contrast. There are no quiet moments to give the loud moments impact. The constant high average level causes listener fatigue -- a sense of tiredness and irritation after extended listening that isn't about volume per se but about the relentless, unvarying level.

The heavy limiting also introduces **inter-sample peaks** (ISPs): when a mastering limiter pushes the signal to 0 dBFS (the maximum digital level), the actual analog waveform reconstructed by the DAC can exceed 0 dBFS between sample points. This causes clipping in the DAC's output stage, producing distortion.

### 8.3 The Loudness Normalization Fix

The good news: streaming services have largely defused the loudness war. Spotify, Apple Music, YouTube, and Tidal all apply **loudness normalization** -- they measure each track's loudness (using the LUFS standard) and adjust playback gain so all tracks play at a similar perceived volume. This means a hyper-compressed master and a dynamic master play at the same perceived level -- and the dynamic master sounds *better* because it has contrast and headroom.

If you're building a Pi streamer and curating a local library, be aware of the mastering quality of your files. Some albums have been remastered multiple times -- seek out the version with the best dynamic range. The "DR Database" (dr.loudness-war.info) catalogs the dynamic range of thousands of releases.

### 8.4 What This Means for Your System

You've built (or are about to build) a system with real amplifiers driving real speakers in a real room. Dynamic range matters here in a way it doesn't through phone speakers or Bluetooth earbuds. A well-mastered recording through a well-built system is a genuinely different experience from a crushed master through consumer gear.

When you get to Chapter 29 (room correction and DSP), you'll have the tools to measure and optimize your system's actual dynamic range -- from the noise floor of your amplifier to the maximum SPL of your speakers. The limiting factor should be the physics of your room and speakers, not the mastering of your music.

> **What happens if...** you play a loudness war casualty (Metallica's *Death Magnetic* is the canonical example, with a DR of 3-4) through your carefully built system? It will sound loud, flat, and fatiguing, with audible clipping artifacts on transients. Compare it to the Guitar Hero version of the same album (which was mastered from a different, more dynamic source), and you'll hear exactly what the loudness war destroys. Your system is revealing enough to expose bad mastering -- which is both a feature and a curse.

---

## 9. Putting It All Together: The Digital Signal Path

### 9.1 From File to Air

Here's the complete digital signal path for your Pi streamer, tying together everything in this chapter:

```
Music File (FLAC, 16-bit/44.1 kHz)
    ↓
Software Decoder (FLAC → PCM)
    ↓
ALSA Audio Subsystem (Linux kernel)
    ↓
I2S Interface (GPIO pins)
    ↓
DAC Chip (PCM5122 on HiFiBerry HAT)
    ├── Digital filter (anti-imaging)
    ├── Delta-sigma modulator (oversampling + noise shaping)
    ├── 1-bit DAC element (pulse stream)
    └── Analog reconstruction filter
    ↓
Analog Line-Level Output (~2 Vrms)
    ↓
Your Amplifier (from Part 3)
    ↓
Your Speakers (from Part 2)
    ↓
Sound Waves → Your Ears
```

Every stage in this chain has been designed to preserve the original signal. The FLAC decode is bit-perfect (lossless). The I2S transfer is bit-perfect (direct digital). The DAC conversion is the only stage where imperfections enter -- and with a decent modern DAC, those imperfections are 100+ dB below the signal level.

### 9.2 What Matters and What Doesn't

Based on everything in this chapter, here's a practical summary:

**Matters a lot**:
- DAC chip quality and implementation (not just the chip -- the analog output stage, power supply, and clock quality of the board)
- Jitter (use I2S or async USB, avoid adaptive USB)
- Source file quality (well-mastered recording > poorly mastered hi-res)
- Proper reconstruction filtering

**Matters for recording/production but not playback**:
- Sample rates above 48 kHz
- Bit depth above 16-bit (24-bit is fine to *store*, but the extra bits aren't audible in playback)

**Doesn't matter (despite what forums say)**:
- "Bit-perfect" playback paths (any non-broken software path is bit-perfect)
- Exotic power supplies for the Pi (the DAC HAT has its own voltage regulators)
- Expensive USB cables (for digital connections -- analog cables are a different story)
- DSD vs PCM for playback

Now that you understand what digital audio is and how it works, the next chapter covers how to get that digital audio to your Pi over the network -- the streaming protocols, server software, and architectural decisions that determine what your system can do.

---

## 10. The Digital Audio Myths

### 10.1 "Vinyl Sounds Better Than Digital"

This is the most persistent audio myth. Vinyl has measurably worse performance than CD on every metric: dynamic range (~65 dB vs 96 dB), frequency response (RIAA curve rolloff vs flat), distortion (0.5-2% THD vs 0.001%), channel separation (20-30 dB vs 90+ dB), and signal-to-noise ratio. Vinyl also degrades with every play as the stylus wears the groove walls.

What vinyl *does* have is different mastering. Many vinyl releases use more dynamic, less compressed masters than their CD or streaming counterparts (because aggressive limiting causes problems with vinyl cutting -- the lacquer groove physically can't handle it). When people compare a vinyl to a CD of the same album and prefer the vinyl, they're usually hearing the better mastering, not the superior format.

The lesson for your Pi streamer: seek out well-mastered digital releases. A well-mastered FLAC file through your Pi streamer will be technically superior to the same recording on vinyl by every objective measure.

### 10.2 "You Can Hear the Difference Between FLAC and 320 kbps MP3"

Maybe, if you're young, you have trained ears, and you're listening on a resolving system in a quiet room. Probably not, under any other conditions. The ABX double-blind test archives contain thousands of tests, and the consensus is clear: at 320 kbps MP3 (LAME encoder) or 256 kbps AAC, the difference from lossless is inaudible to the vast majority of listeners under controlled conditions.

This doesn't mean you should use lossy formats for your library -- storage is cheap, and FLAC preserves the option of re-encoding to different formats without generation loss. But don't lose sleep over streaming services using lossy codecs at high bitrates.

### 10.3 "Cables Make a Difference in Digital Audio"

For **digital** connections (USB, S/PDIF, Ethernet), the cable either works or it doesn't. Digital data is transmitted as discrete symbols, and the receiving device either reconstructs them correctly or it doesn't. A bit is a bit. There is no "better-sounding" USB cable for digital audio, because the cable doesn't carry audio -- it carries data. Error correction in the protocol (USB) or error detection (S/PDIF) handles any transmission issues.

For **analog** connections (the RCA cables from your DAC to your amplifier), cable quality matters slightly more -- but the effect is small and mostly about shielding (rejecting interference) and contact quality, not exotic metallurgy or geometric weaving patterns. A well-made cable with good connectors from a reputable brand (like Mogami, Canare, or Belden) costs $10-20 per pair and will perform identically to a cable costing $500 or $5,000. Buy on engineering, not marketing.

### 10.4 "Higher Sample Rates Sound More Spacious/Airy"

This claim persists because people don't level-match when comparing. Some DACs apply a slight level boost when playing high-sample-rate files (it's an implementation detail of the digital filter). Louder sounds better (Fletcher-Munson again). When you level-match precisely (within 0.1 dB), the "spaciousness" of 192 kHz versus 44.1 kHz disappears.

There's also confirmation bias: if you've paid $30 for a hi-res download versus nothing for a CD rip, you want to hear a difference. The brain obliges.

### 10.5 "Bit-Perfect Playback Matters"

"Bit-perfect" means the digital data arriving at the DAC is identical to the data in the source file -- no processing, no sample rate conversion, no volume adjustment in the digital domain. Audiophile lore holds that bit-perfect playback sounds better than "processed" playback.

In one narrow sense, this is true: if software applies a bad sample rate conversion or clips the signal during digital volume adjustment, the output is degraded. But any competent audio software (MPD, ALSA) handles these operations correctly. The difference between "bit-perfect" and "processed through a competent software stack" is either zero or below the noise floor of your DAC.

CamillaDSP processes audio in 64-bit floating point and outputs to 24-bit or 32-bit integer. The processing headroom is so vast that no information is lost. Your DSP-processed audio is, for all practical purposes, identical to bit-perfect output (with the intentional modifications of your EQ and crossover applied, of course).

> **What happens if...** you spend months optimizing your Pi for "bit-perfect" playback -- disabling services, using a real-time kernel, setting CPU governor to performance mode -- and then add CamillaDSP for room correction? Your "bit-perfect" signal is no longer bit-perfect (it's been processed). And it sounds *better* than the bit-perfect version because the room correction fixes real audible problems. The lesson: correctness of the processing matters far more than purity of the signal path.

---

## 11. A Brief History of Digital Audio

### 11.1 The Origin Story

Digital audio didn't arrive fully formed. The path from analog to digital was decades long and filled with engineering compromises that shaped the formats we use today.

**1928**: Harry Nyquist publishes his sampling theorem framework.
**1949**: Claude Shannon provides the rigorous mathematical proof in "Communication in the Presence of Noise."
**1958**: Max Mathews at Bell Labs creates the first computer-generated music.
**1967**: The NHK Technical Research Institute demonstrates early PCM recording.
**1972**: Denon begins making commercial PCM recordings in Japan.
**1978**: Sony develops the PCM-1600, using a modified U-Matic video recorder for digital audio storage. This is where the 44.1 kHz sample rate originates -- it was chosen to fit within the bandwidth of video equipment.
**1982**: The Compact Disc is released jointly by Sony and Philips. 16-bit, 44.1 kHz, stereo. The format that would define digital audio for 30 years.
**1987**: DAT (Digital Audio Tape) is released, using 48 kHz sample rate (chosen to prevent direct digital copying from CD).
**1995**: MPEG Audio Layer III (MP3) is standardized.
**1999**: Super Audio CD (SACD) and DVD-Audio launch as "hi-res" formats. Neither achieves mass adoption.
**2003**: Apple launches the iTunes Music Store with 128 kbps AAC. Digital distribution begins.
**2012**: Spotify reaches 20 million subscribers, establishing streaming as the dominant distribution model.
**2015**: Tidal launches with lossless streaming.
**2021**: Apple Music adds lossless streaming at no extra cost.

### 11.2 Why This History Matters

Every format choice in your Pi streamer traces back to these historical decisions. 44.1 kHz exists because of video equipment from 1978. 48 kHz exists because of DAT and broadcast standards. FLAC exists because engineers wanted lossless compression for storage and distribution. And the ongoing "hi-res audio" marketing exists because the industry needs something to sell you after you've already bought all the music in standard quality.

Understanding the history helps you separate engineering from marketing. The CD specification (16-bit/44.1 kHz) wasn't a compromise -- it was designed to exceed human hearing capability, and it succeeded. Everything beyond it is either for production convenience or for selling you the same music at a higher price.

---

## Summary

Digital audio works by sampling an analog signal at regular intervals (sample rate) and mapping each sample to a discrete value (quantization at a given bit depth). The Nyquist-Shannon theorem guarantees that a bandlimited signal can be perfectly reconstructed from its samples if the sample rate exceeds twice the highest frequency -- which is why 44.1 kHz captures everything the human ear can hear.

DACs convert the digital stream back to analog using either delta-sigma (oversampling, noise shaping, dominant in modern DACs) or R2R (resistor ladder, simpler concept, harder to manufacture precisely). Jitter -- timing uncertainty in the DAC's clock -- is the primary digital-domain impairment, but modern hardware keeps it far below audibility.

Digital audio travels between devices over I2S (board-level, what your Pi HAT uses), S/PDIF (consumer, coax or optical), USB Audio (computer to DAC), or AES/EBU (professional). Audio is stored as PCM (WAV, FLAC, ALAC) or DSD, and lossy codecs (MP3, AAC, Opus) use perceptual coding to reduce file size with minimal audible impact at high bitrates.

The real enemy of sound quality in the digital age isn't the technology -- it's the loudness war, where aggressive mastering compression destroys dynamic range. Your Pi streamer, driving real amplifiers and speakers, will reveal the difference between a well-mastered and a poorly-mastered recording with uncomfortable clarity.
