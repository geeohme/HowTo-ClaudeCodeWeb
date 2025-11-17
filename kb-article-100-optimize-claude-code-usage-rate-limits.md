# How to optimize Claude Code usage to stay within rate limits while maximizing productivity

**Article ID**: KB-100
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 20-30 minutes

## Overview

Claude Code Web operates under both 5-hour rolling window limits and weekly usage caps. This guide teaches advanced strategies for maximizing your productivity while staying within these limits through context management, efficient prompting, token optimization, and workflow improvements. You'll learn to leverage prompt caching, custom commands, and multi-model strategies to get 10-30% productivity gains while using your rate limits wisely.

## Prerequisites

- Active Claude Pro or Max subscription (required for Claude Code Web access)
- Basic understanding of Claude Code Web interface and task creation
- Connected GitHub repository with at least one coding project
- Familiarity with your plan's usage limits (see KB-005: Understand Pro/Max limits)
- Understanding of effective task description (see KB-007: Describe tasks effectively)

## Steps

### Step 1: Understand Your Rate Limit Structure

Claude Code Web uses a dual-limit system as of August 2025:

**5-Hour Rolling Window**: Your personal timer starts when you send your first message and resets 5 hours after that initial message. All token usage during this period counts against your plan's pool.

**Weekly Limits**: Reset every seven days and provide an overall usage cap:
- **Pro Plan**: 40-80 hours of Sonnet 4 per week
- **Max Plan ($100/month)**: 140-280 hours of Sonnet 4, 15-35 hours of Opus 4 per week
- **Max Plan ($200/month)**: 240-480 hours of Sonnet 4, 24-40 hours of Opus 4 per week

Check your current usage status by typing `/usage` in any Claude Code session to view when your limits reset.

### Step 2: Implement Context Management Best Practices

Context management is the single most important optimization technique for staying within rate limits.

**Create CLAUDE.md Files**: Place a `CLAUDE.md` file in your repository root to document project-specific information that Claude automatically pulls into context:

```markdown
# Project Context

## Common Workflows
- Run tests: `npm test`
- Format code: `npm run format`
- Build: `npm run build`

## Code Style Guidelines
- Use functional components for React
- Prefer const over let
- Always include JSDoc comments for public functions

## Project-Specific Warnings
- The auth module uses legacy OAuth 1.0 - don't refactor without approval
- Database migrations must be reversible

## Testing Requirements
- All PRs must pass lint and tests before submission
- Integration tests require Docker running locally
```

**Use /clear Frequently**: During extended sessions, use `/clear` to reset the context window and remove irrelevant conversation history. This prevents token waste on outdated context and maintains performance.

**When to Clear**:
- After completing a major task or feature
- When switching between unrelated files or components
- When conversations exceed 20-30 exchanges
- Before starting a new, independent task

### Step 3: Optimize Your Prompting Strategy

Specific, detailed instructions on the first attempt reduce iterations and save significant tokens.

**Front-Load Context**: Instead of iterative back-and-forth, provide comprehensive instructions upfront:

```
❌ Bad (Multiple Iterations):
"Add a login form"
"Make it use email and password"
"Add validation"
"Style it with Tailwind"

✅ Good (Single Request):
"Create a login form component in src/components/LoginForm.tsx with:
- Email and password fields with proper input types
- Client-side validation (email format, password min 8 chars)
- Tailwind CSS styling matching our design system (primary button, card layout)
- Error message display for invalid credentials
- Submit handler that calls our existing authService.login() method
- Loading state during authentication"
```

**Specify Files and Constraints**: Mention specific file paths, edge cases, and limitations:

```
"Refactor the user authentication in src/auth/UserAuth.ts to support OAuth 2.0.
Keep backward compatibility with existing session tokens. Update the tests in
src/auth/__tests__/UserAuth.test.ts to cover the new OAuth flow. Don't modify
the database schema - work with the existing users table structure."
```

**Use Extended Thinking for Complex Problems**: For challenging architectural decisions or complex debugging, use thinking keywords to allocate more computation:

