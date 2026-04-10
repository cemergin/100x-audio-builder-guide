<!--
  CHAPTER: 15
  TITLE: Design Tools and Simulation
  PART: 2 — Speakers
  PREREQS: Chapter 14
  KEY_TOPICS: WinISD, VituixCAD, REW, XSim, enclosure modeling, crossover simulation, measurement, iterative methodology
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 15: Design Tools and Simulation

> **Part 2 — Speakers** | Prerequisites: Chapter 14 | Difficulty: Inter→Adv

If Chapters 11-14 gave you the theory, this chapter gives you the tools to turn theory into design. Speaker design without simulation is like software development without a compiler -- you can theorize all day, but until you run the code, you don't know if it works.

The beautiful thing about modern speaker design tools is that they let you make every mistake virtually. Want to see what happens if you tune the port 10 Hz too low? Simulate it. Curious if a 3rd-order crossover sounds better than a 4th-order? Simulate both and compare. These tools will save you hundreds of dollars in wasted materials and hundreds of hours of trial-and-error by letting you converge on a near-optimal design before you cut a single piece of wood.

This chapter walks through the three essential tools -- WinISD for enclosure modeling, VituixCAD for crossover design, and REW for measurement -- and then addresses the important gap between simulation and reality.

### In This Chapter
- WinISD: enclosure modeling from driver import to optimized design
- VituixCAD: crossover design from driver data to optimized network
- REW (Room EQ Wizard): measuring drivers and finished speakers
- XSim as a VituixCAD alternative
- The simulation-reality gap and iterative methodology

### Related Chapters
- [Ch 3: Simulation and Analysis Tools](../part-0-electronics/03-simulation-analysis-tools.md) -- general simulation skills from Part 0
- [Ch 12: The Language of Speakers](12-language-of-speakers.md) -- the parameters you enter into these tools
- [Ch 14: Cabinet Design](14-cabinet-design.md) -- the designs you model with these tools
- [Ch 16: Build: Bookshelf Speakers](16-build-bookshelf-speakers.md) -- applying these tools to a real build
- [Ch 36: Room Acoustics and Treatment](../part-6-system-design/36-room-acoustics-treatment.md) -- room measurement with REW

---

## 1. WinISD: Enclosure Modeling

WinISD (Windows ISD -- Integrated Speaker Design) is the standard free tool for modeling driver-enclosure combinations. It simulates the low-frequency behavior of sealed, ported, and band-pass enclosures using Thiele-Small parameters.

### 1.1 Getting Started

WinISD is a Windows application, but runs well under Wine on macOS and Linux. Download it from the official site (search "WinISD pro alpha"). The "pro alpha" version is the one you want -- it has the most features despite the perpetual "alpha" label.

On first launch, WinISD includes a built-in driver database, but it's somewhat dated. For the best results, enter your driver's T-S parameters manually from the manufacturer's datasheet.

### 1.2 Creating a New Project

1. **File → New Project**
2. **Select or create driver**: Click "New Driver" and enter the T-S parameters from the datasheet:
   - Fs, Qts, Qes, Qms, Vas, Xmax, BL, Re, Le, Sd, Mms
   - Also enter Pe (power handling), Dd (effective diameter), and any other available parameters
3. **Choose enclosure type**: Sealed (Closed), Ported (Vented), or Band-pass
4. **Enter number of drivers**: Typically 1 for a single woofer, 2 if you're using dual woofers
5. **Click OK**: WinISD generates an initial design based on default optimization

### 1.3 Understanding the Plots

WinISD offers several plots, each showing a different aspect of the system's behavior. Switch between them using the toolbar or View menu.

**Transfer Function (Frequency Response)**

This is the primary plot -- SPL versus frequency. It shows the system's bass response from very low frequencies up to a few hundred Hz.

What to look for:
- **-3 dB point (F3)**: The frequency where the response is 3 dB below the passband level. This is the practical lower limit of the speaker's bass.
- **-6 dB point (F6)**: Often a more realistic "usable bass" threshold, since room gain typically adds 3-6 dB below 100 Hz.
- **Response shape**: Is it smooth (Butterworth-like) or peaked (high Q)?
- **Rolloff rate**: Sealed rolls off at 12 dB/octave, ported at 24 dB/octave below tuning.

