<!--
  CHAPTER: 37
  TITLE: System Tuning and Measurement
  PART: 6 — System Design & Integration
  PREREQS: Chapter 36
  KEY_TOPICS: REW deep dive, measurement microphone, frequency response, impedance measurement, distortion, waterfall plots, phase, subjective evaluation, target curves, EQ to taste
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 37: System Tuning and Measurement

> **Part 6 — System Design & Integration** | Prerequisites: Chapter 36 | Difficulty: Inter→Adv

Measurement is the bridge between "I think it sounds good" and "I know it sounds good, and here is the data." Every chapter in this guide has leaned on measurement at some point -- we measured impedance curves, frequency response, distortion, and Thiele-Small parameters in the speaker chapters; we measured gain, bandwidth, and THD in the amplifier chapters; we measured DSP filter response in the digital chapters. Now we bring all of that together to measure and tune a *complete system*: speakers, amplifier, room, and DSP working as one.

This chapter is a deep dive into Room EQ Wizard (REW), the free measurement tool that is the de facto standard for DIY audio. We will cover setup, technique, interpretation, and -- critically -- what to do with the numbers once you have them. Because measurement without action is just data hoarding, and action without measurement is just guessing.

The goal is not a perfectly flat frequency response. The goal is a system that sounds *right* -- to you, in your room, for your music. Measurement tells you where you are. Your ears tell you where you want to be. The gap between the two is what we close in this chapter.

### In This Chapter
- REW setup and measurement microphone selection
- Frequency response measurement and interpretation
- Impedance measurement
- Distortion measurement (THD vs frequency)
- Waterfall plots (cumulative spectral decay)
- Phase measurement
- Subjective listening evaluation and A/B testing
- Target curves: Harman, house curve, B&K
- EQ to taste vs EQ to flat

### Related Chapters
- [Ch 12: The Language of Speakers](../part-2-speakers/12-language-of-speakers.md) — speaker measurement fundamentals
- [Ch 18: How Amplifiers Work](../part-3-amplifiers/18-how-amplifiers-work.md) — distortion specs and what they mean
- [Ch 29: DSP with CamillaDSP](../part-4-digital-audio/29-dsp-camilladsp.md) — applying corrections based on measurements
- [Ch 34: Testing Procedures](../part-5-pcb-professional/34-testing-procedures.md) — systematic testing methodology
- [Ch 35: Matching Amps to Speakers](35-matching-amps-to-speakers.md) — power and impedance specs to verify
- [Ch 36: Room Acoustics and Treatment](36-room-acoustics-treatment.md) — treatment decisions based on measurements
- [Ch 38: Complete Build: Home Hi-Fi](38-complete-build-home-hifi.md) — applying these techniques to the final system
- [Ch 39: Complete Build: Party PA](39-complete-build-party-pa.md) — applying these techniques to the PA system

---

## 1. REW Setup

### 1.1 Software

Room EQ Wizard (REW) is free, runs on Windows, macOS, and Linux, and is absurdly powerful for a free tool. Download it from roomeqwizard.com. It requires Java, which it bundles on most platforms.

Install and launch. The interface looks busy, but you will mainly use four tabs:
- **SPL Meter**: Real-time sound pressure level display
- **Measure**: Where you take measurements
- **All SPL**: Overlay multiple frequency response measurements
- **Waterfall**: Time-frequency decay plots

### 1.2 The Measurement Microphone

Your measurement microphone is the most important piece of equipment in this chapter. An uncalibrated or poorly calibrated mic gives you bad data, and bad data leads to bad decisions.

**Recommended: miniDSP UMIK-1** (~$80)
- USB connection (no audio interface needed)
- Individually calibrated (calibration file included, download from miniDSP website)
- Omnidirectional measurement capsule
- Compatible with REW, CamillaDSP, and every other measurement tool
- Adequate for everything in this guide

**Alternative: Dayton Audio EMM-6** (~$50) + USB audio interface
- XLR connection (needs phantom power from an audio interface)
- Generic calibration file (less accurate than individually calibrated)
- Cheaper total cost if you already have an interface

**Do not use:** your laptop's built-in microphone, a podcast mic, a phone mic, or any mic without a known calibration file. These have wildly uneven frequency responses and will inject their own coloration into your measurements. You will be EQ'ing the mic's flaws instead of the room's flaws.

