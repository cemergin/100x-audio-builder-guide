<!--
  CHAPTER: 11
  TITLE: How Speakers Work
  PART: 2 — Speakers
  PREREQS: Part 0
  KEY_TOPICS: electromagnetic drivers, voice coil, cone/surround/spider, driver types, enclosures, radiation patterns, frequency response, phase alignment, room interaction
  DIFFICULTY: Beginner
  UPDATED: 2026-04-10
-->

# Chapter 11: How Speakers Work

> **Part 2 — Speakers** | Prerequisites: Part 0 | Difficulty: Beginner

You've been using speakers your entire life, but have you ever stopped to think about what's actually happening inside one? A speaker is, at its core, a device that converts electrical energy into air movement -- and by controlling that air movement precisely enough, it recreates music, voices, explosions, and every other sound you've ever heard through a playback system. That's borderline magic, and it's accomplished with remarkably simple physics.

In this chapter, we're going to take the speaker apart -- conceptually -- and understand every piece of the puzzle. We'll start with the electromagnetic driver itself (the thing most people call "a speaker"), then zoom out to the enclosure it lives in, and finally zoom all the way out to the room where you're listening. By the end, you'll understand why a bare driver screwed to a board sounds terrible, why your bookshelf speakers sound different when you move them to the corner, and why a PA system at a concert uses completely different hardware than your home stereo.

If Part 0 gave you the vocabulary of electronics, this chapter gives you the vocabulary of acoustics. Everything in Part 2 builds on what you learn here.

### In This Chapter
- The electromagnetic driver: voice coil, magnet, cone, surround, spider
- How electrical signal becomes air movement
- Driver types: woofer, midrange, tweeter, full-range, compression driver + horn
- Why a bare driver sounds terrible: the enclosure as acoustic system
- Radiation patterns and dispersion
- Frequency response: what flat means and why it matters
- Phase and time alignment at the crossover point
- The room as part of the system

### Related Chapters
- [Ch 0: Electricity Through the Lens of Sound](../part-0-electronics/00-electricity-through-sound.md) -- impedance fundamentals that apply to speaker loads
- [Ch 1: Components and What They Do to Signals](../part-0-electronics/01-components-and-signals.md) -- the components used in crossover networks
- [Ch 12: The Language of Speakers](12-language-of-speakers.md) -- the specifications that quantify everything in this chapter
- [Ch 14: Cabinet Design](14-cabinet-design.md) -- deep dive into enclosure types
- [Ch 36: Room Acoustics and Treatment](../part-6-system-design/36-room-acoustics-treatment.md) -- advanced treatment of room interaction

---

## 1. The Electromagnetic Driver

A speaker driver is an electric motor that moves back and forth instead of spinning. That single sentence is the key insight. Everything else is details.

### 1.1 The Motor: Voice Coil and Magnet

At the heart of every conventional speaker driver is a permanent magnet -- typically a large ferrite ring or a smaller, more powerful neodymium disc -- and a coil of wire called the **voice coil**. The voice coil is a cylinder of wire wound around a lightweight tube called the **former**, and it sits in a narrow gap in the magnetic field created by the permanent magnet.

When you pass electrical current through the voice coil, it creates its own magnetic field (you learned this in Part 0 with inductors). That field interacts with the permanent magnet's field, and the voice coil experiences a force -- pushing it forward or backward depending on the direction of the current. Reverse the current, and the force reverses.

An audio signal is an alternating current. The positive half of the waveform pushes the coil (and the cone attached to it) outward. The negative half pulls it back. The faster the signal alternates (higher frequency), the faster the cone moves back and forth. The larger the signal (higher amplitude), the farther the cone travels on each cycle.

That is the entire conversion: electrical signal to mechanical motion.

**The BL product** (force factor) tells you how strong this motor is. B is the magnetic flux density in the gap (in Tesla), and L is the length of wire in the gap (in meters). Multiply them and you get the force per ampere of current. A high BL means a strong motor -- for the same electrical input, the cone pushes harder. We will quantify this in Chapter 12.

