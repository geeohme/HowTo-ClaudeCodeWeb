# How to use Plan Mode for read-only codebase exploration

**Article ID**: KB-098
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
Plan Mode is a specialized operating mode in Claude Code Web that creates a safe, read-only environment for exploring and analyzing codebases without making any changes. When activated, Claude can only use read-only tools to research your code, understand architectural patterns, and formulate comprehensive implementation plans. This separation of exploration from execution prevents premature changes and mirrors how experienced developers naturally work: understand → plan → architect → build.

## Prerequisites
- Claude Code Web with an active Pro or Max subscription
- A connected GitHub repository
- Basic understanding of Claude Code's tool system
- Familiarity with keyboard shortcuts
- Understanding of codebase navigation concepts

## Steps

### Step 1: Understand Plan Mode Fundamentals

Before using Plan Mode, understand what makes it unique and powerful:

**Read-Only Guarantee**: Plan Mode cannot create, modify, or delete files. All write operations are blocked until you explicitly approve the plan and exit Plan Mode. This makes it completely safe for exploring unfamiliar codebases or reviewing changes before implementation.

**Available Tools in Plan Mode**:
- **Read** - View file contents
- **Glob** - Find files by pattern matching
- **Grep** - Search file contents with regex
- **LS** - List directory contents
- **WebSearch** - Search the web for information
- **WebFetch** - Fetch and analyze web pages
- **Task** - Delegate to research subagents
- **TodoRead/TodoWrite** - Manage task lists
- **NotebookRead** - Read Jupyter notebooks

**Blocked Tools**:
- Write, Edit, NotebookEdit (file modifications)
- Bash commands (unless explicitly configured otherwise)
- Any tool that could alter your codebase

**Context Preservation**: All research and analysis performed in Plan Mode is retained when you exit, so Claude can immediately begin implementation with full understanding of your codebase.

**Automatic Subagent Delegation**: When researching in Plan Mode, Claude may automatically invoke the Explore Subagent—a Haiku-powered specialist that efficiently searches your codebase while saving context tokens and improving performance.

### Step 2: Activate Plan Mode

There are three methods to enter Plan Mode:

**Method 1: Keyboard Shortcut** (Recommended for quick access):

Press `Shift + Tab` twice in rapid succession. You'll see a visual indicator that Plan Mode is active.

```
[Plan Mode Activated]
Claude will now analyze your codebase without making changes.
Ask me to explore, research, or plan your implementation.
```

**Method 2: Command Line** (For CLI users):

```bash
claude --permission-mode plan
```

**Method 3: Default Configuration** (Always start in Plan Mode):

Create or edit `.claude/settings.json`:

```json
{
  "permissions": {
    "defaultMode": "plan"
  }
}
```

With this configuration, every new session starts in Plan Mode automatically. You'll need to explicitly exit Plan Mode to make code changes.

**Verification**: When Plan Mode is active, you'll see an indicator in the interface, and Claude will acknowledge that it's operating in read-only mode.

### Step 3: Use Plan Mode for Initial Codebase Exploration

When encountering a new codebase, use Plan Mode to build understanding without risk:

**Initial Discovery Questions**:

```
You: "I'm new to this codebase. Explore the project structure and explain:
- What this application does
- The main technologies and frameworks used
- How the code is organized
- Key entry points and important files"
```

Claude will systematically explore using Glob, Read, and Grep to:
1. Identify configuration files (package.json, requirements.txt, etc.)
2. Map the directory structure
3. Read key files like README, main entry points
4. Analyze import/dependency patterns
5. Search for documentation

**Architecture Understanding**:

```
You: "Analyze the authentication system. Explain:
- Where authentication logic lives
- What authentication method is used
- How sessions are managed
- What security measures are in place"
```

Claude explores relevant files, traces code flows, and builds a comprehensive picture without touching anything.

**Dependency Investigation**:

```
You: "Find all places where the User model is referenced and explain how it's used across the application"
```

Claude uses Grep and Read to trace usage patterns throughout the codebase.

### Step 4: Implement the Explore-Plan-Code Workflow

This is the recommended workflow pattern for complex changes:

**Phase 1: Explore** (in Plan Mode)

Ask Claude to understand the current state:

```
You: "I want to add email notification functionality when users complete a purchase.
First, explore the codebase and explain:
- How purchases are currently processed
- If there's existing email functionality
- Where this feature should be integrated
- What dependencies might be needed"
```

Claude researches without writing code, giving you a clear picture of the current system.

**Phase 2: Plan** (still in Plan Mode)

Request a detailed implementation plan:

```
You: "Based on your exploration, create a detailed step-by-step plan for implementing
email notifications on purchase completion. Include:
- All files that need to be created or modified
- The specific changes needed in each file
- Dependencies to install
- Testing strategy
- Potential risks or considerations"
```

