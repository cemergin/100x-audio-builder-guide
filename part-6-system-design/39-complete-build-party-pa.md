<!--
  CHAPTER: 39
  TITLE: Complete System Build: Party PA
  PART: 6 — System Design & Integration
  PREREQS: Chapter 38
  KEY_TOPICS: PA system design, SPL requirements, durability, portability, speaker protection, DSP limiters, TPA3255 Class D, horn-loaded PA speakers, subwoofer, powered vs passive PA, system power budget, cable management, quick setup, troubleshooting, complete signal path
  DIFFICULTY: Advanced
  UPDATED: 2026-04-10
-->

# Chapter 39: Complete System Build: Party PA

> **Part 6 — System Design & Integration** | Prerequisites: Chapter 38 | Difficulty: Advanced

Everything about this system is different from the home hi-fi we just built. That system was about nuance -- subtle imaging, controlled bass, the texture of a singer's breath. This system is about impact. Bass you feel in your chest. Volume that fills a field. A setup you can throw in a van, assemble in 30 minutes, run for 8 hours, and not worry about a drunk person stumbling into a speaker stand.

The engineering priorities invert completely. Fidelity matters, but SPL matters more. Low distortion matters, but driver protection matters more. Optimal frequency response matters, but the ability to survive rain, dust, and being loaded in and out of a vehicle every weekend matters more. If the home hi-fi system is a precision instrument, the party PA system is a power tool -- it needs to be reliable, durable, and effective, not delicate.

This chapter traces the complete signal path for a party PA system built from components in this guide. It is designed for outdoor events of 50-100 people, indoor parties in medium-sized venues, and the occasional impromptu gig in a parking lot. We will cover the full chain from phone to dancefloor, with special attention to the things that make PA different from hi-fi: protection, power budgeting, quick deployment, and troubleshooting under pressure.

### In This Chapter
- Different requirements: SPL, durability, portability, weather
- The complete PA signal chain
- Powered vs passive PA: our approach
- System power budget: watts per person
- DSP system protection: high-pass filters, limiters, compressors
- Cable management and connectors
- Quick setup checklist
- Troubleshooting common PA problems
- The complete signal path: phone to dancefloor

### Related Chapters
- [Ch 5: How Effects Shape Sound](../part-1-guitar-pedals/05-how-effects-shape-sound.md) — guitar through the PA via DI box
- [Ch 17: Build: Towers, PA, and Subwoofer](../part-2-speakers/17-build-towers-pa-subwoofer.md) — the PA speakers and subwoofers
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) — the TPA3255 Class D amps
- [Ch 28: Build: Pi Streamer with Physical Controls](../part-4-digital-audio/28-build-pi-streamer.md) — the source
- [Ch 29: Build: Active Crossover and Room Correction](../part-4-digital-audio/29-build-active-crossover-room-correction.md) — crossover, limiter, and system protection
- [Ch 33: Professional Finishing and Aesthetics](../part-5-pcb-professional/33-professional-finishing-aesthetics.md) — rugged enclosure design
- [Ch 34: Testing, Quality Control, and Documentation](../part-5-pcb-professional/34-testing-qc-documentation.md) — pre-event testing protocol
- [Ch 35: Matching Amps to Speakers](35-matching-amps-to-speakers.md) — power and impedance calculations for PA
- [Ch 36: Room Acoustics and Treatment](36-room-acoustics-treatment.md) — venue acoustics
- [Ch 37: System Tuning and Measurement](37-system-tuning-measurement.md) — system measurement and EQ
- [Ch 38: Complete Build: Home Hi-Fi](38-complete-build-home-hifi.md) — the home system (different requirements, same engineering)

---

## 1. PA Requirements: How They Differ From Hi-Fi

### 1.1 SPL Over Fidelity

The primary job of a PA system is to be **loud enough**. Not loud for the sake of loud, but loud enough that everyone in the coverage area can hear the music clearly over the ambient noise, conversation, and general chaos of a party.

Target SPL levels:
- **Small indoor party (20-30 people, living room):** 95-100 dB at the furthest listener. Your home hi-fi system can handle this -- you do not need a PA.
- **Medium indoor party (50-80 people, community hall or warehouse):** 100-105 dB at the back of the room.
- **Outdoor party (50-100 people, garden or field):** 105-110 dB at 10 meters from the speakers.
- **Large outdoor event (200+ people):** 110+ dB at 20+ meters. Beyond the scope of our DIY system -- this is professional rental territory.

For reference, 100 dB is very loud -- shouting conversation level. 110 dB is approaching the pain threshold for some people. These numbers are for peak levels; average SPL during a party is typically 6-10 dB lower.

### 1.2 Durability

A home hi-fi system sits on a shelf and never moves. A PA system:
- Gets loaded into a car or van (bumps, vibration, stacking)
- Gets set up outdoors (rain, dust, sun, humidity)
- Has drunk people dancing near it (accidental bumps, spilled drinks)
- Gets operated by people who are not audiophiles (volume knobs get cranked to maximum)
- Runs at high power for hours (thermal stress on amplifiers and voice coils)

Every component must survive this. Speakers need rugged cabinets with corner protectors, recessed handles, and protective grilles. Amplifiers need ventilation and thermal protection. Cables need strain relief and locking connectors. Everything that can be dropped will be dropped.

### 1.3 Portability

You are not a rental company with a truck and a crew. You are one or two people with a hatchback. The entire PA system needs to:
- Fit in a regular car (not an SUV or van, ideally)
- Be carried by one or two people (no single item over 25 kg)
- Set up in 30 minutes or less
- Break down in 20 minutes

This constrains the system size. Two 15" tops on stands plus two 15" subs is a serious PA rig, but each sub weighs 30-40 kg. Our system uses smaller, lighter components that are still capable of respectable output.

### 1.4 Speaker Protection

