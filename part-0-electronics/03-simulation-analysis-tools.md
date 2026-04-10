<!--
  CHAPTER: 3
  TITLE: Simulation and Analysis Tools
  PART: 0 — Electronics for Audio People
  PREREQS: Chapter 2
  KEY_TOPICS: LTspice, Falstad, KiCad schematic capture, breadboard prototyping, audio probe, signal tracing, simulation workflow
  DIFFICULTY: Beg→Inter
  UPDATED: 2026-04-10
-->

# Chapter 3: Simulation and Analysis Tools

> **Part 0 — Electronics for Audio People** | Prerequisites: Chapter 2 | Difficulty: Beg→Inter

Here's a controversial opinion: you should simulate every circuit before you solder it. Not because simulation is perfect -- it's not. Real components have tolerances, parasitic properties, and temperature dependencies that simulators approximate at best. But simulation lets you make your mistakes for free. You can swap component values in seconds, see the waveform change in real time, and develop an intuition for circuit behavior that would take months of breadboarding alone.

Think of it like this: if schematics are the source code, simulation is the debugger. You can set breakpoints (probe points), step through execution (time-domain simulation), profile performance (frequency analysis), and inspect variables (node voltages and currents). You wouldn't write a complex software system without a debugger. Don't build a complex audio circuit without a simulator.

This chapter covers three simulation environments (LTspice, Falstad, and KiCad), plus the physical tools that bridge simulation and reality: breadboard prototyping, the audio probe, and signal tracing methodology. By the end, you'll have a complete workflow: design on paper (Chapter 2), verify in simulation (this chapter), prototype on a breadboard, and debug with an audio probe.

### In This Chapter
- LTspice: schematic entry, transient analysis, AC analysis, FFT, parameter sweeps
- Falstad Circuit Simulator: browser-based intuition builder
- KiCad schematic capture for documentation
- Breadboard prototyping technique
- Building and using an audio probe
- Signal tracing methodology
- The complete simulation-to-reality workflow

### Related Chapters
- [Ch 2: Reading Schematics and Datasheets](02-reading-schematics-datasheets.md) -- the schematics you'll simulate
- [Ch 4: Your Workbench, Tools, and Safety](04-workbench-tools-safety.md) -- the physical tools for building and measuring
- [Ch 1: Components and What They Do to Signals](01-components-and-signals.md) -- the component behaviors simulation reveals

---

## 1. LTspice: The Industry-Standard Free Simulator

### 1.1 What LTspice Is

**LTspice** (now maintained by Analog Devices, originally from Linear Technology) is a free SPICE circuit simulator. SPICE stands for "Simulation Program with Integrated Circuit Emphasis" -- it's the simulation engine that the entire electronics industry uses, from hobbyists designing guitar pedals to IC engineers designing microprocessors. LTspice puts a graphical schematic editor on top of the SPICE engine and gives you the whole thing for free, with no component limits, no board size restrictions, and no nag screens.

Why LTspice for audio? Because it gives you exactly the analyses you need:

- **Transient analysis**: See the actual waveform over time. What does the signal look like after it passes through your distortion circuit? How does a guitar signal clip as you increase the gain?
- **AC analysis**: See the frequency response (gain and phase vs frequency). What does your tone stack actually do to the signal across the audible spectrum? Where is the -3 dB point?
- **FFT**: See the harmonic content of a waveform. What harmonics does your clipping circuit add? Is it predominantly even or odd harmonics?
- **Parameter sweeps**: What happens if I change this resistor from 10k to 47k? Show me all the possibilities at once, overlaid on the same graph.
- **Noise analysis**: What's the theoretical noise floor of my preamp circuit?

### 1.2 Installation

**Windows**: Download from analog.com/ltspice. Run the installer. Done. It installs with a comprehensive library of Analog Devices / Linear Technology components.

**macOS**: Download the Mac version from the same page. It works but the interface is less polished than the Windows version -- the native Mac UI feels a bit clunky. Some keyboard shortcuts differ. The simulation engine is identical.

**Linux**: LTspice doesn't have a native Linux version, but it runs well under Wine. Alternatively, consider:
- **ngspice** (the open-source SPICE engine) with KiCad's built-in simulation integration
- **Qucs-S** for a graphical SPICE front-end
- **TINA-TI** (another free SPICE simulator from TI, Windows-only but runs under Wine)

### 1.3 Your First Simulation: A Simple RC Low-Pass Filter

Let's simulate the simplest meaningful audio circuit: a resistor-capacitor low-pass filter. This is the circuit behind a guitar tone knob.

**Step 1: Create the schematic**

1. Open LTspice and create a new schematic (File > New Schematic).
2. Place a voltage source: right-click the schematic > Component (or press F2) > search for "voltage" > place it on the left side.
3. Place a resistor: press F2 > search for "res" > place it horizontally to the right of the voltage source.
4. Place a capacitor: press F2 > search for "cap" > place it vertically from the right end of the resistor down to ground.
5. Place ground symbols: press F2 > search for GND (or press G) > connect to the bottom of the voltage source and the bottom of the capacitor.
6. Wire everything: press F3 (or W) to draw wires connecting the components.

Your schematic should look like:

```
   V1 ─── R1 ──┬── Output (label this node)
   │            │
   │           C1
   │            │
  GND         GND
```

**Step 2: Set component values**

Right-click the resistor and set its value to 10k (10,000 ohms).
Right-click the capacitor and set its value to 10n (10 nanofarads = 0.01 μF).

The cutoff frequency of this filter is:
```
f_c = 1 / (2π × R × C) = 1 / (2π × 10000 × 0.00000001) ≈ 1592 Hz
```

So this filter should start rolling off around 1.6 kHz -- cutting the treble, like a tone knob turned about halfway.

