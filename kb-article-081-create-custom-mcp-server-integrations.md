# How to create custom MCP server integrations

**Article ID**: KB-081
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 30-45 minutes

## Overview
The Model Context Protocol (MCP) is an open protocol developed by Anthropic that enables seamless integration between LLM applications like Claude Code and external data sources, tools, and services. This guide will walk you through creating your own custom MCP server to extend Claude Code's capabilities with specialized functionality tailored to your workflow.

## Prerequisites
- Basic understanding of either Python or TypeScript/Node.js
- Node.js 18+ (for TypeScript) or Python 3.10+ (for Python)
- Claude Code installed (desktop or web version with remote MCP support)
- Familiarity with REST APIs and async programming
- Basic knowledge of JSON schemas and data validation

## Steps

### Step 1: Choose Your SDK and Set Up the Project

MCP supports multiple programming languages. The two most popular SDKs are Python and TypeScript.

**For Python:**

```bash
# Create a new project directory
mkdir my-mcp-server
cd my-mcp-server

# Initialize with uv (recommended) or pip
uv init
uv add "mcp[cli]"

# Or with pip
pip install "mcp[cli]"
```

**For TypeScript:**

```bash
# Create a new project directory
mkdir my-mcp-server
cd my-mcp-server

# Initialize npm project
npm init -y

# Install MCP SDK and dependencies
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node

# Initialize TypeScript
npx tsc --init
```

### Step 2: Create the Basic Server Structure

**Python (using FastMCP):**

Create a file named `server.py`:

```python
from mcp.server.fastmcp import FastMCP

# Initialize the MCP server
mcp = FastMCP(name="my-custom-server")

# Server will be configured in the following steps

if __name__ == "__main__":
    # Run the server
    mcp.run()
```

**TypeScript:**

Create a file named `server.ts`:

```typescript
import { McpServer } from '@modelcontextprotocol/sdk/server/mcp.js';

// Initialize the MCP server
const server = new McpServer({
  name: 'my-custom-server',
  version: '1.0.0'
});

// Server configuration will follow
```

### Step 3: Implement Tools (Actions with Side Effects)

Tools enable Claude to perform actions through your server. They can call external APIs, modify data, or trigger computations.

**Python Example - Weather Tool:**

```python
from mcp.server.fastmcp import FastMCP
from pydantic import BaseModel

mcp = FastMCP(name="weather-server")

class WeatherData(BaseModel):
    temperature: float
    humidity: float
    condition: str
    city: str

@mcp.tool()
def get_weather(city: str, units: str = "celsius") -> WeatherData:
    """Get current weather information for a city."""
    # In production, call a real weather API
    # This is a simplified example
    return WeatherData(
        temperature=22.5,
        humidity=65.0,
        condition="partly cloudy",
        city=city
    )

@mcp.tool()
async def send_notification(message: str, priority: str = "normal") -> str:
    """Send a notification with the given message."""
    # Implement your notification logic here
    return f"Notification sent with priority: {priority}"
```

**TypeScript Example - Calculator Tool:**

```typescript
import { z } from 'zod';

server.registerTool(
  'calculate',
  {
    title: 'Calculator',
    description: 'Perform mathematical calculations',
    inputSchema: {
      operation: z.enum(['add', 'subtract', 'multiply', 'divide']),
      a: z.number(),
      b: z.number()
    },
    outputSchema: {
      result: z.number(),
      operation: z.string()
    }
  },
  async ({ operation, a, b }) => {
    let result: number;

    switch (operation) {
      case 'add': result = a + b; break;
      case 'subtract': result = a - b; break;
      case 'multiply': result = a * b; break;
      case 'divide': result = a / b; break;
    }

    const output = { result, operation };
    return {
      content: [{ type: 'text', text: JSON.stringify(output) }],
      structuredContent: output
    };
  }
);
```

### Step 4: Implement Resources (Data Exposure)

