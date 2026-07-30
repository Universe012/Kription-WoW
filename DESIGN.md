# Kription — Shipping Tycoon

**Design document v0.1 — for approval before implementation.**

Simulation-grade model, casual-grade interface. A single self-contained HTML file, portrait-first,
one-handed on a bus. The numbers below are the ones I intend to hard-code as tuning constants; they
are meant to be checked by someone who actually fixes ships.

---

## 1. Core loop

### First 60 seconds
No menus, no fleet builder. The player is dropped into **Fleet** owning one ship:

> **MV Kription Trader** — 2004-built Handysize, 28,200 DWT, geared 4×30t, ice class 1C, NIS flag, DNV.
> Currently idle at **Riga**. Cash **$650,000**. Mortgage **$4.8M** against a **$8.0M** asset.

A single pulsing primary button: **FIND CARGO**. One tap → **Charter** tab, pre-filtered to three
fixtures her size can lift from where she actually is. Each is a card:

```
Riga → Ghent
26,000 mt milling wheat · 1,050 nm
9d          TCE ~$14.7k/d          +$137k
                                    [ FIX ]
```

Second tap fixes it. The ship sails. A voyage progress bar appears in the top bar. At ~5 real
seconds per game day, the voyage completes in **~47 seconds**. A result sheet slides up:
freight, costs, days, and one number in large type — **TCE $14,707/day** — with a one-line
explanation: *"Time Charter Equivalent: what this voyage earned per day after voyage costs. The
number every shipowner is judged on."*

Cash is now ~$726k. That is the entire first minute: **accept cargo → sail → get paid.** Nothing
else is on screen.

### First 10 minutes
Roughly 8–12 voyages / ~120 game days. Systems unlock one at a time, each triggered by a reason to
care (see §6):

| Voyage | Unlock | The lesson |
|---|---|---|
| 1–2 | Nothing. Bunkers shown as one prepaid line. | Fix, sail, get paid. |
| 3 | **Bunkers** — lift quantity and port choice. | Riga MGO is $735; Rotterdam is $690. Buy where it's cheap. |
| 4 | **Ballast legs** — a fixture is offered out of Gdansk while you're at Ghent. | You pay to get to the cargo, and earn nothing doing it. |
| 5 | **ECA zones** — first voyage out of the Baltic to Iberia. | Inside the ECA you burn MGO. Outside it, cheap VLSFO. |
| 6 | **Speed** — a slider on the fixture card. | 14 kn → 12 kn cuts burn ~35% and adds 1.4 days. Sometimes right, sometimes not. |
| 8 | **Market tab** — the Handysize index with 90 days of history. | Rates move. You are not a price taker forever. |
| 10 | **Time charter offers** — 6 months at $13,000/day. | Certainty has a price. |

By minute 10 the player has ~$1.3–1.8M cash, has probably taken a second-hand Supramax on 60% debt,
and has learned that the highest freight rate on the board is often not the best fixture.

### First 5 hours
~3,600 game days ≈ **10 game years**, which covers one full boom-and-bust and most of a second.
This is where the game actually is:

- **Years 0–2:** 1 → 4 ships. All spot, all dry bulk, all short-haul. Learning triangulation.
- **Years 2–4:** First Capesize on the long Brazil–China haul. Discovers 45% of a Cape round voyage
  is ballast. First drydock (special survey, 21 days off-hire, $1.4M). First PSC detention if the
  fleet has been run hard.
- **Years 3–5:** The cycle turns. Players who fixed everything on 3-year time charters at the top
  survive; players fully spot with 70% LTV debt get a covenant breach. **Bankruptcy is live here.**
- **Years 5–7:** Counter-cyclical S&P. Handysize values are down 45%; the smart move is buying steel,
  not chasing freight. Tanker segment unlocks (different cycle, Worldscale, vetting gates).
- **Years 7–10:** Diversified fleet, 12–25 ships, newbuild orders with 30-month lag, NIS registry
  and tonnage tax, FFA hedging, CII retrofits or scrapping the E-rated tonnage.

The intended difficulty curve is not "bigger numbers" — it's **the shift from micromanaging one
voyage to allocating capital across a cycle.** Voyage lengths help enforce this: a Handysize Baltic
run is 9 days (47s), a Cape Brazil–China round voyage is 81 days (7 min). You physically cannot
micromanage 20 ships, so you stop.

---

## 2. Economy math

### 2.1 Starting position

| Item | Value |
|---|---|
| Cash | $650,000 |
| Vessel (2004 Handysize, 28,200 DWT) | $8,000,000 market |
| Mortgage | $4,800,000 (60% LTV, SOFR+250 ≈ 7.0%, 12-yr straight-line amort.) |
| Annual debt service | ~$736,000 (≈$2,016/day, front-loaded interest) |
| Daily OPEX | $4,800 |
| **Net equity** | **$3,850,000** |
| Cash runway with no revenue | ~95 days |

Deliberately tight. Two consecutive bad fixtures hurt; three plus a breakdown is fatal.

### 2.2 Worked voyage P&L — the tutorial fixture

**MV Kription Trader**, Riga → Ghent, 26,000 mt milling wheat, **FIOST** (charterer pays load and
discharge), 1,050 nm, laden speed 12.5 kn. Entire voyage is inside the Baltic + North Sea ECA, so
**MGO throughout** — this is why short Baltic trades look expensive per tonne.

| Line | Basis | $ |
|---|---|---:|
| Gross freight | 26,000 mt × $11.00/mt | 286,000 |
| Address commission | 2.50% (to charterer) | (7,150) |
| Brokerage | 1.25% | (3,575) |
| **Net freight** | | **275,275** |
| Bunkers — sea | 3.50 d × 17.7 mt/d MGO × $735/mt | (45,540) |
| Bunkers — in port | 5.85 d × 3.0 mt/d MGO × $735/mt | (12,900) |
| Port DA — Riga | dues, pilotage ×2, towage, agency, linesmen | (28,000) |
| Port DA — Ghent | as above, Belgian tariffs | (34,000) |
| Canal tolls | none | 0 |
| Cargo handling | FIOST — charterer's account | 0 |
| EU ETS | 79.5 mt MGO × 3.206 tCO₂/mt × 100% scope (intra-EU) × 70% phase-in × €72 × 1.08 | (13,873) |
| FuelEU Maritime | MGO 90.8 gCO₂e/MJ vs 89.34 target; €2,400/t VLSFOe deficit | (3,450) |
| **Voyage costs** | | **(137,763)** |
| **Gross voyage profit** | | **137,512** |
| Voyage days | 3.50 sea + 5.85 port + 0.00 ballast | 9.35 |
| **TCE** | 137,512 ÷ 9.35 | **$14,707/day** |
| OPEX | 9.35 d × $4,800 | (44,880) |
| Debt service | 9.35 d × $2,016 | (18,850) |
| **Net cash to owner** | | **+$73,782** |

Port days are computed, not fudged: load Riga 26,000 mt ÷ 10,000 mt/day = 2.60 d; discharge Ghent
26,000 ÷ 8,000 = 3.25 d. If Ghent's berth queue pushes it past the 6.0 days of agreed laytime, the
player **collects demurrage** at $9,500/day. That is a real and pleasant surprise the first time.

**Sanity check:** $14.7k/day TCE on a 20-year-old 28k Handysize is a firm-but-not-spiking market.
Handysize spot TCE has realistically ranged ~$5k (2016 trough) to ~$32k (2021 spike), typical
mid-cycle ~$11–14k. This fixture sits just above mid-cycle, which is the right place to start a
player: profitable, not comfortable.

### 2.3 Second worked example — why ballast is the whole game

**Capesize 180,000 DWT**, Tubarão → Qingdao, 170,000 mt iron ore, then ballast back to Brazil.
Laden 11,050 nm @ 12.0 kn, ballast 11,050 nm @ 12.5 kn. Freight $22.00/mt.

