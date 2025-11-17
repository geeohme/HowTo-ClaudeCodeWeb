# How to use subagents for specialized tasks

**Article ID**: KB-086
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview
Subagents are specialized AI assistants that Claude Code Web can delegate tasks to for improved efficiency and focus. Each subagent operates with its own context window, custom system prompt, and optional tool restrictions, making them ideal for domain-specific work like code reviews, security audits, documentation generation, and data analysis. This guide teaches you how to create, configure, and leverage subagents to build sophisticated multi-agent workflows that dramatically improve your development productivity.

## Prerequisites
- Claude Code Web with an active project
- Understanding of YAML frontmatter syntax
- Familiarity with Markdown file creation
- Basic knowledge of Claude Code's tool system
- Understanding of slash commands (KB-068) and hooks (KB-061) is helpful

## Steps

### Step 1: Understand Subagent Fundamentals

Before creating subagents, understand what makes them powerful:

**Context Isolation**: Each subagent maintains a separate conversation context from the main agent. This prevents specialized tasks from polluting the main conversation with irrelevant details and allows the subagent to focus exclusively on its domain.

**Automatic Delegation**: Claude intelligently routes tasks to appropriate subagents based on:
- The task description in your request
- The `description` field in subagent configurations
- Current conversation context
- Available tools and capabilities

**Specialized Configuration**: Each subagent can have:
- Custom system prompts tailored to specific domains
- Limited tool access for security and focus
- Specific model assignments (Sonnet, Opus, Haiku)
- Reusable templates for consistent behavior

**Parallel Execution**: Multiple subagents can work simultaneously on different aspects of a problem, dramatically reducing total completion time.

### Step 2: Choose Between Project and User Scopes

Subagents support two configuration scopes:

**Project Scope** (`.claude/agents/`):
- Shared with team via version control
- Project-specific workflows and standards
- Committed to repository for consistency
- Team members automatically get the same subagents

**User Scope** (`~/.claude/agents/`):
- Personal subagents available across all projects
- Individual productivity tools and preferences
- Not shared with team members
- Stored in your user configuration

**Decision Guide**:
```
Use Project Scope for:
✓ Code review standards specific to your codebase
✓ Project-specific documentation generators
✓ Team workflow automation
✓ Shared development patterns

Use User Scope for:
✓ Personal productivity assistants
✓ Cross-project utilities
✓ Individual coding preferences
✓ Learning and experimentation
```

For this guide, we'll create project-scoped subagents.

### Step 3: Create Your First Subagent

Create the subagent directory structure:

```bash
mkdir -p .claude/agents
```

Create a code review subagent file `.claude/agents/code-reviewer.md`:

```markdown
---
name: code-reviewer
description: Expert code review specialist. Analyzes code for quality, security, performance, and maintainability. Use for reviewing pull requests, commits, or specific files.
tools: Read, Grep, Bash
model: sonnet
---

You are an expert code reviewer with deep knowledge of software engineering best practices, security patterns, and performance optimization.

When reviewing code, you should:

1. **Code Quality**: Assess readability, maintainability, and adherence to SOLID principles
2. **Security**: Identify vulnerabilities, injection risks, authentication issues, and data exposure
3. **Performance**: Spot inefficient algorithms, unnecessary operations, and resource leaks
4. **Best Practices**: Check for proper error handling, logging, testing, and documentation
5. **Style Consistency**: Ensure code follows project conventions and patterns

Provide specific, actionable feedback with:
- Clear explanations of issues found
- Severity ratings (Critical, High, Medium, Low)
- Code examples showing improvements
- Links to relevant documentation when applicable

Focus on being constructive and educational, not just critical.
```

This creates a specialized code review agent that Claude can invoke automatically when reviewing code.

### Step 4: Configure Subagent Fields

Understand each configuration field in the YAML frontmatter:

**Name Field** (required):
```yaml
name: data-analyst
```
- Identifies the subagent uniquely
- Use lowercase with hyphens
- Should be descriptive and memorable

**Description Field** (required):
```yaml
description: SQL and data analysis specialist for database queries and insights
```
- Tells Claude when to invoke this subagent
- Be specific about capabilities and use cases
- Include keywords Claude should match on

**Tools Field** (optional):
```yaml
tools: Read, Write, Bash, Grep
```
- Comma-separated list of allowed tools
- Restricts subagent to only these tools
- Omit to inherit all available tools from main agent
- Use for security and focus