Claude creates a comprehensive plan showing exactly what will be done, allowing you to:
- Review the approach before implementation
- Catch potential issues early
- Request modifications to the plan
- Ensure alignment with project standards

**Phase 3: Code** (exit Plan Mode)

After reviewing and approving the plan:

```
You: "The plan looks good. Please implement it."
```

Claude will automatically call the `ExitPlanMode` tool, which presents the plan for your approval. Once you approve, Claude gains access to write tools and executes the plan.

Alternatively, press `Shift + Tab` to manually exit Plan Mode and begin implementation.

### Step 5: Use Plan Mode for Safe Code Review

Plan Mode is ideal for reviewing code changes without accidental modifications:

**Pull Request Review**:

```
You: "Review the changes in src/payment-processor.js and analyze:
- Code quality and maintainability
- Potential bugs or edge cases
- Security considerations
- Performance implications
- Whether it follows our project conventions"
```

Claude reads the file and provides detailed feedback without making changes.

**Comparative Analysis**:

```
You: "Compare the current implementation in auth/login.js with best practices
for JWT authentication. Identify gaps and security concerns."
```

Claude can research both your code and external best practices (using WebSearch) to provide informed analysis.

**Historical Investigation**:

```
You: "This function seems overly complex. Search the git history to understand
why it was implemented this way, then explain if it can be simplified."
```

Use bash commands (if allowed in your Plan Mode configuration) to examine git history, or ask Claude to help you investigate the reasoning behind code decisions.

### Step 6: Leverage the Explore Subagent

When you ask complex exploratory questions in Plan Mode, Claude may automatically delegate to the Explore Subagent:

**Automatic Delegation** occurs when you request:
- Comprehensive codebase searches
- Pattern analysis across many files
- Architectural understanding
- Dependency mapping

**Example Triggering Automatic Delegation**:

```
You: "Find all API endpoints in this application and document what each one does"
```

You'll see Claude invoke the Explore Subagent, which:
1. Efficiently searches for route definitions using Glob and Grep
2. Reads relevant files to understand each endpoint
3. Compiles findings into a structured summary
4. Returns results to the main agent

**Benefits**:
- Faster exploration using the lightweight Haiku model
- Reduced context token usage in main conversation
- Specialized focus on research tasks
- Parallel processing capabilities

**Manual Invocation**:

```
You: "Use the Explore subagent to map out all database models and their relationships"
```

### Step 7: Configure Advanced Plan Mode Settings

Customize Plan Mode behavior for your workflow:

**Allow Specific Bash Commands**:

If you want to permit certain bash commands in Plan Mode (like git operations), configure in `.claude/settings.json`:

```json
{
  "permissions": {
    "defaultMode": "plan",
    "planMode": {
      "allowedCommands": [
        "git log",
        "git show",
        "git diff",
        "git blame",
        "npm list",
        "ls",
        "tree"
      ]
    }
  }
}
```

This allows read-only git exploration while maintaining file safety.

**Per-Project Configuration**:

Different projects may need different Plan Mode settings:

```json
{
  "permissions": {
    "defaultMode": "normal"
  },
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "echo 'Use Plan Mode (Shift+Tab twice) when exploring unfamiliar code'"
          }
        ]
      }
    ]
  }
}
```

**Team Conventions**:

Document Plan Mode usage expectations in your project README:

```markdown
## Development Workflow

### New Features
1. Enter Plan Mode (Shift+Tab twice)
2. Explore relevant code areas
3. Request implementation plan
4. Review plan with team
5. Exit Plan Mode and implement
6. Review changes before committing
```

### Step 8: Use Plan Mode for Documentation Research

Generate comprehensive documentation by exploring in read-only mode:

**API Documentation**:

```
You: "In Plan Mode, explore all REST API endpoints and generate comprehensive
API documentation including:
- Endpoint URLs and HTTP methods
- Request parameters and body structure
- Response formats and status codes
- Authentication requirements
- Example requests and responses"
```

Claude reads route files, controller code, and existing documentation to compile complete API docs.

**Architecture Documentation**:

```
You: "Analyze the entire application architecture and create a technical
architecture document explaining:
- System components and their responsibilities
- Data flow between components
- External service integrations
- Database schema and relationships
- Deployment architecture"
```

**Onboarding Documentation**:

```
You: "Create a developer onboarding guide by exploring:
- How to set up the development environment
- Key concepts and patterns used in this codebase
- Common development workflows
- Where to find what (file organization)
- Testing strategies"
```

All of this research happens safely in Plan Mode before generating the documentation files.

### Step 9: Plan Complex Refactoring Operations

Use Plan Mode to analyze and plan large-scale changes safely:

**Refactoring Analysis**:

```
You: "I want to refactor this application from class components to functional
components with hooks. First, in Plan Mode:
1. Find all class components in the codebase
2. Analyze their complexity and dependencies
3. Identify which ones can be safely converted
4. Determine the conversion order (dependencies first)
5. Create a phased refactoring plan"
```

Claude explores comprehensively and creates a safe, ordered implementation plan.

**Migration Planning**:

```
You: "Plan a migration from Express.js to Fastify. Explore the current Express
implementation and create a detailed migration strategy including:
- All Express-specific code that needs updating
- Fastify equivalents for each pattern
- Breaking changes and compatibility issues
- Testing strategy during migration
- Rollback plan if issues arise"
```

**Performance Optimization**:

```
You: "Analyze the application for performance bottlenecks:
- Identify expensive operations
- Find N+1 query patterns
- Locate unnecessary re-renders
- Spot inefficient algorithms
Then create an optimization plan prioritized by impact"
```

### Step 10: Exit Plan Mode and Execute

When ready to implement your plan, exit Plan Mode:

**Method 1: Automatic Exit via Plan Approval**

When Claude presents a plan and calls `ExitPlanMode`, you'll see:

```
I've analyzed the codebase and created this implementation plan:

[Detailed plan with steps]

Would you like me to proceed with implementation?
```

Respond with approval:

```
You: "Yes, proceed with the implementation"
```

Claude automatically exits Plan Mode and begins executing the plan.

**Method 2: Manual Exit**

Press `Shift + Tab` to toggle out of Plan Mode at any time.

**Method 3: Explicit Instruction**

```
You: "Exit Plan Mode and implement the changes we discussed"
```

**After Exiting**:
- All write tools become available (Write, Edit, Bash, etc.)
- Claude retains all context from the exploration phase
- Implementation proceeds based on the understanding built in Plan Mode
- You can re-enter Plan Mode anytime by pressing Shift+Tab twice

**Best Practice**: Always review the plan carefully before approving. Ask questions, request modifications, or reject the plan if needed:

```
You: "The plan looks good except for step 3. Instead of creating a new file,
modify the existing config.js. Update the plan and show me the revised version."
```

## Expected Results

After mastering Plan Mode for codebase exploration, you should experience:

1. **Risk-Free Exploration**: Confidently explore unfamiliar codebases knowing nothing can be accidentally modified

2. **Comprehensive Understanding**: Deeper codebase knowledge from systematic exploration before making changes

3. **Better Implementation Plans**: More thoughtful, complete plans that consider the full context of your codebase

4. **Fewer Mistakes**: Catching architectural mismatches and potential issues during the planning phase rather than after implementation

5. **Faster Onboarding**: New team members can explore codebases safely and learn through investigation

6. **Documentation Quality**: Better documentation generated from thorough read-only analysis

7. **Informed Code Reviews**: More thorough reviews when you can safely analyze code without modification risk

Example successful workflow:

```
[Shift+Tab twice to enter Plan Mode]

You: "Explore how user authentication works in this app"

Claude: "I'll analyze the authentication system. Using Grep to find auth-related files..."
[Explores codebase using Read, Grep, Glob]
"The authentication system uses JWT tokens with the following architecture:
- Login endpoint: /api/auth/login (src/routes/auth.js)
- Token generation: src/utils/jwt.js using jsonwebtoken library
- Middleware validation: src/middleware/authenticate.js
- User model: src/models/User.js
[Detailed explanation continues...]"

You: "Create a plan to add OAuth2 Google login alongside the existing JWT system"

Claude: "Based on my exploration, here's the implementation plan:
1. Install passport and passport-google-oauth20 dependencies
2. Create new OAuth configuration file at src/config/oauth.js
3. Modify src/routes/auth.js to add /auth/google and /auth/google/callback routes
4. Update src/middleware/authenticate.js to handle both JWT and OAuth tokens
5. Extend User model to support OAuth provider field
[Detailed plan continues...]
Would you like me to proceed with implementation?"

You: "Yes, implement it"

[Claude exits Plan Mode and executes the plan]
```

## Troubleshooting

### Plan Mode Not Activating

**Problem**: Pressing Shift+Tab twice doesn't activate Plan Mode
**Solution**:
- Try pressing the keys more slowly with a clear pause between the two Tab presses
- Ensure you're in the main chat input area (click the input field first)
- Check for browser extensions that might intercept keyboard shortcuts
- On Windows, there's a known bug—use `claude --permission-mode plan` command instead
- Verify you're using an updated version of Claude Code Web
- Try the command line method or settings.json configuration instead

### Plan Not Being Presented for Approval

