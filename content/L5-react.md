# ReAct

> Design-agnostic content for the ReAct pattern page. Structured markdown, ready to drop into any page design. Voice and depth calibrated to `guideline.md`, `templates/pattern.md`, `templates/section-recipes.md`, and `example/rag.html`.

---

## META

- **Title:** ReAct
- **Full name:** Reasoning + Acting
- **Home:** L5 · Shape (Iterative Loops)
- **Breadcrumb:** The Pattern Book / L5 · Orchestration / Shape (Iterative Loops) / ReAct
- **Complexity:** 4 of 5
  - *Why:* The loop is easy to draw and hard to run. You manage tool wiring, a stop condition, error recovery, and a growing transcript. One bad observation can poison every later turn. Getting it reliable is the work.
- **Tags:** agentic · looping · tool-using · stateful
- **Updated:** May 2026

> Filing note: ReAct is the loop *shape*, not the tool call. The tool invocation that happens inside each turn is itself an L5 Action pattern. ReAct is often mis-filed as a tool-use pattern; it is the control-flow loop that wraps the tool use.

---

## 01 · THE IDEA

**Essence (one sentence):**

> A model **thinks** about what to do, **acts** by calling a tool, **reads** the result, and **repeats** until it has the answer.

Bold key terms: **thinks**, **acts**, **reads** (observation), **repeats** (the loop).

Idea tip (pointer down): *↓ How the loop works lives in Section 02*

---

## 02 · DATA FLOW

ReAct is a **loop**, not a straight line. The middle three steps repeat. Make the loop-back visible: the arrow from OBSERVE returns to THOUGHT, not forward to ANSWER. Only the stop condition breaks out.

**Nodes (ordered), with sub-labels and one-sentence step labels:**

1. **USER** · *Goal* — The user states a task that needs more than one lookup.
2. **THOUGHT** · *Plan next move* — The model writes what it should do next, in plain language.
3. **ACTION** · *Call a tool* — The model emits a tool call, like `search("...")` or `calculate(...)`.
4. **OBSERVE** · *Read result* — The tool runs and returns a result the model reads as new input.
5. **CHECK** · *Done?* — The model decides: enough to answer, or loop again.
6. **ANSWER** · *Final reply* — Once the goal is met, the model writes the final answer and stops.

**Loop structure:**

- Steps 2 → 3 → 4 → 5 form the cycle. CHECK has two exits: loop back to THOUGHT, or proceed to ANSWER.

```
USER ─▶ THOUGHT ─▶ ACTION ─▶ OBSERVE ─▶ CHECK ─▶ ANSWER
            ▲                              │
            └──────── loop if not done ────┘
```

**Animation note (the key teaching move):** Play must run at least two full turns before exiting. The reader has to *see* THOUGHT → ACTION → OBSERVE light up, then watch CHECK send the arrow back to THOUGHT for a second pass. A turn counter (`Turn 1`, `Turn 2`) ticks up each lap. The repetition is the pattern. One pass would teach the wrong shape.

**Step-synced legend (jargon + inline definitions):**

- **THOUGHT** — a short reasoning trace the model writes before acting, so its plan is visible and steerable.
- **ACTION** — a structured tool call the model emits as text, parsed and executed by the runner.
- **OBSERVE** — the tool's returned result, fed back into the prompt as the input for the next turn.
- **CHECK** — the stop condition: the loop ends when the model says it can answer, or a turn limit is hit.

---

## 03 · WHEN IT WORKS · WHEN IT FAILS

Six tap-to-reveal cards. Verdict + one-sentence reason. Three handle, three fail.

1. **"Which of our top 3 competitors raised funding most recently, and how much?"**
   - **Handles it ✓** — Search, read each result, compare, answer. Multi-step lookup is exactly the loop's job.

2. **"What is 17% of our Q3 revenue if Q3 was $4.2M?"**
   - **Handles it ✓** — The model thinks, calls a calculator tool, reads the number, answers. No mental-math errors.

3. **"Find the cheapest flight to Tokyo next Tuesday and book it."**
   - **Handles it ✓** — Search flights, read prices, then a booking action. The loop chains lookup into a real action.

4. **"What's the capital of France?"**
   - **Fails ✗** — One fact, already in the model. The loop adds turns, cost, and latency for nothing.

5. **"Write me a poem about the sea."**
   - **Fails ✗** — Pure generation, no facts to fetch and no actions to take. There is nothing to loop over.

6. **"Is this dense legal contract enforceable in California?"**
   - **Fails ✗** — Hard judgment with no clean tool to call. The loop spins or invents tool calls instead of reasoning it through.

Tracker: `0 / 6 tested` → `✓ All 6 tested — pattern understood`

---

## 04 · WORKED EXAMPLE