| Line | Basis | $ |
|---|---|---:|
| Net freight | 170,000 × $22.00 less 3.75% | 3,599,750 |
| Bunkers laden | 38.4 d × 31.2 mt/d | (664,000) |
| Bunkers ballast | 36.8 d × 27.5 mt/d | (561,700) |
| Bunkers in port | 6.0 d × 5.0 mt/d | (16,650) |
| Port DA Tubarão / Qingdao | | (230,000) |
| **Voyage costs** (VLSFO @ $555) | | **(1,472,350)** |
| **Gross voyage profit** | | **2,127,400** |
| Voyage days | 38.4 laden + 36.8 ballast + 6.0 port | 81.2 |
| **TCE** | | **$26,200/day** |

**45% of the voyage days earn nothing.** A player who instead discharges in Qingdao and picks up a
Pacific round voyage (Australia → China, 8-day ballast instead of 37) can run a materially lower
freight rate and still beat this TCE. That asymmetry — *position beats price* — is the single most
important thing the game teaches, and it falls straight out of honest arithmetic rather than a
designed reward.

### 2.4 Tier scaling table

Secondhand values are for **15-year-old** tonnage at mid-cycle. Consumption is total mt/day at
design speed on VLSFO (fixed auxiliary load + cubic propulsion component). TCE bands are
soft / mid / firm spot markets.

| Class | Capacity | 15-yr SH $M | Newbuild $M | OPEX $/d | Design kn | Laden mt/d | TCE band $/d |
|---|---|---:|---:|---:|---:|---:|---|
| Handysize | 28k DWT | 8 | 27 | 4,800 | 14.0 | 24 | 5 / 12 / 26k |
| Supramax | 58k DWT | 15 | 34 | 5,300 | 14.0 | 30 | 6 / 14 / 30k |
| Kamsarmax | 82k DWT | 18 | 36 | 5,600 | 14.0 | 34 | 7 / 15 / 32k |
| Post-Panamax | 93k DWT | 20 | 39 | 5,800 | 14.0 | 36 | 7 / 15 / 31k |
| Capesize | 180k DWT | 26 | 65 | 6,600 | 14.5 | 52 | 8 / 22 / 50k |
| Newcastlemax | 208k DWT | 32 | 72 | 6,900 | 14.5 | 56 | 9 / 24 / 55k |
| VLOC | 325k DWT | 52 | 125 | 8,000 | 14.0 | 70 | long-term COA/TC only |
| MR product | 50k DWT | 20 | 50 | 7,200 | 14.5 | 26 | 11 / 25 / 45k |
| LR1 | 75k DWT | 26 | 60 | 7,600 | 14.5 | 30 | 12 / 26 / 48k |
| Aframax / LR2 | 115k DWT | 38 | 73 | 8,200 | 14.5 | 38 | 14 / 33 / 60k |
| Suezmax | 160k DWT | 42 | 88 | 8,800 | 15.0 | 48 | 15 / 38 / 70k |
| VLCC | 310k DWT | 58 | 128 | 10,500 | 15.0 | 62 | 17 / 45 / 90k |
| Feeder | 1,700 TEU | 16 | 32 | 6,500 | 18.5 | 40 | 8 / 16 / 40k |
| Feedermax | 2,800 TEU | 22 | 42 | 7,200 | 20.0 | 58 | 10 / 20 / 55k |
| Container Panamax | 5,000 TEU | 30 | 62 | 8,500 | 22.0 | 100 (45 @16 kn) | 12 / 26 / 80k |
| Neopanamax | 14,000 TEU | 88 | 145 | 11,500 | 22.0 | 200 (90 @16 kn) | 25 / 50 / 150k |
| ULCV | 23,000 TEU | 150 | 220 | 14,000 | 22.0 | 240 | 35 / 70 / 200k |
| LNG carrier | 174,000 m³ | 175 | 260 | 17,000 | 19.5 | 100 + boil-off | 40 / 80 / 180k |
| VLGC | 91,000 m³ | 68 | 122 | 11,000 | 16.5 | 45 | 20 / 45 / 90k |
| PCTC | 7,000 CEU | 60 | 115 | 12,000 | 19.0 | 60 | 25 / 50 / 110k |
| Reefer | 600k cu.ft | 14 | 40 | 8,500 | 19.0 | 42 | seasonal, 10 / 22 / 45k |
| Heavy-lift / project | 12k DWT, 2×500t | 26 | 55 | 9,500 | 15.0 | 22 | project-priced |
| PSV | 4,000 DWT deck | 18 | 45 | 9,000 | 13.0 | 12 | day rate 12 / 25 / 45k |
| AHTS | 200t bollard pull | 24 | 58 | 11,000 | 14.0 | 18 | day rate 15 / 35 / 70k |
| Wellboat | 3,500 m³ | 32 | 62 | 12,000 | 14.0 | 18 | contract 28 / 36 / 48k |

**Scrap:** priced per lightweight tonne, Alang/Chattogram/Aliağa differ by ~$40/ldt.
Base $520/ldt mid-cycle. Handysize ≈ 6,800 ldt → $3.5M. Capesize ≈ 22,000 ldt → $11.4M.
VLCC ≈ 40,000 ldt → $20.8M. Scrap price is itself cyclical (±35%) and **floors the asset value** —
which is exactly why old tonnage stops falling in a crash, and a mechanic worth having.

### 2.5 Canal tolls

Regressive per-DWT tiers, laden. Ballast/empty gets ~30% off.

| DWT band | Suez $/DWT | Panama $/DWT |
|---|---:|---:|
| < 50k | 3.60 | 3.10 |
| 50–100k | 3.00 | 2.70 |
| 100–200k | 2.60 | 2.40 |
| > 200k | 2.30 | n/a (draft) |

Handysize 28k Suez ≈ $101k. Capesize 180k ≈ $468k. VLCC 310k ≈ $713k. Kamsarmax Panama ≈ $221k plus
a **booking slot fee** that auctions upward in drought years (draft cap drops from 15.24 m to
13.4 m, cutting Kamsarmax intake by ~8,000 mt). Container ships pay a TEU-based tariff instead,
roughly $95/TEU laden.

The alternative is always live: **Cape of Good Hope** adds ~3,400 nm to an Asia–Europe run —
$0 toll, ~10 extra days, ~350 mt extra bunkers. At $555 VLSFO and $22k/day TCE that's ~$414k, so a
Suezmax pays the toll and a laden Handysize on a soft market sometimes does not. Security-driven
rerouting (Red Sea) flips the whole calculation plus a war-risk premium, and is modelled as a
market regime, not a random event.

### 2.6 Bunker prices (mid-cycle base, $/mt)

| Port | VLSFO | MGO | HSFO |
|---|---:|---:|---:|
| Singapore | 545 | 720 | 450 |
| Rotterdam | 515 | 690 | 425 |
| Fujairah | 530 | 730 | 440 |
| Houston | 530 | 715 | 435 |
| Gibraltar | 570 | 760 | 480 |
| Riga | 560 | 735 | — |
| Santos | 600 | 800 | 510 |
| Durban | 625 | 840 | 530 |
| Cape Town | 655 | 880 | — |
| Remote / off-hub | 720–790 | 950 | — |

HSFO only burnable with a scrubber (retrofit ~$2.4M for a Capesize, pays back on the ~$100/mt
spread in ~250 steaming days). MGO mandatory in ECA. LNG dual-fuel and methanol are newbuild-only
premium options (+$18M / +$12M on a Neopanamax) with a real FuelEU advantage.

---

## 3. Progression ladder

