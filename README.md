# Engineering Journal

`engineering-journal` is the durable, personal record of important development work. It is written for future-you first: concise enough to maintain, specific enough to explain what happened and why.

## The rule

Record important change, not every operation.

An entry should answer one of these questions:

- What meaningful work started, changed, shipped, paused, or ended?
- Why did it matter?
- What is the next meaningful move?

If an entry cannot answer one of them, it probably does not belong here.

## Start here

1. Create or update the project row in [PROJECTS.md](PROJECTS.md).
2. Add a one-page project status file from [projects/TEMPLATE.md](projects/TEMPLATE.md) for an important project.
3. Add one short line to [JOURNAL.md](JOURNAL.md) when a significant event happens.
4. Update the current file under `monthly/` at the end of a milestone or month.
5. Create an ADR from `decisions/TEMPLATE.md` only for a decision that would be costly or confusing to revisit later.

Do not update every file for every event. Usually, the project page and the journal entry are enough.

## Core files

| File | Purpose | Update cadence |
| --- | --- | --- |
| `PROJECTS.md` | One-row overview of every important project | At start, status change, and major progress |
| `JOURNAL.md` | Cross-project chronological record | Only after an important event |
| `ROADMAP.md` | Personal development priorities | Monthly or when priorities change |
| `monthly/YYYY-MM.md` | A small plan and end-of-month review | At month start and month end |
| `projects/<project>.md` | One-page status, context, next step | At meaningful milestones |
| `decisions/ADR-<number>-<slug>.md` | Long-lived decision rationale | Only for high-impact decisions |
| `CODEX-RULES.md` | Rules for Codex working in your projects | Review when the system evolves |

## Project-local documentation

For a normal important project, start with only `docs/PROJECT.md`: purpose, current status, key constraints, and next step. Add these only when their value exceeds their maintenance cost:

- `docs/ROADMAP.md` — use when the project has several upcoming milestones.
- `docs/decisions/` — use when there are lasting architecture, platform, data, security, or product-scope decisions.
- `docs/ARCHITECTURE.md` — use when the system is complex enough that a diagram or component map saves repeated explanation.
- `CHANGELOG.md` — use for projects with user-facing or versioned releases.

Do **not** create a project-local journal by default: `JOURNAL.md` here is the single cross-project timeline. Do **not** create a vision, architecture, roadmap, decision log, and changelog merely to look organized.

## Make it visible to Codex

When working in an important project, add a short note to that project's `AGENTS.md` or `README.md` pointing to this repository and `CODEX-RULES.md`. Codex cannot be assumed to carry a reliable global memory into every unrelated future conversation; this Git repository is the source of truth.

## Maintenance rhythm

- During work: record only milestones, releases, decisions, pauses, and durable lessons.
- At month end: complete the current monthly file, then create next month's file from the template.
- Every quarter: trim stale roadmap items and mark completed or abandoned projects clearly.
- Before a major handoff or release: make sure the project page states current phase and next step.

This repository contains no credentials, secrets, customer data, or copied sensitive logs.