In a home hi-fi system, you control the volume knob. In a PA system, someone will always want it louder. Without protection, "louder" eventually means:
- Amplifier clipping (the amp runs out of voltage swing and the peaks get sheared off, sending square-wave-like signals full of harmonics to the tweeter)
- Woofer over-excursion (the woofer cone moves beyond its safe range, bottoming out or tearing the surround)
- Thermal failure (continuous high power heats the voice coil until the adhesive fails or the wire melts)

DSP-based limiters and high-pass filters prevent all three of these. They are not optional for a PA system. They are as essential as seat belts in a car.

---

## 2. The System Architecture

### 2.1 The Complete Signal Chain

```
Phone / Laptop (Bluetooth or AUX cable)
  │
  ▼
Raspberry Pi Streamer (Ch 28)
  │ — receives audio via Bluetooth (aptX), AirPlay, or 3.5mm AUX input
  │ — optional: direct AUX input from phone to USB audio interface, bypassing Pi
  │
  ▼
CamillaDSP on the Pi (Ch 29)
  │ — crossover: 2-way split (subs + tops)
  │ — high-pass filter on sub channel (protects subs below port tuning)
  │ — limiter on each channel (prevents amp clipping and driver damage)
  │ — EQ: venue-appropriate curve (bass boost, mid scoop, etc.)
  │ — output: 4 channels (left sub, right sub, left top, right top)
  │
  ▼
USB Audio Interface (Behringer UMC404HD or similar)
  │ — 4 balanced outputs
  │
  ▼
TPA3255 Class D Amps × 2 (Ch 23)
  │ — Amp 1: left sub + right sub (175W/ch into 4Ω)
  │ — Amp 2: left top + right top (175W/ch into 4Ω)
  │
  ▼
PA Speakers (Ch 17):
  │ — 2× Subwoofer (15" woofer, ported, 4Ω, 95 dB/W/m)
  │ — 2× Top (12" woofer + horn-loaded compression driver, 4Ω, 97 dB/W/m)
  │
  ▼
The Dancefloor
```

### 2.2 Why This Configuration

**Two subs + two tops** is the sweet spot for a portable DIY PA. The subs handle everything below 100-120 Hz, where you need maximum cone area and excursion for bass impact. The tops handle 100 Hz and up, using a horn-loaded compression driver for efficient, controlled high-frequency coverage.

**Separate sub and top amps** allow independent level control and DSP processing per speaker type. You can crank the bass for a dance party or reduce it for a speech event.

**The Pi as DSP processor** gives you crossover, limiter, and EQ in a $50 computer that fits in your pocket. No need for a separate rack-mount DSP unit.

### 2.3 Alternative: Direct Input (No Pi)

For the simplest possible setup, skip the Pi entirely:

```
Phone (3.5mm AUX output)
  │
  ▼
Miniature analog crossover (active, op-amp based)
  │ — splits signal into sub and top
  │
  ▼
TPA3255 Amps
  │
  ▼
Speakers
```

This works but loses all DSP benefits: no limiter, no EQ, no room correction, no precise crossover. It is a backup for when the Pi fails or for ultra-quick setups where you do not need DSP. Build a simple analog 2-way active crossover on a small PCB (an op-amp Sallen-Key filter, covered conceptually in Chapter 13) as an emergency fallback.

---

## 3. Powered vs Passive PA

### 3.1 The Two Approaches

**Powered (active) PA speakers** have the amplifier built into the speaker cabinet. You send them line-level signal, and they handle amplification internally. Examples: QSC K-series, EV ZLX, JBL EON.

**Passive PA speakers** are just drivers in a cabinet. You provide external amplification. This is what we built in this guide.

### 3.2 Why We Built Passive

- **Reparability**: If an amp dies, you swap the amp board. You do not send the entire 20 kg speaker cabinet to a service center.
- **Flexibility**: You can re-use the amps with different speakers, or upgrade the amps independently.
- **Thermal management**: The amp runs in its own ventilated rack case, not trapped inside a sealed speaker cabinet where the voice coil is also generating heat.
- **Learning**: You understand every component because you built every component.

### 3.3 When Powered Makes More Sense

- **Convenience**: One cable per speaker (power + line-level signal, or just power if using Bluetooth input). Fewer boxes to carry.
- **Integrated protection**: Commercial powered speakers have built-in DSP limiters optimized for their specific driver. This is hard to beat.
- **Resale value**: Powered PA speakers hold their value and are easy to sell if you move on.

If you decide to build powered PA speakers, the approach is straightforward: mount a TPA3255 amp board inside the speaker cabinet with the power supply, add a small DSP board (miniDSP or an ADAU1701 module), and wire the line-level input to the DSP, DSP to the amp, and amp to the drivers. The enclosure design from Chapter 33 applies -- you just need to add ventilation and a recessed amp module plate on the rear of the cabinet.

---

## 4. System Power Budget

### 4.1 How Many Watts for How Many People

The rule of thumb for PA power:
- **Outdoor events**: 4-6 watts per person (continuous power)
- **Indoor events**: 2-3 watts per person (the room contains the sound)

For 80 people outdoors:

```
Continuous power needed = 80 × 5 = 400 watts
Peak power (2× continuous for headroom) = 800 watts
```

Our system delivers:
- Sub amps: 2 × 175W = 350W continuous to subs
- Top amps: 2 × 175W = 350W continuous to tops
- **Total: 700W continuous, ~1,200W peak** (Class D amps can deliver brief peaks above their rated continuous power)

This covers 80 people outdoors with some headroom. For 100+ people, you would want more sub power (the limiting factor is always bass). Adding a second pair of subs (and a third TPA3255 board) would extend coverage to 120-150 people.

### 4.2 The Bass Budget

Bass is disproportionately power-hungry. At a dance party, 60-70% of the amplifier power goes to the subwoofers. The tops (especially with horn-loaded compression drivers at 97 dB sensitivity) need relatively little power to produce deafening midrange and treble.

