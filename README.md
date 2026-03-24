# Agent Skills

A catalog of reusable agent skills for AI coding tools such as Claude Code, OpenCode, Cursor, and any other coding assistant

## What This Repository Contains

This repository publishes installable skills that help coding assistants follow a specific workflow. It is structured as a catalog, so new skills can be added over time without changing how the repository is used.

## Getting Started

Install a skill directly from this repository:

```bash
npx skills add https://github.com/giulianotesta7/agent-skills --skill sdd-greenfield-spec
```

That command installs the `sdd-greenfield-spec` skill into your local skills environment so your coding assistant can invoke it when needed.

## Available Skills

| Skill | Purpose | Outputs | Install |
| --- | --- | --- | --- |
| `sdd-greenfield-spec` | Creates a complete Spec-Driven Development package for a brand-new project | `proposal.md`, `spec.md`, `design.md`, `tasks.md` | `npx skills add https://github.com/giulianotesta7/agent-skills --skill sdd-greenfield-spec` |

## Skill Details

### `sdd-greenfield-spec`

Use this skill when you are starting a new project from scratch and want a complete specification before implementation begins.

It is designed for greenfield work such as:
- New apps
- New APIs
- New CLI tools
- New systems or internal platforms

The skill runs a structured discovery interview and then generates the four standard SDD artifacts:
- `proposal.md`
- `spec.md`
- `design.md`
- `tasks.md`

It is intended to work well with AI coding tools including Claude Code, OpenCode, Cursor, and Windsurf.

## Repository Structure

```text
.
├── README.md
└── sdd-greenfield-spec/
    └── SKILL.md
```

Each skill lives in its own directory and is defined by a `SKILL.md` file.

## Usage Notes

- Install only the skill you need.
- Trigger `sdd-greenfield-spec` when the task is a new project and you want planning artifacts before writing code.
- Expect the skill to ask for project context first, then produce the SDD package.

## License

This repository is licensed under the MIT License. See [LICENSE](./LICENSE).
