# How to have Claude Code automatically name branches following conventions

**Article ID**: KB-032
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview
While Claude Code Web automatically creates branches with a default naming pattern, you can configure it to follow your team's specific branch naming conventions by using CLAUDE.md configuration files and custom slash commands. This ensures consistency with existing workflows, ticket tracking systems, and team standards like "feature/TICKET-123-description" or "fix/issue-456-bug-name."

## Prerequisites
- Connected GitHub repository (KB-004)
- Understanding of branch creation basics (KB-031)
- Familiarity with your team's branch naming conventions
- Basic knowledge of Markdown formatting
- Repository write access to add configuration files

## Understanding Branch Naming Options

### Default Claude Naming Pattern

**Out of the box**, Claude creates branches like:
```
claude/add-user-authentication-a1b2c3
claude/fix-calculation-bug-x7y8z9
claude/refactor-api-endpoints-m4n5p6
```

**Pattern**: `claude/[task-description]-[unique-id]`

### Common Team Conventions

**Teams often use structured patterns like:**

```
feature/PROJ-123-add-user-login
fix/PROJ-456-resolve-null-pointer
hotfix/PROJ-789-security-patch
refactor/improve-database-queries
docs/update-api-documentation
```

**Pattern Components:**
- **Type prefix**: `feature/`, `fix/`, `hotfix/`, `refactor/`, `docs/`
- **Ticket reference**: `PROJ-123`, `JIRA-456`, `GH-789` (optional)
- **Description**: `add-user-login`, `resolve-null-pointer`

## Steps

### Step 1: Create a CLAUDE.md Configuration File

CLAUDE.md serves as Claude's instruction manual for your project, including branch naming conventions.

**Create the file in your repository root:**

1. Navigate to your repository on GitHub or locally
2. Create a new file named `CLAUDE.md` in the root directory
3. This file will be version-controlled and shared with your team

**Basic CLAUDE.md Template:**

```markdown
# Claude Code Configuration

## Project Overview
[Brief description of your project]

## Git Workflow

### Branch Naming Convention

**Pattern**: `[type]/[ticket]-[description]`

**Types:**
- `feature/` - New features or enhancements
- `fix/` - Bug fixes
- `hotfix/` - Urgent production fixes
- `refactor/` - Code improvements without functionality changes
- `docs/` - Documentation updates
- `test/` - Test additions or modifications

**Ticket Format**: `PROJ-###` (e.g., PROJ-123)

**Description Format**:
- Use lowercase
- Separate words with hyphens
- Keep concise (max 50 characters)
- Be descriptive and specific

**Examples:**
```
feature/PROJ-123-add-user-authentication
fix/PROJ-456-resolve-checkout-crash
hotfix/PROJ-789-patch-security-vulnerability
refactor/improve-database-connection-pooling
docs/update-api-endpoint-documentation
test/add-payment-integration-tests
```

**Base Branch**: Always branch from `main` (or `develop` for development)

### Commit Message Format

Follow Conventional Commits:
```
type(scope): message

Types: feat, fix, chore, docs, refactor, test, style
```

## Repository Etiquette

- Always create a new branch for each task
- Never commit directly to `main` or `develop`
- Ensure all tests pass before creating PR
- Request review from at least one team member
```

**Save and commit this file to your repository.**

### Step 2: Structure Your Task Descriptions

Claude derives branch names from your task descriptions, so structure them to match your conventions.

**Task Description Format:**

```
[Type]: [Ticket] - [Clear Description]
```

**Examples:**

**For Feature Branches:**
```
✓ "Feature: PROJ-123 - Add user authentication with email validation"
  → Claude will create: feature/PROJ-123-add-user-authentication

✓ "Feature: Add payment gateway integration for Stripe"
  → feature/add-payment-gateway-integration-stripe
```

**For Bug Fixes:**
```
✓ "Fix: PROJ-456 - Resolve null pointer exception in checkout process"
  → fix/PROJ-456-resolve-null-pointer-exception

✓ "Fix: Issue #789 - Memory leak in image processing module"
  → fix/issue-789-memory-leak-image-processing
