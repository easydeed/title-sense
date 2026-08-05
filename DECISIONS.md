# Decisions

Append-only. Newest at the bottom. One entry per ruling, dated, with the reason preserved — the reason is what stops a decision being quietly reversed by someone who only remembers the conclusion.

Format: `D-NNN | date | decision | why | affects`

Entries are never edited or deleted. A reversal is a **new entry** that names the one it overturns.

---

**D-001 | 2026-08-04 | The interpretation layer belongs to TitleSense.**
The chain-of-title opportunity document was written before the two-product split existed and reads as though DeedPro would build the interpretation layer. It won't. Its entire product spec — Ownership Timeline, Loan & Lien Tracker, Items Needing Review, document families, confidence tiers, cautious-language rules — is the TitleSense build sheet and is housed here. DeedPro consumes findings as violet proposals and builds no interpretation.
*Affects:* `docs/PRODUCT_SPEC.md` (the relabeled document), `docs/INTEGRATION_PLAN.md`

**D-002 | 2026-08-04 | Two intake paths, both real, sequenced.**
The prelim product and the TitlePoint chain product are not competing visions; they are v1 and v2 of one product. v1 = prelim PDF/email intake → plain-English summary, white-labeled, PCT as design partner and first paying customer. v2 = the TitlePoint chain layer. v1 satisfies the integration plan's "standalone value first" requirement with a real customer attached; v2 is the moat and comes second.
*Affects:* `ROADMAP.md`, `docs/H1_CONTRACT.md` §6

**D-003 | 2026-08-04 | `prelim_data` added as a v1 finding type.**
Likely the first payload DeedPro consumes: its T-6 prelim import parses PDFs today and would prefer structured output, with its parser demoted to third-party-paper fallback.
*Affects:* `docs/H1_CONTRACT.md` §6.1

**D-004 | 2026-08-04 | TitlePoint ToS review is step 0; credential rotation is the owner's errand.**
No pipeline architecture before the licensing question is answered — a usage restriction discovered late invalidates architecture, not just copy. Old TitlePoint credentials appeared in git history and must be rotated before anything is built against them.
*Affects:* `ROADMAP.md` (owner errands)

**D-005 | 2026-08-04 | Step 0's first question, adopted verbatim:** *are TitlePoint-derived results licensed for display to non-title-professional end users?*
It decides whether the consumer-facing Property Story exists at all. Every other ToS question is secondary to it.
*Affects:* `ROADMAP.md`, `docs/H1_CONTRACT.md` §10

**D-006 | 2026-08-04 | Draft H1 immediately, with all TitlePoint leaf fields marked `pending: live_capture`.**
The wait-or-draft dilemma was false. Envelope semantics are what DeedPro's DX0 designs against; leaf shapes are the only fiction risk, and the `pending` marker eliminates it. H1 v1 therefore contains no invented field while still unblocking the other side.
*Affects:* `docs/H1_CONTRACT.md` §0, §9

**D-007 | 2026-08-04 | Five open questions resolved.**
(a) H3 loop closure is **on-demand re-search**, not standing monitoring — monitoring is a v2.1 decision made *after* the ToS answer, since it implies exactly the query volume under review. (b) Party-name normalization lives in TitleSense; raw and normalized are both always emitted. (c) The Property Story contract is the **document model, not the HTML** — each side renders its own chrome. (d) Finding types ordered by contractibility, not product appeal: `current_vesting` ships last because shipping it early means shipping the mixed-content split before the split has been tested. (e) Appendix G is **quarantined** until a real parser exists; the ~6,779 rows from the first structural pass drifted and are not used.
*Affects:* `docs/H1_CONTRACT.md` §6, §7.1, `docs/INTEGRATION_PLAN.md` §6

**D-008 | 2026-08-04 | v2 search-input construction starts with PCT's working counties only.**
California legal formats are per-county — roughly 2,800 lines of county-specific map codes and required fields. There is no single "California implementation."
*Affects:* `docs/H1_CONTRACT.md` §10

**D-009 | 2026-08-04 | H1 ratified at v1.1, with finding supersession.**
A revised finding is a new finding carrying `supersedes`; content is never mutated under an existing id; `finding_id` is the idempotency/dedup key. DeedPro's officer decisions made against a superseded finding stand as recorded, and supersession surfaces as information, never as silent revision. This is DeedPro's T-5 document-lineage law applied to the wire.
*Affects:* `docs/H1_CONTRACT.md` §3.2

**D-010 | 2026-08-04 | The prohibited-language list is enforced at both ends of the wire.**
TitleSense does not emit it; DeedPro does not render it. A phrase that slips one gate still fails the other.
*Affects:* `docs/H1_CONTRACT.md` §8

**D-011 | 2026-08-04 | Three supersession sub-rules authored TitleSense-side, open to DeedPro amendment.**
(a) Successors are emitted only for findings whose content actually changed — otherwise one amended prelim fires "review the successor" on everything and officers learn to ignore the notice. (b) `supersession_reason` distinguishes *the world changed* (`amended_source_document`, `re_search`) from *we were wrong* (`producer_correction`). (c) Withdrawal is a supersession whose successor asserts nothing, and never means "this was never true."
*Affects:* `docs/H1_CONTRACT.md` §3.2.2–§3.2.4
