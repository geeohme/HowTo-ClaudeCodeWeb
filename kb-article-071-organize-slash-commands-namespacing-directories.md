# How to organize slash commands with namespacing and directories

**Article ID**: KB-071
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 15 minutes

## Overview
As your library of custom slash commands grows, organizing them into a logical structure becomes essential for maintainability and discoverability. This guide teaches you how to use subdirectories and namespacing to organize slash commands effectively, manage command scope between project and personal levels, and implement naming conventions that scale with your team's workflows.

## Prerequisites
- Familiarity with creating custom slash commands in Claude Code Web
- Understanding of the `.claude/commands/` directory structure
- Basic knowledge of file system organization
- Recommended: KB-068: How to create your first custom slash command
- Recommended: KB-069: How to write slash commands as Markdown files

## Steps

### Step 1: Understand Command Scope and Locations

Claude Code Web supports two distinct locations for storing slash commands, each serving different organizational needs:

**Project-level commands** (`.claude/commands/`):
- Stored in your repository at `.claude/commands/`
- Shared with your entire team via version control
- Specific to the current project
- Show `(project)` label in `/help` output
- Ideal for project-specific workflows and team standards

**User-level commands** (`~/.claude/commands/`):
- Stored in your home directory at `~/.claude/commands/`
- Available across all your projects
- Personal to your user account
- Show `(user)` label in `/help` output
- Perfect for personal productivity tools and cross-project utilities

**Example structure:**
```
# Project commands
/path/to/project/.claude/commands/
├── review-code.md
├── fix-bug.md
└── deploy.md

# User commands
~/.claude/commands/
├── quick-fix.md
├── personal-notes.md
└── analyze.md
```

### Step 2: Create a Directory Organization Strategy

Before creating subdirectories, plan your organization strategy based on your team's needs. Common organizational approaches include:

**By functional area:**
```
.claude/commands/
├── testing/
│   ├── unit-test.md
│   ├── integration-test.md
│   └── e2e-test.md
├── documentation/
│   ├── api-doc.md
│   ├── readme.md
│   └── changelog.md
└── deployment/
    ├── staging.md
    └── production.md
```

**By development phase:**
```
.claude/commands/
├── planning/
│   ├── create-ticket.md
│   └── estimate.md
├── development/
│   ├── scaffold.md
│   └── implement.md
└── review/
    ├── code-review.md
    └── pr-summary.md
```

**By technology stack:**
```
.claude/commands/
├── frontend/
│   ├── component.md
│   └── style.md
├── backend/
│   ├── api.md
│   └── database.md
└── infrastructure/
    ├── docker.md
    └── kubernetes.md
```

### Step 3: Create Subdirectory Structure

Create your chosen directory structure using standard file system commands:

```bash
# Navigate to your project root
cd /path/to/your/project

# Create organized command structure
mkdir -p .claude/commands/git
mkdir -p .claude/commands/testing
mkdir -p .claude/commands/refactoring
mkdir -p .claude/commands/documentation
```

For user-level organization:

```bash
# Create personal command structure
mkdir -p ~/.claude/commands/productivity
mkdir -p ~/.claude/commands/analysis
mkdir -p ~/.claude/commands/utilities
```

**Verification:**
```bash
# Verify project structure
ls -R .claude/commands/

# Verify user structure
ls -R ~/.claude/commands/
```

### Step 4: Understand Namespacing Syntax and Current Limitations

Claude Code Web's namespacing feature allows you to organize commands hierarchically, though there are important considerations to understand:

**Documented namespacing behavior:**
According to official documentation, commands in subdirectories should be accessible using the format:
```
/[scope]:[subdirectory]:[command]
```

**Example:**
- File: `.claude/commands/frontend/component.md`
- Expected command: `/project:frontend:component`