| Stage | Fleet | Cash scale | Gate to next stage | New system |
|---|---|---|---|---|
| **0 — Deckhand** | 1× Handysize (20yo, 60% LTV) | $0.65M | Complete 3 voyages | — |
| **1 — Regional trader** | 1–2× Handy/Handymax, Baltic–Continent–Med | $1–3M | $2.5M equity | Bunkers, ballast, ECA, speed |
| **2 — Supramax operator** | 3–5× Handy/Supramax, Atlantic + Med | $3–8M | Credit rating B | TC offers, rate charts, laytime/demurrage |
| **3 — Ocean-going** | + 1–2× Kamsarmax, first Cape | $8–25M | Survive first drydock cycle | Drydock/SS, PSC, canals, seasonality |
| **4 — Cycle player** | 6–10 ships, mixed sizes | $25–60M | Survive a bust with positive equity | S&P asset trading, COA, bareboat, covenants |
| **5 — Diversified owner** | + tankers (MR → Aframax) | $60–150M | Pass first oil-major vetting | Worldscale, vetting, cargo grades, tank cleaning |
| **6 — Shipowner** | + container / PCTC / VLGC | $150–400M | 15 ships, 3 segments | Newbuild orders, yard slots, FFA/bunker hedging |
| **7 — Group** | 20–40 ships, LNG, offshore, NIS + tonnage tax | $400M+ | — | Registry/tonnage-tax optimisation, CII retrofit vs scrap, in-house pool |

Bankruptcy is reachable from stage 2 onward and *likely* at stage 4 for an over-leveraged player.
The failure state is a **covenant breach** (loan-to-value > 80% or cash below the minimum liquidity
undertaking), which triggers a 60-day cure period — sell a ship into a bad market, or the bank
arrests the fleet. That is how it actually happens, and it makes the downturn legible rather than
arbitrary.

---

## 4. Screen map

Five bottom tabs, always visible, ≥44 px targets, `env(safe-area-inset-bottom)` respected.
Portrait-first; on desktop the whole thing centres in a 430 px column.

```
┌─────────────────────────────────────┐
│ $2.41M   +$18.2k/d   Util 87%   ⏱2× │  ← persistent top bar
├─────────────────────────────────────┤
│                                     │
│           ACTIVE SCREEN             │
│                                     │
│         one primary action          │
│                                     │
├─────────────────────────────────────┤
│  Fleet  Charter  Market  Yard  Co.  │  ← bottom tab bar
└─────────────────────────────────────┘
```

| Tab | Primary action | Contents | Bottom sheets |
|---|---|---|---|
| **Fleet** | *Find cargo* (idle ship) | Vessel cards: name, class, position, employment, voyage progress, condition bar, days to drydock, CII letter | Vessel detail (particulars, consumption curve, survey history, P&L to date), speed change, bunker lift, reposition |
| **Charter** | *Fix* | Fixture cards filtered to what your ships can carry from where they are. Segmented control: Spot / Time charter / COA / Bareboat | Fixture detail (laden+ballast legs, laytime terms, port DAs, projected TCE), counter-offer, ship picker |
| **Market** | *(read-only)* | Index sparklines per segment, bunker price list, secondhand value curve, orderbook & scrapping | Index detail with 5-year history and supply/demand drivers, FFA/bunker hedge ticket |
| **Yard** | *Buy* | Tabs: Secondhand / Newbuild / My fleet (sell) / Demolition | Vessel spec sheet, financing slider (LTV, tenor, rate), yard slot & delivery date, scrap yard quotes |
| **Company** | *(context)* | P&L, cash flow, balance sheet, debt schedule, KPIs (fleet TCE, utilisation, OPEX/day, ROE), reputation & vetting, flag/registry, glossary, settings, export/import save | Loan detail, registry change, vetting status per major, glossary entry, debug (long-press version) |

**Progressive disclosure** is the rule everywhere. A voyage card is one line:
`Rotterdam → Santos · 14d · +$412k`. Tapping expands to laden/ballast split, bunker burn by grade,
port disbursements itemised, canal toll, demurrage/despatch, carbon cost, and TCE/day. A first-time
player never sees layer two. Every industry term gets an inline one-liner on first appearance
(`ⓘ TCE` → tap → *"Time Charter Equivalent — voyage profit per day. The industry's yardstick."*)
and a permanent glossary entry.

**Palette:** background `#0B1622` deep navy, surfaces `#132433`, borders `#1E3648`, text `#E6EDF3`,
muted `#7D96AC`, positive `#3FB27F`, negative `#E05252`, hazard `#F08A24`, accent `#4A9EDA`.
System font stack. No gradients, no shine, no coin showers. It should read as an ops dashboard.

---

## 5. Realism dial

### Simulated honestly (the model actually computes these)

- **Speed/consumption** as fixed auxiliary load + cubic propulsion term. Not pure cube — real curves
  flatten at low speed because hotel/aux load doesn't scale. This matters: pure cube would make
  slow-steaming look better than it is.
- **TCE** as `(voyage revenue − voyage costs) ÷ total voyage days`, including ballast and port days.
- **Ballast positioning** with a per-region market liquidity value, so a dead-end discharge port is
  genuinely punished by the fixture list it generates.
- **Real distances** in nm between ~55 ports, real draft/beam/air-draft/LOA restrictions, geared vs
  gearless port compatibility, real cargo handling rates per port and commodity.
- **Laytime, demurrage, despatch**; FIOST vs liner terms; SHINC vs SHEX; address commission and
  brokerage.
- **Port disbursements** built up from dues + pilotage + towage + agency + linesmen, scaled by GT.
- **Canal tolls** on regressive tonnage tiers, draft caps, Panama booking slots, Cape alternatives.
- **OPEX** built up from crew (by nationality/rank mix), stores, lubes, insurance (H&M + P&I),
  repairs, management fee, drydock provision — not a single opaque number.
- **Bunker prices per port** with grade spreads and cyclical crude coupling; lift-quantity decisions
  with tank capacity and ROB constraints.
- **ECA zones** (North Sea, Baltic, North American, Caribbean, Med from 2025) forcing MGO on the
  affected leg fraction.
- **EU ETS** with phased scope (40/70/100%) and 50% extra-EU / 100% intra-EU coverage; **FuelEU**
  penalty from the actual GHG-intensity deficit; **CII/AER** rating with the annual reduction
  factor, D×3 or E×1 triggering a corrective action plan and charterer resistance.
- **Survey cycle**: intermediate ~2.5 yrs, special survey every 5 yrs, cost and off-hire scaling
  with age and deferred maintenance. Deferral is allowed once, at a compounding cost.
- **PSC** inspection frequency and detention probability as a function of flag performance, class
  society, age, and hull condition.
- **Freight cycle** from an explicit supply/demand model: tonne-mile demand × seasonality, versus
  fleet supply adjusted for newbuild deliveries, scrapping, congestion (which absorbs supply), and
  speed (slow steaming absorbs supply). Separate indices per segment with different drivers and
  correlations — dry bulk on Chinese steel and grain, tankers on refinery runs and tonne-mile
  dislocation, container on consumer restocking.
- **Asset values** as a function of newbuild parity, cycle position, remaining life, and scrap
  floor. Buying at the bottom and selling at the top is a first-class strategy.
- **Debt** with LTV, tenor, amortisation, interest, LTV and minimum-liquidity covenants, cure
  periods, and enforcement.
- **Worldscale** for tankers: per-route flat rates in $/mt, fixtures quoted in WS points.

### Abstracted deliberately

| Abstraction | Why |
|---|---|
| Crew as a wage tier (Filipino/Indian/Ukrainian/Norwegian officers × rank mix), not individuals | Individual crew management is a different game. The wage differential is the strategic content, and that survives. |
| Liner services built from ~10 preset trade lanes with editable rotations, rather than free-form port-by-port network design | Free-form network design on a phone is unplayable. Presets carry the geography; the player controls the levers that matter — speed, string size, buffer, contract mix, slot sales. See §5b. |
| Charter party as 3 term templates (owner-friendly / balanced / charterer-friendly) rather than clause negotiation | Clause-by-clause negotiation is unplayable on a phone. Templates preserve the trade-off. |
| Cargo intake = `min(DWT allowance at draft, cubic capacity ÷ stowage factor)`; no stability or stress calculation | Loading computers are real engineering. The *constraint* is what the player needs — and grain cubic vs ore deadweight limits still bite. |
| Weather as per-region-per-season distributions on speed loss and consumption, not routed forecasts | Weather routing as a real decision would need a real met model. Seasonal North Atlantic and monsoon penalties give the same behaviour. |
| Tank cleaning as a cost + days penalty on grade change | Correct enough. Full grade-compatibility matrices are for a tanker-only game. |
| Container ships **chartered out to liner operators**, not running a liner service | Operating a liner network — schedules, slot sharing, empty repositioning, alliances — is a bigger game than everything else combined. Owning boxships is a charter and asset play, which is what most owners actually do. See open question 3. |
| All USD | Shipping is a USD industry. Crew cost FX exposure is real but small and not fun. |
| Insurance as premium + deductible + claims loading | Claims handling is paperwork. |
| Sanctions/KYC as an event, not continuous counterparty screening | The decision moment is the interesting part. |