A common mistake is equal power to subs and tops. If you have 175W available per speaker:
- Subs at 95 dB/W/m: 175W produces 117.4 dB at 1 meter
- Tops at 97 dB/W/m: 175W produces 119.4 dB at 1 meter

The tops will seem louder than the subs. To balance, either:
- Pad the top amps down 3-4 dB in CamillaDSP
- Use a more powerful amp for the subs
- Add more sub cabinets (each additional sub adds ~3 dB)

In our system, CamillaDSP handles this by attenuating the top output by 3-4 dB relative to the sub output. The tops still have headroom, and the bass/treble balance is correct.

### 4.3 Thermal Considerations

At a party, the system runs at high average power for hours. Heat management matters:

**TPA3255 Class D amps**: At 90% efficiency, 175W output means about 19W dissipated as heat per channel. Four channels total = about 78W of heat. This is manageable with the heat sinks included on most TPA3255 boards, but ensure the amp enclosure has ventilation (mesh panels, fan if needed). Do not stack amps in an enclosed rack without airflow.

**Speaker voice coils**: Continuous high power heats voice coils. A 300W-rated woofer can handle 300W of music (which has peaks and valleys), but 300W of continuous bass at one frequency (like a test tone) will burn the coil. Real music has a crest factor of 6-12 dB, meaning average power is 1/4 to 1/16 of peak power. The DSP limiter (section 5) keeps average power in check.

> **What happens if... you run the PA at maximum volume for 6 hours straight on a hot summer day?** The amplifier's heatsink temperature rises. Most TPA3255 boards have thermal protection that reduces output when the die temperature exceeds ~140°C. In an enclosed case in direct sun, this can happen. Solutions: shade the amp rack, use a fan, or build the amps into a ventilated flight case with a 12V computer fan. The speakers are more resilient -- voice coil temperatures stabilize as heat is conducted to the motor structure and dissipated. But sustained maximum power in extreme heat does shorten driver life. Monitor for any change in sound quality (compression, loss of treble) -- these are early signs of thermal stress.

---

## 5. DSP System Protection

This section is the most important in the chapter. Without DSP protection, one moment of carelessness can destroy drivers that took you weeks to build. With it, the system is nearly indestructible.

### 5.1 High-Pass Filter on Subwoofers

Every ported subwoofer has a tuning frequency (the frequency at which the port resonates). Below this frequency, the port stops providing acoustic output, and the driver's cone excursion increases rapidly -- it is essentially unloaded. If you send significant power below the port tuning frequency, the cone exceeds its maximum excursion (Xmax) and the driver is mechanically damaged.

**The fix**: A high-pass filter on the sub channel, set at or just above the port tuning frequency.

If your sub is tuned to 35 Hz:
- Set a Butterworth or Linkwitz-Riley high-pass at 35 Hz, 4th order (24 dB/octave)
- This blocks content below 35 Hz that would cause over-excursion
- The response is down 6 dB at 35 Hz, which matches the port's natural roll-off
- Below 25 Hz, the filter provides 24 dB of attenuation, protecting the driver from infrasonic content (wind noise, mic pops, subsonic rumble from turntables)

In CamillaDSP, this is a single biquad filter per sub channel.

### 5.2 Limiter on Each Channel

A limiter is a compressor with a very high ratio (10:1 or higher) and a fast attack. It catches peaks that would cause clipping or driver damage and reduces them to a safe level.

**Setting the limiter threshold:**

Your TPA3255 clips at its rated output (175W into 4 ohms). The voltage at which it clips:

```
V_clip = sqrt(P × R) = sqrt(175 × 4) = 26.5 Vrms
```

The DAC/interface output that drives the amp to this point depends on the amp's gain. For a typical TPA3255 board with 23 dB gain (voltage gain of ~14x):

```
V_input_clip = 26.5 / 14 = 1.89 Vrms
```

Set the limiter threshold in CamillaDSP so the output never exceeds about 1.7 Vrms -- about 1 dB below clipping. This gives a safety margin.

**Limiter parameters:**
- **Threshold**: -2 dBFS (just below the maximum output level)
- **Attack time**: 1-5 ms (fast enough to catch transients before they clip the amp)
- **Release time**: 50-200 ms (slow enough to avoid audible pumping)
- **Ratio**: 10:1 or higher (effectively a brick-wall limiter)

Apply a separate limiter on each output channel (left sub, right sub, left top, right top). The sub limiter threshold may be lower than the top limiter threshold if you want to run the subs harder.

### 5.3 Compressor for Overall Dynamics

A compressor before the limiter (on the master bus) keeps the overall dynamic range in check. At a party, you want consistent loudness -- quiet passages should not disappear into the noise of conversation, and loud passages should not blow people's ears off.

**Compressor parameters:**
- **Threshold**: -12 dBFS (catches anything above moderate level)
- **Ratio**: 3:1 to 4:1 (moderate compression)
- **Attack time**: 10-30 ms (lets transients through for punch, then compresses)
- **Release time**: 100-300 ms (smooth, unobtrusive gain reduction)
- **Makeup gain**: +4 to +6 dB (compensates for the average level reduction from compression)

The compressor makes the PA sound louder at the same peak power because the average level is higher. This is the same loudness trick used in broadcast radio and music mastering. For a party, it is exactly what you want.

### 5.4 The Protection Chain Order

In CamillaDSP, the processing order matters:

```
Input → EQ → Compressor → Crossover → Per-channel Limiter → Output
```

The EQ shapes the tone first. The compressor tames overall dynamics. The crossover splits to subs and tops. The per-channel limiter is the last line of defense before the amp, ensuring no channel ever exceeds its safe output.

### 5.5 Specific Protection Settings for Our System

