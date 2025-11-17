# How to write slash commands as Markdown files

**Article ID**: KB-069
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Slash commands in Claude Code Web are custom automation shortcuts stored as simple Markdown files. By creating these files in specific directories, you can define reusable prompt templates that streamline repetitive workflows, automate complex multi-step processes, and share common development patterns with your team through version control.

## Prerequisites
- Active Claude Code Web session with a connected repository
- Basic understanding of Markdown syntax
- Familiarity with command line operations (for user-scoped commands)
- Git knowledge (for sharing project-scoped commands with team)

## Steps

### Step 1: Understand Command Scope and Locations

Claude Code Web supports two types of slash commands based on their scope:

**Project-scoped commands** (accessible as `/project:command-name`):
- Location: `.claude/commands/` in your repository root
- Shared with team when checked into version control
- Specific to the current project
- Best for project-specific workflows

**User-scoped commands** (accessible as `/command-name`):
- Location: `~/.claude/commands/` in your home directory
- Available across all your projects and sessions
- Personal commands not shared with team
- Best for general-purpose workflows you use frequently

### Step 2: Create the Commands Directory

For project-scoped commands, create the directory structure in your repository:

```bash
mkdir -p .claude/commands
```

For user-scoped commands, create the directory in your home directory:

```bash
mkdir -p ~/.claude/commands
```

The `.claude/commands` directory can be committed to version control, making your custom commands available to all team members working on the project.

### Step 3: Create Your First Command File

Create a new Markdown file with a descriptive name. The filename (without the `.md` extension) becomes your command name.

For example, to create a command called `/project:analyze-errors`, create:

```bash
touch .claude/commands/analyze-errors.md
```

**Naming conventions:**
- Use lowercase with hyphens for multi-word commands
- Choose descriptive names that clearly indicate the command's purpose
- Avoid spaces and special characters
- Examples: `fix-bug.md`, `run-tests.md`, `deploy-staging.md`

### Step 4: Write the Command Prompt

Open your Markdown file and write the prompt you want Claude to execute. The entire content of the file becomes the prompt.

**Basic example** (`.claude/commands/analyze-errors.md`):

```markdown
Please analyze the error logs in this repository and identify the root cause:

1. Search for error and exception patterns in log files
2. Identify the most frequent errors
3. Trace each error back to the source code
4. Suggest fixes for the top 3 most critical issues
5. Provide a summary report with severity levels
```

When you run `/project:analyze-errors`, Claude receives this entire prompt and executes it.

### Step 5: Add Dynamic Arguments with $ARGUMENTS

Make your commands more flexible by using the special `$ARGUMENTS` keyword to accept parameters:

**Example with arguments** (`.claude/commands/fix-github-issue.md`):

```markdown
Please analyze and fix the GitHub issue: $ARGUMENTS

Follow these steps:
1. Use `gh issue view $ARGUMENTS` to get the issue details
2. Understand the problem described in the issue
3. Search for relevant files in the codebase
4. Implement the necessary changes to fix the issue
5. Write and run tests to verify the fix
6. Ensure code passes linting and type checking
7. Create a descriptive commit message referencing issue #$ARGUMENTS
8. Push changes and create a PR that closes the issue
```

**Usage:**
```
/project:fix-github-issue 1234
```

When invoked with `1234`, every instance of `$ARGUMENTS` in the command is replaced with `1234`.

### Step 6: Structure Complex Commands with Markdown Formatting

Use Markdown features to make your commands clear and maintainable:

**Example with rich formatting** (`.claude/commands/feature-workflow.md`):

```markdown
# Feature Development Workflow

Implement the feature: **$ARGUMENTS**

## Analysis Phase
- Review existing code structure
- Identify integration points
- Check for existing similar features
- Document dependencies

## Implementation Phase
1. Create feature branch with conventional naming
2. Implement core functionality
3. Add error handling and edge cases
4. Write comprehensive unit tests
5. Update integration tests if needed

## Quality Checks
- [ ] Run full test suite
- [ ] Check linting and formatting
- [ ] Verify type safety
- [ ] Review performance implications
- [ ] Update documentation

## Completion
- Create detailed PR with testing notes
- Link related issues
- Tag relevant reviewers

**Testing focus**: Ensure $ARGUMENTS works correctly across all supported scenarios.
```

The Markdown formatting (headers, lists, checkboxes) helps Claude understand the structure and importance of each section.

### Step 7: Create Namespaced Commands with Subdirectories

Organize related commands into subdirectories for better organization:

```bash
mkdir -p .claude/commands/testing
mkdir -p .claude/commands/deployment
mkdir -p .claude/commands/git
```

Create commands in subdirectories:

```bash
# Creates /project:testing/unit command
echo "Run unit tests with coverage reporting" > .claude/commands/testing/unit.md

# Creates /project:testing/integration command
echo "Run integration tests for $ARGUMENTS module" > .claude/commands/testing/integration.md

# Creates /project:deployment/staging command
echo "Deploy current branch to staging environment" > .claude/commands/deployment/staging.md
```

