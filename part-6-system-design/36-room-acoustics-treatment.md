<!--
  CHAPTER: 36
  TITLE: Room Acoustics and Treatment
  PART: 6 — System Design & Integration
  PREREQS: Chapter 35
  KEY_TOPICS: room modes, early reflections, flutter echo, RT60, speaker placement, absorption panels, bass traps, diffusers, measurement-based treatment, listening room vs party venue
  DIFFICULTY: Inter→Adv
  UPDATED: 2026-04-10
-->

# Chapter 36: Room Acoustics and Treatment

> **Part 6 — System Design & Integration** | Prerequisites: Chapter 35 | Difficulty: Inter→Adv

You just spent five parts of this guide learning to build beautiful-sounding speakers, amplifiers, and DSP systems. You measured them, tuned them, and finished them to a professional standard. Then you put them in your living room and everything sounds wrong. The bass is boomy in one spot and nonexistent two feet to the left. Vocals sound smeared. The stereo image wanders. You are convinced something is broken.

Nothing is broken. Your room is just doing what rooms do: reflecting, absorbing, resonating, and generally making a mess of the carefully crafted sound your speakers are trying to produce. The room is the single largest influence on what you actually hear, and it is the one component most DIY builders completely ignore. A $500 pair of speakers in a treated room will sound better than a $5,000 pair of speakers in an untreated one. That is not marketing -- that is physics.

This chapter covers how rooms affect sound, how to position speakers to minimize the damage, and how to build acoustic treatment panels that fix the remaining problems. We will measure before and after, because this guide is about evidence, not opinions.

### In This Chapter
- How rooms affect sound: modes, reflections, flutter echo, and reverb
- Speaker and subwoofer placement strategies
- DIY acoustic treatment: absorption panels, bass traps, and diffusers
- Treatment placement: first reflection points, corners, ceiling, rear wall
- Measurement-based treatment with REW
- The listening room vs the party venue: different problems, different solutions

### Related Chapters
- [Ch 11: How Speakers Work](../part-2-speakers/11-how-speakers-work.md) — room interaction with speakers
- [Ch 12: The Language of Speakers](../part-2-speakers/12-language-of-speakers.md) — frequency response, off-axis behavior
- [Ch 29: DSP with CamillaDSP](../part-4-digital-audio/29-dsp-camilladsp.md) — digital room correction to complement physical treatment
- [Ch 33: Professional Finishing](../part-5-pcb-professional/33-professional-finishing.md) — making treatment panels look good in your room
- [Ch 35: Matching Amps to Speakers](35-matching-amps-to-speakers.md) — power calculations depend on room characteristics
- [Ch 37: System Tuning and Measurement](37-system-tuning-measurement.md) — detailed measurement techniques for evaluating treatment

---

## 1. How Rooms Affect Sound

### 1.1 Room Modes (Standing Waves)

When a speaker produces a bass note, the sound wave travels to the nearest wall, reflects, and travels back. If the room dimension equals exactly half the wavelength, the reflected wave reinforces the original wave perfectly. You get a **standing wave** -- a resonance where certain positions in the room have massively boosted bass, and other positions (the nulls) have almost no bass at all.

**Calculating room modes:**

```
f = c / (2 × L)
```

Where:
- f = frequency of the mode (Hz)
- c = speed of sound (~343 m/s at 20°C)
- L = room dimension (meters)

For a room that is 5 meters long:

```
f = 343 / (2 × 5) = 34.3 Hz  (first axial mode)
```

This means 34.3 Hz will resonate along the room's length. The second mode is at 68.6 Hz (2 × 34.3), third at 102.9 Hz, and so on (integer multiples).

A room has three dimensions, so it has three sets of axial modes:

| Dimension | Length (m) | First Mode (Hz) | Second Mode (Hz) | Third Mode (Hz) |
|-----------|-----------|-----------------|-------------------|------------------|
| Length | 5.0 | 34.3 | 68.6 | 102.9 |
| Width | 4.0 | 42.9 | 85.8 | 128.6 |
| Height | 2.5 | 68.6 | 137.2 | 205.8 |

