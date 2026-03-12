---
name: ea-projects-skill
description: >
  Use when directly mutating EA project state. Triggers: "mark [phase/artifact] complete", "mark [artifact] as done", "[artifact] is 80% done", "Phase B is 40% complete", "set target date for Phase X", "new version of [artifact]", "update status of", "advance phase", "publish", "export EA document", "show project detail for [project]", or any explicit request to update progress, status, version, or dates on a project, phase, or artifact.
license: MIT
metadata:
  author: Costa Pissaris
  version: 1.0.0
  created: 2026-03-10
  last_reviewed: 2026-03-10
  review_interval_days: 90
---

## Overview

ea-projects-skill is the central management hub for all EA engagements. It reads and writes project.json files to track progress, manage versions, and coordinate handoffs to togaf-adm-skill and togaf-artifacts-skill.

## Operations Table

| Operation | Trigger phrases | Action |
|-----------|----------------|--------|
| List | "show projects", "list engagements", "my EA projects" | Delegate to **ea-assistant-skill** Project Dashboard — do not render a separate list format |
| View | "show `<project>`", "detail", "status of `<project>`" | Full project + phase + artifact detail |
| Resume | "resume `<project>`", "continue working on", "pick up" | Load context, show where left off, hand to togaf-adm-skill |
| Update status | "mark `<project>` on hold", "complete `<project>`" | Update project status field |
| Advance phase | "advance to Phase C", "mark Phase B complete" | Update phase status/progress in project.json |
| Update artifact | "mark stakeholder map complete", "capability map is 80% done" | Update artifact status/progress |
| Set target date | "set target for Phase B to June 30" | Update phase or artifact targetDate |
| Set progress | "Phase B is 40% done" | Update progress field (0–100) |
| New phase version | "redo Phase B", "new version of Phase A" | Delegate to togaf-adm-skill |
| New artifact version | "new version of capability map" | Delegate to togaf-artifacts-skill |
| Upload document | "upload", "load document", "use this file" | Run ea-interview-techniques-skill document ingestion mode (see note below) |
| Publish | "publish", "export EA document", "create EA report" | Delegate to togaf-artifacts-skill publish workflow |

## Finding the ea-projects Folder

If the folder path is not known, ask: "Where is your ea-projects folder?" Remember the answer for the session.

## List Display (Dashboard)

The project list display is owned by **ea-assistant-skill**. When a list operation is triggered here, delegate to ea-assistant-skill's Project Dashboard rather than rendering a separate format. This keeps the display consistent regardless of which skill the user entered through.

The authoritative column layout, sort order (newest-first by last-modified `project.json`), and status badges are all defined in ea-assistant-skill.

## View Display (Project Detail)

```
## <Project Name> — <Organisation>
**Status:** Active | **Started:** 2026-01-15 | **Target:** 2026-06-30
**Next Action:** Complete business capability gap analysis

### Phase Plan
| Phase | Depth    | Status       | Progress | Version | Target Date |
|-------|----------|--------------|----------|---------|-------------|
| Prelim| Standard | Complete     | 100%     | v1.0    | —           |
| A     | Standard | Complete     | 100%     | v1.0    | Mar 31      |
| B     | Full     | In Progress  | 45%      | v1.0    | Jun 30      |
| C     | Standard | Not Started  | 0%       | —       | Sep 30      |

### Current Phase Artifacts (Phase B)
| Artifact                | Status       | Progress | Version | Target Date |
|-------------------------|--------------|----------|---------|-------------|
| Business Capability Map | Complete     | 100%     | v1.1    | Mar 15      |
| Process Flow Diagram    | In Progress  | 60%      | v1.0    | Apr 30      |
| Gap Analysis (Business) | Not Started  | 0%       | —       | Jun 30      |

### Uploaded Documents
| File | Uploaded | Pre-populated Artifacts |
|------|----------|------------------------|
| strategy-2026.pdf | 2026-01-15 | Architecture Vision, Capability Map |

### Publications
| Version | Date | Format | File |
|---------|------|--------|------|
| v1.0 | 2026-02-01 | .md | publications/acme-ea-document-v1.0.md |
```

## Resume Flow

1. Ask which project to resume (or infer from context)
2. Load project.json + ea-context.md
3. Display project detail (as above)
4. Say: "Resuming Phase [X] (v[version]). Last completed: [artifact]. Next: [nextAction]."
5. Hand off to togaf-adm-skill guidance mode

## Updating project.json

When any update operation is performed:
1. Read current project.json
2. Apply the update to the relevant field
3. Write the updated project.json back
4. Confirm: "Updated [field] for [project/phase/artifact]."

Always update `nextAction` at the project level when phase or artifact status changes.

## Progress Calculation

- Phase progress: set explicitly by user ("40% done") OR calculated as (complete artifacts / total artifacts) × 100
- Overall project progress: average of all non-Skipped phase progress values

## Upload Document — Project Context

Before running document ingestion, ask: "Which project should this document be attached to?" (or infer from current session context). Pass the project's folder path to the ingestion workflow so extracted content is written to the correct project.json.

## Complete project.json Schema

This is the canonical schema. All skills that read or write project.json must conform to this structure.

```json
{
  "name": "string",
  "organisation": "string",
  "status": "Active | On Hold | Complete",
  "startDate": "YYYY-MM-DD",
  "targetDate": "YYYY-MM-DD",
  "currentPhase": "string",
  "nextAction": "string",
  "governance": {
    "approver": "string",
    "reviewCadence": "string",
    "hasArchitectureBoard": true
  },
  "phases": [
    {
      "phase": "Preliminary | A | B | C | D | E | F | G | H",
      "name": "string",
      "sequence": 1,
      "depth": "Light | Standard | Full",
      "status": "Not Started | In Progress | Complete | Skipped | On Hold",
      "progress": 0,
      "targetDate": "YYYY-MM-DD | null",
      "currentVersion": "1.0",
      "versions": [
        { "version": "1.0", "date": "YYYY-MM-DD", "notes": "string" }
      ],
      "artifacts": [
        {
          "name": "string",
          "status": "Not Started | In Progress | Complete",
          "progress": 0,
          "targetDate": "YYYY-MM-DD | null",
          "currentVersion": "1.0",
          "versions": [
            {
              "version": "1.0",
              "file": "artifacts/<phase>-<slug>-v1.0.md",
              "date": "YYYY-MM-DD",
              "notes": "string"
            }
          ]
        }
      ],
      "nextAction": "string"
    }
  ],
  "documents": [
    {
      "file": "documents/<filename>",
      "uploadedDate": "YYYY-MM-DD",
      "description": "string",
      "extractedContent": {
        "businessDrivers": [],
        "stakeholders": [],
        "capabilities": [],
        "constraints": [],
        "technologyMentioned": [],
        "gaps": []
      },
      "mappedTo": [
        { "artifact": "string", "fields": ["string"] }
      ],
      "gapQuestions": ["string"]
    }
  ],
  "publications": [
    {
      "version": "1.0",
      "date": "YYYY-MM-DD",
      "format": ".md | .docx",
      "file": "publications/<project-name>-ea-document-v1.0.md",
      "notes": "string",
      "snapshotProgress": {
        "overallProgress": 0,
        "phases": [
          {
            "phase": "A",
            "status": "Complete",
            "progress": 100,
            "artifactsIncluded": ["string"]
          }
        ]
      }
    }
  ]
}
```

## Skills Referenced

- **togaf-adm-skill** — resume flow, new phase version
- **togaf-artifacts-skill** — new artifact version, publish
- **ea-interview-techniques-skill** — document ingestion
