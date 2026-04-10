<!--
  CHAPTER: 34
  TITLE: Testing, Quality Control, and Documentation
  PART: 5 — PCB Design & Professional Builds
  PREREQS: Chapter 33
  KEY_TOPICS: systematic testing, pedal testing, speaker testing, amplifier testing, digital testing, burn-in, measurement, spec verification, test checklists, build documentation, BOM, assembly guide, user manual, version control for hardware, photo documentation
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 34: Testing, Quality Control, and Documentation

> **Part 5 — PCB Design & Professional Builds** | Prerequisites: Chapter 33 | Difficulty: Intermediate

Every software engineer knows the pain of discovering a bug in production. A customer reports a crash, you dig through logs, you find a null pointer that was never checked, and you think: "If only I had written a test for that." Now translate that to hardware. A customer reports that their custom pedal buzzes. You ask them to send it back. You open it up. The solder joint on R14 is a cold joint. It passed your initial plug-in-and-play test because R14 is in the tone circuit and you did not listen carefully to the mids. If only you had a test procedure that checked the tone circuit specifically.

Testing in audio is not optional, and "I plugged it in and it sounded fine" is not testing. It is the hardware equivalent of `console.log("works")`. Real testing is systematic: you define what "correct" means for every measurable parameter, you measure it, you record the result, and you do this the same way every time. Quality control is the process that ensures every unit meets the standard. And documentation is what lets you (or someone else) reproduce the build, understand the design, and troubleshoot problems.

This chapter brings Parts 0-5 to their culmination. You designed the circuit (Parts 1-4), laid out the PCB (Ch 31), built the enclosure (Ch 32), finished it (Ch 33), and now you verify it works, document how it works, and create the infrastructure for building more of them. This is the chapter that separates a one-time builder from a repeatable operation.

### In This Chapter
- Systematic testing by product type: pedals, speakers, amplifiers, digital
- Burn-in testing
- Measurement and specification verification
- Creating test checklists
- Build documentation: BOM, assembly guide, schematic, user manual
- Version control for hardware: KiCad + git workflow
- Photo documentation and portfolio building

### Related Chapters
- [Ch 4: Your Workbench, Tools, and Safety](../part-0-electronics/04-workbench-tools-safety.md) — measurement tools and safety procedures
- [Ch 9: Build: Overdrive and Distortion](../part-1-guitar-pedals/09-build-overdrive-distortion.md) — the pedal we test
- [Ch 15: Design Tools and Simulation](../part-2-speakers/15-design-tools-simulation.md) — REW measurement (referenced for speaker testing)
- [Ch 16: Build: Bookshelf Speakers](../part-2-speakers/16-build-bookshelf-speakers.md) — the speakers we test
- [Ch 22: Safety: Working with Mains and High Voltage](../part-3-amplifiers/22-safety-mains-high-voltage.md) — safety procedures for amp testing
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) — the amps we test
- [Ch 28: Build: Pi Streamer](../part-4-digital-audio/28-build-pi-streamer.md) — the digital build we test
- [Ch 31: PCB Design for Audio](31-pcb-design-for-audio.md) — KiCad + git version control
- [Ch 42: Building a Brand and Selling](../part-7-business/42-building-brand-selling.md) — QC feeds into customer confidence
- [Ch 43: Scaling Up](../part-7-business/43-scaling-up.md) — documentation enables batch production

---

## 1. The Philosophy of Testing

### 1.1 Why "It Sounds Fine" Is Not a Test

When you plug in a pedal and strum a chord, you are performing an informal integration test. You are checking one thing: "Does sound come out?" But a build can pass this test while harboring a dozen defects:

- A cold solder joint that works now but will fail after thermal cycling.
- A noise floor 10 dB higher than it should be (you did not notice because you were playing loud).
- An incorrect component value that shifts the frequency response away from the design target.
- A power supply drawing twice the expected current (heading for early component failure).
- A ground loop that only manifests when the pedal is in a chain with other pedals.
- Reversed polarity protection that does not actually work (you never tested it with reversed polarity).

Each of these defects is invisible to the "strum and listen" test. Each will eventually manifest as a customer complaint, a warranty return, or a damaged reputation.

### 1.2 The Test Mindset

Approach testing with the same rigor you would apply to automated testing in software:

- **Define expected behavior first.** Before testing, write down what "correct" looks like for every parameter. The Tube Screamer should draw 4-8 mA at 9V. The noise floor should be below -80 dBV with the effect engaged and no input signal. The bypass should be true bypass (zero insertion loss).
- **Test every parameter independently.** Do not rely on one test to cover multiple parameters. Test bypass separately from the effect circuit. Test the power supply separately from the signal path.
- **Record results.** Write down the measured values. Not "sounds good" -- the actual numbers. "Current draw: 5.2 mA. Noise floor: -84 dBV. Bypass insertion loss: 0 dB."
- **Compare to spec.** If you designed for a specific gain or frequency response, measure it and compare. Deviations are not automatically problems, but they should be understood and documented.

### 1.3 Test Equipment You Need

You already have most of this from [Ch 4](../part-0-electronics/04-workbench-tools-safety.md):

| Equipment | Tests Enabled | Already Own? |
|-----------|--------------|-------------|
| Multimeter | DC voltage, current draw, resistance, continuity | Yes (Ch 4) |
| Oscilloscope | Signal amplitude, waveform shape, noise, frequency | Yes (Ch 4) |
| Audio interface (or DAC/ADC) | Frequency response, noise floor, distortion | Likely (Ch 25) |
| REW (Room EQ Wizard) software | Speaker measurement, frequency response, impedance | Free download |
| UMIK-1 measurement microphone | Speaker frequency response, SPL | Recommended (Ch 15) |
| Dummy load resistors (4 ohm, 8 ohm, 50W+) | Amplifier output power, distortion | Build or buy |
| Signal generator (or software + audio interface) | Inject known signals for measurement | Software is free |
| Dim bulb tester | Safe power-up for amps (Ch 22) | Yes (Ch 22) |
| Thermometer (IR or thermocouple) | Thermal testing | Recommended |

---

## 2. Systematic Testing: Pedals