**Cone Excursion**

SPL is great, but excursion tells you whether the driver can actually produce that SPL without exceeding Xmax. This plot shows peak cone excursion versus frequency at a specified input power.

What to look for:
- **Does excursion exceed Xmax at your target SPL?** If so, you need a larger driver, a higher tuning frequency, or lower SPL targets.
- **In ported systems**: Excursion dips at the tuning frequency (the port does the work) and rises sharply below tuning. If the excursion exceeds Xmax below tuning, consider a subsonic filter.

**Vent Air Velocity (ported only)**

Shows the air velocity in the port. Turbulence occurs above about 15-20 m/s.

What to look for:
- **Maximum velocity at the tuning frequency**: If it exceeds 15 m/s, increase the port area (larger diameter or slot port).
- **WinISD's warning threshold**: The software indicates when velocity is problematic.

**Group Delay**

Shows the time delay introduced by the enclosure at each frequency. Higher group delay means the bass is "late" compared to the direct sound.

What to look for:
- **Sealed**: Group delay is minimal and smooth. This is one of sealed's key advantages.
- **Ported**: Group delay peaks at the tuning frequency. Values below 20 ms are generally considered inaudible for music. Values above 30 ms may be noticeable as "slow" bass.

**Impedance**

Shows the electrical impedance the amplifier sees. For sealed: a single peak at the system resonance (Fc). For ported: a double peak around the tuning frequency.

What to look for:
- **Minimum impedance**: Ensure it stays above what your amplifier can handle (typically above 4 ohms for most amplifiers).
- **Double-peak symmetry (ported)**: In a well-tuned ported system, the two peaks should be roughly equal in height. If one is much taller than the other, the tuning or damping is off.

### 1.4 Optimizing the Design

Now comes the fun part -- iterating.

**Sealed box optimization:**

1. Start with the WinISD default volume
2. Check the Qtc: aim for 0.707 (Butterworth) for maximally flat response, or 0.6-0.65 for tighter bass
3. Adjust box volume:
   - Increase volume → lower Qtc, lower Fc, deeper bass, bigger box
   - Decrease volume → higher Qtc, higher Fc, less bass extension, smaller box
4. Check cone excursion at your target power. If it exceeds Xmax, reduce target power or accept the excursion limit.
5. Note the F3: this is your bass extension. Is it adequate for your application?

**Ported box optimization:**

1. Start with WinISD default volume and tuning
2. Adjust box volume (similar trade-offs to sealed, but also affects port dimensions)
3. Adjust tuning frequency (Fb):
   - Lower Fb → deeper extension but more group delay, more excursion below tuning
   - Higher Fb → more output in the upper bass, less extension, less group delay
4. Check vent air velocity. Increase port area if velocity is too high.
5. Check cone excursion. In a ported system, excursion is minimal at Fb but increases sharply below. Consider adding a high-pass filter (subsonic filter) to protect the driver.
6. Compare multiple scenarios: try 2-3 different volume/tuning combinations and overlay the plots.

**The comparison tool:**

WinISD lets you overlay multiple projects on the same graph. Use this to compare:
- Sealed vs. ported for the same driver
- Different box volumes for the same enclosure type
- Different tuning frequencies for a ported design
- Different drivers in the same box

This is incredibly powerful. You can see at a glance how each design choice affects every parameter.

### 1.5 A Complete Example

Let us model a bookshelf woofer (similar to the Dayton Audio RS150-4 from Chapter 12):

```
Fs = 55 Hz, Qts = 0.46, Vas = 8.5 L, Xmax = 5.75 mm, Sd = 86 cm^2
```

**Sealed, 8 liters:**
- Qtc = 0.46 * sqrt(8.5/8 + 1) = 0.46 * sqrt(2.0625) = 0.46 * 1.436 = 0.66
- Fc = 55 * 1.436 = 79 Hz
- F3 approximately 72 Hz (for Qtc = 0.66, F3 is slightly below Fc)
- Tight, well-damped bass. No peak in the response.

