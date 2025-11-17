# How to use the $ARGUMENTS keyword in slash commands

**Article ID**: KB-070
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
The `$ARGUMENTS` keyword is a powerful placeholder that enables your custom slash commands to accept dynamic input from users. Instead of creating static, hardcoded commands, you can build flexible, reusable workflows that adapt based on the arguments passed at runtime. This guide teaches you how to leverage `$ARGUMENTS` and positional parameters to create sophisticated, parameterized slash commands.

## Prerequisites
- Basic understanding of custom slash commands in Claude Code Web
- Familiarity with creating Markdown files in `.claude/commands/` directory
- Knowledge of how to invoke slash commands in Claude Code interface
- Recommended: KB-068: How to create your first custom slash command
- Recommended: KB-069: How to write slash commands as Markdown files

## Steps

### Step 1: Understand the $ARGUMENTS Placeholder

The `$ARGUMENTS` keyword captures all text typed after the command name when you invoke it. When Claude processes your command, it replaces `$ARGUMENTS` with whatever arguments you provided.

**Example invocation:**
```
/fix-issue 123 high-priority authentication bug
```

In this case, `$ARGUMENTS` would contain: `123 high-priority authentication bug`

**Key characteristics:**
- Captures the entire argument string as-is
- Preserves spacing and special characters
- Available anywhere within your command's Markdown file
- Case-sensitive (must be uppercase: `$ARGUMENTS`)

### Step 2: Create a Basic Command with $ARGUMENTS

Create a simple slash command that uses `$ARGUMENTS` to make it flexible and reusable.

**File: `.claude/commands/analyze.md`**
```markdown
Please perform a detailed code analysis of: $ARGUMENTS

Focus on:
- Code quality and best practices
- Potential bugs or edge cases
- Performance implications
- Security considerations

Provide specific recommendations for improvement.
```

**Usage examples:**
```
/analyze src/auth/login.js
/analyze the entire authentication module
/analyze components/UserProfile.tsx for React best practices
```

Each invocation replaces `$ARGUMENTS` with your specific target, making the command adaptable to different contexts.

### Step 3: Use Multiple References to $ARGUMENTS

You can reference `$ARGUMENTS` multiple times within the same command file. Each occurrence will be replaced with the same argument string.

**File: `.claude/commands/document.md`**
```markdown
# Documentation Request for $ARGUMENTS

Please create comprehensive documentation for: $ARGUMENTS

Include:
1. Overview of what $ARGUMENTS does
2. API reference or interface documentation
3. Usage examples with $ARGUMENTS
4. Common pitfalls and best practices

Format the documentation in Markdown with clear headings and code examples.
```

**Usage:**
```
/document the PaymentProcessor class
```

All four instances of `$ARGUMENTS` will be replaced with "the PaymentProcessor class".

### Step 4: Implement Positional Arguments ($1, $2, $3, etc.)

For more structured commands, use numbered placeholders to capture specific argument positions. Arguments are split by whitespace.

**File: `.claude/commands/create-feature.md`**
```markdown
# Create Feature: $1

Create a new feature named "$1" with priority level: $2

## Requirements
- Feature name: $1
- Priority: $2
- Assigned team: $3

## Implementation Steps
1. Create feature branch: feature/$1
2. Implement core functionality for $1
3. Add tests with $2 priority coverage
4. Assign for review to $3 team
5. Document in changelog under "$1" section

Please confirm the approach before proceeding.
```

**Usage:**
```
/create-feature user-authentication high backend
```

This maps to:
- `$1` = "user-authentication"
- `$2` = "high"
- `$3` = "backend"

### Step 5: Combine $ARGUMENTS with Positional Parameters

You can use both `$ARGUMENTS` (entire string) and positional parameters (specific positions) in the same command for maximum flexibility.

**File: `.claude/commands/refactor.md`**
```markdown
# Refactoring Task

Refactor: $1

## Context
Full request: $ARGUMENTS

## Approach
- Target: $1
- Method: $2
- Apply pattern: $3

## Steps
1. Analyze current implementation of $1
2. Apply $2 refactoring technique
3. Follow $3 design pattern
4. Run tests and verify no regressions
5. Update documentation

Begin with a detailed analysis of $1 before making changes.
```

**Usage:**
```
/refactor UserService.ts extract-method singleton-pattern
```

Results in:
- `$1` = "UserService.ts"
- `$2` = "extract-method"
- `$3` = "singleton-pattern"
- `$ARGUMENTS` = "UserService.ts extract-method singleton-pattern"