### 2.1 Pre-Power Checks

Before applying any power to the pedal, do these checks with the power disconnected:

**Visual inspection:**
- All solder joints shiny and properly wetted (no cold joints, no bridges).
- Correct component placement (check polarity on electrolytic caps, diodes, ICs).
- No solder splashes or debris on the PCB.
- No damaged traces (scratches, cuts, over-etched areas).

**Continuity checks:**
- No short between VCC and GND. Set your multimeter to continuity mode. Touch VCC pad and GND pad. If it beeps, you have a short -- find and fix it before applying power.
- Input jack tip to circuit input -- continuity when bypass is engaged (true bypass).
- Input jack tip to output jack tip -- continuity when bypass is engaged, no continuity when effect is engaged (the signal goes through the effect circuit instead of passing straight through).

### 2.2 Power-Up Tests

**Current draw measurement:**
1. Set multimeter to DC current mode (mA range).
2. Insert multimeter in series with the positive power lead (between the power supply and the pedal).
3. Apply 9V DC power.
4. Read the current draw.

Expected values:

| Pedal Type | Typical Current Draw | Concern Threshold |
|-----------|---------------------|-------------------|
| Simple boost/buffer | 1-5 mA | > 10 mA |
| Overdrive / distortion (op-amp) | 3-10 mA | > 20 mA |
| Fuzz (transistor-based) | 1-5 mA | > 10 mA |
| Modulation (analog) | 5-15 mA | > 30 mA |
| Delay (PT2399) | 20-40 mA | > 60 mA |
| Digital effects (DSP-based) | 50-150 mA | > 200 mA |

If the current draw is significantly higher than expected, power down immediately and inspect for shorts.

**Voltage checks:**
- VCC rail: should be close to 9V (or whatever your supply voltage is). If it is significantly lower, the power supply section has a problem (shorted bypass cap, excessive current draw).
- VBIAS (if applicable): should be approximately half of VCC (4.5V for a 9V supply). If it is not at the midpoint, check the voltage divider resistors and the bias capacitor.
- Op-amp output pins (at idle, no signal): should be at VBIAS. If they are railed to VCC or GND, the op-amp circuit has a problem (open feedback path, shorted input).

### 2.3 Signal Path Tests

**Bypass test:**
1. Apply an audio signal to the input (guitar, or a 1 kHz sine wave from a signal generator).
2. With the effect bypassed, verify signal passes from input to output with no loss and no added noise.
3. Toggle the bypass on and off several times. No clicks, pops, or dropouts.

**Effect engaged test:**
1. Engage the effect.
2. Apply a 1 kHz sine wave at approximately guitar level (100-500 mV peak).
3. Verify the output signal is present and at the expected level.
4. Sweep the frequency from 20 Hz to 20 kHz (if you have a sweep generator) and verify the frequency response matches the design.
5. For overdrive/distortion: verify clipping occurs at the expected input level and the clipping character is correct (soft vs hard).

**Noise floor test:**
1. Effect engaged, no input signal (or input shorted to ground).
2. Turn gain to maximum.
3. Measure the output noise with a multimeter (AC volts) or oscilloscope.
4. A well-designed pedal should have a noise floor below -70 dBV (relative to 1 Vrms). Below -80 dBV is good. Below -90 dBV is excellent.

### 2.4 Mechanical Tests

- **Switch feel**: The footswitch should click firmly and positively. No hesitation, no double-click.
- **Pot taper**: Each pot should sweep smoothly through its range. No scratchy spots, no dead zones, no sudden jumps. Audio taper (log) pots should have a gradual increase; linear pots should increase evenly.
- **Knob security**: Every knob should be firmly attached. Set-screws tight. No wobble.
- **Jack engagement**: Plugs should insert and remove smoothly. The tip contact should be firm.
- **LED**: Verify the LED lights when the effect is engaged and turns off when bypassed. Check brightness -- it should be visible but not blinding (see [Ch 33, section 6](33-professional-finishing-aesthetics.md)).

### 2.5 Pedal Test Checklist

Here is a ready-to-use checklist for the Tube Screamer clone from [Ch 9](../part-1-guitar-pedals/09-build-overdrive-distortion.md):

```
PEDAL TEST CHECKLIST
Model: TS-808 Clone v1.0
Serial: _______________
Date: _______________
Tester: _______________

PRE-POWER
[ ] Visual inspection: solder joints, component placement, polarity ............ PASS / FAIL
[ ] VCC-GND short check: no continuity ......................................... PASS / FAIL
[ ] True bypass continuity: input to output in bypass mode ..................... PASS / FAIL

POWER-UP
[ ] Current draw: _______ mA  (expected: 4-8 mA) ............................. PASS / FAIL
[ ] VCC voltage: _______ V  (expected: ~9V) ................................... PASS / FAIL
[ ] VBIAS voltage: _______ V  (expected: ~4.5V) ............................... PASS / FAIL
[ ] Op-amp pin 1 (output A): _______ V  (expected: ~4.5V) .................... PASS / FAIL
[ ] Op-amp pin 7 (output B): _______ V  (expected: ~4.5V) .................... PASS / FAIL

SIGNAL PATH
[ ] Bypass: signal passes, no loss, no noise .................................. PASS / FAIL
[ ] Effect engaged: signal present at output .................................. PASS / FAIL
[ ] Drive sweep: clipping increases with drive knob ........................... PASS / FAIL
[ ] Tone sweep: treble content changes with tone knob ......................... PASS / FAIL
[ ] Level sweep: output volume changes with level knob ........................ PASS / FAIL
[ ] Noise floor (effect on, no input, max gain): _______ dBV ................. PASS / FAIL

MECHANICAL
[ ] Footswitch: firm click, no bounce ......................................... PASS / FAIL
[ ] Drive pot: smooth sweep, no scratching .................................... PASS / FAIL
[ ] Tone pot: smooth sweep, no scratching ..................................... PASS / FAIL
[ ] Level pot: smooth sweep, no scratching .................................... PASS / FAIL
[ ] Knob security: all tight, no wobble ....................................... PASS / FAIL
[ ] Input jack: firm engagement ............................................... PASS / FAIL
[ ] Output jack: firm engagement .............................................. PASS / FAIL
[ ] DC jack: firm engagement .................................................. PASS / FAIL
[ ] LED: illuminates when engaged, off when bypassed .......................... PASS / FAIL
[ ] LED brightness: visible, not blinding ..................................... PASS / FAIL
[ ] Enclosure: no rattles, back plate secure .................................. PASS / FAIL

RESULT: PASS / FAIL
Notes: _______________________________________________________________
Signed: _______________
```

