# Layered Taxonomy

> Snapshot of the canonical taxonomy. The live version lives in Notion. When in conflict, Notion wins. This file is for Claude Code and contributors who don't have Notion access.

Each *mechanism* has exactly one home in Part I. Named patterns that bundle multiple mechanisms are decomposed and filed per-mechanism, with the bundle cross-referenced. Cross-layer product shapes live in Part II as Recipes.

## Three Rules

**Rule 1 — One axis per layer.** Every layer chapter opens with "Axis: [the one dimension this layer slices on]." Categories partition that axis and nothing else.

**Rule 2 — Disciplines vs Mechanisms.** Crosscuts (C1–C7) own *disciplines* — frameworks for reasoning. Layers (L−1 to L7) own *mechanisms* — specific patterns that do the thing. Mechanisms live in layers; crosscuts cross-reference them.

**Rule 3 — Known fragile boundaries.** Three boundaries are correctly drawn but most likely to be broken by future contributors:

- **L4 Self-Verification vs L2 Verify** — split by call count (one = L4, two = L2). Reader intuition is "verification strategy"; the axis is implementation count.
- **L6 primary purpose, cross-reference secondary** — L6 is MECE in principle, fuzzy in application. Real tasks frequently span categories; primary-purpose filing is a judgment.
- **L0 runtime vs L−1 artifact** — every artifact the runtime reads (prompt, model, eval suite, index, cache contents) has its lifecycle in L−1 and its consumption in L0. Cognitively expensive but consistent.

These boundaries are the cost of choosing axis-purity over reader intuition.

## Scope of Crosscuts

Each crosscut declares its scope with an "Applies to:" header. C1–C5 apply to every AI system. C6 (Multimodal) and C7 (Real-time / Streaming) apply only when the system has a specific property that changes how every layer behaves.

---

## Layers

**Total: 10 layers (L−1, L0, L1, L2, L3a, L3b, L4, L5, L6, L7).**

### L7 · User Surface & Agency
*Axis: UX function — how the user experiences and controls the AI.*

Categories: Conversational surfaces · Inline/embedded surfaces · Background/agentic surfaces · Streaming & progressive disclosure (the UX mechanism; the real-time/streaming discipline is C7) · Handoff & escalation · Approval gates · Autonomy levels & guardrails · Feedback capture surfaces · Clarification & disambiguation surfaces

### L6 · Application Patterns
*Axis: the relationship between input and output.*

Categories: Retrieval & Q&A · Transformation · Extraction & Classification · Generation · Decision & Recommendation · Automation & Workflow Execution

Filing rule: ask "what transformation does this perform on the input to produce the output?" Primary category. When a task combines multiple L6 categories, file under primary and cross-reference secondary. **L6 is MECE in principle, fuzzy in application.**

### L5 · Orchestration
*Axis: how a system's calls and actions are organized across time, the world, and other actors.* Three sub-axes: Shape, Action, Coordination.

**Shape** — execution structure across calls: single-call, sequential pipelines, parallel/fan-out, iterative loops (ReAct shape, reflection), router/dispatcher, hierarchical planning (Planner–Executor).

**Action** — state-changing operations: external action tool invocation, idempotency, compensating actions, approval gates (the mechanism; L7 owns the UX), side-effect sandboxing, multi-step transactions.

**Coordination** — multi-actor protocols: agent-to-agent protocols (MCP, A2A), shared workspace, agent identity & auth, capability negotiation & tool discovery.

### L4 · Single-Call Control
*Axis: call-time stage — what you shape at which point inside one model call.* Four sub-axes: Input Shape, Cognitive Strategy, Self-Verification, Output Shape & Decoding.

**Input Shape**: zero-shot/few-shot, role/persona, delimiters, negative prompting, meta-prompting, prompt templates, example selection/ordering, context packing, lost-in-the-middle mitigation, prompt compression.

