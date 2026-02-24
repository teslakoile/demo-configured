---
name: orchestration
description: Use when the user says "orchestrate", starts the prompt with "Orchestrate ...", says "build the full feature", "run the pipeline", or asks for end-to-end implementation. This skill defines the multi-step agent workflow for building features from planning through to PR-ready code.
---
# Orchestration Workflow

When triggered, execute the following steps in order. Each step should be a distinct phase with a clear handoff.

Trigger examples:
- `Orchestrate creation of the iris FastAPI classifier service with tests`
- `Orchestrate build of a FastAPI ML prediction API`

## Demo-Speed Defaults (apply unless the user asks otherwise)

- Prioritize a fast local demo path: build the feature, start the API locally, and verify with a small smoke check.
- Print the exact `curl` commands used for manual verification (health + at least one prediction request).
- Do not spend time on Docker, CI, deployment, or performance tuning unless explicitly requested.
- Include tests, but do not block completion on running the full test suite if it is slow. Prefer smoke verification plus a clear note that full pytest was deferred.

## Step 1: Planning (use scope-planner subagent if available)

Before writing any code:
1. Identify all components needed (files, modules, endpoints, tests).
2. List which rules from `.cursor/rules/` apply to this task.
3. List which skills from `.cursor/skills/` will be needed.
4. Produce a checklist of deliverables in markdown.
5. Do NOT write code in this step.

## Step 2: Implementation (use coder subagent if available)

With the plan as input:
1. Load the relevant skills (fastapi-scaffold, model-serving).
2. Follow all active rules.
3. Implement each item from the plan checklist.
4. After each file, verify it passes `uv run ruff check <file>`.

Implementation emphasis for demos:
- Build the minimal complete solution needed for local verification first.
- Defer Docker/CI/deployment/perf work unless the user explicitly asks for it.

## Step 3: Quality Review (use quality-reviewer subagent if available)

Review all generated code against:
1. Do tests exist for every endpoint? (testing-requirements rule)
2. Are all functions typed? (typing-standards rule)
3. Is the response envelope consistent? (api-style rule)
4. Is the model loaded at startup, not per-request? (model-lifecycle rule)
5. Are there any `print()` statements? (logging-standard rule)
6. Does the README match the required format? (documentation-style rule)

Produce a pass/fail checklist. If any check fails, go back to Step 2 for fixes.

## Step 4: Final Verification

Run local smoke verification first and print the exact commands used (for example, one health check and one prediction request against the local port).

Then run the quality gate when practical:
```bash
uv run ruff check .
uv run pytest
```

If `uv run pytest` is slow for a live demo, do not block completion as long as:
- tests were implemented,
- local smoke verification passed, and
- you explicitly report that full pytest was deferred for demo speed.
