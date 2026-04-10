<!--
  CHAPTER: 14
  TITLE: Cabinet Design
  PART: 2 — Speakers
  PREREQS: Chapter 13
  KEY_TOPICS: sealed, ported, transmission line, horn-loaded, band-pass, baffle diffraction, bracing, damping materials, MDF, plywood, volume calculations
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 14: Cabinet Design

> **Part 2 — Speakers** | Prerequisites: Chapter 13 | Difficulty: Intermediate

The cabinet is not a box that holds the driver. The cabinet IS part of the speaker. Its internal volume, shape, and tuning fundamentally change the driver's bass response, and its construction determines whether you hear music or the cabinet resonating along with it. A driver that sounds mediocre in one enclosure can sound extraordinary in another, and vice versa.

Think of the enclosure as the runtime environment for the driver. The same application (driver) behaves very differently depending on the container it runs in (sealed vs. ported vs. horn), just as the same code behaves differently in a resource-constrained container versus one with unlimited memory. Choosing the right enclosure for your driver -- and building it properly -- is as important as choosing the right driver in the first place.

In this chapter, we will cover every major enclosure type, understand the physics behind each one, learn when to choose which, and then dive into the practical construction details that separate a resonant, rattling box from a dead-silent acoustic instrument.

### In This Chapter
- Sealed (acoustic suspension) enclosures
- Ported (bass reflex) enclosures
- Transmission line enclosures
- Horn-loaded enclosures
- Band-pass enclosures
- When to use each type
- Baffle diffraction and its mitigation
- Internal bracing and resonance control
- Damping materials: fiberglass, polyfill, acoustic foam
- Construction materials: MDF vs. plywood
- Cabinet volume calculations

### Related Chapters
- [Ch 12: The Language of Speakers](12-language-of-speakers.md) -- T-S parameters that determine enclosure requirements
- [Ch 13: Crossover Design](13-crossover-design.md) -- baffle step compensation ties to cabinet width
- [Ch 15: Design Tools and Simulation](15-design-tools-simulation.md) -- WinISD enclosure modeling
- [Ch 16: Build: Bookshelf Speakers](16-build-bookshelf-speakers.md) -- applying these principles
- [Ch 32: Professional Cabinet Finishing](../part-5-pcb-professional/32-cabinet-finishing.md) -- advanced finishing techniques
- [Ch 33: Woodworking for Audio](../part-5-pcb-professional/33-woodworking-audio.md) -- construction skills

---

## 1. Sealed (Acoustic Suspension) Enclosures

The sealed enclosure is the simplest and most predictable design. The driver is mounted in a completely airtight box. No ports, no vents, no holes of any kind (except the driver cutout, which the driver itself seals).

### 1.1 How It Works

When the cone moves inward, it compresses the air inside the box. When it moves outward, it rarefies (expands) the air. The trapped air acts as a spring -- it resists the cone's motion in both directions. This air spring adds to the driver's mechanical spring (the suspension), creating a stiffer total system.

The effect on the driver's behavior:
- **Resonance frequency increases**: The added stiffness raises the system's resonant frequency (Fc) above the driver's free-air resonance (Fs). Smaller box = stiffer spring = higher Fc.
- **Q factor changes**: The system's total Q (Qtc) depends on both the driver's Qts and the box volume. A smaller box raises Qtc; a larger box keeps Qtc closer to Qts.
- **Bass response rolls off below Fc**: The rolloff rate is 12 dB per octave (2nd order) -- gentle and predictable.

### 1.2 The Key Relationship: Volume, Qtc, and Fc

For a sealed box, the system behavior is determined by the ratio of box volume (Vb) to the driver's Vas:

```
Qtc = Qts * sqrt(Vas/Vb + 1)
Fc = Fs * sqrt(Vas/Vb + 1)
```

This means:
- **Smaller box** → higher Qtc, higher Fc (less bass extension, possibly boomy if Qtc > 0.707)
- **Larger box** → Qtc approaches Qts, Fc approaches Fs (more bass extension, better damping, but diminishing returns and larger cabinet)

