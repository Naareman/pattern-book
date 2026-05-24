# LLM-as-Judge

> Content for one Pattern Book page. Design-agnostic structured markdown. Drop into the page template (`templates/pattern.md`) and the recipes in `templates/section-recipes.md`.

---

## META

- **Title (short):** LLM-as-Judge
- **Full name:** Large Language Model as Evaluator
- **Home:** L2 · Verify (Verification composition: a separate model call scores or grades another model's output)
- **Breadcrumb:** Pattern Book / L2 · Structural Composition / Verify / LLM-as-Judge
- **Complexity:** 2 of 5. The mechanism is one extra model call. The hard part is writing a rubric that resists the judge's biases.
- **Tags:** two-call · scored · rubric-driven
- **Updated:** May 2026

> Filing note for accuracy: this is L2 because the judgment happens in a **separate evaluator call**, often a different model. Verification done inside the same single call to the same model is L4 (Self-Verification). The boundary is call count, not intent.

---

## 01 · THE IDEA

**Essence (one sentence, key terms bolded):**

A first model **writes an answer**; a **second model call grades it** against a **rubric**, returning a score or a verdict instead of a new answer.

End-of-section pointer: How it works lives in Section 02.

---

## 02 · DATA FLOW

Not single-call. There are two distinct model calls, so the diagram earns five nodes.

**Diagram nodes (in order, with sub-labels):**

1. `OUTPUT` · sub: *answer to grade*
2. `RUBRIC` · sub: *+ criteria*
3. `JUDGE` · sub: *2nd model call*
4. `SCORE` · sub: *number or verdict*
5. `GATE` · sub: *keep / redo*

**One-sentence step labels (for the step counter, 5 steps):**

1. Take the answer a first model already produced.
2. Attach the rubric: the exact criteria the answer must meet.
3. A separate model call reads both and judges the answer.
4. The judge returns a score, a label, or a written critique.
5. A simple rule acts on the score: accept, reject, or send back for a redo.

**Step-synced legend rows (nodes a non-expert won't recognize):**

- **RUBRIC:** the written standard the judge grades against (for example: "accurate, cites a source, no rudeness," each scored 1 to 5).
- **JUDGE:** a second model call whose only job is to evaluate, not to rewrite the answer.
- **SCORE:** the judge's output: a number, a pass/fail label, or a short critique, not a replacement answer.
- **GATE:** the rule that turns the score into an action (accept above a threshold, otherwise reject or retry).

(USER-style and plain nodes like `OUTPUT` need no legend row.)

**Jargon needing inline tooltips (with definitions):**

- **rubric:** A written list of criteria the judge scores against, so grading is consistent instead of a vibe.
- **judge call / evaluator call:** A second, separate model call that grades an answer rather than producing one.
- **ground truth:** The known-correct answer or trusted source you would check against; the judge often does not have it.
- **pairwise comparison:** Judging by ranking two answers against each other instead of scoring one in isolation.
- **position bias:** A judge's tendency to favor whichever answer it sees first (or last) when comparing two.
- **verbosity bias:** A judge's tendency to score longer answers higher even when they are not better.
- **self-preference bias:** A judge's tendency to rate text from its own model family more highly (also called self-enhancement bias).

---

## 03 · WHEN IT WORKS · WHEN IT FAILS

Clean boundary here: judging works when "good" is well-defined and checkable from the answer plus rubric. It fails when the criterion is subjective, needs ground truth the judge lacks, or triggers a known bias. Six tap-to-reveal cards (3 positive, 3 negative). Reader predicts pass or fail, then taps.

**Card 1 · Handles it ✓**
"Does this support reply stay polite and follow our refund rules?"
Tone and rule-following are visible in the text, so a rubric can score them directly.

**Card 2 · Handles it ✓**
"Which of these two draft summaries is more faithful to the source?"
With the source attached, pairwise comparison is where judges agree most with humans. Run it twice with the order swapped to cancel position bias.

**Card 3 · Handles it ✓**
"Rate this answer 1 to 5 for whether it actually answered the question asked."
Relevance is judgeable from the question and answer alone, no outside facts needed.

**Card 4 · Fails ✗**
"Is this medical dosage recommendation factually correct?"
The judge has no ground truth to check against, so it may confidently confirm a wrong but fluent answer.

**Card 5 · Fails ✗**
"Pick the funnier of these two jokes."
Humor is subjective with no stable rubric, so the score reflects the judge's taste, not quality.

**Card 6 · Fails ✗**
"Score this 400-word answer against this 80-word one for helpfulness."
Verbosity bias pushes the judge toward the longer answer regardless of which is more helpful.

Tracker: `0 / 6 tested` → `✓ All 6 tested: pattern understood`.

---

## 04 · WORKED EXAMPLE

One specific case: grading a chatbot reply in a refund-support pipeline. This is a real evaluation pass, not the abstract flow restaged.

**User context shown at top of dossier:**
Customer message: *"You charged me twice for the same order. I want both refunded today."*
Generated reply (from the first model): *"I've refunded both charges. You'll see the money back in 3 to 5 business days. Sorry for the trouble."*

**Step 1 · Load the answer to grade.**
The pipeline takes the reply the support model just wrote. It is not regenerated here; it is the thing under test.

**Step 2 · Attach the rubric.**
Three criteria, each scored 1 to 5:
- Accuracy: does it claim only actions the system actually performed?
- Policy: does it avoid promising timelines we don't guarantee?
- Tone: is it empathetic and professional?

**Step 3 · The judge call runs.**
A separate model call receives the customer message, the reply, and the rubric. It is told to score each criterion and explain in one line.

**Step 4 · The judge returns scores and a critique.**
- Accuracy: 2 of 5. *"The reply claims both charges were refunded, but the tool log shows only one refund was issued."*
- Policy: 4 of 5.
- Tone: 5 of 5.

**Step 5 · The gate acts on the score.**
Rule: any criterion below 3 blocks the reply. Accuracy scored 2, so the reply is held and routed back for a redo before it reaches the customer.

**Final result:**
The reply is blocked, not sent. The judge caught a claim of an action that never happened, which a tone-only check would have missed. The redo will reconcile the refund claim with the actual tool log.

---

## 05 · CODE SKETCH

~10 lines, no imports, English-like names. One explanation per line.

```
answer = first_model(customer_message)

rubric = "Score 1-5 each: accuracy, policy, tone. Explain briefly."

judge_prompt = f"Message: {customer_message}\nReply: {answer}\n{rubric}"

verdict = judge_model(judge_prompt)

if verdict.accuracy < 3:
    return redo(answer)
return answer
```

**Per-line explanations:**

1. `answer = first_model(customer_message)`: The first model writes the reply we are about to grade.
2. *(blank line)*: Spacing.
3. `rubric = "..."`: The rubric is plain text: the exact criteria the judge must score.
4. *(blank line)*: Spacing.
5. `judge_prompt = f"..."`: Build one prompt holding the original message, the reply, and the rubric.
6. *(blank line)*: Spacing.
7. `verdict = judge_model(judge_prompt)`: A second, separate model call grades the reply; this call is what makes it L2.
8. *(blank line)*: Spacing.
9. `if verdict.accuracy < 3:`: The gate checks whether the weakest required criterion failed.
10. `return redo(answer)`: If it failed, send the answer back instead of shipping it.
11. `return answer`: Otherwise the reply passes and is returned.

---

## 06 · TRADEOFFS

- **Cost: Medium.** Every judged answer costs a second model call; a strong judge model can cost as much as the generator.
- **Latency: Medium.** The judge runs after the answer, so it adds one full round trip in sequence.
- **Complexity: Medium.** The call is simple; writing a bias-resistant rubric and tuning the gate threshold is the real work.

**Quotable summary line:**
*A second opinion is cheap insurance, but the judge has biases too: write the rubric, swap the order, never assume it knows the truth.*

---

## TERMS

- **rubric:** A written list of criteria the judge scores against, so grading is consistent instead of a vibe.
- **judge call (evaluator call):** A second, separate model call that grades an answer rather than producing one.
- **ground truth:** The known-correct answer or trusted source you would check against; the judge often does not have it.
- **verdict / score:** The judge's output: a number, a pass/fail label, or a short critique, not a rewritten answer.
- **gate:** The rule that turns a score into an action, such as accept above a threshold or otherwise retry.
- **pairwise comparison:** Judging by ranking two answers against each other instead of scoring one in isolation.
- **position bias:** A judge's tendency to favor whichever answer it sees first (or last) when comparing two.
- **verbosity bias:** A judge's tendency to rate longer answers higher even when they are not better.
- **self-preference bias:** A judge's tendency to rate text from its own model family more highly (also called self-enhancement bias).

---

## REFERENCES

- Zheng, L., Chiang, W.-L., Sheng, Y., et al. (2023). *Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena.*
- Liu, Y., Iter, D., Xu, Y., et al. (2023). *G-Eval: NLG Evaluation Using GPT-4 with Better Human Alignment.*
</content>
</invoke>
