# How to Perform Code Reviews with Claude's Assistance

**Article ID**: KB-056
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15-30 minutes

## Overview
Claude Code Web can assist with code reviews by analyzing pull requests, identifying issues beyond traditional linting, providing security analysis, and offering subjective feedback on code quality. This guide shows you how to leverage Claude's capabilities to conduct thorough, efficient code reviews.

## Prerequisites
- Active Claude Pro or Max subscription
- Connected repository with GitHub integration (KB-003, KB-004)
- Understanding of basic Claude Code Web workflows (KB-006, KB-007)
- Familiarity with pull requests and version control
- Optional: GitHub CLI (gh) installed for enhanced PR workflows

## Understanding Claude's Code Review Capabilities

### What Claude Can Review

**Code Quality Issues:**
- Typos in code comments and documentation
- Stale or outdated comments
- Misleading function or variable names
- Inconsistent coding patterns
- Code readability and maintainability
- Logic errors and edge cases

**Security Vulnerabilities:**
- Common security patterns and anti-patterns
- Potential injection vulnerabilities
- Authentication and authorization issues
- Sensitive data exposure risks
- Cryptographic weaknesses

**Architectural Concerns:**
- Design pattern violations
- Code organization and structure
- Dependency management
- Performance implications
- Scalability considerations

**Beyond Traditional Linting:**
- Subjective code quality assessments
- Context-aware suggestions
- Business logic validation
- Documentation quality
- Test coverage gaps

## Steps

### Step 1: Set Up GitHub Integration

**If not already configured:**

Navigate to your Claude Code Web interface and ensure your repository is connected with proper permissions.

**Verify GitHub access:**
```
Check that Claude can:
- Read repository contents
- Access pull request information
- View commit history
- Read branch details
```

**For advanced features, install the GitHub app:**
```
In Claude Code Web, run:
/install-github-app

Follow the prompts to:
1. Authorize the GitHub application
2. Select repositories to grant access
3. Configure required secrets
```

### Step 2: Review an Existing Pull Request

**Option A: Review from PR URL**

Provide Claude with the pull request URL:

```
"Please review this pull request: https://github.com/username/repo/pull/123"
```

**Option B: Review current branch changes**

If you're working on a branch that will become a PR:

```
"Review the changes I've made on this branch compared to main.
Look for:
- Code quality issues
- Potential bugs
- Security concerns
- Missing edge cases"
```

**What Claude will analyze:**
- All modified files in the PR
- Context of surrounding code
- Commit messages and history
- Related code in the repository
- Common patterns in your codebase

### Step 3: Request Specific Types of Reviews

**General Code Review:**
```
"Review the changes in src/components/UserProfile.js.
Focus on code quality, readability, and potential bugs."
```

**Security-Focused Review:**
```
"Perform a security review of the authentication changes in auth.js.
Check for common vulnerabilities and security best practices."
```

**Performance Review:**
```
"Review the database queries in api/users.js for performance issues.
Look for N+1 queries, missing indexes, and inefficient operations."
```

**Documentation Review:**
```
"Review the code comments and documentation in utils/validation.js.
Ensure comments are accurate, helpful, and up-to-date."
```

**Test Coverage Review:**
```
"Review the test files for the payment module.
Identify gaps in test coverage and missing edge cases."
```

### Step 4: Use the Security Review Command

**Run ad-hoc security analysis:**

In Claude Code Web, navigate to your project and run:

```
/security-review
```

**What this does:**
- Searches your codebase for potential vulnerabilities
- Analyzes code changes in context
- Provides detailed explanations of issues found
- Assigns severity ratings to findings
- Offers remediation guidance
- Filters out common false positives

**Example security review output:**
```
[Security Review Results]

HIGH SEVERITY:
- auth.js:45 - Password stored in plain text
  Risk: User credentials could be compromised if database is breached
  Fix: Use bcrypt or argon2 to hash passwords before storage

MEDIUM SEVERITY:
- api.js:78 - SQL query constructed with string concatenation
  Risk: Potential SQL injection vulnerability
  Fix: Use parameterized queries or an ORM

LOW SEVERITY:
- config.js:12 - API key in source code
  Risk: Credentials may be exposed in version control
  Fix: Move to environment variables
```

### Step 5: Implement Multi-Agent Review Pattern

**Use two separate Claude sessions for enhanced review:**

