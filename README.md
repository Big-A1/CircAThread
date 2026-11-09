# CircAThread — Circadian Biology Analysis Suite


[![Open in Browser](https://img.shields.io/badge/Open%20CircAThread-Live-blue)](https://big-a1.github.io/CircAThread/CircAThread.html)
 
**[→ Open CircAThread in your browser](https://big-a1.github.io/CircAThread/CircAThread)** · or download `CircAThread` and open it locally.


 

---

## Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Modules](#modules)
  - [CircATreat — Treatment Scheduling](#circatreat--treatment-scheduling)
  - [CircATrace — Trace Visualisation](#circatrace--trace-visualisation)
  - [CircATrends — Parameter Extraction & Statistics](#circatrends--parameter-extraction--statistics)
- [Input Format](#input-format)
- [Output Formats](#output-formats)
- [Technical Specifications](#technical-specifications)
- [Algorithm Details](#algorithm-details)
- [Comparison with Other Tools](#comparison-with-other-tools)
- [Validation](#validation)
- [Citation](#citation)
- [Funding & Acknowledgements](#funding--acknowledgements)
- [Contact & Contributing](#contact--contributing)

---

## Overview

**CircAThread** is a self-contained circadian biology analysis suite distributed as a single HTML file (~200 KB). It is designed for researchers using LumiCycle photomultiplier platforms to record bioluminescent clock reporters (e.g. BMAL1::Luc, PER2::Luc, CRY1::Luc, REV-ERBα::Luc, DBP::Luc) in stable cell lines or tissue explants.

CircAThread consolidates three analytical steps that are currently distributed across multiple applications — treatment scheduling, representative figure generation, and statistical parameter analysis — into one coherent browser-based workflow.

**Key properties:**

| Property | Detail |
|---|---|
| Distribution | Single `.html` file |
| Installation | None required |
| Cost | Free |
| Internet | Not required after download |
| Data transmission | None — all processing is client-side |
| Browser support | Firefox, Chrome, Safari, Edge (ES2017+) |
| Platform | Windows, macOS, Linux |

---

## Quick Start

1. **Download** `CircAThread.html` from the [latest release](../../releases/latest).
2. **Open** the file in any modern browser — no installation, no server.
3. **Drag and drop** your LumiCycle baseline-subtracted CSV file(s) onto the relevant module tab.
4. Files uploaded in any tab are shared across all three modules automatically.
---

## Modules

CircAThread comprises three integrated modules accessed via the top navigation tabs:

### CircATreat — Treatment Scheduling

**Purpose:** Given a running LumiCycle experiment, calculate the real-world clock time at which a treatment must be administered to reach a specified circadian time (CT) in one or more future cycles.

**Problem it solves:** Chronopharmacological experiments require precise CT-timed dosing. Manual CT calculation from a running trace is error-prone and fails to account for cycle-by-cycle phase drift.

**How to use:**
1. Upload a LumiCycle baseline-subtracted CSV export.
2. Enter the real-world date and time corresponding to recording start (DD/MM/YYYY HH:MM format).
3. Enter one or more target CTs (e.g. `CT2, CT8, CT14`).
4. Click **Calculate**. CircATreat returns a per-cycle table of predicted treatment times with phase descriptors (Rise, Crest, Fall, Trough).
5. Export as a plain-text report for lab records.

**Reference cassettes shown:** Bmal1::Luc, Per2::Luc, Cry1::Luc, Rev-erbα::Luc, Dbp::Luc — representative traces from published datasets are displayed alongside the detected CT0 to aid visual phase interpretation.

---

### CircATrace — Trace Visualisation

**Purpose:** Generate publication-ready, multi-sample bioluminescence trace overlay figures.

**How to use:**
1. Upload one or more LumiCycle baseline-subtracted CSV files.
2. Assign display labels (e.g. `Control`, `Treated`) to each file — independent of the filename.
3. Select colours from one of four built-in palettes, or upload a custom hex-code palette file.
4. Optionally enable:
   - **Normalisation** — rescale each trace to 0–1 for amplitude-independent comparison
   - **Averaging** — overlay mean ± SEM ribbon on replicate traces
   - **Peak/trough markers** — orange triangles (CT0) and teal diamonds (CT12) placed at computed coordinates
5. Export as **PNG**, **JPEG**, or **TIFF** at 180 × 120 mm / 600 dpi (publication resolution).

**Colour palettes:**

| Palette | Notes |
|---|---|
| Okabe–Ito | Universal colour-blind-safe (default) |
| Wong | 8-colour colour-blind-safe |
| Paul Tol Muted | Muted, print-friendly |
| IBM Carbon | High-contrast digital |
| Custom | Upload a `.txt` file of hex codes, one per line |

**Background options:** White · Off-white · Navy

---

### CircATrends — Parameter Extraction & Statistics

**Purpose:** Batch extraction of circadian parameters (period, phase, amplitude) from multiple LumiCycle recordings with integrated group-level statistics.

**How to use:**
1. Upload one or more LumiCycle baseline-subtracted CSV files.
2. Review the auto-detected peak and trough table (horizontally scrollable; export as `.xlsx` at any stage).
3. Assign each file to a named **treatment group** via the dropdown.
4. Select a **statistical test**:
   - Welch's t-test *(default; unequal variance)*
   - Student's unpaired t-test
   - Mann–Whitney U *(non-parametric)*
   - One-way ANOVA + Tukey's HSD or Bonferroni correction
5. Optionally enable **Absolute normalisation** — expresses all groups as % and fold-change of a designated reference group, enabling cross-session comparison.
6. Export individual or all parameter bar charts as **PNG**, **JPEG**, or **TIFF**.

**Parameters computed per sample:**

| Parameter | Definition |
|---|---|
| Period (τ) | Mean of inter-peak intervals in the circadian range (16–36 h) |
| Phase | Time from recording start to first detected peak (h) |
| Amplitude | Mean peak intensity across all detected peaks (counts/sec) |

---

## Input Format

CircAThread accepts **LumiCycle Analysis baseline-subtracted CSV exports** directly. These are the files produced by selecting *Export → Baseline-subtracted data* in Actimetrics LumiCycle Analysis software.

**Expected format:**
- Row 1: Experiment title / metadata (skipped automatically)
- Row 2: Column headers — time column (`Time(days)`) and signal column(s) (`counts/sec`)
- Rows 3+: Numeric data

CircAThread's parser uses a scored column-detection algorithm that is robust to variations in header layout. The time column is automatically converted from fractional days to hours. Files from both single-channel and multi-channel LumiCycle systems are supported.

> **Note:** CircAThread is not designed for raw (non-baseline-subtracted) luminescence data. Apply baseline subtraction in LumiCycle Analysis before importing.

---

## Output Formats

| Output | Format | Module |
|---|---|---|
| Representative trace figure | PNG / JPEG / TIFF (600 dpi) | CircATrace |
| Parameter bar charts | PNG / JPEG / TIFF (600 dpi) | CircATrends |
| Peak/trough coordinate table | `.xlsx` (Microsoft Excel) | CircATrends |
| Treatment time report | Plain text | CircATreat |

---

## Technical Specifications

- **File size:** ~200 KB (single `.html` file)
- **Dependencies:** None — all libraries bundled inline (SheetJS for Excel export; HTML5 Canvas for rendering)
- **Browser requirement:** Any browser supporting HTML5 Canvas API and ES2017 (`async`/`await`, `Object.entries`, `Array.flat`)
- **Tested on:** Firefox 120+, Chrome 118+, Safari 17+, Edge 118+
- **Operating systems:** Windows 10/11, macOS 12+, Linux (Ubuntu 20.04+, Fedora)
- **Data processing:** All computation client-side; no data transmitted to any server
- **Offline capability:** Full — all three modules functional without internet after initial download
- **Figure resolution:** 180 × 120 mm at 600 dpi (suitable for print publication at any standard journal column width)

---

## Algorithm Details

### Smoothing
An 11-point centred moving average (window half-width W = 5 time points, ≈ 50 minutes at standard 10-minute acquisition) is applied before peak/trough detection and figure rendering. Edge effects are handled by symmetric window truncation.

### Peak and trough detection
A data point at index *i* is classified as a peak if the smoothed signal at *i* exceeds all values in the window [*i* − 5, *i* + 5]. No prominence threshold is applied. Inter-event intervals are screened against the circadian range (18–32 h in CircATreat; 16–36 h in CircATrends) to exclude sub-harmonic noise events.

### CT0 anchor
The highest-amplitude peak occurring between 25% and 75% of recording duration is designated CT0 — the first robust peak clear of recording onset transients.

### Period estimation
Period (τ) = arithmetic mean of qualifying inter-peak intervals. Chosen for robustness to incomplete final cycles (common in 5–7 day recordings with substrate depletion).

### CT-to-clock-time projection (CircATreat)
For target CT *x* in cycle *n* from CT0 anchor time *T*₀:

```
T_treatment = T₀ + n·τ + (x / 24)·τ
```

where τ is the per-cycle period estimate. Phase descriptors: CT0–6 = Rise; CT6–12 = Crest; CT12–18 = Fall; CT18–24 = Trough.

---

## Comparison with Other Tools

| Feature | CircAThread | ClockLab v6 | BioDare2 | CircadiPY | easyClock |
|---|---|---|---|---|---|
| Distribution | Single HTML file | Licensed installer | Web application | Python package | Desktop installer |
| Cost | Free | Commercial | Free | Free | Free |
| Installation | None | Yes | None (account required) | Yes (Python) | Yes |
| Offline | Yes | Yes | No | Yes | Yes |
| Data locality | Client-side only | Local | Server upload | Local | Local |
| Primary data type | Bioluminescence | Locomotor / actigraphy | General circadian | General circadian | Locomotor / behavioural |
| Treatment scheduling | **Yes (CircATreat)** | No | No | No | No |
| Multi-trace overlay | Yes | Actogram only | No | No | Yes (actogram) |
| Publication export | PNG/JPEG/TIFF 600dpi | Clipboard only | Basic PNG | matplotlib | Yes |
| Colour-blind palettes | Yes (4 built-in) | No | No | Via matplotlib | Not specified |
| In-browser statistics | Yes | No | No | No | Yes |
| Rhythmicity p-value | No | Yes | Yes | Yes | Yes |
| Period algorithms | Inter-peak mean | Chi²/FFT/Lomb-Scargle/CWT | FFT-NLLS/MESA/JTK/Lomb-Scargle | Cosinor/Lomb-Scargle | JTK/AR-JTK/Cosinor/CWT |

> **Recommended complementary tools:** For formal rhythmicity testing with p-values, use [BioDare2](https://biodare2.ed.ac.uk) (bioluminescence/general circadian) or [easyClock](https://github.com/WuBioinfo/easyClock) (locomotor/behavioural). CircAThread exports raw peak coordinates as `.xlsx` for direct import into these tools.

---

## Validation

CircAThread was validated against manually annotated LumiCycle recordings:

- **BMAL1::Luc C2C12 myoblasts** — 32-channel experiment, 192 h recording. Period estimates agreed with LumiCycle Analysis damped-sine fitting to within the 10-minute acquisition interval across all rhythmic channels (mean period difference < 0.05 h).
- **Per2::Luc tissue explants** — representative trace (146 h), 8 peaks detected, estimated τ = 22.36 h, inter-individual variance < 0.1 h (consistent with biological variability).

Both reporter cassettes produced consistent results, confirming that the peak-detection algorithm does not introduce cassette-dependent bias.

---

## Citation

If you use CircAThread in published work, please cite:

> **Topping, A.W. & Pekovic-Vaughan, V.** (*in preparation*). CircAThread — A Free, Open-Source Circadian Biology Analysis Suite for Bioluminescent Data.

For the archived software release, please also cite the Zenodo record:

> **CircAThread vX.X** [Software]. Zenodo. https://doi.org/[ZENODO_DOI]

A `CITATION.cff` file is included in this repository for reference managers and GitHub's *Cite this repository* button.

---

## Funding & Acknowledgements

Development was supported by the **Biotechnology and Biological Sciences Research Council (BBSRC)** and the **Rosetrees Trust**.

The authors thank the circadian biology community at the Institute of Life Course and Medical Sciences, University of Liverpool, for feedback and validation data.

CircAThread uses [SheetJS](https://sheetjs.com/) (Apache 2.0) for Excel export.

---

## Contact & Contributing

**Bug reports and feature requests:** Please open an [issue](../../issues) on this repository.

**Contributing:** Pull requests are welcome. Please open an issue first to discuss major changes.

**Contact:** Alistair Topping — a.topping@liverpool.ac.uk  
Institute of Life Course and Medical Sciences, University of Liverpool

---

## Licence

CircAThread is released under the [MIT Licence](LICENSE). You are free to use, modify and distribute it, including for commercial research, provided attribution is maintained.

---

*CircAThread is provided as-is for research use. It is not a clinical or diagnostic tool. For formal rhythmicity statistics, the authors recommend cross-validation with [BioDare2](https://biodare2.ed.ac.uk) or equivalent validated tools.*
