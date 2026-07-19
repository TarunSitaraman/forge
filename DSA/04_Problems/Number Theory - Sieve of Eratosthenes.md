---
type: problem
status: stable
tags: [dsa/problem, dsa/number-theory]
canonical: true
related: [[[Number Theory]], [[Sieve of Eratosthenes]]]
difficulty: medium
---
# Number Theory - Count Primes (Sieve of Eratosthenes)

## Problem Statement
Given an integer n, count the number of prime numbers strictly less than n.

**Constraints:**
- 0 ≤ n ≤ 5×10^6
- Must be efficient enough for large n (checking each number individually is too slow)

## Classification
- **Patterns:** [[Number Theory]]
- **Category:** Prime generation via elimination

## Pattern Selection
This is canonical for Number Theory because it demonstrates the Sieve of Eratosthenes—an elegant O(n log log n) algorithm that beats naive O(n√n) primality checking for each number.

## Why Sieve Works
**Key Insight:** Instead of checking if each number is prime individually, mark all MULTIPLES of each prime as composite. Starting from 2, mark 4,6,8...; from 3, mark 6,9,12...; etc. Numbers never marked are prime.

## Python Solution
```python
def countPrimes(n: int) -> int:
    """
    Count primes less than n using Sieve of Eratosthenes.
    Time: O(n log log n)
    Space: O(n) for boolean array
    """
    if n < 3:
        return 0
    
    is_prime = [True] * n
    is_prime[0] = is_prime[1] = False
    
    for i in range(2, int(n ** 0.5) + 1):
        if is_prime[i]:
            # Mark all multiples of i as composite, starting from i*i
            for j in range(i * i, n, i):
                is_prime[j] = False
    
    return sum(is_prime)
```

## Reasoning
1. **Initialize:** Assume all numbers prime initially (except 0, 1)
2. **Iterate to √n:** Only need to check up to √n since any composite number has a factor ≤ √n
3. **Mark Multiples:** For each prime i found, mark i², i²+i, i²+2i... as composite (start from i² since smaller multiples already marked by smaller primes)
4. **Count Remaining:** Sum of True values gives prime count

## Complexity Analysis
- **Time:** O(n log log n) — harmonic-like series sum across all primes
- **Space:** O(n) — boolean array

## Edge Cases & Validation
```python
assert countPrimes(0) == 0
assert countPrimes(1) == 0
assert countPrimes(2) == 0  # No primes less than 2
assert countPrimes(10) == 4  # 2,3,5,7
assert countPrimes(100) == 25
```

## Common Mistakes
1. **Starting Multiples from 2i Instead of i²:** Less efficient but not incorrect; i² optimization avoids redundant marking (2i, 3i already marked by prime 2, 3 respectively when i>2)
2. **Checking All Numbers Individually:** O(n√n) instead of O(n log log n)—misses the sieve's elimination efficiency
3. **Off-by-One:** Counting primes ≤ n instead of < n (must match exact problem requirement)

## Interview Walkthrough
**Interviewer:** "Count primes less than n, efficiently."

**Your Response:**
1. "Checking each number's primality individually is O(n√n)—too slow for large n."
2. "Instead, I'll use Sieve of Eratosthenes: mark all multiples of each prime as composite."
3. "Starting from i² (not 2i) avoids redundant work, since smaller multiples are already marked."
4. "This achieves O(n log log n), which is nearly linear in practice."

## Related Problems
- [[Number Theory - GCD and LCM]] (different number theory technique)
- [[Number Theory Representative Problems]]

## Related Concepts
- [[Number Theory]] — prime number theory
- [[Sieve of Eratosthenes]] — specific algorithm

