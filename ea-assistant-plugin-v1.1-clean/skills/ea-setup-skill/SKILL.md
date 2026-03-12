---
name: ea-setup-skill
description: >
  Use when creating a new EA engagement from scratch. Triggers: "new EA project", "new engagement for [org]", "set up architecture engagement", "start TOGAF project", "kick off an architecture engagement", "create a project for [client]", "I have a new client", "start an EA programme for", "initialise a TOGAF project", "new EA for [name]", or any request to set up, create, or initialise a new enterprise architecture engagement. Also triggers when no project exists and the user wants to begin EA work.
license: MIT
metadata:
  author: Costa Pissaris
  version: 1.0.0
  created: 2026-03-10
  last_reviewed: 2026-03-10
  review_interval_days: 90
---

## Overview

ea-setup-skill creates a new EA project folder, runs a kickoff interview, executes the Preliminary phase to define architecture principles and governance, then configures the remaining ADM phases. Result: a fully initialised project ready to begin Phase A.

## Workflow

1. Ask: "Where should the ea-projects folder be created?" (full path)
2. Ask: "What is the project name?"
3. Create folder structure:
   ```
   <path>/ea-projects/<project-name>/
     project.json
     ea-context.md
     artifacts/
     documents/
     publications/
     notes/
   ```
4. Ask: "Do you have any existing documents to upload? (e.g. strategy doc, business case, IT landscape)"
   - Yes → accept file path(s), run ea-interview-techniques-skill document ingestion mode for each
   - No → continue
5. Run kickoff interview using ea-interview-techniques-skill (Preliminary question set):
   - Skip questions already answered by uploaded documents
   - Show extracted values, ask user to confirm or override
   - Capture: organisation name, business driver, key stakeholders (name/role/concern), scope in/out, timeline, constraints
6. Run full Configuration Mode via togaf-adm-skill — all 7 steps in sequence:
   Step 1: Define architecture principles (pre-populate from uploaded documents if found)
   Step 2: Define governance model (who approves decisions, cadence, board)
   Step 3: Select which ADM phases apply to this engagement
   Step 4: Confirm phase sequence
   Step 5: Set depth per phase (Light / Standard / Full)
   Step 6: Select artifacts per phase and set optional target dates per artifact
   Step 7: Set optional target completion dates per phase
   → Output: complete phases[] and governance{} objects for project.json
7. Write ea-context.md
8. Write project.json
9. Confirm: "Setup complete. Preliminary phase done. Ready to begin [first selected phase]."

## Existing Project DetectionIf `<path>/ea-projects/<n>/project.json` already exists:
- Say: "A project named `<name>` already exists at this path."
- Ask: "Resume it or start a new project with a different name?"
- Resume → use ea-projects-skill resume flow
- New name → go back to step 2

If the directory exists but contains no `project.json` (e.g. a previous setup was abandoned mid-way), treat it as a new project — the folder is empty and safe to initialise.

## ea-context.md Template

The following template is written to `ea-context.md` during step 7:

```markdown
# EA Engagement: <name>
**Organisation:** <org> | **Architect:** <name> | **Date Started:** <date>

## Business Driver
<captured response>

## Scope
**In scope:** <list>
**Out of scope:** <list>

## Stakeholders
| Name | Role | Primary Concern |
|------|------|----------------|

## Architecture Principles
<list from Preliminary phase>

## Governance Model
<captured response>

## Constraints & Assumptions
<captured response>

## Timeline
<captured response>
```

## project.json on Creation

The initial state written to `project.json` during step 8:

- `status`: Active
- `startDate`: today
- `currentPhase`: first selected phase after Preliminary
- `nextAction`: "Begin [phase name]"
- Preliminary phase entry: status=Complete, progress=100, currentVersion=1.0, versions=[{version:"1.0", date:today, notes:"Initial setup"}]
- All other selected phases: status=Not Started, progress=0, artifacts configured with targetDates from configuration
- `documents`: populated if any documents were uploaded
- `publications`: []

## Skills Referenced

- **ea-interview-techniques-skill** — document ingestion + kickoff interview
- **togaf-adm-skill** — Preliminary phase (configuration mode)
- **ea-projects-skill** — for resume flow if project already exists
