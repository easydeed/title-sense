# Roadmap

**Last updated:** 2026-08-14 (Phase 1 complete)

The only file here that changes weekly. Everything else changes when a decision changes it.

---

## Where the code lives

**TitleSense is the founder and owner of this product.** Pacific Coast Title is its
first client. Keep that direction straight in every document — we are not a feature of
a title company; a title company is our first customer.

| Codebase | Owned by | Holds |
|---|---|---|
| **this repo** | TitleSense | Documents, decisions, contract, captures. No application code. Ever. |
| **titlesense-web** | TitleSense | Marketing site — **deployed, private, auth-protected.** Single static `index.html`, no build step. No custom domain until the engine clears Stage 2. |
| **titlesense-core** | TitleSense | **The engine.** Extracted from the client portal 2026-08-14. Private. |
| **TD Hub** | **Pacific Coast Title — the client** | PCT's client portal. Still runs the deployed engine, and still owns the `prelim_analyses` database and the S3 bucket. |

**The engine now lives in a codebase we own.** What remains welded is the *data*: the
`prelim_analyses` table and the S3 bucket are the client's, and the engine reaches them
through three seams rather than owning them. That is Phase 3's problem, and it belongs
in the PCT conversation.

If a fourth codebase appears, it gets a row here the same day.

---

## The one thing this document exists to make obvious

**v1 already exists as working code.** **TitleSense Core** — the engine — has been
analyzing prelims in TD Hub since April 2026: two Anthropic calls wrapped in a
deterministic pre-parser, behind an admin flag that ships dark. See
`reference/titlesense-core/`.

Ownership is settled: owner-built, ours to license (D-013). The engine is deployed for
Pacific Coast Title under a client brand name, which is a **brand instance, not the
product** — and which appears nowhere in our writing (D-014).

That moves Track A from *build the atom* to *harden the engine, unweld it from one
client, and wire the agent-facing half that was built and never turned on*.

---

## Owner errands — the only human dependencies

All three are yours; none can be delegated to a session.

- [x] ~~Settle engine ownership~~ — **done. Owner-built, ours to license (D-013).**
- [ ] **PII and retention on `pdf_text`.** `prelim_analyses.pdf_text` stores every
      prelim in full — owner names, addresses, loan details — with no redaction, no
      retention policy, and no truncation, since April. This is a live compliance
      surface on a title company's production database, not a backlog item. Decide the
      retention rule; the code change is small once the rule exists.
- [ ] **Rotate TitlePoint credentials.** They appeared in old git history and are about to become a production pipeline's foundation. Blocking-adjacent — nothing gets built against the old keys.
- [ ] **Ask TitlePoint the licensing question** (D-005): *are TitlePoint-derived results licensed for display to non-title-professional end users?* Then the secondary set: QA2-vs-production parity, whether derived/cached storage of results is permitted, per-search vs. per-seat pricing, standing-query allowance. Of these, QA2-vs-production parity is the one that touches `reference/` — the captured docs are QA2 and every method signature in H1 rests on them.

---

## Track A — v1 product (the engine exists; scope is *harden, unweld, wire*)

**Unblocked.** Ownership is settled and the engine exists. What remains is hardening —
see Track A-H below for the sequenced version.

- [ ] Read `reference/titlesense-core/ENGINE_CAPTURE.md` end to end before proposing any change
- [ ] **Wire the dormant agent-facing half.** The product TitleSense sells is the part
      that was built and never turned on: `AGENT_MODE_SYSTEM_PROMPT` (rewrites technical
      analysis into agent-friendly English while forbidding fact removal),
      `TessaAgentToggle`, `TessaCheatSheet`. The active summary prompt writes for a
      title professional; TitleSense's audience is everyone who isn't one.
- [ ] **Mark provenance in the UI.** `facts_json`, `raw_extraction_json`, and
      `extraction_json` already separate ground truth from model output in the database.
      The UI renders them as one undifferentiated blob. Surfacing the distinction is
      small work and is the H1 doctrine made visible.
- [ ] **Give the complexity score a basis.** Today it is a violet conclusion presented as
      a bare number, implemented twice, and the displayed value can disagree with the
      stored one by up to 10 points across a band boundary.
- [ ] Evaluate real output against real prelims — is the summary good enough to put a
      title company's brand on and hand to an agent?
