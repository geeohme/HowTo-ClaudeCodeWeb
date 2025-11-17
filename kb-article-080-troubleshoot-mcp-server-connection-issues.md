# How to troubleshoot MCP server connection issues

**Article ID**: KB-080
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-25 minutes

## Overview
MCP (Model Context Protocol) servers enable Claude Code to connect to external tools, databases, and APIs. When these connections fail, you may encounter vague error messages like "Claude was unable to connect" or "Server transport closed unexpectedly." This guide provides systematic troubleshooting steps to diagnose and resolve MCP server connection issues using log analysis, configuration validation, and runtime debugging.

## Prerequisites
- Claude Code installed and configured
- Basic understanding of command-line operations
- Familiarity with JSON configuration files
- Knowledge of your MCP server's runtime requirements (Node.js, Python, etc.)

## Steps

### Step 1: Check MCP Server Logs

Claude Code writes detailed logging information to specific log files that contain critical diagnostic information about connection failures.

**For macOS/Linux:**

```bash
# Navigate to the Claude logs directory
cd ~/Library/Logs/Claude

# View the general MCP connection log
cat mcp.log

# View server-specific error logs (replace SERVERNAME with your server name)
cat mcp-server-SERVERNAME.log
```

**For Windows:**

```bash
# Navigate to AppData logs
cd %APPDATA%\Claude\logs

# View MCP logs
type mcp.log
```

Look for error messages such as:
- "MCP error -32000: Connection closed"
- "Server transport closed unexpectedly"
- "Not connected"
- Runtime errors indicating missing dependencies

**What to look for:**
- Startup failures indicating the server crashed during initialization
- Missing runtime errors (Node.js, Python, etc.)
- Permission denied errors
- Port binding conflicts
- JSON parsing errors from configuration files

### Step 2: Verify Runtime Requirements

MCP servers require specific runtime environments. A missing or misconfigured runtime is one of the most common causes of connection failures.

```bash
# Check Node.js version (for Node-based MCP servers)
node --version

# Check Python version (for Python-based MCP servers)
python --version
python3 --version

# Check npm availability
npm --version

# Verify the runtime is in your PATH
which node
which python3
```

**Common runtime issues:**
- Node.js installed via NVM (Node Version Manager) may not be accessible to Claude Code
- Python virtual environments not activated
- Outdated runtime versions incompatible with your MCP server
- Runtime not in the system PATH

### Step 3: Fix Node Version Manager (NVM) Configuration Issues

If you installed Node.js via NVM, Claude Code may not be able to access the Node.js executable because NVM modifies the PATH only in interactive shells.

**Solution: Create an NVM wrapper script**

```bash
# Create a wrapper script at ~/.local/bin/node-wrapper.sh
mkdir -p ~/.local/bin
cat > ~/.local/bin/node-wrapper.sh << 'EOF'
#!/bin/bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
exec node "$@"
EOF

# Make the wrapper executable
chmod +x ~/.local/bin/node-wrapper.sh
```

Update your MCP server configuration to use the wrapper:

```json
{
  "mcpServers": {
    "your-server-name": {
      "command": "~/.local/bin/node-wrapper.sh",
      "args": ["/path/to/your/server/index.js"],
      "env": {}
    }
  }
}
```

### Step 4: Validate Configuration File Syntax

Invalid JSON syntax in your MCP configuration file will prevent servers from starting. The most common issues are trailing commas and incorrect quotation marks.

```bash
# Check your Claude Code configuration file
cat ~/.claude.json

# Validate JSON syntax using jq (install if needed)
jq . ~/.claude.json

# Alternative: Use Python to validate
python3 -m json.tool ~/.claude.json
```

**Common JSON syntax errors:**

```json
// INCORRECT - trailing comma
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
    }  // ← Remove this trailing comma
  }
}

// CORRECT
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    }
  }
}
```

### Step 5: Check for Port Conflicts

If your MCP server uses HTTP transport, port conflicts can prevent the server from starting.

```bash
# Check if a port is already in use (replace 3000 with your port)
# On macOS/Linux:
lsof -i :3000

# On Windows:
netstat -ano | findstr :3000

# Kill the process using the port if needed (use the PID from above)
# On macOS/Linux:
kill -9 <PID>

# On Windows:
taskkill /PID <PID> /F
```

