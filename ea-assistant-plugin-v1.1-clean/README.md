# EA Assistant Plugin v1.1

Enterprise Architecture assistant for Claude, built on TOGAF 10 / ADM.

## Skills

| Skill | Purpose |
|-------|---------|
| `ea-assistant-skill` | Orchestrator — entry point for all EA work |
| `ea-setup-skill` | New engagement setup and Preliminary phase |
| `ea-projects-skill` | Project state management |
| `ea-interview-techniques-skill` | Stakeholder interviews and document ingestion |
| `togaf-adm-skill` | ADM phase configuration and guidance |
| `togaf-artifacts-skill` | Artifact generation (Mermaid, Word, PPTX) |

## Install

Upload each `skills/<skill-name>/SKILL.md` to your Claude skills directory, or install via:

```
/plugin install ea-skills@somtimz
```

## Structure

```
skills/
  ea-assistant-skill/SKILL.md
  ea-setup-skill/SKILL.md
  ea-projects-skill/SKILL.md
  ea-interview-techniques-skill/SKILL.md
  togaf-adm-skill/SKILL.md
  togaf-adm-skill/references/phase-inputs-outputs.md
  togaf-artifacts-skill/SKILL.md
```

## Version History

- v1.1 (2026-03-12) — Improved trigger descriptions across all skills, added Meeting Prep Mode and Difficult Stakeholder guidance to interview skill, added slash command routing to orchestrator
- v1.0 (2026-03-10) — Initial release
