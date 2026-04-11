<!--
  CHAPTER: 40
  TITLE: Sourcing and Supply Chain
  PART: 7 — From Hobby to Hustle
  PREREQS: Any domain tower
  KEY_TOPICS: electronics suppliers, audio-specific suppliers, PCB manufacturers, bulk pricing, lead time planning, component substitution, preferred supplier list, quality vs cost trade-offs
  DIFFICULTY: Beginner
  UPDATED: 2026-04-10
-->

# Chapter 40: Sourcing and Supply Chain

> **Part 7 — From Hobby to Hustle** | Prerequisites: Any domain tower | Difficulty: Beginner

Up until now, you've been buying parts the way most hobbyists do: searching for the exact component a build guide tells you to buy, adding it to a cart on whatever website comes up first, wincing at the shipping cost, and waiting. That works fine when you're building one pedal for yourself. It falls apart the moment you want to build ten for other people. The difference between a hobbyist and a small-scale builder isn't skill — it's supply chain. Knowing *where* to buy, *when* to buy, and *how much* to buy is the unsexy infrastructure that makes everything else possible.

This chapter is your supplier rolodex. We'll cover every category of part you'll need — from resistors to rare-earth magnets, from PCBs to powder-coated enclosures — and tell you exactly where to get them, what to expect, and what the trade-offs are. Think of this as the chapter you'll bookmark and come back to every time you start a new project. By the end, you'll have a preferred supplier for every category, a backup for each, and a strategy for buying that saves you real money at scale.

If you're a software engineer, think of this as dependency management for physical products. You're picking your registries, pinning your versions, and making sure your build doesn't break because a critical package disappeared from npm overnight. Except here, the packages are capacitors, and "npm install" takes two weeks from Shenzhen.

### In This Chapter
- General electronics suppliers and when to use each
- Audio-specific suppliers for speakers, tubes, and crossover components
- Mechanical and hardware suppliers for enclosures, connectors, and chassis
- PCB manufacturers compared: cost, quality, and turnaround
- The role of Amazon and AliExpress (and their risks)
- Bulk pricing breakpoints and how to exploit them
- Lead time planning for critical components
- When component substitution is safe and when it isn't
- Building and maintaining a preferred supplier list
- Quality vs cost: where to splurge and where to save

### Related Chapters
- [Ch 6: Gain Stages and Clipping Circuits](../part-1-guitar-pedals/06-gain-stages-clipping-circuits.md) — Component selection for pedal circuits
- [Ch 12: The Language of Speakers](../part-2-speakers/12-language-of-speakers.md) — Speaker driver specs and sourcing
- [Ch 18: How Amplifiers Work](../part-3-amplifiers/18-how-amplifiers-work.md) — Tube and transformer sourcing context
- [Ch 41: Costing and Pricing](41-costing-pricing.md) — Turning supplier costs into product prices
- [Ch 43: Scaling Up](43-scaling-up.md) — Supply chain at volume

---

## 1. General Electronics Suppliers

These are your bread-and-butter distributors — the places you'll buy resistors, capacitors, diodes, transistors, ICs, and all the other small components that go into every build. Each has its personality, and knowing which to reach for saves time and money.

### 1.1 Mouser Electronics

**Website:** mouser.com

Mouser is the workhorse. They carry over 6.8 million unique part numbers from more than 1,200 manufacturers, and their US warehouse (Mansfield, Texas) ships fast — typically same-day for orders placed before 8 PM Central. Their stock levels are accurate and updated in real time, which matters more than you think. Nothing kills momentum like ordering 50 op-amps only to get an email saying they're backordered for twelve weeks.

**Best for:** Prototyping and one-off builds where you need a specific part. If a datasheet says "use a TL072," Mouser has seventeen variants of it in stock right now. Their parametric search is solid — you can filter by every spec in the datasheet.

**Pricing:** Fair for single quantities, with price breaks at 10, 25, 100, and 1,000. Shipping is free on orders over $50 in the US, which you'll hit easily. A typical pedal's worth of components runs $15-40 depending on the circuit.

**Watch out for:** Minimum order quantities on some components (usually 1, but occasionally higher for specialty parts). Their website can be overwhelming — use the parametric search rather than the text search.

### 1.2 Digi-Key

**Website:** digikey.com

Digi-Key is Mouser's fraternal twin. Similar selection (13+ million parts), similar US-based shipping speed (Thief River Falls, Minnesota), similar pricing. The real difference is in the search experience. Digi-Key's parametric search is arguably the best in the industry — if you know exactly what specs you need but not the specific part number, Digi-Key will find it faster.

**Best for:** When you need to find a part by specification rather than by name. "I need a film capacitor, 100nF, 5% tolerance, 50V minimum, radial leads, under $0.50" — Digi-Key's filters make this a 30-second task.

**Pricing:** Nearly identical to Mouser. They also offer free shipping on orders over $50 in the US. The same pedal's worth of components will cost roughly the same.

**Mouser vs Digi-Key:** Honestly, flip a coin. Most builders develop a preference based on which site's UI they like better. I keep accounts on both because sometimes one has stock of a part the other doesn't. The real answer is: pick one as your primary and use the other as a backup.

### 1.3 LCSC

**Website:** lcsc.com

LCSC is the electronics arm of JLCPCB (the PCB manufacturer), and it's where things get interesting for cost. LCSC stocks components from Chinese manufacturers, and the prices can be dramatically lower — we're talking 1/5 to 1/10 the price of Mouser for equivalent parts. A 100-pack of 1/4W metal film resistors that costs $3.50 on Mouser costs $0.35 on LCSC.

**Best for:** Bulk orders where you're building quantity. When you're ordering 100 of the same resistor value, 50 of the same capacitor, and 25 of the same IC, LCSC's pricing adds up to serious savings. They're also the default parts library for JLCPCB's assembly service, so if you're having PCBs assembled, sourcing from LCSC keeps everything in one ecosystem.

**Shipping:** This is the trade-off. Ships from Shenzhen, China. Standard shipping takes 1-2 weeks to the US, 2-3 weeks to Europe. Express (DHL/FedEx) cuts that to 3-5 days but costs $15-25. Plan accordingly.

**Watch out for:** Datasheets are sometimes only available in Chinese. Component quality from no-name manufacturers can vary. For signal-path components where specs matter (precision resistors, low-noise op-amps), I'd stick with known brands even on LCSC — they carry TI, Analog Devices, and other name-brand parts alongside budget alternatives.

### 1.4 Tayda Electronics

**Website:** taydaelectronics.com

Tayda is the pedal builder's best friend. They stock exactly the components you need for guitar effects — discrete resistors, film and electrolytic capacitors, diodes, transistors, potentiometers, 1/4" jacks, 3PDT footswitches, LED bezels, and knobs. They also sell pre-drilled and powder-coated 125B and 1590B enclosures, which saves you significant effort.

