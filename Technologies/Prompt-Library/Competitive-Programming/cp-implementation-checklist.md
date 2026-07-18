# Competitive Programming — Implementation Checklist

*Purpose:* Catch the specific classes of bugs that cost contest time (off-by-one, overflow, edge cases) via a systematic pre-submit check, instead of submitting on a hunch.

## When to use
After writing a solution, before submitting, especially under contest time pressure where a wrong-answer penalty is costly.

## Expected input
The code, the problem's constraints, and the approach it implements.

## Expected output
A pass/fail check against the common failure classes, with the specific line/logic at risk named for each.

## The complete prompt
```
Check this competitive programming solution before I submit it.

Problem constraints: {n range, value ranges, edge cases implied by
constraints — e.g. "n can be 0 or 1"}
Approach: {brief description of the algorithm implemented}
Code:
{code}

Check explicitly, and name the specific line if a risk is found:
1. Integer overflow — any multiplication/sum that could exceed the
   language's default integer range given the value constraints.
2. Off-by-one — loop bounds, array indices, especially around binary
   search boundaries or interval endpoints.
3. Edge cases from constraints — n=0, n=1, all elements equal, negative
   numbers if allowed, empty input — does the code handle each explicitly
   or silently assume the general case?
4. Complexity sanity check — does the actual implementation match the
   intended complexity, or did an accidental nested loop/copy sneak in
   extra factor?
5. Output format — exact match to required format (trailing whitespace,
   newline, specific rounding/precision for floats).

Flag pass/fail per category — don't just say "looks fine," name the
specific evidence for each pass.
```

## Variants
- **Floating-point variant:** add explicit precision/epsilon-comparison check for any float output or comparison.
- **Multi-test-case variant:** add explicit check that state is properly reset between test cases in a single run.

## Related prompts
- [[cp-problem-pattern-recognition]]

## Tips
- Always state the actual constraint ranges — overflow checks are meaningless without knowing the value bounds.
- Treat n=0/n=1 as a first-class check every time; it's the single most common wrong-answer cause in contests.

## Common mistakes
- Assuming default integer types are large enough without checking against the actual constraint bounds.
- Not resetting global/static state between test cases in a multi-test-case problem, causing wrong answers on later cases only.

## Example
Input: code computing a product of up to 10^5 numbers each up to 10^9, stored in a 32-bit int.
Expected output: flags overflow risk at the multiplication/accumulation line, recommends a 64-bit accumulator.
