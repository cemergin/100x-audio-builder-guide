<!--
  CHAPTER: 41
  TITLE: Costing and Pricing
  PART: 7 — From Hobby to Hustle
  PREREQS: Chapter 40
  KEY_TOPICS: bill of materials, BOM calculation, labor costing, overhead, pricing strategies, cost-plus pricing, market-based pricing, value-based pricing, margins, real market prices, handmade premium, pricing mistakes
  DIFFICULTY: Intermediate
  UPDATED: 2026-04-10
-->

# Chapter 41: Costing and Pricing

> **Part 7 — From Hobby to Hustle** | Prerequisites: Chapter 40 | Difficulty: Intermediate

Here's the conversation that happens in every DIY audio forum, every week: "I built this overdrive pedal and it sounds amazing. My friends want one. How much should I charge?" The answers range from "$50 — that's what the parts cost" to "$250 — that's what boutique pedals sell for." Both answers are wrong, because neither starts from the right place. The right place is a spreadsheet. The right place is knowing — to the penny — what it costs you to make a thing, and then making a deliberate decision about what to charge above that.

This chapter is about the spreadsheet. We'll walk through complete bills of materials for three real products — a guitar pedal, a pair of bookshelf speakers, and a chip amp — with actual prices pulled from the suppliers in Chapter 40. We'll add labor (honestly), overhead (comprehensively), and then explore three different strategies for setting a price. By the end, you'll understand your margins well enough to know which products are worth your time and which ones are traps.

If you've ever shipped a software product, you know that the code is only part of the cost. There's infrastructure, tooling, support, and your time. Physical products are the same, except the infrastructure is a workbench and the deployment pipeline is a trip to the post office. The principles transfer. The specifics don't.

### In This Chapter
- How to build a complete bill of materials (BOM)
- Walkthrough: guitar pedal BOM with real prices
- Walkthrough: bookshelf speaker pair BOM with real prices
- Walkthrough: chip amp BOM with real prices
- Costing your labor honestly
- Calculating overhead and amortized tool costs
- Three pricing strategies: cost-plus, market-based, value-based
- Understanding gross and net margins
- Real market prices across product categories
- The handmade premium and when it applies
- The most common pricing mistakes and how to avoid them

### Related Chapters
- [Ch 40: Sourcing and Supply Chain](40-sourcing-supply-chain.md) — Where to buy and what things cost
- [Ch 42: Building a Brand and Selling](42-building-brand-selling.md) — Getting your priced products to market
- [Ch 43: Scaling Up](43-scaling-up.md) — How costs change at volume

---

## 1. The Bill of Materials (BOM)

### 1.1 What Goes in a BOM

A BOM is a complete list of every physical thing that becomes part of your finished product or is consumed in making it. "Complete" is the key word. Hobbyists routinely forget half the costs because they don't feel like "real" components. Here's everything that belongs in your BOM:

**Electronic components:**
- Resistors, capacitors, inductors
- Diodes, transistors, ICs
- Potentiometers, switches, relays
- LEDs and LED bezels
- Voltage regulators, bridge rectifiers

**Connectors and jacks:**
- 1/4" input/output jacks
- DC power jacks
- XLR connectors
- Speaker binding posts
- Terminal strips

**Mechanical hardware:**
- Enclosure or chassis
- Screws, nuts, washers, standoffs
- Rubber feet or bumpers
- Knobs
- Back panels, faceplates
- Speaker grilles and mounting hardware

**Wire and solder:**
- Hook-up wire (include the amount used per build)
- Speaker wire
- Solder (yes, you use a measurable amount per build)
- Heat-shrink tubing

**PCB:**
- The PCB itself (amortize setup costs over the run)
- Any stencils used for SMD work

**Cabinet materials (for speakers):**
- MDF or plywood
- Glue
- Veneer or vinyl wrap
- Damping material (polyfill, foam)
- Port tubes

**Packaging:**
- Box
- Foam inserts or bubble wrap
- Tape
- Stickers or labels
- Any included documentation (printed manual, warranty card)

### 1.2 The "Invisible" Costs

These are the things builders forget:

- **Solder:** A spool of good solder costs $15-25 and builds maybe 30-50 pedals. That's $0.30-0.80 per unit.
- **Flux:** $8-12 a bottle, lasts maybe 50 builds. $0.16-0.24 per unit.
- **Sandpaper:** If you're prepping enclosures, you go through sandpaper. $0.25-0.50 per unit.
- **Drill bits:** They wear out. A good step bit costs $15 and lasts maybe 50 enclosures. $0.30 per unit.
- **Tape and labels:** Masking tape for painting, labels for identification. $0.10-0.25 per unit.
- **Test leads and jumper wires:** They break. Budget $0.05 per unit for gradual replacement.

These "invisible" costs add up to $1-3 per unit. That doesn't sound like much until you've built 100 units and realized you've spent $100-300 you didn't account for.

---

## 2. BOM Walkthrough: Guitar Pedal (Overdrive)

Let's cost out a complete overdrive pedal — something like a Tube Screamer variant, which is one of the most popular DIY pedal circuits and a realistic first product. Prices are from Tayda and Mouser at single-unit quantities.

### 2.1 Complete BOM

