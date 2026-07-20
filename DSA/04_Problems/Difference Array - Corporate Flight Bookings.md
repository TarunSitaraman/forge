---
type: problem
status: stable
tags: [dsa/problem, dsa/difference-array]
canonical: true
related: [[[Difference Array]], [[Prefix Sum]]]
difficulty: medium
---
# Difference Array - Corporate Flight Bookings

## Problem Statement
There are `n` flights numbered 1 to n. Given a list of bookings
`[first_i, last_i, seats_i]`, meaning `seats_i` seats were reserved for
every flight from `first_i` to `last_i` inclusive, return an array where
`result[i]` is the total seats reserved for flight `i+1`.

**Constraints:**
- Bookings apply to an inclusive range `[first_i, last_i]`
- Multiple bookings can overlap and stack additively

## Classification
- **Patterns:** [[Difference Array]]
- **Category:** Batch range-increment then single final readout

## Pattern Selection
This is the most direct textbook application of Difference Array: many
range-increment updates, then one final query pass — the canonical
signal ("apply this value to every element in this range, repeated many
times") that should trigger reaching for this pattern instead of naively
looping over each range.

## Why Difference Array Works
**Key insight:** instead of adding `seats_i` to every flight in
`[first_i, last_i]` (O(range length) per booking, O(n·bookings) total),
mark only the two boundaries: `diff[first_i] += seats_i` and
`diff[last_i + 1] -= seats_i`. A single final prefix sum reconstructs the
actual per-flight totals in O(n + bookings) overall.

## Python Solution
```python
def corpFlightBookings(bookings: list[list[int]], n: int) -> list[int]:
    """
    Compute total seats per flight using difference array.
    Time: O(n + bookings) instead of O(n * bookings)
    Space: O(n)
    """
    diff = [0] * (n + 1)  # extra slot for the "last+1" boundary marker

    for first, last, seats in bookings:
        diff[first - 1] += seats       # 1-indexed -> 0-indexed
        diff[last] -= seats            # last (0-indexed) is last_i - 1 + 1

    result = [0] * n
    result[0] = diff[0]
    for i in range(1, n):
        result[i] = result[i - 1] + diff[i]

    return result
```

## Reasoning
1. **Mark boundaries only:** for each booking, `+seats` at the start
   index, `-seats` one past the end index — O(1) per booking regardless
   of range length.
2. **Single final prefix sum:** the running sum of the difference array
   at each position equals the true cumulative seat count at that
   flight.
3. **Index care:** flights are 1-indexed in the problem but the array is
   0-indexed — `first_i` maps to `first_i - 1`, and the "one past the
   end" marker for an inclusive `last_i` lands exactly at index `last_i`
   in the 0-indexed array (no extra `+1` needed beyond that shift).

## Complexity Analysis
- **Time:** O(n + bookings) — O(1) per booking to mark, O(n) for the
  final prefix sum pass
- **Space:** O(n) — the difference/result arrays

## Edge Cases & Validation
```python
assert corpFlightBookings([[1,2,10],[2,3,20],[2,5,25]], 5) == [10,55,45,25,25]
assert corpFlightBookings([[1,2,10],[2,2,15]], 2) == [10,25]
assert corpFlightBookings([[1,1,5]], 1) == [5]
```

## Common Mistakes
1. **Off-by-one on the end marker:** since the range is *inclusive* of
   `last_i`, the decrement must land at `last_i` in 0-indexed terms
   (i.e., one past the last affected 0-indexed flight), not `last_i - 1`.
2. **Forgetting the final prefix-sum pass:** the difference array alone
   isn't the answer — it must be accumulated into actual per-flight
   totals.
3. **Looping over each booking's full range directly:** correct but
   O(n·bookings), missing the entire point of the pattern for large
   inputs.

## Interview Walkthrough
**Interviewer:** "Compute total seats booked per flight, given many
range bookings."

**Your Response:**
1. "Instead of adding seats to every flight in each booking's range
   individually, I'll use a difference array — mark only the start and
   one-past-the-end boundaries."
2. "Each booking becomes two O(1) updates instead of an O(range) loop."
3. "One final prefix sum pass reconstructs the actual per-flight totals."
4. "This is O(n + bookings) total instead of O(n * bookings)."

## Variations
1. **Car Pooling:** identical mechanics, but the output is a boolean
   capacity check rather than the raw totals array
   ([[Difference Array - Range Updates]])
2. **Range Addition:** the pattern in its most generic textbook form —
   no domain framing, just raw range increments
3. **My Calendar III (max overlap count):** related but typically solved
   with a sorted event sweep ([[Sweep Line]]) instead, since it needs the
   running maximum, not final per-index totals

## Related Problems
- [[Difference Array - Range Updates]] (Car Pooling — same mechanics,
  boolean output instead of totals)
- [[Difference Array Representative Problems]]

## Related Concepts
- [[Difference Array]] — core range-increment technique
- [[Prefix Sum]] — the final readout step; difference array is its
  inverse operation

