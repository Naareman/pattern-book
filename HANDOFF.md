# The Pattern Book: handoff and current state

Written 29 Aug 2026. Purpose: everything needed to pick this project up on a different laptop without losing context. If you read one thing, read "Where we stood" and "The one open blocker."

---

## 1. Start here on the new laptop

```bash
git clone https://github.com/Naareman/pattern-book.git
cd pattern-book
```

Nothing to install. No build step, no dependencies, no server needed. Every page is a self-contained static HTML file with inline CSS and JS. Open any file directly in a browser.

- GitHub: https://github.com/Naareman/pattern-book (public)
- Live site: https://naareman.github.io/pattern-book/ (auto-deploys from `main` via GitHub Pages)
- Local path on the old laptop: `/Users/nareman/Documents/Projects/pattern-book/`

Working tree is clean and `main` is in sync with `origin/main`, so the clone gets 100% of the work.

---

## 2. What the project is

An interactive, visual reference for AI system architecture patterns. Philosophy: **"operate, don't read."** Every page is something you DO, not a wall of text. It teaches AI patterns the way a children's museum teaches physics: drag, tap, step through, and the rule reveals itself.

Two audiences, one artifact:
- A **reference** for someone who knows what they want and needs the answer fast.
- A **teacher** for someone who does not know what they are looking for and learns by operating the page.

Two things it is NOT: a textbook (prose walls are failure) and a vendor blog (no marketing words, always state the tradeoff and pick a position).

**Three pillars, names locked:** Layers . Concerns . Builds.
- 10 layers, referenced on the site as plain stack numbers 1 to 10 plus the human name.
- 8 crosscuts (C8 Context Engineering added Aug 2026).
- Part II Recipes, Part III Anti-patterns, plus appendices.

**Design tokens, locked:** background `#FCFCFD`, text `#15171C`, accent `#7C5CF6`. Fonts: Fraunces (display), Inter (body), JetBrains Mono (code).

---

## 3. Where we stood

Last commit: `96009cf`, dated 4 Jun 2026 in git (the session notes were written 23 Aug 2026). Branch `main`, clean, pushed.

**Phase 1 is complete and shipped.** 307 HTML files total: 299 in `pages/`, plus 6 at the root (`index.html`, the four homepage prototypes, `prototypes.html`).

What Phase 1 delivered:

- **Taxonomy fully updated for the 2026 market.** `taxonomy.md` now covers: C8 Context Engineering as a new crosscut (Karpathy's four verbs Write, Select, Compress, Isolate); C2 Prompt Injection Defense expanded (Lethal Trifecta, Agents Rule of Two, CaMeL and Dual-LLM, Guardrail Layering, Sealed tools); C3 Agent Identity (Know Your Agent, verifiable credentials, Agent Cards, wallets, signed manifests, agent-as-principal); the Async and Background shape under L5 (sleep-time compute, speculative tools, scheduled tasks, background mode, the Ralph Pattern); the memory matrix (episodic, semantic, procedural, crossed with user, session, agent); CodeAct; Bounded Execution; Sub-Agent Delegation; MCP federation; Progressive Tool Disclosure; model-native reasoning under L4; GEPA and MIPROv2 under L-1; AI Gateway under L0; RLVR, GRPO, RFT and PRM; late chunking and GraphRAG; the spec-driven surface under L7.
- **10 senior-grade Recipes** with real production receipts and vendor source links, 5-axis tradeoff meters, decomposes-into chips, and decision logic versus alternatives: `build-llm-council`, `build-tool-using-agent`, `build-agentic-rag`, `build-deep-research-agent`, `build-agent-harness`, `build-spec-driven-workflow`, `build-supervisor-worker`, `build-multi-agent-debate`, `build-hierarchical-multi-agent`, `build-swarm`. (20 `build-*.html` files exist in total, counting the earlier wave.)
- **New navigation and appendix pages:** `use-cases.html` (57-row "I want to" cheat sheet across 10 sections), `builds.html` reworked as a curated landing, `anti-patterns.html` (Part III), `common-names.html` (popular name mapped to our slot, 35+ rows).
- **New hubs and cluster guides:** C8 Context Engineering (5 files), the C2 injection-defense sub-hub, the C3 agent-identity sub-hub, the Orchestration Background sub-hub, and Stateful Memory restructured into three memory types.

---

## 4. The one open blocker

**Pick the homepage.** Everything else is unblocked once this is decided.

Four prototypes are live and committed. Compare them at https://naareman.github.io/pattern-book/prototypes.html. Each carries a floating "Prototype X" badge that links back to the comparison index.

| File | What it does |
|---|---|
| `index-A.html` | Trust the map. No doors at all. The baseline. |
| `index-B.html` | Light inline nav: two small text links under the strapline. |
| `index-C.html` | Microcopy line: one italic editorial sentence with two inline links above the map. |
| `index-D.html` | Thin 36px persistent top strip with Build, Improve, Explore pills. |

Background: the earlier big two-door hero was rejected ("freaking awful"), so `index.html` was rolled back to its committed state and these four were built as alternatives instead. `index.html` is currently untouched by the prototypes.

Once one is picked: promote it to `index.html`, delete the other three plus `prototypes.html`, remove the prototype badge, commit, push.

---

## 5. How work is done on this project (standing rules)

These were learned through heavy iteration. They are not preferences to re-derive, they are settled.

**Conversational pace.** One paragraph per message, one thing at a time. No mega-tables or five-things-to-close per message unless synthesis was explicitly asked for. Reference docs like this one are the exception.

**No em dashes, ever.** Also no right or left arrows in prose. They read as AI-written. Use colons, periods, parentheses, semicolons, or commas. In diagrams and code blocks, plain ASCII arrows are fine.

**Always simplify.** Default to the simpler design, fewer elements, less text. If something feels technical or overwhelming, that is a failure. Aim for "normal human feeling."

**Always parallelize.** Split work into independent chunks and fan them out to parallel agents with no dependencies between them. Fix any shared spec or contract first, then launch the batch.

**Stay interactive, delegate the building.** The main thread should not go heads-down editing. Spin up background agents with precise specs, keep the main thread free to converse and catch feedback mid-stream. Agents edit only and report back. Commits and pushes happen centrally from the main thread, never from agents, to avoid push races.

**Agents over-build.** For detail pages, give subtract-only mandates: "simplify, remove, do NOT add."

**Concept quality gate.** After any new concept content, run the four questions: would a student understand it, can it be more interactive, has an AI expert checked for hallucination, and are there a few credible references. Then run the iterative four-reviewer loop (UI/UX, primary student, content writer, AI expert) and revise until genuinely solid. One build pass is never "done."

**Push often.** Commit and push after each real increment so context is never lost between sessions or machines.

---

## 6. The build checklist

The standing pre-ship gate lives in the repo at `page-build-checklist.md`. Every page and every building agent must pass all 8 groups. Point every agent at that file.

Reference pages, the calibration for everything else:
- `pages/layer-single-call-control.html` is THE reference for hubs.
- `pages/pattern-chain-of-thought.html` is THE reference for section and tradeoff styling.
- `example/rag.html` is the original calibrated example of "good."

The 8 groups in one line each:
1. **Matches the template.** Hubs match Single-Call Control's design, not just its structure. Hub shows the breakdown only. Demo and tradeoffs live on a separate `-how-it-works.html`. Canonical section titles. Tradeoff bars share one 170px left baseline. Cluster guide row count equals the parent hub's chip count, 1:1.
2. **Links never lie.** Link only if the target exists. Not built means not clickable and not styled clickable. A card "door" must never hijack a chip click.
3. **Plain words.** No taxonomy codes (L0, L3a, C3) or jargon sub-axis names in user-facing copy. 10-year-old plain. Goal first, not category lists. User-facing layer reference is the plain stack number 1 to 10 plus the human name; L-notation stays only in `taxonomy.md`.
4. **Short, and the best representation.** One-line intros. No prose walls: cut them or convert to a visual. One representation per idea. Ask on every block whether it could be visual or interactive instead.
5. **Interactive, tap not hover.** Everything works on tap and keyboard. The site is used on phones. Do not force a heavy demo where the concept does not warrant one.
6. **Accurate and complete.** AI-expert accuracy pass. Full site-wide cross-check against `taxonomy.md` (items keep getting silently dropped, so re-run the sweep). MECE. A few credible sources.
7. **Design system and accessibility.** Locked tokens only, self-contained inline CSS and JS, the three fonts, real headings, `:focus-visible`, `prefers-reduced-motion`, aria labels, skip link.
8. **The gate.** Four-reviewer loop until solid. Ask "can it be simpler, more interactive, more enjoyable" and act on it. Screenshot-verify against the reference before claiming done. Agents do not commit.

---

## 7. Roadmap: what is left

Agreed sequence, still valid.

**Phase 2 (next, the single highest-leverage upgrade): the Playbook layer.** A tap-to-expand block per leaf carrying production receipts, defaults, failure modes, vendor diff, decision logic, and quantified numbers where they are publicly sourceable.

**Phase 3: build the missing leaves.** Roughly 80 pages for the chips added in Phase 1 that do not yet have a page: Context Engineering, Injection Defense, Agent Identity, Async and Background, CodeAct, Sub-Agent Delegation, and the memory matrix cells.

**Phase 4: demo audit.** Tag every existing demo as "real teach" or "decorative," then replace the decorative ones.

**Phase 5: cleanup.** See the debt list in section 8.

**Phase 6: opinion essays.** 10 to 15 annual "State of X 2026" pieces, one per major cluster. This is the elegant way to add opinion without preaching.

**Phase 7: SEO and AEO.** Meta tags, Open Graph, JSON-LD, `sitemap.xml`, `robots.txt`.

**Phase 8: `whats-new.html`,** a public changelog on a quarterly cadence.

---

## 8. Known debt

**1. `pages/stateful-memory-episodic.html` was overwritten.** Confirmed today. A cluster-guide agent replaced the original leaf during commit `1621f95` (Wave 2). It now has cluster-guide headings ("This session," "Past sessions (per user)," "Agent run log") instead of the leaf's original ones ("A diary of...", "Tap a moment to see...", "How it sits next to...", "When to..."). The original is intact in git history. To recover:

```bash
git show f25b93d:pages/stateful-memory-episodic.html > pages/stateful-memory-episodic.html
```

Then decide whether the cluster-guide content deserves its own separate file, since `layer-stateful-memory.html` and `stateful-memory-what-remembers.html` both link here.

**2. iOS Safari blue-link issue on homepage pillar titles.** An agent was dispatched to fix this but the session ended before it finished. Unverified, likely still present. Needs `-webkit-text-fill-color` or an explicit anchor color reset.

**3. Not actually debt (correcting an earlier note).** `stateful-memory-what-remembers.html` and `stateful-memory-managing.html` were flagged as orphaned. They are not: verified today, they have 3 and 6 inbound links respectively. Leave them alone.

**4. Recipes still marked "coming soon"** in `pages/builds.html`. Several rows are placeholders awaiting a build.

---

## 9. Decisions that are locked (do not relitigate)

- A flat list of all techniques is a wall. Rejected.
- A table layout was tried and rejected: it recreates the disconnected goal-left, technique-far-right feel.
- Per-chip descriptions were tried and rejected ("doesn't look good").
- Goal-first beats method-first for comprehension, but the technique name must stay readable, not a tiny faint tag.
- Group labels must be plain words, never jargon sub-axis names like "Input Shape," and must not reuse a sibling stage's word.
- Use label plus indentation, not label plus a caption sub-line. Too much.
- Whole-card hover background wash washes out the chips. Use border and a slight lift only.
- The homepage pillar formerly called "Stack" is now **"Layers"** everywhere.
- No condescending negations like "(not different AIs)."
- **Numbers rule (agreed 23 Aug 2026):** include real cited numbers where a source exists, skip otherwise. Never fabricate. Only about 30 to 40% of leaves have publicly cited numbers.
- **Opinion mechanism (agreed):** italic "Default in 2026, updated <month>" callouts on recipes, plus dated annual essays, plus opinionated recipes. Elegant, not preachy.

---

## 10. Repo map

```
pattern-book/
  README.md                  what the project is, where to start
  guideline.md               how to write a pattern page (read the One Principle section)
  taxonomy.md                where every pattern lives, the internal filing system
  anchor-list.md             the ~25 patterns to write first, in priority order
  page-build-checklist.md    THE standing pre-ship gate
  CHANGELOG.md               dated change log
  HANDOFF.md                 this file
  index.html                 the live homepage
  index-A/B/C/D.html         the four homepage prototypes awaiting a pick
  prototypes.html            the prototype comparison index
  templates/                 pattern.md, section-recipes.md
  example/rag.html           the calibrated example of "good"
  pages/                     299 files: all hubs, cluster guides, leaves, recipes, appendices
  patterns/  layers/  recipes/  content/    earlier scaffolding, mostly superseded by pages/
```

Note that `patterns/`, `layers/` and `recipes/` are near-empty leftovers from the original scaffold. Real content lives in `pages/`.