```

**For Refactoring:**
```
✓ "Refactor: Database connection pooling for better performance"
  → refactor/database-connection-pooling-better-performance
```

**For Documentation:**
```
✓ "Docs: Update API documentation for v2 endpoints"
  → docs/update-api-documentation-v2-endpoints
```

### Step 3: Reference Naming Conventions in Task Instructions

When starting a task, explicitly remind Claude of your naming conventions.

**Include in your task description:**

```
Please follow the branch naming convention specified in CLAUDE.md.

Task: Feature: PROJ-234 - Add user profile settings page

Create a branch following our pattern: feature/PROJ-234-add-user-profile-settings

[Additional task details...]
```

**Alternative approach - Prepend to task:**

```
Following our CLAUDE.md conventions for branch naming (feature/TICKET-description):

Add a user profile settings page that allows users to update their email, password, and notification preferences. This is for ticket PROJ-234.
```

### Step 4: Create Custom Slash Commands (Advanced)

For teams with strict naming requirements, create custom slash commands that enforce conventions.

**Create a slash command file:**

**Location**: `.claude/commands/new-branch.md`

```markdown
You are creating a new feature branch following our team's strict naming convention.

## Branch Naming Rules

1. **Format**: `[type]/[ticket]-[description]`
2. **Types**: feature, fix, hotfix, refactor, docs, test
3. **Ticket**: Must be in format PROJ-### (e.g., PROJ-123)
4. **Description**: lowercase, hyphen-separated, max 50 chars

## Process

Ask the user for:
1. Branch type (feature/fix/hotfix/refactor/docs/test)
2. Ticket number (e.g., PROJ-123)
3. Short description

Then:
1. Validate the inputs match our convention
2. Create branch name: `[type]/[ticket]-[description]`
3. Verify with user before creating
4. Create the branch from main
5. Confirm branch creation with `git branch --show-current`

## Example

User inputs:
- Type: feature
- Ticket: PROJ-456
- Description: add shopping cart functionality

Result: `feature/PROJ-456-add-shopping-cart-functionality`
```

**Usage:**
```
/new-branch
```

Claude will then prompt you for the required information and create a properly named branch.

### Step 5: Create an Issue-Based Workflow (Advanced)

For GitHub issue integration, create a command that reads issues and creates branches automatically.

**Location**: `.claude/commands/process-issue.md`

```markdown
You are processing a GitHub issue and creating a properly named feature branch.

## Workflow

1. **Read the Issue**
   - Ask user for issue number
   - Use `gh issue view [number]` to read issue details
   - Extract title and labels

2. **Determine Branch Type**
   - Label "bug" → `fix/`
   - Label "enhancement" → `feature/`
   - Label "documentation" → `docs/`
   - Default → `feature/`

3. **Create Branch Name**
   - Format: `[type]/issue-[number]-[description]`
   - Description from issue title:
     - Convert to lowercase
     - Replace spaces with hyphens
     - Remove special characters
     - Truncate to 50 characters

4. **Verify Base Branch**
   - Ensure on `main` branch
   - Pull latest changes: `git pull origin main`

5. **Create Branch**
   - Execute: `git checkout -b [branch-name]`
   - Verify: `git branch --show-current`
   - Push: `git push -u origin [branch-name]`

6. **Confirm with User**
   - Show created branch name
   - Show issue details for context

## Example

Issue #123: "Add user authentication with OAuth"
Labels: enhancement

Result: `feature/issue-123-add-user-authentication-oauth`
```

**Usage:**
```
/process-issue 123
```

### Step 6: Verify Branch Naming in Sessions

**When starting a new task:**

1. **Check the session details** to see the created branch name
2. **Verify it matches your conventions**
3. **If incorrect**, provide feedback:

```
The branch name doesn't match our convention. Please create a new branch named:
feature/PROJ-123-add-user-authentication

Then continue with the task on that branch.
```

**Claude will:**
- Create the correctly named branch
- Switch to it
- Continue the task

### Step 7: Document Edge Cases and Variations

**Add to your CLAUDE.md:**

```markdown
## Branch Naming Edge Cases

