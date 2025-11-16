# How to add a new feature to an existing codebase

**Article ID**: KB-052
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 20-45 minutes

## Overview
Adding a new feature to an existing codebase with Claude Code Web involves a systematic approach that leverages Claude's ability to understand your repository, plan implementations, and write production-quality code. This guide walks you through the proven workflow patterns that lead to successful feature development, from initial planning through testing and integration.

## Prerequisites
- Active Claude Code Web session (KB-001)
- Connected repository with the codebase (KB-004)
- Understanding of effective task descriptions (KB-007)
- Familiarity with viewing and reviewing changes (KB-015)
- Basic knowledge of your codebase structure

## The Recommended Workflow: Explore → Plan → Code → Commit

The most effective pattern for adding features follows four distinct phases. Skipping the early phases often leads to Claude implementing solutions without full context.

### Phase 1: Explore (Research)
**Ask Claude to read and understand relevant code without making changes yet.**

### Phase 2: Plan
**Have Claude create a detailed implementation plan before writing code.**

### Phase 3: Code
**Implement the feature following the plan with iterative verification.**

### Phase 4: Commit
**Review, test, and commit the changes with proper documentation.**

## Steps

### Step 1: Define Your Feature Clearly

Before asking Claude to implement anything, clearly articulate what you want to build.

**Good Feature Description:**
```
Add a password strength indicator to the registration form that:
- Shows strength as user types (weak/medium/strong)
- Requires minimum 8 characters, one uppercase, one number
- Displays visual color-coded feedback (red/yellow/green)
- Prevents form submission if password is weak
```

**Why This Matters:**
- Provides clear success criteria
- Specifies both functionality and UX requirements
- Defines validation rules explicitly
- Sets expectations for user interaction

**Tip:** Reference KB-007 for more guidance on writing effective task descriptions.

### Step 2: Ask Claude to Research First (Don't Skip This!)

Before coding, have Claude explore your codebase to understand the context.

**Example Prompt:**
```
Before implementing the password strength indicator, please:
1. Read the current registration form code (components/RegisterForm.js)
2. Check if we have existing validation utilities
3. Look at how other form validations are implemented
4. Identify the styling approach used for form feedback
5. Check if there are existing password-related utilities

Don't write any code yet - just explore and summarize what you find.
```

**What Claude Will Do:**
- Read relevant files
- Understand existing patterns and conventions
- Identify dependencies and related code
- Note the tech stack and styling approach
- Find reusable utilities or similar implementations

**Expected Output:**
Claude will provide a summary like:
```
I've explored the codebase. Here's what I found:

1. RegisterForm.js uses React hooks and Formik for form management
2. There's a validation.js utility with email and username validators
3. Form errors are displayed using the <FormError> component with red styling
4. The project uses CSS modules for styling
5. No existing password validation beyond required field check

I can now plan the implementation to follow these existing patterns.
```

### Step 3: Request an Implementation Plan

Now ask Claude to create a detailed plan based on the research.

**Example Prompt:**
```
Based on your exploration, create a detailed plan for implementing the password strength indicator. Include:
- Which files need to be modified or created
- What functions/components need to be written
- How it integrates with existing code
- The order of implementation steps
- Testing considerations
```

**Expected Plan:**
```
Implementation Plan:

1. Create utility function (utils/validation.js)
   - Add calculatePasswordStrength(password) function
   - Returns {strength: 'weak'|'medium'|'strong', feedback: string}
   - Checks length, uppercase, numbers, special chars

2. Create PasswordStrengthIndicator component
   - New file: components/PasswordStrengthIndicator.js
   - Accepts password as prop
   - Uses validation utility
   - Returns color-coded UI element
   - Create accompanying CSS module

3. Modify RegisterForm.js
   - Import PasswordStrengthIndicator component
   - Add to password field
   - Update form validation to check strength
   - Disable submit if password is weak

4. Add tests
   - Unit tests for calculatePasswordStrength
   - Component tests for PasswordStrengthIndicator
   - Integration test for form submission blocking

This approach follows the existing patterns and ensures consistency.
```

**Why Planning Matters:**
According to Anthropic's official best practices, asking Claude to plan before coding "significantly improves performance" and ensures the implementation aligns with your codebase conventions.

### Step 4: Approve or Refine the Plan

