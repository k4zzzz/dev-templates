# PM Agent

A pattern for building AI-maintained project management systems using LLMs.

This is an idea file, designed to be copy-pasted to your own LLM agent (e.g. OpenCode with a local model). Its goal is to communicate the high-level idea, but your agent will build out the specifics in collaboration with you.

---

## The core idea

Most teams manage projects with tools like Jira, Linear, or Notion — manually written tickets, hand-maintained roadmaps, and status updates that are always slightly out of date. The overhead of keeping a project plan current is enormous, and it grows with every new requirement, change request, or technical discovery. Planning documents go stale the moment they're written.

The idea here is different. Instead of manually maintaining a project plan, the LLM incrementally builds and maintains a persistent project management system — a structured directory of markdown files that sits between you and the raw source material. When you add new inputs (a technical spec, a meeting transcript, a changed requirement), the LLM doesn't just summarize them. It reads them, extracts the implications, and integrates them across the entire project plan — updating phase definitions, revising effort estimates, adjusting task checklists, flagging new dependencies, and re-rendering the Kanban board and Gantt chart to reflect the current state.

This is the key difference: the project plan is a persistent, compounding artifact. Phase definitions, task breakdowns, effort estimates, and timelines are compiled once and kept current — not re-derived from scratch every time you ask a question. When requirements change, the LLM propagates the change across every affected artifact automatically. The plan stays coherent because the LLM does the maintenance that no one on the team wants to do.

You never (or rarely) write the plan yourself — the LLM writes and maintains all of it. Your job is to provide source material, make decisions, and ask the right questions. The LLM does the grunt work: the decomposition, estimation, cross-referencing, dependency mapping, and bookkeeping that makes a project plan actually useful over time.

In practice, keep the agent open on one side and your file explorer or markdown viewer (e.g. Obsidian) open on the other. The agent makes edits based on your conversation, and you browse the results in real time — reviewing phase plans, checking task checklists, reading the updated Kanban and Gantt. The agent is the project manager; the markdown files are the living plan.

This applies to many contexts:

- **Software projects**: turning a product spec and technical docs into a phased delivery plan with scoped effort, task checklists, and a tracked Kanban board.
- **Infrastructure or migrations**: breaking down a complex migration (cloud, database, monolith-to-services) into sequenced phases with dependencies, risks, and milestones.
- **Research or design sprints**: organizing a time-boxed exploration into stages with clear deliverables and decision points.
- **Client engagements**: translating a statement of work into a tracked execution plan, updated as the engagement evolves.
- **Product launches**: coordinating cross-functional work across engineering, design, QA, and marketing with a unified task board and timeline.

---

## Architecture

There are three layers:

**Raw sources** — your curated collection of input documents. Technical specs, requirements docs, architecture diagrams, meeting notes, existing codebase READMEs, RFCs, design files, or any other raw material relevant to the project. These are immutable — the agent reads from them but never modifies them. This is your source of truth.

**The project plan** — a directory of LLM-generated markdown files. Phase definitions, component breakdowns, effort estimates, task checklists, a Kanban board, a Gantt chart, a risk register, and a project summary. The agent owns this layer entirely. It creates files, updates them when new sources arrive or decisions change, and keeps everything internally consistent. You read it; the agent writes it.

**The schema** — a configuration file (e.g. `AGENTS.md` for OpenCode) that tells the agent how the project plan is structured, what naming conventions to follow, how effort is estimated, and what workflows to execute when ingesting new sources, answering questions, or maintaining the plan. This is the key config file — it's what makes the agent a disciplined project manager rather than a generic chatbot. You and the agent co-evolve this over time as the project's needs become clearer.

---

## Operations

### Ingest

You drop a new source into the raw collection and tell the agent to process it. An example flow: the agent reads the source, discusses key takeaways and implications with you, identifies any new components, tasks, or requirement changes, and then propagates updates across the project plan — revising effort estimates, updating task checklists, adjusting phase definitions, flagging new dependencies or risks, and re-rendering the Kanban board and Gantt chart. A single new input might touch 10–15 plan files.

Prefer to ingest sources one at a time and stay involved — review what the agent updates, ask it to explain its estimates, and correct misunderstandings before they compound. You can also batch-ingest multiple sources with less supervision, but plan quality degrades without human judgment in the loop.

### Plan

You direct the agent to generate or update specific planning artifacts. Examples:

- *"Break down Phase 2 into granular tasks and estimate each."*
- *"The auth system is more complex than we thought — re-estimate the identity component."*
- *"Generate a Gantt chart assuming a 3-person team starting May 1."*
- *"We're cutting the analytics module from v1 — update the plan."*

The agent reads relevant plan files, applies your direction, updates the affected artifacts, and reports what changed.

### Query

You ask questions against the project plan. The agent reads relevant plan files and synthesizes an answer. Examples:

- *"What's the critical path?"*
- *"Which tasks in Phase 1 are blocked by the API design decision?"*
- *"What's the total estimated effort for the backend?"*
- *"What are the top three risks right now?"*

Good answers can be filed back into the project plan as new pages — a risk analysis, a build vs. buy decision, a technical spike summary. Explorations compound in the plan just like ingested sources do.

### Review

Periodically, ask the agent to health-check the project plan. Look for: tasks with no assignable owner, phases with unrealistic timelines, missing dependencies between tasks, risks with no mitigation, components that appear in the Kanban but not in the effort estimate, or Gantt dates that have drifted from the task checklists. The agent is good at surfacing inconsistencies across files and recommending what to resolve next.

---

## Artifacts

The project plan directory contains these core files. The agent creates and maintains all of them:

### `summary.md`
A concise executive summary of the project. What's being built, why, for whom, and to what timeline. Written for someone unfamiliar with the project. Updated on every major ingest or scope change.