**Configure a different port in your MCP server:**

```json
{
  "mcpServers": {
    "your-server": {
      "command": "node",
      "args": ["server.js"],
      "env": {
        "PORT": "3001"
      }
    }
  }
}
```

### Step 6: Use the MCP Inspector for Debugging

The MCP Inspector is an official debugging tool that allows you to test your MCP server in isolation before connecting it to Claude Code.

```bash
# Install the MCP Inspector globally
npm install -g @modelcontextprotocol/inspector

# Run the inspector with your server
npx @modelcontextprotocol/inspector node /path/to/your/server/index.js
```

The inspector opens a web interface where you can:
- Test server initialization
- Verify available tools and resources
- Execute test calls to your server
- View detailed error messages
- Check request/response payloads

**Benefits of using the inspector:**
- Isolates server issues from Claude Code configuration problems
- Provides detailed protocol-level debugging information
- Allows testing server responses before integration

### Step 7: Verify MCP Server Configuration

Use Claude Code's CLI commands to verify your server configuration and status.

```bash
# List all configured MCP servers
claude mcp list

# Check the status of MCP connections
claude mcp status

# Remove and re-add a problematic server
claude mcp remove server-name
claude mcp add server-name command [args...]
```

**Configuration file locations (in order of precedence):**
1. Project-level: `./.mcp.json` (in your project directory)
2. User-level: `~/.claude.json` (recommended for personal tools)
3. Global configuration (varies by OS)

### Step 8: Check Transport Type Compatibility

As of 2025, MCP supports two transport types: stdio (for local processes) and HTTP (for remote servers). The SSE (Server-Sent Events) transport is deprecated.

**Stdio transport (local servers):**

```json
{
  "mcpServers": {
    "local-server": {
      "command": "node",
      "args": ["./server.js"]
    }
  }
}
```

**HTTP transport (remote servers):**

```json
{
  "mcpServers": {
    "remote-server": {
      "url": "http://localhost:3000/mcp"
    }
  }
}
```

**Important notes:**
- Stdio servers communicate via standard input/output
- Do NOT use `console.log()` for debugging stdio servers (it interferes with the protocol)
- Use stderr for debugging output or write to a log file
- HTTP servers are recommended for remote MCP servers

### Step 9: Restart Claude Code and Verify Connection

After making configuration changes, always restart Claude Code to ensure changes take effect.

```bash
# Kill all Claude Code processes (macOS/Linux)
pkill -f "claude"

# Restart Claude Code
claude

# Verify MCP servers are connected
claude mcp list
```

**In the Claude Code UI:**
- Look for MCP server indicators in the status bar
- Check that tools from your MCP servers appear in available tools
- Test a simple operation using an MCP tool

### Step 10: Test with a Minimal MCP Server

If issues persist, test with a minimal MCP server to rule out configuration problems.

