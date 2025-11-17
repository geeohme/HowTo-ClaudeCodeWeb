# How to connect Claude Code to external MCP servers

**Article ID**: KB-076
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-25 minutes

## Overview

The Model Context Protocol (MCP) is an open-source standard that allows Claude Code to connect to hundreds of external tools, databases, and APIs. By connecting to external MCP servers, you can extend Claude Code's capabilities with custom tools, resources, and prompts, enabling integration with services like GitHub, Notion, Sentry, databases, and many more.

## Prerequisites

- Claude Code installed and configured on your system
- Basic understanding of command-line interfaces
- API keys or authentication credentials for the external services you want to connect to
- Node.js and npm/npx installed (for many MCP servers)
- Understanding of environment variables and JSON configuration files

## Steps

### Step 1: Understand MCP Transport Types

Claude Code supports three transport types for connecting to external MCP servers:

**HTTP Transport (Recommended for Remote Services)**
- Best for cloud-based services and remote MCP servers
- Most widely supported transport type
- Examples: Notion, Intercom, Sentry, Box

**SSE (Server-Sent Events) Transport**
- For real-time event streaming connections
- Note: This transport is being deprecated in favor of HTTP
- Examples: Atlassian, Plaid

**Stdio Transport**
- Runs as local processes on your machine
- Ideal for tools requiring direct system access or custom scripts
- Examples: GitHub, Puppeteer, database connections

### Step 2: Add an MCP Server Using CLI Commands

The `claude mcp add` command is the primary way to connect to external MCP servers.

**HTTP Transport Example:**

```bash
claude mcp add --transport http notion https://mcp.notion.com/mcp
```

```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

**SSE Transport Example:**

```bash
claude mcp add --transport sse atlassian https://mcp.atlassian.com/v1/sse
```

**Stdio Transport Example:**

```bash
claude mcp add --transport stdio github -- npx -y @modelcontextprotocol/server-github
```

Note: The `--` separator is required before the command for stdio transports.

### Step 3: Configure Environment Variables and Authentication

Many MCP servers require API keys or authentication tokens. You can pass these using the `--env` flag:

```bash
claude mcp add --transport stdio clickup \
  --env CLICKUP_API_KEY=YOUR_KEY \
  --env CLICKUP_TEAM_ID=YOUR_ID \
  -- npx -y @hauptsache.net/clickup-mcp
```

For database connections:

```bash
claude mcp add --transport stdio db \
  -- npx -y @bytebase/dbhub \
  --dsn "postgresql://readonly:password@db.example.com:5432/analytics"
```

### Step 4: Create Project-Scoped Configuration with .mcp.json

For team collaboration and version control, create a `.mcp.json` file in your project's root directory:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_your_token_here"
      }
    },
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"],
      "env": {}
    }
  }
}
```

This configuration file can be committed to version control (excluding sensitive tokens).

### Step 5: Configure User-Specific Settings

For personal or machine-specific configurations, use the user-level settings file at `~/.claude/settings.local.json`:

```json
{
  "mcpServers": {
    "personal-db": {
      "command": "npx",
      "args": ["-y", "@bytebase/dbhub"],
      "env": {
        "DATABASE_URL": "your-connection-string"
      }
    }
  }
}
```

### Step 6: Manage MCP Servers with CLI Commands

**List all connected MCP servers:**

```bash
claude mcp list
```

**Test a specific MCP server connection:**

```bash
claude mcp get [server-name]
```

**Remove an MCP server:**

```bash
claude mcp remove [server-name]
```

**Add a server with specific scope:**

```bash
claude mcp add [name] --scope user
```

### Step 7: Handle OAuth Authentication

Some MCP servers require OAuth authentication. When adding these servers, Claude Code will:

1. Open a browser window for you to log in
2. Store authentication tokens securely
3. Automatically refresh tokens as needed

For services like Sentry or Figma, use the `/mcp` command to authenticate:

```bash
/mcp
```

Then follow the browser-based authentication flow.

### Step 8: Verify the Connection

After adding an MCP server, verify it's working:

1. Check the server appears in `claude mcp list`
2. Start a conversation with Claude Code
3. Ask Claude to use tools from the connected server
4. Confirm Claude can access the external service

## Expected Results

When successfully connected to external MCP servers:

- The server appears in your `claude mcp list` output
- Claude Code can access tools, resources, and prompts from the external service
- You can perform actions like querying databases, managing GitHub issues, accessing Notion pages, etc.
- Authentication tokens are securely stored and automatically refreshed
- Team members using the same `.mcp.json` file can access the same servers (with their own credentials)

## Troubleshooting

### Common Issue 1: OAuth Authentication Succeeds but Reconnection Fails

**Problem**: After OAuth authentication, you see "Authentication successful, but server reconnection failed. You may need to manually restart Claude Code."
**Solution**: Restart Claude Code completely. The authentication tokens are stored correctly, but a restart is needed to establish the connection. This is a known behavior.

### Common Issue 2: Silent Authentication Failures

**Problem**: MCP server doesn't connect, no error messages, tools aren't available
**Solution**:
- Check if the authentication browser window opened during setup
- Verify your API keys/tokens are correct and not expired
- Re-authenticate using the OAuth flow
- Check project approvals and access levels for the external service

### Common Issue 3: MCP Server Fails in SSH Remote Sessions

**Problem**: OAuth authentication doesn't work when using Claude Code over SSH
**Solution**: MCP servers requiring browser-based OAuth cannot work in remote SSH sessions because there's no way to access a browser. Either:
- Use pre-authenticated API tokens instead of OAuth
- Configure the MCP server on your local machine
- Use port forwarding to access the browser locally

### Common Issue 4: WSL Configuration Issues (Windows)

**Problem**: MCP server works in Claude Desktop but fails in Claude Code CLI on WSL
**Solution**: Some MCP servers only bind to 127.0.0.1, which doesn't work with WSL's virtual interface. Configure the server to listen on all interfaces or use the Windows version of Claude Code.

### Common Issue 5: Environment Variables Not Loading

**Problem**: MCP server can't access API keys or tokens
**Solution**:
- Ensure environment variables are defined in the configuration
- Use proper JSON escaping for special characters
- For sensitive tokens, never commit them to `.mcp.json` - use user-specific configuration instead
- Verify environment variable names match exactly what the server expects

## Additional Tips

- **Security First**: Only connect to MCP servers you trust. Anthropic has not verified the security of all third-party MCP servers. Review the source code of community servers before connecting.
- **Start Simple**: Connect one MCP server at a time to isolate any configuration issues
- **Use HTTP When Available**: HTTP transport is the recommended option for remote services and is more widely supported than SSE
- **Separate Sensitive Data**: Keep API keys and tokens in user-specific configuration files (`~/.claude/settings.local.json`) rather than project-scoped `.mcp.json` files
- **Version Control**: Commit `.mcp.json` to version control for team collaboration, but use `.gitignore` for any files containing secrets
- **Plugin-Relative Paths**: Use `${CLAUDE_PLUGIN_ROOT}` for plugin-relative paths in your configurations
- **Test Connections**: After adding a server, immediately test it with `claude mcp get [server-name]` to catch configuration errors early
- **Monitor Logs**: Check Claude Code logs if servers fail to connect for detailed error messages
- **Keep Updated**: MCP is an evolving standard - check official documentation for the latest transport types and features

## Related Articles

- KB-001: Introduction to Claude Code Web
- KB-075: Advanced configuration and customization (if available)
- KB-077: Building custom MCP servers (if available)

## Sources

- Claude Code MCP Documentation - https://docs.claude.com/en/docs/claude-code/mcp (Accessed: November 16, 2025)
- FastMCP Claude Code Integration - https://gofastmcp.com/integrations/claude-code (Accessed: November 16, 2025)
- InfoQ: Claude Code Gains Support for Remote MCP Servers - https://www.infoq.com/news/2025/06/anthropic-claude-remote-mcp/ (Accessed: November 16, 2025)
- Anthropic Model Context Protocol Announcement - https://www.anthropic.com/news/model-context-protocol (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code MCP server configuration examples 2025"

---
*This article is part of the Claude Code Web knowledge base*