Resources expose data to Claude without performing heavy computation. They use URI patterns and should be read-only.

**Python Example:**

```python
@mcp.resource("config://app/{section}")
def get_config(section: str) -> str:
    """Read application configuration by section."""
    configs = {
        "database": "postgresql://localhost:5432/mydb",
        "api": "https://api.example.com/v1",
        "cache": "redis://localhost:6379"
    }
    return configs.get(section, "Section not found")

@mcp.resource("docs://api/{endpoint}")
def get_documentation(endpoint: str) -> str:
    """Get API documentation for a specific endpoint."""
    return f"Documentation for {endpoint} endpoint..."
```

**TypeScript Example:**

```typescript
import { ResourceTemplate } from '@modelcontextprotocol/sdk/server/mcp.js';

// Static resource
server.registerResource(
  'config',
  'config://app',
  {
    title: 'Application Configuration',
    description: 'Global app configuration',
    mimeType: 'application/json'
  },
  async (uri) => ({
    contents: [{
      uri: uri.href,
      text: JSON.stringify({
        apiUrl: 'https://api.example.com',
        timeout: 5000
      })
    }]
  })
);

// Dynamic resource with parameters
server.registerResource(
  'user-data',
  new ResourceTemplate('users://{userId}/profile', { list: undefined }),
  {
    title: 'User Profile',
    description: 'User profile information'
  },
  async (uri, { userId }) => ({
    contents: [{
      uri: uri.href,
      text: JSON.stringify({
        id: userId,
        name: `User ${userId}`,
        email: `user${userId}@example.com`
      })
    }]
  })
);
```

### Step 5: Add Prompts (Reusable Templates)

Prompts provide reusable templates for common LLM interaction patterns.

**Python Example:**

```python
@mcp.prompt()
def code_review_prompt(language: str, code: str, focus: str = "all") -> str:
    """Generate a code review prompt."""
    focuses = {
        "all": "Review for quality, security, and performance",
        "security": "Focus on security vulnerabilities",
        "performance": "Focus on performance optimizations"
    }

    return f"""Please review this {language} code.

Focus area: {focuses.get(focus, focuses['all'])}

Code:
```{language}
{code}
```

Provide specific suggestions for improvement."""
```

**TypeScript Example:**

```typescript
server.registerPrompt(
  'analyze-error',
  {
    title: 'Error Analysis',
    description: 'Analyze and suggest fixes for errors',
    inputSchema: {
      errorMessage: z.string(),
      stackTrace: z.string().optional(),
      language: z.string()
    }
  },
  async ({ errorMessage, stackTrace, language }) => {
    return {
      messages: [{
        role: 'user',
        content: {
          type: 'text',
          text: `Analyze this ${language} error and suggest fixes:\n\nError: ${errorMessage}\n${stackTrace ? `\nStack trace:\n${stackTrace}` : ''}`
        }
      }]
    };
  }
);
```

### Step 6: Configure Transport Layer

Choose how your server will communicate with Claude Code.

**Python - Standard I/O (Recommended for local servers):**

```python
# The default mcp.run() uses stdio transport
if __name__ == "__main__":
    mcp.run()
```

**TypeScript - HTTP Transport (For remote servers):**

```typescript
import express from 'express';
import { StreamableHTTPServerTransport } from '@modelcontextprotocol/sdk/server/streamableHttp.js';

const app = express();
app.use(express.json());

app.post('/mcp', async (req, res) => {
  const transport = new StreamableHTTPServerTransport({
    sessionIdGenerator: undefined,
    enableJsonResponse: true
  });

  res.on('close', () => transport.close());

  await server.connect(transport);
  await transport.handleRequest(req, res, req.body);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`MCP Server running on http://localhost:${PORT}/mcp`);
});
```

### Step 7: Add Lifecycle Management (Optional but Recommended)

Handle startup and shutdown gracefully with resource management.

**Python Example:**

```python
from contextlib import asynccontextmanager
from collections.abc import AsyncIterator
from dataclasses import dataclass

