# Resume Bullet Impact Rewrite

*Purpose:* Rewrite resume bullets to state concrete impact and scope, instead of listing responsibilities or activities.

## When to use
Updating a resume, especially before applying to a role you actually want (not a mass-apply pass).

## Expected input
The raw bullets (as they currently exist, even rough notes) and, if available, the actual numbers behind them (scale, % improvement, time saved).

## Expected output
Rewritten bullets in impact-first form, plus a flag on any bullet where the real impact isn't known and needs to be found out.

## The complete prompt
```
Rewrite these resume bullets for impact, not activity.

Raw bullets: {bullets}
Known numbers/context (if any): {scale, % improvement, time/cost saved,
team size}

For each bullet:
1. Rewrite as: [action] → [what changed] → [quantified impact], in that
   order. Lead with the outcome that matters to a hiring manager, not the
   task performed.
2. If a real number exists, use it exactly — do not invent or round in a
   way that misrepresents it.
3. If no number is known, do NOT invent one. Instead, flag it: "this
   bullet needs a real number — do you know [specific metric]?"
4. Cut any bullet that describes a responsibility with no discernible
   outcome ("responsible for maintaining X") — either find the outcome or
   drop it.

Output the rewritten bullets, then a separate list of bullets that need a
real number filled in before they're ready to use.
```

## Variants
- **Career-switch variant:** bias rewrites toward transferable-skill framing over domain-specific jargon.
- **Senior/staff variant:** emphasize scope of influence (team size, cross-team impact) over individual task completion.

## Related prompts
- [[interview-story-structuring]]
- [[linkedin-profile-rewrite]]
- [[portfolio-narrative-review]]

## Tips
- Never let the model fabricate a number — insist it flags unknowns instead of guessing plausibly.
- Bring real numbers if you have them at all, even rough ones ("~30% faster") — vague bullets are the single biggest weakness in most resumes.

## Common mistakes
- Accepting an invented-sounding number without verifying it's real and defensible in an interview.
- Rewriting activity ("built a dashboard") without ever landing on the actual impact ("reduced manual reporting time by X hours/week").

## Example
Input: "Responsible for the checkout service," no numbers given.
Expected output: flags this bullet as needing a real number — asks whether there's a conversion rate, latency, or uptime metric tied to your ownership of it.