**Cognitive Strategy** — sub-sliced by where the strategy executes:
- *In-prompt:* Step-Back, Least-to-Most, Plan-and-Solve, Analogical Prompting, Chain-of-Thought.
- *In-decoding:* Tree-of-Thoughts, Skeleton-of-Thought, Self-Consistency-as-search.

**Self-Verification within the call**: Self-Reflection, Chain-of-Verification, Self-Ask, single-model debate-then-decide.

**Self-Verification filing rule:** verification inside one call to the same model = L4. Separate verifier call or different model = L2 Verify.

**Output Shape & Decoding**: structured output, schema enforcement, constrained decoding, streaming-with-validation, format coercion, temperature & sampling, logit biasing, stop sequences, beam search.

### L3b · Stateful Memory
*Axis: how persistent knowledge accumulates and is recalled across sessions.*

Categories: Conversation memory · Long-term user memory · Episodic memory · Memory consolidation · Memory retrieval triggers · Memory eviction & forgetting · Cross-session identity & continuity.

Filing rule: knowledge persists *across* requests/sessions and is keyed to an entity → L3b. Loaded fresh per request → L3a.

### L3a · Stateless Retrieval
*Axis: how external information enters a single call from a corpus.*

Reader trap: the axis for L3a vs L5 Action is **read-only vs side-effectful**, not who is doing the work. Agents do L3a operations all the time.

Categories: Indexing & embedding · Retrieval (dense, sparse, hybrid) — RAG · Reranking · Context assembly · Knowledge tool invocation (read-only) · Caching of knowledge results · Freshness & invalidation · Citation & provenance.

Filing rule (read-only vs side-effectful):
- `SELECT`, file read, sandboxed pure-functional code, read-only scraping → L3a.
- `INSERT/UPDATE/DELETE`, browser clicks/submits, side-effectful code → L5 Action.
- Live web search inside an agent → L3a (read-only).

### L2 · Structural Composition
*Axis: how multiple potential model calls are organized into a single answering act.* Three sub-axes: Combine, Select, Verify.

**Combine** — multiple outputs fuse into the final answer: Ensembling, Decomposition & recomposition.

**Select** — one output chosen from many candidates: Cascading, Confidence-based routing, Specialist-of-experts routing.

**Verify** — one output gated, scored, or revised by a separate judgment: Verification composition (LLM-as-Judge), Generator–critic pairs, Output filtering by classifier.

### L1 · Model Training & Adaptation
*Axis: training lifecycle stage.*

Categories: Pretraining objectives & data · Continued pretraining · SFT · Preference optimization (RLHF, DPO, Constitutional AI / RLAIF) · Reasoning-capability training · PEFT (LoRA, QLoRA, adapters) · Distillation · Model merging · Evaluation during training.

### L0 · Serving Infrastructure
*Axis: infrastructure function — what the runtime provides between caller and model.*

Categories: Inference serving (batching, KV-cache, paged attention, speculative decoding) · Routing & load balancing · Fallback chains · Circuit breakers & retry policies · Cache check & substitution (prompt-cache and semantic-cache runtime mechanism; contents and lifecycle are L−1) · Cost/latency-optimized model selection at runtime · Observability hooks · Quantization & compression at serving time.

Filing rule: operates on the model call as a black box → L0. Operates on what the model says or which model speaks semantically → L2. Lifecycle of an artifact the runtime reads → L−1.

### L−1 · Data & Artifact Lifecycle
*Axis: lifecycle stage of the artifacts that feed and define the system.* Two sub-axes: Data, Deployable Artifacts.

**Data** — datasets consumed by training or retrieval: Collection · Labeling · Quality & deduplication · Synthetic generation · Versioning & lineage · PII scrubbing & redaction · Retention & deletion · Feedback data pipelines.

**Deployable Artifacts** — prompts, model versions, eval suites, retrieval indices, cache contents as releasable artifacts: Prompt versioning · Model versioning · Eval-suite versioning · Retrieval-index versioning · Cache-contents lifecycle · Artifact-lineage tracking · Rollback & roll-forward.

---

