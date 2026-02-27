# Anthropic Skills System - Research Summary

Based on research of the [Anthropic Skills Repository](https://github.com/anthropics/skills)

## What is a Skill?

Skills are **folders of instructions, scripts, and resources** that Claude loads dynamically to improve performance on specialized tasks. They teach Claude how to complete specific tasks in a repeatable way.

## Skill Metadata Structure

### Required Metadata Fields (YAML Frontmatter)

Every skill must have a `SKILL.md` file with YAML frontmatter containing:

```yaml
---
name: skill-name
description: A clear description of what the skill does and when to use it
---
```

**Field Details:**

1. **`name`** (required)
   - Human-friendly name for the skill
   - Maximum 64 characters
   - Lowercase with hyphens for spaces
   - Example: `brand-guidelines`, `mcp-builder`, `webapp-testing`

2. **`description`** (required)
   - Clear description of what the skill does and when to use it
   - **CRITICAL**: Claude uses this to determine when to invoke the skill
   - Maximum 200 characters
   - Should be comprehensive about the skill's purpose
   - Example: `"Guide for creating high-quality MCP servers that enable LLMs to interact with external services through well-designed tools. Use when building MCP servers to integrate external APIs or services."`

### Optional Metadata Fields

```yaml
---
name: skill-name
description: Description here
license: Complete terms in LICENSE.txt
dependencies: python>=3.8, pandas>=1.5.0
compatibility: Requires specific environment or product
---
```

**Optional Fields:**

- **`license`**: License information (e.g., "Complete terms in LICENSE.txt")
- **`dependencies`**: Software packages required (e.g., "python>=3.8, pandas>=1.5.0")
- **`compatibility`**: Environment requirements (rarely needed)

## How Claude Loads Skills: Progressive Disclosure

Skills use a **three-level progressive disclosure system** to manage context efficiently:

### Level 1: Metadata Only (~100 words)
**Always in context**

- Claude has access to the `name` and `description` from ALL available skills
- Uses this to determine which skills are relevant to the current task
- No other content is loaded at this stage

### Level 2: SKILL.md Body (<5k words)
**Loaded when skill triggers**

- After Claude determines a skill is relevant (based on metadata), it loads the SKILL.md body
- Contains instructions, workflows, and guidance
- Should be kept under 500 lines to minimize context bloat
- Only essential information should be here

### Level 3: Bundled Resources (Unlimited)
**Loaded as needed**

- Scripts can be executed without reading into context
- Reference files loaded only when Claude determines they're needed
- Assets used in output without loading into context

## Skill Directory Structure

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter metadata (required)
│   │   ├── name: (required)
│   │   ├── description: (required)
│   │   └── optional fields
│   └── Markdown instructions (required)
└── Bundled Resources (optional)
    ├── scripts/          - Executable code (Python/Bash/etc.)
    ├── references/       - Documentation loaded as needed
    └── assets/           - Files used in output (templates, icons, etc.)
```

### Bundled Resources Explained

#### `scripts/` Directory
- **Purpose**: Executable code for deterministic reliability
- **When to use**: Tasks that are repeatedly rewritten or need deterministic behavior
- **Benefits**: Token efficient, can be executed without loading into context
- **Example**: `scripts/rotate_pdf.py`, `scripts/with_server.py`

#### `references/` Directory
- **Purpose**: Documentation loaded into context as needed
- **When to use**: For detailed information Claude should reference while working
- **Benefits**: Keeps SKILL.md lean, loaded only when needed
- **Examples**: 
  - `references/finance.md` - Financial schemas
  - `references/api_docs.md` - API specifications
  - `references/policies.md` - Company policies
- **Best practice**: For large files (>10k words), include grep search patterns in SKILL.md

#### `assets/` Directory
- **Purpose**: Files used in output, not loaded into context
- **When to use**: Files that will be used in final output
- **Benefits**: Separates output resources from documentation
- **Examples**:
  - `assets/logo.png` - Brand assets
  - `assets/template.pptx` - PowerPoint templates
  - `assets/frontend-template/` - HTML/React boilerplate

## Real-World Examples from Anthropic

### Example 1: Brand Guidelines Skill

```yaml
---
name: brand-guidelines
description: Applies Anthropic's official brand colors and typography to any sort of artifact that may benefit from having Anthropic's look-and-feel. Use it when brand colors or style guidelines, visual formatting, or company design standards apply.
license: Complete terms in LICENSE.txt
---
```

**Key Insight**: Description clearly states WHEN to use it ("when brand colors or style guidelines... apply")

### Example 2: MCP Builder Skill

```yaml
---
name: mcp-builder
description: Guide for creating high-quality MCP (Model Context Protocol) servers that enable LLMs to interact with external services through well-designed tools. Use when building MCP servers to integrate external APIs or services, whether in Python (FastMCP) or Node/TypeScript (MCP SDK).
license: Complete terms in LICENSE.txt
---
```

**Key Insight**: Description includes what it does AND when to use it, plus supported technologies

### Example 3: Web App Testing Skill

```yaml
---
name: webapp-testing
description: Toolkit for interacting with and testing local web applications using Playwright. Supports verifying frontend functionality, debugging UI behavior, capturing browser screenshots, and viewing browser logs.
license: Complete terms in LICENSE.txt
---
```

**Key Insight**: Description lists specific capabilities to help Claude determine relevance

## How the Agent Loads Skills

### Step 1: Skill Discovery
1. User makes a request
2. Claude reviews metadata (`name` + `description`) of ALL available skills
3. Claude determines which skills are relevant based on the description

### Step 2: Skill Loading
1. If a skill is relevant, Claude loads the SKILL.md body
2. Claude reads the markdown instructions and guidance
3. Claude applies the skill's instructions to the task

### Step 3: Resource Loading (As Needed)
1. If Claude needs additional information, it loads reference files
2. If scripts are needed, they can be executed without loading into context
3. Assets are used in output without being loaded into context

## Key Design Principles

### 1. Concise Metadata is Critical
- The `description` field is the ONLY way Claude knows when to use a skill
- Must be clear, comprehensive, and specific
- Should include both WHAT the skill does and WHEN to use it

### 2. Progressive Disclosure
- Keep SKILL.md lean (under 500 lines)
- Move detailed information to reference files
- Only load what's needed when it's needed

### 3. Avoid Duplication
- Information should live in either SKILL.md or references, not both
- Prefer references for detailed information
- Keep only essential procedural instructions in SKILL.md

### 4. What NOT to Include
Do NOT create extraneous documentation:
- ❌ README.md
- ❌ INSTALLATION_GUIDE.md
- ❌ QUICK_REFERENCE.md
- ❌ CHANGELOG.md

Skills should only contain information needed for the AI agent to do the job.

## Comparison: Your Template vs. Anthropic's Approach

### Your Current Template
```yaml
---
name: Skill Template
description: Template for creating new skills
version: 1.0.0
author: Your Name
tags: [template, example]
---
```

### Anthropic's Approach
```yaml
---
name: skill-name
description: Clear description of what it does and when to use it
license: Complete terms in LICENSE.txt
---
```

### Key Differences

1. **Metadata Fields**:
   - Anthropic: Only `name` and `description` are required (sometimes `license`)
   - Your template: Includes `version`, `author`, `tags` (not used by Claude)

2. **Description Focus**:
   - Anthropic: Emphasizes WHEN to use the skill
   - Your template: Generic description

3. **Simplicity**:
   - Anthropic: Minimal metadata, focuses on what Claude needs
   - Your template: More metadata fields that aren't used for skill loading

## Recommendations for Your Project

### 1. Update Metadata Structure
Simplify to match Anthropic's approach:

```yaml
---
name: skill-name
description: What the skill does and when to use it (be specific!)
---
```

### 2. Improve Description Quality
Make descriptions more specific about WHEN to use:

**Before:**
```yaml
description: Extract data from websites systematically and reliably
```

**After:**
```yaml
description: Extract data from websites using BeautifulSoup and Selenium. Use when you need to scrape HTML content, handle pagination, or extract structured data from web pages. Not for sites with APIs (use api-integration instead).
```

### 3. Implement Progressive Disclosure
- Keep SKILL.md under 500 lines
- Move detailed examples to `references/examples.md`
- Move API docs to `references/api_reference.md`
- Keep only essential workflow in SKILL.md

### 4. Add Directory Structure
```
skills/
├── web-scraping/
│   ├── SKILL.md
│   ├── scripts/
│   │   └── scraper_helper.py
│   ├── references/
│   │   ├── beautifulsoup_guide.md
│   │   └── selenium_patterns.md
│   └── assets/
│       └── example_selectors.json
```

## Additional Resources

- [What are Skills?](https://support.claude.com/en/articles/12512176-what-are-skills)
- [Creating Custom Skills](https://support.claude.com/en/articles/12512198-creating-custom-skills)
- [Anthropic Skills Repository](https://github.com/anthropics/skills)
- [Engineering Blog: Agent Skills](https://anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

## Summary

**The key insight**: Skills use **progressive disclosure** where Claude:
1. Sees ALL skill metadata (name + description) - always in context
2. Loads SKILL.md body only when the skill is relevant
3. Loads additional resources only as needed

This means the `description` field is **critical** - it's how Claude decides whether to load your skill. Make it specific about both WHAT the skill does and WHEN to use it.