- [ ] Only then: the email-forward delivery path. Zero integration, deliberately.

**Discipline note:** the summary format is still the product. It now exists and can be
judged rather than designed — which is faster, but only if it's actually judged against
real files rather than assumed good because it runs.

---

## Track A-H — hardening the engine

Nineteen known defects: thirteen from the capture's §12, six found in review. Sequenced
so that each stage makes the next one honest. **Do not reorder for convenience** — the
order is the argument.

### Stage 0 — Triage the dormant half (do this first, it costs a day)

Half the codebase is unreachable from `pipeline.ts`. Until that's resolved, every review
of "does the engine do X" can be answered wrong by someone reading code that never runs.
This is not cleanup; it is a precondition for trustworthy review.

Split the dormant code into two piles, explicitly, in writing:

| PROMOTE — this is the v1 product | DELETE — this is dead weight |
|---|---|
| `AGENT_MODE_SYSTEM_PROMPT` — rewrites analysis into agent-friendly English while forbidding fact removal | `TessaContext.tsx` → calls a route that does not exist |
| `TessaAgentToggle` — "Simplify for Agents" | `usePrelimAnalysis.ts` → calls a route that does not exist |
| `TessaCheatSheet` — realtor cheat sheet | The ~400-line markdown guardrail suite (see note) |
| | Two of three severity badge components |
| | `tessa-card-body-enter` — undefined CSS class |

**Note on the markdown suite.** Before deleting, extract `validatePrelimOutput`'s
21-field checklist into prose. It is the best existing specification of what a complete
prelim summary must contain, and that knowledge should outlive the code. Then delete
the code.

### Stage 1 — Exposure (gates licensing; nothing ships past this)

0. **Extraction — RULED. Bring the engine into a TitleSense-owned codebase.**
   *Three phases. The phase boundary is the discipline; the work inside each is
   negotiable.*

   **Phase 1 — Move it verbatim. COMPLETE 2026-08-14.** Extracted into `titlesense-core`.
   Engine files moved and renamed; nineteen client-coupled files quarantined in
   `_client_coupled/`, to be replaced in Phase 3 rather than ported. Three seams defined
   so the engine no longer imports the client's modules: `DocumentSource`,
   `AnalysisStore`, `VendorLogSink`. Tenant identity parameterized out of the prompt with
   a byte-identical default. Live data contracts left untouched. See D-023.

   **Phase 2 — Prove parity. NEXT, and blocked.** Needs three things before it can start:
   exact dependency versions from the client repo (`pdf-parse` above all — a different
   version extracts different text and would break parity invisibly), real prelims, and
   the historical `prelim_analyses` rows. The last two are the client's data and need
   permission plus a redaction pass.

   **Phase 2 — Prove parity.** The extracted engine must produce **the same output** as
   the deployed one across every real prelim available. Not similar — same. Every
   difference gets explained individually; each is either a bug fixed or a bug
   introduced, and there is no way to tell which without looking.
   *The regression suite already exists:* `prelim_analyses` holds four months of
   `facts_json`, `raw_extraction_json`, and `extraction_json`. It is **PCT's data with
   unredacted PII** — using it requires their sign-off and a redaction pass, which is
   Stage 1's retention item arriving early and usefully.

   **Phase 3 — Then rebuild.** With parity proven, changes are measurable instead of
   hopeful.

   | Rebuild freely | Port verbatim, touch last |
   |---|---|
   | Multi-tenancy and configuration | The pre-parser regexes and classification table |
   | Persistence layer and schema | The five repair rules and their add-or-escalate asymmetry |
   | The API surface the client portal consumes | The four active prompts |
   | Deduplicated scoring, schema validation | The fifteen scoring rules' point values |
   | Provenance in UI, retention, cost ceiling | |

   The right column is the moat: four months of contact with real documents, encoded as
   regexes and thresholds nobody wrote down the reasoning for. It looks the most
   rewritable — dense, undocumented, unglamorous — which is exactly why it moves last,
   after tests exist to catch what changes.

   **Talk to PCT first.** Framed as *"the engine becomes a product, and PCT gets it
   first and cheapest"* this is a good conversation with a design partner. Discovered
   afterward, it is a very different one.

   TitleSense Core runs inside **TD Hub, which Pacific Coast Title owns**. Every
   consequence below follows from that one fact:

   - **Client two has nowhere to deploy.** Selling a second title company means either
     copying code out of PCT's portal — awkward at best, contested at worst — or having
     already extracted it.
   - **Hardening is happening in a client's repo.** Stages 1–4 are nineteen changes to
     a codebase we do not own. Each one needs PCT's cooperation, and each one improves
     an asset sitting inside their system.
   - **The improvements accrue in the wrong place.** Four months of work, and the thing
     of value is embedded in the client's portal rather than in a product we can point
     an acquirer at.
   - **It contradicts our own doctrine.** TitleSense and DeedPro are kept structurally
     separate — separate acquisition stories, a contract as the only coupling point,
     enforced structurally rather than by convention. The engine and a client's portal
     deserve exactly the same treatment, and currently get none of it.

   **The proposal:** extract TitleSense Core into a TitleSense-owned codebase, and make
   TD Hub a *consumer* of it — the same producer/consumer shape H1 already defines for
   DeedPro. PCT's portal calls the engine; the engine belongs to us; the contract is the
   seam. One pattern, applied twice.

   **What this is not:** a rewrite. The pipeline works and has four months in
   production. Extraction is a move, plus an interface.

   **Talk to PCT before doing this, not after.** They have been the design partner and
   the proving ground. Extraction framed as *"the engine becomes a product PCT gets
   first and cheapest"* is a very different conversation from PCT discovering it after
   the fact.