**Best for:** Guitar pedal builds, full stop. If you're building a Tube Screamer clone, you can source probably 90% of the BOM from Tayda at prices that beat Mouser by 30-60%. Their potentiometers are $0.60-1.00 each (vs $3-5 on Mouser), 3PDT footswitches are $1.50-2.00 (vs $5-8 on Mouser), and enclosures start at $3-4 bare or $5-7 powder-coated.

**Shipping:** Ships from Thailand. Standard shipping takes 2-3 weeks, express 1-2 weeks. Shipping cost is reasonable ($3-8 depending on weight). Most pedal builders do a big Tayda order every month or two and keep a stash.

**Watch out for:** Component selection is limited compared to Mouser/Digi-Key. They carry what pedal builders need and not much else. If your circuit calls for something unusual — a specific JFET, a rare op-amp, a precision voltage reference — you'll need to go elsewhere. Also, their potentiometer shafts are sometimes slightly different from brand-name pots, so check knob compatibility.

---

## 2. Audio-Specific Suppliers

General electronics distributors carry components. Audio-specific suppliers carry *products* — complete speaker drivers, crossover components designed for audio, tube amp transformers, and parts that only matter if you're building something people listen through.

### 2.1 Parts Express

**Website:** partsexpress.com

Parts Express is THE audio-specific supplier. If you're building speakers, they're your first stop — period. They carry a massive selection of Dayton Audio drivers (their house brand, excellent value), plus drivers from SB Acoustics, Peerless, HiVi, and others. But it's not just drivers. They have crossover components (inductors, capacitors, resistors — all rated for speaker-level power), binding posts, speaker terminals, damping material, port tubes, and even flat-pack cabinet kits.

**Best for:** Speaker builds of all types. A pair of bookshelf speakers built with Dayton Audio drivers and Parts Express crossover components can cost $100-300 in materials and rival commercial speakers costing 5-10x more. They also carry amplifier boards (Class D plate amps, chip amp modules), PA equipment, and pro audio supplies.

**Pricing:** Very competitive, especially on Dayton Audio products. They run sales frequently — Black Friday and their annual Clearance Sales are legendary in the DIY speaker community. Sign up for their email list. A $15 driver occasionally shows up for $8.

**Shipping:** US warehouse, fast shipping. Free shipping on orders over $99. Their packaging is excellent — they know they're shipping fragile speakers and pack accordingly.

**Community bonus:** Parts Express sponsors speaker design competitions, maintains the Tech Talk forums, and publishes build guides. They're genuinely invested in the DIY audio community.

### 2.2 Madisound

**Website:** madisound.com

If Parts Express is the home improvement store of speaker suppliers, Madisound is the boutique. They specialize in premium drivers — SEAS, Scan-Speak, SB Acoustics, Dayton Audio's Reference series, Purifi, and more. If you're designing a $1,500 pair of speakers with exotic drivers, Madisound probably has them.

**Best for:** High-end speaker builds. When you want a SEAS Excel woofer or a Scan-Speak Revelator tweeter, Madisound is often the only US-based source. They also carry premium crossover components — Mundorf capacitors, Jantzen inductors — the kind of parts that audiophiles argue about on forums.

**Pricing:** Premium. A single Scan-Speak midrange driver can run $100-300. But you're paying for quality that's measurable on the test bench and audible in the room. At this tier, the driver *is* the product.

**Community bonus:** Madisound publishes recommended crossover designs for many of the drivers they sell, which saves you enormous design time.

### 2.3 Meniscus Audio

**Website:** meniscusaudio.com

Meniscus specializes in crossover components. They carry a curated selection of audio-grade capacitors (Audyn, Mundorf, ClarityCap), inductors (Jantzen, Mundorf), and resistors (Mills, Mundorf) — everything you need to build a quality crossover network. They also carry a selection of drivers and publish crossover designs.

**Best for:** When you need specific crossover components that Parts Express doesn't carry. Meniscus is where you go when your crossover design calls for a Mundorf Supreme EVO Silver/Gold capacitor and you can't find it anywhere else in the US.

### 2.4 Antique Electronic Supply (AES) / Tube Depot

**Website:** tubesandmore.com (AES), tubedepot.com

These are your tube amp suppliers. Vacuum tubes, output transformers, power transformers, tube sockets, turret boards, eyelet boards, chassis — everything you need to build or rebuild a tube amplifier.

**AES (Antique Electronic Supply)** is the larger of the two and carries a broader selection. They stock tubes from JJ, Electro-Harmonix, Tung-Sol, Mullard reissues, and occasionally genuine NOS (new old stock) tubes. Their transformer selection includes Hammond, ClassicTone, and Mercury Magnetics.

**Tube Depot** has a more curated selection and offers complete amp kits (Fender Tweed Deluxe, Marshall 18W, Vox AC15 clones, etc.) that include everything you need down to the last screw. If you're building your first tube amp, a Tube Depot kit is a great way to start.

**Pricing:** Tubes are $10-50 each depending on type (a 12AX7 preamp tube runs $12-18, a pair of matched EL34 power tubes runs $35-60). Output transformers are $40-150 for standard sizes. A complete tube amp build runs $300-800 in parts depending on wattage and features.

**Lead time warning:** Transformers can take 4-8 weeks if they're not in stock. Mercury Magnetics transformers are basically always made to order. Plan ahead. If you are building tube amps commercially, keep one output transformer and one power transformer in stock at all times for your standard models -- a customer waiting 6 weeks because you forgot to order a transformer is a customer who cancels.

**Pro tip:** Tube Depot occasionally runs "blemished tube" sales where cosmetically imperfect (but electrically perfect) tubes sell for 30-50% off. Sign up for their email alerts. A "scratched label" EL34 sounds exactly like a pristine one.

---

## 3. Mechanical and Hardware Suppliers

### 3.1 Hammond Manufacturing

**Website:** hammondmfg.com (typically purchased through Mouser, Digi-Key, or Tayda)

Hammond makes the die-cast aluminum enclosures that the entire guitar pedal industry runs on. The 1590B (roughly 4.4" x 2.3") and 125B (roughly 4.7" x 3.7") are the two most popular sizes. They're rugged, easy to drill, provide good electromagnetic shielding, and look professional when finished.

**Key sizes for pedal builders:**
- **1590A** — The tiny one. ~3.6" x 1.5". Mini pedals, single-knob effects.
- **1590B** — The standard. ~4.4" x 2.3". Overdrives, fuzzes, most single effects.
- **125B** — The medium. ~4.7" x 3.7". Effects with more controls, dual circuits.
- **1590BB** — The big one. ~4.7" x 3.7" x 1.5" (taller than 125B). Complex multi-effects, stereo builds.
- **1032L** — Rack-mount chassis for amplifier and preamp builds.

**Pricing:** $5-12 each from Mouser/Digi-Key for bare aluminum. Tayda sells them cheaper. Pre-drilled and powder-coated enclosures from Tayda's custom shop run $7-12 and save hours of work per unit.

### 3.2 McMaster-Carr

**Website:** mcmaster.com

