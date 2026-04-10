<!--
  CHAPTER: 12
  TITLE: The Language of Speakers
  PART: 2 — Speakers
  PREREQS: Chapter 11
  KEY_TOPICS: SPL, sensitivity, impedance curves, Thiele-Small parameters, frequency response graphs, distortion, power handling, datasheets
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 12: The Language of Speakers

> **Part 2 — Speakers** | Prerequisites: Chapter 11 | Difficulty: Intermediate

In Chapter 11 you learned what happens inside a speaker physically. Now we need to quantify it. Every driver you can buy comes with a datasheet full of numbers, graphs, and acronyms -- and if you cannot read that datasheet, you are designing blind. You would never integrate a software library without reading its API docs, and Thiele-Small parameters are exactly that: **the driver's API specification.** They tell you what the driver can do, what it needs from its environment (the enclosure), and where its limits are, all without opening the box.

This chapter is dense. There are a lot of parameters, and each one matters. But by the end, you will be able to pick up any driver datasheet and know exactly what you are looking at. You will know which numbers matter most for a sealed enclosure versus a ported one. You will know why one woofer is perfect for a bookshelf speaker and another belongs in a subwoofer. And you will have the vocabulary to have intelligent conversations about speaker design with anyone.

We are going to take every important spec, explain what it means physically, what its numerical value tells you about the driver, and how it affects your design decisions. Let us get into it.

### In This Chapter
- SPL and sensitivity: what the loudness numbers actually mean
- Impedance curves: why "8 ohms" is a lie (a useful one)
- Thiele-Small parameters: the complete deep dive
- Frequency response graphs: on-axis, off-axis, power response
- Distortion specifications
- Power handling: thermal vs mechanical limits
- Reading and comparing real driver datasheets

### Related Chapters
- [Ch 0: Impedance and AC Basics](../part-0-electronics/00-impedance-ac-basics.md) -- the electrical foundation for impedance curves
- [Ch 11: How Speakers Work](11-how-speakers-work.md) -- the physical systems these specs describe
- [Ch 13: Crossover Design](13-crossover-design.md) -- uses impedance and sensitivity specs directly
- [Ch 14: Cabinet Design](14-cabinet-design.md) -- uses Thiele-Small parameters directly
- [Ch 15: Design Tools and Simulation](15-design-tools-simulation.md) -- entering these specs into modeling software

---

## 1. SPL and Sensitivity

### 1.1 Sound Pressure Level (SPL)

**Sound Pressure Level (SPL)** is the measure of how loud something is, expressed in decibels (dB). The decibel scale is logarithmic because human hearing spans an enormous range -- from the threshold of hearing (0 dB SPL, which is 20 micropascals of pressure) to the threshold of pain (around 130 dB SPL).

Key reference points:
- 30 dB SPL: Quiet bedroom
- 60 dB SPL: Normal conversation
- 85 dB SPL: Loud listening level (where many audiophiles listen)
- 100 dB SPL: Very loud -- a rock concert at a moderate distance
- 115 dB SPL: Front row at a loud concert, threshold of discomfort
- 130 dB SPL: Pain threshold, risk of immediate hearing damage

The critical thing about decibels: **every 10 dB increase sounds roughly twice as loud to the human ear, and requires 10 times the amplifier power.** Every 3 dB increase requires double the amplifier power.

So going from 85 dB to 95 dB (a modest perceived increase) requires 10x more power. Going from 85 dB to 105 dB requires 100x more power. This is why sensitivity matters so much.

### 1.2 Sensitivity: Two Standards, One Confusion

**Sensitivity** tells you how loud a speaker plays for a given input. But there are two standards, and confusing them will lead to design errors.

**Standard 1: dB/W/m (decibels per watt per meter)**
Measured by applying 1 watt of electrical power to the driver and measuring SPL at 1 meter distance. This is the "true" power sensitivity.

**Standard 2: dB/2.83V/m (decibels per 2.83 volts per meter)**
Measured by applying 2.83 volts RMS to the driver and measuring SPL at 1 meter. Why 2.83 volts? Because 2.83V into an 8-ohm load equals exactly 1 watt (P = V^2/R = 2.83^2/8 = 1W).

