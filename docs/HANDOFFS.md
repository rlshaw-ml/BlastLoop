# Handoffs

## Experience lines

Every handoff carries these lines in addition to the numbered packet below. Write them on a failure and on a clean pass.

- **UX / DX / AX effect** - for a finding, how it hits UX, DX, and AX; for a change, whether each side goes up, stays even, or goes down. When any side goes down, leave that candidate unapplied and send it back to the altitude that can raise all three.
- **State inventory** - one entry per cluster of flags that act as one state, or `none`.

Each state-inventory entry lists the flags, the inferred transitions, the impossible combinations, and any stuck state a user can reach. Mark the entry `defect` when a user can reach an impossible combination or a stuck state. Mark it `advice-only` when no user can reach the redundancy.

These lines do not add a verdict. **PASS**, **ROUTE BACK**, escalate, and the **T3 -> T1** restart stay as they are. **T1** records and routes an implicit-state defect. **T1** does not convert it. **Mid** or **Inner** converts only after the three-way test passes, with current behavior pinned by tests first. When the same state is a top-level contract across the system, **T1** routes it to **Blast**, and **Blast** lands that contract only when the three-way test passes. **T3** attacks from the user side, the developer side, and the agent side.

Definitions live in [`docs/UX-DX-AX.md`](./UX-DX-AX.md).

## Why handoffs exist

In BlastLoop, one layer can know there is a problem without owning the best fix.

Seeing the problem and deciding the fix are different responsibilities. The sender's job is to pass a usable packet. The owning altitude's job is to decide what changes and edit the code at that scope.

A stage may recommend a fix that would work from its own vantage. That recommendation is advisory only. Ultimate responsibility for choosing and implementing the change stays with the owning altitude: **Blast**, **Mid**, or **Inner** when **T1** routes structural work, or **T2** for non-structural bug and ops work.

The owning altitude fixes every defect routed to it and re-verifies the fix before the loop exits. Reporting the defect leaves it open.

## Required packet for every handoff

Every handoff must include:

1. **What broke or what changed** - the problem found or the work completed
2. **Where it was observed** - file, module, endpoint, test, runtime path, or review point
3. **Suspected owning altitude** - Blast, Mid, Inner, T1, T2, or T3
4. **Evidence** - failing behavior, code pointer, log, test output, or attack path
5. **What the sender refuses to decide** - the decision that belongs to the next owner
6. **Suggested next owner** - the layer that should take the next action
7. **Optional recommendation** - a possible fix from the sender's vantage, clearly marked as advice and not a decision
8. **Owner Blockers** - keys, DNS, money, legal, or product decisions that only the owner can close. Write `none` when there are none. Code defects stay out of this list.

If a stage is passing work instead of reporting a failure, say so directly. For a clean pass, write "none found" under "what broke."

## T2 secrets addendum

Every **T2** packet also includes:

- **ROTATE** - `yes` when a real secret was found and must be rotated, otherwise `no`
- **HISTORY-PII** - redacted `file:line` entries, or `none`. Never include secret values, personal values, or canary contents.
- **Sweep** - scanned-file count, plus the canary result: the personal-info sweep flagged the fake email, working-tree gitleaks flagged the fake key, and the temp file was deleted

T2 runs this gate on every pass. The self-test points the sweep and working-tree gitleaks at a directory that contains the canary, and it comes before the repo scan. A scanned-file count of 0, a missed canary, or a skipped gate fails T2.

## Defect closure

Any defect found at any stage is fixed by the owning altitude and re-verified before the loop exits.

Only **Owner Blockers** may remain when the loop ends.

## Loop exit

The loop is complete only when **T3** passes with no unfixed defects. The final report lists:

- stages run
- restarts
- fixes
- tests
- **ROTATE**, from the latest T2 packet
- **HISTORY-PII**, from the latest T2 packet
- **Owner Blockers**

## Anti-stall

Each stage produces this packet before the run ends. Announcing that a stage will start does not finish the turn.

## Route map

- **Blast, Mid, and Inner** can hand off among themselves when scope changes or new ownership becomes clear.
- **Build work always goes to T1 first** before T2 or T3.
- **T1** either passes to **T2** or routes correction back to **Blast**, **Mid**, or **Inner**. After correction, **T1** runs again.
- **T2** fixes the bug or ops issue directly, or escalates to **T1** when the issue is structural. Every T2 pass runs the secrets and privacy gate, including the canary self-test.
- **T3** passes only when no unfixed defects remain. A structural issue goes to **T1** for restart. A non-structural defect goes to **T2**, then **T3** runs again.
- **T3 restart loop:** **T3 -> T1 -> Blast/Mid/Inner correction -> T2 -> T3**
- When the system has accounts or tenants, **T3** checks that another account's resource returns **404** or **403** on read, export, rescan, cancel, and delete. **200**, **303**, and **409** on that path are defects.
- **Never skip ownership.** If another altitude owns the decision, send the packet there instead of deciding for it.
- **Do not merge or silently apply another layer's fix.** Recommenders can suggest; owners choose and implement.

## Anti-patterns

- quiet local patches for structural issues
- T2 inventing architecture to get a bug green
- Inner redesigning Blast-owned structure
- Mid silently changing a system contract without Blast owning it
- T1 or T3 prescribing detailed implementation for another altitude
- silent assumptions about who owns the next move
- a stage merging or quietly applying another layer's fix because the recommendation seemed obvious
- leaving a found defect reported and unfixed at loop exit
- filing a code defect under **Owner Blockers**
- a secrets sweep that reports clean after scanning 0 files or missing its canary
- printing secret or personal values in a packet
- ending a turn after only announcing that a stage will start
