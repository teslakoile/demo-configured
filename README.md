# Agent Harness Demo (Configured)

This project demonstrates how agent harness configuration (rules, skills, subagents) produces higher-quality output than vanilla prompting.

## Demo prompt

Orchestrate creation of a FastAPI service that trains a classifier on the iris dataset and serves it as a prediction API.

Keep this demo fast:
- Use a lightweight iris training flow (no hyperparameter search, no heavy optimization)
- Implement health, model info, single prediction, and batch prediction endpoints
- Train and persist the model locally
- Load the model at app startup (not per request)
- Start the API locally on port 8000
- After startup, verify it works with one health check request and one prediction request

## Agent configuration

- **10 rules** in `.cursor/rules/` (canonical source)
- **3 skills** in `.cursor/skills/` (symlinked to `.claude/skills/` and `.agents/skills/`)
- **3 subagents** in `.cursor/agents/` (shared to `.claude/agents/` and `.agents/agents/` via symlinks)
- **AGENTS.md** primary project instructions (Codex and any harness configured to read it)

## Cross-harness compatibility

`.cursor/` is the single source of truth for shared agent configuration. Claude Code and Codex read via symlinks:
- `.claude/skills/` -> `.cursor/skills/`
- `.agents/skills/` -> `.cursor/skills/`
- `.claude/agents/` -> `.cursor/agents/`
- `.agents/agents/*.md` -> `.cursor/agents/*.md`