**Ported, 12 liters, tuned to 45 Hz:**
- F3 approximately 42 Hz -- much deeper bass extension
- Steeper rolloff below 45 Hz
- Port: a 50mm diameter port about 15 cm long
- Vent velocity: check in WinISD, should be under 15 m/s at moderate power

The sealed box is smaller and has tighter bass. The ported box extends deeper but is larger and rolls off more steeply below tuning. Both are valid choices -- the decision depends on your priorities and available space.

---

## 2. VituixCAD: Crossover Design

VituixCAD is the gold standard for passive crossover design. It is a free Windows application (also runs under Wine) that uses measured driver data to simulate complete multi-way speaker systems.

### 2.1 Why VituixCAD Over Textbook Formulas

Textbook crossover formulas assume:
- Drivers with flat frequency response
- Drivers with purely resistive impedance
- No baffle effects
- No driver interaction

Real drivers have none of these. VituixCAD uses actual measured data (frequency response and impedance curves) to simulate the real combined system, including baffle diffraction and inter-driver interference.

The difference between a textbook crossover and a VituixCAD-optimized crossover is the difference between "close" and "correct."

### 2.2 Driver Data: FRD and ZMA Files

VituixCAD needs two data files per driver:

**FRD (Frequency Response Data)**: A text file with three columns: frequency, SPL, phase. Contains the driver's on-axis (and optionally off-axis) frequency response. Many manufacturers provide FRD files on their websites.

**ZMA (Impedance Data)**: A text file with three columns: frequency, impedance magnitude, impedance phase. Contains the driver's impedance curve.

If the manufacturer doesn't provide these files, you can measure them yourself using REW (Section 3). If you can't measure, you can use VituixCAD's built-in driver modeling to generate approximate curves from T-S parameters, but measured data is always better.

### 2.3 Setting Up a Project

1. **Create a new project** and define the system (2-way, 3-way, etc.)
2. **Import driver data**: Load the FRD and ZMA files for each driver. VituixCAD shows the raw driver response immediately.
3. **Define the baffle**: Enter the cabinet front dimensions (width, height) and the position of each driver on the baffle (X and Y coordinates from a reference corner). VituixCAD uses these to simulate baffle diffraction.
4. **Set driver acoustic centers**: Enter the depth offset of each driver's acoustic center from the baffle surface. This is important for phase accuracy.

### 2.4 Building the Crossover Network

VituixCAD provides a schematic editor where you place crossover components:

1. **Start with a standard topology**: For a 2-way LR4 crossover, this is:
   - Low-pass: L1 in series, C1 to ground, L2 in series, C2 to ground (4th order)
   - High-pass: C3 in series, L3 to ground, C4 in series, L4 to ground (4th order)
2. **Add impedance compensation**: Zobel network (Rz + Cz in series, parallel with woofer). Optionally add a notch filter on the tweeter if its Fs impedance peak is near the crossover.
3. **Add an L-pad** on the tweeter for level matching.
4. **Add baffle step compensation** on the woofer circuit.

Set initial component values using the textbook formulas as a starting point. They will not be optimal, but they give the optimizer a reasonable starting position.

### 2.5 Simulation Views

VituixCAD displays an impressive array of information simultaneously:

**On-axis SPL**: The combined system response and individual driver contributions. This is the primary optimization target.

**Off-axis SPL**: Response at multiple angles. Critical for ensuring the crossover works well off-axis, not just on-axis. Many crossovers that look perfect on-axis have terrible off-axis behavior.

**Power response**: Estimated total radiated energy. Shows the speaker's overall tonal balance as heard in a reverberant room.

**Impedance**: The system impedance curve. Verify that it stays within safe limits for the amplifier.

**Phase**: Individual and combined phase response. Shows whether the drivers are combining correctly at the crossover frequency.

**Step response**: Time-domain behavior. Shows the impulse response of the system -- how it responds to a sudden transient.

