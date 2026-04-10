<!--
  CHAPTER: 32
  TITLE: Enclosure Design and Fabrication
  PART: 5 — PCB Design & Professional Builds
  PREREQS: Chapter 31
  KEY_TOPICS: pedal enclosures, Hammond boxes, drilling templates, PCB mounting, speaker cabinets, joinery, material cutting, amp chassis, aluminum vs steel, sheet metal, ventilation, 3D printing, laser cutting, CNC routing, designing for serviceability
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 32: Enclosure Design and Fabrication

> **Part 5 — PCB Design & Professional Builds** | Prerequisites: Chapter 31 | Difficulty: Intermediate

You now have a professionally designed PCB. It is beautiful -- traces routed with care, ground plane intact, silkscreen crisp. And it is sitting naked on your workbench, completely exposed to the world. A bare PCB is like a server running without a firewall: technically functional, practically unacceptable. The enclosure is not just a box -- it is structural protection, electromagnetic shielding, thermal management, and the first thing anyone sees when they look at your build. It is the user interface.

This chapter covers enclosure design and fabrication for every type of audio project you have built so far. Pedal enclosures (those rugged aluminum boxes that survive being stomped on stage). Speaker cabinets (where woodworking meets acoustics). Amplifier chassis (where metal fabrication meets thermal management). And the modern tools that make custom enclosures accessible to anyone: 3D printers, laser cutters, and CNC routers.

The gap between "homemade" and "professional" is often just the enclosure. A mediocre circuit in a beautiful enclosure gets more respect than a brilliant circuit zip-tied to a piece of plywood. That is not how it should be, but it is how it is. And here is the good news: enclosure fabrication is a learnable skill with predictable results. Unlike circuit design, where a wrong component value creates subtle tonal changes you might argue about for hours, a misdrilled hole is obviously wrong and obviously fixable. This is craftwork, and it rewards practice.

### In This Chapter
- Pedal enclosures: Hammond boxes, drilling, PCB mounting
- Speaker cabinets: joinery techniques for strong, airtight boxes
- Material selection and cutting for speaker cabinets
- Amplifier chassis: aluminum vs steel, pre-made vs custom
- Ventilation and thermal management for tube amps
- 3D printing for audio: custom parts and prototypes
- Laser cutting: panels, baffles, and prototypes
- CNC routing: speaker baffles and flat-pack cabinet designs
- Designing for assembly and serviceability

### Related Chapters
- [Ch 9: Build: Overdrive and Distortion](../part-1-guitar-pedals/09-build-overdrive-distortion.md) — the pedal that needs an enclosure
- [Ch 14: Cabinet Design](../part-2-speakers/14-cabinet-design.md) — acoustic cabinet theory (sealed, ported, etc.)
- [Ch 16: Build: Bookshelf Speakers](../part-2-speakers/16-build-bookshelf-speakers.md) — the speaker build that needs a cabinet
- [Ch 23: Build: Chip Amp and Class D Amp](../part-3-amplifiers/23-build-chip-amp-class-d.md) — amp builds that need chassis
- [Ch 24: Build: Tube Guitar Amplifier](../part-3-amplifiers/24-build-tube-guitar-amp.md) — tube amp chassis and ventilation
- [Ch 28: Build: Pi Streamer](../part-4-digital-audio/28-build-pi-streamer.md) — streamer enclosure
- [Ch 31: PCB Design for Audio](31-pcb-design-for-audio.md) — the PCB that goes inside
- [Ch 33: Professional Finishing and Aesthetics](33-professional-finishing-aesthetics.md) — making the enclosure look professional

---

## 1. Pedal Enclosures

### 1.1 The Hammond Standard

Almost every boutique guitar pedal on the market uses a Hammond aluminum die-cast enclosure. These boxes are the de facto standard because they are rugged (die-cast aluminum survives years of stage abuse), affordable ($5-15 each), provide excellent electromagnetic shielding, and come in standardized sizes that every pedal builder knows by heart.

The three sizes you need to know:

| Hammond Model | Dimensions (L x W x H) | Nickname | Use Case |
|---------------|------------------------|----------|----------|
| 1590A | 93 x 39 x 31 mm | "Nano" | Tiny -- boost, buffer, single-knob effects |
| 1590B | 112 x 60 x 31 mm | "B" | Standard small -- most pedals with 2-3 knobs |
| 1590BB | 120 x 95 x 34 mm | "BB" | Medium -- pedals with 4-6 knobs, larger circuits |
| 125B | 122 x 67 x 31 mm | "125B" | Tall and narrow -- good for 3-4 knobs in a row |
| 1590XX | 145 x 121 x 39 mm | "XX" | Large -- complex pedals, multi-effect units |
| 1032L | 150 x 80 x 50 mm | "1032" | Deep -- for circuits with tall components |

For the Tube Screamer clone from [Ch 9](../part-1-guitar-pedals/09-build-overdrive-distortion.md), a **1590B** or **125B** is the right choice. The 1590B is the classic. The 125B gives you a bit more length if you want more comfortable knob spacing.

You can buy these from Tayda Electronics (cheapest, pre-drilled options available), Mammoth Electronics, Love My Switches, or direct from Hammond.

### 1.2 Drilling Templates

Drilling is the most nerve-wracking part of pedal building. You are putting permanent holes in a $10 box, and if you miss by 2mm, the pot will not align with the knob, or the jack will hit the side wall. The solution is a drilling template.

**Creating a drilling template:**

1. **Measure the enclosure interior** with calipers. Do not trust the published dimensions -- measure your actual box. Hammond tolerances are good but not perfect.
2. **Design the template** in software. You can use:
   - KiCad (draw on the Edge.Cuts or User.Drawings layer)
   - Inkscape or Illustrator (for a printable PDF)
   - FreeCAD or Fusion 360 (for a 3D model that also serves as a drill guide)
3. **Mark the hole positions** for: pots, footswitch, input jack, output jack, DC power jack, LED, any toggle switches.
4. **Print at 1:1 scale** on paper. Verify dimensions with a ruler before proceeding.
5. **Tape the template to the enclosure** with masking tape.

**Standard hole positions for a 1590B pedal:**

```
┌────────────────────────────────────┐
│                                    │  ← Top face
│   ○ Pot1        ○ Pot2        ○ Pot3  
│                                    │
│              ○ LED                  │
│                                    │
│           ○ Footswitch             │
│                                    │
└────────────────────────────────────┘

Left side: ○ Input Jack    Right side: ○ Output Jack
                  
Top end: ○ DC Power Jack
```

