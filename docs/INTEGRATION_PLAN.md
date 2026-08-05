# DeedPro ↔ TitleSense — Integration Plan & Shared Context

**Purpose of this document:** sync a TitleSense-side Claude session with the DeedPro-side strategy, so both projects build toward the same seam from day one. Written from the DeedPro side; the TitleSense side should treat this as the current state of agreement, push back where its own findings disagree, and propose amendments rather than silently diverging.

**Owner of both projects:** Jerry (solo founder, escrow/title professional, Southern California). Final authority on all cross-project decisions.

**Status date:** August 2026. **Local annotation 2026-08-04:** §6's five open questions are all answered (D-007); §5's TitleSense column is superseded by `ROADMAP.md`; the H1 contract referenced in §4 now exists at `docs/H1_CONTRACT.md` v1.1. The DeedPro side holds the master and will publish v1.1 of this plan; this copy is annotated, not edited, so the two do not silently diverge.

---

## 1. The One-Paragraph Thesis

**TitleSense understands property; DeedPro acts on it.** TitleSense ingests TitlePoint title-search data (chain of title, open encumbrances) and builds the *interpretation layer* — turning recorded document history into a clear, cautious, confidence-scored story. DeedPro is a deed-preparation engine for California escrow officers — 21 recordable instruments on a recorder-compliant chassis, with a strict human-decision doctrine and full provenance records. The integration makes TitleSense DeedPro's smartest data source and **DeedPro's first API partner** — proving the "why build it when you can plug into DeedPro" infrastructure thesis with a consumer the owner controls, before any stranger integrates.

Two separate products, two separate acquisition stories, integrated through a **public API contract** — never entangled code. Either can be sold alone; the integration survives as a reference customer for the other.

---

## 2. What Each Product Is (Context for the Other Side)

### DeedPro (the acting side)
- **User:** California escrow officers (SoCal independent escrow is the launch market).
- **Catalog:** 21 recordable instruments across three families (deeds, affidavits, declarations), all rendered on a measured, county-recorder-compliant chassis with CI-pinned geometry and statutory wording.
- **Government forms:** fills the BOE-502-A (PCOR) and BOE-502-D from data the deed flow already knows — facts prefilled, legal-claim checkboxes proposed-never-auto-checked, certification blocks always blank.
- **Pipeline extras:** prelim/title-report PDF import (text-layer extraction → candidate fields), matter/file grouping by escrow number, correction/supersession lineage, immutable sha256-stamped stored PDFs, notary signing handoff (designed, deferred until a real user exists).
- **Partner API:** live (nine deed types today), API-key + metering infrastructure, admin console. SDK/webhooks/deep-links are the next build phase (DX0).
- **Property data today:** SiteX (assessor-level enrichment). TitleSense would add chain-level intelligence.

### TitleSense (the understanding side)
- **Data source:** TitlePoint (credentials exist; docs fully indexed — 116 searchable chunks, 19 API methods, async CreateService → poll → GetResultByID workflow, `Deedchain` / `OpenEncumbrances` result filters).
- **Core product:** the interpretation layer — document-type classification, loan-family grouping (DOT → assignment → reconveyance), release matching, open-item detection, party-name normalization, confidence tiers.
- **Outputs:** the officer-facing **Property X-Ray** (current vesting, open encumbrances, items needing review) and the client-facing **Property Story** (plain-English ownership narrative).
- **Must stand alone first:** TitleSense v1 should be valuable without DeedPro. A product that only exists as another product's feed isn't a product.

---

## 3. The Doctrine Line (Non-Negotiable, Both Sides)

DeedPro's entire defensibility is a two-tier rule, machine-enforced by CI:

1. **Facts** (names, APNs, addresses, recording references) may be *prefilled* from external sources — but arrive as amber "candidate" fields carrying `source` + provenance, and must be officer-confirmed before any document generates.
2. **Legal choices / conclusions** (vesting characterization, tax exemptions, instrument selection, "appears released") are **never auto-applied**. They arrive as violet **proposals** the officer must explicitly accept; acceptance is recorded with source, timestamp, and the basis shown at decision time.