```javascript
// minimal-server.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server(
  {
    name: "minimal-test-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

server.setRequestHandler("tools/list", async () => {
  return {
    tools: [
      {
        name: "test_tool",
        description: "A simple test tool",
        inputSchema: {
          type: "object",
          properties: {},
        },
      },
    ],
  };
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

Configure it in `~/.claude.json`:

```json
{
  "mcpServers": {
    "minimal-test": {
      "command": "node",
      "args": ["./minimal-server.js"]
    }
  }
}
```

If this minimal server connects successfully, the issue is likely in your original server code, not in your Claude Code configuration.

## Expected Results

After successful troubleshooting, you should see:
- MCP servers listed as "connected" in `claude mcp list`
- No error messages in `~/Library/Logs/Claude/mcp.log`
- MCP tools appearing in Claude Code's available tools
- Successful execution of MCP tool calls without connection errors
- Server-specific logs showing successful initialization

## Troubleshooting

### Common Issue 1: "Server transport closed unexpectedly"
**Problem**: The MCP server crashes during startup or loses connection immediately.
**Solution**:
- Check server-specific logs in `~/Library/Logs/Claude/mcp-server-SERVERNAME.log`
- Verify all dependencies are installed (`npm install` or `pip install -r requirements.txt`)
- Test the server independently using the MCP Inspector
- Ensure the server process has necessary file system permissions

### Common Issue 2: "MCP error -32000: Connection closed"
**Problem**: The connection terminates after initial establishment.
**Solution**:
- Check for port conflicts if using HTTP transport
- Verify the server isn't exiting due to unhandled errors
- Review server code for proper error handling and connection maintenance
- Ensure keep-alive mechanisms are properly implemented for HTTP servers

### Common Issue 3: "Not connected" when executing tools
**Problem**: The server appears configured but tools fail with connection errors.
**Solution**:
- Restart Claude Code to refresh MCP connections
- Verify the server is actually running (check process list: `ps aux | grep your-server`)
- Check that the configuration scope matches (user vs. project)
- Ensure environment variables required by the server are properly set

### Common Issue 4: NVM-installed Node.js not found
**Problem**: Error indicates Node.js is not found despite being installed via NVM.
**Solution**:
- Create the NVM wrapper script as described in Step 3
- Alternatively, use the full path to the Node.js binary: `/Users/yourname/.nvm/versions/node/v20.0.0/bin/node`
- Or install Node.js globally without NVM for Claude Code compatibility

### Common Issue 5: JSON configuration syntax errors
**Problem**: Changes to `~/.claude.json` don't take effect or cause errors.
**Solution**:
- Validate JSON syntax using `jq . ~/.claude.json` or online JSON validators
- Remove all trailing commas in JSON objects and arrays
- Ensure all strings use double quotes, not single quotes
- Check for missing commas between object properties

## Additional Tips

- **Use `~/.claude.json` for consistency**: While project-level `.mcp.json` files work, the user-level `~/.claude.json` provides more consistent behavior across Claude Code versions
- **Prefer HTTP transport for remote servers**: HTTP is the recommended transport type for remote MCP servers as of 2025
- **Scope appropriately**: Use user scope for personal tooling you'll reuse across projects; use project scope for team-shared configurations
- **Monitor logs continuously**: Keep a terminal open tailing log files during initial setup: `tail -f ~/Library/Logs/Claude/mcp.log`
- **Test servers independently**: Always use the MCP Inspector to validate your server works before troubleshooting Claude Code integration
- **Avoid console.log in stdio servers**: For stdio transport, `console.log()` interferes with the protocol; use stderr or log files instead
- **Check official MCP server examples**: The `modelcontextprotocol/servers` GitHub repository contains reference implementations
- **Update regularly**: MCP is actively developed; keep your SDK and servers updated to benefit from bug fixes
- **Document custom configurations**: If you create wrapper scripts or custom configurations, document them for team members

## Related Articles
- KB-079: How to configure MCP servers in Claude Code
- KB-078: How to create custom MCP tools
- KB-081: How to secure MCP server connections

## Sources
- MCP Server Troubleshooting: Configuration & Errors 2025 | Learn MCP - https://www.mcpstack.org/learn/mcp-server-troubleshooting-guide-2025 (Accessed: November 16, 2025)
- How to Troubleshoot Common MCP Server Issues: A Step-by-Step Guide for Beginners - SuperAGI - https://superagi.com/how-to-troubleshoot-common-mcp-server-issues-a-step-by-step-guide-for-beginners/ (Accessed: November 16, 2025)
- How to Debug MCP Server with Anthropic Inspector? | Snyk - https://snyk.io/articles/how-to-debug-mcp-server-with-anthropic-inspector/ (Accessed: November 16, 2025)
- Claude Code Tips & Tricks: Setting Up MCP Servers - https://cloudartisan.com/posts/2025-04-12-adding-mcp-servers-claude-code/ (Accessed: November 16, 2025)
- Build an MCP Server - Model Context Protocol - https://modelcontextprotocol.io/quickstart/server (Accessed: November 16, 2025)
- Connect Claude Code to tools via MCP - Anthropic - https://docs.anthropic.com/en/docs/claude-code/mcp (Accessed: November 16, 2025)
- MCP filesystem: Server disconnected - Medium - https://medium.com/@jonigl/mcp-filesystem-server-disconnected-750e00917eec (Accessed: November 16, 2025)
- Error executing MCP tool: Not connected - GitHub Issues - https://github.com/modelcontextprotocol/servers/issues/1082 (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