Pot holes are typically 7mm (for standard 16mm pots) or 10mm (for larger 24mm pots). Footswitch holes are 12mm. Jack holes are 10mm (for standard Switchcraft-style jacks) or 12mm (for some Neutrik jacks). DC jack holes are 12mm. LED holes are 5mm.

### 1.3 Drilling Technique

The key to clean holes in aluminum:

1. **Center punch** every hole location. This is critical -- the drill bit will wander on the smooth aluminum surface without a starting dimple. Use a spring-loaded center punch or a nail and hammer.
2. **Start with a pilot hole**. Drill a 3mm (1/8") pilot hole at every location with a standard twist drill bit. Use a drill press if you have one. If using a hand drill, keep it as perpendicular as possible.
3. **Step drill to final size**. A **step drill bit** (also called a Unibit) is the single most important tool for pedal enclosure work. It cuts clean, round holes in sheet metal without grabbing or chattering. Step up to the target diameter in stages.
4. **Deburr**. Use a deburring tool or a larger drill bit twisted by hand to remove the sharp edge from each hole. Sharp aluminum edges will cut wiring insulation (and your fingers).

**Step drill bit sizes you need**: A single step drill that covers 3mm to 12mm handles everything for pedal building. Buy a decent one (Irwin, DeWalt) -- cheap step drills dull fast on aluminum.

> **What happens if...** you skip the center punch? The drill bit will "walk" across the surface, ending up 1-3mm from where you intended. On a small enclosure like a 1590B, that is the difference between a pot shaft that aligns with the knob and one that is visibly off-center. Center punch every hole. Every time.

### 1.4 Fitting the PCB with Standoffs

Your PCB from [Ch 31](31-pcb-design-for-audio.md) needs to mount securely inside the enclosure without touching the aluminum walls (which are grounded through the enclosure):

- **Standoffs**: M3 nylon or metal hex standoffs (6-10mm height) are the standard. Four standoffs, one near each corner of the PCB.
- **Mounting holes**: Include M3 mounting holes in your PCB design (3.2mm drill, 6mm pad). Position them where they will not interfere with traces or components.
- **Assembly**: Screw standoffs to the enclosure bottom (drill and tap, or use self-tapping screws), then screw the PCB to the standoffs.

For pedals, an alternative to standoffs is using the pot and jack bushings as the mounting system -- the pots and jacks screw through the enclosure wall and hold the PCB in position. This works for small circuits but is less robust than dedicated standoffs.

**Pot and switch mounting**: Mount pots and switches through the enclosure wall, then solder them to the PCB (if using board-mounted components) or connect them with wires (if using panel-mounted components with off-board wiring). Board-mounted pots and jacks save wiring time but require precise alignment between PCB holes and enclosure holes.

### 1.5 Off-Board Wiring

Some components live on the enclosure, not the PCB: the footswitch, the jacks (sometimes), the LED (sometimes), the DC jack. These are connected to the PCB with wires.

**Off-board wiring best practices:**

- Use **stranded wire** (22-24 AWG) for off-board connections. It is more flexible than solid wire and less likely to fatigue and break from vibration.
- **Color code**: Red for VCC, black for GND, white/yellow/blue for signal, green for LED.
- **Keep wires short** and route them away from signal-carrying components.
- **Use connectors** (JST, Molex, or simple pin headers) on the PCB so you can disconnect the board from the wiring harness for servicing. Soldering wires directly to the PCB works but makes disassembly annoying.
- **Dress the wires**: Twist related pairs together (signal + ground) and route them neatly. A pedal with spaghetti wiring inside will be a nightmare to debug.

---

## 2. Speaker Cabinets

### 2.1 Why Joinery Matters for Speakers

A speaker cabinet is not just a box -- it is a pressure vessel. The woofer pushes air, and the enclosure must contain that pressure without leaking or vibrating. Every air leak is a source of distortion and lost bass extension. Every loose joint is a rattle waiting to happen at high volume.

This is why joinery matters more for speaker cabinets than for most woodworking projects. A bookshelf can have slightly loose joints and nobody notices. A speaker cabinet with loose joints will buzz, rattle, and lose low-frequency output.

The acoustic requirements:
- **Airtight**: No gaps. None. If you can feel air leaking from a joint when you press on the woofer cone, that joint is a problem.
- **Rigid**: The walls should not vibrate. Wall vibration colors the sound (it is called "cabinet talk") and wastes energy that should be going into making sound from the drivers.
- **Strong**: The cabinet has to withstand internal pressure changes and the mechanical stress of mounted drivers.

### 2.2 Joinery Techniques

From simplest to most refined:

**Butt Joint with Glue and Screws**

The simplest joint. One board's face meets another board's edge. Held together with wood glue and screws (or nails).

```
Side view:
┌──────┐
│      │
│  A   │──── Board B butts against A
│      │
└──────┘
```

- **Strength**: Moderate. The glue does most of the work; screws are for clamping during glue-up and added security.
- **Airtightness**: Good if you use enough glue and clamp tightly. Run a bead of wood glue on both surfaces.
- **Appearance**: You can see the end grain of board B. Not beautiful but functional.
- **Best for**: PA speakers and utility cabinets where looks do not matter. The subwoofer from [Ch 17](../part-2-speakers/17-build-towers-pa-subwoofer.md) is a good candidate -- it is going to be hidden under a table anyway.
- **Tools needed**: Drill, screwdriver, clamps, wood glue.

**Dado (Housing) Joint**

A groove cut into one board that the other board slides into. The groove depth is typically half the board thickness.

```
Side view:
┌──────┐
│  ┌─┐ │
│  │B│ │  ← Board B sits in a groove cut into Board A
│  └─┘ │
│  A   │
└──────┘
```

- **Strength**: Excellent. The groove provides mechanical locking and greatly increases the glue surface area.
- **Airtightness**: Excellent. The groove-and-board fit seals well, and glue fills any remaining gaps.
- **Appearance**: The joint is hidden from the front. Much cleaner than a butt joint.
- **Best for**: Bookshelf speakers and any cabinet where you want strength and a clean interior. This is what we used for the bookshelf speakers in [Ch 16](../part-2-speakers/16-build-bookshelf-speakers.md).
- **Tools needed**: Table saw with dado blade, or router with straight bit and guide.

**Rabbet (Rebate) Joint**

An L-shaped step cut into the edge of one board that the other board sits in.

```
Side view:
┌──────┐
│      └─┐
│  A     │ B  ← Board B sits in a step cut into A
│      ┌─┘
└──────┘
```

- **Strength**: Good. Better than butt, not quite as strong as dado.
- **Airtightness**: Good. The step provides alignment and increased glue area.
- **Appearance**: Hides end grain from one direction. Cleaner than butt, but the step is visible from the other direction.
- **Best for**: General-purpose cabinet construction. A good compromise between simplicity and quality.
- **Tools needed**: Table saw or router with rabbet bit.

**Mitre Joint**

Both boards are cut at 45 degrees and meet at the corner. No end grain visible.

```
Side view:
    ╱│
   ╱ │
  ╱  │ B
 ╱   │
╱    │
A────┘
```

- **Strength**: Weakest of the four on its own. The glue surface is long-grain to long-grain (good), but there is no mechanical locking. Reinforce with splines, biscuits, or corner braces.
- **Airtightness**: Requires careful fitting. Any gap in the mitre is visible and leaks air.
- **Appearance**: Beautiful. No visible end grain, clean lines. This is what high-end commercial speakers use.
- **Best for**: Hi-fi speakers where aesthetics matter. The floor-standing towers from [Ch 17](../part-2-speakers/17-build-towers-pa-subwoofer.md) look dramatically better with mitred corners.
- **Tools needed**: Table saw with precise 45-degree capability, or mitre saw. Clamp setup for mitres is trickier (band clamps or mitre clamps).

**Round-Over Edges**

Not a joint but a critical detail: rounding the front edges of the baffle (the board the drivers are mounted in) with a router and round-over bit. This reduces **diffraction** -- sound waves hitting a sharp corner bend around it and create comb-filtering artifacts. A 12-20mm round-over on the baffle edges measurably smooths the frequency response.

### 2.3 Choosing the Right Joinery

| Project | Recommended Joinery | Why |
|---------|--------------------|----|
| PA speakers (for outdoor parties) | Butt joint + screws + glue | Strong, fast, tolerates rough handling. Nobody at a party inspects your joinery. |
| Subwoofer | Butt joint or rabbet | These are often hidden. Strength and airtightness matter more than looks. |
| Bookshelf speakers (hi-fi) | Dado or rabbet | Good balance of strength, airtightness, and appearance. |
| Floor-standing towers | Mitre + splines or dado | Appearance matters. These are furniture that happens to make sound. |
| Portable/gigging speakers | Rabbet or dado + screws | Needs to survive transport. |
| Guitar speaker cab | Butt + corner blocks + Tolex | Traditional construction. Tolex covering hides everything. |

### 2.4 Glue-Up and Clamping

The glue-up is the most time-sensitive operation in cabinet construction. Once you apply wood glue, you have about 10-15 minutes of open time before the glue starts to set. Prepare everything before you start.

**Preparation:**
- Do a **dry fit** first. Assemble the entire cabinet without glue to verify all joints fit, all panels are the right size, and you know the assembly order. Mark mating surfaces with a pencil so you know which edge goes where.
- Pre-drill screw holes (if using screws) during the dry fit. This way, during glue-up, you just drive the screws without fumbling.
- Have all clamps ready and adjusted to approximately the right opening. You do not want to be adjusting clamps with glue-covered hands.
- Lay out a drop cloth or newspaper. Wood glue squeeze-out is messy.

**Assembly order** (for a rectangular cabinet):
1. Bottom panel + two side panels. Glue and clamp (or screw). Let the glue tack for 5 minutes before adding more panels.
2. Top panel. Glue and clamp to the sides.
3. Verify the cabinet is square. Measure the diagonals -- if they are equal, the cabinet is square. If not, rack the cabinet by pushing on the longer diagonal until the measurements match. Clamp in this position.
4. Front baffle (if separate from the sides). Glue and clamp.
5. Back panel: do NOT glue. Screw it on with a gasket for serviceability.
6. Internal braces can be glued before or during main assembly, depending on access.

**Clamping:**
- **Bar clamps** or **pipe clamps** for clamping across wide panels. You need at least 4 clamps for a bookshelf speaker (one per side).
- **Band clamps** (strap clamps) are excellent for mitred cabinets. The strap wraps around the entire cabinet and pulls all joints tight simultaneously.
- **Corner clamps** hold two boards at 90 degrees during glue-up. Useful for butt joints.
- **Cauls**: A straight piece of wood placed between the clamp and the workpiece to distribute clamping pressure evenly. Prevents dents from clamp pads.

**Glue squeeze-out**: When you tighten the clamps, excess glue will squeeze out of the joints. This is a good sign -- it means you used enough glue and the joint is tight. Wipe off the exterior squeeze-out with a damp rag before it dries. Interior squeeze-out can be left (it improves airtightness) or cleaned for aesthetics.

**Curing time**: Wood glue reaches handling strength in 30-60 minutes and full strength in 24 hours. Do not stress the joints or move the cabinet before 24 hours.

### 2.5 Sealing

Even with good joinery, seal every joint:

- **Wood glue** (Titebond II or III) is your primary sealant. It fills micro-gaps and creates an airtight bond when clamped properly.
- **Silicone sealant** along interior corners as a backup. Run a bead of 100% silicone caulk along every interior corner joint after assembly.
- **Driver gaskets**: Use foam gasket tape between the driver frame and the baffle to seal the driver mounting. Without this, air leaks around the driver mounting bolts and through any small gap between the driver frame and the baffle surface.
- **Terminal cup gaskets**: The binding post or speaker terminal cup on the back panel also needs a gasket.

### 2.6 Port Construction

For ported (bass reflex) cabinets, the port tube or slot port must be airtight and smooth inside:

**Tube ports**: PVC pipe is cheap and effective. Glue it into a hole cut in the baffle or back panel. Flare the ends (both inside and outside) to reduce air turbulence and chuffing noise. You can buy pre-made flared port tubes from Parts Express in various diameters (2", 3", 4") and lengths.

**Slot ports**: Built from the cabinet material itself (MDF walls forming a rectangular duct). More flexible in tuning (you can adjust length easily) and less prone to chuffing at high output. All interior surfaces of the slot must be smooth -- sand and seal them. A slot port that opens along one cabinet wall is the easiest to build: one extra MDF panel forms one wall of the port, and the cabinet wall forms the other.

**Port length and diameter**: These are calculated from your simulation in [Ch 15](../part-2-speakers/15-design-tools-simulation.md). Getting them right is critical -- they determine the tuning frequency of the cabinet. The port acts as a Helmholtz resonator -- change the length by 10mm and the tuning shifts by several Hz.

**Port placement**: Ports can be on the front baffle (easy to test and adjust), the back panel (hidden from view), or the bottom (firing into the floor for reinforcement). Front-firing ports are the easiest to prototype with because you can change the port length without disassembling the cabinet.

**The lean test**: After assembly, push gently on the woofer cone. You should feel significant resistance (the air inside is being compressed). If the cone pushes in easily, you have a leak. Find it and seal it.

> **What happens if...** you build a ported speaker cabinet with leaky joints? The port is calculated based on the cabinet being airtight except for the port itself. Air leaks act as additional ports, changing the tuning. Your carefully designed port frequency shifts, bass response changes, and the simulation from [Ch 15](../part-2-speakers/15-design-tools-simulation.md) no longer matches reality. In a sealed cabinet, leaks are even worse -- they turn your sealed design into an uncontrolled "leaky sealed" design with reduced bass extension.

### 2.7 Cabinet Assembly Checklist

Before moving to finishing ([Ch 33](33-professional-finishing-aesthetics.md)), verify the cabinet meets these criteria:

- [ ] All joints are tight, with full glue contact (no gaps visible from outside)
- [ ] Cabinet is square (diagonals are equal, within 1mm)
- [ ] All internal bracing is glued securely
- [ ] Driver cutouts are the correct diameter (test-fit the driver before finishing)
- [ ] Port dimensions match the design (measure length and diameter/area)
- [ ] Back panel fits with even gaps and all screw holes align
- [ ] Terminal cup hole is cut and the cup fits
- [ ] Mounting holes for internal crossover board are drilled
- [ ] All interior surfaces are sanded smooth (no splinters or rough edges near wiring)
- [ ] No visible glue squeeze-out on exterior surfaces (sand or scrape off before finishing)

---

## 3. Material Selection and Cutting

### 3.1 Materials for Speaker Cabinets

| Material | Thickness | Density | Cost | Use Case |
|----------|-----------|---------|------|----------|
| MDF (Medium Density Fiberboard) | 18-25mm | 700-800 kg/m3 | Low | Most speaker cabinets. Dense, consistent, easy to work, takes veneer well. The industry standard. |
| HDF (High Density Fiberboard) | 3-6mm | 800-1000 kg/m3 | Medium | Thin panels, internal bracing. Denser than MDF. |
| Baltic Birch Plywood | 12-25mm | 650-700 kg/m3 | Medium-High | Premium cabinets. Stronger than MDF, lighter, beautiful exposed edges. Used in high-end PA. |
| Solid hardwood | 18-25mm | 500-900 kg/m3 | High | Rarely used for full cabinets (warps, heavy). Used for trim, feet, and accent pieces. |
| Pine plywood | 12-18mm | 400-500 kg/m3 | Low | Budget PA. Light, resonant (not ideal for hi-fi), cheap. |

**MDF is the default choice** for most speaker cabinets. It is dense (which damps vibration), consistent (no voids, knots, or grain direction to worry about), cheap, and easy to machine. Its downsides: it is heavy, hates water (swells and disintegrates if it gets wet), and the dust is nasty (always wear a dust mask when cutting MDF -- the fine particles are a respiratory irritant).

**Baltic birch plywood** is the premium alternative. It is lighter than MDF for the same strength, has beautiful exposed edges (the laminated layers look great with a clear finish), and does not disintegrate from a splash of water. It costs 2-3x more than MDF and is harder to find in thick sheets.

For the bookshelf speakers from [Ch 16](../part-2-speakers/16-build-bookshelf-speakers.md), 18mm MDF is the right call. For tower speakers that will be visible furniture, consider Baltic birch or MDF with veneer.

### 3.2 Cutting Techniques

**Table saw**: The workhorse. Makes straight, precise cuts in sheet goods. Use a quality blade (80-tooth for MDF -- the more teeth, the cleaner the cut). A crosscut sled makes cutting panels to exact dimensions reliable and safe.

**Circular saw with guide**: If you do not have a table saw, a circular saw with a clamped straightedge guide produces surprisingly clean cuts. Use a fine-tooth blade and cut slowly.

**Router with straight bit and guide**: For dadoes and rabbets. Clamp a straightedge guide to the workpiece and run the router along it. Set the depth with multiple passes rather than one deep cut.

**Jigsaw**: For curved cuts, specifically driver cutouts. Mark the circle (use a compass), drill a start hole inside the circle, and cut along the line. A jigsaw does not make the cleanest cuts, but the driver frame covers the edge.

**Hole saw or circle cutter**: For smaller driver cutouts (tweeters). A hole saw in a drill press makes clean circular cuts. For larger woofer cutouts, a router with a circle-cutting jig is more precise than a jigsaw.

**CNC router**: For perfect driver cutouts, complex shapes, and batch production. If you have access to one (makerspace, or you invest in a tabletop CNC like a Shapeoko), it transforms cabinet fabrication. We will cover this in section 7.

### 3.3 Internal Bracing

MDF panels vibrate, especially at bass frequencies. Internal bracing stiffens the panels and pushes the resonant frequency of the cabinet walls above the operating range of the drivers.

**Types of bracing:**

- **Shelf bracing**: A horizontal shelf glued between opposing walls. Simple and effective. One or two shelves in a bookshelf speaker make a big difference.
- **Window bracing**: A piece of MDF with a large hole cut in it, glued between opposing walls. Stiffens the walls while allowing air to flow freely inside the cabinet.
- **Matrix bracing**: A grid of interlocking braces (think egg carton). Maximum stiffness. Used in high-end speakers.
- **Dowel bracing**: Wooden dowels glued between opposing walls. Quick, easy, and effective for small cabinets.

**Where to brace**: Focus on the largest unsupported panel spans. The side panels of a tower speaker are the most prone to vibration because they have the largest area. The back panel is next. The baffle is usually stiffened by the driver cutouts and mounting hardware.

### 3.4 Damping Material

After bracing, add damping material inside the cabinet to absorb internal reflections that would otherwise bounce back through the cone and color the sound:

- **Polyester fiberfill** (polyfill): Cheap, widely available, effective. Loosely fill the cabinet -- do not pack it tight. Polyfill also effectively increases the apparent volume of a sealed cabinet (the air acts more isothermal), which can lower the system resonance.
- **Acoustic foam**: Open-cell foam (not closed-cell). Line the walls. More controlled than polyfill but more expensive.
- **Fiberglass or mineral wool**: The most effective absorption material. Line the walls with 25mm thick panels. Wear gloves and a mask when handling -- the fibers are irritating. Wrap it in fabric to prevent fibers from entering the driver.
- **Felt**: Line the back wall and sides behind the woofer. Less effective than fiberglass but easier to handle.

For a ported cabinet, keep damping material away from the port -- you do not want to obstruct airflow through the port.

---

## 4. Amplifier Chassis

### 4.1 Aluminum vs Steel

| Property | Aluminum | Steel |
|----------|----------|-------|
| Weight | Light (2,700 kg/m3) | Heavy (7,800 kg/m3) |
| Machinability | Easy to drill, cut, bend | Harder to drill, can be bent |
| EMI shielding | Good | Better (higher permeability for magnetic fields) |
| Heat dissipation | Excellent (thermal conductivity 205 W/mK) | Poor (50 W/mK) |
| Cost | Higher | Lower |
| Appearance | Bright, takes anodizing well | Dull, takes powder coating well |
| Corrosion | Self-passivating (oxide layer) | Rusts without coating |

**For tube amplifiers**: Steel is traditional. The steel chassis provides superior magnetic shielding (important with large power transformers generating stray magnetic fields) and is stronger for supporting heavy transformers. The tube amp from [Ch 24](../part-3-amplifiers/24-build-tube-guitar-amp.md) uses a steel chassis.

**For solid-state amplifiers**: Aluminum is preferred. The LM3886 chip amp from [Ch 23](../part-3-amplifiers/23-build-chip-amp-class-d.md) generates heat that needs to be dissipated. Aluminum's excellent thermal conductivity makes the entire chassis a heat sink. Aluminum is also lighter, which matters if the amp is going in a portable rig.

**For the Pi streamer from [Ch 28](../part-4-digital-audio/28-build-pi-streamer.md)**: Aluminum project boxes or 3D-printed enclosures. The Pi generates minimal heat and needs no magnetic shielding.

### 4.2 Pre-Made Chassis

Several manufacturers sell pre-made amp chassis:

- **Hammond** (again): The 1444 series and 1441 series are popular for amplifier builds. Available in aluminum and steel, with pre-formed U-shapes and separate bottom plates.
- **Bud Industries**: Similar to Hammond, with a range of sizes.
- **Par-Metal**: Higher-end, heavier gauge, popular with tube amp builders.
- **Modulus Amp**: Chassis specifically designed for tube amp projects (5F1, Bassman, Plexi, etc.), with punched holes for tube sockets and transformers.

Pre-made chassis save enormous time. Drilling and punching holes for tube sockets in 16-gauge steel is difficult without a punch set, and getting the layout symmetrical requires careful work. Pre-punched chassis eliminate the most error-prone fabrication step.

### 4.3 Chassis Layout Planning

Before cutting or drilling anything, plan the chassis layout on paper or in CAD:

**The signal flow rule**: Just like PCB layout from [Ch 31](31-pcb-design-for-audio.md), the chassis layout should follow signal flow. Input on one side, output on the other. The signal never doubles back on itself.

**The heat rule**: Hot components (output tubes, power transistors, resistors dissipating power) go in areas with ventilation. The heat sink or ventilated section of the chassis is typically at the rear or top.

**The weight rule**: Heavy components (transformers) go in the bottom of the chassis or are mounted to the strongest structural members. A 3 kg power transformer hanging from a thin aluminum panel will eventually warp the panel.

**The access rule**: Components that need adjustment or replacement should be accessible without removing the entire circuit. In a tube amp, tubes must be accessible from the top for replacement. In a solid-state amp, the PCB should be accessible by removing the bottom plate.

Here is a classic tube amp chassis layout showing these principles:

```
┌──────────────────────────────────────────────────────┐
│                    TOP VIEW                           │
│                                                       │
│  [PT]       [V1] [V2]     [V3] [V4]     [OT]  [CT] │
│  Power      Preamp         Output        Output Choke│
│  Xformer    Tubes          Tubes         Xformer      │
│                                                       │
│  ← COOL END (input)          HOT END (output) →      │
│                                                       │
│  Signal flows left to right                           │
│  Transformers at opposite ends (reduces hum coupling) │
└──────────────────────────────────────────────────────┘
```

The power transformer and output transformer are at opposite ends of the chassis, oriented at 90 degrees to each other. Preamp tubes (which amplify small signals and are sensitive to hum) are near the power transformer end but as far as physically possible from the PT itself. Output tubes are in the center or toward the output transformer end.

### 4.4 Building from Sheet Metal

If you need a custom size (for a unique circuit or a very specific aesthetic), you can fabricate from sheet metal:

**Materials:**
- 16-gauge (1.6mm) steel: Standard for tube amp chassis. Strong enough to support transformers.
- 18-gauge (1.2mm) steel: Adequate for lighter builds.
- 16-gauge (1.6mm) aluminum: Standard for solid-state amps. Easy to work.
- 14-gauge (2.0mm) aluminum: For larger builds or when you want extra rigidity.

**Bending:**
- A sheet metal brake bends clean, straight lines. Desktop brakes (like those from Grizzly or Harbor Freight) handle 24" widths and are affordable.
- Mark your bend lines accurately. A 1mm error in the bend location cascades through the entire chassis.
- Aluminum bends easily. Steel requires more force and a firmer setup.

**Cutting:**
- Aviation snips for thin stock (22-gauge and thinner).
- Nibbler tool for thicker stock.
- Angle grinder with a cutting disc for straight cuts in any thickness.
- Jigsaw with a metal-cutting blade for curves.

**Punching:**
- Tube socket holes (typically 29mm for octal sockets) are best made with a Greenlee or Q-Max chassis punch. These punch clean holes in sheet metal without deformation.
- Transformer bolt holes are standard drill sizes (6mm or 8mm).

### 4.5 Mounting Transformers

Transformers in tube amps are heavy (1-5 kg each) and generate stray magnetic fields. Mounting them correctly is critical:

- **Through-bolts**: Four bolts through the transformer mounting tabs, through holes in the chassis, secured with nuts and lock washers underneath. This is the strongest mount.
- **Rubber grommets**: Where wires from the transformer pass through the chassis, use rubber grommets in the chassis holes. Without grommets, the sharp metal edge of the hole will eventually cut through the wire insulation. In a tube amp with 400V+ B+ supply, cut insulation means electrocution hazard.
- **Orientation**: Orient the power transformer and output transformer at 90 degrees to each other. Their magnetic fields will then be perpendicular, minimizing inductive coupling (hum). This is not a suggestion -- it is the standard practice for tube amps.
- **Spacing**: Keep the power transformer as far from the preamp tubes as possible. The preamp is a high-gain, sensitive circuit. The power transformer radiates a 50/60 Hz magnetic field that will induce hum in anything nearby.

### 4.6 Ventilation

Tube amplifiers dissipate significant heat. A pair of 6V6 output tubes in the [Ch 24](../part-3-amplifiers/24-build-tube-guitar-amp.md) build dissipates about 15-25 watts as heat. A pair of 6L6 tubes: 30-50 watts. That heat must go somewhere.

**Convection (passive cooling):**
- Tubes are mounted with their glass envelopes protruding above the chassis (through tube socket cutouts).
- Heat rises naturally from the tubes. The chassis top is open or perforated above the tubes.
- The chassis bottom should have ventilation holes or slots to allow cool air to enter from below.
- Allow at least 25mm of clearance above the tube envelope -- do not enclose tubes in a tight space.

**Forced air (active cooling):**
- For high-power tube amps or enclosed chassis designs, a small fan moves air across the tubes and components.
- Use a low-noise fan (Noctua 40mm or 60mm fans are excellent -- quiet and reliable).
- Position the fan to pull air across the hottest components (output tubes, power resistors).
- Fan noise must be below the noise floor of the amplifier. In a recording studio amp, even a quiet fan may be unacceptable.

**Ventilation patterns:**
- Louvered slots are traditional for amp chassis. Punch a row of rectangular slots with a louvered punch die.
- Round holes in a grid pattern work equally well and are easier to drill.
- Mesh or perforated panels provide maximum airflow and look professional.

> **What happens if...** you build a tube amp with no ventilation? The chassis temperature climbs, components get hot, electrolytic capacitors dry out (dramatically shortening their lifespan), resistor values drift, solder joints weaken, and eventually something fails. Tube amps without ventilation can reach internal temperatures of 80-100C, which is well above the rated temperature of most electrolytic capacitors (85-105C). A ventilated chassis keeps internal temps 30-40C lower.

---

## 5. 3D Printing for Audio

### 5.1 What to 3D Print (and What Not To)

3D printing (FDM -- fused deposition modeling -- the most common type) is excellent for:

- **Custom knobs**: Design knobs that match your aesthetic perfectly. Print in PLA or PETG, then paint or leave as-is.
- **Encoder mounting brackets**: The rotary encoder for the Pi streamer from [Ch 28](../part-4-digital-audio/28-build-pi-streamer.md) needs a bracket to mount it at the right height behind the front panel.
- **Display bezels**: The OLED display from the Pi streamer needs a bezel to frame it in the front panel and hold it at the correct depth.
- **Small enclosure prototypes**: Before committing to metal fabrication, print a prototype enclosure to check fitment and ergonomics.
- **Cable strain reliefs**: Custom strain reliefs for cables exiting enclosures.
- **Drill guides**: Print a guide that sits on the enclosure and has holes at the exact positions you need to drill. More accurate than a paper template.
- **Internal mounting brackets**: For mounting PCBs, batteries, or other components at odd angles inside an enclosure.

3D printing is NOT good for:

- **Full enclosures for pedals**: PLA and PETG do not provide electromagnetic shielding. Pedals in plastic enclosures will pick up noise from power supplies, fluorescent lights, and RF interference. Use metal enclosures for pedals.
- **Speaker enclosures**: 3D-printed walls are not dense or rigid enough for good acoustic performance, and printing a large enclosure takes days.
- **Heat sinks or thermal management**: PLA softens at 60C, PETG at 80C. Neither works near hot components.
- **Structural parts under mechanical stress**: 3D-printed parts are not as strong as machined metal. Do not use them for mounting heavy transformers or as load-bearing elements.

### 5.2 Filament Selection

| Filament | Print Temperature | Glass Transition | Strength | Use Case |
|----------|------------------|-----------------|----------|----------|
| PLA | 190-220C | ~60C | Moderate | Prototypes, display parts, knobs |
| PETG | 220-250C | ~80C | Good | Functional parts, brackets, bezels |
| ABS | 230-260C | ~100C | Good | Higher temp applications, needs enclosure during printing |
| ASA | 240-260C | ~100C | Good | Outdoor/UV-exposed parts |
| TPU | 210-230C | ~60C | Flexible | Gaskets, feet, strain reliefs |

**For most audio projects, PETG is the best choice.** It is stronger than PLA, handles higher temperatures, and prints almost as easily. Use PLA only for prototypes you do not plan to keep.

### 5.3 Design Tips for Printed Audio Parts

- **Wall thickness**: Minimum 1.2mm (3 perimeters at 0.4mm nozzle). For knobs, 2mm walls provide a solid feel.
- **Infill**: 40-60% for functional parts. 100% for small parts like knobs (gives them a satisfying weight).
- **Orientation**: Print knobs upside-down so the top surface (what you see and touch) is flat against the build plate -- this gives the best surface finish.
- **Tolerances**: FDM printing has tolerances of approximately +/- 0.2mm. For press-fit holes (like a knob onto a pot shaft), design the hole 0.1-0.2mm smaller than the shaft and test fit. Adjust and reprint as needed.
- **D-shaft flats**: Pot shafts are D-shaped (a circle with a flat). Your knob's bore needs to match. Design the D-shape slightly undersized and ream it to fit.

---

## 6. Laser Cutting

### 6.1 What to Laser Cut

Laser cutters excel at cutting flat sheet material with high precision. For audio projects:

- **Acrylic front panels**: Clear or colored acrylic with engraved text and cutouts for controls. A laser-cut acrylic front panel on an aluminum enclosure looks stunning.
- **MDF speaker baffle prototypes**: Before committing to a full cabinet build, laser-cut the baffle to verify driver cutout dimensions and layout.
- **Wood veneer pieces**: For decorative inlays on speaker cabinets.
- **Gaskets**: Cut cork or rubber gaskets for driver mounting or enclosure sealing.
- **Stencils**: For painting graphics on enclosures.

### 6.2 Design for Laser Cutting

Design files for laser cutting are typically vector files (SVG, DXF, or AI format):

- **Cut lines**: Vectors that the laser follows at full power, cutting through the material.
- **Engrave areas**: Vectors or raster areas that the laser traces at lower power, marking the surface without cutting through.
- **Kerf**: The laser removes material as it cuts (typically 0.1-0.2mm for a CO2 laser). Account for this in your dimensions. If you need a hole exactly 7mm for a pot shaft, make the design hole 7.1mm to account for kerf.

### 6.3 Services and Access

- **Local makerspaces**: Many have laser cutters available for member use. This is the most cost-effective option.
- **Ponoko**: Online laser cutting service. Upload your design, choose material, get a quote, receive cut parts by mail.
- **SendCutSend**: Similar to Ponoko, also does metal cutting.
- **Etsy custom fabricators**: Search for "custom laser cutting" and you will find individuals who will cut your designs at reasonable prices.

For front panels, laser-cut acrylic is an excellent option. Design the panel in Inkscape or Illustrator, specify cut lines for control holes and engrave lines for text and graphics. The engraved text is permanent, precise, and looks professional.

---

## 7. CNC Routing

### 7.1 What CNC Does Best for Audio

CNC (Computer Numerical Control) routing is essentially a computer-controlled router that carves shapes from flat stock. For audio:

- **Speaker baffles with precise driver cutouts**: A CNC router cuts a perfect circle to the exact diameter the driver datasheet specifies. No wobble, no off-center, no rough edges. If you build speakers regularly, CNC-cut baffles are a game changer.
- **Cabinet panels cut to exact dimensions**: Every panel from the same CAD file, every edge perfectly straight, every dimension identical. Batch production becomes trivial.
- **Flat-pack cabinet designs**: Design the entire cabinet as interlocking CNC-cut panels (using tab-and-slot joinery) that ship flat and assemble with glue. This is how some commercial speaker kits work, and it is ideal if you want to sell speaker kits (more on this in [Ch 42](../part-7-business/42-building-brand-selling.md)).
- **Radiused edges**: A CNC router can cut round-over edges on the baffle in the same operation as the driver cutout. No separate router pass needed.
- **Complex shapes**: Horn mouths, curved baffles, and other shapes that are impossible to cut by hand.

### 7.2 CNC Options

**Tabletop CNC routers** (Shapeoko, X-Carve, MPCNC):
- Work area: 300x300mm to 800x800mm depending on model.
- Cost: $400-2,000.
- Can cut MDF, plywood, softwoods, acrylic, and aluminum (slowly).
- Adequate for speaker baffles and small panels.

**Full-size CNC routers** (at makerspaces or professional shops):
- Work area: 1200x2400mm or larger (full sheet of plywood).
- Can cut all cabinet panels from a single sheet in one run.
- Access through makerspaces ($50-200/hour) or CNC service bureaus.

**CNC services**:
- Upload DXF or SVG files and receive cut parts.
- Ponoko, SendCutSend, and local CNC shops all offer this.
- Economical for one-off projects where you do not want to learn CNC operation.

### 7.3 CNC for Batch Production

CNC truly shines when you are making multiple units. Consider the workflow for producing 10 pairs of bookshelf speakers:

1. Design all panels in CAD. Nest them on a standard sheet of MDF (1220 x 2440mm) to minimize waste. Nesting software (like DeepNest, free and open source) automatically arranges parts for optimal material usage.
2. Generate G-code toolpaths.
3. Clamp the sheet to the CNC table.
4. Run the job. The CNC cuts all panels for 10 cabinets from two or three sheets, with identical dimensions on every piece.
5. Break panels free from tabs, sand tab marks, and you have a stack of perfectly matched parts.

Compare this to hand-cutting 60+ panels with a table saw. The CNC approach produces more consistent results, wastes less material, and scales linearly with time. This is the path to the batch production discussed in [Ch 43](../part-7-business/43-scaling-up.md).

### 7.4 Design for CNC

Design CNC toolpaths in software like:
- **Fusion 360** (free for hobbyists): Full 3D CAD + CAM. Design the part, generate toolpaths, export G-code.
- **VCarve** (Vectric): 2D/2.5D CAM software popular with CNC hobbyists. Good for speaker baffles and flat panels.
- **FreeCAD**: Open source, capable but steeper learning curve.

**Key CNC design parameters:**
- **Tool diameter**: The bit determines the smallest inside corner radius. A 6mm bit cannot cut a sharp inside corner -- it will leave a 3mm radius. Design with this in mind (dog-bone fillets at inside corners).
- **Depth per pass**: Do not try to cut 18mm MDF in one pass. Use 3-5mm depth per pass to avoid stressing the tool and getting rough cuts.
- **Tabs**: Small tabs hold the cut piece to the stock material so it does not fly off during the final pass. Break them off after the cut and sand smooth.
- **Feed rate**: How fast the tool moves through the material. Too fast = rough cuts, tool breakage. Too slow = burning. Follow the tool manufacturer's recommendations.

---

## 8. Designing for Assembly and Serviceability

### 8.1 The "Future You" Test

When designing an enclosure, ask yourself: "If this breaks in two years, can I fix it without destroying the enclosure?"

This is the audio equivalent of writing maintainable code. A beautifully sealed enclosure that requires desoldering 15 wires to access the PCB is a maintenance nightmare. A well-designed enclosure lets you:

- **Access the PCB** by removing a few screws.
- **Replace a component** without disassembling the entire unit.
- **Test the circuit** with probes while the unit is partially assembled.
- **Replace mechanical parts** (pots, switches, jacks) that wear out over time.

### 8.2 Design Principles

**Modular assembly**: Build the enclosure in sections that can be separated.
- Pedals: Remove the back plate (4 screws on a Hammond box) to access everything.
- Speakers: Use a removable back panel or terminal panel for access to the crossover and wiring.
- Amps: Use a removable bottom plate for chassis-top-mounted components, or a removable top for chassis-bottom-mounted components.

**Connectors over direct soldering**: Use pluggable connectors (pin headers, JST connectors, spade terminals) for connections between the PCB and panel-mounted components. This lets you separate the PCB from the enclosure without desoldering.

**Adequate clearance**: Leave enough space around components for fingers and tools. A PCB jammed into a too-small enclosure with 1mm clearance on all sides is impossible to work on. Plan for 5-10mm clearance around the PCB edges.

**Labeled internals**: Silkscreen labels on the PCB (component values, polarity marks, test points) and labels inside the enclosure (wire colors, connection points) make future troubleshooting dramatically easier.

**Test points**: Include test points on the PCB for key voltages and signals. When the circuit is inside the enclosure, you need a place to clip a scope probe. A test point is a plated pad with a label on the silkscreen. Add them at: power supply output, signal input, signal output, bias voltage, and any other node you might need to measure.

### 8.3 The Speaker Cabinet Access Panel

Speaker cabinets need periodic access for:
- Crossover component inspection or modification
- Driver replacement
- Damping material adjustment
- Wiring inspection

Options:
- **Removable back panel**: The entire back panel is screwed (not glued) to the cabinet. Use T-nuts embedded in the cabinet walls so the screws thread into metal, not MDF. Add a foam gasket between the back panel and the cabinet for airtightness.
- **Removable terminal cup**: A large terminal cup that also serves as an access hole when removed. Limited access but adequate for wiring inspection.
- **Removable driver**: The woofer or tweeter itself, when removed, provides access to the interior. Useful for crossover adjustments.

For the bookshelf speakers from [Ch 16](../part-2-speakers/16-build-bookshelf-speakers.md), a screwed-on back panel with a foam gasket is the best approach. It is airtight when assembled and provides full interior access when removed.

### 8.4 The Amplifier Chassis Layout

Amplifier chassis need to balance three competing requirements:
1. **Signal path**: Short, clean routing from input to output.
2. **Thermal management**: Hot components need airflow and heat sinking.
3. **Serviceability**: You need to reach every component for testing and replacement.

A common layout for the LM3886 chip amp from [Ch 23](../part-3-amplifiers/23-build-chip-amp-class-d.md):

```
┌──────────────────────────────────────────┐
│ FRONT PANEL                              │
│  [Input]  [Vol]  [Selector]   [Power]    │
├──────────────────────────────────────────┤
│                                          │
│  PCB mounted on standoffs                │
│  ┌────────────────────────┐              │
│  │ Input → Preamp → Amp   │              │
│  │                        │              │
│  │ Power Supply Section   │  Transformer │
│  └────────────────────────┘              │
│                                          │
│  Heat sink on rear panel ─────────────── │
├──────────────────────────────────────────┤
│ REAR PANEL                               │
│  [Speaker Out L] [Speaker Out R] [IEC]   │
└──────────────────────────────────────────┘
```

The PCB sits in the middle on standoffs. The transformer is in one corner (away from the input). The heat sink is on the back panel (outside the chassis for maximum cooling). The front panel has controls, the back panel has speaker outputs and the IEC mains inlet. Remove the bottom plate to access the PCB.

> **What happens if...** you design an enclosure that is beautiful but impossible to service? You (or your customer, if you are selling builds per [Ch 42](../part-7-business/42-building-brand-selling.md)) will eventually need to repair it. If repair means destroying the enclosure, the build is not professional -- it is a sealed time bomb. Professional audio gear is designed to be serviced. Your builds should be too.

---

## 9. Enclosure Design Software

### 9.1 CAD Tools for Enclosure Design

You do not need to design enclosures by hand-drawing dimensions on paper (though that works for simple projects). Software makes iteration faster and prototyping more reliable.

**Fusion 360** (free for personal/hobby use):
- Full 3D parametric modeling. Design the enclosure, the PCB mounting, the panel cutouts -- everything in one model.
- Parametric means you can change a dimension and the entire model updates. If you decide to switch from a 1590B to a 125B enclosure, change the outer dimensions and all internal features adjust.
- Export to STL for 3D printing, DXF for laser cutting, STEP for CNC.
- KiCad can export a STEP file of the PCB with components. Import this into Fusion 360 to verify the PCB fits inside the enclosure. This is the hardware equivalent of integration testing.

**FreeCAD** (free, open source):
- Full parametric 3D CAD. More complex interface than Fusion 360 but no cloud dependency.
- Good for users who prefer open-source tools.
- Exports to all standard formats.

**Inkscape** (free, open source, 2D):
- For drilling templates, panel layouts, and laser cutting designs.
- Vector-based, so designs scale perfectly.
- Export to SVG (laser cutter), PDF (printing), DXF (CNC).

**SketchUp** (free web version):
- Easy to learn 3D modeling. Good for speaker cabinet visualization.
- Less precise than Fusion 360 for dimensioned manufacturing drawings.
- Useful for quick "does this look right?" checks.

### 9.2 Designing for Manufacturing Tolerances

Every manufacturing process has tolerances. Your design must account for them:

| Process | Typical Tolerance | Implication |
|---------|------------------|-------------|
| Hand drilling (with template) | +/- 1mm | Allow 2mm clearance around drilled holes |
| Drill press | +/- 0.3mm | More precise, but still allow 1mm clearance |
| Laser cutting | +/- 0.1mm | Very precise, design to tight tolerances |
| CNC routing | +/- 0.1-0.3mm | Precise, but tool diameter limits inside corners |
| 3D printing (FDM) | +/- 0.2mm | Plan for iteration on tight-fit parts |
| PCB manufacturing | +/- 0.1mm | Highly precise, trust the design files |
| Hammond enclosure dimensions | +/- 0.5mm | Measure your actual box, do not trust catalog dimensions |

The golden rule: **measure the actual parts, design to the actual measurements, and leave clearance for error.** A PCB designed to fit a 1590B with 0.5mm clearance on each side will fit some boxes and not others, because Hammond's manufacturing tolerance is in that range. Design with 2mm clearance and you will never have fitment problems.

### 9.3 Iterative Design

The best enclosure designs come from iteration:

1. **Version 0** (cardboard mockup): Cut a cardboard box to approximate size. Place the PCB and components inside. Check clearances, reach, and general layout. This takes 15 minutes and catches major problems before you commit any real material.
2. **Version 1** (3D-printed prototype or first metal drill): Build the real enclosure based on your measurements. Fit the PCB. Mark what does not work.
3. **Version 2** (corrected): Fix the issues from version 1. This is usually the version you keep.

Do not skip the mockup. A $0 cardboard box will tell you that your PCB is 3mm too wide before you discover it by ruining an $8 aluminum enclosure.

> **What happens if...** you design an enclosure entirely in CAD, trust the model, and order 50 units without building a prototype? You find out that the pot shafts are 2mm too high because you measured the pot from the wrong reference point. Now you have 50 enclosures with holes in the wrong position. The cost of a single prototype is trivial compared to the cost of a batch error. Always prototype.

---

## 10. Enclosure Design Workflow Summary

### 10.1 The Process

Regardless of what you are building, the enclosure design workflow follows the same pattern:

1. **Measure what goes inside**: PCB dimensions, component heights, connector positions, wire routing space.
2. **Choose the enclosure type**: Pre-made box, custom fabrication, or 3D-printed prototype.
3. **Design the layout**: Position components, connectors, and controls. Check for clearances and conflicts.
4. **Create fabrication drawings or templates**: Drilling templates for metal, cut lists for wood, CAD files for CNC.
5. **Prototype**: Build one, check fitment, identify issues.
6. **Revise**: Fix the issues and build the final version.
7. **Document**: Record dimensions, hole positions, and assembly steps for future reproduction.

### 10.2 Enclosure Checklist

Before finalizing any enclosure design:

- [ ] PCB fits with adequate clearance on all sides
- [ ] All connectors are accessible from outside
- [ ] Controls are reachable and logically positioned
- [ ] Ventilation is adequate for heat-generating components
- [ ] Mounting hardware does not interfere with internal components
- [ ] Wires have routing paths that do not cross sensitive circuits
- [ ] The enclosure can be opened for service without destruction
- [ ] The enclosure can be closed and sealed after service
- [ ] Electromagnetic shielding is adequate (metal for analog audio)
- [ ] The enclosure fits the intended use environment (ruggedized for stage, refined for home)

With the enclosure designed and fabricated, the next step is making it look professional. [Chapter 33](33-professional-finishing-aesthetics.md) covers finishes, graphics, and the aesthetic details that separate a DIY project from a commercial product.