### `phases.md`
A breakdown of the project into sequential delivery phases. Each phase has a name, a description of its goal, a list of its constituent components, entry and exit criteria, key milestones, and a rough timeline. Dependencies between phases are made explicit. This is the spine of the project plan.

### `components/`
A subdirectory with one file per major component or functional area (e.g. `components/auth.md`, `components/data-pipeline.md`). Each component file contains: a description of what the component does, its dependencies on other components, a breakdown of sub-tasks with individual effort estimates, a total effort estimate in story points or person-days, an assessment of complexity and risk, and the phase(s) it belongs to.

### `tasks.md`
A comprehensive, hierarchical checklist of all tasks across the entire project, grouped by phase and then by component. Each task has a checkbox, a short description, an effort estimate, and an optional owner field. This is the ground-truth task list — the Kanban board is derived from it.

### `kanban.md`
A Kanban board rendered in markdown, with columns for `Backlog`, `In Progress`, `In Review`, and `Done`. Every task from `tasks.md` appears on the board. Tasks are represented as cards with their phase, component, estimate, and owner. The agent moves cards between columns when you report status updates. This file is re-rendered from `tasks.md` on every update to stay in sync.

### `gantt.md`
A Gantt chart rendered in markdown (using Mermaid `gantt` syntax). Shows all phases and major milestones on a timeline, with dependencies between phases visualized as arrows. Assumes a team size and start date that you configure in the schema. Updated whenever phase definitions, estimates, or timelines change.

### `risks.md`
A risk register. Each risk has: a description, the phase or component it affects, a likelihood rating (Low / Medium / High), an impact rating, a mitigation strategy, and an owner. The agent adds new risks as it reads sources and flags ones that have materialized.

### `decisions.md`
A log of key technical and product decisions, in the format: decision made, date, rationale, alternatives considered, and implications for the plan. Decisions that affect scope or timeline are cross-referenced in `phases.md` and `tasks.md`.

### `index.md`
A catalog of all files in the project plan directory — each file listed with a link and a one-line summary of what it contains. The agent reads the index first when answering queries, then drills into specific files. Updated on every ingest.

### `log.md`
An append-only chronological record of what happened and when — ingests, plan updates, decisions, review passes, and status syncs. Each entry starts with a consistent prefix (e.g. `## [2026-04-15] ingest | API Spec v2`) so the log is parseable with simple tools. The log gives you a timeline of how the plan evolved.

---

## Effort estimation

The agent uses a consistent effort estimation approach defined in the schema. A recommended default:

- Estimates are in **story points** (Fibonacci: 1, 2, 3, 5, 8, 13, 21) or **person-days**, whichever you prefer — configure one in the schema and use it consistently.
- Each task gets an individual estimate. Component totals are summed from tasks. Phase totals are summed from components.
- Estimates reflect **net implementation effort**, not calendar time. The Gantt chart applies a configurable **velocity factor** (e.g. 0.6 for a team spending ~60% of time on project work) to convert effort to calendar duration.
- Uncertainty is tracked alongside estimates: Low (well-understood), Medium (some unknowns), High (significant unknowns). High-uncertainty estimates are flagged in the risk register.
- When new information changes an estimate significantly (>20%), the agent re-derives the affected totals and flags the delta in the log.

---

## Schema configuration (`AGENTS.md`)

Document the following in your schema file so the agent behaves consistently across sessions:

```
Project name and one-line description
Team size and composition
Start date and target delivery date
Effort unit (story points or person-days)
Velocity factor for Gantt conversion
Phase naming conventions
Component naming conventions
Estimation guidelines specific to this domain
What counts as a task vs. a sub-task
How to handle scope changes (update in place vs. version)
Kanban column definitions
Risk rating rubric
Any non-negotiable constraints (regulatory, dependency, budget)
```

---

## Tips and tricks

- **Obsidian** works well as a viewer alongside the agent. The graph view shows component dependencies visually. Dataview can query task frontmatter to generate dynamic status tables.
- **Mermaid** is natively supported in Obsidian and GitHub. Use `gantt`, `graph TD`, and `flowchart` blocks throughout the plan — the agent can render architecture diagrams, dependency graphs, and decision trees as Mermaid, not just the Gantt.
- **Meeting transcripts are first-class sources.** Drop a transcript from a planning call or sprint retro into the raw sources and ask the agent to ingest it. It will extract decisions, new requirements, and raised risks and integrate them across the plan.
- **The plan is a git repo.** You get version history for free. Before any major re-estimation or scope change, commit the current state. You can always diff what changed.
- **Use the log as a standup feed.** `grep "^## \[" log.md | tail -10` gives you the last 10 plan events — a quick summary of what's changed recently.
- **Scope cuts are first-class operations.** When something is cut from the plan, don't delete it — move it to a `deferred.md` file with a note on why. Scope has a way of coming back, and having the prior estimates preserved saves re-work.
- **Spike tasks belong in the plan.** When something has high uncertainty, create an explicit time-boxed spike task in the relevant phase. The spike's output is a decision entry in `decisions.md` and a revised estimate for the work it was investigating.

---

## Why this works

The tedious part of project management is not the thinking — it's the bookkeeping. Keeping estimates consistent as scope changes, propagating a one-week slip through every downstream dependency, updating task statuses across the board, making sure the Gantt still reflects reality. Teams abandon project plans because the maintenance burden grows faster than the value. Estimates go stale. The Kanban drifts from the actual work. The Gantt becomes a fiction.

LLMs don't get bored, don't forget to update a dependency, and can touch 15 files in one pass. The plan stays maintained because the cost of maintenance is near zero.

Your job is to provide source material, make the hard calls, and ask the right questions. The agent's job is everything else.
