# How to customize PR descriptions and titles

**Article ID**: KB-035
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15-20 minutes

## Overview
Claude Code Web can create pull requests with well-structured descriptions and titles, but you may want to customize them to match your team's conventions, include specific sections, or follow standards like Conventional Commits. This guide shows you three methods to customize PR descriptions and titles: using custom slash commands, defining conventions in CLAUDE.md files, and requesting customizations directly in conversation.

## Prerequisites
- Connected GitHub repository in Claude Code Web (see KB-004)
- Basic understanding of Git branches and pull requests
- Basic knowledge of Markdown formatting
- Familiarity with your team's PR conventions or templates

## Methods for Customization

### Method 1: Ask Claude Directly in Conversation

The simplest approach is to request specific PR formatting when you ask Claude to create a pull request.

#### Step 1: Complete Your Code Changes

Work with Claude to complete the code changes you want to include in your PR as you normally would.

#### Step 2: Request a Custom PR

When you're ready to create the pull request, provide specific instructions:

**Example Request:**
```
Create a pull request with the following format:

Title: Follow conventional commits format (feat:, fix:, docs:, etc.)

Description should include:
- Summary: Brief overview of changes
- Motivation: Why these changes were needed
- Changes Made: Bulleted list of key modifications
- Testing: How the changes were tested
- Breaking Changes: Any breaking changes (if applicable)
- Related Issues: Reference to issue numbers
```

#### Step 3: Review and Refine

Claude will generate the PR based on your template. You can:
- Ask Claude to revise specific sections
- Request additions or removals
- Refine the language or formatting
- Add or remove details

**Expected Output:**
```
Title: feat: add user email validation to registration form

Description:
## Summary
Implemented comprehensive email validation for user registration to prevent
invalid email addresses from being stored in the database.

## Motivation
We were seeing malformed email addresses causing issues with our email
notification system. This validation ensures only properly formatted emails
are accepted.

## Changes Made
- Added email regex validation in registration controller
- Implemented frontend validation with real-time feedback
- Added unit tests for email validation logic
- Updated error messages for better user experience

## Testing
- Unit tests: 15 new test cases for validation edge cases
- Manual testing: Tested with 20+ invalid email formats
- Integration tests: Verified form submission with valid/invalid emails

## Breaking Changes
None

## Related Issues
Fixes #123
```

### Method 2: Create Custom Slash Commands

For repetitive PR workflows, create custom slash commands that automatically apply your preferred template.

#### Step 1: Create the Commands Directory

Your custom slash commands should be stored in your repository's `.claude/commands/` directory:

```bash
mkdir -p .claude/commands
```

#### Step 2: Create a PR Slash Command File

Create a new file `.claude/commands/create-pr.md` with your custom template:

```markdown
---
name: create-pr
description: Create a pull request with our team's standard format
---

Create a pull request following our team conventions:

**Title Format:**
Use Conventional Commits format: `type(scope): description`
- Types: feat, fix, docs, style, refactor, test, chore
- Scope: component or area affected (optional)
- Description: imperative mood, lowercase, no period

**Description Template:**

## 📋 Summary
[Provide 2-3 sentence overview of the changes]

## 🎯 Motivation and Context
[Explain why these changes are needed and what problem they solve]

## 🔧 Changes Made
- [List key changes as bullet points]
- [Be specific about what was added, modified, or removed]

## ✅ Testing Performed
- [ ] Unit tests added/updated
- [ ] Integration tests passed
- [ ] Manual testing completed
- [ ] Edge cases verified

**Test Details:**
[Describe specific tests performed]

## 💥 Breaking Changes
[List any breaking changes or write "None"]

## 📸 Screenshots
[If UI changes, add before/after screenshots or write "N/A"]

## 🔗 Related Issues
Closes #[issue-number]
Related to #[issue-number]

## 📝 Additional Notes
[Any other context, deployment notes, or considerations]

---

Before creating the PR:
1. Run tests and ensure they pass
2. Review the git diff for unintended changes
3. Verify the branch is up to date with the base branch
```

#### Step 3: Create Additional Specialized Commands

You can create variant commands for different scenarios:

**.claude/commands/hotfix-pr.md** (for urgent fixes):
```markdown
---
name: hotfix-pr
description: Create a hotfix pull request with expedited format
---

Create a HOTFIX pull request with urgency indicators:

**Title:** `🚨 HOTFIX: [brief description]`

**Description:**
## 🚨 CRITICAL FIX

**Issue:** [What was broken]

**Impact:** [Who/what was affected]

**Root Cause:** [Why it happened]

**Solution:** [How it was fixed]

**Verification:** [How we know it's fixed]

**Rollback Plan:** [How to revert if needed]

Label this PR as: `hotfix`, `urgent`
Request immediate review from: [team leads]
```

**.claude/commands/docs-pr.md** (for documentation):
```markdown
---
name: docs-pr
description: Create a documentation pull request
---

Create a documentation pull request:

**Title:** `docs: [what documentation was updated]`

**Description:**
## 📚 Documentation Update

**What Changed:**
[List documentation files and sections updated]

**Why:**
[Reason for documentation changes]

**Accuracy:**
- [ ] Technical accuracy verified
- [ ] Code examples tested
- [ ] Links validated
- [ ] Screenshots current

**Type:** [New docs / Update / Correction / Reorganization]
```

#### Step 4: Use Your Custom Commands

In your Claude Code Web session:

1. Type `/` to see available commands
2. Select your custom command (e.g., `/create-pr`)
3. Claude will follow the template automatically
4. The PR will be created with your standard format

#### Step 5: Share with Your Team

Commit the `.claude/commands/` directory to your repository:

```bash
git add .claude/commands/
git commit -m "Add custom PR slash commands for team"
git push
```

Now all team members using Claude Code Web on this repository will have access to these standardized commands.

### Method 3: Use CLAUDE.md Configuration File

Define PR conventions globally for your repository so Claude always follows them.

#### Step 1: Create CLAUDE.md File

In your repository root, create a file named `CLAUDE.md`:

```bash
touch CLAUDE.md
```

#### Step 2: Define PR Conventions

Add your PR standards to `CLAUDE.md`:

```markdown
# Project Configuration for Claude Code

## Pull Request Guidelines

### PR Title Format
Always use Conventional Commits format for PR titles:
- `feat(component): add new feature` - New features
- `fix(component): resolve bug` - Bug fixes
- `docs(section): update documentation` - Documentation updates
- `refactor(module): restructure code` - Code refactoring
- `test(suite): add tests` - Test additions
- `chore(area): maintenance tasks` - Maintenance work

Keep titles under 72 characters. Use imperative mood.

### PR Description Structure

Every pull request MUST include:

1. **Summary** (required)
   - 2-3 sentences describing the changes
   - Focus on WHAT changed, not HOW

2. **Motivation** (required)
   - Explain WHY the changes were needed
   - Link to relevant issues or discussions

3. **Changes** (required)
   - Bulleted list of key modifications
   - Organize by component or file if extensive

4. **Testing** (required)
   - Describe test coverage
   - List manual testing performed
   - Include test commands if applicable

5. **Breaking Changes** (if applicable)
   - Clearly mark any breaking changes
   - Provide migration guide if needed

6. **Screenshots** (for UI changes)
   - Before/after comparisons
   - Multiple viewports if responsive

### PR Best Practices

- Reference related issues: "Fixes #123" or "Closes #456"
- Tag reviewers appropriate for the changes
- Keep PRs focused on a single concern
- Limit PR size to ~400 lines when possible
- Self-review before requesting review

### Commit Message Format

When creating commits within PRs:
- Use conventional commits format
- Write meaningful commit messages
- Atomic commits (one logical change per commit)
- Never use `--no-verify`
```

#### Step 3: Add Team-Specific Requirements

Customize further for your team's needs:

```markdown
## Team-Specific PR Requirements

### Labels
Claude should suggest appropriate labels:
- `bug` - Bug fixes
- `enhancement` - New features
- `documentation` - Doc changes
- `breaking-change` - Breaking changes
- `dependencies` - Dependency updates

### Review Requirements
- Frontend changes: Tag @frontend-team
- Backend changes: Tag @backend-team
- Security-related: Tag @security-team
- Breaking changes: Require 2+ approvals

### Checklist Items
Every PR description should include this checklist:
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No console errors or warnings
- [ ] Backward compatible (or breaking changes documented)
- [ ] Reviewed own code changes
- [ ] Ready for review
```

