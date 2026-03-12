---
name: togaf-adm-skill
description: >
  Use when working through TOGAF ADM phases — configuring, starting, resuming, or executing any phase. Triggers: "start Phase [X]", "begin Phase [X]", "resume Phase [X]", "continue Phase [X]", "configure phases", "what do I do in Phase B", "skip Phase D", "set Phase B depth to Full", "what artifacts does Phase B produce", "what are the inputs to Phase C", "how does Phase E work", "define phase sequence", "set up the ADM", or any request to progress through, configure, or understand any TOGAF ADM phase or its artifacts, inputs, and outputs.
license: MIT
metadata:
  author: Costa Pissaris
  version: 1.0.0
  created: 2026-03-10
  last_reviewed: 2026-03-10
  review_interval_days: 90
---

# TOGAF ADM

togaf-adm-skill guides EA engagements through the Architecture Development Method in two modes: **configuration** (planning which phases apply, their sequence, depth, and artifacts) and **guidance** (interactively executing each phase with the user). Configuration mode is invoked during ea-setup-skill or the Preliminary phase; guidance mode is invoked during delivery as the user progresses through selected phases.

## Mode Selection

| Trigger | Mode |
|---|---|
| Called from ea-setup-skill or Preliminary phase | Configuration mode |
| User says "start phase X", "continue phase", "resume" | Guidance mode |
| User says "what do I need for Phase B?" | Guidance mode |
| No prior configuration exists | Configuration mode first, then Guidance mode |

---

## Configuration Mode

Configuration mode produces a `phases` array for `project.json`. Work through the following steps one at a time, waiting for user input at each step before proceeding.

### Step 1 — Architecture Principles

Present the default principles one at a time. For each, ask the user to confirm, modify, or skip. After all defaults, ask if they want to add any additional principles.

Default principles:
1. Business-led
2. Technology-agnostic
3. Reuse before buy before build
4. Security by design
5. Cloud-first

### Step 2 — Governance Model

Ask the following questions one at a time:

1. Who approves architecture decisions?
2. What is the review cadence (e.g. weekly, per phase, ad hoc)?
3. Is there a formal architecture board?

Record answers for inclusion in the governance section of `project.json`.

> Captured governance answers are stored in the `governance` object in `project.json` (alongside `phases`).

### Step 3 — Phase Selection

Present all ADM phases and ask which apply to this engagement. Offer common patterns as shortcuts:

| Pattern | Phases |
|---|---|
| Full programme | Preliminary → A → B → C → D → E → F → G → H |
| Strategy engagement | A → B |
| Technology refresh | A → C → D → E → F |
| Governance only | G → H |

Ask: "Which pattern fits, or would you like to select phases individually?"

> **Note:** Requirements Management is not a selectable phase — it runs continuously across all phases and is automatically included in every engagement. It does not appear in `phases[]` in `project.json`.

### Step 4 — Sequence

Confirm the phase order with the user. Note that **Phase B and Phase C can overlap** — ask whether they should run concurrently or sequentially.

### Step 5 — Depth per Phase

For each selected phase, ask the user to choose a depth level:

- **Light** — Key artifacts only, 1–2 interviews, no formal governance
- **Standard** — Priority artifacts, structured interviews, review checkpoint
- **Full** — All artifacts, formal governance, signed architecture contracts

### Step 6 — Artifacts per Phase

For each selected phase and its chosen depth, present the default artifact list below. Ask the user to confirm, add, or remove artifacts, and optionally set a target completion date for each.

> These defaults are embedded here so step 6 doesn't require reading togaf-artifacts-skill. If an artifact isn't listed below, check togaf-artifacts-skill's full artifact catalog for the complete set.

#### Artifact Defaults by Phase and Depth

| Phase | Light (key only) | Standard (priority) | Full (all) |
|-------|-----------------|---------------------|------------|
| **Preliminary** | Architecture Principles | + Governance and Support Strategy | + Organisational Model for EA, Tailored Architecture Framework, Initial Architecture Repository |
| **A — Vision** | Architecture Vision Document, Stakeholder Map | + Statement of Architecture Work, Capability Assessment | + Communications Plan, Architecture Definition Document (high-level) |
| **B — Business** | Business Capability Map, Business Gap Analysis | + Process Flow Diagram, Business Interaction Matrix | + Organisation Map, Architecture Definition Document (business), Architecture Requirements Specification (business), Business Architecture Roadmap Components |
| **C — Info Sys** | Application Portfolio Catalog, Application Architecture Gap Analysis | + Data Entity Catalog, Data Architecture Gap Analysis, Application Interaction Matrix | + Application Communication Diagram, Data Flow Diagram, Architecture Definition Document (IS), Architecture Requirements Specification (IS), IS Architecture Roadmap Components |
| **D — Technology** | Technology Portfolio Catalog, Technology Gap Analysis | + Technology Standards Catalog, Architecture Definition Document (technology) | + Technology Landscape Diagram, Architecture Requirements Specification (technology), Technology Architecture Roadmap Components |
| **E — Opportunities** | Architecture Roadmap (initial), Work Package Definitions | + Implementation and Migration Strategy | + Transition Architecture(s), Solution Building Blocks |
| **F — Migration** | Architecture Roadmap (finalised), Implementation and Migration Plan | + Finalised Architecture Definition Document | + Transition Architectures (updated), Finalised Architecture Requirements Specification |
| **G — Governance** | Compliance Assessments | + Architecture Contracts | + Architecture Compliance Recommendations |
| **H — Change** | Change Requests | + Architecture Update Documents | + New Statement of Architecture Work |

### Step 7 — Phase Target Dates

For each selected phase, ask for an optional target completion date.

### Configuration Output

Produce a `phases` array for `project.json`. Each phase entry must include:

```json
{
  "phase": "B",
  "name": "Business Architecture",
  "sequence": 2,
  "depth": "Standard",
  "status": "Not Started",
  "progress": 0,
  "targetDate": "2026-06-30",
  "currentVersion": "1.0",
  "versions": [],
  "artifacts": [],
  "nextAction": "Begin stakeholder interviews for business capability mapping"
}
```

The top-level `project.json` also includes a `governance` object alongside `phases`:

```json
"governance": {
  "approver": "string",
  "reviewCadence": "string",
  "hasArchitectureBoard": true
}
```

---

## Guidance Mode

Guidance mode walks the user interactively through a selected phase. Load phase state from `project.json` before starting.

### Executing a Phase

1. Load phase state from `project.json`.
2. Check `project.json` documents array for pre-extracted content mapped to this phase.
3. Show phase summary:
   - Purpose of the phase
   - Progress % (artifacts complete vs. total)
   - Next action
4. For each incomplete artifact:
   a. Show any pre-populated fields from uploaded documents.
   b. Ask the user to confirm or override pre-populated values.
   c. Use **ea-interview-techniques-skill** for remaining gap questions only — do not re-ask questions already answered by uploaded documents.
   d. Generate the artifact via **togaf-artifacts-skill**.
   e. Update artifact `status` and `progress` in `project.json`.
5. When all artifacts are complete:
   - Run a phase checkpoint (summarise decisions, flag risks, confirm completeness).
   - Mark phase `status` as `Complete`.
   - Advance `currentPhase` in `project.json` to the next phase in sequence.

### Resuming Mid-Phase

When the user resumes a phase already in progress:

1. Read artifact statuses from `project.json`.
2. Skip artifacts with `status: Complete`.
3. Continue from the first artifact with `status: In Progress` or `Not Started`.
4. Open with: "Resuming Phase [X]. Completed: [artifact list]. Next: [artifact name]."

### Adding a Document Mid-Engagement

If the user says "upload a document", "load this file", or similar at any point:

1. Call **ea-interview-techniques-skill** in document ingestion mode.
2. Extract and map content to phases and artifacts.
3. Re-evaluate gap questions for the current phase and all remaining phases — remove questions now answered by the document.

### Creating a New Phase Version

Triggered by: "redo Phase B", "new version of Phase A", or similar.

1. Bump the phase `currentVersion` (e.g. `1.0` → `2.0`).
2. Reset all artifact `status` values to `Not Started` (preserve existing artifact files).
3. Add an entry to the phase `versions[]` array with the date and user-provided notes.
4. Re-run guidance mode for the phase from Step 1.

---

## ADM Phase Reference

### Phase Map

```
              ┌─────────────────────┐
              │     Preliminary     │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Requirements Mgmt  │ ← continuous across all phases
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase A: Vision    │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase B: Business  │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase C: Info Sys  │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase D: Technology│
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase E: Opportun. │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase F: Migration │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase G: Governance│
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │  Phase H: Change    │
              └──────────┬──────────┘
                         │
                    (new cycle)
```

### Quick Phase Selector

| Goal | Start with |
|---|---|
| New EA programme | Preliminary |
| Architecture engagement request | Phase A |
| Define business capabilities | Phase B |
| Map applications to business | Phase C |
| Define technology platform | Phase D |
| Plan implementation projects | Phase E |
| Build a roadmap | Phase F |
| Govern ongoing projects | Phase G |
| Manage architecture change | Phase H |

### Phase Summary

| Phase | Purpose | Key Artifacts |
|---|---|---|
| Preliminary | Prepare the organisation for TOGAF adoption; establish governance and tailor the ADM | Architecture Principles Catalog, Governance Model |
| A — Architecture Vision | Define scope, stakeholders, and high-level vision; obtain approval to proceed | Stakeholder Map, Architecture Vision Document, Business Scenario, Statement of Architecture Work |
| B — Business Architecture | Develop baseline and target Business Architecture; identify gaps | Business Capability Map, Process Flow Diagram, Organisation/Actor Catalog, Business Interaction Matrix |
| C — Information Systems Architecture | Develop Data and Application Architectures; map applications to business functions | Data Entity Catalog, Application Portfolio Catalog, Application/Function Matrix, Data Flow Diagram |
| D — Technology Architecture | Define the technology platform supporting data and applications; set standards | Technology Portfolio Catalog, Technology Standards Catalog, System/Technology Matrix |
| E — Opportunities and Solutions | Identify implementation projects and work packages from gap analyses | Project Context Diagram, Benefits Diagram, Implementation Factor Assessment |
| F — Migration Planning | Develop a detailed Implementation and Migration Plan; prioritise and sequence projects | Architecture Roadmap (Gantt), Transition Architecture descriptions, Migration Plan |
| G — Implementation Governance | Ensure architecture conformance during implementation; manage contracts | Architecture Contract, Compliance Assessment, Implementation Governance Model |
| H — Architecture Change Management | Monitor architecture performance; assess and manage change requests | Architecture Change Request, Change Impact Assessment |
| Requirements Management | Continuously identify, store, and manage architecture requirements across all phases | Requirements Repository, Requirements Impact Assessment |

> Full inputs and outputs per phase: see `references/phase-inputs-outputs.md`

---

## TOGAF 10 vs TOGAF 9

- Restructured into five parts: Introduction & Core, ADM, ADM Guidelines & Techniques, Architecture Content, and Enterprise Architecture Capability & Governance.
- Updated Content Metamodel with a clearer class hierarchy and improved Architecture Repository classification.
- Strengthened guidance on agile integration and team-based architecture practice.
- New emphasis on Enterprise Architecture as an ongoing practice, not just a one-time process.