**Session 1 - Writing code:**
```
"Implement user registration with email validation and password hashing"
```

**Session 2 - Reviewing code:**
```
"Review the user registration implementation in auth/register.js.
I want a fresh perspective - act as a code reviewer seeing this for
the first time. Check for bugs, security issues, and best practices."
```

**Why this works:**
- Simulates having multiple engineers review code
- Fresh perspective catches issues the implementer might miss
- Reduces confirmation bias
- Provides more objective feedback

### Step 6: Address Review Comments

**For simple fixes, use one-shot resolution:**

```
"Fix all the review comments on PR #123 and push the changes back to
the PR branch. The main issues are:
- Rename confusing variable names
- Add missing error handling
- Update stale comments"
```

**For complex issues, be more specific:**

```
"Regarding the review comment about the authentication flow:
Implement the suggested token refresh mechanism. Store refresh tokens
securely and add rotation logic. Reference the OAuth2 best practices."
```

### Step 7: Review Code for Specific Patterns

**Check for consistency:**
```
"Review all components in src/components/ and ensure they follow
the same naming conventions, prop validation patterns, and error
handling approaches as defined in our style guide."
```

**Identify anti-patterns:**
```
"Scan the codebase for common React anti-patterns like:
- Mutating state directly
- Missing key props in lists
- Uncontrolled components that should be controlled
- Missing cleanup in useEffect hooks"
```

**Validate architectural decisions:**
```
"Review how the new payment module integrates with the existing
architecture. Does it follow our layered architecture pattern?
Are dependencies properly injected? Is it testable?"
```

### Step 8: Generate Review Summary

**Request comprehensive review summary:**

```
"Provide a comprehensive code review summary for this PR including:
1. Overall code quality assessment
2. Critical issues that must be addressed
3. Suggestions for improvement
4. Positive aspects worth noting
5. Recommendation: Approve, Request Changes, or Comment"
```

**Example response structure:**
```
Code Review Summary - PR #123: Add user profile editing

CRITICAL ISSUES (must fix):
- Missing input validation on email field
- Password update allows empty password

SUGGESTIONS:
- Extract duplicate validation logic into shared utility
- Add unit tests for edge cases
- Consider using a form library like Formik

POSITIVE ASPECTS:
- Clean, readable code structure
- Good error messages for users
- Proper accessibility attributes

RECOMMENDATION: Request Changes
Rationale: Critical security issues must be addressed before merge
```

## Expected Results

When using Claude for code reviews effectively, you should:

- Receive detailed, contextual feedback beyond what linters provide
- Identify security vulnerabilities before they reach production
- Catch logic errors, edge cases, and maintainability issues
- Get consistent code quality across your codebase
- Accelerate review process while maintaining thoroughness
- Learn from Claude's suggestions to improve your coding practices
- Generate professional review comments for team collaboration

**Typical review output includes:**
- Specific line numbers and file locations
- Explanation of why something is an issue
- Concrete suggestions for fixes
- Severity ratings for different issues
- Code examples showing better approaches

## Troubleshooting

### Common Issue 1
**Problem**: Claude's review is too generic or surface-level
**Solution**:
- Be more specific about what to review
- Provide context about your codebase standards
- Reference specific files or functions to focus on
- Ask for deeper analysis: "Don't just check syntax, evaluate the logic and architecture"
- Provide examples of issues you want to catch
- Ask follow-up questions about specific concerns

### Common Issue 2
**Problem**: Too many false positives or nitpicky comments
**Solution**:
- Specify severity levels you care about: "Only flag medium and high severity issues"
- Provide context about intentional design decisions
- Ask Claude to focus on specific types of issues
- Clarify your coding standards and preferences
- Request filtering: "Skip style issues, focus on logic and security"
- Use: "Prioritize actionable feedback over stylistic preferences"

### Common Issue 3
**Problem**: Claude doesn't have access to the full PR context
**Solution**:
- Ensure GitHub integration is properly configured
- Provide the PR URL directly
- Share relevant commit hashes or branch names
- Copy key parts of the PR description to Claude
- Mention related files or dependencies
- Provide business context for the changes

### Common Issue 4
**Problem**: Security review misses certain vulnerability types
**Solution**:
- Explicitly ask Claude to check for specific vulnerabilities
- Reference OWASP Top 10 or CWE categories
- Provide examples of past security issues in your codebase
- Ask for review of specific security domains (auth, data validation, etc.)
- Request multiple passes focusing on different security aspects
- Combine automated tools with Claude's analysis

