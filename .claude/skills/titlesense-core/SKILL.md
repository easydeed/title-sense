---
name: titlesense-core
description: Answer any question about TitleSense Core, the prelim analysis engine living in the TD Hub codebase. Covers the two-call Anthropic pipeline, the deterministic pre-parser, all six guardrail layers, the extraction JSON schema, prelim_analyses persistence, the complexity score, kill switches, cron and webhook triggers, and the results UI. Use whenever prelim analysis, prelim summary, complexity score, pre-parser, analyzePrelim, prelim_analyses, or the engine's guardrails come up, and before proposing any change to prelim processing.
---

# TitleSense Core — the prelim analysis engine

Full reference: `reference/titlesense-core/ENGINE_CAPTURE.md` (~1000 lines).
**Read-only.** That file is a capture; it is never edited to describe intent, only
re-captured from reality.

**The ownership boundary, and it is the most important thing on this page.**
**TitleSense** owns and built the engine — TitleSense is the founder of this product.
**TD Hub is a client portal owned by Pacific Coast Title**, TitleSense's first client.
The engine currently runs *inside* TD Hub. So every file path, database table, and
component in the capture is a location inside a **client's** system, not ours. Never
describe TD Hub as our codebase, and never assume a change there is ours to make
unilaterally.

**Naming, and this is a hard rule.** The engine is **TitleSense Core** — owner-built,
ours to license. The client-facing assistant name deployed for Pacific Coast Title is a
*brand instance*, not the product, and it must not appear in anything we write: not in
prose, not in plans, not in commit messages. It survives only inside the capture and the
code, as ~50 literal identifiers (`src/lib/tessa/`, `TESSA_AUTO_ANALYSIS_ENABLED`,
`tessa_prelim_enabled`, `TessaPrelimResultsModal.tsx`, `operation = 'tessa_extract'`).
Read those as TitleSense Core internals under a legacy namespace. Use them when naming
a file or symbol; never as the name of the product.

## Shape of the thing

Two Anthropic calls (`claude-sonnet-4-20250514`), wrapped in determinism:

```
Trigger (webhook | cron | manual)
  └─ isTessaTriggerAllowed()      env kill switch AND admin DB flag
     └─ analyzePrelim()
        1. Download PDF           S3 by storageKey, or HTTP (60s timeout)
        2. extractPdfText()       pdf-parse; reject <100 chars; cap 50,000
        3. computeFacts()         regex pre-parser → PrelimFacts  ← GROUND TRUTH
        4. callExtraction()       Claude #1 → strict JSON, temperature 0
        5. validateAndRepairExtraction()   inject facts, overwrite taxes
        6. callSummary()          Claude #2 → markdown, temperature 0.1
        7. computeComplexity()    deterministic 0–100 + reasons
        8. persist status=complete
```

**The central design decision: the LLM is never the sole source of truth.** The
pre-parser runs before the model and its output is injected back over the model's
answer afterward. Taxes are overwritten wholesale whenever the pre-parser found any.

The summary call receives the **repaired extraction JSON, not the PDF text** — so it
cannot reintroduce a hallucination the repair step removed.

## Guardrail asymmetry (important)

Repair rules 1, 2, and 5 can only **add or escalate** — inject missing requirements,
inject missing deeds of trust, force `foreclosure_detected` true. Rules 3 and 4
**replace** (taxes, tax defaults). Nothing in repair can quietly delete a risk the
model found. Preserve that asymmetry in any change.

## Three persisted layers — this is amber/violet, already separated

| Column | What it is | Doctrine |
|---|---|---|
| `facts_json` | Deterministic pre-parser output | Ground truth — fact |
| `raw_extraction_json` | LLM output *before* guardrails | Audit trail |
| `extraction_json` | LLM output *after* guardrails — what the UI renders | Mixed |

Diffing raw against repaired is how guardrail effectiveness is measured. Note the gap:
the UI renders `extraction_json` without marking which values came from the
deterministic parser, so ground truth and model inference look identical on screen.

## Traps

**The complexity score is implemented twice.** `complexity.ts` produces the stored
value; `TessaComplexityScore.tsx` has its own byte-for-byte copy for the gauge. The
modal never passes `facts`, so rules 14–15 never fire in the UI — the displayed score
can read up to 10 points below the stored one and cross a band boundary. Never "fix"
one copy alone.

**No runtime schema validation.** `stripMarkdownFences()` then `JSON.parse()` then a
TypeScript cast. An out-of-enum `severity` flows straight to the database and the UI.

**`pdf_text` holds the entire prelim** — owner names, addresses, loan details. No
redaction, no retention policy, no truncation. Any PII or retention work must account
for this column first.

**Half the code is dormant.** Four of eight prompts are never called
(`TESSA_SYSTEM_PROMPT`, `AGENT_MODE_SYSTEM_PROMPT`, `buildPrelimAnalysisPrompt`, the
markdown guardrail suite ~400 lines). `TessaCheatSheet`, `TessaAgentToggle`,
`TessaChatWidget` are referenced only by the barrel. **Dormant is not the same as dead** —
the agent-mode prompt and cheat sheet are the v1 product, awaiting wiring; the markdown
suite and the orphaned routes are genuinely dead. See `ROADMAP.md` Track A-H. `TessaContext.tsx` and
`usePrelimAnalysis.ts` call routes that **do not exist**. Always check whether a symbol
is actually reachable from `pipeline.ts` before reasoning about its behavior.

**Model name is hardcoded twice** — `MODEL` in `ai-client.ts` plus string literals in
`pipeline.ts`. An upgrade requires changing both.

## Kill switches — `either-off = off`

`effective = env_allows AND tessa_prelim_enabled`. Env flags default **allow** and are
the nuclear backstop; the DB flag defaults **deny**, so a fresh deploy ships dark.
`assertTessaLlmAllowed()` is checked twice on purpose — once before any row mutation,
once immediately before each Anthropic call.

There is **no cost cap** — no token budget, no daily ceiling, no rate limiter. Control
is the on/off switches plus a 5-per-cycle cron batch. The resume runbook warns
explicitly about a backlog flood on the first cycles after unpause.

## When proposing changes

1. Say whether the symbol is **active or dormant** — half the codebase isn't wired.
2. Respect the add-or-escalate asymmetry in repair.
3. Anything touching the score must address both copies.
4. Anything touching `pdf_text` is a PII question first, a code question second.
5. Changes currently land in **TD Hub — the client's portal**, not here. This repo holds
   the capture and the contract. Extraction into a TitleSense-owned codebase is an open
   architectural question; see `ROADMAP.md`.
6. The engine is multi-tenant-in-waiting: brand tokens (`#F26B2B`, `#1B2A4A`), the
   assistant name, and the company name are currently hardcoded. Anything touching
   those is licensing work, not styling work.

The reference's own reading order for the code: `pipeline.ts` → `analysis-config.ts`
→ `tessa-pre-parser.ts` → `tessa-prompts.ts` → `tessa-guardrails.ts` (line 521 on)
→ `complexity.ts` → `schema/tessa.ts` → `TessaPrelimResultsModal.tsx`.
