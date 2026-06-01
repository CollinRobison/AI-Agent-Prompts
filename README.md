# AI-Agent-Prompts
A repo to store various agent prompts that I find useful.

## Copilot Agent Skills

### PowerPoint One-Pager

**Location:** `.github/skills/powerpoint-onepager/`

Generates a themed one-pager PowerPoint slide from any `.pptx` template and a data file. Copilot agent mode picks this skill up automatically via `SKILL.md`.

**How to invoke in Copilot agent mode:**

> *"Generate a one-pager from `deck.pptx` and `data.csv`"*
> *"Create a themed PowerPoint slide using `template.pptx` and `metrics.json`"*
> *"Build a one-pager slide from my data file"*

Copilot will ask for the template and data file if not provided, install dependencies, run the script, and confirm the output path.

**Manual usage:**

```bash
pip install -r .github/skills/powerpoint-onepager/requirements.txt
python .github/skills/powerpoint-onepager/generate_onepager.py \
  --template deck.pptx \
  --data data.json \
  --output onepager.pptx \
  --title "Q1 Analysis"
```

Supported data formats: `.json`, `.csv`, `.xlsx`, `.xls`, `.md`

See [`.github/skills/powerpoint-onepager/README.md`](.github/skills/powerpoint-onepager/README.md) for full documentation.