### 1.3 Target Alignments

The Qtc value determines the system's bass response shape:

- **Qtc = 0.500 (Bessel)**: Maximum group delay flatness. Very well-damped, tight bass, but the -3 dB point is well above Fc. Bass sounds "fast" and "dry" but limited in extension.
- **Qtc = 0.577 (sub-Butterworth)**: Extremely well-damped. Good transient response. Early rolloff.
- **Qtc = 0.707 (Butterworth, B2)**: The "textbook" alignment. Maximally flat response -- no peak, no dip before the rolloff. The -3 dB point equals the system Fc. This is the most common target for sealed speakers.
- **Qtc = 1.0**: Has a slight peak (~1.25 dB) before rolloff. Bass sounds "full" and slightly warm. Some designers intentionally target this for speakers that will be used far from walls (where room gain compensates for the natural rolloff).
- **Qtc > 1.0**: Increasing peak before rolloff. Sounds boomy and poorly controlled. Generally undesirable.

Most designers target Qtc between 0.6 and 0.8 for home audio.

### 1.4 Advantages of Sealed Enclosures

- **Simplest to build**: No port to tune, no port resonance issues
- **Predictable, gentle rolloff**: 12 dB/octave below Fc, versus 24 dB/octave for ported. This means sealed speakers reach deeper than ported at very low frequencies (below the port tuning), even though ported is louder in the upper bass.
- **Excellent transient response**: The well-damped sealed system has minimal group delay. Bass is tight, clean, and well-defined.
- **Compact size**: For a given driver, a sealed box is typically smaller than a ported box (though with higher Fc).
- **No port noise**: Ported boxes can produce turbulent noise from the port at high excursion. Sealed boxes have no such issue.
- **Robust low-frequency behavior**: The sealed box constrains the driver's excursion below resonance. A ported speaker's excursion becomes uncontrolled below the port tuning frequency.

### 1.5 Disadvantages of Sealed Enclosures

- **Less bass extension** for a given box size compared to ported
- **Lower efficiency** below the system resonance
- **Requires a driver with appropriate Qts** (typically 0.3-0.5 for good sealed results)