**Current state (as of November 2025):**
There is a known issue (GitHub Issue #2422) where the documented namespacing syntax does not work consistently. Commands placed in subdirectories may:
- Not appear in autocomplete with the namespace prefix
- Return "command not found" when invoked with namespace syntax
- Work only when invoked by the base filename

**Practical workaround:**
Until the namespacing feature is fully stable, subdirectories primarily serve as organizational tools that:
- Keep related commands grouped logically in the file system
- Show the subdirectory name in command descriptions when listed via `/help`
- Help prevent command name conflicts by providing visual separation

### Step 5: Create Namespaced Commands with Descriptive Names

While working around namespacing limitations, use descriptive filenames that include context:

**Instead of relying on directory namespace:**
```
.claude/commands/frontend/component.md  → /component
.claude/commands/backend/component.md   → /component (CONFLICT!)
```

**Use descriptive filenames:**
```
.claude/commands/frontend/frontend-component.md  → /frontend-component
.claude/commands/backend/backend-component.md    → /backend-component
```

**Example command creation:**
```bash
# Git-related commands with explicit prefixes
touch .claude/commands/git/git-review-pr.md
touch .claude/commands/git/git-commit-fix.md
touch .claude/commands/git/git-branch-cleanup.md

# Testing commands with explicit prefixes
touch .claude/commands/testing/test-unit.md
touch .claude/commands/testing/test-integration.md
touch .claude/commands/testing/test-coverage.md
```

### Step 6: Implement a Naming Convention

Establish and document a team-wide naming convention for slash commands:

**Recommended naming patterns:**

**Prefix-based naming:**
```
git-*      → Git operations (/git-review-pr, /git-commit)
test-*     → Testing operations (/test-unit, /test-e2e)
doc-*      → Documentation (/doc-api, /doc-readme)
deploy-*   → Deployment (/deploy-staging, /deploy-prod)
refactor-* → Refactoring (/refactor-extract, /refactor-rename)
```

**Action-object naming:**
```
/create-component
/analyze-performance
/review-security
/generate-tests
/update-dependencies
```

**Verb-first clarity:**
```
/fix-[target]      → /fix-bug, /fix-typo
/create-[target]   → /create-feature, /create-test
/analyze-[target]  → /analyze-code, /analyze-logs
/generate-[target] → /generate-docs, /generate-schema
```

**Document your convention in `.claude/CLAUDE.md`:**
```markdown
## Slash Command Naming Convention

Our team follows these naming patterns:

- `git-*`: Git and version control operations
- `test-*`: Testing and quality assurance
- `doc-*`: Documentation generation
- `deploy-*`: Deployment operations
- `refactor-*`: Code refactoring tasks

Examples:
- `/git-review-pr` - Comprehensive pull request review
- `/test-unit` - Generate unit tests for specified file
- `/doc-api` - Generate API documentation
```

### Step 7: Organize Commands by Access Scope

Decide which commands belong at the project level versus user level:

**Project-level candidates (`.claude/commands/`):**
- Team workflows and standards
- Project-specific deployment procedures
- Code review checklists aligned with team guidelines
- Technology stack-specific commands
- Project architecture patterns

**User-level candidates (`~/.claude/commands/`):**
- Personal productivity shortcuts
- Cross-project utilities
- Your preferred code style helpers
- Personal note-taking templates
- Generic analysis tools

**Example implementation:**

```bash
# Project-level: Team code review standard
cat > .claude/commands/review/team-code-review.md << 'EOF'
# Team Code Review Checklist

Review the code according to our team standards:

1. **Architecture**: Follows our microservices patterns
2. **Testing**: Includes unit tests with >80% coverage
3. **Documentation**: JSDoc comments on all public APIs
4. **Security**: No hardcoded credentials or secrets
5. **Performance**: No N+1 queries or obvious bottlenecks

Provide actionable feedback with specific line references.
EOF

# User-level: Personal quick analysis
cat > ~/.claude/commands/utilities/quick-analyze.md << 'EOF'
Perform a quick analysis of $ARGUMENTS focusing on:
- Obvious bugs or errors
- Code smell detection
- Quick improvement suggestions

Keep it concise and actionable.
EOF
```

### Step 8: Create a Command Index and Documentation

Maintain a reference document that lists all available commands for your team:

**Create `.claude/commands/README.md`:**
```markdown
# Project Slash Commands

## Git Operations (`git/` directory)
- `/git-review-pr` - Comprehensive pull request review with checklist
- `/git-commit-fix` - Generate proper conventional commit message
- `/git-branch-cleanup` - Identify and remove stale branches

## Testing (`testing/` directory)
- `/test-unit` - Generate unit tests for specified file
- `/test-integration` - Create integration test scenarios
- `/test-coverage` - Analyze and improve test coverage

## Documentation (`documentation/` directory)
- `/doc-api` - Generate API documentation from code
- `/doc-readme` - Update README with current project state
- `/doc-changelog` - Generate changelog from git history

## Usage Examples

```
# Review pull request #123
/git-review-pr 123

# Generate unit tests for auth module
/test-unit src/auth/login.js

# Create API documentation
/doc-api
```

## Adding New Commands

1. Create `.md` file in appropriate subdirectory
2. Use descriptive filename with category prefix
3. Update this README with command description
4. Test command before committing
```

**Commit the index:**
```bash
git add .claude/commands/README.md
git commit -m "Add slash commands index and documentation"
```

### Step 9: Manage Command Conflicts and Precedence

When commands with the same name exist in multiple locations, understand the resolution order:

**Current conflict behavior:**
- If a user-level and project-level command have the same name, you may need to use the prefixed version
- `/command-name` typically resolves to the most specific scope
- Use `/project:command-name` or `/user:command-name` to disambiguate

**Avoid conflicts proactively:**

```bash
# Check for naming conflicts before creating new commands
ls .claude/commands/**/*.md | xargs -n1 basename | sort | uniq -d
ls ~/.claude/commands/**/*.md | xargs -n1 basename | sort | uniq -d

# List all commands to identify potential conflicts
# In Claude Code Web:
/help
```

**Handle conflicts explicitly:**
```markdown
<!-- If you have both: -->
<!-- .claude/commands/analyze.md (project) -->
<!-- ~/.claude/commands/analyze.md (user) -->

<!-- Invoke specifically: -->
/project:analyze   # Uses project version
/user:analyze      # Uses user version
```

### Step 10: Version Control Best Practices

Implement version control practices for your command library:

**Create `.gitignore` exceptions if needed:**
```bash
# In your project's .gitignore

# Keep .claude directory structure
!.claude/
!.claude/commands/
!.claude/commands/**/*.md

# But ignore personal notes or local-only commands if any
.claude/commands/local/
.claude/commands/personal/
```

**Commit commands with meaningful messages:**
```bash
# Add all commands in a directory
git add .claude/commands/testing/
git commit -m "Add testing slash commands for unit and integration tests"

# Document breaking changes
git add .claude/commands/deployment/
git commit -m "Update deployment commands: Changed /deploy argument order

BREAKING CHANGE: /deploy-prod now requires environment as first argument:
  Old: /deploy-prod v1.2.3 us-east-1
  New: /deploy-prod us-east-1 v1.2.3"
```

**Use pull requests for command changes:**
```bash
# Create feature branch for new commands
git checkout -b add-refactoring-commands

# Add commands
mkdir -p .claude/commands/refactoring
touch .claude/commands/refactoring/refactor-extract-method.md

# Commit and push
git add .claude/commands/refactoring/
git commit -m "Add refactoring slash commands"
git push -u origin add-refactoring-commands

# Create PR for team review
gh pr create --title "Add refactoring slash commands" \
  --body "Adds /refactor-extract-method and /refactor-rename commands"
```

## Expected Results

After implementing organized slash command structure, you should observe:

1. **Clear command organization in file system:**
   ```
   .claude/commands/
   ├── README.md
   ├── git/
   │   ├── git-review-pr.md
   │   ├── git-commit-fix.md
   │   └── git-branch-cleanup.md
   ├── testing/
   │   ├── test-unit.md
   │   ├── test-integration.md
   │   └── test-coverage.md
   └── documentation/
       ├── doc-api.md
       └── doc-readme.md
   ```

2. **Discoverable commands in `/help` output:**
   ```
   Available commands:
   /git-review-pr - Comprehensive PR review (project:git)
   /git-commit-fix - Generate commit message (project:git)
   /test-unit - Generate unit tests (project:testing)
   /test-coverage - Analyze coverage (project:testing)
   /doc-api - Generate API docs (project:documentation)
   ```

3. **Clear command naming that indicates purpose:**
   - `/git-review-pr` immediately indicates git-related PR review
   - `/test-unit` clearly indicates unit testing operation
   - `/doc-api` obviously generates API documentation

4. **Reduced command name conflicts:**
   - Descriptive names prevent accidental overwrites
   - Directory organization provides visual grouping
   - README documents all available commands

5. **Team-wide consistency:**
   - All team members see the same commands
   - Workflows standardized across the project
   - New team members discover commands via `/help` and README

## Troubleshooting

### Common Issue 1: Namespaced Commands Not Working

**Problem**: Commands in subdirectories cannot be invoked using `/namespace:command` syntax; getting "command not found" error.

**Solution**:
- This is a known limitation (GitHub Issue #2422) as of November 2025
- **Workaround**: Use descriptive filenames instead of relying on directory-based namespacing
  ```bash
  # Instead of:
  .claude/commands/frontend/component.md  → /frontend:component (broken)

  # Use:
  .claude/commands/frontend/frontend-component.md  → /frontend-component (works)
  ```
- Monitor the Claude Code GitHub repository for updates on namespacing support
- Subdirectories still provide organizational benefits and appear in descriptions

### Common Issue 2: Command Conflicts Between Project and User Level

**Problem**: Same command name exists in both `.claude/commands/` and `~/.claude/commands/`, causing confusion about which one executes.

**Solution**:
- Use explicit scope prefixes to invoke specific version:
  ```
  /project:analyze    # Explicitly use project version
  /user:analyze       # Explicitly use user version
  ```
- Rename one of the commands to avoid conflict entirely
- Review your user-level commands and remove duplicates:
  ```bash
  # List user commands
  ls -la ~/.claude/commands/**/*.md

  # List project commands
  ls -la .claude/commands/**/*.md

  # Identify and resolve conflicts
  ```

### Common Issue 3: Commands Not Appearing After Adding to Subdirectory

**Problem**: Created command in subdirectory but it doesn't show up in `/help` or can't be invoked.

**Solution**:
- Verify the file has `.md` extension: `command.md` not `command.txt`
- Check file permissions are readable:
  ```bash
  chmod 644 .claude/commands/subdirectory/command.md
  ```
- Ensure there are no typos in the directory path
- Refresh Claude Code Web session or start new conversation
- Verify the command file has content (empty files may not register)

### Common Issue 4: Team Members Not Seeing New Commands

**Problem**: You added commands to `.claude/commands/` but team members don't see them.

**Solution**:
- Ensure commands are committed to version control:
  ```bash
  git status .claude/commands/
  git add .claude/commands/
  git commit -m "Add new slash commands"
  git push
  ```
- Verify `.claude/` directory is not in `.gitignore`
- Ask team members to pull latest changes:
  ```bash
  git pull origin main
  ```
- Team members may need to refresh their Claude Code Web session

### Common Issue 5: Difficulty Managing Large Command Library

**Problem**: Too many commands making it hard to find the right one.

**Solution**:
- Implement more specific subdirectory organization
- Create a searchable README.md in `.claude/commands/`
- Use consistent naming prefixes for related commands
- Archive rarely-used commands to a separate directory:
  ```bash
  mkdir .claude/commands/archived/
  git mv .claude/commands/old-command.md .claude/commands/archived/
  ```
- Consider splitting very large command sets across multiple subdirectories with clear categories

## Additional Tips

- **Start simple, then organize**: Begin with flat command structure, reorganize as library grows

- **Use command templates**: Create template files for common command patterns
  ```bash
  # Template for new commands
  cp .claude/commands/templates/basic-command.md .claude/commands/new-command.md
  ```

- **Document usage in command files**: Include usage instructions as comments
  ```markdown
  <!-- Usage: /git-review-pr <pr-number> -->
  <!-- Example: /git-review-pr 456 -->

  Review pull request #$ARGUMENTS with our team checklist...
  ```

- **Regular command audits**: Periodically review and clean up unused commands
  ```bash
  # Review commands last modified more than 6 months ago
  find .claude/commands/ -name "*.md" -mtime +180 -ls
  ```

- **Alphabetize within directories**: Keep commands alphabetically sorted for easier browsing
  ```bash
  .claude/commands/git/
  ├── git-branch-cleanup.md
  ├── git-commit-fix.md
  └── git-review-pr.md
  ```

- **Consider command categories in naming**: Group related commands with common prefixes
  ```
  /git-*     → All git operations
  /test-*    → All testing operations
  /deploy-*  → All deployment operations
  ```

- **Create index commands**: Build meta-commands that list other related commands
  ```markdown
  # .claude/commands/help-git.md

  Here are available git-related commands:
  - /git-review-pr <number> - Comprehensive PR review
  - /git-commit-fix - Generate conventional commit message
  - /git-branch-cleanup - Clean up stale branches

  Type any command for more details or use it directly.
  ```

- **Share organizational patterns**: Document your organizational strategy in project documentation
  ```markdown
  # In .claude/CLAUDE.md

  ## Slash Commands Organization

  We organize commands by functional area:
  - `git/` - Version control operations
  - `testing/` - Test generation and execution
  - `documentation/` - Docs generation
  - `deployment/` - Deployment workflows

  Use descriptive names with category prefixes (e.g., `/git-review-pr`).
  ```

- **Leverage both scopes strategically**: Use project-level for team standards, user-level for personal productivity

- **Monitor for namespacing updates**: Check Claude Code release notes for improvements to namespacing feature

## Related Articles
- KB-068: How to create your first custom slash command in .claude/commands/
- KB-069: How to write slash commands as Markdown files
- KB-070: How to use the $ARGUMENTS keyword in slash commands
- KB-072: How to create project-specific vs personal slash commands
- KB-073: How to share commonly used slash commands with your team

## Sources
- Slash commands - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
- Slash Command Namespacing Bug - GitHub Issue #2422 - https://github.com/anthropics/claude-code/issues/2422 (Accessed: November 16, 2025)
- Claude Code Custom Slash Commands Hierarchy - https://www.danielcorin.com/til/anthropic/custom-slash-commands-hierarchy/ (Accessed: November 16, 2025)
- Claude Code Tips & Tricks: Custom Slash Commands - https://cloudartisan.com/posts/2025-04-14-claude-code-tips-slash-commands/ (Accessed: November 16, 2025)
- Claude Code Best Practices - Anthropic Engineering - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Google Search: "Claude Code slash commands subdirectory organization best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
