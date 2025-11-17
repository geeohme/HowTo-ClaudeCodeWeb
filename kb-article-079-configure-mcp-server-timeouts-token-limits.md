# How to configure MCP server timeouts and token limits

**Article ID**: KB-079
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
Model Context Protocol (MCP) servers can be configured with custom timeout and token limit settings to optimize performance and prevent resource exhaustion. This guide covers how to configure MCP server startup timeouts, tool call timeouts, and token output limits in Claude Code Web to handle long-running operations and large responses effectively.

## Prerequisites
- Claude Code Web installed and configured
- At least one MCP server configured in your environment
- Basic understanding of environment variables
- Familiarity with MCP server configuration files

## Steps

### Step 1: Configure MCP Server Startup Timeout

Set the `MCP_TIMEOUT` environment variable to control how long Claude Code waits for an MCP server to start before timing out.

```bash
# Set a 10-second startup timeout (value in milliseconds)
export MCP_TIMEOUT=10000

# For longer initialization times (30 seconds)
export MCP_TIMEOUT=30000
```

**For persistent configuration**, add this to your shell profile (`~/.bashrc`, `~/.zshrc`, or `~/.profile`):

```bash
echo 'export MCP_TIMEOUT=10000' >> ~/.bashrc
source ~/.bashrc
```

**Notes:**
- Default timeout varies by implementation (typically 5-10 seconds)
- Increase this value if your MCP servers perform heavy initialization tasks
- Values are in milliseconds (10000 = 10 seconds)

### Step 2: Configure MCP Tool Output Token Limits

Claude Code displays warnings when MCP tool output exceeds 10,000 tokens and enforces a default maximum of 25,000 tokens. Adjust this limit using the `MAX_MCP_OUTPUT_TOKENS` environment variable.

```bash
# Increase the maximum MCP output to 50,000 tokens
export MAX_MCP_OUTPUT_TOKENS=50000

# For very large outputs (100,000 tokens)
export MAX_MCP_OUTPUT_TOKENS=100000
```

**Best practices for token limits:**
- Monitor your MCP tools' typical output sizes
- Start with conservative limits (25,000-50,000 tokens)
- Consider that Claude Sonnet 4.5 has a 200k token context window
- Multiple MCP tools share this context window

### Step 3: Configure Claude Code Response Output Limits

Control the maximum tokens Claude can output in responses using the `CLAUDE_CODE_MAX_OUTPUT_TOKENS` environment variable.

```bash
# Set maximum Claude output to 100,000 tokens
export CLAUDE_CODE_MAX_OUTPUT_TOKENS=100000

# Default is typically 32,000 tokens
export CLAUDE_CODE_MAX_OUTPUT_TOKENS=32000
```

### Step 4: Configure Individual MCP Server Timeouts

Some MCP implementations support per-server timeout configuration in their JSON configuration files. Locate your MCP server configuration (typically in `~/.config/claude/mcp.json` or similar).

```json
{
  "mcpServers": {
    "my-server": {
      "command": "node",
      "args": ["/path/to/server/index.js"],
      "timeout": 120000,
      "env": {
        "API_KEY": "your-api-key"
      }
    }
  }
}
```

**Configuration options:**
- `timeout`: Request timeout in milliseconds (e.g., 120000 = 2 minutes)
- This timeout applies to individual tool calls from this server
- Not all MCP server implementations support this option

### Step 5: Configure Tool Call Timeouts (Advanced)

For MCP gateway or proxy implementations, set tool-specific timeout values:

```bash
# Set tool call timeout to 120 seconds
export MCP_TOOL_CALL_TIMEOUT=120
```

This is particularly useful for:
- Web scraping tools that may take time to load pages
- Database query tools processing large datasets
- API calls to external services with variable response times

### Step 6: Verify Configuration

After setting environment variables, restart Claude Code and verify the configuration:

```bash
# Check environment variables are set
echo $MCP_TIMEOUT
echo $MAX_MCP_OUTPUT_TOKENS
echo $CLAUDE_CODE_MAX_OUTPUT_TOKENS

# Restart Claude Code to apply changes
# (Method varies by platform - typically close and reopen)
```

