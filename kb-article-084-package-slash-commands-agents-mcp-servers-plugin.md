# How to package slash commands, agents, and MCP servers as a plugin

**Article ID**: KB-084
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 30-45 minutes

## Overview
Claude Code plugins are shareable packages that bundle slash commands, specialized agents, MCP servers, and hooks into single installable units. Released in public beta in October 2025, plugins solve the "how do I set up the same agentic workflow for my setup" problem by letting teams standardize their agentic development configurations and distribute them with a single command.

## Prerequisites
- Claude Code installed (terminal or VS Code extension)
- Basic understanding of Git and GitHub
- Familiarity with slash commands, agents, or MCP servers
- Text editor for creating configuration files
- Understanding of JSON and Markdown syntax

## Steps

### Step 1: Create the Plugin Directory Structure

Create a new directory for your plugin following the standardized structure:

```bash
mkdir my-plugin
cd my-plugin
```

Set up the required directory structure:

```bash
mkdir -p .claude-plugin
mkdir -p commands
mkdir -p agents
mkdir -p skills
mkdir -p hooks
```

Your plugin directory should look like this:

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json          # Required: plugin metadata
├── commands/                 # Custom slash commands (optional)
├── agents/                   # Custom agents (optional)
├── skills/                   # Agent Skills (optional)
├── hooks/                    # Event handlers (optional)
├── .mcp.json                # MCP server definitions (optional)
└── README.md                # Plugin documentation
```

### Step 2: Create the plugin.json Manifest

Create the required `.claude-plugin/plugin.json` file with your plugin's metadata:

```json
{
  "name": "my-plugin",
  "version": "1.0.0",
  "description": "A comprehensive plugin with commands, agents, and MCP servers",
  "author": {
    "name": "Your Name",
    "email": "[email protected]",
    "url": "https://github.com/yourusername"
  },
  "homepage": "https://github.com/yourusername/my-plugin",
  "repository": "https://github.com/yourusername/my-plugin",
  "license": "MIT",
  "keywords": ["development", "automation", "productivity"]
}
```

**Required fields:**
- `name`: Plugin identifier (lowercase, hyphenated)
- `version`: Semantic version (e.g., "1.0.0")
- `description`: Brief description of the plugin's purpose

**Optional fields:**
- `author`: Object with name, email, and url
- `homepage`: Documentation URL
- `repository`: Source code repository
- `license`: License type
- `keywords`: Array of searchable keywords

### Step 3: Add Slash Commands

Create custom slash commands as Markdown files in the `commands/` directory:

```bash
cat > commands/deploy.md << 'EOF'
---
title: Deploy Application
description: Deploy the application to production
---

Deploy the current application to production environment.

Steps:
1. Run tests to ensure code quality
2. Build the application
3. Deploy to production server
4. Verify deployment was successful

Please confirm the deployment target before proceeding.
EOF
```

Commands are Markdown files with frontmatter metadata. The frontmatter should include:
- `title`: Display name of the command
- `description`: Brief description of what the command does

### Step 4: Add Specialized Agents

Create agent definitions as Markdown files in the `agents/` directory:

```bash
cat > agents/security-reviewer.md << 'EOF'
---
name: security-reviewer
description: Reviews code for security vulnerabilities
---

You are a security-focused code reviewer. When invoked, you should:

1. Scan the codebase for common security vulnerabilities:
   - SQL injection risks
   - XSS vulnerabilities
   - Authentication/authorization issues
   - Exposed secrets or credentials
   - Insecure dependencies

2. Check for security best practices:
   - Input validation
   - Output encoding
   - Secure session management
   - HTTPS usage
   - CORS configuration

3. Provide detailed findings with:
   - Severity level (Critical, High, Medium, Low)
   - Location in code
   - Explanation of the risk
   - Recommended remediation