**Here is where it gets tricky**: for an 8-ohm driver, both standards give the same number. But for a 4-ohm driver, 2.83V produces 2 watts (2.83^2/4 = 2W), so the dB/2.83V/m number is 3 dB higher than the dB/W/m number. The driver is not actually more efficient -- it is just receiving more power.

Why does this matter? When comparing drivers with different impedances:
- Use **dB/W/m** to compare true electro-acoustic efficiency (how much sound per watt)
- Use **dB/2.83V/m** to compare what you will actually hear from the same amplifier (because amplifiers are essentially voltage sources)

Most modern datasheets specify 2.83V/m. When you see "sensitivity: 87 dB" on a 4-ohm driver, that driver's true efficiency is only 84 dB/W/m. Always check which standard is being used.

### 1.3 What Sensitivity Numbers Mean in Practice

- **82-86 dB**: Low sensitivity. Common in small sealed designs and subwoofers. Needs significant amplifier power. An 83 dB speaker needs 10x more power than a 93 dB speaker to play at the same volume.
- **87-90 dB**: Moderate sensitivity. Typical for quality home speakers. Works well with 50-200W amplifiers.
- **91-95 dB**: High sensitivity. Common in larger woofers and some horn-loaded designs. Can be driven by lower-power amplifiers, even tubes.
- **96-100+ dB**: Very high sensitivity. Horn-loaded designs, PA speakers. Can produce ear-damaging levels with modest amplifiers.

> **What happens if you pair a 95 dB tweeter with an 85 dB woofer?**
>
> The tweeter is 10 dB louder than the woofer at the same power. Without level matching (padding the tweeter down), the speaker will sound painfully bright. This is extremely common -- tweeters are typically 3-8 dB more sensitive than woofers, and the crossover network must include attenuation (L-pad or resistor network) to bring the tweeter down to match. We cover this in Chapter 13.

---

## 2. Impedance Curves

### 2.1 The "8 Ohm" Lie

When a speaker is labeled "8 ohms," that is the **nominal impedance** -- a rough average that tells the amplifier what kind of load to expect. But the actual impedance varies wildly with frequency. A speaker labeled "8 ohms" might have an impedance of 40 ohms at one frequency and 5 ohms at another.

You learned in Part 0 that impedance in AC circuits depends on frequency. A speaker is an electromagnetic device with resistance (the voice coil wire), inductance (the coil), and mechanical elements (mass, compliance, damping) that all interact to create a frequency-dependent impedance.

### 2.2 Anatomy of an Impedance Curve

If you measure a woofer's impedance across frequency and plot it, you will see a distinctive shape:

**The Resonance Peak**: A large, sharp peak at the driver's free-air resonance frequency (Fs). At Fs, the driver's mechanical resonance causes the voice coil's back-EMF to peak, making the electrical impedance shoot up. A typical woofer with a nominal impedance of 8 ohms might have a resonance peak of 30-80 ohms.

**The Impedance Minimum**: After the resonance peak, the impedance drops to its minimum value -- this is close to the DC resistance (Re) of the voice coil. For an "8 ohm" driver, the minimum impedance is typically 5-7 ohms. This minimum impedance is what the amplifier actually has to drive at that frequency, so it matters for amplifier compatibility.

**The Rising Slope**: Above the impedance minimum, the impedance gradually rises because the voice coil's inductance (Le) becomes increasingly significant at higher frequencies (inductive reactance = 2 * pi * f * Le). By 10 kHz, a woofer's impedance might be 20-30 ohms.

**In a ported enclosure**, the impedance curve has a distinctive double peak around the resonance region -- one peak above the port tuning frequency and one below, with a dip to near-minimum between them. This double-hump is a telltale sign of a ported system and its shape tells you the tuning frequency and damping of the system.

### 2.3 Why the Impedance Curve Matters for Design

The impedance curve matters for several reasons:

1. **Amplifier compatibility**: The amplifier must be rated to drive the minimum impedance. A speaker that dips to 3 ohms is a difficult load -- some amplifiers cannot deliver enough current and will clip, overheat, or engage protection circuits.

2. **Crossover design**: A passive crossover designed for a flat 8-ohm load will behave differently when the actual impedance is 5 ohms at one frequency and 30 ohms at another. The crossover frequency and slope will shift. This is why impedance compensation networks (Zobel circuits, notch filters) are essential -- we cover them in Chapter 13.

