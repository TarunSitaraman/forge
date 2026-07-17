# LinkedIn Profile Rewrite

*Purpose:* Rewrite a LinkedIn headline and about section to be findable
in recruiter search and understandable in a 10-second skim — the two
things LinkedIn actually gets used for.

## When to use
When updating a LinkedIn profile, especially before or during a job
search when recruiter search visibility matters.

## Expected input
Current headline/about text (or a rough description if starting from
scratch), target role/keywords, and 2-3 accomplishments you want surfaced.

## Expected output
A rewritten headline and about section, plus the specific keywords used
and why, so you can judge if they match how recruiters actually search.

## The complete prompt
```
Rewrite my LinkedIn headline and about section for search visibility and
a fast, accurate skim.

Current headline/about: {text, or "starting from scratch"}
Target role/keywords: {what you want to be found for}
Key accomplishments to surface: {2-3, with real numbers if available}

Rewrite:
1. Headline: specific enough to convey specialization, not just a job
   title — include the 2-3 keywords a recruiter would actually search
   for this role. Keep it scannable, not a run-on phrase.
2. About section: open with one concrete, specific line (not "passionate
   about..."). Cover what you do, one or two concrete accomplishments
   with real numbers, and — if actively searching — what you're looking
   for next, since recruiters filter on this explicitly.
3. Do not invent accomplishments or numbers — if I didn't give you a
   real one, prompt me for it rather than generating a plausible-sounding
   placeholder.
4. Keep the about section skimmable — a recruiter spends seconds here,
   not minutes.

Output the headline and about section, then list the specific keywords
you used and why each matters for search visibility.
```

## Variants
- **Passive (not job-searching) variant:** drop the "what you're looking for" line, emphasize current expertise/thought leadership instead.
- **Career-switcher variant:** bias keywords toward transferable skills and the target field's vocabulary, not your prior field's.

## Related prompts
- [[resume-bullet-impact-rewrite]]

## Tips
- Give the model real target-role keywords, ideally pulled from actual job postings you're interested in — generic keyword guessing produces generic search visibility.
- Never let the model fabricate a number in the about section; flag it for you to fill in with something real instead.

## Common mistakes
- Writing an about section that's really a copy of the resume in paragraph form, which doesn't serve LinkedIn's skim-first reading pattern.
- Choosing a headline optimized for how you see yourself rather than the keywords recruiters actually search.

## Example
Input: current headline = "Software Engineer," target = "senior backend roles at Series B+ startups," accomplishment = "led migration serving 2M users."
Expected output: headline like "Senior Backend Engineer | Distributed Systems & Scale | Ex-{Company}," about section opening with the migration accomplishment stated concretely with the real number.