Focus on actionable security improvements without making changes to code.
EOF
```

Agents should clearly define their purpose, capabilities, and behavior patterns.

### Step 5: Add Agent Skills (Optional)

Skills are model-invoked capabilities that Claude autonomously decides when to use. Create skills following the 2025 Skills schema:

```bash
mkdir -p skills/code-analyzer
cat > skills/code-analyzer/SKILL.md << 'EOF'
---
name: code-analyzer
description: Analyzes code quality and suggests improvements
allowed-tools: Read, Grep, Glob, Bash
version: 1.0.0
---

# Code Analyzer Skill

This skill analyzes code for quality issues and suggests improvements.

## Capabilities
- Identifies code smells and anti-patterns
- Suggests refactoring opportunities
- Checks for best practices compliance

## Tool Usage
- **Read**: For reading source files
- **Grep**: For searching code patterns
- **Glob**: For finding files by pattern
- **Bash**: For running analysis tools

## Activation
This skill activates when the user asks about code quality, refactoring, or best practices.
EOF
```

**2025 Skills Schema Features:**
- `allowed-tools`: Declares which tools the skill can use
- `version`: Tracks skill version
- Enhanced activation triggers based on context

### Step 6: Add MCP Server Configurations

Create a `.mcp.json` file at the plugin root to define MCP servers:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/directory"],
      "env": {}
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**Configuration structure:**
- **mcpServers**: Object containing server definitions
- Each server has:
  - `command`: Executable to run (e.g., "npx", "python")
  - `args`: Array of command arguments
  - `env`: Environment variables (can reference system variables with `${VAR_NAME}`)

MCP servers in plugins start automatically when the plugin is enabled.

### Step 7: Add Hooks (Optional)

Create workflow hooks in `hooks/hooks.json`:

```json
{
  "SessionStart": [
    {
      "hooks": [
        {
          "type": "command",
          "command": "./hooks/session-start.sh"
        }
      ]
    }
  ],
  "UserPromptSubmit": [
    {
      "matchers": [
        {
          "type": "regex",
          "pattern": "deploy"
        }
      ],
      "hooks": [
        {
          "type": "command",
          "command": "./hooks/pre-deploy-check.sh"
        }
      ]
    }
  ]
}
```

Create the corresponding hook scripts:

```bash
mkdir -p hooks
cat > hooks/session-start.sh << 'EOF'
#!/bin/bash
# This hook runs when a Claude Code session starts

echo "Loading development context..."
echo '{"hookSpecificOutput": {"additionalContext": "Environment: Production. Remember to run tests before deployment."}}'
EOF

chmod +x hooks/session-start.sh
```

**Available hook events:**
- `SessionStart`: Fires when Claude Code starts or resumes
- `UserPromptSubmit`: Fires when user submits a prompt
- `TurnStart`: Fires before Claude processes a turn
- `TurnEnd`: Fires after Claude completes a turn

### Step 8: Create Plugin Documentation

Create a comprehensive `README.md` file:

```markdown
# My Plugin

A comprehensive Claude Code plugin providing deployment commands, security review agents, and tool integrations.

## Features

- **Slash Commands**
  - `/deploy`: Deploy application to production

- **Agents**
  - `security-reviewer`: Automated security vulnerability scanning

- **Skills**
  - `code-analyzer`: Code quality analysis and suggestions

- **MCP Servers**
  - Filesystem access for project files
  - GitHub integration for repository operations

## Installation

### Via Marketplace

```bash
/plugin marketplace add yourusername/my-plugin-marketplace
/plugin install my-plugin
```

### Local Development

```bash
/plugin marketplace add file:///path/to/dev-marketplace
/plugin install my-plugin@local
```

## Usage

After installation, use `/help` to see all available commands.

## Requirements

- Claude Code 2.0.13 or higher
- GitHub token for GitHub MCP server (set GITHUB_TOKEN environment variable)

## License

