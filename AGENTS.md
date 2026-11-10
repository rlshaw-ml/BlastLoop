# BlastLoop routing card

## Load the right build prompt

- Load [`prompts/blast.md`](./prompts/blast.md) for system-level structure, repo shape, top-level contracts, or broad architectural moves.
- Load [`prompts/mid.md`](./prompts/mid.md) for subsystem work, feature slices, integrations, or module-level refactors.
- Load [`prompts/inner.md`](./prompts/inner.md) for local file edits, unit changes, targeted bug fixes, and small tests.

## Then run the test loop in order

1. [`prompts/t1-structural.md`](./prompts/t1-structural.md)
2. [`prompts/t2-bug-ops.md`](./prompts/t2-bug-ops.md), including the mandatory secrets and privacy gate
3. [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md), including the cross-tenant check when accounts or tenants exist

## Restart rule

If T3 finds a structural problem, do **not** patch around it in place.

Restart like this:

**T3 -> T1 -> route correction to Blast, Mid, or Inner -> T2 -> T3**

If T3 finds a non-structural defect, send it to **T2**. After T2 fixes it, run **T3** again.

## Close defects before exit

Any defect found at any stage is fixed by the owning altitude and re-verified before the loop exits. A race, a missing signature check, a cross-tenant success, or a rate limit that breaks under parallel calls stays open until that fix is verified.

Only items that need the owner may remain: keys, DNS, money, legal, or product decisions. Put those in the named **Owner Blockers** list in the packet. Write `none` when the list is empty.

## Exit

The loop is complete only when **T3** passes with no unfixed defects. The final report lists stages run, restarts, fixes, tests, **ROTATE**, **HISTORY-PII**, and **Owner Blockers**.

## Anti-stall

Each stage produces its handoff packet before the run ends. A turn that only announces the next stage has not finished that stage.

## Rule of thumb

**Structure first, function second, attack third.**

See [`docs/HANDOFFS.md`](./docs/HANDOFFS.md) for the required handoff packet and route rules. A stage may recommend a fix, but the owning altitude still decides and implements it.