Review Claude's plan and provide feedback.

**If the plan looks good:**
```
Great plan! Please proceed with implementation following these steps.
```

**If adjustments are needed:**
```
The plan looks good, but let's make two changes:
1. Instead of creating a new component, integrate the indicator directly into RegisterForm
2. Use our existing useFormValidation hook instead of adding to Formik directly

Please revise the plan with these adjustments.
```

**Benefit of This Step:**
- Catch misunderstandings before code is written
- Ensure architectural alignment
- Validate approach with team patterns
- Save time by avoiding rework

### Step 5: Implement with Extended Thinking for Complex Features

For complex features, use extended thinking mode to improve Claude's reasoning.

**Example Prompt:**
```
Please implement the password strength indicator following the approved plan. Think carefully about edge cases and user experience.
```

**Keywords that trigger extended thinking:**
- "think carefully"
- "think hard"
- "ultrathink"
- "analyze thoroughly"

**What Happens:**
Claude will engage deeper reasoning, considering:
- Edge cases and error conditions
- Performance implications
- Accessibility requirements
- Cross-browser compatibility
- Security considerations

### Step 6: Implement Incrementally (For Large Features)

Break large features into smaller implementation steps.

**Example Approach:**
```
Let's implement this in stages:

Step 1: First, create just the calculatePasswordStrength utility function and its tests. Commit this separately.

[Wait for completion and review]

Step 2: Now create the PasswordStrengthIndicator component with basic styling.

[Wait for completion and review]

Step 3: Finally, integrate it into the RegisterForm with validation.

[Wait for completion and review]
```

**Benefits:**
- Easier to review each piece
- Catch issues early
- Can test incrementally
- Better git history
- Simpler to debug if something breaks

### Step 7: Use Test-Driven Development (Recommended)

For features with clear input/output expectations, write tests first.

**Example Prompt:**
```
Before implementing the password strength calculator, please write tests that define the expected behavior:

Test cases:
- "pass" → weak (too short, no uppercase, no numbers)
- "Password" → weak (no numbers)
- "Password1" → medium (meets basic requirements)
- "P@ssw0rd!123" → strong (all requirements + special chars)

Write the tests first, confirm they fail, then implement the function to make them pass.
```

**TDD Workflow:**
1. Claude writes tests based on specifications
2. Runs tests to confirm failures
3. Implements feature to pass tests
4. Verifies all tests pass
5. You review implementation

**Why TDD Works Well:**
- Ensures feature meets exact requirements
- Prevents over-engineering
- Provides built-in validation
- Creates regression protection
- Documents expected behavior

### Step 8: Iterate on Implementation

Review Claude's initial implementation and refine as needed.

**Common Refinements:**
```
The password strength indicator works well, but let's make a few improvements:
1. Add a smooth color transition animation
2. Include specific feedback messages ("Add a number", "Add uppercase letter")
3. Make the strength meter accessible with ARIA labels

Please update the implementation with these enhancements.
```

**Iteration Pattern:**
- Claude implements feature
- You test and review
- Provide specific feedback
- Claude refines
- Repeat until satisfied

**Tip:** Use screenshots for UI features. Claude can see visual output and iterate based on what the interface looks like.

### Step 9: Verify Integration and Test

Before finalizing, ensure the feature works correctly within the larger application.

**Example Verification Prompt:**
```
Please verify the password strength indicator integration:
1. Check that existing registration functionality still works
2. Ensure form validation properly blocks weak passwords
3. Test edge cases (empty input, very long passwords, special characters)
4. Verify no console errors
5. Run the existing test suite to ensure nothing broke
```

**What to Check:**
- Feature works as specified
- No regression in existing functionality
- Tests pass (both new and existing)
- No errors or warnings
- Performance is acceptable
- Accessibility standards met

### Step 10: Review Changes Before Committing

Use Claude Code Web's change review features.

**Example Prompt:**
```
Please show me a summary of all changes made for this feature.
```

**What to Review:**
- All modified files
- New files created
- Lines added/removed
- Ensure no unintended changes
- Check for debugging code or comments
- Verify no sensitive data

**Reference:** See KB-015 for detailed guidance on reviewing changes.

### Step 11: Document the Feature

Ensure proper documentation is included or updated.

