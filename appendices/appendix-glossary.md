<!--
  APPENDIX: A
  TITLE: Glossary of Audio and Electronics Terminology
  UPDATED: 2026-04-10
-->

# Appendix A: Glossary of Audio and Electronics Terminology

> A comprehensive reference of terms used throughout this guide, organized alphabetically. Each entry includes a brief definition and a cross-reference to the chapter where the concept is explored in depth.

---

## A

**AC (Alternating Current)** — Electric current that periodically reverses direction. Audio signals are AC; power from a wall outlet is AC at 50/60 Hz. *(See: Ch 0)*

**AC Coupling** — Using a capacitor to pass AC signals while blocking DC. Used between amplifier stages to prevent DC offset from propagating. *(See: Ch 18)*

**Acoustic Center** — The effective point in space from which a speaker driver's sound appears to originate. Different from the physical center of the cone; it varies with frequency and driver type. Relevant for time alignment in multi-way speakers. *(See: Ch 14)*

**Active Component** — A component that can amplify or control current flow, requiring external power. Transistors, op-amps, and vacuum tubes are active. Contrast with passive components. *(See: Ch 1)*

**Active Crossover** — A crossover network that operates at line level (before amplification), using active components like op-amps. Each frequency band gets its own power amplifier. *(See: Ch 29)*

**ADC (Analog-to-Digital Converter)** — A circuit that converts a continuous analog signal into discrete digital samples. Resolution is measured in bits; sampling speed in Hz. *(See: Ch 25)*

**Additive Synthesis** — A sound synthesis technique that creates complex tones by summing multiple sine waves (harmonics) at different frequencies, amplitudes, and phases. Each partial can be independently controlled over time. *(See: Ch 27)*

**Admittance** — The reciprocal of impedance (Y = 1/Z), measured in siemens. How easily current flows through a component or circuit. *(See: Ch 0)*

**ADSR (Attack, Decay, Sustain, Release)** — The four stages of an amplitude envelope used to shape how a synthesized sound evolves over time. Attack is the rise time, decay is the initial fall, sustain is the held level, and release is the fade after the note ends. *(See: Ch 27)*

**Aliasing** — Distortion that occurs when a signal is sampled at less than twice its highest frequency (below the Nyquist rate). Produces false low-frequency artifacts. *(See: Ch 25)*

**ALSA (Advanced Linux Sound Architecture)** — The low-level audio subsystem in the Linux kernel that provides device drivers and a programming interface for sound cards. The foundation on which higher-level audio systems like PulseAudio and PipeWire are built. *(See: Ch 28)*

**Amplification Factor (Mu)** — See Mu. *(See: Ch 20)*

**Amplifier** — A circuit that increases the amplitude (voltage, current, or power) of a signal. Types include Class A, AB, B, D, and many others. *(See: Ch 18)*

**Amplitude** — The magnitude of a signal, typically measured as peak, peak-to-peak, or RMS voltage. Corresponds to loudness for audio signals. *(See: Ch 0)*

**Anode** — The positive terminal of a device. In a vacuum tube, the plate that collects electrons. In a diode, the terminal current flows into. *(See: Ch 1, Ch 18)*

**Anti-Aliasing Filter** — A low-pass filter placed before an ADC that removes all frequency content above the Nyquist frequency (half the sample rate). Prevents aliasing artifacts during digital sampling. *(See: Ch 25)*

**Asynchronous USB** — A USB audio transfer mode where the DAC provides its own master clock and controls the data flow timing, rather than relying on the computer's clock. Reduces jitter compared to adaptive or synchronous USB modes. *(See: Ch 25)*

**Attenuation** — Reduction in signal amplitude, usually expressed in decibels (dB). The opposite of amplification. *(See: Ch 5)*

**A-Weighting** — A frequency-weighting curve applied to sound level measurements that approximates the human ear's sensitivity at moderate listening levels. De-emphasizes low and very high frequencies where hearing is less sensitive. Used in noise specifications (dBA). *(See: Ch 12)*

**AWG (American Wire Gauge)** — A standardized system for measuring wire diameter. Lower numbers = thicker wire. Common audio: 22 AWG (hook-up), 14-16 AWG (speaker). *(See: Ch 0)*

---

## B

**Baffle** — The front panel of a speaker enclosure on which the driver is mounted. Its size and shape affect sound radiation patterns. *(See: Ch 12)*

**Baffle Step** — The frequency at which a speaker driver transitions from radiating into a half-space (blocked by the baffle) to radiating into full space (sound diffracts around the baffle). Causes a 6 dB drop in on-axis response. *(See: Ch 14)*

**Balanced Audio** — A method of transmitting audio using two signal conductors (hot and cold) plus a ground. Noise picked up equally on both conductors cancels out at the receiver. Used in XLR connections. *(See: Ch 33)*

**Bandwidth** — The range of frequencies a circuit or device can handle. Measured as the difference between upper and lower -3 dB points. *(See: Ch 5)*

**Bass Reflex** — A speaker enclosure design that uses a port (tube or slot) to extend low-frequency response by using the back wave of the driver. *(See: Ch 13)*

**Bias** — A DC voltage or current applied to an active device to set its operating point. In tube amps, bias determines idle current through the output tubes. *(See: Ch 18, Ch 20)*

**Bias (Cathode/Self)** — A biasing method for vacuum tubes where the cathode resistor develops a voltage that negatively biases the grid relative to the cathode. Self-adjusting and simpler than fixed bias, though it reduces available output voltage swing. *(See: Ch 20)*

**Bias (Fixed)** — A biasing method for vacuum tubes where a separate negative voltage supply provides grid bias. Allows precise adjustment of idle current and maximizes output power, but requires periodic adjustment as tubes age. *(See: Ch 20)*

**Bi-amping** — Using separate amplifiers for the low-frequency and high-frequency sections of a speaker, with an active crossover dividing the signal before amplification. *(See: Ch 29, Ch 35)*

**Bias Point** — See Operating Point. The DC conditions (voltage and current) at which an active device is set to operate in a circuit. *(See: Ch 18, Ch 20)*

**Bipolar Junction Transistor (BJT)** — A three-terminal semiconductor device (base, collector, emitter) that amplifies current. NPN and PNP types. Used in gain stages and buffers. *(See: Ch 1, Ch 6)*

**Biquad** — A second-order IIR (Infinite Impulse Response) digital filter section defined by five coefficients. The fundamental building block of audio DSP, capable of implementing low-pass, high-pass, band-pass, notch, peaking EQ, and shelving filters. *(See: Ch 27)*

**Bit Depth** — The number of bits used to represent each audio sample in digital audio. 16-bit = 96 dB dynamic range; 24-bit = 144 dB. *(See: Ch 25)*

**Blocking Capacitor** — See AC Coupling. *(See: Ch 18)*

**BOM (Bill of Materials)** — A complete list of all components and materials needed to build a product, with quantities and costs. *(See: Ch 41)*

**Breakup** — The condition where a speaker cone begins to flex and deform rather than moving as a rigid piston. Creates distortion at high frequencies. Also used to describe amplifier distortion onset. *(See: Ch 12)*

**Bridge Rectifier** — A circuit of four diodes that converts AC to pulsating DC. The first stage of most power supplies. *(See: Ch 1, Ch 18)*

**Buffer** — A unity-gain (gain of 1) amplifier stage used to match impedance between circuits. Provides low output impedance to drive cables and subsequent stages. *(See: Ch 5, Ch 6)*

**Butterworth Filter** — A filter designed for maximally flat passband response (no ripple). Common in crossover networks. 2nd-order Butterworth has a -3 dB crossover point. *(See: Ch 14)*

**Bypass Capacitor** — A capacitor connected between a power supply pin and ground to shunt high-frequency noise, providing a local charge reservoir. Typically 100nF ceramic. *(See: Ch 1)*

---

## C

**Capacitance** — The ability to store electric charge, measured in farads (F). Capacitors block DC and pass AC, with impedance inversely proportional to frequency. *(See: Ch 1)*

**Capacitor** — A passive component that stores energy in an electric field. Types include ceramic, film, electrolytic, and tantalum, each with different characteristics. *(See: Ch 1)*