3. **Power delivery**: The amplifier delivers maximum power at the impedance minimum. At the resonance peak (where impedance is high), the amplifier delivers very little power. This means the driver is actually receiving wildly varying power across frequency, even though the amplifier's voltage output is constant.

4. **System resonance identification**: The resonance peak tells you Fs directly. In a ported system, the double peak tells you the tuning frequency and system damping. You can literally read the enclosure design from the impedance curve.

---

## 3. Thiele-Small Parameters: The Driver's API Spec

In the 1960s and 70s, Neville Thiele and Richard Small developed a set of parameters that completely characterize a driver's low-frequency behavior in a mathematically predictable way. Before their work, speaker design was largely trial and error. After it, you could model a driver in an enclosure on paper (and eventually in software) before building anything.

Think of T-S parameters as a driver's API specification. They tell you what the driver can do, what constraints it operates under, and what it needs from its "host" (the enclosure). You do not need to open the driver to use them -- just like you do not need to read a library's source code to use its API.

### 3.1 Fs — Free-Air Resonance Frequency

**What it is**: The frequency at which the driver naturally resonates when mounted in free air (no enclosure). It is determined by the moving mass and the suspension compliance -- heavier cone = lower Fs, stiffer suspension = higher Fs.

**Unit**: Hertz (Hz)

**Physically**: Tap the cone gently and let it ring. The frequency it vibrates at is Fs. It is the frequency where the cone's mass and the suspension's springiness are perfectly balanced.

**What the number tells you**:
- **Low Fs (20-40 Hz)**: Designed for subwoofer/deep bass duty. Heavy cone, compliant suspension. Good candidate for ported or sealed subwoofer enclosures.
- **Medium Fs (35-60 Hz)**: Typical woofer for bookshelf or tower speakers. Good bass potential in a reasonably sized box.
- **High Fs (60-100+ Hz)**: Midrange driver or small woofer. Will not produce deep bass regardless of enclosure.

**Design implication**: Fs sets the lower bound of what the driver can do. You cannot get a driver with Fs = 70 Hz to produce meaningful output at 30 Hz. An enclosure can extend response somewhat below Fs, but there is a limit.

### 3.2 Qts — Total Q Factor

**What it is**: A dimensionless number that describes the total damping of the driver at resonance. It combines electrical damping (from the voice coil interacting with the amplifier) and mechanical damping (from the suspension).

**Physically**: After you tap the cone and it starts resonating at Fs, how quickly does the vibration die out? Low Q = dies quickly (heavily damped). High Q = rings for a long time (lightly damped).

**What the number tells you**:
- **Low Qts (0.2-0.35)**: Heavily damped. Excellent for sealed enclosures, where the sealed air provides additional damping. Tight, controlled bass.
- **Medium Qts (0.35-0.5)**: Versatile. Works in either sealed or ported enclosures. Most common range for quality woofers.
- **High Qts (0.5-1.0+)**: Lightly damped. Better suited for ported enclosures (where the port provides damping at the tuning frequency) or open baffle/free-air applications. Sealed boxes will sound boomy.

**Design implication**: Qts is arguably the single most important parameter for deciding which enclosure type to use. It is the first thing I check when evaluating a driver.

### 3.3 Qes — Electrical Q Factor

**What it is**: The Q factor considering only electrical damping. Electrical damping comes from the voice coil's resistance and its interaction with the amplifier's output impedance.

**Unit**: Dimensionless

**Physically**: When the cone moves, the voice coil cuts through the magnetic field and generates a back-EMF (a voltage that opposes the motion). This back-EMF is dissipated as heat in the voice coil resistance and the amplifier's output impedance, creating a braking force. Qes quantifies this electrical braking.

**What the number tells you**: A low Qes means strong electrical damping (strong motor, low resistance). A high Qes means weak electrical damping. In most drivers, Qes is the dominant component of Qts (electrical damping is stronger than mechanical damping).

**Design implication**: Qes interacts with the amplifier's output impedance. A high-output-impedance amplifier (like a tube amp without feedback) reduces the electrical damping, effectively increasing Qts. This is why some drivers sound "boomy" on tube amps but "tight" on solid-state amps. We will discuss this amplifier-speaker interaction in Part 3.

