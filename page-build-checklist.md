# Page build + verification checklist

The standing gate. Every page, and every agent that builds or edits a page, must pass ALL of these before the page is "done." Compiled from Nareman's feedback. Reference pages: **layer-single-call-control.html** (hub model) and **pattern-chain-of-thought.html** (approved section/tradeoff reference).

## 1. Matches the template
- Layer hub shows the BREAKDOWN only: eyebrow pill -> title with a "See it in action ->" pill on the title row -> flow legend + caption -> stage cards directly. NO "01 Inside this layer" numbered label or serif heading before the cards.
- The interactive concept demo + tradeoffs live on a separate `<layer>-how-it-works.html` page, never on the hub.
- Section titles use ONE canonical wording site-wide: `See It Work`, `A Real Example` (atomic pages: `An Example`), `When It Matters`, idea h2 `What is it?`, and the tradeoffs h2 is `The price you pay.` everywhere it is the Low->High cost meter (leave a genuine comparison section alone).
- Tradeoff meter: every bar starts from the same left baseline (fixed 170px label column), monochrome accent, Low->High, stacks on mobile.

## 2. Links never lie
- Link a thing ONLY if its target page actually exists. Not built yet = not clickable AND must not look clickable (no accent color, no underline, default cursor).
- A whole-card "door" must ignore clicks on inner chips: clicking a chip must never silently navigate to the card's guide. A chip with its own page links to that page (stop propagation); a chip without a page does nothing.
- No dead links, no wrong destinations anywhere.

## 3. Plain words
- No internal taxonomy codes (L-1...L7, L3a, L3b, C1-C7) and no jargon sub-axis names ("Input Shape", "discipline crosscut") in any user-facing copy. A reader arriving from the homepage will not know them. Use plain language or the layer's human name.
- 10-year-old plain. Define any necessary term inline. No clever metaphors. No condescending negations like "(not different AIs)".
- No em dashes, ever. Use colons, periods, or parentheses.
- Goal/intent first ("Want to X? -> Technique"), not abstract category lists.

## 4. Short + best representation
- One-line intros. No explanatory paragraphs or prose walls. If a block is a wall, cut it or convert it to a visual (chips, small flow, before/after, tap-to-reveal).
- One representation per idea. MECE, no redundancy across sections.
- Always ask: is a list the best way to show this, or would a small visual / before-after / tap interaction land better?

## 5. Interactive, tap not hover
- At least one thing to operate where it genuinely helps. Do NOT force a heavy animated demo onto a concept that does not warrant one.
- Everything works on tap/click + keyboard. No hover-only reveals (the site is used on phones).

## 6. Accurate + complete
- AI-expert accuracy pass: claims are correct, not just plausible-sounding (no hallucination).
- Taxonomy completeness: every category/bullet for that layer/crosscut is present, nothing invented, nothing dropped. MECE.
- Include a few credible sources. A few is enough.

## 7. Design system + accessibility
- Locked palette tokens only. Self-contained inline CSS/JS. Fonts: Fraunces (display), Inter (body), JetBrains Mono (code).
- Real headings, `:focus-visible`, `prefers-reduced-motion`, aria labels, skip link.

## 8. The gate (before "done")
- Four-reviewer loop: UI/UX (consistent with the template?), content writer, primary student, AI-expert (accuracy + taxonomy completeness). Revise and re-review until genuinely solid.
- VERIFY before declaring done: screenshot the page against Single-Call / Chain-of-Thought and confirm it matches. Never claim "done" without showing the result.
- Agents do not git commit or push; commits are central.