**Carbon Composition Resistor** — An older resistor type made from a mixture of carbon and clay. Higher noise than metal film, but used in vintage audio circuits for tonal characteristics. *(See: Ch 1)*

**Cathode** — The negative terminal of a device. In a vacuum tube, the heated element that emits electrons. In a diode, the terminal current flows out of. *(See: Ch 1, Ch 18)*

**CE Marking** — European conformity marking indicating a product meets EU safety, health, and environmental directives. Required for products sold in the EU. *(See: Ch 42)*

**Chip Amp** — Colloquial term for an integrated circuit power amplifier, such as the LM3886 or TPA3255. A complete amplifier on a single IC. *(See: Ch 23)*

**Circular Buffer** — A fixed-size data structure that wraps around to the beginning when it reaches the end, used in DSP for implementing delay lines, FIR filters, and audio buffering. Also called a ring buffer. *(See: Ch 27)*

**Class A Amplifier** — An amplifier topology where the output device conducts for the full 360 degrees of the signal cycle. Low distortion, low efficiency (25-50%), high heat. *(See: Ch 18)*

**Class AB Amplifier** — A compromise topology where each output device conducts for slightly more than half the signal cycle. Moderate distortion, moderate efficiency (50-70%). The most common topology for tube and solid-state audio amplifiers. *(See: Ch 18)*

**Class D Amplifier** — An amplifier that uses pulse-width modulation (PWM) to switch output devices on and off rapidly. Very high efficiency (85-95%), compact, and cool-running. Not "digital" despite the name. *(See: Ch 23)*

**Clipping** — Distortion that occurs when an amplifier's output signal exceeds its maximum voltage swing, flattening the peaks of the waveform. Can be soft (gradual) or hard (abrupt). *(See: Ch 5, Ch 6)*

**Closed Back (Sealed Enclosure)** — A speaker enclosure with no port, trapping the back wave inside. Tighter, more controlled bass than ported designs, but less bass extension. *(See: Ch 13)*

**CMRR (Common Mode Rejection Ratio)** — A measure of how well a differential amplifier or balanced input rejects noise that appears equally on both input conductors. Higher is better. *(See: Ch 33)*

**Cold Solder Joint** — A defective solder connection caused by insufficient heat, resulting in a gray, grainy appearance instead of a smooth, shiny joint. Causes intermittent connections and noise. *(See: Ch 4)*

**Compliance** — How easily a speaker cone moves, measured in mm/N. The reciprocal of stiffness. Part of the Thiele-Small parameters (Cms or Vas). *(See: Ch 12)*

**Compression (Driver)** — The reduction in a speaker driver's output efficiency at high power levels, caused by voice coil heating (power compression) or magnetic saturation. A driver rated at 90 dB sensitivity may lose 1-3 dB of output at full rated power. *(See: Ch 12)*

**Compression Driver** — A specialized speaker driver for high-frequency sound reinforcement, typically coupled to a horn for increased efficiency and directivity. *(See: Ch 12)*

**Convolution** — A mathematical operation that applies an impulse response to a signal, used in audio DSP for room correction, reverb simulation, and speaker compensation. Implemented efficiently using the FFT. *(See: Ch 27)*

**Crest Factor** — The ratio of peak value to RMS value of a signal. For a sine wave, the crest factor is 1.414 (3 dB). Music typically has a crest factor of 4-10 (12-20 dB), meaning peaks are much higher than the average level. *(See: Ch 0, Ch 18)*

**Critical Band** — The bandwidth of the auditory filter in the human ear at a given frequency. Two tones within the same critical band interact perceptually (masking), while tones in different critical bands are perceived independently. Critical bandwidth is roughly 1/3 octave above 500 Hz. *(See: Ch 12)*

**Crossover Frequency** — The frequency at which a crossover network transitions from one driver to another. Each driver operates on its side of the crossover frequency. *(See: Ch 14)*

**Crossover Network** — A filter circuit that divides an audio signal into separate frequency bands for different speaker drivers (woofer, tweeter, midrange). Can be passive or active. *(See: Ch 14, Ch 15)*

**Current** — The flow of electric charge, measured in amperes (A). In circuits, it's the flow of electrons through conductors. *(See: Ch 0)*

**Cutoff (Tube)** — The condition where a vacuum tube stops conducting because the grid voltage is sufficiently negative to repel all electrons. In a push-pull output stage, one tube reaches cutoff during the opposite half of the signal cycle in Class B or near-cutoff in Class AB. *(See: Ch 20)*

**C-Weighting** — A frequency-weighting curve for sound level measurements that is relatively flat across the audible range, with gentle roll-offs at very low and very high frequencies. Approximates the ear's response at high SPL levels. Used for measuring peak sound levels (dBC). *(See: Ch 12)*

---

## D

**DAC (Digital-to-Analog Converter)** — A circuit that converts discrete digital samples back into a continuous analog signal. The final stage before analog amplification in digital audio systems. *(See: Ch 25)*

**Damping Factor** — The ratio of speaker impedance to amplifier output impedance. Higher damping factor means the amplifier has better control over speaker cone movement. Typically 50-1000 for solid-state, 10-50 for tube amps. *(See: Ch 18, Ch 35)*

**Damping Material** — Material placed inside a speaker enclosure (polyfill, acoustic foam, fiberglass) to absorb internal reflections, reduce standing waves, and make the enclosure appear acoustically larger. *(See: Ch 13)*

**dB (Decibel)** — A logarithmic unit for expressing ratios. In audio: +3 dB = double the power; +6 dB = double the voltage; +10 dB = subjectively "twice as loud." *(See: Ch 0)*

**dBFS (Decibels Full Scale)** — A measurement of signal level relative to the maximum digital level (0 dBFS). All valid digital audio levels are 0 or negative. *(See: Ch 25)*

**dBSPL (Decibels Sound Pressure Level)** — A measurement of acoustic pressure relative to the threshold of human hearing (20 micropascals). Normal conversation is ~60 dBSPL; pain threshold is ~120 dBSPL. *(See: Ch 12)*

**DC (Direct Current)** — Electric current that flows in one direction. Battery power is DC. Audio circuits use DC power supplies to operate. *(See: Ch 0)*

**DC Blocking** — See AC Coupling. The use of a capacitor in series with the signal path to prevent DC voltage from passing between stages while allowing the AC audio signal through. *(See: Ch 18)*

**DC Offset** — An unwanted constant DC voltage added to an AC signal. Causes speaker cones to sit off-center, reducing headroom and potentially damaging drivers. *(See: Ch 18)*

**Decoupling Capacitor** — See Bypass Capacitor. *(See: Ch 1)*

**Delay** — A time-based audio effect that creates echoes by recording a signal and playing it back after a set time interval. Analog delays use BBD chips; digital delays use memory. *(See: Ch 5)*

**DFT (Discrete Fourier Transform)** — The mathematical operation that decomposes a discrete-time signal into its constituent frequency components. Converts a block of time-domain samples into frequency-domain coefficients (frequency bins). The FFT is a computationally efficient algorithm for computing the DFT. *(See: Ch 27)*

**Dielectric** — The insulating material between a capacitor's plates. Different dielectrics (ceramic, polyester, polypropylene) give capacitors different characteristics. *(See: Ch 1)*

**Diffraction** — The bending of sound waves around obstacles, including speaker cabinet edges. Causes frequency response irregularities. *(See: Ch 14)*

**DIP (Dual Inline Package)** — An IC package with two rows of through-hole pins. Standard for many op-amps and audio ICs in DIY builds. *(See: Ch 1)*

**Distortion** — Any alteration of the original signal waveform. Can be harmonic (adding overtones), intermodulation (creating sum/difference frequencies), or crossover (at zero-crossing point). *(See: Ch 5, Ch 6)*

**Dither** — Low-level noise intentionally added to a digital audio signal before reducing its bit depth (e.g., 24-bit to 16-bit). Converts harsh quantization distortion into a benign, constant noise floor. Properly dithered 16-bit audio has no audible distortion artifacts. *(See: Ch 25)*