### 3.4 Qms — Mechanical Q Factor

**What it is**: The Q factor considering only mechanical damping -- friction in the suspension (surround and spider).

**Unit**: Dimensionless

**Physically**: How lossy is the suspension? A rubber surround dissipates more energy (lower Qms) than a treated cloth surround (higher Qms).

**What the number tells you**: Typical values range from 2 to 10. Higher means less mechanical loss. Qms is almost always much higher than Qes, meaning that mechanical damping is the minor contributor to overall damping.

**Design implication**: Qms is less critical for enclosure design decisions than Qes and Qts. However, a very low Qms (heavily damped suspension) can indicate a driver that will have reduced efficiency.

**The relationship**: 1/Qts = 1/Qes + 1/Qms. Since Qms is typically much higher than Qes, Qts is slightly lower than Qes.

### 3.5 Vas — Equivalent Compliance Volume

**What it is**: The volume of air that has the same "springiness" (compliance) as the driver's suspension. Expressed as a volume of air (in liters or cubic feet).

**Physically**: Imagine putting the driver's suspension in a box and saying "how big would a box of air need to be to have the same spring constant?" That is Vas.

**What the number tells you**:
- **Large Vas (50-200+ liters)**: Very compliant suspension. The driver wants a large enclosure. Typically large-diameter woofers designed for big cabinets or subwoofers.
- **Medium Vas (15-50 liters)**: Moderate compliance. Works in medium-sized bookshelf to small tower enclosures.
- **Small Vas (5-15 liters)**: Stiff suspension. Works in compact enclosures. Common in small woofers designed for bookshelf speakers.

**Design implication**: Vas directly determines the enclosure volume needed. In a sealed enclosure, the box volume interacts with Vas to determine the system Q and the low-frequency cutoff. A general rule: a sealed box volume of 0.7 * Vas gives a Qtc (system Q) of about 0.707 for a Butterworth alignment. Larger boxes lower the system Q; smaller boxes raise it.

In a ported enclosure, Vas still determines the optimal box volume, but the relationship is more complex and typically requires simulation software.

### 3.6 Xmax — Maximum Linear Excursion

**What it is**: The maximum distance the cone can move from its rest position while maintaining approximately linear behavior (low distortion). Usually specified as one-way (peak) excursion in millimeters.

**Physically**: How far the cone can travel before the motor's force becomes non-linear (the voice coil leaves the magnetic gap) or the suspension becomes non-linear (the surround or spider reaches its mechanical limit).

**What the number tells you**:
- **Short Xmax (2-4mm)**: Typical for midrange drivers and tweeters. Not designed for large excursions.
- **Medium Xmax (5-8mm)**: Typical for woofers in bookshelf speakers. Adequate for moderate bass at moderate volumes.
- **Long Xmax (10-20mm+)**: Subwoofer territory. Designed for deep bass at high volumes. These drivers are built for massive excursion.

**Design implication**: Xmax determines maximum output, especially at low frequencies where the driver needs to move a lot of air. The maximum SPL at a given frequency is determined by Xmax and cone area (Sd). This is particularly important for subwoofers and PA woofers, where running out of excursion (bottoming out) causes severe distortion and can damage the driver.

In simulation tools like WinISD, you can plot cone excursion versus frequency and make sure you are not exceeding Xmax at your desired output level. This is one of the most useful simulations you can run.

### 3.7 BL — Force Factor (BL Product)

**What it is**: The product of the magnetic flux density (B, in Tesla) in the voice coil gap and the length of wire in the gap (L, in meters). Expressed in Tesla-meters (Tm) or Newton per Ampere (N/A) -- they are the same unit.

**Physically**: BL tells you how much force the motor produces per ampere of current. High BL = strong motor. Low BL = weak motor.

**What the number tells you**:
- **Low BL (3-6 Tm)**: Modest motor. Common in budget drivers and some full-range designs.
- **Medium BL (7-12 Tm)**: Strong motor. Typical of quality woofers.
- **High BL (13-25+ Tm)**: Very strong motor. PA woofers, compression drivers. Lots of force available.

