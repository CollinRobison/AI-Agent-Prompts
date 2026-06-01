---
name: powerpoint-onepager
description: >
  Use this skill when the user asks to generate a one-pager, create PowerPoint slides,
  build a themed slide from data, or produce a branded single-slide summary. The skill
  reads any .pptx template to extract its visual theme (colors, fonts, layout) and
  combines it with data from a JSON, CSV, Excel (.xlsx/.xls), or Markdown file to
  produce a fully themed one-pager slide with a title, bullet points, charts, and a
  data table.
allowed-tools: shell
---

When asked to generate a one-pager or create themed PowerPoint slides, follow these steps:

1. **Gather inputs** — if the user has not already provided them, ask for:
   - A template `.pptx` file (any PowerPoint deck whose theme should be matched)
   - A data file (`.json`, `.csv`, `.xlsx`, `.xls`, or `.md`)
   - An optional title for the one-pager (defaults to the title field in the data or "Data Analysis One-Pager")

2. **Install dependencies**:
   ```
   pip install -r .github/skills/powerpoint-onepager/requirements.txt
   ```

3. **Run the script**:
   ```
   python .github/skills/powerpoint-onepager/generate_onepager.py --template <template.pptx> --data <datafile> --output onepager.pptx --title "<title>"
   ```
   Omit `--title` if no title override is needed.

4. **Confirm output** — verify the file was created and report the path to the user (e.g. `onepager.pptx`).