**Drill File** — A data file (typically Excellon format) included with PCB manufacturing outputs that specifies the locations, diameters, and types of all holes to be drilled in the board. Sent to the manufacturer alongside Gerber files. *(See: Ch 31)*

**Driver** — A transducer that converts electrical signals into sound. Types include woofer (low frequency), tweeter (high frequency), midrange, full-range, and subwoofer. *(See: Ch 12)*

**DSP (Digital Signal Processing)** — The manipulation of audio signals in the digital domain using mathematical algorithms. Used for crossovers, equalization, room correction, and effects. *(See: Ch 27)*

**Dry Signal** — The unprocessed, original audio signal. Contrast with "wet" (processed) signal. *(See: Ch 5)*

---

## E

**Efficiency (Speaker)** — The ratio of acoustic power output to electrical power input, expressed as a percentage. Most home speaker drivers are 0.5-2% efficient; the rest of the energy becomes heat. Not the same as sensitivity (which is measured in dB at 1W/1m), though the two are related. *(See: Ch 12)*

**EIA (Electronics Industries Alliance)** — An organization that establishes standards for electronic components. EIA resistor and capacitor value series (E12, E24, E96) define standard component values. *(See: Ch 1)*

**Electrolytic Capacitor** — A polarized capacitor using an oxide layer as the dielectric and an electrolyte as one plate. High capacitance in small size, used for power supply filtering and audio coupling. Must be installed with correct polarity. *(See: Ch 1)*

**EMI (Electromagnetic Interference)** — Unwanted electromagnetic energy that couples into circuits, causing noise. Sources include power supplies, motors, radio transmitters, and digital circuits. *(See: Ch 33)*

**Enclosure** — The box or cabinet that houses a speaker driver or electronic circuit. Speaker enclosures profoundly affect sound; electronic enclosures provide shielding and protection. *(See: Ch 13)*

**ENIG (Electroless Nickel Immersion Gold)** — A PCB surface finish providing excellent solderability and corrosion resistance. Used by OSH Park and available as an upgrade from most PCB manufacturers. *(See: Ch 40)*

**Envelope (ADSR)** — See ADSR. *(See: Ch 27)*

**Equal Loudness Contour** — Curves (historically called Fletcher-Munson curves, now standardized as ISO 226) showing how human perception of loudness varies with frequency. At low listening levels, we are much less sensitive to bass and treble. At high levels, the response flattens. Explains why music sounds "thin" at low volume. *(See: Ch 12)*

**Equalization (EQ)** — The process of adjusting the frequency response of an audio signal. Types include parametric, graphic, shelving, and dynamic. *(See: Ch 5, Ch 27)*

**ESR (Equivalent Series Resistance)** — The total internal resistance of a capacitor. Important for electrolytic capacitors in power supply filtering — lower ESR is better. *(See: Ch 1)*

**Excursion** — The distance a speaker cone travels from its rest position. Xmax is the maximum linear excursion (one-way); beyond it, distortion rises rapidly. Larger excursion capability means more bass output, especially in small enclosures. *(See: Ch 12)*

---

## F

**Farad (F)** — The SI unit of capacitance. Most capacitors in audio circuits are measured in microfarads (uF), nanofarads (nF), or picofarads (pF). *(See: Ch 1)*

**Feedback (Negative)** — Returning a portion of an amplifier's output to its input with inverted polarity. Reduces gain but improves linearity, bandwidth, and stability. Most solid-state amplifiers use heavy negative feedback; many tube amplifiers use less. *(See: Ch 18)*

**Feedback (Positive)** — Returning a portion of an amplifier's output to its input with the same polarity. Increases gain and can cause oscillation. Used intentionally in oscillator circuits and some distortion effects; avoided in amplifier design where stability is desired. *(See: Ch 18)*

**Ferrofluid** — A magnetically responsive liquid placed in the voice coil gap of some speaker drivers (especially tweeters). Improves heat dissipation from the voice coil and provides mechanical damping, increasing power handling and smoothing the impedance peak at resonance. *(See: Ch 12)*

**FET (Field Effect Transistor)** — A transistor controlled by voltage (gate) rather than current (base). Types include JFET and MOSFET. Used for buffers, preamps, and effects circuits. *(See: Ch 1, Ch 6)*

**FFT (Fast Fourier Transform)** — An efficient algorithm for computing the Discrete Fourier Transform, converting a block of time-domain samples into frequency-domain data. Widely used in audio analysis (spectrum analyzers, measurement software) and for efficient convolution in DSP. *(See: Ch 27)*

**Film Capacitor** — A capacitor using a thin plastic film as the dielectric. Types include polyester (Mylar), polypropylene, and polystyrene. Low distortion, non-polarized, preferred for audio signal path. *(See: Ch 1)*

**Filter** — A circuit that passes some frequencies while attenuating others. Types: low-pass, high-pass, band-pass, band-reject (notch). Crossovers are filters. *(See: Ch 5, Ch 14)*

**FIR Filter (Finite Impulse Response)** — A digital filter that computes each output sample as a weighted sum of the current and previous input samples only (no feedback). Always stable, can achieve linear phase, but requires many taps for steep low-frequency filters. Used for room correction convolution and high-quality crossovers. *(See: Ch 27)*

**FLAC (Free Lossless Audio Codec)** — A compressed digital audio format that preserves all original data (lossless). Typical compression ratio of 50-70% of original file size. *(See: Ch 25)*

**Fletcher-Munson Curves** — See Equal Loudness Contours. Historical curves showing that human perception of loudness varies with frequency. We are less sensitive to low and very high frequencies at lower volumes. *(See: Ch 12)*

**Flux** — A chemical agent used in soldering to clean oxide from metal surfaces, allowing solder to flow and bond properly. Available as rosin-core (inside the solder), liquid, or paste. *(See: Ch 4)*

**FM Synthesis (Frequency Modulation Synthesis)** — A synthesis technique where one oscillator modulates the frequency of another, producing harmonically rich timbres from simple waveforms. Made famous by the Yamaha DX7 synthesizer. *(See: Ch 27)*

**Fourier Transform** — The mathematical operation that decomposes a signal into its constituent sine wave components (frequencies). The fundamental bridge between the time domain and frequency domain in signal analysis. *(See: Ch 27)*

**Free-Air Resonance (Fs)** — See Resonant Frequency (Fs). The frequency at which a speaker driver resonates when not mounted in any enclosure. A fundamental Thiele-Small parameter. *(See: Ch 12)*

**Frequency** — The number of cycles per second of a periodic signal, measured in hertz (Hz). Human hearing range: approximately 20 Hz to 20 kHz. *(See: Ch 0)*

**Frequency Bin** — A discrete frequency point in the output of a DFT or FFT. Each bin represents a narrow band of frequencies; the spacing between bins equals the sample rate divided by the FFT size. For a 4096-point FFT at 48 kHz, each bin spans approximately 11.7 Hz. *(See: Ch 27)*

**Frequency Response** — A measure of a device's output level across the frequency spectrum. Typically specified as a range at a given tolerance (e.g., 40 Hz - 20 kHz +/- 3 dB). *(See: Ch 12, Ch 18)*

**Fulfillment** — The complete process of receiving an order, picking and packing the product, shipping it to the customer, and providing tracking information. Can be self-managed or outsourced to a third-party logistics (3PL) provider. *(See: Ch 42)*

**Full Range Driver** — A single speaker driver designed to reproduce the entire audible frequency range. Compromised at the frequency extremes but eliminates the need for a crossover. *(See: Ch 12)*

**Fundamental Frequency** — The lowest frequency component of a musical note. The perceived pitch. Higher-frequency components are harmonics or overtones. *(See: Ch 5)*

**Fuse** — A protective device that melts and opens the circuit when current exceeds a rated value. Essential safety component in mains-powered equipment. Available as slow-blow (tolerates brief surges) and fast-blow types. *(See: Ch 18)*

---

## G

**Gain** — The ratio of output signal to input signal. Expressed as a dimensionless ratio (10x), or in decibels (20 dB). *(See: Ch 5, Ch 6, Ch 18)*

