---
type: interview-guide
status: stable
tags: [dsa/interview, dsa/hashing]
canonical: true
related: [[[Hashing]], [[Coding Interviews]]]
---
# Hashing Interview Guide

## Pattern Recognition
**Green Flags:** O(1) lookup needed; duplicate detection; frequency counting; two-sum style problems

## Communication Template
`Hash map gives O(1) average lookup, letting me check 'have I seen this before' or 'what's the complement I need' in constant time instead of O(n) scan.`

## Core Mechanics
```python
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

## Common Mistakes
1. Using mutable objects as dict keys (fails, need hashable types)
2. Not handling hash collisions conceptually (though Python handles internally)
3. Checking existence before insertion in wrong order (self-matching bugs)

## Practice Problems
- Two Sum, Group Anagrams, Top K Frequent Elements

## Checklist
- [ ] Confirmed O(1) average time operations
- [ ] Verified key types are hashable
- [ ] Checked insertion/lookup order doesn't cause self-matching

## Related
- [[Hashing]]
- [[Hashing Representative Problems]]
