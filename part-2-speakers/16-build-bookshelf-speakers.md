<!--
  CHAPTER: 16
  TITLE: Build: Bookshelf Speakers
  PART: 2 — Speakers
  PREREQS: Chapter 15
  KEY_TOPICS: 2-way bookshelf, driver selection, crossover design in VituixCAD, cabinet design in WinISD, construction, crossover assembly, measurement, listening evaluation
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 16: Build: Bookshelf Speakers

> **Part 2 — Speakers** | Prerequisites: Chapter 15 | Difficulty: Intermediate

This is it. Five chapters of theory, specifications, design techniques, and simulation tools -- and now we build. In this chapter, you are going to design and construct a pair of 2-way bookshelf speakers from raw drivers, passive crossover components, and MDF panels. These will not be a kit with pre-cut parts and a pre-designed crossover. You are going to make every decision yourself, based on everything you have learned.

By the end, you will have a pair of speakers that you designed, built, measured, and tuned -- speakers whose every component value you can explain and justify. When someone asks "why did you use that crossover frequency?" or "why is the port tuned to that frequency?", you will have a real answer, not "because the kit instructions said so." That is the difference between following a recipe and knowing how to cook.

Let us build something worth listening to.

### In This Chapter
- Design brief: what we are building and why
- Driver selection: evaluating candidates with datasheets and simulation
- Crossover design in VituixCAD
- Cabinet design in WinISD
- Cabinet construction: cutting, gluing, bracing, finishing
- Crossover assembly: components, soldering, wiring
- Final assembly and measurement with REW
- Listening evaluation and adjustment

### Related Chapters
- [Ch 12: The Language of Speakers](12-language-of-speakers.md) -- driver specs we reference throughout
- [Ch 13: Crossover Design](13-crossover-design.md) -- crossover theory behind our design
- [Ch 14: Cabinet Design](14-cabinet-design.md) -- enclosure theory behind our design
- [Ch 15: Design Tools and Simulation](15-design-tools-simulation.md) -- the tools we use for design
- [Ch 17: Build: Towers, PA, and Sub](17-build-towers-pa-subwoofer.md) -- the next builds
- [Ch 18: Amplifier Matching](../part-3-amplifiers/18-amplifier-matching.md) -- pairing these with an amp
- [Ch 32: Professional Cabinet Finishing](../part-5-pcb-professional/32-cabinet-finishing.md) -- advanced finishing options

---

## 1. Design Brief

### 1.1 What We Are Building

A pair of **2-way bookshelf speakers** for home listening. The "2-way" means we use two drivers per speaker: a woofer (handling bass and midrange) and a tweeter (handling treble). A passive crossover inside each cabinet splits the signal.

### 1.2 Design Goals

- **Use case**: Music listening in a small to medium room (15-25 square meters / 160-270 square feet)
- **Placement**: On stands, away from walls (free-standing), or on a bookshelf near a wall
- **Target frequency response**: 55 Hz to 20 kHz, plus or minus 3 dB (with room gain bringing the effective bass lower)
- **Target SPL**: Comfortable listening levels up to 90 dB at the listening position (about 2-3 meters distance)
- **Amplifier requirement**: Compatible with typical home amplifiers (50-100W into 4 or 8 ohms)
- **Budget**: $150-250 per pair (drivers + crossover components + cabinet materials), excluding tools

### 1.3 Design Decisions Up Front

- **Enclosure type**: We will evaluate both sealed and ported, and choose based on simulation
- **Crossover type**: Passive, likely 2nd or 4th order Linkwitz-Riley
- **Crossover frequency**: 2,000-3,000 Hz (determined by driver selection)
- **Cabinet material**: 18mm MDF with vinyl wrap or paint finish

---

## 2. Driver Selection

Driver selection is the most consequential decision in the entire build. We are going to evaluate candidates methodically, using the datasheet analysis skills from Chapter 12.

### 2.1 Woofer Candidates

