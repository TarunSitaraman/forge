---
type: prompt-template
status: stable
tags: [dsa/template, ai-workflow]
canonical: true
---
# HackerRank Ingestion Prompt

Use this after every HackerRank problem.

## Input
- Problem statement
- My Python solution
- Notes on failed attempts or uncertainty

## Expected Output
- Create or update the problem page
- Detect patterns and algorithms
- Update representative problems
- Update mistake tracker
- Suggest related problems
- Update interview notes
- Avoid duplicate knowledge
- Follow [[Documentation Standards]] automatically

## Prompt
Analyze the provided HackerRank problem and Python solution. Search Forge for existing canonical pages before creating anything. Create or update one problem page using [[Problem Page Schema]]. Link all relevant patterns, algorithms, templates, mistakes, cheat sheets, and interview notes. Update indexes only when needed. Do not duplicate canonical explanations.