### 1.2 The Mechanical System: Cone, Surround, and Spider

The voice coil is attached to a **cone** (also called the diaphragm) -- the large visible surface of the driver. When the voice coil moves, the cone moves with it, pushing air.

But the cone needs to be held in place so it can only move back and forth along one axis (called the **excursion** axis), not wobble side to side. Two flexible elements do this job:

- **The surround** (also called the rim or edge): This is the flexible ring connecting the outer edge of the cone to the driver's metal frame (the **basket**). It is usually made of rubber, foam, or treated fabric. You can see it from the front -- it is the soft ring around the cone.

- **The spider** (also called the suspension): This is a corrugated disc of stiff fabric located behind the cone, connecting the voice coil former to the basket. You typically cannot see it from the front. The spider provides most of the driver's restoring force -- the "spring" that pulls the cone back to center when the signal stops.

Together, the surround and spider form the driver's **suspension system**. They do three things:

1. **Center the voice coil** in the magnetic gap so it does not rub against the magnet (which would cause distortion or destruction)
2. **Provide a restoring force** -- a mechanical spring that returns the cone to its rest position
3. **Allow linear motion** along the excursion axis while resisting sideways movement

Think of it like a software system: the magnet and voice coil are the "engine," the cone is the "output interface," and the suspension is the "constraint system" that keeps everything operating within safe parameters.

### 1.3 The Dustcap and Frame

Two more parts round out the driver:

- **The dustcap**: The dome or flat cap in the center of the cone. It keeps dust and debris out of the magnetic gap (dust in the gap = scratchy sounds and eventual failure). In some designs, especially tweeters, the dustcap is also a radiating surface that contributes to the high-frequency output.

- **The basket (frame)**: The rigid metal structure that holds everything together and provides mounting points for screwing the driver into a cabinet. Stamped steel is cheap and common. Cast aluminum is stiffer, heavier, and found on better drivers. The basket matters because a flexing frame means the cone's motion is not perfectly controlled.

### 1.4 How It All Works Together

Here is the complete signal chain:

1. Amplifier sends electrical audio signal (alternating current) to the speaker terminals
2. Current flows through the voice coil
3. Voice coil's magnetic field interacts with permanent magnet's field
4. Voice coil (and attached cone) is pushed forward and pulled backward
5. Cone pushes and pulls air, creating pressure waves
6. Pressure waves reach your ears
7. Your brain interprets them as sound

The fidelity of this chain depends on how linearly each step operates. "Linear" here means the output is directly proportional to the input at every point. Non-linearity means distortion -- and every real driver introduces some. The suspension gets stiffer at the extremes of travel. The magnetic field is not perfectly uniform across the voice coil's range of motion. The cone flexes at high frequencies instead of moving as a perfect rigid piston.

Speaker engineering is the art of managing these non-linearities.

---

## 2. Driver Types

No single driver can reproduce the full range of human hearing (roughly 20 Hz to 20,000 Hz). The physics make it impossible to optimize one cone for both the deep, slow movements needed for bass and the tiny, rapid movements needed for treble. So we use specialized drivers.

### 2.1 Woofers

A **woofer** is a driver designed for low frequencies -- typically below 1,000-3,000 Hz, depending on the design. Woofers have:

- **Large cones** (typically 5" to 18" in diameter) to move enough air for bass. Low frequencies require moving a lot of air, and a bigger cone moves more air per excursion cycle.
- **Heavy cones** (relatively) -- the added mass lowers the resonant frequency, which is desirable for bass reproduction.
- **Long excursion capability** -- the voice coil and suspension are designed to allow the cone to travel a significant distance (sometimes 10-20mm peak-to-peak or more) without distortion.
- **Flexible, compliant suspensions** to allow that long travel.

The trade-off: large, heavy cones cannot change direction fast enough to reproduce high frequencies accurately. As frequency increases, the cone stops moving as a rigid piston and starts "breaking up" -- different parts of the cone move independently, creating distortion and uneven frequency response. This is why we hand off high frequencies to other drivers.

