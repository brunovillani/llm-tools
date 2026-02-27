# Skills Library

This directory contains reusable skills that can be integrated into your LLM agents. Each skill is a self-contained module with instructions, examples, and optional helper scripts.

## 📋 Available Skills

### [web-scraping/](web-scraping/)
Extract data from websites systematically and reliably.
- **Use Cases**: Data collection, content monitoring, research automation
- **Capabilities**: HTML parsing, data extraction, pagination handling
- **Tools**: BeautifulSoup, Selenium, requests

### [api-integration/](api-integration/)
Connect to external APIs with proper error handling and authentication.
- **Use Cases**: Third-party integrations, data fetching, service orchestration
- **Capabilities**: REST/GraphQL APIs, authentication, rate limiting
- **Tools**: requests, httpx, API client libraries

### [testing-automation/](testing-automation/)
Generate and execute automated tests for code quality assurance.
- **Use Cases**: Test generation, test execution, quality assurance
- **Capabilities**: Unit tests, integration tests, test frameworks
- **Tools**: pytest, jest, unittest

### [_template/](_template/)
Template for creating new skills.

## 🎯 What is a Skill?

A skill is a specialized capability that an agent can use to perform specific tasks. Each skill contains:

- **SKILL.md**: Main instruction file with YAML frontmatter
- **scripts/**: Optional helper scripts and utilities
- **examples/**: Reference implementations and usage patterns
- **resources/**: Additional files, templates, or assets

## 📝 Skill Structure

### SKILL.md Format

```markdown
---
name: Skill Name
description: Brief description of what this skill does
version: 1.0.0
author: Your Name
tags: [tag1, tag2, tag3]
---

# Skill Name

[Detailed instructions for the agent on how to use this skill]

## When to Use This Skill

[Scenarios where this skill is appropriate]

## Prerequisites

[Required tools, libraries, or setup]

## Instructions

[Step-by-step guide for using the skill]

## Examples

[Concrete examples of the skill in action]

## Best Practices

[Tips and guidelines for effective use]

## Common Pitfalls

[Things to avoid or watch out for]
```

## 🚀 Using Skills

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

## 🛠️ Creating Custom Skills

### 1. Copy the Template
```bash
cp -r skills/_template skills/your-skill-name
```

### 2. Edit SKILL.md
Update the frontmatter and content:

```markdown
---
name: Your Skill Name
description: What your skill does
version: 1.0.0
author: Your Name
tags: [relevant, tags]
---

# Your Skill Name

[Your skill instructions]
```

### 3. Add Resources (Optional)
- **scripts/**: Helper scripts the agent can use
- **examples/**: Example code or usage patterns
- **resources/**: Templates, config files, etc.

### 4. Test the Skill
Verify the skill works by having an agent use it on test tasks.

### 5. Document Usage
Add your skill to this README with a brief description.

## 💡 Best Practices

### Writing Effective Skills

1. **Clear Instructions**: Be specific and actionable
2. **Include Examples**: Show concrete usage patterns
3. **Document Prerequisites**: List required tools and setup
4. **Handle Errors**: Include error handling guidance
5. **Best Practices**: Share tips and common pitfalls
6. **Keep Focused**: One skill = one capability

### Organizing Skills

- **Single Responsibility**: Each skill should do one thing well
- **Composable**: Skills should work together
- **Self-Contained**: Include all necessary resources
- **Well-Documented**: Clear instructions and examples
- **Versioned**: Track changes and compatibility

### Skill Composition

Combine multiple skills for complex tasks:

```markdown
For this task, you'll need to:
1. Use the api-integration skill to fetch data
2. Use the web-scraping skill to extract additional info
3. Use the testing-automation skill to verify results
```

## 📚 Skill Categories

### Data & Integration
- Web scraping
- API integration
- Database operations
- File processing

### Development
- Testing automation
- Code generation
- Debugging workflows
- Documentation generation

### Analysis
- Data analysis
- Performance profiling
- Security scanning
- Log analysis

### Automation
- Workflow automation
- Deployment scripts
- Monitoring setup
- Backup procedures

## 🔧 Advanced Techniques

### Parameterized Skills

Make skills configurable:

```markdown
---
name: API Integration
parameters:
  base_url: https://api.example.com
  timeout: 30
  retry_count: 3
---
```

### Skill Dependencies

Document when skills depend on others:

```markdown
## Dependencies

This skill requires:
- The `authentication` skill for API access
- The `error-handling` skill for robust error management
```

### Conditional Skill Usage

Define when to use specific skills:

```markdown
## When to Use

Use this skill when:
- ✅ You need to extract structured data from HTML
- ✅ The website doesn't provide an API
- ✅ You need to handle pagination

Don't use this skill when:
- ❌ An API is available (use api-integration instead)
- ❌ The site blocks scrapers (respect robots.txt)
- ❌ Real-time interaction is needed (use browser automation)
```

## 🤝 Contributing Skills

When contributing new skills:

1. **Follow the Template**: Use the standard structure
2. **Test Thoroughly**: Verify the skill works in practice
3. **Document Completely**: Include all necessary information
4. **Provide Examples**: Show real-world usage
5. **Update README**: Add your skill to this list

## 📖 Additional Resources

- [Skill Development Guide](../examples/skill-development-guide.md)
- [Agent Integration Patterns](../examples/agent-skill-integration.md)
- [Best Practices for Skill Design](../examples/skill-best-practices.md)

## 🐛 Troubleshooting

### Skill Not Loading
- Verify SKILL.md exists in the skill directory
- Check YAML frontmatter is valid
- Ensure skill directory is in the agent's skills path

### Skill Instructions Unclear
- Review examples in the skill
- Check for prerequisite setup steps
- Consult the skill's documentation

### Scripts Not Executing
- Verify scripts have execute permissions
- Check script dependencies are installed
- Review script paths in SKILL.md