**Model Field** (optional):
```yaml
model: sonnet    # or opus, haiku, inherit
```
- `sonnet`: Use Claude Sonnet (balanced speed/capability)
- `opus`: Use Claude Opus (maximum capability)
- `haiku`: Use Claude Haiku (fastest, cost-effective)
- `inherit`: Use same model as main conversation
- Omit to use default model

### Step 5: Create Specialized Subagents for Common Tasks

**Security Auditor**:

Create `.claude/agents/security-auditor.md`:

```markdown
---
name: security-auditor
description: Security specialist for vulnerability scanning, penetration testing analysis, and security best practices. Use for security reviews and threat modeling.
tools: Read, Grep, Bash
model: opus
---

You are a cybersecurity expert specializing in application security, vulnerability assessment, and threat modeling.

Your focus areas:
- Input validation and injection vulnerabilities (SQL, XSS, Command)
- Authentication and authorization flaws
- Sensitive data exposure and encryption issues
- Security misconfigurations
- Dependency vulnerabilities
- API security issues

For each finding, provide:
- OWASP category classification
- Exploitation scenario
- Impact assessment
- Remediation steps with code examples
- Prevention strategies for future development

Prioritize findings by exploitability and business impact.
```

**Documentation Generator**:

Create `.claude/agents/doc-writer.md`:

```markdown
---
name: doc-writer
description: Technical documentation specialist. Creates comprehensive README files, API documentation, user guides, and code comments. Use for any documentation tasks.
tools: Read, Write, Grep
model: sonnet
---

You are a technical writer specializing in clear, comprehensive software documentation.

Documentation standards:
- Use clear, concise language accessible to the target audience
- Include practical examples and use cases
- Provide setup instructions with all dependencies
- Add troubleshooting sections for common issues
- Use proper Markdown formatting with headers, code blocks, and lists
- Include diagrams or ASCII art where helpful
- Add table of contents for long documents

For API documentation:
- Document all endpoints, parameters, and responses
- Provide example requests and responses
- Include authentication requirements
- Note rate limits and best practices

Focus on helping users quickly understand and successfully use the software.
```

**Test Engineer**:

Create `.claude/agents/test-engineer.md`:

```markdown
---
name: test-engineer
description: Testing specialist for writing unit tests, integration tests, and test plans. Use when creating or improving test coverage.
tools: Read, Write, Bash, Grep
model: sonnet
---

You are a test engineering expert focused on comprehensive test coverage and quality assurance.

Testing approach:
- Write clear, maintainable tests that serve as documentation
- Follow AAA pattern (Arrange, Act, Assert)
- Test happy paths, edge cases, and error conditions
- Use descriptive test names that explain what's being tested
- Mock external dependencies appropriately
- Ensure tests are isolated and can run in any order

Coverage goals:
- All public APIs and functions
- Error handling and validation logic
- Edge cases and boundary conditions
- Integration points between components

Provide:
- Complete test files with all necessary imports
- Test data and fixtures
- Setup and teardown code
- Comments explaining complex test scenarios
```

### Step 6: Invoke Subagents in Your Workflow

**Automatic Invocation**:

Claude automatically selects appropriate subagents based on your request:

```
You: "Please review the authentication module for security issues"
→ Claude invokes security-auditor subagent

You: "Create documentation for the new API endpoints"
→ Claude invokes doc-writer subagent

You: "Add unit tests for the user service"
→ Claude invokes test-engineer subagent
```

The main agent recognizes keywords and task types that match subagent descriptions.

**Explicit Invocation**:

You can explicitly request a specific subagent:

```
You: "Use the code-reviewer agent to analyze src/payment-processor.js"

You: "I need the security-auditor to check this authentication code"

You: "Have the test-engineer create tests for all the utility functions"
```

**Built-in Plan Subagent**:

When Claude operates in plan mode, it automatically uses the built-in Plan subagent to research your codebase:

```
You: "Create a plan to add user authentication"
→ Plan subagent researches codebase
→ Main agent receives research summary
→ Creates comprehensive implementation plan
```

### Step 7: Manage Subagent Tool Access

Control which tools each subagent can use for security and focus:

**Full Tool Access** (omit tools field):
```yaml
---
name: full-stack-dev
description: Full-stack development specialist
---
# Inherits all tools: Read, Write, Edit, Grep, Bash, etc.
```

**Restricted Read-Only Access**:
```yaml
---
name: code-analyzer
description: Static code analysis specialist
tools: Read, Grep
---
# Can only read and search, cannot modify code
```

**Write-Focused Access**:
```yaml
---
name: content-generator
description: Content and documentation generator
tools: Read, Write, Grep
---
# Can read existing files and write new content
```

