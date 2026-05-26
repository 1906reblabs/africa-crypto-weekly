# Antifragile Africa Crypto — Intelligence OS

**Institutional-grade intelligence on African crypto markets.**  
Weekly reports covering capital flows, regulatory arbitrage, OTC signals, and hidden trades across all 54 African states.

> *"Africa already has the world's most advanced crypto use case in production, at scale. It just doesn't look like what the Western industry built."*

---

## What This Repo Does

This repository stores every weekly ACIOS intelligence report as a structured Markdown file and automatically converts each one to a full-featured HTML page on every push. The build system is a Python + Jinja2 pipeline that parses section-structured `.md` files into the complete 10-component weekly publication format.

**Write `.md` → Push to GitHub → GitHub Actions builds `.html` → GitHub Pages serves it.**

---

## Repository Structure

```
acios/
│
├── reports/                        ← Write your weekly .md reports here
│   ├── vol09.md                    ← Vol. 09 — 26 May 2026
│   ├── vol08.md
│   └── ...
│
├── templates/                      ← Jinja2 HTML templates (do not edit unless restyling)
│   ├── issue.j2                    ← Individual report page template
│   └── index.j2                    ← Archive / homepage template
│
├── output/                         ← Generated HTML files (auto-created by build; gitignored or deployed)
│   ├── antifragile_africa_weekly_26may2026.html
│   ├── index.html
│   └── ...
│
├── .github/
│   └── workflows/
│       └── build.yml               ← GitHub Actions workflow (auto-build + deploy)
│
├── afi_parser.py                   ← Markdown report parser
├── build.py                        ← Build orchestrator (CLI: report / index / all)
├── requirements.txt                ← Python dependencies
└── README.md                       ← This file
```

---

## How the Build System Works

```
reports/vol09.md
        │
        ▼
   afi_parser.py          Parses YAML frontmatter + 13 structured sections
        │                 into a Python dict
        ▼
   build.py               Feeds the dict into Jinja2 templates
        │
        ├── templates/issue.j2   → output/antifragile_africa_weekly_26may2026.html
        └── templates/index.j2  → output/index.html
```

---

## Local Development

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Build a single report

```bash
python build.py report reports/vol09.md
# → output/antifragile_africa_weekly_26may2026.html
```

### 3. Build the archive index only

```bash
python build.py index reports/
# → output/index.html
```

### 4. Build everything (all reports + index)

```bash
python build.py all reports/
# → output/*.html + output/index.html
```

---

## Writing a Weekly Report

Every report is a single `.md` file in `reports/`. The filename convention is `volNN.md` (e.g. `vol09.md`).

### Frontmatter

Start every file with a YAML block between `---` markers:

```yaml
---
vol: "09"
vol_roman: "IX"
date: "26 May 2026"
filename: "antifragile_africa_weekly_26may2026.html"
title: "Your headline here — *italic via asterisks*"
title_plain: "Your headline here — plain text version"
standfirst: "One-paragraph summary shown under the headline."
audit: "4.91"
agents: "28"
layers: "7"
new_signal: "Short signal label for chip display"
next_vol: "10"
badges:
  - text: "Latest"
    cls: "badge-vol"
  - text: "★ Some Signal"
    cls: "badge-gold"
  - text: "✓ Prediction Confirmed"
    cls: "badge-confirmed"
---
```

**Badge classes:** `badge-vol` (steel), `badge-gold` (gold), `badge-alert` (red), `badge-confirmed` (green).

---

### Section Reference

Sections begin with `## SECTION_NAME`. The parser recognises these 13 sections:

| Section header | What it contains |
|---|---|
| `## SPREADS` | Pipe-delimited spread rows |
| `## EXECUTIVE BRIEF` | Headline, paragraphs, risk line, summary |
| `## INSIGHTS` | `### INSIGHT` blocks |
| `## HEATMAP` | Caption + `### ALERTS` + `### CELLS` |
| `## FULLSTACK` | `### LAYER Name` blocks + `### CROSS-LAYER` |
| `## FRAGILITY` | Score, bars, risk cards |
| `## OPPORTUNITIES` | `### OPP` blocks |
| `## POWERMAP` | `### CARD` and `### SHIFT` blocks |
| `## BLACKSWAN` | `### SCENARIO` blocks |
| `## SIGNAL_NOISE` | `### TABLE` + `### UNRESOLVED` |
| `## RECOMMENDATIONS` | `### REC R01` blocks |
| `## MEMORY` | `### TRENDS`, `### COUNTRIES`, `### RESOLVED`, `### PREDICTIONS`, `### WATCHLIST` |

---

### Section Format Details

#### SPREADS
```
Country · CCY | ~Val% | val_cls | ↑ TREND LABEL | trend_cls | Short note text.
```
`val_cls` options: `alert` (red), `gold`, `green`, or empty.  
`trend_cls` options: `t-up`, `t-down`, `t-alert`, `t-gold`, `t-neutral`.

#### EXECUTIVE BRIEF
```
HEADLINE: YOUR ALL-CAPS HEADLINE HERE

Paragraph one. Use **bold** and *italic* for emphasis. Separate paragraphs with blank lines.

---

Paragraph two after a --- divider.

RISK: One-sentence key risk statement.
SUMMARY: One-line bottom-line summary.
```

#### INSIGHTS
```
### INSIGHT

CONF: HIGH
HEADLINE: The insight headline in title case

Body text for this insight. Can be multiple paragraphs separated by blank lines.

SOURCE: Agent Name · Agent Name · Agent Name
```
`CONF` options: `HIGH`, `MEDIUM`, `LOW`.