Test with an MCP tool that previously timed out or exceeded token limits to confirm the new settings are working.

## Expected Results

After proper configuration, you should observe:

- **Startup timeouts**: MCP servers with long initialization times start successfully without timeout errors
- **Output warnings reduced**: Fewer or no warnings about MCP tool output exceeding token limits
- **Large responses handled**: Claude Code successfully processes larger MCP tool outputs without truncation
- **Long operations complete**: Time-intensive MCP operations finish without premature timeout cancellation
- **Stable connections**: Reduced frequency of hung connections or resource exhaustion issues

## Troubleshooting

### Common Issue 1
**Problem**: MCP server still times out on startup despite increased `MCP_TIMEOUT`
**Solution**:
- Verify the environment variable is set in the correct shell profile
- Ensure Claude Code was fully restarted after setting the variable
- Check server logs for actual initialization errors (not just timeouts)
- Some servers may require platform-specific timeout configuration

### Common Issue 2
**Problem**: Token limit warnings persist even after increasing `MAX_MCP_OUTPUT_TOKENS`
**Solution**:
- Confirm the environment variable syntax is correct (no spaces around `=`)
- Check if the MCP server itself has internal output limits
- Consider implementing pagination in the MCP server to return smaller chunks
- Verify you're not hitting Claude's overall context window (200k tokens)

### Common Issue 3
**Problem**: Tool calls still timeout during long-running operations
**Solution**:
- Differentiate between startup timeout (`MCP_TIMEOUT`) and tool call timeout
- Check if the MCP server supports the `timeout` configuration in JSON
- Implement progress notifications in your MCP server (resets timeout clock)
- Consider breaking long operations into multiple smaller tool calls
- Set `MCP_TOOL_CALL_TIMEOUT` if using a compatible MCP gateway

### Common Issue 4
**Problem**: Configuration changes don't take effect
**Solution**:
- Ensure environment variables are exported (use `export` command)
- Restart your terminal session or source your profile file
- On some platforms, Claude Code may need a full system restart
- Verify variables with `printenv | grep MCP` or `printenv | grep CLAUDE`

## Additional Tips

- **Context window management**: With Claude Sonnet 4.5's 200k token window, individual MCP tools can consume 14,000+ tokens just listing their capabilities. Monitor total context usage across all active MCP servers.

- **Progressive timeout increases**: Start with modest timeout increases and increment gradually. Setting extremely high timeouts can mask underlying performance issues in MCP servers.

- **Timeout vs. Progress notifications**: MCP specification recommends that implementations may reset the timeout clock when receiving progress notifications, but should always enforce a maximum timeout to prevent indefinite waits.

- **Development vs. Production settings**: Use higher timeouts and token limits during development for debugging, but optimize and reduce them for production use to prevent resource exhaustion.

- **Server-specific optimization**: Some MCP servers benefit from pagination or filtering at the server level rather than relying solely on token limit increases. Review your MCP server's documentation for built-in optimization options.

- **Multiple servers consideration**: Each active MCP server contributes to overall context consumption. If using many MCP servers simultaneously, you may need to increase Claude's output token limits to accommodate the combined context.

## Related Articles
- KB-078: How to debug MCP server connection issues (if available)
- KB-080: How to optimize MCP server performance (if available)
- KB-XXX: How to set up MCP servers in Claude Code Web

## Sources
- Model Context Protocol Lifecycle Specification - https://modelcontextprotocol.io/specification/2025-06-18/basic/lifecycle (Accessed: November 16, 2025)
- Connect Claude Code to tools via MCP - https://docs.claude.com/en/docs/claude-code/mcp (Accessed: November 16, 2025)
- MCP TypeScript SDK Issue #245 - https://github.com/modelcontextprotocol/typescript-sdk/issues/245 (Accessed: November 16, 2025)
- Optimising MCP Server Context Usage in Claude Code - https://scottspence.com/posts/optimising-mcp-server-context-usage-in-claude-code (Accessed: November 16, 2025)
- Google Search Suggestion: "MCP server timeout configuration Model Context Protocol 2025"

---
*This article is part of the Claude Code Web knowledge base*