1. **`pdf_text` retention.** Full prelims — owner names, addresses, loan details — stored
   unredacted since April, including on failed rows. Owner decides the rule; the code is
   small once the rule exists. *Blocked on the owner errand.*
2. **Unweld the brand.** Brand tokens (`#F26B2B`, `#1B2A4A`), assistant name, and company
   name are hardcoded. Until they are configuration, the engine can only ever be one
   client's. This is the single change that converts a feature into a licensable product.
3. **Cost ceiling.** No token budget, no daily cap, no rate limiter — control is on/off
   plus a 5-per-cycle cron batch, and the resume runbook warns of a backlog flood. Fine
   for one client. Not fine for several.

### Stage 2 — Truth integrity (doctrine violations)

3a. **Silent truncation — the most consequential defect found so far.** `pdf-extract.ts`
   caps extraction at `MAX_CHARS = 50_000`. In production, **3,435 of 3,468 stored rows
   sit exactly at that ceiling** — roughly 99% of every prelim ever analyzed was cut off
   mid-document. Prelims put exceptions, legal descriptions, and tax detail *late*, so
   the omitted portion is disproportionately the part that matters.

   Nothing records that this happened. `extraction_json` carries no truncation marker,
   the UI shows none, and the summary reads as a summary of the document rather than of
   its first 50,000 characters. That is a direct violation of the rule that **absence is
   stated, never implied** — the engine makes claims about a document it did not finish
   reading, and does not say so.

   - **Minimum fix, small:** record `truncated: true` plus the original character count
     in the output and surface it. An officer must be told the back of the file was not
     read.
   - **Real fix, needs parity work:** raise or remove the cap. Changes model input, so it
     changes output, so it happens *after* a parity baseline exists — not before.

   Ranked above the duplicated complexity score because it is silent and it fires on
   almost every document.

4. **One scoring implementation.** The gauge has its own byte-for-byte copy and never
   receives `facts`, so the displayed score can sit up to 10 points below the stored one
   and cross a band boundary. Under H1's lineage law a displayed conclusion that
   disagrees with the recorded one is a defect of record, not of UI. Delete the copy,
   import the shared function, and either pass `facts_json` or drop rules 14–15.
5. **Runtime schema validation.** `JSON.parse` plus a TypeScript cast means an
   out-of-enum `severity` reaches the database and the screen. A Zod parse at the
   `callExtraction` boundary closes it in a few lines.
6. **Show fact vs. inference.** `facts_json`, `raw_extraction_json`, and
   `extraction_json` already separate ground truth from model output in the database;
   the UI renders one undifferentiated blob. Marking provenance on screen is the H1
   doctrine made visible, and it is the feature that distinguishes this from every
   "AI summary" competitor.
