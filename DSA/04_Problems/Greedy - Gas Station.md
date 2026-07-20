---
type: problem
status: stable
tags: [dsa/problem, dsa/greedy]
canonical: true
related: [[[Greedy]]]
difficulty: medium
---
# Greedy - Gas Station

## Problem Statement
There are `n` gas stations in a circle. Station `i` has `gas[i]` fuel and
it costs `cost[i]` fuel to travel from station `i` to station `i+1`.
Starting with an empty tank at some station, determine the starting
station index from which you can complete the full circuit, or -1 if
impossible. If a solution exists, it's guaranteed unique.

**Constraints:**
- Circular route — station n-1 connects back to station 0
- Sum of all gas may or may not be enough to cover sum of all costs

## Classification
- **Patterns:** [[Greedy]]
- **Category:** Single-pass greedy with a global feasibility precheck

## Pattern Selection
This is canonical for the "two intertwined greedy facts" pattern: one
fact establishes *whether* a solution exists at all (global sum check),
and a second, non-obvious fact identifies exactly *where* it starts —
both computed in a single O(n) pass.

## Why Greedy Works

**Fact 1 (feasibility):** a valid start exists if and only if
`sum(gas) >= sum(cost)`. If total gas is less than total cost, no
starting point can possibly work, full stop.

**Fact 2 (which start, the non-obvious part):** if a solution exists, the
correct starting station is the one **right after** the point where the
running tank balance hits its lowest value. Equivalently: if the tank
goes negative starting from candidate `start`, no station between `start`
and the point of failure could have been a valid start either — so jump
straight past the failure point.

**Why skipping is safe:** suppose starting at `start` fails at station
`i` (tank goes negative). For any station `j` with `start <= j < i`,
starting at `j` accumulates a tank balance that is a *prefix* of the
`start`-to-`i` failure — meaning it's non-negative up to `j`'s own
partial sum but still runs out by `i` (or sooner). So none of
`start..i-1` can be the answer; the next candidate is `i+1`.

## Python Solution
```python
def canCompleteCircuit(gas: list[int], cost: list[int]) -> int:
    """
    Find the unique valid starting station using a single greedy pass.
    Time: O(n)
    Space: O(1)
    """
    total_surplus = 0
    tank = 0
    start = 0

    for i in range(len(gas)):
        diff = gas[i] - cost[i]
        total_surplus += diff
        tank += diff

        if tank < 0:
            # This start (and everything since the last reset) fails.
            # The next station is the only remaining candidate.
            start = i + 1
            tank = 0

    return start if total_surplus >= 0 else -1
```

## Reasoning
1. **Single pass, two running totals:** `total_surplus` tracks the
   global feasibility fact; `tank` tracks the current candidate
   segment's running balance.
2. **On tank going negative:** the current `start` candidate (and every
   station since the last reset) is proven invalid — jump `start` to
   `i + 1` and reset `tank` to 0.
3. **After the loop:** `start` holds the only station that could
   possibly work. Confirm feasibility with `total_surplus >= 0` before
   returning it.

## Complexity Analysis
- **Time:** O(n) — single pass, O(1) work per station
- **Space:** O(1) — three running variables

## Edge Cases & Validation
```python
assert canCompleteCircuit([1,2,3,4,5], [3,4,5,1,2]) == 3
assert canCompleteCircuit([2,3,4], [3,4,3]) == -1
assert canCompleteCircuit([5], [4]) == 0        # single station, surplus
assert canCompleteCircuit([3], [4]) == -1       # single station, deficit
assert canCompleteCircuit([0,0], [0,0]) == 0    # exact break-even everywhere
```

## Common Mistakes
1. **Checking `sum(gas) >= sum(cost)` but then brute-forcing the start
   with a nested O(n²) simulation:** works, but misses the O(n) greedy
   insight entirely — a very common "correct but suboptimal" submission.
2. **Not resetting `tank` to 0 on failure:** carrying over a negative
   balance corrupts the next candidate segment's calculation.
3. **Forgetting the final feasibility check:** the loop always leaves
   *some* `start` value even when no solution exists — must gate the
   return on `total_surplus >= 0`, otherwise an infeasible circuit
   returns a bogus station index instead of -1.

## Interview Walkthrough
**Interviewer:** "Find the starting gas station to complete the circuit,
or say it's impossible."

**Your Response:**
1. "First, a solution can only exist if total gas ≥ total cost — that's
   a simple sum comparison, O(n)."
2. "For *where* to start, here's the key insight: if starting at station
   `start` runs the tank negative by station `i`, then no station
   between `start` and `i` could have worked either — they'd all fail by
   at least `i`. So I can jump straight to `i+1` as the next candidate."
3. "That means a single pass, resetting my candidate start and tank
   balance every time it goes negative, finds the unique answer in
   O(n) with O(1) space."

## Variations
1. **Non-circular version:** simpler — a linear route only needs the
   running-balance reset logic, no wraparound
2. **Multiple valid starts allowed (if guarantee removed):** would
   require returning all candidates, not just one

## Related Problems
- [[Greedy - Jump Game]] (different greedy invariant: max-reach tracking)
- [[Greedy Representative Problems]]

## Related Concepts
- [[Greedy]] — combined feasibility + single-pass locate pattern