**Development Access**:
```yaml
---
name: feature-developer
description: Feature implementation specialist
tools: Read, Write, Edit, Bash, Grep
---
# Full development capabilities including running commands
```

**Security Best Practices**:
- Audit tools: Read, Grep only
- Documentation: Read, Write, Grep
- Development: All tools
- Testing: Read, Write, Bash, Grep

### Step 8: Create Multi-Agent Workflows

Combine multiple subagents for complex workflows:

**Feature Development Pipeline**:

Create `.claude/agents/feature-implementer.md`:

```markdown
---
name: feature-implementer
description: Implements new features following best practices. Use for feature development tasks.
tools: Read, Write, Edit, Bash, Grep
---

You implement new features with high quality standards.

Workflow:
1. Analyze existing codebase patterns
2. Design the feature following project architecture
3. Implement with clean, maintainable code
4. Add inline documentation
5. Follow existing code style and conventions

Consider:
- Error handling and validation
- Performance implications
- Backward compatibility
- Security best practices
```

**Workflow Example**:
```
You: "Implement user profile editing feature"

Claude's execution:
1. Plan subagent: Research current user system
2. feature-implementer: Build the implementation
3. test-engineer: Create comprehensive tests
4. doc-writer: Document the new API
5. code-reviewer: Final quality review
6. security-auditor: Security assessment

All subagents work in sequence, each in their domain.
```

**Parallel Execution**:

For independent tasks, subagents can work in parallel:

```
You: "Prepare for release: update docs, run tests, and review security"

Claude delegates in parallel:
- doc-writer: Updates all documentation
- test-engineer: Runs test suite and analyzes coverage
- security-auditor: Performs security scan

Results combined when all complete.
```

### Step 9: Test and Refine Your Subagents

**Testing Approach**:

1. **Single Task Test**: Invoke the subagent with a simple, clear task
```
You: "Use code-reviewer to review src/utils/validation.js"
```

2. **Verify Behavior**: Check that the subagent:
   - Uses only allowed tools
   - Follows the system prompt instructions
   - Stays focused on its domain
   - Provides output in the expected format

3. **Test Edge Cases**:
```
You: "Review this code for security issues" (should use security-auditor)
You: "Fix the bugs in this code" (should use feature-implementer, not reviewer)
```

4. **Refine System Prompts**: Based on results, adjust:
   - Clarity of instructions
   - Level of detail in output
   - Tone and formatting preferences
   - Domain boundaries

**Iteration Example**:
```markdown
# Initial version - too vague
---
description: Reviews code
---

# Improved version - specific
---
description: Expert code review specialist. Analyzes code for quality, security, performance, and maintainability. Use for reviewing pull requests, commits, or specific files.
---
```

### Step 10: Share Subagents with Your Team

**Project-Scoped Sharing**:

1. Create subagents in `.claude/agents/`
2. Commit to version control:

```bash
git add .claude/agents/
git commit -m "Add specialized subagents for code review and testing"
git push
```

3. Team members pull the changes and automatically have access

**Documentation**:

Create `.claude/agents/README.md` to document your subagents:

```markdown
# Project Subagents

## Available Subagents

### code-reviewer
**When to use**: Pull request reviews, commit analysis, general code quality checks
**Example**: "Review the authentication module"

### security-auditor
**When to use**: Security assessments, vulnerability scanning, threat modeling
**Example**: "Audit the payment processing code for security issues"

### test-engineer
**When to use**: Creating tests, improving coverage, test strategy
**Example**: "Write unit tests for the user service"

### doc-writer
**When to use**: README files, API docs, user guides
**Example**: "Document the new REST API endpoints"

## Usage Tips
- Claude automatically selects the right subagent based on your request
- You can explicitly request: "Use the security-auditor to check..."
- Subagents work best with clear, specific instructions
```

**Best Practices for Teams**:
- Document each subagent's purpose and usage
- Use consistent naming conventions
- Version control all subagents
- Review and update subagent prompts regularly
- Share successful patterns across projects

## Expected Results

After implementing subagents, you should experience:

1. **Specialized Expertise**: Tasks handled by domain-focused agents with deeper knowledge
2. **Context Clarity**: Main conversation stays high-level while subagents handle details
3. **Automatic Delegation**: Claude routes tasks to appropriate specialists without explicit instruction
4. **Improved Output Quality**: Specialized prompts produce more consistent, domain-specific results
5. **Team Consistency**: Shared subagents ensure uniform standards across team members
6. **Faster Workflows**: Parallel subagent execution reduces total completion time
7. **Reusable Patterns**: Once created, subagents work across all relevant tasks

