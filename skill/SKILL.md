---
name: hackathon-tech-doc
description: >
  Generate a one-page technical document (.docx) for a hackathon/project with a
  LOCKED, jury-consistent structure: title block, Overview, Architecture (text
  arrow-flow + principle), Technology Stack (narrative + 3-column table), and an
  Appendix of step details. Use this whenever a team asks for a "technical doc",
  "ტექნიკური დოკუმენტი", architecture description, or a short project write-up for
  the jury. Supports Georgian (ka), English (en), or bilingual (both) labels —
  the structure stays identical across every team.
---

# Hackathon Technical Doc

Produces the **same one-page technical document** for every team so the jury sees
an identical layout and can compare projects fairly. Teams supply only the
*content*; the structure, fonts, colors, section order, and table shape are fixed
by `generate_doc.py` and must not be changed.

## What the document always contains (do not reorder or add sections)

1. **Title block** — project title (navy), then `org | event | doc-kind` subtitle.
2. **1. Overview** — one paragraph: what the product is (lead sentence in bold)
   and its technical core.
3. **2. Architecture** — a single text **arrow-flow** of the pipeline
   (`A → B → C → …`) followed by one short *principle* sentence. No image.
4. **3. Technology Stack** — one narrative paragraph, then a 3-column table:
   **Layer | Technology | Why this choice**.
5. **4. Appendix: Step Details** — short bold-labeled bullets (`Label: text`),
   marked as supplementary "read only if time permits" material.

## ⛔ HARD LIMIT: exactly ONE page — never more

The document **MUST fit on a single page**. A two-page result is a **failed
output**, not an acceptable one — do not deliver it. This rule overrides
completeness: if the team gives more information than fits, **cut it**.

Length budget per field (these are ceilings, not targets — `example_content.json`
is already at the maximum density):

| field | max |
|-------|-----|
| `title` | 50 characters |
| `overview` | 4 sentences / ~60 words |
| `architecture_flow` | 9 stages, each ≤ 30 characters |
| `architecture_principle` | 1 sentence / ~12 words |
| `stack_summary` | 2 sentences / ~40 words |
| `stack_table` | 8 rows; "why" cell ≤ ~15 words |
| `appendix` | 5 items, each ≤ ~30 words |

If the output is longer than one page, trim in this order until it fits:
1. Shorten `appendix` items, then drop the least important ones (keep ≥ 3).
2. Shorten the "why" cells in `stack_table`, then merge/drop minor rows (keep ≥ 5).
3. Shorten `stack_summary`, then `overview`.

**Never** fix overflow by reducing fonts, margins, or spacing, editing
`generate_doc.py`, or removing a required section.

## How to use

1. **Gather the team's project info.** Ask for: project title; org + event (e.g.
   "GTU | Hackathon 2026"); a 3–4 sentence overview; the pipeline stages in order;
   a one-line design principle; 5–9 stack rows (layer / tech / why); and 3–6
   appendix steps. If something is missing, infer sensibly from what they describe
   and keep each field tight.
2. **Choose language.** Set `"lang"` to `"ka"`, `"en"`, or `"both"`. Section
   headings and table headers come from a fixed label set — the team only controls
   the body language of their own text.
3. **Write a `content.json`** following `example_content.json`. Use `**double
   asterisks**` to bold a span inside `overview` / `stack_summary` (e.g. the lead
   sentence). Each appendix item is `"Label: description"`.
4. **Generate:**
   ```bash
   pip install python-docx --break-system-packages -q
   python generate_doc.py content.json <project>-technical-doc.docx
   ```
5. **Verify one page (mandatory).** If LibreOffice is available, convert to PDF
   and check the page count:
   ```bash
   soffice --headless --convert-to pdf <project>-technical-doc.docx
   ```
   If it is more than 1 page, trim content (see the trim order above), regenerate,
   and check again. Repeat until it is exactly 1 page. If you cannot render, stay
   strictly within the length budget above. Only then share the `.docx`.

## Input fields (content.json)

| field | required | notes |
|-------|----------|-------|
| `lang` | no | `"ka"` (default), `"en"`, or `"both"` |
| `title` | yes | project name |
| `org` | no | institution / team |
| `event` | no | e.g. "Hackathon 2026" |
| `overview` | yes | 3–4 sentences; `**bold**` the lead clause |
| `architecture_flow` | yes | array of pipeline stages, rendered as `A → B → C` |
| `architecture_principle` | no | one short sentence |
| `stack_summary` | no | one narrative paragraph above the table |
| `stack_table` | yes | array of `[layer, technology, why]` rows (5–9 ideal) |
| `appendix` | no | array of `"Label: text"` strings (3–6 ideal) |

## Rules for consistency (important for jury fairness)

- **Never** edit `generate_doc.py` styling, rename sections, add/remove sections,
  or change the table columns. Every team's doc must look the same.
- The architecture is always the **text arrow-flow**, never an embedded image.
- **ONE page maximum — no exceptions.** Respect the length budget; shorten or cut
  content, never reduce the locked fonts or spacing. Never deliver a 2-page doc.
- The example (`example_content.json`) reproduces a real reference doc — use it as
  the gold standard for tone, density, and length.

## Files

- `generate_doc.py` — the locked generator (run it; don't modify it).
- `example_content.json` — a complete worked example to copy the shape from.
