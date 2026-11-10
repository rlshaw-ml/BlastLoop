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

If a stage is passing work instead of reporting a failure, say so directly. For a clean pass, write "none found" under "what broke." When **Public surface and abuse economics** is required, "none found" does not replace that section. The eight answers still sit in the T3 packet.

## T3 packet: Public surface and abuse economics

On every T3 pass, the handoff includes a completed **Public surface and abuse economics** section, in addition to the packet above, when the product is publicly reachable or spends money per request. Per-request spend includes a paid API, a model call, email, SMS, or any other metered call. The same section is defined in [`prompts/t3-adversarial.md`](../prompts/t3-adversarial.md). Keep the two lists the same.

Skip the section only when the product has no public door and no per-request spend. Say why in one line. Any other skip is incomplete.

A public door is any path someone outside the project can reach: an unauthenticated route, a session, a web UI, MCP, a CLI, a webhook, a published package, a public domain, or public docs.

T3 writes this section during its review. A finished section with answers that hold can pass. A missing or incomplete section, when the section is required, is a defect and it is structural: the packet omitted the section, or an item is blank, partial, or backed only by a comment. Route that as a restart: **T3 -> T1**, with correction owned by **Mid**. The defect stays open until **Mid** fixes it and **T3** passes again. **T2** and **Inner** do not take it as a local bug. T1 and T2 do not fail a first pass only because T3 has not written the section yet. T3 may recommend a fix. That recommendation is advice. **Mid** decides and implements the correction after **T1** routes it.

Evidence for a limit, reservation, authorization check, price source, or alert is the code you read and a test that fails when that control is skipped. A comment that claims the control exists does not count. For release leftovers, record the check you ran and what it showed.

