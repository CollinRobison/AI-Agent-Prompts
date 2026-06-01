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

### Pre-flight template inspection

Run this before generating to understand where the template's existing shapes sit,
so you can flag any layout conflicts to the user upfront:

```
python3 - <<'EOF'
from pptx import Presentation
prs = Presentation("<template.pptx>")
for i, layout in enumerate(prs.slide_layouts[:3]):
    print(f"Layout {i}: '{layout.name}'")
    for sp in layout.placeholders:
        print(f"  ph idx={sp.placeholder_format.idx}  "
              f"left={sp.left}  top={sp.top}  w={sp.width}  h={sp.height}")
EOF
```

Use the output to mentally mark the template's **safe content zone** — the area
free of decorative headers, footer bars, logo marks, and bubble/shape overlays.
Generated content must stay inside this zone. If the template's decorative elements
occupy the top 15–20% and sides, reduce the slide margin accordingly when you run
the script, or warn the user that the template is high-decoration and may clip content.

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

## Step 5 — Verify theme fidelity programmatically

After the script succeeds, run this cross-platform Python check to confirm the output matches the template's slide master, dimensions, and color scheme:

```
python3 .github/skills/powerpoint-onepager/verify_theme.py \
  --template <template.pptx> \
  --output <report.pptx>
```

Expected passing output:
```
[verify] slide dimensions  PASS  (9144000 x 5143500)
[verify] slide master      PASS  (same theme XML fingerprint)
[verify] accent colors     PASS  template=['2F5597','4472C4','70AD47']  output=['2F5597','4472C4','70AD47']
[verify] background        PASS  bg=FFFFFF
[verify] fonts             PASS  heading=Calibri  body=Calibri
[verify] slide count       PASS  2 slide(s)
[result] PASS — output matches template theme
```

If any line shows `FAIL`:
- **Dimensions mismatch** → the template was not loaded as the base; check the script ran without errors
- **Accent/background mismatch** → the theme XML may use `sysClr` references; note this for the user and confirm the console output from Step 4 looks correct
- **Font mismatch** → the template font may not be embeddable; treat as cosmetic and flag for the user

---

## Step 6 — Analyst checklist before confirming output

After the script runs, think through the following:

**Story clarity**
- Does the title make the purpose immediately obvious?
- Would an executive reading Slide 1 understand the key takeaway in 10 seconds?

**Chart selection** (the script auto-selects, but verify it makes sense)

Use this decision table — apply the **first matching rule** top-to-bottom:

| Data pattern | Chart |
|---|---|
| Labels are dates / time periods (months, quarters, years, weeks) | **line** |
| Labels are ordered/ranked categories (scores, ratings, ranks, grades, tiers) | **bar** |
| Categorical comparison — unordered groups (regions, products, segments) | **bar** |
| Part-of-a-whole — ≤ 5 slices, each a % share that sums to 100% | **pie** |
| Anything else or ≥ 8 categories | **bar** |

Common mistakes to catch:
- **Do NOT use pie for ordinal data** (quality scores 1–10, star ratings, tier levels) — even with ≤ 7 categories. Pie implies equal-standing parts of a whole; ranked categories need a bar so magnitude differences are readable.
- **Do NOT use line for unordered categories** — a line implies a continuous trend between adjacent points.
- If the auto-selected chart looks wrong, tell the user what was chosen and why a different type would be clearer.

**Insights quality**
- If the data has a `summary` field in JSON, those bullets are used verbatim — check that they're analytical ("Revenue grew 14% MoM, driven by enterprise segment") not just descriptive ("Revenue is 142000")
- If no summary is in the data, the script auto-generates stat bullets — suggest the user add a `"summary"` array in JSON for richer insights

**Layout quality** — this is where slides most often look unprofessional; check each:
- **No clipping**: is any text box or chart image cut off at the slide edge or behind a template shape?
- **No overlap**: do the generated shapes land on top of the template's decorative header, footer, bubble, or logo elements? If so, flag this and suggest the user choose a cleaner template or increase the top/side margin.
- **Readable contrast**: on dark-background templates (`dark_bg=True`), all text must be light-colored — check that chart axis labels, bullet text, and KPI labels are not dark-on-dark.
- **Chart axes legible**: axis tick labels should not be rotated more than 45° and should not overlap each other. If there are many categories, prefer horizontal bar or reduce label count.
- **KPI boxes proportional**: three KPI boxes should fill the row evenly without crowding. If values are long strings, they may overflow — flag this.
- **Sufficient whitespace**: there should be visible breathing room between every content block. Packed slides look unprofessional.

**Theme fidelity**
- Cross-reference the `[verify]` results from Step 5 against the template's visual brand
- On dark-background templates confirm the script switched to light text (`dark_bg=True` in console output)
- If any `FAIL` lines appeared in Step 5, surface those specifically to the user

---

## Step 7 — Confirm output

Report to the user:
- The output path (`report.pptx`)
- Number of slides generated and their purpose
- The theme verification result: **PASS** or a list of specific `FAIL` checks with remediation advice
- A brief summary of the theme extracted (colors, fonts)
- Any suggestions for improving the data for a richer result (e.g., "add a `summary` field for custom insight bullets")