**Example Prompt:**
```
Please update the documentation for this feature:
1. Add JSDoc comments to the calculatePasswordStrength function
2. Update the component documentation for RegisterForm
3. Add an entry to CHANGELOG.md
4. Update the validation utils README if there is one
```

**Documentation Checklist:**
- [ ] Code comments for complex logic
- [ ] JSDoc/docstrings for functions
- [ ] Component usage examples
- [ ] README updates if applicable
- [ ] Changelog entry
- [ ] API documentation updates

### Step 12: Commit with Meaningful Messages

Create clear, descriptive commit messages.

**Example:**
```
Please commit these changes with a clear message explaining what was added and why.
```

**Claude Will Generate:**
```
Add password strength indicator to registration form

- Implements real-time password strength validation
- Provides visual feedback (weak/medium/strong)
- Prevents form submission with weak passwords
- Includes comprehensive test coverage
- Follows existing form validation patterns
```

**Best Practice:** Review the commit message and ensure it clearly communicates the change for future reference.

## Advanced Patterns

### Pattern 1: Visual Iteration for UI Features

For features with visual components:

1. **Provide Design Reference:**
   - Share mockups, screenshots, or design specs
   - Describe desired appearance in detail

2. **Implement Initial Version:**
   - Claude creates first iteration

3. **Screenshot and Iterate:**
   - Take screenshot of result
   - Share with Claude for refinement
   - Repeat 2-3 times until perfect

**Example:**
```
Here's a screenshot of the current password indicator. Please adjust:
- Make the strength bar thicker (8px instead of 4px)
- Add more spacing between indicator and input (12px)
- Use our brand colors: red #E74C3C, yellow #F39C12, green #27AE60
```

### Pattern 2: Multi-Step Feature Checklist

For complex features with many requirements, use markdown checklists.

**Example:**
```
Implement user profile editing feature with these requirements:

**Core Functionality:**
- [ ] Create EditProfile component
- [ ] Add form fields for name, email, bio, avatar
- [ ] Implement form validation
- [ ] Connect to PUT /api/users/:id endpoint
- [ ] Show success/error feedback

**User Experience:**
- [ ] Add loading state during save
- [ ] Disable form during submission
- [ ] Preserve unsaved changes warning
- [ ] Add cancel button to revert changes

**Testing:**
- [ ] Unit tests for validation logic
- [ ] Component tests for EditProfile
- [ ] Integration test for save flow
- [ ] Test error handling

Please implement these in order, committing each major section separately.
```

### Pattern 3: Using Subagents for Complex Features

For features requiring deep investigation or parallel work:

**Example:**
```
This feature requires understanding our authentication system in detail. Please use a subagent to investigate our auth implementation and report back on:
- How tokens are currently managed
- Where session data is stored
- What auth middleware we use

Then use those findings to plan the new "remember me" feature.
```

**When to Use Subagents:**
- Complex research needed
- Investigating unfamiliar parts of codebase
- Verifying implementation details
- Parallel independent tasks

### Pattern 4: Custom Slash Commands for Repeated Workflows

For features you add frequently, create reusable prompts.

**Example: Create `.claude/commands/add-feature.md`:**
```markdown
When I ask you to add a feature:

1. First, explore the relevant parts of the codebase
2. Create an implementation plan
3. Wait for my approval before coding
4. Implement following our patterns:
   - Use TypeScript with strict types
   - Follow our naming conventions (camelCase for functions)
   - Add tests for all new functions
   - Use our existing UI components where possible
5. Update documentation
6. Run tests before committing

Always ask clarifying questions if requirements are ambiguous.
```

**Usage:**
```
/add-feature password strength indicator for registration
```

## Expected Results

After following this workflow for adding a new feature, you should have:

1. **Well-Integrated Code:**
   - Feature works seamlessly with existing code
   - Follows project conventions and patterns
   - Uses existing utilities and components where appropriate

2. **Comprehensive Testing:**
   - Unit tests for new functions
   - Component/integration tests for new features
   - All tests passing

3. **Proper Documentation:**
   - Code comments for complex logic
   - Updated relevant documentation files
   - Clear commit messages

4. **Clean Git History:**
   - Logical commits that tell a story
   - No debugging code or temporary files
   - Changes isolated to feature branch

5. **Production-Ready Code:**
   - Error handling implemented
   - Edge cases considered
   - Performance optimized
   - Accessibility requirements met