### Common Issue 5
**Problem**: Difficulty tracking review comments and fixes
**Solution**:
- Ask Claude to create a checklist of issues found
- Request numbered list of action items
- Have Claude create GitHub issue or PR comment drafts
- Use separate sessions for review vs. implementation
- Ask for before/after code snippets
- Request summary at the end with all changes made

## Additional Tips

### Best Practices for Code Reviews with Claude

**Set clear review objectives:**
```
Before: "Review this code"
Better: "Review this authentication code for security vulnerabilities,
focusing on session management and input validation"
```

**Provide codebase context:**
```
"Our project uses:
- React with TypeScript
- Jest for testing
- ESLint with Airbnb config
- Prefer functional components over class components
Review with these standards in mind"
```

**Use iterative review approach:**
1. First pass: High-level architecture and major issues
2. Second pass: Security and error handling
3. Third pass: Code quality and maintainability
4. Final pass: Documentation and tests

**Combine human and AI review:**
- Use Claude for initial automated review
- Human reviewer focuses on business logic and UX
- Claude handles consistency checks and pattern matching
- Human makes final approval decision

### Review Checklists for Different Code Types

**API Endpoint Review:**
```
"Review this API endpoint for:
- Input validation and sanitization
- Authentication and authorization checks
- Error handling and status codes
- Rate limiting considerations
- Documentation completeness
- Response data structure consistency"
```

**Database Code Review:**
```
"Review this database code for:
- SQL injection vulnerabilities
- N+1 query problems
- Missing indexes
- Transaction handling
- Connection pool management
- Migration safety"
```

**Frontend Component Review:**
```
"Review this React component for:
- Proper prop types and validation
- Accessibility (ARIA labels, keyboard navigation)
- Performance (unnecessary re-renders)
- Error boundaries and error handling
- Responsive design considerations
- State management best practices"
```

### Creating Custom Review Templates

**Define review template for your team:**

```
"Use this review template for all PRs:

FUNCTIONALITY:
- Does the code do what it's supposed to do?
- Are edge cases handled?
- Is error handling appropriate?

SECURITY:
- Any security vulnerabilities?
- Input validation present?
- Sensitive data properly protected?

QUALITY:
- Code readable and maintainable?
- Follows project conventions?
- DRY principle applied?

TESTING:
- Adequate test coverage?
- Tests actually test the right things?
- Edge cases covered?

DOCUMENTATION:
- Code comments where needed?
- README updated if needed?
- API docs current?"
```

### Leveraging Claude for Pre-Commit Reviews

**Before committing, quick self-review:**

```
"I'm about to commit these changes. Do a quick pre-commit review
for obvious issues, console.logs I forgot to remove, commented-out
code, or debug statements still present."
```

**Pre-push branch review:**

```
"Review all commits on this branch before I push:
git log main..HEAD

Check for:
- Coherent commit messages
- No credentials or secrets
- Consistent code style across commits
- Logical grouping of changes"
```

### Learning from Review Patterns

**Identify recurring issues:**

```
"I've noticed you flagged similar issues in the last three reviews.
Can you create guidelines I should follow to prevent these issues
in the future?"
```

**Build team knowledge base:**
```
"Compile the most common code review issues from this project into
a 'Common Mistakes' guide that new team members can reference."
```

## Related Articles
- KB-003: How to authenticate your GitHub account with Claude Code Web
- KB-004: How to connect your first repository to Claude Code Web
- KB-007: How to describe a task effectively for Claude to understand
- KB-015: How to view changes made by Claude Code in your repository
- KB-023: How to approve or reject Claude's suggested changes
- KB-024: How to ask Claude to revise its approach to a problem

## Sources
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Automate Security Reviews with Claude Code - https://claude.com/blog/automate-security-reviews-with-claude-code (Accessed: November 16, 2025)
- Claude Code Common Workflows - https://docs.claude.com/en/docs/claude-code/tutorials (Accessed: November 16, 2025)
- GitHub Action for Claude Code Reviews - https://github.com/anthropics/claude-code-action (Accessed: November 16, 2025)
- Claude Code Security Review Action - https://github.com/anthropics/claude-code-security-review (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