MIT
```

### Step 9: Test the Plugin Locally

Create a local marketplace for testing:

```bash
mkdir -p ../dev-marketplace/.claude-plugin
```

Create a `marketplace.json` file:

```json
{
  "name": "Development Marketplace",
  "description": "Local marketplace for plugin development",
  "plugins": [
    {
      "name": "my-plugin",
      "source": "../my-plugin",
      "version": "1.0.0",
      "description": "A comprehensive plugin with commands, agents, and MCP servers",
      "category": "development",
      "tags": ["automation", "security", "deployment"],
      "strict": true
    }
  ]
}
```

**Marketplace.json plugin entry fields:**
- `name`: Plugin identifier (required)
- `source`: Relative or absolute path to plugin directory
- `version`: Plugin version
- `description`: Plugin description
- `category`: Plugin category for organization
- `tags`: Searchable tags
- `strict`: When true (default), requires plugin.json; when false, marketplace entry serves as complete manifest

Add the local marketplace to Claude Code:

```bash
/plugin marketplace add file:///absolute/path/to/dev-marketplace
```

Install your plugin:

```bash
/plugin install my-plugin@local
```

Verify installation:

```bash
/help
```

You should see your custom commands listed.

### Step 10: Publish to GitHub

Initialize a Git repository for your plugin:

```bash
git init
git add .
git commit -m "Initial plugin release"
```

Create a GitHub repository and push:

```bash
git remote add origin https://github.com/yourusername/my-plugin.git
git branch -M main
git push -u origin main
```

Create a marketplace repository:

```bash
mkdir my-plugin-marketplace
cd my-plugin-marketplace
mkdir .claude-plugin
```

Create the marketplace.json:

```json
{
  "name": "My Plugin Marketplace",
  "description": "Custom Claude Code plugins for development automation",
  "version": "1.0.0",
  "plugins": [
    {
      "name": "my-plugin",
      "source": "github:yourusername/my-plugin",
      "version": "1.0.0",
      "description": "A comprehensive plugin with commands, agents, and MCP servers",
      "category": "development",
      "tags": ["automation", "security", "deployment"],
      "strict": true
    }
  ]
}
```

**Source field formats:**
- `github:username/repo`: GitHub repository
- `github:username/repo#branch`: Specific branch
- `github:username/repo#v1.0.0`: Specific tag
- `file:///path/to/plugin`: Local filesystem (for development)
- `https://example.com/plugin.zip`: Direct URL to plugin archive

Push the marketplace repository:

```bash
git init
git add .
git commit -m "Initial marketplace release"
git remote add origin https://github.com/yourusername/my-plugin-marketplace.git
git branch -M main
git push -u origin main
```

### Step 11: Distribute Your Plugin

Users can now add your marketplace and install your plugin:

```bash
/plugin marketplace add yourusername/my-plugin-marketplace
/plugin install my-plugin
```

To update your plugin:

1. Make changes to your plugin repository
2. Update the version in `plugin.json`
3. Commit and push changes
4. Update the version in `marketplace.json`
5. Commit and push marketplace changes

Users can update with:

```bash
/plugin update my-plugin
```

## Expected Results

After successfully packaging and installing your plugin:

1. **Custom commands appear in `/help`**: Your slash commands are listed and can be invoked with `/command-name`

2. **Agents are available**: Specialized agents can be invoked explicitly or Claude will suggest them when appropriate

3. **MCP servers are active**: Tools and resources from MCP servers are available to Claude during conversations

4. **Hooks execute automatically**: Workflow hooks trigger at specified events without manual intervention

5. **Plugin shows in marketplace**: Using `/plugin list` shows your installed plugin with version and description

6. **Seamless updates**: Running `/plugin update` fetches the latest version from your marketplace

## Troubleshooting

### Common Issue 1: Plugin Not Found After Installation

**Problem**: After running `/plugin install my-plugin`, the plugin doesn't appear in `/plugin list` or commands aren't available.

**Solution**:
- Verify the marketplace was added correctly: `/plugin marketplace list`
- Check that plugin.json is in `.claude-plugin/` directory (not at root)
- Ensure the plugin name in marketplace.json matches the plugin.json name field
- Try removing and re-adding the marketplace
- Check Claude Code logs for error messages

### Common Issue 2: MCP Server Fails to Start

