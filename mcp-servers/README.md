# MCP Server Configurations

This directory contains templates for configuring various MCP (Model Context Protocol) servers. These configurations can be used with MCP-compatible clients to extend your LLM agent's capabilities.

## 📋 Available Templates

### [filesystem.json](filesystem.json)
Provides file system access capabilities.
- **Use Case**: Reading, writing, and managing files
- **Capabilities**: File operations, directory traversal, search
- **Security**: Configure allowed directories to restrict access

### [git.json](git.json)
Enables git repository operations.
- **Use Case**: Version control, repository analysis, commit history
- **Capabilities**: Git commands, diff viewing, branch management
- **Security**: Limit to specific repositories

### [database.json](database.json)
Connects to databases for queries and management.
- **Use Case**: Data retrieval, schema inspection, queries
- **Capabilities**: SQL execution, schema browsing
- **Security**: Use read-only credentials when possible

### [web-search.json](web-search.json)
Provides internet search capabilities.
- **Use Case**: Research, fact-checking, current information
- **Capabilities**: Web search, result parsing
- **Security**: API key required (not included in template)

### [custom-template.json](custom-template.json)
Blank template for creating your own MCP server configurations.

## 🔧 Configuration Schema

Each MCP server configuration follows this structure:

```json
{
  "mcpServers": {
    "server-name": {
      "command": "executable-or-script",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR": "value"
      }
    }
  }
}
```

### Fields

- **server-name**: Unique identifier for the server
- **command**: Executable command or script to run
- **args**: Array of command-line arguments
- **env**: Environment variables (optional)

## 🚀 Usage

### 1. Choose a Template
Select the template that matches your needs or start with `custom-template.json`.

### 2. Customize Configuration
Edit the template with your specific settings:
- Update paths to match your system
- Add required API keys to environment variables
- Configure access restrictions

### 3. Add to MCP Client
Copy the configuration to your MCP client's config file (typically `mcp.json` or similar).

### 4. Test Connection
Verify the server starts correctly and provides expected capabilities.

## 🔒 Security Best Practices

1. **Credentials**: Never commit API keys or passwords
   - Use environment variables
   - Reference external credential files
   - Add credential files to `.gitignore`

2. **Access Control**: Limit server permissions
   - Restrict filesystem access to specific directories
   - Use read-only database credentials when possible
   - Whitelist allowed operations

3. **Validation**: Test configurations in safe environments first
   - Use test databases or sandboxed filesystems
   - Verify command execution is as expected
   - Monitor for unexpected behavior

## 💡 Examples

### Combining Multiple Servers

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "C:\\Projects"]
    },
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"]
    }
  }
}
```

### Using Environment Variables

```json
{
  "mcpServers": {
    "database": {
      "command": "mcp-server-postgres",
      "env": {
        "DB_HOST": "localhost",
        "DB_PORT": "5432",
        "DB_NAME": "mydb",
        "DB_USER": "${DB_USER}",
        "DB_PASSWORD": "${DB_PASSWORD}"
      }
    }
  }
}
```

## 🐛 Troubleshooting

### Server Won't Start
- Verify the command is installed and in PATH
- Check all required arguments are provided
- Ensure environment variables are set

### Permission Errors
- Check file/directory permissions
- Verify user has necessary access rights
- Review security restrictions in configuration

### Connection Issues
- Confirm network connectivity (for remote services)
- Verify API keys are valid
- Check firewall settings

## 📚 Additional Resources

- [MCP Protocol Documentation](https://modelcontextprotocol.io/)
- [Available MCP Servers](https://github.com/modelcontextprotocol/servers)
- [Creating Custom MCP Servers](https://modelcontextprotocol.io/docs/building-servers)
