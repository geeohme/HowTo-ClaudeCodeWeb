# How to install and configure an MCP server

**Article ID**: KB-075
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview
The Model Context Protocol (MCP) is an open standard that connects Claude Code to external tools and data sources. This guide walks you through installing and configuring MCP servers to extend Claude Code's capabilities, enabling access to databases, file systems, APIs, monitoring tools, and more. MCP acts as a universal connector, similar to USB-C for AI applications.

## Prerequisites
- Claude Code CLI installed and configured
- Basic command line knowledge
- Node.js and npm/npx installed (for most MCP servers)
- API keys or credentials for specific services (GitHub, Stripe, etc.) if using those servers
- Understanding of JSON configuration files

## Steps

### Step 1: Understand MCP Server Types

MCP servers come in two main types:

**Local MCP Servers**: Run on your machine, require installation and maintenance, but offer more control and can access local resources.

**Remote MCP Servers**: Hosted by vendors, accessed via URL, require no installation or maintenance, and are automatically updated.

**Transport Types**:
- `stdio` - Standard input/output (most common for local servers)
- `http` - HTTP-based remote servers
- `sse` - Server-Sent Events for real-time remote servers

### Step 2: Install a Local MCP Server (CLI Method)

The basic syntax for adding an MCP server is:

```bash
claude mcp add <name> <command> [parameters...]
```

**Example - Filesystem Server**:

```bash
claude mcp add my-filesystem -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

This installs the filesystem MCP server, giving Claude Code read/write access to your Documents folder.

**Example - GitHub Server with Environment Variable**:

```bash
claude mcp add github -e GITHUB_TOKEN=your-github-token -- npx -y @modelcontextprotocol/server-github
```

The `-e` flag adds environment variables needed for authentication.

### Step 3: Configure Remote MCP Servers

Remote servers are simpler to set up - just add the vendor's URL with the appropriate transport type:

**HTTP Transport**:

```bash
# Sentry monitoring
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# Stripe payments
claude mcp add --transport http stripe https://mcp.stripe.com
```

**SSE Transport**:

```bash
# Atlassian/Linear
claude mcp add --transport sse atlassian https://mcp.atlassian.com/v1/sse
```

Remote servers handle updates, scaling, and availability automatically.

### Step 4: Choose the Right Configuration Scope

MCP servers support three configuration scopes:

**Local Scope** (default):
- Specific to one project directory
- For experimental configurations or sensitive credentials
- Not shared with team members

```bash
claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem ~/Documents
```

**Project Scope**:
- Shared with team via version control
- Project-specific tools required for collaboration
- Committed to repository

```bash
claude mcp add shared-tools -s project -- npx -y @your-team/mcp-tools
```

**User Scope**:
- Available across all your projects
- Personal utilities and development tools
- Stored in your user configuration

```bash
claude mcp add github -s user -e GITHUB_TOKEN=your-token -- npx -y @modelcontextprotocol/server-github
```

### Step 5: Verify Installation

After adding an MCP server, verify it's configured correctly:

```bash
# List all configured MCP servers
claude mcp list