- `"think"` - Basic reasoning (simple problems)
- `"think hard"` - Intermediate reasoning (moderate complexity)
- `"think harder"` - Advanced reasoning (complex refactoring)
- `"ultrathink"` - Maximum reasoning (architectural decisions, multi-file migrations)

### Step 4: Leverage Prompt Caching for Repeated Context

As of March 2025, prompt cache read tokens no longer count against your Input Tokens Per Minute (ITPM) limit for Claude 3.7 Sonnet, allowing you to dramatically increase throughput.

**How Caching Helps**: When you reference the same files, documentation, or context across multiple messages, caching can:
- Reduce token costs by up to 90%
- Decrease latency by up to 85%
- Allow more requests within your rate limit window

**Cache-Optimized Workflows**:

1. **Batch Similar Tasks**: Group related work that uses the same context:
   ```
   "I need to update error handling across multiple components. Let's start with
   these files that all use our ErrorBoundary pattern: src/components/UserProfile.tsx,
   src/components/Dashboard.tsx, src/components/Settings.tsx. For each file..."
   ```

2. **Maintain Consistent Context**: Keep relevant documentation files in the conversation:
   ```
   "Using the API documentation in docs/api-reference.md as reference, update these
   three endpoint handlers to use the new authentication middleware..."
   ```

3. **Use CLAUDE.md for Stable Context**: Information in CLAUDE.md gets cached efficiently across sessions since it's automatically included.

### Step 5: Create Custom Slash Commands for Repetitive Workflows

Custom slash commands compress long, repetitive prompts into simple triggers, saving tokens and time.

**Create a Commands Directory**: In your repository root, create `.claude/commands/` and add Markdown files:

```bash
mkdir -p .claude/commands
```

**Example: Pull Request Command** (`.claude/commands/pr.md`):

```markdown
Review all commits on this branch since diverging from main. Then:

1. Check git status and diff to understand changes
2. Analyze the full commit history
3. Create a PR with this format:

Title: Brief, descriptive title (max 60 chars)

Body:
## Summary
- [Bullet point of main changes]
- [Bullet point of impact]

## Testing
- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] Manual testing completed
- [ ] Documentation updated

4. Push to remote and create the PR
```

**Example: Code Review Command** (`.claude/commands/review.md`):

```markdown
Perform a thorough code review of $ARGUMENTS focusing on:

1. **Security**: Check for injection vulnerabilities, authentication issues, data exposure
2. **Performance**: Identify inefficient queries, N+1 problems, unnecessary computations
3. **Maintainability**: Assess code clarity, documentation, test coverage
4. **Best Practices**: Verify adherence to project conventions in CLAUDE.md

Provide specific file/line feedback with suggested improvements.
```

**Usage**: Type `/pr` or `/review src/components/NewFeature.tsx` to execute these commands instantly.

### Step 6: Implement Structured Workflows

Following proven workflow patterns reduces wasted iterations and optimizes token usage.

**Explore-Plan-Code-Commit Workflow**:

1. **Explore**: "Analyze the current authentication system and identify what needs to change for OAuth 2.0 support"
2. **Plan**: "Create a step-by-step migration plan with file changes needed"
3. **Code**: "Implement Step 1 of the plan - update the AuthProvider component"
4. **Commit**: "Run tests, fix any issues, and commit this change"

**Test-Driven Development (TDD) Workflow**:

1. "Write failing tests for the new user search feature in src/features/users/__tests__/UserSearch.test.ts"
2. "Implement the minimum code in UserSearch.tsx to make these tests pass"
3. "Refactor the implementation while keeping tests green"
4. "Add edge case tests and handle them"

**Visual Iteration Workflow**: For UI work, provide screenshots or design mocks:

```
"Here's a screenshot of our current dashboard [attach image]. Update it to match
this new design [attach mockup]. Focus on the navigation header and sidebar layout
first, then we'll iterate on the chart components."
```

### Step 7: Use Model Selection Strategically

Rate limits apply separately to each model, allowing you to distribute work across models based on task complexity.

**Model Selection Guide**:

- **Claude Haiku**: Simple, high-volume tasks
  - Code formatting and style fixes
  - Simple bug fixes with clear reproduction steps
  - Documentation generation from code comments
  - Running tests and reporting results

