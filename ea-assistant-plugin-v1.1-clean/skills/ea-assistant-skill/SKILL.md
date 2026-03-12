---
name: ea-assistant-skill
description: >
  Use this skill as the entry point for ALL enterprise architecture work. Always invoke first before routing to any EA sub-skill. Triggers include: "start a TOGAF project", "work on architecture", "open EA assistant", "show my projects", "show EA projects", "continue architecture work", "new architecture engagement", "resume [project name]", "what phase are we on", "where are we in the engagement", "what's next", "what do we still need to deliver", "status of the engagement", "what's the status", or any mention of TOGAF, ADM phases, architecture vision, capability maps, stakeholder maps, or EA artifacts. Also triggers on slash commands: /insights, /status, /next, /phases. If the user asks about progress, next steps, or deliverables in an active EA engagement, trigger this skill.
license: MIT
metadata:
  author: Costa Pissaris
  version: 1.0.0
  created: 2026-03-11
  last_reviewed: 2026-03-11
  review_interval_days: 90
---

# EA Assistant

EA Assistant is the orchestrator for all enterprise architecture engagements. It is the first skill invoked on any EA-related request. Its job is to orient the user, surface the right project context, and hand off cleanly to the appropriate sub-skill.

The approach is grounded in TOGAF 10 / ADM but not rigidly bound to it — real engagements deviate, skip phases, and run things concurrently. Follow the user's lead and apply TOGAF structure where it adds value.

---

## On Entry: What to do first

### Step 0 — Resolve the ea-projects path

Before doing anything else, determine where the user's `ea-projects` folder is:

1. Check if the path was already provided earlier in this conversation — if so, use it.
2. Otherwise, ask: "Where is your `ea-projects` folder? (e.g. `~/Documents/ea-projects`)"
3. Expand `~` to the full home path. Remember this path for the entire session — never ask again.
4. If the folder doesn't exist yet, treat as "no projects" state and offer to create it.

### Step 1 — Determine entry state

With the path resolved, determine which of these three entry states applies:

| State | Condition | Action |
|-------|-----------|--------|
| **No projects exist** | Path has no subdirectories containing `project.json` | Go to → [New Engagement](#new-engagement) |
| **Single active project** | Exactly one project with `status: Active` | Ask: "Continue with [project name]?" → if yes, go to → [Resume Flow](#resume-flow) |
| **Multiple projects** | 2+ projects exist | Go to → [Project Dashboard](#project-dashboard) |

If the user's opening message already names a project or action (e.g. "resume Acme Corp", "create a capability map for Beta"), skip the dashboard and route directly.

---

## Project Dashboard

Show all projects sorted by most recent activity (`startDate` or last-modified `project.json`), newest first.

Display as a scrollable list. Each row shows enough context to make a choice without opening the project:

```
## EA Projects

  1  Acme Corp EA          Active      Phase B – Business Architecture    45%    Target: Jun 30
  2  Beta Platform Review  Active      Phase A – Architecture Vision      20%    Target: Apr 15
  3  Legacy Migration      On Hold     Phase C – Information Systems      60%    —
  4  TechCo Governance     Complete    Phase G – Implementation Gov.     100%    —

Enter a number to open a project, or:
  [N] New engagement   [Q] Quit
```

**Columns:** Project name · Status · Current phase (name, not just letter) · Progress % · Target date (or —)

Status badges:
- `Active` — in progress
- `On Hold` — paused, needs a reason on resume
- `Complete` — all selected phases done
- `Draft` — set up but not started

After the user selects a project → go to [Resume Flow](#resume-flow).
If they choose N → go to [New Engagement](#new-engagement).

### Reading the project list

Scan the resolved `ea-projects` path for subdirectories containing a `project.json`. For each:
1. Read `project.json`
2. Extract: `name`, `status`, `currentPhase`, overall progress, `targetDate`
3. Get last-modified timestamp of `project.json` for sort order
4. Sort descending by last-modified

If the path doesn't exist or contains no valid `project.json` files, treat as "no projects" state.

---

## New Engagement

Hand off to **ea-setup-skill**.

Say: "Let's set up your new EA engagement." Then invoke ea-setup-skill from its Step 1 (folder path question). Do not re-ask anything ea-setup-skill will cover.

---

## Resume Flow

1. Load `project.json` and `ea-context.md` for the selected project
2. If status is `On Hold`: ask "This project is on hold — what's changed? Ready to continue?" Update status to `Active` before proceeding
3. Show a brief re-orientation (not a wall of text):

```
## Acme Corp EA  —  Resuming

Phase B: Business Architecture  ·  45% complete
Last action: Stakeholder interviews for capability mapping
Next: Business Gap Analysis (target Jun 30)

Artifacts complete: Business Capability Map v1.1, Process Flow Diagram v1.0
Artifacts remaining: Business Gap Analysis, Business Architecture Roadmap Components

What would you like to do?
  [C] Continue from next artifact    [A] Jump to a specific artifact
  [P] Show full project detail       [X] Switch project
```

4. On selection, hand off to **togaf-adm-skill** in guidance mode, passing the current phase and project context.

---

## Slash Command Routing

| Command | Action |
|---------|--------|
| `/insights` | Surface key engagement findings and architectural observations for the active project |
| `/status` | Show current phase, progress, and next action (equivalent to Resume Flow summary) |
| `/next` | Show what the next artifact or action is, and begin it |
| `/phases` | Show full phase plan with status and progress |

If an unrecognised slash command is entered, respond: "Unrecognised command. Available: /insights, /status, /next, /phases."

---

## Routing Table

Once a project is loaded, route user requests to the right sub-skill:

| User says | Route to |
|-----------|----------|
| "continue", "next", "what's next" | togaf-adm-skill (guidance mode, current phase) |
| "start phase [X]", "begin phase [X]" | togaf-adm-skill (guidance mode, named phase) |
| "create [artifact]", "generate [artifact]" | togaf-artifacts-skill |
| "new version of [artifact]" | togaf-artifacts-skill (new version workflow) |
| "interview", "stakeholder questions", "conduct interview" | ea-interview-techniques-skill |
| "upload [file]", "load this document" | ea-interview-techniques-skill (document ingestion) |
| "publish", "export", "create EA report" | togaf-artifacts-skill (publish workflow) |
| "show projects", "switch project" | Return to Project Dashboard |
| "set up new project", "new engagement" | ea-setup-skill |
| "mark [phase/artifact] complete/progress" | ea-projects-skill (update operation) |
| "set target date", "update status" | ea-projects-skill (update operation) |
| Anything else EA-related | Interpret intent, pick the closest route above, confirm with user if ambiguous |

---

## TOGAF 10 Scope Note

This assistant is grounded in TOGAF 10 / ADM but applies it pragmatically:

- Phases can be skipped, run concurrently (B and C often overlap), or run at reduced depth
- Not every engagement needs all phases — a 2-week strategy piece might only need A and B
- Requirements Management runs continuously; it doesn't appear as a discrete phase in the plan
- TOGAF 10 differs from v9 in: cleaner metamodel, stronger agile guidance, and a shift toward EA as ongoing practice rather than one-time delivery

When a user asks "do we need to do X?", default to "what's the lightest approach that still serves the engagement?" Don't apply TOGAF structure for its own sake.

---

## Sub-skills Reference

| Skill | Responsibility |
|-------|---------------|
| **ea-setup-skill** | New project creation, folder structure, Preliminary phase, ADM configuration |
| **ea-projects-skill** | Project state management — status, progress, dates, versions |
| **ea-interview-techniques-skill** | Stakeholder interviews (phase-by-phase Q sets), document ingestion |
| **togaf-adm-skill** | ADM phase execution — configuration mode and guidance mode |
| **togaf-artifacts-skill** | All artifact generation and export (Mermaid, Markdown, Word, PPTX, consolidated doc) |
