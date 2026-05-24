# V1 Anchor Patterns

> The ~25 patterns to write first. Listed in priority order with rationale. Each anchor pattern gets a full pattern page following the template; everything else can be added later as the project matures.

## Priority Logic

Three signals determine order:
1. **Most-searched** — patterns readers will look for first
2. **Format-proving** — patterns that validate the template against different shapes
3. **Foundational** — patterns other patterns refer to

The first five are non-negotiable. The rest are flexible.

---

## Tier 1 — Calibration Patterns

These prove the format works across different pattern shapes. Write these first.

### 1. RAG → L3a · Retrieval Strategies
**Status:** ✅ Done (see `example/rag.html`)
**Why first:** Most-searched. Complex enough to prove the template. Already exists as the calibrated example.

### 2. Chain-of-Thought → L4 · Cognitive Strategy (in-prompt)
**Why second:** Validates the template against a *simple* pattern. The diagram is tiny (USER → MODEL → USER with internal complexity). Section 02 will be sparse; Sections 03 and 04 carry more weight. Pick this to prove the template flexes.

### 3. LLM-as-Judge → L2 · Verify
**Why third:** Validates a Verify-axis pattern. Two-model setup. Tests how the diagram handles forking.

### 4. ReAct → L5 · Shape (Iterative Loops)
**Why fourth:** Validates the template against a *loop*. The diagram loops back on itself; the dossier has multiple turns. Hardest shape to fit cleanly. If ReAct works, anything works.

### 5. Tool Use → L3a · Knowledge tool invocation
**Why fifth:** Validates a pattern that hovers near the L3a/L5 boundary. Forces explicit handling of the read-only/side-effectful distinction.

---

## Tier 2 — High-Demand Patterns

These are widely used and frequently asked about. Write after Tier 1 is done.

### 6. Self-Consistency → L4 · Cognitive Strategy (in-decoding)
Demonstrates the same-name split between L4 and L2 Combine. Use as a teaching case for the appendix precedent.

### 7. Reflection / Self-Reflection → L4 · Self-Verification
The single-call version. Pair with #11 (a multi-call reflection variant) to show the L4/L2 boundary in action.

### 8. Reranking → L3a · Retrieval Strategies
The next step up from naive RAG. Reader who finished RAG will want this.

### 9. Router / Cascade → L2 · Select
The "cheap model first, escalate" pattern. Foundational for cost engineering.

### 10. Few-Shot Prompting → L4 · Input Shape
Foundational. Many other patterns assume the reader knows it.

### 11. Reflexion → L5 · Shape (Iterative Loops)
The multi-call reflection variant (vs #7's single-call). Shows the L4 vs L2/L5 boundary.

### 12. Structured Output → L4 · Output Shape & Decoding
Production essential. JSON mode, schema enforcement.

### 13. Speculative Decoding → L0 · Inference Serving
Filing precedent: an inference optimization, not a composition. Use as a teaching case.

### 14. Semantic Caching → L0 · Cache check & substitution + L−1 · Cache contents lifecycle
The classic same-pattern, two-homes case. Pair the two pages so the artifact/runtime split is visible.

### 15. Planner–Executor → L5 · Shape (Hierarchical)
The non-loop agentic pattern. Pairs with ReAct (#4) to show two distinct agent shapes.

---

## Tier 3 — Coverage Patterns

These round out the coverage. Less urgent but solid for completeness.

### 16. HyDE → L3a · Retrieval Strategies
Less common, but interesting failure-mode case.

### 17. Self-RAG → L3a · Retrieval Strategies
Model decides when to retrieve. Useful for cost discussions.

### 18. Tree-of-Thoughts → L4 · Cognitive Strategy (in-decoding)
Pairs with #6 to show the in-decoding sub-axis.

### 19. Ensembling → L2 · Combine
Multi-model voting. Pairs with #9 (Select) to show the L2 sub-axes.

### 20. Constrained Decoding → L4 · Output Shape & Decoding
Grammar-based output control. Pairs with #12.

### 21. Hybrid Search → L3a · Retrieval Strategies
Vector + keyword. Common production pattern.

### 22. Long-Term Memory → L3b
The whole L3b axis. Pick the cleanest mechanism (e.g., summarize-and-store).

### 23. LoRA / PEFT → L1 · Adaptation Methods
Training-time pattern. Validates the template against a training pattern (where the "code" is a config and the "dossier" is a before/after).

### 24. DPO / RLHF → L1 · Preference Optimization
Foundational alignment. Pair with #23 to show the L1 axis.

### 25. Fallback Chains → L0 · Fallback chains
Production essential. Cheap to write.

---

## After Tier 3

Variants and less-common patterns can fill in over time. The taxonomy has ~80 patterns total; the 25 anchors above cover the most-asked 80%.

## Writing Cadence

Write one anchor pattern fully (header → footer, all four tests passing) before starting the next. Resist the temptation to draft three at once — calibration drifts when pages are half-finished in parallel.

After every five anchor pages:
- Open the previous five back-to-back and check they feel like the same artifact
- Update `section-recipes.md` if a new recipe emerged
- Update `taxonomy.md` if a new filing precedent surfaced