**Design implication**: BL affects efficiency (strong motor = more sound per watt), electrical damping (strong motor = lower Qes = more damped), and excursion control. A high-BL driver tends to have tight, well-controlled bass but lower Qts (which favors sealed enclosures). A low-BL driver tends to have higher Qts and may work better in ported enclosures.

BL and moving mass (Mms) together determine the driver's efficiency. The figure of merit is BL^2 / (Re * Mms) -- higher means more efficient.

### 3.8 Le — Voice Coil Inductance

**What it is**: The inductance of the voice coil, in millihenries (mH).

**Physically**: The voice coil is a coil of wire, and all coils have inductance. This inductance causes the impedance to rise at high frequencies (inductive reactance = 2 * pi * f * Le).

**What the number tells you**:
- **Low Le (0.1-0.5 mH)**: The impedance rise with frequency is gentle. Better for woofers that need to extend into the midrange. Also easier for the crossover to deal with.
- **High Le (1-3 mH)**: Significant impedance rise at high frequencies. The driver naturally rolls off in the upper midrange. The crossover must compensate with a Zobel network (we will learn this in Chapter 13).

**Design implication**: Le affects the crossover behavior because the crossover "sees" the driver's impedance, not a pure resistor. High Le makes the high-pass section of the crossover behave differently than expected. A Zobel network (a series resistor and capacitor wired across the driver) flattens the impedance rise and lets the crossover work as designed.

### 3.9 Re — DC Resistance

**What it is**: The resistance of the voice coil wire, measured with a DC ohmmeter. In ohms.

**Physically**: Just the resistance of the copper (or aluminum) wire wound on the voice coil. It is always lower than the nominal impedance because the nominal impedance includes the reactive components.

**What the number tells you**: For an "8 ohm" driver, Re is typically 5.5-7 ohms. For a "4 ohm" driver, Re is typically 3-3.5 ohms.

**Design implication**: Re determines the minimum impedance the amplifier sees (it cannot go lower than Re except in bizarre reactive situations). It also affects Qes -- lower Re means more electrical damping (lower Qes) for a given BL.

### 3.10 Other Useful Parameters

**Sd — Effective Piston Area**: The area of the cone that actually moves air, in square meters or square centimeters. Larger Sd = more air displaced per millimeter of excursion. Critical for output capability.

**Mms — Moving Mass**: The total mass of everything that moves (cone, voice coil, surround, spider, air load), in grams. Heavier = lower Fs, lower efficiency. Lighter = higher Fs, higher efficiency.

**Cms — Mechanical Compliance**: How "springy" the suspension is, in meters per Newton. Higher compliance = softer suspension = lower Fs. Related to Vas by: Vas = rho * c^2 * Cms * Sd^2 (where rho is air density and c is speed of sound).

**Efficiency (eta-0)**: The percentage of electrical power converted to acoustic power. Typically 0.5% to 5% for direct radiators. Yes, speakers are incredibly inefficient -- most of the power becomes heat in the voice coil. Horns improve this dramatically.

**EBP (Efficiency Bandwidth Product)**: Fs / Qes. A quick rule of thumb:
- EBP < 50: Driver prefers a sealed enclosure
- 50 < EBP < 90: Works in either sealed or ported
- EBP > 90: Driver prefers a ported enclosure

This is a rough guide, not a hard rule. But it is useful for quick screening.

---

## 4. Frequency Response Graphs

### 4.1 On-Axis Frequency Response

The on-axis frequency response graph is the "hero spec" -- it is what most datasheets show prominently. It plots SPL (vertical axis, in dB) versus frequency (horizontal axis, in Hz) measured directly in front of the driver at 1 meter distance, with a specified input level.

What to look for in a driver's raw frequency response:

- **Usable bandwidth**: The range over which the response is reasonably flat. A woofer might be usable from 50 Hz to 4 kHz. A tweeter from 1.5 kHz to 20 kHz.
- **Breakup modes**: Sharp, narrow peaks at specific frequencies where the cone stops behaving as a rigid piston and starts flexing in resonant modes. These are visible as sharp spikes (sometimes 5-15 dB above the average level). You generally want to cross over BELOW the breakup region, because breakup peaks produce harsh, colored sound.
- **Rolloff**: How the response falls off at the extremes. The low-frequency rolloff is determined by Fs and Q. The high-frequency rolloff is determined by cone mass, inductance, and breakup.
- **Smoothness in the pass band**: Is the response smooth (within 2-3 dB) in the intended operating range, or does it have sharp peaks and dips?