### Multiple Related Tasks
For tasks that span multiple branches:
```
feature/PROJ-123-user-auth-backend
feature/PROJ-123-user-auth-frontend
feature/PROJ-123-user-auth-tests
```

### Emergency Hotfixes
Use `hotfix/` prefix and include severity:
```
hotfix/PROD-001-critical-security-patch
hotfix/PROD-002-urgent-payment-fix
```

### Experimental Features
Use `experiment/` prefix:
```
experiment/test-new-caching-strategy
experiment/PROJ-999-ai-recommendations
```

### Dependency Updates
Use `deps/` prefix:
```
deps/upgrade-react-v19
deps/security-patch-openssl
```
```

## Expected Results

After configuring Claude Code to follow your branch naming conventions, you should:

- **Consistent branch names** across all Claude-created branches
- **Automatic adherence** to team patterns like `feature/TICKET-123-description`
- **Integration with ticket systems** through branch names
- **Improved team collaboration** with standardized naming
- **Better branch organization** in repository branch lists
- **Easier branch identification** and purpose recognition
- **Compatibility with CI/CD** systems expecting specific patterns
- **Reduced manual branch renaming** and corrections

**Example repository branch list:**
```
feature/PROJ-123-add-user-authentication
feature/PROJ-124-implement-payment-gateway
fix/PROJ-125-resolve-login-timeout
docs/PROJ-126-update-api-documentation
refactor/improve-error-handling
test/PROJ-127-add-integration-tests
```

All created by Claude following your documented conventions.

## Troubleshooting

### Common Issue 1
**Problem**: Claude still creates branches with default `claude/` prefix
**Solution**:
- Ensure CLAUDE.md is committed to repository root
- Reference CLAUDE.md explicitly in your task description
- Include the desired branch name format in your task:
  ```
  "Following our CLAUDE.md convention, create branch: feature/PROJ-123-add-login
  Then implement user login functionality..."
  ```
- Give Claude the exact branch name to use
- Create a custom slash command for strict enforcement

### Common Issue 2
**Problem**: Branch name doesn't include ticket number
**Solution**:
- Include ticket number prominently in task description
- Format: "Feature: PROJ-123 - Add user login"
- Or explicitly state: "This is for ticket PROJ-123"
- Update CLAUDE.md to emphasize ticket number requirement
- Consider making ticket number the first element in description

### Common Issue 3
**Problem**: Branch name has incorrect type prefix
**Solution**:
- Start task description with type: "Feature:", "Fix:", "Refactor:"
- Map clearly in CLAUDE.md: Feature → feature/, Fix → fix/
- Use custom slash command that asks for type explicitly
- Provide examples of each type in CLAUDE.md
- Be explicit: "This is a bug fix, use fix/ prefix"

### Common Issue 4
**Problem**: Description part is too long or has special characters
**Solution**:
- Provide concise task descriptions (focus on action, not implementation)
- Specify in CLAUDE.md: "max 50 characters for description"
- Example: "Add user authentication" not "Add comprehensive user authentication system with email validation, password hashing, and session management"
- Remove special characters from task descriptions
- Use hyphens instead of spaces in description keywords

### Common Issue 5
**Problem**: Team uses different conventions for different project areas
**Solution**:
- Document all variations in CLAUDE.md
- Create separate slash commands for frontend/backend/docs
- Example CLAUDE.md section:
  ```markdown
  ## Area-Specific Conventions

  **Frontend**: `frontend/feature/TICKET-description`
  **Backend**: `backend/feature/TICKET-description`
  **Database**: `db/feature/TICKET-description`
  **Mobile**: `mobile/feature/TICKET-description`
  ```
- Include area prefix in task description
- Or use dedicated commands: `/frontend-feature`, `/backend-fix`

### Common Issue 6
**Problem**: Need to rename an already-created branch
**Solution**:
- If task just started, cancel and restart with correct name
- If work already done, rename manually:
  ```bash
  git branch -m old-branch-name feature/PROJ-123-new-name
  git push origin -u feature/PROJ-123-new-name
  git push origin --delete old-branch-name
  ```
- Then inform Claude: "Continue working on branch feature/PROJ-123-new-name"
- Or create PR and manually edit the source branch

### Common Issue 7
**Problem**: CI/CD pipeline expects specific branch name patterns
**Solution**:
- Document CI/CD requirements in CLAUDE.md
- Example:
  ```markdown
  ## CI/CD Requirements

  Our pipeline only runs on branches matching:
  - `feature/*` - Full test suite
  - `fix/*` - Test suite + security scan
  - `hotfix/*` - Fast-track with critical tests only

  ALWAYS use these exact prefixes.
  ```
- Test branch name with regex pattern before starting task
- Set up branch protection rules on GitHub to enforce patterns

## Additional Tips

### Integration with Project Management Tools

**Jira Integration:**
```markdown
## Branch Naming - Jira Integration

