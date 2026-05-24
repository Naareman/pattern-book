# Few-Shot Prompting — Pattern Content

> Design-agnostic content for one Pattern Book page. Structured by section so it can drop into any page design. Voice and depth calibrated to `guideline.md` and `example/rag.html`.

---

## META

- **Title:** Few-Shot
- **Full name:** Few-Shot Prompting
- **Breadcrumb:** The Pattern Book / L4 · Single-Call Control / Input Shape / Few-Shot
- **Home:** L4 · Input Shape
- **Complexity:** 1 of 5. The mechanism is one prompt with examples in it. No retrieval, no extra calls, no training. The only hard part is choosing and ordering good examples, and that is judgment, not engineering.
- **Tags:** in-context · single-call · prompt-only
- **Updated:** May 2026

---

## 01 · THE IDEA

**Essence (one sentence, key terms bolded):**

Show the model a few **labeled examples** of the task right in the **prompt**, and it copies the pattern for your new input: no training, just **in-context learning**.

*Pointer:* How it works lives in Section 02.

---

## 02 · DATA FLOW

This is a single-call input-shaping pattern. The diagram is thin on purpose. Do not pad it.

**Diagram (3 nodes):**

```
USER  →  MODEL  →  ANSWER
(examples + new input)   (reads the pattern)   (same shape as examples)
```

**Step labels (for the step-through):**

- Step 1 of 3: You write a few solved examples, then your new input, in one prompt.
- Step 2 of 3: The model reads the examples and infers the task from them.
- Step 3 of 3: It answers the new input in the same shape as the examples.

**Step-synced legend (only nodes that need a definition):**

- **PROMPT** — the full text you send the model. Here it holds the examples plus the new input.
- **MODEL** — the language model. It changes nothing internally; it just conditions its next output on the examples it can see.

**Jargon to define inline (Recipe 7 tooltips):**

- **shot** — one labeled example in the prompt (one input paired with its correct output).
- **few-shot** — a prompt with a handful of shots, usually 2 to 10.
- **zero-shot** — a prompt with instructions but no examples.
- **in-context learning** — the model adapts to a task from examples in the prompt alone, without updating its weights.

---

## 03 · WHEN IT WORKS · WHEN IT FAILS

Query test cards. Tap to reveal verdict + one-sentence reason. Mix is 3 help / 3 hurt-or-waste.

1. **"Classify each support ticket as billing, bug, or feature-request."**
   - Verdict: Handles it. (Green)
   - Reason: Three labeled tickets teach the exact label set and tone better than any description.

2. **"Extract every date from this contract as YYYY-MM-DD."**
   - Verdict: Handles it. (Green)
   - Reason: Two examples pin the output format so the model stops returning "March 3rd" and "03/03".

3. **"Rewrite this sentence in our brand voice."**
   - Verdict: Handles it. (Green)
   - Reason: Voice is hard to specify but easy to demonstrate; two before/after pairs carry the style.

4. **"Summarize this 40-page deposition."**
   - Verdict: Wastes tokens. (Red)
   - Reason: One clear instruction does the job; example summaries just burn context the document needs.

5. **"Score these 2,000 reviews; label set has 30 niche categories."**
   - Verdict: Fails. (Red)
   - Reason: A few shots cannot cover 30 categories, and they bias the model toward whichever ones you showed.

6. **"Solve this multi-step word problem and explain each step."**
   - Verdict: Wrong tool. (Red)
   - Reason: This needs reasoning, not pattern-matching; Chain-of-Thought fits, plain example answers do not.

**Tracker:** 0 / 6 tested → ✓ All 6 tested.

---

## 04 · WORKED EXAMPLE

One specific real case end to end. A formatting task where 2 examples fix the output. Not the abstract flow.

**Case:** A team pipes user-typed addresses into a shipping API that demands a strict JSON shape. Zero-shot, the model keeps inventing keys and merging the unit into the street line. Two examples fix it.

**User input (the new one to handle):**
> "send it to apt 4b, 12 oak street, austin tx 78701"