Print this. Check every box. Every unit. No exceptions.

---

## 3. Systematic Testing: Speakers

### 3.1 Impedance Measurement

The first test for a completed speaker is impedance verification. This confirms the drivers are wired correctly and the crossover is assembled properly.

**What to measure:**
- DC resistance (Re) at the speaker terminals: should be roughly 60-80% of the nominal impedance. An 8-ohm speaker typically measures 5.5-7 ohms DC resistance.
- If you have access to an impedance measurement system (DATS, REW + audio interface), measure the impedance curve across frequency. It should show the characteristic peaks and dips that match your crossover design.

**What indicates a problem:**
- Open circuit (infinite resistance): a broken wire, a disconnected driver, or a failed crossover component.
- Very low resistance (< 2 ohms for an 8-ohm system): a short circuit somewhere, possibly a solder bridge in the crossover or a pinched wire.
- Impedance curve that does not match the design: a crossover component with the wrong value, or a driver wired out of phase.

### 3.2 Frequency Response Measurement

This is where [Ch 15: Design Tools and Simulation](../part-2-speakers/15-design-tools-simulation.md) and your REW measurement skills come together. You simulated the speaker in VituixCAD -- now you verify the real thing matches the simulation.

**Measurement setup:**
1. Place the speaker on a stand in the measurement position. Outdoors or in a large room is ideal (to minimize room reflections). Indoors, use gated (windowed) measurements in REW to exclude room reflections.
2. Position the UMIK-1 measurement microphone at 1 meter distance, on the tweeter axis (or the designed listening axis).
3. Run a frequency sweep from 20 Hz to 20 kHz using REW.
4. Compare the measured response to the VituixCAD simulation.

**What to look for:**
- The overall shape should match the simulation within +/- 3 dB through the crossover region.
- The crossover frequency should produce a smooth transition, not a deep notch or sharp peak.
- Low-frequency extension should match the predicted -3 dB point (within 10-15% for a ported design, closer for sealed).
- No sharp, narrow peaks or dips in the midrange -- these indicate diffraction, resonance, or a crossover problem.

**Common discrepancies and causes:**

| Measurement vs Simulation | Likely Cause |
|--------------------------|-------------|
| Deep notch at crossover frequency | Drivers wired out of phase (swap +/- on one driver) |
| Elevated tweeter level | L-pad attenuation too low, or wrong resistor value in crossover |
| Reduced tweeter level | L-pad attenuation too high, or wrong resistor value |
| Bass rolloff higher than predicted | Cabinet leak, wrong port dimensions, or wrong driver |
| Broad peak at 500-2000 Hz | Baffle diffraction (missing round-over on edges) |
| Narrow resonance peak | Panel vibration (missing bracing) or crossover resonance |

### 3.3 Distortion Check

Play music or a test tone at the rated power level and listen for:
- **Buzzing or rattling**: Loose components inside the cabinet, loose driver screws, or objects vibrating against the cabinet.
- **Port chuffing**: A "whooshing" noise from the port at high bass levels. Indicates the air velocity in the port is too high (port diameter too small or excursion too high).
- **Driver distortion**: At very high levels, the driver reaches its excursion limits. Listen for sudden distortion onset as you increase volume. This should match the power handling spec.

### 3.4 Cabinet Seal Test

**The lean test**: With the back panel fully sealed, press gently on the woofer cone. You should feel firm resistance. The cone should push back when you release (the compressed air inside pushes it back).

If the cone pushes in easily with little resistance, air is leaking. Common leak locations:
- Back panel gasket
- Driver mounting (missing gasket)
- Terminal cup
- Port joint (in a ported cabinet)
- Panel joints (inadequate glue or seal)

Find and fix every leak. In a sealed cabinet, even a small leak defeats the purpose of the sealed enclosure. In a ported cabinet, leaks change the effective port tuning.

### 3.5 Polarity Check

All drivers in a multi-way speaker must be wired in phase (in the same polarity). Out-of-phase drivers create a cancellation at the crossover frequency that sounds like a "hole" in the midrange.

**Battery test**: Disconnect the speaker from the amplifier. Touch a 1.5V AA battery briefly to the speaker terminals (positive to positive, negative to negative). Watch the woofer cone -- it should move OUTWARD (toward you). If it moves inward, the wiring polarity is reversed.

Do this for each driver individually to verify polarity. Then verify with a measurement: an in-phase system should show smooth response through the crossover; an out-of-phase system shows a deep null at the crossover frequency.

### 3.6 Speaker Test Checklist

```
SPEAKER TEST CHECKLIST
Model: 2-Way Bookshelf v1.0
Serial: _______________
Date: _______________
Tester: _______________

IMPEDANCE
[ ] DC resistance at terminals: _______ ohms (expected: _______ ohms) ........ PASS / FAIL
[ ] No short circuit (resistance > 3 ohms for 8-ohm system) .................. PASS / FAIL

FREQUENCY RESPONSE
[ ] Measured response matches simulation within +/- 3 dB crossover region ..... PASS / FAIL
[ ] No deep notches or sharp peaks in 200 Hz - 10 kHz range .................. PASS / FAIL
[ ] Bass extension (-3 dB point): _______ Hz (expected: _______ Hz) .......... PASS / FAIL
[ ] Crossover transition smooth ................................................ PASS / FAIL

PHYSICAL
[ ] Cabinet seal test: firm resistance when pressing woofer cone .............. PASS / FAIL
[ ] No rattles or buzzes at moderate listening level .......................... PASS / FAIL
[ ] No port chuffing at moderate bass levels (ported only) .................... PASS / FAIL
[ ] Driver polarity: woofer correct, tweeter correct .......................... PASS / FAIL
[ ] Crossover component values verified against schematic ..................... PASS / FAIL

COSMETIC
[ ] Cabinet finish: no defects, even coating .................................. PASS / FAIL
[ ] Driver mounting: all screws tight, even gap ............................... PASS / FAIL
[ ] Terminal cup: secure, labeled correctly ................................... PASS / FAIL
[ ] Grille: fits correctly, does not rattle (if applicable) ................... PASS / FAIL

PAIR MATCHING (for stereo pair)
[ ] Left/right impedance within 0.5 ohms ..................................... PASS / FAIL
[ ] Left/right frequency response within 1 dB ................................ PASS / FAIL

RESULT: PASS / FAIL
Notes: _______________________________________________________________
Signed: _______________
```

