# Template: Mistake Tracker

*Guidance: this is a running log of your RECURRING failure modes, not a
list of individual bugs. Each row should represent a category of mistake
you've made more than once — the goal is to make your own blind spots
visible so you can check for them deliberately.*

---

## Recurring mistakes

| Mistake pattern | First noticed | Times recurred | Example problems | Fix / deliberate check |
|---|---|---|---|---|
| {e.g. "misjudge complexity bound from constraints"} | {date} | {count, update over time} | {links} | {the specific habit to build, e.g. "always compute complexity from constraints before coding"} |
| {e.g. "off-by-one on binary search boundaries"} | {date} | {count} | {links} | {e.g. "always test with n=1 and n=2 by hand before submitting"} |

## Mistake categories to watch for

*(Seed this list from real experience — don't pre-fill with generic CP
advice. Add a category only after it's actually bitten you more than
once.)*

- {category}: {what it looks like when it happens}

## Retired mistakes

*(Move a mistake here once you've gone a meaningful streak — e.g. 10+
problems — without it recurring. This is evidence of real improvement,
worth keeping visible.)*

| Mistake pattern | Retired on | Streak without recurrence |
|---|---|---|

---

## Best practices
- Only log a mistake as a "pattern" after it recurs at least twice —
  a single bug isn't a pattern yet.
- Update the "times recurred" count honestly; this number, tracked over
  weeks, is your best evidence of whether you're actually improving on
  that specific weakness.
- Use `Systems/Prompt-Library/Competitive-Programming/cp-post-solution-analysis.md`
  right after solving a problem to catch mistakes while the reasoning is fresh.
