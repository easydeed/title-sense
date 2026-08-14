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


**D-012 | 2026-08-04 | TitlePoint documentation stays in the repo; no redistribution concern.**
The TitlePoint documentation site is publicly accessible, so retaining the captured package in `reference/` and using it internally is settled — it does not need to be raised with TitlePoint and does not gate anything. Owner ruling; owner has the domain knowledge here.
*What remains open, separately:* QA2-vs-production **parity**, which is a technical accuracy question, not a licensing one. Every method signature, parameter name, and result container in `docs/H1_CONTRACT.md` rests on QA2 pages. It stays on the ToS errand list.
*Affects:* `reference/README.md`, `ROADMAP.md`

**D-013 | 2026-08-05 | The engine is owner-built. Ownership settled.**
The prelim analysis pipeline in TD Hub was built by the owner. It is TitleSense's to license and white-label. This closes what had been the highest open errand and unblocks all of Track A.
*Affects:* `ROADMAP.md`, `reference/titlesense-core/`

**D-014 | 2026-08-05 | The engine is named TitleSense Core. The client assistant name is a brand instance and appears nowhere in our writing.**
The name given to Pacific Coast Title's deployment is one client's assistant, not the product. The product is **TitleSense Core** — the licensed engine. The client brand name is eradicated from all prose we author: ROADMAP, skills, contract, plans, commit messages.
*The one exception, deliberate:* `reference/titlesense-core/ENGINE_CAPTURE.md` retains it, because ~50 occurrences are literal code identifiers (`src/lib/tessa/`, `TESSA_AUTO_ANALYSIS_ENABLED`, `tessa_prelim_enabled`, `TessaPrelimResultsModal.tsx`, `operation = 'tessa_extract'`). Scrubbing them would break the capture's correspondence with the code and destroy its only value. The capture's header carries the translation instead. Renaming the code namespace is a TD Hub task, not a documentation task.
*Consequence:* brand tokens, assistant name, and company name are currently hardcoded in the engine. Until they are configuration, the engine can serve only one client — see ROADMAP Track A-H, Stage 1.
*Affects:* everything

**D-015 | 2026-08-05 | Hardening is sequenced, and the sequence is the argument.**
Nineteen known defects — thirteen from the capture's §12, six from review — ordered as Track A-H: triage the dormant half first (because unreachable code makes every subsequent review answerable wrongly), then exposure, then truth integrity, then tests, then cosmetics. Not reordered for convenience. Explicitly **not** on the list: rebuilding the pipeline. It works, it has four months in production, and its central design decision — the model is never the sole source of truth — is the asset.
*Affects:* `ROADMAP.md` Track A-H

**D-016 | 2026-08-05 | Adversarial self-review is a standing mechanism, not a mood.**
`.claude/skills/self-review/` defines four passes — exposure, truth, structure, and the uncomfortable questions — with assigned severities and required evidence. A review that concludes "looks good" has failed. Nothing ships with an open Exposure or Truth finding. Reviewer findings that need a ruling go to the owner as proposals and are never self-resolved or logged as rulings.
*Affects:* every milestone

**D-017 | 2026-08-05 | Amends D-014. The naming exception is code identifiers, not one file.**
D-014 stated the client brand name is eradicated from all authored prose, with `reference/titlesense-core/ENGINE_CAPTURE.md` as the sole exception. That was too narrow and failed its own first verification: `ROADMAP.md` legitimately names `TessaAgentToggle`, `TessaCheatSheet`, `TessaContext`, `TessaChatWidget`, and `tessa-card-body-enter` — real files and symbols in TD Hub, several of which Stage 0 exists to promote or delete. Renaming them in our documents would make the instructions point at files that do not exist.

**The rule, corrected:** the client brand name may appear anywhere as a **literal code identifier** — a file, symbol, env var, DB column, or CSS class that exists in the codebase — and must appear in a code span or fenced block when it does. It may never appear as **prose**: not as the name of the product, the engine, the feature, or the company's offering. The distinction is identifier vs. prose, not file vs. file.