### 4.2 Off-Axis Response

Good datasheets show response curves at multiple angles -- typically 0 degrees (on-axis), 15, 30, 45, and 60 degrees off-axis. The set of off-axis curves is sometimes called the **family of curves** or the **polar response**.

What to look for:
- **Smooth, gradual rolloff with angle**: At each angle, the response should look like a slightly attenuated version of the on-axis response. If sharp peaks or dips appear at certain angles but not on-axis, that indicates diffraction or resonance issues.
- **Beaming onset**: The frequency where the off-axis response starts to diverge significantly from on-axis. This is roughly where the wavelength equals the driver diameter.
- **Consistency**: The off-axis curves should be evenly spaced and smooth. Erratic off-axis behavior means the driver will excite the room unpredictably.

### 4.3 Power Response

**Power response** is the total acoustic output integrated over all angles. It represents the total energy the driver radiates into the room. This is particularly useful because in most rooms, reflected sound (which comes from all angles) contributes significantly to what you hear.

Not all datasheets show power response. Design tools like VituixCAD can compute it from the off-axis measurements.

### 4.4 Sensitivity-Normalized vs. Impedance-Normalized Response

When comparing two drivers, be careful about the reference level:
- A response measured at a constant voltage (2.83V) reflects what you hear from a voltage-source amplifier
- A response normalized to 1W (impedance-compensated) reflects true electro-acoustic efficiency

For home audio design with solid-state amplifiers (which are essentially voltage sources), constant-voltage measurements are more relevant. For PA design where you are thinking about amplifier power budgets, 1W sensitivity matters more.

---

## 5. Distortion Specifications

### 5.1 THD — Total Harmonic Distortion

**THD** measures the harmonics generated by the driver. If you feed a 100 Hz tone, the driver will produce not only 100 Hz but also some 200 Hz (2nd harmonic), 300 Hz (3rd harmonic), and so on. THD expresses the total power in these harmonics as a percentage of the fundamental.

THD varies with:
- **Frequency**: Distortion is typically highest near the resonance frequency and at the extremes of the driver's bandwidth.
- **Level**: Distortion increases dramatically as you approach the driver's excursion limits. A woofer might produce 0.5% THD at moderate levels but 10% THD when pushed hard near Xmax.

**What is acceptable?** For a quality home speaker:
- Below 1% THD in the operating range at moderate levels is good
- Below 0.5% is very good
- Below 0.1% is excellent (rare in drivers, more achievable in complete systems at moderate SPL)

For PA speakers, higher distortion is acceptable because the listening environment (outdoor, high ambient noise) masks it.

### 5.2 IMD — Intermodulation Distortion

When two frequencies are present simultaneously, the non-linearities in the driver create sum and difference frequencies. Playing 1000 Hz and 1100 Hz simultaneously produces not only those frequencies but also 100 Hz (difference), 2100 Hz (sum), and other combinations.

IMD is particularly insidious because the distortion products are not harmonically related to the signal -- they are musically unrelated frequencies that sound harsh and unnatural. This is one reason multi-way speakers often sound cleaner than full-range drivers: in a 2-way system, the woofer does not have to simultaneously reproduce a 100 Hz bass note and a 3,000 Hz vocal, so IMD between those frequencies is eliminated.

### 5.3 Distortion vs. Frequency Graphs

Some datasheets include a "distortion vs. frequency" graph, showing 2nd harmonic, 3rd harmonic, and sometimes higher harmonics plotted separately across the frequency range. These are incredibly informative:

- **2nd harmonic distortion** (the "warm" distortion) is often caused by asymmetric motor non-linearity (the force is different pushing out vs. pulling in). Visible as a curve that rises at low frequencies.
- **3rd harmonic distortion** (the "harsh" distortion) is often caused by symmetric non-linearity (the motor weakens equally in both directions at extreme excursion). Visible as a curve that rises at the frequency where Xmax is being approached.

---

## 6. Power Handling

### 6.1 The Two Failure Modes

