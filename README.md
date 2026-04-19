# Agent Skills

A catalog of reusable agent skills for AI coding tools such as Claude Code, OpenCode, Cursor, and any other coding assistant

## What This Repository Contains

This repository publishes installable skills that help coding assistants follow a specific workflow. It is structured as a catalog, so new skills can be added over time without changing how the repository is used.

## Getting Started

Install a skill directly from this repository:

```bash
npx skills add https://github.com/giulianotesta7/agent-skills --skill sdd-greenfield-spec
```

or:

```bash
npx skills add https://github.com/giulianotesta7/agent-skills --skill api-integration-builder
```

That command installs the `sdd-greenfield-spec` skill into your local skills environment so your coding assistant can invoke it when needed.

## Available Skills

| Skill | Purpose | Outputs | Install |
| --- | --- | --- | --- |
| `sdd-greenfield-spec` | Creates a complete Spec-Driven Development package for a brand-new project | `proposal.md`, `spec.md`, `design.md`, `tasks.md` | `npx skills add https://github.com/giulianotesta7/agent-skills --skill sdd-greenfield-spec` |
| `api-integration-builder` | Guides assistants through reliable API integrations, typed clients, webhook handlers, retries, validation, and payload mapping | Integration client/server code, DTO mapping, webhook flows, integration specs | `npx skills add https://github.com/giulianotesta7/agent-skills --skill api-integration-builder` |

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

### `api-integration-builder`

Use this skill when building integrations against third-party or internal APIs, especially when the task involves webhook handling, retries, idempotency, payload validation, or transport-to-domain mapping.

It is designed for practical backend and automation work such as:
- Third-party API clients
- Internal service adapters
- Webhook receivers and processors
- n8n-connected integrations
- FastAPI / Flask / Node.js integration layers

## Repository Structure

```text
.
├── README.md
├── skills/
│   └── api-integration-builder/
│       ├── SKILL.md
│       └── assets/
│           └── integration-spec-template.md
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