Notice something unfortunate: the second mode along the length (68.6 Hz) coincides with the first mode along the height (68.6 Hz). When modes overlap like this, the resonance is even stronger. This is why room dimension ratios matter -- a perfectly cubic room is the worst possible shape because all three dimensions produce the same mode frequencies.

There are also **tangential modes** (bouncing off two pairs of walls) and **oblique modes** (bouncing off all three pairs). These are weaker than axial modes but add to the mess.

**What you hear:** At your listening position, some bass frequencies are unnaturally loud (10-20 dB boost at a mode) and others are unnaturally quiet (10-20 dB cut at a null). This creates the impression of "one-note bass" where certain bass notes boom out and others disappear. Moving your head a foot in any direction changes the balance completely.

> **What happens if... your room is perfectly square (4m × 4m)?** All three axial mode series align. The first mode for both length and width is 42.9 Hz. The reinforcement at this frequency is extreme -- easily 20+ dB of boost at the corners. The bass will sound like every note is the same note. This is the acoustic nightmare scenario. If you are building a dedicated listening room, make it rectangular with dimensions in a ratio like 1 : 1.4 : 1.9 (Bolt area ratios) to spread the modes out evenly.

### 1.2 Early Reflections

Sound from the speaker reaches you in two ways: the **direct sound** (straight line from speaker to ear) and **reflected sound** (bouncing off walls, floor, ceiling, and furniture first). Reflections that arrive within about 10-20 milliseconds of the direct sound are called **early reflections**.

Your brain cannot separate early reflections from the direct sound -- it fuses them together. But the reflected sound has traveled a longer path, so it arrives slightly later and from a different direction. This causes:

- **Comb filtering**: The reflected sound interferes with the direct sound. At some frequencies they add (constructive interference), at others they cancel (destructive interference). The result is a series of peaks and dips in the frequency response that look like the teeth of a comb.
- **Smeared imaging**: If a strong reflection arrives from the right wall while you are listening to a sound panned center, your brain gets confused about where the sound is coming from. The stereo image becomes vague and washed out.
- **Coloration**: The reflected sound picks up the tonal character of whatever surface it bounced off. A bare drywall wall reflects highs and mids efficiently; a bookshelf scatters them. The reflections add their character to the sound you hear.

The most significant early reflections come from:
1. The side walls (at the point halfway between speaker and listener)
2. The floor (between speaker and listener)
3. The ceiling (directly above the midpoint)
4. The wall behind the speakers (reflected back toward you)

### 1.3 Flutter Echo

Place yourself between two parallel walls in an empty room. Clap your hands. If you hear a rapid "brrrrrrr" after the clap, that is **flutter echo** -- sound bouncing back and forth between parallel surfaces, each bounce slightly quieter than the last. In a room with furniture and treatment, flutter echo is usually not a problem. In a bare, hard-surfaced room, it can be severe.

Flutter echo affects the midrange and high frequencies most (the wavelengths small enough to bounce cleanly between walls). It makes everything sound buzzy and metallic. Even small amounts of treatment or furniture on one wall breaks the parallel and eliminates flutter echo.

### 1.4 Reverb and RT60

After the early reflections, sound continues bouncing around the room, losing energy with each reflection. The cumulative effect of thousands of late reflections is **reverberation** -- the gradual decay of sound in a space.

**RT60** is the time it takes for a sound to decay by 60 dB (effectively to silence). Typical values:

| Room Type | RT60 (seconds) |
|-----------|---------------|
| Recording studio control room | 0.2-0.4 |
| Living room (furnished) | 0.4-0.7 |
| Lecture hall | 0.8-1.2 |
| Church | 1.5-3.0 |
| Empty warehouse | 3.0-8.0 |
| Concert hall (classical) | 1.5-2.5 |

