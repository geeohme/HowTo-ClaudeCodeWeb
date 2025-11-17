# How to use MCP servers for browser automation

**Article ID**: KB-077
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 25-30 minutes

## Overview
MCP (Model Context Protocol) servers enable Claude Code to perform browser automation tasks by connecting to specialized servers that control web browsers programmatically. This guide demonstrates how to install, configure, and use browser automation MCP servers to navigate websites, interact with elements, capture screenshots, and extract data—all through natural language commands in Claude Code.

## Prerequisites
- Claude Code with an active Pro or Max subscription
- Node.js 18+ and npm installed in your development environment
- Basic understanding of MCP server configuration (see KB-075)
- Familiarity with web selectors (CSS selectors, XPath)
- For advanced features: Docker (optional), Chrome/Chromium browser

## Steps

### Step 1: Choose a Browser Automation MCP Server

Several browser automation MCP servers are available, each with different capabilities:

**Puppeteer MCP Server** (Recommended for beginners):
- Uses Puppeteer (Node.js library for Chrome/Chromium control)
- Supports both new browser instances and connecting to existing Chrome windows
- Local execution with full control
- Best for: Web scraping, testing, screenshot capture

**Browserbase MCP Server** (Recommended for production):
- Cloud-based browser automation with Stagehand integration
- 20-40% faster performance through automatic caching
- Built-in anti-detection features
- Best for: Production automation, complex workflows, distributed tasks

**Playwright MCP Server**:
- Cross-browser support (Chrome, Firefox, Safari, Edge)
- Robust testing capabilities
- Best for: Cross-browser testing, comprehensive automation

**Chrome DevTools MCP Server**:
- Direct Chrome DevTools Protocol access
- Advanced debugging capabilities
- Best for: Performance analysis, deep browser inspection

For this guide, we'll use the **Puppeteer MCP Server** for its simplicity and the **Browserbase MCP Server** for advanced use cases.

### Step 2: Install the Puppeteer MCP Server

**Option A: Using the Claude CLI (Recommended)**

Run this command in your terminal:

```bash
claude mcp add puppeteer @modelcontextprotocol/server-puppeteer
```

This automatically adds the server to your MCP configuration.

**Option B: Manual Configuration via .mcp.json**

Create or edit `.mcp.json` in your project root:

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"],
      "env": {}
    }
  }
}
```

**Option C: Global Installation**

Install globally for reuse across projects:

```bash
npm install -g puppeteer-mcp-server
```

Then configure in `.mcp.json`:

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "puppeteer-mcp-server",
      "args": [],
      "env": {}
    }
  }
}
```

### Step 3: Install the Browserbase MCP Server (Optional)

For cloud-based browser automation with advanced features:

**Obtain API Credentials:**
1. Sign up at https://www.browserbase.com
2. Create a new project and note your Project ID
3. Generate an API key from your dashboard
4. (Optional) Get a Gemini API key from Google AI Studio for vision capabilities

**Configure the Server:**

Add to your `.mcp.json`:

```json
{
  "mcpServers": {
    "browserbase": {
      "command": "npx",
      "args": ["@browserbasehq/mcp-server-browserbase"],
      "env": {
        "BROWSERBASE_API_KEY": "your-api-key-here",
        "BROWSERBASE_PROJECT_ID": "your-project-id-here",
        "GEMINI_API_KEY": "your-gemini-key-here"
      }
    }
  }
}
```

**For sensitive credentials**, use environment variable expansion:

```json
{
  "mcpServers": {
    "browserbase": {
      "command": "npx",
      "args": [
        "@browserbasehq/mcp-server-browserbase",
        "--modelName",
        "anthropic/claude-sonnet-4.5"
      ],
      "env": {
        "BROWSERBASE_API_KEY": "${BROWSERBASE_API_KEY}",
        "BROWSERBASE_PROJECT_ID": "${BROWSERBASE_PROJECT_ID}"
      }
    }
  }
}
```

Then set environment variables in your shell or `.env` file before starting Claude Code.

### Step 4: Verify MCP Server Installation

After configuration, restart Claude Code or reload the MCP servers:

```bash
claude mcp list
```

You should see your browser automation server(s) listed with their status.

In Claude Code Web, you can verify by asking:
```
What MCP tools are available?
```

Claude should list browser automation tools like:
- `puppeteer_navigate`
- `puppeteer_screenshot`
- `puppeteer_click`
- `puppeteer_fill`
- `puppeteer_evaluate`

### Step 5: Perform Basic Browser Navigation

Ask Claude to navigate to a website:

```
Using the Puppeteer MCP server, navigate to https://example.com and take a screenshot
```

