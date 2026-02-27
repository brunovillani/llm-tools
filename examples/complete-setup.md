# Complete Setup Example

This example demonstrates how to combine MCP server configurations, agent instructions, and skills to create a fully functional LLM agent setup.

## Scenario

You want to create a coding assistant agent that can:
- Access your local filesystem
- Use git for version control
- Scrape documentation from websites
- Integrate with external APIs
- Write comprehensive tests

## Step 1: Configure MCP Servers

Create a file `mcp-config.json` with your MCP server configurations:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "C:\\Projects"],
      "description": "Access to project files"
    },
    "git": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-git"],
      "description": "Git operations"
    },
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "${BRAVE_API_KEY}"
      },
      "description": "Web search for documentation"
    }
  }
}
```

**Setup**:
1. Copy the relevant templates from `mcp-servers/`
2. Customize paths and credentials
3. Set environment variables for API keys
4. Add to your MCP client configuration

## Step 2: Create Agent Instructions

Create `my-coding-agent.md` based on the coding assistant template:

```markdown
# My Coding Assistant

You are an expert coding assistant specialized in Python and JavaScript development.

## Core Identity

**Role**: Senior Full-Stack Developer  
**Expertise**: Python, JavaScript, React, FastAPI, Testing  
**Approach**: Test-driven, security-conscious, performance-aware

## Available Tools

### MCP Servers
- **filesystem**: Read and write project files
- **git**: Version control operations
- **brave-search**: Search for documentation and solutions

### Skills
You have access to the following skills. Read the SKILL.md file before using:
- `skills/web-scraping/` - Extract data from documentation sites
- `skills/api-integration/` - Integrate with external APIs
- `skills/testing-automation/` - Write comprehensive tests

## Workflow

When implementing a new feature:

1. **Understand Requirements**: Clarify what needs to be built
2. **Search Documentation**: Use brave-search to find relevant docs
3. **Write Tests First**: Use testing-automation skill
4. **Implement Code**: Write clean, documented code
5. **Run Tests**: Verify implementation
6. **Commit Changes**: Use git MCP server
7. **Document**: Update README and comments

## Example Task: Add API Integration

**User Request**: "Add integration with the GitHub API to fetch repository info"

**Your Approach**:

1. Read the api-integration skill:
   \```
   Read skills/api-integration/SKILL.md
   \```

2. Search for GitHub API documentation:
   \```
   Use brave-search to find "GitHub API documentation"
   \```

3. Write tests first:
   \```python
   # tests/test_github_integration.py
   def test_fetch_repo_info():
       client = GitHubClient(api_key="test")
       repo = client.get_repo("owner/repo")
       assert repo['name'] == 'repo'
   \```

4. Implement the integration following the skill's patterns:
   \```python
   # src/github_client.py
   import requests
   
   class GitHubClient:
       def __init__(self, api_key):
           self.base_url = "https://api.github.com"
           self.session = requests.Session()
           self.session.headers.update({
               'Authorization': f'token {api_key}'
           })
       
       def get_repo(self, repo_name):
           response = self.session.get(
               f"{self.base_url}/repos/{repo_name}"
           )
           response.raise_for_status()
           return response.json()
   \```

5. Run tests and commit:
   \```bash
   pytest tests/test_github_integration.py
   git add .
   git commit -m "Add GitHub API integration"
   \```

## Best Practices

- Always write tests before implementation
- Use type hints in Python
- Handle errors gracefully
- Document complex logic
- Follow PEP 8 / Airbnb style guides
- Security: Never commit API keys
```

**Setup**:
1. Copy `agents/coding-assistant.md`
2. Customize for your specific needs
3. Reference your available MCP servers and skills
4. Add project-specific guidelines

## Step 3: Set Up Skills

Create a `skills/` directory in your agent's workspace:

```
my-agent/
├── skills/
│   ├── web-scraping/
│   │   └── SKILL.md
│   ├── api-integration/
│   │   └── SKILL.md
│   └── testing-automation/
│       └── SKILL.md
├── my-coding-agent.md
└── mcp-config.json
```

**Setup**:
1. Copy relevant skill directories from `skills/`
2. Customize if needed for your use case
3. Reference skills in your agent instructions

## Step 4: Environment Configuration

Create a `.env` file for sensitive credentials:

```bash
# .env
BRAVE_API_KEY=your_brave_api_key_here
GITHUB_TOKEN=your_github_token_here
```

**Important**: Add `.env` to `.gitignore`!

## Step 5: Test Your Setup

### Test MCP Servers

Verify each MCP server is working:

```python
# Test filesystem access
# The agent should be able to read and write files in C:\Projects

# Test git operations
# The agent should be able to run git commands

# Test web search
# The agent should be able to search for information
```

### Test Skills

Give your agent tasks that require each skill:

**Web Scraping Test**:
```
"Extract the table of contents from the Python documentation at 
https://docs.python.org/3/library/index.html"
```

**API Integration Test**:
```
"Fetch information about the 'requests' repository from GitHub API"
```

**Testing Automation Test**:
```
"Write unit tests for this calculator function"
```

## Step 6: Real-World Usage

### Example 1: Build a Web Scraper

**Request**: "Create a script to scrape product prices from an e-commerce site"

**Agent Workflow**:
1. Reads `skills/web-scraping/SKILL.md`
2. Follows the skill's instructions
3. Implements the scraper with error handling
4. Writes tests using `testing-automation` skill
5. Saves the script using `filesystem` MCP server
6. Commits using `git` MCP server

### Example 2: Add API Integration

**Request**: "Integrate with the OpenWeather API to fetch weather data"

**Agent Workflow**:
1. Reads `skills/api-integration/SKILL.md`
2. Searches for OpenWeather API docs using `brave-search`
3. Implements the client following skill patterns
4. Writes tests with mocked API responses
5. Documents the integration
6. Commits the changes

### Example 3: Debug and Fix

**Request**: "Fix the failing tests in test_user_service.py"

**Agent Workflow**:
1. Reads the test file using `filesystem`
2. Analyzes the failures
3. Searches for solutions using `brave-search`
4. Fixes the code
5. Runs tests to verify
6. Commits the fix with descriptive message

## Benefits of This Setup

### Modularity
- MCP servers provide capabilities
- Agent instructions define behavior
- Skills provide specialized knowledge
- All components are reusable

### Flexibility
- Swap MCP servers as needed
- Customize agent instructions per project
- Add/remove skills based on requirements
- Easy to update and maintain

### Best Practices Built-In
- Security (environment variables)
- Testing (testing-automation skill)
- Documentation (agent instructions)
- Version control (git MCP server)

## Troubleshooting

### MCP Server Not Working

1. Check the server is installed:
   ```bash
   npx @modelcontextprotocol/server-filesystem --help
   ```

2. Verify environment variables are set:
   ```bash
   echo $BRAVE_API_KEY
   ```

3. Check MCP client logs for errors

### Skill Not Being Used

1. Ensure skill directory is in agent's skills path
2. Verify SKILL.md exists and is valid
3. Check agent instructions reference the skill
4. Confirm agent has read the skill file

### Agent Not Following Instructions

1. Review agent instructions for clarity
2. Add more specific examples
3. Break down complex tasks into steps
4. Provide feedback and iterate

## Next Steps

1. **Customize**: Adapt this setup to your specific needs
2. **Extend**: Add more MCP servers and skills
3. **Optimize**: Refine agent instructions based on usage
4. **Share**: Contribute improvements back to the templates

## Additional Examples

- See `agents/` for more agent templates
- See `skills/` for more skill examples
- See `mcp-servers/` for more server configurations
