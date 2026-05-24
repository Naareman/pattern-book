# Tool Use: Content

> Design-agnostic content for one Pattern Book page. Final, ready-to-use copy. Drop into any page design.
> Concept: Tool Use, specifically read-only Knowledge tool invocation.

---

## META

- **Title:** Tool Use
- **Full name:** Read-Only Knowledge Tool Invocation
- **Home:** L3a · Knowledge tool invocation (read-only)
- **Breadcrumb:** The Pattern Book / L3a · Stateless Retrieval / Knowledge tool invocation / Tool Use
- **Complexity:** 2 of 5. The mechanism is one extra round-trip: the model emits a structured call, you run it, you hand the result back. The cost sits in the boundary judgment (which tools are safe to expose) and in validating the model's arguments, not in the loop itself.
- **Tags:** stateless · grounded · read-only · single-turn
- **Updated:** May 2026

---

## 01 · THE IDEA

One-sentence essence (bold the key terms):

> The model can't look things up on its own, so you hand it a **read-only tool**, it **emits a structured call**, you **run the tool**, and it answers from the **result**.

Pointer: How it works lives in Section 02.

---

## 02 · DATA FLOW

Recipe: animated step-through diagram + step-synced legend.

**Nodes (in order), with sub-labels:**

1. `USER` (sub: *Question*)
2. `MODEL` (sub: *Decides: needs a tool*)
3. `CALL` (sub: *Structured request*)
4. `TOOL` (sub: *Runs, returns data*)
5. `MODEL` (sub: *Answers from result*)
6. `USER` (sub: *Grounded answer*)

Note for the build: the model node appears twice on purpose. The same model is called once to ask for the tool (step 2) and again to answer once the data comes back (step 5). The sub-labels carry that difference.

**Per-step labels (one sentence each):**

- Step 1 of 6: User sends a question the model can't answer from memory alone.
- Step 2 of 6: The model decides a tool is needed and which one to call.
- Step 3 of 6: The model emits a structured call: the tool name plus arguments.
- Step 4 of 6: Your code runs the tool and gets back a result.
- Step 5 of 6: The result is fed back to the model, which now has the fact it lacked.
- Step 6 of 6: The model writes the final answer using the returned data.

**Step-synced legend (one row per non-obvious node):**

- **MODEL (step 2):** the language model reads the question and judges whether its own knowledge is enough or a tool is needed.
- **CALL (step 3):** a structured request the model produces, naming a tool and its arguments, usually as JSON. Your code, not the model, runs it.
- **TOOL (step 4):** a function you expose to the model. Here it is read-only: it fetches or computes, it does not change anything.

**Jargon to define inline (tooltips):**

- *tool*: a function the model can ask to run, like a weather lookup or a database query.
- *structured call*: the model's request to use a tool, formatted as machine-readable fields (tool name plus arguments), not free text.
- *read-only*: the tool fetches or computes but changes no state. No writes, no sends, no clicks.
- *round-trip*: one full exchange where the model asks, the tool answers, and the model continues.
- *schema*: the description you give the model of a tool: its name, what it does, and what arguments it takes.

---

## 03 · WHEN IT WORKS · WHEN IT FAILS

Recipe: query test cards, tap-to-reveal.

**Instruction line (above the cards):** Tap each request. Does it belong on this page?

This section sorts requests three ways, not two. A request is **read-only tool use ✓** when the tool only reads. It is **L5 Action ✗** when the tool writes, sends, books, or clicks: same machinery, but a side effect pushes it to a different layer. And it is **no tool needed ✗** when the model can already answer on its own. The work behind a read and a write can look identical. The side effect is the whole boundary.

Six cards, mixed verdicts:

1. **"What's the weather in Cairo right now?"**
   - Verdict: Read-only tool ✓
   - Reason: A weather API call fetches data and changes nothing, so it stays L3a.

2. **"Book me the 9am flight to Cairo."**
   - Verdict: L5 Action, not this pattern ✗
   - Reason: Booking writes a reservation and charges a card. That side effect moves it to L5 Action.

3. **"How many orders shipped from the Berlin warehouse yesterday?"**
   - Verdict: Read-only tool ✓
   - Reason: A `SELECT` query reads rows and mutates nothing, so it is L3a even though it touches the database.

4. **"Mark order 4471 as refunded."**
   - Verdict: L5 Action, not this pattern ✗
   - Reason: This is an `UPDATE`. It changes a row, so it is a side-effectful action and lives in L5.

5. **"What's 17.5% of 2,480,000?"**
   - Verdict: Read-only tool ✓
   - Reason: A calculator tool computes and returns a number with no side effect, so it is L3a. Handy too, since models slip on long arithmetic.

6. **"Summarize this paragraph for me."**
   - Verdict: No tool needed ✗
   - Reason: The answer lives in the model's own language ability, so a tool just adds a round-trip for nothing.

Tracker: `0 / 6 tested` → `All 6 tested: boundary understood`.

---

## 04 · WORKED EXAMPLE

