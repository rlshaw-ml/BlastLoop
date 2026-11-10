# BlastLoop routing card

## Load the right build prompt

- Load [`prompts/blast.md`](./prompts/blast.md) for system-level structure, repo shape, top-level contracts, or broad architectural moves.
- Load [`prompts/mid.md`](./prompts/mid.md) for subsystem work, feature slices, integrations, or module-level refactors.
- Load [`prompts/inner.md`](./prompts/inner.md) for local file edits, unit changes, targeted bug fixes, and small tests.

## Then run the test loop in order

1. [`prompts/t1-structural.md`](./prompts/t1-structural.md)
2. [`prompts/t2-bug-ops.md`](./prompts/t2-bug-ops.md), including the mandatory secrets and privacy gate
3. [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md), including the cross-tenant check when accounts or tenants exist, **Public surface and abuse economics** on every pass for a product that is publicly reachable or spends money per request, and the attack classes on every pass

## Restart rule

If T3 finds a structural problem, do **not** patch around it in place.

Restart like this:

**T3 -> T1 -> route correction to Blast, Mid, or Inner -> T2 -> T3**

If T3 finds a non-structural defect, send it to **T2**. After T2 fixes it, run **T3** again.

## Close defects before exit

Any defect found at any stage is fixed by the owning altitude and re-verified before the loop exits. A race, a missing signature check, a cross-tenant success, or a rate limit that breaks under parallel calls stays open until that fix is verified.

Only items that need the owner may remain: keys, DNS, money, legal, or product decisions. Put those in the named **Owner Blockers** list in the packet. Write `none` when the list is empty.

## Exit

The loop is complete only when **T3** passes with no unfixed defects. The final report lists stages run, restarts, fixes, tests, **ROTATE**, **HISTORY-PII**, **Owner Blockers**, the attack-class evidence, and the novel probe.

## Anti-stall

Each stage produces its handoff packet before the run ends. A turn that only announces the next stage has not finished that stage.

## Rule of thumb

**Structure first, function second, attack third.**

## Public surface and abuse economics

On every **T3** pass, complete **Public surface and abuse economics** when the product is publicly reachable or spends money per request (a paid API, a model call, email, SMS, or any other metered call).

Skip it only when the product has no public door and no per-request spend, and say why in one line.

The section lives in [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md) and in the T3 packet in [`docs/HANDOFFS.md`](./docs/HANDOFFS.md).

**T3** writes the section during its review. A missing or incomplete section, when it is required, is a defect and it is structural. Restart at **T1**. **Mid** owns the correction. It stays open until **T3** passes again.

## Attack classes

On every **T3** pass, run the attack classes in [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md) and in the T3 packet in [`docs/HANDOFFS.md`](./docs/HANDOFFS.md). The classes are inbound webhooks, payment and entitlement abuse, model safety, resource and spend exhaustion, tool-protocol surfaces, session and identity lifecycle, secrets and personal data in runtime surfaces, supply chain, classic web, and concurrency and recovery.

Each class needs one attempted attack and evidence: a test, a request and response, or a ruled-out reason. A found issue is a defect and blocks exit. The list is a floor. Also record one product-specific novel probe.

See [`docs/HANDOFFS.md`](./docs/HANDOFFS.md) for the required handoff packet and route rules. A stage may recommend a fix, but the owning altitude still decides and implements it.

## Three-way test

Name the **UX**, **DX**, and **AX** effect of each finding or change. Convert or redesign only when all three rise or stay even. When a candidate drops one side, leave it unapplied and send it back to the altitude that can raise all three.

Shared definitions, the implicit-state smell, and the defect threshold are in [`docs/UX-DX-AX.md`](./docs/UX-DX-AX.md). The handoff lines are in [`docs/HANDOFFS.md`](./docs/HANDOFFS.md).