### Step 6: Handle Arguments with Quotes for Multi-Word Values

When you need to pass arguments containing spaces as single units, use quotes in your invocation.

**File: `.claude/commands/bug-fix.md`**
```markdown
# Bug Fix Request

Issue: $1
Severity: $2
Description: $3

Please investigate and fix the bug: "$1" with severity level $2.

Additional context: $3

## Fix Checklist
- [ ] Reproduce the issue: $1
- [ ] Identify root cause
- [ ] Implement fix
- [ ] Add regression test
- [ ] Update issue tracker
```

**Usage with quotes:**
```
/bug-fix "login fails on mobile" critical "Users report 500 error on iOS Safari"
```

This properly separates:
- `$1` = "login fails on mobile"
- `$2` = "critical"
- `$3` = "Users report 500 error on iOS Safari"

### Step 7: Create Advanced Workflow Commands

Combine `$ARGUMENTS` with structured instructions to create sophisticated, multi-step workflows.

**File: `.claude/commands/pr-review.md`**
```markdown
# Pull Request Review: $ARGUMENTS

Please conduct a comprehensive review of PR: $ARGUMENTS

## Review Checklist

### 1. Fetch and Analyze PR
```bash
gh pr view $ARGUMENTS
gh pr diff $ARGUMENTS
```

### 2. Code Quality Assessment
- Architecture and design decisions
- Code clarity and maintainability
- Test coverage and quality
- Documentation completeness

### 3. Security Review
- Input validation
- Authentication/authorization
- Data sanitization
- Dependency vulnerabilities

### 4. Performance Considerations
- Algorithm efficiency
- Database query optimization
- Resource usage
- Potential bottlenecks

### 5. Provide Feedback
Generate a detailed review comment addressing:
- Strengths of the implementation
- Required changes (blocking issues)
- Suggested improvements (non-blocking)
- Questions for clarification

Post the review summary and indicate if the PR is ready to merge.
```

**Usage:**
```
/pr-review 456
/pr-review https://github.com/org/repo/pull/789
```

### Step 8: Test and Debug Your Commands

Before sharing commands with your team, test them thoroughly with various argument patterns.

**Testing checklist:**
1. **No arguments**: What happens if invoked without arguments?
   ```
   /analyze
   # $ARGUMENTS will be empty
   ```

2. **Single argument**: Test with one parameter
   ```
   /analyze app.js
   ```

3. **Multiple arguments**: Test with several parameters
   ```
   /create-feature auth-system high backend
   ```

4. **Special characters**: Test with paths and symbols
   ```
   /analyze src/utils/email-validator.ts
   ```

5. **Long argument strings**: Test with extensive input
   ```
   /document all authentication flows including OAuth2, JWT, and session-based auth
   ```

**Debugging tip:** Add a debug section to your command during development:
```markdown
Debug Info:
- Full arguments: [$ARGUMENTS]
- First arg: [$1]
- Second arg: [$2]
- Third arg: [$3]
```

## Expected Results

After implementing `$ARGUMENTS` in your slash commands, you should observe:

1. **Flexible command invocation**: Same command works for different targets
   ```
   /analyze login.js
   /analyze payment-processor.py
   /analyze entire codebase
   ```

2. **Dynamic prompt generation**: Claude receives contextual instructions
   - Command adapts to your specific needs
   - No need to create duplicate commands for similar tasks

3. **Consistent command structure**: Arguments populate predictably
   - First word after command → `$1`
   - Second word → `$2`
   - Entire string → `$ARGUMENTS`

4. **Reusable workflows**: Share parameterized commands with your team
   - Team members use same command with their specific contexts
   - Maintains consistency while allowing flexibility

**Example output in Claude Code interface:**
When you invoke `/pr-review 456`, Claude processes your command and responds with:
```
I'll conduct a comprehensive review of PR #456. Let me start by fetching the PR details...

[Executes gh pr view 456]
[Executes gh pr diff 456]
[Performs analysis based on review checklist]
[Provides structured feedback]
```

## Troubleshooting

### Common Issue 1: Arguments Not Substituting
**Problem**: The literal text "$ARGUMENTS" appears in the prompt instead of being replaced with actual arguments.
**Solution**:
- Verify you're using uppercase: `$ARGUMENTS` (not `$arguments` or `$Arguments`)
- Ensure the command file is saved as plain text Markdown (`.md` extension)
- Check that the file is in the correct location: `.claude/commands/`
- Restart Claude Code session if the command was just created