### Flagged as too fiddly to be fun — recommend leaving out

1. **Full stowage and hold planning.** Real skill, zero phone affordance.
2. **NOR tendering pedantry** — WWWW, turn time, notice periods, weather working days. I model
   laytime and demurrage; the tendering ritual would be a tutorial nobody finishes.
3. **Individual PSC deficiency codes.** "3 deficiencies, no detention" carries the information.
4. **A complete Worldscale flat rate table.** ~40 routes, not 300+.
5. **Ballast water treatment, garbage management plans, MARPOL Annex V logs.** Compliance theatre.
6. **Bunker quantity/quality surveying as a continuous mechanic.** Excellent as an *event*
   (off-spec fuel, choose: deviate to debunker, or burn it and risk the engine). Terrible as a
   per-lift minigame.
7. **EU ETS/FuelEU at Handysize scale** costs ~$17k on a $137k voyage — real, but it's a lot of UI
   for a rounding error early on. I fold both into one **Carbon** line that expands, and it only
   appears once the player is trading into the EU with more than two ships.

---

## 5b. Liner services (container operating)

Per your call, containers are a **full liner service**, not just an asset play. Chartering boxships
out to AI operators stays available as the low-risk path, so both routes to the segment exist — but
the player can build and run their own network.

The design principle: a liner service is a **product the player creates and maintains**, not a
fixture they accept. It's the only part of the game with recurring rather than voyage-based revenue,
and it deserves to feel structurally different.

### The Service object

A **Service** is a closed loop with a fixed frequency, maintained by a **string** of vessels:

```
AE7  Asia – North Europe                          weekly
Shanghai → Ningbo → Yantian → Singapore → Suez →
Rotterdam → Hamburg → Antwerp → Suez → Singapore → Shanghai
11 × Neopanamax 14,000 TEU · 16.0 kn · 3d buffer
```

**The central equation, and the reason this segment is worth building:**

```
string size = ceil( round-voyage days / frequency days ) + buffer ships
```

Asia–North Europe round trip is ~21,000 nm plus ~10 port calls at ~1.4 days and two canal transits.
That gives:

| Service speed | Sea days | + port/canal | Round voyage | Ships for weekly | Burn per ship | **Fleet burn mt/day** |
|---:|---:|---:|---:|---:|---:|---:|
| 18.0 kn | 48.6 | 16 | 64.6 | 10 | 130 | **1,300** |
| 16.0 kn | 54.7 | 16 | 70.7 | 11 | 90 | **990** |
| 14.0 kn | 62.5 | 16 | 78.5 | 12 | 62 | **744** |

Slowing the service **requires more ships** to hold weekly frequency, but cuts total fuel burn
sharply. This is not a designed mechanic — it's arithmetic, and it is precisely why the industry
slow-steamed its way through 2009 and absorbed enormous surplus capacity doing it. A player who
works this out has learned something true about the industry. It also makes charter-in demand
endogenous: when bunkers spike, the player *wants* more ships, which bids up the TC market the
player also participates in as an owner.

### Revenue

- **Freight per TEU per port-pair**, from the lane's container spot index, adjusted by the player's
  **schedule reliability** score. Unreliable services can only sell at a discount.
- **Directional imbalance is the defining feature.** Headhaul (Asia→Europe) runs 90–98% full;
  backhaul runs 55–65% full at roughly 40% of the headhaul rate. Any model that treats a loop as
  symmetrical is wrong, and the asymmetry is where the strategy lives.
- **Empty repositioning** falls straight out of the imbalance: boxes accumulate at the wrong end.
  ~$300–500/TEU to move them back, or refuse marginal backhaul cargo and eat the equipment
  shortage. Industry-wide roughly 20–25% of all moves are empties.
- **Reefer plugs** are limited, high-yield slots — capex to add, strong margin, seasonal demand.
- **Slot sharing / VSA:** sell slots on your service to other operators (steady, low-risk income
  that de-risks a thin lane), or buy slots on theirs to offer a lane you don't serve. This is the
  alliance mechanic in miniature and it's the right amount of it.

### Costs

Terminal handling is the line that surprises people, and it should surprise the player too:

| Cost | Basis |
|---|---|
| Vessel cost | Owned (OPEX + debt) or **chartered in** at TC hire — a real strategic choice |
| Bunkers | At service speed, with ECA legs on the North Europe and North America ends |
| **Terminal handling (THC)** | $150–250 per move by port. A 14,000 TEU ship working 6,000 boxes at Rotterdam ≈ **$1.1M in THC alone** |
| Port DA + canal | TEU-based tariffs, ~$95/TEU laden through Suez |
| Empty repositioning | Per TEU, driven by the imbalance above |
| Inland haulage | Per-TEU abstraction where the O–D pair is inland rather than port-to-port |
| **EU ETS** | This is where carbon genuinely bites — full scope on intra-EU legs, 50% on the Asia inbound, across ~1,000 mt/day of fleet burn |

### Schedule reliability

Congestion, weather and port productivity create delays, and **delay propagates around the loop** —
a ship late out of Yantian is late into Rotterdam and late back to Shanghai. **Buffer days** are the
defence: build slack into the rotation (costs a ship in the string) or run tight and miss schedules.
Reliability drives contract renewals and rate premiums. Real-world schedule reliability has ranged
from ~30% to ~85%, so there's enormous room for the player to be good or bad at this.

### Contracts vs spot

Annual contracts with BCOs lock in 50–70% of slots at a fixed rate negotiated once a year; the
remainder sells at index. Structurally the same spot-versus-fixed tension as the bulk side, which
is a pleasing consistency rather than a new concept to teach.

### Making this work on a phone

Services live inside **Charter** as a fourth segmented option, not a sixth tab. Each service is one
card: `AE7 Asia–NEur · 11× Neopanamax · weekly · 89% util · +$4.2M/mo`. Tapping opens the rotation,
string, speed slider (with live string-size implication), utilisation by leg, and reliability.

Service *creation* is the one genuinely complex flow in the whole game, so it's a guided sequence:
pick a lane from ~10 presets → pick frequency → **the game computes required string size at your
chosen speed** → assign ships (own or charter in) → set contract/spot mix → launch. The presets
carry the geography; the player carries the decisions.

**Onboarding matters here.** The segment opens with a **2-ship regional feeder** service
(Singapore → Jakarta → Surabaya, weekly, 2× 1,700 TEU) so the player learns string sizing,
utilisation and reliability at trivial scale before committing 11 ships and $1B to Asia–Europe.

This is a substantial addition — it becomes **Phase 6**, and it pushes the single-file estimate to
roughly 450–600 KB. Flagged in open question 8.

---

## 6. Onboarding and balance

Complexity is gated by *reason to care*, per the table in §1. Additional gates:
drydock at voyage ~15 (when the first survey window actually arrives), PSC at voyage ~18,
S&P at stage 4, carbon at 3+ ships trading EU, vetting on the first tanker.

**Skill must pay.** Three levers separate a good player from a rate-chaser, and I want each to be
worth roughly 15–30% on annualised return:

1. **Ballast discipline** — staying in liquid markets. Worth ~20–35% on fleet TCE, because ballast
   days are pure denominator.
