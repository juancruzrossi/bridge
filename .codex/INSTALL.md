# Bridge SDD — Codex Installation

Add the following to your Codex system prompt or project instructions:

```
Fetch and follow instructions from https://raw.githubusercontent.com/juancruzrossi/bridge/main/.codex/INSTALL.md
```

## Setup

Bridge SDD is a Spec-Driven Development framework. To use it with Codex:

1. Clone the repository into your project:
   ```bash
   git clone https://github.com/juancruzrossi/bridge.git .bridge-plugin
   ```

2. Read the skills from `.bridge-plugin/skills/` when starting a new feature workflow.

3. Follow the workflow: interview → plan → execute → review → archive

## Available Skills

| Skill | File | Purpose |
|-------|------|---------|
| bridge-interview | `skills/bridge-interview/SKILL.md` | Clarify requirements via Socratic interview |
| bridge-plan | `skills/bridge-plan/SKILL.md` | Generate implementation plan with adversarial review |
| bridge-execute | `skills/bridge-execute/SKILL.md` | Wave-based parallel execution |
| bridge-review | `skills/bridge-review/SKILL.md` | Verify implementation against plan and brief |
| bridge-quick | `skills/bridge-quick/SKILL.md` | Fast-path for small tasks |
| bridge-debug | `skills/bridge-debug/SKILL.md` | Root-cause debugging |
| bridge-status | `skills/bridge-status/SKILL.md` | Show feature progress |
| bridge-archive | `skills/bridge-archive/SKILL.md` | Archive completed features |

## Artifacts

Bridge stores all artifacts in `.bridge/` at the project root:

```
.bridge/
├── wip/<feature-name>/    ← active features
│   ├── BRIEF.md
│   ├── PLAN.md
│   └── REVIEW.md
└── archive/<feature-name>/ ← completed features
```