McMaster-Carr sells everything mechanical. Screws, standoffs, rubber feet, washers, aluminum sheet, steel brackets, adhesives, heat-shrink tubing, terminal strips, grommets — if it's a physical thing that isn't an electronic component, McMaster probably has it with same-day shipping and a CAD drawing you can download.

**Best for:** Hardware for speaker cabinets and amplifier chassis. When you need M3 standoffs exactly 12mm long in stainless steel with female threads on both ends, McMaster has fourteen options. Their search is phenomenal.

**Pricing:** Not the cheapest. You're paying for selection, speed, and reliability. For one-off builds, the convenience is worth it. For production runs, find the same part cheaper on Amazon or from a specialty fastener supplier.

### 3.3 Neutrik

**Website:** neutrik.com (purchased through Mouser, Digi-Key, or B&H)

Neutrik is the industry standard for professional audio connectors. Their XLR connectors, speakON connectors, 1/4" jacks, and combo connectors are what you'll find in every professional audio device on the planet. When you build a power amplifier with Neutrik connectors, people trust it. When you build one with cheap Chinese connectors, people wonder what else you cheapened.

**Key products:**
- **NJ3FP6C** — 1/4" stereo jack, panel mount. The standard for guitar/instrument jacks. ~$3 each.
- **NC3FD-L-1** / **NC3MD-L-1** — XLR female/male panel mount. ~$3-4 each.
- **NL4MP** — speakON panel mount. ~$4-5 each. Required for any serious speaker/amp connection.
- **NCJ6FA-H** — Combo XLR/1/4" jack. ~$7-8 each. Use for versatile input panels.

**Switchcraft** is the alternative — also professional quality, slightly different mechanical design. Some builders prefer the Switchcraft 11 open-frame 1/4" jack for pedals because it's easier to solder and wire.

---

## 4. PCB Manufacturers

If you've designed your own PCB in KiCad (covered in Part 5), you need someone to fabricate it. The PCB manufacturing industry has been commoditized to a remarkable degree — you can get professional-quality boards for pennies.

### 4.1 JLCPCB

**Website:** jlcpcb.com

JLCPCB is the price leader. Five copies of a standard 2-layer PCB (up to 100x100mm) for $2. Yes, two dollars. Add $1 for shipping and you've got five professional boards for $3. Turnaround is 1-3 days for production, plus 5-10 days for standard shipping from China.

**Best for:** Everything, honestly. Prototyping, production runs, simple boards, complex boards. Their quality is consistently good. Green solder mask is cheapest; other colors (black, white, blue, red) add $1-2.

**PCBA (PCB Assembly):** This is where JLCPCB really shines for scaling up. Upload your BOM and pick-and-place file, and they'll solder SMD components onto your boards for you. Assembly setup is $8, plus per-component costs (typically $0.002-0.02 per SMD part placed). For a pedal PCB with 30 SMD components, assembly adds maybe $10 per board on top of the PCB cost. That saves you an hour of SMD soldering. At 10+ units, it's a no-brainer.

**Components:** JLCPCB sources from LCSC, so check component availability on LCSC *before* submitting your PCBA order. Some parts labeled "Extended" incur a small surcharge. "Basic" parts are cheapest.

### 4.2 PCBWay

**Website:** pcbway.com

PCBWay is JLCPCB's main competitor and offers more options for specialty work — heavy copper, rigid-flex, aluminum substrate (for LED boards), and custom stencils. Their pricing is slightly higher than JLCPCB for standard boards ($5 for 5 boards) but still incredibly cheap.

**Best for:** When you need something JLCPCB doesn't offer, or when you want better customer service for complex orders. PCBWay assigns a project manager to each order and they'll email you if they spot issues in your Gerbers.

**Purple solder mask:** PCBWay is known for their purple boards. It's become a recognizable aesthetic in the DIY audio community.

### 4.3 OSH Park

**Website:** oshpark.com

OSH Park is the premium, US-based option. Boards are manufactured in the US with a distinctive purple solder mask and gold (ENIG) finish. Quality is excellent. Pricing is $5 per square inch for 2-layer boards (so a typical pedal board at 2" x 3" = 6 sq in = $30 for three copies).

**Best for:** Small prototype runs where you want US-based manufacturing and don't want to wait for shipping from China. Also good for boards where ENIG finish matters (fine-pitch SMD, impedance-controlled traces). Not cost-effective for production.

### 4.4 Quick Comparison

| Feature | JLCPCB | PCBWay | OSH Park |
|---------|--------|--------|----------|
| 5x 2-layer PCBs (100x100mm) | $2 | $5 | ~$30 (3 copies) |
| Turnaround | 1-3 days | 2-5 days | ~12 days |
| Shipping (to US) | 5-10 days | 5-10 days | Included |
| Assembly (PCBA) | Yes, excellent | Yes, good | No |
| Location | China | China | USA |
| Best for | Production | Specialty | Prototyping (US) |

---

## 5. Amazon, AliExpress, and Opportunistic Buying

### 5.1 When They're Fine

Amazon and AliExpress have their place. Here's when to use them:

- **Tools.** Soldering irons, wire strippers, drill bits, clamps, heat guns — Amazon is great for tools. Buy the mid-range option (not the cheapest, not the most expensive).
- **Non-critical supplies.** Solder wick, flux, heat-shrink tubing, cable ties, storage bins. These don't need to be precision components.
- **Commodity wire.** Hook-up wire, speaker wire (for non-critical applications), patch cables.
- **LEDs.** Standard indicator LEDs are fine from Amazon. A bag of 100 5mm LEDs for $3 will work perfectly for pedal indicators.
- **Consumables.** Sandpaper, masking tape, adhesives, rubber feet, velcro.

### 5.2 When They're Not Fine

- **Precision components.** That bag of "1% metal film resistors" from AliExpress? Maybe they're 1%. Maybe they're 5%. You have no way to verify without measuring every one. For crossover networks where a 1% resistor needs to actually be 1%, buy from Mouser.
- **Active components.** Fake op-amps, counterfeit transistors, and re-marked ICs are rampant on AliExpress and occasionally on Amazon. If you buy an "LM4562" ultra-low-noise op-amp from AliExpress for $0.50 and it sounds mediocre, it's probably an NE5532 with a new label. Or worse.
- **Tubes.** Absolutely never buy vacuum tubes from Amazon or AliExpress. Counterfeit tubes are everywhere, and a fake power tube can damage your output transformer — which is a $100+ mistake.
- **Connectors branded as Neutrik.** If it says "Neutrik" on AliExpress for 1/4 the price, it's not Neutrik. It's a counterfeit that will fail.
- **Capacitors in the signal path.** Cheap electrolytics from unknown brands have higher leakage, lower lifespan, and worse performance than branded parts from Mouser.

### 5.3 The Rule of Thumb

If the component is in the signal path or handles significant power, buy it from a reputable distributor. If it's a screw, a knob, or a piece of wire, buy it wherever it's cheapest.

### 5.4 When AliExpress Is Actually Great

Despite the warnings above, AliExpress is genuinely excellent for specific categories:

**TPA3255 and TPA3116 amplifier boards ($15-40).** These are complete Class D amplifier modules with the TI chip, power supply section, and connectors on a pre-built PCB. The Chinese boards are essentially identical to each other (they all use the same reference design from TI's datasheet), and they measure very well. The TPA3255 board you buy for $25 from AliExpress performs identically to one you would build yourself for $40-50 in parts. This is one of the few cases where the cheap option is also the correct option.

**DSP boards based on ADAU1701/ADAU1467 ($20-60).** MiniDSP-style boards from Chinese makers like Sure Electronics or generic ADAU1701 boards provide real DSP processing for a fraction of miniDSP's retail price. These are useful for active crossovers, speaker protection limiters, and EQ in PA speakers. The caveat: documentation is often poor or Chinese-only. If you are comfortable reading schematics and writing DSP configs from datasheets, these boards are incredible value.

**Bluetooth receiver modules ($8-20).** The CSR8675 and QCC5125-based Bluetooth modules support aptX HD and LDAC codecs with very respectable audio quality. These are commodity modules -- the audio performance is determined by the Qualcomm chip, not the board manufacturer. A $12 module from AliExpress sounds the same as a $40 one from a US retailer because it uses the same chip.

**LED strips, ring lights, and indicator components.** For visual elements in your builds (illuminated knobs, VU meter backlighting, enclosure accent lighting), AliExpress is the go-to. WS2812B addressable LED strips are $3-8 per meter, and they work perfectly for adding visual flair to an amplifier chassis or a speaker grille.

**Knobs, feet, and decorative hardware.** Machined aluminum knobs that look identical to $5-8/each knobs on US sites sell for $1-2/each on AliExpress. Rubber feet, self-adhesive bumpers, and decorative screws are similarly cheap. These are cosmetic items where brand provenance does not matter.

---

## 6. Bulk Pricing and Breakpoints

One of the biggest differences between hobby buying and business buying is understanding pricing tiers. Every component on Mouser, Digi-Key, and LCSC has price breaks — and they can be dramatic.

### 6.1 Typical Price Break Structure

Let's look at a real example: a common 1/4W metal film resistor (say, 10k ohm) on Mouser:

| Quantity | Unit Price | Total |
|----------|-----------|-------|
| 1-9 | $0.10 | $0.10-0.90 |
| 10-49 | $0.065 | $0.65-3.19 |
| 50-99 | $0.042 | $2.10-4.16 |
| 100-499 | $0.025 | $2.50-12.48 |
| 500-999 | $0.017 | $8.50-16.83 |
| 1,000+ | $0.012 | $12.00+ |

That's an 88% price reduction from single-unit to 1,000+. Even the jump from 1 to 100 is a 75% reduction.

### 6.2 How to Exploit Breakpoints

**Strategy 1: Standardize your designs.** If every pedal you build uses 10k, 47k, and 100k resistors (and most do), you can buy all three values in bulk. Ten different pedal designs using the same five resistor values means you're buying 500 at a time even if you only build 50 of each pedal.

**Strategy 2: Round up to the next breakpoint.** If you need 80 capacitors, buy 100. The extra 20 cost almost nothing at the breakpoint price, and you'll use them eventually.

**Strategy 3: Consolidate orders.** Don't order five parts from Mouser on Monday, three more on Wednesday, and two on Friday. Batch your orders. Keep a running wish list and place one order when it hits the free shipping threshold. This is basic, but hobbyists turned builders forget it constantly.

**Strategy 4: Share orders with other builders.** The DIY audio community is collaborative. If you and two other builders each need 50 of the same op-amp, combine into one 150-unit order and split the savings.

### 6.3 The Math Matters More Than You Think

Let's say you build a guitar pedal with 40 components. At hobby (single-unit) prices from Mouser, your BOM might be $25. At 100-unit breakpoint prices, that same BOM drops to $12-15. Over 50 pedals, that's $500-650 in savings — just from buying smarter. This is before you even consider LCSC pricing for the same parts.

### 6.4 Typical Component Costs: Pedal BOM at Three Price Tiers

Here is what the same overdrive pedal BOM looks like at three different buying strategies:

| Component Category | Hobby (1-9 qty, Mouser) | Smart Hobby (Tayda + Mouser) | Batch (100+ qty, LCSC + Tayda) |
|-------------------|------------------------|----------------------------|-------------------------------|
| Resistors (15x) | $1.50 | $0.45 | $0.15 |
| Film capacitors (6x) | $3.00 | $0.72 | $0.30 |
| Electrolytic caps (4x) | $2.00 | $0.32 | $0.20 |
| Ceramic caps (4x) | $0.60 | $0.20 | $0.08 |
| Op-amp (JRC4558D) | $2.50 | $0.45 | $0.25 |
| Diodes (3x) | $0.45 | $0.09 | $0.06 |
| Transistor (1x) | $0.30 | $0.04 | $0.03 |
| Potentiometers (3x) | $12.00 | $2.07 | $1.80 |
| Footswitch (3PDT) | $6.50 | $1.65 | $1.35 |
| Jacks (2x 1/4") | $6.00 | $1.10 | $0.90 |
| DC jack | $1.50 | $0.25 | $0.20 |
| Enclosure (125B, powder-coated) | $10.00 | $6.50 | $5.50 |
| Knobs (3x) | $6.00 | $2.25 | $1.50 |
| PCB | $6.00 (OSH Park) | $0.40 (JLCPCB) | $0.25 (JLCPCB) |
| LED + bezel + resistor | $0.75 | $0.23 | $0.15 |
| Wire, solder, consumables | $2.00 | $1.00 | $0.65 |
| **Total BOM** | **$59.10** | **$17.72** | **$13.37** |

The difference between the hobby tier and the smart-hobby tier is staggering: $59 versus $18 for the exact same pedal. The smart-hobby builder uses Tayda for the common parts and JLCPCB for the PCB. The batch builder pushes further by buying at breakpoints from LCSC and negotiating with Tayda for bulk enclosure pricing.

### 6.5 Typical Component Costs: Speaker BOM at Two Tiers

| Component Category | Hobby (Parts Express, single qty) | Batch (Parts Express sale + bulk) |
|-------------------|----------------------------------|----------------------------------|
| Woofers (2x Dayton RS150-8) | $59.60 | $49.80 (sale price or 4+ qty) |
| Tweeters (2x Dayton DC28F-8) | $42.60 | $36.20 (sale price) |
| Crossover components (both speakers) | $38.10 | $32.00 (bulk pricing on caps/inductors) |
| MDF (1/4 sheet) | $10.00 | $8.00 (buying full sheets for multiple pairs) |
| Hardware (binding posts, port tubes, screws, feet) | $18.32 | $14.50 |
| Finishing (vinyl wrap, contact cement) | $10.00 | $8.00 |
| Damping material | $4.00 | $3.00 |
| Wire, solder, consumables | $2.00 | $1.50 |
| **Total BOM (pair)** | **$184.62** | **$153.00** |

Drivers dominate the speaker BOM at any volume. The best way to reduce speaker BOM cost is to buy drivers during Parts Express sales (they run 15-25% off on Dayton Audio products 3-4 times per year). Sign up for their email list and buy when the sale hits.

### 6.6 Evaluating Component Quality: What to Look For

Not all components with the same specifications are created equal. Here is how to tell the difference:

**Resistors:** A "10k 1% metal film" from Mouser (brand: Yageo, Vishay, KOA) and one from LCSC (brand: UNI-ROYAL or similar Chinese manufacturer) will both measure within 1% of 10k. For most applications, they are interchangeable. But measure a batch of 100 from each: the Yageo resistors will cluster tightly around 10.00k (typically within 0.3%), while the budget Chinese resistors may spread across the full 1% band. This matters only for precision applications (crossover networks, measurement circuits). For pull-down resistors and LED limiting, it is irrelevant.

**Capacitors:** Film capacitors from established manufacturers (WIMA, Panasonic, Epcos/TDK) have tight tolerances, low ESR, and long lifespans. Budget film caps from unknown brands may meet their rated capacitance when new but drift more with age and temperature. For signal-path audio capacitors (tone circuits, coupling caps), use branded parts. For bypass and filtering, anything that meets the voltage and capacitance rating is fine.

**Potentiometers:** The difference between a $0.70 Alpha pot (Tayda) and a $4.00 Bourns pot (Mouser) is in the wiper quality, taper accuracy, and lifespan. Alpha pots feel slightly scratchy when new and develop noise after 500-1,000 rotations. Bourns pots feel smooth and last 10,000+ rotations. For a $125 budget pedal, Alpha is acceptable. For a $200+ premium pedal, use Bourns — the feel of the knob is one of the first things a buyer notices.

**Connectors:** Genuine Neutrik vs counterfeit: genuine connectors have the Neutrik logo laser-etched (not printed), the contacts are bright silver or gold, the body has no mold flash, and the locking mechanism clicks firmly. Counterfeits have printed logos that rub off, dull contacts, visible mold seams, and a mushy locking feel. Buy from authorized distributors only.

---

## 7. Lead Time Planning

### 7.1 The Critical Path

In project management terms, your "critical path" is the longest sequence of dependent tasks. In audio building, it's usually the part with the longest lead time. Everything else can wait, but if your output transformers take 6 weeks, you'd better order them before you start anything else.

**Components with long lead times:**
- **Output and power transformers** (tube amps): 2-8 weeks, depending on manufacturer. Hammond transformers are usually in stock at distributors. ClassicTone: 2-4 weeks. Mercury Magnetics: 4-8 weeks (handmade to order).
- **Custom speaker drivers:** If you're ordering from a manufacturer with a custom spec (different impedance, different cone material), expect 6-12 weeks and a minimum order quantity (MOQ) of 50-200 units.
- **Specialty vacuum tubes:** Common tubes (12AX7, EL34, 6L6) are usually in stock. Rare types (6V6S, 5881, EL84 matched quartets) can take 2-4 weeks.
- **CNC-machined parts:** Custom enclosures, heatsinks, or brackets from a CNC service: 1-3 weeks.
- **PCBs from China (standard shipping):** 2-3 weeks door to door. Express: 1-2 weeks.

### 7.2 The Order-First Workflow

When starting a new product build, follow this sequence:

1. **Week 0:** Finalize design. Generate BOM. Order long-lead-time items (transformers, custom drivers, specialty tubes).
2. **Week 1:** Order PCBs (JLCPCB). Order common components from Mouser/LCSC. Order enclosures.
3. **Week 2-3:** PCBs arrive. Common components arrive. Start building prototypes.
4. **Week 4-6:** Long-lead-time items arrive. Complete builds. Test.

If you reverse this — build first, order transformers when you realize you need them — you'll sit idle for weeks staring at a half-finished amp. Ask me how I know.

### 7.3 Building a Relationship with Your Suppliers

Once you are ordering regularly, treat your key suppliers as business partners, not vending machines:

**Parts Express:** Their tech support team includes people who actually design speakers. If you are unsure which driver matches your enclosure design, call them. They will pull up Thiele-Small parameters and help you evaluate options. They also have a "project gallery" where customer builds get featured -- submit yours for free exposure to their audience of 100,000+ DIY audio enthusiasts.

**Tayda Electronics:** They offer a custom drilling and powder-coating service for enclosures. Upload your drill template as a PDF, choose your powder-coat color, and they ship ready-to-build enclosures. This service saves hours per unit and costs only $2-3 more than a bare enclosure. At 20+ enclosures, email them directly to negotiate a volume discount.

**JLCPCB:** For repeat orders, save your project on their platform. Reordering the same PCB takes 30 seconds. For PCBA orders, maintain a "frozen" BOM that does not change between production runs -- this avoids component substitution issues. If a part in your BOM goes out of stock at LCSC, JLCPCB will email you before production starts, giving you time to approve a substitute or source the part yourself.

**Local suppliers (lumber yards, metal shops):** Introduce yourself and explain what you do. A lumber yard that knows you build speaker cabinets will set aside premium MDF or Baltic birch when they get a shipment. A metal shop that knows you order 20 chassis per quarter will prioritize your work and offer faster turnaround.

### 7.4 Safety Stock

For components you use constantly (common resistors, capacitors, potentiometers, jacks, footswitches, wire), keep a "safety stock" — enough to build 2-3 units without placing an order. Reorder when you dip below that threshold. This prevents the "I can't finish this build because I'm out of 100nF caps" problem.

A small parts organizer with labeled bins is the physical equivalent of a well-stocked pantry. Spend $30 on a good organizer. It will pay for itself the first time it saves you a two-week wait for a $0.10 component.

---

## 8. Component Substitution

### 8.1 When Specs Matter

Some components in audio circuits are chosen for *specific electrical characteristics*, and substituting a "close enough" part will change the circuit's behavior in audible ways. Here's where to be precise:

**Precision resistors in crossover networks.** A speaker crossover is a filter, and the crossover frequency is set by exact component values. If the design calls for a 6.8-ohm 1% resistor and you use a 6.8-ohm 5% resistor, the actual resistance could be anywhere from 6.46 to 7.14 ohms. In a crossover, that shifts the frequency response by a dB or more, and different values in the left and right speaker destroy stereo imaging. Use 1% or better.

**Matched transistors in fuzz pedals.** A Fuzz Face circuit relies on two transistors with specific gain (hFE) values — typically one around 70-80 and one around 100-120 for a germanium version. Random transistors from a bag will sound wrong. You need to measure and select (or buy pre-matched pairs from a supplier like Small Bear Electronics).

**Film capacitors in tone circuits.** The capacitor in a Tube Screamer's tone control sets the frequency response curve. A 220nF Mylar cap and a 220nF ceramic cap have the same nominal value but different actual behavior at audio frequencies (ceramics can be microphonic and have voltage-dependent capacitance). Use the type specified in the design.

**Output transformers in tube amps.** Impedance ratio, frequency response, saturation characteristics, and primary inductance all vary between manufacturers. A "15W output transformer" from Hammond and one from Mercury Magnetics are not the same thing. They'll both work, but they'll sound different and have different headroom characteristics.

### 8.2 When Specs Don't Matter

**Bypass capacitors on IC power pins.** A 100nF ceramic cap is a 100nF ceramic cap. Brand doesn't matter. Don't pay $0.50 for a "premium" bypass cap when a $0.02 generic does the identical job.

**Chassis hardware.** Screws, nuts, washers, standoffs — any manufacturer is fine. Stainless steel is nicer than zinc-plated for aesthetics, but electrically it doesn't matter.

**Hook-up wire (within the same gauge and insulation type).** Wire is wire. Stranded 22 AWG from Mouser and stranded 22 AWG from Amazon carry the same signal.

**Pull-down resistors.** The 1M resistor at a pedal's input that prevents popping when you engage the effect? Any 1M resistor. 1%, 5%, carbon, metal film — doesn't matter.

**LED resistors.** The current-limiting resistor for your indicator LED? As long as it's in the right ballpark (1k-10k for a standard LED on 9V), exact value is not critical. Your LED will be slightly brighter or dimmer. Nobody will notice.

### 8.3 The Substitution Decision Framework

Ask yourself these questions:

1. **Is this component setting a frequency, gain, or bias point?** If yes, match the value and tolerance exactly.
2. **Is this component in the audio signal path?** If yes, match the type (film, ceramic, electrolytic) as well as the value.
3. **Is this component handling significant power?** If yes, match or exceed the power rating.
4. **Is the component's parasitic characteristics relevant?** (ESR for electrolytics, microphonics for ceramics, inductance for resistors at high frequency.) If yes, match the type.
5. **Is this component just "being there"?** (Bypass caps, pull-downs, LED resistors, mounting hardware.) If yes, any equivalent works.

---

## 9. Building a Preferred Supplier List

### 8.4 International Sourcing and Customs

If you are sourcing from LCSC, JLCPCB, Tayda, or AliExpress, your parts are shipping internationally. Here is what to know:

**Customs duties (US):**
- Electronic components imported into the US are generally duty-free or subject to very low tariffs (0-2.5%) under HTS Chapter 85. Your $100 LCSC order will not incur significant duties.
- Enclosures and mechanical parts may be classified differently and can attract higher tariffs (3-8%), especially aluminum enclosures.
- Orders under $800 (de minimis threshold in the US as of 2026) are exempt from formal customs entry and duties. Most individual component orders fall under this threshold. Note: this threshold has been subject to policy changes; verify the current limit before placing large orders.

**Customs duties (EU):**
- The EU has a much lower de minimis threshold: VAT is charged on all imported goods regardless of value (since July 2021). Expect to pay 19-25% VAT (depending on country) on the declared value plus shipping cost. LCSC and AliExpress often collect this at checkout.
- Customs duty rates for electronic components are typically 0-4.5%.

**Customs duties (UK, Australia, Canada):**
- Similar structure to EU: GST/VAT is charged on imports above a low threshold ($20 AUD in Australia, £135 in the UK, $20 CAD in Canada). Most component orders will trigger tax collection.

**Declared value:** Chinese suppliers (LCSC, AliExpress) sometimes offer to declare a lower value on the customs form to reduce your duty. Do not ask them to do this. Under-declaration is customs fraud and can result in fines, seizure of goods, and being flagged for future inspections. The few dollars you "save" is not worth the legal risk.

**Lead time for international orders:**
- LCSC / JLCPCB standard shipping to US: 10-14 days
- LCSC / JLCPCB express (DHL/FedEx) to US: 4-6 days
- Tayda (Thailand) to US: 14-21 days standard, 7-10 days express
- AliExpress to US: 14-40 days (wildly variable by seller)
- Mouser/Digi-Key (US domestic): 1-3 days

**Tip for international builders:** If you are outside the US, Mouser and Digi-Key both have international warehouses and local-currency pricing. Mouser ships from local warehouses in the EU, UK, and Asia. Check whether ordering from a local warehouse avoids import duties -- in many cases it does, because the goods are already imported and duty-paid.

### 9.1 Why You Need One

After a few builds, you'll have accounts on six different supplier websites, half-remembered bookmarks, and a vague feeling that you bought that capacitor somewhere cheaper last time but can't remember where. A preferred supplier list eliminates this chaos.

### 9.2 Structure

Create a spreadsheet (or a simple database — you're a software engineer, use whatever tool you prefer) with these columns:

| Column | Example |
|--------|---------|
| Component category | Film capacitor, 100nF |
| Preferred supplier | Mouser |
| Part number | WIMA MKS2C031001A00JSSD |
| Unit price (qty 1) | $0.42 |
| Unit price (qty 100) | $0.18 |
| Backup supplier | LCSC |
| Backup part number | C62210 |
| Backup unit price | $0.05 |
| Notes | Use for all pedal tone circuits. Mouser for prototypes, LCSC for batch. |

### 9.3 Organizing by Product Type

Maintain sub-lists for each product type you build:

**Guitar pedal BOM template:**
- Resistors: Tayda (budget) or Mouser (precision)
- Film caps: Mouser (WIMA or Panasonic)
- Electrolytics: Mouser (Nichicon or Panasonic)
- Diodes: Tayda or Mouser
- ICs: Mouser (always brand-name from authorized distributor)
- Potentiometers: Tayda (Alpha pots) for budget, Bourns from Mouser for premium
- Footswitch: Tayda (cheapest reliable 3PDT)
- Jacks: Tayda (Lumberg) or Mouser (Switchcraft)
- Enclosure: Tayda (Hammond, pre-drilled, powder-coated)
- PCB: JLCPCB
- Knobs: Tayda or Amazon (Davies Molding clones)

**Speaker build BOM template:**
- Drivers: Parts Express (Dayton Audio) or Madisound (SEAS/SB Acoustics)
- Crossover caps: Parts Express (Dayton Audio) or Meniscus (Mundorf/ClarityCap)
- Crossover inductors: Parts Express or Meniscus (Jantzen)
- Crossover resistors: Parts Express (Dayton Audio) or Mouser (Mills)
- Binding posts: Parts Express
- Terminal cups: Parts Express
- Damping material: Parts Express (polyfill, acoustic foam)
- Port tubes: Parts Express
- Cabinet material: Local lumber yard (MDF, Baltic birch plywood)
- Screws/hardware: McMaster-Carr or local hardware store
- Veneer/finish: Rockler or Woodcraft

**Tube amp BOM template:**
- Tubes: Tube Depot or AES
- Output transformer: Hammond (budget) or ClassicTone/Mercury Magnetics (premium)
- Power transformer: Hammond or ClassicTone
- Tube sockets: AES or Mouser (Belton/Cinch)
- Filter capacitors: Mouser (JJ or F&T brand for tube amps)
- Resistors: Mouser (carbon composition for vintage tone, metal film for low noise)
- Coupling caps: Mouser (Mallory 150, Sprague Orange Drop, or SoZo)
- Potentiometers: Mouser (CTS or Bourns)
- Chassis: Hammond (from Mouser) or custom from a metal shop
- Turret/eyelet board: AES
- Wire: Mouser (Gavitt cloth-covered for vintage builds)

### 9.4 Keeping It Updated

Review your supplier list quarterly. Prices change, parts go obsolete, and new suppliers emerge. When a part goes end-of-life (EOL), find a replacement immediately — don't wait until you're mid-build and desperate.

### 9.5 The Quarterly Supply Chain Review

Set a recurring calendar reminder every three months. During the review:

1. **Check inventory levels.** Are any common components below the reorder point? Place orders before you run out.

2. **Review pricing.** Log into Mouser, LCSC, and Tayda and spot-check the prices of your top 10 most-used components. Prices drift over time -- a resistor that was $0.03 last year might be $0.05 now, or (more often) cheaper. Update your BOM spreadsheet with current prices so your product costing stays accurate.

3. **Check for EOL (End of Life) notices.** Both Mouser and Digi-Key flag parts that are nearing end of life. If a component in your BOM is flagged, find a replacement immediately. Test the replacement in your circuit before the original part disappears. Common EOL surprises: specific op-amp variants (the TL072 has been manufactured for 40+ years and will probably outlive all of us, but the TLE2072 was discontinued in 2023), specific potentiometer models, and niche transistors.

4. **Evaluate new suppliers.** The DIY audio supply landscape evolves. New PCB services emerge, existing suppliers add or drop product lines, and shipping costs change. If you hear about a new supplier in a forum or Discord community, try a small test order and evaluate quality, speed, and cost.

5. **Review your shipping costs.** International shipping rates change frequently. If you order regularly from LCSC or Tayda, check whether their shipping rates or transit times have changed. Sometimes a shipping provider becomes faster or cheaper, and sometimes (especially around Chinese New Year) lead times balloon.

6. **Archive old BOMs.** Keep a history of BOM versions with dates and prices. This lets you track cost trends over time and demonstrates to yourself whether your cost optimization efforts are paying off. A BOM that dropped from $22 to $14 over the course of a year because you switched to LCSC for passives and Tayda for hardware is a concrete achievement worth documenting.

---

## 10. Quality vs Cost Trade-Offs

### 10.1 Where to Spend More

**Signal-path components.** Op-amps, transistors, film capacitors, and precision resistors that directly shape the sound. The difference between a $0.50 generic op-amp and a $3.00 TI OPA2134 is measurable (lower noise, lower distortion, better slew rate) and audible.

**Connectors.** A Neutrik 1/4" jack costs $3 and will survive 10,000 insertion cycles without degradation. A cheap Chinese jack costs $0.50 and starts crackling after 500 cycles. Your customers plug and unplug cables every day. Connectors are the most handled part of any audio product.

**Transformers (tube amps).** The output transformer is the single most important component in a tube amp's tone. A $40 Hammond OT and a $150 Mercury Magnetics OT both work, but the Mercury has tighter frequency response, lower leakage inductance, and better saturation characteristics. This is where "handmade premium" pricing is justified.

**Potentiometers (in premium products).** Alpha pots from Tayda are $0.70 and work fine for budget pedals. Bourns pots are $4 and have smoother taper, less channel-to-channel variation, and longer lifespan. For a $200 pedal, use Bourns.

**Speaker drivers.** The driver is 80-90% of a speaker's sound. Don't design a beautiful cabinet and then install the cheapest driver you can find. Match driver quality to the product's price tier.

### 10.2 Where to Save

**Passive components outside the signal path.** Power supply filter capacitors, LED current resistors, pull-up/pull-down resistors — these just need to be the right value and adequately rated. Cheapest option that meets specs.

**Wire.** Standard hook-up wire is fine for internal connections. You don't need "audiophile" wire with oxygen-free copper and teflon insulation for the 6 inches between your PCB and your output jack. Save that $20/foot cable for speaker runs over 50 feet.

**Hardware.** Screws, standoffs, cable clips, rubber feet — buy in bulk from the cheapest source. Amazon, AliExpress, or a fastener supplier. A rubber foot is a rubber foot.

**Packaging materials.** Boxes, foam inserts, packing peanuts — Amazon or a packaging supplier. Don't overspend on packaging early. A clean cardboard box with adequate padding is professional enough to start.

**PCBs.** JLCPCB at $2 for five boards is essentially free. There's no quality reason to pay more unless you need specific capabilities (flex, heavy copper, aluminum substrate) or US-based manufacturing for marketing reasons.

### 10.5 Real Numbers: What a Typical Monthly Parts Budget Looks Like

Here is what a pedal builder producing 10 pedals per month actually spends on components:

**Monthly component order (10 overdrive pedals):**

| Category | Source | Monthly Spend |
|----------|--------|--------------|
| Passive components (R, C, D) - bulk stock replenishment | LCSC | $15-20 |
| Active components (ICs, transistors) | Mouser | $8-12 |
| Potentiometers, switches, jacks | Tayda | $35-50 |
| Enclosures (pre-drilled, powder-coated) | Tayda | $55-70 |
| PCBs | JLCPCB | $5-8 (order 20-30 at a time) |
| Knobs and hardware | Tayda / Amazon | $25-30 |
| Wire, solder, consumables | Local / Amazon | $10-15 |
| Packaging (boxes, foam, labels) | Amazon / Uline | $8-12 |
| Shipping costs for incoming parts | Various | $10-20 |
| **Monthly total** | | **$171-237** |
| **Per pedal** | | **$17.10-23.70** |

At batch pricing, the BOM comes in around $14-16 per unit (the higher monthly total includes shipping costs and occasional overbuys to hit price breakpoints). Compare this to the $59 per-pedal cost of buying everything from Mouser at single-unit pricing. Smart sourcing cuts your materials cost by 65-75%.

For a speaker builder producing 2 pairs per month, the monthly spend is higher ($350-500) but more concentrated: drivers account for 50-60% of the spend, and those prices have fewer bulk discount opportunities at small volume.

> **What happens if... you stock up on components speculatively and the product does not sell?** You are left with $200-500 of components you cannot use. This is "inventory risk," and it is a real cost of batch buying. Mitigate it by: (1) only bulk-buying components shared across multiple products (common resistor and capacitor values, standard jacks and footswitches), (2) keeping model-specific components (unique ICs, specialized caps) at 2-3 units of safety stock rather than 50, and (3) validating demand before investing in inventory -- sell 5 units individually before ordering parts for 50.

### 10.3 Spotting Counterfeit Components

Counterfeit electronic components are a real problem, especially for active devices (ICs, transistors, tubes). Here is how to protect yourself:

**Common counterfeiting targets:**
- Op-amps: The OPA2134, LME49720, and NE5532 are frequently counterfeited. Fakes are usually relabeled (re-marked) lower-quality ICs that measure worse and may be noisy.
- Power transistors: IRFP250, 2SC5200/2SA1943 pairs (used in power amps) are commonly faked. Fake power transistors may have lower current ratings than specified, leading to premature failure under load.
- Vacuum tubes: "New Old Stock" (NOS) tubes from the 1960s-70s are valuable and heavily counterfeited. A "Mullard EL34" bought on eBay for $40 is almost certainly a Russian Sovtek with a Mullard label.
- Capacitors: High-value audio capacitors (Mundorf, Jensen, Sprague Orange Drop) are occasionally counterfeited with cheaper internals in branded casings.

**How to identify counterfeits:**
1. **Price that is too good.** If an OPA2134 costs $3 on Mouser and someone on AliExpress sells it for $0.30, it is fake. Genuine semiconductor pricing does not vary by 10x between legitimate channels and mystery sellers.
2. **Markings quality.** Genuine ICs have laser-etched markings that are crisp and cannot be rubbed off with solvent. Counterfeits often have printed markings that smear with acetone or isopropyl alcohol. Test: rub the IC marking with an acetone-soaked cotton swab. If it smears, it is re-marked.
3. **Package quality.** Genuine ICs have consistent pin spacing, clean mold lines, and no residue. Counterfeits may have slightly irregular pin spacing, rough mold flash, or residue from the re-marking process.
4. **Performance testing.** Measure the IC's noise floor, offset voltage, or gain-bandwidth product against the datasheet specification. A genuine OPA2134 has 8 nV/sqrt(Hz) input noise. A fake (re-marked NE5532) has 5 nV/sqrt(Hz) -- which is actually lower, but the slew rate and distortion characteristics are different, and the fake will not perform to OPA2134 specifications in circuits that rely on its specific characteristics.

**The only reliable defense:** Buy from authorized distributors (Mouser, Digi-Key, Newark, Farnell). These companies buy directly from manufacturers and maintain a chain of custody. The $2 premium over an AliExpress listing is the cheapest insurance against hours of debugging a circuit that "should work but sounds wrong."

### 10.4 The 80/20 Rule of Component Spending

In a typical audio product, about 20% of the components account for 80% of the sound quality. Identify those components (typically: active devices, signal-path capacitors, connectors, and transformers/drivers) and allocate budget accordingly. Everything else can be value-engineered without audible impact.

---

## Summary

**The Essentials:**
- Mouser and Digi-Key are your general-purpose distributors — reliable, fast, extensive
- Tayda is the pedal builder's budget hero — cheap, but limited to common components
- LCSC is the bulk buyer's dream — dramatically cheaper, but ships from China
- Parts Express is the speaker builder's first stop — drivers, crossover parts, everything audio
- JLCPCB is the PCB standard — $2 for 5 boards, excellent assembly services
- Neutrik and Switchcraft for connectors — always

**The Strategy:**
- Standardize your designs to consolidate component purchases
- Hit price breakpoints by buying in smart quantities
- Order long-lead-time items first (transformers, specialty drivers, custom parts)
- Keep safety stock of frequently used components
- Know when substitution is safe and when it changes the product
- Maintain a living preferred supplier list organized by product type
- Spend on signal-path components and connectors; save on hardware and consumables

**The Mindset:**
- Your supply chain is infrastructure. Invest time in it once and it pays dividends on every build.
- A $0.10 component you don't have costs more than a $1.00 component you do, because it costs you two weeks of waiting.
- Build relationships with your suppliers. Call them. Email them. Ask questions. The good ones (especially the audio-specific suppliers) employ people who actually know audio and will help you make good choices.

### Appendix: Quick-Reference Supplier Decision Matrix

When you are about to order, use this matrix to pick the right supplier:

| What You Need | First Choice | Why | Backup |
|---------------|-------------|-----|--------|
| Resistors (1-50) | Tayda | Cheapest per unit, ships from Thailand | Mouser |
| Resistors (100+) | LCSC | Dramatically cheaper at volume | Tayda |
| Film capacitors (signal path) | Mouser (WIMA/Panasonic) | Known quality, tight tolerance | Digi-Key |
| Film capacitors (non-critical, batch) | LCSC | 1/5 the price of Mouser | Tayda |
| Op-amps and ICs | Mouser | Guaranteed genuine, authorized distributor | Digi-Key |
| Potentiometers (budget) | Tayda (Alpha) | $0.69 each, adequate quality | Amazon |
| Potentiometers (premium) | Mouser (Bourns) | Smooth feel, tight taper, long life | Digi-Key |
| 3PDT footswitch | Tayda | $1.65, cheapest reliable source | Amazon (Daier brand) |
| Enclosures (bare) | Tayda (Hammond) | Cheapest Hammond source | Mouser |
| Enclosures (powder-coated) | Tayda custom shop | $5-7, saves hours of finishing | — |
| Speaker drivers (Dayton Audio) | Parts Express | Best selection, frequent sales | Amazon |
| Speaker drivers (premium: SEAS, SB Acoustics) | Madisound | Only reliable US source for many models | Parts Express (limited selection) |
| Crossover components | Parts Express | Audio-grade, correct power ratings | Meniscus |
| Speaker cabinet MDF/plywood | Local lumber yard | No shipping cost for heavy sheet goods | Home Depot |
| Tubes (vacuum) | Tube Depot or AES | Genuine, tested, matched options | — |
| Output transformers | Tube Depot (Hammond) | In-stock, reasonable price | AES (ClassicTone, Mercury Magnetics) |
| PCBs (prototype or production) | JLCPCB | $2 for 5 boards, unbeatable | PCBWay |
| PCB assembly (PCBA) | JLCPCB | Cheapest, good quality, integrated with LCSC | PCBWay |
| XLR/speakON connectors | Mouser (Neutrik) | Industry standard, authorized | Digi-Key |
| 1/4" jacks (pedal) | Tayda (Lumberg) | Budget, reliable | Mouser (Switchcraft) |
| Screws, standoffs, hardware | McMaster-Carr | Exhaustive selection, fast shipping | Amazon |
| Tools (soldering, drilling) | Amazon | Wide selection, competitive pricing | Mouser (select tools) |

> **What happens if... a critical component goes out of stock everywhere?** This happens. The 2020-2023 chip shortage wiped out availability of common audio ICs (TL072, NE5532, LM3886) for months at a time. Your defenses: (1) keep 2-3 builds' worth of critical ICs in stock at all times, (2) identify substitute ICs that work in your circuit (an OPA2134 can substitute for a TL072 in most applications with equal or better performance), (3) redesign your PCB to accept multiple IC footprints where possible (an 8-pin DIP socket accepts any compatible op-amp), and (4) watch for "end of life" notices from manufacturers -- Mouser and Digi-Key both flag parts nearing EOL. When you see one, stockpile or find an alternative immediately.

---

*Next up: [Chapter 41: Costing and Pricing](41-costing-pricing.md) — where we turn those supplier costs into actual product prices, and learn why most new builders underprice everything.*
