# PowerPoint One-Pager Skill

A Python-based Copilot Agent Skill that extracts the complete visual theme from any `.pptx` template — using the slide-master's XML color scheme (official `dk1`/`lt1`/`accent1-6` slots, major/minor fonts) — then generates a professionally themed presentation with KPI callouts, annotated charts, insight bullets, and data tables.

This skill lives at `.github/skills/powerpoint-onepager/` and is registered via `SKILL.md` so Copilot agent mode can invoke it with analyst+designer judgment.

## What gets extracted from the template

The script reads the **slide-master theme XML** — the same source PowerPoint itself uses:

| XML slot | What it means | Used for |
|---|---|---|
| `lt1` | Official background color | Slide background, chart bg |
| `dk1` | Official dark/text color | All body and title text |
| `accent1–6` | The brand's 6 accent colors | KPI boxes, chart bars, table headers |
| `majorFont` | Heading typeface | Titles |
| `minorFont` | Body typeface | Bullets, table cells |

As a fallback, shapes on the slides are scanned for fill and text colors.

## Slide types

| Slide | Purpose | Elements |
|---|---|---|
| 1 – Summary | Executive overview | Title, KPI callout row, insight bullets, compact chart, mini-table |
| 2 – Analysis | Visual deep-dive | Large primary chart + secondary chart (bar + line or pie), trend annotations |
| 3 – Detail | Full data | Wide data table + secondary metric chart |

## Requirements

```bash
pip install -r .github/skills/powerpoint-onepager/requirements.txt
```

## Usage

```bash
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template deck.pptx \
  --data data.json \
  --output report.pptx \
  --title "Q1 Analysis" \
  --slides auto
```

### Arguments

| Flag | Default | Description |
|---|---|---|
| `--template` | *(required)* | Path to the source `.pptx` whose theme to match |
| `--data` | *(required)* | Path to data file (`.json` / `.csv` / `.xlsx` / `.xls` / `.md`) |
| `--output` | `onepager.pptx` | Output file path |
| `--title` | from data or `"Data Analysis"` | Slide title override |
| `--slides` | `auto` | `1`, `2`, `3`, or `auto` (script decides based on data size) |

`auto` slide logic:
- **1 slide** — < 8 records, ≤ 3 columns
- **2 slides** — 8–18 records or 2–3 numeric columns
- **3 slides** — > 18 records or 4+ numeric columns

### Data format examples

#### JSON (with optional `summary` for custom insight bullets)
```bash
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template deck.pptx --data .github/skills/powerpoint-onepager/sample_data.json
```

#### CSV
```bash
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template deck.pptx --data metrics.csv --output report_csv.pptx --slides 2
```

#### Excel
```bash
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template deck.pptx --data metrics.xlsx --slides 3
```

#### Markdown table
```bash
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template deck.pptx --data metrics.md
```

## Tips for best results

- **Provide a `summary` array in JSON** for fully controlled, analyst-quality insight bullets instead of auto-derived stats.
- **Include a `label` column** and at least one numeric column for chart generation.
- **More distinct brand colors** in the template = richer theme extraction. Templates with visible text styles and colored shapes give stronger signals.
- **On dark-background templates** the script auto-detects and switches to white text.
- Use `--slides 3` for a full data report; `--slides 1` for a tight executive one-pager.