### Common Issue 2: Positional Arguments Out of Sync
**Problem**: `$2` contains unexpected text or is empty when you expect a value.
**Solution**:
- Arguments are split by whitespace—use quotes for multi-word arguments
- Count carefully: `$1` is the first word, `$2` is the second, etc.
- Remember that `$0` is not used (unlike some scripting languages)
- Test with a debug command that explicitly shows each argument:
  ```markdown
  Args: 1=[$1] 2=[$2] 3=[$3] All=[$ARGUMENTS]
  ```

### Common Issue 3: Special Characters Breaking Parsing
**Problem**: File paths with spaces or special characters cause unexpected behavior.
**Solution**:
- Wrap arguments in quotes: `/analyze "src/my components/Header.tsx"`
- Use relative paths when possible: `/analyze ./Header.tsx`
- Escape special characters if needed (though quotes usually suffice)
- For complex paths, consider using `$ARGUMENTS` instead of positional parameters

### Common Issue 4: Empty Arguments Not Handled
**Problem**: Command fails or behaves unexpectedly when invoked without arguments.
**Solution**:
- Add graceful handling in your command:
  ```markdown
  $ARGUMENTS

  If no specific target was provided, please analyze the current working directory.
  ```
- Provide helpful error messages:
  ```markdown
  Target for analysis: $ARGUMENTS

  Note: If no target specified, please ask the user what they'd like to analyze.
  ```

### Common Issue 5: $ARGUMENTS Contains Unexpected Formatting
**Problem**: Arguments include extra spaces, newlines, or formatting characters.
**Solution**:
- `$ARGUMENTS` captures exactly what you type—include this in your instructions
- Trim whitespace in your command logic if needed:
  ```markdown
  Please analyze: $ARGUMENTS

  (Ignore any extra whitespace in the target specification)
  ```
- Be explicit about argument format in command documentation:
  ```markdown
  Usage: /command <file-path> <priority> <team>
  Example: /command src/app.js high backend
  ```

## Additional Tips

- **Document your commands**: Include usage examples in your command file as comments
  ```markdown
  <!-- Usage: /analyze <file-or-directory> -->
  <!-- Example: /analyze src/components/Button.tsx -->

  Please analyze: $ARGUMENTS
  ```

- **Create command variants**: Build related commands for different argument patterns
  - `/fix-issue` for single issue number: `$1`
  - `/fix-issues` for multiple issues: `$ARGUMENTS`

- **Use descriptive argument names in documentation**: Help users understand expected format
  ```markdown
  # Syntax: /deploy <environment> <version> <rollback-enabled>
  Deploy to: $1
  Version: $2
  Rollback: $3
  ```

- **Provide default behaviors**: Make commands useful even with missing arguments
  ```markdown
  Environment: ${1:-staging}
  # If $1 is empty, document that it defaults to staging
  ```

- **Combine with .claude/CLAUDE.md**: Reference your slash commands in project documentation
  ```markdown
  ## Available Custom Commands
  - `/pr-review <pr-number>` - Comprehensive PR review
  - `/create-feature <name> <priority> <team>` - Scaffold new feature
  ```

- **Version control your commands**: Track changes to `.claude/commands/` in git
  - Share improvements with your team
  - Roll back if a command change causes issues
  - Document breaking changes in commit messages

- **Namespace complex command libraries**: Organize related commands in subdirectories
  ```
  .claude/commands/
    git/
      pr-review.md      → /git:pr-review
      commit-fix.md     → /git:commit-fix
    test/
      run-suite.md      → /test:run-suite
      coverage.md       → /test:coverage
  ```

## Related Articles
- KB-068: How to create your first custom slash command in .claude/commands/
- KB-069: How to write slash commands as Markdown files
- KB-071: How to organize slash commands with namespacing and directories
- KB-072: How to create project-specific vs personal slash commands
- KB-073: How to share commonly used slash commands with your team
- KB-074: How to debug and test custom slash commands

## Sources
- Slash Commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- Claude Code Commands Repository - https://github.com/wshobson/commands (Accessed: November 16, 2025)
- Awesome Claude Code Resources - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)
- Google Search: "Claude Code $ARGUMENTS slash commands documentation 2025"
- Google Search: "$ARGUMENTS Claude Code slash command markdown file example"

---
*This article is part of the Claude Code Web knowledge base*