## Troubleshooting

### Common Issue 1
**Problem**: Claude jumps straight to coding without understanding the codebase
**Solution**:
- Always explicitly ask Claude to explore first
- Use prompts like "Read the relevant files before implementing"
- Request a summary of findings before proceeding
- Reference the Explore → Plan → Code workflow explicitly
- Use phrases like "Don't write code yet, just research"

### Common Issue 2
**Problem**: Implementation doesn't follow project patterns or conventions
**Solution**:
- Create a `.claude/CLAUDE.md` file documenting your conventions
- Ask Claude to reference similar existing code
- Request a plan review before implementation
- Point Claude to example implementations: "Follow the pattern used in UserProfile.js"
- Include code style guidelines in your task description

### Common Issue 3
**Problem**: Feature is too complex and Claude gets overwhelmed
**Solution**:
- Break the feature into smaller sub-features
- Implement incrementally with separate commits
- Use checklist approach for tracking progress
- Consider TDD to keep scope focused
- Simplify initial implementation, then iterate

### Common Issue 4
**Problem**: Tests are missing or inadequate
**Solution**:
- Explicitly request tests in your task description
- Use TDD workflow - write tests first
- Ask Claude to "write comprehensive tests covering edge cases"
- Reference existing test files: "Follow the test patterns in auth.test.js"
- Review test coverage and request additional tests if needed

### Common Issue 5
**Problem**: Feature works but breaks existing functionality
**Solution**:
- Always run full test suite after implementation
- Ask Claude to verify no regressions
- Test the feature in context of the larger application
- Review all changed files carefully
- Use git diff to spot unintended changes

### Common Issue 6
**Problem**: Claude's plan seems off or misunderstands requirements
**Solution**:
- This is exactly why you review the plan before implementation!
- Provide specific corrections and ask for a revised plan
- Add more context or clarify ambiguous requirements
- Share examples or reference implementations
- Break down requirements into smaller, clearer pieces

## Additional Tips

**Start Small:** If you're new to Claude Code Web, start with smaller features to build confidence in the workflow.

**Use Context Effectively:** The more Claude understands your codebase, the better implementations you'll get. Take time for the exploration phase.

**Leverage Existing Code:** Always ask Claude to follow patterns from similar existing implementations rather than inventing new approaches.

**Iterate Freely:** Don't expect perfection on the first try. The iterative approach leads to better results than trying to specify everything upfront.

**Clear Context Regularly:** After completing a feature, use `/clear` to start fresh for the next feature. This prevents context confusion.

**Think in User Stories:** Frame features as user stories when possible: "As a user, I want to see password strength so that I can create a secure password."

**Review Before Merging:** Always review changes on GitHub (see KB-015) before merging to main branch.

**Keep Sessions Focused:** One feature per session works better than mixing multiple unrelated features.

**Save Successful Prompts:** When you find prompts that work well for your codebase, save them as slash commands for reuse.

**Communicate Constraints:** Tell Claude about performance requirements, browser support, accessibility needs, or other constraints upfront.

**Use Visual Feedback:** For UI features, screenshots help Claude iterate faster than verbal descriptions alone.

## Related Articles
- KB-007: How to describe a task effectively for Claude to understand
- KB-015: How to view changes made by Claude Code in your repository
- KB-021: How to provide additional context to Claude during a task
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-023: How to approve or reject changes proposed by Claude
- KB-024: How to ask Claude to revise its approach to a problem
- KB-043: How to configure project-specific settings in .claude/settings.json

## Sources
- Claude Code Best Practices - Anthropic - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code on the web - Anthropic - https://claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
- Building agents with the Claude Agent SDK - Anthropic - https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk (Accessed: November 16, 2025)
- My 7 essential Claude Code best practices for production-ready AI in 2025 - eesel AI - https://www.eesel.ai/blog/claude-code-best-practices (Accessed: November 16, 2025)
- How I use Claude Code (+ my best tips) - Builder.io - https://www.builder.io/blog/claude-code (Accessed: November 16, 2025)
- Claude Code 2.0 Best Practices for AI Coding: Developer Workflow 2025 - skywork.ai - https://skywork.ai/blog/claude-code-2-0-best-practices-ai-coding-workflow-2025/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