**Problem**: Claude explores but never presents a plan or calls ExitPlanMode
**Solution**:
- Explicitly request a plan: "Create a detailed implementation plan"
- Ask Claude to exit Plan Mode: "Exit Plan Mode and implement your proposal"
- Manually exit by pressing Shift+Tab
- Verify you're actually in Plan Mode (should see indicator)
- Check that your request is clear about wanting implementation after planning

### Read-Only Exploration Too Limited

**Problem**: Can't run necessary git or diagnostic commands in Plan Mode
**Solution**:
- Configure allowed bash commands in `.claude/settings.json` (see Step 7)
- Add read-only commands to the allowlist: git log, git diff, git show, etc.
- Temporarily exit Plan Mode for specific commands, then re-enter
- Use alternative approaches: ask Claude to use Grep instead of git grep
- Consider if the command is truly necessary for exploration vs. implementation

### Explore Subagent Not Being Invoked

**Problem**: Complex exploration requests aren't delegating to Explore subagent
**Solution**:
- Make requests more explicitly exploratory: "Search the entire codebase for..."
- Use words that trigger delegation: "explore", "find all", "analyze across"
- Break down very specific requests into broader exploratory questions
- The subagent invocation is automatic—trust Claude to delegate when beneficial
- Direct invocation: "Use the Explore subagent to find all database queries"

### Context Lost When Exiting Plan Mode

**Problem**: Claude seems to forget exploration findings after exiting Plan Mode
**Solution**:
- This shouldn't happen—context is preserved across Plan Mode transitions
- Try explicitly summarizing: "Summarize your findings before we proceed"
- Request the plan in writing: "Document the plan in detail before implementation"
- If context is truly lost, this is a bug—report it and restart the session
- Use TodoWrite to capture key findings during exploration

### Accidental Plan Mode Exit

**Problem**: Accidentally exit Plan Mode before finishing exploration
**Solution**:
- Simply press Shift+Tab twice again to re-enter Plan Mode
- No work is lost—you can continue exploring
- Claude retains all previous exploration context
- Consider setting Plan Mode as default in settings.json
- Be more deliberate with Shift+Tab presses to avoid accidental toggles

## Additional Tips

- **Start Every Complex Task in Plan Mode**: Make it a habit to explore before implementing, especially in unfamiliar code areas

- **Use Plan Mode for Learning**: When learning a new codebase or technology, Plan Mode lets you safely investigate without fear of breaking things

- **Combine with Git Worktrees**: Explore in Plan Mode in one worktree while implementing in another (see KB-088)

- **Document Exploration Findings**: Ask Claude to create documentation from Plan Mode explorations before exiting

- **Iterative Planning**: Stay in Plan Mode through multiple rounds of refinement until the plan is perfect

- **Team Code Reviews**: Use Plan Mode during pair programming or code review sessions for safe collaborative exploration

- **Security Audits**: Plan Mode is perfect for security reviews where you want analysis without any risk of modification

- **Default to Safe**: When in doubt, use Plan Mode. It's easy to exit but harder to undo accidental changes

- **Keyboard Shortcut Mastery**: Practice the Shift+Tab+Tab rhythm until it becomes muscle memory

- **Plan Mode for Meetings**: Enter Plan Mode when demoing code to stakeholders to prevent live-coding accidents

- **Exploration Templates**: Create saved prompts for common exploration tasks you perform regularly

- **Cross-Reference**: Use WebSearch in Plan Mode to compare your codebase against documentation and best practices

- **Phased Workflows**: For large projects, explore in Plan Mode, exit to implement one phase, re-enter Plan Mode to plan the next phase

- **Teaching Tool**: Great for mentoring junior developers—they can explore without breaking things while learning

## Related Articles
- KB-086: How to use subagents for specialized tasks
- KB-056: How to perform code reviews with Claude Code Web
- KB-061: How to create a SessionStart hook for repository initialization
- KB-013: How to view the file structure of your repository
- KB-088: How to use git worktrees for parallel development sessions

## Sources
- ClaudeLog - Plan Mode Documentation - https://claudelog.com/mechanics/plan-mode/ (Accessed: November 16, 2025)
- Claude Code Best Practices - Anthropic Engineering - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Common workflows - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/common-workflows (Accessed: November 16, 2025)
- Understanding Claude Code Plan Mode and the Architecture of Intent - Jamie Lord - https://lord.technology/2025/07/03/understanding-claude-code-plan-mode-and-the-architecture-of-intent.html (Accessed: November 16, 2025)
- Claude Code Plan Mode: Revolutionizing the Senior Engineer's Workflow - Medium - https://medium.com/@kuntal-c/claude-code-plan-mode-revolutionizing-the-senior-engineers-workflow-21d054ee3420 (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code Plan Mode read-only codebase exploration 2025"

---
*This article is part of the Claude Code Web knowledge base*