# Test the connection by asking Claude to use the server
# For example, with filesystem server installed:
# "List the files in my Documents folder"
```

### Step 6: Manual Configuration (Advanced)

For advanced users, you can directly edit the configuration file at `~/.claude.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/username/Documents"],
      "env": {}
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "your-github-token"
      }
    },
    "postgres": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://localhost/mydb"],
      "env": {
        "POSTGRES_PASSWORD": "your-password"
      }
    }
  }
}
```

This method is useful for:
- Copying configurations between machines
- Backing up server configurations
- Bulk editing multiple servers
- Troubleshooting configuration issues

### Step 7: Install Recommended MCP Servers

Based on community feedback, these servers are particularly useful:

**Filesystem Server** - Direct file read/write access:
```bash
claude mcp add filesystem -- npx -y @modelcontextprotocol/server-filesystem ~/Projects
```

**GitHub Server** - Repository management:
```bash
claude mcp add github -e GITHUB_TOKEN=your-token -- npx -y @modelcontextprotocol/server-github
```

**PostgreSQL Server** - Database queries:
```bash
claude mcp add postgres -e POSTGRES_PASSWORD=your-password -- npx -y @modelcontextprotocol/server-postgres postgresql://localhost/yourdb
```

**Brave Search** - Web searching:
```bash
claude mcp add brave-search -e BRAVE_API_KEY=your-key -- npx -y @modelcontextprotocol/server-brave-search
```

## Expected Results

After successful installation and configuration:

1. **MCP servers appear in configuration**: Running `claude mcp list` shows your installed servers
2. **Claude Code can access server capabilities**: You can ask Claude to perform actions using the server (e.g., "Read the file project.json" with filesystem server)
3. **No error messages**: Servers start without errors when Claude Code initializes
4. **Server tools are available**: Claude will automatically use appropriate MCP servers when needed for tasks

Example interaction with filesystem server installed:
```
You: "What files are in my Documents folder?"
Claude: [Lists files using the filesystem MCP server]
```

## Troubleshooting

### Server Not Found or Command Fails

**Problem**: Error message "command not found" or "server failed to start"
**Solution**:
- Ensure Node.js and npx are installed: `node --version` and `npx --version`
- Check that the server package name is correct
- Verify you have internet connection for npx to download packages
- Try running the npx command directly to see detailed error messages

### Authentication Errors

**Problem**: "Authentication failed" or "Invalid token" errors
**Solution**:
- Verify your API keys/tokens are correct and not expired
- Check environment variable syntax: `-e KEY=value` with no spaces around `=`
- Ensure tokens have appropriate permissions for the service
- For GitHub tokens, verify the token has repo access scope

### Server Starts But Doesn't Respond

**Problem**: Server appears configured but Claude can't use it
**Solution**:
- Check the server logs for errors
- Verify the transport type matches the server (stdio vs http vs sse)
- Ensure file paths or URLs are absolute and correct
- Restart Claude Code to reinitialize MCP connections
- Try removing and re-adding the server

### Permission Denied Errors

**Problem**: "Permission denied" when accessing files or resources
**Solution**:
- Check that the specified paths exist and are accessible
- Verify file/directory permissions allow read/write as needed
- For filesystem servers, ensure the path is within allowed directories
- On Unix systems, check that you have execute permissions on the command

### Configuration File Errors

**Problem**: JSON syntax errors in `~/.claude.json`
**Solution**:
- Validate JSON syntax using a JSON validator tool
- Check for missing commas, brackets, or quotes
- Ensure all strings are properly quoted
- Back up the file before manual editing
- Use the CLI commands instead of manual editing when possible

## Additional Tips

- **Start with one server**: Install and test one MCP server before adding multiple to isolate issues
- **Use user scope for personal tools**: Keep personal API keys in user scope rather than project scope
- **Security first**: Never commit API keys or tokens to version control; use environment variables
- **Check official documentation**: Visit modelcontextprotocol.io for the latest server implementations
- **Explore the MCP ecosystem**: The official GitHub repository (github.com/modelcontextprotocol) has reference implementations for popular services
- **Remote over local**: When available, prefer remote MCP servers to reduce maintenance burden
- **Trust verification**: Only install MCP servers from trusted sources - Anthropic has not verified all third-party servers
- **Use specific paths**: For filesystem servers, grant access to specific directories rather than entire drives
- **Monitor resource usage**: Some MCP servers may consume system resources; monitor performance
- **Keep servers updated**: For local servers using npx with `-y` flag, servers auto-update; for pinned versions, update manually

## Related Articles
- KB-074: Understanding the Model Context Protocol (MCP)
- KB-076: Building custom MCP servers
- KB-077: MCP security best practices
- KB-078: Debugging MCP server connections

## Sources
- Connect Claude Code to tools via MCP - https://docs.anthropic.com/en/docs/claude-code/mcp (Accessed: November 16, 2025)
- Remote MCP support in Claude Code - https://www.anthropic.com/news/claude-code-remote-mcp (Accessed: November 16, 2025)
- Model Context Protocol Specification - https://modelcontextprotocol.io/specification/2025-06-18 (Accessed: November 16, 2025)
- Introducing the Model Context Protocol - https://www.anthropic.com/news/model-context-protocol (Accessed: November 16, 2025)
- Model Context Protocol GitHub Repository - https://github.com/modelcontextprotocol (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
