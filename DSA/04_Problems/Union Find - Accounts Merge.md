---
type: problem
status: stable
tags: [dsa/problem, dsa/union-find]
canonical: true
related: [[[Union Find]], [[Disjoint Set]]]
difficulty: medium
---
# Union Find - Accounts Merge

## Problem Statement
Given a list of accounts, each `[name, email1, email2, ...]`, merge
accounts that share at least one email (transitively — if A shares an
email with B, and B shares with C, all three merge). Return the merged
accounts, each with the name followed by sorted, deduplicated emails.

**Constraints:**
- Same name doesn't imply same person — only shared emails do
- Merging is transitive, not just pairwise

## Classification
- **Patterns:** [[Union Find]]
- **Category:** Entity resolution via connectivity, non-integer elements

## Pattern Selection
This is canonical for Union-Find applied to **non-integer, string-keyed**
elements — unlike typical Union-Find problems indexed by `0..n-1`, here
the elements being unioned are email strings, requiring an
email-to-representative-index mapping layer before the standard
Union-Find machinery applies.

## Why Union-Find Works
**Key insight:** treat every email as a node. For each account, union all
of that account's emails together (they belong to the same person by
definition). After processing every account, all emails belonging to the
same real person end up in the same connected component — regardless of
which account first introduced them.

## Python Solution
```python
from collections import defaultdict

def accountsMerge(accounts: list[list[str]]) -> list[list[str]]:
    """
    Merge accounts sharing emails using Union-Find with a string-to-index map.
    Time: O(N*K*α(N*K)) where N=accounts, K=avg emails per account
    Space: O(N*K)
    """
    parent = {}
    owner = {}  # email -> account name, for final output

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]  # path compression
            x = parent[x]
        return x

    def union(x, y):
        root_x, root_y = find(x), find(y)
        if root_x != root_y:
            parent[root_y] = root_x

    # Initialize every email as its own component, union within each account
    for account in accounts:
        name = account[0]
        first_email = account[1]
        for email in account[1:]:
            if email not in parent:
                parent[email] = email
            owner[email] = name
            union(first_email, email)

    # Group emails by their root representative
    groups = defaultdict(list)
    for email in parent:
        groups[find(email)].append(email)

    # Build final output
    result = []
    for root, emails in groups.items():
        result.append([owner[root]] + sorted(emails))

    return result
```

## Reasoning
1. **Map emails, not indices:** since Union-Find is defined over
   `0..n-1` integers, use a dict-based `parent` map keyed by email string
   instead of an array.
2. **Union within each account:** every email in one account belongs to
   the same person, so union them all against the account's first email.
3. **Group by root:** after all unions, `find(email)` groups every email
   by its ultimate connected component.
4. **Attach names, sort emails:** use the `owner` map (any account name
   associated with an email in that component) and sort per the output
   spec.

## Complexity Analysis
- **Time:** O(N·K·α(N·K)) — N accounts, K average emails per account,
  near-constant find/union with path compression
- **Space:** O(N·K) — parent map and owner map sized to total emails

## Edge Cases & Validation
```python
accounts = [
    ["John","johnsmith@mail.com","john_newyork@mail.com"],
    ["John","johnsmith@mail.com","john00@mail.com"],
    ["Mary","mary@mail.com"],
    ["John","johnnybravo@mail.com"]
]
result = accountsMerge(accounts)
# First two "John" accounts merge (share johnsmith@mail.com);
# the "Mary" and third "John" (no shared email) stay separate
assert len(result) == 3

# No shared emails at all -> nothing merges
accounts2 = [["A","a@x.com"], ["B","b@x.com"]]
assert len(accountsMerge(accounts2)) == 2
```

## Common Mistakes
1. **Unioning by account index instead of by email:** merges accounts
   that happen to be adjacent in the input list rather than ones that
   actually share an email — wrong grouping entirely.
2. **Forgetting path compression:** still correct, but degrades to
   O(N·K) per find in the worst case (long chains), losing the near-O(1)
   guarantee.
3. **Using the wrong account name for a merged group:** since a
   component can span multiple accounts (possibly with the same name by
   coincidence, or legitimately duplicated), any owner along that
   component works — but forgetting to build the `owner` map at all is a
   common oversight.

## Interview Walkthrough
**Interviewer:** "Merge accounts that share any email, transitively."

**Your Response:**
1. "This is connectivity — accounts sharing an email should end up in
   the same component, and Union-Find is the natural fit."
2. "Since emails are strings, not `0..n-1` integers, I use a dict-based
   parent map keyed by email instead of an array."
3. "For each account, I union all of its emails together against the
   first one — that captures 'these all belong to one person.'"
4. "After processing every account, grouping emails by their root
   representative gives the final merged sets, transitively, for free."

## Variations
1. **Similar String Groups:** same Union-Find-over-strings pattern, but
   the "connects to" relation is edit-distance-based instead of exact
   email match
2. **Number of Islands II (online queries):** Union-Find over grid
   coordinates with incremental land additions

## Related Problems
- [[Union Find - Number of Connected Components]] (integer-indexed
  baseline version)
- [[Union Find - Redundant Connection]] (cycle detection via union
  return value)
- [[Union Find Representative Problems]]

## Related Concepts
- [[Union Find]] — extended to string-keyed elements via a mapping layer
- [[Disjoint Set]] — underlying data structure