**The integration rule that follows:** every TitleSense **interpretation** crossing into DeedPro is a **proposal, never a fact.** "Appears released," "likely match," "current vesting reads X" — these are conclusions, and conclusions arrive violet. Raw recorded facts (a document's recording date, instrument number, party names *as recorded*) may arrive as candidate facts with `source: 'titlesense'`.

TitleSense's own cautious-language discipline (already drafted: "appears released," never "title is clear"; confidence tiers Confirmed/Likely/Possible/None) maps almost 1:1 onto this taxonomy. Keep it — it is the shared law.

**One subtlety DeedPro learned the hard way (share it):** vested-owner strings from title data are *mixed content* — "JOHN DOE AND JANE DOE, HUSBAND AND WIFE AS JOINT TENANTS" is a name **plus** a legal characterization. TitleSense should emit these **split**: parties as facts, vesting characterization as a separate interpreted field — because DeedPro will route the former amber and the latter violet.

---

## 4. The Three Handshake Points

### H1 — The contract on paper (FIRST, cheap, before either side builds the seam)
A one-to-few-page spec defining what crosses the wire:
- **Finding types** (v1 candidates): `current_vesting`, `open_encumbrance`, `chain_summary`, `recorded_instrument_observed`.
- **Payload shapes** per finding: raw-fact fields vs. interpretation fields explicitly separated; every interpretation carries a confidence tier and a plain-language basis string (DeedPro records the basis the officer saw when deciding).
- **Identity anchors:** APN + county as the join key; recording references (date + instrument number) for document-level matching.
- **The proposals-never-facts rule** stated in the contract itself.
- Versioned from day one (`v1` in the payload envelope).

*Timing: draft after DeedPro's S-wave completes, feeding directly into DX0's design. TitleSense can review/amend the draft anytime.*

### H2 — The deep link (the first real integration)
TitleSense finding → **DeedPro opens with a document staged from the payload.**
- Example: X-Ray shows "open deed of trust, no matching reconveyance" → one click → DeedPro's builder opens with a reconveyance pre-staged (facts amber, interpretations violet), matter-linked.
- Requires: DeedPro's DX0 deliverables (API auth for the link, staging endpoint, deep-link URL pattern) + TitleSense emitting the payload per H1.
- Note: reconveyance/assignment instruments are DeedPro's **wave-3 catalog** — deliberately timed to land with this integration, because TitleSense findings are what make them valuable.

### H3 — The loop closure (last, hardest, most impressive)
DeedPro-generated deed records at the county → TitleSense later observes it appearing in the chain → confirmation flows back → DeedPro's matter shows "instrument now appears in the recorded chain (observed by TitleSense on DATE)."
- Requires: DeedPro's `recorded_at` / `instrument_number` fields (queued: RED-S4), TitleSense re-search or monitoring capability, and a webhook path in both directions.
- This is independent verification of the audit story — prepared here, confirmed recorded there. Diligence gold.

---

## 5. Sequenced Plans, Both Sides

### DeedPro (current, locked queue — foundation before surface)
| # | Work | Status |
|---|------|--------|
| 1 | RED-S1 — per-request connection pool, concurrency proof | ✅ built, merging |
| 2 | RED-S2 — PDFs to object storage, CASCADE removed, **executed** restore drill | next |
| 3 | RED-S3 — sessions (refresh/revocation, lockout, rate limiting, pause-preserve-resume UX) | queued |
| 4 | RED-S4 — `recorded_at` / `instrument_number` + registry-version stamping | queued (H3 prerequisite) |
| 5 | Doctrine A — vested-owner split (names=fact, characterization=proposal) | queued (H1-relevant) |
| 6 | Doctrine B — AI assistant boundary (explain-yes / select-no) | queued |
| 7 | **DX0** — partner surface investigation: SDK, webhooks, key lifecycle, deep-link pattern — **designed for partner #1 = TitleSense** | gate for H2 |
| — | NOTARY1 (signing handoff) | deferred; trigger = first real user |
| — | Wave-3 catalog (reconveyance, assignment of DOT, TOD deed, deed in lieu) | deferred; naturally pairs with H2 |

### TitleSense (proposed — the TitleSense session should refine this)
| # | Work | Notes |
|---|------|-------|
| 1 | TitlePoint pipeline — async search flow (CreateService → poll → GetResultByID), chain + open-encumbrance retrieval, against the indexed docs | Credentials exist; **rotate them first** (they appeared in old git history) |
| 2 | Interpretation layer — classification, loan families, release matching, confidence tiers, cautious language | The moat; the longest work |
| 3 | Standalone v1 — Property X-Ray + Property Story, no DeedPro dependency | Must be independently valuable |
| 4 | H1 contract review — receive DeedPro's draft, amend from the data-reality side | Cheap, early |
| 5 | Consume DeedPro's SDK — deep-link emission (H2), then loop-closure observation (H3) | After DX0 ships |

**Deliberate asynchrony:** neither side waits on the other until H2. DeedPro's next weeks are foundation; TitleSense's are pipeline + interpretation. They converge exactly when DeedPro's door (DX0) opens and TitleSense has something to walk through it with.

---

## 6. Open Questions (for the TitleSense Session to Weigh In On)

1. **Finding-type list for H1** — are the four v1 candidates right? What does the TitlePoint data actually support cleanly first?
2. **Monitoring vs. on-demand** — is H3's "observe the recorded deed appear" a re-search on demand, or does TitleSense build standing monitoring? (Affects H3 timing materially.)
3. **Party-name normalization ownership** — both products need it; it should live in TitleSense (it's interpretation), with DeedPro consuming normalized + raw forms. Agree?
4. **The Property Story delivery path** — DeedPro's share machinery can deliver the Story as the officer's client-facing artifact (officer-branded, share-tokened). Does TitleSense render it, or hand DeedPro structured content to render?
5. **Commercial/API posture toward TitlePoint** — TitlePoint is a Fidelity product; heavy usage patterns and terms-of-service review belong on the TitleSense side early, not after the interpretation layer is built on assumptions.

---

## 7. Ground Rules (Both Sessions)

- **Reference implementations first.** TitleSense builds against the indexed TitlePoint docs; DeedPro built its chassis against real recorded documents. Neither side codes against memory of an API.
- **No fabricated success.** Every integration failure surfaces loudly with its reason. A green light that can't go red is a decoration.
- **Claims are earned.** Neither product states certifications, integrations, or capabilities that don't exist yet — in code, copy, or docs.
- **The owner rules cross-project conflicts.** Where the two sessions' plans disagree, both state the disagreement plainly and Jerry decides — this document then gets amended, versioned, and re-shared so there is exactly one current agreement.

---

*DeedPro side: this plan is reflected in `docs/OWNER_LEDGER.md` (the queue) and will shape DX0's investigation scope. TitleSense side: propose amendments freely — v1 of a contract that survived argument beats v3 of one that never met reality.*
