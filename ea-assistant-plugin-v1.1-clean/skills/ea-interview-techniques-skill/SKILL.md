---
name: ea-interview-techniques-skill
description: >
  Use when preparing for or running stakeholder interviews in an EA engagement. Triggers: "what should I ask [role]", "I have a call/meeting with", "I have 45 minutes with", "prep for my interview", "interview questions for Phase X", "what do I cover with [CIO/CFO/CISO/Director/Commissioner/CAO]", "how do I probe", "they keep giving vague answers", "run a discovery workshop", "facilitate a workshop", "Phase B questions", "Phase C discovery". Always trigger when a stakeholder role is mentioned alongside intent to meet or interview. Also triggers on document upload for architecture input extraction: "they sent me a document", "use this PDF", "review this for architecture inputs", or any file upload in an EA engagement context.
license: MIT
metadata:
  author: Costa Pissaris
  version: 1.1.0
  created: 2026-03-10
  last_reviewed: 2026-03-12
  review_interval_days: 90
---

# EA Interview Techniques

Structured interviews are the primary mechanism for eliciting architecture inputs from stakeholders. Each ADM phase has distinct questions aligned to its artifacts. This skill provides phase-by-phase question sets, facilitation guidance, output routing, and document ingestion mode.

## Phase Auto-Routing

When the user mentions a specific phase or the active project phase is known from context, **auto-load the matching question set** without asking. Don't make the user navigate — just show the right questions.

| Trigger phrase | Load |
|---|---|
| "Phase A", "architecture vision", "scope", "Vision" | Phase A questions |
| "Phase B", "business architecture", "capabilities", "processes" | Phase B questions |
| "Phase C", "information systems", "applications", "data" | Phase C questions |
| "Phase D", "technology", "infrastructure", "cloud" | Phase D questions |
| "Phase E", "opportunities", "solutions", "roadmap" | Phase E questions |
| "Phase F", "migration", "implementation plan" | Phase F questions |
| "Phase G", "governance", "compliance", "contracts" | Phase G questions |
| "Phase H", "change", "architecture change management" | Phase H questions |
| "Preliminary", "principles", "governance model" | Preliminary questions |

If no phase is mentioned, ask: "Which phase are we interviewing for?" before loading a question set.

## Interview Principles

- Ask one question at a time — avoid overwhelming stakeholders
- Probe responses with "Can you give an example?" or "What would success look like?"
- Capture verbatim responses before summarising
- Validate captured outputs with the stakeholder before proceeding
- Map all responses to the relevant artifact field

## Meeting Prep Mode

When the user says "I have a call with [role]", "I have 45 minutes with [role]", or "prep for my meeting with [role]":

1. Identify which ADM phase the engagement is currently in
2. Load the matching phase question set
3. Filter and prioritise questions most relevant to that stakeholder's role and concerns (use the Stakeholder Map if available in project context)
4. Present a **shortlist of 5–7 questions** tailored to the role — not the full list
5. Add role-specific probes: what this stakeholder typically cares about, what they're likely to resist, what success looks like from their position

**Role heuristics:**

| Role | Typical concerns | Likely resistance |
|---|---|---|
| CAO / CEO | Risk, optics, timeline, cost | Anything that delays or destabilises operations |
| CFO / Finance Commissioner | Budget certainty, ROI, procurement risk | Open-ended cost estimates, vendor lock-in |
| CIO / IT Director | Technical feasibility, integration complexity, support burden | Scope creep, new platforms that increase support load |
| CISO | Security, compliance, data residency | Cloud services without clear security posture |
| Operations / Planning roles | Practicality, day-to-day workability | Theoretical architecture that ignores operational reality |
| Legal / Risk | Liability, compliance, audit trail | Anything undocumented or informally governed |

## Facilitation Patterns

### One-on-One Stakeholder Interview
- Duration: 45–60 minutes
- Format: Conversational, one question at a time
- Capture: Record responses in context; generate Requirements Register entries at the end
- Validate: Summarise and confirm with stakeholder before closing

### Workshop (Group)
- Duration: 2–4 hours
- Format: Present questions on screen, capture group responses collaboratively
- Use: Capability mapping workshops, gap analysis reviews, roadmap prioritisation
- Technique: Dot-voting for prioritisation, affinity grouping for capabilities

### Handling Difficult Stakeholders