---

## 4. Systematic Testing: Amplifiers

### 4.1 Safety First

Amplifier testing involves mains voltage (for the power supply) and potentially lethal voltages (tube amps). Before any amplifier testing, review [Ch 22: Safety](../part-3-amplifiers/22-safety-mains-high-voltage.md).

**Non-negotiable safety rules for amp testing:**
- Use a **dim bulb tester** for the first power-up of any new amplifier build. A 60W incandescent bulb in series with the mains limits fault current and prevents catastrophic failure.
- **Never** probe inside a tube amp with both hands. One-hand rule: keep one hand in your pocket.
- **Always** discharge filter capacitors before touching anything in a tube amp. Use your discharge resistor from [Ch 22](../part-3-amplifiers/22-safety-mains-high-voltage.md).
- **Always** use a fuse. If the fuse blows during testing, find the problem. Do not replace the fuse with a larger one.
- **Never** operate a power amplifier without a load connected. Solid-state amps can survive (most have protection), but tube amps can arc and destroy the output transformer.

### 4.2 DC Offset at Output

Before connecting speakers, check the DC offset at the amplifier output:

1. Power up the amplifier with no input signal and no speaker connected.
2. Measure DC voltage between the speaker output terminals with a multimeter set to DC volts.
3. The reading should be less than 50 mV. Ideally less than 20 mV.

**Why this matters:** A DC offset at the output means a constant DC current will flow through your speaker's voice coil. This pushes the cone to one side of its travel (offset from center), reducing available excursion in one direction and causing asymmetric distortion. Large DC offsets (>100 mV) can overheat the voice coil and damage the speaker.

If the DC offset is too high:
- For the LM3886 chip amp from [Ch 23](../part-3-amplifiers/23-build-chip-amp-class-d.md): check the input DC blocking capacitor and the offset trim circuit if present.
- For tube amps: check the phase inverter balance and the output transformer center tap.
- For Class D amps: the TPA3255 module from [Ch 23](../part-3-amplifiers/23-build-chip-amp-class-d.md) should have negligible DC offset. If it does not, the module may be defective.

### 4.3 Power Output Measurement

**Setup:**
1. Connect a dummy load resistor to the speaker output. Use a resistor that matches the amplifier's rated impedance (8 ohms typical). The resistor must be rated for the power you are testing -- a 50W resistor for a 50W amp. For higher powers, use a parallel combination of smaller resistors or a dedicated load bank.
2. Apply a 1 kHz sine wave to the input at a level that drives the amplifier just below clipping.
3. Measure the AC RMS voltage across the dummy load with a multimeter or oscilloscope.
4. Calculate power: P = V^2 / R.

**Example**: The LM3886 into 8 ohms. If you measure 18 Vrms across the load: P = 18^2 / 8 = 40.5 watts. The LM3886 is rated for 38W continuous into 8 ohms, so this is right on spec.

**Clipping detection**: Increase the input level while watching the output on an oscilloscope. When the sine wave tops and bottoms start to flatten (clip), you have reached maximum clean power. Note the voltage at this point -- that is your maximum unclipped power.

### 4.4 Distortion Measurement

If you have an audio interface and analysis software (REW, ARTA, or even Audacity with analysis plugins):

1. Feed a 1 kHz sine wave at a level that produces 1W into the dummy load (2.83 Vrms into 8 ohms).
2. Record the output through the audio interface (use an attenuator -- do not feed amplifier output levels directly into a line input).
3. Analyze the recording for THD (Total Harmonic Distortion).

Expected THD:
- Class D (TPA3255): < 0.1% at 1W
- LM3886 chip amp: < 0.05% at 1W
- Tube amp (5F1 style): 1-5% at 1W (tube distortion is the point)

### 4.5 Frequency Response

Sweep a sine wave from 20 Hz to 20 kHz at constant amplitude. Record the output level at each frequency (or use REW to do an automated sweep). The amplifier should be flat (+/- 1 dB) from at least 20 Hz to 20 kHz for hi-fi applications.

Roll-offs at the frequency extremes indicate:
- Low-frequency rolloff: Input coupling capacitor too small or power supply sag.
- High-frequency rolloff: Compensation capacitor too large, bandwidth-limited op-amp, or cable capacitance.

For guitar amps, the frequency response is intentionally shaped (the tone stack is a deliberate EQ curve), so "flat" is not the target. Compare to the expected tone stack response.

### 4.6 Noise Floor

1. Short the input (connect a short cable from the input jack to itself, or use a shorting plug).
2. Set gain to maximum.
3. Measure the output noise with a multimeter (AC volts, RMS) or record it and analyze in software.
4. A good hi-fi amp should have a noise floor below -80 dBV (referenced to 1 Vrms) with the input shorted.

### 4.7 Protection Circuit Verification

For solid-state amplifiers with protection circuits:

**Short-circuit protection**: Briefly short the speaker output (touch the positive and negative terminals together for 1-2 seconds). The protection circuit should engage immediately (typically by shutting down the output or limiting current). After removing the short, the amplifier should recover to normal operation.

**Do not do this casually.** Short-circuit testing stresses components. Do it once to verify, not repeatedly.

**Thermal protection**: Run the amplifier at half rated power (25W for a 50W amp) into a dummy load for 30 minutes. Monitor the heat sink temperature with a thermometer. The heat sink should stabilize (reach thermal equilibrium) at a temperature well below the thermal shutdown threshold (typically 80-100C for the LM3886).