We need a 5" to 6.5" woofer with:
- Fs below 60 Hz (for adequate bass in a bookshelf)
- Qts between 0.3 and 0.5 (works in sealed or ported)
- Vas under 20 liters (does not need a huge box)
- Xmax of at least 5mm (adequate output at bookshelf volumes)
- Smooth response to at least 3 kHz (so we have crossover options)
- Low Le (for easy crossover design)

**Candidate 1: SB Acoustics SB15NRXC30-4 (5" woofer)**

```
Impedance: 4 ohm | Re: 3.2 ohm
Fs: 52 Hz | Qts: 0.40 | Qes: 0.47 | Qms: 2.69
Vas: 7.3 L | Xmax: 5.5 mm
BL: 5.1 Tm | Le: 0.25 mH
Sd: 81 cm^2 | Sensitivity: 84 dB (2.83V/m)
Power handling: 40W RMS
```

Why it is interesting: SB Acoustics makes excellent drivers at reasonable prices. The low Le (0.25 mH) means minimal impedance rise at high frequencies -- easy for the crossover. The Qts of 0.40 works beautifully in either sealed or ported. The Vas of 7.3 liters means a compact box. The smooth response (per published data) extends to 5 kHz+ before breakup, giving us flexibility on crossover frequency.

**Candidate 2: Dayton Audio RS150-4 (5.25" woofer)**

```
Impedance: 4 ohm | Re: 3.4 ohm
Fs: 55 Hz | Qts: 0.46 | Qes: 0.53 | Qms: 3.49
Vas: 8.5 L | Xmax: 5.75 mm
BL: 5.3 Tm | Le: 0.32 mH
Sd: 86 cm^2 | Sensitivity: 84.6 dB (2.83V/m)
Power handling: 40W RMS
```

Why it is interesting: The Reference Series from Dayton Audio offers excellent value. Slightly larger cone area than the SB, similar sensitivity. The Qts of 0.46 is higher, which means a sealed enclosure will have a Qtc around 0.65-0.7 in a typical box -- good Butterworth alignment. FRD/ZMA measurement files are available from Dayton's website, making VituixCAD modeling easy.

**Candidate 3: SB Acoustics SB17NRXC35-4 (6.5" woofer)**

```
Impedance: 4 ohm | Re: 3.2 ohm
Fs: 37 Hz | Qts: 0.33 | Qes: 0.37 | Qms: 3.05
Vas: 18.5 L | Xmax: 6 mm
BL: 6.7 Tm | Le: 0.35 mH
Sd: 132 cm^2 | Sensitivity: 86 dB (2.83V/m)
Power handling: 50W RMS
```

Why it is interesting: Larger driver means more bass capability. The lower Fs (37 Hz) and larger Sd give significantly deeper bass potential. But the Vas of 18.5 liters means a larger box -- 12-15 liters minimum for sealed, 15-20 for ported. That is getting large for a "bookshelf" speaker but is doable. The low Qts (0.33) strongly favors sealed operation.

### 2.2 Tweeter Candidates

We need a 1" dome tweeter with:
- Fs below 1,000 Hz (well below any crossover frequency we would choose)
- Smooth response from 1.5 kHz to 20 kHz
- Sensitivity that can be matched to the woofer (within 6 dB, padded down with an L-pad)
- Good off-axis behavior (wide, smooth dispersion)

**Candidate A: SB Acoustics SB26STAC-C000-4 (1" ring radiator tweeter)**

```
Impedance: 4 ohm | Re: 3.2 ohm
Fs: 500 Hz | Sensitivity: 92 dB (2.83V/m)
Power handling: 60W RMS
```

Why it is interesting: Ring radiator tweeters have excellent off-axis response and low distortion. The 92 dB sensitivity is about 8 dB above our woofer candidates -- easily padded down with an L-pad. The low Fs (500 Hz) means we can cross as low as 1.5 kHz if needed. SB Acoustics provides FRD/ZMA files.

**Candidate B: Dayton Audio RST28A-4 (1.1" aluminum dome tweeter)**

```
Impedance: 4 ohm | Re: 3.3 ohm
Fs: 800 Hz | Sensitivity: 88 dB (2.83V/m)
Power handling: 40W RMS
```

Why it is interesting: Good match for the Dayton RS woofers. Lower sensitivity difference to the woofer (only 3-4 dB to pad down). Aluminum dome with smooth response and a waveguide that improves off-axis behavior. Less expensive than the SB tweeter.

### 2.3 Making the Choice

For this build, let us go with the **SB Acoustics SB15NRXC30-4 woofer** and **SB Acoustics SB26STAC-C000-4 tweeter**. Here is the reasoning:

- The SB15 woofer has the lowest Le (easiest crossover design), excellent response smoothness, and a Qts that works well in a compact sealed enclosure
- The SB26 tweeter is a high-performance ring radiator with superb off-axis behavior and low Fs, giving us crossover flexibility
- Both are 4-ohm drivers from the same manufacturer, and measurement data is readily available
- The pair costs approximately $80-100 total (both drivers for one speaker, so $160-200 for a stereo pair)

The 8 dB sensitivity difference (92 dB tweeter vs. 84 dB woofer) is large but manageable with an L-pad. This is actually typical -- tweeters are almost always more sensitive than woofers.

> **What happens if you choose drivers from different impedance families (e.g., 8-ohm woofer and 4-ohm tweeter)?**
>
> The crossover component values will be different for each driver, and the impedance interaction becomes more complex. It is doable but adds complexity. Matching impedance (both 4 ohm or both 8 ohm) simplifies the design. If you do mix impedances, VituixCAD handles it correctly -- just enter each driver's actual impedance data.

---

## 3. Enclosure Design with WinISD

### 3.1 Sealed vs. Ported

Let us model both and compare.

**Sealed, 7 liters net:**
```
Qtc = 0.40 * sqrt(7.3/7 + 1) = 0.40 * sqrt(2.043) = 0.40 * 1.429 = 0.572
Fc = 52 * 1.429 = 74.3 Hz
F3 ≈ 82 Hz (for Qtc = 0.572, the F3 is above Fc)
F6 ≈ 62 Hz
```

Wait, let me recalculate more carefully. For Qtc = 0.572:
- The F3 for a 2nd-order Butterworth shape depends on Qtc. For Qtc < 0.707, F3 > Fc.
- Actually, for Qtc = 0.572, F3 is approximately Fc * sqrt(1 - 1/(4*Qtc^2) + sqrt((1 - 1/(4*Qtc^2))^2 + 1/Qtc^2 - 2))... this gets complex. Let WinISD calculate it.

In practice, with these parameters in WinISD, you will see:
- A sealed 7-liter box gives F3 around 75-85 Hz, with a smooth, well-damped rolloff
- Bass is tight and well-controlled, excellent transient response

**Ported, 10 liters, tuned to 42 Hz:**
- F3 around 45-50 Hz -- significantly deeper bass
- Steeper rolloff below 42 Hz
- A round port of 40mm diameter, approximately 12 cm long

The ported option gives noticeably deeper bass in a slightly larger box. The trade-off is the steeper rolloff below tuning and the added complexity of the port.

### 3.2 The Decision

For a bookshelf speaker on stands in a typical room, room gain (boundary reinforcement from the wall behind the speaker and the floor) typically adds 3-6 dB below 100 Hz. A sealed box with F3 around 80 Hz will sound like it reaches into the 50s Hz range with room gain -- perfectly adequate for most music.

Let us go with **sealed, 7 liters**, for the following reasons:
- Simpler construction (no port to tune)
- Excellent transient response (tight, clean bass)
- Compact size suitable for a bookshelf or stand
- The room gain will bring effective bass into the 50-60 Hz range
- If the listener wants deeper bass, a subwoofer (Chapter 17) is the right solution

### 3.3 Cabinet Dimensions

Target: 7 liters net internal volume with 18mm MDF.

Working backwards from the driver requirements:
- Minimum baffle width: The SB15 has a mounting diameter of approximately 130mm plus some edge clearance. Let us use 170mm internal width (206mm external).
- Height: Enough for the woofer, tweeter, and spacing. The tweeter mounts above the woofer. Internal height of approximately 270mm (306mm external).
- Depth: Calculated to give 7 liters with the above width and height.

```
Net volume target: 7 liters = 7,000,000 mm^3
Width (internal): 170 mm
Height (internal): 270 mm
Depth (internal): 7,000,000 / (170 * 270) = 152.5 mm → round to 155 mm
External depth: 155 + 36 = 191 mm → round to 190 mm
```

Accounting for component displacement (brace + woofer magnet + crossover ≈ 0.5 liters), let us increase depth slightly:

```
Adjusted target: 7.5 liters gross
Depth (internal): 7,500,000 / (170 * 270) = 163 mm
External depth: 163 + 36 = 199 mm → round to 200 mm
```

**Final external dimensions: 206mm W x 306mm H x 200mm D** (approximately 8.1" x 12" x 7.9")

This is a compact bookshelf speaker -- roughly the size of a Kef LS50 or similar.

---

## 4. Crossover Design in VituixCAD

### 4.1 Setup

1. Import the SB15NRXC30-4 FRD and ZMA files (download from SB Acoustics website)
2. Import the SB26STAC-C000-4 FRD and ZMA files
3. Define the baffle: 206mm wide x 306mm tall
4. Position the tweeter center at approximately (103, 80) mm from the bottom-left corner -- slightly above center horizontally, near the top of the baffle
5. Position the woofer center at approximately (103, 190) mm -- centered horizontally, below the tweeter

### 4.2 Initial Crossover Topology

We will start with a **2nd-order Linkwitz-Riley** crossover at 2,500 Hz. Why 2nd order instead of 4th? For a bookshelf speaker with well-behaved drivers, 2nd order uses fewer components (lower cost, less insertion loss) and still provides adequate protection. If the result is not satisfactory, we can upgrade to LR4.

**Initial circuit (before optimization):**

Woofer low-pass:
```
L1 (series) = R / (pi * Fc) = 4 / (pi * 2500) = 0.51 mH
C1 (parallel) = 1 / (pi * Fc * R) = 1 / (pi * 2500 * 4) = 31.8 uF
```

Tweeter high-pass:
```
C2 (series) = 1 / (pi * Fc * R) = 31.8 uF
L2 (parallel) = R / (pi * Fc) = 0.51 mH
```

Plus:
- Zobel on woofer: Rz = 3.2 ohms, Cz = Le/Re^2 = 0.25m / (3.2^2) = 24.4 uF
- L-pad on tweeter: approximately 8 dB attenuation to match woofer sensitivity

### 4.3 Optimization

Enter these components into VituixCAD and run the simulation. The initial response will not be flat -- it never is. The textbook values assume flat-response, resistive drivers, which these are not.

Now run the optimizer:
1. Select all crossover components (L1, C1, C2, L2, and L-pad resistors) as variable
2. Fix the Zobel values (these are calculated, not optimized)
3. Set target: flat on-axis response from 300 Hz to 20 kHz
4. Run optimization

The optimizer will adjust component values to achieve the best flat response. Typical optimized values for a build like this might look like:

```
Woofer low-pass:
  L1: 0.56 mH (air-core inductor)
  C1: 22 uF (film capacitor)

Tweeter high-pass:
  C2: 6.8 uF (film capacitor)
  L2: 0.30 mH (air-core inductor)

Tweeter L-pad:
  R1: 3.3 ohms (series)
  R2: 10 ohms (parallel)

Woofer Zobel:
  Rz: 3.3 ohms
  Cz: 22 uF (film capacitor)
```

(Note: these are illustrative values. Your actual optimized values will depend on the specific driver measurements you import.)

### 4.4 Checking the Results

After optimization, check every plot in VituixCAD:

- **On-axis response**: Should be flat within plus or minus 2 dB from 500 Hz to 18 kHz, with natural rolloff below 500 Hz
- **Off-axis at 30 degrees**: Should be smooth, gently declining, no sharp dips or peaks at the crossover frequency
- **Power response (PIR)**: Should be smooth and gently declining from 1 kHz upward
- **Impedance**: Should stay above 3.2 ohms minimum, no wild swings
- **Phase**: Should be smooth through the crossover region

If the 2nd-order result has issues (a dip at the crossover, rough off-axis response), try upgrading to 4th-order LR4 and re-optimizing. More components give the optimizer more degrees of freedom.

### 4.5 Baffle Step Compensation

Check whether the VituixCAD optimization has naturally compensated for baffle step. With a 206mm wide baffle, the baffle step occurs around 600-700 Hz. If the on-axis response shows a lean in the upper bass / lower midrange (200-600 Hz) relative to the treble, add a BSC network to the woofer circuit and re-optimize.

For free-standing speakers on stands, you likely need 4-6 dB of BSC. For bookshelf placement against a wall, 0-2 dB.

---

## 5. Cabinet Construction

### 5.1 Cutting List

For ONE speaker (cut two of everything for a stereo pair):

| Part | Dimensions (mm) | Quantity |
|------|-----------------|----------|
| Front baffle | 206 x 306 | 1 |
| Back panel | 206 x 306 | 1 |
| Top panel | 206 x 164 | 1 |
| Bottom panel | 206 x 164 | 1 |
| Side panels | 270 x 164 | 2 |
| Internal brace | 170 x 128 | 1 |

Note: The side panels' height is the internal height (306 - 36 = 270mm). The top, bottom, and side depths are the internal depth (200 - 36 = 164mm). The front and back overlap the sides, top, and bottom.

If you have access to a table saw, cut the panels yourself from a sheet of 18mm MDF. If not, most lumber yards and big-box hardware stores will make cuts for you (bring a cutting diagram). For the cleanest results, use a track saw or CNC.

### 5.2 Driver Cutouts

The front baffle needs two cutouts:
- **Woofer cutout**: A circle matching the driver's cutout diameter (check the datasheet -- for the SB15, approximately 116mm). Use a hole saw, router with circle-cutting jig, or jigsaw.
- **Tweeter cutout**: A smaller circle matching the tweeter's cutout diameter (approximately 52mm for the SB26).

Also drill mounting holes for the driver screws. Use the driver's template (usually available as a PDF on the manufacturer's website) or measure carefully from the datasheet.

If possible, **flush-mount** the drivers (route a recess so the driver flange sits flush with the baffle surface). This reduces baffle diffraction significantly. Use a router with a template or straight bit to cut a shallow recess matching the driver flange depth (typically 3-5mm deep).

### 5.3 Terminal Cup Cutout

Drill a cutout in the back panel for the **binding post terminal cup** -- a plate with two banana plug / binding post connectors (positive and negative). Standard round terminal cups need a 70mm hole. Rectangular ones need an appropriate rectangular cutout.

### 5.4 Assembly Sequence

1. **Dry-fit everything first**: Assemble the box without glue to verify all pieces fit correctly and the drivers mount properly.

2. **Glue the box**: Use wood glue (PVA / yellow glue, such as Titebond II). Apply glue to all mating surfaces, assemble, and clamp firmly. The glue joint, when cured, is stronger than the MDF itself.

   Assembly order:
   a. Glue the two side panels to the bottom panel
   b. Add the back panel
   c. Add the internal brace (glue it to the side panels at approximately mid-height)
   d. Add the top panel
   e. Wait for the front baffle -- it goes on LAST, after the crossover and damping are installed inside

3. **Let cure**: 24 hours minimum for full glue cure.

4. **Seal all joints**: Run a bead of silicone sealant or hot glue along all internal joint lines. A sealed box must be airtight. Even a small air leak degrades bass performance.

5. **Sand the exterior**: Sand all edges and surfaces smooth. Round the front baffle edges with a router (at least a 12mm roundover bit) to reduce diffraction.

### 5.5 Damping

Line the inside walls (except the front baffle) with 25mm (1") acoustic foam or fiberglass board. Secure with spray adhesive. Add a handful of polyfill (loosely packed) in the remaining air space.

### 5.6 Finishing

For a clean look:
- **Vinyl wrap**: The easiest finish for MDF. Available in matte black, woodgrain, and many other patterns. Apply with a heat gun and squeegee. Very forgiving of imperfect surfaces.
- **Paint**: Sand the MDF smooth, apply a coat of MDF sealer (or diluted PVA glue), sand again, then apply 2-3 coats of spray paint. Automotive-grade spray paint gives a beautiful finish.
- **Veneer**: For a wood look. Apply real wood veneer with contact cement or iron-on adhesive. Sand and lacquer. More difficult but stunning results.

Advanced finishing techniques are covered in Chapter 32.

---

## 6. Crossover Assembly

### 6.1 Component Selection

**Capacitors**: Use film capacitors (polypropylene preferred). For values above 10 uF, you may need to parallel smaller caps (e.g., two 10 uF caps for 20 uF). Audio-grade film caps from Dayton Audio, Mundorf, or Jantzen are excellent. Tolerance: 5% or better.

**Inductors**: Use air-core inductors for the tweeter circuit (no saturation, low distortion). For the woofer circuit, air-core is ideal but large-value inductors (>0.5 mH) in air-core are physically large and have significant DC resistance. Iron-core (ferrite or laminated steel) inductors are acceptable for the woofer low-pass if their DCR (DC resistance) is low. Check that the inductor's current rating exceeds your expected maximum current.

**Resistors**: Use non-inductive wire-wound resistors or ceramic resistors rated for at least 10W. Metal oxide film resistors (10W) are also acceptable. The L-pad resistors dissipate real power -- do not use small 0.25W resistors.

### 6.2 Crossover Board Layout

Mount the crossover components on a piece of perforated board (perfboard) or a dedicated crossover board. Layout tips:

- Keep woofer and tweeter circuits physically separated to prevent inductive coupling between their inductors
- Orient inductors at right angles to each other if they must be close
- Use heavy-gauge wire (at least 18 AWG) for connections
- Solder all connections -- do not use push terminals or wire nuts

### 6.3 Wiring Diagram

```
Speaker terminal (+) ──┬──── L1 ────┬──── Woofer (+)
                       │            │
                       │           C1        Rz ── Cz
                       │            │         │     │
                       │           GND    Woofer terminals
                       │
                       └──── C2 ────┬──── R1 ──┬──── Tweeter (+)
                                    │          │
                                   L2         R2     
                                    │          │
                                   GND        GND

Speaker terminal (-) ──────── Woofer (-) ──── Tweeter (-)

(Note: Tweeter polarity may need to be reversed depending on crossover
 order. Check VituixCAD -- if it shows reversed polarity, swap the
 tweeter's + and - connections.)
```

### 6.4 Testing Before Installation

Before mounting the crossover in the cabinet:
1. Connect the crossover to the drivers (temporarily) with clip leads
2. Connect to an amplifier at LOW volume
3. Verify both drivers produce sound
4. Verify the tweeter is not noticeably louder than the woofer (the L-pad is working)
5. Verify no buzzing or rattling (a loose component or bad solder joint)

---

## 7. Final Assembly

### 7.1 Installing the Crossover

Mount the crossover board inside the cabinet, on the back panel or a side panel. Use hot glue, screws into the MDF, or cable ties through holes drilled in the board and cabinet. Ensure the board is secure and cannot rattle.

### 7.2 Wiring

1. Solder the speaker wire from the terminal cup to the crossover input
2. Solder speaker wire from the crossover outputs to the driver terminals (or use quick-disconnect terminals crimped onto the wire ends -- this allows easy driver removal for future crossover changes)
3. Double-check polarity: positive terminal → positive on each driver (unless VituixCAD indicates reversed polarity for the tweeter)

### 7.3 Mounting the Front Baffle

1. Apply a gasket (foam weatherstripping tape) to the driver flanges or the baffle cutout edges
2. Mount the drivers first, screwing them into the baffle (use machine screws into T-nuts for repeatability, or self-tapping screws into the MDF for simplicity)
3. Apply wood glue to the baffle edges
4. Attach the front baffle to the cabinet and clamp
5. Once cured, run a bead of sealant on the inside around the baffle joint

### 7.4 Air Leak Test

For a sealed box, press gently on the woofer cone. It should push in and return slowly (like pressing on a drum head). If it returns quickly with little resistance, there is an air leak. Find and seal it with silicone caulk.

---

## 8. Measurement with REW

### 8.1 First Measurement

Set up the measurement microphone at 1 meter distance, on-axis (at the tweeter height), and run a frequency sweep.

Compare the measured response to the VituixCAD prediction. Look for:

- **Overall shape match**: The general shape of the response should be recognizable. If it is wildly different, check wiring (especially tweeter polarity).
- **Level match at crossover**: Both drivers should be contributing correctly at the crossover frequency. A deep notch at the crossover suggests reversed polarity on one driver.
- **Bass rolloff**: Should match the sealed-box prediction from WinISD (after accounting for room effects).
- **Treble extension**: Should extend to at least 18 kHz.

### 8.2 Troubleshooting Common Issues

**Deep dip at the crossover frequency**: Tweeter polarity is reversed. Swap the tweeter's positive and negative connections.

**Bright, harsh sound / treble much louder than expected**: L-pad is not attenuating enough, or L-pad is wired incorrectly. Verify the L-pad resistor values and wiring.

**No bass below 200 Hz**: Air leak in the enclosure. Check all joints and the driver gaskets.

**Strange resonance at a specific frequency**: Panel resonance (knock on the cabinet walls to check), or an internal standing wave (add more damping material).

**Response matches simulation in treble but not in midrange**: The measurement is being affected by room reflections. Use time-gating in REW to isolate the direct sound, or measure outdoors.

### 8.3 Iterative Improvement

If the measurement reveals issues that the simulation did not predict:

1. Import the measured response into VituixCAD as the new driver data
2. Re-optimize the crossover
3. Swap crossover components to the new values (this is why temporary wiring or quick-disconnect terminals are valuable)
4. Re-measure
5. Repeat until the measurement matches your target

Typically, one or two iterations are sufficient. If you started with good driver data, the first simulation might be close enough that no changes are needed.

---

## 9. Listening Evaluation

Measurement tells you the objective truth. Listening tells you the subjective truth. Both matter.

### 9.1 Setup

Place the speakers on stands (if free-standing) at ear level, forming an equilateral triangle with your listening position. Toe the speakers in slightly so the tweeters point toward your ears. Use your best amplifier and source material.

### 9.2 What to Listen For

**Tonal balance**: Does the speaker sound neutral? Is there too much bass, too little bass, too bright, too warm? A flat-measuring speaker should sound "right" on a wide variety of music.

**Midrange clarity**: Play vocals (male and female). Do they sound natural and present, or recessed and muffled? The crossover region (around 2-3 kHz) is critical here -- any problems will be most apparent on vocals.

**Bass quality**: Play bass-heavy music. Is the bass tight and defined, or loose and boomy? A well-designed sealed box should have very clean, punchy bass -- not chest-thumping deep bass (that is the subwoofer's job) but well-defined and musical.

**Treble quality**: Play acoustic guitar, cymbals, hi-hats. Does the treble sound airy and detailed, or harsh and fatiguing? Sibilance (excessive "sss" and "shh" sounds on vocals) indicates a peak in the 5-8 kHz range.

**Imaging**: Play a well-recorded stereo track. Do instruments have a defined location in space between the speakers? Can you "see" the soundstage? Good time alignment and dispersion create precise imaging.

**Dynamic range**: Play something with quiet passages and loud passages. Does the speaker handle both gracefully? At high volumes, does the bass distort or the tweeter get harsh?

### 9.3 Test Tracks

Some recommended tracks for evaluation:
- Female vocals: Norah Jones "Don't Know Why" (midrange clarity, imaging)
- Male vocals: Leonard Cohen "Hallelujah" (lower midrange, resonance)
- Acoustic guitar: Rodrigo y Gabriela "Tamacun" (transient response, dynamics)
- Bass test: Massive Attack "Angel" (bass depth and control)
- Imaging test: Pink Floyd "Money" (stereo separation, spatial effects)
- Full orchestra: Beethoven Symphony No. 7, 2nd movement (dynamic range, tonal balance across the full spectrum)
- Sibilance test: Diana Krall "Peel Me a Grape" (checks for 5-8 kHz peaks)

### 9.4 Adjustments Based on Listening

If you hear issues that the measurements did not show (or that you tolerated in the measurements), consider:

- **Too bright**: Increase tweeter L-pad attenuation by 1-2 dB. Or add a small series resistor (0.5-1 ohm) in the tweeter circuit.
- **Too warm/bassy**: Reduce BSC (if present) or add a small series resistor in the woofer circuit.
- **Thin bass**: Add more BSC, or consider converting to ported (drill a port hole and insert a port tube -- simulate first!).
- **Harsh at high volume**: The drivers may be approaching their limits. The crossover might benefit from a steeper slope (upgrade to LR4) to better protect the tweeter.

---

## 10. Bill of Materials Summary

For one stereo pair:

| Component | Quantity | Approx. Cost |
|-----------|----------|--------------|
| SB Acoustics SB15NRXC30-4 woofer | 2 | $60-70 |
| SB Acoustics SB26STAC-C000-4 tweeter | 2 | $50-60 |
| 18mm MDF panel (4' x 4' or equivalent) | 1 | $25-35 |
| Crossover capacitors (film, various values) | Set | $20-30 |
| Crossover inductors (air-core, various values) | Set | $15-25 |
| L-pad resistors (10W wire-wound) | 4 | $5-8 |
| Binding post terminal cups | 2 | $8-12 |
| Speaker wire (18 AWG, 3 meters) | 1 | $5 |
| Acoustic foam or fiberglass (for damping) | Small sheet | $10-15 |
| Wood glue, screws, gasket tape | Misc | $10-15 |
| Vinyl wrap or paint | As needed | $10-20 |
| **Total** | | **$220-290** |

### Tools Needed
- Table saw or circular saw (for cutting MDF)
- Jigsaw or hole saw (for driver cutouts)
- Router with roundover bit (for edge rounding -- optional but recommended)
- Soldering iron and solder (for crossover assembly)
- Drill and bits
- Clamps (at least 4 bar clamps for cabinet glue-up)
- Sandpaper (120, 220, 400 grit)
- Measurement mic and REW (for final measurement)

---

## 11. Chapter Summary and What Comes Next

You have now built your first pair of speakers from scratch. You selected the drivers based on T-S parameters and datasheet analysis. You modeled the enclosure in WinISD and chose sealed for its simplicity and excellent transient response. You designed the crossover in VituixCAD, complete with impedance compensation and level matching. You built the cabinets from MDF, assembled the crossovers, wired everything together, and verified the result with REW measurements.

These bookshelf speakers are a complete, high-quality design that will outperform many commercial speakers at the same price point. They are also your learning platform -- now that you understand every part of the design, you can experiment. Try different capacitor types (polypropylene vs. Mylar). Try adjusting the crossover frequency. Try adding a port. Every change is an opportunity to deepen your understanding.

In Chapter 17, we scale up: three more builds of increasing complexity. A floor-standing tower for deeper bass and more authority. A PA speaker designed for raw output and durability. And a subwoofer to handle the frequencies that no bookshelf speaker can touch. Each build introduces new concepts and new challenges, building on everything you have learned so far.