Here is a complete CamillaDSP protection configuration for the Chapter 39 PA system. These are the starting-point values -- adjust based on your specific drivers and listening tests.

**Sub channel protection:**

| Parameter | Value | Why |
|-----------|-------|-----|
| High-pass filter frequency | 35 Hz | Matches sub port tuning frequency |
| High-pass filter type | Linkwitz-Riley 4th order (24 dB/oct) | Steep enough to protect below tuning |
| Limiter threshold | -2 dBFS | 1 dB below amp clipping point |
| Limiter attack | 3 ms | Fast enough to catch kick drum transients |
| Limiter release | 100 ms | Slow enough to avoid audible pumping on bass |
| Max continuous output | ~170W into 4Ω | Keeps below thermal limit of voice coil |

**Top channel protection:**

| Parameter | Value | Why |
|-----------|-------|-----|
| High-pass filter frequency | 100 Hz | Below crossover (120 Hz) for smooth transition |
| High-pass filter type | Linkwitz-Riley 4th order | Matches sub low-pass for flat sum |
| Limiter threshold | -4 dBFS | Lower than subs because compression driver is more fragile |
| Limiter attack | 1 ms | Compression drivers are fast and fragile -- protect quickly |
| Limiter release | 50 ms | Faster release OK for mid/high content |
| Tweeter protection HF limiter | -6 dBFS above 5 kHz | Extra headroom reduction above 5 kHz where the compression driver diaphragm is most vulnerable |

**Master bus compressor:**

| Parameter | Value |
|-----------|-------|
| Threshold | -12 dBFS |
| Ratio | 3:1 |
| Attack | 20 ms |
| Release | 200 ms |
| Makeup gain | +4 dB |
| Knee | Soft (if available in CamillaDSP) |

These settings prioritize driver safety over maximum volume. You can increase the limiter thresholds by 1-2 dB for more headroom if your drivers can handle it, but never exceed 0 dBFS on any channel -- that is the amp's clipping point, and clipped signals destroy tweeters.

### 5.6 Weather Protection

If there is any chance of rain -- and outdoor events always have some chance -- plan for it:

**For speakers:**
- Cover the tops with heavy-duty plastic bags or purpose-built speaker rain covers. A 33-gallon trash bag fits over most 12" tops on stands. Cut a small hole for the cable entry, secure with gaffer tape.
- Subs on the ground are more vulnerable to puddles. Raise them off the ground on pallets, plywood sheets, or even upside-down plastic bins. 2 inches of elevation is enough to survive a moderate rain.
- If rain is heavy and sustained, stop the event and protect the equipment. No party is worth a $500 set of blown drivers from water damage.

**For electronics:**
- The amp rack and Pi must stay dry. Position them under a canopy, tent, or at minimum under a tarp. Even light drizzle on a hot amp heatsink can cause thermal shock that cracks solder joints.
- Cover the amp rack with a plastic bag or tarp if rain starts unexpectedly. The cooling fan (if present) can pull moisture into the enclosure, so turn it off during rain if possible.
- Disconnect the USB audio interface before covering -- trapped moisture in a USB connector can short the data lines.

**For cables:**
- SpeakON and XLR connectors are designed for outdoor use and tolerate moisture well. Keep the mating surfaces clean and dry, but do not panic if they get damp.
- RCA connectors (if you used them anywhere in the signal chain) are NOT weather-resistant. Moisture on an RCA ground causes corrosion and intermittent contact. This is another reason to use balanced connections throughout the PA system.

> **What happens if... the limiter is set too aggressively?** An over-aggressive limiter (threshold too low, ratio too high) causes audible "pumping" -- the volume ducks noticeably on every kick drum hit and then recovers. The music sounds like it is breathing. Raise the threshold until the limiter only activates on the loudest peaks. If the limiter is constantly active, you are trying to push more volume than the system can handle. Either turn down the master level or accept that you need more speakers/amps.

---

## 6. Cable Management and Connectors

### 6.1 SpeakON Connectors

For speaker-level connections in a PA, RCA and binding posts are not acceptable. You need **speakON connectors** (made by Neutrik):

- **Locking**: They twist-lock into the socket. They cannot be accidentally pulled out.
- **High current rated**: speakON NL2 (2-pole) is rated for 40A. NL4 (4-pole) is rated for 40A per pair. Far more than any audio signal requires.
- **No exposed metal**: Unlike banana plugs or bare wire, you cannot short a speakON connector by touching the contacts together. This matters when you are handling cables in the dark behind a PA stack.
- **Rugged**: Designed for road use. They survive being stepped on, thrown in a bin, and plugged/unplugged hundreds of times.

Install speakON sockets on your PA speaker cabinets (replacing or supplementing the binding posts from the original build) and on your amp rack output panel. Wire speakON cables with 12 AWG speaker wire for runs up to 15 meters.

**Wiring convention:**
- Pin 1+: positive (hot)
- Pin 1-: negative (return)
- For bi-amp cabinets using NL4: pins 1+/1- for woofer, pins 2+/2- for tweeter

### 6.2 Cable Gauge for PA

PA cable runs are longer than home hi-fi (10-20 meters from amp to speaker is common). Use heavier gauge:

| Run Length | Minimum AWG for 4Ω Speaker | Minimum AWG for 8Ω Speaker |
|-----------|---------------------------|---------------------------|
| 5 m | 14 AWG | 16 AWG |
| 10 m | 12 AWG | 14 AWG |
| 15 m | 12 AWG | 12 AWG |
| 20 m | 10 AWG | 12 AWG |

The rule is the same as home hi-fi: cable resistance should be less than 5% of speaker impedance. But longer runs and lower impedance speakers demand thicker cable.

### 6.3 Signal Cables

Between the USB audio interface and the amps, use balanced cables (XLR or TRS). For PA applications with cable runs that may pass near power cables, dimmers, or lighting equipment, balanced connections reject the noise that unbalanced cables pick up.