2. **Speed optimisation** — the optimum genuinely moves with bunker price and freight rate. When
   rates are high, steam fast (days are valuable); when rates are low and bunkers dear, slow down.
   A player who re-optimises each fixture beats a fixed-speed player by ~10–18%.
3. **Counter-cyclical asset trading** — buy at the scrap floor, sell near newbuild parity. Worth
   more than everything else combined over 10 game years, which is realistic and is the intended
   late-game revelation.

**Bankruptcy** via covenant breach, as described in §3. No fake ads, no paywalls, no artificial
timers, no premium currency. Debug panel behind a long-press on the version number in Company →
Settings: add cash, skip days, force market regime (boom/bust/normal), spawn any vessel, trigger
any event.

---

## 7. Technical shape

- One `index.html`. Inline `<style>` and `<script>`. No build step, no bundler, no CDN — charts are
  hand-rolled inline SVG (sparklines and a single history chart; both trivial). Estimated
  250–400 KB.
- **Model/UI separation:** the file is laid out as
  `§CONSTANTS` (all tunables — vessel classes, ports, distances, bunker prices, cost curves, cycle
  parameters) → `§MODEL` (pure functions: `consumptionAt(v, speed, laden)`, `voyageEstimate(...)`,
  `stepMarket(...)`, `computeCII(...)`) → `§STATE` → `§SIM` (tick loop) → `§UI` (render) →
  `§BOOT`. Every constant carries a source comment and a plausible range so you can tune without
  reading UI code.
- **Save:** `localStorage` autosave every 10 s and on `visibilitychange`/`pagehide`, plus
  Export/Import as JSON with a schema version and a migration hook. If `localStorage` throws
  (private mode, sandboxed iframe), fall back to in-memory state and show a persistent hazard-orange
  banner saying so.
- **Offline progress:** on load, compute elapsed wall time, cap at **12 hours** of real time, run
  the sim forward in coarse steps (voyage-completion granularity, not per-tick), then show a
  *"While you were away"* sheet: voyages completed, TCE each, events that fired, cash delta.
  Decisions that require player input are **deferred, not auto-resolved** — ships that finished a
  voyage sit idle rather than auto-fixing, and events queue up.
- **Performance:** fixed 250 ms sim tick with accumulator, render on `requestAnimationFrame` only
  when dirty. Card lists are plain DOM with a hard cap of ~60 nodes. No canvas animation loop, no
  physics, no per-frame layout thrash. A 40-ship fleet is ~40 float updates per tick — nothing.

### Build phases (each ends playable)

| Phase | Delivers |
|---|---|
| **1** | Engine + Fleet/Charter tabs + dry bulk spot voyages + real ports/distances + TCE + tutorial + save/offline. Playable end-to-end. |
| **2** | Bunkers, speed optimisation, ECA, port DAs, laytime/demurrage, full voyage breakdown sheet, Market tab with indices and charts. |
| **3** | Time charter / bareboat / COA, S&P and Shipyard, asset value cycle, debt and covenants, bankruptcy, Company tab. |
| **4** | Compliance: drydock/surveys, PSC, flag/class/registry incl. NIS/NOR and tonnage tax, CII, EU ETS, FuelEU, vetting. Tankers + Worldscale. |
| **5** | Specialised tonnage (LNG/VLGC/PCTC/reefer/heavy-lift/PSV/AHTS/wellboat), canals and seasonality, war risk, events, FFA and bunker hedging, NIS/NOR and tonnage tax, glossary, debug panel, polish. Boxships chartered out to AI operators. |
| **6** | **Full liner service** (§5b): services, strings, rotations, string sizing vs service speed, headhaul/backhaul imbalance, empty repositioning, THC, schedule reliability and buffers, BCO contracts vs spot, slot sharing/VSA, charter-in. Feeder tutorial service first. |

---

## 8. Numbers I'd most want you to check

Ranked by how much I doubt them and how much they'd distort the game if wrong:

1. **Secondhand asset values (§2.4).** Highest uncertainty by far. These swing 2–3× across a cycle
   and my figures are one snapshot of "mid-cycle". If the base is off, the entire S&P strategy — the
   late-game core — mis-scales. I've floored them at scrap, which limits the damage.
2. **Port disbursement accounts.** $28k Riga / $34k Ghent for a Handysize. I'm reasoning from GT-scaled
   European tariffs. Regional variation is huge (Australian and Brazilian DAs are much higher than
   my scaling suggests) and I may be understating slow/remote ports by 30–50%.
3. **Cargo handling rates per port.** 10,000 mt/day loading grain at Riga, 8,000 discharging at
   Ghent, 40,000 mt/day iron ore at Qingdao. Real rates vary by terminal, not port, and directly set
   voyage days — which directly sets TCE. A 20% error here moves TCE ~8%.
4. **Specialised segment day rates.** LNG at $40–180k/day, wellboats at $28–48k, AHTS at $15–70k.
   These markets are thin and I'm least confident here. The Norwegian aquaculture and offshore
   numbers especially.
5. **Newbuild prices.** Yard-slot dependent and up sharply in recent years. My figures may be low
   for 2026 delivery.
6. **OPEX build-up.** $4,800/day for a 20-year-old Handysize feels right in aggregate; the crew/
   insurance/repair *split* is more of a guess, and it matters because the registry and flag levers
   act on components, not the total.
7. **Canal toll formula.** My tiered $/DWT reproduces headline transits within ~15%, but the real
   SCNT calculation and Panama's auction mechanics are more complex. Directionally right.
8. **Scrap $/ldt and LDT-to-DWT ratios.** Confident on price ($480–560/ldt), less so on LDT ratios
   for the specialised classes.

Deliberately *not* on this list because I'm confident: cubic speed/consumption relationship, TCE
definition, CO₂ emission factors per fuel, EU ETS phase-in percentages and scope rules, FuelEU
penalty formula, commission structure, survey intervals, ECA boundaries.

---

## 9. Open questions

### Resolved

1. **Time compression** — 1 game day = **5 real seconds**. 47-second tutorial voyage, ~10 game years
   per 5-hour session, two full cycles. Player gets 1×/2×/4×/8× and pause, defaulting to 1×.
2. **Containers** — **full liner service**, designed in §5b. Becomes Phase 6. Charter-out to AI
   operators remains as the low-risk alternative path into the segment.
3. **Norwegian angle** — **mid-game strategic unlock.** Generic start; registry choice (NIS/NOR/
   Panama/Liberia/Marshall Islands) and tonnage tax become a real lever at stage 4–5. Wellboats and
   PSV/AHTS are a proper late specialised segment, not the framing.
4. **Bankruptcy** — **restructuring path.** Covenant breach (LTV > 80% or cash below minimum
   liquidity undertaking) → 60-day cure period → if unresolved, the bank enforces: fleet goes, player
   continues with one ship and a wrecked credit rating (higher margins, lower max LTV, some
   charterers refuse them). Real consequence, run survives.

### Assumed unless you say otherwise

5. **Segment rollout** — dry bulk complete first (Phases 1–3), then compliance and tankers
   (Phase 4), then specialised (Phase 5), then liner (Phase 6). Each phase ends playable. The
   alternative — all segments present but thin from the start — would make Phase 1 shallow
   everywhere instead of complete somewhere, so I'd rather go deep first.
6. **Setting** — **contemporary**, starting 2026. Red Sea rerouting live as a market regime,
   EU ETS at 100% phase-in, ECA including the Med. Reason: every number in §2 is calibrated to
   roughly-now, so a historical start would mean recalibrating the whole cost base to a different
   decade and I'd rather spend that effort on the model. Say the word if you want a 2002
   pre-supercycle start — the arc is genuinely better, it's just a different tuning pass.
7. **Difficulty** — one tuned curve, no switch. The restructuring path already softens the failure
   state, and a difficulty selector tends to mean two curves tuned badly instead of one tuned well.
