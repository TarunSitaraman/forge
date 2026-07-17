# Legacy Codebase Rapid Onboarding

*Purpose:* Get productive in an unfamiliar codebase in hours, not weeks, by extracting its real architecture and danger zones instead of reading file-by-file.

## When to use
First day on a new codebase, or picking up ownership of a module you didn't write.

## Expected input
Repo access (file tree, key entry points), and the task you actually need to accomplish in it.

## Expected output
A map of the system's real architecture, its riskiest areas, and a concrete first-task plan.

## The complete prompt
```
You are helping me get productive fast in an unfamiliar codebase. My actual
task is: {task}.

Codebase context:
{file tree / key files / entry points}

Produce:
1. Architecture map: the 5-8 components that matter, how data flows between
   them, in one paragraph each — not a file-by-file summary.
2. Danger zones: code with signs of fragility (no tests, high churn, TODO/FIXME
   density, god objects) that my task is likely to touch.
3. The shortest path to accomplishing {task}: which files I actually need to
   read and change, in order.
4. Three questions I should ask a human who knows this codebase, that I
   cannot answer from the code alone.
```

## Variants
- **Ownership handoff variant:** ask instead for "what would break silently if I changed X" per component.
- **Debugging-entry variant:** narrow scope to just the subsystem containing a specific bug.

## Related prompts
- [[root-cause-five-whys]]
- [[architecture-decision-record]]

## Tips
- Feed in the actual file tree, not a description — structure reveals architecture better than prose.
- Always ask for the "questions for a human" section; it prevents false confidence.

## Common mistakes
- Trying to understand the whole codebase before starting the actual task — scope to the task instead.
- Treating the danger-zone list as exhaustive rather than a starting point for caution.

## Example
Input: task = "add a new payment provider," repo = e-commerce monorepo file tree.
Expected output: identifies the existing payment abstraction layer, flags the checkout service as a danger zone (high churn, thin tests), and lists the 4 files to touch.
