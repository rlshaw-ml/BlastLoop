# Handoffs

## Why handoffs exist

In BlastLoop, one layer can know there is a problem without owning the best fix.

Seeing the problem and deciding the fix are different responsibilities. The sender's job is to pass a usable packet. The owning altitude's job is to decide what changes and edit the code at that scope.

A stage may recommend a fix that would work from its own vantage. That recommendation is advisory only. Ultimate responsibility for choosing and implementing the change stays with the owning altitude: **Blast**, **Mid**, or **Inner** when **T1** routes structural work, or **T2** for non-structural bug and ops work.

## Required packet for every handoff

Every handoff must include:

1. **What broke or what changed** - the problem found or the work completed
2. **Where it was observed** - file, module, endpoint, test, runtime path, or review point
3. **Suspected owning altitude** - Blast, Mid, Inner, T1, T2, or T3
4. **Evidence** - failing behavior, code pointer, log, test output, or attack path
5. **What the sender refuses to decide** - the decision that belongs to the next owner
6. **Suggested next owner** - the layer that should take the next action
7. **Optional recommendation** - a possible fix from the sender's vantage, clearly marked as advice and not a decision

If a stage is passing work instead of reporting a failure, say so directly. For a clean pass, write "none found" under "what broke." On a public release, "none found" does not replace the **Public surface and abuse economics** section. The eight answers still sit in the T3 packet.

## T3 packet: Public surface and abuse economics

Before any public release, the T3 handoff includes a completed **Public surface and abuse economics** section, in addition to the packet above. The same section is defined in [`prompts/t3-adversarial.md`](../prompts/t3-adversarial.md). Keep the two lists the same.

A public release is any of these:

- npm publish, or any other package-registry publish
- a new public domain
- public docs or a help center
- a launch post or other public announcement
- on a product already reachable outside the project: a change that adds or alters an unauthenticated route, a second door, a spend or credit path, a route that takes a caller-supplied object key, or a response someone outside the project can read

If the product is already public, refresh the section on the next T3 before a further public release.

Every T3 packet states whether a public release is in scope. When none of the actions above is in scope, say that in one line and name the reason. When one is in scope, answer all eight items below.

T3 writes this section during its review. A finished section with answers that hold can pass. A missing or incomplete section on a public release is structural: the release packet omitted the section, or an item is blank, partial, or backed only by a comment. Route that as a restart: **T3 -> T1**, with correction owned by **Mid**. **T2** and **Inner** do not take it as a local bug. T1 and T2 do not fail a first pass only because T3 has not written the section yet. T3 may recommend a fix. That recommendation is advice. **Mid** decides and implements the correction after **T1** routes it.

Evidence for a limit, reservation, authorization check, price source, or alert is the code you read and a test that fails when that control is skipped. A comment that claims the control exists does not count. For release leftovers, record the check you ran and what it showed.

1. **Unauthenticated routes and second doors** - every unauthenticated route and every second door (sessions, web UI, MCP, CLI, webhooks). A second door is any other path to the same capability. For each, say whether it hits a model, a database, disk, email, or a paid API.
2. **Expensive routes** - for every route or door that spends budget, credits, or a paid call: the budget or credit is reserved atomically before the work; a parallel burst cannot multiply that reservation; a caller who fails auth or the limit is rejected before any work starts (HTTP doors use 401 or 429; other doors use that door's auth or limit error); the limiter is an atomic increment, keyed per user and per trusted IP, and it fails closed. The trusted IP is the platform or proxy client address, not a header the caller sets. If the limiter cannot record the increment, the route rejects the request. Verified with a code read and a test. Item 2 is incomplete when item 1 lists a model call, email send, or paid API and this item says none.
3. **Account cost** - the cost of one free account, and the cost of many accounts opened through aliases, plus-addresses, OAuth, and IP rotation.
4. **Clone surface** - what one response teaches a cloner: fields, versions, examples, fixtures, and published packages.
5. **Object-level authorization** - for every route that takes an object id, slug, or other caller-supplied key, the check that keeps one caller from reading or changing another caller's object.
6. **Price and credit source of truth** - prices and credit grants come from the server. Client-controlled metadata cannot set a price or grant credits.
7. **Alerts** - alerts that fire on route spikes, provider-spend spikes, debit failures, and signup spikes, including the signal and where it is sent.
8. **Release leftovers** - test and debug routes disabled in production; dependency advisories checked; package-name squatting checked (the published name is the one the project owns, and confusing lookalikes already on the registry are noted).

On a public release, "none" is a complete answer only when the inventory shows that case does not exist, and the packet says which inventory fact makes it none. A blank item, a partial route list, or comment-only evidence is incomplete.

## Route map

- **Blast, Mid, and Inner** can hand off among themselves when scope changes or new ownership becomes clear.
- **Build work always goes to T1 first** before T2 or T3.
- **T1** either passes to **T2** or routes correction back to **Blast**, **Mid**, or **Inner**.
- **T2** either fixes the bug or ops issue directly, or escalates to **T1** when the issue is structural.
- **T3** either passes the result or sends a structural issue to **T1** for restart. A missing or incomplete **Public surface and abuse economics** section on a public release is one of those structural issues. **T1** routes that correction to **Mid**.
- **T3 restart loop:** **T3 -> T1 -> Blast/Mid/Inner correction -> T2 -> T3**. For this section, the correction owner is **Mid**: **T3 -> T1 -> Mid -> T2 -> T3**.
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
- a public release that passes because a comment says a limit, a budget, or an authorization check exists
- **T2** or **Inner** locally patching a missing or incomplete **Public surface and abuse economics** section instead of restarting through **T1** to **Mid**
