# Hackathon Technical Doc / ჰაკათონის ტექნიკური დოკუმენტი

A Claude **skill** that generates a one-page technical document (`.docx`) for any
project with a **locked, jury-consistent structure**. Every team produces the same
layout, so judges can compare projects fairly.

> Claude-ის **სქილი**, რომელიც ნებისმიერი პროექტისთვის ქმნის 1-გვერდიან ტექნიკურ
> დოკუმენტს (`.docx`) **ფიქსირებული, ჟიურისთვის თანმიმდევრული სტრუქტურით**. ყველა
> გუნდი ერთნაირ ლეიაუტს იღებს, ჟიური კი ადვილად ადარებს პროექტებს.


## What's inside / რა შედის

```
skill/
  SKILL.md             # the skill definition Claude reads
  generate_doc.py      # locked .docx generator (do not edit styling)
  example_content.json # a complete worked example
dist/
  hackathon-tech-doc.skill   # installable bundle (zip) for Cowork/Claude
examples/
  atlas-sample-output.docx   # sample rendered output
```

## Document structure (fixed) / დოკუმენტის სტრუქტურა (ფიქსირებული)

1. **Title block** — project title + `org | event | doc-kind` subtitle
2. **Overview** — what the product is + its technical core
3. **Architecture** — a text arrow-flow (`A → B → C …`) + one design principle
4. **Technology Stack** — narrative paragraph + a 3-column table (Layer · Technology · Why)
5. **Appendix** — short bold-labeled step details

Languages / ენები: Georgian (`ka`), English (`en`), or bilingual (`both`). The
section headings come from a fixed label set, so the structure is identical for
every team regardless of language.

---

## Browser only — no install needed / მხოლოდ ბრაუზერით, ინსტალაციის გარეშე

If a team has **no Claude desktop and no Python installed**, run everything in the
browser with **Google Colab** (free — only a Google account is needed):

> თუ გუნდს **არც Claude-ის დესკტოპი აქვს და არც Python დაყენებული**, ყველაფერი
> ბრაუზერში კეთდება **Google Colab**-ით (უფასო — საჭიროა მხოლოდ Google ანგარიში):

**1. Open Google Colab and create a new notebook**

Go to <https://colab.research.google.com> → **New notebook**.

**2. In the first cell, clone the repo and install the dependency**

```python
!git clone https://github.com/andriagv/hackathon-tech-doc-github.git
%cd hackathon-tech-doc-github/skill
!pip install python-docx -q
```

**3. In a new cell, write your project content**

Copy the shape below and fill in your project details
(or see `skill/example_content.json` for a complete worked example):

```python
import json
content = {
    "lang": "ka",
    "title": "ჩემი პროექტი",
    "org": "ჩემი გუნდი",
    "event": "Hackathon 2026",
    "overview": "**ერთი წინადადება პროდუქტზე.** დანარჩენი აღწერა...",
    "architecture_flow": ["წყარო", "დამუშავება", "ბაზა", "API", "UI"],
    "architecture_principle": "მოკლე პრინციპი.",
    "stack_summary": "სტეკის მოკლე აღწერა.",
    "stack_table": [
        ["Backend",  "FastAPI",      "სწრაფი async API"],
        ["DB",       "PostgreSQL",   "სანდო რელაციური ბაზა"],
        ["Frontend", "React",        "კომპონენტური UI"]
    ],
    "appendix": ["ნაბიჯი 1: დეტალი.", "ნაბიჯი 2: დეტალი."]
}
json.dump(content, open("content.json", "w"), ensure_ascii=False, indent=2)
```

> **არ გინდა JSON ხელით შეავსო?** გამოიყენე პრომპტი ქვემოდან — ჩასვი ნებისმიერ
> AI-ში (claude.ai, ChatGPT და სხვ.) და მზა `content = {…}` ბლოკს დაგიბრუნებს,
> ზუსტად ისე, როგორც ეს უჯრა ელის.

---

### Prompt to auto-generate your content (give this to any AI)