**Verification command** (replaces the earlier raw grep, which could not tell the two apart):
`sed 's/\`[^\`]*\`//g' FILE | grep -in tessa` — strips code spans first; any remaining hit is a real violation.

*Note:* D-014's substance stands; only its exception clause is corrected. When the TD Hub namespace rename happens, these identifiers change at the source and the documents follow.
*Affects:* `ROADMAP.md`, `.claude/skills/`, `CLAUDE.md`


**D-018 | 2026-08-06 | The marketing site is a separate repository. This repo never holds application code.**
`titlesense-web` holds the v0-generated marketing site. It is not merged into this repo, for two reasons. First, this is a documents repo — five authored documents, an append-only ledger, a ratified contract, and `CLAUDE.md` as law — and a Next.js application would bury that signal under hundreds of files. Second, and less obvious: v0's GitHub sync means an AI tool holds write access to whatever repo it is attached to. In a marketing repo that is harmless. Attached here, it would be write access to `DECISIONS.md` and `docs/H1_CONTRACT.md`. There is no benefit to accepting that.
*Consequence:* three repos now exist — this one (documents), TD Hub (the engine), `titlesense-web` (marketing). `ROADMAP.md` carries the index, and any fourth repo is added there the day it is created.
*Operational rules for `titlesense-web`:* private; Vercel Deployment Protection enabled before the first deploy finishes, because Vercel deploys every push by default and preview URLs are live on the public internet; v0 works on its own branch and lands changes by pull request, never pushing straight to `main`; no custom domain until the engine clears hardening Stages 1 and 2.
*Affects:* `ROADMAP.md`


**D-019 | 2026-08-06 | Corrects the record: TD Hub is the client's portal, not ours. TitleSense is the founder and owner.**
Earlier documents described TD Hub loosely as "the codebase," which read as though it were TitleSense's. It is not. **TD Hub is a client portal owned by Pacific Coast Title**, TitleSense's first client. TitleSense built and owns the engine (D-013); PCT owns the portal the engine currently runs inside. Corrected in `ROADMAP.md`, `reference/titlesense-core/ENGINE_CAPTURE.md`, and `.claude/skills/titlesense-core/SKILL.md`.
*Direction of the relationship, stated once so it is never inverted again:* TitleSense is the founder of this product; a title company is our first customer. We are not a feature of a client's portal.
*The consequence this exposes, raised as a proposal and awaiting an owner ruling:* TitleSense does not currently own a codebase that runs its own engine. A second client has nowhere to deploy, nineteen hardening changes are queued against a repo we do not own, and four months of improvements are accruing inside a client's system. Proposed remedy is extraction into a TitleSense-owned codebase with TD Hub as a consumer — the same producer/consumer shape H1 already defines for DeedPro. Logged as ROADMAP Track A-H, Stage 1, item 0.
*Affects:* `ROADMAP.md`, `reference/titlesense-core/ENGINE_CAPTURE.md`, `.claude/skills/titlesense-core/SKILL.md`, `CLAUDE.md`


**D-020 | 2026-08-06 | Extraction ruled. The engine comes into a TitleSense-owned codebase, in three phases.**
Resolves the proposal raised in D-019. TitleSense Core is extracted from the client's portal into `titlesense-core`, a codebase TitleSense owns, and the portal becomes a consumer of it — the producer/consumer shape H1 already defines for DeedPro, applied a second time.
**Phase 1: move verbatim** — byte-for-byte, improve nothing. **Phase 2: prove parity** — the extracted engine must produce the same output as the deployed one on every real prelim available, with each difference explained individually. **Phase 3: rebuild**, boundaries first (multi-tenancy, persistence, API surface, deduplicated scoring, schema validation), domain logic last (pre-parser regexes, classification table, repair rules, prompts, scoring values).
*Why phased rather than a clean rewrite:* the engine has four months of contact with real prelims from a real title company, and there is no test suite. The pre-parser's classification table and the repair rules are the residue of documents that broke the system and got fixed — tacit knowledge that cannot be re-derived by reasoning, only by re-encountering the same documents. A rewrite without parity replaces something hardened by reality with something hardened by nothing, and provides no way to detect what was lost.
*Dependency:* Phase 2's regression data is PCT's, with unredacted PII. It requires their sign-off and a redaction pass.
*Affects:* `ROADMAP.md` Stage 1 item 0

