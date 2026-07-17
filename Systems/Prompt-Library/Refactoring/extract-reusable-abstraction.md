# Extract a Reusable Abstraction

## Purpose
Decide whether duplicated logic across multiple call sites should
actually be extracted into a shared abstraction — and if so, design that
abstraction to fit the real variation across call sites, not just the
two you happened to notice.

## When to Use
When you spot the same logic in 2+ places and are tempted to extract a
helper, or during a refactor when "this looks duplicated" comes up.

## Expected Inputs
The duplicated code from each call site, and enough context to see where
the call sites' needs actually differ (not just where they look similar).

## Expected Outputs
A recommendation on whether to extract (sometimes the answer is no), and
if yes, a proposed abstraction whose parameters cover the real variation
across all current call sites — not over-generalized for hypothetical
future needs.

## Complete Prompt
```
Help me decide whether to extract a shared abstraction from this
duplicated logic, and design it if so.

Call site 1: {code + context}
Call site 2: {code + context}
Call site 3 (if any): {code + context}

Analyze:
1. Is this duplication genuine (same concept, coincidentally similar
   code) or accidental (different concepts that happen to look alike
   right now, but would diverge if one changed)? Extracting accidental
   duplication creates false coupling — say so if that's what this is.
2. If genuine: what's the actual variation across call sites (different
   inputs, slightly different behavior at the edges)? The abstraction's
   parameters must cover this REAL variation, not a hypothetical future
   one.
3. Propose the extracted function/class signature, and show each call
   site updated to use it.
4. Flag if extracting would require passing so many parameters or flags
   that the abstraction is worse than the duplication it removes — if
   so, recommend leaving the duplication as-is instead.
```

## Variations
- **Cross-module variant:** add explicit consideration of where the
  shared abstraction should live (a shared utils module vs. one owning
  module) given which call sites depend on it.
- **Over-abstraction check variant:** run this specifically to review an
  EXISTING abstraction that's accumulated too many flags/parameters, to
  decide if it should be split back apart.

## Tips
- When in doubt about genuine vs. accidental duplication, wait for a third call site before extracting — two data points rarely reveal the real variation.
- A parameter list that keeps growing is a sign the abstraction is absorbing unrelated concerns; split it back apart.

## Common Mistakes
- Extracting abstractions from accidental (not genuine) duplication,
  creating coupling between concepts that should be free to diverge.
- Designing the abstraction's parameters around imagined future call
  sites instead of the real variation across current ones.
- Accepting an abstraction with a growing pile of boolean flags instead
  of recognizing that's a sign the duplication should stay separate.

## Example Usage
Input: three call sites all validating and formatting a phone number,
each with a slightly different country-code default. Expected output:
confirms genuine duplication, proposes a function taking the number and
an explicit default country code as parameters (matching the real
variation), updates all three call sites.

## Related Prompts
- [[refactor-legacy-function]]
- [[code-simplification-pass]]
