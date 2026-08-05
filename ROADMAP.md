# Roadmap

**Last updated:** 2026-08-04

The only file here that changes weekly. Everything else changes when a decision changes it.

---

## The one thing this document exists to make obvious

**The v1 product is not blocked by anything.** The TitlePoint work is blocked on two owner errands; the PCT prelim product needs none of it — no TitlePoint credentials, no ToS answer, no chain parsing. If both errands stall for a month, v1 still ships.

Do not let the blocked track make the unblocked track feel blocked.

---

## Owner errands — the only human dependencies

Nothing on the v2 track moves until both clear. Both are yours; neither can be delegated to a session.

- [ ] **Rotate TitlePoint credentials.** They appeared in old git history and are about to become a production pipeline's foundation. Blocking-adjacent — nothing gets built against the old keys.
- [ ] **Ask TitlePoint the licensing question** (D-005): *are TitlePoint-derived results licensed for display to non-title-professional end users?* Then the secondary set: QA2-vs-production parity, whether derived/cached storage of results is permitted, per-search vs. per-seat pricing, standing-query allowance.

---

## Track A — v1 product (unblocked, has a customer)

The goal is one excellent output: **prelim in → branded plain-English summary out.** Nothing more until that one thing is good.

- [ ] Get one real redacted prelim from PCT
- [ ] Design the one-page summary against that document — the atom of the product
- [ ] Show it to PCT; revise against what an actual title person says about it
- [ ] Repeat with 3–5 more prelims until the format holds without hand-editing
- [ ] Only then: the email-forward delivery path (forward to an address, get the summary back). Zero integration, deliberately — it sidesteps title-industry IT approval entirely.

**Discipline note:** the summary format is the product. The email plumbing is a detail that becomes easy once the format is settled, and impossible to settle if built first.

---

## Track B — v2 foundation (blocked on the errands above)

- [ ] **Appendix G parser** — turns `DocType.html` into a real `doctype_subtype` → `ts_class` mapping table. *Technically unblocked* (it's parsing a document already in hand), but low value until v1 exists. Roughly half a day. The output is the highest-value asset the project owns: an industry-standard classification vocabulary nobody had to invent.
- [ ] Live capture of one real production result → fills the entire `pending: live_capture` register (H1 §9). **Needs both errands cleared.**
- [ ] TitlePoint pipeline: `CreateService3/4` → `GetRequestSummaries` → `GetFilteredResultByID`
- [ ] Interpretation layer: classification, loan families, release matching, confidence tiers
- [ ] Property X-Ray (officer-facing) and Property Story (client-facing)

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
