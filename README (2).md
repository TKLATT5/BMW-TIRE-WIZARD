# TIRE-WIZARD

Internal BMW Motorrad tire reference and recommendation tool for parts & accessories sales. Built as a single self-contained web page, deployed on GitHub Pages, and used at the counter to match customers to the right tire fast.

**Live:** https://tklatt5.github.io/TIRE-WIZARD/

---

## What it does

TIRE-WIZARD has two modes, switchable by the tabs at the top:

**1. Tire Recommendation Wizard** — Walk a customer through their bike, riding style, conditions, and priorities, and get a ranked set of tire recommendations. Shows the bike's OEM front/rear size and rim widths up front so you can confirm fitment before anything else, and flags load-index considerations on heavy bikes (RT, K 1600, R 18, GS Adventure) where two-up loaded touring matters.

**2. Quick Tire Browser** — Every stocked tire on one screen, grouped by riding style. Tap a style chip to focus on one group, or use the filters (BMW model family, brand, conditions, free-text search) to narrow down. Built for the times you just need to see the whole catalog organized, not answer a questionnaire.

Everything is scoped to **tires the dealership actually stocks** — not a generic tire database.

---

## Coverage

- **112 BMW models** across 9 categories: GS/Adventure (12), F-Series GS (19), R-Series Roadster (25), S-Series Sport (11), K-Series (18), F-Series Street (8), Cruiser (6), R 12 Heritage (5), Scooter (8).
- **33 tires** across 8 brands: Michelin, Dunlop, Metzeler, Mitas, Pirelli, Continental, Shinko, Heidenau.
- Coverage runs from current models (R 1300 GS, CE 02, R 18 Roctane) back through oilheads, classic K-bikes, and the F 650 singles.

The Quick Tire Browser organizes tires into 7 riding-style groups:

| Group | What it covers |
|---|---|
| Off-Road / Knobby | Serious dirt, rock & sand — 50/50 to 90% off-road |
| Adventure 50/50 | Even split, equal road and trail |
| Adventure-Road / Crossover | Road-biased ADV & street crossover (incl. Dunlop Mutant) |
| Sport-Touring | All-day road, big mileage, strong wet grip |
| Sport / Track | Maximum grip — canyon & track |
| Cruiser / Bagger | Bias-ply, heavy V-twin & bagger touring |
| Scooter / Urban | Small wheels, wet city grip |

---

## How to run it

It's a single `index.html` file with no build step, no dependencies, and no backend. To work on it:

1. Clone or download the repo.
2. Open `index.html` in any browser. That's it.
3. Commit and push to `main` — GitHub Pages redeploys the live site automatically.

---

## Editing the data

All data lives in two JavaScript objects inside `index.html`. No database, no external files.

### Add or fix a bike — `BIKES`

Bikes are grouped by category. Each entry looks like:

```js
{model:"R 1300 GS (2024-present)", years:[2024,2025,2026],
 front:"120/70 R19", frontRim:"3.00x19",
 rear:"170/60 R17", rearRim:"4.50x17",
 note:"Flagship adventure tourer.",
 bikeType:"gs_big", terrain:["offroad","mixed","road"]}
```

Heavy bikes also carry `weightKg`, `loadLevel`, `minLI` (minimum load index front/rear), and a `twoUpNote` so the wizard can warn on loaded touring.

`bikeType` controls which tires match. Valid values: `gs_big`, `gs_small`, `gs_adv`, `g310gs`, `r_series`, `s_series`, `f_street`, `g310r`, `rt`, `r18`, `ce04`, `c400`.

### Add or fix a tire — `TIRES`

```js
{brand:"Dunlop", name:"Mutant",
 spectrum:[3,5], bikes:["gs_big","gs_small","r_series","f_street","s_series","g310r","g310gs"],
 weather:"all-weather", terrain:["road","mixed","sport"], priority:["value","grip","mileage"],
 tread:"Crossover Street", split:"85% Road / 15% Light Off",
 wetRating:5, dryRating:4, mileageRating:4, speedRating:"W/ZR",
 bestFor:"All-weather street, nakeds, sport-tourers, road-biased ADV",
 oem:"", features:["IN STOCK at dealership", "..."],
 loadIndex:{front:69,rear:73}, heavyBikeOk:false,
 revzilla:"https://...", desc:"..."}
```

Key fields:
- `spectrum` — `[min, max]` on a 1–7 riding-style scale (1 = full off-road, 7 = track). This drives both the wizard match and the Quick Tire Browser grouping.
- `bikes` — which `bikeType` values this tire fits.
- `weather` — `wet`, `dry`, or `all-weather`.
- Put `IN STOCK at dealership` in `features` to get the green "In stock" badge.
- Set `oem` to a non-empty string to get the "OEM" badge.

**Riding-style grouping is automatic.** The browser reads each tire's `spectrum`, `terrain`, and `tread` to assign its group — you don't tag the group by hand. Add a tire and it drops into the right section on its own.

---

## Important notes for the counter

- **Always confirm the OEM size shown in Step 1 before ordering.** A few entries carry a "confirm" note where BMW changed sizes across model years (e.g. R 850 R early vs. late, F 650 ST Strada front).
- **Vintage bikes return limited or no matches by design.** Classic K-bikes (K1, K75, K100) and older F 650 singles run bias-ply / odd-size tires the dealership doesn't stock modern radials for. The tool still shows their correct OEM size for reference; the empty result is telling you "we don't carry modern rubber in this size."
- **The Dunlop Mutant has no 21-inch front** — it does NOT fit the F 850/900 GS or any 90/90-21 bike, even though it fits a huge range otherwise.
- **R 18 Roctane is the odd one out** in the cruiser family: 21-inch front / 18-inch rear, not the 16-inch rear of the other R 18 baggers.

---

## Changelog

**Latest**
- Expanded the model list to 112 bikes — added 26 models across Tier 1 (modern: G 650 X-series, HP2 trio, R 18 B / Roctane, CE 02, K 1600 GTL Exclusive, and more) and Tier 2 (classic K-bikes and F 650 singles).
- Rebuilt the **Quick Tire Browser**: replaced the single-point riding-style slider with an all-tires-visible layout grouped into 7 riding-style sections, with tappable style chips, free-text search, a reset button, and at-a-glance Wet/Dry/Miles ratings on each card.
- Added the **Dunlop Mutant** (now in stock) and corrected its fitment to its true crossover range.
- Fixed OEM data errors uncovered during the audit: **G 310 GS** (110/80-19 / 150/70-17), **R nineT Scrambler** (120/70-19 / 170/60-17), **R 1200 C** (170/80-15 rear).

---

## Project notes

- Single-file app: HTML + CSS + vanilla JS, no frameworks, no build.
- Internal sales reference — stock tires only.
- Data is hand-maintained against BMW OEM specs; verify against the fiche before ordering.