**Step 3: Configure the voltage source for AC analysis**

Right-click the voltage source. In the properties, set:
- DC value: 0
- Small Signal AC Analysis: AC Amplitude = 1, AC Phase = 0

The AC amplitude of 1V makes gain calculations trivial -- the output voltage in volts IS the gain (since gain = Vout/Vin and Vin = 1V).

**Step 4: Run AC Analysis**

Click Simulate > Edit Simulation Cmd. Choose the AC Analysis tab:
- Type of sweep: Decade
- Number of points per decade: 100 (gives smooth curves)
- Start frequency: 10 Hz
- Stop frequency: 100000 Hz (100 kHz)

Click OK. A SPICE directive appears on your schematic: `.ac dec 100 10 100k`

Run the simulation (click the running man icon or Simulate > Run).

**Step 5: Read the results**

Click on the output node (the junction of R1 and C1). LTspice plots the frequency response on two panels: magnitude (in dB, top) and phase (in degrees, bottom).

You should see:
- Flat response (~0 dB) from 10 Hz up to about 500 Hz
- Response starts to bend around 1 kHz
- The -3 dB point at approximately 1592 Hz (confirming our calculation)
- Above 1592 Hz, the response drops at -20 dB per decade (-6 dB per octave) -- the characteristic slope of a first-order filter
- Phase starts at ~0 degrees at low frequencies, reaches -45 degrees at the cutoff frequency, and approaches -90 degrees at high frequencies

**Congratulations**: you've just simulated a tone control and proven with both math and simulation that it does what Chapter 1 predicted.

### 1.4 Transient Analysis: Seeing Waveforms in Time

Now let's watch what happens in the time domain. Change the voltage source to output a real signal.

Right-click the voltage source and click "Advanced":
- Function: SINE
- DC offset: 0
- Amplitude: 1 (1V peak)
- Freq: 1000 (1 kHz)

Change the simulation command: go to Simulate > Edit Simulation Cmd > Transient tab:
- Stop time: 5m (5 milliseconds -- enough to see several cycles of a 1 kHz wave)
- (leave other fields at defaults)

This creates the directive: `.tran 5m`

Run it. Click on the output node. You'll see the output sine wave. Now hover over the input and click to add the input trace. At 1 kHz (below the cutoff), the output is nearly the same amplitude as the input -- the filter barely affects it.

Now change the frequency to 10 kHz (right-click the voltage source, change Freq to 10000) and re-run. The output amplitude is significantly reduced -- the filter is working. At 10 kHz (about 2.6 octaves above the 1.6 kHz cutoff), the attenuation should be about -16 dB (roughly 1/6 of the input amplitude).

### 1.5 FFT: Seeing Harmonics and Distortion

The FFT (Fast Fourier Transform) converts a time-domain waveform into a frequency-domain spectrum -- showing you which frequencies are present and at what amplitudes. This is essential for analyzing distortion circuits.

Set up a transient simulation with a 1 kHz sine wave input (1V amplitude). Run it with a long enough time window (at least 10 ms, preferably 50-100 ms for better resolution). After running:

1. Click on the output node to display the waveform
2. Right-click on the plot area and select "View > FFT" (or press Ctrl+F)
3. Set the number of data points to a power of 2 (4096, 8192, or 16384) for clean results
4. Check "Windowed" (Hanning window reduces spectral leakage)

For a clean sine wave through a linear circuit (like our RC filter), the FFT shows a single spike at 1 kHz with everything else at least 100 dB below. This confirms the circuit isn't adding harmonics.

Now the interesting test: add clipping diodes to the circuit (two 1N4148 diodes in antiparallel from the output to ground) and run the FFT again. You'll see harmonics appear at 2 kHz, 3 kHz, 4 kHz, and beyond. The relative heights of these harmonics tell you exactly what kind of distortion the circuit produces:

- Strong odd harmonics (3rd, 5th, 7th) with weak even harmonics: symmetrical clipping
- Mix of odd and even harmonics: asymmetrical clipping or a non-symmetric circuit
- Primarily 2nd and 3rd harmonics: gentle, musical distortion
- Strong harmonics all the way out to the 10th and beyond: hard, aggressive clipping

We'll use this technique extensively in the pedal chapters to analyze and design distortion circuits.

### 1.6 Parameter Sweeps: Exploring "What If?"

This is where simulation truly shines over physical prototyping. Instead of asking "what does this circuit do with R1 = 10k?", you can ask "what does this circuit do for every value of R1 from 1k to 100k?"

Use the `.step` directive. In your RC filter example:

1. Right-click R1 and change its value to `{Rval}` (with curly braces -- this makes it a parameter)
2. Add a SPICE directive to the schematic: `.step param Rval 1k 100k 10k`
3. Run the AC analysis

LTspice runs the simulation multiple times, once for each value of Rval (1k, 11k, 21k, ... 91k, 100k), and overlays the results on the same graph. You see a family of curves showing how the cutoff frequency shifts as the resistance changes.

For a tone knob simulation, you'd parameterize the pot value to see how the frequency response changes as you "turn the knob." The result is a family of curves showing the full range of the tone control -- something that would take hours of breadboard measurements but takes seconds in simulation.

**Parameter sweep of capacitor values**:
```
.step param Cval list 10n 22n 47n 100n
```
This runs the simulation for four specific cap values -- useful for comparing standard capacitor values in a tone circuit.

**Logarithmic sweep** (good for resistors and frequencies):
```
.step dec param Rval 1k 100k 5
```
This steps Rval through 5 values per decade from 1k to 100k on a logarithmic scale.

