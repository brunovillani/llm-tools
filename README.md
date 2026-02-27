# LLM Tools

A comprehensive project for managing MCP (Model Context Protocol) server configurations, LLM agent instruction templates, and reusable skills for AI assistants.

## 📁 Project Structure

```
llm-tools/
├── mcp-servers/          # MCP server configuration templates
├── agents/               # Agent instruction templates
├── skills/               # Reusable skill templates
├── examples/             # Complete usage examples
└── README.md            # This file
```

## 🚀 Quick Start

### Using MCP Server Configurations

1. Navigate to `mcp-servers/`
2. Copy a template (e.g., `filesystem.json`)
3. Customize the configuration for your needs
4. Add to your MCP client configuration

### Using Agent Templates

1. Browse `agents/` for available templates
2. Copy a template that matches your use case
3. Customize the instructions for your specific needs
4. Use with your LLM agent system

### Using Skills

1. Explore `skills/` for available skills
2. Copy the skill directory to your agent's skills folder
3. Reference the skill in your agent configuration
4. The agent will automatically load and use the skill

## 📚 Components

### MCP Server Configurations

Pre-configured templates for common MCP servers:
- **Filesystem**: File system access and manipulation
- **Git**: Repository operations and version control
- **Database**: Database queries and management
- **Web Search**: Internet search capabilities
- **Custom Template**: Blank template for your own servers

[Learn more →](mcp-servers/README.md)

### Agent Instructions

Ready-to-use instruction templates for different agent types:
- **Coding Assistant**: Code generation, debugging, refactoring
- **Researcher**: Information gathering and synthesis
- **Debugger**: Issue identification and resolution
- **Code Reviewer**: Quality, security, and best practices
- **Base Template**: Starting point for custom agents

[Learn more →](agents/README.md)

### Skills Library

Modular, reusable skills for common tasks:
- **Web Scraping**: Extract data from websites
- **API Integration**: Connect to external services
- **Testing Automation**: Generate and run tests
- **Template**: Create your own skills

[Learn more →](skills/README.md)

## 💡 Examples

Check out the `examples/` directory for complete, end-to-end examples showing how to combine MCP configurations, agent instructions, and skills.

## 🛠️ Creating Custom Components

### Custom MCP Server Configuration

1. Copy `mcp-servers/custom-template.json`
2. Define your server's command and arguments
3. Add any required environment variables
4. Test with your MCP client

### Custom Agent Instructions

1. Copy `agents/base-template.md`
2. Define your agent's role and capabilities
3. Add specific instructions and guidelines
4. Include examples and best practices

### Custom Skill

1. Copy `skills/_template/` directory
2. Edit `SKILL.md` with your skill's instructions
3. Add any helper scripts or resources
4. Document usage examples

## 📖 Best Practices

- **Version Control**: Keep your configurations in git
- **Documentation**: Document customizations and use cases
- **Modularity**: Keep skills focused on single responsibilities
- **Testing**: Test configurations before deploying
- **Security**: Never commit sensitive credentials

## 🤝 Contributing

Feel free to add your own templates and skills to this repository:
1. Follow the existing structure and format
2. Include clear documentation
3. Add examples where helpful
4. Test thoroughly before committing

## 📄 License

This project is provided as-is for personal and commercial use.