### 1.3 Loading the Calibration File

In REW, go to Preferences → Mic/Meter and load the calibration file for your specific microphone (the UMIK-1's file is downloaded from the miniDSP website using your mic's serial number). This calibration file tells REW the deviation from flat for your specific mic at each frequency, allowing it to compensate.

### 1.4 SPL Meter Calibration

REW needs to know the absolute SPL level. Without calibration, the frequency response shape is correct but the absolute level (in dB SPL) is unknown.

**Option 1:** Use the UMIK-1's built-in sensitivity rating (loaded with the calibration file). This gives you ±2 dB accuracy, which is good enough for room measurement.

**Option 2:** Use a standalone SPL meter (like a NIOSH-certified phone app or a dedicated meter) to measure a known test signal, then calibrate REW to match. This gets you to ±1 dB.

For our purposes, option 1 is fine. We are making relative comparisons (before treatment vs after treatment, position A vs position B), so absolute accuracy matters less than consistency.

---

## 2. Frequency Response Measurement

### 2.1 The Measurement Signal

REW uses a **logarithmic sine sweep** as its default measurement signal. This sweep starts at 20 Hz and ends at 20 kHz, taking about 5-10 seconds. The sweep method has significant advantages over pink noise (which some other tools use):

- Much better signal-to-noise ratio (the sweep concentrates all energy at one frequency at a time)
- Can separate direct sound from reflections using windowing
- Allows impulse response extraction
- Gives cleaner distortion measurements

When to use pink noise: for quick, rough SPL checks and real-time analyzer (RTA) display. When to use a sweep: for every serious measurement. Always use the sweep.

### 2.2 Measurement Distance

**1 meter (speaker measurement):**
Place the microphone 1 meter from the speaker, on-axis with the tweeter, at the same height as the tweeter. This is the standard distance for speaker measurements. At 1 meter, you are in the speaker's near field for most of the frequency range, which minimizes room influence. Use this distance for:
- Verifying your speaker's frequency response against VituixCAD predictions (Chapter 14)
- Checking crossover behavior
- Comparing speakers to each other

**Listening position (room measurement):**
Place the microphone at your listening position, at ear height when seated. This measures what you actually hear -- the speaker's output as modified by the room. Use this for:
- Room acoustics analysis
- Treatment evaluation
- EQ and DSP correction targeting

### 2.3 Taking a Measurement

1. Set the SPL to about 75-80 dB at the listening position (use the REW SPL meter and your system volume control). This is loud enough for good signal-to-noise but not fatiguing during multiple measurements.
2. In REW, go to the Measure panel. Set:
   - Start frequency: 20 Hz
   - End frequency: 20 kHz
   - Level: adjust until the signal is about 75-80 dB SPL
   - Length: 256k or 512k samples (longer = cleaner, especially at low frequencies)
3. Make sure the room is quiet. Turn off HVAC if possible. Close windows.
4. Click "Start Measuring." REW plays the sweep and captures the result.
5. The measurement appears as a frequency response graph.

### 2.4 Interpreting the Frequency Response

The raw measurement at your listening position will look like a roller coaster. This is normal. A typical room measurement has:

- **10-20 dB of variation** in the bass (20-200 Hz) from room modes
- **3-8 dB of variation** in the midrange (200 Hz - 2 kHz) from early reflections and furniture
- **5-10 dB of variation** in the treble (2-20 kHz) from speaker directivity and room reflections

**Smoothing:** REW can apply fractional-octave smoothing to the response. Use 1/6 octave smoothing for room measurements -- this averages out the narrowest peaks and dips, showing you the trend. Do not use heavy smoothing (1 octave or more) as it hides real problems. For speaker-only measurements at 1 meter, use 1/12 or 1/24 octave smoothing to see more detail.

**What to look for:**
- **Bass modes**: Narrow peaks of 10+ dB in the 30-150 Hz range. Compare to the calculated room modes from Chapter 36. The peaks should roughly correspond to the calculated mode frequencies.
- **Crossover region**: If your speaker has a crossover at 2.5 kHz, look for a dip or bump there. A well-designed crossover should be smooth through the transition.
- **Treble roll-off**: At the listening position, the treble naturally rolls off above 8-10 kHz due to room absorption and off-axis speaker behavior. A gentle roll-off is normal and expected.
- **Overall trend**: The response should generally follow a downward slope from bass to treble when measured at the listening position (more on this in section 8 on target curves).