## Crosscuts (C1–C7)

Each is a discipline (a way of reasoning), not a mechanism.

### C1 · Evaluation
*Applies to: every AI system.*
Eval methodology · Agent trajectory evaluation · Regression testing · Eval-driven development · Statistical rigor · Prompt lifecycle thinking · Hallucination detection methodology.

### C2 · Safety, Alignment & Refusal
*Applies to: every AI system.*
Refusal calibration · Harm taxonomies · Red-teaming · Jailbreak-resistance · Dual-use review · Refusal-vs-helpfulness tradeoff · Principle-based behavioral evaluation.

### C3 · Privacy, Security & Governance
*Applies to: every AI system that handles user, customer, or tenant data.*
Access control · Tenant isolation discipline · Audit-logging · Compliance mapping (GDPR, HIPAA, SOC2) · Data residency · Runtime PII-handling · Provenance & watermarking.

### C4 · Observability & Debuggability
*Applies to: every AI system in production.*
Tracing discipline · Replay frameworks · Attribution thinking · Drift detection methodology · Incident postmortem patterns · Causal analysis frameworks.

### C5 · Cost, Latency & Reliability
*Applies to: every AI system with cost, latency, or reliability constraints.*
Cost attribution · Cost-quality tradeoff · Latency budgets · Latency-quality tradeoff · Context-strategy tradeoff (RAG vs long-context vs hybrid) · SLA/SLO design · Error budgets · Incident response.

### C6 · Multimodal
*Applies to: systems that handle non-text modalities (vision, audio, or mixed).*
Modality selection thinking · Cross-modal grounding discipline · Fusion strategy thinking · Mixed-modality handoff thinking · Modality-specific eval/safety/cost frameworks.

### C7 · Real-time / Streaming
*Applies to: systems that produce or consume output incrementally with sub-second latency expectations.*
Streaming output discipline · Time-to-first-token frameworks · Streaming eval · Streaming safety · Streaming validation thinking · Backpressure & cancellation · Streaming cost attribution · Continuous-context disciplines for voice.

---

## Part II — Recipes

Recipes are documentation, not taxonomy. A recipe is a named composition of Part I patterns. Recipes overlap by design.

Planned recipes: Conversational Assistant · Document Q&A · Coding Agent · Research Agent · Browsing Agent · Customer Support Agent · Reasoning System with Code Execution (PAL) · Summarization Service · Voice Assistant · Multi-Agent Research Team.

---

## Edge Cases & Filing Precedents

(Abbreviated. Full appendix is in Notion.)

### Same-name patterns with split homes
- **Self-critique / Constitutional self-revision** → L4 if one call; L2 Verify if separate call.
- **Self-Consistency** → L4 in-decoding if same model + prompt; L2 Combine (Ensembling) if different models or prompts.
- **Approval gates** → L7 (UX) and L5 Action (runtime mechanism).
- **Hallucination detection** → L4 (claim extraction), L2 Verify (scoring), L3a (citation/provenance). Methodology is C1.
- **Clarification & disambiguation** → L7 (UX) and L5 Shape (control-flow shape).

### Patterns frequently mis-filed
- **ReAct** → L5 Shape (Iterative Loops). Tool invocation inside is L5 Action.
- **Speculative decoding** → L0 (inference-time optimization).
- **Live web search inside an agent** → L3a (read-only).
- **Code execution** → L5 Action by default; L3a only if sandboxed and pure-functional.
- **Browser actions** → L3a for read-only scraping; L5 Action for clicks/submits.
- **Database ops** → L3a for SELECT; L5 Action for INSERT/UPDATE/DELETE.
- **Semantic caching** → split: runtime check & substitution is L0; cache contents and lifecycle are L−1. Same for prompt caching.
- **PAL / Program-of-Thought** → Recipe, not Part I pattern.
- **Long-context vs RAG decision** → C5 framework, not a pattern.
- **Prompt / model / eval-suite versioning** → L−1 Deployable Artifacts.
