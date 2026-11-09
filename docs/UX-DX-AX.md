# UX, DX, and AX

Every stage names the effect of a finding or a change on user experience (UX), developer experience (DX), and agent experience (AX). Convert or redesign only when all three rise or stay even. When a candidate drops one side, leave it unapplied and send it back to the altitude that can raise all three.

## The three sides

- **UX:** the user is not stuck, and the screen is not contradictory. The flow matches what a person can actually do.
- **DX:** the developer has one readable place for the logic, and the contract is clear.
- **AX:** an agent can name the current state and the legal transitions. Unnamed flags and missing transitions hurt AX.

## Three-way test

Write one line for UX, one for DX, and one for AX. Each line says up, even, or down.

Apply a change only when every line is up or even. A down line sends that candidate back to the altitude that can raise all three. The stage that sees the tradeoff leaves the candidate unapplied. When this stage is that altitude, drop the candidate and choose another change here.

The test adds no verdict. **PASS**, **ROUTE BACK**, escalate, and the **T3 -> T1** restart stay as they are.

## Implicit state

Independent flags that together act as one state are one structural smell under **T1**. Impossible combinations and a stuck state a user can reach belong to that smell. Making the state explicit often lifts UX, DX, and AX together. The smell is not a separate stage and not a separate product.

## Threshold

An impossible combination a user can actually reach is an invalid state, and it is a defect. A stuck state a user can reach is a defect.

Messy flag redundancy that no user can reach is advice-only. That includes a state that is hard to extend when no user can reach an invalid or stuck state. Record it in the state inventory. It does not force a conversion, and the stage can still pass.

## Who does what

- **T1** detects the smell and routes it. The packet lists the flags, the inferred transitions, the impossible combinations, and any stuck state a user can reach. T1 does not convert flags into states. T1 does not fix bugs.
- **Mid** takes the smell when the state crosses a slice (UI, API, webhook).
- **Inner** takes the smell when the state stays in one component.
- **Blast** takes the smell when the same state is a top-level contract across the system.
- **Mid** and **Inner** convert only after the three-way test passes. They pin the behavior users already rely on with tests first, then write named states and the allowed transitions. A reachable stuck state is not behavior to pin.
- **T2** applies a direct fix only when the three-way test passes. When the fix needs a new state shape, T2 escalates to **T1**.
- **T3** attacks from the user side, the developer side, and the agent side. Structural defects go to **T1**. Non-structural defects go to **T2**.

When the stuck or invalid state comes from flags that act as one unnamed state, it is structural. When named states and allowed transitions already exist and one path is simply wrong, it is non-structural.

## T3 probes

- **User:** stuck screens, double submit, back or refresh mid-flow, out-of-order async events such as a webhook before a redirect.
- **Developer:** a state that is hard to extend.
- **Agent:** unnamed flags, missing transitions, a packet that cannot say what state the app is in.

A reachable invalid state is a defect. A reachable stuck state is a defect.