**Step 1 — Write two solved examples.**
The prompt opens with two messy-in, clean-out pairs:
```
In:  "742 evergreen terrace, springfield, or 97403"
Out: {"street":"742 Evergreen Terrace","unit":null,"city":"Springfield","state":"OR","zip":"97403"}

In:  "ste 200, 1 infinite loop, cupertino ca 95014"
Out: {"street":"1 Infinite Loop","unit":"Ste 200","city":"Cupertino","state":"CA","zip":"95014"}
```

**Step 2 — Append the new input.**
The real address is added in the same `In:` line format, with an empty `Out:` for the model to fill.

**Step 3 — Model infers the rules from the examples.**
From two shots it picks up: title-case the street, split the unit into its own field, uppercase the two-letter state, keep zip as a string. None of this was stated in words.

**Step 4 — Model returns the new output in the learned shape.**

**Final answer:**
```
{"street":"12 Oak Street","unit":"Apt 4B","city":"Austin","state":"TX","zip":"78701"}
```
The unit is split out, the state is uppercase, the keys match exactly. Two examples did what a paragraph of instructions kept getting wrong.

---

## 05 · CODE SKETCH

~10 lines, no imports, hover-explainable. Builds a prompt from labeled examples plus the new input.

```python
examples = [                                    # a few solved cases
  ("742 evergreen terrace, springfield or 97403", '{"street":"742 Evergreen Terrace",...}'),
  ("ste 200, 1 infinite loop, cupertino ca 95014", '{"street":"1 Infinite Loop",...}'),
]

prompt = "Format each address as JSON.\n\n"      # one line of instruction
for raw, clean in examples:                      # turn each example into In/Out text
    prompt += f"In: {raw}\nOut: {clean}\n\n"

prompt += f"In: {new_address}\nOut:"             # append the new input, leave Out blank

answer = model(prompt)                           # model fills Out in the shape it just saw
```

**Per-line explanation (for hover):**

- Line 1: A short list of labeled examples, each a (messy input, clean output) pair.
- Lines 2 to 3: The two shots that teach the format. The "..." stands in for the full JSON.
- Line 5: One plain instruction. With good examples, the instruction can be this short.
- Line 6: Loop over the examples to render them as text.
- Line 7: Format each as `In: ... / Out: ...` so the model sees a clear, repeating shape.
- Line 9: Add the new input in the same format, with `Out:` empty for the model to complete.
- Line 11: Send the whole thing in one call; the model continues the pattern.

---

## 06 · TRADEOFFS

Three bars (green = low, yellow = medium, red = high) plus one quotable line.

- **Cost:** Low-to-medium (2 of 5). One call, but the examples ride along in every request, so each call costs more tokens than zero-shot.
- **Latency:** Low (1 of 5). Still a single round-trip; the extra example tokens add a little prefill time, nothing structural.
- **Complexity:** Low (1 of 5). No retrieval, no training, no extra calls. The judgment is which examples to show and in what order.

**Quotable line:**
> *One call, no training. The examples teach the task, but every call pays to carry them.*

---

## TERMS

Inline tooltips. Each one sentence, plain English.

- **shot** — one labeled example in the prompt: an input paired with its correct output.
- **few-shot** — a prompt that includes a handful of shots, usually 2 to 10.
- **zero-shot** — a prompt with instructions only and no examples.
- **in-context learning** — the model adapts to a task from examples in the prompt, without changing its weights.
- **prompt** — the full text sent to the model in one call.
- **token** — a chunk of text the model reads and bills by; examples add tokens to every call.
- **label** — the correct output attached to an example input, the part the model learns to reproduce.

---

## REFERENCES

Real papers only.

1. Brown et al. (2020). *"Language Models are Few-Shot Learners."* The GPT-3 paper; showed large models perform new tasks from a few in-context examples with no weight updates.
2. Lu et al. (2022). *"Fantastically Ordered Prompts and Where to Find Them: Overcoming Few-Shot Prompt Order Sensitivity."* Showed the order of examples alone can swing accuracy from near-chance to near state-of-the-art, which is why selection and ordering matter.