Claude will execute something like:
1. Launch a browser instance
2. Navigate to the specified URL
3. Capture a screenshot
4. Return the screenshot for your review

### Step 6: Interact with Web Elements

**Clicking Elements:**

```
Navigate to https://github.com/search and click the search box
```

Claude will use the `puppeteer_click` tool with appropriate CSS selectors.

**Filling Forms:**

```
Go to the login page and fill in the username field with "testuser"
```

Claude will use `puppeteer_fill` with selector and value parameters.

**Selecting Dropdown Options:**

```
On the registration form, select "United States" from the country dropdown
```

Claude will use `puppeteer_select` to choose the option.

### Step 7: Execute JavaScript in Browser Context

For advanced interactions, execute custom JavaScript:

```
Navigate to https://news.ycombinator.com and run JavaScript to extract all article titles
```

Claude will use `puppeteer_evaluate` to run JavaScript like:

```javascript
Array.from(document.querySelectorAll('.titleline > a')).map(a => a.textContent)
```

### Step 8: Extract Structured Data

Ask Claude to extract and format data:

```
Go to Amazon.com, search for "wireless headphones", and extract the first 5 product titles and prices into a CSV file
```

Claude will:
1. Navigate to Amazon
2. Fill the search box
3. Click search
4. Extract product information using selectors
5. Format the data as CSV
6. Save to a file

### Step 9: Connect to an Existing Chrome Instance (Advanced)

For debugging or working with authenticated sessions:

**Launch Chrome with debugging enabled:**

```bash
# macOS/Linux
google-chrome --remote-debugging-port=9222

# Windows
"C:\Program Files\Google\Chrome\Application\chrome.exe" --remote-debugging-port=9222
```

**Then ask Claude:**

```
Connect to the Chrome instance on debugging port 9222 and navigate to the admin dashboard
```

Claude will use `puppeteer_connect_active_tab` to attach to your existing browser session, preserving cookies and authentication.

### Step 10: Use Advanced Browserbase Features

For cloud-based automation with anti-detection:

```
Using Browserbase with advanced stealth enabled, navigate to the target site and extract the pricing table data
```

Configure with additional flags in `.mcp.json`:

```json
{
  "mcpServers": {
    "browserbase": {
      "command": "npx",
      "args": [
        "@browserbasehq/mcp-server-browserbase",
        "--advancedStealth",
        "--proxies",
        "--browserWidth",
        "1920",
        "--browserHeight",
        "1080"
      ],
      "env": {
        "BROWSERBASE_API_KEY": "${BROWSERBASE_API_KEY}",
        "BROWSERBASE_PROJECT_ID": "${BROWSERBASE_PROJECT_ID}"
      }
    }
  }
}
```

### Step 11: Create Persistent Browser Sessions

For workflows requiring session persistence:

```
Create a persistent Browserbase session, log into the application, and save the context for later use
```

Claude will use Browserbase's `--contextId` flag to maintain session state across multiple automation runs.

### Step 12: Handle Complex Multi-Step Workflows

Combine multiple browser actions in a single request:

```
Please automate this workflow:
1. Navigate to the e-commerce site
2. Search for "running shoes"
3. Filter by size 10 and price under $100
4. Add the first matching item to cart
5. Proceed to checkout and take a screenshot of the cart summary
6. Extract the total price and save it to a file
```

Claude will orchestrate multiple MCP tool calls to complete the entire workflow.

## Expected Results

After successful configuration and execution:

1. **MCP Server Status**: `claude mcp list` shows your browser automation server as "active"
2. **Tool Availability**: Claude can list and describe browser automation tools
3. **Browser Launch**: Browser windows open automatically (Puppeteer) or in the cloud (Browserbase)
4. **Screenshots**: You receive visual confirmation of browser states
5. **Data Extraction**: Structured data saved to files in requested formats (CSV, JSON, etc.)
6. **Console Output**: Browser console logs captured and returned when requested
7. **Error Handling**: Claude reports navigation errors, timeout issues, or selector problems

## Troubleshooting

### Issue 1: MCP Server Not Appearing in Available Tools
**Problem**: After configuration, browser automation tools don't appear when you ask Claude what tools are available.
**Solution**:
- Restart Claude Code completely
- Run `claude mcp list` to verify the server is loaded
- Check `.mcp.json` syntax for JSON errors
- Ensure Node.js 18+ is installed: `node --version`
- Try manual installation: `npx @modelcontextprotocol/server-puppeteer` to check for errors