**Gain-Bandwidth Product (GBP)** — A constant for a given op-amp, equal to the gain multiplied by the bandwidth at that gain. If an op-amp has a GBP of 3 MHz and is set to a gain of 10, its bandwidth is 300 kHz. Limits how much gain is available at high audio frequencies. *(See: Ch 18)*

**Gain Staging** — The practice of managing signal levels through a chain of audio devices to optimize signal-to-noise ratio and minimize distortion. Each stage should receive an appropriate level. *(See: Ch 5, Ch 33)*

**Gerber File** — The standard file format for PCB manufacturing data. Contains separate layers (copper, solder mask, silkscreen, drill) that the fabrication house uses to produce the board. Generated from PCB design software like KiCad. *(See: Ch 31)*

**Ground** — The reference point (0V) in a circuit. Also the safety earth connection in mains-powered equipment. Ground loops (multiple ground paths) cause hum. *(See: Ch 0, Ch 33)*

**Ground Loop** — A condition where multiple ground paths create a loop that acts as an antenna for electromagnetic interference, causing audible hum (typically 50/60 Hz). *(See: Ch 33)*

**Ground Plane** — A large, continuous copper area on a PCB connected to ground. Provides low-impedance return path, reduces noise, and improves shielding. *(See: Ch 31)*

**Group Delay** — The rate of change of phase with respect to frequency, representing how much a filter delays different frequencies relative to each other. Measured in seconds. Constant group delay means linear phase (all frequencies delayed equally). Non-constant group delay causes waveform distortion. *(See: Ch 14, Ch 27)*

---

## H

**Harmonic Distortion** — Distortion that adds integer multiples of the fundamental frequency. Second harmonic (2x) sounds warm; third harmonic (3x) sounds edgy. Even harmonics are generally more pleasing than odd. *(See: Ch 5, Ch 6)*

**HASL (Hot Air Solder Leveling)** — A common, inexpensive PCB surface finish. Adequate for most DIY work but less flat than ENIG. *(See: Ch 31)*

**Headroom** — The margin between the normal operating level and the maximum level before clipping. More headroom = cleaner signal at high volumes. *(See: Ch 5, Ch 18)*

**Heat Sink** — A metal structure (usually finned aluminum) that dissipates heat from a component. Essential for power transistors, voltage regulators, and power amp ICs. *(See: Ch 23)*

**Henry (H)** — The SI unit of inductance. Speaker crossover inductors are typically measured in millihenries (mH). *(See: Ch 1)*

**hFE** — The DC current gain of a bipolar transistor (collector current / base current). Critical for fuzz pedal transistor selection. *(See: Ch 1, Ch 6)*

**High-Pass Filter (HPF)** — A filter that passes frequencies above a cutoff frequency while attenuating those below. Used in crossovers to route high frequencies to tweeters. *(See: Ch 14)*

**Horn** — A flared acoustic structure that increases the efficiency and directivity of a speaker driver by providing better impedance matching between the driver and the air. *(See: Ch 12)*

---

## I

**I2C (Inter-Integrated Circuit)** — A serial communication protocol used between ICs on a PCB. Two wires: SDA (data) and SCL (clock). Used for DAC/ADC control, OLED displays, and sensor reading. *(See: Ch 27)*

**I2S (Inter-IC Sound)** — A serial bus for carrying digital audio data between ICs. Separate clock and data lines. The standard interface between a DAC chip and a digital source. *(See: Ch 25)*

**Idle Current** — The DC current flowing through an amplifier's output devices when no signal is present. In tube amps, set by the bias adjustment. Higher idle current increases Class A operation range (lower crossover distortion) but increases heat dissipation. *(See: Ch 20)*

**IIR Filter (Infinite Impulse Response)** — A digital filter that uses both input samples and its own previous output samples (feedback). Very efficient, requiring far fewer coefficients than FIR filters for equivalent steepness, but introduces frequency-dependent phase shift (minimum phase). The biquad is the standard IIR building block. *(See: Ch 27)*

**Impedance** — The total opposition to current flow in an AC circuit, combining resistance and reactance. Measured in ohms. Speaker impedance is nominally 4, 8, or 16 ohms but varies with frequency. *(See: Ch 0, Ch 12)*

**Impedance Matching** — Ensuring that the output impedance of one device is appropriate for the input impedance of the next. For maximum power transfer, impedances should match. For maximum voltage transfer (most audio), source impedance should be much lower than load impedance. *(See: Ch 5, Ch 18)*

**Impulse Response** — The output of a system when given an infinitely short, infinitely loud pulse (an impulse) as input. Captures the complete behavior of a linear system -- its frequency response, phase response, and time-domain ringing. Used in convolution-based DSP for room correction. *(See: Ch 27)*

**Inductance** — The property of a conductor that opposes changes in current, creating a magnetic field. Measured in henries (H). Inductors pass DC and impede AC, opposite of capacitors. *(See: Ch 1)*

**Inductor** — A passive component that stores energy in a magnetic field. Used in crossover networks and power supply filters. Types include air-core, iron-core, and ferrite-core. *(See: Ch 1, Ch 14)*

**Input Impedance** — The impedance seen looking into the input of a circuit. Guitar amps: typically 1M ohm. Line-level inputs: typically 10k-47k ohm. *(See: Ch 5)*

**Intermodulation Distortion (IMD)** — Distortion caused by the mixing of two or more frequencies, creating sum and difference frequencies not harmonically related to the originals. Sounds harsh and unmusical. *(See: Ch 18)*

**Isochronous Transfer** — A USB transfer mode that guarantees a fixed bandwidth with consistent timing, used for audio streaming. Tolerates no retries (unlike bulk transfer), so occasional bit errors are accepted in exchange for guaranteed real-time delivery. *(See: Ch 25)*

---

## J

**JACK (JACK Audio Connection Kit)** — A professional-grade, low-latency audio server for Linux (and other platforms) that provides real-time audio routing between applications. Supports inter-application audio connections with sample-accurate synchronization. *(See: Ch 28)*

**JFET (Junction Field Effect Transistor)** — A type of FET that uses a reverse-biased p-n junction as its gate. Prized in audio for tube-like characteristics. Common in guitar effect input stages. *(See: Ch 1, Ch 6)*

**Jitter** — Small timing variations in a digital signal's clock. Causes errors in DAC reconstruction, manifesting as noise and distortion in the analog output. Lower jitter = better sound. *(See: Ch 25)*

---

## K

**KiCad** — A free, open-source PCB design software suite. Industry-capable, widely used by DIY audio builders. *(See: Ch 31)*

---

## L

**LC Filter** — A filter using an inductor (L) and capacitor (C). Used in passive speaker crossovers and power supply filtering. *(See: Ch 14)*

**Lead Time** — The time between ordering a component and receiving it. Critical for planning builds, especially for custom transformers and specialty parts. *(See: Ch 40)*

**LFO (Low-Frequency Oscillator)** — An oscillator running below the audible range (typically 0.1-20 Hz) used to modulate audio parameters. LFOs create effects like tremolo (modulating amplitude), vibrato (modulating pitch), and chorus (modulating delay time). *(See: Ch 5, Ch 27)*

**Liability** — Legal responsibility for harm or damages caused by a product. Product liability laws hold manufacturers responsible for defective products that cause injury. Relevant when selling audio equipment, particularly mains-powered devices. Liability insurance is essential for any audio business. *(See: Ch 42)*

**Line Level** — A standardized signal level for audio interconnection between devices. Consumer line level is -10 dBV (~0.316V RMS); professional is +4 dBu (~1.23V RMS). *(See: Ch 33)*

**Linear Phase** — A filter characteristic where all frequencies are delayed by exactly the same amount (constant group delay). The waveform shape is perfectly preserved, just shifted in time. Achievable with symmetric FIR filters; not possible with analog or IIR filters. *(See: Ch 27)*

**Linkwitz-Riley Filter** — A crossover filter type where the high-pass and low-pass outputs sum to a flat response at the crossover frequency. Both outputs are -6 dB at the crossover point (4th order). The preferred crossover type for most speaker designs. *(See: Ch 14)*