> **What happens if...** you don't have a SPICE model for the exact component you're using? Use a generic model with similar specifications. A generic "NPN BJT" model with the right hFE and transition frequency will get you 90% of the way there. SPICE simulation is about understanding *behavior and trends*, not predicting exact values to three decimal places. The breadboard is where you fine-tune. The rare exception is distortion circuits where the exact clipping characteristics matter -- there, you want accurate diode models (which are usually well-defined by just a few parameters).

### 1.7 Adding Third-Party SPICE Models

LTspice includes models for many Analog Devices / Linear Technology parts, but for audio work you'll need models for other manufacturers' components.

**Where to find models**:
- **TI.com**: SPICE models for all TI op-amps (TL072, NE5532, OPA series, etc.)
- **ONsemi.com**: Transistor models (2N3904, 2N5088, MPSA18, etc.)
- **Manufacturer product pages**: Most semiconductor manufacturers provide SPICE models on the product page for each part

**How to add them**:
1. Download the `.lib` or `.sub` file
2. Place it in LTspice's `lib/sub` directory (or anywhere you'll remember)
3. In your schematic, add a SPICE directive: `.lib path/to/model.lib`
4. Add a component symbol and set the SPICE model name in its properties

For guitar pedal simulation, the most commonly needed external models are: TL072/TL071, JRC4558, NE5532, 2N3904, 2N5088, 2N5457 (JFET), J201 (JFET), 1N4148, 1N34A (germanium), and various LEDs.

### 1.8 Advanced LTspice Techniques for Audio

**WAV file input**: LTspice can use a WAV file as a voltage source, letting you simulate with actual guitar, voice, or music signals:
```
V1 input 0 wavefile="guitar_riff.wav"
```
The WAV file must be 16-bit mono. The voltage values correspond to the sample values scaled by the file's bit depth.

**Multi-tone signals for IMD testing**: To test intermodulation distortion (how two frequencies interact through a nonlinear circuit), use two sine waves:
```
V1 input 0 SINE(0 0.5 1000)
V2 input2 0 SINE(0 0.5 1500)
```
Sum them through mixing resistors and feed the combined signal into your circuit. The FFT will reveal intermodulation products at sum and difference frequencies (2500 Hz, 500 Hz, etc.) that aren't in either input signal. These IM products are a measure of nonlinear distortion.

**Noise analysis**: LTspice can predict a circuit's noise floor:
```
.noise V(output) V1 dec 100 10 100k
```
This calculates the noise spectral density at the output, referred to the input source V1, across the audio band. The result tells you the theoretical signal-to-noise ratio of your circuit.

**Monte Carlo analysis**: Real components have tolerances. Monte Carlo runs the simulation many times with randomized component values within their tolerance to show the range of possible performance:
```
.step param run 1 50 1
.param R1val mc(10k, 0.05)
```
The `mc()` function generates a random value within the specified tolerance (5% here) for each run. After 50 runs, you see the spread of performance -- essential for production design where every build needs to work, not just the prototype with hand-selected parts.

---

## 2. Falstad Circuit Simulator: Interactive Intuition

### 2.1 What Falstad Is

The **Falstad Circuit Simulator** (falstad.com/circuit/) is a browser-based, real-time circuit simulator. No installation required. It's less precise than LTspice but far more intuitive -- you can see animated current flow (moving dots along the wires), voltage colors (green for high, gray for ground, red for negative), and waveforms that update instantly as you change component values by right-clicking and adjusting sliders.

### 2.2 Why Falstad Matters for Learning

Falstad excels at building intuition through visual and interactive feedback:

**Current animation**: You can *see* current flowing through the circuit. In an AC circuit, you watch the dots oscillate back and forth. In a filter, you see current preferring one path over another at different frequencies. When you add a capacitor to ground, you watch current get "diverted" through it at high frequencies. This visual feedback is extraordinarily useful for internalizing circuit behavior that's otherwise abstract.

**Instant feedback**: Change a capacitor value and immediately see the waveform change on the virtual oscilloscope trace. No re-running simulations, no waiting. It's the "hot reload" of circuit simulation.

**Pre-built circuits**: Falstad includes a library of hundreds of pre-built circuits, organized by category. The "Audio" category includes filters, amplifiers, oscillators, and mixers. You can load them, tweak values, and observe behavior.

**Interactive exploration**: Right-click any component to get a slider that adjusts its value in real-time. Watch the circuit respond as you drag the slider. This "play" mode is how intuition is built -- you develop a feel for "if I increase this cap, the filter moves lower."

### 2.3 Recommended Falstad Exercises

To build your audio electronics intuition, work through these exercises:

1. **RC low-pass filter**: Build it, add a scope trace on the output, and sweep the input frequency using the voltage source's frequency slider. Watch the output amplitude decrease at high frequencies. Observe the current animation: at low frequencies, most current flows through the resistor to the output; at high frequencies, most current is diverted through the capacitor to ground.

2. **RC high-pass filter**: Swap the resistor and capacitor positions (cap in series, resistor to ground). Now bass is attenuated and treble passes. Watch how the current behavior reverses.

3. **Voltage divider**: Build one with two resistors. Change the ratio and see the output voltage change in real-time. Then replace one resistor with a capacitor and observe: the "division ratio" becomes frequency-dependent. You've just built a filter from first principles.

4. **Diode clipping**: Put a sine wave through a resistor and add antiparallel diodes from the output to ground. Watch the waveform tops get flattened (clipped). Change the input amplitude with a slider and observe the transition from clean to distorted. Switch to a single diode and see asymmetric clipping.

5. **Transistor amplifier**: Build a common-emitter stage with bias resistors. Adjust the bias with sliders and watch the output waveform change in real-time -- from clipped (insufficient bias) to clean amplification (correct bias) to saturated clipping (too much bias). This exercise alone is worth hours of textbook reading.

6. **Op-amp gain stage**: Build an inverting amplifier and adjust the feedback resistor. Watch the output amplitude change. Add diodes in the feedback path and observe how clipping appears at the output as gain increases.

