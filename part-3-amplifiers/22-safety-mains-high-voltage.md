<!--
  CHAPTER: 22
  TITLE: Safety: Working with Mains and High Voltage
  PART: 3 — Amplifiers
  PREREQS: Chapter 21
  KEY_TOPICS: mains wiring, fusing, ground fault protection, isolation transformers, tube amp lethal voltages, capacitor discharge, one-hand rule, safe probing, dim bulb tester, legal considerations
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 22: Safety: Working with Mains and High Voltage

> **Part 3 — Amplifiers** | Prerequisites: Chapter 21 | Difficulty: Intermediate

---

> **THIS IS THE MOST IMPORTANT CHAPTER IN THIS GUIDE.**
>
> Everything else in this book — the theory, the circuit design, the builds — is worthless if you're dead. Electricity at mains and high-voltage levels kills quickly, silently, and without warning. There are no second chances with a lethal shock. You don't get to learn from the mistake because you won't be around to learn.
>
> Read this chapter completely before you plug in *anything* you've built. Read it again before your first tube amp project. And if at any point you're unsure, tired, or rushing — **stop working and come back tomorrow**.

---

This chapter is not about making you afraid. It's about making you *competent*. Electricians, engineers, and technicians work safely with dangerous voltages every day because they follow procedures, use the right tools, and never take shortcuts. You can do the same.

The difference between a hobbyist who works safely for decades and one who gets hurt is not talent or intelligence. It's discipline. Following the rules every single time, even when it feels unnecessary, even when you're "just going to check one thing real quick." That "one quick thing" is when accidents happen.

Let's build that discipline.

### In This Chapter
- Mains wiring: live, neutral, earth — what each does and why
- Fusing: why, how, and where
- Ground fault protection (GFCI/RCD)
- Isolation transformers for workbench safety
- Tube amp voltages: why 250-600V DC kills
- Capacitor discharge: procedures and tools
- The one-hand rule
- Safe probing techniques
- Building a dim bulb tester
- When to stop and ask for help
- Legal considerations for selling equipment

### Related Chapters
- [Ch 4: Your Workbench, Tools, and Safety](../part-0-electronics/04-workbench-tools-safety.md) — low-voltage safety basics
- [Ch 21: Power Supply Design](21-power-supply-design.md) — the circuits that produce dangerous voltages
- [Ch 24: Build: Tube Guitar Amplifier](24-build-tube-guitar-amp.md) — where you'll need all of this
- [Ch 23: Build: Chip Amp and Class D Amp](23-build-chip-amp-class-d.md) — mains safety for solid-state builds

---

## 1. Understanding Mains Electricity

Before you wire a single thing to mains power, you must understand what each wire does and what happens if you get it wrong.

### 1.1 The Three Wires

In most residential electrical systems worldwide, there are three conductors:

**Live (Hot) — BROWN or BLACK wire**
This conductor carries the voltage that alternates between positive and negative peaks (120V RMS in North America, 230V RMS in most of the world). The peak voltage is the RMS value times √2: 170V peak for 120V systems, 325V peak for 230V systems. This wire is *always* dangerous to touch when the circuit is energized. It is the wire that will shock you.

**Neutral — BLUE or WHITE wire**
The return path for current. In a properly wired system, neutral is close to earth potential (0V) at the service panel. However: **never assume neutral is safe to touch.** If the neutral connection is broken or if there's a fault, neutral can be at full mains potential. Treat neutral as dangerous.

**Earth/Ground — GREEN-YELLOW STRIPED or GREEN wire**
The safety conductor. Connected to the metal chassis of your equipment and to the earth bus in the building's electrical panel. Earth normally carries *no* current. Its purpose is to provide a low-impedance path to ground if a live wire contacts the chassis, causing the fuse or breaker to trip immediately and disconnecting the dangerous voltage.

### 1.2 Wire Color Codes

| Conductor | IEC (Europe, most of world) | North America | UK (modern) |
|-----------|----------------------------|---------------|-------------|
| Live/Hot  | Brown                      | Black (or Red)| Brown       |
| Neutral   | Blue                       | White         | Blue        |
| Earth     | Green-Yellow striped       | Green (or bare)| Green-Yellow|

If you're working on older equipment, the color codes may be different. **Always verify with a multimeter.** Never trust color alone in old wiring.

### 1.3 What Happens If You Get It Wrong

**Live and neutral swapped:** The equipment may appear to work, but the switch and fuse are now on the neutral line, not the live. When you turn the equipment "off," the live wire is still connected inside the chassis. Anyone who opens the chassis expecting it to be safe can be fatally shocked.

**Earth disconnected:** The equipment works normally — until a fault occurs. If a live wire contacts the chassis (due to a loose connection, vibration, or component failure), the chassis becomes energized at mains potential. Anyone touching the chassis is the path to ground. Without earth, the fuse won't blow, and the condition can persist indefinitely.

**Earth and neutral swapped:** The chassis is connected to the neutral line, which is close to but not at ground potential. Under certain fault conditions, the chassis can become energized. Also violates electrical codes and can cause nuisance tripping of GFCIs.

