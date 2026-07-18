---
type: problem-pack
status: stable
tags: [dsa/problem, dsa/interval-processing]
canonical: true
---
# Interval Processing Representative Problems

Core problem variants that exercise Interval Processing for scheduling and range problems.

## Variant 1: Interval Merging & Overlapping
**Recognition:** Merge overlapping intervals or check for overlap.
**Mechanics:** Sort by start; merge if overlapping.
**Key Challenge:** Boundary handling (inclusive/exclusive); stable sort.

- [[Interval Processing - Merge Intervals]]
- Variant: Insert interval into sorted list
- Variant: Intersect intervals

## Variant 2: Meetings & Scheduling
**Recognition:** Find meeting rooms needed or schedule without conflicts.
**Mechanics:** Sort events; track active intervals; find maximum overlap.
**Key Challenge:** Handling start/end times; simultaneous events.

- [[Interval Processing - Meeting Rooms]]
- Variant: Sky line problem
- Variant: Employee free time

## Variant 3: Interval Counting & Coverage
**Recognition:** Count/measure coverage of intervals or find uncovered gaps.
**Mechanics:** Sweep line or difference array for coverage.
**Key Challenge:** Overlapping coverage; partial overlaps.

- Variant: Video stitching (minimum intervals to cover range)
- Variant: Data stream as disjoint intervals
- Variant: Teemo attacking (combat duration)

## Variant 4: Interval Queries
**Recognition:** Answer queries about intervals (conflicts, coverage).
**Mechanics:** Pre-process with sorted events; binary search or sweep line.
**Key Challenge:** Offline vs. online; query efficiency.

## Variant 5: Optimization Over Intervals
**Recognition:** Select optimal subset of intervals (maximize count, minimize cost).
**Mechanics:** Greedy: sort by property; select greedily.
**Key Challenge:** Proving greedy choice is optimal.

## Cross-Linking
- **Pattern:** [[Interval Processing]]
- **Related:** [[Sweep Line]], [[Greedy]]
- **Interview Guide:** [[Interval Processing Interview Guide]]