#### Step 4: Commit CLAUDE.md to Repository

```bash
git add CLAUDE.md
git commit -m "docs: add Claude Code PR conventions"
git push
```

#### Step 5: Verify Claude Follows Conventions

When you next ask Claude to create a PR, it will automatically:
- Read the CLAUDE.md file
- Apply the conventions you defined
- Follow your team's PR template
- Include required sections

**Test it:**
```
Create a pull request for these changes
```

Claude will automatically apply the formats defined in CLAUDE.md.

## Combining Methods

For maximum flexibility, you can combine all three methods:

**Use Case Matrix:**

| Scenario | Best Method |
|----------|-------------|
| One-time custom PR | Ask directly in conversation |
| Recurring standard PRs | Custom slash command |
| Team-wide conventions | CLAUDE.md file |
| Project standards + variations | CLAUDE.md + slash commands |
| Quick adhoc changes | Direct conversation |
| Multiple PR types (hotfix, feature, docs) | Multiple slash commands |

**Example Combined Workflow:**

1. **CLAUDE.md** defines baseline PR requirements (required sections, title format)
2. **Slash commands** provide templates for different PR types (feature, hotfix, docs)
3. **Direct requests** handle unique situations or overrides

## Advanced: PR Title Patterns

### Conventional Commits Format

**Structure:** `type(scope): description`

**Common Types:**
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation only
- `style` - Formatting, missing semi-colons, etc.
- `refactor` - Code change that neither fixes a bug nor adds a feature
- `perf` - Performance improvement
- `test` - Adding or updating tests
- `chore` - Build process, dependencies, etc.
- `ci` - CI/CD changes

**Examples:**
```
feat(auth): add OAuth2 login support
fix(api): resolve race condition in user endpoint
docs(readme): update installation instructions
refactor(database): simplify query builder
test(auth): add integration tests for login flow
```

### Team-Specific Patterns

**With Issue Numbers:**
```
[JIRA-123] feat: add user dashboard
GH-456: fix: resolve memory leak
#789 - docs: update API reference
```

**With Priority Indicators:**
```
[P0] fix: critical security vulnerability
[HIGH] feat: implement payment processing
[LOW] chore: update dependencies
```

## Expected Results

After implementing PR customization, you should:
- See consistent PR titles following your chosen convention
- Have structured PR descriptions with all required sections
- Save time by not manually formatting each PR
- Enable team members to use the same standards automatically
- Create professional, informative pull requests
- Improve PR review efficiency with standardized format

**Example of Well-Formatted PR:**

```
Title: feat(auth): implement two-factor authentication

Description:
## Summary
Added two-factor authentication (2FA) support using TOTP (Time-based One-Time
Password) to enhance account security.

## Motivation
Security audit recommended implementing 2FA for all user accounts. This change
provides optional 2FA during registration and mandatory 2FA for admin accounts.

## Changes Made
- Implemented TOTP generation and validation using speakeasy library
- Added QR code generation for authenticator app setup
- Created 2FA setup flow in user settings
- Added backup codes generation and storage
- Updated authentication middleware to verify 2FA tokens
- Added database migrations for 2FA-related fields

## Testing
- [x] Unit tests for TOTP generation/validation (18 tests)
- [x] Integration tests for 2FA setup flow (12 tests)
- [x] Manual testing with Google Authenticator
- [x] Manual testing with Authy
- [x] Tested backup code flow
- [x] Verified backwards compatibility for users without 2FA

## Breaking Changes
None - 2FA is optional for existing users

## Screenshots
[QR code setup screen]
[2FA verification prompt]
[Backup codes display]

## Related Issues
Closes #234
Related to #198 (security audit findings)

## Additional Notes
- 2FA is optional by default, can be enforced via admin settings
- Backup codes are one-time use and encrypted in database
- Consider documenting 2FA setup process in user guide
```

## Troubleshooting

