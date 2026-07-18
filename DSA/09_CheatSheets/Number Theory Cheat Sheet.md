---
type: cheat-sheet
status: stable
tags: [dsa/cheat-sheet, dsa/number-theory]
canonical: true
related: [[Pattern Index]], [[Template Index]]
---
# Number Theory Cheat Sheet

## Signals
GCD/LCM computation; prime checking/generation; modular arithmetic.

## Core Moves
- GCD: Euclidean algorithm gcd(a,b) = gcd(b, a%b)
- LCM: a*b / gcd(a,b)
- Sieve of Eratosthenes: mark composites starting from i*i
- Fast exponentiation: divide-conquer on exponent

## Complexity
O(log(min(a,b))) GCD; O(n log log n) sieve.

## Template Links
- [[Euclidean Algorithm]], [[Sieve of Eratosthenes]], [[Fast Exponentiation]]

## Watch For
- Negative number handling in GCD
- Off-by-one in sieve boundaries

## Canonical Depth
See [[Number Theory]] for full explanation.
