# Agent Instructions Guide

## Overview

An agent instruction file (also called agent config, AGENTS.md, or skill manifest depending on the ecosystem) gives an AI agent the context, goals, constraints, and actions it must follow. It should be concise, machine‑parsable where needed, and written so both humans and agents can reliably interpret intent and workflow.

## Required pieces of information

**Agent name** — a short identifier used by tooling and logs; keep it stable and unique. 

**Purpose or description (Persona)** — one or two sentences describing the agent’s goal and the problems it solves. This orients reviewers and the agent’s decision policy. 

**Primary workflows or tasks** — explicit, stepwise workflows the agent should execute (e.g., triage ticket → run tests → open draft PR). Workflows let the agent map intent to actions. 

**Inputs and context required** — list of files, environment variables, API keys, or runtime context the agent must have to operate safely and correctly. 

**Actions the agent may perform** — allowed operations (read files, run tests, create PRs, modify code) and any forbidden actions (push to main, delete infra). Explicit action scopes prevent unsafe behavior. 

**Acceptance criteria and success signals** — how the agent knows a task is complete (tests pass, PR created with checklist, CI green). These are used for automated gating. 

**Failure handling and rollback rules** — what to do on errors (open an incident, revert changes, notify owners) and escalation paths. 

**Metadata and versioning** — author, version, last updated timestamp; required for audits and reproducibility. 

## Optional but strongly recommended fields

**Example conversations or prompts** — sample inputs and expected outputs to reduce hallucination and align tone. 

**Coding conventions and lint rules** — links or short rules so generated code follows project style. 

**Test harness or smoke tests** — minimal commands the agent can run to validate a change. 

**Security constraints** — dependency policies, secrets handling rules, and vulnerability thresholds. 

## File formats and structure

Markdown with frontmatter is common for human readability plus machine parsing (e.g., AGENTS.md or SKILL.md with YAML frontmatter). 

YAML or JSON manifests are used when tooling needs strict schema validation. Include a small human‑readable description block when possible.

## Validation checklist before deploying an agent
Schema validated (YAML/JSON parses and required keys present). 

Least privilege for allowed actions; forbidden actions explicitly listed. 

Example prompts included for each workflow to reduce ambiguity. 

CI gates defined so any code changes require tests and human review before merge. 

Audit metadata present (author, version, changelog). 

## Best practices for writing agent instructions
Be explicit and prescriptive: stepwise workflows and concrete success/failure signals reduce risky behavior. 

Keep human reviewers in the loop: require draft PRs and at least one human approver for production changes. 

Provide minimal runnable context: include build/test commands and the smallest set of files the agent needs. 

Version and test the instructions: treat the instruction file like code—review, test, and version it. 

## Key Sections Explained
YAML Frontmatter: This machine-readable section configures the agent's name, description for auto-selection, and tool permissions.
Persona/Role: A detailed system prompt that sets the agent's identity and background.
Expertise/Skills: A bulleted list of the agent's core competencies.
Communication Protocol & Workflow: Specific guidelines on interaction and process flow, which are crucial for multi-agent coordination.
Quality Checklist: Specific rules or constraints to validate the agent's output and ensure high-quality results. 

## Suggested file formats and structure

- YAML or JSON manifest for machine validation and tooling integration.

- Markdown companion (e.g., SKILL.md) with the same frontmatter plus human‑readable examples and rationale.

- Schema: provide a JSON Schema or OpenAPI fragment so CI can validate new/changed skill files.

## Minimal YAML example

```yaml
name: saved-filters-skill
description: Scaffold Saved Filters feature and open draft PR with tests.
version: "1.0.0"
author: frontend-team
capabilities:
  - generate-component
  - create-migration
  - run-tests
inputs:
  files:
    - src/components/SavedFilters/**
  env:
    - DATABASE_URL
outputs:
  - draft_pr_url
preconditions:
  branch: feature/*
  ci_status: passing
success_criteria:
  - tests: all-pass
  - pr: draft-created
allowed_actions:
  - read: repo
  - create: branch
  - open: draft-pr
forbidden_actions:
  - push: main
failure_handling:
  on_error: open-issue
  notify: team-frontend
security:
  secrets_handling: masked-logs
audit:
  changelog: CHANGELOG.md
```

##Validation checklist before enabling a skill

- Schema validated and required keys present.

- Least privilege enforced in allowed/forbidden actions.

- Example runs included for each workflow.

- CI gates require tests and human review for any code changes.

- Secrets policy defined and audited.

- Rollback and failure paths documented and tested.

## Best practices for writing and maintaining skill files

- Keep workflows small and deterministic; prefer multiple focused skills over one monolith.

- Treat the skill file as code: review, test, and version it.

- Require draft PRs and at least one human approver for production changes.

- Store prompts and example runs in the file so behavior is reproducible and auditable.

- Add monitoring and metrics so you can measure impact and regressions.
