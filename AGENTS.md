# BlastLoop routing card

## Load the right build prompt

- Load [`prompts/blast.md`](./prompts/blast.md) for system-level structure, repo shape, top-level contracts, or broad architectural moves.
- Load [`prompts/mid.md`](./prompts/mid.md) for subsystem work, feature slices, integrations, or module-level refactors.
- Load [`prompts/inner.md`](./prompts/inner.md) for local file edits, unit changes, targeted bug fixes, and small tests.

## Then run the test loop in order

1. [`prompts/t1-structural.md`](./prompts/t1-structural.md)
2. [`prompts/t2-bug-ops.md`](./prompts/t2-bug-ops.md)
3. [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md)

## Restart rule

If T3 finds a structural problem, do **not** patch around it in place.

Restart like this:

**T3 -> T1 -> route correction to Blast, Mid, or Inner -> T2 -> T3**

## Rule of thumb

**Structure first, function second, attack third.**

## Public release gate

Before any public release, **T3** completes **Public surface and abuse economics**. A public release is npm publish or any other package publish, a new domain, public docs or a help center, a launch post, or, on a product already reachable outside the project, a change that adds or alters an unauthenticated route, a second door, a spend or credit path, a route that takes a caller-supplied object key, or a response someone outside the project can read.

The section lives in [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md) and in the T3 packet in [`docs/HANDOFFS.md`](./docs/HANDOFFS.md).

**T3** writes the section during its review. A missing or incomplete section in the release packet is structural. Restart at **T1**. **Mid** owns the correction.

See [`docs/HANDOFFS.md`](./docs/HANDOFFS.md) for the required handoff packet and route rules. A stage may recommend a fix, but the owning altitude still decides and implements it.