Example successful interaction:
```
You: "Prepare the authentication module for production"

Claude: "I'll coordinate several specialists for this:
- security-auditor: Scanning for vulnerabilities
- code-reviewer: Quality and maintainability check
- test-engineer: Verifying test coverage
- doc-writer: Updating API documentation

[Executes in parallel, provides comprehensive report]"
```

## Troubleshooting

### Subagent Not Being Invoked

**Problem**: Claude doesn't use your subagent even for relevant tasks
**Solution**:
- Make the `description` field more specific with clear keywords
- Include use case examples in the description
- Explicitly request the subagent: "Use the code-reviewer agent..."
- Verify the `.md` file is in the correct directory
- Check YAML frontmatter syntax for errors
- Restart Claude Code Web session

### Subagent Has Wrong Tool Access

**Problem**: Subagent tries to use tools it shouldn't have access to
**Solution**:
- Add explicit `tools` field in YAML frontmatter
- List only allowed tools: `tools: Read, Grep`
- Verify tool names are spelled correctly and capitalized
- Restart session for changes to take effect
- Check if subagent inherits tools (omitted tools field means all tools)

### Subagent Output Quality Issues

**Problem**: Subagent doesn't follow instructions or produces poor results
**Solution**:
- Make system prompt more specific and directive
- Add examples of desired output format
- Include explicit dos and don'ts
- Try different model: `opus` for complex tasks, `haiku` for simple ones
- Break complex subagent into multiple specialized subagents
- Test with simpler tasks first to isolate issues

### Multiple Subagents Conflicting

**Problem**: Claude invokes wrong subagent or multiple subagents for same task
**Solution**:
- Make subagent descriptions more distinct and non-overlapping
- Use specific domain keywords in descriptions
- Explicitly request the desired subagent
- Review description fields for ambiguity
- Consider merging similar subagents

### Subagent Changes Not Reflected

**Problem**: Updates to subagent files don't take effect
**Solution**:
- Start a new conversation in Claude Code Web
- Verify file was saved correctly
- Check YAML syntax with a validator
- Ensure file is in correct directory (`.claude/agents/`)
- Clear browser cache if using web interface
- For team members: ensure they pulled latest changes

### Performance or Timeout Issues

**Problem**: Subagent tasks take too long or timeout
**Solution**:
- Use faster model: `haiku` instead of `opus`
- Reduce tool access to only essentials
- Break large tasks into smaller subtasks
- Simplify system prompt to focus subagent
- Check if subagent is doing unnecessary work
- Consider if task should be split across multiple subagents

## Additional Tips

- **Start Simple**: Create basic subagents first, add complexity as you learn what works
- **Iterate Based on Results**: Refine system prompts based on actual subagent behavior
- **Use Descriptive Names**: `security-auditor` is clearer than `sec` or `audit`
- **Domain Separation**: Each subagent should have a clear, distinct responsibility
- **Tool Minimalism**: Grant only necessary tools; restrictions improve focus and security
- **Model Selection**: Use `opus` for complex analysis, `sonnet` for balanced tasks, `haiku` for simple operations
- **Test Individually**: Verify each subagent works correctly before building complex workflows
- **Document Intent**: Include comments in subagent files explaining design decisions
- **Version Control**: Treat subagents as code; review, test, and version them
- **Monitor Usage**: Pay attention to which subagents get invoked and refine accordingly
- **Leverage Built-ins**: Use the built-in Plan subagent as a reference for quality prompts
- **Think Workflows**: Design subagents that complement each other for end-to-end processes
- **Security First**: Audit-focused subagents should be read-only (no Write, Edit, or Bash)
- **Share Learnings**: Document successful patterns for your team
- **Avoid Over-Engineering**: Don't create a subagent for every tiny task; focus on common, complex workflows

## Related Articles
- KB-068: How to create your first custom slash command in .claude/commands/
- KB-061: How to create a SessionStart hook for repository initialization
- KB-075: How to install and configure an MCP server
- KB-027: How to manage concurrent tasks effectively
- KB-056: How to perform code reviews with Claude Code Web

## Sources
- Subagents - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/sub-agents (Accessed: November 16, 2025)
- Building agents with the Claude Agent SDK - https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk (Accessed: November 16, 2025)
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Practical guide to mastering Claude Code's main agent and Sub-agents - https://jewelhuq.medium.com/practical-guide-to-mastering-claude-codes-main-agent-and-sub-agents-fd52952dcf00 (Accessed: November 16, 2025)
- Claude Code Subagents Enable Modular AI Workflows - InfoQ - https://www.infoq.com/news/2025/08/claude-code-subagents/ (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code subagents task delegation best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
