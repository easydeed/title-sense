---
name: titlepoint-reference
description: Answer any TitlePoint API question — methods, service types, parameters, search workflow, result filters, legal formats, doc types, plant/county coverage — from the captured documentation in reference/. Use whenever TitlePoint, CreateService, GetResultByID, GetFilteredResultByID, Deedchain, OpenEncumbrances, title plants, or TPX/Hybrid searching comes up, and before writing any code that calls TitlePoint.
---

# TitlePoint reference

The captured TitlePoint documentation lives in `reference/`. **Read-only** — never
edit, reformat, or update it. Its value is that it is a faithful capture.

Three files, one corpus:

| File | Use for |
|---|---|
| `titlepoint-title-searching-inventory.json` | Exact lookup — 19 methods, 32 service types, 162 parameters, 53 pages with heading paths |
| `titlepoint-title-searching-chunks.jsonl` | Semantic search — 116 chunks, each with source URL, heading path, methods, service types, parameters |
| `titlepoint-title-searching-reference.md` | Full cleaned text, ~46k lines. Grep it; don't read it whole |

## Rules

1. **Search before answering.** Never answer a TitlePoint question from model
   memory. Grep the reference or parse the inventory.
2. **Cite the source filename** (and URL where useful) with every answer.
3. **Prefer exact matches** for method names, service types, parameters, and
   county names. Inventory for exact lookup, JSONL for semantic.
4. **Never silently merge conflicting parameter definitions.** Surface the conflict.
5. **Say when the documentation doesn't answer it.** This is the most important
   rule here — see the payload limit below.
6. **Deprecated stays deprecated**, even where other examples still use it.
   - Deprecated: `CreateService`, `CreateService2`, `GetResultByID`,
     `GetResultByID2`, `GetResultByRequestID`, `GetResultByRequestID2`
   - Current: `CreateService3` / `CreateService4`, `GetRequestSummaries`,
     `GetResultByID3`, `GetFilteredResultByID`

## The workflow

Searches are asynchronous:

```
CreateService3 / CreateService4   →  RequestID + OrderID
GetRequestSummaries               →  poll until complete
                                     (one call may produce MANY results)
GetResultByID3                    →  per result ID
```

`GetRequestSummaries` is not skippable. A single create call can spawn an address
lookup, a property search, a tax search, and a related-parcel search. Summaries
also carry plant dates, name variations, and search parameters that
`GetResultByRequestID3` skips.

## Four traps that have already cost time

**The filters live on the wrong-looking method.** `Deedchain`, `DeedchainOnly`,
`OpenEncumbrances`, and `OpenEncumbrancesOnly` are `filterParams` on
**`GetFilteredResultByID`** — *not* `GetResultByID3`. Code written against
`GetResultByID3` returns results with no filters applied and no error.

**`Deedchain` is ownership-only.** It returns only Conveyances and Full Value
Deeds. Deeds of trust, assignments, and reconveyances are excluded by design.
The loan/lien picture comes from `OpenEncumbrances`, separately.

**The chain's start date is data-determined.** The anchor FVD must be dated at or
older than the From Date; if the nearest is newer, the system walks further back.
Always report the actual anchor, never the requested From Date.

**Appendix G (`DocType.html`) is quarantined** per D-007e. An initial structural
parse drifted on repeated headers; the ~6,779 row figure is unreliable and no
mapping table is built on it until a real parser exists.

## Payload limit — do not infer past it

The docs specify **request** envelopes completely and **result** payloads only at
the container level. The one documented result envelope, `DocumentListResult`,
elides the interior of `Parties`, `Items`, and `DocumentIdentifications` — exactly
where recording date, instrument number, and party position live.

Those fields are unknown. They are marked `pending: live_capture` in
`docs/H1_CONTRACT.md` §9 and get filled from one real captured production result.
**Do not infer, guess, or complete them.** If asked for a result field shape the
docs don't give, say the documentation doesn't provide it.

## Three vocabularies, not interchangeable

- **TitlePoint categories** (`General.DocTypeCategoryList`): `Conveyances`,
  `CourtRelated`, `Delinquencies`, `FVD`, `IME`, `Miscellaneous`, `NME`, `VME`
- **TPX/Xpress numeric** (`DocumentTypeCategory`): 1=Conveyance, 2=Mortgage,
  4=Judgment & Lien, 6=Easements, 7=Restrictions, 8=Leases, 9=Misc (3 and 5 are
  undocumented in the capture)
- **DocType/DocSubtype pairs** (Appendix G, quarantined)

Which applies depends on plant type. Never flatten them.

## Coverage

California legal formats are **per-county** — ~2,800 lines of county-specific map
codes and required fields. There is no single "California implementation." Build
order starts with PCT's working counties (D-008). LA, Orange, Riverside, San
Bernardino, and San Diego are all modern TitlePoint plant counties.

Known input constraints: `customerRef` caps at 11 alphanumeric for San Diego and
Imperial, 9 numeric for Los Angeles, 15 elsewhere.

## Caveat

The capture is from the **QA2** documentation site. Production parity is an open
question on the owner's ToS errand. Treat everything here as QA2-accurate until
confirmed.
