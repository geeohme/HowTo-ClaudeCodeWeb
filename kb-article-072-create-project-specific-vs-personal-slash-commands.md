# How to create project-specific vs personal slash commands

**Article ID**: KB-072
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15 minutes

## Overview
Understanding when to create project-specific versus personal slash commands is crucial for building an efficient Claude Code Web workflow. Project-specific commands live in your repository and are shared with your team, while personal commands reside in your home directory and are available across all your projects. This guide helps you decide which scope to use, how to organize commands across both locations, and strategies for managing a robust command library.

## Prerequisites
- Understanding of custom slash commands in Claude Code Web
- Familiarity with creating Markdown files in `.claude/commands/`
- Access to both project repository and home directory
- Knowledge of git version control (for project commands)
- Recommended: KB-068: How to create your first custom slash command
- Recommended: KB-069: How to write slash commands as Markdown files

## Steps

### Step 1: Understand the Two Command Scopes

Claude Code Web supports two distinct scopes for custom slash commands, each serving different purposes.

**Project-specific commands** (`.claude/commands/`):
- Located in your repository at `.claude/commands/`
- Shared with team members through version control
- Specific to the current project's workflows
- Invoked as `/project:command-name` (or `/command-name` if no conflict)
- Version-controlled and collaborative
- Best for team workflows, project conventions, and shared automation

**Personal commands** (`~/.claude/commands/`):
- Located in your home directory at `~/.claude/commands/`
- Available across all your projects and repositories
- Private to your development environment
- Invoked as `/command-name` (takes precedence over project commands)
- Not shared with team members
- Best for personal productivity, cross-project patterns, and individual preferences

**Command resolution order:**
1. Personal commands (`~/.claude/commands/`) - highest priority
2. Project commands (`.claude/commands/`) - lower priority
3. Built-in commands - lowest priority

When naming conflicts occur, use the namespaced form `/project:command-name` to explicitly invoke a project command.

### Step 2: Decide Which Scope to Use

Use this decision framework to determine the appropriate scope for each command:

**Use project-specific commands when:**

✅ **Team collaboration**: The command encodes team-wide workflows
```markdown
Example: .claude/commands/code-review.md
- Standardizes review process across team
- Ensures consistent quality checks
- Shared conventions everyone should follow
```

✅ **Project-specific patterns**: The command references project structure, tools, or conventions
```markdown
Example: .claude/commands/run-tests.md
- Uses project-specific test framework
- References project directory structure
- Runs project-specific CI/CD commands
```

✅ **Documentation and onboarding**: The command helps new team members understand workflows
```markdown
Example: .claude/commands/setup-environment.md
- Documents project setup process
- Encodes team knowledge
- Reduces onboarding friction
```

✅ **Versioned workflows**: The command should evolve with the project
```markdown
Example: .claude/commands/deploy-staging.md
- Deployment process changes over time
- Team needs to stay synchronized
- Historical context preserved in git
```

**Use personal commands when:**

✅ **Cross-project utility**: The command works identically across all your projects
```markdown
Example: ~/.claude/commands/explain-code.md
- Generic code explanation request
- No project-specific assumptions
- Useful everywhere you work
```

✅ **Personal productivity**: The command reflects your individual working style
```markdown
Example: ~/.claude/commands/daily-summary.md
- Your personal reporting format
- Individual preferences for summaries
- Not relevant to other team members
```

✅ **Private workflows**: The command contains personal or sensitive information
```markdown
Example: ~/.claude/commands/prepare-demo.md
- Your specific demo preparation routine
- Personal notes or reminders
- Client-specific confidential details
```

✅ **Experimental commands**: The command is in development and not ready to share
```markdown
Example: ~/.claude/commands/experimental-refactor.md
- Testing new workflow patterns
- May need significant iteration
- Will move to project scope when mature
```

### Step 3: Create Project-Specific Commands

Set up commands that will be shared with your team through version control.

**Create the project commands directory:**
```bash
cd /path/to/your/project
mkdir -p .claude/commands
```

