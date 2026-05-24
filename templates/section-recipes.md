# Section Recipes

> The catalog of interactive section types. Don't invent new interactions per page; pick from these. Adapt where the pattern demands.

For implementation, refer to `../example/rag.html`. This file describes *what each recipe does and when to use it*.

---

## Recipe 1 — Animated Step-Through Diagram

**Where it lives:** Section 02 (Data Flow).
**What it does:** Walks the reader through the mechanism one step at a time. The reader operates Step, Play, and Reset.

**Visual elements:**
- Nodes (boxes with labels): plain state when idle, red and lifted when active, green when done
- Arrows between nodes: faint when idle, red when active step crosses them, green when done
- Step counter showing current step / total
- One-sentence step description below the diagram
- Three buttons: **Step** (advance one), **Play** (auto-walk), **Reset** (back to start)

**Key behaviors:**
- The diagram has a fixed minimum height (~120px) so the active-node lift doesn't push other elements around
- The step-info area has a fixed minimum height (~56px) so changing text doesn't reflow
- The controls row has a fixed minimum height (~60px) so the whole panel stays solid

**When to use:** Every pattern. This is the default Section 02.

**When to adapt:** If the pattern is single-call (CoT), the diagram has 3 nodes instead of 5. Shrink the diagram, keep the controls. Don't pad with imaginary intermediate steps.

---

## Recipe 2 — Step-Synced Legend

**Where it lives:** Below the diagram in Section 02.
**What it does:** Reveals the definition of the current step's node, one row at a time.

