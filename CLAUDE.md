# CLAUDE.md

Project instructions for Claude Code. Read `README.md` for orientation and
`ROADMAP.md` for what's actually next.

**Owner:** Jerry — escrow/title professional, solo founder. Final authority on
every decision. Where you disagree with a plan, say so plainly and let him rule;
don't silently diverge.

**Who owns what — get this right before anything else.** TitleSense is the **founder and
owner** of this product and the engine, TitleSense Core. **TD Hub is a client portal
owned by Pacific Coast Title**, our first client, and the engine currently runs inside
it. So every path in `reference/titlesense-core/ENGINE_CAPTURE.md` is a location in a
*client's* system. Never describe TD Hub as ours, never assume a change there is ours to
make unilaterally, and never phrase TitleSense as a feature of a title company's
platform. (D-013, D-019)

**What this is:** TitleSense makes property records readable for the people in a
transaction who aren't title professionals. v1 is the prelim product (prelim in,
plain-English summary out, Pacific Coast Title as first customer). v2 is the
TitlePoint chain layer. Sibling project DeedPro prepares deeds and consumes
TitleSense findings across a public API contract — two products, never entangled
code.

---

## The law

These aren't preferences. They're why the product is safe to build.

**1. Facts may be prefilled; conclusions may not.** Recorded facts (recording
date, instrument number, party names *as recorded*) cross as candidate facts —
amber in DeedPro, officer-confirmed. Every conclusion — "appears released,"
"current vesting reads X" — crosses as a proposal, violet, explicitly accepted.
Full rules in `docs/H1_CONTRACT.md` §2.

**2. Mixed content is split, never emitted whole.**
`JOHN DOE AND JANE DOE, HUSBAND AND WIFE AS JOINT TENANTS` is names **plus** a
legal characterization. Parties are fact; the vesting characterization is
interpretation. Never one field.

**3. Cautious language, enforced.**
Permitted: *appears released · likely related · may still affect the property ·
no matching release was identified · professional review recommended*
Prohibited, anywhere in output or copy: *the title is clear · the loan is paid ·
this lien is invalid · you legally own the property · there are no title problems*
The product explains records. It does not perform a title examination.

**4. Absence is stated, never implied.** "No matching release found" is a claim
about what was searched, and is meaningless without the coverage that bounds it.

**5. Never code against memory of an API.** TitlePoint questions get answered
from `reference/`, with the source file cited. Result-payload leaf fields are
**unknown** and marked `pending: live_capture` in `docs/H1_CONTRACT.md` §9 —
do not infer them, do not fill them in, do not "complete" them.

**6. No fabricated success.** A failure surfaces loudly with its reason. A green
light that can't go red is a decoration.

---

## Naming

The engine is **TitleSense Core**. The assistant name deployed for Pacific Coast Title is
a **brand instance** — one client's branding, not the product — and it appears nowhere in
anything we author: not prose, not plans, not commit messages. It survives only inside
`reference/titlesense-core/ENGINE_CAPTURE.md` and the TD Hub code, as literal identifiers.
Use those identifiers when naming a file or symbol; never as the name of the product.

**The line is identifier vs. prose, not file vs. file** (D-017). The name may appear as a
literal code identifier anywhere — file, symbol, env var, DB column, CSS class — and must
sit in a code span or fenced block when it does. It may never appear as prose. To check:
`sed 's/\`[^\`]*\`//g' FILE | grep -in tessa` — strips code spans first; anything left is
a real violation. (D-014, D-017)

## Review

Before any milestone, and before declaring anything done, run the `self-review` skill.
Four passes: exposure, truth, structure, uncomfortable questions. A review that concludes
"looks good" has failed. Nothing ships with an open Exposure or Truth finding. (D-016)

## Working rules

**Don't create documents.** The repo holds five authored documents on purpose.
A new file requires a question no existing document answers — otherwise it's a
line in `DECISIONS.md`, an edit to `ROADMAP.md`, or a section in a doc that
already exists. Do not add LICENSE, CONTRIBUTING, CHANGELOG, ARCHITECTURE, or
summary files unless asked.

**Version inside the file, never in the filename.** `H1_CONTRACT.md` with a
version header and a changelog at the bottom — never `H1_CONTRACT_v2.md`.

**`DECISIONS.md` is append-only.** Never edit or delete an entry. A reversal is
a new entry naming the one it overturns. Propose entries; don't write rulings
the owner hasn't made.

**`reference/` is read-only.** Captured TitlePoint documentation. Never edit,
reformat, or "update" it — its value is that it's a faithful capture.

**`docs/H1_CONTRACT.md` is ratified.** It survived three rounds of negotiation
and one amendment. Don't reword it. Amendments go through the owner and land as
a changelog entry.

---

## Never commit

- Credentials of any kind. This project already lost TitlePoint keys to git
  history once; rotation fixed the keys, not the habit.
- Customer prelims. They carry names, addresses, APNs, and loan amounts
  belonging to PCT's customers. Redaction is a per-document judgment — the
  default is never. Working copies live outside the repo.
- Captured TitlePoint responses. Licensed data, terms still under review.

`.gitignore` covers these. If something matching those patterns appears in
`git status`, stop and say so rather than staging it.

---

## Status is not kept here

This file holds **law** — rules that hold regardless of what week it is. It does not
hold status, and nothing that changes should ever be restated in it.

What is blocked, what is next, what the owner still owes, and which codebase holds what
all live in **`ROADMAP.md`**. Read it at the start of a session and answer "what should
I work on" from there, never from memory of this file. When they disagree, `ROADMAP.md`
is right.

This section exists because the previous version of it went stale within a day.

---

## Commit messages

Reference the decision: `D-009: supersession semantics into H1` beats
`update contract`. The ledger and the git history then explain each other.
