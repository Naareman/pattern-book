# The Pattern Book

> An interactive, visual reference for AI system architecture patterns. Built for engineers learning, leveling up, or making architectural decisions.

The Pattern Book teaches AI patterns the way a children's museum teaches physics: every page is a thing you operate, not a thing you read. Drag, click, step, hover — the rule reveals itself through the interaction.

## Repo Layout

```
pattern-book/
├── README.md                    ← you are here
├── guideline.md                 ← how to write a pattern page
├── taxonomy.md                  ← where every pattern lives (snapshot from Notion)
├── anchor-list.md               ← the ~25 patterns to write first, in order
├── CHANGELOG.md                 ← versioning starts clean
│
├── templates/
│   ├── pattern.md               ← the template every pattern page fills in
│   └── section-recipes.md       ← interactive section types and when to use them
│
├── example/
│   └── rag.html                 ← the calibrated example. This is what "good" looks like.
│
├── patterns/                    ← one file per pattern (L3a-rag.html, L4-cot.html, ...)
├── layers/                      ← one file per layer chapter (L0.html, L3a.html, ...)
└── recipes/                     ← Part II compositions (coding-agent.html, ...)
```

## Where to Start

If you're writing your first pattern page:

1. **Read `guideline.md`.** Especially the One Principle section. Everything else follows from it.
2. **Open `example/rag.html` in a browser.** Click everything. This is the calibration. Match this quality.
3. **Pick from `anchor-list.md`.** The list is in priority order. Take the first unwritten one.
4. **Open `templates/pattern.md`.** Fill it in.
5. **Match interactive sections to `templates/section-recipes.md`.** Don't invent new interaction types per page; pick from the recipes that fit your pattern.
6. **Check `taxonomy.md`** to confirm the home layer, sub-axis, and any filing rules that affect your pattern.

## The Two Things This Project Is Not

- **Not a textbook.** Long-form prose is failure. If you're writing more than three sentences in a section, something has gone wrong.
- **Not a vendor blog.** No marketing language, no "powerful," no "cutting-edge." Tradeoffs always. Pick a position.

## The Two Things This Project Is

- **A reference.** A reader who knows what they're looking for finds the answer fast and leaves.
- **A teacher.** A reader who doesn't know what they're looking for learns by operating the page.

Both audiences served by the same artifact. The trick is that the teaching is in the interaction, not in additional explanation.