Recipe: case-file dossier, one specific read-only case. A weather lookup, end to end, shown through the actual artifacts it produces. (Keep this concrete: real call, real JSON, real answer. The abstract sequence lives in Section 02; here we follow one request and look at what it leaves behind.)

**User asks:** "Do I need an umbrella in Cairo today?"

**Step 1: The call the model emits**
The model has no live weather in its training data, so it asks for the tool instead of guessing. What it produces is not prose, it is a request:
`get_weather(city="Cairo", units="metric")`
The model writes this. It does not run it.

**Step 2: The data your code gets back**
Your code runs the function and hits the weather API. The API only reads current conditions, so nothing in the world changes. The raw result:
`{ "condition": "clear", "temp_c": 31, "precip_mm": 0 }`

**Step 3: The result handed back to the model**
You pass that JSON to the model in a follow-up message. The fact it was missing (no rain, 31°C) is now in front of it.

**Step 4: The grounded answer**
The model reads the result and writes a plain-language reply tied to it.

**Answer returned to user:**
> "No umbrella needed. Cairo is clear and 31°C today, with no rain forecast."

Read-only throughout: the model fetched a fact and nothing else. Nothing was booked, written, or sent. Swap `get_weather` for `book_flight` and the same shape becomes an L5 Action.

---

## 05 · CODE SKETCH

Recipe: hover-explainable code. ~10 lines, no imports, no boilerplate. Show the schema, the model emitting a call, executing it, feeding the result back.

```python
# describe the read-only tool to the model
tools = [{"name": "get_weather",
          "params": {"city": "string"}}]

reply = model(messages, tools=tools)        # model may answer or request a tool

if reply.tool_call:                          # model chose to call get_weather
    args = reply.tool_call.args              # {"city": "Cairo"}
    result = get_weather(**args)             # YOUR code runs it; read-only
    messages += [reply, tool_result(result)] # feed the data back to the model
    reply = model(messages)                  # model answers using the result

answer = reply.text
```

**Per-line plain explanation:**

- Line 1 (comment): Marks the tool-description step.
- Lines 2 to 3: Declare the tool's schema: its name and the arguments it takes. This is all the model knows about it.
- Line 4 (blank): (blank line)
- Line 5: Send the conversation plus the tool list to the model. It may answer directly or ask to call a tool.
- Line 6 (blank): (blank line)
- Line 7: Check whether the model requested a tool call instead of answering.
- Line 8: Pull the arguments the model chose, for example the city.
- Line 9: Your code runs the function. It reads weather and changes nothing, which keeps this L3a.
- Line 10: Append the model's request and the tool's result to the conversation.
- Line 11: Ask the model again. Now it has the data and can answer.
- Line 12 (blank): (blank line)
- Line 13: Read out the final text answer.

---

## 06 · TRADEOFFS

Recipe: tradeoff bars + quote. 5-segment bars, green = low, yellow = medium, red = high.

- **Cost, Low (2/5):** One extra model call per tool use. The tool itself (an API or query) is usually cheap next to the model.
- **Latency, Medium (3/5):** Two round-trips minimum, plus however long the tool takes. A slow API is the bottleneck, not the model.
- **Complexity, Medium (3/5):** The loop is simple. The real work is validating the model's arguments and keeping exposed tools strictly read-only.

Quotable line:

> *Hand the model a key it can only read with. The moment the tool writes, you're not in L3a anymore.*

---

## TERMS

- **Tool:** a function you let the model ask to run, such as a weather lookup, a calculator, or a database read.
- **Structured call:** the model's request to use a tool, formatted as machine-readable fields (a tool name and arguments), not free text.
- **Schema:** the description you give the model of a tool: its name, purpose, and the arguments it accepts.
- **Read-only:** a tool that fetches or computes but changes no state. No writes, no sends, no bookings, no clicks.
- **Side-effectful:** an operation that changes the world: an `INSERT/UPDATE/DELETE`, a sent email, a booking, a form submission. These are L5 Action, not L3a.
- **Round-trip:** one full exchange. The model asks for a tool, your code answers, and the model continues.
- **Function calling:** the practical mechanism (offered by OpenAI, Anthropic, and others) where you declare tool schemas and the model returns a structured request to call one. It is the common way this pattern is implemented. Your code still runs the tool.
- **L5 Action:** the orchestration layer for side-effectful tool calls. Same machinery as L3a, but the tool changes state.

---

## REFERENCES

1. Schick et al. (2023). *"Toolformer: Language Models Can Teach Themselves to Use Tools."* arXiv:2302.04761. Shows a model learning when to call tools (calculator, search, QA) and how to insert the calls itself.
2. OpenAI. *"Function calling"* (API documentation, no paper). Describes the practical mechanism: you declare tool schemas, the model returns a structured call, your code executes it and returns the result. The widely used implementation of this pattern.

> Accuracy note: the read-only vs side-effectful split is The Pattern Book's own taxonomy boundary (taxonomy.md, L3a filing rule), not a claim from these sources. The sources cover the tool-calling mechanism. The L3a/L5 line is editorial.
