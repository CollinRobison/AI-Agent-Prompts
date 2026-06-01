---
name: powerpoint-onepager
description: >
  Use this skill when the user asks to generate a one-pager, create PowerPoint slides,
  build a themed slide from data, or produce a branded summary deck. The skill reads
  any .pptx template to extract its full visual theme from the slide-master XML
  (official color scheme slots dk1/lt1/accent1-6, major/minor fonts, slide dimensions)
  and combines it with data from a JSON, CSV, Excel (.xlsx/.xls), or Markdown file to
  produce a professionally themed presentation with KPI callouts, insight bullets,
  annotated charts, and data tables — styled to match the original template exactly.
allowed-tools: shell
---

## Think like a data analyst AND a designer

Before running the script, read the data and the template context so you can make
intelligent decisions about layout, chart types, and slide count. The goal is a
presentation that tells a clear, compelling data story — not just a dump of numbers.

---

## Step 1 — Gather inputs

Ask the user if not already provided:

- **Template** `.pptx` — any PowerPoint deck whose visual brand should be matched
- **Data file** — `.json`, `.csv`, `.xlsx`, `.xls`, or `.md`
- **Title** — optional; the script will infer one from the data if omitted
- **Number of slides** — see Step 3 for how to decide

---

## Step 2 — Install dependencies

```
pip install -r .github/skills/powerpoint-onepager/requirements.txt
```

---

## Step 3 — Decide how many slides

Read the data file first. Use the following rubric:

| Data size / complexity | Recommended slides |
|---|---|
| < 8 records, 1–2 columns | **1** — a tight one-pager is clearest |
| 8–18 records OR 2–3 numeric columns | **2** — summary + visual analysis |
| > 18 records OR 4+ columns OR multiple categories | **3** — summary + analysis + detail table |

**Also consider:** if the user says "executive summary," lean toward 1–2 slides.
If they say "full report" or "deep dive," lean toward 3.
If unsure, ask: *"The data has X records and Y metrics — would you like 1, 2, or 3 slides?"*

Pass `--slides 1`, `--slides 2`, `--slides 3`, or `--slides auto` (the script will decide).

---

## Step 4 — Run the script

```
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template <template.pptx> \
  --data <datafile> \
  --output report.pptx \
  --title "<title>" \
  --slides <1|2|3|auto>
```

The script prints the extracted theme so you can verify it matched the template:
```
[theme]  bg=FFFFFF  text=1F1F1F  accents=['2F5597', '4472C4', '70AD47']  fonts=Calibri/Calibri  dark_bg=False
[data]   12 records  cols=['month', 'revenue', 'units', 'region']
[slides] generating 2 slide(s) — title: 'Q1 Performance'
[done]   saved → report.pptx
```

If the extracted theme looks wrong (e.g., wrong background color), tell the user so they can provide a better-themed template.

---

## Step 5 — Analyst checklist before confirming output

After the script runs, think through the following:

**Story clarity**
- Does the title make the purpose immediately obvious?
- Would an executive reading Slide 1 understand the key takeaway in 10 seconds?

**Chart selection** (the script auto-selects, but verify it makes sense)
- Time-series data (months, quarters, years) → line chart ✓
- Categorical comparison (regions, products, segments) → bar chart ✓
- Part-of-a-whole (shares, allocations ≤ 7 categories) → pie chart ✓
- If the auto selection looks wrong, re-run with adjusted data or note it for the user

**Insights quality**
- If the data has a `summary` field in JSON, those bullets are used verbatim — check that they're analytical ("Revenue grew 14% MoM, driven by enterprise segment") not just descriptive ("Revenue is 142000")
- If no summary is in the data, the script auto-generates stat bullets — suggest the user add a `"summary"` array in JSON for richer insights

**Theme fidelity**
- Confirm the printed `accents` and `bg` match the visual brand of the template
- On dark-background templates the script auto-switches to light text

---

## Step 6 — Confirm output

Report to the user:
- The output path (`report.pptx`)
- Number of slides generated and their purpose
- A brief summary of the theme extracted (colors, fonts)
- Any suggestions for improving the data for a richer result (e.g., "add a `summary` field for custom insight bullets")