For home hi-fi listening, you want RT60 in the range of 0.3-0.5 seconds. Short enough that bass does not pile up and imaging stays focused, but long enough that the room does not feel dead and claustrophobic.

For a party venue, reverb is usually the enemy. An empty warehouse with RT60 of 5+ seconds turns your PA system into an intelligibility disaster -- every beat piles on top of the last. You either treat the space (expensive for a one-off party) or use speaker placement and DSP to minimize the problem (more on this in section 7).

---

## 2. Speaker Placement

### 2.1 The Basics: Away From Walls

When a speaker is placed near a wall, the wall acts as an acoustic mirror. The reflected bass from the wall adds to the direct bass from the speaker, boosting low frequencies. This is called **boundary reinforcement** or **boundary gain**:

- Speaker in open space (no boundaries): no reinforcement
- Speaker near one wall: +3 dB bass boost (half-space radiation)
- Speaker in a corner (two walls): +6 dB bass boost (quarter-space)
- Speaker in a tri-corner (two walls + floor): +9 dB bass boost (eighth-space)

Some bass boost from wall proximity is normal and expected in a room (it is part of the "room gain" mentioned in Chapter 35). But too much makes the bass boomy and masks midrange detail.

**The 38% rule**: Place your speakers at 38% of the room dimension from the front wall. For a room that is 5 meters long, that means the speaker is about 1.9 meters from the front wall. This position minimizes the excitation of the strongest axial modes along the room length. It is not magic -- it is a starting point that puts you away from the worst mode reinforcement.

In practice, most people cannot place speakers 2 meters from the wall in their living room. Do the best you can. Even getting them 30-50 cm off the wall makes a significant difference compared to flush against it.

### 2.2 The Stereo Triangle

For two-channel stereo listening, the classic setup is an **equilateral triangle**: the distance between the two speakers equals the distance from each speaker to your head.

```
           [Listener]
            /      \
           /        \
          /    d     \
         /            \
   [Left] ---- d ---- [Right]
```

This geometry creates the widest, most stable stereo image. Widen the triangle and the center image weakens. Narrow it and the image becomes a blob in the middle.

**Toe-in** is the angle at which you point the speakers inward toward the listening position:

- **No toe-in (straight ahead)**: Widest soundstage, but the treble can sound dull at the listening position because most tweeters beam high frequencies forward and you are off-axis.
- **Full toe-in (aimed directly at listener)**: Most treble energy, sharpest imaging, but the soundstage can feel narrow and the sweet spot is tiny.
- **Moderate toe-in (aimed slightly behind the listener's head)**: The compromise most people prefer. You get good treble response, a reasonably wide soundstage, and a sweet spot that does not vanish if you lean six inches to the side.

Start with the speakers aimed so their axes cross about a foot behind your head. Adjust from there by ear.

### 2.3 The Subwoofer Crawl

Subwoofer placement is critical because bass frequencies interact strongly with room modes. The "perfect" position for a sub depends entirely on your specific room, and there is no formula that works universally. Instead, use the **subwoofer crawl**:

1. Place the subwoofer at your listening position (yes, on the couch or chair where you sit). It will look ridiculous. That is fine.
2. Play a bass-heavy track or a low-frequency test tone (40-80 Hz sweep works well).
3. Get on your hands and knees and crawl along the walls of the room, stopping every foot or so.
4. Listen for the position where the bass sounds the smoothest and most even -- not the loudest, but the most balanced.
5. That is where you put the subwoofer.

Why does this work? The physics of room modes is reciprocal: the relationship between source position and listener position is symmetric. If the bass sounds good at position X when the sub is at position Y, it will also sound good at position Y when the sub is at position X.

Common good positions for subwoofers:
- Along the front wall, but not in the corner (reduces the worst mode excitation)
- About 1/4 to 1/3 of the way along the room length
- Between the main speakers (aesthetically clean, often acoustically decent)

> **What happens if... you put the subwoofer in the corner?** You get maximum bass output (that +9 dB boundary gain). But you also excite every room mode at maximum strength. The bass will be very loud and very uneven -- massive peaks at mode frequencies and deep nulls in between. Corner placement is great for maximum output in a PA setting where you need sheer volume. It is usually bad for home hi-fi where you want smooth, accurate bass. If you must corner-place (space constraints), plan to use heavy DSP EQ to tame the peaks.

---

## 3. DIY Acoustic Treatment: Absorption Panels

### 3.1 How Absorption Works

Acoustic absorption converts sound energy into heat. When a sound wave hits a fibrous material, the air molecules vibrate between the fibers, losing energy to friction. Thicker absorbers work at lower frequencies because lower frequencies have longer wavelengths that need to penetrate deeper into the material.

**The key insight**: an absorber is most effective when it is placed at a point where the sound wave's *particle velocity* is highest. For a wave reflecting off a hard wall, the particle velocity is zero at the wall surface (it is a pressure maximum there) and highest at one-quarter wavelength from the wall. This is why thin foam panels stuck directly to a wall do almost nothing for bass -- the bass wavelength's velocity maximum is feet away from the wall, but the foam is only inches thick.

### 3.2 Material Selection

The two standard materials for DIY acoustic panels are:

**Rigid fiberglass** (Owens Corning 703 or equivalent):
- Density: 3 lb/ft³ (48 kg/m³)
- Excellent absorption across mid and high frequencies
- 2" thick: effective above ~500 Hz
- 4" thick: effective above ~250 Hz
- Available at insulation supply companies (not Home Depot -- you want the rigid board, not the fluffy batt)

**Mineral wool** (Rockwool/Roxul Safe'n'Sound or ComfortBoard):
- Density: 4-8 lb/ft³ (64-128 kg/m³)
- Similar absorption characteristics to fiberglass
- Slightly denser = slightly better at lower frequencies
- More widely available at building supply stores
- Easier to cut (use a bread knife or insulation knife)

Both work great. Use whichever is more available in your area.

**Do not use:** open-cell foam (the stuff sold as "acoustic panels" on Amazon for $30). It is typically only 1-2 inches thick and absorbs almost nothing below 1 kHz. You end up with a room that has dead highs but untreated bass and lower mids -- the worst of both worlds. The room sounds muffled instead of controlled.

### 3.3 Building Absorption Panels

Here is the complete build for a 2' × 4' × 4" absorption panel:

**Materials per panel:**
- 1x rigid fiberglass or mineral wool board, 2' × 4' × 4" (or two 2" boards stacked)
- 1x fabric: acoustically transparent (breathable) fabric. Burlap, muslin, or guilford of maine fabric. Test: hold it to your mouth and blow -- if air passes easily, it is acoustically transparent.
- Wood frame: 1×4 lumber (which is actually 3/4" × 3-1/2"), four pieces to make a frame
- Wood screws or pocket hole screws for frame assembly
- Staple gun and staples for fabric
- Picture hanging hardware (Z-clips or french cleats for heavy panels)

**Build process:**