**Estimated SPL in room (PIR)**: VituixCAD's "Predicted In-Room" response, which combines on-axis and off-axis data with a model of typical room reflections. This is arguably the most useful single curve, as it predicts what you'll actually hear.

### 2.6 Optimization

VituixCAD's optimizer is where the magic happens. Here is how to use it:

1. **Select which components to optimize**: Check the boxes next to the components you want the optimizer to adjust. Start by optimizing all crossover components, leaving the impedance compensation components fixed at their calculated values.

2. **Define the target**: You can optimize for:
   - Flat on-axis response (the default and usually a good starting point)
   - Flat power response
   - Flat PIR (predicted in-room response)
   - A custom target curve (e.g., slightly downward-sloping on-axis response)

3. **Set frequency range**: Typically from 200 Hz to 20 kHz. Below 200 Hz, the enclosure dominates (and WinISD handles that). Above 20 kHz is inaudible.

4. **Run the optimizer**: VituixCAD searches for component values that minimize the deviation from the target. This typically takes a few seconds.

5. **Evaluate the result**: Look at ALL the plots, not just on-axis. A flat on-axis response with a terrible off-axis response isn't a good design. Iterate: adjust the target or topology and re-optimize.

6. **Snap to standard values**: Real components come in standard values (e.g., 1.0 uF, 1.5 uF, 2.2 uF, 3.3 uF, 4.7 uF, 6.8 uF, 10 uF for capacitors). Snap each optimized value to the nearest standard value and re-simulate to verify the response is still acceptable.

### 2.7 Common Pitfalls

- **Trusting the optimizer blindly**: The optimizer finds a mathematical optimum, but that doesn't mean it sounds good. Always check the off-axis response and impedance. An optimizer might achieve a flat on-axis response by creating a deep off-axis cancellation -- technically optimal by the target metric, but terrible in practice.

- **Too many free variables**: If you let the optimizer adjust 12 components simultaneously with no constraints, it might find an exotic solution with wildly impractical values (0.3 uF capacitors, 0.02 mH inductors). Constrain the search or use a sensible topology.

- **Ignoring the crossover frequency**: The optimizer might shift the effective crossover frequency far from your intended value. If you designed for 2.5 kHz and the optimizer moves it to 4 kHz, the woofer is operating in its breakup region -- a problem the optimizer's target doesn't capture.

- **Using bad driver data**: Garbage in, garbage out. If the FRD/ZMA files are inaccurate (measured in a reflective room, with a bad microphone, or simulated from T-S parameters), the optimized crossover will be wrong.

---

## 3. REW (Room EQ Wizard): Measurement

REW is a free, cross-platform measurement tool that turns your computer into a sophisticated audio analyzer. For speaker building, REW is used to measure driver frequency response, finished speaker response, and room acoustics.

### 3.1 What You Need

**Hardware:**
- A **measurement microphone**: The miniDSP UMIK-1 (USB measurement mic with calibration file) is the standard recommendation. About $100. It plugs directly into your computer's USB port.
- A **power amplifier**: To drive the speaker under test. Any clean amplifier will do.
- A **microphone stand**: To position the mic precisely.

**Software:**
- REW itself (free download from roomeqwizard.com)
- The microphone's calibration file (provided by the manufacturer or downloadable from their website)

### 3.2 Measuring a Driver

To measure a driver's frequency response for use in VituixCAD:

1. **Mount the driver on a test baffle** or in its intended enclosure. For initial measurements, a flat baffle (a large board, at least 2x2 feet) is common. Note the baffle dimensions and driver position -- VituixCAD can simulate baffle diffraction separately, so you may want to measure on a known baffle and let VituixCAD add the diffraction.

2. **Set up the microphone**: Place it at 1 meter distance, on-axis with the driver, at the same height as the driver center. Use a mic stand for stability.

3. **Go outdoors or use gating**: Indoor measurements include room reflections that contaminate the data. Two approaches:
   - **Outdoor measurement**: Take the speaker and mic outside, away from reflective surfaces. Ground-plane measurement (mic and speaker both on the ground) is a common technique.
   - **Indoor measurement with time-gating**: REW can apply a time-domain window to the measurement, removing reflections that arrive after a certain delay. The window length determines the lowest frequency that can be measured accurately. In a typical room, a 5 ms window gives reliable data above about 300 Hz.

