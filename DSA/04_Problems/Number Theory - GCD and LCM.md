---
type: problem
status: stable
tags: [dsa/problem, dsa/number-theory]
canonical: true
related: [[[Number Theory]], [[Euclidean Algorithm]]]
difficulty: easy
---
# Number Theory - GCD and LCM

## Problem Statement
Implement functions to compute the Greatest Common Divisor (GCD) and Least Common Multiple (LCM) of two integers.

**Constraints:**
- Handle positive integers
- GCD(0, n) = n by convention

## Classification
- **Patterns:** [[Number Theory]]
- **Category:** Fundamental number theory algorithms

## Pattern Selection
This is canonical for Number Theory because Euclidean algorithm demonstrates elegant recursive reduction using modular arithmetic.

## Why Euclidean Algorithm Works
**Key Insight:** gcd(a,b) = gcd(b, a mod b). This works because any common divisor of a and b also divides (a mod b).

## Python Solution
```python
def gcd(a: int, b: int) -> int:
    """
    Compute GCD using Euclidean algorithm.
    Time: O(log(min(a,b)))
    Space: O(1) iterative, O(log(min(a,b))) if recursive
    """
    while b:
        a, b = b, a % b
    return a

def lcm(a: int, b: int) -> int:
    """
    Compute LCM using relationship: lcm(a,b) = a*b / gcd(a,b)
    """
    return a * b // gcd(a, b)

def gcdRecursive(a: int, b: int) -> int:
    """Recursive version for clarity."""
    if b == 0:
        return a
    return gcdRecursive(b, a % b)
```

## Reasoning
1. **GCD via Modulo:** Repeatedly replace (a,b) with (b, a mod b) until b=0
2. **Termination:** a mod b strictly decreases, guaranteeing termination
3. **LCM Formula:** Derived from gcd relationship: a*b = gcd(a,b) * lcm(a,b)

## Complexity Analysis
- **Time:** O(log(min(a,b))) — each step roughly halves the smaller number
- **Space:** O(1) iterative

## Edge Cases & Validation
```python
assert gcd(12, 8) == 4
assert gcd(17, 5) == 1  # Coprime numbers
assert gcd(0, 5) == 5
assert lcm(4, 6) == 12
assert lcm(1, 1) == 1
```

## Common Mistakes
1. **Not Handling GCD(0, n):** Should return n, not error
2. **Integer Overflow in LCM:** For very large numbers, a*b might overflow before division (less issue in Python)
3. **Wrong Order of Operations:** Must divide by gcd AFTER multiplying, or divide one number by gcd first to avoid overflow

## Interview Walkthrough
**Interviewer:** "Compute GCD of two numbers efficiently."

**Your Response:**
1. "Euclidean algorithm: gcd(a,b) = gcd(b, a mod b), repeat until b=0."
2. "This works because common divisors of a,b also divide a mod b."
3. "For LCM, use relationship: lcm(a,b) = a*b/gcd(a,b)."

## Related Problems
- [[Number Theory Representative Problems]]

## Related Concepts
- [[Number Theory]] — foundational algorithms
- [[Euclidean Algorithm]] — specific algorithm implementation