1. **Cut frame pieces.** Two 48" pieces and two 22-1/2" pieces (accounting for the 3/4" thickness of the long pieces on each side). Assemble with screws or pocket holes into a rectangular frame.

2. **Optional: add a cross-brace.** For a 2' × 4' panel, a horizontal brace at the midpoint keeps the frame from warping. This is especially helpful if you are using lighter lumber.

3. **Insert the insulation.** Place the rigid board(s) inside the frame. They should fit snugly. If they are slightly oversized, compress them gently -- this is fine and actually improves absorption slightly.

4. **Wrap with fabric.** Lay the fabric face-down on a clean surface. Place the panel face-down on the fabric. Pull the fabric tight over the back, starting with the long sides. Staple to the back of the frame, pulling evenly so there are no wrinkles on the front face. Then fold and staple the short sides, tucking the corners like wrapping a present.

5. **Attach hanging hardware.** French cleats are the strongest option for heavy panels. Z-clips work for lighter ones. The panel should sit flat against the wall with a small air gap (even 1-2 inches) behind it -- this air gap extends the effective depth and improves low-frequency absorption.

**Cost per panel:** approximately $20-40 in materials, depending on your area. Commercial acoustic panels of equivalent quality cost $100-300 each.

**Time per panel:** about 1-2 hours for your first one, 30-45 minutes once you have a rhythm.

> **What happens if... you use fabric that is not acoustically transparent?** The fabric reflects high frequencies instead of letting them pass through to the absorber. The panel still works for mids and (somewhat) bass, but the high-frequency absorption drops. Your room ends up with controlled mids but still-live highs, which sounds unnatural. Always test the fabric with the blow test.

### 3.4 Air Gap Mounting

Mounting a 4" panel with a 4" air gap behind it gives you an effective depth of 8", which extends the absorption down to about 125 Hz. This is significantly better than the same panel mounted flush against the wall. Use spacer blocks or a mounting rail to create the gap. This is the single cheapest way to improve your panel's low-frequency performance.

---

## 4. DIY Bass Traps

### 4.1 Why Bass Traps Are Different

Bass frequencies have wavelengths measured in meters (100 Hz = 3.43 meters, 50 Hz = 6.86 meters). To absorb them effectively, you need absorptive material at the quarter-wavelength point. For 50 Hz, that is 1.7 meters from the wall. Nobody is building a 1.7-meter-thick panel.

Instead, bass traps exploit room geometry. Sound pressure is highest in the **corners** of a room (where two or three surfaces meet). Corners are where room modes stack up most intensely. By placing thick absorption in the corners, you treat the bass at the point of maximum pressure, which is also where the absorber is most effective.

### 4.2 Corner Bass Traps

The most effective DIY bass trap is a floor-to-ceiling column of absorption in the room corners.

**Design:**
- Rigid fiberglass or mineral wool, stacked floor to ceiling
- Triangular cross-section that fits in the corner (the panel faces the room at 45 degrees across the corner)
- Minimum 4" thick, ideally 6-8" for deeper bass absorption
- Covered in acoustically transparent fabric

**Build approach 1: Stacked panels**
Build triangular frames from 1×6 lumber. Cut the insulation to fit inside. Stack the frames floor to ceiling in the corner. Wrap the exposed face with fabric. This is the easiest method.

**Build approach 2: Superchunk**
Fill the entire corner triangle with insulation, floor to ceiling. No frame needed -- just stack the insulation material (cut into triangles) from floor to ceiling and wrap the exposed face with fabric, using a tension wire or wooden strips to hold the fabric. This gives maximum absorption because the entire depth is filled with absorptive material.

A superchunk bass trap in all four vertical room corners is the single most effective acoustic treatment you can install. It treats room modes, reduces RT60, and smooths the bass response. If you do nothing else in this chapter, build four corner bass traps.

### 4.3 How Much Treatment Is Too Much?

This is a real concern. Over-treatment makes a room sound dead, claustrophobic, and unnatural. You are not building an anechoic chamber -- you are building a room where music sounds good.

**Guidelines:**
- Treat first reflection points with panels (usually 2-4 panels on side walls, 1-2 on ceiling)
- Put bass traps in corners (4 vertical corners minimum)
- Do NOT cover every wall surface with absorption
- Leave some reflective surfaces for liveliness, especially on the rear wall (or use diffusion instead of absorption there)
- Target RT60 of 0.3-0.5 seconds for a home listening room
- Measure after each phase of treatment (Chapter 37) -- stop when the measurements and your ears agree that the room sounds good

---

## 5. DIY Diffusers

### 5.1 What Diffusion Does

Absorption removes acoustic energy from the room. Diffusion *redirects* it -- scattering a coherent reflection into many smaller reflections arriving from different angles at slightly different times. The result is a sense of spaciousness and liveliness without the harshness of a direct reflection.

Diffusion is most useful on the **rear wall** of a listening room. The rear wall reflection arrives late enough that it does not cause the imaging problems of early reflections, but in a small room it can still cause coloration. Diffusing this reflection makes the room feel larger and more open without deadening the sound.

### 5.2 QRD (Quadratic Residue Diffuser)

A QRD diffuser is a series of wells (slots) of different depths, calculated from a mathematical sequence. When sound enters the wells, each well reflects at a slightly different time (because of its depth), scattering the sound in many directions.

**Design parameters:**
- **Design frequency**: The lowest frequency you want to diffuse. This determines the deepest well depth. For a design frequency of 500 Hz: deepest well = λ/(2N) where N is the prime number you base the sequence on. Typical: N=7, design frequency 500 Hz, maximum well depth ≈ 17 cm.
- **Well width**: Must be less than half the wavelength of the highest frequency you want to diffuse. For diffusion up to 4 kHz: well width < 4.3 cm. Practically, 3-5 cm is common.
- **Sequence**: For a QRD based on prime N=7, the sequence of well depths (as multiples of the unit depth) is: 0, 1, 4, 2, 2, 4, 1. This repeats across the diffuser.

**Build process:**
1. Calculate well depths for your chosen prime and design frequency
2. Cut strips of wood (MDF or hardwood) to the width of each well
3. Cut divider strips to the depth of the deepest well (these create the well walls)
4. Assemble: back panel + divider strips at the calculated depths + side rails
5. Sand and finish

**Alternatively: CNC route it.** If you have access to a CNC router (many makerspaces do), you can route the entire diffuser from a single thick panel of MDF. This gives cleaner results and is faster once you have the toolpath programmed.

### 5.3 Skyline Diffuser

A skyline (or cityscape) diffuser is a 2D variant: a grid of square columns at different heights. It scatters sound in two dimensions (horizontal and vertical) rather than one. It also looks visually striking -- like a miniature city skyline.

**Build process:**
1. Cut many square blocks (e.g., 5 cm × 5 cm) at varying heights calculated from a 2D quadratic residue sequence
2. Glue them to a base panel in the calculated arrangement
3. Sand and finish

This is a satisfying weekend project and makes a great visual feature on a rear wall. It also works as an excellent conversation starter when guests visit.

> **What happens if... you just put random blocks at random heights?** You still get *some* diffusion -- any irregular surface scatters sound to some degree. But a mathematically designed diffuser is optimized to scatter evenly across the target frequency range. Random blocks tend to have gaps in their scattering pattern. That said, a bookshelf filled with books of varying sizes is a surprisingly decent diffuser, and it costs nothing.

---

## 6. Treatment Placement

### 6.1 First Reflection Points (The Mirror Trick)

The most important panels to place are at the **first reflection points** on the side walls, ceiling, and floor. These reflections arrive within 5-15 ms of the direct sound and cause the most damage to imaging and clarity.

Finding the first reflection points is simple:

1. Sit in your listening position.
2. Have a friend hold a mirror flat against the side wall.
3. The friend slowly slides the mirror along the wall.
4. When you can see the speaker in the mirror, mark that spot. That is the first reflection point.
5. Repeat for the other speaker on the same wall, and then for both speakers on the other wall.
6. Do the same on the ceiling (hold the mirror flat against the ceiling).

Place absorption panels at each marked point. For a typical stereo setup, you need:
- 2 panels on each side wall (one for each speaker) = 4 side panels
- 1-2 panels on the ceiling (a "cloud" panel centered above the listening position)
- The floor is usually handled by carpet or a rug between speakers and listener

### 6.2 Corners (Bass Traps)

Put bass traps in the vertical corners of the room. All four if possible, but prioritize the corners behind the speakers first (the front corners of the room). These are where the bass modes interact with the speaker's output most directly.

If you have space, also treat the horizontal corners where walls meet the ceiling, at least in the front half of the room. These edge bass traps can be smaller (triangular panels mounted at the wall-ceiling junction).

### 6.3 Rear Wall

The rear wall (behind your listening position) is a judgment call:

- **Small room (under 4m deep)**: The rear wall reflection arrives quickly and benefits from either absorption or diffusion. Diffusion is often preferred here because absorption on the rear wall can make the room feel too dead.
- **Large room (over 5m deep)**: The rear wall reflection is late enough that it blends into the room's natural reverb. Treatment is less critical, but diffusion still improves the sense of space.
- **In between**: Experiment. A mix of diffusion and absorption (e.g., a diffuser panel with absorption on either side) gives you control over both the timing and the energy of the rear reflection.

### 6.4 The Wall Behind the Speakers

This reflection bounces the speaker's rearward radiation back into the room. If the speaker is far from the front wall (1+ meters), this reflection arrives late enough to smear the bass and lower midrange. Absorption here helps, especially thick panels or bass traps.

If the speaker is close to the front wall (less than 0.5 meters), the reflection is so early that it is essentially part of the speaker's output. Some speakers are designed for near-wall placement and account for this. Do not over-absorb the front wall if your speakers are designed for it.

---

## 7. Measurement-Based Treatment

### 7.1 The Process

Acoustic treatment should never be done by guesswork. Measure, treat, re-measure. Here is the workflow:

**Before treatment:**
1. Set up your speakers in their intended positions
2. Use REW (Room EQ Wizard) with a measurement microphone at the listening position (detailed in Chapter 37)
3. Measure the frequency response, RT60, and waterfall plot
4. Identify problems:
   - Which frequencies have room mode peaks? (Look for narrow 10+ dB peaks in the bass)
   - What is the RT60? (Too long = too reverberant)
   - Are there comb filter patterns in the midrange? (Early reflections)

**Treatment phase 1: Bass traps in corners**
- Install corner bass traps
- Re-measure
- Expect: smoother bass response (peaks reduced by 3-8 dB), shorter RT60 in the bass

**Treatment phase 2: First reflection panels**
- Install panels at first reflection points
- Re-measure
- Expect: cleaner midrange and treble, better imaging (harder to measure, easier to hear), reduced comb filtering

**Treatment phase 3: Rear wall and fine-tuning**
- Add diffusion or absorption on the rear wall
- Add any additional panels needed
- Re-measure
- Expect: RT60 in target range, smoother overall response

**After treatment:**
- Compare the before and after measurements
- Use CamillaDSP (Chapter 29) for final EQ correction of remaining room mode issues that treatment could not fully address
- Do a final listening evaluation (Chapter 37)

### 7.2 What Treatment Can and Cannot Fix

**Treatment CAN fix:**
- Excessive reverb (RT60 too high)
- Early reflection smearing
- Flutter echo
- Some room mode energy (reducing peak levels)

**Treatment CANNOT fix:**
- Room mode nulls (where bass cancels). No amount of absorption fills in a null. Only changing speaker position, listener position, or adding multiple subwoofers can address nulls.
- Fundamental room dimension problems (a square room will always have overlapping modes)
- Insufficient bass extension from the speaker itself

This is why DSP room correction (Chapter 29) complements physical treatment. Treatment handles the time-domain problems (reflections, reverb) that DSP cannot fix. DSP handles the frequency-domain problems (mode peaks, response deviations) that treatment cannot fully fix. Together, they cover most issues.

---

## 8. The Listening Room vs The Party Venue

### 8.1 The Listening Room

Your home listening room is a controlled environment. You know the dimensions, the furniture, the wall materials. You control where the speakers go and where you sit. The goal is *fidelity* -- accurate reproduction of the recording.

**Priorities:**
1. Smooth bass response (bass traps + DSP correction)
2. Clean stereo imaging (first reflection treatment)
3. Appropriate RT60 (0.3-0.5 seconds)
4. Comfortable, natural-sounding room (not over-treated)

Treatment is permanent (or at least semi-permanent). You build it once, measure and optimize it, and enjoy it for years.

### 8.2 The Party Venue: Indoor

A party in a warehouse, community hall, or large room presents the opposite challenge. The space is large, the surfaces are hard (concrete, metal, glass), and RT60 can be 3-8 seconds. You cannot treat the room -- you are there for one night.

**Strategies:**
- **Speaker placement**: Point speakers away from the nearest hard wall. In a long room, place speakers along the long axis so reflections from side walls arrive at oblique angles and are weaker.
- **Use directional speakers**: Horn-loaded PA speakers from Chapter 17 have controlled dispersion. Aim them at the audience, not at the walls.
- **DSP delay for distributed systems**: If you use multiple speaker pairs (front and delay speakers), delay the rear speakers by the sound travel time so they reinforce rather than fight the front speakers.
- **Limit bass in reverberant spaces**: Bass modes in a large, hard room are devastating. If the bass is unintelligible, reduce sub level or use a high-pass filter to tighten the bass. Less is more in a reverberant space.
- **Accept imperfection**: You are not going for audiophile quality. You are going for a fun, loud, intelligible experience. Get the midrange clear (that is where vocals and melody live), keep the bass controlled, and let the room's natural reverb fill in the rest.

### 8.3 The Party Venue: Outdoor

An outdoor event in an open field is acoustically the simplest scenario. No walls, no modes, no reflections (except from the ground). Sound propagates, attenuates with distance, and that is it.

**The challenges are different:**
- **Distance**: Sound attenuates with distance (6 dB per doubling). You need raw SPL to cover the area.
- **Wind**: Wind carries sound in one direction and attenuates it in the other. Place speakers downwind of the audience if possible.
- **Noise ordinances**: Without walls to contain the sound, your PA carries to the neighbors. Know the local regulations.
- **Power**: You need electricity in a field. Generator or long extension runs. Plan power capacity.

No acoustic treatment needed. Just enough amp power, efficient speakers, and correct aiming. This is where the PA system from Chapter 39 shines -- it was designed for exactly this scenario.

### 8.4 The Middle Ground: Your Garage or Basement

Many of us end up using a garage, basement, or spare bedroom as both a listening room and an occasional party space. The treatment approach:

- Bass traps: permanent, in all corners
- Side wall panels: permanent, at first reflection points
- Rear wall diffusion: permanent
- Subwoofer and speaker placement: optimized for the primary use case (usually the stereo listening position)

When party mode hits, you crank it up. The treatment helps more than hurts -- it keeps the bass tighter and the overall sound cleaner even at party volumes. The only concession: you might add a pair of PA speakers for the party that you remove for serious listening.

---

## 9. Summary and Treatment Shopping List

For a typical home listening room (15-30 m²), here is the minimum recommended treatment:

| Item | Quantity | Placement | Estimated Cost |
|------|----------|-----------|---------------|
| Bass traps (floor-to-ceiling, 6" thick) | 4 | Vertical room corners | $50-80 each |
| Absorption panels (2' × 4' × 4") | 4-6 | First reflection points (side walls) | $25-40 each |
| Ceiling cloud (2' × 4' × 4") | 1-2 | Above listening position | $25-40 each |
| Rear wall diffuser (QRD or skyline) | 1-2 | Rear wall, centered | $40-80 each (DIY) |

**Total material cost: approximately $300-600.** This is less than most people spend on cables, and it makes a hundredfold bigger difference.

Pair this treatment with measurement-based DSP correction from CamillaDSP (Chapter 29) and you have a room that lets your carefully built speakers and amplifiers actually sound the way they were designed to sound. In Chapter 37, we will measure the results and fine-tune the entire system.