- **Claude Sonnet**: Balanced performance for most tasks (default)
  - Feature implementation
  - Moderate refactoring
  - Test writing
  - Code reviews

- **Claude Opus**: Complex reasoning and architecture
  - Large-scale refactoring across many files
  - Architecture decisions
  - Complex debugging with unclear root causes
  - Migration planning for major framework changes

**Token-Efficient Tool Use**: Claude 3.7 Sonnet supports calling tools in a token-efficient manner, reducing output token consumption by up to 70% (average 14% reduction). Use Sonnet for tool-heavy workflows like file editing and git operations.

### Step 8: Optimize max_tokens Settings for Your Tasks

If you're hitting output token per minute (OTPM) limits, reduce max_tokens to better approximate your actual completion sizes.

**Analyze Your Usage Patterns**: Review recent tasks to understand typical response sizes:
- Simple bug fixes: 500-1000 tokens
- Feature implementation: 1500-3000 tokens
- Large refactoring: 3000-8000 tokens
- Architecture planning: 2000-4000 tokens

**Set Appropriate Limits**: For focused tasks, specify constraints:

```
"Fix the login validation bug in UserAuth.ts. Keep the response focused -
just show the fix and explain the root cause briefly."
```

### Step 9: Implement Multi-Claude Workflows for Parallel Optimization

Run parallel Claude instances for independent tasks to maximize productivity without hitting single-session limits.

**Use Git Worktrees**: Create separate working directories for parallel development:

```bash
# Main task: Feature development in main worktree
git worktree add ../project-bugfix main

# In Claude Session 1: Work on feature in main repository
# In Claude Session 2: Work on bugfix in ../project-bugfix worktree
```

**Parallel Verification Pattern**:
- **Session 1**: Write implementation code
- **Session 2**: Write comprehensive tests and perform code review
- Merge both when complete

**See Related Articles**: KB-088, KB-089, KB-090 for detailed git worktree workflows.

### Step 10: Implement Checklist-Based Workflows for Large Tasks

For migrations, bulk fixes, or multi-file changes, use checklist-driven development to track progress efficiently.

**Generate Task Checklist**: Start large projects by creating a systematic plan:

```
"We need to migrate from Redux to Zustand across our app. First, analyze all Redux
usage and generate a Markdown checklist of files to migrate, ordered by dependency
(lowest dependencies first)."
```

**Claude Response** (example):
```markdown
# Redux to Zustand Migration Checklist

## Phase 1: Utilities & Stores (No dependencies)
- [ ] src/store/slices/uiSlice.ts → src/store/useUIStore.ts
- [ ] src/store/slices/themeSlice.ts → src/store/useThemeStore.ts

## Phase 2: Feature Stores
- [ ] src/store/slices/authSlice.ts → src/store/useAuthStore.ts (depends on UI)
- [ ] src/store/slices/userSlice.ts → src/store/useUserStore.ts (depends on Auth)

## Phase 3: Components
- [ ] src/components/Header.tsx
- [ ] src/components/UserProfile.tsx
...
```

**Work Systematically**: Address items one at a time, checking them off as you complete them:

```
"Complete the first item in the checklist - migrate src/store/slices/uiSlice.ts.
Run tests after migration and update the checklist."
```

### Step 11: Use Headless Mode for Automation

For CI/CD pipelines, pre-commit hooks, and automated workflows, use headless mode to avoid interactive rate limit consumption.

**Headless Command Structure**:

```bash
claude -p "Run eslint on all changed files and fix auto-fixable issues"
```

**With Structured Output**:

```bash
claude -p "Analyze test coverage and report files under 80%" \
  --output-format stream-json > coverage-report.json
```

**Pre-Commit Hook Example** (`.git/hooks/pre-commit`):

```bash
#!/bin/bash
claude -p "Check all staged files for console.log statements and TODO comments.
Report any found with file and line number." --allowedTools Read Grep
```

### Step 12: Monitor and Analyze Your Usage Patterns

Regular usage monitoring helps you identify optimization opportunities.

**Check Usage Frequently**: Use `/usage` command to see:
- Time until your 5-hour window resets
- Time until your weekly limit resets
- Remaining capacity in current windows