### 2.5 Multiple Measurement Positions

A single measurement at one position captures the room interaction at that exact point. Move the mic six inches and the bass response changes significantly (room modes are position-dependent).

For a more representative picture, take 3-5 measurements in a cluster around your listening position: center, 6 inches left, 6 inches right, 6 inches forward, 6 inches back. REW can average these into a single response using its "Average" function. The average smooths out the worst position-dependent variations and gives you a better basis for EQ corrections.

> **What happens if... you EQ based on a single measurement point?** You might fix a dip that is specific to that one point while making things worse at adjacent positions. A room mode null at your exact measurement point might lead you to boost that frequency, which will be way too loud two feet to the side. Always average multiple positions for EQ decisions. Fix the peaks (which tend to be consistent across positions), not the dips (which are position-dependent and unfixable with EQ).

---

## 3. Impedance Measurement

### 3.1 Why Measure Impedance

You need the impedance curve of your speakers for three reasons:
1. **Verify compatibility with your amplifier** (Chapter 35 -- is the minimum impedance above the amp's rated load?)
2. **Set up a DSP crossover correctly** (the impedance affects the driver's behavior near crossover)
3. **Diagnose driver problems** (a damaged voice coil or failed capacitor changes the impedance curve)

### 3.2 Measurement Setup

REW can measure impedance using a simple series resistor method:

1. **Series resistor**: Place a precision resistor (10 ohms, 1% tolerance, 5W or larger) in series between the amplifier output and the speaker.
2. **Two connections to the audio interface**:
   - Channel 1: measures the voltage across the amplifier output (before the resistor)
   - Channel 2: measures the voltage across the speaker (after the resistor)
3. REW computes the impedance from the ratio of these two voltages and the known resistor value.

**Important**: Use a low drive level for impedance measurements. A few hundred milliwatts is enough. High levels can change the impedance (voice coil heating changes resistance, large excursion changes inductance).

### 3.3 Reading the Impedance Curve

A typical two-way speaker impedance curve shows:

- **Woofer resonance peak (Fs)**: A large peak (often 30-80 ohms) at the driver's resonant frequency. For a ported speaker, there are two peaks flanking the port tuning frequency, with a dip to nearly Re (DC resistance) between them.
- **Impedance minimum**: The lowest point, usually between the resonance peak and the crossover region. This is the number you compare against your amp's minimum load rating.
- **Crossover region**: The impedance may dip or have irregularities around the crossover frequency due to the interaction of the crossover components with the drivers.
- **Rising impedance at high frequencies**: The tweeter's voice coil inductance causes impedance to rise above 5-10 kHz.

Compare your measured curve to the simulation from VituixCAD (Chapter 14). They should be reasonably close. Large deviations indicate a build error: wrong crossover component value, wiring mistake, or damaged driver.

---

## 4. Distortion Measurement

### 4.1 THD vs Frequency

Total Harmonic Distortion (THD) measured as a function of frequency reveals where your system is working hard and where it is comfortable.

In REW, take a measurement at a moderate SPL (e.g., 85 dB at 1 meter). The "Distortion" chart shows THD at each frequency. Typical results for a well-built speaker:

- **20-50 Hz**: THD may be 3-10% -- the woofer is near its excursion limits at low frequencies. This is normal.
- **50-500 Hz**: THD should be under 1-2% in this range. Higher distortion here suggests the woofer is struggling (too small for the enclosure, misaligned voice coil, or port problems).
- **500 Hz - 3 kHz (crossover region)**: Watch for a THD bump near the crossover frequency. If both drivers are working hard near their limits at the crossover, distortion rises. A well-designed crossover minimizes this.
- **3-20 kHz**: THD should be well under 1%. Tweeters are very efficient in their passband and produce minimal distortion.

### 4.2 Distortion at Different Power Levels

Repeat the distortion measurement at several SPL levels: 75 dB, 85 dB, 95 dB. Compare the curves. You should see:

- **Distortion rises with level everywhere** (more power = more nonlinearity). This is expected.
- **Distortion rises faster at the frequency extremes** (low bass, high treble) because the drivers are closer to their mechanical and thermal limits there.
- **A specific frequency where distortion jumps** may indicate a resonance issue, a port chuff (turbulence in the bass reflex port at high levels), or a crossover problem.

This data tells you the *clean operating range* of your speaker. If THD stays under 1% up to 95 dB at 1 meter, you have a speaker that can play loud and clean. If THD hits 5% at 85 dB, your speaker is running out of steam earlier than desired.

> **What happens if... your distortion measurement shows 10% THD at 80 Hz?** At moderate levels, this suggests a problem. Check: Is the port too small (port chuffing causes turbulent distortion)? Is the woofer bottoming out (excursion exceeds Xmax)? Is there a loose screw or vibrating panel (mechanical buzz adds harmonic content)? Press gently on the woofer cone to check for rubbing. Listen at close range for buzzes. Measure again with just a sine tone at 80 Hz and slowly increase the volume -- the frequency at which distortion jumps usually points to the cause.

---

## 5. Waterfall Plots (Cumulative Spectral Decay)

### 5.1 What They Show

A waterfall plot is a 3D graph showing how energy at each frequency decays over time after the signal stops. The X-axis is frequency, the Y-axis is time (typically 0-300 milliseconds), and the Z-axis (shown as color or as the cascading "waterfall") is energy level.

**What you want to see:** All frequencies decay at roughly the same rate, and everything is down by 20-30 dB within 100-200 ms. The waterfall should look like a smooth cliff dropping away uniformly.

**What you do not want to see:**
- **Ridges** at specific frequencies that persist for a long time (300+ ms). These are resonances -- either room modes (in room measurements) or cabinet/driver resonances (in speaker measurements at 1 meter).
- **A bass range that decays much slower than the rest.** This is classic room mode ringing. The mode stores energy and releases it slowly, creating the "boomy" bass that room treatment addresses.

### 5.2 Reading the Plot

In REW, after taking a measurement, switch to the Waterfall tab. Set the time range to 300 ms and the level range to 30 dB. You should see:

- **Midrange and treble**: Rapid decay. Should be down 20 dB within 50-100 ms.
- **Bass**: Slower decay, especially at room mode frequencies. In an untreated room, some modes may ring for 300-500 ms. After treatment with bass traps, these should reduce to 150-250 ms.

Compare the waterfall before and after room treatment. The improvement in bass decay is usually dramatic and visually obvious.

### 5.3 Speaker Cabinet Resonances

When measuring at 1 meter (near-field), waterfall plots can reveal cabinet resonances. A poorly braced cabinet panel vibrates at its resonant frequency, adding coloration. You see this as a ridge in the waterfall at the panel's resonant frequency (often 200-500 Hz for a typical bookshelf speaker).

The fix: add internal bracing to the cabinet at the offending panel. This was covered in the speaker build chapters (16, 17), but the waterfall plot is how you verify that your bracing is working.

---

## 6. Phase Measurement

### 6.1 Why Phase Matters

Phase describes the timing relationship between frequencies. In a speaker system, the phase is affected by:

- **The crossover**: Each filter section rotates the phase. A second-order (12 dB/octave) filter adds 180 degrees of phase rotation. A fourth-order adds 360 degrees.
- **Driver acoustic centers**: The woofer and tweeter are physically offset (the tweeter is in front of the woofer, or vice versa). This offset adds a frequency-dependent phase difference.
- **Room reflections**: Each reflection adds its own phase contribution.

### 6.2 Phase Through the Crossover

In a well-designed crossover, the phase of the woofer's output and the tweeter's output should be aligned at the crossover frequency. If they are not, the two drivers partially cancel each other, creating a dip in the response at crossover.

REW shows the phase on the same graph as the frequency response (click "Show Phase" in the measurement display). Look at the crossover region:

- **Good alignment**: The phase transitions smoothly through the crossover frequency without sudden jumps. The two drivers' outputs are in phase (or are designed to be exactly 180 degrees apart, in a "reverse polarity" crossover design).
- **Poor alignment**: A 90-180 degree jump at the crossover frequency, accompanied by a dip in the frequency response. This usually means the crossover topology is wrong or the drivers' acoustic centers are not aligned.

For our active crossover setup (CamillaDSP from Chapter 29), phase alignment is straightforward: you design a Linkwitz-Riley crossover (which sums flat) and add delay to the closer driver to align acoustic centers. Measure, verify, adjust.

### 6.3 Phase in Room Measurements

At the listening position, the phase plot is often useless because room reflections add random phase contributions at every frequency. Do not try to EQ based on phase measured at the listening position. Phase alignment should be done with near-field measurements (1 meter, on-axis) where the direct sound dominates.

---

## 7. Subjective Listening Evaluation

### 7.1 Why Measurement Is Not Everything

Here is a sentence that might surprise you, seven sections into a measurement chapter: **measurement is a tool, not the goal.** Your system is for listening to music, not for producing flat graphs.

Measurement tells you the objective truth about your system's frequency response, distortion, and decay characteristics. But it cannot tell you:
- Whether you *enjoy* the sound
- Whether the system handles complex musical passages with composure
- Whether the soundstage feels natural and three-dimensional
- Whether the system fatigues you after an hour of listening

These qualities are subjective but real. And they require you to sit down, close your eyes, and listen.

### 7.2 A/B Testing Methodology

When comparing two configurations (before and after EQ, two different speaker positions, two different amps), follow these rules for honest evaluation:

**Level matching**: This is the most critical step. A difference of even 0.5 dB in level is perceived as "better" -- the louder signal always sounds better, even if it is objectively worse. Match levels to within 0.5 dB using the REW SPL meter or a measurement mic. In CamillaDSP, this means adjusting the gain so both configurations produce the same SPL at the listening position.

**Quick switching**: The auditory memory is short -- about 2-5 seconds. If it takes you 30 seconds to switch between configurations, you are relying on memory rather than perception, and memory is unreliable. Set up your A/B comparison so you can switch in under 2 seconds. In CamillaDSP, you can set up two configuration files and switch between them quickly.

**Blind testing (if possible)**: Have someone else do the switching without telling you which configuration is active. This eliminates expectation bias. You would be amazed how many "night and day" differences disappear under blind conditions.

**Fatigue awareness**: After 30-60 minutes of critical listening, your ears adapt and your judgment degrades. Take breaks. Come back the next day and listen again. If a change still sounds better on the second day, it is real.

**Focus on specifics**: Do not ask "which sounds better overall?" That is too vague. Ask specific questions:
- Which has tighter, more controlled bass?
- Which has clearer vocals?
- Which has a wider, more convincing stereo image?
- Which can I listen to for longer without fatigue?

### 7.3 The Difference Between "Accurate" and "Preferred"

Research from Harman International (the company behind JBL, AKG, Mark Levinson, and others) has consistently shown that most listeners do not prefer a perfectly flat in-room response. They prefer a response that:

- Has a gentle bass boost of 3-6 dB below 200 Hz
- Is relatively flat from 200 Hz to 2 kHz
- Has a gentle high-frequency roll-off starting around 3-5 kHz
- Falls about 5-8 dB from 1 kHz to 20 kHz

This is not a flaw in human perception -- it is how we naturally hear in rooms. Direct sound from a speaker that measures flat at 1 meter arrives with a slight treble emphasis (because the mic was on-axis, but your ears are not always perfectly aimed at the speaker). Room reflections add proportionally more bass energy. The combination should sound balanced, and research shows it does when the in-room response follows this contoured curve.

> **What happens if... you EQ your system to be perfectly flat at the listening position?** It will sound thin and bright. The bass will seem lacking because you have removed the expected room gain contribution. The treble will seem aggressive because you have matched on-axis high-frequency level to the bass level, but in a room the treble naturally attenuates more than bass. Flat in-room response is not what any professional mastering studio targets, and it is not what sounds best to most listeners.

---

## 8. Target Curves

### 8.1 The Harman Target

The Harman target curve (also called the Harman preference curve) is the result of extensive listening tests conducted by Harman International, led by researcher Dr. Sean Olive. Hundreds of listeners were asked to adjust the EQ of various speakers until they sounded "best" to them. The average preferred curve was remarkably consistent across listeners:

```
Approximate Harman Target (in-room, at listening position):

20 Hz:    +6 dB (relative to 1 kHz)
50 Hz:    +5 dB
100 Hz:   +4 dB
200 Hz:   +2 dB
500 Hz:   +1 dB
1 kHz:     0 dB (reference)
2 kHz:    -1 dB
5 kHz:    -2 dB
10 kHz:   -4 dB
20 kHz:   -6 dB
```

This gives a gentle downward slope from bass to treble. You can load this as a target curve in REW (import a target file) and then see how your measured response compares.

### 8.2 The B&K (Bruel & Kjaer) Curve

Before the Harman research, the B&K curve was the industry standard for evaluating loudspeakers in rooms. It is similar in shape -- a gradual downward slope -- but is based on measurements in a standardized listening room rather than listener preference data. Many professional studios use a variant of the B&K curve as their reference.

For practical purposes, the Harman target and B&K curve are similar enough that either is a valid starting point. The Harman target has the stronger scientific backing.

### 8.3 Your House Curve

A "house curve" is simply your personal preference target. Maybe you like more bass than the Harman target suggests. Maybe you are treble-sensitive and prefer a steeper roll-off above 5 kHz. Maybe you listen to a lot of bass-heavy electronic music and want +8 dB at 50 Hz.

This is entirely valid. The purpose of measurement is to give you control, not to dictate taste. Use the Harman target as a starting point, listen, and adjust until you are happy.

**How to define your house curve:**
1. Measure your system's in-room response at the listening position
2. Apply the Harman target in CamillaDSP as a starting EQ
3. Listen for a few days with music you know well
4. Adjust: more bass? less treble? a midrange dip for recordings that sound harsh?
5. Measure again with your adjustments active
6. Save this as your personal house curve in CamillaDSP

---

## 9. EQ to Taste vs EQ to Flat

### 9.1 What DSP EQ Can Fix

DSP equalization (via CamillaDSP from Chapter 29) is incredibly powerful but has limitations:

**EQ CAN fix:**
- Room mode peaks (narrow peaks of 10+ dB that are consistent across measurement positions). Use a parametric EQ filter: set the frequency to the peak, the Q to match the peak's width, and reduce the gain to bring it down.
- Overall tonal balance (the trend from bass to treble). Use a shelf filter or a series of gentle broad parametric filters.
- Speaker frequency response deviations (a 3 dB bump at 2 kHz from a crossover issue). Use a narrow parametric cut.

**EQ CANNOT fix:**
- Room mode nulls (dips caused by cancellation). Boosting a null sends more power to the speaker at that frequency, which creates more sound that... also cancels. You waste amplifier power, increase distortion, and still have a null. Never boost nulls.
- Time-domain problems (reverb, echoes, smeared reflections). EQ changes level, not timing. This is why physical treatment (Chapter 36) is essential and DSP cannot replace it entirely.
- Directivity changes (you cannot make a beamy speaker sound like a wide-dispersion one with EQ).

### 9.2 The EQ Workflow

Here is the practical workflow for system EQ using REW and CamillaDSP:

**Step 1: Measure at multiple positions around the listening area** (section 2.5). Average the responses.

**Step 2: Import or define your target curve** (Harman, B&K, or personal house curve).

**Step 3: Identify peaks to cut.** Look for narrow peaks that rise above the target curve. These are almost always room modes. For each one:
- Frequency: center of the peak
- Q: narrow enough to affect only the peak (typically Q = 5-10 for room modes)
- Gain: negative, enough to bring the peak down to the target level

**Step 4: Apply broad tonal corrections.** If the overall trend deviates from the target (too much bass, too bright, etc.), use low-shelf, high-shelf, or broad parametric filters (Q = 0.5-1.5) to tilt the response toward the target.

**Step 5: Do NOT fill in dips.** Resist the urge to boost frequencies that are below the target. If a dip is consistent across multiple measurement positions, it might be a speaker response issue (fixable with a gentle boost). If it varies between positions, it is a room null (not fixable with EQ).

**Step 6: Load the EQ into CamillaDSP.** REW can export filter parameters in a format compatible with CamillaDSP's parametric EQ. Alternatively, you can generate FIR (Finite Impulse Response) filters for more precise correction.

**Step 7: Measure again with EQ active.** Verify that the peaks are reduced and the overall response is closer to the target.

**Step 8: Listen.** Does it sound better? If yes, great. If something sounds wrong despite measuring well, trust your ears and adjust. The measurement is a guide, not a master.

### 9.3 Parametric EQ vs FIR Convolution

CamillaDSP supports both:

**Parametric EQ (IIR filters):**
- Familiar: frequency, Q, gain -- same controls as any audio EQ
- Low latency (a few samples of delay)
- Introduces phase shifts (all IIR filters do)
- Good for broad corrections and room mode taming

**FIR convolution:**
- Can correct both magnitude and phase simultaneously
- Higher latency (half the filter length, typically 10-50 ms)
- Can target very specific frequency/time domain issues
- REW can generate FIR correction filters directly

For most home hi-fi use, parametric EQ is sufficient and simpler. FIR convolution is the step beyond, used when you want to also correct phase irregularities or apply very precise frequency-domain corrections. The latency penalty of FIR is usually irrelevant for music listening but can matter for video (lip-sync) or live performance (monitoring).

> **What happens if... you apply too much EQ?** Aggressive EQ creates problems. Deep cuts reduce headroom (the amp has to work harder at other frequencies to maintain relative balance). Narrow boosts can cause ringing (the filter itself resonates). Too many filters in series accumulate phase shifts. If you find yourself applying more than 10-12 filters with a total correction range exceeding ±10 dB, stop and reassess. Either your room needs more physical treatment, your speaker placement needs adjustment, or your speaker has a fundamental response problem that EQ cannot solve gracefully.

---

## 10. The Complete Measurement Session

### 10.1 Before You Start

- Room treatment is installed (Chapter 36)
- Speakers are placed and aimed (Chapter 36)
- Amp and DSP are connected and functioning (Chapter 35, 29)
- Measurement mic is set up with calibration file loaded
- Room is quiet (HVAC off, windows closed)
- REW is configured with correct audio device settings

### 10.2 The Sequence

1. **SPL calibration**: Play pink noise through your system. Verify the SPL meter in REW reads a reasonable value (75-80 dB at the listening position).

2. **Near-field speaker measurement (1m)**: One speaker at a time, 1 meter on-axis with the tweeter. This gives you the speaker's intrinsic response, separated from room effects. Compare to your VituixCAD simulation. Check the crossover region for smooth transition.

3. **Impedance measurement**: Verify the impedance curve matches your expectations. Check minimum impedance against amp specifications. Look for anomalies (a flattened resonance peak might indicate a damaged driver; a missing peak might indicate a wiring error).

4. **In-room frequency response (listening position)**: Both speakers playing simultaneously. Measure at 5 positions around the listening area. Average them.

5. **Waterfall plot**: From the listening position measurement. Check for room mode ringing and any cabinet resonances that propagate into the room.

6. **RT60 measurement**: REW can estimate RT60 from the impulse response. Target: 0.3-0.5 seconds for home hi-fi.

7. **Compare to target curve**: Overlay the Harman target (or your preferred target) on the averaged in-room response.

8. **Design EQ corrections**: Create parametric EQ filters to bring the response toward the target. Prioritize cutting peaks over boosting dips.

9. **Implement in CamillaDSP**: Transfer the filter settings to your CamillaDSP configuration.

10. **Verify**: Measure again with DSP active. Confirm corrections are working.

11. **Listen**: A/B compare the corrected system against the uncorrected system (save both CamillaDSP configs). Level-match and compare.

### 10.3 Documenting Your Results

Save all REW measurements with descriptive names:
- `Living Room - Left Speaker - 1m On-Axis - 2026-04-10`
- `Living Room - Both Speakers - Listening Position Center - Pre-EQ`
- `Living Room - Both Speakers - Listening Position Center - Post-EQ`

These become your baseline. Six months from now, if something sounds "off," re-measure and compare to the saved baseline. A driver failure, a crossover component degradation, or even a change in furniture will show up in the measurements.

---

## 11. Summary

The measurement workflow in this chapter is the quality control process for your entire audio system. It validates every decision you made in Parts 2-5:

- **Speaker build**: Does the measured frequency response match the simulation?
- **Amplifier match**: Does the impedance curve confirm compatibility?
- **Room treatment**: Did the bass traps reduce mode ringing? Did the panels clean up reflections?
- **DSP correction**: Is the final in-room response close to your target curve?

If all four answers are yes, you have a system that is not just assembled but *tuned*. The difference between an assembled system and a tuned system is the difference between a race car with the engine bolted in and a race car that has been dyno-tuned and aligned. Same hardware, dramatically different performance.

In the next two chapters, we put every single piece together -- every component from every Part of this guide -- into two complete systems: a home hi-fi rig for pure listening pleasure, and a party PA rig for making a crowd dance. Measurement is what ties them together.