**Problem**: MCP server defined in .mcp.json doesn't start or shows errors.

**Solution**:
- Verify the command and args are correct for your MCP server
- Check that required environment variables are set (e.g., GITHUB_TOKEN)
- Test the MCP server command manually outside Claude Code
- Ensure the MCP server package is available (e.g., run `npx -y package-name` manually)
- Review server logs for specific error messages
- Restart Claude Code after configuration changes

### Common Issue 3: Hooks Not Executing

**Problem**: Hook scripts don't run when expected events occur.

**Solution**:
- Verify hooks.json is in the `hooks/` directory
- Check that hook scripts have execute permissions (`chmod +x script.sh`)
- Ensure script paths in hooks.json are relative to plugin root
- Use `./hooks/script-name.sh` format for paths
- Test scripts manually to verify they work
- Check that SessionStart hooks output JSON with `hookSpecificOutput.additionalContext` for context injection

### Common Issue 4: Marketplace.json Parsing Errors

**Problem**: Marketplace fails to load or shows JSON parsing errors.

**Solution**:
- Validate JSON syntax using a JSON validator
- Ensure all required fields are present (name, source at minimum)
- Check that source paths/URLs are correctly formatted
- Verify the marketplace.json is in `.claude-plugin/` directory
- Use absolute paths for local file sources: `file:///full/path/to/plugin`
- For GitHub sources, use format: `github:username/repo`

## Additional Tips

- **Version semantically**: Follow semantic versioning (MAJOR.MINOR.PATCH) for plugin versions. Increment MAJOR for breaking changes, MINOR for new features, PATCH for bug fixes.

- **Use strict mode during development**: Setting `"strict": true` in marketplace.json ensures your plugin.json is always validated, catching configuration errors early.

- **Test with multiple scopes**: Test your plugin with project-scoped (.mcp.json in project), plugin-scoped (in plugin), and user-scoped configurations to ensure proper precedence.

- **Document environment variables**: Clearly document any required environment variables in your README, including how to obtain API keys or tokens.

- **Provide examples**: Include example usage in your README for each command, agent, and feature to help users get started quickly.

- **Use descriptive names**: Choose clear, descriptive names for commands, agents, and skills that indicate their purpose at a glance.

- **Leverage hooks for automation**: Use SessionStart hooks to load environment-specific context, and TurnEnd hooks for validation or cleanup tasks.

- **Keep plugins focused**: Create focused plugins that solve specific problems rather than trying to bundle everything together. Users can install multiple plugins.

- **Security considerations**: Never include secrets or tokens directly in configuration files. Always use environment variable references like `${GITHUB_TOKEN}`.

- **Community sharing**: Consider submitting your plugin to community marketplaces like awesome-claude-code-plugins for broader discovery.

## Related Articles

- KB-055: How to create and use slash commands
- KB-056: How to create and use subagents
- KB-059: How to integrate MCP servers
- KB-074: How to create agent skills

## Sources

- Customize Claude Code with plugins - https://www.anthropic.com/news/claude-code-plugins (Accessed: November 16, 2025)
- Claude Code Plugins Documentation - https://docs.claude.com/en/docs/claude-code/plugins (Accessed: November 16, 2025)
- Plugin Marketplaces Documentation - https://docs.claude.com/en/docs/claude-code/plugin-marketplaces (Accessed: November 16, 2025)
- Plugins Reference - https://docs.claude.com/en/docs/claude-code/plugins-reference (Accessed: November 16, 2025)
- Claude Code GitHub Repository - https://github.com/anthropics/claude-code/tree/main/plugins (Accessed: November 16, 2025)
- Hooks Reference - https://docs.claude.com/en/docs/claude-code/hooks (Accessed: November 16, 2025)
- Connect Claude Code to tools via MCP - https://docs.claude.com/en/docs/claude-code/mcp (Accessed: November 16, 2025)
- awesome-claude-code-plugins - https://github.com/ccplugins/awesome-claude-code-plugins (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