4. **Configure REW**: Select the measurement microphone as the input, load the calibration file, select the output driving the speaker.

5. **Run a sweep**: REW plays a sine sweep through the speaker and records the result. The measurement takes a few seconds.

6. **Export**: Save the measurement as an FRD file for import into VituixCAD.

7. **Measure impedance**: REW can also measure the driver's impedance using a series resistor technique (the DATS tool from Dayton Audio is a dedicated impedance measurement jig that integrates with REW). Export as a ZMA file.

### 3.3 Measuring Off-Axis Response

For complete VituixCAD modeling, measure at multiple angles:
- 0 degrees (on-axis)
- 15 degrees horizontal
- 30 degrees horizontal
- 45 degrees horizontal
- 60 degrees horizontal
- Optionally: 15, 30 degrees vertical (above and below axis)

At each angle, reposition the microphone (or rotate the speaker on a turntable) and repeat the measurement. This gives you a complete picture of the driver's radiation pattern.

VituixCAD can import multi-angle FRD data and use it for its off-axis and power response simulations.

### 3.4 Measuring a Finished Speaker

After building and assembling your speaker:

1. **Measure on-axis at 1 meter**: This is the primary verification. Compare to your VituixCAD simulation.
2. **Measure off-axis**: Check at 15 and 30 degrees horizontally. Compare to simulation.
3. **Measure impedance**: Compare to simulation. Large deviations indicate a problem (wrong component values, a wiring error, an improperly sealed enclosure).

### 3.5 Comparing Measurement to Simulation

The moment of truth: overlay your measured response on the VituixCAD prediction.

They will not match perfectly. Expect:
- **Low-frequency differences** due to measurement room acoustics (unless measured outdoors or with careful gating)
- **Small midrange variations** (1-2 dB) due to component tolerances and driver-to-driver variation
- **High-frequency differences** due to microphone placement sensitivity (at 10 kHz, moving the mic 1 cm changes the measurement)

What to worry about:
- **Large deviations (>3 dB)** in the crossover region -- this usually means a crossover component is wrong or a driver is wired with incorrect polarity
- **Missing bass** -- check the enclosure for air leaks (sealed) or a blocked port (ported)
- **Sharp peaks or dips** not in the simulation -- check for loose drivers, rattling braces, or resonant panels

### 3.6 The Iterative Cycle

If the measurement doesn't match the simulation:
1. Identify the discrepancy
2. Hypothesize the cause (component tolerance? Baffle effect not modeled? Room reflection?)
3. Update the VituixCAD model with the measured driver data (import your measurements as the driver data)
4. Re-optimize the crossover using the measured data
5. Swap crossover components to the new values
6. Re-measure
7. Repeat until satisfied

This iterative process is how all professional speaker designers work. The simulation gets you 90% of the way; measurement and iteration get you the last 10%.

---

## 4. XSim: A VituixCAD Alternative

**XSim** is another free crossover simulation tool. It is simpler than VituixCAD and runs on Windows.

### 4.1 When to Use XSim

XSim is useful when:
- You want a simpler interface for quick crossover calculations
- You are already familiar with XSim from forums and tutorials
- You need to run a quick simulation without the setup overhead of VituixCAD

### 4.2 Differences from VituixCAD

- XSim doesn't simulate baffle diffraction (you must include it in your driver data or account for it separately)
- XSim's optimizer is less sophisticated
- XSim doesn't compute off-axis response or power response
- XSim has a simpler, more intuitive interface that is less overwhelming for beginners

For serious speaker design, VituixCAD is the better tool. For quick experiments and learning, XSim is a fine starting point.

---

## 5. The Simulation-Reality Gap

This section might be the most important in the chapter. Simulations are not reality. They are models, and every model has limitations.

### 5.1 Why Simulations Do Not Perfectly Match Builds