**Vague or evasive answers:**
- "Can you walk me through a specific example of when that happened?"
- "If we fixed that tomorrow, what would be different?"
- "On a scale of 1–10, how big a problem is this for your team?"

**Sceptical or resistant stakeholders:**
- Acknowledge their concern before asking the next question: "That makes sense — the last programme didn't deliver. Given that, what would make this one credible for you?"
- Ask what success would need to look like for them specifically
- Don't push for agreement — capture the resistance as a stakeholder concern in the project record

**Stakeholder goes off-topic:**
- "That's useful context — I want to make sure I capture it. Can I park it here and come back to it, so we stay on track for the [artifact] we're building today?"

**Stakeholder provides a document instead of answering:**
- Switch to Document Ingestion Mode (see below) — don't force the interview format
- Extract answers from the document, then run a shorter interview covering only the gaps

### Document Review
- Trigger: Stakeholder provides a document instead of answering questions directly, uploads a file, says "they sent me this", or shares a PDF/Word doc
- Action: Use Document Ingestion Mode (see below) to extract content, then generate gap questions
- Follow-up: Run interview covering only questions not answered by the document



### Preliminary Phase
**Goal**: Establish architecture principles and governance model

1. What are the top 3 strategic goals of the organisation for the next 3 years?
2. What constraints (regulatory, financial, technical) must the architecture comply with?
3. Who are the key decision-makers for IT investment?
4. Is there an existing architecture governance body? How does it operate?
5. What does good architecture practice look like in your organisation?

**Outputs captured**: Architecture Principles draft, Governance Model inputs

---

### Phase A — Architecture Vision
**Goal**: Define scope, concerns, and high-level target

1. What business problem or opportunity is driving this architecture engagement?
2. What does success look like at the end of this engagement?
3. Who are the key stakeholders and what are their primary concerns?
4. What is in scope and out of scope for this architecture?
5. Are there any known constraints or assumptions?
6. What existing architecture assets or documents should we consider?
7. What is the desired timeline for completion?

**Outputs captured**: Statement of Architecture Work inputs, Stakeholder Map entries, Architecture Vision narrative

---

### Phase B — Business Architecture
**Goal**: Define business capabilities, processes, and gaps

1. What are the primary business functions this organisation performs?
2. Can you describe the key end-to-end business processes?
3. Where do you see pain points or inefficiencies today?
4. What capabilities do you wish the business had but currently lacks?
5. How is the organisation structured (divisions, teams, geography)?
6. What business outcomes are most important to improve?
7. What does the future state of the business look like in 3–5 years?

**Outputs captured**: Business Capability Map, Process Flow inputs, Gap Analysis (business)

---

### Phase C — Information Systems
**Goal**: Understand data entities and application portfolio

1. What are the key data domains your organisation manages (customers, products, financials, etc.)?
2. What applications currently support each business function?
3. Which applications are considered strategic and which are candidates for replacement?
4. Where does data duplication or inconsistency occur?
5. What are the critical integration points between systems?
6. Are there regulatory requirements for data residency, retention, or privacy?

**Outputs captured**: Application Portfolio Catalog, Data Entity Catalog, App/Function Matrix

---

### Phase D — Technology Architecture
**Goal**: Understand current and desired technology platform

1. What is the current technology stack (infrastructure, cloud, on-premise)?
2. What technology standards or approved products does the organisation mandate?
3. What technology capabilities are missing or insufficient?
4. What is the organisation's cloud strategy?
5. What are the key technology constraints (budget, skills, vendor lock-in)?
6. What does the technology landscape look like in 3 years?

**Outputs captured**: Technology Portfolio Catalog, Technology Standards, Gap Analysis (technology)

---

### Phase E — Opportunities and Solutions
**Goal:** Identify implementation projects and work packages

1. Which capability gaps identified in earlier phases are highest priority to address?
2. What projects are already in flight that the architecture must align with?
3. What work packages or initiatives would close the key gaps?
4. Are there opportunities to reuse existing assets or platforms?
5. What transition states are acceptable between the baseline and target architecture?

**Outputs captured:** Work Package Catalog, Project Context Diagram, Transition Architecture(s)

---

### Phase F — Migration Planning
**Goal:** Gather inputs for the detailed implementation and migration plan

1. What is the total investment budget available for the transformation programme?
2. Are there sequencing dependencies between the work packages identified in Phase E?
3. What is the realistic timeline for each major workstream?
4. What organisational change or training will be required alongside technical delivery?
5. How will the migration plan be tracked and reported?