### 2.2 Midrange Drivers

A **midrange driver** covers the middle of the frequency spectrum -- roughly 300 Hz to 5,000 Hz, though the exact range varies by design. This is the most critical range for music and speech because the human ear is most sensitive to these frequencies.

Midrange drivers are typically:
- Smaller cones (3" to 6" diameter)
- Lighter, stiffer cones (often paper, polypropylene, or woven materials)
- Designed for low distortion in the vocal range

In a 3-way speaker (woofer + midrange + tweeter), the midrange driver is arguably the most important driver because it handles the frequencies your ear cares about most. Many 2-way speakers skip the dedicated midrange and have the woofer handle both bass and midrange (a "woofer-midrange" or "mid-woofer"), handing off to the tweeter somewhere between 1.5 kHz and 3 kHz.

### 2.3 Tweeters

A **tweeter** reproduces high frequencies, typically above 1,500-4,000 Hz up to 20,000 Hz or beyond. High frequencies require very small, very light diaphragms that can change direction thousands of times per second.

Common tweeter types:

- **Dome tweeters**: The most common type in home audio. A small dome (typically 0.75" to 1.1" diameter) made of silk (soft dome -- smooth, warm sound) or metal like aluminum or titanium (hard dome -- extended high-frequency response but can have a sharp resonance peak). The dome is attached directly to a voice coil -- there is no separate cone.

- **Ribbon tweeters**: Instead of a dome and voice coil, a thin metallic ribbon acts as both the conductor (carries current) and the diaphragm (moves air). The ribbon sits in a magnetic field and is driven directly. Ribbons are extremely light, so they can respond to transients incredibly fast. Trade-off: they are fragile and often have lower sensitivity (they need more power).

- **Planar/AMT tweeters**: The Air Motion Transformer (AMT or "Heil") tweeter uses a pleated diaphragm with conductive traces. Instead of pushing air like a piston, the pleats squeeze and expand, "squirting" air out. Very fast, very detailed, good dispersion in one axis. Increasingly popular in quality speakers.

- **Horn-loaded tweeters**: A small driver (often a compression driver) coupled to a horn. The horn acts as an acoustic transformer, improving efficiency dramatically. Common in PA speakers where high output is essential. We will talk more about horns below.

### 2.4 Full-Range Drivers

A **full-range driver** attempts to cover the entire frequency spectrum with a single cone. Think of it as the "monolith" approach versus the microservices approach of multi-way speakers.

Full-range drivers are typically 3" to 8" in diameter with a lightweight, stiff cone (often paper) and a large, carefully designed dustcap or "whizzer cone" -- a small secondary cone on top of the main cone that decouples from the main cone at high frequencies and radiates treble.