**Create a team workflow command** (`.claude/commands/feature-workflow.md`):
```markdown
# Feature Development Workflow

Implement the feature: **$ARGUMENTS**

## Branch Creation
- Create branch: `feature/$ARGUMENTS`
- Follow team naming convention

## Implementation Checklist
- [ ] Write failing tests first (TDD approach)
- [ ] Implement feature to pass tests
- [ ] Run full test suite: `npm test`
- [ ] Check linting: `npm run lint`
- [ ] Verify TypeScript types: `npm run type-check`
- [ ] Update relevant documentation

## Code Review Preparation
- [ ] Self-review all changes
- [ ] Write clear commit messages
- [ ] Create PR with our team template
- [ ] Link related issues
- [ ] Add screenshots/demos if UI changes

## Definition of Done
Per team standards:
- All tests passing
- Code coverage > 80%
- No linting errors
- Documentation updated
- PR approved by 2 team members

Please follow this workflow for: $ARGUMENTS
```

**Commit to version control:**
```bash
git add .claude/commands/
git commit -m "Add team feature workflow command"
git push
```

All team members who pull these changes will now have access to `/project:feature-workflow`.

### Step 4: Create Personal Commands

Set up commands in your home directory for cross-project use.

**Create the personal commands directory:**
```bash
mkdir -p ~/.claude/commands
```

**Create a personal productivity command** (`~/.claude/commands/morning-checklist.md`):
```markdown
# My Morning Development Checklist

Good morning! Let's start the day productively.

## Repository Status
1. Check git status for uncommitted changes
2. Fetch latest changes from remote
3. Review any merge conflicts

## Communication Check
4. Summarize any GitHub notifications or mentions
5. Check for assigned issues or PRs requiring attention
6. List any critical issues in the project

## Planning
7. Review today's calendar and deadlines
8. Suggest priorities based on issue labels and due dates
9. Identify any blockers that need immediate attention

## Environment Verification
10. Verify development environment is up to date
11. Check for available package updates
12. Review any security advisories

Please provide a concise summary and suggest where I should focus first.
```

**Create a cross-project utility command** (`~/.claude/commands/explain.md`):
```markdown
Please provide a detailed explanation of: $ARGUMENTS

Include:
1. **Purpose**: What does this code/concept accomplish?
2. **How it works**: Step-by-step breakdown of the logic
3. **Key concepts**: Important patterns or techniques used
4. **Common pitfalls**: What could go wrong or be misunderstood?
5. **Related concepts**: What else should I know about?
6. **Practical examples**: Real-world use cases

Use clear language and provide code examples where helpful.
```

These commands are now available in all your Claude Code sessions across any project.

### Step 5: Organize Commands with Namespacing

Use subdirectories to organize related commands within each scope.

**Project command organization:**
```bash
.claude/commands/
├── testing/
│   ├── unit.md           # /project:testing/unit
│   ├── integration.md    # /project:testing/integration
│   └── e2e.md           # /project:testing/e2e
├── deployment/
│   ├── staging.md        # /project:deployment/staging
│   ├── production.md     # /project:deployment/production
│   └── rollback.md       # /project:deployment/rollback
├── git/
│   ├── pr-template.md    # /project:git/pr-template
│   └── release-notes.md  # /project:git/release-notes
└── onboarding/
    ├── setup.md          # /project:onboarding/setup
    └── architecture.md   # /project:onboarding/architecture
```

**Personal command organization:**
```bash
~/.claude/commands/
├── code-analysis/
│   ├── explain.md        # /code-analysis/explain
│   ├── optimize.md       # /code-analysis/optimize
│   └── security.md       # /code-analysis/security
├── documentation/
│   ├── api-doc.md        # /documentation/api-doc
│   └── readme.md         # /documentation/readme
├── productivity/
│   ├── morning.md        # /productivity/morning
│   ├── summary.md        # /productivity/summary
│   └── review.md         # /productivity/review
└── learning/
    ├── research.md       # /learning/research
    └── examples.md       # /learning/examples
```

**Create organized structure:**
```bash
# Project commands
mkdir -p .claude/commands/{testing,deployment,git,onboarding}

# Personal commands
mkdir -p ~/.claude/commands/{code-analysis,documentation,productivity,learning}
```

### Step 6: Handle Command Conflicts and Precedence

Manage situations where project and personal commands have the same name.

