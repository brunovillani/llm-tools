# Skills Instructions Guide

## Core required fields for a skill information file

**name** — short, unique identifier for the skill.

**description** — one‑sentence summary of the skill’s purpose and scope.

**version** — semantic version or timestamp for change tracking.

**author/maintainer** — contact or team responsible for the skill.

**capabilities** — explicit list of actions the skill can perform (e.g., generate-code, run-tests, open-pr).

**inputs** — required inputs and their types (files, env vars, API tokens, prompts).

**outputs** — expected outputs (PR created, patch file, test results, logs).

**preconditions** — environment or repository state required before running (branch, CI status, DB available).

**postconditions / success criteria** — concrete signals that indicate success (tests pass, PR created with checklist, lint green).

**permissions / allowed actions** — least‑privilege list of operations the skill may perform (read repo, create draft PR); include explicit forbidden actions.

**workflows** — one or more named, ordered workflows mapping goals to steps the skill will execute.

**failure handling** — what to do on errors (revert, open issue, notify owners) and escalation path.

**security & secrets policy** — how secrets are accessed, masked, and audited.

**audit metadata** — changelog, last updated timestamp, and audit trail requirements.

## Strongly recommended fields (improve safety and reliability)

**example prompts / sample runs** — concrete input → expected output pairs.

**test harness / smoke tests** — commands the skill can run to validate changes.

**coding style / lint rules** — links or short rules so generated code conforms to project standards.

**resource limits & timeouts** — max runtime, memory, and rate limits for actions.

**monitoring hooks** — metrics to emit (runs, failures, time to complete).

**rollback plan** — automated rollback steps and manual rollback checklist.

**dependencies** — external services, tools, or other skills required.

**usage examples** — short recipes for common tasks (triage ticket → open draft PR).

## Using Skills

### 1. Choose a Skill
Browse available skills and select one that matches your needs.

### 2. Copy to Your Agent
Copy the skill directory to your agent's skills folder:
```
your-agent/
  skills/
    web-scraping/
      SKILL.md
      scripts/
      examples/
```

### 3. Reference in Agent Instructions
Update your agent instructions to reference the skill:

```markdown
## Skills

When you need to extract data from websites, use the web-scraping skill.
Read the skill instructions at skills/web-scraping/SKILL.md before proceeding.
```

### 4. Agent Reads and Uses Skill
When the agent encounters a relevant task, it will:
1. Read the SKILL.md file
2. Follow the instructions
3. Use any provided scripts or resources
4. Apply best practices from the skill