**Identify High-Token Tasks**: Review your session history to find patterns:
- Which tasks consume the most tokens?
- Are you iterating excessively on certain types of work?
- Could any repetitive tasks be converted to slash commands?

**Optimize Based on Data**:
- If UI tasks require many iterations: Start providing design mockups and detailed specifications
- If debugging consumes excessive tokens: Create better reproduction steps and log analysis before engaging Claude
- If similar tasks repeat: Create custom slash commands or skills

## Expected Results

After implementing these optimization strategies, you should experience:

**Efficiency Gains**:
- 10-30% productivity increase with solid workflows
- 50-70% reduction in iterations for well-specified tasks
- 2-5x more tasks completed per rate limit window

**Usage Metrics**:
- Pro users: 10-40 focused prompts per 5-hour window
- Max (5x) users: 225+ messages per 5-hour window
- Max (20x) users: 900+ messages per 5-hour window

**Quality Improvements**:
- First-attempt success rate improves from 30-40% to 60-80%
- Reduced back-and-forth conversation length
- More consistent code quality and adherence to project standards

**Workflow Evidence**:
- CLAUDE.md file established in your repository
- 3-5 custom slash commands for your most common tasks
- Structured workflow adoption (Explore-Plan-Code-Commit or TDD)
- Efficient use of `/clear` command (visible in session history)

## Troubleshooting

### Issue 1: Hitting 5-Hour Limit Too Quickly

**Problem**: You're running out of capacity in your 5-hour window before completing important work.

**Solutions**:
- **Be More Specific**: Review your recent prompts. Are you providing complete context upfront? Vague prompts lead to multiple iterations.
- **Clear Context Regularly**: Use `/clear` after completing each major task to prevent carrying unnecessary context.
- **Use Haiku for Simple Tasks**: Switch to Claude Haiku for code formatting, simple fixes, and documentation generation.
- **Batch Related Work**: Group similar tasks that share context to leverage prompt caching.
- **Check for Context Bloat**: If conversations exceed 20-30 messages, you're likely carrying too much context. Start fresh sessions for new tasks.

### Issue 2: Hitting Weekly Limits Mid-Week

**Problem**: You've exhausted your weekly limit with several days remaining in the cycle.

**Solutions**:
- **Audit Your Task Types**: Review what consumed the most usage. Are you using Opus for tasks that Sonnet could handle?
- **Implement Headless Automation**: Move repetitive checks (linting, formatting, simple tests) to headless mode or local scripts.
- **Use Multi-Claude Strategy**: Leverage git worktrees to run parallel sessions for independent tasks, distributing load across your rate limit windows.
- **Create More Slash Commands**: Identify your 5 most common tasks and create optimized slash commands to reduce token usage.
- **Consider Plan Upgrade**: If optimization doesn't resolve the issue and you're consistently productive, upgrading from Max 5x to Max 20x provides 4x more capacity.

### Issue 3: Excessive Iterations on Simple Tasks

**Problem**: Tasks that should complete in 1-2 exchanges are taking 5-10 iterations.

**Solutions**:
- **Improve Task Descriptions**: Include file paths, expected behavior, edge cases, and constraints in your initial prompt.
- **Provide Examples**: Show Claude what you want by including code snippets or screenshots.
- **Use Extended Thinking**: Add "think hard" for moderately complex tasks to get better first attempts.
- **Update CLAUDE.md**: Document your coding standards, common patterns, and project-specific requirements so Claude has this context automatically.
- **Reference Existing Code**: Point Claude to similar, correctly implemented features: "Follow the same pattern as UserProfile.tsx but for Organization entities."

### Issue 4: Context Window Confusion Across Sessions

**Problem**: Claude seems to have outdated information or doesn't remember previous work across sessions.

**Solutions**:
- **Use CLAUDE.md**: Critical project information should live in CLAUDE.md, not in conversation history.
- **Create Session Start Hooks**: Use SessionStart hooks to initialize environment state (see KB-061).
- **Explicitly Reference Previous Work**: Start new sessions with: "Continuing from the OAuth migration we started yesterday. The AuthProvider has been updated (see commit abc123). Now we need to..."
- **Use Git History**: Claude can read git logs and diffs: "Review the last 3 commits on this branch to understand what's been done, then continue with the next step."