**D-021 | 2026-08-06 | One law file. Agent doorways point at it; they never copy it. And law is separated from status.**
`AGENTS.md` had been created as a full copy of `CLAUDE.md` retitled for another agent. Within hours it was already stale — it predated D-019 and D-020, still implied the client's portal was ours, and its verification command had been corrupted in transit. Two agents reading two rulebooks pick different rules, and nobody can tell which.
**Rule:** `CLAUDE.md` is the single law file. Other agent entry points (`AGENTS.md`, `.cursor/rules/`, and any future convention) are **pointers** to it. They may repeat a short list of permanent prohibitions as a safety net for an agent that ignores the pointer — never the full ruleset, and only rules chosen for never needing to change.
**Second rule, and the more important one:** `CLAUDE.md` holds law; `ROADMAP.md` holds status. The stale section was stale because a law file was carrying weekly-changing content — the blocked list, what to work on next. Status is never restated in a law file. Where the two disagree, `ROADMAP.md` wins.
*Affects:* `CLAUDE.md`, `AGENTS.md`


**D-022 | 2026-08-06 | Brand system: ink ground, amber = verified fact, violet = interpretation. Marketing site deployed private.**
The palette is semantic rather than decorative. Amber and violet already carry meaning in H1 §2 — candidate facts and proposals — and the engine already separates them in the database via `facts_json` and `extraction_json`. A brand whose colors encode what the product believes is defensible in a way an arbitrary choice is not, and it gives every future surface one visual grammar. Ink `#0E1218`, vellum `#EFEDE6` for document surfaces, amber `#E8A33D`, violet `#8B6BF5`. Type: Archivo display, Newsreader body, IBM Plex Mono for recorded-document data.
*Explicitly not the client's colors.* `#F26B2B` and `#1B2A4A` belong to the deployed client instance. TitleSense licenses to that company's competitors; a marketing site wearing one customer's palette tells every other title company whose product it really is.
*Content rules, held:* no invented statistics, no customer logos, no testimonials, no stock photography, no pricing. The site's strongest trust signal is the section listing what the system refuses to say.
*Deployment:* single static `index.html`, no build step, Vercel, deployment protection enabled and verified. Private repo. No custom domain until the engine clears hardening Stages 1 and 2.
*Affects:* `ROADMAP.md` Track M


**D-023 | 2026-08-14 | Phase 1 complete. The engine is out of the client's portal.**
Forty-five exported files triaged into three buckets. Engine code moved to `titlesense-core` and renamed off the client's brand namespace; nineteen client-coupled files — their migrations, order routes, settings and admin routes, health route, ops panel, brand-carrying components — quarantined in `_client_coupled/` as reference, to be **replaced in Phase 3 rather than ported**.
**Three seams defined,** because the export could not run: `pipeline.ts` imported the client's database and S3 modules, and `ai-client.ts` imported their database for vendor logging. The engine now receives `DocumentSource`, `AnalysisStore`, and `VendorLogSink` instead of importing them. Control flow, logging, and error handling unchanged.
**Tenant identity parameterized.** The persona string hardcoded the client's company and assistant name. It is now a `TenantIdentity` value whose default reproduces the deployed prompt byte-for-byte — verified, 2509 characters, empty diff. Editing the prompt directly would have changed model output and destroyed the parity baseline before it could be measured.
**Live data contracts deliberately untouched:** the settings key `tessa_prelim_enabled`, the log values `'tessa_extract'` and `'tessa_summarize'`, the `prelim_analyses` table and columns, and the class name `TessaAnalysisPausedError` (caught by name at runtime). These change by migration in Phase 3, never by find-and-replace. Each carries a comment saying so.
*Standing item, so it cannot survive quietly:* **the default `TenantIdentity` still names the client.** That is correct today — it is the parity anchor. **Replacing it with a neutral default is a Phase 2 exit criterion**, not optional cleanup.
*Also open:* the engine has no `package.json`. Dependency versions must be copied from the client repo rather than resolved fresh; `pdf-parse` is pinned by parity, and the deep import `pdf-parse/lib/pdf-parse` is deliberate — the package index carries a debug harness that reads a local file.
*Affects:* `ROADMAP.md` Stage 1 item 0