1. **Unauthenticated routes and second doors** - every unauthenticated route and every second door (sessions, web UI, MCP, CLI, webhooks). A second door is any other path to the same capability. For each, say whether it hits a model, a database, disk, email, or a paid API.
2. **Expensive routes** - for every route or door that spends budget, credits, or a paid call: the budget or credit is reserved atomically before the work; a parallel burst cannot multiply that reservation; a caller who fails auth or the limit is rejected before any work starts (HTTP doors use 401 or 429; other doors use that door's auth or limit error); the limiter is an atomic increment, keyed per user and per trusted IP, and it fails closed. The trusted IP is the platform or proxy client address, not a header the caller sets. If the limiter cannot record the increment, the route rejects the request. Verified with a code read and a test. Item 2 is incomplete when item 1 lists a model call, email send, or paid API and this item says none.
3. **Account cost** - the cost of one free account, and the cost of many accounts opened through aliases, plus-addresses, OAuth, and IP rotation.
4. **Clone surface** - what one response teaches a cloner: fields, versions, examples, fixtures, and published packages.
5. **Object-level authorization** - for every route that takes an object id, slug, or other caller-supplied key, the check that keeps one caller from reading or changing another caller's object.
6. **Price and credit source of truth** - prices and credit grants come from the server. Client-controlled metadata cannot set a price or grant credits.
7. **Alerts** - alerts that fire on route spikes, provider-spend spikes, debit failures, and signup spikes, including the signal and where it is sent.
8. **Release leftovers** - test and debug routes disabled in production; dependency advisories checked; package-name squatting checked (the published name is the one the project owns, and confusing lookalikes already on the registry are noted).

"None" is a complete answer only when the inventory shows that case does not exist, and the packet says which inventory fact makes it none. A blank item, a partial route list, or comment-only evidence is incomplete.

## T3 packet: attack classes

Every T3 packet records the attack classes below. The same classes are defined in [`prompts/t3-adversarial.md`](../prompts/t3-adversarial.md). Keep the two lists the same. They apply to any stack. A named stack in the prompt is only an illustration.

For each class, record one concrete attack you attempted and the evidence: a test, a request and response, or a ruled-out reason. Cover every property in the class. One ruled-out reason can cover a property the product does not have.

A found issue is a defect. It blocks exit until the owning altitude fixes it and **T3** passes again.

1. **Inbound webhooks and callbacks** - signature verification, a timestamp window, replay rejection, idempotent processing, and out-of-order events.
2. **Payment and entitlement abuse** - plan, quantity, coupon, refund, trial, dispute or chargeback, and entitlement changes. Only a server-side ledger decides. The same rule covers any client-writable field (mass assignment).
3. **Model and LLM safety** - direct and indirect prompt injection. Model output stays untrusted: no exec, render, or tool use until it is validated. Hidden prompt, rubric, or formula extraction through an oracle or inversion, from public or free responses. Data leakage through the model. Excessive agency.
4. **Resource and spend exhaustion** - fail-closed ceilings on any metered resource (model tokens, email, SMS, compute, storage), oversized or looping requests, and an operator kill switch that stops paid calls without a deploy.
5. **Agent and tool protocol surfaces** - tool poisoning, untrusted tool output, confused deputy, secrets in tool arguments or results, unsafe shell interpolation in a CLI, and env leakage. This covers MCP and any tool server or plugin.
6. **Session, key, and identity lifecycle** - revoke, expire, and rotate take effect on the next request, with no cache window. Account-linking hijack. Email-change, reset, magic-link, and invite takeover (host header, open redirect, token reuse, token in logs or the referrer). Enumeration and timing on login, signup, and object ids.
7. **Secrets and PII in runtime surfaces** - logs, error payloads, client bundles, CLI or tool output, and analytics. Retention, and whether delete and export are complete.
8. **Supply chain and release** - lockfile integrity, dependency confusion, install scripts, publish provenance and signing, package-name squatting, and advisories.
9. **Classic web** - injection (SQL, command, template, path traversal, SSRF, deserialization), XSS, CSRF on cookie flows, CORS, content security policy and clickjacking, cache and CDN rules on authenticated or private data (`no-store`), security headers, verbose errors, default credentials, and debug routes.
10. **Concurrency and recovery** - races and time-of-check to time-of-use on any balance, quota, or state transition. Idempotency keys on retries. Rollback and recovery paths.

These classes are a floor, not a ceiling. Ask what general class of attack this product would invite that is not listed. Record at least one product-specific novel probe, with the same evidence rule.

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
- attack-class evidence, one line per class
- the product-specific novel probe

## Anti-stall

Each stage produces this packet before the run ends. Announcing that a stage will start does not finish the turn.

## Route map

- **Blast, Mid, and Inner** can hand off among themselves when scope changes or new ownership becomes clear.
- **Build work always goes to T1 first** before T2 or T3.
- **T1** either passes to **T2** or routes correction back to **Blast**, **Mid**, or **Inner**. After correction, **T1** runs again.
- **T2** fixes the bug or ops issue directly, or escalates to **T1** when the issue is structural. Every T2 pass runs the secrets and privacy gate, including the canary self-test.
- **T3** passes only when no unfixed defects remain. A structural issue goes to **T1** for restart. A non-structural defect goes to **T2**, then **T3** runs again.
- **T3 restart loop:** **T3 -> T1 -> Blast/Mid/Inner correction -> T2 -> T3**. When **Public surface and abuse economics** is missing or incomplete, the correction owner is **Mid**: **T3 -> T1 -> Mid -> T2 -> T3**.
- When the system has accounts or tenants, **T3** checks that another account's resource returns **404** or **403** on read, export, rescan, cancel, and delete. **200**, **303**, and **409** on that path are defects.
- On every T3 pass for a product that is publicly reachable or spends money per request, **T3** completes **Public surface and abuse economics**. A one-line skip is allowed only when the product has no public door and no per-request spend.
- On every T3 pass, **T3** records the attack classes and one product-specific novel probe. Each class needs an attempted attack and evidence. A found issue is a defect and blocks exit.
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
- a T3 pass on a publicly reachable or per-request-spend product because a comment says a limit, a budget, or an authorization check exists
- **T2** or **Inner** locally patching a missing or incomplete **Public surface and abuse economics** section instead of restarting through **T1** to **Mid**
- a T3 pass that skips an attack class, or that records a class with no attempted attack and no ruled-out reason
- exiting without the product-specific novel probe
