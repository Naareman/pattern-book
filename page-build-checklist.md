# Page build + verification checklist

The standing gate. Every page, and every agent that builds or edits a page, must pass ALL of these before the page is "done." Compiled from Nareman's feedback. Reference pages: **layer-single-call-control.html** (hub model) and **pattern-chain-of-thought.html** (approved section/tradeoff reference).

## 0. Guiding stance
- Operate, don't read: the page is something you DO, not a wall you read.
- Subtract first: default to removing before adding. Agents over-build. When in doubt, less and simpler.

## 1. Matches the template
- Every page at the same level as Single-Call Control (all layer hubs) must MATCH ITS DESIGN, not just its structure: same header, same "See it in action" pill, same flow legend, same stage cards, same spacing. Single-Call Control is THE reference for hubs; Chain-of-Thought is THE reference for section/tradeoff styling.
- Layer hub shows the BREAKDOWN only: eyebrow pill -> title with a "See it in action ->" pill on the title row -> flow legend + caption -> stage cards directly. NO "01 Inside this layer" numbered label or serif heading before the cards.
- Considerations follow the SAME 2-level model as layer hubs: the consideration hub is `consideration-<name>.html` (stage cards only), the demo + tradeoffs live on `consideration-<name>-how-it-works.html`. Reference pair: `layer-serving-infrastructure.html` + `serving-infrastructure-how-it-works.html`. Mirror their exact `.title-row` / `.how-pill` markup + CSS.
- Cluster guide row count == hub stage-card chip count, 1:1. A cluster guide (e.g. `consideration-evaluation-check.html`) has EXACTLY one "Want to X? -> Technique" row per chip on its parent hub's stage card, in the same order. Do NOT invent extra sub-clusters that re-frame the same leaf with multiple goals. If a chip is on the hub it has one row in the guide; if it is not on the hub it does not appear in the guide.
- The interactive concept demo + tradeoffs live on a separate `<layer>-how-it-works.html` page, never on the hub.
- Section titles use ONE canonical wording site-wide: `See It Work`, `A Real Example` (atomic pages: `An Example`), `When It Matters`, idea h2 `What is it?`, and the tradeoffs h2 is `The price you pay.` everywhere it is the Low->High cost meter (leave a genuine comparison section alone).
- Tradeoff meter: every bar starts from the same left baseline (fixed 170px label column), monochrome accent, Low->High, stacks on mobile.
- Cross-page consistency: a recurring element (section title, tradeoff meter, a block, a connector) must look and read IDENTICALLY on every page. Parallel agents drift, so always normalize back to the reference.
- Logical order: order items the way a person actually does them; a "check / review / verify" step comes last. Do not follow raw taxonomy order if it reads illogically.
- CSS hardening for stage cards (locked): grid `repeat(N, minmax(0, 1fr))`, `.stage-card { min-width: 0; height: 100%; }`, `.stages { align-items: stretch; }`, chip `white-space: normal; word-break: break-word; max-width: 100%;`. Without this, long chip names break the grid.
- Stage card shape (locked): small icon + cluster TITLE on its own full-width row + ONE short single-clause lead under the title (Single-Call's leads are the bar for tightness; rewrite the lead if it ever wraps to 3+ lines on a card) + the chips + "see how ->" pushed to the bottom.

## 2. Links never lie
- Link a thing ONLY if its target page actually exists. Not built yet = not clickable AND must not look clickable (no accent color, no underline, default cursor).
- A whole-card "door" must ignore clicks on inner chips: clicking a chip must never silently navigate to the card's guide. A chip with its own page links to that page (stop propagation); a chip without a page does nothing.
- No dead links, no wrong destinations anywhere.

## 3. Plain words
- No internal taxonomy codes (L-1...L7, L3a, L3b, C1-C7) and no jargon sub-axis names ("Input Shape", "discipline crosscut") in any user-facing copy. A reader arriving from the homepage will not know them. Use plain language or the layer's human name.
- DECIDED 2026-05-28: the user-facing layer reference is the plain stack number 1-10 (Data & artifacts = 1, Serving & infrastructure = 2, Training & adaptation = 3, Composition = 4, Stateless Retrieval = 5, Stateful Memory = 6, Single-call control = 7, Orchestration & agents = 8, Application patterns = 9, User experience = 10) plus the human name. Use it IDENTICALLY on the homepage list and on every layer page eyebrow, breadcrumb, and title tag. The L-notation (L-1...L7, L3a/L3b) lives ONLY in taxonomy.md as the internal filing system and never appears on a page. The L3a/L3b pairing is conveyed by grouping/wording, not codes.
- 10-year-old plain. Define any necessary term inline. No clever metaphors. No condescending negations like "(not different AIs)".
- No em dashes, ever. Use colons, periods, or parentheses.
- Goal/intent first ("Want to X? -> Technique"), not abstract category lists.
- No unexplained jargon or bare acronyms (RLHF, ReAct, zero/few-shot). Spell out and define on first use, or cut. If a beginner would ask "what's that?", it is not done.
- Distinct labels: never reuse a sibling's word (e.g. a cluster "Shape the input" next to a stage "Shape its output").
- Chip names on hub cards are PLAIN. Rewrite jargon discipline names from the taxonomy into homepage-reader English (e.g. "SLA/SLO Design" -> "Setting speed and uptime targets"; "Error Budgets & Graceful Degradation" -> "How much can break before you act"; "Cost-Quality Tradeoff" -> "Spending more for better answers"; "Context-Strategy Tradeoff" -> "Choosing what to pack into the prompt"). Keep the rename truthful; no invented disciplines.

## 4. Short + best representation
- One-line intros. No explanatory paragraphs or prose walls. If a block is a wall, cut it or convert it to a visual (chips, small flow, before/after, tap-to-reveal).
- One representation per idea. MECE, no redundancy across sections.
- Always ask: is a list the best way to show this, or would a small visual / before-after / tap interaction land better?
- Can this be visual? Show, don't tell: add a small icon, diagram, or before/after, and make it interactive (a tap/toggle) wherever that carries the idea better than a sentence. Ask it on every block.
- Strong lead copy: if an intro line or title reads flat or weak, rewrite it stronger or remove it. No filler openers.
- No duplication: do not repeat what already lives on the homepage or in another section. Link to it or cut it.

## 5. Interactive, tap not hover
- At least one thing to operate where it genuinely helps. Do NOT force a heavy animated demo onto a concept that does not warrant one.
- Everything works on tap/click + keyboard. No hover-only reveals (the site is used on phones).
- Phone-first reach: every control is visible and tappable on a small screen. No tiny text, no buttons placed so far away you would have to zoom out to find them.

## 6. Accurate + complete
- AI-expert accuracy pass: claims are correct, not just plausible-sounding (no hallucination).
- Taxonomy completeness: every category/bullet for that layer/crosscut is present, nothing invented, nothing dropped.
- Cross-check against `taxonomy.md`, both for THIS page and across the WHOLE site: every layer, sub-axis, category, crosscut, and recipe in the taxonomy has a real page/section and is actually built. We keep silently dropping items (6 layers were missed once), so re-run this sweep and never assume it is done.
- MECE: the breakdown is mutually exclusive (items/sections do not overlap or repeat) AND collectively exhaustive (covers everything in the taxonomy, no gaps).
- Include a few credible sources. A few is enough.

## 7. Design system + accessibility
- Locked palette tokens only. Self-contained inline CSS/JS. Fonts: Fraunces (display), Inter (body), JetBrains Mono (code).
- Real headings, `:focus-visible`, `prefers-reduced-motion`, aria labels, skip link.

## 8. The gate (before "done")
- Four-reviewer loop: UI/UX (consistent with the template?), content writer, primary student, AI-expert (accuracy + taxonomy completeness). Revise and re-review until genuinely solid.
- Simplify + delight check: can it be simpler (less text, fewer elements)? Can it be more interactive / more enjoyable? If yes, do it before shipping.
- VERIFY before declaring done: screenshot the page against Single-Call / Chain-of-Thought and confirm it matches. Never claim "done" without showing the result.
- Agents do not git commit or push; commits are central.