**Invoke namespaced commands:**
```
/project:testing/unit
/project:testing/integration api
/project:deployment/staging
```

### Step 8: Test Your Command

After creating your command file, test it in Claude Code Web:

1. Start a new message in your Claude Code session
2. Type `/` to see the slash command menu
3. Look for your command (it should appear automatically)
4. Invoke the command with or without arguments
5. Observe Claude's execution of your prompt

**Example test:**
```
/project:fix-github-issue 1234
```

Claude will receive your template with `1234` replacing `$ARGUMENTS` and begin executing the workflow.

### Step 9: Commit Project Commands to Version Control

Share your project-scoped commands with your team by committing them:

```bash
git add .claude/commands/
git commit -m "Add custom slash commands for common workflows"
git push
```

Team members who pull these changes will automatically have access to the same commands in their Claude Code sessions.

## Expected Results

After successfully creating slash commands:

1. **Command appears in autocomplete**: When you type `/` in Claude Code, your custom commands appear in the suggestion list
2. **Immediate execution**: Invoking the command sends the prompt to Claude, who begins executing the workflow
3. **Consistent behavior**: The same command produces consistent results across different sessions
4. **Team availability**: Project-scoped commands appear for all team members after they pull the repository
5. **Argument substitution**: When using `$ARGUMENTS`, the parameter values correctly replace the keyword throughout the prompt

**Example output when invoking `/project:analyze-errors`:**
```
I'll analyze the error logs in your repository. Let me start by searching for error patterns...

[Claude proceeds to search log files, identify errors, trace sources, and provide fixes]
```

## Troubleshooting

### Command Not Appearing in Autocomplete
**Problem**: Your newly created command doesn't show up when typing `/`
**Solution**:
- Verify the file is saved in the correct directory (`.claude/commands/` or `~/.claude/commands/`)
- Check that the file has a `.md` extension
- Ensure the filename doesn't contain spaces or special characters
- Try refreshing your Claude Code session or reconnecting the repository
- For project commands, confirm the `.claude` directory is at the repository root

### $ARGUMENTS Not Being Replaced
**Problem**: The literal text "$ARGUMENTS" appears in Claude's execution instead of the value you provided
**Solution**:
- Verify you're providing an argument when invoking: `/project:command-name argument-value`
- Check the spelling: it must be exactly `$ARGUMENTS` (all caps, with dollar sign)
- Ensure there are no extra spaces or characters around `$ARGUMENTS` in your template
- Note: If no argument is provided, `$ARGUMENTS` remains as literal text

### Command Executes But Doesn't Work as Expected
**Problem**: Claude receives the prompt but doesn't follow the workflow correctly
**Solution**:
- Make your instructions more explicit and structured
- Use numbered steps instead of paragraphs for complex workflows
- Add specific tool or command examples (like `gh issue view`, `npm test`)
- Include expected outcomes for each step
- Test the prompt manually first before converting it to a slash command
- Consider breaking complex commands into smaller, focused commands

### User-Scoped Commands Not Available
**Problem**: Commands in `~/.claude/commands/` don't appear in Claude Code Web
**Solution**:
- User-scoped commands may have different availability depending on your Claude Code setup
- Verify the directory path is exactly `~/.claude/commands/`
- For Claude Code Web, focus on project-scoped commands in `.claude/commands/`
- Check Claude Code documentation for the latest information on user-scoped command support

### Permission Errors When Creating Directories
**Problem**: Cannot create `.claude/commands/` directory
**Solution**:
- Ensure you have write permissions to the repository directory
- Check that the repository is properly cloned locally
- Verify you're in the correct working directory
- For user-scoped commands, ensure your home directory is writable

## Additional Tips

- **Start simple**: Begin with basic single-purpose commands before creating complex multi-step workflows
- **Use descriptive names**: Command names should clearly indicate their purpose (e.g., `fix-linting-errors` rather than `fix`)
- **Document complex commands**: Add comments or explanatory headers in your Markdown to help team members understand the workflow
- **Chain commands carefully**: While you can invoke multiple commands in sequence, keep individual commands focused on single responsibilities
- **Version control everything**: Commit your `.claude/commands/` directory to share workflows and maintain consistency across the team
- **Iterate based on results**: Refine your command prompts based on how Claude interprets and executes them
- **Reference specific tools**: Include exact command-line tools and syntax (e.g., `npm test`, `git status`) for more precise execution
- **Create command libraries**: Build a collection of commands for different phases of development (testing, deployment, debugging, documentation)
- **Use templates**: Create template commands that can be copied and customized for similar workflows
- **Combine with custom instructions**: Slash commands work alongside your project's custom instructions in `.claude/instructions.md` for comprehensive automation

## Related Articles
- KB-043: Configure project settings
- KB-044: Use config command
- KB-033: How to create a pull request automatically from completed work
- KB-052: Add new feature
- KB-054: Write unit tests

## Sources
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Slash commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- GitHub - awesome-claude-code - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