### Common Issue 1
**Problem**: Claude doesn't follow the CLAUDE.md conventions
**Solution**:
- Verify CLAUDE.md is in the repository root
- Check the file name is exactly `CLAUDE.md` (case-sensitive)
- Ensure the file is committed and pushed to GitHub
- Try explicitly mentioning: "Follow the PR conventions in CLAUDE.md"
- Restart your Claude Code session to reload configuration

### Common Issue 2
**Problem**: Custom slash commands don't appear in the menu
**Solution**:
- Verify files are in `.claude/commands/` directory
- Check files have `.md` extension
- Ensure frontmatter is properly formatted with `---` delimiters
- Confirm `name:` and `description:` fields are present
- Commit and push the `.claude/commands/` directory
- Refresh the Claude Code session

### Common Issue 3
**Problem**: PR titles don't follow Conventional Commits format
**Solution**:
- Explicitly request the format: "Use conventional commits for the PR title"
- Add the requirement to CLAUDE.md
- Include examples in your slash command template
- Specify the type prefix you want: "Use 'feat:' prefix for this PR"

### Common Issue 4
**Problem**: Missing required sections in PR description
**Solution**:
- List required sections explicitly when requesting the PR
- Use a checklist in your CLAUDE.md or slash command
- Review the PR description and ask Claude to add missing sections
- Create a more detailed slash command template with all sections

### Common Issue 5
**Problem**: PR descriptions are too generic or lack detail
**Solution**:
- Ask Claude to elaborate: "Expand the testing section with specific test cases"
- Provide context about what details are important to your team
- Update your slash command to request specific information
- Use follow-up requests: "Add more detail about the database changes"

### Common Issue 6
**Problem**: Want different PR formats for different types of changes
**Solution**:
- Create multiple slash commands (e.g., `/create-pr`, `/hotfix-pr`, `/docs-pr`)
- Specify the type when requesting: "Create a hotfix PR with urgency details"
- Maintain a library of templates in `.claude/commands/`
- Document when to use each format in your team guidelines

## Additional Tips

### Best Practices
- Start with simple templates and iterate based on team feedback
- Review and update CLAUDE.md as your processes evolve
- Keep slash command templates concise but comprehensive
- Include examples in templates to guide Claude's responses
- Document your PR conventions in your team wiki or README

### Efficiency Tips
- Create shortcuts for your most common PR types
- Use descriptive names for slash commands (e.g., `/feature-pr`, `/bug-pr`)
- Include automated checks in your templates (e.g., "Run tests before creating PR")
- Combine PR creation with code review reminders

### Team Collaboration
- Share your `.claude/commands/` directory across the team
- Maintain CLAUDE.md as a living document
- Review PR formats during retrospectives
- Create templates collaboratively with your team
- Version control all customization files

### Advanced Techniques
- Use CLAUDE.md for global rules, slash commands for specific scenarios
- Create language-specific PR templates (e.g., for Python vs JavaScript changes)
- Include automated lint checks in PR descriptions
- Add deployment instructions for production-bound changes
- Reference testing pyramids or coverage requirements

## Related Articles
- KB-003: How to authenticate your GitHub account with Claude Code Web
- KB-004: How to connect your first GitHub repository to Claude Code Web
- KB-014: How to understand which branch Claude Code is working on
- KB-015: How to view changes made by Claude Code in your repository
- KB-022: How to intervene and guide Claude during code generation

## Sources
1. Claude Code Slash Commands Documentation - https://docs.claude.com/en/docs/claude-code/slash-commands (Accessed: November 16, 2025)
2. Conventional Commits Specification - https://www.conventionalcommits.org/ (Accessed: November 16, 2025)
3. GitHub Pull Request Best Practices - https://docs.github.com/en/pull-requests/collaborating-with-pull-requests (Accessed: November 16, 2025)
4. Awesome Claude Code Resources - https://github.com/hesreallyhim/awesome-claude-code (Accessed: November 16, 2025)

**Additional Search Queries:**
- "Claude Code custom slash commands pull request templates"
- "CLAUDE.md file configuration examples"
- "Claude Code Web PR automation best practices"

---
*This article is part of the claudecodeweb knowledge base*