**LLC (Limited Liability Company)** — A business entity that provides personal liability protection while allowing pass-through taxation. Common structure for small audio businesses. *(See: Ch 42)*

**Load Line** — A graphical representation of the relationship between voltage and current in an amplifier output stage, determined by the power supply voltage and load impedance. Used to set the operating point of tube amplifiers. *(See: Ch 20)*

**Low-Pass Filter (LPF)** — A filter that passes frequencies below a cutoff frequency while attenuating those above. Used in crossovers to route low frequencies to woofers. *(See: Ch 14)*

**LTspice** — A free SPICE circuit simulation program from Analog Devices. Used for designing and testing audio circuits before building them. *(See: Ch 3)*

---

## M

**Magnet Structure** — The assembly of permanent magnets, pole pieces, and top plate that creates the magnetic field in which the voice coil operates. Common magnet materials include ferrite (ceramic, low cost, heavy), neodymium (high strength, light, expensive), and alnico (smooth field, vintage tone). *(See: Ch 11, Ch 12)*

**Margin (Gross)** — Revenue minus direct material costs, divided by revenue. Indicates how much revenue remains after component costs. *(See: Ch 41)*

**Margin (Net)** — Revenue minus all costs (materials, labor, overhead), divided by revenue. Indicates actual profit. *(See: Ch 41)*

**Markup** — The percentage added to cost to determine selling price. Distinct from margin: a 100% markup on a $50 cost gives a $100 price with a 50% margin. A common source of confusion in pricing calculations. *(See: Ch 41)*

**Masking** — A psychoacoustic phenomenon where a louder sound makes a quieter sound inaudible, even though both are present. Frequency masking (simultaneous sounds in nearby frequency bands) and temporal masking (loud sound hides quiet sounds immediately before and after) are both exploited in lossy audio compression (MP3, AAC). *(See: Ch 25)*

**Master Clock** — The reference oscillator in a digital audio system that determines sample timing. All devices in the chain should lock to a single master clock to prevent jitter and synchronization errors. In a Pi streamer, the DAC's onboard oscillator typically serves as master clock. *(See: Ch 25)*

**Matched Pair** — Two components (usually transistors or tubes) selected for having very similar electrical characteristics. Required for differential amplifier stages, push-pull output stages, and some fuzz circuits. *(See: Ch 6, Ch 20)*

**Metal Film Resistor** — A precision resistor made by depositing a thin metal film on a ceramic core. Low noise, tight tolerance (1% or better), the standard for modern audio circuits. *(See: Ch 1)*

**Microfarad (uF)** — One millionth of a farad. Common unit for electrolytic and larger film capacitors in audio circuits. *(See: Ch 1)*

**MIDI (Musical Instrument Digital Interface)** — A protocol for communication between electronic musical instruments and controllers. Used for switching, preset recall, and parameter control. *(See: Ch 27)*

**Minimum Phase** — A filter or system characteristic where the phase response is the minimum possible for a given magnitude (frequency) response. All analog filters and IIR digital filters are minimum phase. The phase at any frequency can be calculated from the amplitude response, and vice versa. *(See: Ch 27)*

**MOQ (Minimum Order Quantity)** — The smallest quantity a supplier will sell. Important when sourcing custom or specialty components. *(See: Ch 40)*

**MOSFET (Metal Oxide Semiconductor FET)** — A type of FET widely used in power amplification (Class D amps), switching, and some guitar effects. High input impedance, can handle significant current. *(See: Ch 1, Ch 22)*

**Mu (Amplification Factor)** — The voltage gain inherent to a vacuum tube, determined by its physical construction. A 12AX7 has mu = 100; a 12AT7 has mu = 60; a 12AU7 has mu = 20. *(See: Ch 18)*

**Multiband** — A system divided into multiple frequency bands, each processed separately. Multiband compression, multiband crossovers, multi-way speakers. *(See: Ch 14, Ch 15)*

---

## N

**Nanofarad (nF)** — One billionth of a farad. Common unit for film and ceramic capacitors in audio signal circuits. 1 nF = 1,000 pF = 0.001 uF. *(See: Ch 1)*

**Negative Feedback** — See Feedback (Negative). *(See: Ch 18)*

**Noise Shaping** — A technique used with dither that reshapes the spectrum of quantization noise, pushing it into frequency ranges where human hearing is less sensitive. Makes dithered audio sound quieter than its measured noise floor would suggest. Used in delta-sigma DACs and during bit-depth reduction. *(See: Ch 25)*

**NOS (New Old Stock)** — Unused components (typically vacuum tubes) that were manufactured decades ago but never installed. Sought by audiophiles for their perceived superior quality. *(See: Ch 40)*

**Notch Filter** — A narrow band-reject filter that attenuates a specific frequency while passing all others. Used for removing hum or resonances. *(See: Ch 27)*

**Nyquist Frequency** — Half the sampling rate of a digital audio system. The highest frequency that can be accurately represented. At 44.1 kHz sampling: Nyquist = 22.05 kHz. *(See: Ch 25)*

---

## O

**Ohm** — The SI unit of resistance and impedance. Named after Georg Ohm. *(See: Ch 0)*

**Ohm's Law** — V = I x R. The fundamental relationship between voltage, current, and resistance. The foundation of all circuit analysis. *(See: Ch 0)*

**Op-Amp (Operational Amplifier)** — A high-gain differential amplifier IC used as a building block for audio circuits. Common types: TL072 (general purpose), NE5532 (low noise), OPA2134 (high quality). *(See: Ch 1, Ch 6)*

**Open Baffle** — A speaker mounted on a flat board without an enclosure. The front and back waves interact, creating a dipole radiation pattern. Simple construction but requires careful frequency response management. *(See: Ch 13)*

**Operating Point** — The DC conditions (voltage and current) at which an active device is set to operate when no signal is present. Determined by bias circuitry. Also called the quiescent point or Q-point. *(See: Ch 18, Ch 20)*

**Oscillation** — Unwanted self-sustaining signal generation in a circuit, caused by positive feedback. Manifests as squealing, motorboating, or high-frequency tones. *(See: Ch 18)*

**Oscillator** — A circuit that generates a periodic waveform (sine, square, triangle, sawtooth) without an external signal input. Used in synthesis, LFOs, and clock generation. *(See: Ch 27)*

**Oscilloscope** — A test instrument that displays signal voltage over time. Essential for visualizing waveforms, measuring frequency, identifying distortion, and debugging circuits. *(See: Ch 4)*

**Output Impedance** — The impedance seen looking back into the output of a circuit. Lower output impedance provides better control of the load (higher damping factor for amplifiers). *(See: Ch 18)*

**Output Transformer** — In a tube amplifier, the transformer that matches the high impedance of the output tubes to the low impedance of the speaker. Critical for tone and power transfer. *(See: Ch 18, Ch 20)*

**Overdrive** — A category of distortion effect that simulates the sound of an amplifier being driven hard. Softer clipping than distortion or fuzz. *(See: Ch 5, Ch 6)*

**Oversampling** — The technique of sampling or processing at a rate higher than necessary (typically 2x, 4x, or 8x), then filtering and decimating back to the target rate. In DACs, oversampling relaxes the requirements on the analog reconstruction filter. In ADCs, it improves resolution and reduces aliasing. *(See: Ch 25)*

---

## P

**Pad (Attenuator)** — A network of resistors used to reduce signal level by a fixed amount. L-pad attenuators are used for speaker level adjustments. *(See: Ch 14)*

**Panelization** — The practice of arranging multiple PCB designs (or multiple copies of the same design) onto a single larger panel for manufacturing. Reduces per-board cost and simplifies handling during assembly. Panels are broken apart after soldering. *(See: Ch 31, Ch 43)*

**Parallel Circuit** — Components connected so that the same voltage appears across each. Total resistance is less than the smallest individual resistance. *(See: Ch 0)*

**Passive Component** — A component that cannot amplify; it can only store or dissipate energy. Resistors, capacitors, and inductors are passive. *(See: Ch 1)*

**Passive Crossover** — A crossover network built from passive components (inductors, capacitors, resistors) that operates at speaker level (after amplification). *(See: Ch 14)*