### Issue 2: "Browser Failed to Launch" Error
**Problem**: Puppeteer reports it cannot launch the browser.
**Solution**:
- Install Chrome/Chromium: Puppeteer needs a browser binary
- On Linux servers, install dependencies: `sudo apt-get install -y libgbm1 libnss3 libatk-bridge2.0-0`
- Set explicit executable path in environment:
  ```json
  "env": {
    "PUPPETEER_EXECUTABLE_PATH": "/usr/bin/chromium-browser"
  }
  ```
- Use Browserbase instead for cloud-based execution without local browser requirements

### Issue 3: Selectors Not Finding Elements
**Problem**: Claude cannot find elements to click or fill.
**Solution**:
- Ask Claude to take a screenshot first to inspect the page structure
- Use more specific selectors: `button[type="submit"]` instead of just `button`
- Wait for elements to load: "Navigate and wait 3 seconds before clicking"
- Use XPath for complex selections: `//div[@class="specific"]/button`
- Check for iframes: Elements might be in a separate frame context

### Issue 4: Browserbase API Authentication Errors
**Problem**: "Invalid API key" or "Unauthorized" errors from Browserbase.
**Solution**:
- Verify API key is correct and active in Browserbase dashboard
- Ensure environment variables are properly set before starting Claude Code
- Check for whitespace or quotes in the API key value
- Confirm your Browserbase subscription is active
- Use `echo $BROWSERBASE_API_KEY` to verify the variable is set

### Issue 5: Timeout Errors on Slow Pages
**Problem**: Operations timeout before pages fully load.
**Solution**:
- Increase timeout in your request: "Navigate with a 30-second timeout"
- Configure server-level timeouts in `.mcp.json` (see KB-079)
- Use Browserbase's caching features to speed up repeated visits
- Ask Claude to wait for specific elements: "Wait for the login button to appear"

### Issue 6: Anti-Bot Detection Blocking Automation
**Problem**: Websites detect automation and block access.
**Solution**:
- Use Browserbase with `--advancedStealth` flag for anti-detection
- Enable proxies: Add `--proxies` flag to Browserbase configuration
- Randomize viewport sizes and user agents
- Add realistic delays: "Wait 2-3 seconds between actions"
- Consider using authenticated sessions with `puppeteer_connect_active_tab`

## Additional Tips

- **Start Simple**: Test basic navigation and screenshots before complex workflows
- **Screenshot Everything**: Use screenshots to debug selector issues and verify page state
- **Use Specific Selectors**: Prefer IDs and unique classes over generic tags
- **Handle Dynamic Content**: Allow time for JavaScript-heavy pages to load completely
- **Combine with Other MCP Servers**: Use filesystem MCP servers to save extracted data, or GitHub MCP to commit scraped data
- **Version Control Your Config**: Commit `.mcp.json` to share browser automation setup with your team
- **Secure Your Credentials**: Never commit API keys; use environment variables with `.gitignore` for `.env` files
- **Monitor Rate Limits**: Browserbase and other services have usage quotas; check your dashboard
- **Use Headless for Speed**: Headless mode (default) runs faster than headed browsers
- **Keep Dependencies Updated**: Run `npx @modelcontextprotocol/server-puppeteer@latest` periodically
- **Test Across Browsers**: Use Playwright MCP for cross-browser compatibility testing
- **Leverage Browser DevTools**: Use `puppeteer_connect_active_tab` to manually inspect and test selectors first

## Related Articles
- KB-075: Install and configure MCP server
- KB-076: Connect Claude Code to external MCP servers
- KB-079: Configure MCP server timeouts and token limits
- KB-045: Safely provide API keys to Claude Code
- KB-042: Set environment variables in Claude Code

## Sources
- Model Context Protocol Official Specification - https://modelcontextprotocol.io/specification/2025-06-18 (Accessed: November 16, 2025)
- Browserbase MCP Server GitHub Repository - https://github.com/browserbase/mcp-server-browserbase (Accessed: November 16, 2025)
- Browserbase MCP Integration - https://www.browserbase.com/mcp (Accessed: November 16, 2025)
- Puppeteer MCP Server - https://www.pulsemcp.com/servers/modelcontextprotocol-puppeteer (Accessed: November 16, 2025)
- Puppeteer MCP Server GitHub - https://github.com/merajmehrabi/puppeteer-mcp-server (Accessed: November 16, 2025)
- Model Context Protocol GitHub Repository - https://github.com/modelcontextprotocol/modelcontextprotocol (Accessed: November 16, 2025)
- Make Playwright Smart with Model Context Protocol - https://medium.com/executeautomation/make-playwright-ui-testing-smart-with-model-context-protocol-of-claude-ai-18c26892193d (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