Format: `[type]/[JIRA-KEY]-[description]`

Example: `feature/PROJ-1234-add-user-dashboard`

This allows Jira to automatically link branches to tickets.
Smart commits work: `git commit -m "PROJ-1234 Add user authentication"`
```

**GitHub Issues Integration:**
```markdown
## Branch Naming - GitHub Issues

Format: `[type]/issue-[number]-[description]`

Example: `fix/issue-456-resolve-memory-leak`

GitHub automatically links when branch name contains `issue-[number]`.
```

**Linear Integration:**
```markdown
## Branch Naming - Linear Integration

Format: `[type]/[TEAM-ID]-[description]`

Example: `feature/ENG-123-implement-auth`

Linear auto-links branches matching team identifier pattern.
```

### Template for Different Project Types

**Monorepo Projects:**
```markdown
## Monorepo Branch Naming

Format: `[workspace]/[type]/[ticket]-[description]`

Examples:
- `web/feature/PROJ-123-add-dashboard`
- `api/fix/PROJ-124-resolve-timeout`
- `mobile/refactor/improve-navigation`
```

**Microservices:**
```markdown
## Microservices Branch Naming

Format: `[service]-[type]/[ticket]-[description]`

Examples:
- `auth-service-feature/PROJ-123-add-oauth`
- `payment-service-fix/PROJ-124-resolve-timeout`
- `user-service-refactor/improve-caching`
```

**Library/Package Projects:**
```markdown
## Library Branch Naming

Format: `[type]/[version]-[description]`

Examples:
- `feature/v2-add-async-support`
- `fix/v1-resolve-memory-leak`
- `docs/v2-update-migration-guide`
```

### Advanced CLAUDE.md Configuration

**Complete example with all options:**

```markdown
# Claude Code Configuration - [Project Name]

## Project Context
[Brief description, tech stack, architecture notes]

## Git Workflow

### Default Branch
- Base all new branches from: `develop`
- Never commit directly to: `main`, `develop`

### Branch Naming Convention

**Strict Format**: `[type]/[ticket]-[description]`

**Required Types** (must be exactly one of):
- `feature` - New functionality
- `fix` - Bug fixes
- `hotfix` - Production emergencies
- `refactor` - Code improvements
- `docs` - Documentation only
- `test` - Test additions
- `deps` - Dependency updates
- `ci` - CI/CD changes

**Ticket Format**:
- Required for feature/fix/hotfix
- Format: `PROJ-###` (e.g., PROJ-123)
- Optional for refactor/docs/test/deps/ci

**Description Rules**:
- Use lowercase only
- Separate words with single hyphens
- No special characters except hyphens
- Max 50 characters
- Use imperative mood (add, fix, update, not adding, fixed, updated)
- Be specific and descriptive

**Validation Regex**: `^(feature|fix|hotfix|refactor|docs|test|deps|ci)\/(PROJ-\d+-)?.+$`

**Valid Examples**:
```
✓ feature/PROJ-123-add-user-authentication
✓ fix/PROJ-456-resolve-checkout-crash
✓ hotfix/PROJ-789-patch-security-vulnerability
✓ refactor/improve-database-queries
✓ docs/update-installation-guide
✓ test/add-payment-integration-tests
✓ deps/upgrade-react-v19
✓ ci/add-docker-build-step
```