Use quality XLR or TRS cables with metal-shell connectors (Neutrik NC3XX or equivalent). Label every cable with both ends' destinations. In a PA setup, you will have 4+ signal cables and 4+ speaker cables. Mislabeling means the wrong driver gets the wrong signal, and you discover this when you play music and the subwoofer tries to reproduce cymbals.

### 6.4 The Cable Bag

Bring spares. For every cable in the system, bring one spare. The cable that fails will be the one you did not bring a backup for. Also bring:

- Electrical tape
- A multimeter (for checking continuity on a suspect cable)
- A few extra speakON and XLR connectors plus a soldering iron (for field repair)
- A DI box (for connecting a guitar, keyboard, or other instrument with 1/4" output to the PA system's balanced input)
- 3.5mm to RCA adaptor (for connecting a phone directly to the amp if the Pi fails)
- A short XLR-to-3.5mm cable (for connecting a phone to the balanced interface)
- Gaffer tape (for taping cables to the floor so people do not trip)

---

## 7. Quick Setup Checklist

This is the field deployment procedure. Print it. Laminate it. Keep it in the PA case.

### 7.0 Complete Packing and Transport List

Before you leave the house, verify everything is in the vehicle. Print this list and check it off:

**Speakers:**
- [ ] 2x subwoofer cabinets
- [ ] 2x top speaker cabinets
- [ ] 2x tripod speaker stands with 35mm pole adapters
- [ ] 2x speaker stand carry bag (optional but protects stands)

**Amplification and DSP:**
- [ ] Amp rack case containing: 2x TPA3255 amp boards, power supplies, cooling fan
- [ ] Raspberry Pi in protective case with pre-loaded CamillaDSP configuration
- [ ] Pi power supply (5V 3A USB-C)
- [ ] USB audio interface (Behringer UMC404HD or similar)
- [ ] USB cable (Pi to audio interface)

**Cables:**
- [ ] 4x speaker cables with speakON connectors (2 for subs, 2 for tops) — label both ends
- [ ] 4x balanced signal cables (XLR or TRS) from interface to amps — label both ends
- [ ] 1x spare speaker cable
- [ ] 1x spare signal cable
- [ ] 1x 3.5mm to RCA adapter (phone backup input)
- [ ] 1x 3.5mm to XLR cable (phone to interface backup)

**Power:**
- [ ] 1x heavy-duty power strip (15A rated, surge protected) with at least 6 outlets
- [ ] 1x extension cord (12 AWG, at least 25 feet / 8 meters)
- [ ] 1x spare extension cord
- [ ] 1x circuit tester plug (verifies outlet is wired correctly and grounded — $5 from any hardware store)

**Tools and spares:**
- [ ] Multimeter
- [ ] Gaffer tape (1 roll minimum)
- [ ] Electrical tape
- [ ] Cable ties (various sizes)
- [ ] Small toolkit: screwdriver, pliers, wire strippers, soldering iron + solder (for field repair)
- [ ] Spare speakON and XLR connectors (2 each)
- [ ] Spare fuses for amp rack
- [ ] DI box (for guitar or keyboard input)
- [ ] Flashlight or headlamp (you will be behind the speaker stack in the dark)

**Environment protection:**
- [ ] Tarps or plastic sheeting (2x) — for speaker covers if rain threatens
- [ ] Bungee cords or ratchet straps — for securing speakers on stands in wind
- [ ] Plastic bags — for covering amp rack and Pi in case of unexpected rain

**Loading the vehicle:**
Subs go in first (heaviest items on the bottom, against the back seat or rear wall). Tops next, on their sides with the horn facing up. Speaker stands in a bag or bundled with velcro straps. Amp rack and cables last (first out). Nothing should be loose — a 20 kg sub sliding forward during a hard brake will damage anything in its path.

### 7.0.1 Outdoor Event Power Requirements

For an outdoor event without permanent power, you need to plan your electrical load:

| Component | Power Draw |
|-----------|-----------|
| TPA3255 amp board #1 (subs, full power) | ~220W from wall (175W output / 0.90 efficiency + standby) |
| TPA3255 amp board #2 (tops, moderate power) | ~150W from wall |
| Raspberry Pi + USB interface | ~15W |
| Cooling fan (if used) | ~5W |
| Phone charging | ~10W |
| **Total** | **~400W continuous, ~600W peak** |

A standard US 15A / 120V outlet provides 1,800W. You are well within capacity even at full power. A single heavy-duty extension cord (12 AWG, 50 feet) handles this load without significant voltage drop.

**Generator sizing:** If using a portable generator, choose one rated for at least 2,000W continuous (to handle startup inrush and leave headroom). A Honda EU2200i or equivalent inverter generator ($1,000-1,200) is the gold standard -- it produces clean sine-wave power (important for audio electronics, which can buzz or hum on cheap generators that produce modified sine wave output), runs quietly (48-57 dBA at rated load), and runs for 8+ hours on a single tank at 25% load.

Never use a cheap contractor generator ($200-400 "open frame" type) for audio. They produce dirty power with voltage spikes and frequency instability that can damage your amp's power supply and inject audible buzz into the audio.

**Battery power:** For truly remote locations, a portable power station (EcoFlow Delta 2, Bluetti AC200P, or similar) with 1-2 kWh capacity can run the system for 2-4 hours at moderate levels. This is enough for a sunset session or a short event. For all-night parties, a generator is the only practical option.

### 7.1 Arrival and Assessment (5 minutes)

- [ ] Survey the venue: Where is the power outlet? Where is the audience? Where is the best speaker position?
- [ ] Check power: Is there a working outlet within cable reach? Is it on a circuit with enough capacity (15A minimum, 20A preferred)?
- [ ] Identify the best speaker position: For outdoor events, face speakers away from neighbors and toward the audience. For indoor events, place speakers at one end of the room, aimed along the longest dimension.

### 7.2 Unload and Position (10 minutes)

- [ ] Place subwoofers on the ground at the front of the audience area. Subs do not need stands -- bass is omnidirectional at PA frequencies.
- [ ] Place tops on speaker stands (tripod stands with 35mm poles). Height: tweeter at or above head height of the audience (standing). This ensures the sound travels over the crowd to the back.
- [ ] Position the amp rack and Pi/DSP unit behind or beside the sub stack, out of the way.

### 7.3 Cable All Connections (10 minutes)

- [ ] Speaker cables: Amp sub channels → subwoofers. Amp top channels → tops.
- [ ] Signal cables: USB audio interface outputs → amp inputs.
- [ ] USB cable: Pi → USB audio interface.
- [ ] Power: Pi power supply, amp power supply → power strip → wall outlet.
- [ ] Source: Phone or laptop connected to Pi via Bluetooth/AirPlay, or AUX cable to interface.
- [ ] Run cables neatly along walls or tape to the floor with gaffer tape. No trip hazards.

### 7.4 Power On and Sound Check (5 minutes)

- [ ] Power on in order: Pi first (wait for full boot), then USB interface, then amps.
- [ ] Set CamillaDSP volume to -30 dB (very quiet).
- [ ] Play a familiar test track from your phone.
- [ ] Verify all four channels are producing sound: check each sub and each top individually by muting channels in CamillaDSP.
- [ ] Verify correct signal routing: bass frequencies in subs, mids/highs in tops.
- [ ] Gradually increase volume to performance level.
- [ ] Walk the venue: check coverage at the back, sides, and front. Adjust speaker aim if needed.
- [ ] Listen for any distortion, buzzing, or rattling. Fix before the event starts.

### 7.5 Performance EQ (2-3 minutes)

- [ ] Indoor venue with hard surfaces (warehouse, hall): Cut bass 3-6 dB to reduce boom. Add slight mid-range boost for vocal clarity.
- [ ] Outdoor event: Boost bass 3-6 dB (no walls to reinforce). Treble may need a slight boost to compensate for the lack of room reflections.
- [ ] Small room with carpet and curtains: Minimal EQ changes from your baseline.
- [ ] Trust your ears. Walk around. Ask a friend standing in the crowd. Adjust.

### 7.6 During the Event

- [ ] Monitor the amp rack periodically. Check for excessive heat, flashing protection LEDs, or any unusual smells (burning is bad).
- [ ] Watch the limiter activity in CamillaDSP's web interface. Occasional limiter engagement on peaks is normal. Constant limiting means you are pushing too hard -- turn down the master level.
- [ ] If someone asks you to turn it up and you are already at 90% of the system's capacity, the answer is "it is already very loud." Blowing a driver to satisfy one request ruins the party for everyone.

---

## 8. Troubleshooting Common PA Problems

### 8.1 Feedback (Screech/Howl)

If you are running a microphone through the PA (for announcements or a live performer), feedback happens when the microphone picks up the PA's output, which is amplified and sent back through the PA, creating a loop that builds to a screech.

**Immediate fix:** Reduce the mic channel level. Move the speaker further from the mic, or aim the speaker away from the mic.

**Better fix:** Use a directional microphone (cardioid pattern) aimed away from the speakers. Most PA feedback occurs at one or two specific frequencies where the room resonance, speaker response, and mic response all align. Identify the frequency (it will be a clear, sustained tone) and notch it out with a narrow parametric EQ cut (-6 to -10 dB, Q = 10-15) in CamillaDSP. This is called "ringing out" the system.

**Best fix:** Use an automatic feedback suppressor (software or hardware). Some CamillaDSP configurations can detect and suppress feedback automatically, though this requires additional configuration.

### 8.2 Uneven Coverage

People at the front are getting blasted while people at the back can barely hear. This is the inverse square law in action -- SPL drops 6 dB every time you double the distance.

**Fix:** Aim the tops slightly upward (tilt up 5-10 degrees). This throws more energy to the back of the crowd and reduces the level at the front. For very deep coverage (30+ meters), add delay speakers halfway back -- a second pair of tops on stands, fed from a delayed output channel in CamillaDSP. The delay time should equal the sound travel time from the main speakers to the delay speakers:

```
Delay (ms) = distance (meters) / 0.343
```

For delay speakers 15 meters behind the mains:

```
Delay = 15 / 0.343 = 43.7 ms
```

Set the delay speaker output 3-6 dB below the mains so the perceived source remains at the front.

### 8.3 Distortion at Volume

The system sounds clean at moderate levels but distorts at high volume. Possible causes:

1. **Amp clipping**: The limiter is not set correctly, or the input gain is too high. Check the limiter threshold and reduce if needed.
2. **Speaker distortion**: You are exceeding the speaker's clean operating range. Check the THD data from Chapter 37 -- if the speaker produces 5%+ THD at your operating level, you have exceeded its comfort zone. Turn down, or add more speakers to share the load.
3. **DSP clipping**: CamillaDSP's internal processing is hitting 0 dBFS. This happens when you boost EQ without reducing the overall level. Add -3 to -6 dB of headroom at the CamillaDSP input stage.
4. **Port chuffing**: At high bass levels, air velocity through the subwoofer port exceeds the turbulence threshold, creating a chuffing or whooshing sound. This is a speaker design issue -- the port is too small for the output level. Reduce bass level, or plug the port (converting to a sealed box, which sacrifices low bass extension but eliminates port distortion).

### 8.4 Ground Hum

Same as home hi-fi (Chapter 38, section 8), but worse in PA because:
- Cable runs are longer (more antenna area to pick up interference)
- Venue wiring may be poor (old buildings, generator power)
- Lighting dimmers are nearby (dimmers create massive EMI at multiples of 50/60 Hz)

**Fix:** Use balanced connections for all signal cables. Use a DI box with ground lift for any instrument connected to the PA. If the Pi creates a ground loop through the USB interface, use a USB ground loop isolator.

### 8.5 No Sound

The most stressful problem because the party is starting and nothing is working.

**Systematic diagnosis:**
1. Is the Pi booted? Check for activity LEDs. Access the web interface from your phone.
2. Is CamillaDSP running? Check the web interface. If it shows an error, restart CamillaDSP.
3. Is the audio interface recognized? Check the Pi's audio output device settings.
4. Are the amps powered? Check power indicators.
5. Is the source playing? Play music on your phone and verify it is casting to the Pi.
6. Are all cables connected? Trace each cable from source to speaker. A disconnected speakON connector in the dark behind the sub stack is the most common cause.

If the Pi has failed completely, fall back to the direct input path (section 2.3): phone → 3.5mm cable → analog crossover → amps → speakers. This is why you built the analog backup crossover.

---

## 9. The Complete Signal Path: Phone to Dancefloor

Like Chapter 38's Spotify-to-eardrum trace, let's follow a bass drop from your phone through the PA system:

1. **Phone**: Your DJ app (or Spotify, or SoundCloud) outputs audio via AirPlay or Bluetooth to the Pi. The track is a compressed audio stream (AAC or aptX codec).

2. **Pi reception**: The Pi's wireless radio receives the stream. The audio distribution (moOde/Volumio) decodes it to PCM.

3. **CamillaDSP processing**: The audio enters the DSP pipeline:
   - **Input EQ**: A high-shelf boost of +3 dB above 3 kHz compensates for the outdoor environment's treble loss. A low-shelf boost of +4 dB below 100 Hz gives the bass extra impact.
   - **Compressor**: The bass drop's peak is 14 dB above the average level. The compressor engages at -12 dBFS, 3:1 ratio, reducing the peak by about 5 dB. Makeup gain adds 3 dB. The result: the bass drop is still impactful but only 9 dB above average instead of 14 dB. The system sounds louder overall.
   - **Crossover**: The low-pass (LR4 at 120 Hz) sends the bass energy to the sub channels. The high-pass (LR4 at 120 Hz) sends the mids and highs to the top channels.
   - **Sub high-pass filter**: A 4th-order high-pass at 35 Hz blocks infrasonic content from overloading the subs.
   - **Per-channel limiters**: The bass drop's sub channel hits -2 dBFS. The limiter engages briefly (3 ms attack), shaving 2 dB off the peak. The top channel is well below the threshold. The limiter protects the subs from the momentary overload.

4. **DAC conversion**: The USB audio interface converts four channels of digital audio to four analog signals. Each signal is a balanced, low-impedance output.

5. **Amplification**: The TPA3255 boards receive the balanced signals (converted to single-ended at the input, or via a balanced-to-single-ended conversion if the board supports it). The Class D output stage switches at ~600 kHz, modulating the pulse width to recreate the audio waveform at the speaker terminals. The sub amp delivers a 25 Vrms, 6-amp burst for the bass drop into the 4-ohm subwoofer load. The top amp delivers a moderate 10 Vrms signal for the mid-high content.

6. **Subwoofer**: The 15" woofer receives the amplified bass signal. The cone moves forward 5 mm (well within its 8 mm Xmax), pushing a wall of air. The port adds its own acoustic output as the air inside the cabinet resonates at the tuning frequency. The bass radiates omnidirectionally, filling the space.

7. **Top speaker**: The 12" woofer reproduces the 120-2000 Hz range (kick drum attack, bass guitar harmonics, vocals). The horn-loaded compression driver handles 2000 Hz and up (cymbals, vocal sibilance, synth leads). The horn concentrates the high-frequency energy into a 90° × 60° coverage pattern, throwing it into the crowd instead of wasting it on the sky.

8. **The dancefloor**: At 5 meters from the stack, the combined SPL is about 108 dB. The bass drop hits with physical impact -- you feel it in your ribcage before you hear it in your ears. At 10 meters, the SPL is about 102 dB -- still very loud. At 20 meters (the back of the crowd), it is about 96 dB -- loud enough to dance to, quiet enough to shout a conversation.

9. **The neighbors**: At 100 meters, the SPL has dropped to about 68 dB -- about the level of normal conversation. At 200 meters, it is 62 dB. Know your local noise ordinances. Most residential areas have a limit of 55-65 dB at the property line after 10 PM.

Total elapsed time from bass drop in the audio file to bass drop hitting the dancefloor: about 80-150 ms (audio buffer + DSP processing + DAC conversion + amplifier propagation + sound travel time). Nobody on the dancefloor notices or cares. They are too busy dancing.

---

## 10. After the Party

### 10.1 Breakdown Procedure

1. **Amps off first** (prevents power-off transients from hitting speakers).
2. **Disconnect speaker cables** from amps (speakON connectors twist-unlock easily).
3. **Disconnect signal cables**.
4. **Pi and interface off** (just unplug).
5. **Lower tops from stands**. Never carry a speaker on a stand -- lower it first, then fold the stand.
6. **Coil cables properly** (over-under technique prevents tangles and cable damage). Each cable gets its own coil, secured with a velcro strap.
7. **Load vehicle**: Subs first (heavy, go on the bottom). Tops next. Stands and cables last. Nothing loose -- secure everything so it does not slide.

### 10.2 Emergency Procedures

Things go wrong at events. Here is how to handle the most common emergencies:

**Rain starts during an outdoor event:**
1. Immediately reduce volume to 50% (protect speakers from moisture-induced thermal stress).
2. If rain is light and brief (5-10 minutes), cover the amp rack with a tarp/plastic sheet and keep playing. The speakers can handle light drizzle for a short time.
3. If rain is heavy or sustained, power down the amps first, then disconnect speaker cables. Cover everything with tarps. Do not resume until equipment is dry.
4. After the event, leave the speaker grilles off overnight in a dry room to allow any moisture in the cabinets to evaporate.

**A speaker stops working mid-event:**
1. Check the speakON connection at the speaker end (most common cause -- the connector was not fully twist-locked).
2. Check the amp channel: does the amp's protection LED indicate a fault? If yes, power-cycle the amp. If the fault returns, the speaker cable or speaker may have a short.
3. Swap the speaker cable with a spare. If the speaker works with the new cable, the old cable has a break.
4. If the speaker is dead and the cable is good, the driver or internal wiring has failed. You cannot fix this in the field. Swap in a spare speaker if you have one, or redistribute: run the remaining top as a mono center speaker and reduce volume to avoid overloading it.

**The Pi crashes or freezes:**
1. Power-cycle the Pi (unplug and re-plug the USB-C power). It should reboot in 30-60 seconds and CamillaDSP will start automatically if configured to do so.
2. If the Pi does not boot, switch to the backup path: phone 3.5mm output directly to the USB audio interface (or to a small analog mixer if you have one), bypassing the Pi entirely. You lose DSP processing but keep the music playing.
3. If you frequently experience Pi crashes at events, the problem is usually power supply related. Use a high-quality 5V 3A power supply (the official Raspberry Pi supply, not a random phone charger) and avoid sharing the power strip circuit with high-draw equipment like lighting dimmers.

**Power trips the circuit breaker:**
1. Reduce the PA volume to zero immediately (to prevent speaker damage from the power surge when the breaker is reset).
2. Identify what tripped the breaker. If the PA was on the same circuit as lighting, a fog machine, or a refrigerator, one of those probably caused the overload.
3. Reset the breaker. Move the PA to a different circuit if possible, or disconnect the non-essential load.
4. Gradually bring the PA volume back up. If the breaker trips again at moderate volume, your power source is inadequate -- reduce the system to tops-only (lower power draw) or find a better power source.

### 10.3 Post-Event Inspection

The day after:
- Check each speaker for driver damage: push gently on each cone (should move smoothly with no rubbing or scratching sound).
- Check each amp: power on and verify no protection lights or error indicators.
- Check all cables: visual inspection for damage, kinks, or loose connectors.
- Recharge any batteries (if using battery-powered components).
- Replace any consumables (gaffer tape, cable ties).
- Note any issues in a maintenance log. Fix them before the next event.

---

## 11. Scaling Up and Scaling Down

### 11.1 Scaling Down: Small Indoor Party

For 20-30 people in a living room, the full PA rig is overkill. Scale down to:
- Skip the subs (the tops have enough bass for a small room)
- One TPA3255 board (two channels: left top + right top)
- CamillaDSP still runs, but the crossover is configured as full-range with a high-pass at 50 Hz to protect the tops from excessive bass excursion

This reduces the system to two speakers, one amp, and the Pi. It fits in a backpack.

### 11.2 Scaling Up: Large Outdoor Event

For 150-200 people, the base system is not enough bass. Scale up by:
- Adding 2 more subwoofers (4 subs total, +6 dB of bass compared to 2 subs)
- Adding a third TPA3255 board for the additional subs
- Optionally adding delay speakers (a second pair of tops on stands, 15-20 meters behind the mains, with appropriate delay in CamillaDSP)

Each additional sub pair adds about 6 dB of bass output. Four subs can produce chest-thumping bass for a crowd of 200 in an open field.

### 11.3 The Guitar Player Wants to Plug In

Your PA system can double as a live performance rig. A guitarist with a pedalboard (from Part 1 of this guide) can connect via a DI box:

```
Guitar → Pedalboard (Ch 5-10) → DI Box → PA balanced input → CamillaDSP → Amp → Tops
```

The DI (Direct Injection) box converts the guitar's high-impedance unbalanced signal to a low-impedance balanced signal for the PA. It also provides ground lift to prevent hum. If the guitarist uses an amp simulator pedal (many modern pedalboards include one), the DI signal can go straight to the PA without any amp on stage.

For vocals, connect a dynamic mic (Shure SM58 or equivalent) to the USB audio interface's mic input (with phantom power if using a condenser mic). CamillaDSP can add a separate channel for the mic with its own EQ and level control.

This is where the PA becomes a complete sound system -- music playback, live instruments, and vocals, all managed through CamillaDSP. It is also where the system complexity starts to justify a dedicated mixer, but that is a topic for Chapter 42 (Part 7 -- turning this into a service).

---

## 12. Summary

The party PA system is the other side of the coin from the home hi-fi. Same fundamental engineering, completely different priorities:

| Aspect | Home Hi-Fi (Ch 38) | Party PA (Ch 39) |
|--------|-------------------|------------------|
| Primary goal | Fidelity | SPL |
| Amp type | Class AB (LM3886) | Class D (TPA3255) |
| Speaker type | 2-way bookshelf | Horn top + sub |
| Crossover | 2.5 kHz (tweeter) | 120 Hz (sub/top) |
| DSP focus | Room correction | Protection (limiter) |
| Power budget | 50W/channel | 175W/channel |
| Cable connectors | RCA + banana | Balanced + speakON |
| Setup time | Once, permanent | 30 minutes per event |
| Biggest risk | Ground hum | Blown driver |
| Secret weapon | Room treatment | DSP limiter |

Both systems are built from the same guide. Both use the same engineering principles. Both sound better than commercial alternatives at their price points because you optimized them for their specific purpose rather than accepting a one-size-fits-all compromise.

The home hi-fi system rewards you with hours of musical enjoyment in your personal space. The party PA system rewards you with the look on people's faces when the bass drops and the whole field starts moving. Both are worth building. And now, having built both, you understand audio systems from the transistor to the dancefloor.

If you want to take this further -- turn your skills into a side business, build systems for other people, or offer sound services for events -- turn to Part 7.
