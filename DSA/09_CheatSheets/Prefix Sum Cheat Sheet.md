---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/prefix-sum]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Prefix Sum Cheat Sheet

## Signals
Range sum queries; subarray sum problems; immutable array with repeated queries.

## Core Moves
- Build: prefix[i] = prefix[i-1] + arr[i-1]
- Query range [i,j]: prefix[j+1] - prefix[i]
- For exact-sum counting: hash map tracks prefix occurrences, check prefix-k.

## Complexity
O(n) build, O(1) per query. O(n) space.

## Template Links
- [[Python - Prefix Sum]]

## Watch For
- [[Off-by-One]]
- [[Boundary Errors]]

## Canonical Depth
See [[Prefix Sum]] for full explanation.
