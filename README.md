# BlastLoop

BlastLoop is Ross Shaw's practical coding framework for AI agents: **Blast**, **Mid**, and **Inner** write code at different altitudes, then **T1 structural**, **T2 bug/ops**, and **T3 adversarial** test it in order.

**Why it exists:** AI coding agents make teams faster, but they also make failure faster. BlastLoop applies an operations mindset to agent work: each change has an owner at the right altitude, and every result passes structural, operational and adversarial gates in a fixed order before it ships.

> **Open newsletter / public write-up is live.**
> Fuller BlastLoop explanation: WORLDWRIGHT Issue 3, [AI Did Not Remove Engineering Failure. It Made It Faster.](https://worldwright.beehiiv.com/p/ai-did-not-remove-engineering-failure)

**Version:** 1.0.0
**Tagline:** structure first, function second, attack third.

## Use BlastLoop with an AI coding agent in under 5 minutes

1. Clone this repo into the project where you want agent help.
2. Point your agent at [`AGENTS.md`](./AGENTS.md).
3. Load one build prompt based on the altitude of the work:
   - system or repo shape -> [`prompts/blast.md`](./prompts/blast.md)
   - subsystem or feature slice -> [`prompts/mid.md`](./prompts/mid.md)
   - local file or unit change -> [`prompts/inner.md`](./prompts/inner.md)
4. Have the agent write code at that altitude. BlastLoop is **not** spec first, plan first, then one implementer. The build layer should produce code at its own scope.
5. Run the test loop in order:
   - [`prompts/t1-structural.md`](./prompts/t1-structural.md)
   - [`prompts/t2-bug-ops.md`](./prompts/t2-bug-ops.md)
   - [`prompts/t3-adversarial.md`](./prompts/t3-adversarial.md)
6. If T3 finds a structural problem, restart through T1, route correction back to Blast, Mid, or Inner, then run T2 and T3 again.

That is the whole public cut: load the right prompt, write at the right altitude, then test in the right order.

See [`docs/HANDOFFS.md`](./docs/HANDOFFS.md) for the required handoff packet and routing rules. A stage may recommend a fix from its own vantage, but the owning altitude still decides and implements the change.

## B / M / I: three coding altitudes

### Blast
**Blast** writes system-level code and structure.

Use Blast when the job changes repo shape, service boundaries, top-level interfaces, shared contracts, deployment structure, or the skeleton of a new capability.

### Mid
**Mid** writes subsystem code.

Use Mid when the job is bigger than a local patch but smaller than a system redesign: a feature slice, a service module, an integration surface, or a subsystem refactor.

### Inner
**Inner** writes local unit code.

Use Inner when the job is contained to a few files, functions, tests, or adapters. Inner is the local-unit altitude in BlastLoop: focused scope, clear ownership, and direct completion of the change.

## T1 -> T2 -> T3 test loop

Run tests in this order.

### T1 structural
T1 looks for structural issues: wrong boundaries, logic at the wrong altitude, leaky abstractions, broken file ownership, missing interfaces, or misplaced cross-cutting concerns.

T1 does not behave like a general bug fixer. Its job is to decide whether the code is shaped correctly and to route correction back to the right writer: Inner, Mid, or Blast.

### T2 bug / ops
T2 fixes bugs and operational issues directly when the structure is already sound.

If T2 discovers the issue is actually architectural, it escalates back to T1 instead of papering over the problem locally.

### T3 adversarial
T3 attacks the result: edge cases, misuse, race conditions, weird inputs, brittle assumptions, security posture, rollback paths, and failure behavior.

If T3 finds a **structural** problem, the loop restarts like this:

**T3 -> T1 -> B/M/I correction -> T2 -> T3**

That restart rule matters. Structural breakage should be repaired structurally, not patched over as a local bug.

## What this is not

- **Not a fact-checker.** BlastLoop is a coding and testing framework, not a truth engine.
- **Not GitHub Spec Kit.** Spec Kit is spec -> plan -> tasks -> implement -> converge. BlastLoop is multiple coding altitudes plus a staged test loop.
- **Not a competing "agent style."** BlastLoop can work with Spec Kit, vibe coding, focused local implementation prompts, or red/blue testing. Those can sit inside an altitude or a test layer; BlastLoop is the structure-first loop that routes them.
- **Not a multi-agent org chart.** This public cut does not define management layers, bot hierarchies, or private automation.
- **Not red/blue testing alone.** Adversarial testing is T3 only, not the whole method.

## Works with other agent styles

BlastLoop does not require one coding style or one planning school.

- Spec Kit can shape planning before code is written.
- Vibe coding or other implementation styles can operate inside Blast, Mid, or Inner, depending on scope.
- Focused local implementation prompts can be used inside Inner when they fit the task.
- Red/blue testing fits naturally in T3 adversarial work.

BlastLoop's job is to route the work: choose the right altitude, then run T1 -> T2 -> T3 in order.

See [`docs/COMPARISON.md`](./docs/COMPARISON.md) for a short side-by-side.

## Repository contents

- [`AGENTS.md`](./AGENTS.md) - routing card for which prompt to load
- [`prompts/`](./prompts/) - loadable instructions for each build and test role
- [`docs/HANDOFFS.md`](./docs/HANDOFFS.md) - required handoff packet and ownership rules
- [`docs/COMPARISON.md`](./docs/COMPARISON.md) - short comparison to adjacent public methods
- [`examples/session.md`](./examples/session.md) - fictional walkthrough of a small feature through the loop

## License

MIT. See [`LICENSE`](./LICENSE).
