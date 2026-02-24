# Agent Harness Demo

## Project overview
FastAPI prediction service wrapping a scikit-learn classifier trained on the iris dataset. Uses uv for dependency management.

## Conventions
- Read `.cursor/rules/` for all project conventions. They are the canonical source.
- Read `.cursor/skills/` (or `.agents/skills/`) for on-demand workflow procedures.
- Use `uv` for all dependency and environment operations. Never use pip directly.
- For Python implementation tasks, create `.python-version` with `3.11`.
- Use `pyproject.toml` for all project metadata and dependencies once code work begins. No setup.py, no requirements.txt.

## Project structure
All source code lives in `src/`. Tests live in `tests/`. Model artifacts go in `models/`.

## Orchestration shortcut
If the user prompt starts with `Orchestrate` (for example, `Orchestrate creation of ...`), use the `orchestration` skill from `.cursor/skills/orchestration/` and run its workflow:
1. `scope-planner`
2. `coder`
3. `quality-reviewer`

For Python implementation tasks started via orchestration, create `.python-version` (`3.11`) and `pyproject.toml` first, then proceed with the feature work.

## Demo defaults
For live demos in this repo (unless the user says otherwise):
- Prefer a fast local verification path on a local port (for example, port `8000`) before optional extras.
- Print the exact `curl` commands used for manual verification.
- Do not spend time on Docker, CI, deployment, or performance tuning.
- Include tests, but do not block completion on running the full test suite if it is slow; report that it was deferred.

## Quality gates
Before considering any non-demo task complete, run:
1. `uv run ruff check .`
2. `uv run pytest`

For live demos, follow the Demo defaults above (smoke verification first; full pytest may be deferred if slow).
