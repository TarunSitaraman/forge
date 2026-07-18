---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/number-theory]
canonical: true
related: [[[Number Theory]], [[Coding Interviews]]]
---
# Number Theory Interview Guide

## Pattern Recognition
**Green Flags:** GCD/LCM computation, prime checking/generation, modular arithmetic, divisor counting

## Communication Template
`For GCD, I'll use Euclidean algorithm: gcd(a,b) = gcd(b, a%b), terminating when b=0.`

## Core Mechanics
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a

def sieve_of_eratosthenes(n):
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n + 1, i):
                is_prime[j] = False
    return [i for i in range(n+1) if is_prime[i]]
```

## Common Mistakes
1. Off-by-one in sieve boundary conditions
2. Not handling negative numbers in GCD
3. Integer overflow in modular exponentiation (less an issue in Python)

## Practice Problems
- GCD/LCM, Sieve of Eratosthenes, Fast Exponentiation

## Checklist
- [ ] Verified edge cases (0, negative numbers)
- [ ] Confirmed O(n log log n) for sieve
- [ ] Modular arithmetic correctly applied where needed

## Related
- [[Number Theory]]
- [[Number Theory Representative Problems]]