**D-024 | 2026-08-14 | Near-miss: read-only `reference/` was silently reorganized. Staging is checked for deletions before every commit.**
An agent session moved the entire `reference/` tree to `docs/reference/` without being asked. `CLAUDE.md` forbids this explicitly — `reference/` holds faithful captures and is never edited, reformatted, or reorganized. Caught uncommitted and restored with `git restore reference/`.
**What it would have cost:** every path in `CLAUDE.md`, `ROADMAP.md`, `AGENTS.md`, and both skills points at `reference/titlesense-core/`. One `git add -A` would have broken all of them at once, and the break would have surfaced later as a session quietly failing to find the capture and answering from memory instead.
**Worse, it nearly destroyed the only copy of a file.** `TESSA_PRELIM_SUMMARIES_IMPLEMENTATION.md` existed only under the moved directory and was not in git. Rescuing it *before* deleting the stray tree was the difference between a fix and a permanent loss. It now lives at `reference/titlesense-core/TESSA_PRELIM_SUMMARIES_IMPLEMENTATION.md` and is committed.
**Rule:** before any `git add -A`, run `git status --porcelain` and read the deletions. A `D ` line for a file nobody was asked to remove is a stop, not a formality. Agents are not trusted to stage unsupervised.
*Second finding, confirming D-021 empirically:* `.agents/` held copies of the skills. Two were byte-identical; **`titlesense-core` had already drifted.** Duplicated instruction files diverge in days, not months. Deleted. One law file; doorways point, never copy.
*Affects:* `CLAUDE.md`

**D-025 | 2026-08-14 | Engine dependencies pinned exactly. The client portal has no lockfile — parity is measured against stored rows, not against re-running the deployed engine.**
`titlesense-core` pins every dependency to an exact version, no caret ranges: `@anthropic-ai/sdk` 0.82.0, `pdf-parse` 1.1.1, `drizzle-orm` 0.45.2, with `react` 19.2.3 and `lucide-react` 1.18.0 as optional peers (UI only — the host application provides them). Values taken from what is **installed** in the client portal, not from its `package.json` ranges.
**The finding that forced this:** the client portal has no root lockfile. Its caret ranges had already floated — `drizzle-orm` from ^0.45.1 to 0.45.2, `lucide-react` from ^1.7.0 all the way to 1.18.0. Nobody chose those versions. `pdf-parse`, the one that governs text extraction and therefore what the model sees, was correctly pinned exact.
**Consequence for Phase 2, and it is structural:** the environment that produced four months of analyses cannot be reconstructed, so parity cannot be defined as "re-run the deployed engine and diff." The baseline must be the **stored `prelim_analyses` rows** — `facts_json` and `extraction_json` as recorded. This was already the intended approach; it is now the only available one.
**Consequence for the client, worth raising alongside the PII finding:** their production application can change behavior on redeploy with no code change, because dependency resolution is unpinned. Same posture as the retention finding — something we found, with a fix.
*Also:* the lockfile `titlesense-core` generates is committed. It is the first reproducible record of what this engine runs against.
*Affects:* `ROADMAP.md` Phase 2


