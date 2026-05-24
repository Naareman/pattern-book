# Pattern Page Template

> This is the structural template. Every pattern page has these sections in this order. Section content varies; structure does not. For the visual style and interaction recipes, see `section-recipes.md` and `../example/rag.html`.

The output is HTML, not Markdown. Use `example/rag.html` as the starting point; copy and adapt.

---

## Section Order (locked)

```
┌────────────────────────────────────────────┐
│ HEADER                                     │
│  · Breadcrumb (Pattern Book / L_ / cat /)  │
│  · Pattern title (large serif)             │
│  · Full name (italic)                      │
│  · Meta strip: Home · Complexity · Tags    │
│    · Updated                                │
├────────────────────────────────────────────┤
│ 01 · The Idea                              │
│  Big serif sentence, key terms bolded      │
├────────────────────────────────────────────┤
│ 02 · Data Flow                             │
│  Animated step-through diagram + legend    │
├────────────────────────────────────────────┤
│ 03 · When it Works · When it Fails         │
│  Query test cards, tap-to-reveal           │
├────────────────────────────────────────────┤
│ 04 · Worked Example                        │
│  Case-file dossier, one specific real case │
├────────────────────────────────────────────┤
│ 05 · Code Sketch                           │
│  ~10 lines, hover any line for explainer   │
├────────────────────────────────────────────┤
│ 06 · Tradeoffs                             │
│  Cost / Latency / Complexity bars + quote  │
├────────────────────────────────────────────┤
│ BACK NAVIGATION                            │
│  Link to parent category page              │
├────────────────────────────────────────────┤
│ FOOTER                                     │
│  Layer · Category · Pattern Book           │
└────────────────────────────────────────────┘
```

No "Where to go next." No "Related." No "Overview." No "Conclusion."

---

## Section-by-Section Content Spec

### Header

- **Breadcrumb** — exact path through taxonomy: `Pattern Book / [Layer] / [Category] / [Pattern]`.
- **Title** — short, canonical name. "RAG" not "Retrieval-Augmented Generation (RAG)". Save the full form for the full-name field.
- **Full name** — italic, smaller. "Retrieval-Augmented Generation".
- **Meta strip** — four items:
  - **Home** — `L3a · Retrieval Strategies` (layer · sub-axis or category)
  - **Complexity** — five dots, fill 1–5 based on conceptual difficulty + implementation cost
  - **Tags** — 2–4 short descriptors. Examples: `stateless · grounded · cite-able`
  - **Updated** — month and year

### 01 · The Idea

One Fraunces-serif sentence (large, ~28–38px). Bold the key terms. End with a pointer down to Section 02 ("How it works lives in Section 02").

The idea section does **not** show the flow. It states the essence in one breath.

### 02 · Data Flow

The animated step-through diagram. Single source of truth for *how the pattern works mechanically*.

Components:
- Diagram with labeled nodes and arrows, animatable
- Step counter and one-sentence step label
- Three controls: **Step**, **Play**, **Reset**
- Step-synced legend below (reveals one row at a time matching the active node)

Use the diagram-and-legend recipe from `section-recipes.md`.

### 03 · When it Works · When it Fails

Query test cards. 4–6 specific real-sounding questions. Each card hides a verdict:
- Green for "Handles it ✓" with reason
- Red for "Fails ✗" with reason

Reader taps each card to predict, then sees the verdict. Tracker at the bottom counts progress.

Don't replace this with a bulleted "works on / doesn't work on" list. The prediction-then-reveal is the learning move.

### 04 · Worked Example

One specific real case end-to-end. Not the abstract flow restaged. Not a hypothetical.

For RAG: a real HR question, real retrieved chunks, real cited answer. For ReAct: a real research task, real tool calls, real intermediate results. For LLM-as-Judge: a real generator output, a real judgment, a real decision.

Case-file dossier styling. Auto-plays when scrolled into view.

If you find yourself rendering the same flow as Section 02, you're doing the wrong thing. Pick a *case*, not the abstract sequence.

### 05 · Code Sketch

~10 lines. Hover any line for a one-sentence plain-English explanation.

- No imports
- No error handling unless part of the pattern
- Variable names read like English
- Comments only where the code can't speak for itself

If your pattern can't be shown in 10 lines, you're showing the wrong thing. Show the smallest representative shape.

### 06 · Tradeoffs

Three rows: Cost, Latency, Complexity. 5-segment bars each (green = low, yellow = medium, red = high).

Below the bars: one quotable italic line summarizing the pattern. This is what readers will quote.

Don't compare to alternative patterns. Name the tradeoff for this pattern as it stands.

### Back Navigation

A single clickable card at the bottom, before the footer. Returns the reader to the parent category page.

- Arrow icon on the left
- "Back to category" hint
- Category name (e.g., "L3a · Retrieval Strategies")

### Footer

One line, all caps, faint mono:
```
[Layer] · [Category] · The Pattern Book
Pattern [number]
```

---

## What Goes Where When Patterns Don't Fit Cleanly

### Patterns with thin Data Flow

Some patterns are essentially single-call (CoT, structured output). The diagram is just `USER → MODEL → USER` with internal complexity. That's fine. Make the diagram smaller and put the interesting interaction elsewhere — usually inside Section 04 (the case) which can be much richer.

### Patterns with no clean query test

Some patterns don't have "input domains it handles vs doesn't" (CoT helps or hurts depending on the *type of question*, not the *content*). Replace the query test with a different prediction interaction: present two prompts and ask which one CoT will improve. The structure (predict-then-reveal) stays; the question shape adapts.

### Patterns that are really Recipes

If you need more than one diagram, or your code sketch keeps growing past 10 lines, or your worked example spans multiple distinct steps with different mechanisms — you're not writing a pattern, you're writing a recipe. Move to `recipes/`.

---

## File Naming

```
patterns/
├── L3a-rag.html
├── L4-chain-of-thought.html
├── L2-llm-as-judge.html
└── ...
```

Format: `[layer]-[pattern-slug].html`. Lowercase. Hyphens, not underscores.
