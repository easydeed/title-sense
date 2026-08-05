# TitleSense

Plain-English understanding of property records, for the people in a real estate transaction who aren't title professionals.

**Owner:** Jerry — solo founder, escrow/title professional, Southern California. Final authority on every decision here.
**Sibling project:** DeedPro (deed preparation for California escrow officers). Two products, two acquisition stories, integrated through a public API contract — never entangled code.

---

## Where things stand

| | |
|---|---|
| **v1 product** | Prelim in → branded plain-English summary out. Design partner and first customer: Pacific Coast Title. **Not blocked by anything.** |
| **v2 product** | TitlePoint chain intelligence — Property X-Ray, Property Story. **Blocked on two owner errands** (see ROADMAP). |
| **Code written** | None yet. This is by design; the contract came first. |
| **Wire contract** | H1 v1.1, ratified. |

---

## Start here

1. `ROADMAP.md` — what's next, what's blocked, and on whom. **The only file that changes weekly.**
2. `DECISIONS.md` — every ruling ever made, dated, one entry each. Append-only.
3. `docs/H1_CONTRACT.md` — the TitleSense → DeedPro wire contract.
4. `docs/PRODUCT_SPEC.md` — what the interpretation layer is: X-Ray, Story, document families, confidence tiers, cautious-language rules.
5. `docs/INTEGRATION_PLAN.md` — the cross-project agreement with DeedPro.

`reference/` holds the captured TitlePoint documentation package. **Read-only. Never edited.**
`archive/` holds superseded documents, each with a one-line note saying what replaced it. **Nothing is ever deleted.**

---

## The rule that keeps this to five documents

> **A new document requires a question it answers that no existing document answers.**

Everything else is one of three things:

- a **ruling** → append a line to `DECISIONS.md`
- a **change of plan** → edit `ROADMAP.md`
- a **change to the contract or the product** → edit that document in place and add a changelog entry at its bottom

Three further rules, each earned:

**Version inside the file, not in the filename.** `H1_CONTRACT.md` with `**Version:** v1.1` in its header and a changelog at the bottom — never `H1_CONTRACT_v1.1.md`. Version-in-filename is how five documents become fifty, and the reader can no longer tell which one is real. *(This project already made that mistake once, in this file's first hour.)*

**Nothing exists until it's in a file.** Chat is a workshop, not a store. A decision that lives only in a conversation is a decision that will be re-litigated in three months by someone who can't find it — and that someone is you.

**Supersede, don't accumulate.** When a document is replaced, it moves to `archive/` with a header line naming its replacement and the date. The corpus stays small because old things leave, not because new things are forbidden.

---

## Working rules for this repo

Deliberately a section here, not a sixth document.

**Nothing sensitive is ever committed.** `.gitignore` covers three categories, each for a specific reason: credentials (this project already lost TitlePoint keys to git history once — rotation fixes the keys, not the habit), customer prelims (they carry names, addresses, APNs, and loan amounts belonging to PCT's customers, and redaction is a per-document judgment, so the default is never), and captured TitlePoint responses (licensed data whose terms are still under review per D-005).

**Working prelims live outside the repo.** A folder on your machine that git cannot see. Even redacted ones — redaction that was adequate for showing a colleague is not the same standard as a public remote, and the two are easy to conflate at 11pm.

**If this repo's history ever held the old TitlePoint credentials, rotation alone doesn't clear it.** Rotating makes the leaked keys useless, which is the part that matters. But the old values stay in history until the history is rewritten or the repo is replaced. Worth knowing before the remote goes anywhere.

**Commit messages reference decisions.** `D-009: supersession semantics into H1` beats `update contract`. The ledger and the history then explain each other.

