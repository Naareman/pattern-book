# Layered Taxonomy (canonical)

> The canonical taxonomy for The Pattern Book. Every layer states its axis. Every layer states a filing rule. Each *mechanism* has exactly one home in Part I; named patterns that bundle multiple mechanisms are decomposed and filed per-mechanism, with the bundle cross-referenced. Cross-layer product shapes live in Part II as Recipes.
>
> Source of truth: Notion — https://www.notion.so/Layered-Taxonomy-369df9b0d69381bbbb27cdfdf58e1692 . When in conflict, Notion wins. This file mirrors the Notion version for Claude Code and contributors without Notion access.

## Three Rules

**Rule 1 — One axis per layer.** Every layer chapter opens with: *"Axis: [the one dimension this layer slices on]."* Categories partition that axis and nothing else.

**Rule 2 — Disciplines vs Mechanisms.** Crosscuts (C1–C7) own *disciplines* (frameworks for reasoning about a concern). Layers (L−1 to L7) own *mechanisms* (specific patterns that do the thing). Mechanisms live in layers and are cross-referenced from disciplines.

**Rule 3 — Known fragile boundaries.** Three boundaries are correctly drawn but most likely to be broken, because the axis the document slices on isn't the axis a reader naturally thinks in.

- **L4 Self-Verification vs L2 Verify** — split by call count (one = L4, two = L2). Reader intuition is "verification strategy"; the axis is implementation count.
- **L6 primary purpose, cross-reference secondary** — L6 is MECE in principle, fuzzy in application. Primary-purpose filing is a judgment, not a derivation.
- **L0 runtime vs L−1 artifact** — every artifact the runtime reads (prompt, model, eval suite, index, cache contents) has its lifecycle in L−1 and its consumption in L0. Consistent but cognitively expensive: "caching" must be looked up in two places.

These are the cost of choosing axis-purity over reader intuition.

**Scope of crosscuts.** Each crosscut declares scope with an *Applies to:* header. C1–C5 apply to every AI system. C6 (Multimodal) and C7 (Real-time / Streaming) apply only when the system has a specific property that changes how every layer behaves. Scope is declared per-crosscut, not a structural tier.

**Counts:** Layers: 10 (L−1, L0, L1, L2, L3a, L3b, L4, L5, L6, L7). Crosscuts: 7 (C1–C7; C6, C7 conditionally scoped). Categories: ~65 across Part I. Recipes (non-taxonomy): ~10.

