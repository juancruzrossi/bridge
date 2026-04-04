# Neural SDD — Codex Installation

Add the following to your Codex system prompt or project instructions:

```
Fetch and follow instructions from https://raw.githubusercontent.com/juancruzrossi/neural/main/.codex/INSTALL.md
```

## Setup

Neural SDD is a Spec-Driven Development framework. To use it with Codex:

1. Clone the repository into your project:
   ```bash
   git clone https://github.com/juancruzrossi/neural.git .neural-plugin
   ```

2. Read the skills from `.neural-plugin/skills/` when starting a new feature workflow.

3. Follow the workflow: interview → plan → execute → review → archive

## Available Skills

| Skill | File | Purpose |
|-------|------|---------|
| neural-interview | `skills/neural-interview/SKILL.md` | Clarify requirements via Socratic interview |
| neural-plan | `skills/neural-plan/SKILL.md` | Generate implementation plan with adversarial review |
| neural-execute | `skills/neural-execute/SKILL.md` | Wave-based parallel execution |
| neural-review | `skills/neural-review/SKILL.md` | Verify implementation against plan and brief |
| neural-quick | `skills/neural-quick/SKILL.md` | Fast-path for small tasks |
| neural-debug | `skills/neural-debug/SKILL.md` | Root-cause debugging |
| neural-status | `skills/neural-status/SKILL.md` | Show feature progress |
| neural-archive | `skills/neural-archive/SKILL.md` | Archive completed features |

## Artifacts

Neural stores all artifacts in `.neural/` at the project root:

```
.neural/
├── wip/<feature-name>/    ← active features
│   ├── BRIEF.md
│   ├── PLAN.md
│   └── REVIEW.md
└── archive/<feature-name>/ ← completed features
```
