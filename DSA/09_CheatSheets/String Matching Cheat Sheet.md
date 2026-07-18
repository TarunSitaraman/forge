---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/string-matching]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# String Matching Cheat Sheet

## Signals
Find pattern in text; efficient substring search beyond naive O(n*m).

## Core Moves
- KMP: precompute failure function, avoid re-examining matched chars
- Rabin-Karp: rolling hash for O(n+m) average
- Built-in: str.find() for simple cases (often sufficient)

## Complexity
O(n+m) for KMP/Rabin-Karp vs O(n*m) naive.

## Template Links
- [[KMP]], [[Rabin Karp]], [[Boyer Moore]]

## Watch For
- Off-by-one in failure function
- Using naive when large input expected

## Canonical Depth
See [[String Matching]] for full explanation.
