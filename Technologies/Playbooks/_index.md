# Playbooks

*Reusable engineering workflows. Each one is execution-focused: goal,
inputs, outputs, steps, checklists, AI prompts to use, expected
artifacts, and a Mermaid diagram of the flow. None of these are textbook
notes — they're procedures you run.*

## Index

| Playbook | Use it when |
|---|---|
| [`starting-a-software-project.md`](starting-a-software-project.md) | Kicking off any new build |
| [`hackathons.md`](hackathons.md) | Entering a timed hackathon |
| [`learning-a-framework.md`](learning-a-framework.md) | Picking up a new framework fast, for a real task |
| [`preparing-for-interviews.md`](preparing-for-interviews.md) | Prepping for behavioral/technical/system-design interviews |
| [`building-an-architecture.md`](building-an-architecture.md) | Designing a system before implementation |
| [`reading-documentation.md`](reading-documentation.md) | Extracting a task-specific answer from a doc set |
| [`reading-research-papers.md`](reading-research-papers.md) | Evaluating a paper for a real purpose |
| [`writing-presentations.md`](writing-presentations.md) | Building a presentation around one argument |
| [`preparing-demos.md`](preparing-demos.md) | Scripting and rehearsing a live demo |
| [`open-source-contributions.md`](open-source-contributions.md) | Landing a merged PR in someone else's project |
| [`internship-applications.md`](internship-applications.md) | Running an internship application cycle |
| [`debugging-production-issues.md`](debugging-production-issues.md) | Handling a live production incident |
| [`building-an-mvp.md`](building-an-mvp.md) | Scoping and shipping a hypothesis-testing MVP |
| [`planning-a-semester-project.md`](planning-a-semester-project.md) | Planning a long-running academic project |
| [`preparing-for-exams.md`](preparing-for-exams.md) | Studying for an exam with limited time |
| [`deployment.md`](deployment.md) | Shipping a change to production safely and reversibly |
| [`repository-cleanup.md`](repository-cleanup.md) | Bringing a neglected repo back to a navigable state |
| [`prompt-creation.md`](prompt-creation.md) | Turning a proven prompt into a real Prompt Library entry |

## How a playbook is structured

Every playbook has, at minimum, these sections: **Goal**, **Inputs**,
**Outputs**, **Steps** (or **Step-by-step workflow**), **Checklists** (or
**Checklist**), **AI prompts**, **Expected artifacts** (or
**Deliverables**), plus a closing **Mermaid workflow** diagram. The three
most recently added playbooks (`deployment.md`, `repository-cleanup.md`,
`prompt-creation.md`) also include explicit **Prerequisites** and
**Common mistakes** sections — a stronger shape worth backporting into
the earlier fifteen over time, not a competing convention. The "AI
prompts" section always links directly to `../Prompt-Library/`
entries — playbooks are the execution layer, the Prompt Library is the
tool you reach for at specific steps.

## Adding a playbook

Only add one for a workflow you've actually run more than once and would
want a checklist for next time. If it's a one-off process, it belongs in
the relevant `Projects/` folder instead.
