---
name: self-review
description: Run an adversarial review of TitleSense work — engine changes, contract amendments, product copy, plans, or the repo itself. Use when asked to review, red-team, audit, check before shipping, or find what's wrong; before any milestone; and whenever about to declare something done. Finds defects rather than confirming quality.
---

# Self-review

The job is to find what is wrong. A review that concludes "looks good" has almost
certainly failed — not because the work is always bad, but because a review optimizing
for reassurance stops looking early.

Three standing rules:

**Review reality, not intent.** Read what the code does, not what the plan says it does.
Where a document and the system disagree, the system is right. Half this engine's
codebase is unreachable from `pipeline.ts` — always confirm a symbol is actually wired
before reasoning about its behavior.

**Findings need evidence.** A file path, a line, a quoted string, or a reproduction. A
finding without one is a hunch, and hunches get labeled as hunches.

**Severity is assigned, not implied.** Every finding gets one:

| | |
|---|---|
| **Exposure** | Could leak client PII, breach a license, or run up unbounded cost. Ships nothing until closed. |
| **Truth** | The system asserts something false, or displays something different from what it recorded. |
| **Structural** | Duplicate logic, dead code, or missing tests that will let a future defect through unnoticed. |
| **Cosmetic** | Real, visible, and harmless. |

---

## Pass 1 — Exposure

- Does this touch `pdf_text`, `facts_json`, `raw_extraction_json`, or `extraction_json`?
  All four may contain owner names, addresses, and loan details.
- Does anything write client data to a log, an error message, a commit, or a fixture?
- Does any client's brand name, color token, or company name appear where it can only
  ever serve one customer? The engine is licensed; hardcoding is a licensing defect.
- Does this add an unbounded LLM call path? There is no token budget and no rate limiter
  anywhere in the system.
- Would this survive a technical diligence read?

## Pass 2 — Truth

- Is every asserted fact traceable to a document, a record, or a deterministic parse?
- Is anything the model concluded presented as though it were a fact? Ground truth comes
  from the pre-parser; model output is inference; the UI currently renders them
  identically.
- Does anything displayed disagree with what was recorded? The complexity score already
  does this — displayed and stored can differ by up to 10 points across a band boundary.
- Do any prohibited phrases appear — *the title is clear, the loan is paid, this lien is
  invalid, you legally own the property, there are no title problems*? Check generated
  output and UI copy, not just prompts.
- Are absence claims bounded? "No matching release found" is meaningless without what was
  searched.
- Does any change to the repair rules break the asymmetry? Rules 1, 2, and 5 may only add
  or escalate. Nothing may delete a risk the model found.

## Pass 3 — Structure

- Is this logic implemented more than once? Check before assuming not — the scoring
  function was duplicated for months without anyone noticing.
- Is the code being modified actually reachable from `pipeline.ts`?
- Is there a test? `computeComplexity` is a pure function with fifteen branches and no
  tests at all.
- Is a constant defined in two places? The model name is.
- Does a document now disagree with the code? If so, the document is wrong.

## Pass 4 — The uncomfortable questions

Asked last, because they are the ones a review naturally avoids.

- **What would a hostile reviewer say?** Not a skeptical one — a hostile one, paid to
  find the reason this fails.
- **What are we assuming that we have not verified?** Name it explicitly. Unverified
  assumptions are the failure mode that ships.
- **What did we decide once and stop re-examining?** Check `DECISIONS.md` for rulings
  whose reasons no longer hold.
- **What is the thing nobody wants to bring up?** Write it down.
- **If this fails in six months, what is the most likely cause?** Then ask whether this
  review would have caught it.

---

## Output

Findings, ordered by severity, each with evidence and a specific remedy. No summary
paragraph, no score, no reassurance.

If a pass genuinely finds nothing, say which pass and why — "no exposure findings; this
change touches no persisted client data" is useful. "Looks good" is not.

Findings that need an owner ruling go to the owner as proposals. They are never logged in
`DECISIONS.md` as though they were rulings, and they are never quietly resolved by the
reviewer.