> **What happens if you use a driver with Qts = 0.8 in a sealed box?**
>
> The system Qtc will be well above 1.0 in any reasonable box size. The result: a prominent bass hump followed by rapid rolloff. The bass will sound boomy, bloated, and poorly controlled. This driver wants a ported enclosure (where the port provides damping at the tuning frequency) or a very large sealed box (which defeats the purpose of sealed's compact advantage). This is why checking Qts is the first step in choosing an enclosure type.

---

## 2. Ported (Bass Reflex) Enclosures

The ported enclosure is the most popular enclosure type for good reason: it provides more bass output and deeper extension than sealed, at the cost of some complexity and trade-offs.

### 2.1 How It Works

A ported box has a tube (the port) that connects the interior air to the outside. The air in the port has mass, and the air in the box has springiness (compliance). Together, they form a **Helmholtz resonator** -- like blowing across the top of a bottle.

At the port's resonant frequency (the tuning frequency, Fb), the port's output is at maximum and is in phase with the cone's output. This constructive addition extends the bass response below what the driver alone could achieve.

Here is the key insight: **at the tuning frequency, the port does most of the work.** The cone's excursion actually reaches a minimum at Fb because the port's resonance unloads the cone. This is extremely useful -- it means the driver is under less stress at the frequency where the port is contributing, extending the system's maximum SPL capability.

### 2.2 Tuning Frequency and Port Dimensions

The port tuning frequency is determined by the port length, port area, and box volume:

```
Fb = (c / (2 * pi)) * sqrt(Sv / (Lv * Vb))
```

Where:
- c = speed of sound (343 m/s)
- Sv = port cross-sectional area (m^2)
- Lv = port effective length (m), including end corrections
- Vb = net box volume (m^3)

In practice, you use WinISD or another tool to calculate port dimensions. But the relationships are:

- **Longer port** → lower tuning frequency
- **Smaller port area** → lower tuning frequency (for same length), but risk of port noise
- **Larger box** → lower tuning frequency (for same port)

### 2.3 Port Area and Turbulence

The port must be large enough in cross-section to prevent turbulent air flow at high excursion levels. When air velocity in the port gets too high, it transitions from smooth (laminar) flow to turbulent flow, producing a "chuffing" noise -- a breathy, windy sound that is very audible on bass-heavy material.

**Rule of thumb**: Minimum port area should be about 35-50% of the driver's Sd (effective cone area) for moderate listening levels. For high-SPL applications (subwoofers, PA speakers), use 50-100% of Sd or more.

WinISD can plot the air velocity in the port versus frequency, so you can verify that it stays below the turbulence threshold (typically 15-20 m/s for round ports, lower for slot ports with sharp edges).

**Flared ports**: Ports with flared (trumpet-shaped) ends reduce turbulence at a given air velocity by smoothing the air flow at entry and exit. If your design is tight on port area, flared ports can buy you some headroom.

### 2.4 Round Ports vs. Slot Ports

**Round ports**: Typically PVC pipe or commercial port tubes. Easy to implement, predictable behavior, widely understood. Flared versions are available commercially.

**Slot ports**: Rectangular ports formed by the cabinet walls. One or more walls of the port are cabinet walls, which saves space and allows longer port lengths. Slot ports can have a larger cross-sectional area for a given internal volume, reducing turbulence risk. However, they can be harder to model precisely and the sharp edges can increase turbulence.

### 2.5 Advantages of Ported Enclosures

- **More bass output** than sealed for a given box size (typically 3-6 dB more at the tuning frequency)
- **Lower -3 dB point** for the same box volume and driver
- **Reduced excursion at the tuning frequency** extends maximum SPL capability
- **Higher efficiency** in the bass range

### 2.6 Disadvantages of Ported Enclosures

- **Steep rolloff below tuning**: Below Fb, the response falls at 24 dB/octave (4th order) instead of sealed's 12 dB/octave. The bass drops off a cliff.
- **Uncontrolled excursion below tuning**: Below Fb, the port stops loading the cone, and excursion increases rapidly. The driver can easily exceed Xmax below the tuning frequency, causing distortion or damage. A subsonic filter (high-pass filter below Fb) is recommended.
- **Port noise**: Turbulence in the port can be audible, especially at high levels.
- **Group delay**: Ported systems have more group delay than sealed, meaning bass is slightly "late" relative to the direct-radiator's output. Whether this is audible is debated, but it is measurable.
- **More complex to build**: The port must be precisely tuned, the box must be larger, and port placement within the box matters.
- **Transient response**: The resonant nature of the port means the system rings slightly at the tuning frequency. Transient response is not as tight as sealed.

### 2.7 Choosing the Tuning Frequency

Typical approach:
- For maximum bass extension: tune Fb near or slightly below Fs
- For maximum output (boom box effect): tune Fb slightly above Fs
- For balanced performance: tune Fb about 0.9 * Fs

WinISD lets you compare these trade-offs interactively. The simulation chapter (Chapter 15) will walk through this process.

> **What happens if the port is too small?**
>
> At high listening levels, the air velocity in the port becomes turbulent. You hear a "chuffing" or "whooshing" noise on bass-heavy content. The port effectively becomes obstructed, and the system starts to behave like a sealed box -- losing the bass extension you designed for. Worse, the increased back-pressure can cause the driver's excursion to increase, risking mechanical damage. This is one of the most common problems in budget ported speakers and subwoofers.

---

## 3. Transmission Line Enclosures

The transmission line (TL) is an elegant enclosure design that absorbs the back wave rather than trapping it (sealed) or resonating it (ported).

### 3.1 How It Works

A transmission line is a long, tapered, stuffed tube behind the driver. The line is typically one-quarter wavelength long at the driver's resonant frequency. The stuffing material (typically loosely packed long-fiber wool or polyester stuffing) absorbs the back wave progressively along the line's length.

At the open end of the line (the terminus), some low-frequency energy escapes and adds to the front-wave output -- similar to a port. But because the line is heavily damped, this output is spread over a broader frequency range than a port's sharp resonance, resulting in smoother bass extension.

### 3.2 Characteristics

- **Excellent bass quality**: Deep, smooth, well-damped bass extension. Many enthusiasts consider TL bass the "best" bass quality of any enclosure type.
- **Large cabinets**: The quarter-wave line for a 40 Hz driver is about 2.15 meters (7 feet) long. Even folded within the cabinet, this requires a large enclosure. TL speakers are typically tall and deep.
- **Stuffing is critical**: The type, density, and placement of the stuffing material dramatically affect the performance. Too much stuffing kills the output. Too little and the line resonates like a pipe organ. Getting it right requires experimentation or careful simulation.
- **Design complexity**: TL design is more complex to model than sealed or ported. Martin King's Quarter-Wave TL software is the primary simulation tool.

### 3.3 When to Use Transmission Lines

TL enclosures are best for:
- Audiophile home speakers where bass quality is paramount
- Situations where deep bass extension is desired without the transient response compromise of ported designs
- Projects where cabinet size is not a constraint (floor-standing speakers)
- Enthusiasts who enjoy the design challenge

TL enclosures are not practical for:
- Bookshelf speakers (too much volume needed)
- PA speakers (too large, too fragile)
- Subwoofers (ported or sealed are more practical at extreme low frequencies)

---

## 4. Horn-Loaded Enclosures

Horn loading uses the back wave to drive a horn, dramatically increasing efficiency. We introduced the concept in Chapter 11 with compression drivers; here we discuss horn-loaded enclosures for woofers.

### 4.1 How It Works

The horn acts as an acoustic transformer. At the throat (small end, connected to the driver), the air pressure is high and the air velocity is low. At the mouth (large end, opening into the room), the pressure is lower but the air volume velocity is higher. This impedance transformation matches the driver more efficiently to the room air, extracting more acoustic power from a given electrical input.

### 4.2 Horn Types

**Front-loaded horn**: The front of the driver fires into a horn. The driver's output is amplified by the horn's impedance-matching effect. The back wave is usually handled by a sealed or ported enclosure behind the driver.

**Back-loaded horn**: The back of the driver fires into a horn, while the front radiates directly. The horn augments the bass by adding the back wave's energy at low frequencies. The direct radiation from the front handles midrange and treble. This is popular for full-range drivers.

**Folded horn**: The horn path is folded within the cabinet to fit a long horn into a manageable size. The sound path zigzags through internal chambers. Folded horns can be very efficient for bass but are complex to design and build.

### 4.3 Horn Dimensions

A horn's low-frequency limit is determined by the mouth area -- the mouth must be comparable to the wavelength at the lowest desired frequency. For 50 Hz bass, the wavelength is about 6.9 meters, and the horn mouth needs an area of roughly 1-2 square meters. This is why bass horns are enormous.

The horn's flare rate (how quickly the cross-section expands from throat to mouth) determines the horn's loading profile. Common flare profiles:
- **Exponential**: Constant percentage area increase per unit length. Classic horn shape, smooth response.
- **Conical**: Linear area increase. Simpler to build but less efficient loading.
- **Tractrix**: A profile that provides better loading at the mouth termination, reducing reflections.
- **Hyperbolic**: A family of curves that includes exponential as a special case.

### 4.4 When to Use Horn-Loaded Enclosures

- **PA systems**: The efficiency gain is critical when you need 120+ dB SPL. Horn-loaded bass bins and horn-loaded compression drivers for mids and highs are standard in professional PA.
- **High-efficiency home audio**: Enthusiasts who use low-power tube amplifiers (2-20 watts) often use horn speakers because the high efficiency means they can reach satisfying listening levels with minimal power.
- **Cinema sound**: THX-certified cinema systems use horn-loaded designs for controlled directivity and high output.

Horn-loaded enclosures are NOT suitable when:
- Size is constrained (horns are large)
- Flat frequency response is the priority (horns can have coloration from resonances in the horn body)
- Budget is tight (horn cabinets are complex to build)

---

## 5. Band-Pass Enclosures

Band-pass enclosures are specialized designs primarily used for subwoofers.

### 5.1 How They Work

In a band-pass enclosure, the driver fires into a sealed chamber on one side and a ported chamber on the other. The driver is completely enclosed -- the only output to the room is through the port(s). This creates a band-pass filter behavior: the output is naturally limited to a specific frequency band, rolling off both above and below.

### 5.2 Types

**4th-order band-pass**: Sealed chamber on one side, ported on the other. One port. The output is naturally band-passed with 12 dB/octave slopes on each side.

**6th-order band-pass**: Ported chambers on both sides. Two sets of ports. Wider bandwidth and potentially higher output, but more complex and harder to tune.

### 5.3 When to Use Band-Pass

Band-pass enclosures are used almost exclusively for subwoofers where:
- The natural band-pass behavior eliminates the need for an external low-pass filter
- Maximum output in a narrow frequency band is desired (e.g., car audio subwoofers)
- The driver does not need to be visible (it is fully enclosed)

For general speaker building, band-pass is rarely the best choice. Sealed and ported designs are more versatile and easier to design correctly.

---

## 6. When to Use Each Enclosure Type

Here is the decision framework:

| Enclosure | Best For | Typical Qtc/Driver Qts | Size | Bass Quality | Efficiency |
|-----------|----------|----------------------|------|-------------|------------|
| Sealed | Accuracy, compact builds, subs with room gain | Qts 0.3-0.5 | Small-Medium | Excellent | Lower |
| Ported | Bass extension, efficiency, PA | Qts 0.3-0.5 | Medium-Large | Good | Higher |
| Transmission Line | Audiophile bass quality | Qts 0.3-0.5 | Large | Superb | Moderate |
| Horn (back-loaded) | Full-range + bass augmentation | Qts 0.3-0.5 | Large | Good | High |
| Horn (front-loaded) | PA, high SPL | Varies | Very Large | Moderate | Very High |
| Band-pass | Subwoofers | Qts 0.3-0.5 | Medium | Moderate | Higher |

**Quick decision guide for home audio:**
- Want tight, accurate bass in a compact bookshelf? → Sealed
- Want deeper bass extension in a floor-standing tower? → Ported
- Want the best possible bass quality and have room for a large cabinet? → Transmission line
- Building a subwoofer? → Sealed (music) or ported (home theater/maximum output)
- Building a PA speaker? → Ported woofer + horn-loaded compression driver for mids/highs

---

## 7. Baffle Diffraction

### 7.1 The Physics

When sound waves traveling along the surface of the speaker baffle reach the edge, part of the wave continues forward and part diffracts (bends) around the edge. This diffracted wave creates a secondary source that interferes with the primary wave. The interference creates ripples in the frequency response -- peaks and dips that depend on the distance from the driver to the edge.

### 7.2 The Effect

The diffraction ripples are typically plus or minus 1-3 dB at frequencies where the path length from the driver to the edge equals the wavelength. For a driver mounted asymmetrically on a typical cabinet, the ripples span roughly 500 Hz to 3,000 Hz -- right in the middle of the audio band where the ear is most sensitive.

### 7.3 Mitigation Strategies

**Edge rounding or chamfering**: Rounding the cabinet edges with a large radius (at least 20mm, ideally 40mm+) significantly reduces diffraction artifacts. The smooth transition eliminates the hard edge that causes the secondary radiation. This is the single most effective mitigation and costs almost nothing.

**Edge foam or felt**: Applying a strip of acoustic foam or felt around the edge of the baffle, near the drivers, absorbs the surface wave before it reaches the edge. Effective but aesthetically challenging.

**Asymmetric driver placement**: Placing the driver off-center on the baffle ensures that the distances to each edge are all different. This spreads the diffraction notches across multiple frequencies instead of concentrating them at one frequency, making the ripple smaller at any given frequency. Most speaker designs place the tweeter slightly off-center horizontally.

**Flush mounting**: Mounting the driver so its front surface is flush with the baffle (rather than surface-mounted) reduces the diffraction from the driver flange.

**Narrow baffle**: A narrow baffle pushes the diffraction frequency higher, above the crossover region where it is less audible. This is one reason why many modern speakers have narrow front baffles.

VituixCAD models baffle diffraction, so you can simulate the effect of different baffle dimensions and driver placements before building.

---

## 8. Internal Bracing and Resonance Control

### 8.1 The Problem: Panel Resonances

A speaker cabinet's walls are large, flat panels, and every panel has resonant frequencies where it vibrates like a drum. When the driver excites these resonances (which it does, because it generates significant internal pressure), the panels radiate sound. This is audible as a boxy, "woody" coloration in the midrange.

Panel resonance is the reason a cheap speaker sounds "boxy" and a good speaker sounds "transparent." The cabinet should be acoustically inert -- it should not contribute any sound of its own.

### 8.2 Bracing Strategies

**Window bracing**: A framework of rectangular cutouts (like window frames) connects opposite walls, preventing them from vibrating independently. Very effective for large panels.

**Shelf bracing**: Horizontal shelves inside the cabinet that connect the front and back panels. These reduce the effective panel size by dividing large panels into smaller ones, which have higher resonant frequencies (and are therefore less excited by bass content).

**Dowel bracing**: Round dowels connecting opposing walls. Simple, easy to install, reasonably effective. Place them asymmetrically to avoid creating regular patterns that could create their own resonances.

**Matrix bracing**: A grid of interlocking braces that divides the interior into small cells. Extremely effective but reduces internal volume significantly and is complex to build.

**General principle**: The goal is to increase the resonant frequency of every panel above the audible range, or at least above the frequency range where the drivers generate significant internal pressure. Making panels smaller (by dividing them with braces) and stiffer (by coupling them to opposing walls) achieves this.

### 8.3 How Much Bracing Is Enough?

For a well-built bookshelf speaker with 18mm MDF walls:
- Front and back panels (the largest) should have at least one horizontal brace connecting them
- Side panels may benefit from one brace each if they are large
- The top and bottom are inherently smaller and stiffer (they are the narrowest dimension) and often do not need bracing

For a floor-standing tower:
- Much taller side panels need multiple braces
- Window-style bracing every 15-20 cm of height is a good practice
- The longer the panel, the lower its resonant frequency and the more audible the problem

---

## 9. Damping Materials

Internal damping material serves two purposes: it absorbs the driver's back wave (reducing internal reflections and standing waves) and it provides acoustic resistance that damps panel resonances.

### 9.1 Fiberglass (Mineral Wool)

**What it is**: Inorganic fiber insulation, commonly available as building insulation (e.g., Owens Corning 703 or 705 rigid fiberglass panels).

**Properties**: Excellent broadband absorption, especially at mid and high frequencies. Rigid fiberglass (703/705) is preferred over fluffy insulation because it maintains its position and density over time.

**Placement**: Line the inside walls of the cabinet with 1-2" thick panels. Leave the area immediately behind the woofer relatively clear to avoid loading the cone's back wave excessively.

**Caution**: Fiberglass is an irritant -- wear gloves, a mask, and eye protection when cutting and handling it. Encase it in a fabric bag (muslin or polyester) to prevent fibers from migrating into the driver.

### 9.2 Polyester Stuffing (Polyfill)

**What it is**: Fluffy polyester fiber stuffing, the same material used to stuff pillows and toys.

**Properties**: Moderate absorption, primarily at higher frequencies. Less effective than fiberglass at lower frequencies. However, polyfill has a unique property: it slows down the speed of sound inside the enclosure by about 10-15%, making the box "appear" acoustically larger than it physically is. This is useful in sealed enclosures where you want to slightly lower the system resonance without increasing box size.

**Placement**: Loosely fill the enclosure to a density of about 0.5-1 lb per cubic foot. Do not pack it tightly -- that reduces its effectiveness.

**Advantages**: Cheap, safe to handle, easy to work with. No irritation.

### 9.3 Acoustic Foam

**What it is**: Open-cell polyurethane foam, typically available in sheets of various thicknesses.

**Properties**: Good absorption at mid and high frequencies, less effective at low frequencies. Convenient to cut to shape and glue to cabinet walls.

**Placement**: Line the inside walls. Often used in combination with polyfill.

### 9.4 Long-Fiber Wool

**What it is**: Natural wool or synthetic long-fiber equivalents. Traditionally used in transmission line speakers.

**Properties**: Excellent damping, good at slowing air movement in a transmission line. The long fibers prevent the material from compacting over time.

### 9.5 General Guidelines

- **Sealed enclosures**: Moderate fill (polyfill at 0.5-1 lb/ft^3, or fiberglass on walls). The goal is to damp internal reflections and standing waves, and potentially to "enlarge" the effective volume slightly.
- **Ported enclosures**: Less fill than sealed. Over-damping a ported enclosure reduces the port's effectiveness. Line the walls with fiberglass or foam but keep the port and the area near the port clear. Do NOT block the port entrance with stuffing.
- **Transmission lines**: Specific damping profiles along the line length, typically denser near the driver and tapering to lighter damping near the terminus. This is critical and design-specific.

---

## 10. Construction Materials

### 10.1 MDF (Medium-Density Fiberboard)

**What it is**: Engineered wood product made from wood fibers bonded with resin. Dense, uniform, and smooth.

**Thickness**: 18mm (3/4") for most speaker cabinets. 25mm (1") for subwoofers and very large enclosures. 12mm (1/2") only for very small speakers or non-structural internal panels.

**Advantages**:
- Dense (about 750 kg/m^3) and acoustically "dead" -- does not ring when tapped
- Uniform -- no grain, no voids, no knots. CNC machines and router bits love it.
- Easy to work with hand tools and power tools
- Takes paint and vinyl wrap beautifully (smooth surface)
- Cheap and widely available

**Disadvantages**:
- Heavy (a medium-sized speaker cabinet weighs 10-20 kg)
- Swells and disintegrates when exposed to water (do not leave it in a damp garage)
- Produces very fine dust when cut that is unhealthy to breathe -- always use a dust mask
- Not structural on its own (screw-holding strength is mediocre; use glued joints)
- Cannot be left unfinished (looks bad and is not water-resistant)

### 10.2 Baltic Birch Plywood

**What it is**: Multi-layer birch veneer plywood, known for its consistent quality and void-free layers.

**Thickness**: 18mm (3/4") for most applications.

**Advantages**:
- Stronger than MDF for a given thickness
- Lighter than MDF (about 650 kg/m^3)
- Excellent screw-holding strength
- Beautiful exposed edges can be finished with lacquer for a premium look
- Water-resistant (compared to MDF)
- Can be CNC-routed or cut with standard woodworking tools

**Disadvantages**:
- More expensive than MDF (typically 2-3x)
- Slightly more "resonant" than MDF -- the layers can ring. May need more bracing or damping.
- Surface requires sanding for a smooth finish
- Not as uniformly dense as MDF

### 10.3 Other Materials

**Standard plywood**: Cheaper than Baltic birch but has voids in the inner layers. Acceptable for PA cabinets where durability matters more than acoustic perfection.

**Particle board (chipboard)**: Cheap but weak, crumbly edges, poor screw retention. Do not use it for speaker cabinets.

**Solid wood**: Resonant, inconsistent, splits, warps. Not recommended for speaker cabinets despite its aesthetic appeal. (If you want the wood look, use MDF with wood veneer.)

**Bamboo plywood**: Strong, stiff, attractive. A premium alternative to Baltic birch.

**Corian / solid surface**: Used in some high-end commercial speakers. Extremely dense and dead. Hard to work with.

### 10.4 Practical Recommendation

For most DIY builds:
- **MDF for anything that will be painted or vinyl-wrapped** (which is most home speakers)
- **Baltic birch for PA speakers** (stronger, lighter, better screw retention for hardware)
- **Baltic birch for visible natural-finish speakers** (beautiful edge grain)
- **18mm thickness for bookshelf and floor-standing speakers**
- **18mm with double-thick front baffle (36mm) for subwoofers** or large woofer cabinets

---

## 11. Cabinet Volume Calculations

### 11.1 Internal vs. External Dimensions

The volume that matters is the **net internal volume** -- the air space inside the box after accounting for wall thickness, bracing, and the volume displaced by components inside the box.

For a rectangular box with external dimensions W x H x D and wall thickness T:
```
Internal volume = (W - 2T) * (H - 2T) * (D - 2T)
```

For 18mm MDF:
```
Internal volume = (W - 36) * (H - 36) * (D - 36) mm
```

Convert to liters by dividing by 1,000,000 (mm^3 to liters).

### 11.2 Accounting for Internal Components

Subtract the volume occupied by:
- **Bracing**: Estimate the total volume of brace material. A typical shelf brace might displace 0.2-0.5 liters.
- **Driver displacement**: The driver motor structure (magnet, basket) protrudes into the box. Check the driver datasheet for "volume displacement." If not specified, estimate based on the motor depth and basket diameter. A typical 6.5" woofer might displace 0.3-0.5 liters.
- **Crossover components**: A crossover board with large inductors and capacitors might displace 0.2-0.3 liters.
- **Port volume (if ported)**: The port tube occupies internal volume. Subtract the volume of the port tube material.
- **Damping material**: Dense fiberglass displaces some air volume. Polyfill is mostly air and can be neglected.

### 11.3 Practical Example

Target: 10 liters net internal volume for a sealed bookshelf speaker.

Choose external dimensions: 200mm W x 320mm H x 250mm D (about 8" x 12.6" x 9.8")

With 18mm MDF:
```
Internal: (200-36) * (320-36) * (250-36) = 164 * 284 * 214 = 9,965,344 mm^3 = 9.97 liters
```

Subtract components:
- Brace: 0.2 liters
- Woofer displacement: 0.3 liters
- Crossover: 0.2 liters
- Total subtracted: 0.7 liters
- Net volume: 9.97 - 0.7 = 9.27 liters

That is close to our 10 liter target. We could adjust the depth to 255mm to add a bit more volume, or accept 9.3 liters and check in simulation that the response is still acceptable.

### 11.4 Golden Ratio Proportions

To minimize internal standing waves, avoid cubic or near-cubic box shapes. Internal dimensions with non-integer ratios work best. The "golden ratio" proportions of approximately 1 : 1.6 : 2.6 (width : height : depth) are a common recommendation. This distributes the internal standing wave modes more evenly across frequency, reducing the severity of any single mode.

For a bookshelf speaker, a common approach is:
- Width: determined by the woofer diameter (need at least woofer diameter + 25mm on each side for mounting flange and edge clearance)
- Height: 1.5-2x the width
- Depth: 1-1.5x the width

---

## 12. Chapter Summary and What Comes Next

In this chapter, you learned:

- Sealed enclosures trap the back wave and provide predictable, well-damped bass with a gentle 12 dB/octave rolloff
- Ported enclosures use a Helmholtz resonator (tuned port) for more bass output and deeper extension, but with steeper rolloff below tuning and port turbulence risks
- Transmission lines absorb the back wave for excellent bass quality but require large cabinets
- Horn-loaded enclosures provide dramatically higher efficiency for PA applications
- Band-pass enclosures are specialized subwoofer designs
- Baffle diffraction creates response ripples that can be mitigated with edge rounding and asymmetric driver placement
- Internal bracing prevents panel resonances that color the sound
- Damping materials (fiberglass, polyfill, foam) control internal reflections and standing waves
- MDF is the standard material for home speakers; Baltic birch plywood is preferred for PA and natural-finish builds
- Cabinet volume calculations must account for wall thickness, bracing, and component displacement

In Chapter 15, we will put all of these concepts into practice with simulation tools. You will learn to model enclosures in WinISD, design crossovers in VituixCAD, and measure real speakers with REW. The simulation tools are where theory becomes design.