A speaker driver can fail in two completely different ways, and its power handling is limited by whichever threshold is reached first:

**Thermal failure**: The voice coil overheats. The wire insulation softens, coils shift, and eventually the wire shorts or the former deforms. This is the dominant failure mode at frequencies above the driver's operating range, where the cone cannot move much (due to its mass) and all the electrical energy is converted to heat in the voice coil. It is also the long-term failure mode at any frequency -- continuous high power eventually cooks the coil.

**Mechanical failure**: The cone exceeds its maximum excursion. The voice coil bottoms out against the back plate, the surround tears, or the spider cracks. This is the dominant failure mode at low frequencies, where the driver is making large excursions. It often manifests as a loud "crack" or "pop" -- and the driver may survive a single event but repeated bottoming destroys it.

### 6.2 Continuous vs. Peak Power

**Continuous power handling (RMS)**: The power the driver can handle continuously for a long period (typically tested with a shaped noise signal over hours). This is the thermal limit. A driver rated for 50W RMS can dissipate 50W of heat without cooking its voice coil.

**Peak (program) power handling**: The short-term power the driver can handle on transients. Typically 2-4x the continuous rating. A 50W RMS driver might handle 200W peaks. This is more relevant for music, which is full of short peaks.

**Be skeptical of power ratings on cheap drivers.** Some manufacturers test at a single frequency or for a very short duration to inflate the number. A "300W" driver from a dubious brand might not actually survive 300W of continuous program material.

### 6.3 Power Handling vs. Frequency

This is critical and often overlooked: **a driver's power handling varies dramatically with frequency.** Below the enclosure's tuning frequency (in a ported box) or below Fs (in a sealed box), the cone unloads -- the air spring no longer limits excursion effectively -- and cone excursion skyrockets for a given power input. The driver might handle 100W comfortably at 60 Hz but blow up at 20W at 25 Hz because the excursion exceeds Xmax.

This is one of the most common failure modes in subwoofers: the user plays content with very deep bass (organ music, electronic music, movie explosions) and the driver exceeds Xmax at frequencies below the port tuning. A high-pass filter or subsonic filter that rolls off frequencies below the system's useful range is cheap insurance against this failure.

> **What happens if you put twice the rated power into a speaker?**
>
> It depends on the signal and the frequency. With a continuous sine wave, you will quickly overheat and destroy the voice coil. With dynamic music at moderate average levels but high peaks, the driver might survive for a long time -- voice coils can handle brief overloads because the thermal mass of the coil takes time to heat up. But you are living on borrowed time, and one sustained loud passage could be the end. The safe rule: treat continuous power ratings seriously, and give yourself headroom. A 50W-rated driver on a 100W amplifier is fine if you do not turn it up all the way. A 50W-rated driver on a 100W amplifier turned to clipping is a dead driver.

---

## 7. Reading and Comparing Driver Datasheets

Let us put all of this into practice by walking through how you would evaluate drivers for a project. We will compare two woofers that might be candidates for a 2-way bookshelf speaker.

### 7.1 Hypothetical Woofer A: The Bookshelf Candidate

```
Dayton Audio RS150-4 (5.25" woofer)
Nominal impedance:    4 ohms
Re:                   3.4 ohms
Fs:                   55 Hz
Qts:                  0.46
Qes:                  0.53
Qms:                  3.49
Vas:                  8.5 liters
Xmax:                 5.75 mm (one-way)
BL:                   5.3 Tm
Le:                   0.32 mH
Sd:                   86 cm^2
Sensitivity:          84.6 dB (2.83V/m)
Power handling:       40W RMS
```

Reading this datasheet:
- **Fs = 55 Hz**: Moderate. Will not hit 30 Hz but should give useful bass into the 50s Hz range in a box.
- **Qts = 0.46**: On the higher side. Could work in either sealed or ported. EBP = 55/0.53 = 104, which suggests ported might be favorable.
- **Vas = 8.5 liters**: Small. Good news -- this driver does not need a big box. A 7-12 liter enclosure is realistic for a bookshelf speaker.
- **Xmax = 5.75 mm**: Moderate. Fine for bookshelf levels but will not produce room-shaking bass.
- **Sensitivity = 84.6 dB (2.83V/m)**: Low. But remember, this is a 4-ohm driver, so the true 1W sensitivity is even lower (about 81.6 dB). You will need a capable amplifier. But for a bookshelf speaker that will be listened to at moderate levels in a small room, this is fine.
- **Le = 0.32 mH**: Low. Good high-frequency extension, less impedance compensation needed.