**D-026 | 2026-08-14 | Silent truncation at 50,000 characters. Ranked the top truth defect; parity is measured on truncated input.**
`pdf-extract.ts` caps text extraction at `MAX_CHARS = 50_000`. Production data shows the cap is not an edge case: **3,435 of 3,468 stored rows are exactly at the ceiling.** Roughly 99% of prelims analyzed since April were read only in part, and because prelims place exceptions, legal descriptions, and tax detail late in the document, the unread portion is disproportionately the substantive one.
**Why it ranks above every other Stage 2 item:** it is silent. No truncation marker in `extraction_json`, none in the UI, and a summary that presents itself as a summary of the document. Under the standing rule that absence is stated and never implied, the engine is making claims about a document it did not finish reading without disclosing that it stopped.
**Fix is split in two, deliberately.** Marking truncation — `truncated: true` plus the original character count, surfaced to the officer — is small and safe and happens now. Raising or removing the cap changes what the model sees, therefore changes output, and therefore happens only after a parity baseline exists.
**Consequence for Phase 2, and it simplifies things:** the deployed engine's input was `pdf_text` *as stored* — already truncated. So parity is measured as stored input → extracted engine → compare against stored `facts_json` and `extraction_json`. **The source PDFs are not needed.** The parity corpus is a table export, not 3.1 GB of client documents, which makes the data ask smaller and easier to grant.
*Sampling ruling:* a stratified 80–100 rows weighted toward `high` and `very_high` complexity, plus every foreclosure case (those exercise the most repair rules), gives better behavioral coverage than 500 random rows. The population is heavily skewed — 61% moderate, 3.3% very_high — so a random draw would surface almost none of the interesting cases.
*Standing constraint:* real prelims and stored rows are the client's data containing owner names, addresses, APNs, and lien details. Nothing moves until access is agreed, and the export is redacted at source.
*Affects:* `ROADMAP.md` Stage 2, Phase 2


**D-027 | 2026-08-14 | Parity harness built and self-verified. Phase 2 is blocked on one small data export and nothing else.**
`parity/` in `titlesense-core`: stored `pdf_text` → extracted engine → field-level comparison against stored `facts`, `extraction`, and score. Comparison is by category rather than whole-blob diff — facts, structure, values, score, prose — because a blob diff produces noise nobody reads. Severity is assigned automatically: CRITICAL for a differing fact, a dropped item, or a prohibited phrase; MAJOR for a differing value or a band change; MINOR within-band. Exit 1 on any CRITICAL. `--dry-run` exercises the pre-parser and comparator with no Anthropic calls, so the harness can be developed without spending tokens.
**Every severity path is proven, not assumed:** a corrupted fact, a dropped lien, a prohibited phrase in summary text, and a band crossing each fire correctly with the expected exit code. Redaction in `import.ts` is proven too — tokens are stable across `pdf_text` and the JSON fields, and a grep for the original values returns zero hits.
**The finding that justified building this before data arrived:** `import.ts` could not read JSONL at all. A file beginning with `{` was parsed as a single JSON document and failed on line two. That was caught against three rows of invented data rather than against a client's customer records. The redaction logic was correct; the parser was broken — untested code fails in the boring place.
*Recorded judgments, so they are not second-guessed later:*
- **Instrument numbers, recording dates, and document types are deliberately not redacted.** They are public record, and facts key off them — redacting would break comparison for no privacy gain.
- **A band crossing is MAJOR, not CRITICAL, and this is provisional.** During parity a band change is a finding to investigate, not a build break. Once Stage 2 item 4 removes the duplicated scoring implementation, an unexplained band crossing means a regression and the severity is promoted.
- **A green fixture run is a regression guard, not proof of parity.** Fixture expectations were hand-authored against the current engine. Only production-captured rows can serve as a baseline, and if the deployed engine was wrong about something, the baseline carries that error forward — a disagreement is a finding, not automatically a failure.
*Blocking input:* a stratified export of 80–100 `prelim_analyses` rows where `extraction_json IS NOT NULL`, weighted toward `high` and `very_high`, every foreclosure case included. A few megabytes of JSON, redacted at import.
*Affects:* `ROADMAP.md` Phase 2
