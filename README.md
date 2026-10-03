# Hackathon Technical Doc / ჰაკათონის ტექნიკური დოკუმენტი

Claude-ის **skill**, რომელიც ნებისმიერი პროექტისთვის ქმნის 1-გვერდიან ტექნიკურ
დოკუმენტს (`.docx`) **ფიქსირებული სტრუქტურით**. ყველა გუნდი ერთნაირ layout-ს იღებს,
ჟიური კი ადვილად ადარებს პროექტებს.

ყველაფერი კეთდება ბრაუზერში, **Google Colab**-ით — ინსტალაცია არ გჭირდება, საჭიროა
მხოლოდ Google ანგარიში.

---

## ნაბიჯი 1: დააგენერირე შენი პროექტის content

დააკოპირე ქვემოთ მოცემული prompt, ჩასვი ნებისმიერ AI-ში (claude.ai, ChatGPT და სხვ.)
და ბოლოში, `<<< … >>>`-ის ნაცვლად, აღწერე შენი პროექტი: რას აკეთებს, ვისთვისაა,
რა ტექნოლოგიებს იყენებ, როგორია architecture/pipeline და რა ძირითადი ტექნიკური
გადაწყვეტილებები მიიღე ან შეგიძლია პირდაპირ კოდი ჩაუგდო(მთლიანი პროექტი) LLM ს და ის დაგიწერს.

AI დაგიბრუნებს მზა Python code block-ს — `content = {…}`. შეინახე, მე-4 ნაბიჯში
დაგჭირდება.

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
- Start the block with `import json` and end with exactly this line:
  json.dump(content, open("content.json","w"), ensure_ascii=False, indent=2)

Here is my project description:
<<< Describe your project here: what it does, for whom, what technologies you use,
the architecture/pipeline, and the key technical decisions >>>
```

სრული მაგალითი იხილე [skill/example_content.json](skill/example_content.json)-ში.

---

## ნაბიჯი 2: გახსენი Google Colab

გადადი <https://colab.research.google.com>-ზე → **New notebook**.

## ნაბიჯი 3: პირველ cell-ში ჩასვი და გაუშვი

```python
!git clone https://github.com/andriagv/hackathon-tech-doc-github.git
%cd hackathon-tech-doc-github/skill
!pip install python-docx -q
```

## ნაბიჯი 4: მეორე cell-ში ჩასვი შენი content

დაამატე ახალი cell (**+ Code**), ჩასვი 1-ლ ნაბიჯში AI-სგან მიღებული
`content = {…}` ბლოკი და გაუშვი. ის შექმნის `content.json` ფაილს.

## ნაბიჯი 5: დააგენერირე დოკუმენტი

ახალ cell-ში:

```python
!python generate_doc.py content.json my-project-technical-doc.docx
```

## ნაბიჯი 6: გადმოწერე

Colab-ის მარცხენა **Files** პანელში გახსენი `hackathon-tech-doc-github/skill`,
`my-project-technical-doc.docx`-ზე დააჭირე right-click → **Download**.

---

## წესები

- **ნუ** შეცვლი სტილს `generate_doc.py`-ში, ნუ დაარქმევ/დაამატებ სექციებს და ნუ
  შეცვლი `stack_table`-ის სვეტებს — სწორედ ეს უზრუნველყოფს ერთგვაროვან დოკუმენტებს.
- შეინარჩუნე **ერთი გვერდი**: ტექსტი მოამოკლე, შრიფტი ნუ დაამცირებ.

![preview](docs/preview.png)

## License

MIT — see [LICENSE](LICENSE).