**Visual elements:**
- Dashed-border box below the diagram
- Empty-state line by default: *"Step through the flow ↑ Definitions appear here"*
- One row per non-self-explanatory node (USER nodes typically don't need a row)
- Each row: bold short label (`EMBED`) + plain-English explanation, with hover-tooltips on jargon

**Key behaviors:**
- Box has fixed minimum height so layout doesn't jump
- Only one row visible at a time — the one matching the current active step
- Empty state returns when no step has a corresponding row (USER nodes, complete state)

**When to use:** Anywhere the diagram has nodes with names a non-expert won't recognize (EMBED, SEARCH, RERANK, JUDGE, etc).

**When to skip:** If every node is named in plain English (USER, MODEL, ANSWER), no legend needed.

---

## Recipe 3 — Query Test Cards

**Where it lives:** Section 03 (When it Works · When it Fails).
**What it does:** Presents 4–6 real-sounding questions or scenarios. Reader taps each to reveal whether the pattern handles it. Builds boundary intuition through prediction.

**Visual elements:**
- Stack of card-shaped tappable items
- Each card shows a single question in Fraunces serif
- Tapping reveals: colored border (green = handles, red = fails) + verdict label + one-sentence reason
- Tracker at the bottom: `0 / 6 tested` → `✓ All 6 tested — pattern understood`

**Key behaviors:**
- Cards are independent; tap in any order
- Reveals are permanent within the session
- Mix of clear positives and clear negatives — 3 green and 3 red is a good default
- The reason matters more than the verdict. One sentence each.

**When to use:** Patterns with clean input/output boundaries (RAG, tool use, structured output).

**When to adapt:** For patterns with no domain boundaries (CoT helps based on *task type*, not *input domain*), replace with a different prediction interaction. Same shape — predict-then-reveal — different content.

---

## Recipe 4 — Case-File Dossier

**Where it lives:** Section 04 (Worked Example).
**What it does:** Walks through one specific real case from input to output, with each step animating in.

**Visual elements:**
- White card with `CASE FILE` stamp in top-right
- User question at the top in a bordered box
- 3–5 numbered steps below, each appearing in sequence:
  - Circular number badge (turns green when done)
  - Step heading (mono caps)
  - Step detail (Fraunces serif), with code-styled snippets and pull-quote callouts where useful
- Final answer in a black bar at the bottom (visually distinct from intermediate steps)
- Replay button below

**Key behaviors:**
- Auto-plays once when the section scrolls into view (using IntersectionObserver)
- Replay button restarts the animation
- Each step has a brief delay before the next appears (~900ms)
- Step badges turn green as they complete
- The final answer is the visual punchline — different styling, comes after all steps are done

**When to use:** Every pattern. The case grounds the abstraction.

**When to adapt:**
- For multi-step agents (ReAct), the dossier may have more steps and require a different visual rhythm (turns of think-act-observe).
- For instant patterns (CoT), the dossier may be just two states (input → output) with the reasoning shown in the middle as a "thought bubble."
- For training-time patterns (L1), the dossier is more like a "before / after" comparison.

**Critical:** This is **a specific case**, not the abstract flow from Section 02 re-staged. If you're showing the same nodes with the same labels, you're doing the wrong thing.

---

## Recipe 5 — Hover-Explainable Code

**Where it lives:** Section 05 (Code Sketch).
**What it does:** Shows the pattern's shape in ~10 lines of code. Hovering any line shows a one-sentence plain-English explanation in the box below.

**Visual elements:**
- Monospace code block on paper-warm background
- Syntax-colored: red for keywords, green for strings, faint for comments
- Each line wrapped in a hoverable span
- Hovered line gets a yellow background
- Explainer box below the code: dashed border, Fraunces serif, shows the explanation for whichever line is hovered

**Key behaviors:**
- Idle state shows "Hover a line ↑" in the explainer
- Mouse leaves the code → returns to idle? Or holds the last hovered explanation? Pick one and be consistent. (Default: returns to idle.)
- Blank lines exist for visual spacing but their explainer says `(blank line)` if hovered

**When to use:** Every pattern that involves code-level mechanism. Some training-time patterns may skip code (the "code" is a training config) — in that case, replace with a different shape: a small YAML config, a hyperparameter table, etc.

**Constraint:** ~10 lines is hard limit. If you need more, you're not showing the pattern, you're showing an implementation.

---

## Recipe 6 — Tradeoff Bars + Quote

**Where it lives:** Section 06 (Tradeoffs).
**What it does:** Shows Cost, Latency, and Complexity as 5-segment bars, with a quotable one-line summary.

**Visual elements:**
- Three rows, each: bold mono label (`COST`, `LATENCY`, `COMPLEXITY`) + 5 bar segments
- Segments fill green (low) → yellow (medium) → red (high)
- Below the rows: dashed border separator
- Italic Fraunces line below: the quotable summary, prefixed with `— `

**Key behaviors:**
- The bars are static (not interactive). They state the pattern's character.
- The quote is the page's takeaway line. Should survive being copy-pasted out of context.

**When to use:** Every pattern. No exceptions. Same format on every page so readers build muscle memory.

**Don't:** Compare to other patterns ("RAG is cheaper than fine-tuning"). State the tradeoff for this pattern as it stands.

---

## Recipe 7 — Inline Term Tooltip

**Where it lives:** Anywhere jargon appears for the first time.
**What it does:** Underlines the term with a yellow highlight; hover shows a black tooltip with the plain-English definition.

**Visual elements:**
- The term: dotted underline, half-yellow background highlight, help cursor
- The tooltip: black box, white text, appears above the term on hover, has a small downward-pointing arrow

**Key behaviors:**
- Tooltip appears with a 0.2s fade-in
- Tooltip is `max-width: 280px` so it doesn't sprawl across the screen
- Definition should be one sentence, plain English, no jargon-in-jargon

**When to use:** First time a non-obvious technical term appears on the page. Examples: vector, embedding, top-k, chunks, corpus, prompt, context window, tokens, KV cache.

**Don't:**
- Define a term in a separate glossary section. Inline only.
- Repeat the tooltip for every occurrence on the same page. Once is enough.

---

## Recipe 8 — Back Navigation Card

**Where it lives:** Bottom of every page, before the footer.
**What it does:** Returns the reader to the parent category page.

**Visual elements:**
- Single card, white background, hard black shadow
- Big red Fraunces arrow on the left
- "Back to category" hint above the category name
- Category name in bold Fraunces

**Key behaviors:**
- Hover: card lifts and shifts left slightly; arrow slides left a bit more
- Click: standard navigation

**When to use:** Every pattern page. Don't omit.

**Don't:** Add a "next pattern" link or a list of related patterns. That dilutes the close. The reader returns to the category page to choose what's next.

---

## Visual Style Constants

All recipes share these design choices:

- **Color palette:** warm cream paper (`#f7f3e9`), ink black (`#1a1816`), red accent (`#d94f3a`), green for success (`#4a7d4d`), yellow for highlights (`#e6b54a`)
- **Type:** Fraunces (serif, display + body emphasis), JetBrains Mono (technical), Inter (body sans where used)
- **Cards:** 2px ink border, 6px radius, hard 4px ink shadow that depresses on click
- **Buttons:** same border/shadow language, with yellow primary buttons for the main action in each section
- **Spacing:** generous. 32–56px section padding. The page should breathe.

When a new section type is needed, follow these constants so the page feels like one artifact.

---

## When You Need a Recipe That Doesn't Exist

If a pattern genuinely needs an interaction type not in this catalog:

1. First, ask whether one of the existing recipes can adapt. Most can.
2. If not, draft the new recipe and add it here before using it on a page. Recipe-first, page-second.
3. New recipes should share the visual style constants above.
4. New recipes should be reusable — if it's only useful for one pattern, it's probably not a recipe, it's a one-off.
