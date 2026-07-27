# Packaging SPC Analyzer

**Statistical process control for semiconductor Advanced Packaging / Process Integration data — entirely in your browser.**

[![No install](https://img.shields.io/badge/install-none-3fd0c9)](#quick-start)
[![Single file](https://img.shields.io/badge/build-single%20HTML%20file-5b9bd5)](#whats-in-the-repo)
[![Data stays local](https://img.shields.io/badge/data-never%20leaves%20your%20machine-4fbf7b)](#privacy)
[![License: MIT](https://img.shields.io/badge/license-MIT-a78bfa)](#license)

One HTML file. Open it, drop in a CSV or Excel export from bump, wire bond, or die attach metrology, and get control charts, capability indices, Western Electric rule detection, DOE main-effects and factorial analysis, before-vs-after lot comparison, Cpk forecasting, and a live FMEA — with no server, no upload, and no installation of any kind.

This is the packaging-side companion to **[ETEST SPC Analyzer](../etest-spc-analyzer)**, built to the same constraints and the same look, for the process integration side of advanced packaging: bump, RDL, TSV, wire bond, die attach, and underfill.

---

## Table of contents

- [Why this exists](#why-this-exists)
- [Quick start](#quick-start)
- [Features](#features)
- [Data format](#data-format)
- [Default spec limits](#default-spec-limits)
- [Walkthrough](#walkthrough)
- [How the numbers are calculated](#how-the-numbers-are-calculated)
- [Western Electric rules](#western-electric-rules)
- [DOE module](#doe-module)
- [Known behaviour worth understanding](#known-behaviour-worth-understanding)
- [Performance](#performance)
- [Privacy](#privacy)
- [Deploying to GitHub Pages](#deploying-to-github-pages)
- [Browser support](#browser-support)
- [What's in the repo](#whats-in-the-repo)
- [Roadmap](#roadmap)
- [License](#license)

---

## Why this exists

Advanced packaging process integration produces metrology long before it produces a yield number: bump height off a 3D scanner, warpage off a shadow moiré tool, wire pull off a bond tester, void percentage off a C-SAM scan. That data usually lives in whatever export format the metrology tool happens to speak, gets pasted into a spreadsheet once, and never gets a proper control chart.

The SPC packages built for this world tend to be licensed, tied to an MES, or built for wafer fab and awkward for anything measured in microns of warpage rather than volts and amps. This is the opposite: a single file, no license, no server, built around the parameters that are actually native to packaging — bump, RDL, TSV, wire bond, coplanarity, underfill — with a DOE module for the process window studies that packaging integration runs constantly and ETEST rarely does.

It is built for the engineer with a metrology export open in one window and a disposition meeting in twenty minutes.

---

## Quick start

**Option 1 — just open it**

1. Download `packaging-spc-analyzer.html`
2. Double-click it

The app boots with a simulated lot already loaded, so every tab works before you touch a file.

**Option 2 — use the hosted version**

Open the GitHub Pages link for this repo. Same file, nothing installed.

**Option 3 — your own data**

1. Open the app
2. **Data & columns** → drop a `.csv`, `.xlsx`, or `.xls` file
3. Confirm the column roles the app guessed
4. Set your spec limits on the **Spec limits** tab
5. Everything else populates

---

## Features

### Data handling
- **CSV, XLSX and XLS upload** — drag and drop or browse. Delimiter is auto-detected (comma, tab, semicolon), quoted fields and embedded commas are handled correctly.
- **Built-in sample lots** — realistic packaging process data so the tool is useful before you have an export in front of you.
- **User-mappable columns** — nothing is hardcoded. Any numeric column can be a parameter; lot, unit/panel, and timestamp roles are all assignable.
- **Stress-test generator** — one click builds a 50,000+ row lot to prove the tool holds up on a full-panel or full-lot export.

### Control charts
Every chart shades the 1σ, 2σ and 3σ zones, marks the centre line, and overlays your spec limits.

| Chart | Use it for |
|---|---|
| **Individuals (I)** | Per-measurement behaviour, with rule violations marked in red |
| **Moving range (MR)** | Short-term variation and the σ<sub>within</sub> estimate |
| **X̄** | Lot-level control, subgrouped by lot/panel or by fixed n |
| **R** | Within-subgroup spread |
| **EWMA** | Small sustained shifts, λ adjustable |
| **CUSUM** | Fastest detection of a shift you already suspect, k and h adjustable |
| **Histogram** | Distribution shape against the spec window |

### Capability
Cp, Cpk, Pp, Ppk, sigma level, theoretical PPM, observed PPM, per-parameter yield, σ<sub>within</sub>, σ<sub>overall</sub>, and a drift ratio that tells you at a glance whether the process is noisy or whether it moved. Colour-coded against the usual 1.00 / 1.33 thresholds, with an all-parameters table underneath and CSV export.

### Western Electric rules
All eight rules, each independently toggleable, each with a live hit count. Violations table gives you point number, lot, unit ID, value, distance from centre in sigma, and which rules fired — exportable to CSV.

### DOE — main effects and factorial analysis
In place of a wafer map, this app carries a design-of-experiments module suited to packaging process windows:
- **Main effects plots** for any factor column against any response parameter
- **Full and fractional factorial** analysis with estimated effects for each factor and interaction
- Works directly from a tagged dataset — map which columns are factors and which are responses, the same way you map parameters elsewhere in the app

This is the tab for reflow profile studies, bond force/time/temperature windows, and underfill cure optimization — anywhere you ran a designed experiment rather than just watching a running process.

### Before vs after
Load a baseline into slot A and a comparison lot into slot B. Get mean shift, sigma change, ΔCpk, out-of-control point counts, and yield shift per parameter, with a Cpk bar comparison and a distribution overlay. This is the tab for proving a process change worked.

### Trend and roadmap
Cpk is computed per time block, fitted with least squares, and projected forward with a 95% band. The read-out tells you the slope per block and how many blocks until you reach Cpk 1.33 at the current rate. Underneath, a **variation reduction roadmap** shows what σ has to become for Cpk targets of 1.00, 1.33, 1.67 and 2.00, the percentage reduction that implies, and the PPM you would land on.

### FMEA
A default FMEA seeded with real packaging failure modes — bump bridging, warpage-induced die crack, wire bond lifting, RDL open, TSV via resistance excursion, underfill voiding. Severity, occurrence and detection are editable on a 1–10 scale with RPN recalculating live. Add rows, delete rows, sort by RPN, export to CSV.

---

## Data format

The app expects one row per measurement. A typical packaging metrology export looks like this:

```csv
LOT,UNIT,PANEL_X,PANEL_Y,TIMESTAMP,BumpHeight,Warpage,DieShift,WireBondPull,BallShear,SolderVoid,RDL_Width,TSV_Rc,Coplanarity,UnderfillVoid
L26P0412,U0001,3,-6,2026-06-02 07:14:02,48.21,62.4,3.1,11.8,410,2.4,9.98,0.041,14.2,0.9
L26P0412,U0002,3,-5,2026-06-02 07:14:11,48.05,61.9,2.9,12.1,398,2.1,10.02,0.039,13.8,1.1
```

### Column roles

| Role | Required | Notes |
|---|---|---|
| **Parameters** | Yes | Any numeric columns. Select as many as you like. |
| **Lot ID** | No | Enables lot filtering |
| **Unit / Panel ID** | No | Enables unit-level filtering and subgrouping |
| **Timestamp** | No | Used for ordering context and the trend/forecast blocks |
| **DOE factors** | No | Set on the DOE tab — categorical or numeric columns that were varied in a designed experiment |

Column names are auto-detected on load, but every role is reassignable on the **Data & columns** tab. Nothing is locked.

Rows with non-numeric or empty parameter values are skipped for that parameter rather than dropping the whole row.

---

## Default spec limits

Recognised parameter names are pre-loaded with plausible limits so the app is immediately useful. **These are starting points, not your process window** — set your own on the Spec limits tab.

| Parameter | Unit | LSL | USL | Notes |
|---|---|---|---|---|
| Bump height | µm | 44 | 52 | Two-sided |
| Warpage | µm | — | 80 | One-sided |
| Die shift / placement | µm | — | 5 | One-sided |
| Wire bond pull | gf | 8 | — | One-sided; higher is better |
| Ball shear | gf | 300 | — | One-sided; higher is better |
| Solder void | % | — | 5 | One-sided |
| RDL line width | µm | 9 | 11 | Two-sided |
| TSV via resistance | Ohm | — | 0.08 | One-sided |
| Coplanarity | µm | — | 20 | One-sided |
| Underfill void | % | — | 3 | One-sided |

A blank limit is treated as genuinely one-sided — Cpk uses the single side rather than assuming symmetry. For an unrecognised parameter, limits are auto-fitted at ±4σ of the loaded data, which is a placeholder and nothing more.

**Auto-fit from data (±4σ)** refits every limit at once. Useful for exploring an unfamiliar dataset, dangerous if you forget you pressed it.

---

## Walkthrough

Load the built-in sample lots and open **Before vs after** to see the intended workflow: a baseline lot with a bump-height excursion tied to a reflow profile drift, and a comparison lot after the profile was corrected. The Cpk and yield shift between the two tells the same kind of story the ETEST tool tells with Vt — a capability number that moved because the process moved, not because it got noisier.

From there, the **DOE** tab is where you'd go next in a real investigation: tag the reflow parameters that were varied as factors, pick bump height or void percentage as the response, and read the main effects directly instead of eyeballing a spreadsheet pivot.

---

## How the numbers are calculated

**σ within** — for individuals, MR̄ / 1.128 (d₂ for n = 2). For subgrouped data, R̄ / d₂(n). Cp and Cpk use this, so they describe the process as it could be if it only had its short-term noise.

**σ overall** — the sample standard deviation across every point in the current filter. Pp and Ppk use this, so they describe the process as the customer actually received it, drift included.

**Control limits**

| Chart | Centre | Limits |
|---|---|---|
| I | X̄ | X̄ ± 3σ<sub>within</sub> |
| MR | MR̄ | UCL = 3.267·MR̄, LCL = 0 |
| X̄ | X̿ | X̿ ± A₂R̄ |
| R | R̄ | D₃R̄ and D₄R̄ |

Constants come from the standard d₂ / A₂ / D₃ / D₄ tables for n = 2 to 25.

**EWMA** — zᵢ = λxᵢ + (1−λ)zᵢ₋₁ with limits X̄ ± Lσ√(λ/(2−λ)·(1−(1−λ)²ⁱ)). The limits widen out from the start-up value rather than being flat.

**CUSUM** — standardised tabular form: SH = max(0, SH₋₁ + zᵢ − k), SL = max(0, SL₋₁ − zᵢ − k), signalling past ±h. Defaults k = 0.5 and h = 5 detect a 1σ shift in roughly ten points.

**PPM and sigma level** — theoretical PPM integrates the normal tails beyond each limit using σ<sub>overall</sub>. Observed PPM counts real out-of-spec rows, which is the honest number when the distribution is skewed. Sigma level is Z<sub>bench</sub> + 1.5, following the usual shift convention.

**DOE effects** — main effects are computed as the difference in response mean between factor levels; full and fractional factorial effects follow the standard Yates-style contrast estimate for the design provided.

**Cpk projection** — Cpk is computed per time block, fitted by least squares, and extended forward with a 95% interval from the residual standard error. The roadmap inverts the Cpk equation to find the σ required for each target, holding the mean where it is.

---

## Western Electric rules

| # | Rule | Detects |
|---|---|---|
| 1 | One point beyond 3σ | Gross excursion, single-point failure |
| 2 | Nine consecutive points on the same side of centre | Sustained mean shift |
| 3 | Six consecutive points rising or falling | Drift, tool wear, material lot change |
| 4 | Fourteen consecutive points alternating | Over-adjustment, two-stream mixing |
| 5 | Two of three consecutive beyond 2σ, same side | Sudden moderate shift |
| 6 | Four of five consecutive beyond 1σ, same side | Small sustained shift |
| 7 | Fifteen consecutive within ±1σ | Stratification, or limits that no longer match the process |
| 8 | Eight consecutive beyond 1σ, either side | Mixture of two distributions |

Each rule can be switched off independently, and the hit counts update live so you can see what a rule is costing you before you decide to keep it.

---

## DOE module

The DOE tab is the packaging app's replacement for a wafer map — process integration in advanced packaging runs far more designed experiments (reflow profile windows, bond force/temperature/time studies, underfill cure optimization) than it runs single-die spatial analysis.

- Map any set of columns as **factors** and any parameter as the **response**
- **Main effects plots** show the response mean at each factor level, so a shift jumps out visually before you trust a number
- **Full factorial** analysis estimates every main effect and interaction for balanced designs
- **Fractional factorial** analysis handles reduced designs, flagging confounded effects rather than silently misattributing them

As with everything else in the app, this runs on whatever you loaded — there is no assumption that your DOE was run in a particular software's format, only that the factor and response columns exist somewhere in your file.

---

## Known behaviour worth understanding

**The run rules over-fire on per-unit data, and that is not a bug.** Rules 2, 6 and 8 were written for charts of 25 to 50 subgroup points. Run them across tens of thousands of individual unit measurements and a run of nine on one side becomes common by construction — more so for a naturally skewed parameter like solder void percentage or underfill void percentage, where most units sit near zero and a long tail does the rest.

Rule 1 stays trustworthy at any sample size. For lot-level control, subgroup by lot or panel and read the X̄ chart, where one point per lot is the unit the rules were actually designed for.

**Capability assumes normality.** Cp, Cpk, Pp, Ppk and theoretical PPM all integrate a normal distribution. For void percentage and other bounded-at-zero parameters, compare theoretical PPM against observed PPM — a wide gap tells you the normal assumption is failing and the observed number is the one to quote.

---

## Performance

Tested on the built-in 50,000+ row stress lot:

| Operation | Time |
|---|---|
| Generate, parse and index 50k+ rows | under 2 s |
| Full SPC and rule analysis, one parameter | under 250 ms |
| All-parameters capability table | roughly 1 s |
| Chart redraw on filter change | under 100 ms |

Data is held in typed arrays in a column-oriented store. Statistics are computed over **every** row; charts plot a decimated subset — 2,000, 4,000, 8,000 or all points, selectable — so scrolling and hovering stay smooth on large lots. When decimation is active the app says so above the I chart, so you always know whether you are looking at all of it.

---

## Privacy

This matters more than any feature in the list.

- Your file is read by the browser's `FileReader` and parsed in the tab. It is never transmitted.
- There are no `fetch` calls, no XHR, no analytics, no telemetry, no error reporting.
- Nothing is written to `localStorage`, `sessionStorage`, cookies, or IndexedDB. Close the tab and it is gone.
- The only network activity is loading library files from a CDN when the page first opens.

**For a genuinely air-gapped machine**, download the CDN libraries the file references, save them next to the HTML file, and change the `<script src="...">` tags to point at the local copies. The app then works with no network at all.

Your fab's data is your fab's data. Verify all of the above yourself — it is one file, and you can read it.

---

## Deploying to GitHub Pages

Free public hosting, no build step.

1. Create a repository — for example `packaging-spc-analyzer`
2. Upload `packaging-spc-analyzer.html` and this `README.md`
3. Rename the HTML file to `index.html`, or add a small `index.html` that redirects to it
4. Go to **Settings → Pages**
5. Under **Source**, choose **Deploy from a branch**
6. Select branch `main` and folder `/ (root)`, then **Save**
7. Wait about a minute

Your tool is then live at:

```
https://ikoghoddds-bit.github.io/packaging-spc-analyzer/
```

Anyone with the link can use it, and their data still never leaves their machine — GitHub Pages serves the file and nothing else. To update, replace the file and commit.

> **One caution.** A GitHub Pages site is public. Publish the tool, never a dataset. Keep sample and customer data out of the repository entirely.

---

## Browser support

Chrome, Edge, Firefox and Safari, current versions. Requires a browser with `FileReader`, typed arrays, and canvas — anything from the last several years qualifies. Works offline once loaded, apart from the initial CDN fetch. Responsive down to tablet width.

---

## What's in the repo

```
packaging-spc-analyzer.html    the entire application — HTML, CSS and JavaScript in one file
README.md                      this document
```

That is the whole repository. No `package.json`, no `node_modules`, no bundler, no config. CDN dependencies are loaded by `<script>` tag for charting and Excel parsing.

CSV parsing, all statistics, all SPC logic, and the DOE engine are written from scratch in the file with no other dependencies.

---

## Roadmap

- Shared "Cpk shift" comparison view across both this tool and [ETEST SPC Analyzer](../etest-spc-analyzer)
- Gauge R&R module
- Multi-lot trend view across more than two slots
- PDF report export
- Non-normal capability using Box-Cox or Johnson transforms, for void-percentage-type parameters

Ideas and bug reports are welcome in Issues.

---

## Author

**Joseph Ikogho** — Process Engineer, semiconductor. Built for engineers who need to answer a process integration question before the meeting starts.

---

## License

MIT. Use it, fork it, take it into your fab, change it to match your process. No warranty — verify the statistics against your own reference data before you disposition material on them.