**Component tolerances**: A capacitor labeled 10 uF might actually be 9.3 uF or 10.8 uF. A typical film capacitor has 5-10% tolerance. Inductors wound on iron cores can vary by 10-20%. In a multi-component crossover, these tolerances accumulate.

Mitigation: Use 1-2% tolerance capacitors (available from audio component suppliers). Measure inductors with an LCR meter and adjust the number of turns. Or accept the tolerance and measure the finished speaker.

**Driver-to-driver variation**: Two drivers from the same production run may have slightly different T-S parameters and frequency response. Variations of 0.5-1 dB are common.

Mitigation: Measure both drivers and use the averaged data. For stereo pairs, try to match drivers as closely as possible.

**Mounting effects**: The simulation assumes the driver is perfectly mounted on a flat baffle. In reality, the driver cutout, gasket, screw flanges, and nearby port opening all slightly modify the driver's response.

Mitigation: Measure the driver in its actual enclosure and use that measurement in VituixCAD.

**Enclosure resonances**: Simulations don't model panel resonances. If your cabinet is resonant, the measurement will show features the simulation doesn't predict.

Mitigation: Build solid, well-braced cabinets (Chapter 14).

**Room effects**: Below about 300 Hz, the room dominates the response. A simulation predicts free-field behavior; a room measurement includes boundary reinforcement, room modes, and furniture reflections.

Mitigation: For bass, compare to simulation only the portion of the measurement above 200-300 Hz (where gating can remove room effects). For full-range assessment, measure outdoors.

### 5.2 The Iterative Methodology

The professional approach to speaker design is:

1. **Design in simulation**: Use WinISD for the enclosure and VituixCAD for the crossover. Get as close to optimal as possible before building anything.

2. **Build a prototype**: Construct the enclosure and crossover. Use temporary wiring (clip leads or spring terminals) so you can swap components easily.

3. **Measure**: Use REW to measure the finished speaker. Compare to simulation.

4. **Update the model**: Import measured data into VituixCAD. Re-optimize the crossover.

5. **Swap components**: Install the updated crossover values.

6. **Re-measure**: Verify the improvement.

7. **Listen**: The final judge. Does it sound right? Are there any annoying colorations? Sometimes your ear will notice problems that measurements miss (or that you did not think to measure).

8. **Finalize**: When measurement and listening confirm the design, solder the crossover permanently, do final assembly, and finish the cabinet.

This is no different from the software development cycle: design → implement → test → debug → iterate. The tools just measure different things.

### 5.3 When Good Enough Is Good Enough

Perfectionism is a trap. At some point, you're chasing 0.5 dB deviations that are inaudible in a real room with real music. Here are reasonable targets:

- **On-axis response**: Within plus or minus 2 dB of target from 500 Hz to 15 kHz
- **Impedance minimum**: Above 80% of nominal (e.g., above 3.2 ohms for a 4-ohm speaker)
- **Off-axis response**: Smooth, no sharp peaks or dips, gently declining with angle
- **Bass response**: Within 3 dB of simulation above 60 Hz

If you hit these targets, you have a very good speaker. Publish the build, share it on forums, and enjoy the music. You can always revisit the crossover later if inspiration strikes.

---

## 6. Chapter Summary and What Comes Next

In this chapter, you learned:

- WinISD models enclosure behavior: frequency response, cone excursion, vent velocity, group delay, and impedance for sealed, ported, and band-pass designs
- VituixCAD is the complete crossover design tool: imports measured driver data, simulates baffle effects, optimizes crossover networks, and shows on-axis, off-axis, power response, and impedance
- REW measures real speakers using a calibrated measurement microphone, enabling you to compare your build to the simulation
- XSim is a simpler crossover tool suitable for quick calculations and learning
- The simulation-reality gap exists due to component tolerances, driver variation, mounting effects, and room acoustics
- The iterative methodology (simulate → build → measure → adjust → re-measure) is the professional approach

You now have the complete theoretical foundation and the tools to design a speaker. In Chapter 16, we are going to use ALL of this -- WinISD, VituixCAD, careful driver selection, cabinet construction, crossover assembly, measurement, and iteration -- to build a 2-way bookshelf speaker from scratch. It is time to make something real.
