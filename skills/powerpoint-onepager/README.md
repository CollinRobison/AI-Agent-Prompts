# PowerPoint One-Pager Skill

A Python-based Copilot skill script that reads any `.pptx` template, extracts its visual theme, ingests structured data from multiple file formats, and generates a branded single-slide one-pager with a title, bullets, charts, and a table.

## Requirements

```bash
pip install -r requirements.txt
```

## Usage

```bash
python generate_onepager.py --template deck.pptx --data data.json --output onepager.pptx --title "Q1 Analysis"
```

`--template` and `--data` are required. `--output` defaults to `onepager.pptx`. `--title` is optional.

### Data format examples

#### JSON
```bash
python generate_onepager.py --template deck.pptx --data sample_data.json
```

#### CSV
```bash
python generate_onepager.py --template deck.pptx --data metrics.csv --output onepager_csv.pptx
```

#### Excel (`.xlsx` / `.xls`)
```bash
python generate_onepager.py --template deck.pptx --data metrics.xlsx --output onepager_excel.pptx
```

#### Markdown table (`.md`)
```bash
python generate_onepager.py --template deck.pptx --data metrics.md --output onepager_md.pptx
```

## What gets extracted from the template

During theme extraction, the script inspects the provided template and captures:

- Slide background color (with fallbacks)
- Accent and text colors from shape fills and text runs
- Heading/body fonts from text runs
- Slide dimensions (width/height)
- Optional image/logo placement candidates (for future layout anchoring)

## Output

The generated `.pptx` contains one slide with:

- Theme-matched background and typography
- A title block
- A bullet summary section (from `summary` when provided, otherwise auto-derived insights)
- 1–2 matplotlib charts (bar + optional line) colored using extracted accent colors
- A compact data table styled to match the template palette

## Tips for best results

- Use templates with visible text styles and brand colors so extraction has stronger signals.
- Include a `label` field and at least one numeric field (for example `value`) for chart generation.
- Provide a `summary` array in JSON for fully controlled bullet points.
- Keep markdown input in a standard pipe table format.
- If an input has no usable rows, the script exits with a clear error instead of generating an empty slide.
