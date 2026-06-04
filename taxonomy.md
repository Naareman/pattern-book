# Layered Taxonomy (canonical)

> The canonical taxonomy for The Pattern Book. Every layer states its axis. Every layer states a filing rule. Each *mechanism* has exactly one home in Part I; named patterns that bundle multiple mechanisms are decomposed and filed per-mechanism, with the bundle cross-referenced. Cross-layer product shapes live in Part II as Recipes.
>
> Source of truth: Notion — https://www.notion.so/Layered-Taxonomy-369df9b0d69381bbbb27cdfdf58e1692 . When in conflict, Notion wins. This file mirrors the Notion version for Claude Code and contributors without Notion access.

## Three Rules

**Rule 1 — One axis per layer.** Every layer chapter opens with: *"Axis: [the one dimension this layer slices on]."* Categories partition that axis and nothing else.

**Rule 2 — Disciplines vs Mechanisms.** Crosscuts (C1–C8) own *disciplines* (frameworks for reasoning about a concern). Layers (L−1 to L7) own *mechanisms* (specific patterns that do the thing). Mechanisms live in layers and are cross-referenced from disciplines.

**Rule 3 — Known fragile boundaries.** Three boundaries are correctly drawn but most likely to be broken, because the axis the document slices on isn't the axis a reader naturally thinks in.

- **L4 Self-Verification vs L2 Verify** — split by call count (one = L4, two = L2). Reader intuition is "verification strategy"; the axis is implementation count.
- **L6 primary purpose, cross-reference secondary** — L6 is MECE in principle, fuzzy in application. Primary-purpose filing is a judgment, not a derivation.
- **L0 runtime vs L−1 artifact** — every artifact the runtime reads (prompt, model, eval suite, index, cache contents) has its lifecycle in L−1 and its consumption in L0. Consistent but cognitively expensive: "caching" must be looked up in two places.

These are the cost of choosing axis-purity over reader intuition.

**Scope of crosscuts.** Each crosscut declares scope with an *Applies to:* header. C1–C5 and C8 apply to every AI system. C6 (Multimodal) and C7 (Real-time / Streaming) apply only when the system has a specific property that changes how every layer behaves. Scope is declared per-crosscut, not a structural tier.

**Counts:** Layers: 10 (L−1, L0, L1, L2, L3a, L3b, L4, L5, L6, L7). Crosscuts: 8 (C1–C8; C6, C7 conditionally scoped). Categories: ~80 across Part I. Recipes (non-taxonomy): ~20. Anti-patterns appendix (Part III).