**Outputs captured:** Implementation and Migration Plan inputs, Architecture Roadmap priorities

---

### Phase G — Implementation Governance
**Goal:** Gather inputs for architecture compliance and contract oversight

1. Which implementation projects are currently in flight that require architecture oversight?
2. How are architecture contracts established with delivery teams?
3. What process exists to assess whether a project is compliant with the approved architecture?
4. Who has authority to approve deviations from the agreed architecture?
5. How are change requests from implementation projects currently handled?

**Outputs captured:** Architecture Contract inputs, Compliance Assessment inputs, Implementation Governance Model

---

### Phase H — Architecture Change Management
**Goal:** Capture change drivers and assess architecture evolution needs

1. What business or technology changes are driving a need to revisit the current architecture?
2. How significant is the proposed change — is it a refinement or a fundamental shift?
3. What parts of the current architecture are affected?
4. What is the urgency and timeline for addressing this change?
5. Should this change trigger a new ADM cycle, or can it be addressed as a controlled update?

**Outputs captured:** Architecture Change Request inputs, Change Impact Assessment inputs

---

## Facilitation Patterns

### One-on-One Stakeholder Interview
- Duration: 45–60 minutes
- Format: Conversational, one question at a time
- Capture: Record responses in context; generate Requirements Register entries at the end
- Validate: Summarise and confirm with stakeholder before closing

### Workshop (Group)
- Duration: 2–4 hours
- Format: Present questions on screen, capture group responses collaboratively
- Use: Capability mapping workshops, gap analysis reviews, roadmap prioritisation
- Technique: Dot-voting for prioritisation, affinity grouping for capabilities

### Document Review
- Trigger: Stakeholder provides a document instead of answering questions directly
- Action: Use Document Ingestion Mode (see below) to extract content, then generate gap questions
- Follow-up: Run interview covering only questions not answered by the document

## Output Routing Table

After each interview, route captured information to the appropriate artifact:

| Interview Response | Target Artifact |
|---|---|
| Business goals / drivers | Architecture Vision, Principles |
| Stakeholder names / roles / concerns | Stakeholder Map |
| Business capabilities mentioned | Business Capability Map |
| Process descriptions | Process Flow Diagram |
| Application names / owners | Application Portfolio Catalog |
| Pain points / gaps | Gap Analysis |
| Future state desires | Target Architecture |
| Regulatory / policy constraints | Requirements Register |
| Timeline preferences | Architecture Roadmap |

## Document Ingestion Mode

**Trigger:** User says "use this document", "load this file", "I have a strategy doc", "they sent me a document", "they gave me this PDF", "can you review this for architecture inputs", "what do I do with this file", or uploads any file (.pdf, .docx, .md, .txt) in the context of an EA engagement.

**When a file is uploaded with no other instruction:** Default to Document Ingestion Mode. Don't ask what to do with it — extract architecture inputs and show pre-populated + gap questions automatically.

**Before starting:** Confirm which project this document belongs to. If the project folder path isn't already in context, ask: "Which project should this document be attached to, and where is its folder?" Do not proceed with ingestion until the project folder path is known.

**Workflow:**

1. Read the document at the provided file path
2. Extract and categorise content into these buckets:
   - Business drivers / strategic goals
   - Stakeholders (names, roles, concerns)
   - Business capabilities or functions
   - Applications or systems mentioned
   - Technology components or platforms
   - Constraints (regulatory, financial, technical)
   - Gaps or pain points
   - Timelines or deadlines
3. Map extracted content to artifact fields using the Output Routing Table above
4. Pre-populate interview answers: for each phase question answered by the document, mark as "answered from document" and show the extracted value
5. Generate gap questions: flag phase questions NOT answered by the document
6. Show a summary with two sections:
   - **Pre-populated from document** — list of extracted values with → artifact mapping
   - **Gap questions (need interview)** — list of remaining questions
7. Store extraction results in `project.json` documents array
8. Copy document to `documents/` folder in project

**Supported formats:** .md, .txt, .pdf (read as text), .docx (read as text)

**During subsequent interviews:** Skip pre-populated questions, show the extracted value, ask the user to confirm or override.

**project.json documents entry structure:**

```json
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
    { "artifact": "Architecture Vision", "fields": ["problem statement"] }
  ],
  "gapQuestions": []
}
```
