Read all documents in the raw/ folder.

For each document, write a one-paragraph summary and save it to
plan/sources/<filename>.md. Note the document type (spec, transcript,
RFC, notes, etc.), what decisions or requirements it establishes, and
any open questions it raises.

Then, working across all sources together:

1. SUMMARY
   Write plan/summary.md — a concise executive overview of what is being
   built, why, for whom, and to what timeline. Written for someone
   unfamiliar with the project.

2. PHASES
   Write plan/phases.md — break the project into sequential delivery
   phases. For each phase: name, goal, entry and exit criteria, list of
   components it contains, key milestones, dependencies on other phases,
   and a rough duration. Make sequencing and dependencies explicit.

3. COMPONENTS
   For each major functional area or system component you identify, write
   plan/components/<component-name>.md. Each file must contain: what the
   component does, which phase(s) it belongs to, its dependencies on
   other components, a breakdown of its sub-tasks with individual effort
   estimates in story points (Fibonacci: 1, 2, 3, 5, 8, 13, 21), a
   summed total estimate, a complexity rating (Low / Medium / High), and
   any risks specific to that component.

4. TASKS
   Write plan/tasks.md — a complete hierarchical task checklist for the
   entire project, grouped first by phase and then by component. Each
   task gets: a checkbox [ ], a short description, a story point
   estimate, and an Owner field (leave blank if unknown). This is the
   ground-truth task list everything else is derived from.

5. KANBAN
   Write plan/kanban.md — a Kanban board in markdown with four columns:
   Backlog, In Progress, In Review, Done. Every task from tasks.md
   appears as a card. Each card shows: task name, phase, component,
   estimate, and owner. All tasks start in Backlog. The board must
   stay in sync with tasks.md — if tasks.md changes, regenerate this
   file.

6. GANTT
   Write plan/gantt.md — a Gantt chart using Mermaid gantt syntax.
   Show all phases and major milestones on a timeline. Derive calendar
   durations from story point totals using a default velocity of 8
   story points per person per week and a default team size of 1 unless
   the source documents specify otherwise. Show phase dependencies as
   sequential blocks. Include a section at the top of the file that
   states the assumptions used (team size, velocity, start date).

7. RISKS
   Write plan/risks.md — a risk register. For each risk you identify
   across all sources and your own analysis: description, affected phase
   or component, likelihood (Low / Medium / High), impact (Low / Medium
   / High), mitigation strategy, and owner. Flag any high-uncertainty
   component estimates as risks automatically.

8. DECISIONS
   Write plan/decisions.md — a log of key technical and product decisions
   found in the source documents. For each: the decision, the date if
   known, the rationale, alternatives considered, and implications for
   the plan.

9. INDEX
   Write plan/index.md — a catalog of every file in the plan/ directory,
   with a relative link and a one-line description of what it contains,
   organized by category (Sources, Phases, Components, Tracking,
   Reference).

10. LOG
    Write plan/log.md — an append-only record of this run. Start with a
    single entry using the format:
    ## [YYYY-MM-DD] init | Initial plan generated from <N> source files
    List every source file processed and every plan file created.

After writing all files, print a short completion report: how many source
files were read, how many plan files were created, total story points
estimated, number of phases, number of components, and any ambiguities
in the source material that need human review before the plan should be
considered reliable.
