# Structured Output

> Pattern content for The Pattern Book. Design-agnostic structured markdown. Drop into any page design.

---

## META

- **Title:** Structured Output
- **Full name:** Structured Output (Schema Enforcement / Constrained Decoding)
- **Home:** L4 · Output Shape & Decoding
- **Breadcrumb:** The Pattern Book / L4 · Single-Call Control / Output Shape & Decoding / Structured Output
- **Complexity:** 2 / 5 (two dots filled)
  - *Why:* The idea is simple: make the model return a fixed shape. Calling a hosted "structured output" mode is one line. The conceptual depth (prompting versus token-masking) and the failure modes (silent quality loss, impossible schemas) push it past trivial.
- **Tags:** `single-call · machine-readable · format-guaranteed`
- **Updated:** May 2026

---

## 01 · THE IDEA

**Essence (one sentence):**

Free text breaks code that expects fields; **Structured Output** forces the model to answer in a fixed **schema**, so the next program can read it without guessing.

*Key terms bolded:* **Structured Output**, **schema**.

*Pointer:* ↓ How it works lives in Section 02.

---

## 02 · DATA FLOW

Diagram type: animated step-through (5 nodes). The mechanism that matters is at the SHAPE node, where the schema constrains generation.

**Nodes / steps:**

| # | Node | Sub-label | Step sentence |
|---|------|-----------|---------------|
| 1 | USER | Request + schema | User sends a request and the target shape they want back. |
| 2 | SCHEMA | Rules → grammar | The schema is compiled into rules the decoder can enforce. |
| 3 | MODEL | Generate tokens | The model produces the answer one token at a time. |
| 4 | SHAPE | Mask invalid tokens | At each step, tokens that would break the schema are blocked. |
| 5 | PARSE | Valid object | Code parses the guaranteed-valid output and uses it directly. |

**Step-synced legend (one row per non-obvious node):**

- **SCHEMA:** the shape you require, written as a [schema]. A JSON Schema or a grammar that lists allowed fields, types, and values.
- **MODEL:** the language model writes the answer as a stream of [tokens], the small text pieces it predicts one at a time.
- **SHAPE:** [constrained decoding] in action. Before each token is chosen, choices that would violate the schema are removed, so the running output stays valid.
- **PARSE:** turn the text into a real object (a [JSON] dictionary, a typed record) the rest of the program can call into.

**Two-mode toggle (interaction, sits under the diagram):**

A small two-state toggle. The reader flips it and the SHAPE node changes behaviour. This is the one idea most people get wrong, so make them operate it.

| Toggle state | What SHAPE does | What you actually get |
|---|---|---|
| **Ask nicely** (prompt for JSON) | Nothing enforces the shape. The prompt requests JSON; the model usually complies. | Output is *probably* valid. It can still break format or drift off-schema, and your code must guard for that. |
| **Enforce** (constrained decoding) | Before each token, choices that would violate the schema are masked out. | Output *cannot* be malformed. It always parses and always matches the schema. |

*Caption to keep nearby:* A plain "JSON mode" sits between the two: it guarantees the result parses as [JSON], but not that it matches your specific schema. Only schema-level enforcement guarantees the exact shape.

---

## 03 · WHEN IT WORKS · WHEN IT FAILS

Query test cards. Tap to reveal verdict + one-sentence reason. Mix is 3 works / 3 fails.

1. **Q:** "Extract invoice number, total, and due date from this PDF text into fields our billing system reads."
   **Verdict:** Works ✓
   **Reason:** Downstream code needs exact fields; enforcing the schema means it never has to parse prose.

2. **Q:** "Classify each support ticket as billing, bug, or feature_request."
   **Verdict:** Works ✓
   **Reason:** A closed set of labels maps cleanly to an enum the decoder can lock to one of three values.

3. **Q:** "Return tool-call arguments so our function runs without a human reading them."
   **Verdict:** Works ✓
   **Reason:** Function calling is structured output: the args must be a valid object or the call crashes.

4. **Q:** "Write a thoughtful product critique for the launch blog."
   **Verdict:** Fails ✗
   **Reason:** There is no shape to enforce; a schema adds nothing and the value is in the prose.

5. **Q:** "Force a strict JSON schema onto a hard medical-reasoning question to keep it tidy."
   **Verdict:** Fails ✗
   **Reason:** Tight constraints can crowd out the model's reasoning room and quietly lower answer quality.

6. **Q:** "Make the model fill a schema that demands a field the input never contains."
   **Verdict:** Fails ✗
   **Reason:** An impossible schema forces a guess or an empty value; constraints cannot invent missing facts.

**Tracker:** `0 / 6 tested` → `✓ All 6 tested: pattern understood`

---

## 04 · WORKED EXAMPLE

One specific case, end to end. A resume parser feeding an applicant-tracking database. This is a real extraction case, not the abstract flow restaged.