> **RULE: Triple-check your mains wiring before first power-up. Verify live, neutral, and earth with a multimeter. Verify earth continuity from the mains plug to the chassis with a continuity tester. Do this every time, on every build.**

### 1.4 How to Verify Mains Wiring with a Multimeter

Before you connect your project, verify the mains wiring at the IEC inlet (or terminal strip) with the mains cable unplugged:

1. **Set your multimeter to resistance/continuity mode.**
2. **Identify the IEC inlet pins.** Looking at the back of the inlet (solder/crimp side):
   - The center pin on the bottom row is earth
   - The left pin is typically live, the right pin is neutral (but verify with the manufacturer's datasheet)
3. **Verify earth:** Touch one probe to the earth pin of the IEC inlet. Touch the other to the chassis. You should read < 0.5 ohm (near zero). If not, your earth connection is bad.
4. **Verify no shorts:** Check between each pair of pins (live-neutral, live-earth, neutral-earth). You should read open circuit (infinite/OL on the meter) between all pairs when the power switch is off. With the switch on, live-neutral should show the transformer primary resistance (typically 2-20 ohms depending on the transformer size).
5. **After connecting mains power (with the project unplugged from the mains):** You can also verify that the outlet itself is wired correctly using a simple outlet tester (a $5 plug-in device with indicator lights that shows open ground, reversed polarity, etc.). Every workshop should have one.

### 1.5 International Differences

If you're building for 240V countries (Europe, UK, Australia, most of Asia):
- The mains voltage is roughly double (230V RMS, 325V peak)
- Fuse ratings are approximately half (because P = V × I, same power at double voltage means half the current)
- The mains plug styles are different but the IEC C14 inlet is universal
- UK plugs have their own fuse inside the plug — an extra layer of protection that other countries don't have
- Australian/NZ mains uses different color codes: brown (live), blue (neutral), green-yellow (earth) — same as IEC standard

If you're designing equipment that might be used in multiple countries, use a transformer with multiple primary taps (120V and 240V) or a universal primary (100-240V). Mark the current tap position clearly on the chassis. Connecting a 120V transformer to 240V mains will cause immediate transformer saturation, massive current draw, and potential fire.

---

## 2. Fusing

A fuse is a deliberately weak link in the circuit. It's designed to burn out and disconnect the power before the wiring, components, or (most importantly) you are damaged by excessive current.

### 2.1 Why Fuses Exist

Without a fuse, a short circuit draws enormous current from the mains. The wiring heats up, insulation melts, and fire can result. A fuse limits the duration of the fault by opening (burning out) when the current exceeds its rating.

### 2.2 How to Size a Fuse

The fuse rating should be:
1. **Higher than the normal operating current** (so it doesn't blow during normal use)
2. **Lower than what the wiring and components can safely handle** (so it blows before damage occurs)

For a power transformer with a 160VA rating on 120V mains:

```
Normal operating current = VA / V = 160 / 120 = 1.33A
```

A 2A slow-blow fuse is appropriate. It won't blow during normal operation (including the brief inrush current at power-on if slow-blow) but will blow quickly on a short circuit.

### 2.3 Fast-Blow vs Slow-Blow

**Fast-blow (F):** Opens almost immediately when current exceeds the rating. Use for circuits with no inrush current or where quick protection is needed.

**Slow-blow (T, for "time-delay"):** Tolerates brief current surges (like the inrush when charging filter capacitors) but still blows on sustained overcurrent. **Use slow-blow for amplifier mains fuses.** The inrush current at power-on can be 5-10x the normal operating current for a fraction of a second. A fast-blow fuse would nuisance-trip every time you turned the amp on.

### 2.4 Where to Place Fuses

**Primary fuse (mandatory):** On the live (hot) line, *before* the power switch and transformer. This protects against transformer faults and wiring faults. In an IEC inlet with integrated fuse holder, this is automatic.

**Secondary fuses (recommended):** On each secondary winding output, after the rectifier. These protect against amplifier faults. The LM3886's internal protection may make this unnecessary for chip amp builds, but it's still good practice.

**Speaker fuse (optional but wise):** A fast-blow fuse in the speaker line, rated slightly above the maximum expected speaker current. This is a last resort if DC protection fails. For a 100W amp into 8 ohms: Irms_max = sqrt(100/8) = 3.5A. A 5A fast-blow fuse is reasonable.

### 2.5 The IEC Inlet

Use an **IEC C14 inlet** with integrated fuse holder and power switch for any mains-connected project. These are the standard 3-pin inlets you see on the back of every piece of professional audio equipment. They provide:
- Proper strain relief for the mains cable
- Built-in fuse holder (accessible from outside the chassis)
- Proper earth connection
- EMI filter (in filtered versions)

Do not hardwire a mains cable into your project. Use an IEC inlet so the cable can be disconnected. This is both safer and required by most safety standards.

---

## 3. Ground Fault Protection

### 3.1 GFCI / RCD — What They Are

A **GFCI (Ground Fault Circuit Interrupter)** — called an **RCD (Residual Current Device)** outside North America — is a device that monitors the current balance between the live and neutral wires. In a normal circuit, the current flowing out through the live wire exactly equals the current returning through the neutral. If they differ (meaning current is leaking somewhere else — possibly through a person), the GFCI trips in milliseconds.

Trip threshold: typically **30 mA** for personnel protection. This is below the 50-75 mA threshold for ventricular fibrillation in most people.

Trip time: **< 30 milliseconds.** Fast enough to prevent lethal exposure in most cases.

### 3.2 Use a GFCI

> **Your workbench mains outlet MUST be GFCI/RCD protected. No exceptions. No excuses.**

If your workshop doesn't have GFCI outlets, buy a GFCI-protected extension cord or a portable RCD adaptor. They cost $20-30 and can save your life.

A GFCI does not prevent you from getting shocked. You will still feel the shock. But it limits the duration to milliseconds, which is usually not long enough to cause cardiac arrest or serious burns.

### 3.3 Limitations

A GFCI protects against current flowing from live to earth (through you). It does **not** protect against:
- Contact between live and neutral (where current returns through neutral normally — the GFCI sees balanced current)
- Voltage within the equipment (e.g., touching both the B+ rail and ground inside a tube amp — this current path doesn't go through the mains GFCI)

This is why GFCI protection is necessary but not *sufficient* for tube amp work. It protects against many common mains fault scenarios, but the high-voltage DC inside the amp requires separate precautions.

---

## 4. Isolation Transformers

### 4.1 What They Do

An **isolation transformer** provides galvanic isolation between the mains and your circuit. It has a 1:1 turns ratio (output voltage equals input voltage), but there is no direct electrical connection between primary and secondary. The energy is transferred magnetically.

### 4.2 Why They Matter for Workbench Safety

Without an isolation transformer, one terminal of the mains is connected to earth (neutral). If you touch a live circuit point and anything grounded (a grounded oscilloscope, a metal bench leg, a water pipe), current flows through you to earth and back through the neutral — potentially fatal.

With an isolation transformer, neither output terminal is referenced to earth. Touching one terminal and ground simultaneously does not complete a circuit through you. You'd need to touch *both* output terminals simultaneously to get shocked.

This doesn't make high voltage safe — 400V DC will still kill you inside a tube amp regardless of whether an isolation transformer is upstream. But it eliminates the class of accidents where you accidentally complete a circuit to ground through your body while probing the chassis.

### 4.3 When to Use One

**Always on your bench when working on mains-connected circuits.** The isolation transformer should be part of your standard test setup. Connect your project to the isolation transformer, not directly to the wall outlet.

**Note:** An isolation transformer does *not* replace a GFCI. Use both. The GFCI protects against faults on the primary side of the isolation transformer and during the moments when you're connecting or disconnecting equipment.

### 4.4 Sizing

Your isolation transformer must be rated for at least the VA of the equipment under test. For a guitar tube amp drawing 100W, use at least a 150VA isolation transformer. For a stereo solid-state amp, 300-500VA.

Avoid working on multiple pieces of equipment through the same isolation transformer simultaneously — you lose the isolation benefit if both are connected.

---

## 5. Tube Amp Voltages — The Lethal Reality

This section is specifically about the dangers inside tube amplifiers. If you're building only the solid-state amps from Chapter 23, this section is informational. If you're building the tube amp in Chapter 24, this section is **mandatory reading**.

### 5.1 The Numbers

| Location in a typical tube guitar amp | Voltage | Lethal? |
|---------------------------------------|---------|---------|
| Heater supply                         | 6.3V AC | No      |
| Preamp plate                          | 150-250V DC | **YES** |
| Screen grid (power tube)              | 300-400V DC | **YES** |
| Power tube plate                      | 300-450V DC | **YES** |
| B+ rail (first filter cap)            | 350-450V DC | **YES** |
| Rectifier output (before filtering)   | 350-460V peak | **YES** |
| Output transformer primary            | Up to B+ | **YES** |
| Bias supply                           | -30 to -60V DC | Painful, rarely lethal |
| Speaker output                        | 0-30V AC (signal) | No, but tickly |

### 5.2 How Electricity Kills

Electricity kills in three primary ways:

**Ventricular fibrillation:** Current passing through the heart disrupts its electrical rhythm. The heart quivers instead of pumping. Without immediate defibrillation, death follows in minutes. **It takes as little as 50 milliamps (0.05A) across the heart to cause fibrillation.** At 300V with a body resistance of 1000 ohms (wet skin, good contact): I = 300/1000 = 300 mA — six times the lethal threshold.

Let's put that in perspective. You feel a 9V battery on your tongue. That's a few milliamps through a very small area of wet tissue. Now imagine 300 times that current flowing through your heart muscle. The math is unambiguous.

**Respiratory arrest:** Current through the chest muscles can cause them to contract and lock. You can't breathe, can't call for help, and can't let go of the conductor. The "can't let go" phenomenon is particularly terrifying — at currents above about 20 mA, your muscles contract involuntarily, and if you're gripping a wire, your hand clenches and you cannot release it. The only release comes when the power is disconnected (by someone else) or when the circuit is broken by tissue damage.

**Burns:** High current causes resistive heating in body tissues. Even non-lethal shocks can cause deep burns at the contact points. Electrical burns are deceptive — the surface damage may look minor, but the deep tissue damage can be extensive because current concentrates at the small contact area.

### 5.3 Body Resistance — It's Lower Than You Think

The "1000 ohms" figure used above is realistic for wet skin and solid contact. But body resistance varies enormously:

| Condition | Approximate Resistance |
|-----------|----------------------|
| Dry skin, light touch | 100,000 - 500,000 ohms |
| Dry skin, firm grip | 10,000 - 50,000 ohms |
| Moist skin, firm grip | 1,000 - 5,000 ohms |
| Wet skin, solid contact | 500 - 1,000 ohms |
| Broken skin (cut) | 200 - 500 ohms |
| Internal (hand-to-hand) | 300 - 500 ohms |

When you're working on a tube amp in a warm workshop with slightly sweaty hands, your resistance might be 2000-5000 ohms. At 350V: I = 350/2000 = 175 mA. Still more than three times the lethal threshold. Even at 50,000 ohms (dry skin): I = 350/50000 = 7 mA. This is painful and can cause involuntary muscle contraction, potentially causing you to fall, drop tools into the chassis, or make secondary contact with other live points.

**The bottom line: there is no safe scenario for touching 300V+.** The only safe approach is to never touch live high-voltage circuits.

### 5.4 The Persistence Problem

### 5.3 The Persistence Problem

Here is what makes tube amps uniquely dangerous compared to solid-state equipment:

> **The filter capacitors in a tube amp store lethal energy that persists after the amp is unplugged.**

A 47 uF capacitor charged to 400V stores:

```
E = 0.5 × C × V² = 0.5 × 0.000047 × 400² = 3.76 joules
```

That doesn't sound like much, but consider: lethal cardiac fibrillation can be caused by as little as 0.01 joules delivered across the heart. The stored energy in a single tube amp filter cap is **375 times the lethal threshold**.

And without a bleeder resistor (or if the bleeder has failed open), this charge can persist for **hours**. The amp can be unplugged, sitting on your bench, completely silent, appearing dead — and contain enough stored energy to kill you the instant you touch the wrong point inside.

**This is why the procedures in the next section exist. This is why you follow them every single time.**

---

## 6. Capacitor Discharge: Procedures and Tools

### 6.1 The Discharge Tool

Build a discharge tool before you do any tube amp work. It's simple:

**Materials:**
- A 10k ohm, 10W wirewound resistor (handles up to 16W peak at 400V)
- Two alligator clips or banana plugs
- Insulated wire (rated for at least 600V — use silicone-insulated test lead wire)
- Optional: a 1M ohm, 1/4W resistor in parallel (for a slow final discharge after the initial discharge)

**Construction:**
```
Alligator clip ──[insulated wire]──[10k 10W resistor]──[insulated wire]── Alligator clip
```

The 10k resistor limits the discharge current to I = 400V / 10k = 40 mA, which is safe for the resistor and won't damage the capacitor. The discharge time constant is τ = 10k × 47uF = 0.47 seconds. After 5 time constants (2.4 seconds), the voltage has dropped to less than 1% of the original.

### 6.2 The Discharge Procedure

**EVERY TIME you are about to work inside a tube amp, follow this procedure. No exceptions.**

1. **Turn off the amplifier.**
2. **Unplug the mains cable.** Physically remove it from the back of the amp. Verify it's disconnected.
3. **Wait 5 minutes.** This allows the bleeder resistors (if present and functional) to do their work.
4. **Clip the discharge tool** between B+ and ground (chassis). Leave it connected for at least 30 seconds. You may see a small spark when you first connect it — this is the capacitor discharging. If you see a large spark, the bleeder resistors are not working and the caps were still fully charged.
5. **Measure with a multimeter.** Set your meter to DC voltage, 600V range or auto-range. Measure between B+ and ground. Measure across each individual filter capacitor. **The voltage must be below 50V before you proceed.** Ideally below 10V.
6. **Leave the discharge tool connected** while you work. This ensures any residual charge or leakage is continuously drained.

### 6.3 Why Not Just Short the Caps?

A common question from beginners: "Why not just touch a screwdriver across the cap terminals to discharge them?"

**Never do this.** A capacitor charged to 400V discharged through a short circuit produces an enormous instantaneous current (limited only by the capacitor's ESR and the resistance of the screwdriver/wire). This can:
- Weld the screwdriver to the terminal, creating a shower of sparks and molten metal
- Damage the capacitor (excessive discharge current can rupture the dielectric)
- Create a loud bang that startles you into dropping the screwdriver into the chassis (where it can short other things)
- Damage nearby components from the current pulse
- Burn your hand

The 10k discharge resistor limits the current to a safe 40 mA. The capacitor discharges in 2-3 seconds. No sparks, no drama, no risk. Use the proper tool.

### 6.4 Advanced: Using a Chicken Stick

Some experienced technicians use a "chicken stick" — a discharge tool with an insulated handle and a neon indicator lamp in parallel with the discharge resistor. The neon lamp glows when there's voltage present and goes dark when the cap is discharged. This provides visual confirmation of the discharge process.

To build one: wire a NE-2 neon lamp (with a 100k series resistor to limit current through the lamp) in parallel with your 10k discharge resistor. The neon lamp fires at about 65V and glows brighter at higher voltages. When the lamp goes dark, the voltage is below approximately 65V. Still measure with a multimeter afterward — the neon lamp is a convenience, not a precision instrument.

### 6.5 Common Mistakes

**"I just turned it off 10 seconds ago, it's probably discharged."** No. Without bleeder resistors, it might hold lethal voltage for hours. With bleeder resistors, it might take 30-60 seconds. Measure. Don't assume.

**"The tubes are pulled, so there's no power."** The filter capacitors don't care whether tubes are installed. They hold their charge regardless.

**"I'm only touching the preamp section."** The B+ rail runs through the entire amplifier. Many preamp components are connected to or near the same high-voltage rail. Don't assume any part of the circuit is safe without measuring.

**"My multimeter reads 0V."** Is your multimeter set to the correct range? Are the probes properly connected? Is the battery dead? Verify your meter is working by first measuring a known voltage source (like a 9V battery) before trusting a "0V" reading inside a tube amp.

---

## 7. The One-Hand Rule

> **When probing or working on a live (powered) circuit, keep one hand behind your back or in your pocket.**

This is one of the oldest and most important electrical safety rules. Here's why:

If you touch a high-voltage point with one hand and ground with the other, current flows from one hand, across your chest (through your heart), and out the other hand. This is the most dangerous current path because it crosses the heart directly.

If you touch a high-voltage point with one hand and your other hand is in your pocket, the current path is from your hand, down your arm, through your body to your feet, and to ground through the floor. This path has much higher resistance (especially if you're wearing shoes and standing on a dry floor) and does not cross the heart directly.

The one-hand rule doesn't make high voltage safe. It reduces the risk of the most immediately lethal current path. It's not a substitute for proper procedures — it's an additional layer of defense.

**Additional one-hand-rule practices:**
- Wear shoes with rubber soles on a dry floor
- Don't lean against grounded metal (bench frames, equipment racks) while probing
- Remove watches, rings, and bracelets that can contact circuit points accidentally
- Use insulated-handle tools exclusively

---

## 8. Safe Probing Techniques

When you need to take measurements on a live circuit (like setting bias on a tube amp), follow these practices:

### 8.1 Clip First, Then Power On

Never try to touch a probe to a test point in a live circuit. Your hand might slip. Instead:

1. **With the circuit unpowered and discharged**, clip your test leads to the measurement points.
2. Position the meter where you can read it without reaching over or into the chassis.
3. **Then** power on the circuit.
4. Take your readings.
5. **Power off** and discharge before moving the clips to different points.

This eliminates the most dangerous moment — reaching into a live chassis with a probe in your hand.

### 8.2 Use the Right Probes

- **CAT III rated probes** minimum for mains work. CAT III means the probes are designed for distribution-level voltages (up to 600V or 1000V depending on the rating). Cheap hobby probes are often rated only CAT I (low voltage) — their insulation can break down at mains voltages.
- **Probe tip covers:** Use probes with finger guards that prevent your fingers from sliding down to the tip.
- **Retractable tips or shrouded banana plugs** on the meter end prevent accidental contact.

### 8.3 Probe Only What You Need To

Have a plan before you open the chassis. Know which measurements you need, which test points to use, and what the expected readings are. Don't go "exploring" in a live chassis.

---

## 9. Building a Dim Bulb Tester

The **dim bulb tester** (also called a "current limiter" or "light bulb tester") is the single most useful safety tool for first power-up of any mains-connected project. It's simple, cheap, and has saved countless amplifiers (and builders) from catastrophe.

### 9.1 What It Is

A standard incandescent light bulb wired in series with the mains line to your project:

```
Wall outlet ──► [Live] ──► [Light bulb, 60-100W] ──► [Live to your project]
                [Neutral] ──────────────────────────► [Neutral to your project]
                [Earth] ────────────────────────────► [Earth to your project]
```

The light bulb limits the maximum current to what the bulb draws at full brightness. A 100W bulb on 120V mains: I_max = 100/120 = 0.83A. A 60W bulb: 0.5A.

### 9.2 How to Read It

**Bulb stays dark (or very dim glow):** Your circuit is drawing normal current. The bulb's resistance is very low compared to your circuit's impedance. This is the correct behavior for a properly working amplifier at idle (no signal, no speaker connected).

**Bulb glows dimly:** Your circuit is drawing moderate current. This might be normal for a Class AB amp with moderate quiescent current, or for a tube amp with heaters warming up. If the brightness matches what you'd expect for the known load, proceed with caution.

**Bulb glows at full brightness:** Your circuit has a low-impedance fault — possibly a short circuit or a wiring error. **Do not remove the dim bulb tester and connect directly to mains.** Power off immediately and find the problem. The dim bulb just saved your transformer, rectifiers, and possibly you.

**Bulb flashes brightly then dims:** Normal inrush current charging filter capacitors. The bulb should dim to its steady-state level within 1-3 seconds.

### 9.3 Building a Proper Dim Bulb Tester

For a permanent tool:

**Materials:**
- A metal or plastic electrical box
- An IEC C14 inlet (input from wall)
- An IEC C13 outlet (output to your project) — or hardwired output leads
- A standard light bulb socket (E27/E26)
- A bypass switch (to remove the bulb from the circuit after testing)
- An inline fuse (3A, as additional protection)
- A 60W and 100W incandescent bulb (keep both on hand)

Wire the light bulb in series with the live conductor. The bypass switch shorts across the bulb socket when you're ready to run at full power. Use the 60W bulb for initial testing, then the 100W for more current capacity.

**Important:** Use incandescent bulbs, not LED or CFL. Incandescent bulbs have a predictable resistance that limits current smoothly. LED and CFL "bulbs" contain electronics that don't behave the same way.

### 9.4 Choosing the Right Bulb Wattage

The bulb wattage determines the maximum current your circuit can draw during testing:

| Bulb Wattage (120V) | Max Current | Good For |
|---------------------|-------------|----------|
| 25W | 0.21A | Small tube amps (5F1 Champ), preamps |
| 40W | 0.33A | Medium tube amps, chip amp initial test |
| 60W | 0.50A | Most amp builds, initial testing |
| 100W | 0.83A | Higher-power amps, full-functionality test |
| 150W | 1.25A | High-power builds |

For 240V countries, the wattage equivalents are different (same power at double voltage means half the current). Use the corresponding wattage for your mains voltage.

**Start with the lowest wattage that's reasonable for your circuit.** For a 5F1 Champ that draws about 0.3A normally, a 40W bulb is perfect — it limits fault current while allowing normal operation. If the circuit works with a 40W bulb, try a 60W, then 100W, then remove the tester entirely.

### 9.5 Dim Bulb Limitations

The dim bulb tester has limits:
- It only protects against **overcurrent** faults (shorts, low-impedance faults). It does not protect against high-voltage faults within the equipment.
- It limits power to the circuit, which means your amplifier won't operate at full power during testing. This is expected.
- Some circuits behave differently with the current-limited supply (voltage may be lower, bias may be off). Use the dim bulb for initial safety testing, then remove it for full performance testing.

### 9.6 First Power-Up Procedure (for Any Mains-Connected Build)

1. **Visual inspection.** Check all wiring against the schematic. Check for solder bridges, cold joints, loose connections, misrouted wires. Check that the mains wiring is correct (live, neutral, earth).
2. **Continuity check.** With the unit unplugged, verify earth continuity from the mains plug to the chassis. Verify no shorts between live and neutral, live and earth, or neutral and earth.
3. **Smell test.** Before powering on, bring your nose close to the circuit and sniff. Any existing burning smell indicates a problem that occurred during assembly (soldering mishap, heated insulation).
4. **Dim bulb tester.** Connect the project through the dim bulb tester.
5. **Stand back and power on.** Don't hover over the project. Turn on from a distance. Watch the light bulb.
6. **Observe for 30 seconds.** Watch for smoke, burning smell, unusual sounds, or the dim bulb going to full brightness.
7. **Sniff again.** The smell test after power-on catches components that are overheating, overstressed insulation, or burning PCB material. If you smell anything burning, power off immediately.
8. **If all is well,** proceed to voltage measurements (using the clip-first technique from Section 8).
9. **If anything is wrong,** power off immediately, disconnect, and investigate.

---

## 10. First Aid — What to Do If Someone Is Shocked

Despite all precautions, accidents can happen. Knowing what to do can save a life.

### 10.1 If Someone Is Being Shocked (Still in Contact)

1. **Do NOT touch the person.** You will become part of the circuit. You will also be shocked.
2. **Disconnect the power.** Pull the plug. Trip the breaker. If you can't reach the source, use a dry, non-conductive object (wooden broom handle, dry plastic chair) to push the person away from the contact.
3. **Call emergency services immediately** (911 in the US, 112 in Europe, 000 in Australia).

### 10.2 After the Person Is Free from Contact

1. **Check for responsiveness.** Tap their shoulder and shout. If they're responsive, keep them calm, call for medical help, and monitor them. Even if they seem fine, **any shock from mains or high voltage requires medical evaluation** — cardiac arrhythmias can develop hours after the initial shock.
2. **If they're not breathing or have no pulse:** Begin CPR immediately. If an AED (Automated External Defibrillator) is available, use it. Continue CPR until emergency services arrive.
3. **Check for burns.** Electrical burns may be present at the contact points. Don't remove clothing stuck to burned skin. Cover burns with a clean, dry cloth.

### 10.3 After a Shock to Yourself

If you receive a shock and are able to let go:
1. **Stop all work immediately.** You may feel fine but be disoriented or in mild shock.
2. **Call someone.** Don't work alone after a shock event.
3. **Seek medical evaluation.** Even a "minor" shock from mains voltage can cause cardiac disturbances that appear hours later. Go to the emergency room or call your doctor. This is not optional.
4. **Document what happened.** Write down what you touched, what you were doing, and what the voltage source was. This helps both your medical provider and your future safety analysis.

### 10.4 Having an AED on Site

If you regularly work with high-voltage equipment, consider having an **AED (Automated External Defibrillator)** accessible in your workshop or the building. AEDs are designed for untrained users — they analyze the heart rhythm and deliver a shock only if needed. They are the single most effective intervention for ventricular fibrillation caused by electrical shock.

Many workplaces, community centers, and gyms have AEDs mounted on walls. Know where the nearest one is to your workshop.

---

## 11. Workbench Setup for High-Voltage Work

Your workbench setup can make the difference between a safe workspace and a dangerous one. Here's how to set up a proper workbench for amplifier work.

### 11.1 The Bench Surface

- **Non-conductive surface:** Wood, laminate, or a rubber mat. Not bare metal. If your bench has a metal frame, consider a rubber mat on top.
- **Keep it clean and dry.** Solder splashes, wire clippings, and spilled coffee create unexpected conductive paths. Clean up regularly.
- **Adequate lighting.** You need to see what you're doing inside a chassis. A desk lamp that can be positioned to shine into the chassis is essential. LED headlamps are excellent for tube amp work.

### 11.2 Essential Test Equipment

At minimum, you need:
- **Digital multimeter (DMM):** CAT III 600V rated or better. Fluke, Keysight, or Brymen are reliable brands. Cheap multimeters can have inadequate voltage isolation — don't trust your life to a $10 meter.
- **Dim bulb tester:** As described in Section 9.
- **Capacitor discharge tool:** As described in Section 6.
- **Isolation transformer:** As described in Section 4.
- **GFCI-protected outlet.**

Strongly recommended:
- **Oscilloscope:** For observing waveforms, measuring ripple, and verifying amplifier performance. Even a basic 2-channel digital scope (Rigol DS1054Z or similar) is immensely useful. Set up a dedicated probe for measuring B+ ripple at high voltage using a 10:1 probe in 10x mode.
- **Variac (variable autotransformer):** Allows you to slowly bring up the mains voltage rather than applying full voltage instantly. Useful for first power-up of tube amps. Connect: wall → dim bulb tester → variac → isolation transformer → amplifier.
- **Signal generator:** For testing amplifier response. A smartphone with a signal generator app is sufficient for basic testing.

### 11.3 Fire Safety

- **Keep a fire extinguisher within reach.** A small CO2 or dry chemical extinguisher is appropriate for electrical fires. Do NOT use water on an electrical fire.
- **Know where your main breaker is.** If something catches fire, the first step (after personal safety) is to kill the power.
- **Have a clear exit path.** Don't block your workshop exit with equipment or stored materials.

---

## 12. When to Stop — And When to Ask for Help

### 10.1 Stop If You Are:

- **Tired.** Fatigue causes mistakes. The kind of mistakes that kill in high-voltage work. Go to bed. The amp will be there tomorrow.
- **Frustrated.** If you've been debugging for hours and can't find the problem, you're more likely to skip safety steps in your impatience. Walk away.
- **Rushing.** "I'll just check one thing real quick" is the preamble to most workshop accidents. If you don't have time to do it safely, you don't have time to do it.
- **Under the influence.** Alcohol, drugs, or certain medications slow reaction time and impair judgment. Not compatible with lethal voltages.
- **Unsure.** If you don't understand what you're about to do, don't do it. Ask first.

### 10.2 Ask for Help When:

- You don't understand a part of the schematic — especially the mains wiring or B+ supply.
- Your measurements don't match expected values and you don't know why.
- You smell something burning but can't identify the source.
- A component failed and you're not sure why.
- You're about to modify a circuit design and want someone to check your work.

**Where to find help:**

- **DIYAudio.com forums:** Active community of experienced builders who are generous with their knowledge. Post your schematic, measurements, and photos. They will help.
- **The Gear Page and Amp Garage forums:** For guitar amp specific questions.
- **Local electronics/maker communities:** A mentor who can physically inspect your work is invaluable, especially for your first high-voltage project.
- **Professional repair technicians:** If you've built something that doesn't work and you can't figure out why safely, there's no shame in paying a professional to diagnose it.

---

## 11. Legal Considerations for Selling Mains-Connected Equipment

If you build amplifiers and want to sell them (even to friends), you're entering a legal and liability landscape that you need to understand.

### 11.1 Safety Standards

Commercial electronic equipment connected to mains must comply with safety standards:

- **UL (Underwriters Laboratories) — North America:** UL 60065 (audio equipment) or the newer UL 62368-1. Requires testing and certification by a UL-recognized lab.
- **CE marking — Europe:** EN 60065 or EN 62368-1, plus EMC testing (EN 55032/55035). Self-declaration is technically allowed but you must have supporting test data.
- **CSA — Canada:** Similar requirements to UL.
- **Other regions:** Most countries have their own certification requirements (CCC for China, PSE for Japan, etc.).

### 11.2 What This Means for DIY Builders

Formal UL/CE certification costs thousands of dollars and requires design changes to pass. Most DIY builders selling one-offs or small runs can't practically achieve this. Your options:

**Build for personal use only.** No regulations apply to equipment you build for yourself.

**Sell as "kits" or "assembled to customer specification."** Some jurisdictions treat custom-built, one-off equipment differently from mass-produced goods. This is a gray area — consult a local attorney.

**Get liability insurance.** If you sell any mains-connected equipment, seriously consider product liability insurance. If someone is injured by your product, you face personal liability that can be financially devastating. Insurance doesn't replace safe design, but it provides a financial safety net.

**Disclose the risks.** At minimum, include documentation with your product that specifies: proper use, safety warnings, fuse specifications, and that the equipment should be serviced by qualified personnel only.

### 11.3 The Ethical Bottom Line

If you sell an amplifier, you are responsible for its safety. Not "somewhat" responsible — fully responsible. Build it right. Fuse it right. Ground it right. Test it thoroughly. Document it clearly. If you're not confident in the safety of a build, don't sell it.

---

## 12. Safety Checklist

Print this checklist and tape it to your workbench. Use it every time.

### Before Starting Work

- [ ] GFCI/RCD outlet verified working (press TEST button monthly)
- [ ] Isolation transformer connected (for mains-connected projects)
- [ ] Dim bulb tester ready
- [ ] Discharge tool ready and tested
- [ ] Multimeter verified working (test on known voltage source)
- [ ] Workspace clean and dry
- [ ] No distractions (put the phone down)
- [ ] Not tired, frustrated, or impaired

### Before First Power-Up (New Build)

- [ ] Visual inspection of all wiring against schematic
- [ ] Mains wiring correct: live to fuse/switch, neutral to neutral, earth to chassis
- [ ] Earth continuity verified (plug pin to chassis: < 0.5 ohm)
- [ ] No shorts: live-to-neutral, live-to-earth, neutral-to-earth
- [ ] All solder joints inspected (no bridges, no cold joints)
- [ ] Correct fuse installed (slow-blow, correct rating)
- [ ] Bleeder resistors installed (tube amps)
- [ ] All connectors secured (no loose wires that could contact chassis)
- [ ] Speaker disconnected (for first power-up)

### Before Working Inside a Tube Amp

- [ ] Amplifier turned OFF
- [ ] Mains cable UNPLUGGED and physically removed
- [ ] Wait 5 minutes
- [ ] Discharge each filter cap with discharge tool (30+ seconds each)
- [ ] Measure B+ to ground: below 50V (ideally below 10V)
- [ ] Measure across each filter cap individually: below 50V
- [ ] Leave discharge tool connected during work

### During Live Measurements

- [ ] One-hand rule: other hand behind back or in pocket
- [ ] Clip leads attached BEFORE power-on
- [ ] Wearing shoes on dry floor
- [ ] No jewelry on hands/wrists
- [ ] Know where the power switch is (to turn off quickly if needed)
- [ ] Another person present or aware you're doing high-voltage work

---

## 13. Chapter Summary

This chapter covers safety procedures that must become second nature:

1. **Mains wiring** has three conductors. All three must be connected correctly. Triple-check before power-up.
2. **Fuses** are your first line of defense. Slow-blow on the primary, sized correctly.
3. **GFCI/RCD** on your workbench outlet is not optional. It limits shock duration to milliseconds.
4. **Isolation transformers** prevent ground-fault shocks through your body to earth. Use one for all bench work.
5. **Tube amps contain lethal voltages** (250-600V DC) that persist in filter capacitors after power is removed. Discharge and verify every single time.
6. **The one-hand rule** prevents the most lethal current path through your heart.
7. **Clip first, then power on.** Never probe a live chassis with a hand-held probe.
8. **The dim bulb tester** limits current during first power-up, catching wiring errors and shorts before they cause damage.
9. **Stop when tired, frustrated, or unsure.** Come back tomorrow.
10. **Ask for help** when you need it. The online community is full of people who want you to succeed safely.

The safety procedures in this chapter are not suggestions. They are requirements. Follow them every time. The builds in Chapters 23 and 24 assume you have internalized these practices.

You are now ready to build amplifiers. Let's start with the solid-state builds.

---

*Next: [Chapter 23: Build: Chip Amp and Class D Amp](23-build-chip-amp-class-d.md)*
*Previous: [Chapter 21: Power Supply Design](21-power-supply-design.md)*