**Invalid Examples**:
```
✗ Feature/PROJ-123-Add-Auth (wrong case)
✗ feature/add auth (no hyphens)
✗ add-authentication (no type)
✗ feature/PROJ-123/add-auth (wrong separator)
✗ feature/PROJ-123-Adding_authentication (wrong tense, underscore)
```

### Commit Message Format

Follow [Conventional Commits](https://www.conventionalcommits.org/):

**Format**: `type(scope): message`

**Types**: feat, fix, chore, docs, refactor, test, style, perf, ci

**Example**: `feat(auth): add OAuth2 login flow`

**Rules**:
- Use lowercase for type and message
- Keep subject under 72 characters
- Use imperative mood
- No period at end

### Pull Request Requirements

1. Branch must follow naming convention
2. All tests must pass
3. Code coverage must not decrease
4. At least one approving review required
5. Squash merge preferred
6. Delete branch after merge

## Development Standards

### Code Style
- Follow existing patterns
- Run `npm run lint` before committing
- Format with Prettier (happens automatically)

### Testing Requirements
- Write tests for all new features
- Update tests for bug fixes
- Ensure 80%+ code coverage
- Run full suite: `npm test`

### Documentation
- Update README for new features
- Add JSDoc comments for public APIs
- Update CHANGELOG.md with changes

## Common Commands

- Run dev server: `npm run dev`
- Run tests: `npm test`
- Run tests (watch): `npm run test:watch`
- Build: `npm run build`
- Lint: `npm run lint`
- Format: `npm run format`

## Repository Structure

```
/src          - Source code
/tests        - Test files
/docs         - Documentation
/scripts      - Build and utility scripts
/.claude      - Claude configuration and commands
```

## Notes for Claude

- Always check CLAUDE.md before starting work
- Follow all naming conventions strictly
- Run tests after making changes
- Ask if conventions are unclear
- Reference ticket numbers in commits when available
```

### Best Practices for Maintaining Conventions

**Regular Review:**
- Review CLAUDE.md quarterly
- Update when team conventions change
- Get team input on improvements
- Keep examples current

**Team Alignment:**
- Share CLAUDE.md with all developers
- Include in onboarding documentation
- Reference in PR templates
- Enforce in code review

**Tooling Support:**
- Set up pre-commit hooks to validate branch names
- Configure CI to check branch naming
- Use GitHub branch protection rules
- Create PR templates that reference conventions

**Continuous Improvement:**
- Track how well conventions work
- Collect feedback from team
- Adjust based on pain points
- Document exceptions and why they exist

### Slash Command Library

**Create `.claude/commands/` directory with:**

**`new-feature.md`** - Create feature branch
**`new-fix.md`** - Create bugfix branch
**`new-hotfix.md`** - Create hotfix branch
**`new-refactor.md`** - Create refactor branch
**`new-docs.md`** - Create documentation branch
**`update-branch-name.md`** - Rename current branch to follow conventions

Each enforces your specific naming pattern with appropriate prompts.

## Related Articles
- KB-031: How to create a new branch for a specific task
- KB-014: How to understand which branch Claude Code is working on
- KB-033: How to create pull requests automatically
- KB-004: How to connect your first GitHub repository
- KB-016: How to start a new Claude Code session
- KB-006: How to create your first coding task

## Sources
1. Claude Code Best Practices - Anthropic Engineering - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
2. A practical guide to automating git workflows with Claude Code - eesel AI - https://www.eesel.ai/blog/git-workflows-claude-code (Accessed: November 16, 2025)
3. Claude Code Custom Slash Command: Process GitHub Issues - https://gist.github.com/kieronlawson/e512f4bdd24a010ba76b58682f2b22fa (Accessed: November 16, 2025)
4. Model Context Protocol Documentation CLAUDE.md - https://github.com/modelcontextprotocol/docs/blob/main/CLAUDE.md (Accessed: November 16, 2025)
5. Git Branch Naming Conventions - https://github.com/naming-convention/naming-convention-guides/blob/master/git/branch-naming.md (Accessed: November 16, 2025)
6. Claude Code for web - Official Blog - https://simonwillison.net/2025/Oct/20/claude-code-for-web/ (Accessed: November 16, 2025)
7. Conventional Commits Specification - https://www.conventionalcommits.org/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