One specific case, end to end, multiple think-act-observe turns. This is a *case*, not the abstract flow restaged.

**User asks:**

> "Who is the current CEO of the company that bought GitHub, and what year were they born?"

This needs three facts chained: who bought GitHub, who runs that company now, and their birth year. No single lookup answers it.

**Turn 1**

- **Thought:** I need to find who acquired GitHub first.
- **Action:** `search("company that acquired GitHub")`
- **Observe:** *"Microsoft acquired GitHub in 2018 for $7.5 billion."*

**Turn 2**

- **Thought:** GitHub belongs to Microsoft. Now I need Microsoft's current CEO.
- **Action:** `search("current CEO of Microsoft")`
- **Observe:** *"Satya Nadella is the chairman and CEO of Microsoft."*

**Turn 3**

- **Thought:** The CEO is Satya Nadella. Now I need his birth year.
- **Action:** `search("Satya Nadella birth year")`
- **Observe:** *"Satya Nadella was born on August 19, 1967."*

**Turn 4 (check)**

- **Thought:** I have all three facts. I can answer now. Stop looping.

**Answer returned to user:**

> "GitHub was acquired by Microsoft. Microsoft's current CEO is Satya Nadella, who was born in 1967."

Note for the design: the punchline is the chaining. The model could not plan search two until it saw the result of search one. A single-shot call cannot do that.

---

## 05 · CODE SKETCH

~10 lines. The loop shape: reason, call a tool, observe, repeat until done. One plain-English line per line.

```python
for turn in range(MAX_TURNS):                # loop, but never more than MAX_TURNS times
    thought, action = model(transcript)      # model writes its reasoning and picks a tool
    if action == "finish":                   # the model decided it can answer now
        return thought                       # break out and return the final answer
    result = run_tool(action)                # actually call the chosen tool
    transcript += f"{thought}\n{action}\n{result}"  # append the turn so the next loop sees it
return "Stopped: too many steps."            # safety valve if the loop never finished
```

**Per-line explanations:**

- **Line 1** — Loop turn by turn, capped at MAX_TURNS. The loop is the whole pattern; the cap stops it spinning forever.
- **Line 2** — Ask the model what to think and which tool to call next, given everything so far.
- **Line 3** — Watch for the model's signal that it has enough to answer.
- **Line 4** — Stop the loop and hand back the final answer.
- **Line 5** — Run the tool the model named and capture its result.
- **Line 6** — Append thought, action, and result to the transcript so the next turn has full context.
- **Line 7** — If the loop runs out of turns without finishing, give up cleanly instead of burning tokens.

---

## 06 · TRADEOFFS

5-segment bars. Green = low, yellow = medium, red = high.

- **Cost:** High (4/5)
  - Every turn is a fresh model call, and the transcript grows each loop, so token cost compounds turn over turn.
- **Latency:** High (4/5)
  - Turns run in sequence, each waiting on a tool, so wall-clock time stacks up across the loop.
- **Complexity:** High (4/5)
  - You own tool wiring, the stop condition, error recovery, and bad-observation cascades. Reliability is the hard part.

**Quotable line (the takeaway):**

> *Give a model tools and a loop, and it can chase a multi-step answer. The cost is every turn, the risk is a loop that never ends.*

---

## TERMS (inline tooltips)

- **ReAct** — a loop where a model alternates between reasoning in words and taking actions with tools.
- **Reasoning trace (thought)** — the short plain-language plan a model writes before each action, so its intent is visible.
- **Action** — a structured tool call the model emits as text, which a runner parses and executes.
- **Observation** — the result a tool returns, fed back into the prompt as input for the next turn.
- **Tool** — an external function the model can call, such as web search, a calculator, or a database query.
- **Transcript** — the running record of every thought, action, and observation, re-sent to the model each turn.
- **Stop condition** — the rule that ends the loop: the model signals "done," or a maximum turn count is hit.
- **Turn** — one full pass of think, act, observe. A ReAct run is several turns.
- **Error cascade** — when a wrong observation early in the loop misleads every later turn.
- **Token** — the unit a model reads and bills by. A growing transcript means more tokens per turn.

---

## REFERENCES

- Yao, S., Zhao, J., Yu, D., Du, N., Shafran, I., Narasimhan, K., & Cao, Y. (2022). *"ReAct: Synergizing Reasoning and Acting in Language Models."* (arXiv:2210.03629) — the source of the pattern.
- Schick, T., Dwivedi-Yu, J., Dessì, R., Raileanu, R., Lomeli, M., Zettlemoyer, L., Cancedda, N., & Scialom, T. (2023). *"Toolformer: Language Models Can Teach Themselves to Use Tools."* (arXiv:2302.04761) — context for the tool-use the loop depends on.
