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
| **5** | Containers, specialised (LNG/VLGC/PCTC/reefer/heavy-lift/PSV/AHTS/wellboat), canals and seasonality, war risk, events, FFA and bunker hedging, glossary, debug panel, polish. |

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

1. **Time compression.** I've assumed 1 game day = 5 real seconds, giving a 47-second tutorial
   voyage and ~10 game years per 5-hour session — enough for two full cycles. Slower makes the
   cycle a story you live rather than watch, but a 5-hour session would only cover one boom.
2. **Segment rollout.** Dry bulk complete first, then tankers, then container/specialised (my plan)
   — or all segments present but thinner from Phase 1?
3. **Containers.** Charter-out asset play (my recommendation — it's what owners do, and liner
   operations is a whole second game), or do you actually want to run a liner service?
4. **The Norwegian angle.** NIS/NOR and tonnage tax as a mid-game strategic unlock, or should the
   player start as a Norwegian owner with that as the framing from minute one? Same question for
   wellboats and offshore — background flavour, or a first-class segment?
5. **Setting.** Contemporary (2026 rates, Red Sea rerouting live, EU ETS at 100%), or start in a
   named historical year — 2002, pre-supercycle — so the boom is a recognisable arc?
6. **Bankruptcy.** Hard game-over with a run summary and restart, or a restructuring path (hand
   equity to the bank, continue smaller) so a 5-hour run isn't deleted?
7. **Difficulty.** One tuned curve, or an explicit easy/realistic/brutal switch at new game?
8. **File size.** A faithful build with ~55 ports, ~24 vessel classes and 5 segments lands around
   250–400 KB in one HTML file. Confirm that's acceptable versus trimming scope.

---

*Awaiting approval before implementation. Nothing below Phase 0 is built yet.*