**Example conflict scenario:**
- Project command: `.claude/commands/review.md` (team's code review workflow)
- Personal command: `~/.claude/commands/review.md` (your personal review checklist)

**Resolution strategies:**

**Strategy 1: Use explicit namespacing**
```bash
/project:review    # Always invokes the project command
/review            # Invokes personal command (higher precedence)
```

**Strategy 2: Rename for clarity**
```bash
# Project: .claude/commands/team-review.md
/project:team-review

# Personal: ~/.claude/commands/my-review.md
/my-review
```

**Strategy 3: Use subdirectories**
```bash
# Project: .claude/commands/workflows/review.md
/project:workflows/review

# Personal: ~/.claude/commands/personal/review.md
/personal/review
```

**Check available commands:**
Type `/help` in Claude Code to see all available commands with their scopes indicated as `(project)` or without label (personal/built-in).

### Step 7: Create a Migration Strategy

Move commands between scopes as they mature or as needs change.

**Migrating personal → project (promoting a command):**

When a personal command proves valuable for the whole team:

```bash
# 1. Copy command from personal to project
cp ~/.claude/commands/my-workflow.md .claude/commands/team-workflow.md

# 2. Generalize the command (remove personal preferences)
# Edit .claude/commands/team-workflow.md to remove personal specifics

# 3. Document the command
# Add usage examples and team-specific context

# 4. Commit to version control
git add .claude/commands/team-workflow.md
git commit -m "Add team workflow command (promoted from personal)"

# 5. Announce to team
# Let team know about the new shared command

# 6. Optionally remove or rename personal version
rm ~/.claude/commands/my-workflow.md
# OR keep both with different purposes
```

**Migrating project → personal (localizing a command):**

When you want to customize a project command for personal use:

```bash
# 1. Copy command from project to personal
cp .claude/commands/workflow.md ~/.claude/commands/my-workflow.md

# 2. Customize for your needs
# Edit ~/.claude/commands/my-workflow.md with personal preferences

# 3. Use explicit namespacing to access original
# Use /project:workflow for team version
# Use /my-workflow for personal version
```

### Step 8: Implement Advanced Configuration with Frontmatter

Use YAML frontmatter to configure command behavior and metadata.

**Example with frontmatter** (`.claude/commands/auto-fix.md`):
```markdown
---
description: Automatically detect and fix common code issues
disable-model-invocation: false
allowed-tools: ["Bash", "Edit", "Read", "Grep"]
---

# Automatic Code Fixer

Please scan the codebase for common issues and fix them automatically:

1. **Linting errors**: Run linter and auto-fix where possible
2. **Formatting**: Apply consistent code formatting
3. **Type errors**: Fix obvious TypeScript type issues
4. **Import optimization**: Remove unused imports, organize imports
5. **Dead code**: Identify and remove unreachable code

Provide a summary of changes made.
```

**Frontmatter options:**

- `description`: Brief description shown in command listings (recommended for all commands)
- `disable-model-invocation`: Set to `true` to prevent Claude from auto-invoking this command
- `allowed-tools`: Specify which tools the command can use

**Best practices for frontmatter:**
```markdown
---
description: Clear, concise description (used by Claude for context)
---

# Command Title

[Command content...]
```

### Step 9: Document Your Command Library

Create documentation to help team members and your future self.

**Project documentation** (`.claude/COMMANDS.md`):
```markdown
# Custom Slash Commands

This project includes custom slash commands to streamline common workflows.

## Available Commands

### Feature Development
- `/project:feature-workflow <name>` - Complete feature development workflow
- `/project:feature-test <name>` - Generate comprehensive tests for a feature

### Code Review
- `/project:pr-review <number>` - Perform thorough PR review with team checklist
- `/project:self-review` - Pre-submission self-review checklist

### Deployment
- `/project:deployment/staging` - Deploy current branch to staging
- `/project:deployment/production <version>` - Production deployment workflow
- `/project:deployment/rollback <version>` - Rollback to previous version

### Testing
- `/project:testing/unit <file>` - Generate unit tests
- `/project:testing/integration <module>` - Create integration tests
- `/project:testing/e2e <feature>` - End-to-end test scenarios

## Usage Examples

### Starting a new feature
```
/project:feature-workflow user-authentication
```

### Reviewing a pull request
```
/project:pr-review 123
```

### Running full test suite
```
/project:testing/run-all
```

## Contributing New Commands

1. Create command in `.claude/commands/` or appropriate subdirectory
2. Use descriptive naming with hyphens (e.g., `analyze-performance.md`)
3. Include frontmatter with `description` field
4. Test thoroughly before committing
5. Update this documentation
6. Submit PR with clear description of command purpose
```

**Personal documentation** (`~/.claude/PERSONAL-COMMANDS.md`):
```markdown
# My Personal Claude Code Commands

Quick reference for my personal slash command library.

## Daily Workflows
- `/productivity/morning` - Morning development checklist
- `/productivity/summary` - Generate daily work summary
- `/productivity/review` - End-of-day review and planning

## Code Analysis
- `/code-analysis/explain <target>` - Detailed code explanation
- `/code-analysis/optimize <file>` - Performance optimization suggestions
- `/code-analysis/security <module>` - Security vulnerability scan

## Documentation
- `/documentation/api-doc <endpoint>` - Generate API documentation
- `/documentation/readme <section>` - Create README section

## Learning
- `/learning/research <topic>` - Research and summarize topic
- `/learning/examples <concept>` - Find practical code examples
```

### Step 10: Maintain and Evolve Your Command Library

Establish practices for keeping both command scopes well-maintained.

**Regular maintenance tasks:**

**For project commands:**
```bash
# Monthly review checklist
1. Review command usage (which are being used?)
2. Update commands as project evolves
3. Archive or remove obsolete commands
4. Gather team feedback on command effectiveness
5. Update documentation

# Version control practices
git log .claude/commands/  # Review command history
git diff .claude/commands/ # Check pending changes
```

**For personal commands:**
```bash
# Quarterly review
1. Identify rarely used commands (candidates for removal)
2. Consolidate similar commands
3. Update commands based on new patterns learned
4. Consider promoting valuable commands to project scope
5. Backup command directory

# Backup personal commands
cp -r ~/.claude/commands ~/backups/claude-commands-$(date +%Y%m%d)
```

**Evolution strategies:**

**Iterative improvement:**
```markdown
# Version 1: Basic command
Analyze: $ARGUMENTS

# Version 2: Add structure
Analyze: $ARGUMENTS
1. Code quality
2. Performance
3. Security

# Version 3: Add context and examples
Analyze: $ARGUMENTS
[Detailed workflow with examples]
Include specific tools: npm test, eslint
Provide actionable recommendations
```

**Command lifecycle:**
1. **Experiment** in personal commands
2. **Stabilize** through iteration and refinement
3. **Promote** to project commands when team-valuable
4. **Maintain** and update as project evolves
5. **Archive** when no longer relevant

## Expected Results

After implementing this command organization strategy, you should have:

1. **Clear command separation**: Project commands for team workflows, personal commands for individual productivity
2. **No naming conflicts**: Strategic use of namespacing prevents command collisions
3. **Easy command discovery**: Type `/help` to see all available commands with scope indicators
4. **Team synchronization**: All team members have identical project commands after pulling from git
5. **Personal efficiency**: Your personal commands are available across all projects
6. **Organized structure**: Related commands grouped in logical subdirectories
7. **Documentation**: Clear reference for what commands exist and how to use them

**Example command listing in `/help`:**
```
Available commands:
  /project:feature-workflow <name>     (project) - Feature development workflow
  /project:pr-review <number>          (project) - Comprehensive PR review
  /morning                             - Personal morning checklist
  /explain <target>                    - Detailed code explanation
  /project:testing/unit <file>         (project) - Generate unit tests
  /productivity/summary                - Generate work summary
```

**Expected workflow:**
```bash
# Team member joins project
git clone <repository>
cd project
# Automatically has access to all /project:* commands

# Developer uses personal command across projects
cd ~/any-project
# Can use /morning, /explain, etc. in any project

# Command invocation
/project:feature-workflow auth-system    # Uses team workflow
/explain src/auth/login.js              # Uses personal explanation command
```

## Troubleshooting

### Personal Commands Not Available Across Projects

**Problem**: Personal commands created in `~/.claude/commands/` don't appear in Claude Code Web sessions.

**Solution**:
- Verify the directory path is exactly `~/.claude/commands/` (expand `~` to full home path if needed)
- Check file permissions: `chmod 644 ~/.claude/commands/*.md`
- Ensure files have `.md` extension
- Restart Claude Code Web session or refresh the browser
- Note: Some Claude Code Web environments may have restricted access to user home directories

### Project Commands Not Syncing to Team

**Problem**: Team members don't see project commands after pulling from git.

**Solution**:
- Verify `.claude/commands/` is committed to version control: `git status`
- Check that `.claude/` is not in `.gitignore`
- Ensure team members have pulled latest changes: `git pull`
- Confirm commands are at repository root in `.claude/commands/` (not in subdirectories of project)
- Team members may need to refresh their Claude Code Web session

### Command Name Conflicts

**Problem**: Personal and project commands have the same name, causing confusion.

**Solution**:
- Use explicit namespacing: `/project:command-name` for project commands
- Rename one of the commands for clarity
- Use subdirectories to create unique namespaces
- Document the precedence order (personal > project > built-in)
- Establish team naming conventions to avoid conflicts

### Commands Not Appearing in /help

**Problem**: Newly created commands don't show up in the `/help` listing.

**Solution**:
- Verify file has `.md` extension
- Check file is in correct directory (`.claude/commands/` or `~/.claude/commands/`)
- Ensure filename doesn't contain spaces or special characters
- Add frontmatter with `description` field (recommended):
  ```markdown
  ---
  description: Brief command description
  ---
  ```
- Refresh Claude Code Web session or reconnect repository
- For project commands, ensure `.claude` directory is at repository root

### Difficulty Choosing Between Scopes

**Problem**: Uncertain whether a command should be project-specific or personal.

**Solution**:
- **Default to personal**: Start in personal scope for experimentation
- **Ask these questions**:
  - Will this command work identically across all my projects? → Personal
  - Does this command encode team-specific workflows? → Project
  - Should this command evolve with the project over time? → Project
  - Is this command tied to project structure/tools? → Project
  - Do I want this command's history version-controlled? → Project
  - Is this command based on my personal preferences? → Personal
- **Migrate later**: You can always move commands between scopes as needs evolve

### Lost Personal Commands When Switching Machines

**Problem**: Personal commands don't transfer to new development machines.

**Solution**:
- **Backup strategy**: Store personal commands in a private git repository
  ```bash
  cd ~/.claude/commands
  git init
  git remote add origin <private-repo-url>
  git add .
  git commit -m "Backup personal commands"
  git push -u origin main
  ```
- **Sync across machines**: Clone personal commands repository on new machine
  ```bash
  git clone <private-repo-url> ~/.claude/commands
  ```
- **Use dotfiles management**: Include `~/.claude/` in dotfiles repository
- **Cloud sync**: Use cloud storage (Dropbox, Google Drive) with symlink
  ```bash
  ln -s ~/Dropbox/claude-commands ~/.claude/commands
  ```

## Additional Tips

- **Start with personal, promote to project**: Develop commands personally, then share valuable ones with the team
- **Establish naming conventions**: Agree on team standards for command naming (e.g., `verb-noun.md` format)
- **Version control command evolution**: Use git commits to document why commands changed
- **Create command templates**: Maintain template files for common command patterns
- **Regular audits**: Review command libraries quarterly to remove obsolete commands
- **Share discovery**: When you create a useful personal command, consider if the team would benefit
- **Document command provenance**: Note where commands came from (original author, inspiration source)
- **Use consistent structure**: Maintain similar formatting across all commands for easier maintenance
- **Consider command composition**: Create simple, focused commands that can be chained together
- **Protect sensitive information**: Never commit personal commands containing credentials or sensitive data
- **Backup personal commands**: Regularly backup `~/.claude/commands/` to prevent data loss
- **Test before sharing**: Thoroughly test project commands before committing to avoid breaking team workflows
- **Monitor command usage**: Track which commands are actually being used to inform maintenance decisions

## Related Articles
- KB-068: How to create your first custom slash command in .claude/commands/
- KB-069: How to write slash commands as Markdown files
- KB-070: How to use the $ARGUMENTS keyword in slash commands
- KB-071: How to organize slash commands with namespacing and directories
- KB-073: How to share commonly used slash commands with your team

## Sources
- Slash commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- Claude Code Tips & Tricks: Custom Slash Commands - https://cloudartisan.com/posts/2025-04-14-claude-code-tips-slash-commands/ (Accessed: November 16, 2025)
- Awesome Claude Code Resources - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code project-specific vs personal slash commands best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