@dataclass
class AppContext:
    db_connection: any
    cache: any

@asynccontextmanager
async def app_lifespan(server: FastMCP) -> AsyncIterator[AppContext]:
    # Startup: Initialize resources
    print("Starting server, connecting to resources...")
    db = await connect_database()
    cache = await connect_cache()

    try:
        yield AppContext(db_connection=db, cache=cache)
    finally:
        # Shutdown: Clean up resources
        print("Shutting down, disconnecting resources...")
        await db.disconnect()
        await cache.disconnect()

mcp = FastMCP("my-server", lifespan=app_lifespan)
```

### Step 8: Configure Claude Code to Use Your Server

**For Local Servers (stdio transport):**

Use the Claude Code CLI wizard:

```bash
claude mcp add
```

Or manually edit `~/.claude.json` or `~/.config/claude/claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "my-custom-server": {
      "command": "python",
      "args": ["/absolute/path/to/server.py"]
    }
  }
}
```

For TypeScript/Node.js servers:

```json
{
  "mcpServers": {
    "my-custom-server": {
      "command": "node",
      "args": ["/absolute/path/to/dist/server.js"]
    }
  }
}
```

**For Remote Servers (HTTP transport):**

Since June 2025, Claude Code supports remote MCP servers with OAuth:

```bash
claude mcp add --transport http my-server http://localhost:3000/mcp
```

Or in the config file:

```json
{
  "mcpServers": {
    "my-remote-server": {
      "transport": "http",
      "url": "https://your-server.com/mcp"
    }
  }
}
```

### Step 9: Test Your MCP Server

**Using MCP Inspector (Recommended for development):**

```bash
# For Python
uv run mcp dev server.py

# For TypeScript
npx @modelcontextprotocol/inspector node dist/server.js
```

The inspector provides a web interface to test tools, resources, and prompts interactively.

**Using Claude Code:**

1. Restart Claude Code after updating the configuration
2. Start a new conversation
3. Type commands that would trigger your tools
4. Claude should now have access to your custom server's capabilities

### Step 10: Add Error Handling and Logging

**Python Example:**

```python
from mcp.server.fastmcp import Context
from mcp.server.session import ServerSession
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

@mcp.tool()
async def risky_operation(ctx: Context[ServerSession, None], input_data: str) -> str:
    """Perform an operation that might fail."""
    try:
        await ctx.info(f"Processing: {input_data}")
        await ctx.report_progress(progress=0.5, total=1.0)

        # Your operation here
        result = await perform_operation(input_data)

        await ctx.info("Operation completed successfully")
        return result
    except Exception as e:
        logger.error(f"Operation failed: {str(e)}")
        await ctx.error(f"Error: {str(e)}")
        raise