| Component | Qty | Source | Unit Price | Total |
|-----------|-----|--------|-----------|-------|
| **Electronics** | | | | |
| 1/4W metal film resistors (various values) | 15 | Tayda | $0.03 | $0.45 |
| Ceramic disc capacitors (various) | 4 | Tayda | $0.05 | $0.20 |
| Film capacitors (various) | 6 | Tayda | $0.12 | $0.72 |
| Electrolytic capacitors (various) | 4 | Tayda | $0.08 | $0.32 |
| 1N4148 signal diodes | 2 | Tayda | $0.03 | $0.06 |
| 1N4001 protection diode | 1 | Tayda | $0.03 | $0.03 |
| JRC4558D op-amp (or equivalent) | 1 | Tayda | $0.45 | $0.45 |
| 8-pin DIP socket | 1 | Tayda | $0.08 | $0.08 |
| 2N3904 transistor (buffer) | 1 | Tayda | $0.04 | $0.04 |
| **Controls** | | | | |
| B500K potentiometer (Drive) | 1 | Tayda | $0.69 | $0.69 |
| A100K potentiometer (Volume) | 1 | Tayda | $0.69 | $0.69 |
| B20K potentiometer (Tone) | 1 | Tayda | $0.69 | $0.69 |
| **Connectors & Switching** | | | | |
| 1/4" mono jack (Switchcraft style) | 2 | Tayda | $0.55 | $1.10 |
| 2.1mm DC power jack | 1 | Tayda | $0.25 | $0.25 |
| 3PDT footswitch (true bypass) | 1 | Tayda | $1.65 | $1.65 |
| **Indicators** | | | | |
| 5mm LED (color of choice) | 1 | Tayda | $0.05 | $0.05 |
| LED bezel (chrome) | 1 | Tayda | $0.15 | $0.15 |
| LED current-limiting resistor | 1 | Tayda | $0.03 | $0.03 |
| **Mechanical** | | | | |
| Hammond 125B enclosure (pre-drilled, powder-coated) | 1 | Tayda | $6.50 | $6.50 |
| Davies 1510 style knobs | 3 | Tayda | $0.75 | $2.25 |
| Rubber feet (adhesive) | 4 | Tayda | $0.05 | $0.20 |
| PCB (amortized: 5 boards for $2 from JLCPCB) | 1 | JLCPCB | $0.40 | $0.40 |
| **Wire & Consumables** | | | | |
| Hook-up wire (various colors, ~18" total) | 1 | Tayda | $0.30 | $0.30 |
| Solder (amortized) | — | — | $0.50 | $0.50 |
| Flux (amortized) | — | — | $0.20 | $0.20 |
| Heat-shrink tubing | — | — | $0.15 | $0.15 |
| **Packaging** | | | | |
| Small cardboard box | 1 | Amazon | $0.75 | $0.75 |
| Foam insert | 1 | Amazon | $0.30 | $0.30 |
| Printed info card | 1 | — | $0.15 | $0.15 |
| | | | **BOM TOTAL** | **$18.30** |

### 2.2 Notes on This BOM

That $18.30 is for a single unit at hobby prices. At batch quantities (buying 50-100 units worth of components at price-break quantities from Tayda and LCSC), this drops to roughly **$12-14 per unit**. The enclosure is the single most expensive component — that's normal for pedals.

If you switch to a bare aluminum enclosure and do your own drilling and finishing, you save $3-4 per unit but add 30-45 minutes of labor. Whether that trade-off makes sense depends on how you value your time (we'll get to that).

---

## 3. BOM Walkthrough: Bookshelf Speakers (Pair)

A pair of 2-way bookshelf speakers with Dayton Audio drivers, a simple crossover, and an MDF cabinet. This is a realistic "first speaker product" that sounds genuinely excellent.

### 3.1 Complete BOM (Per Pair)

| Component | Qty | Source | Unit Price | Total |
|-----------|-----|--------|-----------|-------|
| **Drivers** | | | | |
| Dayton Audio RS150-8 6" woofer | 2 | Parts Express | $29.80 | $59.60 |
| Dayton Audio DC28F-8 1-1/8" dome tweeter | 2 | Parts Express | $21.30 | $42.60 |
| **Crossover Components (per speaker x2)** | | | | |
| Crossover inductor 1.5mH (woofer low-pass) | 2 | Parts Express | $5.75 | $11.50 |
| Crossover capacitor 6.8uF film (tweeter high-pass) | 2 | Parts Express | $3.20 | $6.40 |
| Crossover capacitor 10uF film (woofer low-pass) | 2 | Parts Express | $4.50 | $9.00 |
| Crossover resistor 8 ohm 10W (tweeter padding) | 2 | Parts Express | $1.10 | $2.20 |
| Crossover resistor 3.3 ohm 10W (impedance eq) | 2 | Parts Express | $1.10 | $2.20 |
| Crossover inductor 0.3mH (tweeter) | 2 | Parts Express | $3.40 | $6.80 |
| **Cabinet Materials** | | | | |
| 3/4" MDF sheet (4'x8', makes ~4 pairs) | 0.25 | Local lumber | $40.00 | $10.00 |
| Wood glue (amortized) | — | — | — | $1.50 |
| Cabinet screws (various) | ~24 | McMaster | $0.08 | $1.92 |
| Port tubes (2" diameter, 4" long) | 2 | Parts Express | $2.30 | $4.60 |
| Polyfill damping material | — | Parts Express | — | $4.00 |
| **Hardware & Finishing** | | | | |
| Binding post cups (dual banana, gold-plated) | 2 | Parts Express | $4.50 | $9.00 |
| Speaker mounting screws | 8 | McMaster | $0.10 | $0.80 |
| Rubber feet (adhesive) | 8 | Amazon | $0.25 | $2.00 |
| Vinyl wrap (black ash — amortized per pair) | — | Parts Express | — | $8.00 |
| Contact cement for vinyl (amortized) | — | — | — | $2.00 |
| **Wire & Consumables** | | | | |
| Internal speaker wire (16 AWG, ~4 ft total) | — | Parts Express | — | $1.50 |
| Solder (amortized) | — | — | — | $0.50 |
| Sandpaper (various grits, amortized) | — | — | — | $1.50 |
| **Packaging** | | | | |
| Shipping box (double-wall corrugated) | 1 | Uline/Amazon | $5.00 | $5.00 |
| Foam corner protectors | 8 | Amazon | $0.40 | $3.20 |
| Packing material | — | — | — | $2.00 |
| | | | **BOM TOTAL** | **$197.82** |

### 3.2 Notes on This BOM

Speakers are driver-heavy in terms of cost. The two pairs of drivers account for $102.20 — over half the BOM. This is why driver selection is the most important decision in a speaker build: it determines both the sound and the economics.

Upgrading to premium drivers changes the picture dramatically. Swap the Dayton Audio woofers for SB Acoustics SB17NRXC35-8 ($72 each from Madisound) and the tweeters for SB Acoustics SB26STAC-C000-4 ($45 each from Madisound), and the driver cost alone jumps from $102 to $234. But now you're in the premium tier where speakers sell for $1,200-2,000 per pair.

Cabinet materials are the second largest cost, and the most variable. MDF is cheapest ($10/pair). Baltic birch plywood looks better and sounds slightly different ($25-35/pair). Solid hardwood is beautiful but expensive and heavy ($60-120/pair).

---

## 4. BOM Walkthrough: Chip Amp (LM3886-Based Stereo)

A simple, high-quality stereo amplifier based on the LM3886 — a legendary chip amp IC that delivers 40-68W per channel with very low distortion. This is a great first amplifier product because it sounds excellent, is straightforward to build, and the circuit is well-documented.

### 4.1 Complete BOM

| Component | Qty | Source | Unit Price | Total |
|-----------|-----|--------|-----------|-------|
| **Active Components** | | | | |
| LM3886TF power amp IC | 2 | Mouser | $7.80 | $15.60 |
| **Power Supply** | | | | |
| Toroidal transformer (2x25V, 160VA) | 1 | Antek/Parts Express | $45.00 | $45.00 |
| Bridge rectifier (25A) | 1 | Mouser | $1.80 | $1.80 |
| Filter capacitors 10,000uF 50V | 4 | Mouser | $4.50 | $18.00 |
| Power supply film caps (0.1uF bypass) | 4 | Mouser | $0.25 | $1.00 |
| Power supply resistors (bleeder) | 2 | Mouser | $0.10 | $0.20 |
| Fuse holder + fuse | 1 | Mouser | $2.50 | $2.50 |
| IEC power inlet (with switch and fuse) | 1 | Mouser | $4.20 | $4.20 |
| Power indicator LED + resistor | 1 | Mouser | $0.30 | $0.30 |
| **Amplifier Circuit (x2 channels)** | | | | |
| Input coupling capacitors (2.2uF film) | 2 | Mouser | $1.20 | $2.40 |
| Feedback resistors (20k, 1%) | 2 | Mouser | $0.10 | $0.20 |
| Gain resistors (1k, 1%) | 2 | Mouser | $0.10 | $0.20 |
| Zobel network (10 ohm + 0.1uF) | 2 sets | Mouser | $0.30 | $0.60 |
| Output inductor (hand-wound, 10 turns) | 2 | Mouser (wire) | $0.50 | $1.00 |
| Bypass/decoupling capacitors (various) | 8 | Mouser | $0.20 | $1.60 |
| Mute circuit resistors/caps | 4 | Mouser | $0.15 | $0.60 |
| **Connectors** | | | | |
| RCA input jacks (gold, panel mount) | 2 pair | Parts Express | $3.50 | $7.00 |
| Neutrik speakON output connectors | 2 | Mouser | $4.50 | $9.00 |
| Binding posts (alternative to speakON) | 4 | Parts Express | $2.75 | $11.00 |
| **Mechanical** | | | | |
| Aluminum chassis (Hammond 1455 series or custom) | 1 | Mouser | $28.00 | $28.00 |
| Heatsinks (TO-220 compatible, large) | 2 | Mouser | $6.50 | $13.00 |
| Thermal pads/compound | 2 | Mouser | $0.50 | $1.00 |
| Input selector switch (if included) | 1 | Mouser | $3.50 | $3.50 |
| Volume potentiometer (Alps Blue Velvet or similar) | 1 | Mouser | $8.50 | $8.50 |
| Knob (machined aluminum) | 1 | Amazon | $4.00 | $4.00 |
| Rubber feet | 4 | Amazon | $0.50 | $2.00 |
| Standoffs, screws, washers | ~20 | McMaster | $0.15 | $3.00 |
| **PCBs** | | | | |
| Amplifier PCBs (2x mono boards) | 2 | JLCPCB | $0.40 | $0.80 |
| Power supply PCB | 1 | JLCPCB | $0.40 | $0.40 |
| **Wire & Consumables** | | | | |
| Hook-up wire (various gauges) | — | Mouser | — | $3.00 |
| Power cord (IEC) | 1 | Amazon | $3.00 | $3.00 |
| Solder (amortized) | — | — | — | $1.00 |
| Heat-shrink, cable ties | — | — | — | $1.00 |
| **Packaging** | | | | |
| Shipping box | 1 | Uline | $4.00 | $4.00 |
| Foam inserts | — | — | — | $3.00 |
| | | | **BOM TOTAL** | **$201.40** |

### 4.2 Notes on This BOM

The power supply transformer is the most expensive single component at $45. This is typical for any amplifier — the transformer is the heaviest, most material-intensive part. You can save $10-15 by using a standard EI transformer instead of a toroidal, but toroidals have lower stray magnetic field (less hum) and look more professional.

The LM3886 itself is only $15.60 for both channels. The IC that does the actual amplification is one of the cheapest parts. Everything else is support: power delivery, filtering, connectors, and mechanical packaging.

Using binding posts instead of (or in addition to) Neutrik speakON adds $11 but makes the amp more accessible to consumers who don't have speakON cables. For a product, include both.

---

## 5. Labor Costing

### 5.1 Track Your Time Honestly

This is where most hobbyists-turned-builders lie to themselves. They say "oh, that pedal only took me an hour." It did not. Here's what "building a pedal" actually entails:

1. **Prep work** (15-25 min): Gathering components, organizing workspace, reviewing the build notes.
2. **PCB population** (30-60 min): Soldering all components to the PCB. More for through-hole, less for pre-assembled SMD.
3. **Enclosure work** (20-45 min): Drilling (if not pre-drilled), deburring, fitting test. Add 30-60 min for painting/finishing.
4. **Off-board wiring** (20-30 min): Connecting jacks, switch, pots, LED, DC jack to the PCB.
5. **Final assembly** (10-15 min): Mounting the PCB in the enclosure, securing knobs, final wire routing.
6. **Testing** (15-30 min): Power-on test, signal test, all controls functional, noise check, bypass test.
7. **Troubleshooting** (0-60 min): If something doesn't work. Average across builds, budget 10 min.
8. **Packaging** (5-10 min): Boxing, labeling, any documentation.

**Realistic total: 2-4 hours per pedal.** Call it 3 hours average for an experienced builder.

**For speakers:** Cabinet cutting (2-4 hours if hand-cutting, 30 min if CNC pre-cut), assembly and gluing (2-3 hours plus drying time), sanding and finishing (3-6 hours depending on finish), crossover assembly and wiring (1-2 hours), driver installation and testing (1-2 hours), final testing and measurement (1-2 hours). **Total: 12-20 hours per pair.** Add more for premium finishes.

**For the chip amp:** PCB assembly (1-2 hours), power supply assembly (1-2 hours), chassis prep and drilling (1-3 hours), final assembly and wiring (2-3 hours), testing and burn-in (2-4 hours). **Total: 8-14 hours.**

### 5.2 What's Your Time Worth?

This is a philosophical question masquerading as a business question. Here are some anchors:

- **$20/hour** — Minimum if you're doing this as a side gig. Below this, you'd earn more driving for a rideshare service.
- **$30-40/hour** — Reasonable for skilled manual work. This is what a decent electrician or carpenter charges.
- **$50-75/hour** — What your time is worth if you're a mid-career software engineer giving up billable hours to solder.
- **$100+/hour** — Only sustainable if your products command premium prices and your brand supports it.

For starting out, **$25-35/hour is realistic**. You can increase it as your skills and efficiency improve and your brand commands higher prices.

### 5.3 The Labor Math

Using our pedal example at $30/hour for 3 hours of labor:

- BOM: $18.30
- Labor: $90.00
- **Build cost so far: $108.30**

That's before overhead. The labor is nearly 5x the materials cost. This is why most custom audio products are expensive — and why most hobbyist builders underprice their work. They cost the materials but "forget" the labor, or they value their time at $0/hour because "it's a hobby."

If you're selling products, it's not a hobby anymore. Pay yourself.

---

## 6. Overhead

### 6.1 Fixed Overhead

These are costs you incur regardless of how many units you build:

**Workspace:** If you rent shop space or a maker space membership, that's a direct monthly cost. Even if you work from your garage, consider the opportunity cost of that space. A typical maker space runs $100-300/month. A dedicated workshop rental runs $300-800/month depending on location.

**Insurance:** Product liability insurance for a small electronics maker runs $300-500/year. We'll discuss this more in Chapter 42, but include it in your overhead from day one.

**Website and online presence:** Domain name ($12/year), hosting or Shopify ($30-80/month), email service, etc. Budget $50-100/month for basic online presence.

**Software:** Design tools are mostly free (KiCad, LTspice, WinISD), but you might pay for accounting software ($15-30/month), an inventory tracker, or design tools.

### 6.2 Amortized Tool Costs

Your tools are a capital investment. Spread their cost over the number of units they'll help you build:

| Tool | Cost | Expected Units | Per Unit |
|------|------|---------------|----------|
| Soldering station (Hakko FX-888D) | $110 | 500+ | $0.22 |
| Drill press | $200 | 200+ | $1.00 |
| Oscilloscope (Rigol DS1054Z) | $350 | 300+ | $1.17 |
| Multimeter (Fluke 117) | $200 | 1000+ | $0.20 |
| Audio analyzer (miniDSP UMIK-1 + REW) | $100 | 100+ | $1.00 |
| Table saw (for speaker cabinets) | $400 | 50+ pairs | $8.00 |
| Router + bits | $200 | 50+ pairs | $4.00 |
| Clamps (set) | $100 | 100+ pairs | $1.00 |
| Step drill bits (set) | $25 | 100 | $0.25 |

For a pedal builder, amortized tool costs add roughly **$3-5 per unit**. For a speaker builder, it's **$15-25 per pair** (woodworking tools are more expensive).

### 6.3 Per-Unit Overhead

Some overhead scales with each unit:

- **Marketplace fees:** Reverb takes 5% + payment processing (~3%). Etsy takes 6.5% + listing fees. eBay takes 13.25% on most categories. Your own Shopify store has payment processing (~3%) but no marketplace fee.
- **Shipping supplies:** $1-5 per unit depending on size and weight.
- **Listing time:** Photographing, writing descriptions, managing listings. Budget 15-30 minutes per unique product listing, amortized over units sold.

### 6.4 Putting It All Together

Let's add overhead to our pedal:

| Cost Category | Amount |
|--------------|--------|
| BOM (materials) | $18.30 |
| Labor (3 hrs @ $30/hr) | $90.00 |
| Amortized tools | $3.50 |
| Workspace (amortized, home shop) | $2.00 |
| Insurance (amortized) | $1.00 |
| Marketplace fee (5% + 3% on $175 sale) | $14.00 |
| Shipping supplies | $2.00 |
| **Total cost per unit** | **$130.80** |

At a $175 selling price, your pre-tax profit is $44.20 per pedal. That's a 25% net margin. Not amazing, but viable — and this is at single-unit BOM prices. At batch pricing, the BOM drops to $12-14, and your margin improves to $50-58 per unit (29-33%).

---

## 7. Pricing Strategies

### 7.1 Cost-Plus Pricing

**Method:** Calculate your total cost, add a markup percentage.

**Formula:** Selling Price = Total Cost x (1 + Markup%)

Using our pedal: $130.80 x 1.35 (35% markup) = $176.58, round to **$175**.

**When it works:** Straightforward, ensures you never sell at a loss, easy to calculate. Good for custom orders where there's no direct market comparison.

**When it doesn't:** You might leave money on the table if the market values your product higher than cost-plus suggests. Conversely, if your costs are high (slow builder, expensive workspace), cost-plus might price you out of the market.

### 7.2 Market-Based Pricing

**Method:** Research what comparable products sell for. Price within that range based on your product's relative quality and uniqueness.

**The research:** Go to Reverb, Etsy, or wherever your product would sell. Search for similar items. Note the price range.

For our overdrive pedal:
- Mass-produced Tube Screamer (Ibanez TS-9): $100 new
- Mid-tier boutique overdrives (EQD, Walrus Audio): $150-200
- Premium boutique overdrives (Analogman, Klon KTR): $250-350+
- Other small-maker/DIY overdrives on Reverb: $80-180

Your pedal is handmade and custom, but you're a new brand with no reputation. Pricing at $130-175 positions you above mass-produced but below established boutique brands. That's the right slot for starting out.

**When it works:** Gives you a reality check against what customers actually pay. Prevents both underpricing and overpricing for commodity products.

**When it doesn't:** If your product is truly unique (no direct comparables), market-based pricing gives you no anchor. It also doesn't guarantee profitability — if the market price is below your cost, you need to reduce costs or choose a different product.

### 7.3 Value-Based Pricing

**Method:** Price based on the value the customer perceives, not your costs.

This is where custom and unique products shine. Consider:

- A pair of custom-designed bookshelf speakers with premium drivers, a hand-built crossover, and a walnut veneer cabinet. Your BOM is $350, your labor is $600 (20 hours), overhead is $100. Total cost: $1,050. A comparable commercial speaker (Dynaudio Evoke 20, KEF R3 Meta) sells for $1,600-2,000. But yours is *custom-designed for the buyer's room*, hand-built, one-of-a-kind. You can price at $1,800-2,500 because the value to the buyer extends beyond the components.

- A tube guitar amplifier with hand-selected tubes, point-to-point wiring, and a custom tolex finish. Your cost is $600. Similar handwired boutique amps (Matchless, Dr. Z, Two Rock) sell for $2,000-5,000. You can price at $1,200-2,000 depending on wattage and features.

**When it works:** High-end, custom, unique products where the buyer cares about craftsmanship, uniqueness, or solving a specific problem. The more custom the work, the more value-based pricing applies.

**When it doesn't:** Commodity products where the buyer can easily compare your price to twenty alternatives. A standard Tube Screamer clone is a commodity — value-based pricing doesn't work because the buyer knows they can get functionally identical products for $100.

### 7.4 The Hybrid Approach

In practice, you'll use all three:

1. **Calculate cost-plus** to know your floor (minimum viable price).
2. **Research market prices** to know the ceiling and the competitive landscape.
3. **Assess the value** to position within that range.

If your cost-plus price is higher than the market ceiling, you have a problem — either reduce costs or don't make that product. If your cost-plus price is far below the market ceiling, congratulations — you have room for healthy margins.

---

## 8. Understanding Margins

### 8.1 Gross Margin

**Gross Margin = (Revenue - BOM Cost) / Revenue**

This is your margin before labor and overhead. It tells you how much room you have to cover everything else.

For our pedal at $175: ($175 - $18.30) / $175 = **89.5% gross margin**

That looks incredible, right? It is, for the materials. But "materials" is only part of the story for handmade goods.

### 8.2 Net Margin

**Net Margin = (Revenue - All Costs) / Revenue**

This is what you actually keep.

For our pedal: ($175 - $130.80) / $175 = **25.3% net margin**

That's $44.20 per pedal. Build and sell two pedals a week, and that's $4,590 per year in profit. Not quit-your-day-job money, but real income from a side hustle.

### 8.3 Target Margins by Product Type

Based on what's sustainable in the market:

| Product | Target Gross Margin | Target Net Margin |
|---------|-------------------|------------------|
| Guitar pedals | 80-90% | 20-35% |
| Custom speakers | 60-75% | 25-40% |
| Tube amplifiers | 55-70% | 20-35% |
| Chip amplifiers | 65-80% | 25-35% |
| Complete systems | 50-65% | 20-30% |

The higher the labor-to-materials ratio, the more important it is to price your labor correctly. Speakers and amps have high labor, so labor costing dominates. Pedals have lower absolute labor cost per unit but you can build more of them.

---

## 9. Real Market Prices

### 9.1 Guitar Pedals ($100-300)

The pedal market is the most competitive DIY audio market. Hundreds of small makers sell on Reverb, and buyers are price-sensitive because mass-produced alternatives exist for every effect type.

- **$80-120:** Basic clones with no special features. Thin margins unless you batch-produce.
- **$130-180:** Custom graphics, premium components, some unique circuit modification. This is the sweet spot for small makers.
- **$200-300:** Unique circuits, premium build quality (top-mounted jacks, relay bypass, internal trimpots), limited runs, established brand reputation.
- **$300+:** Collector/hype territory. Reserved for established names with waiting lists.

**Realistic starting point:** $130-175 for a well-built pedal with good aesthetics.

### 9.2 Custom Speakers ($500-3,000 per pair)

The speaker market is less competitive than pedals because the barrier to entry is higher (woodworking skills, crossover design knowledge, measurement equipment). Buyers are often audiophiles who appreciate craftsmanship and are willing to pay for custom work.

- **$300-600:** Budget drivers (Dayton Audio), MDF cabinets, vinyl wrap. Competes with commercial speakers 2-3x the price.
- **$600-1,200:** Mid-range drivers (SB Acoustics, Peerless), nicer cabinets, veneer or painted finish. Sweet spot for value-oriented audiophiles.
- **$1,200-2,500:** Premium drivers (SEAS, Scan-Speak), hardwood or premium veneer cabinets, measured and matched crossovers. Competes with commercial speakers at $3,000-6,000.
- **$2,500+:** Exotic drivers, custom cabinetry, room-specific tuning. Essentially bespoke furniture that makes sound.

**Realistic starting point:** $600-1,000 per pair for a mid-range build with good aesthetics and measured performance.

### 9.3 Custom Amplifiers ($500-2,000)

- **$300-500:** Chip amps (LM3886, TPA3255 Class D). Lower power, simpler construction. Excellent value for the buyer.
- **$500-1,000:** Small tube amps (5-15W), chip amps with premium chassis and components.
- **$1,000-2,000:** Medium tube amps (15-50W), hand-wired, quality transformers.
- **$2,000+:** High-power tube amps, point-to-point wiring, premium transformers, custom chassis.

**Realistic starting point:** $400-700 for a chip amp, $800-1,500 for a tube amp.

### 9.4 Complete Sound Systems ($2,000-10,000)

This is where everything comes together — speakers, amplification, crossover, and potentially room treatment, all designed as an integrated system. This is also where the highest margins live, because you're selling *design expertise* and *system integration* on top of the hardware.

- **$2,000-4,000:** Active speakers with built-in amplification, or a matched amp + speaker combination.
- **$4,000-7,000:** Multi-way speakers with separate amplification, DSP crossover, custom tuning.
- **$7,000-10,000+:** Full-room systems with room treatment consultation, custom installation, and premium components throughout.

---

## 10. The Handmade Premium

### 10.1 Why People Pay More for Handmade

It's not irrational. Handmade audio products offer things mass-produced ones can't:

- **Customization.** "I want an overdrive that's darker than a Tube Screamer but not as compressed as a Klon." A mass producer can't do that. You can.
- **Quality control.** You test every unit personally. You listen to it. Mass producers do statistical sampling.
- **Repairability.** Hand-wired products can be repaired. A surface-mount production pedal often can't.
- **The story.** People enjoy owning something made by a person, not a factory. They display it. They tell their friends.
- **Support.** You answer emails. You know the circuit. You can troubleshoot.

### 10.2 When the Premium Applies

The handmade premium only works if the product *looks* and *feels* handmade-in-the-good-way. This is where Part 5's finishing techniques pay off. A pedal with a beautiful powder-coated enclosure, laser-etched graphics, and a smooth finish commands a premium. A pedal with a scratched bare aluminum enclosure and hand-scrawled labeling looks like what it is — amateur.

The premium is justified when:
- Build quality is visibly excellent
- Sound quality is demonstrably good (demo videos, measurements)
- The product offers something the mass market doesn't
- Your brand has a story and a presence

### 10.3 Quantifying the Premium

Based on market observation, the handmade premium for audio products is roughly:

- **Pedals:** 30-80% over mass-produced equivalent
- **Speakers:** 0-50% (audiophiles value measured performance over "handmade" story)
- **Tube amps:** 50-200% (handwired amps are a well-established premium category)
- **Complete systems:** 50-100% (custom design and integration are inherently high-value services)

---

## 11. Pricing Mistakes

### 11.1 The Most Common Mistake: Underpricing

This deserves its own section because nearly every new builder does it. You build a beautiful overdrive pedal. BOM was $18. You think "I'll sell it for $60, that's a great markup on materials!" Except you didn't account for the 3 hours of labor ($90), the overhead ($15), or the marketplace fees ($5). Your "great markup" is actually a **$68 loss per unit**. You're paying people to take your pedals.

I've seen builders sell gorgeous hand-wired tube amplifiers for $400 because "the parts cost $300 and I wanted to make a little profit." The 20 hours of skilled labor they donated to that amp is worth $600-1,000. They didn't make a little profit. They lost $500-900.

**The fix:** Always calculate your full cost (BOM + labor + overhead) before setting a price. If the full cost is higher than what the market will bear, you either reduce costs or don't sell that product. You never sell at a loss and call it a business.

### 11.2 Forgetting Hidden Costs

- Marketplace fees (5-13% of selling price — on a $175 pedal, that's $8.75-22.75)
- Payment processing (3% of selling price)
- Shipping costs (if you offer "free shipping," you're absorbing $5-15 per sale)
- Returns (budget 5-10% of sales for returns/warranty claims)
- Time spent on customer communication (pre-sale questions, post-sale support)

These can easily total 15-25% of revenue. If your margins don't account for them, they eat your profit.

### 11.3 Overpricing (Rarer, But It Happens)

This usually stems from emotional attachment. "I spent 40 hours on these speakers, and they're the best thing I've ever made." Maybe they are. But if the drivers are Dayton Audio, the cabinet is MDF, and your brand is unknown, the market won't pay $2,500 for them no matter how many hours you spent. Your labor rate isn't the customer's problem — it's yours.

The fix: always reality-check your price against the market. If comparable products sell for $800, and your cost is $600, pricing at $900-1,000 is justified by the handmade premium. Pricing at $2,500 is not.

### 11.4 The Pricing Calculator: A Methodology You Can Replicate

Here is a step-by-step pricing calculator you can use for any product. Copy this into a spreadsheet and fill in the numbers for each product you build.

**Step 1: Calculate BOM cost**
- List every component (use the format from sections 2-4 of this chapter)
- Include consumables (solder, flux, sandpaper, heat-shrink)
- Include packaging (box, foam, tape, documentation)
- Sum: this is your **Materials Cost**

**Step 2: Calculate labor cost**
- Time each phase of the build (or estimate based on similar builds)
- Multiply total hours by your labor rate ($25-35/hour for starting out)
- Sum: this is your **Labor Cost**

**Step 3: Calculate overhead**
- Amortized tool cost per unit (section 6.2)
- Amortized workspace cost per unit (monthly rent / units per month)
- Insurance amortized per unit (annual premium / units per year)
- Sum: this is your **Overhead Cost**

**Step 4: Calculate total cost**
```
Total Cost = Materials + Labor + Overhead
```

**Step 5: Determine your selling price**
- Calculate cost-plus floor: Total Cost × 1.30 (30% markup minimum)
- Research market price range for comparable products
- Position your price within the range based on quality and brand

**Step 6: Calculate net margin after fees**
```
Marketplace fee = Selling Price × 0.08 (approximate for Reverb)
Shipping cost = $5-15 (depending on product size and weight)
Net revenue = Selling Price - Marketplace fee - Shipping cost
Net profit = Net revenue - Total Cost
Net margin = Net profit / Selling Price
```

**Step 7: Sanity check**
- Is net margin above 20%? If not, reduce costs or increase price.
- Is net profit per unit above $30? If not, the product may not be worth your time at low volumes.
- Divide net profit by labor hours: is your effective hourly rate above $20? If not, you are undercharging.

**Example: the overdrive pedal through the calculator:**

| Step | Value |
|------|-------|
| Materials (BOM at smart-hobby prices) | $18.30 |
| Labor (3 hours × $30/hr) | $90.00 |
| Overhead (tools + workspace + insurance) | $6.50 |
| **Total Cost** | **$114.80** |
| Cost-plus floor (1.30x) | $149.24 |
| Market range (Reverb search) | $130-200 |
| **Selling price (chosen)** | **$175.00** |
| Reverb fee (8%) | -$14.00 |
| Shipping (baked into price) | -$8.00 |
| Net revenue | $153.00 |
| **Net profit** | **$38.20** |
| **Net margin** | **21.8%** |
| Effective hourly rate | $12.73/hr |

That effective hourly rate looks low. It improves dramatically with batch production (3 hours drops to 1.5 hours per unit): net profit stays the same but your effective rate doubles to $25.47/hr. And at batch BOM prices ($13 instead of $18.30), your net profit jumps to $43.50 per unit at $29/hr.

This is why Chapter 43 (Scaling Up) is not optional -- it is where the economics actually start working.

### 11.5 The Psychology of Pricing

Pricing is not purely rational. The buyer's perception of value is shaped by psychological factors that you can use honestly and ethically:

**Anchor pricing:** When you list a pedal for $175, the buyer mentally compares it to other pedals they have seen. If the first thing they see in your listing is "comparable to boutique pedals selling for $250+," the $175 feels like a good deal. This is anchoring -- the $250 number sets the reference point. Use it honestly: if your pedal genuinely compares to $250 pedals in build quality and sound, say so.

**Price endings:** $175 feels meaningfully cheaper than $180, even though the difference is negligible. Prices ending in 5 or 9 are perceived as value-oriented. Prices ending in 0 are perceived as round and premium. $200 feels premium. $199 feels like a deal. Choose the ending that matches your positioning.

**Bundle pricing:** If you sell three pedal models, offer a "pedalboard bundle" of all three at 10-15% off the individual prices. The buyer perceives a deal, you move more inventory, and the total sale value is higher than a single pedal. For speakers + amp combinations, bundles are even more powerful -- the buyer saves money and gets a pre-matched system.

**The decoy effect:** If you offer two pedals -- a basic model at $125 and a premium model at $200 -- adding a mid-range model at $165 makes the premium model look more reasonable by comparison. The middle option often becomes the best seller because it seems like the "sensible choice" between extremes.

**Perceived value of presentation:** A pedal in a plain cardboard box sells for less than the same pedal in a fitted box with a foam insert and a printed card. The $2 packaging upgrade can justify a $10-15 price increase because it signals quality and care. This is not deception -- it is communication.

### 11.6 Not Adjusting Prices Over Time

As you get faster (3 hours becomes 2 hours per pedal), as you buy in bulk (BOM drops from $18 to $13), and as your brand grows (you can charge more), your pricing should evolve. Review and adjust prices quarterly. Many builders set a price on day one and never touch it, leaving money on the table for years.

---

## Summary

**The Numbers Game:**
- A guitar pedal BOM runs $12-18, builds in 2-4 hours, sells for $130-200
- A speaker pair BOM runs $150-350, builds in 12-20 hours, sells for $500-2,500
- A chip amp BOM runs $150-200, builds in 8-14 hours, sells for $400-700
- A tube amp BOM runs $250-500, builds in 15-25 hours, sells for $800-2,000

**The Strategy:**
- Always know your full cost: BOM + labor + overhead + fees
- Use cost-plus as your floor, market-based as your ceiling, value-based to position
- Target 40-60% gross margin and 20-35% net margin
- The handmade premium is real but must be earned through quality and presentation
- Most new builders underprice. You probably will too. Resist the urge.

**The Mindset:**
- Your time has value. If you don't charge for it, you're running a charity, not a business.
- Materials are the minority of your costs. Labor and overhead are the majority.
- Pricing is not set-and-forget. Review quarterly as your costs and efficiency change.
- Some products have better margins than others. Let the math guide which products to focus on.

### Quick Reference: Revenue per Hour by Product Type

This table shows the effective revenue per hour of labor for each product category at typical prices and build times. Use it to decide where to focus your effort:

| Product | Typical Sell Price | BOM | Labor Hours | Overhead + Fees | Net Profit | Profit/Hour |
|---------|-------------------|-----|-------------|----------------|------------|-------------|
| Overdrive pedal (single) | $175 | $18 | 3.0 | $22 | $45 | $15/hr |
| Overdrive pedal (batch of 10) | $175 | $14 | 1.5 | $20 | $51 | $34/hr |
| Bookshelf speakers (pair) | $800 | $200 | 16 | $90 | $110 | $6.88/hr |
| Bookshelf speakers (batch of 3 pairs) | $800 | $160 | 12 | $85 | $155 | $12.92/hr |
| Custom speakers (premium drivers, hardwood) | $2,000 | $450 | 25 | $175 | $775 | $31/hr |
| LM3886 chip amp | $500 | $200 | 10 | $55 | $135 | $13.50/hr |
| Tube amp (15W combo) | $1,200 | $400 | 20 | $115 | $285 | $14.25/hr |
| PA sound service (per event) | $300-500 | $0* | 6** | $30 | $270-470 | $45-78/hr |

*PA service uses equipment you already own. BOM is zero (amortized into the system cost).
**Includes setup, operation, and breakdown.

Notice that PA sound services and custom premium speakers have the highest profit per hour. This is why many builders eventually evolve from "person who sells pedals" to "person who designs and installs custom audio systems." The economics improve dramatically as you sell expertise rather than just hardware.

> **What happens if... you undercharge for your first few sales to "build a reputation"?** This is the most common trap in the DIY audio community. You sell five pedals at $80 each ($400 total revenue), losing money on each one because your total cost is $110 per unit. You now have five customers who expect $80 pedals. When you raise the price to $175 (where you actually make money), they feel betrayed — "it was $80 last month!" You have anchored your brand at a price point that is unsustainable. Instead: charge the real price from day one. If nobody buys at $175, the problem is marketing, not pricing. Better to sell zero units at the right price (and fix your marketing) than to sell ten units at the wrong price (and dig a hole you cannot climb out of).

---

*Next up: [Chapter 42: Building a Brand and Selling](42-building-brand-selling.md) — where we take your properly-priced products and get them in front of buyers.*