*(The previous Crosscut/Lens two-tier structure was retired. "Lenses" added a structural asymmetry that wasn't paying its weight; the only difference was scope, now declared per-crosscut via "Applies to:". Multimodal and Real-time / Streaming are C6 and C7, peers with declared conditional scope.)*

---

## Part I — The Layered Taxonomy

### L7 · User Surface & Agency
**Axis: UX function — how the user experiences and controls the AI.**

Categories: Conversational surfaces · Inline / embedded surfaces · Background / agentic surfaces · Streaming & progressive disclosure (UX mechanism; the real-time/streaming *discipline* is C7) · Handoff & escalation · Approval gates · Autonomy levels & guardrails · Feedback capture surfaces · Clarification & disambiguation surfaces (UX of asking a follow-up; the *control-flow shape* of the clarification loop is L5 Shape Conditional Branching) · **Spec-driven workflow surface** *(Mechanism)* — spec, implementation, and tests are visible artifacts the user reviews, edits, and approves before each phase advances; post-vibe-coding workflow.

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

**Shape** (structure of execution across calls): Single-call patterns · Sequential pipelines · Parallel / fan-out / fan-in · Iterative loops (ReAct shape, reflection loops) · Router / dispatcher patterns · Hierarchical planning (Planner–Executor, plan-of-plans) · Conditional branching · **Asynchronous / Background** *(Mechanism, 8th shape)* — execution happens outside the user's request thread; the user is not waiting on the same call. Sub-mechanisms: *Sleep-time compute* (Letta) · *Background mode* (OpenAI) · *Scheduled tasks* (Claude) · *Speculative tool execution* (PASTE) · *The Ralph Pattern* (fresh agent instance re-spawned each turn) · **CodeAct (code-as-action loop)** *(Mechanism, shape variant)* — every tool call is expressed as code in a sandboxed runtime; the loop alternates "write code → execute → observe." · **Bounded Execution** *(Mechanism, termination condition under Iterative loops)* — hard caps on steps / tokens / wall-clock / cost that terminate a loop regardless of internal stopping criteria.

**Action** (state-changing operations): External Action (state-changing tool invocation) · Idempotency & action verification · Compensating actions & rollback · Approval gates (mechanism; L7 owns the UX) · Side-effect sandboxing · Multi-step action transactions · **Sealed tools / capability-scoped tool exposure** *(Mechanism)* — the agent receives a narrow capability handle whose internals (credentials, exact endpoint, raw payload) it cannot see or alter; mitigates injection by removing the surface to attack · **Code execution sandbox as a service** *(Mechanism)* — managed isolated runtimes the agent uses for code-as-action; productized examples include E2B, Modal, Daytona, Firecracker-based services.

**Coordination** (multi-actor protocols): Agent-to-agent protocols (MCP, A2A, message-passing) · Shared workspace / blackboard · Agent identity & auth · Capability negotiation & tool discovery · **Sub-Agent Delegation** *(Pattern)* — parent agent delegates a scoped task to a fresh-context specialist sub-agent with its own system prompt, tool set, and model; only the summarized result returns, keeping the parent's context clean. First-class peer of Supervisor and Swarm. · **MCP federation / gateway** *(Mechanism)* — a single MCP endpoint fronts many downstream MCP servers; the agent sees one capability surface, the gateway handles routing, auth, and policy. · **Progressive Tool Disclosure** *(Mechanism)* — only a small list of tools is loaded into context up front; further tools are surfaced on demand via a search/lookup tool (e.g. RAG-MCP, `tools/list` + tool search). Mitigates "every tool in the system prompt" anti-pattern. · **Agent Card-based capability advertisement** *(Mechanism)* — each agent publishes a machine-readable manifest (A2A Agent Card) describing its identity, skills, endpoints, and auth requirements; other agents discover it before delegating.

Filing rule: Shape = "in what order/structure do calls execute?" Action = "what does the system do to outside state?" Coordination = "how do multiple actors communicate?" ReAct: the loop is Shape; the tool calls inside are Action. Multimodal: multimodal tool I/O (screenshot-in / click-out) as Action; cross-modal action reasoning is C6.

### L4 · Single-Call Control
**Axis: call-time stage — what you shape at which point inside one model call.** Sub-axes: Input Shape, Cognitive Strategy, Self-Verification, Output Shape & Decoding.

**Input Shape**: zero-shot / few-shot framing · role / persona · delimiters · negative prompting · meta-prompting · prompt templates · example selection · example ordering · context packing · lost-in-the-middle mitigation · prompt compression.

**Cognitive Strategy** (sliced by where it executes):
- In-prompt (authored as text the model follows): Step-Back, Least-to-Most, Plan-and-Solve, Analogical Prompting, Chain-of-Thought (prompt-invoked).
- In-decoding (search over generations): Tree-of-Thoughts, Skeleton-of-Thought, Self-Consistency-as-search.
- **Model-native reasoning** (capability baked into the model weights, surfaced via API knobs, no prompt-level CoT required): **Extended thinking budget** *(Mechanism)* — explicit token allowance for internal reasoning before the visible answer · **Reasoning tokens** *(Mechanism)* — separate hidden-state tokens the model spends on thought, billed and rate-limited separately · **Adaptive thinking** *(Mechanism)* — low / medium / high / max settings the caller picks per request to trade cost for depth · **Interleaved thinking** *(Mechanism)* — the model is allowed to think between tool calls inside a single agent turn, not only before the first response · **Encrypted reasoning items** *(Mechanism)* — opaque reasoning traces returned to the client so a stateless re-call can resume from the prior thought without exposing the raw chain; needed for zero-data-retention (ZDR) deployments.

**Self-Verification within the call**: Self-Reflection, Chain-of-Verification, Self-Ask, single-model debate-then-decide.
Filing rule: verification inside one call to the same model = L4. Separate verifier call or different model = L2 Verify.

**Output Shape & Decoding**: structured output · schema enforcement · constrained decoding · streaming-with-validation · format coercion · temperature & sampling · logit biasing · stop sequences · beam search.

Filing rule: if it happens inside one call to one model, it's L4. A second call or a tool = L5. A second model = L2.
Self-Consistency: L4 in-decoding when N samples come from same model + same prompt; L2 Combine (Ensembling) when different models or prompts.

### L3b · Stateful Memory
**Axis: how persistent knowledge accumulates and is recalled across sessions.**

Sub-structure (scope matrix): memory is described as a **TYPE × SCOPE** matrix. Every stored memory has both.

**TYPES** (what kind of knowledge):
- *Episodic* — specific past events ("on 14 Mar the user asked X, system answered Y").
- *Semantic* — distilled facts and preferences ("user lives in Cairo," "prefers Arabic responses").
- *Procedural* — how-to knowledge and learned routines ("when the user asks about CBE circulars, search markazy first").

**SCOPES** (who/what owns the memory):
- *User* — keyed to an end-user identity, persists across all that user's sessions.
- *Session* — keyed to a single conversation/run, dies when the session closes.
- *Agent* — keyed to the agent itself, shared across all users (e.g. global procedural memory the agent learned from operator feedback).

Existing categories map onto the matrix as follows: Conversation memory = Episodic × Session (rolling window / summarized). Long-term user memory = Semantic × User. Episodic memory = Episodic × User. Cross-session identity & continuity spans Semantic × User and Procedural × User.

Operational mechanisms (apply across cells of the matrix):
- Memory consolidation (compress / promote over time)
- Memory retrieval triggers (when to surface a memory)
- Memory eviction & forgetting
- **Temporal knowledge graph** *(Mechanism)* — memory stored as time-stamped nodes/edges so the system can answer "what did the user believe on date X" and not just "current state." Productized in Graphiti / Zep.
- **OS-style memory tiering** *(Mechanism)* — main context / recall buffer / archival store with explicit promotion and eviction between tiers, modeled on OS virtual memory. Letta's MemGPT design.
- **Task Ledger / Activity Log** *(Mechanism)* — append-only log of everything the agent has done this run; used for re-planning and post-hoc review. Magentic-One.
- **Virtual filesystem** *(Mechanism)* — memory exposed to the agent as files and directories it reads/writes with normal file tools; persistence is implemented by the host. Deep Agents.
- **Compaction-on-schedule** *(Mechanism)* — periodic compression of the running context into a shorter summary on a tick (token count, turn count, wall-clock), not only when the window is full.

Filing rule: persists across requests/sessions and keyed to an entity → L3b. Loaded fresh per request → L3a. To file a new memory mechanism, place it on the TYPE × SCOPE matrix first; operational mechanisms attach to one or more cells.

### L3a · Stateless Retrieval
**Axis: how external information enters a single call from a corpus.**

Reader trap: the L3a vs L5 Action axis is read-only vs side-effectful, not who does the work. A live web search by an agent is L3a (read-only).

Categories: Indexing & embedding · Retrieval (dense, sparse, hybrid) · Reranking · Context assembly · Knowledge tool invocation (read-only) · Caching of knowledge results · Freshness & invalidation · Citation & provenance.

Named indexing mechanisms:
- **Late chunking** *(Mechanism)* — embed the long document first, then derive chunk vectors from the contextualized token embeddings; chunks inherit full-document context instead of being embedded in isolation.
- **ColBERT-style late interaction** *(Mechanism)* — store per-token embeddings and score at query time with a MaxSim interaction across all token pairs; higher recall at the cost of larger indexes.

Named retrieval mechanisms:
- **GraphRAG** *(Mechanism)* — extract an entity/relationship graph from the corpus offline and use community summaries + graph traversal as the retrieval substrate for global, multi-hop questions.
- **Headless semantic layer as agent tool** *(Mechanism)* — the agent queries a governed metrics/semantic layer (Cube, dbt OSI MCP) instead of raw tables; the layer enforces joins, definitions, and access control.

Filing rule: SELECT / file read / sandboxed pure-functional code / read-only scraping → L3a. INSERT/UPDATE/DELETE, browser clicks/submits, side-effectful code → L5 Action. Live web search inside an agent → L3a.
Sidebar — Long-Context vs RAG: when the corpus fits the context window, the L3a stack collapses into L4 Input Shape (context packing). The tradeoff framework (cost/latency/recall/precision) is a C5 discipline.

Multimodal: multimodal embeddings, image/audio retrieval, cross-modal search as L3a; cross-modal grounding is C6.

### L2 · Structural Composition
**Axis: how multiple potential model calls are organized into a single answering act.** Sub-axes: Combine, Select, Verify.

**Combine** (multiple outputs fuse): Ensembling (vote, average, blend) · Decomposition & recomposition.
**Select** (one output chosen from candidates): Cascading (cheap→expensive) · Confidence-based routing · Specialist-of-experts routing (system-level Mixture-of-Experts; distinct from L1 architectural MoE inside a single model's weights).
**Verify** (one output gated/scored/revised by separate judgment): Verification composition (LLM-as-Judge ⭐) · Generator–critic pairs · Output filtering by classifier.

Filing rule: fused from multiple outputs = Combine; chosen from candidates = Select; gated by separate judgment = Verify. Self-verification inside one call is L4, not L2 Verify.
Speculative decoding = L0 (no semantic composition). Caching splits: runtime check & substitution = L0; cache contents & lifecycle = L−1.

### L1 · Model Training & Adaptation
**Axis: training lifecycle stage.**

Categories: Pretraining objectives & data · Continued pretraining · Supervised fine-tuning (SFT) · Preference optimization · Reasoning-capability training · PEFT (LoRA, QLoRA, adapters, prefix tuning) · Distillation · Model merging · Evaluation during training (checkpoint selection, dev-set hygiene).

**Preference optimization — named variants** *(all Mechanisms)*:
- *RLHF* — RL from human-labeled preference pairs.
- *Constitutional AI / RLAIF* — preference labels generated by an AI critic against a written constitution.
- *DPO (Direct Preference Optimization)* — closed-form objective over preference pairs, no separate reward model.
- *KTO (Kahneman–Tversky Optimization)* — uses binary "desirable/undesirable" labels instead of pairwise comparisons.
- *IPO (Identity Preference Optimization)* — DPO variant that avoids the implicit reward modeling assumption, more robust to label noise.

**Reasoning-capability training — named mechanisms** *(all Mechanisms; purpose is to create native reasoning capability in the weights, regardless of mechanism shape)*:
- *RLVR (RL with Verifiable Rewards)* — RL where the reward is a deterministic checker (unit tests pass, math answer matches), not a learned reward model.
- *GRPO / DAPO / GSPO algorithm family* — group-relative policy optimization variants used to train reasoning models at scale without a value network.
- *RFT (Reinforcement Fine-Tuning)* — productized RLVR offered as a fine-tuning API (e.g. OpenAI's offering) where the customer supplies a grader function.
- *Process Reward Models (PRM)* — reward each step of a reasoning trace, not just the final answer; the trained PRM then guides decoding or further RL.
- *Reasoning-trace distillation* — train a smaller model on long chains-of-thought emitted by a larger reasoning model.

**Model merging — named methods** *(all Mechanisms)*:
- *Task Vectors* — fine-tuned-minus-base weight deltas treated as additive "skills" you can combine or subtract.
- *TIES (TrIm, Elect Sign, and Merge)* — sparsifies and sign-aligns task vectors before averaging to reduce interference.
- *DARE (Drop And REscale)* — randomly drops most delta weights and rescales the rest; lets many task vectors stack without conflict.

Filing rule: what stage of the training lifecycle? If the purpose is to create native reasoning capability in weights, it's Reasoning-capability training regardless of mechanism shape.
Architectural note: *Mixture-of-Experts (architectural MoE)* inside the model (sparse expert layers gated per token) is an L1 pretraining-architecture choice; system-level routing across whole models is L2 Select Specialist-of-experts.
Multimodal: vision/audio encoders, fusion architectures (late vs early) as L1; fusion strategy thinking is C6.

### L0 · Serving Infrastructure
**Axis: infrastructure function — what the runtime provides.**

Categories: Inference serving (batching, KV-cache, paged attention, speculative decoding) · Routing & load balancing · Fallback chains · Circuit breakers & retry policies · Cache check & substitution (prompt-cache prefix + semantic-cache similarity; the runtime mechanism — contents/lifecycle are L−1) · Cost/latency-optimized model selection at runtime (applies C5 frameworks) · Observability hooks (emits traces; the discipline is C4) · Quantization & compression at serving time · **AI Gateway** *(Mechanism, platform family)* — a single proxy in front of all model providers that owns virtual API keys, per-team rate limits and budgets, provider failover, semantic caching, and gateway-level guardrails. Productized examples include Portkey, LiteLLM, Kong AI Gateway, OpenRouter, Helicone.

Filing rule: operates on the model call as a black box → L0. Operates on what the model says or which model speaks → L2. Lifecycle of an artifact the runtime reads → L−1.

### L−1 · Data & Artifact Lifecycle
**Axis: lifecycle stage of the artifacts that feed and define the system.** Sub-axes: Data, Deployable Artifacts.

**Data** (consumed by L1 training or L3a/L3b retrieval): Collection & sourcing · Labeling & annotation · Data quality & deduplication · Synthetic data generation · Versioning & lineage · PII scrubbing & redaction · Retention & deletion policies · Feedback data pipelines.
**Deployable Artifacts** (configs that define runtime behavior): Prompt versioning · Model versioning · Eval-suite versioning · Retrieval-index versioning · Cache-contents lifecycle (population, invalidation, eviction, schema versioning) · Artifact-lineage tracking (cross-ref C4) · Rollback & roll-forward · **Programmatic prompt optimization** *(Mechanism category, distinct from prompt versioning which merely tracks human-authored versions)* — optimizers that *generate* prompts from a training set + metric. Named optimizers: **GEPA** *(Mechanism)*, **MIPROv2** *(Mechanism)*, **TextGrad** *(Mechanism)*, **BetterTogether** *(Mechanism)*. · **Skills as deployable artifacts** *(Mechanism)* — packaged capability bundles (e.g. `SKILL.md` format) loaded into the agent via progressive disclosure: only the trigger description is in context until a matching task arrives, then the full skill is hydrated. Cross-LLM Skills format is a portable artifact, not a runtime feature.

Filing rule: data consumed by training/retrieval → L−1 Data. Config defining runtime behavior → L−1 Deployable Artifacts. The runtime reads the artifact (L0 consumer); the artifact's lifecycle is L−1.

---

## Crosscuts (C1–C8)
Every crosscut is a discipline, not a mechanism. Mechanisms live in layers and are cross-referenced.

### C1 · Evaluation — *Applies to: every AI system.*
**Axis: how to judge whether the system produces the right output or trajectory.**
Eval methodology (benchmarks, golden sets, human eval, rubrics) · Agent trajectory evaluation · Regression testing · Eval-driven development · Statistical rigor · Prompt lifecycle thinking · Faithfulness & groundedness evaluation · Hallucination detection methodology (claim extraction L4; verifier scoring L2; attribution L3a; framework C1).

Additional disciplines:
- **Three-level eval (span / trace / persona)** *(Discipline)* — evaluate at the single-call level (span), the full multi-step run (trace), and the simulated-user level (persona); each catches failures the others miss.
- **Error-analysis-first methodology** *(Discipline)* — start every eval workstream by reading failing traces and inducing failure modes from the data, not by picking a metric first. (Hamel Husain.)
- **Trajectory-level reward modeling** *(Discipline)* — train or score reward over whole trajectories rather than per-step, to capture goal achievement instead of local plausibility.
- **Reference-free faithfulness judges** *(Discipline)* — judges that score grounding against the retrieved context, not against a written reference answer; required where no ground-truth answer exists.

### C2 · Safety, Alignment & Refusal — *Applies to: every AI system.*
**Axis: keeping AI behavior within intended bounds.**
Refusal calibration · Harm taxonomies & severity reasoning · Red-teaming methodology · Jailbreak-resistance · Dual-use review · Refusal-vs-helpfulness tradeoff · Principle-based behavioral evaluation.

**Sub-discipline — Prompt Injection Defense** *(Discipline family; mechanisms live in L0 / L4 / L5)*:
- **Lethal Trifecta** *(Discipline)* — Simon Willison's framing: an agent becomes injection-exploitable when it simultaneously has access to (1) untrusted content, (2) private data, and (3) external communication. Mitigation = remove at least one leg.
- **Agents Rule of Two** *(Discipline)* — Meta's heuristic: an agent session should hold at most two of {untrusted input, sensitive data, state-changing actions} at any time.
- **CaMeL / Dual-LLM pattern** *(Discipline + Pattern)* — split into a privileged planner LLM that never sees untrusted content and a quarantined LLM that processes untrusted content but cannot call sensitive tools; data flows between them are policy-checked.
- **Defense-in-depth Guardrail Layering** *(Discipline)* — four sequential gates: input gateway, tool-call gate, tool-response gate, final output gate. No single gate is trusted alone.
- **Sealed-tool / Plan-Then-Execute pattern** *(Pattern)* — the planner produces a fixed plan over sealed (capability-scoped) tools; execution does not re-plan based on tool outputs, so injected instructions in a tool response cannot change the plan.

### C3 · Privacy, Security & Governance — *Applies to: every system handling user/customer/tenant data.*
**Axis: protecting data, users, and tenants.**
Access control · Tenant isolation discipline · Audit-logging · Compliance mapping (GDPR, HIPAA, SOC2) · Data residency · Runtime PII-handling · Provenance & watermarking.

**Sub-discipline — Agent Identity & Provenance** *(Discipline family)*:
- **Know Your Agent (KYA)** *(Discipline)* — the agent-equivalent of KYC: before another system trusts an agent, it verifies who operates it, what model it runs, what version, and on whose behalf.
- **Verifiable Credentials (W3C VC v2)** *(Mechanism)* — cryptographically-signed claims about an agent (issuer, holder, attributes) that any party can verify without contacting the issuer.
- **Agent Cards (A2A)** *(Mechanism)* — machine-readable manifest published by an agent declaring identity, skills, endpoints, and auth (cross-ref L5 Coordination).
- **Agent wallets** *(Mechanism)* — secure store for an agent's keys, credentials, and delegated authorizations; the agent signs requests from the wallet rather than holding raw secrets in context.
- **Cryptographically-signed agent manifests** *(Mechanism)* — manifest contents are signed by the operator so a relying party can detect tampering or impersonation.
- **Agent-as-principal vs user-as-principal models** *(Discipline)* — does the downstream system authorize the agent in its own right, or only as a delegate acting on behalf of a named user? Choice drives audit, liability, and revocation.

### C4 · Observability & Debuggability — *Applies to: every system in production.*
**Axis: how to explain what the system did after the fact.**
Tracing discipline · Replay frameworks · Attribution thinking · Drift detection methodology · Incident postmortem patterns · Causal analysis frameworks.

Additional disciplines:
- **Three-level eval (span / trace / persona)** *(Discipline)* — also applies here as the structural model for what to log and what to render in a trace viewer (cross-ref C1).
- **Error-analysis-first methodology** *(Discipline)* — observability is judged by whether it lets you do error analysis cheaply, not by how much it logs (cross-ref C1).
- **Trajectory-level reward modeling** *(Discipline)* — observability should surface whole-trajectory outcomes, not only span-level signals (cross-ref C1).
- **Reference-free faithfulness judges** *(Discipline)* — production traces rarely have references; judges must score against retrieved context (cross-ref C1).
- **Agent benchmark gaming** *(Anti-pattern)* — many agent benchmarks can be gamed without solving the underlying task; relying on public-benchmark scores as the observability signal masks real failure modes. See Part III.

### C5 · Cost, Latency & Reliability — *Applies to: every system with cost/latency/reliability constraints.*
**Axis: engineering AI systems against budgets and SLAs.**
Cost attribution · Cost-quality tradeoff · Latency budgets (perceived vs actual, tail) · Latency-quality tradeoff · Context-strategy tradeoff (RAG vs long-context vs hybrid; cross-ref L3a) · SLA/SLO design · Error budgets & graceful degradation · Incident response.

### C6 · Multimodal — *Applies to: systems with non-text modalities (text-only can skip).*
**Axis: reasoning about systems spanning text, vision, audio, or mixed.**
Modality selection · Cross-modal grounding (cross-ref C1) · Fusion strategy (early vs late; cross-ref L1) · Modality-specific eval · Latency budgets across modalities (cross-ref C5, L0) · Modality-specific safety & refusal (cross-ref C2) · Multimodal cost attribution (cross-ref C5) · Mixed-modality handoff thinking (mechanism is L7).

### C7 · Real-time / Streaming — *Applies to: systems with sub-second incremental output (batch can skip).*
**Axis: reasoning where time-to-first-token and continuous output matter more than throughput.**
Streaming output discipline (cross-ref L7) · Time-to-first-token frameworks (cross-ref C5) · Streaming eval (cross-ref C1) · Streaming safety (cross-ref C2) · Streaming validation (partial JSON; cross-ref L4 Output Shape) · Backpressure & cancellation · Streaming cost attribution · Continuous-context for voice (turn-taking, interruption, barge-in).

### C8 · Context Engineering — *Applies to: every AI system.*
**Axis: deciding what enters, stays in, and leaves the model's context window.**

Karpathy's 4-verb framework. Each verb is a discipline; the mechanisms live in existing layers.

- **WRITE** *(Discipline)* — put information somewhere it can be retrieved later. Mechanisms: L−1 Deployable Artifacts (prompts, skills, specs), L3b Stateful Memory (write side: storing episodic / semantic / procedural memories, append-only task ledger, virtual filesystem writes).
- **SELECT** *(Discipline)* — choose which information to load into the next call. Mechanisms: L3a Stateless Retrieval (indexing, retrieval, reranking, knowledge tool invocation), L3b retrieval triggers (read side of memory), L5 Progressive Tool Disclosure (selecting which tools to surface).
- **COMPRESS** *(Discipline)* — fit more meaning into fewer tokens. Mechanisms: L4 Input Shape (prompt compression, context packing, lost-in-the-middle mitigation), L3b memory consolidation, L3b compaction-on-schedule.
- **ISOLATE** *(Discipline)* — keep contexts separated so failure or contamination in one doesn't poison another. Mechanisms: L5 Sub-Agent Delegation (each sub-agent has its own context), L5 Sealed tools, C2 CaMeL / Dual-LLM pattern.

Position: C8 is the umbrella discipline for everything that decides what loads into context. New context-management techniques are filed first under the appropriate verb here, then to the layer that owns the mechanism.

---

## Part II — Recipes
Recipes overlap by design; not part of the MECE Part I taxonomy. A recipe is a named composition of Part I patterns, with sequencing and tradeoff notes. Recipes never define new categories.

Recipes to write: Conversational Assistant · Document Q&A / Knowledge Chatbot · Coding Agent · Research Agent · Browsing Agent · Customer Support Agent · Reasoning System with Code Execution (PAL / Program-of-Thought) · Summarization Service · Voice Assistant · Multi-Agent Research Team.

Additional recipes (each with "decomposes into" note):

- **Tool-using Agent Loop** — the canonical "agent." Decomposes into: ReAct (L5 Shape Iterative loops) + L5 Action (tool invocation) + Tool Use (L5 Action) + L5 Iterative loop + Bounded Execution (L5 termination).
- **LLM Council / Model Council** — multiple models independently answer, then peer-review each other's anonymized answers, then a chairman synthesizes. Decomposes into: L2 Combine (Ensembling, N different models) + L2 Verify (anonymized peer review) + L2 Combine (chairman synthesis).
- **Agentic RAG** — RAG where the retrieval/answer loop is driven by an agent that can re-query, re-rank, and self-check. Decomposes into: L3a Retrieval + L4 Self-Verification + L5 Iterative loops + L5 Action.
- **Deep Research Agent** — long-horizon research with parallel sub-agents writing to a shared artifact. Decomposes into: L5 Hierarchical planning + L5 Sub-agent delegation + L3a Retrieval + L4 Cognitive Strategy + L7 surface (research report as visible artifact).
- **Agent Harness** — the "brain / hands / session" decomposition that surrounds a model with a runnable agent. Decomposes into: L5 iterative loop + L5 sub-agents + L3a/L5 sandboxed tool I/O + L−1 Skills artifacts + C4 observability.
- **Spec-Driven Engineering Workflow** — Karpathy's post-vibe-coding pattern. Decomposes into: L7 approval gates + L5 Coordination + L−1 specs as artifacts + C1 evals as living tests.
- **Supervisor / Worker (orchestrator-worker)** — persistent worker agents with identity, supervised by an orchestrator. Decomposes into: L5 Hierarchical + persistent worker agents with identity. Distinct from Planner–Executor which executes stateless steps.
- **Multi-agent Debate (cross-model adversarial)** — multiple models argue in rounds, judge synthesizes. Decomposes into: L5 Coordination + multiple model calls + adversarial round structure + judge synthesis. Distinct from single-call Debate-then-decide which is L4.
- **Hierarchical Multi-Agent (supervisors of supervisors)** — recursive composition of the supervisor pattern. Decomposes into: L5 Hierarchical recursive composition.
- **Swarm / Peer-to-peer agents** — agents hand off to each other with no central supervisor. Decomposes into: L5 Coordination with no supervisor. (OpenAI's standalone Swarm library is deprecated; the pattern survives in Agents SDK Handoffs.)

---

## Part III — Anti-patterns
Things that look right but break in production. Each entry is filed as an Anti-pattern, with the layer or discipline where the correct mechanism lives.

- **Stuffing every tool in the system prompt at scale** *(Anti-pattern)* — works for ten tools, collapses at a hundred: context bloat, selection errors, cost. Correct mechanism: L5 Coordination Progressive Tool Disclosure (RAG-MCP, `tools/list` + tool search).
- **Caching error/rate-limit responses** *(Anti-pattern)* — caching a `rate_limited` or `not_found` reply turns a transient failure into a permanent one. (Markazy production lesson.) Correct mechanism: L0 Cache check & substitution with explicit cache-key policy excluding error states; L−1 cache-contents lifecycle.
- **"Agent" as a marketing term without distinguishing runtime-orchestration vs static workflow** *(Anti-pattern)* — calling a fixed pipeline an "agent" hides whether the system actually does runtime planning. Correct framing: L5 Shape distinguishes Sequential pipeline vs Iterative loop vs Hierarchical planning; pick the term that matches the shape.
- **Trusting an LLM-as-Judge that knows which output came from the favored model** *(Anti-pattern)* — brand and style leakage make the judge prefer the in-house or larger model. Correct mechanism: L2 Verify with anonymization (strip identifying markers, randomize order); see also C1 reference-free faithfulness judges.
- **Vibe-coding without specs or evals** *(Anti-pattern)* — generate, eyeball, ship; no artifact to regress against. Correct mechanism: Spec-Driven Engineering Workflow recipe (L7 approval gates + L−1 specs + C1 evals).
- **Benchmark gaming** *(Anti-pattern)* — most public agent benchmarks are gameable without solving the tasks (memorized solutions, overfit prompts, leaked test data). Correct mechanism: C1 error-analysis-first methodology on your own traces; C4 trajectory-level outcomes from production.

---

## Appendix — Edge Cases & Filing Precedents
Format: Pattern → home layer + the principle that decided it.

### Sub-axis precedents within layers
- L2 Combine vs Select vs Verify — fused / chosen-from-candidates / gated-by-separate-judgment.
- L4 four sub-axes — Input Shape / Cognitive Strategy / Self-Verification / Output Shape & Decoding (decided by where in the single call it shapes behavior).
- L4 Cognitive Strategy in-prompt vs in-decoding vs model-native — authored as prompt text vs search procedure over generations vs capability in the weights surfaced via API knobs.
- L5 Shape vs Action vs Coordination — execution structure / change to the world / communication with another actor.
- L3b TYPE × SCOPE — every memory mechanism is placed first on the matrix (episodic/semantic/procedural × user/session/agent), then operational mechanisms attach to cells.
- L−1 Data vs Deployable Artifacts — consumed by training/retrieval vs read as runtime config.

### Same-name patterns with split homes
- Self-critique / Constitutional self-revision → L4 if one call; L2 Verify if a separate verifier call.
- Self-Consistency → L4 in-decoding if same model+prompt; L2 Combine if different models/prompts.
- Approval gates → L7 (UX) and L5 Action (runtime mechanism).
- Hallucination detection → L4 (claim extraction), L2 Verify (scoring), L3a (citation/provenance); methodology is C1.
- Clarification & disambiguation → L7 (UX) and L5 Shape (clarification-loop control flow).
- Mixture-of-Experts → L1 if architectural (sparse expert layers inside one model); L2 Select if system-level routing across whole models.
- Agent Cards → L5 Coordination (capability advertisement mechanism); C3 Privacy (identity & provenance discipline).
- Skills → L−1 Deployable Artifacts (the packaged artifact); L5 Progressive Tool Disclosure (the runtime loading mechanism).

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
- Programmatic prompt optimization (GEPA, MIPROv2, TextGrad) → L−1 Deployable Artifacts (it *generates* prompts), not L4 (which *uses* prompts).
- AI Gateway → L0 (runtime infrastructure); gateway-level guardrails cross-ref C2.
- Extended thinking / reasoning tokens → L4 Cognitive Strategy model-native sub-axis (not L1; the capability comes from L1 training, but the *control surface* is L4 per-call).

### Discipline-vs-mechanism precedents
- Prompt lifecycle → C1 (thinking) + L−1 (versioning mechanism).
- Tenant isolation → C3 (discipline) + L0/L3a (mechanisms).
- Observability → C4 (discipline) + L0 (hooks).
- Cost attribution → C5 (framework) fed by L0 observability hooks.
- Refusal calibration → C2 (framework) + L4 (output shaping) + L7 (refusal UX).
- Context engineering → C8 (umbrella discipline) + L−1 / L3a / L3b / L4 / L5 (mechanisms per verb).
- Prompt injection defense → C2 (discipline family) + L0 gateway / L4 input handling / L5 sealed tools (mechanisms).
- Agent identity & provenance → C3 (discipline family) + L5 Coordination (Agent Cards, KYA, wallets as mechanisms).

### Known fragile boundaries
- L4 Self-Verification vs L2 Verify — boundary is call count (1 = L4, 2 = L2). Watch for L4 patterns with two calls or L2 Verify patterns inside one call.
- L6 multi-category tasks — "primary purpose, cross-reference secondary" is a judgment; resolve by explicit precedent.
- L0 runtime vs L−1 artifact split — artifact lifecycle → L−1; runtime read → L0; both exist for every artifact the runtime reads.

### Provisional patterns
Holding state, not permanent. Time-bounded (decide-by date), capped at 5, cited (candidate homes + what makes it hard), resolved into the appendix when decided. *(Currently empty.)*

---

## Appendix — Common Names
Widely-used framings mapped to our taxonomy slots. One row per popular name; multiple slots are normal because most named patterns are recipes.

| Common name | Our slot |
|---|---|
| Ng's Four Patterns (Reflection, Tool Use, Planning, Multi-Agent Collaboration) | L4 Self-Verification + L5 Action + L5 Hierarchical + L5 Coordination |
| Plan-and-Execute, ReWOO | L5 Hierarchical Planning variants |
| Mixture of Experts (system-level) | L2 Select / Specialist-of-experts routing |
| Mixture of Experts (architectural) | L1 Pretraining-architecture choice |
| Self-RAG, Corrective RAG (CRAG), GraphRAG | L3a + L4 Self-Verification (GraphRAG also adds an L3a indexing mechanism) |
| LangGraph Supervisor / Swarm / Network / Mesh | L5 Coordination variants |
| OpenAI Handoffs | L5 Coordination (agent-to-agent) |
| Manager pattern (agents-as-tools) | L5 Hierarchical |
| Reflexion | L4 Self-Verification + L3b memory loop |
| Magentic-One orchestrator | L5 Hierarchical + dynamic replanning + L3b Task Ledger |
| Best-of-N | L2 Combine (Ensembling) or L4 in-decoding |
| Constitutional AI, RLAIF | L1 Preference optimization |
| DPO, KTO, IPO | L1 Preference optimization variants |
| RLVR, GRPO/DAPO/GSPO, RFT, PRM | L1 Reasoning-capability training |
| Task Vectors, TIES, DARE | L1 Model merging methods |
| Llama Guard / Shield Gemma / NeMo Guardrails | L2 Verify (Output filtering) or L0 gateway |
| Portkey / LiteLLM / Kong AI / OpenRouter / Helicone | L0 AI Gateway |
| E2B / Modal / Daytona / Firecracker | L5 Action — Code execution sandbox as a service |
| Graphiti / Zep | L3b Temporal knowledge graph |
| Letta (MemGPT) | L3b OS-style memory tiering + L5 Asynchronous (sleep-time compute) |
| Deep Agents | L3b Virtual filesystem |
| MCP / A2A | L5 Coordination (agent-to-agent protocols); A2A Agent Cards also in C3 |
| RAG-MCP, `tools/list` + tool search | L5 Coordination Progressive Tool Disclosure |
| Claude Skills, SKILL.md | L−1 Deployable Artifacts (Skills) + L5 Progressive Tool Disclosure |
| GEPA, MIPROv2, TextGrad, BetterTogether | L−1 Programmatic prompt optimization |
| Extended thinking / reasoning tokens / adaptive thinking | L4 Cognitive Strategy — model-native reasoning |
| CodeAct | L5 Shape — code-as-action loop |
| The Ralph Pattern, Sleep-time compute, Background mode, Scheduled tasks, PASTE | L5 Shape — Asynchronous / Background |
| Sub-agents (Claude Code, etc.) | L5 Coordination — Sub-Agent Delegation |
| Lethal Trifecta, Agents Rule of Two, CaMeL / Dual-LLM | C2 Prompt Injection Defense |
| KYA, Verifiable Credentials, Agent wallets | C3 Agent Identity & Provenance |
| Three-level eval (span / trace / persona), error-analysis-first | C1 + C4 |
| Context Engineering (Karpathy) | C8 (this taxonomy's umbrella) |
| Prompt Engineering | L4 Input Shape + parts of L4 Cognitive Strategy |
| Spec-driven development | Recipe (Spec-Driven Engineering Workflow) + L7 spec-driven workflow surface |
| Vibe coding | Anti-pattern (see Part III) |

---

## Final Slicing Axes
- L7 — UX function
- L6 — input → output relationship
- L5 — orchestration across time, world, actors (Shape, Action, Coordination)
- L4 — call-time stage inside one model call (Input Shape, Cognitive Strategy [in-prompt / in-decoding / model-native], Self-Verification, Output Shape & Decoding)
- L3b — persistent knowledge across sessions (TYPE × SCOPE matrix)
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
- C8 — deciding what enters, stays in, and leaves the context window (WRITE / SELECT / COMPRESS / ISOLATE)
