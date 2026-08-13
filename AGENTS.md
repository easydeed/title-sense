# AGENTS.md

**This is a pointer, not a second rulebook.**

The project's law lives in **`CLAUDE.md`**. Read it now, in full, before doing anything
else. Current status — what is blocked, what is next, which codebase holds what — lives
in **`ROADMAP.md`**. Every ruling ever made is in **`DECISIONS.md`**.

Those three are the only sources. This file is never expanded into a copy of them: a
duplicate rulebook goes stale within a day and then two agents follow two different sets
of rules. (D-021)

---

## The few rules repeated here deliberately

Only because an agent that ignores the pointer above still must not do these. They are
chosen for being permanent — if any of them ever needs updating, update `CLAUDE.md` and
leave this list alone.

**Never commit** credentials, customer prelims (they carry real names, addresses, APNs,
and loan amounts), or captured vendor API responses. If something matching those appears
in `git status`, stop and say so rather than staging it.

**`DECISIONS.md` is append-only.** Never edit or delete an entry. Propose entries; never
write a ruling the owner has not made.

**`reference/` is read-only.** It holds faithful captures of external systems. Never
edit, reformat, or "complete" anything in it — including things that look unfinished.

**Don't create documents.** A new file requires a question no existing document answers.

**Say so when you disagree.** The owner rules. Never silently diverge.