### 2.4 Limitations

Falstad is not suitable for precision design work:
- Component models are simplified (ideal op-amps, simplified transistors)
- No support for real IC SPICE models
- Noise analysis isn't supported
- Complex circuits can slow down the browser
- Results shouldn't be used for final design validation
- No FFT capability (can't analyze harmonic content)

Use Falstad for learning and quick exploration. Use LTspice (or physical prototyping) for design validation.

---

## 3. KiCad Schematic Capture for Documentation

### 3.1 Why Capture Schematics Digitally

Even if you design on paper and simulate in LTspice, capturing your final schematic in KiCad is valuable because:

1. **Documentation**: A clean KiCad schematic is the definitive record of your circuit. The file format is text-based and version-controllable with git.
2. **Path to PCB**: When you're ready to make a PCB (Part 5), the KiCad schematic feeds directly into the PCB layout tool via netlist export.
3. **BOM generation**: KiCad can generate a Bill of Materials -- a complete parts list with quantities, values, and part numbers. Essential for ordering components and tracking build costs.
4. **ERC**: The Electrical Rules Check catches wiring errors before you build -- unconnected pins, shorted power rails, missing power connections.
5. **Sharing**: KiCad files are the lingua franca of the open-source hardware community. Sharing your design as a KiCad project lets others build on your work.

### 3.2 KiCad Schematic Entry Workflow

A typical KiCad schematic workflow:

1. **Create a new project**: This creates a `.kicad_pro` project file and associated schematic and PCB files.
2. **Place components**: 'A' to add from library. Search by name, description, or keyword.
3. **Set values**: Double-click each component to set its value and reference designator.
4. **Wire connections**: 'W' to draw wires between component pins. Wires snap to pins automatically.
5. **Add power symbols**: 'P' for power symbols (VCC, GND, +9V). These create global net connections.
6. **Add net labels**: 'L' for named nets.
7. **Add notes**: 'T' for text annotations explaining design decisions.
8. **Annotate**: Tools > Annotate automatically numbers all components (R1, R2, C1, C2...).
9. **ERC**: Inspect > Electrical Rules Check to find errors.
10. **Generate netlist**: For PCB layout or for export to LTspice simulation.

### 3.3 Schematic Hygiene Tips

- **One function block per area**: Keep the input stage components together, the gain stage together, etc. Use boxes or dotted lines to visually group blocks.
- **Consistent wire routing**: Horizontal and vertical wires only. No diagonal wires. Keep wires short and direct.
- **Pin-to-pin wiring**: Every wire should connect to a component pin at each end. "Hanging" wires (wires that end without connecting) are errors that ERC will flag.
- **Generous spacing**: Don't crowd components. A readable schematic is more valuable than a compact one.
- **Component alignment**: Align similar components in rows or columns for visual clarity.

---

## 4. Breadboard Prototyping Technique

### 4.1 What a Breadboard Is

A **solderless breadboard** (or "protoboard") is a plastic board with rows of interconnected holes. You push component leads and jumper wires into the holes to build temporary circuits without soldering. It's the fastest way to test a circuit in the real world.

The standard breadboard layout:
```
  + power bus rail ─────────────────────────  (connected horizontally across the board)
  - ground bus rail ────────────────────────  (connected horizontally across the board)

  a b c d e     f g h i j                     (component area)
  1 ● ● ● ● ●     ● ● ● ● ●                 (holes in a-e are connected vertically)
  2 ● ● ● ● ●     ● ● ● ● ●                 (holes in f-j are connected vertically)
  3 ● ● ● ● ●     ● ● ● ● ●                 (center gap separates the two halves)
  ...                                          (DIP ICs straddle this gap)
  30 ● ● ● ● ●    ● ● ● ● ●

  + power bus rail ─────────────────────────
  - ground bus rail ────────────────────────
```

Key rules:
- Power bus rails (top and bottom of each section) are connected horizontally across the board
- In the component area, holes in each row (a-e or f-j) are connected vertically within that 5-hole group
- The center gap separates the two halves -- a DIP IC straddles this gap with its pins on each side
- There is NO connection between the left half (a-e) and right half (f-j) of a row

### 4.2 Layout Strategy

Good breadboard layout follows these principles:

**Power first**: Connect your power supply to the bus rails before anything else. Run +V to one rail and GND to the other. Color-code: red wire for positive, black for ground. If you need a virtual ground (V/2), create it with a voltage divider and buffer, and dedicate a separate row or rail to it.

**IC placement**: Place ICs straddling the center gap. Pin 1 is typically at the top-left (identified by a dot, notch, or both on the IC package). Each pin now has 4 available connection holes (a-d or f-i) for wiring.

**Signal flow**: Maintain left-to-right signal flow, mirroring the schematic. Input on the left, output on the right. This makes it easier to trace the circuit visually and correlate with the schematic.

**Keep leads short**: Long jumper wires act as antennas, picking up noise and potentially causing oscillation in high-gain circuits. Use the shortest practical jumper for each connection, especially in signal paths and between gain stages.

**Bypass capacitors**: Place a 100 nF ceramic capacitor from each IC's power pin (VCC) to the nearest ground point, as physically close to the IC as possible. This is non-negotiable. Skipping bypass caps is the single most common cause of breadboard oscillation, motorboating (low-frequency oscillation that sounds like a motorboat engine), and noise.

**Star grounding on the breadboard**: For high-gain circuits, avoid routing high-current ground returns (from the output stage or power supply) through the same ground bus segment as sensitive input stages. Use separate jumpers to a common ground point if needed.

### 4.3 Common Breadboard Mistakes

| Mistake | Symptom | Fix |
|---|---|---|
| Missing bypass caps | Oscillation, squealing, motorboating, random noise | Add 100 nF caps at every IC power pin, close to the IC |
| Long jumper wires | Noise pickup, oscillation at high gain | Shorten leads, reroute for minimum length |
| Power bus breaks | Circuit works on one side of board, not the other | Some boards have a break in the middle of the power bus rails -- bridge with a jumper wire |
| Wrong IC pin connections | Circuit doesn't work at all, or works strangely | Double-check pinout against datasheet with the IC in hand |
| Reversed polarity on electrolytics | Cap heats up, may leak, swell, or pop | Check polarity markings before powering on |
| Breadboard contact issues | Intermittent behavior, crackling sounds | Breadboard contacts wear out with use -- try different holes, clean leads, or use a new breadboard |
| 5-hole row confusion | Short between components that shouldn't connect | Remember: holes a-e in a row are all connected. Two component leads in the same row ARE connected |
| Forgetting the center gap | IC pins shorted in pairs | The IC must straddle the center gap so each pin is independent |

### 4.4 When to Move Beyond the Breadboard

Breadboards are prototyping tools, not final build platforms. Move to a PCB (or at minimum, perfboard/stripboard) when:

- The circuit is finalized and you want a permanent build
- You're experiencing noise or stability issues that might be breadboard-related
- The circuit handles significant current (power amplifiers)
- You plan to put it in an enclosure for regular use
- You're building something you'll sell

Breadboards work well for: pedal circuits (low current, audio frequencies), preamp circuits, filter experiments, op-amp evaluations, and any circuit where you're still iterating on the design.

---

## 5. Building and Using an Audio Probe

### 5.1 What an Audio Probe Is

An **audio probe** is a simple tool that lets you listen to the audio signal at any point in a circuit. It's essentially a DC-blocking capacitor, a current-limiting resistor, and an audio jack, wired to a probe tip. You plug the jack into a small amplifier or headphones, touch the probe to any node in the circuit, and *hear* what the signal sounds like at that point.

This is the audio equivalent of `console.log()` or `printf()` debugging. Instead of staring at voltage readings on a multimeter (which tell you if a signal exists but not what it sounds like), you hear the actual audio. Is it clean? Distorted? Humming? Silent? Too loud? Too thin? The answer is immediate and intuitive.

The audio probe is arguably the most important debugging tool for audio circuits. Build one before you build your first circuit.

### 5.2 Building an Audio Probe

The circuit is trivially simple:

```
  Probe tip ──── C1 (10 nF) ──── R1 (10kΩ) ──── Tip (of output jack)
                                                   │
                                       Sleeve (of output jack) ── Ground clip
```

Components:
- **C1: 10 nF (0.01 μF) capacitor**: Blocks DC, passes audio. Film type preferred, rated for at least 50V (or 630V if you'll ever probe tube amp circuits). The value isn't critical: 1 nF to 100 nF all work. Smaller values reduce bass (which can be useful for avoiding loud thumps when probing DC-biased nodes). Larger values let more bass through for full-range monitoring.
- **R1: 10 kΩ resistor**: Limits current and provides some isolation so the probe doesn't significantly load the circuit under test. 1 kΩ gives more signal but loads the circuit more. 100 kΩ loads less but the signal is quieter. 10 kΩ is a good compromise.
- **Output jack**: 1/4" mono or 3.5 mm, depending on your monitoring setup.
- **Probe tip**: A rigid wire for touching circuit nodes. A test probe tip from a multimeter, a stiff piece of 22 AWG solid wire, or even a sewing needle soldered to the wire.
- **Ground clip**: An alligator clip on a lead for connecting to the circuit's ground reference.

Build it on a small piece of perfboard, or "dead-bug" style (components soldered in the air between the probe tip wire, a ground wire, and the jack). House it in a pen-shaped enclosure, a small project box, or just leave it as a bare assembly wrapped in heat-shrink tubing.

Total cost: about $3-5 in parts. Total build time: 15-30 minutes.

### 5.3 Using the Audio Probe

**Monitoring setup**: Connect the probe's output to a small practice amp (a battery-powered amp is ideal -- no ground loop issues), a headphone amplifier, or even directly to powered computer speakers. Set the monitoring volume LOW initially -- you don't know how hot the signal will be at each probe point, and a sudden loud signal in headphones is painful and potentially damaging to your hearing.

**Probing technique**:

1. **Clip the ground lead** to the circuit's ground (the ground bus on the breadboard, or a chassis ground point on a PCB).

2. **Power up the circuit** under test.

3. **Feed a signal into the circuit input**. This can be a guitar, a signal generator, or a phone playing music through a cable. A sustained tone (like a keyboard pad or a signal generator sine wave) is easiest to analyze, but real music or guitar is more revealing of dynamic behavior.

4. **Touch the probe tip to the circuit's input node** -- right where the signal enters, after the input jack. You should hear the clean input signal in your monitoring system. This confirms your probe and monitoring setup work.

5. **Move the probe to each successive stage**: after the first coupling cap, at the output of the first gain stage, after the clipping stage, after the tone control, after the volume pot, at the output. Listen at each point.

6. **At each point, ask yourself**:
   - Is the signal present? (If not, you've found a break in the signal path.)
   - Is it louder or quieter than the previous stage? (Gain stage should be louder; tone stack and volume should be quieter.)
   - Is it clean or distorted? (Should match the circuit's intended behavior at that point.)
   - Is there hum or buzz? (Indicates grounding or power supply issues.)
   - Is there hiss? (Normal at high gain, but excessive hiss indicates a noise problem.)
   - Does it sound thin or muffled? (May indicate a coupling cap issue or impedance problem.)

The audio probe turns circuit debugging from an abstract measurement exercise into a sensory experience. Combined with a multimeter for DC voltage checks, it's all you need to debug any audio circuit.

### 5.4 Safety Note for High-Voltage Work

> **What happens if...** you probe a node that's at a high DC voltage (like the plate of a tube in an amplifier at 300V DC)? The probe's capacitor blocks the DC from reaching your monitoring equipment and ears -- but the capacitor must be rated for that voltage. A 10 nF ceramic cap rated for 50V will likely fail (and possibly explode) at 300V. For tube amp work, use a capacitor rated for at least 630V (a film polypropylene cap in this voltage range costs about $0.50). Also: the probe tip is now at high DC voltage. Don't touch it. Don't let it touch other nodes. Tube amp probing requires the same safety precautions as any tube amp work (Chapter 4).

---

## 6. Signal Tracing Methodology

### 6.1 The Binary Search of Audio Debugging

Signal tracing is a systematic debugging technique: you follow the signal through the circuit, stage by stage, to find exactly where the problem occurs. It's the "binary search" of debugging -- at each step, you narrow down the problem location.

The principle: **if the signal is good here but bad there, the problem is between here and there**.

This sounds obvious, but it's remarkably powerful. With a 6-stage pedal circuit, binary search finds the problem in at most 3 probe points (log2(6) ≈ 2.6). Without a systematic approach, people randomly probe, swap components, and re-solder joints for hours. With signal tracing, you isolate the fault in minutes.

### 6.2 The Signal Tracing Procedure

**Step 1: Verify the input**
Touch the probe to the circuit's input (right after the input jack, before any components). Is the signal present? Is it clean? Is it at the expected level?

If no signal at the input: the problem is upstream -- broken cable, dead signal source, bad solder joint on the input jack, or the bypass switch isn't routing to the circuit.

**Step 2: Probe the midpoint**
Find the approximate middle of the signal path (e.g., between the gain stage and the tone control) and probe there. Is the signal present and correct?

If yes: the problem is in the second half. Move your probe to the 75% point.
If no: the problem is in the first half. Move your probe to the 25% point.

**Step 3: Narrow down**
Continue halving the search space until you've identified the specific stage (or specific connection) where the signal fails or degrades.

**Step 4: Identify the failure mode**

Once you've found the problematic stage, categorize the failure:

| What You Hear at the Probe | Likely Cause | Debugging Action |
|---|---|---|
| No signal at all | Open circuit: broken wire, bad solder joint, dead component | Check continuity with multimeter, re-solder joints, check component orientation |
| Signal present but very quiet | Wrong bias, low gain, attenuated by wrong component value | Measure DC voltages at the stage, compare to schematic expectations |
| Signal present but distorted | Wrong bias causing clipping, component failure, power supply issue | Measure bias voltages, check power supply, verify component values |
| 60/120 Hz hum | Ground loop, poor power supply filtering, poor layout | Check grounding, add power supply filtering, reroute wires |
| High-pitched whine or oscillation | Parasitic feedback, missing bypass cap, unstable op-amp | Add bypass caps, shorten wires, check for coupling between input and output |
| Signal present but thin/tinny | Coupling cap too small (cutting bass), impedance mismatch | Calculate coupling cap cutoff frequency, check impedances |
| Signal present but muffled | Parasitic capacitance, cable loading, wrong capacitor value | Check cap values, reduce stray capacitance |
| Signal cuts in and out | Loose connection, bad solder joint, worn breadboard contacts | Wiggle components while probing to find the intermittent connection |

### 6.3 Voltage Measurements to Complement Audio Probing

While the audio probe tells you what the signal *sounds like*, a multimeter tells you what the DC conditions *are*. Use both together:

1. **Measure DC voltages at transistor pins**: Base, collector, emitter. Compare to expected values.
   - NPN BJT in active mode: Vbe ≈ 0.6V, Vce should be between 1V and (Vcc - 1V)
   - If Vce ≈ 0V: transistor is saturated (turned fully on) -- check bias
   - If Vce ≈ Vcc: transistor is cut off (turned fully off) -- check bias

2. **Measure DC voltages at op-amp pins**: The output should sit at the virtual ground reference (Vcc/2 for single-supply circuits, ~0V for dual-supply).
   - If the output is pinned to one supply rail: the feedback loop isn't working. Check for open feedback resistors, swapped inputs, or missing virtual ground reference.

3. **Measure the power supply voltage** at the circuit: Should be the expected value (9V, 12V, etc.). If it's significantly lower, check for shorts drawing excessive current.

### 6.4 Common Debugging Patterns

**The circuit worked last night but not today**:
- Temperature-sensitive components (germanium transistors) drifted
- A loose breadboard connection shifted
- Battery dying (voltage dropping below minimum for the circuit)
- Condensation (if you moved from cold to warm environment)

**The circuit works but has a constant background hiss**:
- All analog circuits have some noise -- a small amount of hiss is normal at high gain
- If excessive: check whether the first gain stage is amplifying power supply noise
- Check for a noisy component (try temporarily shorting the input to ground -- if hiss disappears, the noise is from the signal source; if it persists, it's internal)
- Use lower-noise components at the input stage where noise has the most gain ahead of it

**The circuit oscillates** (makes a constant tone, squeal, or motorboating that isn't the input signal):
- Missing bypass capacitors on IC power pins (most common cause!)
- Too much gain in the feedback loop at some frequency
- Input and output wires running parallel (stray coupling)
- Breadboard parasitic capacitance coupling between high-gain stages
- Op-amp driving a capacitive load without a series output resistor

**The circuit sounds "different" from the simulation**:
- This is expected to some degree -- simulations use ideal models
- Check that you used the correct component values (470 nF where 47 nF was intended -- a factor of 10 error is the most common assembly mistake)
- Verify breadboard wiring against the schematic, node by node
- Check for stray capacitance on breadboard rows (10-20 pF per row is common)

---

## 7. The Complete Simulation-to-Reality Workflow

### 7.1 The Step-by-Step Process

Here's the workflow we'll use throughout the rest of this guide:

1. **Design on paper**: Draw a block diagram of what you want (gain, filter, distortion, etc.). Choose a topology from Chapter 1 or from datasheets/app notes (Chapter 2).

2. **Calculate component values**: Use the equations from Chapters 0-1 to choose resistor, capacitor, and other values. Document your calculations -- when something doesn't work, you need to trace back to your assumptions.

3. **Simulate in LTspice**: Enter the schematic, run AC analysis (frequency response), transient analysis (waveform shape), and FFT (harmonic content). Verify the circuit does what you designed it to do.

4. **Explore in simulation**: Use parameter sweeps to optimize. What if the gain resistor is 20k instead of 10k? What if the tone cap is 33 nF instead of 22 nF? Find the sweet spot before you touch a soldering iron.

5. **Build on breadboard**: Transfer the simulated circuit to a breadboard. Use the same component values. Don't forget bypass caps on every IC power pin.

6. **Test with audio probe**: Feed a signal in and trace through the circuit with the audio probe. Does each stage sound right? Compare to what the simulation predicted.

7. **Measure with multimeter** (and oscilloscope if available): Verify DC voltages and AC signal levels. Compare to simulation.

8. **Iterate**: If something doesn't match expectations, check wiring, check component values, re-simulate with adjusted parameters. Repeat until satisfied.

9. **Document in KiCad**: Capture the final, tested schematic in KiCad. This becomes the definitive record and the starting point for PCB layout.

### 7.2 What Simulation Can and Can't Tell You

**Simulation is reliable for**:
- Verifying circuit topology (does this filter architecture work?)
- Comparing alternatives (which clipping configuration produces more even harmonics?)
- Calculating frequency response, gain, and phase accurately
- Predicting clipping behavior and harmonic content
- Exploring parameter sensitivity (how much does performance change with 5% component tolerance?)
- Catching design errors before building (wrong polarity, wrong signal path, incorrect gain staging)

**Simulation is NOT reliable for**:
- Predicting exact noise levels (heavily layout-dependent, hard to model stray coupling)
- Modeling parasitic effects of real layouts (stray capacitance, ground loops, EMI pickup)
- Predicting subjective audio quality ("does it sound good?" requires ears)
- Modeling mechanical effects (microphonics, vibration sensitivity)
- Replacing physical testing (always build and verify before committing to a PCB)

### 7.3 When to Skip Simulation

Not every circuit needs full simulation:

- **Copying a known, published design** (like a well-documented Tube Screamer clone) -- the design is already validated. Go straight to building.
- **Simple modifications** to a working circuit (changing a cap value to shift a tone control) -- you can calculate the new cutoff frequency by hand and go to breadboard.
- **Emergency repairs** -- skip simulation, use the audio probe and multimeter to find the fault.
- **One-component changes** during breadboard iteration -- just swap the part and listen.

Simulation is most valuable when you're designing something new, combining blocks in untested ways, or trying to understand *why* a circuit behaves the way it does.

---

## 8. Simulation Across the Guide

The simulation tools from this chapter will appear throughout every domain tower in the guide:

| Domain | Primary Sim Tool | What You'll Simulate |
|---|---|---|
| Guitar Pedals (Part 1) | LTspice | Gain stages, clipping circuits, tone controls, complete pedal designs |
| Speakers (Part 2) | WinISD, VituixCAD | Enclosure tuning, crossover networks, system frequency response |
| Amplifiers (Part 3) | LTspice | Power stages, feedback loops, output impedance, thermal behavior |
| Digital Audio (Part 4) | CamillaDSP, REW | DSP crossovers, room correction, digital EQ, measurement |
| PCB Design (Part 5) | KiCad | Schematic-to-PCB flow, DRC, manufacturing output |
| System Design (Part 6) | Various | Full signal chain analysis, impedance matching, gain staging |

The skills you've learned here -- running AC analysis, interpreting frequency response plots, using parameter sweeps, building on breadboards, probing with an audio probe, signal tracing -- are the skills you'll use in every chapter that follows. They are the core practical skills of audio electronics, and they transfer to every domain.

---

## 9. Exercises: Simulation and Prototyping Practice

### 9.1 LTspice Exercises

Complete these in order -- each builds on the previous:

**Exercise 1: Verify a coupling cap**
Build an RC high-pass filter in LTspice with C = 100 nF and R = 1 MΩ (simulating a coupling cap into a high-impedance input). Run AC analysis from 1 Hz to 100 kHz. Verify that:
- The -3 dB point is at approximately 1.6 Hz
- The signal is essentially flat across the entire audio band (20 Hz - 20 kHz)
- The phase shift is negligible above 10 Hz

Now change C to 1 nF and re-run. Note how the -3 dB point jumps to 159 Hz -- you're now cutting audible bass. This demonstrates why coupling cap selection matters.

**Exercise 2: Simulate a guitar tone knob**
Build a low-pass filter: signal source → 250kΩ resistor (representing the tone pot) → capacitor to ground. Use `.step param Cval list 10n 22n 47n 100n` to sweep through common tone cap values. Run AC analysis and observe how larger caps produce darker tone (lower cutoff frequency).

**Exercise 3: Simulate diode clipping**
Build a simple clipping circuit: 1 kHz sine wave (1V amplitude) → 10kΩ resistor → output node. Add two 1N4148 diodes in antiparallel from the output to ground. Run transient analysis (.tran 5m) and observe the clipped waveform. Then run FFT and note the harmonics.

Now change one diode to a 1N34A germanium (or use a .model statement with different parameters) and re-run. Observe asymmetric clipping and the appearance of even harmonics in the FFT.

**Exercise 4: Op-amp gain stage**
Build a non-inverting amplifier: TL072 op-amp, Rf = 100kΩ, Rg = 10kΩ (gain = 11, about 21 dB). Add a 1V, 1 kHz sine wave input. Run transient analysis and verify the output is approximately 11V peak -- wait, that exceeds a ±15V supply? If you're using ±15V rails, the output will be ~11V, clean. If you're on a single 9V supply with 4.5V virtual ground, the output clips because ±4.5V × 11 = ±49.5V peak, but the op-amp can only swing to about ±3.5V. Reduce the input amplitude until the output is clean -- this teaches headroom management.

**Exercise 5: Parameter sweep a tone stack**
If you're ambitious, build the Fender tone stack in LTspice (there are published SPICE netlists online). Sweep the "mid" pot from 0% to 100% and observe the frequency response family of curves. This visualization explains more about how a tone stack works than any amount of text.

### 9.2 Falstad Exercises

Open falstad.com/circuit/ and work through:

1. Build an RC low-pass filter. Observe the current animation: at low frequencies, current flows mostly through the resistor to the output; at high frequencies, current is diverted through the capacitor to ground.

2. Build a voltage divider with two resistors. Replace one with a capacitor. Observe how the division ratio becomes frequency-dependent -- you've built a filter.

3. Load the "Op-Amp Inverting Amplifier" from the Circuits menu. Adjust the feedback resistor with right-click. Watch the output amplitude change in real-time.

### 9.3 Breadboard Exercises

1. **Power supply verification**: Before building any circuit, power your breadboard from your supply and measure the voltage at multiple points on the power bus rails. Verify continuity across the entire rail. Some breadboards have a break in the middle of the power rail -- find it now, not after hours of debugging.

2. **Bypass cap experiment**: Build a simple op-amp circuit (TL072 buffer) on a breadboard. Get it working. Then remove the bypass cap and observe what happens (it may oscillate, produce noise, or work fine depending on the layout). Add the bypass cap back. This teaches you viscerally why bypass caps matter.

3. **Audio probe test**: Build the audio probe from section 5. Connect your monitoring setup. Touch the probe to various points on a working circuit while feeding a signal. Practice identifying: clean signal, distorted signal, hum, silence (no signal), and noise.

### 9.4 Signal Tracing Practice

Using a working circuit (even a simple buffer or gain stage on breadboard):

1. Introduce a deliberate fault: disconnect one wire.
2. Without looking at which wire you disconnected, use the audio probe and signal tracing methodology to find the fault. Time yourself.
3. Reconnect the wire and introduce a different fault (wrong component value, reversed diode, etc.).
4. Signal-trace to find it.

This practice builds debugging confidence before you encounter real faults in builds you care about.

---

## 10. Quick Reference: Simulation Cheat Sheet

### LTspice Commands

| Command | Purpose | Example |
|---|---|---|
| `.tran <stop>` | Transient analysis | `.tran 10m` (simulate 10 ms) |
| `.ac dec <pts> <start> <stop>` | AC frequency sweep | `.ac dec 100 10 100k` |
| `.dc <source> <start> <stop> <step>` | DC sweep | `.dc V1 0 9 0.1` |
| `.noise V(<out>) <src> dec <pts> <start> <stop>` | Noise analysis | `.noise V(out) V1 dec 100 10 100k` |
| `.step param <name> <start> <stop> <step>` | Parameter sweep | `.step param R1 1k 100k 10k` |
| `.step param <name> list <v1> <v2> ...` | Discrete parameter sweep | `.step param C1 list 10n 22n 47n` |
| `.meas` | Automated measurement | `.meas TRAN Vmax MAX V(out)` |
| `.lib <file>` | Include SPICE model library | `.lib TL072.lib` |
| `.param <name> <value>` | Define parameter | `.param Rgain 100k` |

### Falstad Tips

- Right-click any component to adjust its value with a slider
- Use Circuits menu to load pre-built examples
- Scope traces: right-click a node and select "Show in Scope"
- Export circuit links: File > Export as Link (shareable URL)

### Breadboard Rules

1. Power bus first, bypass caps second, ICs third, everything else after
2. Pin 1 of DIP ICs: top-left when notch is at top, pins count counter-clockwise
3. 100 nF bypass cap on EVERY IC power pin, as close to the IC as physically possible
4. Keep signal wires short; keep input wires far from output wires
5. When in doubt, test with the audio probe before adding more components

---

## Key Takeaways

1. **LTspice** is free, powerful, and industry-standard. Learn AC analysis (frequency response), transient analysis (waveforms), FFT (harmonics), and parameter sweeps (design exploration).
2. **Falstad** is the best tool for building intuition -- animated current flow and instant feedback make abstract concepts visceral. Use it for learning, not for design validation.
3. **KiCad** captures schematics digitally for documentation, BOM generation, and the path to PCB layout.
4. **Breadboard prototyping** bridges simulation and reality. Keep leads short, always use bypass caps on every IC, and double-check pinouts before powering on.
5. **The audio probe** is your most important debugging tool. Build one (10 minutes, $3) before you start building circuits.
6. **Signal tracing** is binary search for circuits: follow the signal, find where it goes wrong, narrow down the cause systematically.
7. **The complete workflow** is: design, calculate, simulate, breadboard, probe, measure, iterate, document. This cycle produces reliable results.
8. **Simulation complements but doesn't replace** physical prototyping. Use both.

---

*Previous: [Chapter 2 -- Reading Schematics and Datasheets](02-reading-schematics-datasheets.md)*
*Next: [Chapter 4 -- Your Workbench, Tools, and Safety](04-workbench-tools-safety.md)*
