# System Design — Interview-Style Walkthrough

*Purpose:* Drive a system design exercise (interview or real design) through requirements-first structured reasoning, instead of jumping straight to a diagram of components.

## When to use
Practicing for a system design interview, or kicking off a real greenlight design doc for a new system.

## Expected input
The system to design (e.g. "design a URL shortener," "design a notification system"), and any given constraints; if none given, state you want them elicited.

## Expected output
A structured walkthrough: requirements, scale estimation, high-level design, deep dive on the hardest component, and tradeoffs — in that order.

## The complete prompt
```
Walk through designing this system, interview-style: {system to design}
Given constraints: {constraints, or "none given — elicit them"}

Follow this order strictly, don't jump to a diagram first:
1. Functional requirements — the 3-5 core things the system must do.
   If ambiguous, state the assumption explicitly rather than guessing
   silently.
2. Non-functional requirements — scale (users, QPS, data volume),
   latency, consistency vs. availability needs. Do rough back-of-envelope
   estimation, showing the math.
3. High-level design — the core components and data flow, justified by
   the requirements above (not a generic reference architecture).
4. Deep dive — pick the single hardest problem this design creates (e.g.
   "how do we guarantee uniqueness at this write volume without a single
   point of contention") and solve it in detail.
5. Tradeoffs — state what this design sacrifices and under what growth
   condition it would need to change.

At each step, if I haven't given enough information, ask rather than
assume for anything that would meaningfully change the design.
```

## Variants
- **Interview-practice variant:** ask the model to also play interviewer, probing with follow-up questions after the design.
- **Real design-doc variant:** extend deep dive to cover 2-3 hard problems, not just one, and produce a written doc format.

## Related prompts
- [[architecture-decision-record]]
- [[scalability-stress-test-review]]
- [[service-boundary-analysis]]

## Tips
- Always require rough numeric estimation (QPS, storage) with shown math — vague scale talk ("a lot of users") produces vague design choices.
- Insist requirements come before components; a design built before requirements are stated is unjustifiable by construction.

## Common mistakes
- Sketching a component diagram before establishing scale, producing a design that's either wildly over- or under-engineered.
- Picking an easy "deep dive" topic instead of the system's actual hardest problem.

## Example
Input: "design a rate limiter," constraint = "must work across a distributed fleet of API servers."
Expected output: requirements (per-user limits, fairness), scale estimate, high-level design (token bucket + centralized counter store), deep dive on the distributed-counter consistency problem, tradeoffs on strict vs. eventual consistency.
