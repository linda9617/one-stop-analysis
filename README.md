# One-Stop Analysis

A self-contained, browser-based tool for merging plate-reader result files with well/plate metadata, visualizing data, running statistics, and exporting analysis-ready tables directly into GraphPad Prism — no installation or server required.

---

## Features

### Step 1 — Upload Files
- Upload a **Metadata CSV/TSV** (well layout, siRNA IDs, conditions, doses, etc.) and one or more **Result CSV/TSV** files (e.g., plate-reader output)
- Drag-and-drop or click-to-upload; multiple result files supported
- Auto-detects **Well** and **Plate** columns; inline quality badge shows `✔ 240/240 (100%)` next to each selector
- **Merge Key** cards appear after upload — configure Plate / Well columns independently for metadata and each result file
- Live 2-column preview of both files

### Step 2 — Configure Columns
- **Metadata Columns**: select which metadata columns to carry forward; rename with custom Output names; *Select All* pre-fills output names with original column names
- **Columns to Merge**: per-file column selection with optional output name aliases and short-name suffixes
- **Filter Rows / Wells**: omit rows by column value, specific well IDs, or with a checkbox to remove all-empty columns
- **Computed Columns**: define custom formula columns using variables (`a`, `b`, `c`…) mapped to result columns — e.g., `(a+b)/2`

### Step 3 — Results
- **Merge Summary**: matched / unmatched well counts, total columns
- **Plate Heatmap**: HTML-based 9-stop red-white-blue color scale; Small / Large size toggle; **Export Figure** → `YYYYMMDD_HeatMap.png`
- **Bar Chart** (Chart.js): group-by selection, per-plate tabs; **Export Figure** → `YYYYMMDD_BarChart.png`
- **Statistics Table** with three tabs:
  - **Assay Window**: FC, SSMD, Z-factor, S-pooled, D-ratio, Welch t-test vs control
  - **Disease Window**: same metrics relative to a disease-state control group
  - **Dose Group**: 4PL EC50 fitting + 2-way ANOVA with Dunnett or all-pairs post-hoc (BH-FDR)
- **Export Stats CSV** → `YYYYMMDD_Assay_Win.csv`, `YYYYMMDD_Disease_Win.csv`, `YYYYMMDD_EC50.csv`, `YYYYMMDD_ANOVA.csv`, or `YYYYMMDD_EC50_ANOVA.csv`
- **Merged Data Table** with masked-well toggle; **Export CSV** for downstream use

### Step 4 — Prism Export
- Reshape merged data into GraphPad Prism-compatible table formats:
  - **XY** — dose-response / scatter; optional 4PL fit preview, 2-way ANOVA, EC50 within the tool
  - **Column** — single-group bar/dot
  - **Grouped** — multi-group bars
- Split output by any categorical column (each value → separate tab)
- Copy-paste ready output displayed in the browser

---

## Getting Started

0. [linda9617.github.io/one-stop-analysis/](https://linda9617.github.io/one-stop-analysis/)
1. Download `index.html`
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari)
3. No internet connection required — all libraries (Chart.js 4.4.0, PapaParse 5.4.1) are bundled inline

> **Try it instantly** — click **Load Template Data** on the Upload page to load a built-in 384-well example dataset (12 siRNAs × 5 insulin doses × 2 conditions × 2 replicate plates).

---

## Input File Format

### Metadata File
A CSV or TSV with one row per well. Must contain at least a **Well** column (e.g., `A01`, `B03`). Optional: Plate column, metadata columns (siRNA, Condition, Dose, Omit flag, etc.).

| Well | siRNA   | Ins | Condition | Omit |
|------|---------|-----|-----------|------|
| C03  | siNTC   | 0   | Normal    | 0    |
| C04  | siNTC   | 0   | Normal    | 0    |
| D05  | siGene1 | 0.1 | Disease   | 1    |

### Result File(s)
One or more CSV/TSV files, each with a **Well** column and one or more numeric result columns (e.g., `Ratio`, `RFU`, `CT`).

| Well | Ratio  |
|------|--------|
| C03  | 0.4821 |
| C04  | 0.5013 |

---

## Technology

| Library    | Version | Usage                        |
|------------|---------|------------------------------|
| PapaParse  | 5.4.1   | CSV/TSV parsing              |
| Chart.js   | 4.4.0   | Bar chart rendering          |
| Vanilla JS | —       | All logic; no frameworks     |

All dependencies are inlined — the entire app is a single `.html` file.

---

## License

For internal / research use. Please contact the author before redistribution.

---

*Built by ILYH*

<img width="1271" height="1158" alt="image" src="https://github.com/user-attachments/assets/d3c8d50e-eca4-4dc0-9f42-df15aadd2deb" />

<img width="1212" height="912" alt="image" src="https://github.com/user-attachments/assets/866645ff-0886-480d-ac99-2bd10da49031" />

<img width="1194" height="1151" alt="image" src="https://github.com/user-attachments/assets/fa3c480d-4b9b-4a85-a691-fa2a50771951" />