**Passive Radiator** — An unpowered speaker cone (no motor) that radiates sound driven by the air pressure inside a sealed enclosure, functioning similarly to a port. *(See: Ch 13)*

**PCB (Printed Circuit Board)** — A board with copper traces that provide electrical connections between components. Can be single-layer, double-layer, or multi-layer. *(See: Ch 31)*

**PCBA (Printed Circuit Board Assembly)** — The process of soldering components onto a PCB. When outsourced, refers to the service of having a manufacturer populate your boards. *(See: Ch 40, Ch 43)*

**Peak-to-Peak (Vpp)** — The voltage difference between the positive and negative peaks of an AC signal. For a sine wave, Vpp = 2 x Vpeak. *(See: Ch 0)*

**Pentode** — A vacuum tube with five elements (cathode, control grid, screen grid, suppressor grid, plate). Higher gain and output power than triodes. Used in power amplifier stages. *(See: Ch 18)*

**Pentode Mode** — Operating a pentode or beam-power tube with its screen grid at full voltage, providing maximum gain and output power. Produces more odd-order harmonics than triode mode, resulting in a brighter, more aggressive tone. *(See: Ch 20)*

**Phase** — The timing relationship between two signals of the same frequency. Measured in degrees (0-360). Phase shifts in crossovers affect how drivers sum acoustically. *(See: Ch 14)*

**Phase Inversion** — A 180-degree phase shift, effectively flipping the signal upside down. Occurs in many amplifier stages and must be tracked in multi-stage designs. *(See: Ch 18)*

**Phase Response** — A graph showing how much phase shift a system introduces at each frequency. Together with the frequency (amplitude) response, completely characterizes a linear system. *(See: Ch 14, Ch 27)*

**Pick-and-Place** — An automated machine used in PCB assembly that picks surface-mount components from reels or trays and places them onto solder-pasted pads on the PCB with high speed and precision. A key part of the PCBA manufacturing process. *(See: Ch 43)*

**Picofarad (pF)** — One trillionth of a farad. Common unit for small ceramic and silver mica capacitors. Used in treble circuits, oscillator tuning, and RF filtering. *(See: Ch 1)*

**Pink Noise** — Random noise with equal energy per octave, rolling off at -3 dB per octave (or -10 dB per decade). Sounds more balanced than white noise to human hearing because our perception is roughly logarithmic. Used for acoustic measurement and speaker testing. *(See: Ch 37)*

**PipeWire** — A modern Linux audio/video server that replaces both PulseAudio and JACK, providing low-latency professional audio routing and consumer audio management in a single unified framework. Increasingly the default audio system on Linux. *(See: Ch 28)*

**Plate Dissipation** — The power dissipated as heat by the plate (anode) of a vacuum tube, equal to plate voltage multiplied by plate current. Must not exceed the tube's maximum rated plate dissipation or the plate will overheat and the tube will fail. *(See: Ch 20)*

**Plate Resistance (rp)** — The AC resistance of a vacuum tube's plate circuit, defined as the change in plate voltage divided by the change in plate current at a constant grid voltage. Related to mu and transconductance by: rp = mu / gm. *(See: Ch 20)*

**Point-to-Point Wiring** — A construction method where components are soldered directly between terminals, turret posts, or eyelet boards, without a printed circuit board. Traditional method for tube amplifiers. *(See: Ch 20)*

**Polarity** — The orientation of a component with respect to positive and negative terminals. Electrolytic capacitors and diodes are polarized and must be installed correctly. *(See: Ch 1)*

**Port** — A tube or slot in a bass reflex speaker enclosure that allows controlled venting of the internal air volume. Port dimensions determine the tuning frequency. *(See: Ch 13)*

**Potentiometer (Pot)** — A three-terminal variable resistor. Linear (B) taper varies evenly; logarithmic/audio (A) taper varies logarithmically, matching human loudness perception. *(See: Ch 1)*

**Power (Watts)** — The rate of energy transfer. P = V x I = V^2/R = I^2 x R. In audio, refers to the electrical power delivered to a speaker or dissipated by a component. *(See: Ch 0)*

**Power Dissipation** — The amount of electrical energy converted to heat by a component, measured in watts. Must be managed through heat sinks, ventilation, and component ratings to prevent thermal failure. *(See: Ch 18, Ch 23)*

**Power Supply Rejection Ratio (PSRR)** — A measure of how well a circuit rejects noise on its power supply. Higher PSRR = less power supply noise in the output. *(See: Ch 18)*

**Preamplifier** — An amplifier that boosts a low-level signal (microphone, guitar pickup, phono cartridge) to line level. Provides gain and often tone shaping. *(See: Ch 18)*

**Psychoacoustics** — The study of how humans perceive sound. Encompasses equal loudness contours, masking, critical bands, spatial hearing, and the non-linear relationship between physical sound properties and perceived qualities. Understanding psychoacoustics helps make better design decisions about what specifications actually matter. *(See: Ch 12)*

**PulseAudio** — A sound server for Linux that manages audio routing between applications and hardware. Provides per-application volume control, network audio, and automatic device switching. Being replaced by PipeWire in many distributions. *(See: Ch 28)*

**Push-Pull** — An output stage topology using two active devices operating in anti-phase, each handling one half of the signal swing. Cancels even-order harmonics. Standard for most tube and solid-state power amplifiers. *(See: Ch 20)*

**PWM (Pulse Width Modulation)** — A technique for encoding analog values in digital signals by varying the duty cycle. Used in Class D amplifiers. *(See: Ch 23)*

---

## Q

**Q Factor** — A dimensionless parameter describing the sharpness of a filter or resonance. Higher Q = narrower bandwidth / sharper resonance. In speakers, Qts is the total Q factor of the driver in free air. *(See: Ch 12, Ch 14)*

**Qes** — The electrical Q factor of a speaker driver, related to the electromagnetic damping. Part of the Thiele-Small parameters. *(See: Ch 12)*

**Qms** — The mechanical Q factor of a speaker driver, related to the mechanical damping of the suspension. Part of the Thiele-Small parameters. *(See: Ch 12)*

**Qts** — The total Q factor of a speaker driver: 1/Qts = 1/Qes + 1/Qms. Determines the driver's suitability for different enclosure types. Qts < 0.4: suited for vented; Qts 0.4-0.7: sealed; Qts > 0.7: open baffle. *(See: Ch 12, Ch 13)*

**Quiescent Current** — See Idle Current. The DC current flowing through a circuit when no signal is present. Determines the operating point and Class A operating range of an amplifier. *(See: Ch 18, Ch 20)*

---

## R

**Rail Voltage** — The power supply voltage(s) available to an amplifier circuit. A dual-rail supply provides +V and -V (e.g., +/-30V), determining the maximum output voltage swing before clipping. A single-rail supply provides only +V and ground. *(See: Ch 18, Ch 21)*

**RC Filter** — A filter using a resistor and capacitor. The simplest filter type. Low-pass RC filters are ubiquitous in audio circuits (tone controls, de-emphasis, smoothing). *(See: Ch 5)*

**Reactance** — The frequency-dependent opposition to current flow by capacitors and inductors. Capacitive reactance decreases with frequency; inductive reactance increases. *(See: Ch 0)*

**Reconstruction Filter** — A low-pass analog filter at the output of a DAC that smooths the staircase waveform of discrete samples into a continuous analog signal. Removes images (copies of the audio spectrum) created by the DAC's sample-and-hold process. *(See: Ch 25)*

**Rectifier** — A component or circuit that converts AC to DC. Single diodes create half-wave rectification; bridge rectifiers create full-wave. *(See: Ch 1, Ch 18)*

**Reflow** — A soldering process for surface-mount components where solder paste is applied to PCB pads, components are placed on the paste, and the entire assembly is heated in an oven until the paste melts and forms solder joints. The standard method for professional SMD assembly. *(See: Ch 31, Ch 43)*