If the heat sink temperature is still climbing after 30 minutes without stabilizing, your thermal management is inadequate -- the heat sink is too small or needs better airflow.

### 4.8 Safety Verification

This is the most critical test category for amplifiers, especially mains-powered ones.

**Chassis ground continuity:**
- With the amp unplugged, measure continuity between the chassis ground terminal (where the mains ground wire connects) and multiple points on the chassis.
- The chassis must be solidly grounded. This is the safety ground that protects against electrocution if a mains wire contacts the chassis.
- Resistance should be less than 0.5 ohms from the ground terminal to any point on the chassis.

**Fuse verification:**
- Verify the correct fuse value is installed (check the schematic).
- Verify the fuse blows under a fault condition (this is a destructive test -- only if you have spare fuses and want to verify).

**No exposed live metal:**
- With the amp fully assembled, visually inspect all external surfaces. No mains-voltage-carrying conductor should be accessible without opening the chassis.
- Check: mains input wiring is inside the chassis. IEC connector pins are recessed. Fuse holder does not expose contacts. Speaker terminals cannot contact the chassis.

### 4.9 Amplifier Test Checklist

```
AMPLIFIER TEST CHECKLIST
Model: LM3886 Chip Amp v1.0
Serial: _______________
Date: _______________
Tester: _______________

SAFETY (complete these first)
[ ] Dim bulb tester: lamp glows dimly then stabilizes ....................... PASS / FAIL
[ ] Chassis ground continuity: < 0.5 ohm to all chassis points ............. PASS / FAIL
[ ] Fuse installed: correct value (_____ A) .................................. PASS / FAIL
[ ] No exposed live metal ..................................................... PASS / FAIL
[ ] Mains wiring secure, strain reliefs in place ............................. PASS / FAIL

DC AND POWER
[ ] DC offset at output: _______ mV (expected: < 50 mV) .................... PASS / FAIL
[ ] Power supply voltage: +_______ V / -_______ V (expected: +/- _____ V) .. PASS / FAIL
[ ] Idle current draw: _______ mA ............................................. PASS / FAIL

PERFORMANCE
[ ] Max clean power into 8 ohms: _______ W (expected: _______ W) ........... PASS / FAIL
[ ] THD at 1W: _______ % (expected: < _______ %) ........................... PASS / FAIL
[ ] Frequency response: +/- _____ dB, 20 Hz - 20 kHz ....................... PASS / FAIL
[ ] Noise floor (input shorted, max gain): _______ dBV ...................... PASS / FAIL

PROTECTION
[ ] Short-circuit protection: engages, recovers .............................. PASS / FAIL
[ ] Thermal test (half power, 30 min): heat sink _______ C, stable ......... PASS / FAIL

MECHANICAL
[ ] All controls smooth, no scratching ........................................ PASS / FAIL
[ ] All jacks secure ......................................................... PASS / FAIL
[ ] Enclosure: no rattles, ventilation clear ................................. PASS / FAIL
[ ] Power switch: firm, positive action ....................................... PASS / FAIL

RESULT: PASS / FAIL
Notes: _______________________________________________________________
Signed: _______________
```

---

## 5. Systematic Testing: Digital Audio

### 5.1 Pi Streamer Tests

The Pi streamer from [Ch 28](../part-4-digital-audio/28-build-pi-streamer.md) has different test requirements -- it is software-heavy and the failure modes are different from analog circuits.

**Functional tests:**
- **Boot test**: Power on. The system should boot to the Volumio/moOde interface within 60-90 seconds. If it does not boot, check the SD card, power supply, and Pi status LED.
- **Network discovery**: The streamer should appear on the local network. Verify you can access the web interface from a browser on another device.
- **Physical controls**: Every physical control should work:
  - Rotary encoder: turn clockwise (volume up), counterclockwise (volume down), press (play/pause or mute).
  - Buttons: each button performs its assigned function.
  - OLED display: shows correct information (track title, volume level, source).
- **Audio output**: Play a test track. Verify audio comes from the DAC output at the expected quality.
- **Multiple source test**: Test each streaming source independently:
  - Spotify Connect: stream from the Spotify app.
  - AirPlay: stream from an iOS device.
  - DLNA/UPnP: stream from a DLNA server.
  - Local files: play from a USB drive or network share.
  - Bluetooth (if enabled): pair and stream from a phone.
- **Gapless playback**: Play two consecutive tracks that should be gapless (a live album, a concept album). Verify no gap or click at the transition.
- **DSP pipeline** (if configured per [Ch 29](../part-4-digital-audio/29-build-active-crossover-room-correction.md)): Verify CamillaDSP is processing audio correctly. Check that EQ, crossover, and room correction are active and correct.

### 5.2 Digital Audio Quality Tests

- **Bit-perfect output**: Play a known 16-bit/44.1 kHz test file and verify the DAC is receiving a 16/44.1 stream (not resampled). Check the DAC status (some DACs show the incoming sample rate).
- **Latency**: For real-time use (like a DSP crossover), measure the latency from input to output. Use a loopback test with a click signal.
- **Dropout test**: Stream music for several hours. Note any dropouts, glitches, or interruptions. Dropouts indicate network issues, insufficient processing power, or buffer configuration problems.

### 5.3 Digital Test Checklist

