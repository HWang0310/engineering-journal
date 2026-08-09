# Engineering Journal Design

## Goal

Create a durable, low-maintenance personal development record under `/Users/hwang/Movies/Program/engineering-journal` that preserves only important project context, decisions, milestones, and plans.

## Design

The repository has five global records: a project index, chronological journal, personal roadmap, Codex rules, and monthly plans. Each important project can receive one concise status page; only high-impact decisions receive an ADR. Markdown and Git are the only technologies, keeping the system searchable, portable, and inspectable for years.

## Boundaries

This system is not an issue tracker, a daily diary, a detailed changelog of every commit, or an automatic cross-conversation memory. It deliberately excludes trivial edits and task-level work.

## Maintenance model

Record significant changes at the point they happen, use monthly files for light planning and review, and revise the roadmap only when priorities materially change. Project-local documentation starts with `docs/PROJECT.md`; additional documents are added only if they solve a recurring comprehension problem.