```

**TypeScript Example:**

```typescript
server.registerTool(
  'safe-operation',
  {
    title: 'Safe Operation',
    description: 'Operation with error handling',
    inputSchema: { data: z.string() }
  },
  async ({ data }) => {
    try {
      console.log(`Processing: ${data}`);

      const result = await performOperation(data);

      return {
        content: [{
          type: 'text',
          text: JSON.stringify({ success: true, result })
        }]
      };
    } catch (error) {
      console.error('Operation failed:', error);

      return {
        content: [{
          type: 'text',
          text: JSON.stringify({
            success: false,
            error: error.message
          })
        }],
        isError: true
      };
    }
  }
);
```

## Expected Results

After successfully creating and configuring your custom MCP server, you should experience:

1. **Server Registration**: Your server appears in Claude Code's available MCP servers list
2. **Tool Discovery**: Claude can discover and use your custom tools when relevant to user requests
3. **Resource Access**: Claude can read data from your exposed resources using the URI patterns you defined
4. **Prompt Templates**: Claude can leverage your custom prompts for specialized tasks
5. **Real-time Communication**: For HTTP servers, Claude Code maintains persistent connections with OAuth authentication
6. **Graceful Operation**: The server handles startup, shutdown, and errors without crashes

When testing, Claude should naturally invoke your tools when appropriate. For example, if you created a weather tool, asking "What's the weather in Paris?" should trigger your custom weather tool.

## Troubleshooting

### Common Issue 1
**Problem**: Server not appearing in Claude Code after configuration
**Solution**:
- Verify the config file path (use `~/.claude.json` for consistency)
- Check that the command path is absolute, not relative
- Restart Claude Code completely (quit and reopen)
- Check server logs for startup errors using `claude mcp logs my-server`

### Common Issue 2
**Problem**: Tools not being called by Claude
**Solution**:
- Ensure tool descriptions are clear and specific
- Verify input schema is properly defined with all required fields
- Check that the tool name is descriptive (avoid generic names like "tool1")
- Test the tool directly with MCP Inspector first
- Make sure the server is actually running (check status with `claude mcp status`)

### Common Issue 3
**Problem**: Python server fails with import errors
**Solution**:
- Install dependencies in the correct environment: `uv add <package>` or `pip install <package>`
- Verify Python version is 3.10 or higher: `python --version`
- Use absolute imports in your server code
- Check that the virtual environment is activated when running

### Common Issue 4
**Problem**: TypeScript server transpilation errors
**Solution**:
- Ensure `tsconfig.json` has `"module": "ES2020"` or higher
- Set `"moduleResolution": "node"` in TypeScript config
- Import with `.js` extensions even for `.ts` files (ESM requirement)
- Build the project before running: `npx tsc`

### Common Issue 5
**Problem**: Remote MCP server connection fails
**Solution**:
- Verify the server is accessible at the specified URL
- Check CORS settings if running on different domains
- Ensure OAuth configuration is correct (if using authentication)
- Test the endpoint with curl: `curl -X POST http://localhost:3000/mcp`
- Review server logs for connection errors

## Additional Tips

- **Start Simple**: Begin with a single tool or resource, then expand functionality incrementally
- **Use Type Safety**: Leverage Pydantic (Python) or Zod (TypeScript) for robust schema validation
- **Descriptive Metadata**: Write clear titles and descriptions - Claude uses these to determine when to use your tools
- **Async by Default**: Use async/await for all I/O operations to prevent blocking
- **Test Locally First**: Always test with MCP Inspector before integrating with Claude Code
- **Version Your Server**: Include version numbers and maintain backward compatibility when updating
- **Environment Variables**: Use `.env` files for configuration and secrets (never hardcode API keys)
- **Documentation**: Document your tools, resources, and prompts clearly for team members
- **Monitoring**: Add logging and metrics to track usage and debug issues
- **Security**: Validate all inputs, sanitize outputs, and implement rate limiting for production servers
- **Browse Examples**: The official MCP repositories contain reference implementations for common patterns

## Related Articles

- KB-080: Advanced MCP workflows and integrations (if available)
- KB-082: Deploying MCP servers to production (if available)
- KB-060: Setting up Claude Code development environment (if available)

## Sources

- Model Context Protocol Specification - https://modelcontextprotocol.io/specification/2025-06-18 (Accessed: November 16, 2025)
- MCP Python SDK Documentation - https://github.com/modelcontextprotocol/python-sdk (Accessed: November 16, 2025)
- MCP TypeScript SDK Documentation - https://github.com/modelcontextprotocol/typescript-sdk (Accessed: November 16, 2025)
- Anthropic Model Context Protocol Announcement - https://www.anthropic.com/news/model-context-protocol (Accessed: November 16, 2025)
- MCP Server Repository - https://github.com/modelcontextprotocol/servers (Accessed: November 16, 2025)
- Remote MCP Support in Claude Code - https://www.anthropic.com/news/claude-code-remote-mcp (Accessed: November 16, 2025)
- Anthropic MCP Course - https://anthropic.skilljar.com/introduction-to-model-context-protocol (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