```
DIGITAL AUDIO TEST CHECKLIST
Model: Pi Streamer v1.0
Serial: _______________
Date: _______________
Tester: _______________

BOOT AND NETWORK
[ ] Boot to interface within 90 seconds ....................................... PASS / FAIL
[ ] Web interface accessible from browser ..................................... PASS / FAIL
[ ] Network discovery (appears in app) ........................................ PASS / FAIL

PHYSICAL CONTROLS
[ ] Rotary encoder: volume up/down ............................................ PASS / FAIL
[ ] Rotary encoder: press (play/pause) ........................................ PASS / FAIL
[ ] Buttons: all functions correct ............................................. PASS / FAIL
[ ] OLED display: shows track info, volume .................................... PASS / FAIL

STREAMING SOURCES
[ ] Spotify Connect ............................................................ PASS / FAIL
[ ] AirPlay ................................................................... PASS / FAIL
[ ] DLNA/UPnP ................................................................. PASS / FAIL
[ ] Local files (USB) ......................................................... PASS / FAIL
[ ] Bluetooth (if applicable) ................................................. PASS / FAIL

AUDIO QUALITY
[ ] Audio output: clean, no noise ............................................. PASS / FAIL
[ ] Gapless playback .......................................................... PASS / FAIL
[ ] Bit-perfect output verified ............................................... PASS / FAIL

STABILITY
[ ] 4-hour continuous playback: no dropouts ................................... PASS / FAIL

RESULT: PASS / FAIL
Notes: _______________________________________________________________
Signed: _______________
```

---

## 6. Burn-In Testing

### 6.1 What Burn-In Does

Burn-in is the process of running a completed unit at moderate operating levels for an extended period (24-48 hours) before final delivery. The purpose is to catch **infant mortality failures** -- components that are marginal and will fail early in their lifespan.

The bathtub curve of component failure rates shows that failures are most likely in two periods: very early (manufacturing defects, weak components) and very late (wear-out). Burn-in catches the early failures so they happen on your bench instead of at the customer's house.

### 6.2 Burn-In Procedures by Product

**Pedals:**
- Connect the pedal in a signal chain with a looping audio source.
- Set all controls to moderate positions (drive at noon, tone at noon, level at noon).
- Run for 24 hours.
- After burn-in, repeat the signal path tests from section 2.3.

**Speakers:**
- Play music at moderate listening level (80-85 dB SPL) for 24-48 hours.
- This also serves as a **break-in** period for the driver suspension, which loosens slightly with use. The change is subtle but measurable.
- After burn-in, re-measure frequency response and compare to initial measurement. Minor changes (1-2 dB below 100 Hz) are normal.

**Amplifiers:**
- Connect to a dummy load.
- Play music (or a repeating test signal) at 25-50% rated power for 48 hours.
- Monitor heat sink temperature periodically. It should remain stable.
- After burn-in, check DC offset and current draw again. Compare to initial values.
- Re-run the safety tests.

**Digital:**
- Leave the streamer running and streaming music for 48 hours.
- Verify no crashes, no memory leaks (check system memory usage), no audio dropouts.
- Power cycle 5 times to verify reliable boot.

### 6.3 Thermal Cycling

For products that will experience temperature changes (portable PA equipment, outdoor installations, vehicle audio):

1. Run the unit at room temperature (22C) for 2 hours.
2. Place in a cool environment (10C -- an unheated garage in winter, or a cooler with ice packs nearby but not touching) for 2 hours while still running.
3. Return to room temperature for 2 hours.
4. Repeat 3-5 cycles.
5. Test all parameters after cycling.

Thermal cycling stresses solder joints and component connections. A marginal solder joint that works at room temperature may fail when the board contracts in the cold and re-expands when warm.

---

## 7. Measurement and Spec Verification

### 7.1 Does It Meet Spec?

If you designed your build to specific targets (a speaker with a -3 dB point at 50 Hz, an amplifier with 40W output, a pedal with a specific gain range), the final step is verifying those specifications.

Create a **specification vs measurement** table:

```
SPECIFICATION VERIFICATION
Model: 2-Way Bookshelf Speaker v1.0

| Parameter              | Design Target    | Measured Value | Within Spec? |
|------------------------|-----------------|----------------|--------------|
| Impedance (nominal)    | 8 ohms          | 7.8 ohms       | YES          |
| Sensitivity (1W/1m)    | 85 dB SPL       | 84.2 dB SPL    | YES (within 1dB) |
| Frequency response     | +/- 3 dB 60Hz-20kHz | +/- 2.8 dB 62Hz-20kHz | YES |
| -3 dB point (bass)     | 55 Hz           | 58 Hz          | CLOSE (see notes) |
| Crossover frequency    | 2.5 kHz         | 2.5 kHz        | YES          |
| Max SPL (at 1m)        | > 100 dB        | 103 dB         | YES          |

Notes: Bass extension slightly higher than target. Port may be slightly short.
Consider lengthening port by 10mm on v1.1.
```

This table serves two purposes: it tells you whether the build is acceptable for delivery, and it creates a record that informs the next revision.

### 7.2 Documenting Deviations

If a measurement does not meet the target, document it with:
- **The measured value** (not "slightly off" -- the number).
- **The probable cause** (component tolerance, layout issue, measurement limitation).
- **The impact** (audible? negligible? functional problem?).
- **The action** (accept as-is, rework this unit, revise the design for next version).

This is the same discipline as post-incident reviews in software. The value is not in the document itself but in the thinking process it forces.

---

## 8. Build Documentation

### 8.1 Why Documentation Matters

Documentation is the difference between a one-off project and a reproducible product. If you build one pedal and it works, great. If you want to build ten more, or hand the design to someone else, or sell it, you need documentation. And the best time to create documentation is while you are building the first unit, not six months later when you have forgotten half the details.

### 8.2 Bill of Materials (BOM)

The BOM is a complete list of every component in the build, with enough detail to source and identify each one.

**BOM format:**

