---
name: agent-name
description: A clear description of the agent's purpose and when it should be used.
tools: Read, Write, Edit, Bash, Glob, Grep # Comma-separated list of allowed tools
model: sonnet # Optional: specify the model to use (e.g., sonnet, opus)
---

# [Agent Name]
[Brief description of the agent's role and purpose, expertise, and approach to execute tasks]

## Expertise
*   [Skill 1: e.g., Expert in Python coding style]
*   [Skill 2: e.g., Code review for best practices]
*   [Skill 3: e.g., Explaining technical concepts clearly]

## Communication Style
[Specify how this agent should interact with the main agent or other subagents, including expected input/output formats.]

- **[Trait 1]**: [Description]
- **[Trait 2]**: [Description]

## Development Workflow
[Define the structured implementation phases or specific steps the agent should follow when performing its task.]

### Instructions
[Define details about specific instructions and the steps to execute them.]

#### When [Task description]

1. **[Step 1]**: [Description]
2. **[Step 2]**: [Description]

## Quality Checklist
[List criteria for a successful output, e.g., "Always ensure unit tests pass", "Check for documentation links".]

## Best Practices (Do's and Don'ts)
- ✅ **Do**: [Description]
- ❌ **Dont**: [Description]

## Response Format
**For [Task Type 1]**:
```markdown
## [Section 1]
[Content]
## [Section 2]
[Content]
```

## Example Interactions

### Example 1: [Scenario Name]

**User**: "[Example user request]"

**Response**:
[Example response showing the agent's approach and style]

## Skills Integration

When appropriate, leverage specialized skills:

- **[Skill 1]**: Use the `skill-name` skill for [purpose]
- **[Skill 2]**: Use the `skill-name` skill for [purpose]
- **[Skill 3]**: Use the `skill-name` skill for [purpose]

## Constraints & Limitations

- **[Constraint 1]**: [Description]
- **[Constraint 2]**: [Description]
- **[Constraint 3]**: [Description]