#### HEATMAP
```
CAPTION: **Bold lead sentence.** Rest of caption in plain text.

### ALERTS

⚡ Label Text | Body text for this alert with **bold** supported.
★ Another Label | Another alert body.

### CELLS

Country Name | Score | score_cls | border_cls | ↑ TREND | trend_cls | Short note.
```
`score_cls`: `c-gr` (green), `c-r` (red), `c-g` (gold), `c-s` (steel).  
`border_cls`: `l-green`, `l-red`, `l-gold`, `l-steel`, `l-dim`.

#### FULLSTACK
```
### LAYER Layer Name Here

Paragraph text. Separate paragraphs with blank lines.

Use --- to separate if needed.

### LAYER Another Layer

For [1st order] / [2nd order] / [3rd order] analysis:

[1st order]: First order text here.

[2nd order]: Second order text.

[3rd order]: Third order text.

### CROSS-LAYER

#### Cross-layer insight title

Body text for this cross-layer insight.
```

#### FRAGILITY
```
SCORE: 3.5
SCORE_CLS: low
STATUS: MEDIUM — DECLINING TREND
CHANGE: ↓ -0.3 from Vol. 07 (3.8)
DESC: One paragraph description of the index.

### BARS

Bar label text | 50% | fb-med | 5.0

### RISKS

#### RISK 01: Risk Title Here
LEVEL: MEDIUM
LEVEL_CLS: risk-med

Risk description text here.
```
`SCORE_CLS`: `low` (green), `med` (gold), `high` (red).  
`fb-*` classes: `fb-low` (steel), `fb-med` (gold), `fb-high` (red).  
`risk-*` classes: `risk-low`, `risk-med`, `risk-high`.

#### OPPORTUNITIES
```
### OPP

TIER: Tier 1 · Category Label
TITLE: Opportunity title text
SCORE: 9.2

Thesis paragraph one.

Thesis paragraph two. Use --- to separate paragraphs.

FOR WHOM: Description of who this is for
TIMEFRAME: Time window
TRIGGER: What triggers action
INVALIDATION: What would invalidate this thesis
```

#### POWERMAP
```
### CARD Country Name — Short Status Label

STATUS: STATUS TEXT

Row label | Row value | p-state
Row label | Row value | p-contested
Row label | Row value | p-crypto

### SHIFT

BORDER_CLS: green-border
LABEL: ★ Label text
LABEL_CLS: green
TITLE: Power shift title

Body text for this power shift description.
```
`p-*` classes: `p-state` (red), `p-contested` (gold), `p-crypto` (green).  
`BORDER_CLS`: empty (gold border) or `green-border`.  
`LABEL_CLS`: empty (gold) or `green`.

#### BLACKSWAN
```
### SCENARIO

CARD_CLS: s-low
PROB: LOW
TREND: → Stable
TREND_CLS: t-neutral
NEW: false
TITLE: Scenario title here

Paragraph one.

---

Paragraph two.

TRIGGER: Trigger signal text here.
```
`CARD_CLS`: `s-remote`, `s-low`, `s-med`, `s-pos`, `s-high`.

#### SIGNAL_NOISE
```
### TABLE

Development description | sn-ss | STRONG SIGNAL | Rationale text here.
Another development | sn-s | SIGNAL | Rationale.
Noise item | sn-n | NOISE | Rationale.

### UNRESOLVED

#### Unresolved item title here

CONDITION: Becomes SIGNAL if... Becomes NOISE if...

REVIEW: Vol. 10 · Date · Context
```
`sn-*` classes: `sn-ss` (sky/strong signal), `sn-s` (green/signal), `sn-n` (grey/noise), `sn-u` (gold/unresolved).

#### RECOMMENDATIONS
```
### REC R01

ACTION: The specific action to take.

FOR WHOM: Who this is for
TIMEFRAME: Time horizon
TRIGGER: Trigger condition
RISK: Risk of acting or not acting
```

#### MEMORY
```
### TRENDS

**Trend Name — STATUS:** Trend description and update.

### COUNTRIES

**Country:** Country memory update.

### RESOLVED

**P-XX-XX — CORRECT/INCORRECT:** Resolution note.

### PREDICTIONS

P-09-01 | Prediction statement. Falsification condition. | HIGH

### WATCHLIST

**Date/Event:** What to monitor and why.
```

---

## GitHub Actions: Automatic Build and Deploy

The workflow file at `.github/workflows/build.yml` triggers on every push to `main` that touches `reports/`, `templates/`, or the build scripts. It:

1. Installs Python dependencies
2. Runs `python build.py all reports/`
3. Deploys the `output/` folder to the `gh-pages` branch

**GitHub Pages setup (one-time):**

1. Go to your repo → **Settings** → **Pages**
2. Set **Source** to `Deploy from a branch`
3. Set **Branch** to `gh-pages` / `/ (root)`
4. Save

Your reports will be live at `https://<your-username>.github.io/<repo-name>/`.

---

## Requirements

```
pyyaml>=6.0
markdown2>=2.4
jinja2>=3.1
```

Install with:
```bash
pip install -r requirements.txt
```

---

## Disclaimer

All content produced by this system is for informational and research purposes only. Not financial advice. Simulated data where noted. All figures are estimates or model outputs. Past signals do not guarantee future accuracy. For institutional and professional use only. © 2026 Antifragile Africa Crypto.