7. **Give the score a basis.** Today it is a violet conclusion presented as a bare
   number. It needs the same treatment every other interpretation gets: what drove it,
   and how confident we are.

### Stage 3 — Lock it down

8. **Test `computeComplexity` first.** A pure function, fifteen branches, no I/O — the
   highest-value test target in the system and the one that catches a regression in #4.
   Then the pre-parser's classification table, then the repair rules.
9. **Preserve the repair asymmetry in tests.** Rules 1, 2, and 5 may only add or
   escalate; 3 and 4 replace. Nothing may delete a risk the model found. Write that as a
   test so a future refactor can't quietly break it.
10. **Model name in one place.** Currently the constant plus two string literals.
11. **Repair retry loop.** The JSON path has no equivalent of `buildRepairPrompt`. Decide
    deliberately whether to add one or to accept deterministic injection as the only
    correction — either is defensible; silence isn't.

### Stage 4 — Cosmetic (last, genuinely)

12. Status rail mismatch — `computing_facts` and `validating` are shown but never
    persisted; `downloading` is persisted but not shown.
13. Summary markdown renders as literal asterisks.

### Not on this list, deliberately

Rebuilding the pipeline. It works, it has been in production four months, and its central
design decision — the model is never the sole source of truth — is the thing worth
keeping. Hardening is not rewriting.

---

## Track B — v2 foundation (blocked on the errands above)

- [ ] **Appendix G parser** — turns `DocType.html` into a real `doctype_subtype` → `ts_class` mapping table. *Technically unblocked* (it's parsing a document already in hand), but low value until v1 exists. Roughly half a day. The output is the highest-value asset the project owns: an industry-standard classification vocabulary nobody had to invent.
- [ ] Live capture of one real production result → fills the entire `pending: live_capture` register (H1 §9). **Needs both errands cleared.**
- [ ] TitlePoint pipeline: `CreateService3/4` → `GetRequestSummaries` → `GetFilteredResultByID`
- [ ] Interpretation layer: classification, loan families, release matching, confidence tiers
- [ ] Property X-Ray (officer-facing) and Property Story (client-facing)

---

## Track M — marketing site

- [x] Brand system settled: ink ground, amber = verified fact, violet = interpretation.
      Semantic, not decorative — the palette encodes the doctrine (D-022).
- [x] Single-page site built, deployed to Vercel, deployment protection on, verified
      locked in a private window.
- [ ] Read it on a phone. The hero stacks below 900px; that layout is untested on a real
      device.
- [ ] Decide the CTA properly. `mailto:` is the right placeholder while building quietly;
      a form implies a product ready for inbound.
- [ ] Owner judgment: is the headline right for a title company executive? *"Your agents
      can't read a prelim. They call you instead."* — blunt on purpose, but the audience
      call is Jerry's.
- [ ] Copy pass against the engine, using `self-review` Pass 2: does any line claim
      something TitleSense Core does not actually do?
- [ ] **Not yet:** custom domain, public launch, naming the client, any statistic.

---

## Track C — the DeedPro seam (paced by the other side)

- [x] H1 contract drafted, amended, ratified at v1.1
- [ ] Receive `docs/PRELIM_FIELD_MAP.md` from DeedPro → map into H1 §6.1 → contract goes to v1.2
- [ ] H2 deep link — gated on DeedPro's DX0
- [ ] H3 loop closure — on-demand re-search (D-007a)

---

## Waiting on

| What | From | Since |
|---|---|---|
| `PRELIM_FIELD_MAP.md` | DeedPro side | 2026-08-04 |
| `pdf_text` retention rule | Owner errand | 2026-08-05 |
| Ruling on the three authored supersession sub-rules (D-011) | DeedPro side | 2026-08-04 |
| TitlePoint licensing answer | TitlePoint / owner errand | 2026-08-04 |

---

## Explicitly not doing yet

Kept here so they stop reappearing as ideas:

- SoftPro / Qualia marketplace integrations — year two
- Standing property monitoring — v2.1, and only after the ToS answer
- Any county outside PCT's working set
- Consumer-facing Story surfaces — dependent on D-005's answer
- Additional title companies beyond PCT
- The chat surface (`TessaChatWidget`, `TessaContext`) — the routes it calls don't exist
- Reviving the ~400-line dormant markdown guardrail suite — wire it or delete it, but not now
