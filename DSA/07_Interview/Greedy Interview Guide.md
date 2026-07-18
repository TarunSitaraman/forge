---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/greedy]
canonical: true
related: [[[Greedy]], [[Coding Interviews]], [[Communicating Thought Process]]]
---
# Greedy Interview Guide

This guide prepares you to communicate and defend Greedy solutions in technical interviews.

## Pattern Recognition (30 seconds)

**Green Flags for Greedy:**
- Local optimal choice leads to global optimal (provable)
- Interval scheduling ("maximum non-overlapping")
- Sorting by some metric then processing in order works
- No need to reconsider past choices

**Yellow Flags:**
- "Optimal" but choices affect future options unpredictably → might need DP

**Red Flags (probably NOT Greedy):**
- Can't prove local choice is always safe
- Counter-example exists where greedy fails (0-1 knapsack)

## Communication Template

```
Interviewer: "Maximum number of non-overlapping intervals."

You: "I'll sort by end time, then greedily select intervals that 
don't overlap with the last selected one.

Proof of correctness: Among all valid choices, selecting the interval 
with earliest end time leaves maximum room for future intervals. 
This greedy choice never leads to a worse outcome than any other choice."
```

## The Core Mechanics

```python
def greedy_solution(items):
    # 1. Sort by relevant metric
    items.sort(key=lambda x: x.relevant_property)
    
    # 2. Greedily process
    result = []
    for item in items:
        if is_valid_choice(item, result):
            result.append(item)
    
    return result
```

## Variations & Pressure Test

**Interviewer:** "How do you know greedy works here, not DP?"

Your Response:
- "I can prove exchange argument: any optimal solution can be transformed 
  to match greedy's choice without losing optimality."
- "If I can't prove this, I'd default to DP for safety."

**Interviewer:** "What if elements can be used multiple times (unbounded)?"

Your Response:
- "Still greedy if ratio-based (value/weight) sorting captures the tradeoff."
- "Classic example: fractional knapsack (greedy works) vs 0-1 knapsack (needs DP)."

## Common Mistakes

### Mistake 1: Wrong Sort Order
```python
# ❌ WRONG - sorting by start time for interval scheduling
intervals.sort(key=lambda x: x[0])

# ✓ CORRECT - sort by end time
intervals.sort(key=lambda x: x[1])
```

### Mistake 2: Not Proving Greedy Choice
```
# ❌ WEAK: "I'll just pick the biggest one each time"
# ✓ STRONG: "I pick the earliest-ending interval because it leaves 
#           maximum room for subsequent choices—provable via exchange argument"
```

### Mistake 3: Assuming Greedy Works Without Verification
- Always test with small counter-example before committing
- If greedy fails on any test case, switch to DP

## The Greedy Proof Framework

1. **Identify the choice:** What local decision are we making?
2. **State the claim:** Why is this choice never worse than alternatives?
3. **Exchange argument:** Show any optimal solution can be modified to match greedy's first choice without losing optimality
4. **Induction:** Apply same logic to remaining subproblem

## Practice Problems By Category

### Category 1: Interval Scheduling
- Non-overlapping Intervals
- Meeting Rooms
- Merge Intervals

### Category 2: Optimization
- Jump Game
- Gas Station
- Task Scheduler

### Category 3: Construction
- Assign Cookies
- Candy Distribution
- Reorganize String

## The Checklist

- [ ] Identified the greedy choice clearly
- [ ] Attempted proof (exchange argument or similar)
- [ ] Tested with counter-examples to verify
- [ ] Confirmed sort order matches the greedy strategy
- [ ] Compared with DP alternative if uncertain

## Related Interview Content
- [[Coding Interviews]]
- [[Communicating Thought Process]]
- [[Greedy]] — Pattern deep dive
- [[Greedy Representative Problems]] — Practice problems