### 7.2 Hypothetical Woofer B: The PA Candidate

```
Eminence Delta-12A (12" woofer)
Nominal impedance:    8 ohms
Re:                   5.37 ohms
Fs:                   57 Hz
Qts:                  0.49
Qes:                  0.56
Qms:                  3.89
Vas:                  114 liters
Xmax:                 4.3 mm (one-way)
BL:                   12.8 Tm
Le:                   1.04 mH
Sd:                   507 cm^2
Sensitivity:          97 dB (2.83V/m)
Power handling:       400W RMS
```

Reading this datasheet:
- **Fs = 57 Hz**: Similar to Woofer A. But with a much larger cone area (Sd), this driver moves far more air.
- **Qts = 0.49**: Similar range, works in sealed or ported.
- **Vas = 114 liters**: Enormous. This driver wants a big box. Not a bookshelf candidate!
- **Xmax = 4.3 mm**: Short for a 12" driver. This is a PA woofer designed for efficiency, not deep bass extension. It relies on cone area, not excursion.
- **Sensitivity = 97 dB**: A full 12 dB more sensitive than Woofer A. At the same amplifier power, this is roughly 16 times louder. THIS is why PA speakers use large drivers -- efficiency.
- **BL = 12.8 Tm**: A much stronger motor. This translates directly to that high sensitivity.
- **Le = 1.04 mH**: Higher. The impedance rises significantly at high frequencies, which means this driver's usable range does not extend as high. You would cross over to a compression driver at 1.5-2 kHz.

### 7.3 The Comparison Exercise

Same Fs, similar Qts -- but completely different applications. The RS150 is optimized for accuracy in a small box: low distortion, smooth response, extended bandwidth, but low sensitivity and modest output. The Delta-12A is optimized for output: massive sensitivity, huge power handling, but needs a huge box and has limited bandwidth.

This is the kind of analysis you should do for every driver you consider. The numbers tell the story if you know how to read them.

### 7.4 Tweeter Datasheet Considerations

When evaluating tweeters, the key specs are different:

- **Fs**: Should be well below the intended crossover frequency (at least 2:1 ratio, ideally 3:1). If you plan to cross over at 2.5 kHz, the tweeter's Fs should be below 1.2 kHz, preferably below 800 Hz.
- **Sensitivity**: Must be matchable to the woofer (usually by padding down with resistors).
- **Frequency response smoothness**: Look for a smooth, extended response up to 20 kHz. Avoid tweeters with sharp resonance peaks.
- **Off-axis response**: Especially important for tweeters since they handle the most directional frequencies.
- **Power handling**: Less critical than for woofers since the crossover protects tweeters from the power-hungry bass frequencies. But ensure the tweeter can handle realistic program power at the crossover frequency.
- **Resonance frequency and impedance**: The impedance peak at Fs must not fall within the crossover's passband, or you need an impedance compensation notch filter.

---

## 8. Chapter Summary and What Comes Next

In this chapter, you learned:

- SPL is measured in decibels; sensitivity ratings use either 1W or 2.83V reference (and the distinction matters for different impedances)
- The impedance curve reveals resonance frequency, minimum impedance, and enclosure behavior -- it is far more informative than the nominal rating
- Thiele-Small parameters are the driver's complete low-frequency specification: Fs (resonance), Qts/Qes/Qms (damping), Vas (compliance volume), Xmax (excursion), BL (motor strength), Le (inductance), Re (resistance)
- Frequency response graphs show usable bandwidth, breakup behavior, and off-axis performance
- Distortion increases at low frequencies (excursion limited) and high power levels
- Power handling has two limits: thermal (voice coil heat) and mechanical (excursion limit), and it varies with frequency
- Datasheet comparison is the foundation of driver selection for any build

You now have the vocabulary to describe any driver's behavior precisely. In Chapter 13, we will use this knowledge to design the crossover network that splits the signal between drivers -- the component that, more than anything else, determines how a multi-way speaker actually sounds.