```
BILL OF MATERIALS
Model: TS-808 Clone v1.0
Date: 2026-04-10
Designer: [Your Name]

| Ref  | Description           | Value    | Package        | Qty | Supplier    | Part Number    | Unit Cost |
|------|-----------------------|----------|----------------|-----|-------------|----------------|-----------|
| R1   | Resistor              | 510k     | 0805           | 1   | Mouser      | 603-RC0805FR... | $0.02    |
| R2   | Resistor              | 10k      | 0805           | 1   | Mouser      | 603-RC0805FR... | $0.02    |
| R3   | Resistor              | 4.7k     | 0805           | 1   | Mouser      | 603-RC0805FR... | $0.02    |
| ...  | ...                   | ...      | ...            | ... | ...         | ...            | ...       |
| C1   | Ceramic Capacitor     | 100nF    | 0805           | 1   | Mouser      | 80-C0805C104... | $0.03    |
| C2   | Film Capacitor        | 47nF     | Box 5mm        | 1   | Mouser      | 80-R82EC2470... | $0.15    |
| C7   | Electrolytic Cap      | 47uF/25V | Radial D5mm    | 1   | Mouser      | 647-UVR1E470... | $0.10    |
| U1   | Op-Amp IC             | JRC4558  | DIP-8          | 1   | Tayda       | A-1001         | $0.35    |
| D1   | Signal Diode          | 1N4148   | DO-35          | 2   | Mouser      | 78-1N4148      | $0.05    |
| D3   | Schottky Diode        | 1N5817   | DO-41          | 1   | Mouser      | 78-1N5817      | $0.20    |
| LED1 | LED (blue, diffused)  | 5mm      | Through-hole   | 1   | Tayda       | A-1554         | $0.08    |
| SW1  | 3PDT Footswitch       | -        | Panel mount    | 1   | Tayda       | A-3500         | $1.50    |
| J1   | Mono Audio Jack       | 6.35mm   | Panel mount    | 1   | Tayda       | A-2563         | $0.50    |
| J2   | Mono Audio Jack       | 6.35mm   | Panel mount    | 1   | Tayda       | A-2563         | $0.50    |
| J3   | DC Barrel Jack        | 2.1mm    | Panel mount    | 1   | Tayda       | A-2237         | $0.30    |
| ENC1 | Hammond Enclosure     | 1590B    | -              | 1   | Tayda       | A-1234         | $4.50    |

SUBTOTAL (components): $XX.XX
PCB (5 boards from JLCPCB): $2.00/board
Enclosure: $4.50
Knobs (3x): $2.00
Hardware (screws, standoffs, wire): $2.00
TOTAL PER UNIT: $XX.XX
```

KiCad can export a BOM directly from the schematic (Tools → Generate BOM). The output is a CSV that you can open in a spreadsheet and annotate with supplier information and pricing. Store this CSV in your git repository alongside the KiCad project files.

### 8.3 Assembly Guide

An assembly guide is a step-by-step document that lets someone (including future-you) build the unit from a pile of parts.

**Assembly guide structure:**

1. **Tools required**: List every tool needed for the build.
2. **Pre-assembly checks**: Verify all BOM components are present. Check PCB for manufacturing defects.
3. **SMD component soldering**: Start with SMD components (lowest profile first). List each component by reference designator and value.
4. **Through-hole component soldering**: Next, through-hole components from shortest to tallest.
5. **IC socket installation**: Install sockets before ICs.
6. **Off-board wiring**: Wire connections to panel-mounted components.
7. **Enclosure assembly**: Mount PCB, install panel-mounted components.
8. **IC insertion**: Insert ICs into sockets.
9. **Testing**: Reference the test checklist.

Include photos at key steps. A photo of the correct orientation for the IC, a photo of the completed SMD side before through-hole assembly, a photo of the off-board wiring. Photos are worth thousands of words when someone is staring at a half-built board wondering which way D3 goes.

### 8.4 Schematic (As-Built)

The as-built schematic is the final, verified schematic that matches the actual produced unit. During development, you may have made changes from the original design -- different component values, added components, removed components. The as-built schematic reflects what was actually built, not what was originally planned.

Tag this version in git. It is the manufacturing record.

### 8.5 User Manual

If you are selling your builds (or even giving them to friends), include a simple user manual:

**User manual contents:**
1. **What the product does**: One paragraph description.
2. **Controls**: Each knob, switch, and jack explained. What it does, what the range is, where to start.
3. **Connection guide**: How to connect the product to the rest of the signal chain. What goes in, what comes out, what power it needs.
4. **Specifications**: Key specs (impedance, power, voltage, frequency response).
5. **Troubleshooting**: Common issues and solutions. "No sound? Check the bypass switch. Check the power supply. Check the cable."
6. **Safety information**: For amplifiers: do not open, mains voltage inside. For speakers: maximum power handling.
7. **Warranty/support**: How to contact you if something is wrong.

Keep it concise. One page for a pedal. Two to three pages for a speaker or amplifier.

---

## 9. Version Control for Hardware

### 9.1 KiCad + Git: The Full Workflow

We introduced KiCad + git in [Ch 31](31-pcb-design-for-audio.md). Here is the complete workflow for hardware version control:

**Repository structure:**

```
tube-screamer-pcb/
├── .gitignore
├── README.md
├── hardware/
│   ├── tube-screamer.kicad_pro
│   ├── tube-screamer.kicad_sch
│   ├── tube-screamer.kicad_pcb
│   ├── tube-screamer.kicad_dru
│   ├── fp-lib-table
│   ├── sym-lib-table
│   └── custom-libs/
│       ├── my-audio-symbols.kicad_sym
│       └── my-audio-footprints.pretty/
├── bom/
│   └── tube-screamer-bom.csv
├── docs/
│   ├── assembly-guide.md
│   ├── test-checklist.md
│   └── user-manual.md
├── gerbers/                    # Generated files -- optionally tracked
│   └── v1.0/
│       ├── tube-screamer-F_Cu.gbr
│       └── ...
└── photos/
    ├── v1.0-assembled.jpg
    └── v1.0-pcb-bare.jpg
```

### 9.2 Branching Strategy

Use a simple branching strategy:

- **main**: The current production version. Every commit on main should be a buildable, tested version.
- **dev**: Active development. Experimental changes, untested modifications.
- **Feature branches**: For specific changes (e.g., `feature/smd-opamp`, `fix/ground-plane-gap`).

Merge to main only when the changes have been tested on a physical board.

### 9.3 Tagging Releases

Tag every version you actually manufacture:

```bash
git tag -a v1.0 -m "First production run - 10 boards ordered from JLCPCB 2026-04-10"
git tag -a v1.1 -m "Fixed footprint for C5, wider power traces - 5 boards 2026-05-15"
git tag -a v2.0 -m "Major layout revision: SMD op-amp, smaller board - 10 boards 2026-07-01"
```

When a customer has a problem with a v1.0 board, you can check out that tag and see exactly what they have. When you want to compare v1.0 and v2.0, you can diff them.

