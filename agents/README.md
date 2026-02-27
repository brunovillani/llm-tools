# Agent Instruction Templates

This directory contains instruction templates for different types of LLM agents. Each template provides comprehensive guidelines, best practices, and behavioral patterns for specific agent roles.

## 📋 Available Templates

### [coding-assistant.md](coding-assistant.md)
A comprehensive coding assistant specialized in software development.
- **Focus**: Code generation, debugging, refactoring, best practices
- **Use Cases**: Full-stack development, code review, architecture design
- **Strengths**: Multi-language support, testing, documentation

### [researcher.md](researcher.md)
A research-focused agent for information gathering and analysis.
- **Focus**: Information synthesis, fact-checking, comprehensive research
- **Use Cases**: Literature review, market research, technical investigation
- **Strengths**: Source verification, structured reporting, deep analysis

### [debugger.md](debugger.md)
A specialized debugging agent for identifying and fixing issues.
- **Focus**: Root cause analysis, systematic debugging, issue resolution
- **Use Cases**: Bug fixing, performance optimization, error diagnosis
- **Strengths**: Methodical approach, hypothesis testing, verification

### [code-reviewer.md](code-reviewer.md)
A code review specialist focused on quality and best practices.
- **Focus**: Code quality, security, performance, maintainability
- **Use Cases**: Pull request reviews, security audits, refactoring suggestions
- **Strengths**: Pattern recognition, standards enforcement, constructive feedback

### [base-template.md](base-template.md)
A blank template for creating custom agent instructions.

## 🎯 How to Use

### 1. Select a Template
Choose the template that best matches your use case or start with `base-template.md` for a custom agent.

### 2. Customize Instructions
Edit the template to fit your specific needs:
- Adjust the agent's personality and tone
- Add domain-specific knowledge or constraints
- Include project-specific guidelines
- Define custom workflows or processes

### 3. Deploy to Your Agent
Copy the customized instructions to your LLM agent system's configuration.

### 4. Iterate and Improve
Refine the instructions based on agent performance and user feedback.

## 📝 Template Structure

Each template follows a consistent structure:

```markdown
# Agent Identity
- Role definition
- Core capabilities
- Behavioral guidelines

# Instructions
- Specific task guidelines
- Best practices
- Do's and don'ts

# Communication Style
- Tone and personality
- Response formatting
- User interaction patterns

# Workflows
- Common task patterns
- Step-by-step processes
- Decision frameworks

# Examples
- Sample interactions
- Use case demonstrations
```

## 💡 Best Practices

### Writing Effective Instructions

1. **Be Specific**: Clear, concrete instructions work better than vague guidelines
2. **Use Examples**: Show desired behavior with concrete examples
3. **Set Boundaries**: Define what the agent should and shouldn't do
4. **Prioritize**: Order instructions by importance
5. **Test Thoroughly**: Validate with real-world scenarios

### Customization Tips

- **Domain Knowledge**: Add industry-specific terminology and practices
- **Constraints**: Define technical limitations or requirements
- **Personality**: Adjust tone to match your use case (formal, casual, technical)
- **Context**: Include project-specific information when relevant
- **Skills**: Reference specific skills the agent should use

### Common Pitfalls to Avoid

- ❌ Overly complex or contradictory instructions
- ❌ Vague or ambiguous guidelines
- ❌ Too many priorities (everything is important = nothing is important)
- ❌ Lack of examples or concrete guidance
- ❌ Ignoring edge cases and error handling

## 🔄 Combining Templates

You can combine elements from multiple templates for hybrid agents:

```markdown
# Multi-Role Agent Example

## Primary Role: Coding Assistant
[Include coding-assistant core instructions]

## Secondary Role: Code Reviewer
[Include code-reviewer quality checks]

## Research Capability
[Include researcher fact-checking guidelines]
```

## 🛠️ Advanced Techniques

### Conditional Behavior
Define different behaviors based on context:
```markdown
When working on production code:
- Prioritize stability and testing
- Require comprehensive documentation

When prototyping:
- Focus on rapid iteration
- Allow experimental approaches
```

### Skill Integration
Reference specific skills for complex tasks:
```markdown
For API integration tasks:
- Use the api-integration skill
- Follow the authentication patterns
- Implement proper error handling
```

### Progressive Disclosure
Structure instructions from general to specific:
```markdown
1. High-level principles (always apply)
2. Domain-specific guidelines (when relevant)
3. Task-specific instructions (for particular scenarios)
```

## 📚 Additional Resources

- [Prompt Engineering Guide](https://www.promptingguide.ai/)
- [LLM Best Practices](https://platform.openai.com/docs/guides/prompt-engineering)
- [Agent Design Patterns](https://www.anthropic.com/index/claude-prompt-engineering)

## 🤝 Contributing

When adding new templates:
1. Follow the established structure
2. Include comprehensive examples
3. Document the agent's intended use cases
4. Test with various scenarios
5. Add to this README with description