### Issue 5: Uncertainty About What Counts Toward Limits

**Problem**: You're not sure which operations consume your rate limit budget.

**Understanding**:
- **Counts Toward Limits**: All message exchanges, file reads, context processing, code generation
- **Optimized with Caching**: Repeated file reads, CLAUDE.md content (automatically cached)
- **Token-Efficient (as of Claude 3.7)**: Tool calls for editing, bash operations (14-70% reduction)
- **Doesn't Count**: `/usage` command, viewing UI elements, time spent reading Claude's responses

**Strategy**: Focus optimization on reducing message count (better prompts), context size (/clear regularly), and leveraging cached content (CLAUDE.md, repeated file references).

## Additional Tips

- **Start Sessions Strategically**: Begin your 5-hour window when you have focused work time available, not during meetings or context-switching periods.

- **Use Visual References Aggressively**: Screenshots, design mockups, error messages, and log files reduce the need for descriptive back-and-forth.

- **Develop a Personal Prompt Library**: Keep a local file of your best-performing prompts for common tasks and refine them over time.

- **Leverage Your Shell Tools**: Document custom scripts and tools in CLAUDE.md, then have Claude use them: "Run the project's analyze-bundle.sh script and interpret the results."

- **Test Your Slash Commands**: New custom slash commands should be tested and refined. Bad commands waste tokens on unclear instructions.

- **Contribute to Team Efficiency**: Share your optimized slash commands, CLAUDE.md best practices, and workflow discoveries with your team by committing them to the repository.

- **Combine Workflow Patterns**: Use "Explore-Plan-Code-Commit" for new features but switch to "TDD" for bug fixes requiring test coverage.

- **Monitor Anthropic Announcements**: Token efficiency features are actively improving (March 2025 caching update, tool use optimization). Stay informed about new capabilities.

- **Use /clear as a Circuit Breaker**: If you find yourself in a confusing back-and-forth, use `/clear` and restart with a more comprehensive prompt rather than continuing a tangled conversation.

- **Optimize for First-Attempt Success**: Track your success rate informally. If less than 50% of tasks succeed on the first attempt, focus on improving prompt specificity before worrying about other optimizations.

## Related Articles

- KB-005: Understand Pro/Max limits (prerequisite understanding)
- KB-007: Describe tasks effectively (essential for optimization)
- KB-027: How to manage concurrent tasks effectively
- KB-028: How to prioritize tasks when approaching your plan's concurrent limit
- KB-061: Create SessionStart hook for repository initialization
- KB-068: Create your first custom slash command
- KB-083: Create custom skill for repeated workflows
- KB-088: Use git worktrees for parallel sessions
- KB-092: Implement TDD with Claude Code

## Sources

- Rate limits - Claude Docs - https://docs.claude.com/en/api/rate-limits (Accessed: November 16, 2025)
- Claude Code Best Practices \\ Anthropic - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Anthropic unveils new rate limits to curb Claude Code power users | TechCrunch - https://techcrunch.com/2025/07/28/anthropic-unveils-new-rate-limits-to-curb-claude-code-power-users/ (Accessed: November 16, 2025)
- Token-saving updates on the Anthropic API | Claude - https://claude.com/blog/token-saving-updates (Accessed: November 16, 2025)
- Our approach to API rate limits | Anthropic Help Center - https://support.claude.com/en/articles/8243635-our-approach-to-api-rate-limits (Accessed: November 16, 2025)
- Claude Code Usage Limits and Subscription Plans Explained (Aug 2025) - Geeky Gadgets - https://www.geeky-gadgets.com/claude-code-usage-limits-pricing-plans-guide-sept-2025/ (Accessed: November 16, 2025)
- Everything We Know About Claude Code Limits - Portkey AI - https://portkey.ai/blog/claude-code-limits/ (Accessed: November 16, 2025)
- Anthropic API Pricing: Complete Guide and Cost Optimization Strategies (2025) - Finout - https://www.finout.io/blog/anthropic-api-pricing (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