**Advantages**: No crossover needed (the crossover introduces its own problems, as you'll learn in Chapter 13). Perfect time alignment (one source for all frequencies). Simpler system.

**Trade-offs**: Compromised bass (not enough cone area or excursion for deep bass), compromised treble (the cone is too large to be an ideal tweeter), and narrowing dispersion at high frequencies (the cone "beams" -- more on this in Section 4).

Full-range drivers are the darling of certain audiophile communities (especially single-ended tube amplifier enthusiasts) and are genuinely excellent for midrange clarity. They work best in specialized enclosures (back-loaded horns, transmission lines) that augment their bass response.

### 2.5 Compression Drivers and Horns

If conventional drivers are like directly-coupled output stages, compression drivers with horns are like transformer-coupled stages -- they use acoustic impedance matching to dramatically increase efficiency.

A **compression driver** is a specialized driver with a small diaphragm (typically 1" to 4" diameter, often titanium or phenolic) and a very powerful motor (high BL product). The diaphragm doesn't radiate directly into the room. Instead, it fires into a small chamber (the **compression chamber**) that narrows the acoustic output through a small opening (the **throat**) into a **horn**.

The horn is a flared tube that gradually expands from the throat to a much larger opening (the **mouth**). This gradual expansion transforms the high-pressure, low-volume air movement at the throat into lower-pressure, higher-volume air movement at the mouth. The result: dramatically higher efficiency.

A typical direct-radiator tweeter might be 87-92 dB sensitive (1W/1m). A compression driver on a horn can easily hit 105-110 dB at the same power. That is a 15-20 dB difference -- meaning the horn system needs roughly 30 to 100 times less amplifier power to produce the same volume.

**Why not use horns for everything?** Because horns have a lower frequency limit determined by the mouth size (the mouth must be comparable to the wavelength), and bass wavelengths are enormous (17 feet at 65 Hz). Bass horns exist -- they are called "folded horns" and they are the size of a refrigerator. That is fine for a nightclub, but not for your living room.

Compression drivers and horns are the backbone of PA systems. We will build one in Chapter 17.

> **What happens if you connect a tweeter to bass frequencies?**
>
> This is not academic curiosity -- it is a real failure mode. If you send low-frequency signals to a tweeter, the voice coil will try to make excursions it was never designed for. Best case: severe distortion as the dome bottoms out. Worst case: the voice coil overheats (trying to dissipate power it cannot convert to motion efficiently) or the suspension tears. Tweeters can die in milliseconds from a bass transient. This is why crossovers are not optional in multi-way systems -- they are protective devices as much as they are frequency-routing devices.

---

## 3. The Enclosure: Why Bare Drivers Sound Terrible

Screw a woofer to a flat board, wire it up, and play music. What do you hear? Thin, anemic sound with almost no bass. The driver is working fine -- so why does it sound so bad?

### 3.1 The Cancellation Problem

When a cone moves forward, it creates a positive pressure wave in front (compression) and simultaneously a negative pressure wave behind (rarefaction). At low frequencies, where the wavelength is much larger than the cone, these two waves wrap around the edge of the board and meet. Since they are exactly out of phase, they cancel each other out.

This is called **acoustic short-circuit**, and it's the fundamental reason bare drivers have no bass. The front wave and back wave cancel below a frequency determined by the size of the baffle (the board the driver is mounted on). For a typical 12" baffle, cancellation becomes severe below about 450 Hz.

Think of it this way: the driver is working hard, pushing air forward and pulling it backward, but the net result in the room is near zero because the two waves cancel. It is like trying to fill a bathtub with the drain open.

### 3.2 What an Enclosure Does

An enclosure (cabinet, box -- we use these terms interchangeably) solves the cancellation problem by isolating the front wave from the back wave. The simplest approach: seal the driver in a box. Now the back wave is trapped inside the box and can't reach the front to cancel.

But there is more to it than just "prevent cancellation." The trapped air inside a sealed box acts as a spring -- it compresses when the cone moves inward and expands when the cone moves outward. This spring interacts with the driver's mechanical spring (the suspension) to change the system's resonant frequency and damping behavior. Different enclosure types manage the back wave in different ways:

- **Sealed (acoustic suspension)**: Traps the back wave. Simple, predictable, well-damped bass.
- **Ported (bass reflex)**: Uses a tuned port (tube) to invert the phase of the back wave at a specific frequency and radiate it constructively through the port. More bass output, but with trade-offs.
- **Transmission line**: Gradually absorbs the back wave through a long, stuffed line.
- **Horn-loaded**: Uses the back wave to drive a horn for increased efficiency.

We'll explore all of these in detail in Chapter 14. For now, just understand: the enclosure isn't a passive container. It's an active part of the acoustic system. Choosing the right enclosure for a driver is as critical as choosing the right driver in the first place.

### 3.3 The Infinite Baffle Thought Experiment

If you could mount a driver in an infinitely large, flat wall that extended in every direction, the front and back waves could never meet (they radiate into separate half-spaces). This is called an **infinite baffle**, and it is the theoretical ideal -- the driver behaves as if it has perfect isolation between front and back.

You can approximate this by mounting a driver in a wall between two rooms. This is actually done in some high-end installations and recording studios. The wall acts as a near-infinite baffle, and the driver can produce deep bass without a box.

Understanding the infinite baffle concept helps you see that the purpose of any enclosure is to approximate this isolation, each in a different way with different trade-offs.

---

## 4. Radiation Patterns and Dispersion

Sound doesn't come out of a speaker like light from a laser -- it spreads out. But how it spreads depends dramatically on the frequency and the size of the driver.

### 4.1 The Relationship Between Wavelength and Driver Size

Here is the key rule: **when the wavelength of the sound is much larger than the driver, the driver radiates omnidirectionally (sound goes everywhere equally). When the wavelength is smaller than the driver, the sound begins to "beam" -- it concentrates into a narrow cone in front of the driver.**

Some numbers to ground this:
- 100 Hz has a wavelength of about 3.4 meters (11 feet). Every driver is tiny compared to this, so bass radiates in all directions.
- 1,000 Hz has a wavelength of about 34 cm (13.4 inches). A 10" woofer is comparable in size, so it starts to beam at this frequency.
- 10,000 Hz has a wavelength of about 3.4 cm (1.3 inches). Even a 1" tweeter is comparable in size, so treble beams noticeably.

This is why your woofer sounds the same whether you're directly in front of it or off to the side, but your tweeter sounds noticeably duller when you move off-axis. The high frequencies are being directed forward in a beam while the lows go everywhere.

### 4.2 Why Dispersion Matters

Dispersion matters for two reasons:

1. **Listening window**: If you're not sitting exactly on-axis (directly in front of the speaker), the off-axis frequency response determines what you hear. Since most people aren't surgically positioned in a single sweet spot, good off-axis response means the speaker sounds good over a wider area.

2. **Room interaction**: The sound that bounces off walls, ceiling, and floor (early reflections and reverberation) is predominantly the off-axis sound. Even at the sweet spot, a significant portion of what you hear is reflected sound. If the off-axis response is dramatically different from the on-axis response (e.g., the highs drop off a cliff 30 degrees off-axis), the reflected sound will be "colored" differently from the direct sound, and the speaker will sound unnatural in a room.

The best speakers have a smooth, gradually narrowing dispersion as frequency increases. The off-axis response should look like a slightly attenuated version of the on-axis response, not a completely different shape with peaks and dips.

### 4.3 Controlled Directivity

**Controlled directivity** means intentionally managing the speaker's radiation pattern so that it is consistent across a wide frequency range. This is a major goal in professional speaker design.

Horns are the classic tool for controlled directivity. A well-designed horn can maintain a consistent coverage angle (say, 90 degrees horizontal by 40 degrees vertical) across its entire operating range. This is incredibly valuable in PA systems where you need to cover a specific audience area without blasting sound onto walls and ceilings.

In home audio, waveguides serve a similar purpose. A **waveguide** is a shallow horn-like structure around a tweeter that controls its dispersion to better match the woofer's dispersion at the crossover frequency. This improves off-axis response and makes the speaker sound more natural in a room. Many modern speakers use waveguide-loaded tweeters.

> **What happens if the woofer and tweeter have very different dispersion patterns at the crossover frequency?**
>
> You get a "dispersion mismatch." Imagine the woofer is beaming narrowly at 3 kHz (where it crosses over to the tweeter) but the tweeter is radiating widely at the same frequency. On-axis, the crossover might be perfectly flat. But off-axis, you hear much less woofer and much more tweeter at the crossover frequency -- creating a bright, harsh sound off-axis. This is one of the subtler reasons crossover frequency selection matters. You want to cross over at a frequency where both drivers have similar dispersion.

---

## 5. Frequency Response

The single most important specification for a speaker is its **frequency response** -- a graph showing how loud the speaker plays at each frequency for a given input level.

### 5.1 What a Flat Response Means

A "flat" frequency response means the speaker reproduces all frequencies at equal loudness. If you feed it pink noise (equal energy per octave), every frequency band comes out at the same level. On a frequency response graph, this looks like a straight horizontal line.

Why does flat matter? Because a flat speaker adds nothing and subtracts nothing from the recording. It is a transparent window to the music. If the recording engineer intended a particular balance of bass, midrange, and treble, a flat speaker reproduces that balance faithfully.

In practice, no speaker is perfectly flat. A good speaker might be within plus or minus 3 dB across its operating range. A great speaker might hold plus or minus 1.5 dB. A cheap speaker might have 10 dB swings.

### 5.2 When Flat Does Not Matter (Or Is Not Desired)

Not everyone wants a flat speaker, and not every application demands one:

- **PA speakers** are often designed with a gently rising response in the upper midrange/treble (a "presence peak") to improve vocal intelligibility in noisy environments.
- **Studio monitors** aim for flat response because they are tools for making decisions about a mix.
- **Hi-fi speakers** often have a slight warmth (mild bass emphasis) or sparkle (mild treble emphasis) that makes them pleasant to listen to for long periods. This is sometimes called the speaker's "voicing" or "house sound."
- **Some audiophile speakers** intentionally have a recessed upper midrange to reduce listening fatigue, even though this technically makes them less accurate.

The important thing is that the response should be smooth -- free of sharp peaks and dips. A speaker that is 2 dB warm in the bass but smooth overall will sound far more pleasant than a speaker that is flat on average but has a 6 dB peak at 3 kHz.

### 5.3 Reading a Frequency Response Graph

A frequency response graph has:
- **X-axis**: Frequency (Hz), typically from 20 Hz to 20,000 Hz, on a logarithmic scale
- **Y-axis**: Sound pressure level (dB SPL), typically spanning a 30-40 dB range

Key things to look for:
- **Smoothness**: Are there sharp peaks or dips? These indicate resonances or cancellations.
- **Extension**: How low does the bass go before it rolls off? How high does the treble extend?
- **Roll-off rate**: How steeply does the response fall off at the frequency extremes?
- **Crossover region**: In a multi-way speaker, look for dips or bumps where the drivers hand off to each other.

We will learn to read driver datasheets and measurement graphs in detail in Chapter 12.

### 5.4 On-Axis vs. Off-Axis Response

The on-axis frequency response is measured directly in front of the speaker. But as we discussed in Section 4, the speaker's output varies with angle. A complete picture requires measuring at multiple angles -- 15 degrees, 30 degrees, 45 degrees, 60 degrees off-axis, both horizontal and vertical.

**Power response** (or sound power) is the total acoustic output summed over all angles. It tells you how much total energy the speaker is putting into the room, regardless of direction. A speaker with good power response will excite the room evenly, leading to natural-sounding reflections.

The relationship between on-axis response and power response is called the **directivity index (DI)**. A speaker whose DI increases smoothly with frequency will sound natural in typical rooms.

---

## 6. Phase and Time Alignment

When two drivers reproduce the same frequency (which happens in the crossover region where both are operating), their outputs need to arrive at your ears at the same time and in the same phase. If they don't, cancellation or reinforcement occurs, creating dips or peaks in the response.

### 6.1 What Phase Means in This Context

**Phase** describes where a wave is in its cycle at a given moment. Two waves that are "in phase" (0 degrees phase difference) reinforce each other -- their peaks line up. Two waves that are 180 degrees out of phase cancel each other -- one's peak lines up with the other's trough.

In a multi-way speaker, the crossover filter introduces phase shifts (all analog filters do). Different filter orders introduce different amounts of phase shift. A 2nd-order filter introduces 180 degrees of phase shift at the crossover frequency. A 4th-order Linkwitz-Riley filter introduces 360 degrees (effectively back in phase). The crossover design must account for these phase shifts so the combined acoustic output of the two drivers is correct.

### 6.2 Physical Time Alignment

Even if the crossover gets the electrical phase right, the drivers need to be physically aligned so their sound waves arrive at the listener at the same time. The acoustic center of a woofer (where the sound effectively originates) is typically set back from the baffle surface because of the cone's depth. A dome tweeter's acoustic center is near the front of the dome.

If the woofer's acoustic center is 2 inches behind the tweeter's, the woofer's sound arrives later. At the crossover frequency, this time difference translates to a phase offset that can degrade the combined response.

Solutions include:
- **Stepped baffles**: The tweeter is mounted on a recessed section of the baffle, moving it back to align with the woofer's acoustic center.
- **Sloped baffles**: The entire baffle is tilted so the woofer is closer to the listener and the tweeter is farther away, equalizing the path lengths.
- **Digital delay**: In an active system (covered in Part 4), you can delay the tweeter's signal digitally to align it with the woofer. This is incredibly precise and is one of the killer advantages of active crossovers.

### 6.3 Why It Matters

Poor time alignment causes:
- A dip or bump in the frequency response at the crossover point
- Poor imaging (the sound doesn't "snap" to a precise location in space)
- Phase response that varies with vertical listening position (tilt your head up or down and the sound changes character)

Good time alignment is one of the things that separates a "sounds fine" speaker from a "sounds amazing" speaker. You may not consciously notice it, but your brain's spatial processing absolutely does.

> **What happens if you wire one driver with reversed polarity?**
>
> If you accidentally connect one driver with its positive and negative terminals swapped (reversed polarity), it will move outward when the other moves inward. At the crossover frequency where both are playing, they will be 180 degrees out of phase and largely cancel. You will hear a huge dip at the crossover frequency and the sound will be thin and phasey. This is one of the most common wiring mistakes in speaker building, and it is easy to check: just make sure both drivers push outward when you connect a battery briefly to the speaker terminals (the positive terminal of the battery goes to the positive terminal of the speaker).

---

## 7. The Room as Part of the System

Here is a truth that speaker designers sometimes wish was not true: **the room you listen in is as important as the speaker itself.** The same speaker will sound dramatically different in a small, carpeted bedroom versus a large, tiled kitchen. Understanding why will save you from blaming your speakers for problems caused by your room.

### 7.1 Boundary Reinforcement

When you place a speaker near a wall, the sound reflecting off the wall reinforces the direct sound, boosting bass frequencies. Place it near two walls (in a corner) and you get even more reinforcement. Specifically:

- **Free-standing** (away from all walls): No reinforcement. The speaker radiates into "full space" (4-pi steradians).
- **Against one wall**: The wall acts as a mirror, effectively doubling the bass output (+6 dB boost at low frequencies). The speaker now radiates into "half space" (2-pi).
- **Wall-floor junction**: Two boundaries. Bass boosted further.
- **Corner**: Three boundaries. Maximum bass boost. This can be 12-18 dB at very low frequencies compared to free-standing.

This is why the same speaker can sound thin when placed on stands in the middle of a room and boomy when shoved into a corner. Neither placement is "wrong" -- but the speaker's response changes, and you may need to adjust accordingly (EQ, port plugs, choosing a different speaker designed for near-wall placement).

### 7.2 Room Modes

In a rectangular room, sound waves bounce back and forth between parallel walls. At certain frequencies, the distance between walls is an exact multiple of the half-wavelength, and a **standing wave** (room mode) forms. At these frequencies, some locations in the room have extremely loud bass (the peaks of the standing wave) and others have almost no bass (the nulls).

For example, in a room that is 4 meters long, the first axial mode occurs at approximately:

```
f = c / (2 * L) = 343 / (2 * 4) = 42.9 Hz
```

At 43 Hz, there is a pressure peak at each wall and a null in the center of the room. If you sit at the center, you'll hear almost no 43 Hz energy. Move your chair a foot and suddenly the bass is back. This isn't the speaker's fault -- it's physics.

Room modes are a major reason why speaker measurements made in an anechoic chamber (a room designed to have no reflections) don't perfectly predict how a speaker sounds in your room. They're also a major reason room treatment and EQ (covered in Chapter 36) are important parts of any serious listening setup.

### 7.3 Early Reflections

When sound leaves the speaker, the first copy that arrives at your ear is the **direct sound**. Shortly after (within about 1-10 milliseconds), reflected copies arrive -- bounced off the nearest walls, floor, and ceiling. These are **early reflections**.

Your brain uses early reflections to perceive the size and character of the space. But if the early reflections are too loud or have a very different tonal character from the direct sound (because the speaker's off-axis response is uneven), they can smear the sound or add coloration.

This is another reason why a speaker's off-axis response matters so much: the early reflections ARE the off-axis sound. A speaker with smooth, well-controlled off-axis response will integrate better with any room.

### 7.4 Implications for Speaker Building

As a speaker builder, you need to keep the room in mind at every stage:

- **During driver selection**: Consider the room size. A 12" woofer in a small bedroom will excite room modes violently. A 5" woofer in a large living room might not have enough bass output.
- **During cabinet design**: The enclosure's bass response interacts with room gain. A speaker designed for free-standing use will be boomy near walls. A speaker designed for near-wall use (reduced baffle step compensation) will sound thin when pulled out into the room.
- **During crossover tuning**: If you can only measure in your room (not an anechoic chamber), you need to account for room effects in your measurements. REW (Room EQ Wizard, covered in Chapter 15) helps you do this.
- **After building**: Room treatment (absorption panels, bass traps) and EQ (parametric EQ or DSP, covered in Part 4) are the final stages of making your system sound great.

The speaker and the room are a system. You can't fully optimize one without considering the other.

---

## 8. Putting It All Together: How a Complete Speaker System Works

Let's trace the signal through a complete 2-way bookshelf speaker (the kind we'll build in Chapter 16):

1. **Amplifier output** sends the full-range audio signal to the speaker's input terminals.
2. **Passive crossover network** inside the cabinet splits the signal. The low-pass section (inductors) sends bass and midrange to the woofer. The high-pass section (capacitors) sends treble to the tweeter. The crossover also includes impedance compensation, level matching, and baffle step correction.
3. **Woofer** receives the low-passed signal and reproduces frequencies from its low-frequency limit (determined by the enclosure) up to the crossover frequency. Its cone moves back and forth, pushing air.
4. **Tweeter** receives the high-passed signal and reproduces frequencies from the crossover frequency up to its high-frequency limit. Its dome vibrates rapidly, pushing air.
5. **Crossover region**: Both drivers are producing sound near the crossover frequency. The crossover filters are designed so that their outputs sum correctly (flat combined response, proper phase alignment).
6. **Enclosure** controls the woofer's back wave. In a sealed box, it traps the back wave and the sealed air acts as a spring. In a ported box, the port radiates additional bass energy at the tuning frequency.
7. **Cabinet walls** are rigid and well-damped to avoid adding their own resonances to the sound. Internal bracing and damping material keep the panels from vibrating.
8. **Sound radiates** into the room. Bass goes everywhere. Midrange and treble are more directional.
9. **Room** adds boundary reinforcement, modes, and reflections that color the final sound.
10. **Your ears** receive the combined direct and reflected sound. Your brain interprets it as music.

Every step in this chain matters. Over the next six chapters, we'll learn to optimize each one.

---

## 9. Chapter Summary and What Comes Next

In this chapter, you learned:

- A speaker driver is an electromagnetic motor (voice coil + magnet) attached to a cone that pushes air
- The cone, surround, and spider form a mechanical system with mass, compliance, and damping
- Different driver types (woofer, midrange, tweeter, full-range, compression driver) are optimized for different frequency ranges
- An enclosure is necessary to prevent acoustic short-circuit (front and back wave cancellation)
- Radiation patterns narrow as frequency increases relative to driver size
- Flat frequency response means equal output at all frequencies; smooth response matters more than ruler-flat
- Phase and time alignment at the crossover point are critical for accurate combined response
- The room dramatically affects what you hear through boundary reinforcement, room modes, and reflections

In Chapter 12, we'll learn the formal language of speakers -- the specifications and parameters that quantify everything we discussed qualitatively here. You'll learn to read a driver datasheet the way a software engineer reads an API specification: understanding what every number means and how it affects your design decisions.
