# Authoring Guideline

> Read this before writing any pattern page, recipe, or layer chapter. This is the only writing standard for the project.

---

## The One Principle

**A reader should learn the pattern by operating the page, not by reading about it.**

A children's museum teaches gravity by letting you drop a ball, not by handing you a textbook. The Pattern Book teaches AI patterns the same way. Drag the slider. Step through the diagram. Click the query to find out if RAG handles it. The rule is *in* the interaction.

If a section is just text, it's a section that hasn't found its interaction yet.

---

## Three Tests Before You Write Anything

1. **Can this section be operated, not just read?** If yes, find the operation.
2. **What does this section uniquely do?** No two sections should answer the same question.
3. **What word in this section will a non-expert not know?** Define it inline with a hover-tooltip.

---

## Voice

Explain to a smart person who has never built an AI system. Not dumbed down — unjargoned.

- Short sentences. Most under 15 words.
- Concrete before abstract. Show the thing, then name it.
- Active voice. Subject does verb to object.
- Analogies are fine when they earn their place.

**Never:**
- Hedging — "in some sense," "arguably," "it could be said that."
- Marketing — "powerful," "cutting-edge," "state-of-the-art."
- Filler — "really," "very," "quite," "essentially," "actually."
- Jargon without immediate unpacking.
- More than one idea per sentence.

---

## Length

Hard limits, not suggestions. If you can't fit, rewrite shorter.

| Section | Budget |
|---|---|
| Tagline | 1 sentence, under 20 words |
| The idea | 2–3 sentences |
| When it works / fails | 3–6 items, each under 15 words |
| Worked example | 4–6 steps |
| Code sketch | ~10 lines, no boilerplate, no imports |
| Tradeoffs | 3 bars + 1 quotable summary line |

Whole page fits about one desktop scroll of content (plus the interactive sections that scroll naturally).

---

## The Pattern Page Template

Every page has these sections in this order:

1. **Header** — breadcrumb, title, full name, complexity dots, home in taxonomy, last-updated date
2. **The Idea** — one-sentence essence. No flow yet (that's Section 02).
3. **Data Flow** — the diagram. Operate it. Only place the abstract flow lives.
4. **When it Works / When it Fails** — boundary test. Reader predicts, then sees the answer.
5. **Worked Example** — one specific real case (not the abstract flow again).
6. **Code Sketch** — the shape in code. Hover any line for an explanation.
7. **Tradeoffs** — Cost, Latency, Complexity bars + one quotable line.
8. **Back Navigation** — link to the parent category page.

That's the structure. The template file (`templates/pattern.md`) has the exact HTML scaffold.

### What's missing on purpose

- **No "Where to go next" or "Related" section.** It dangles after Tradeoffs and dilutes the close. Use the back-nav to return to the category page; that's where related patterns live.
- **No "Overview" or "Background" section.** Generic headers banned. Section names must say what's inside.
- **No conclusion paragraph.** Tradeoffs is the close. The quotable line is the takeaway.

---

## MECE Sections — No Repetition

Each section does exactly one job:

| Section | Unique job |
|---|---|
| 01 The Idea | The essence in one breath. |
| 02 Data Flow | The mechanism. |
| 03 Works / Fails | The boundary. |
| 04 Worked Example | One specific case. |
| 05 Code | The shape in code. |
| 06 Tradeoffs | The price. |

If two sections show the same thing, one of them is wrong. The flow lives only in Section 02. Section 04 is a *specific case*, not the abstract flow restaged.

---

## Interactive Section Recipes

Don't invent new interaction types per page. Pick from `templates/section-recipes.md`. The recipes are:

- **Animated step-through diagram** (Section 02 default)
- **Step-synced legend** (sits below the diagram, reveals definitions on the current step)
- **Query test cards** (Section 03 default — tap-to-reveal verdicts)
- **Case-file dossier** (Section 04 default — auto-plays one specific real walkthrough)
- **Hover-explainable code** (Section 05 default)
- **Tradeoff bars + quote** (Section 06 default)
- **Inline term tooltip** (anywhere jargon appears)

Pick the recipes that fit your pattern. Adapt where the pattern demands. Don't create new interactions without a clear reason.

---

## Defining Jargon

Every technical term gets a hover-tooltip the first time it appears.

```html
<span class="term" data-def="A list of numbers that represents the meaning of text. Similar meanings get similar lists.">vector</span>
```

Terms to expect across patterns: vector, embedding, top-k, chunks, corpus, prompt, context window, tokens, parameters, fine-tuning. Define each one inline the first time. Don't write a glossary section.

---

## Code Sketches

The code is illustrative, not runnable. Its job is to show the *shape* of the pattern in code, not to be copy-pasted.

- ~10 lines max.
- No imports.
- No error handling unless it's part of the pattern.
- Variable names that read like English.
- Comments only where the code can't speak for itself.

Every line should be hoverable with a one-sentence plain-English explanation.

---

## Tradeoffs

Three dimensions, every pattern, no exceptions: **Cost · Latency · Complexity**.

Use 5-segment bars. Fill with:
- Green segments for low values
- Yellow segments for medium
- Red segments for high

Add a one-sentence summary in italics under the bars. This is the **quotable line** of the page. RAG's: *"Cheap. Fast. Cite-able. Only as good as your retriever."*

---

## The Four Tests Before You Ship

1. **30-second test** — Skim only diagrams, headers, bullets. Do you understand the pattern?
2. **Simple-person test** — Read aloud to a non-AI person. Do they follow?
3. **Expert test** — Show to an experienced AI engineer. No eye-rolls? No nitpicks?
4. **Copy test** — Could a reader explain the pattern to a colleague after one read?

All four must pass. Resist checking the box if you're not sure.

---

## What "Done" Looks Like

A reader opens the page, walks through it in 3–5 minutes (operating, not just reading), and leaves understanding the pattern. They could explain it to a coworker the next day. They know when to use it, when not to, and the rough cost.

If your page achieves that without being more than one desktop scroll of content + interactive sections, it ships.

---

## Editing Pass

Before any page ships:

- Cut 20% of the words.
- Hide every section and check the headers alone tell the story.
- Hide every header and check the interactions tell the story.
- Read one randomly chosen sentence aloud. Does it sound like a person?

If yes to all four, ship.

---

## When the Pattern Doesn't Fit the Template

Two cases:

1. **The pattern is simpler than the template assumes.** Some patterns (Chain-of-Thought) have almost nothing in Section 02 because the diagram is essentially user → model → user. That's fine. Shrink the section. Don't pad it with filler.

2. **The pattern is more than one mechanism.** If a pattern spans multiple layers (PAL, ReAct as a product), it's a **Recipe**, not a Part I pattern. Move it to `recipes/`. Recipes have their own template (still TBD; use the pattern template as a starting point until one exists).

---

## Taxonomy Discipline

Every page declares its home in the metadata header: **Layer · Sub-axis · Category.**

If you're filing a new pattern and aren't sure where it lives:

1. Check `taxonomy.md` for the layer's filing rule.
2. Check the appendix's filing precedents.
3. If a precedent applies, use it. If not, decide using the filing rule and add a new precedent.

The three fragile boundaries are documented in the taxonomy under Rule 3. When in doubt at those boundaries, file by mechanism (what the code does), not by intent (what you want it to mean).