*(The previous Crosscut/Lens two-tier structure was retired. "Lenses" added a structural asymmetry that wasn't paying its weight; the only difference was scope, now declared per-crosscut via "Applies to:". Multimodal and Real-time / Streaming are C6 and C7, peers with declared conditional scope.)*

---

## Part I — The Layered Taxonomy

### L7 · User Surface & Agency
**Axis: UX function — how the user experiences and controls the AI.**

Categories: Conversational surfaces · Inline / embedded surfaces · Background / agentic surfaces · Streaming & progressive disclosure (UX mechanism; the real-time/streaming *discipline* is C7) · Handoff & escalation · Approval gates · Autonomy levels & guardrails · Feedback capture surfaces · Clarification & disambiguation surfaces (UX of asking a follow-up; the *control-flow shape* of the clarification loop is L5 Shape Conditional Branching).

Filing rule: if the user interacts with it directly, it's L7. If the system uses it without the user seeing, it's not.

Multimodal: voice surfaces, vision-in / vision-out, mixed-modality handoff (mechanisms here; the discipline of choosing across modalities is C6).

### L6 · Application Patterns
**Axis: the relationship between input and output.**

- Retrieval & Q&A — returns existing content located via the input
- Transformation — output preserves input semantics in altered form
- Extraction & Classification — output is structured metadata derived from the input
- Generation — output is new content not derivable from the input alone
- Decision & Recommendation — output selects, ranks, or scores from a set
- Automation & Workflow Execution — output is the execution of a multi-step procedure

Filing rule: ask "what transformation does this perform on the input to produce the output?" That determines the primary category; colloquial framing is ignored. When a task combines categories, file under primary and cross-reference secondary. Composed L6 products are Recipes, not new categories. L6 is MECE in principle, fuzzy in application.

Multimodal: image generation, voice generation, document parsing as L6 mechanisms. Modality selection (OCR-then-reason vs vision-native) is C6.

### L5 · Orchestration
**Axis: how a system's calls and actions are organized across time, the world, and other actors.** Sub-axes: Shape, Action, Coordination.

**Shape** (structure of execution across calls): Single-call patterns · Sequential pipelines · Parallel / fan-out / fan-in · Iterative loops (ReAct shape, reflection loops) · Router / dispatcher patterns · Hierarchical planning (Planner–Executor, plan-of-plans) · Conditional branching.

**Action** (state-changing operations): External Action (state-changing tool invocation) · Idempotency & action verification · Compensating actions & rollback · Approval gates (mechanism; L7 owns the UX) · Side-effect sandboxing · Multi-step action transactions.

**Coordination** (multi-actor protocols): Agent-to-agent protocols (MCP, A2A, message-passing) · Shared workspace / blackboard · Agent identity & auth · Capability negotiation & tool discovery.

Filing rule: Shape = "in what order/structure do calls execute?" Action = "what does the system do to outside state?" Coordination = "how do multiple actors communicate?" ReAct: the loop is Shape; the tool calls inside are Action. Multimodal: multimodal tool I/O (screenshot-in / click-out) as Action; cross-modal action reasoning is C6.

### L4 · Single-Call Control
**Axis: call-time stage — what you shape at which point inside one model call.** Sub-axes: Input Shape, Cognitive Strategy, Self-Verification, Output Shape & Decoding.

**Input Shape**: zero-shot / few-shot framing · role / persona · delimiters · negative prompting · meta-prompting · prompt templates · example selection · example ordering · context packing · lost-in-the-middle mitigation · prompt compression.

**Cognitive Strategy** (sliced by where it executes):
- In-prompt (authored as text the model follows): Step-Back, Least-to-Most, Plan-and-Solve, Analogical Prompting, Chain-of-Thought (prompt-invoked).
- In-decoding (search over generations): Tree-of-Thoughts, Skeleton-of-Thought, Self-Consistency-as-search.

**Self-Verification within the call**: Self-Reflection, Chain-of-Verification, Self-Ask, single-model debate-then-decide.
Filing rule: verification inside one call to the same model = L4. Separate verifier call or different model = L2 Verify.

**Output Shape & Decoding**: structured output · schema enforcement · constrained decoding · streaming-with-validation · format coercion · temperature & sampling · logit biasing · stop sequences · beam search.

Filing rule: if it happens inside one call to one model, it's L4. A second call or a tool = L5. A second model = L2.
Self-Consistency: L4 in-decoding when N samples come from same model + same prompt; L2 Combine (Ensembling) when different models or prompts.

### L3b · Stateful Memory
**Axis: how persistent knowledge accumulates and is recalled across sessions.**

Categories: Conversation memory (rolling window, summarized history) · Long-term user memory (preferences, prior facts) · Episodic memory (specific past events) · Memory consolidation (compress / promote over time) · Memory retrieval triggers (when to surface a memory) · Memory eviction & forgetting · Cross-session identity & continuity.

Filing rule: persists across requests/sessions and keyed to an entity → L3b. Loaded fresh per request → L3a.

### L3a · Stateless Retrieval
**Axis: how external information enters a single call from a corpus.**

Reader trap: the L3a vs L5 Action axis is read-only vs side-effectful, not who does the work. A live web search by an agent is L3a (read-only).

Categories: Indexing & embedding · Retrieval (dense, sparse, hybrid) · Reranking · Context assembly · Knowledge tool invocation (read-only) · Caching of knowledge results · Freshness & invalidation · Citation & provenance.

Filing rule: SELECT / file read / sandboxed pure-functional code / read-only scraping → L3a. INSERT/UPDATE/DELETE, browser clicks/submits, side-effectful code → L5 Action. Live web search inside an agent → L3a.
Sidebar — Long-Context vs RAG: when the corpus fits the context window, the L3a stack collapses into L4 Input Shape (context packing). The tradeoff framework (cost/latency/recall/precision) is a C5 discipline.

Multimodal: multimodal embeddings, image/audio retrieval, cross-modal search as L3a; cross-modal grounding is C6.

### L2 · Structural Composition
**Axis: how multiple potential model calls are organized into a single answering act.** Sub-axes: Combine, Select, Verify.

**Combine** (multiple outputs fuse): Ensembling (vote, average, blend) · Decomposition & recomposition.
**Select** (one output chosen from candidates): Cascading (cheap→expensive) · Confidence-based routing · Specialist-of-experts routing.
**Verify** (one output gated/scored/revised by separate judgment): Verification composition (LLM-as-Judge ⭐) · Generator–critic pairs · Output filtering by classifier.

Filing rule: fused from multiple outputs = Combine; chosen from candidates = Select; gated by separate judgment = Verify. Self-verification inside one call is L4, not L2 Verify.
Speculative decoding = L0 (no semantic composition). Caching splits: runtime check & substitution = L0; cache contents & lifecycle = L−1.

### L1 · Model Training & Adaptation
**Axis: training lifecycle stage.**

Categories: Pretraining objectives & data · Continued pretraining · Supervised fine-tuning (SFT) · Preference optimization (RLHF, DPO, Constitutional AI / RLAIF) · Reasoning-capability training (RL on reasoning traces, reasoning-trace distillation, process-reward training) · PEFT (LoRA, QLoRA, adapters, prefix tuning) · Distillation · Model merging · Evaluation during training (checkpoint selection, dev-set hygiene).

Filing rule: what stage of the training lifecycle? If the purpose is to create native reasoning capability in weights, it's Reasoning-capability training regardless of mechanism shape.
Multimodal: vision/audio encoders, fusion architectures (late vs early) as L1; fusion strategy thinking is C6.

### L0 · Serving Infrastructure
**Axis: infrastructure function — what the runtime provides.**

Categories: Inference serving (batching, KV-cache, paged attention, speculative decoding) · Routing & load balancing · Fallback chains · Circuit breakers & retry policies · Cache check & substitution (prompt-cache prefix + semantic-cache similarity; the runtime mechanism — contents/lifecycle are L−1) · Cost/latency-optimized model selection at runtime (applies C5 frameworks) · Observability hooks (emits traces; the discipline is C4) · Quantization & compression at serving time.

Filing rule: operates on the model call as a black box → L0. Operates on what the model says or which model speaks → L2. Lifecycle of an artifact the runtime reads → L−1.

### L−1 · Data & Artifact Lifecycle
**Axis: lifecycle stage of the artifacts that feed and define the system.** Sub-axes: Data, Deployable Artifacts.

**Data** (consumed by L1 training or L3a/L3b retrieval): Collection & sourcing · Labeling & annotation · Data quality & deduplication · Synthetic data generation · Versioning & lineage · PII scrubbing & redaction · Retention & deletion policies · Feedback data pipelines.
**Deployable Artifacts** (configs that define runtime behavior): Prompt versioning · Model versioning · Eval-suite versioning · Retrieval-index versioning · Cache-contents lifecycle (population, invalidation, eviction, schema versioning) · Artifact-lineage tracking (cross-ref C4) · Rollback & roll-forward.

Filing rule: data consumed by training/retrieval → L−1 Data. Config defining runtime behavior → L−1 Deployable Artifacts. The runtime reads the artifact (L0 consumer); the artifact's lifecycle is L−1.

---

## Crosscuts (C1–C7)
Every crosscut is a discipline, not a mechanism. Mechanisms live in layers and are cross-referenced.

### C1 · Evaluation — *Applies to: every AI system.*
**Axis: how to judge whether the system produces the right output or trajectory.**
Eval methodology (benchmarks, golden sets, human eval, rubrics) · Agent trajectory evaluation · Regression testing · Eval-driven development · Statistical rigor · Prompt lifecycle thinking · Faithfulness & groundedness evaluation · Hallucination detection methodology (claim extraction L4; verifier scoring L2; attribution L3a; framework C1).

### C2 · Safety, Alignment & Refusal — *Applies to: every AI system.*
**Axis: keeping AI behavior within intended bounds.**
Refusal calibration · Harm taxonomies & severity reasoning · Red-teaming methodology · Jailbreak-resistance · Dual-use review · Refusal-vs-helpfulness tradeoff · Principle-based behavioral evaluation.

### C3 · Privacy, Security & Governance — *Applies to: every system handling user/customer/tenant data.*
**Axis: protecting data, users, and tenants.**
Access control · Tenant isolation discipline · Audit-logging · Compliance mapping (GDPR, HIPAA, SOC2) · Data residency · Runtime PII-handling · Provenance & watermarking.

### C4 · Observability & Debuggability — *Applies to: every system in production.*
**Axis: how to explain what the system did after the fact.**
Tracing discipline · Replay frameworks · Attribution thinking · Drift detection methodology · Incident postmortem patterns · Causal analysis frameworks.

### C5 · Cost, Latency & Reliability — *Applies to: every system with cost/latency/reliability constraints.*
**Axis: engineering AI systems against budgets and SLAs.**
Cost attribution · Cost-quality tradeoff · Latency budgets (perceived vs actual, tail) · Latency-quality tradeoff · Context-strategy tradeoff (RAG vs long-context vs hybrid; cross-ref L3a) · SLA/SLO design · Error budgets & graceful degradation · Incident response.

### C6 · Multimodal — *Applies to: systems with non-text modalities (text-only can skip).*
**Axis: reasoning about systems spanning text, vision, audio, or mixed.**
Modality selection · Cross-modal grounding (cross-ref C1) · Fusion strategy (early vs late; cross-ref L1) · Modality-specific eval · Latency budgets across modalities (cross-ref C5, L0) · Modality-specific safety & refusal (cross-ref C2) · Multimodal cost attribution (cross-ref C5) · Mixed-modality handoff thinking (mechanism is L7).

### C7 · Real-time / Streaming — *Applies to: systems with sub-second incremental output (batch can skip).*
**Axis: reasoning where time-to-first-token and continuous output matter more than throughput.**
Streaming output discipline (cross-ref L7) · Time-to-first-token frameworks (cross-ref C5) · Streaming eval (cross-ref C1) · Streaming safety (cross-ref C2) · Streaming validation (partial JSON; cross-ref L4 Output Shape) · Backpressure & cancellation · Streaming cost attribution · Continuous-context for voice (turn-taking, interruption, barge-in).

---

## Part II — Recipes
Recipes overlap by design; not part of the MECE Part I taxonomy. A recipe is a named composition of Part I patterns, with sequencing and tradeoff notes. Recipes never define new categories.

Recipes to write: Conversational Assistant · Document Q&A / Knowledge Chatbot · Coding Agent · Research Agent · Browsing Agent · Customer Support Agent · Reasoning System with Code Execution (PAL / Program-of-Thought) · Summarization Service · Voice Assistant · Multi-Agent Research Team.

---

## Appendix — Edge Cases & Filing Precedents
Format: Pattern → home layer + the principle that decided it.

### Sub-axis precedents within layers
- L2 Combine vs Select vs Verify — fused / chosen-from-candidates / gated-by-separate-judgment.
- L4 four sub-axes — Input Shape / Cognitive Strategy / Self-Verification / Output Shape & Decoding (decided by where in the single call it shapes behavior).
- L4 Cognitive Strategy in-prompt vs in-decoding — authored as prompt text vs search procedure over generations.
- L5 Shape vs Action vs Coordination — execution structure / change to the world / communication with another actor.
- L−1 Data vs Deployable Artifacts — consumed by training/retrieval vs read as runtime config.

### Same-name patterns with split homes
- Self-critique / Constitutional self-revision → L4 if one call; L2 Verify if a separate verifier call.
- Self-Consistency → L4 in-decoding if same model+prompt; L2 Combine if different models/prompts.
- Approval gates → L7 (UX) and L5 Action (runtime mechanism).
- Hallucination detection → L4 (claim extraction), L2 Verify (scoring), L3a (citation/provenance); methodology is C1.
- Clarification & disambiguation → L7 (UX) and L5 Shape (clarification-loop control flow).

### Patterns frequently mis-filed
- ReAct → L5 Shape (Iterative Loops); tool invocation inside is L5 Action.
- Speculative decoding → L0 Inference Serving (not L2).
- Live web search inside an agent → L3a (read-only).
- Code execution → default L5 Action; L3a only if sandboxed and pure-functional.
- Browser actions → L3a for read-only scraping; L5 Action for clicks/submits.
- Database ops → L3a for SELECT; L5 Action for INSERT/UPDATE/DELETE.
- Semantic caching & prompt caching → runtime check/substitution L0; contents & lifecycle L−1.
- PAL / Program-of-Thought → Recipe (L4 Cognitive Strategy + L3a sandboxed execution + L5 Shape).
- Long-context vs RAG decision → C5 framework (mechanisms are L3a and L4 Input Shape).
- Reasoning-capability training → L1 category, regardless of mechanism shape.
- Prompt / model / eval-suite versioning → L−1 Deployable Artifacts (not L0).

### Discipline-vs-mechanism precedents
- Prompt lifecycle → C1 (thinking) + L−1 (versioning mechanism).
- Tenant isolation → C3 (discipline) + L0/L3a (mechanisms).
- Observability → C4 (discipline) + L0 (hooks).
- Cost attribution → C5 (framework) fed by L0 observability hooks.
- Refusal calibration → C2 (framework) + L4 (output shaping) + L7 (refusal UX).

### Known fragile boundaries
- L4 Self-Verification vs L2 Verify — boundary is call count (1 = L4, 2 = L2). Watch for L4 patterns with two calls or L2 Verify patterns inside one call.
- L6 multi-category tasks — "primary purpose, cross-reference secondary" is a judgment; resolve by explicit precedent.
- L0 runtime vs L−1 artifact split — artifact lifecycle → L−1; runtime read → L0; both exist for every artifact the runtime reads.

### Provisional patterns
Holding state, not permanent. Time-bounded (decide-by date), capped at 5, cited (candidate homes + what makes it hard), resolved into the appendix when decided. *(Currently empty.)*

---

## Final Slicing Axes
- L7 — UX function
- L6 — input → output relationship
- L5 — orchestration across time, world, actors (Shape, Action, Coordination)
- L4 — call-time stage inside one model call (Input Shape, Cognitive Strategy, Self-Verification, Output Shape & Decoding)
- L3b — persistent knowledge across sessions
- L3a — external information into one call
- L2 — organizing multiple potential calls into one answering act (Combine, Select, Verify)
- L1 — training lifecycle stage
- L0 — runtime infrastructure function
- L−1 — lifecycle of data and deployable artifacts (Data, Deployable Artifacts)
- C1 — judging output/trajectory quality
- C2 — keeping behavior within bounds
- C3 — protecting data, users, tenants
- C4 — explaining behavior after the fact
- C5 — engineering against budgets and SLAs
- C6 — reasoning across modalities (multimodal systems only)
- C7 — reasoning about real-time / streaming systems (streaming systems only)