```
You are an assistant that prepares the content for a hackathon technical document.
Return ONLY a single Python code block — a `content = {…}` dictionary — and nothing
else (no explanation, no text outside the block). End it with the json.dump line.

Fill in EXACTLY these fields (do not change the structure, do not add/remove fields):
- lang: "en"   (use "ka" for Georgian, or "both" for bilingual headings)
- title: project name (short)
- org: team / university
- event: e.g. "Hackathon 2026"
- overview: 3–4 sentences. Wrap the first sentence — what the product does — in
  **double asterisks** (bold). Then describe the technical core.
- architecture_flow: a list of 5–9 pipeline stages in order (source → … → UI).
- architecture_principle: one short sentence — the key design principle.
- stack_summary: one paragraph on the logic of the stack.
- stack_table: 5–9 rows, each = [layer, technology, why this choice]. Make "why" specific.
- appendix: 3–6 items, each formatted as "Label: detail."

Rules:
- Write all text in English (keep product/tech names as they are).
- Be concrete and concise — the document must fit on ONE page. Do not over-write.
- Do not invent facts; if something is unclear, infer reasonably from my description.
- End with exactly this line:
  json.dump(content, open("content.json","w"), ensure_ascii=False, indent=2)

Here is my project description:
<<< Describe your project here: what it does, for whom, what technologies you use,
the architecture/pipeline, and the key technical decisions >>>
```

Copy the result into Colab cell 3, run it, then continue below.

---

**4. Generate the document**

```python
!python generate_doc.py content.json my-project-technical-doc.docx
```

**5. Download it**

In Colab's left **Files** panel, open `hackathon-tech-doc-github/skill`,
right-click `my-project-technical-doc.docx` → **Download**.

---

## Use with Claude Code agent (VS Code / Cursor)

If you have **Claude Code** running as an agent inside your IDE, you don't need to
clone this repo at all. The agent can read the skill definition directly from GitHub
and generate the `.docx` file on its own.

> თუ IDE-ში (VS Code, Cursor) **Claude Code** აგენტი გაქვს, repo-ს კლონირება არ
> გჭირდება — აგენტი SKILL.md-ს პირდაპირ GitHub-ზე კითხულობს და `.docx`-ს თავისით
> ქმნის.

**1. Add one line to your project's `CLAUDE.md`** (create it in the project root if
it doesn't exist):

```markdown
For generating a hackathon technical document (.docx), read the skill definition at:
https://raw.githubusercontent.com/andriagv/hackathon-tech-doc-github/main/skill/SKILL.md
```

**2. That's it.** Now ask the agent (in chat or via `/hackathon-tech-doc`):

> *"Create a technical doc for my project."*

The agent will:
1. Fetch and read the skill definition from the URL above
2. Gather your project's info from the existing code and conversation
3. Generate the `.docx` — no Python install or repo clone needed

---

## Install as a Claude skill / Claude სქილად დაყენება

**Cowork / Claude desktop:** open `dist/hackathon-tech-doc.skill` and click
**Save skill**. Then ask Claude: *"create a technical doc for my project"*.

> **Cowork / Claude desktop:** გახსენი `dist/hackathon-tech-doc.skill` და დააჭირე
> **Save skill**-ს. შემდეგ სთხოვე Claude-ს: *„შემიქმენი ტექნიკური დოკი ჩემს პროექტზე"*.

## Use from the command line / CLI-ით გამოყენება

```bash
git clone https://github.com/andriagv/hackathon-tech-doc-github.git
cd hackathon-tech-doc-github/skill
pip install python-docx --break-system-packages -q
# copy example_content.json, fill in your project, then:
python generate_doc.py content.json my-project-technical-doc.docx
```

## content.json fields

| field | required | notes |
|-------|----------|-------|
| `lang` | no | `"ka"` (default), `"en"`, or `"both"` |
| `title` | yes | project name |
| `org` | no | institution / team |
| `event` | no | e.g. "Hackathon 2026" |
| `overview` | yes | 3–4 sentences; `**bold**` the lead clause |
| `architecture_flow` | yes | array of pipeline stages → rendered as `A → B → C` |
| `architecture_principle` | no | one short sentence |
| `stack_summary` | no | one narrative paragraph above the table |
| `stack_table` | yes | array of `[layer, technology, why]` rows (5–9 ideal) |
| `appendix` | no | array of `"Label: text"` strings (3–6 ideal) |

## Consistency rules / თანმიმდევრობის წესები

Do **not** edit the styling in `generate_doc.py`, rename or add sections, or change
the table columns — that keeps every team's document looking the same. Keep output
to **one page**: shorten prose rather than shrinking fonts.

> **ნუ** შეცვლი სტილს `generate_doc.py`-ში, ნუ დაარქმევ/დაამატებ სექციებს და ნუ
> შეცვლი ცხრილის სვეტებს — სწორედ ეს უზრუნველყოფს ერთგვაროვან დოკუმენტებს.
> შეინარჩუნე **ერთი გვერდი**: ტექსტი მოამოკლე, შრიფტი ნუ დაამცირებ.

![preview](docs/preview.png)

## License

MIT — see [LICENSE](LICENSE).
