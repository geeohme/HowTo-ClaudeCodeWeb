# How to create your first custom slash command in .claude/commands/

**Article ID**: KB-068
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 10-15 minutes

## Overview
Custom slash commands allow you to create reusable prompt templates that Claude Code Web can execute with a simple `/command-name` syntax. By storing frequently-used prompts as Markdown files in the `.claude/commands/` directory, you can standardize workflows across your team and boost productivity with custom automation that fits your specific development needs.

## Prerequisites
- Access to Claude Code Web
- A project repository (for project-scoped commands)
- Basic understanding of Markdown formatting
- Familiarity with Claude Code Web's chat interface

## Steps

### Step 1: Create the .claude/commands Directory

Navigate to your project root and create the `.claude` directory structure if it doesn't already exist:

```bash
mkdir -p .claude/commands
```

This creates a project-scoped commands directory that will be version-controlled with your repository, allowing your entire team to share the same custom commands.

**Note**: There are two scopes for custom slash commands:
- **Project commands**: `.claude/commands/` (shared with team via git)
- **Personal commands**: `~/.claude/commands/` (available across all your projects)

For your first command, we'll use the project scope.

### Step 2: Create Your First Command File

Create a new Markdown file in the `.claude/commands/` directory. The filename (without the `.md` extension) becomes your command name.

For example, create a simple code review command:

```bash
touch .claude/commands/review-code.md
```

This will create the `/review-code` slash command (or `/project:review-code` if you have namespace conflicts).

### Step 3: Write the Command Prompt

Open the newly created file and write your prompt in plain language. The content is the instruction that Claude will receive when the command is invoked.

```markdown
Please review the code in this file or directory for:

1. Code quality and best practices
2. Potential bugs or edge cases
3. Performance considerations
4. Security vulnerabilities
5. Readability and maintainability

Provide specific suggestions for improvement with code examples where applicable.
```

Save the file. Your custom command is now ready to use!

### Step 4: Test Your Command

In Claude Code Web, type `/help` to see your new command listed. It should appear with a `(project)` label indicating it's a project-scoped command.

To use your command, simply type:

```
/review-code
```

Claude will execute the prompt you defined in the Markdown file.

### Step 5: Add Arguments Support (Optional)

Make your commands more flexible by using the `$ARGUMENTS` placeholder to accept parameters.

Create a new command file `.claude/commands/fix-issue.md`:

```markdown
Please help me fix the GitHub issue #$ARGUMENTS by:

1. Analyzing the issue description and requirements
2. Locating the relevant code files
3. Implementing a fix that addresses the root cause
4. Ensuring the fix doesn't introduce new issues
5. Suggesting test cases to verify the fix

Focus on writing clean, maintainable code that follows the project's existing patterns.
```

Now you can invoke it with arguments:

```
/fix-issue 1234
```

The `$ARGUMENTS` will be replaced with `1234`, allowing Claude to work on that specific issue.

### Step 6: Organize Commands with Namespaces (Optional)

For larger projects, organize commands into subdirectories for better structure:

```bash
mkdir -p .claude/commands/testing
mkdir -p .claude/commands/documentation
mkdir -p .claude/commands/refactoring
```

Create commands in subdirectories:

```bash
touch .claude/commands/testing/unit-test.md
touch .claude/commands/documentation/api-doc.md
```

These will be accessible as:
- `/testing:unit-test`
- `/documentation:api-doc`

### Step 7: Commit Your Commands to Version Control

Add your commands to git so your team can use them:

```bash
git add .claude/commands/
git commit -m "Add custom slash commands for code review and issue fixes"
git push
```

Team members who pull these changes will automatically have access to the same slash commands.

## Expected Results

After completing these steps, you should:

1. See your custom command(s) listed when you type `/help` in Claude Code Web
2. Be able to invoke your command with `/command-name` syntax
3. Have Claude execute your pre-defined prompt template
4. See `(project)` next to project-scoped commands in the help listing
5. Be able to pass arguments to commands using `$ARGUMENTS` placeholder

When you type `/review-code`, Claude should immediately start reviewing code according to your template. If using arguments, typing `/fix-issue 1234` should incorporate "1234" into the prompt.

## Troubleshooting

### Command Not Appearing in /help

**Problem**: Your new command doesn't show up in the `/help` list
**Solution**:
- Verify the file has a `.md` extension
- Ensure the file is in the correct directory (`.claude/commands/`)
- Try refreshing Claude Code Web or starting a new conversation
- Check that the filename doesn't contain special characters (use lowercase and hyphens)

### Arguments Not Being Replaced

**Problem**: The `$ARGUMENTS` text appears literally instead of being replaced
**Solution**:
- Ensure you're using the exact string `$ARGUMENTS` (all caps, singular)
- Verify you're passing arguments when invoking: `/command-name some-argument`
- Check for typos in the placeholder variable

### Command Conflicts with Built-in Commands

**Problem**: Your command name conflicts with a built-in or user-scoped command
**Solution**:
- Use the namespaced version: `/project:command-name`
- Rename your command to avoid conflicts
- Organize commands in subdirectories for automatic namespacing

### Commands Not Syncing Across Team

**Problem**: Team members don't see your custom commands
**Solution**:
- Verify the `.claude/commands/` directory is committed to git
- Ensure team members have pulled the latest changes
- Check that `.claude/` is not in `.gitignore`
- Confirm team members are using Claude Code Web in the same repository

## Additional Tips

- **Keep commands focused**: Each command should serve a specific, well-defined purpose
- **Use descriptive names**: Choose command names that clearly indicate their function (e.g., `/generate-tests` not `/gt`)
- **Document complex commands**: Add a comment at the top of the Markdown file explaining what the command does and how to use it
- **Leverage context**: Commands have access to the full conversation history and codebase context
- **Chain commands**: You can ask Claude to run multiple commands sequentially in one message
- **Iterate and refine**: Start simple and enhance commands based on team feedback
- **Share examples**: Include example usage in the command file as comments for team reference
- **Use templates**: Create command templates for common patterns (code generation, refactoring, documentation)

## Related Articles
- KB-067: Understanding Claude Code Web's built-in slash commands
- KB-069: Advanced slash command patterns and best practices
- KB-070: Sharing and maintaining team slash command libraries
- KB-033: Managing Claude Code Web project configuration

## Sources
- Slash commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- Claude Code Tips & Tricks: Custom Slash Commands - https://cloudartisan.com/posts/2025-04-14-claude-code-tips-slash-commands/ (Accessed: November 16, 2025)
- How to Add Custom Slash Commands in Claude Code - https://aiengineerguide.com/blog/claude-code-custom-command/ (Accessed: November 16, 2025)
- Claude Code Slash Commands: Boost Your Productivity - https://alexop.dev/tils/claude-code-slash-commands-boost-productivity/ (Accessed: November 16, 2025)
- awesome-claude-code GitHub Repository - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
