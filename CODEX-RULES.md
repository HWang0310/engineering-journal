# Codex Journal Update Rules

## Purpose

Use this repository to preserve decisions and progress that future-you will otherwise have to rediscover. Before work on an important project, read `README.md`, its project page (if present), and the relevant current monthly plan.

## When Codex should proactively propose or make an update

Consider an update when any of these happens:

- A new important project or repository is created.
- A meaningful feature, milestone, proof of concept, migration, or technical breakthrough is completed.
- A high-impact architecture, data, platform, security, dependency, or product-scope decision is made.
- The project changes direction, is paused, resumed, completed, or archived.
- A user-facing release, important GitHub release, or major version is published.
- A failure, incident, or discarded approach yields a lesson likely to help future work.
- A monthly plan is set, materially changed, or reviewed.

## What to update

- Update `PROJECTS.md` when the project status, phase, next step, or repository changes.
- Add one concise dated entry to `JOURNAL.md` for the event itself.
- Update `projects/<project>.md` when its purpose, phase, key decisions, risks, or next step changes.
- Create an ADR only if the decision has durable alternatives and meaningful consequences.
- Update `monthly/YYYY-MM.md` for planned versus actual milestone progress.
- Update `ROADMAP.md` only when personal priorities change, normally monthly or quarterly.

## Explicitly do not record

Do not create journal or ADR entries for:

- Variable renames, formatting, copy edits, or routine dependency bumps.
- Small bug fixes with no reusable lesson or change in project direction.
- Routine test runs, local experiments, or ordinary commits.
- Every prompt, command, conversation, or intermediate implementation step.
- Temporary ideas that were neither adopted nor consequential.

## Decision threshold

Create an ADR when future work would reasonably ask, “Why was this chosen instead of the alternatives?” Examples include a database or hosting choice, a durable data model, a major framework adoption, an API boundary, or a decision to stop pursuing a product direction.

If the answer is merely “it was the fastest small implementation detail,” do not write an ADR.

## Writing standard

Use concrete dates, names, links, and outcomes. Write enough context to understand the entry in several years, but aim for the smallest complete record. Do not write secrets or sensitive data.