**Resistance** — Opposition to current flow, measured in ohms. Converts electrical energy to heat. For DC circuits: V = IR (Ohm's Law). *(See: Ch 0)*

**Resonance** — The tendency of a system to oscillate at specific frequencies with greater amplitude. Speaker drivers have a resonant frequency (Fs) where impedance peaks. *(See: Ch 12)*

**Resonant Frequency (Fs)** — The frequency at which a speaker driver naturally vibrates most easily. A fundamental Thiele-Small parameter that determines low-frequency performance. *(See: Ch 12)*

**REW (Room EQ Wizard)** — Free software for acoustic measurement and analysis. Used with a calibrated microphone to measure speaker frequency response, room acoustics, and system performance. *(See: Ch 15)*

**RFI (Radio Frequency Interference)** — Electromagnetic interference at radio frequencies (>100 kHz). Can be picked up by audio circuits, causing buzzing, whining, or radio reception. Shielding and filtering mitigate RFI. *(See: Ch 33)*

**Ring Buffer** — See Circular Buffer. *(See: Ch 27)*

**Ripple** — The residual AC component in a DC power supply after rectification and filtering. Lower ripple = cleaner power = less hum. *(See: Ch 18)*

**RMS (Root Mean Square)** — A method of measuring AC voltage or power that represents the equivalent DC heating value. For sine waves: Vrms = Vpeak x 0.707. *(See: Ch 0)*

---

## S

**Sample Rate** — The number of times per second an analog signal is measured (sampled) to create a digital representation. CD quality: 44.1 kHz. Studio quality: 96 kHz or 192 kHz. *(See: Ch 25)*

**Saturation** — The condition where an active device (transistor, tube, transformer core) reaches its maximum output. In tube amps, saturation creates warm, musical distortion. *(See: Ch 18)*

**Saturation (Tube)** — Specifically in vacuum tubes, the condition where increasing plate voltage produces little additional plate current because all available electrons from the cathode are already being collected. Also used loosely to describe the warm distortion character when a tube stage is driven hard. *(See: Ch 20)*

**Schematic** — A diagram showing the electrical connections and components of a circuit using standardized symbols. The blueprint for building a circuit. *(See: Ch 2)*

**Screen Dissipation** — The power dissipated as heat by the screen grid of a pentode or beam-power tube. Must be kept within rated limits; excessive screen dissipation can destroy a tube faster than excessive plate dissipation. *(See: Ch 20)*

**Sealed Enclosure** — See Closed Back. *(See: Ch 13)*

**Sensitivity (Speaker)** — The sound pressure level produced by a speaker at 1 meter with 1 watt of input, typically measured in dBSPL/W/m. Higher sensitivity = louder per watt. Typical home speakers: 84-90 dB. PA speakers: 95-105 dB. *(See: Ch 12)*

**Series Circuit** — Components connected end-to-end so that the same current flows through each. Total resistance is the sum of individual resistances. *(See: Ch 0)*

**Signal-to-Noise Ratio (SNR)** — The ratio of the desired signal level to the background noise level, expressed in dB. Higher is better. CD quality: ~96 dB. *(See: Ch 18, Ch 25)*

**Silkscreen** — The printed markings on a PCB that label component positions, values, polarity markings, and other reference information. Applied as a layer of ink (typically white) on top of the solder mask. Specified in Gerber files as the "silkscreen" or "legend" layer. *(See: Ch 31)*

**SKU (Stock Keeping Unit)** — A unique identifier assigned to each distinct product or variant in your catalog. Used for inventory tracking, order management, and sales analysis. Each combination of product, color, and option should have its own SKU. *(See: Ch 42)*

**Slew Rate** — The maximum rate of change of an amplifier's output voltage, measured in V/us (volts per microsecond). Limits the amplifier's ability to accurately reproduce fast transients. *(See: Ch 18)*

**SMD/SMT (Surface Mount Device/Technology)** — Components designed to solder directly onto the surface of a PCB, without through-hole leads. Smaller, enabling denser circuits. Harder to hand-solder but ideal for automated assembly. *(See: Ch 31)*

**Solder** — A metal alloy (typically tin-lead or lead-free tin-silver-copper) that melts at relatively low temperatures to create electrical and mechanical connections. *(See: Ch 4)*

**Solder Mask** — A protective polymer layer applied over the copper traces of a PCB, leaving only the pads exposed for soldering. Prevents solder bridges and protects traces from corrosion. Typically green, but available in many colors. *(See: Ch 31)*

**SpeakON** — Neutrik's locking speaker connector, designed for high-current speaker connections. The professional standard for speaker-to-amplifier connections. *(See: Ch 33)*

**Spectrogram** — A visual representation of how the frequency content of a signal changes over time, displayed as a 2D image with time on the horizontal axis, frequency on the vertical axis, and color or brightness indicating amplitude. Created from a series of Short-Time Fourier Transforms. *(See: Ch 27)*

**Spectral Leakage** — An artifact of the FFT that occurs when the analysis window does not contain an exact integer number of cycles of a frequency component. The energy of that component "leaks" into adjacent frequency bins. Reduced by applying window functions before the FFT. *(See: Ch 27)*

**Spider** — The corrugated fabric or composite suspension element inside a speaker driver that centers the voice coil in the magnetic gap and provides a restoring force. Located behind the cone, connecting the voice coil former to the driver frame. Also called the "inner suspension." *(See: Ch 11)*

**SPL (Sound Pressure Level)** — See dBSPL. *(See: Ch 12)*

**Standing Wave** — A resonance created inside an enclosure when sound reflects between parallel surfaces. The wavelength matches the distance between the surfaces. Causes peaks and dips in frequency response. *(See: Ch 13)*

**Star Grounding** — A grounding scheme where all ground connections radiate from a single point, preventing ground loops. Common in tube amplifier construction. *(See: Ch 20, Ch 33)*

**Step Response** — The output of a system when given a sudden, sustained input change (a step function). Reveals how a system transitions between two levels -- its overshoot, ringing, and settling time. Related to the impulse response by integration. A well-designed audio system shows a clean, fast step response with minimal ringing. *(See: Ch 18, Ch 27)*

**STFT (Short-Time Fourier Transform)** — A series of Fourier transforms applied to successive overlapping windowed segments of a signal, producing a time-frequency representation. The basis for spectrograms and many real-time audio analysis tools. *(See: Ch 27)*

**Surround** — The flexible ring of rubber, foam, or treated cloth that connects the outer edge of a speaker cone to the driver frame. Allows the cone to move freely along the excursion axis while maintaining an air seal. Foam surrounds degrade over time; rubber surrounds are more durable. *(See: Ch 11)*

---

## T

**THD (Total Harmonic Distortion)** — A measurement of all harmonic distortion components combined, expressed as a percentage of the fundamental. THD of 0.01% is considered very low; 1% is audible. *(See: Ch 18)*

**THD+N (Total Harmonic Distortion + Noise)** — THD with the noise floor included. A more comprehensive measurement of signal quality than THD alone. *(See: Ch 18)*

**Thermal Resistance** — A measure of how effectively heat flows from a component to its heat sink or the ambient environment, measured in degrees Celsius per watt (C/W). Lower thermal resistance means better heat dissipation. Critical for calculating whether power transistors, voltage regulators, and power amp ICs will stay within safe operating temperatures. *(See: Ch 23)*

**Thiele-Small Parameters** — A set of electromechanical parameters that characterize a speaker driver's behavior: Fs (resonant frequency), Qts (total Q), Vas (equivalent volume), Xmax (maximum excursion), Sd (effective cone area), and others. Used for enclosure design. *(See: Ch 12)*

**Through-Hole** — Components with wire leads that pass through holes in the PCB and are soldered on the opposite side. Easier to hand-solder than SMD. *(See: Ch 3, Ch 31)*

**Time Alignment** — Adjusting the arrival time of sound from different speaker drivers so they reach the listening position simultaneously. Achieved physically (by offsetting drivers on the baffle) or electronically (using DSP delay). Critical at the crossover frequency where both drivers contribute. *(See: Ch 14, Ch 29)*

**Toroid (Toroidal Transformer)** — A transformer wound on a doughnut-shaped core. Lower stray magnetic field, lighter, and more efficient than EI transformers. Standard for quality audio power supplies. *(See: Ch 21)*

**Transconductance** — The relationship between input voltage and output current in an amplifying device. Measured in siemens (S) or millisiemens (mS). Key specification for vacuum tubes and FETs. *(See: Ch 18)*

**Transfer Function** — A mathematical expression (usually in the s-domain for analog or z-domain for digital) that describes the input-output relationship of a filter or system. Encodes both the frequency response and phase response. In audio DSP, filter coefficients are derived from the desired transfer function. *(See: Ch 27)*

**Transformer** — A device that transfers electrical energy between circuits through electromagnetic induction, typically changing voltage level. Power transformers, output transformers, and input transformers are all used in audio. *(See: Ch 18)*

**Transient** — A brief, sudden change in a signal — the attack of a drum hit, the pluck of a guitar string. Transient response measures how accurately a system reproduces these fast changes. *(See: Ch 12)*

**Transient Response** — How quickly and accurately a system responds to sudden signal changes (transients). Good transient response means fast rise time, minimal overshoot, and quick settling. Affected by bandwidth, phase response, and damping in both electronic and acoustic systems. *(See: Ch 12, Ch 18)*

**Transmission Line (Speaker)** — A speaker enclosure design where the back wave travels through a long, usually tapered, damped line. Combines characteristics of sealed and ported designs. *(See: Ch 13)*

**Treble** — High-frequency audio, generally above 2-4 kHz. *(See: Ch 5)*

**Tremolo** — A modulation effect that varies the amplitude (volume) of a signal at a regular rate. Not to be confused with vibrato (pitch variation). *(See: Ch 5)*

**Triode** — A vacuum tube with three elements: cathode, control grid, and plate (anode). The simplest amplifying tube. Used in preamp stages. 12AX7 contains two triode sections. *(See: Ch 18)*

**Triode Mode** — Operating a pentode or beam-power tube with its screen grid connected to the plate, effectively converting it into a triode. Reduces output power but produces a smoother distortion character with more even-order harmonics. Many tube amplifiers offer a triode/pentode switch. *(See: Ch 20)*

**True Bypass** — A switching method that directly connects the input jack to the output jack when an effect is disengaged, completely removing the effect circuit from the signal path. *(See: Ch 5)*

**Tube (Vacuum Tube/Valve)** — An electronic device that controls current flow through a vacuum using heated elements. Types: triode, pentode, beam tetrode. Used in guitar and hi-fi amplifiers. *(See: Ch 18, Ch 20)*

**Tuning Frequency** — The resonant frequency of a ported speaker enclosure, determined by port dimensions and cabinet volume. The frequency at which the port provides maximum bass output. *(See: Ch 13)*

**Turret Board** — A non-conductive board with metal turret posts that serve as solder terminals. A construction method for tube amplifiers that provides mechanical strength and easy modification. *(See: Ch 20)*

---

## U

**UL (Underwriters Laboratories)** — A safety certification organization. UL listing indicates a product has been tested and certified to meet safety standards. Not legally required for most consumer electronics in the US but provides credibility. *(See: Ch 42)*

**Ultralinear** — A tube amplifier output stage topology where the screen grids of the output pentodes are connected to taps on the output transformer primary, typically at 40-43% of the winding. A compromise between pentode mode (maximum power) and triode mode (minimum distortion), offering most of the power of pentode operation with distortion approaching triode levels. *(See: Ch 20)*

**Unbalanced Audio** — A single-conductor-plus-ground transmission method. Standard for guitar cables (1/4" TS) and consumer audio (RCA). More susceptible to noise pickup than balanced. *(See: Ch 33)*

---

## V

**Vas** — A Thiele-Small parameter representing the equivalent air volume of the driver's suspension compliance. A driver with high Vas needs a large enclosure. *(See: Ch 12)*

**Via** — A plated hole in a PCB that provides an electrical connection between copper layers. Allows traces to route between the top and bottom (or inner) layers of a multi-layer board. Via size affects impedance and current-carrying capacity. *(See: Ch 31)*

**Vibrato** — A modulation effect that varies the pitch (frequency) of a signal. Often confused with tremolo. *(See: Ch 5)*

**Virtual Ground** — A point in a circuit held at ground potential by feedback, without being directly connected to ground. Created by op-amp circuits and used as a signal reference in single-supply designs. *(See: Ch 6)*

**VituixCAD** — Free software for speaker crossover design and optimization. Models frequency response, impedance, and directivity. *(See: Ch 14, Ch 15)*

**Voice Coil** — The coil of wire attached to a speaker cone that moves in a magnetic gap to produce sound. Its diameter, winding, and material affect the driver's performance and power handling. *(See: Ch 12)*

**Voltage** — The electrical potential difference between two points, measured in volts (V). The "pressure" that pushes current through a circuit. *(See: Ch 0)*

**Voltage Divider** — Two resistors in series that divide a voltage proportionally. Vout = Vin x R2 / (R1 + R2). Fundamental building block of attenuators and bias circuits. *(See: Ch 0)*

**Voltage Regulator** — A circuit that maintains a constant output voltage despite changes in load current or input voltage. Linear regulators (78xx series) and switching regulators. *(See: Ch 1)*

---

## W

**Watt** — The SI unit of power. Named after James Watt. In audio: amplifier power output, speaker power handling, and resistor power dissipation are all measured in watts. *(See: Ch 0)*

**Wave Soldering** — A bulk soldering process for through-hole components where a populated PCB is passed over a wave of molten solder. The solder wicks into the through-hole joints simultaneously. Used in production environments for boards with many through-hole components. *(See: Ch 43)*

**Wavetable Synthesis** — A synthesis technique that stores single cycles of complex waveforms in a lookup table and reads through them at different speeds to produce different pitches. Multiple wavetables can be crossfaded to create evolving timbres. *(See: Ch 27)*

**Wet Signal** — The processed output of an effect. Contrast with "dry" (unprocessed) signal. Mix controls blend wet and dry. *(See: Ch 5)*

**White Noise** — Random noise with equal energy per frequency (flat spectrum). Sounds brighter/hissier than pink noise because higher octaves contain more total energy. Used as a test signal and in acoustic measurements. *(See: Ch 37)*

**Window Function** — A mathematical function applied to a block of samples before computing the FFT to reduce spectral leakage. Common windows include Hann (good general-purpose), Hamming (slightly narrower main lobe), and Blackman (better sidelobe suppression at the cost of frequency resolution). The choice of window affects the tradeoff between frequency resolution and amplitude accuracy. *(See: Ch 27)*

**WinISD** — Free software for modeling speaker enclosures. Takes Thiele-Small parameters as input and predicts frequency response, port velocity, cone excursion, and group delay for different enclosure designs. *(See: Ch 13)*

**Woofer** — A speaker driver designed to reproduce low frequencies, typically below 2-5 kHz. Larger cone diameter and longer excursion than tweeters. *(See: Ch 12)*

**Word Clock** — A digital timing signal that synchronizes multiple digital audio devices to a common sample rate. A square wave at the sample rate frequency (e.g., 44.1 kHz, 48 kHz). Ensures all devices in a chain convert samples at exactly the same instant. *(See: Ch 25)*

---

## X

**Xmax** — A Thiele-Small parameter representing the maximum one-way linear excursion of a speaker cone, measured in millimeters. Beyond Xmax, distortion increases rapidly. Determines maximum clean output at low frequencies. *(See: Ch 12)*

**XLR** — A professional audio connector with 3 or more pins, used for balanced audio connections and microphones. Locking mechanism prevents accidental disconnection. *(See: Ch 33)*

---

## Z

**Zobel Network** — A series resistor-capacitor network connected across a speaker driver or amplifier output to compensate for the rising impedance caused by voice coil inductance. Stabilizes the load for the crossover or amplifier. *(See: Ch 13, Ch 14)*

**Z-Transform** — The mathematical tool used to analyze discrete-time (digital) systems, analogous to the Laplace transform for continuous-time systems. Converts difference equations into algebraic transfer functions. Digital filter stability and frequency response are analyzed in the z-domain. *(See: Ch 27)*

---

*This glossary covers the core terminology used throughout the guide. For deeper explanations of any term, refer to the chapter noted in parentheses.*