**Case file**

- **User asks (system input):** "Parse this resume into our candidate record."
  Raw text pasted in: *"Mariam Hassan, Senior Backend Engineer. 7 yrs. Cairo. Python, Go, Postgres. mariam.h@example.com."*

- **Step 1: Define the schema.**
  The ATS needs five typed fields: `name` (string), `title` (string), `years_experience` (integer), `email` (string), `skills` (array of strings). Anything else is rejected.

- **Step 2: Constrain generation.**
  The schema is compiled to a grammar. As the model writes, the decoder masks any token that would break the shape. It cannot, for example, put words where `years_experience` expects a number.

- **Step 3: Generate under the constraint.**
  The model reads "7 yrs" and decides the value is 7. The constraint does a different job: it only allows digits in `years_experience`, so the model cannot park the text `"7 yrs"` in a number field. The model finds the value; the constraint enforces the type.

- **Step 4: Parse and store.**
  The output is guaranteed valid against the schema, so the ATS loads it with no try/except parsing dance and no defensive cleanup.

- **→ Answer returned (the object):**
  ```json
  {
    "name": "Mariam Hassan",
    "title": "Senior Backend Engineer",
    "years_experience": 7,
    "email": "mariam.h@example.com",
    "skills": ["Python", "Go", "Postgres"]
  }
  ```
  *Pull-quote callout:* The downstream system never sees prose. It receives a record it can write straight to a row.

---

## 05 · CODE SKETCH

~10 lines. Hover any line for the explanation. No imports, no error handling.

```python
# 1. declare the shape you require
class Candidate(Schema):
    name: str
    years_experience: int
    skills: list[str]

# 2. call the model, forcing output to match
record = model.generate(resume_text, schema=Candidate)

# 3. use the parsed object directly: no string parsing
save_to_database(record.name, record.years_experience)
```

**Per-line explanation (for hover):**

- `# 1. declare the shape you require`: Comment marking the schema definition.
- `class Candidate(Schema):`: Define the target shape. This class is the schema the output must satisfy.
- `    name: str`: One field, typed as text.
- `    years_experience: int`: One field, typed as a whole number. The decoder will not allow words here.
- `    skills: list[str]`: One field, typed as a list of text values.
- *(blank line)*: (blank line)
- `# 2. call the model, forcing output to match`: Comment marking the constrained call.
- `record = model.generate(resume_text, schema=Candidate)`: Run generation under the schema. Invalid tokens are masked, so the result is a valid `Candidate`.
- *(blank line)*: (blank line)
- `# 3. use the parsed object directly: no string parsing`: Comment marking safe downstream use.
- `save_to_database(record.name, record.years_experience)`: Read fields off the object as plain attributes. No JSON parsing or cleanup needed.

---

## 06 · TRADEOFFS

5-segment bars (green = low, yellow = medium, red = high).

- **Cost:** Low. (2/5 green). One model call, no extra round-trips. The constraint runs during decoding.
- **Latency:** Low to Medium. (2/5, green + yellow). Compiling the schema into a constraint costs once. After that, per-token masking adds little overhead.
- **Complexity:** Medium. (3/5, green + yellow x2). Easy to call a hosted mode. Harder to author good schemas, and to spot when constraints quietly hurt quality.

**Quotable line (italic, the page takeaway):**

*Guarantees the shape, not the wisdom. JSON mode parses; only enforcement matches your schema.*

---

## TERMS (inline tooltip definitions)

- **schema:** A description of the shape you require: which fields exist, their types, and allowed values.
- **JSON:** A plain-text format for nested key-value data that almost every program can read.
- **JSON mode:** A model setting that guarantees the output parses as JSON, but not that it matches your specific schema.
- **constrained decoding:** Blocking tokens that would break the required shape, at the moment each token is chosen.
- **guided generation:** Another name for constrained decoding. The allowed output is restricted to a grammar or pattern.
- **tokens:** The small text pieces a model predicts one at a time. Words are often one to three tokens.
- **grammar:** A set of rules listing which sequences of tokens are allowed. A schema can be compiled into one.
- **function calling:** A structured-output mode where the model returns arguments for a function as a valid object.
- **enum:** A field whose value must be one of a fixed, listed set of options.

---

## REFERENCES

- Willard, B. T., & Louf, R. (2023). *Efficient Guided Generation for Large Language Models.* arXiv:2307.09702. https://arxiv.org/abs/2307.09702. Introduces finite-state-machine indexing over a model's vocabulary to enforce regular expressions and grammars during decoding; implemented in the open-source Outlines library.
- OpenAI. *Structured Outputs* (API guide). https://platform.openai.com/docs/guides/structured-outputs. Describes schema-enforced outputs and function calling; documents the distinction between plain JSON mode and schema-constrained generation.