### 9.4 Tracking the BOM in Git

The BOM CSV file belongs in the repository. Every time you change a component (different supplier, different value, different package), update the BOM and commit:

```bash
git add bom/tube-screamer-bom.csv
git commit -m "BOM: switch R7 from Mouser to Tayda (30% cheaper, same spec)"
```

This creates a complete history of every component change and the reason for it. When [Ch 43: Scaling Up](../part-7-business/43-scaling-up.md) talks about batch production, you will need this history to manage supply chain changes.

---

## 10. Photo Documentation

### 10.1 Why Photographs Matter

Photograph every build. Every single one. For three reasons:

1. **Portfolio**: If you sell your builds ([Ch 42](../part-7-business/42-building-brand-selling.md)), photos are your primary sales tool. A good photo sells a pedal better than any description.
2. **Build record**: If a unit comes back for repair, photos of the original assembly help you identify what changed.
3. **Documentation**: Photos supplement your assembly guide. "Solder R14 as shown" is clearer when there is an actual photo showing R14's position.

### 10.2 What to Photograph

| Subject | Purpose | When |
|---------|---------|------|
| Bare PCB (front and back) | Manufacturing record, defect documentation | When boards arrive from manufacturer |
| Populated PCB (SMD complete) | Assembly documentation, inspection record | After SMD soldering, before through-hole |
| Populated PCB (complete) | Assembly documentation, inspection record | After all soldering |
| Enclosure interior (assembled) | Build record, troubleshooting reference | After final assembly |
| Completed unit (exterior, all angles) | Portfolio, sales photos, customer documentation | After final assembly and testing |
| Detail shots (knobs, LED, jacks) | Portfolio, quality documentation | After final assembly |
| Test setup | Verification record, troubleshooting | During testing |

### 10.3 Photography Tips for Audio Gear

You do not need a professional camera. A modern smartphone takes adequate photos for documentation and good-enough photos for sales. But technique matters:

- **Lighting**: Use natural light or a desk lamp at 45 degrees. Avoid direct overhead light (creates harsh shadows) and flash (creates reflections on metal surfaces and washes out colors). A cloudy day near a window is the best lighting you can get for free.
- **Background**: Plain, uncluttered. A sheet of white paper works. A piece of dark fabric works. Do not photograph your pedal on a cluttered workbench -- it looks messy and the eye does not know where to focus.
- **Angle**: For pedals, a slight overhead angle (30-45 degrees from horizontal) shows the top surface and gives a sense of depth. For speakers, a straight-on shot shows the driver and cabinet proportions. For amps, a 3/4 angle shows the front panel and top panel simultaneously.
- **Focus**: Tap to focus on the product. The background should be slightly soft (out of focus), putting visual emphasis on the build.
- **Scale**: Include something for scale reference in at least one photo -- a hand, a guitar pick, a standard cable. People need to understand how big the product is.

### 10.4 Building a Portfolio

Over time, your photo documentation becomes a portfolio. Organize it:

```
portfolio/
├── pedals/
│   ├── ts-clone-v1/
│   │   ├── hero-shot.jpg
│   │   ├── angle-1.jpg
│   │   ├── detail-knobs.jpg
│   │   └── interior.jpg
│   └── fuzz-v2/
│       └── ...
├── speakers/
│   ├── bookshelf-v1/
│   │   ├── hero-shot.jpg
│   │   ├── pair-shot.jpg
│   │   └── detail-crossover.jpg
│   └── ...
└── amps/
    └── ...
```

Your best photos go on your website, social media, or marketplace listings ([Ch 42](../part-7-business/42-building-brand-selling.md)). The complete set goes into your build records.

---

## 11. Putting It All Together

### 11.1 The Complete Quality Pipeline

From start to finish, a professional build follows this pipeline:

```
Design → PCB (Ch 31) → Enclosure (Ch 32) → Finishing (Ch 33) 
    → Assembly → Testing (this chapter) → Documentation (this chapter)
        → Delivery or Sale (Ch 42)
```

At each stage, quality is verified before proceeding to the next. You do not assemble a PCB that failed DRC. You do not finish an enclosure that does not fit the PCB. You do not deliver a unit that did not pass the test checklist. Each stage is a quality gate.

### 11.2 The Minimum Viable Documentation Set

For every build you intend to reproduce (or sell), maintain at least:

1. **Schematic** (KiCad, in git)
2. **PCB layout** (KiCad, in git)
3. **BOM** (CSV, in git)
4. **Test checklist** (completed and filed per serial number)
5. **Photos** (at least one exterior photo per unit)

For builds you intend to sell at scale, add:

6. **Assembly guide** (step-by-step with photos)
7. **User manual** (customer-facing)
8. **Specification sheet** (measured, not just designed)

### 11.3 What This Enables

All the work in this chapter -- the testing, the documentation, the version control, the photos -- enables everything in [Part 7: From Hobby to Hustle](../part-7-business/). Without test records, you cannot offer a warranty. Without a BOM, you cannot quote a price. Without an assembly guide, you cannot hire someone to help you build. Without photos, you cannot sell online. Without version control, you cannot manage product revisions.

This chapter is the bridge between "I can build one of these" and "I can build a hundred of these and every single one meets the same standard."

---

## 12. Summary

Testing is verification. Quality control is consistency. Documentation is memory. Together, they transform your builds from individual achievements into a repeatable practice.

The test checklists in this chapter are starting points -- customize them for each product. The documentation templates are minimal -- expand them as your builds grow more complex. The version control workflow is standard git -- adapt it to your preferences. The photo practice is a habit -- start now, even if your first photos are mediocre. Everything improves with practice.

With Part 5 complete, you have the full stack: circuit design from earlier Parts, PCB layout from Ch 31, enclosure fabrication from Ch 32, professional finishing from Ch 33, and testing, documentation, and version control from this chapter. You can take any audio circuit, produce it as a professional product, verify it meets spec, and document it for reproduction.

Next stop: [Part 6](../part-6-system-design/) brings everything together into complete sound systems, and [Part 7](../part-7-business/) turns your builds into a business.