8. **File size** — with the liner service in, one HTML file lands around **450–600 KB**. That's
   fine for load performance (it's text, gzips to ~80 KB, no assets) but it is a big single file to
   navigate. Mitigated by the strict `§CONSTANTS → §MODEL → §STATE → §SIM → §UI → §BOOT` layout.
   Flag it now if you'd rather trim scope than carry the size.

---

*Awaiting approval before implementation. Nothing is built yet.*

---

## Appendix A — Phase 1 as built

Phase 1 ships in `index.html`: one self-contained file, no build step, no
dependencies, ~137 KB (42 KB gzipped). Engine, Fleet and Charter tabs, dry bulk
spot voyages over a real sea-routing graph, TCE, the tutorial, save/offline.
Market opens at voyage 8; Yard and the full Company P&L follow in Phase 3.

### Where the shipped model differs from §2.2

The doc's worked example was computed by hand. The game computes port time from
each terminal's actual productivity, so the numbers moved:

| | Doc §2.2 | As built | Why |
|---|---:|---:|---|
| Cargo | 26,000 mt | 27,099 mt | Intake solved from deadweight less bunkers/constants, not assumed |
| Freight | $11.00/mt | $11.14/mt | Index opens at 112, plus per-fixture dispersion |
| Voyage days | 9.35 | 10.83 | Riga 11,400 mt/day load, Ghent 8,000 discharge, +0.5d per call for berthing and documents |
| **Gross voyage profit** | **$137,512** | **$137,017** | — |
| **TCE** | **$14,707/d** | **$12,649/d** | Entirely the 1.5 extra days of port time |
| Port disbursements | $62,000 | $62,000 | Exact — a Handysize is the 20,000 GT reference size |
| Carbon | $17,323 | $26,654 | The doc priced ETS at the 2025 70% phase-in; the game is set in 2026 at 100% |

The TCE move is the honest one: **$12,649 is a better number than $14,707**,
because it sits on the Handysize mid-cycle benchmark rather than above it, and it
comes out of terminal rates rather than an assumption. It also confirms port
handling rates as the most load-bearing uncertain input in the model (§8, item 3)
— 1.5 days moved TCE by 14%.

At 5 real seconds per game day the opening voyage runs **54 seconds**.

### Calibration achieved

Freight rates were solved, not guessed: for each of the 53 routes the game
inverts the voyage P&L to find the $/mt that yields the segment's benchmark
round-voyage TCE at index 100, biased slightly by port liquidity so awkward
geography clears above the benchmark. Result at index 100:

| Segment | n | Min | Median | Max | Doc target |
|---|---:|---:|---:|---:|---:|
| Handy / Supramax | 43 | $10,495 | **$12,090** | $13,040 | $12,000 |
| Panamax / Kamsarmax | 6 | $14,228 | **$15,010** | $16,058 | $15,000 |
| Capesize | 4 | $21,648 | **$22,241** | $22,396 | $22,000 |

Rates arbitraging to near-equal TCE across routes is not a flattening of the
game — it is what an efficient freight market does. The player's edge comes from
position, speed and cycle timing, not from finding a magically better trade.

### Sea routing

Dijkstra over a 31-waypoint graph; ports carry real distances to each gateway
they can reach, so Suez-versus-Cape will fall out of the graph in Phase 5 rather
than being scripted. Validated against 27 known port pairs:
**5.1% distance-weighted mean absolute error.** The residual is concentrated in
very short intra-basin legs where a hub network always overstates
(Riga–Klaipeda 350 nm against ~250) — small in absolute terms and on no priced
route. Long-haul accuracy is the part that matters and it is good:
Tubarão–Qingdao +5%, Port Hedland–Qingdao +1%, Rotterdam–Singapore −2%,
Newcastle–Chiba 0%.

Four routing bugs were found and fixed by that validation, each of which would
have quietly distorted the economics: Baltic ports routing out through the
Skagerrak and back, Black Sea to Egypt detouring west past Sicily, Singapore to
west India going round the Gulf of Oman, and no direct Caribbean–Brazil leg.

### Cycle behaviour

Over 1,325 simulated days the Handysize index travelled **39.2 to 183.4** — a
genuine boom and bust inside a single long session, with the three segments
correlated but not locked together.

### Known Phase 1 limits

- Insolvency stops the run rather than restructuring; the covenant, cure and
  enforcement path arrives with the debt system in Phase 3.
- Only spot voyage charter. Time charter, bareboat and COA are Phase 3.
- Hull condition, surveys, PSC, CII and vetting are modelled as fields on the
  vessel but not yet exercised — Phase 4.
- Canal transits are not yet priced; no Phase 1 route uses one.

---

## Appendix B — Phases 2–6 as built

`index.html` is now the complete game: **309 KB, 96 KB gzipped**, one file, no
dependencies, no build step. Six phases in, all systems live and tested.

### What each phase added

**Phase 2 — operations.** Bunker planning with per-port price differentials and
a hurry premium when tanks run dry; the speed/consumption trade with a solver
that sweeps the whole curve rather than assuming an analytic optimum; ECA fuel
switching computed from the fraction of each leg inside one; laytime, demurrage
and despatch.

**Phase 3 — employment and capital.** All four employment types: voyage charter,
time charter (charterer pays voyage costs, hire stops on off-hire), bareboat
(no OPEX at all), and COA with a real performance penalty. S&P board with a
bid/ask spread, newbuild ordering on a 24–36 month lag with a 20% deposit,
demolition priced per lightweight tonne. Mortgages with LTV and minimum-liquidity
covenants, a 60-day cure period, and enforcement.

**Phase 4 — class and compliance.** Intermediate and special surveys on the real
5-year cycle, with deferral available once at a compounding cost. Port State
Control with Poisson deficiencies and detention. Seven flags and seven class
societies trading crew cost against inspection frequency and charterer
acceptance. CII/AER on the IMO reference-line form with annual tightening.
Oil-major vetting gating tanker cargoes. Tankers on Worldscale.

**Phase 5 — the world.** Canal tolls on regressive tonnage tiers with Panama
drought restrictions and Suez draft limits; the Cape alternative as a first-class
choice. Seasonal weather by basin, Baltic ice, the Northern Sea Route open
July–November to ice-classed tonnage. War risk premiums with armed guards. Twelve
decision events. FFAs and bunker swaps. Gas, car carriers, reefers, project
cargo, offshore support and live fish carriers.

**Phase 6 — liner.** Services, strings, rotations, and the string-size/speed
trade; headhaul/backhaul imbalance and the empty repositioning it forces;
terminal handling; schedule reliability with buffer ships; BCO contract cover
against spot.

### Calibration, all solved rather than guessed

Every segment's rates were found by inverting the voyage P&L for the benchmark
round-voyage TCE at mid-cycle, then verified:

| Segment | n routes | Median TCE at index 100 | Target |
|---|---:|---:|---:|
| Handy / Supramax | 43 | $12,090 | $12,000 |
| Panamax / Kamsarmax | 6 | $15,010 | $15,000 |
| Capesize | 4 | $22,241 | $22,000 |
| MR / LR1 product | 8 | $25,063 | $25,000 |
| Suezmax | 6 | $38,104 | $38,000 |
| VLCC | 5 | $42,321 | $45,000 |
| LNG carrier | 7 | $82,882 | $80,000 |
| VLGC | 3 | $44,055 | $45,000 |
| PCTC | 6 | $48,880 | $52,000 |
| Reefer | 5 | $19,991 | $21,000 |
| Heavy-lift | 5 | $24,050 | $26,000 |

Liner lanes were solved the same way, to EBIT per ship-year: **$9.5M deep-sea,
$3.5M regional**, at a 12–26% margin and $1,111/TEU all-in cost against a real
$1,100–1,400. The Asia–North Europe string table:

| Service speed | Round voyage | Ships for weekly | Burn/ship | **Fleet burn** | EBIT/yr |
|---:|---:|---:|---:|---:|---:|
| 14 kn | 97.6 d | 14 | 57 mt/d | **794 mt/d** | $212M |
| 16 kn | 88.6 d | 13 | 81 mt/d | **1,056 mt/d** | $185M |
| 18 kn | 81.6 d | 12 | 113 mt/d | **1,352 mt/d** | $152M |
| 20 kn | 76.0 d | 11 | 152 mt/d | **1,672 mt/d** | $114M |
| 22 kn | 71.4 d | 11 | 200 mt/d | **2,200 mt/d** | $58M |

Slowing the service needs more ships and burns far less fuel. The builder screen
shows the arithmetic — `ceil(88.6 ÷ 7) + 1 buffer` — rather than hiding it.

Canal tolls verified against headline transits: Suez Handysize $101,520,
Capesize $468,000, VLCC $713,000, Neopanamax $840,000, ULCV $1.38M; Panama
Kamsarmax $256,400 rising to $518,900 in a drought year. A laden VLCC at 22 m
draft is excluded from Suez and routed round the Cape, as she is in life.

Special survey costs were re-fitted to `k × GT^0.6` after linear-in-GT put a
Neopanamax at $11M: now $1.10M Handysize, $2.74M Capesize, $3.81M VLCC.

Sea routing validated against 27 known port pairs at **5.1% distance-weighted
mean absolute error**.

### Bugs the calibration and playtesting caught

Each of these would have silently distorted the economics:

1. Baltic ports routing out through the Skagerrak and back (Riga–Klaipeda
   1,120 nm against ~250).
2. Black Sea to Egypt detouring 1,300 nm west past Sicily.
3. Singapore to west India routing round the Gulf of Oman (4,160 vs ~2,400).
4. No direct Caribbean–Brazil leg (Houston–Santos 9,120 vs ~6,000).
5. The Northern Sea Route open year-round to any ship, making every Asia–Europe
   voyage 7,460 nm.
6. `cargoIntake` dividing by `grainCubic`, which tankers do not have — every wet
   fixture returned NaN tonnes.
7. `newMarket` seeding only the three dry indices, so the eight new ones began
   with `phase === undefined` and NaN-cascaded through hire, liner revenue and
   finally cash.
8. `burnFuel` ignoring the on-hire flag, so a ship on time charter paid for the
   charterer's bunkers and posted negative TCE on voyages that earn hire.
9. Port disbursements billed only on a phase *transition*, so a voyage starting
   at the load port never raised the load-port bill.
10. The ballast leg hidden before its unlock but still counted in the displayed
    total — visible arithmetic that did not add up.
11. Liner economics 5× too profitable, from omitting container equipment, inland
    haulage, commission, admin and network overhead.
12. Feeder services structurally loss-making, from charging the feeder operator
    full terminal handling that in reality sits with the deep-sea principal.

### Verified behaviour

- Time charter: zero bunkers and zero port DA to our account, TCE exactly hire
  less 3.75% commission, CII still accruing from the charterer's voyages.
- Restructuring: 19 ships to 1, debt released, rating D, run continues.
- Save/load round-trips with a live fleet, services, COAs and open derivatives.
- Eleven indices stay finite over 1,000 days and decorrelate properly — in one
  run containers reached 143 while crude sat at 37.
- Tonnage tax vs profits tax resolves annually and reports which basis applied.

### Still abstracted, deliberately

Bareboat ships are not navigated (there is nothing to simulate — that is the
point of a bareboat). Time-charter and COA ships run shadow voyages so position,
wear and CII stay real while the charterer makes the routing decisions. Liner
services accrue against a rotation model rather than tracking each box. The
seven items flagged in §5 as too fiddly to be fun remain out.

---

## Appendix D — Utilisation fix

Player report: money bleeding away because there was rarely anything on offer at
the discharge port, leaving ships idle for days or weeks.

Measured before touching anything, playing a single Handysize optimally for a
year: **91.7% utilisation, 30.3 idle days — and all 30.3 of them with an empty
board.** Not one idle day was a rate the player had declined. Three distinct
faults, none of them a tuning problem:

**1. Sixteen discharge ports had no outbound cargo at all.** Casablanca received
five inbound trades and offered nothing back, and it is one of the commonest
Handysize discharge ports in the game. Unrealistic as well as unfair — Morocco is
the world's largest phosphate exporter. Added 18 dry and 3 wet backhauls that
exist in life and were simply missing: Casablanca phosphate and DAP, Turkish and
Italian steel, German scrap into Turkey (the largest scrap trade in the world),
French grain to North Africa, US east and west coast scrap exports, Gulf products
out of Jebel Ali and Fujairah. Dead-end ports: 16 → 2, and both remaining ones
are correct (an LNG carrier discharging Zeebrugge ballasts home — that *is* the
trade; Stavanger is a day-rate offshore base).

**2. The board was only rebuilt on a 6-day timer.** A ship discharging just after
a refresh sat with literally nothing to accept, paying OPEX for every day of it.
The board is now generated the instant a ship becomes free (`ensureBoard`, called
from voyage settlement and charter redelivery), tops up rather than being wiped —
so an offer the player is part-way through reading does not vanish — and offers
lapse individually after 12 days.

**3. Ship's gear was required at every port without shore cranes.** But a crude
terminal loads through hoses and an ore berth through a shiploader, which is
precisely why Capesizes, VLCCs and LNG carriers are all gearless. The check shut
those segments out of their own trades entirely — a VLCC at Rotterdam, a Capesize
at Qingdao and an LNG carrier at Sodegaura each had **zero** offers. Gear is now
required only for cargo that must be lifted (steel, scrap, forest products,
project).

Also fixed: a flat 3,000 nm ballast cap, which stranded every long-haul segment
(a VLCC discharging Rotterdam *must* ballast ~6,400 nm back to the Gulf). The cap
now scales with the voyage — `clamp(ladenNm × 1.6, 1800, 9000)`.

Two supporting changes: the board search widens progressively at a genuinely
awkward port, so a thin position yields *bad* options rather than none — with
negative-TCE quotes suppressed unless the board would otherwise be bare — and the
onward-liquidity line on each fixture card now shows from the first voyage rather
than waiting for the ballast unlock, turning an invisible trap into a visible
decision.

### After

| | Before | After |
|---|---:|---:|
| Utilisation, played optimally | 91.7% | **100%** |
| Idle days per year | 30.3 | **0** |
| Idle days with an empty board | 30.3 | **0** |
| Worst wait between fixtures | 5.5 d | **0 d** |
| Voyages per year | 13 | 14–16 |
| Cash after one year | $3.94M | $3.19M |
| Lowest cash in the first 60 days | — | $359,590 |

Cash after a year is slightly *lower*, and that is correct: forced waiting used
to be followed by an unusually good board, so the old figure flattered a player
who had no choice in the matter. Utilisation is now the player's decision — hold
out for a better rate and idle deliberately, or take the workmanlike cargo. The
first 60 days still dip to $359k, so the opening stays tight without ever being
dead time.

---

## Appendix E — Voyage working capital

Player report: costs while sailing wipe out the balance after a few fixtures,
especially on voyages over 40 days.

This was a cash-flow timing gap, not a profitability one. Freight was credited
**100% on discharge**, while bunkers, port disbursements, canal tolls, war risk,
OPEX and debt service were all paid *during* the voyage. So the player funded the
entire voyage out of pocket and was repaid only at the end.

Measured on the opening position ($649,061 cash, one 22-year Handysize):

| Voyage | Days | Net freight | TCE | Cash needed to reach discharge |
|---|---:|---:|---:|---:|
| Riga → Ghent | 11.2 | $242,818 | $8,980 | $212,447 |
| Ghent → Aliaga | 24.3 | $386,931 | $6,394 | $388,130 |
| Casablanca → Santos | 37.0 | $602,469 | $7,873 | $541,497 |
| Casablanca → Mumbai | 43.5 | $899,629 | $8,253 | **$810,680** |

That last one is a **profitable** voyage that bankrupts you before it pays.

### The fix is what the charterparty actually says

Voyage charters pay the bulk of the freight against the **bill of lading** — once
cargo is loaded — with the balance on right and true delivery. Standard terms are
95/5. So `CFG.FREIGHT_ON_BL = 0.95`: 95% credited on completion of loading, the
balance on discharge. If an event later cuts the total below what was advanced,
there is simply no balance to collect — you keep the advance, which is also what
happens in life.

Simulated from the real opening position, the advance now lands on day 2.5–4.8 on
a direct voyage, and the cash trough never goes negative on any of them.

### Made visible rather than merely survivable

A voyage can be profitable and still be one you cannot afford, so the fixture
sheet now carries a **Cash flow** section: the freight terms, what the advance is
and which day it arrives, the working capital needed until then, and what you
actually hold. Short of it and the card carries a `Needs $X cash` chip, the
section turns red, and the fix button reads *"Fix anyway — you cannot fund this"*.
Informed, not blocked.

### Result — 180 days, five seeds, three strategies

| Strategy | Avg voyage | Went bust | Worst cash trough | Avg equity |
|---|---:|---:|---:|---:|
| Best TCE (expert) | 23.7 d | 0/5 | $533,027 | $3.51M |
| Biggest headline $ (new player) | 33.7 d | 0/5 | $416,025 | $3.41M |
| Always the longest voyage | 30.6 d | 0/5 | $265,081 | $2.68M |

Nobody goes bust from timing any more, and skill still separates the strategies —
chasing the biggest number on the card costs about $840k of equity over half a
year against reading TCE, and always taking the longest voyage costs $830k more.
The punishment moved from "you are dead" to "you are behind", which is where it
belongs.

Also fixed here: `.kv` rows had `white-space: nowrap` on the value, so any
sentence-length value overlapped its own label. Added a stacked variant and
applied it to the four rows that carry prose.

---

## Appendix F — Fuel was being paid for twice

Player report: still too expensive; ran out of money after five contracts,
having had to buy bunkers. "Money flows out of my account like a waterfall
when on a voyage."

Two faults, one of them a straight accounting bug.

### 1. Fuel was charged on the lift AND on the burn

`doBunker` debited cash when fuel was lifted, and `burnFuel` debited cash again
as the same fuel was consumed. Buy 500 mt and you paid for it twice. The bug
only surfaced if the player used the Bunker button — which is exactly what the
report describes, and why the voyage estimate had always reconciled against
actuals in testing (a ship that never lifts consumes only the free stem she was
created with, and pays once).

Now there is one rule: **cash moves when you buy fuel** — a lift, the voyage
stem, or an emergency purchase at sea. Drawing on what is already in the tanks
is not a cash movement; it is inventory already paid for. It is still charged to
the voyage P&L at cost, which is what keeps TCE honest and matches how a real
voyage account is drawn up.

### 2. The Bunker button defaulted to a ruinous purchase of the wrong grade

From the opening position, one tap offered **540 mt of VLSFO for $301,320 — 46%
of all cash — while the ship lay at Riga, inside the Baltic ECA, where VLSFO
cannot legally be burned.** The sheet showed tonnes and never mentioned that she
had 6.8 days of fuel aboard.

Rebuilt around endurance rather than tonnage:

- Grade defaults to what she can actually burn where she is — MGO inside an ECA.
- The lift defaults to a **working stem of about three weeks' steaming**, not
  45% of tank capacity. At Riga that is 31 mt for $23k (4% of cash) instead of
  540 mt for $301k (46%).
- An **Endurance** panel leads: what is aboard, what she burns per day, and how
  many days that is — coloured red under 8 days.
- Quick presets for 10 / 21 / 35 days and Fill.
- A warning when a lift exceeds 25% of cash.
- And the note that matters most: **you do not have to buy anything here.** Fuel
  for a fixture is bought at the load port and already priced into the estimate.
  Lifting early is an arbitrage decision, not a chore.

### Supporting changes

- **Voyage stem**: fuel the voyage still needs is bought at the load port at the
  screen price, timed to the same moment as the freight advance so the two
  largest cash movements offset. Previously the tanks simply ran dry mid-ocean
  and the player paid a hurry premium on fuel the estimate had quoted at the
  normal price.
- The opening ship now carries a stem weighted to her trade — 340 mt MGO / 240 mt
  VLSFO, since the Baltic and North Sea are an ECA throughout. Endurance at the
  start went from 6.8 days to 19.3.
- Ships bought or delivered elsewhere get a stem matched to where they lie.
- The fixture sheet's Cash flow section now shows the fuel to be bought at the
  load port and the **net cash when she loads** (advance less stem).

### Five consecutive contracts from the opening position

| # | Route | Days | TCE | Cash start | Trough | Cash end |
|---|---|---:|---:|---:|---:|---:|
| 1 | Riga → Ghent | 11.3 | $13,504 | $650,000 | $602,442 | $786,628 |
| 2 | Ghent → Aliaga | 20.0 | $14,475 | $786,628 | $723,292 | $1,061,931 |
| 3 | Iskenderun → Antwerp | 29.0 | $12,129 | $1,061,931 | $978,483 | $1,334,023 |
| 4 | Amsterdam → Iskenderun | 23.5 | $16,428 | $1,334,023 | $1,257,247 | $1,593,241 |
| 5 | Iskenderun → Antwerp | 28.5 | $19,391 | $1,593,241 | $1,505,675 | $1,994,846 |

Monotonic. Equity $2.10M → $3.74M over 112 days. Stress-tested over 180 days
across five seeds: no strategy goes bust, and skill still separates them —
$3.74M equity reading TCE against $2.97M always taking the longest voyage.

---

## Appendix G — "Still bleeding money on a voyage"

Measured a full voyage day by day, attributing every cash movement:

```
ballast       1 day    steady −$6,519/day
loading       4 days   steady −$6,519/day   lump −$24,000 (load port DA)
laden         4 days   steady −$6,519/day   lump +$298,928 (95% freight advance)
discharging   5 days   steady −$6,519/day   lump −$36,000 (discharge port DA)
```

**There is no leak.** The rate under way is identical to the rate docked, and the
lumps are all identified. The player's perception is nonetheless correct: OPEX and
debt service never stop, so after the freight advance lands on day 6 they watch
eight more days of steady outflow. On a 40-day voyage that is 34 days of visible
drain on a voyage that is comfortably profitable.

Two real faults surfaced in that $6,519, and one presentation gap.

### 1. Age was charged to OPEX twice

`opexDay()` already scales with age (+1.2%/yr over 10). A ship's starting hull
condition is *also* derived from age (`1 − (age−8)×0.012`), and `accrueDaily`
multiplied OPEX by `1 + (1−hull)×0.35`. So a 22-year Handysize in perfectly normal
condition for her age paid a 5.9% "condition" penalty on top of the age slope she
had already paid.

The condition penalty is meant to price **neglect**, not age. It now measures the
shortfall against an age-appropriate hull (`expectedHull(age)`), so a ship in
normal order pays nothing and a hard-run one pays more than before (coefficient
raised 0.35 → 0.6 now that it only captures neglect).

### 2. The opening flag was Norway NIS

Which costs +7% on OPEX for a tonnage-tax benefit worth nothing until profits are
large — and contradicted this document's own resolved decision (§9.3: *generic
start; registry becomes a real lever at stage 4–5*). Corrected to Marshall Islands
with BV class. NIS remains available and is still the right answer later.

Daily fixed charge on the opening ship: **$6,519 → $5,808, down 10.9%.**

### 3. The estimate quietly flattered itself

`voyageEstimate` computed OPEX without the condition multiplier that
`accrueDaily` applied, so every quoted "Net contribution" was optimistic by ~6%.
Both now call one shared `opexDayFull(cls, age, ship)`. Quoted $54,572 against
charged $54,802 on a test voyage — agreement within 0.4%.

### 4. The falling balance had no counterweight on screen

While a ship is at sea the balance drops every single day and nothing told the
player the voyage was winning. The Fleet card now carries the running result:

`Riga → Ghent · 5.2d to go · +$74k`

Not a softened cost — the other half of the ledger, shown continuously.

Five consecutive contracts from the opening position now run $650,000 →
$2,074,841, trough never below $604,577, equity $2.10M → $3.82M in 112 days.
