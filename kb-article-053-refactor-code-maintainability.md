# How to refactor code for better maintainability

**Article ID**: KB-053
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 20-40 minutes

## Overview
Refactoring improves code structure, readability, and maintainability without changing functionality. This guide teaches you how to use Claude Code Web systematically to refactor codebases of any size—from single functions to entire projects—while maintaining quality and avoiding regressions. You'll learn proven workflows including exploration-first approaches, checklist-based tracking, and test-driven refactoring.

## Prerequisites
- Claude Code Web Pro/Max account with an active session
- A connected repository with code that needs refactoring
- Basic understanding of your codebase's structure and language
- Familiarity with describing tasks effectively (KB-007)
- Understanding of how to provide context to Claude (KB-021)

## Understanding Refactoring with Claude Code Web

### What Makes Good Refactoring?
- **Preserves functionality**: External behavior remains unchanged
- **Improves structure**: Code becomes more readable and organized
- **Enhances maintainability**: Future changes become easier
- **Reduces technical debt**: Removes code smells and anti-patterns

### Claude Code Web's Strengths for Refactoring
- **Full project awareness**: Understands relationships between files
- **Pattern recognition**: Identifies repeated code and inconsistencies
- **Multi-file operations**: Safely refactors across multiple files simultaneously
- **Context preservation**: Maintains architectural decisions throughout changes

## Steps

### Step 1: Explore Before You Refactor

Before making any changes, have Claude analyze your codebase to understand existing patterns and identify refactoring opportunities.

**Ask Claude to explore:**
```
Analyze the authentication code in the /src/auth directory. Identify:
- Repeated patterns or duplicated code
- Inconsistent coding styles
- Functions that are too long or complex
- Hard-coded values that should be constants
- Opportunities for better organization
```

Claude will read files, examine structure, and provide a comprehensive analysis without modifying anything.

**Pro Tip**: The explore-first approach significantly improves refactoring outcomes by ensuring Claude understands the full context before making changes.

### Step 2: Create a CLAUDE.md File for Project Context

Add a `.claude/CLAUDE.md` file to your repository root. Claude automatically loads this file at session start, ensuring consistent refactoring decisions.

**Create the file:**
```bash
touch .claude/CLAUDE.md
```

**Add refactoring-relevant context:**
```markdown
# Project Guidelines for Claude Code

## Code Style
- Use functional components with hooks (not class components)
- Prefer async/await over promises
- Maximum function length: 50 lines
- Use descriptive variable names (no single letters except loop counters)

## Architecture Patterns
- Services in /services handle API calls
- Utils in /utils are pure functions with no side effects
- Components in /components are presentational only
- State management uses React Context (not Redux)

## Testing Requirements
- All refactored functions must maintain existing test coverage
- Run `npm test` before committing
- Integration tests are in /tests/integration

## Known Issues to Preserve
- The legacy payment processing in payment.legacy.js must remain unchanged
- Auth tokens are stored in sessionStorage (not localStorage) for security
```

This ensures Claude applies consistent patterns throughout all refactoring tasks.

### Step 3: Plan Your Refactoring Strategy

For complex refactoring, ask Claude to create a detailed plan before writing any code.

**Request a refactoring plan:**
```
Create a step-by-step plan to refactor the user management module:
1. Analyze all files in /src/users
2. Identify shared functionality that can be extracted
3. Propose a new file structure
4. List which files will be created, modified, or removed
5. Identify potential risks or breaking changes
```

Claude will provide a comprehensive plan. Review it, ask questions, and approve before proceeding.

**Why planning matters**: Research shows that planning first improves performance for problems requiring deeper architectural thinking, preventing hasty implementations that create new technical debt.

### Step 4: Use Checklists for Multi-Step Refactoring

For refactoring tasks with multiple steps, have Claude create and track a checklist.

**Request a checklist:**
```
Refactor the shopping cart functionality to separate business logic from UI components. Create a checklist of all steps needed.
```

**Claude will create something like:**
```markdown
## Shopping Cart Refactoring Checklist

- [ ] Extract cart calculation logic to /services/cartService.js
- [ ] Create pure functions for addItem, removeItem, updateQuantity
- [ ] Move price calculation to /utils/pricing.js
- [ ] Update CartComponent to use new service methods
- [ ] Update CheckoutComponent to use new service methods
- [ ] Add unit tests for new service functions
- [ ] Run integration tests to verify functionality
- [ ] Remove old inline logic from components
```

Ask Claude to work through the checklist, updating it as tasks are completed. This provides visibility into progress and ensures no steps are missed.

### Step 5: Refactor with Test-Driven Safety

When refactoring code with existing tests, use those tests as your safety net.

**Approach for tested code:**
```
Refactor the calculateDiscount function in pricing.js to be more readable and efficient.
Before making changes:
1. Run the existing tests to establish a baseline
2. Make the refactoring changes
3. Run tests again to verify behavior is preserved
4. If tests fail, explain what changed and why
```

**For untested code, add tests first:**
```
The validateEmail function in utils.js has no tests. Before refactoring:
1. Write comprehensive tests covering current behavior (including edge cases)
2. Verify tests pass with current implementation
3. Refactor the function for better clarity
4. Confirm tests still pass
```

This test-first approach creates measurable targets and prevents regressions.

### Step 6: Extract Repeated Code into Reusable Functions

One of the most common refactoring patterns is extracting duplicated logic.

**Identify duplication:**
```
Find all places in the codebase where we validate form inputs. Show me the repeated patterns.
```

**Request extraction:**
```
Extract the repeated form validation logic from login.js, register.js, and profile.js
into a reusable validateForm() function in /utils/validation.js.

Requirements:
- The new function should accept a form object and validation rules
- Return an object with {isValid: boolean, errors: {...}}
- Update all three files to use the new function
- Preserve existing validation behavior exactly
```

Claude will create the utility function and update all references safely.

### Step 7: Modernize Code Patterns

Refactor legacy code to use modern language features and best practices.

**Example: Converting callbacks to async/await:**
```
Refactor the file upload functions in /src/upload to use async/await instead of callbacks.
Files to update:
- uploadImage.js
- uploadDocument.js
- uploadBatch.js

Ensure error handling is preserved using try/catch blocks.
```

**Example: Converting class components to functional components:**
```
Convert the UserProfile class component to a functional component with hooks.
Current file: /src/components/UserProfile.js

Maintain all existing functionality:
- State management (name, email, avatar)
- Lifecycle methods (componentDidMount → useEffect)
- Event handlers
- Prop types and default props
```

### Step 8: Improve Code Organization and Structure

Refactor file structure to better represent the application architecture.

**Reorganization request:**
```
The /src/components directory has 47 files with no organization.
Propose and implement a better structure:
- Group related components into subdirectories
- Separate page-level components from reusable UI components
- Create index.js files for cleaner imports
- Update all import statements throughout the codebase
```

Claude can safely move files and update all references across your project.

### Step 9: Refactor for Performance (When Needed)

When you've identified performance bottlenecks, Claude can help optimize while maintaining functionality.

**Request performance refactoring:**
```
The ProductList component re-renders too frequently. Refactor to improve performance:
1. Identify expensive calculations that should be memoized
2. Add React.memo where appropriate
3. Use useCallback for event handlers passed to child components
4. Implement useMemo for filtered/sorted data
5. Measure before/after render performance
```

Always measure performance before and after to confirm improvements.

### Step 10: Use Git Worktrees for Parallel Refactoring (Advanced)

For large codebases, you can run multiple Claude sessions on different refactoring tasks simultaneously.

**Setup git worktrees:**
```bash
# Main session refactors authentication
git worktree add ../project-auth-refactor feature/refactor-auth

# Second session refactors data visualization
git worktree add ../project-viz-refactor feature/refactor-viz
```

Open separate Claude Code Web sessions in each worktree directory. Each Claude instance works independently without conflicts.

**When to use this approach:**
- Large-scale refactoring projects with independent modules
- Tight deadlines requiring parallel work
- Experimental refactoring you want to compare against another approach

### Step 11: Review and Verify Refactored Code

After refactoring, thoroughly review the changes before committing.

**Request a summary:**
```
Summarize all changes made during this refactoring session:
- Files created, modified, or deleted
- Key structural changes
- Any behavior changes (there should be none for pure refactoring)
- Test results
```

**Verify the refactoring:**
- Run all tests: `npm test` or equivalent
- Check that the application still runs correctly
- Review diffs in version control
- Test edge cases manually if needed

### Step 12: Commit with Clear Documentation

Create a clear commit message that explains the refactoring.

**Ask Claude to help:**
```
Create a git commit for these refactoring changes with:
- A clear summary of what was refactored
- Why the refactoring improves the code
- Confirmation that functionality is preserved
```

**Example commit message:**
```
Refactor user authentication to extract shared validation logic

- Extracted repeated validation patterns into /utils/validation.js
- Reduced code duplication across login, register, and profile forms
- Improved test coverage for validation edge cases
- No functional changes; all existing tests pass

This refactoring improves maintainability and makes future validation
changes easier by centralizing the logic.
```

## Expected Results

After successfully refactoring code with Claude Code Web, you should observe:

### Code Quality Improvements
- **Reduced duplication**: Shared logic extracted to reusable functions
- **Better organization**: Files and functions logically structured
- **Improved readability**: Code is easier to understand and modify
- **Modern patterns**: Legacy code updated to current best practices
- **Consistent style**: Uniform coding conventions throughout

### Maintained Functionality
- **All tests passing**: Existing test suite validates behavior is preserved
- **No regressions**: Application functionality remains identical
- **Performance maintained or improved**: No degradation in performance
- **API contracts preserved**: Public interfaces remain unchanged

### Enhanced Maintainability
- **Easier future changes**: New features are simpler to implement
- **Clear structure**: New developers can navigate code more easily
- **Reduced technical debt**: Code smells and anti-patterns eliminated
- **Better documentation**: Code is more self-documenting

## Troubleshooting

### Common Issue 1
**Problem**: Refactoring breaks existing tests
**Solution**:
- Review what the test was actually testing—is it testing implementation details or behavior?
- If testing behavior: The refactoring changed functionality (unintended)—revert and investigate
- If testing implementation: Update the test to verify behavior with the new structure
- Consider whether the test itself needs refactoring to be less brittle

### Common Issue 2
**Problem**: Claude makes changes that don't align with project conventions
**Solution**:
- Create or update `.claude/CLAUDE.md` with explicit style guidelines
- Show Claude examples of properly styled code in your project
- Reference existing files: "Follow the same pattern used in UserService.js"
- Provide more specific instructions about expected patterns

### Common Issue 3
**Problem**: Refactoring one module creates conflicts in dependent modules
**Solution**:
- Ask Claude to analyze dependencies first: "Show me all files that import or depend on this module"
- Request comprehensive refactoring: "Update this module AND all files that depend on it"
- Use git worktree to test refactoring in isolation before merging
- Consider staging refactoring in smaller, independent steps

### Common Issue 4
**Problem**: Large refactoring task is overwhelming and unclear where to start
**Solution**:
- Break it into smaller, focused tasks
- Start with exploration: "Analyze this module and identify the top 3 refactoring priorities"
- Create a multi-session plan, tackling one priority per session
- Use the checklist approach to track progress across multiple sessions

### Common Issue 5
**Problem**: Refactored code works but is slower than the original
**Solution**:
- Ask Claude to analyze performance: "Why might this refactored version be slower?"
- Request optimization: "Refactor for both clarity AND performance"
- Consider whether extreme optimization is necessary—clear code is often better than micro-optimized code
- Use profiling tools to identify actual bottlenecks before optimizing

### Common Issue 6
**Problem**: Need to refactor legacy code with no tests
**Solution**:
- Write characterization tests first: Tests that document current behavior (even if buggy)
- Ask Claude: "Create tests that verify the current behavior of this function, including edge cases"
- Once tests are in place, refactor with confidence
- This is safer than refactoring untested code directly

## Additional Tips

- **Start small**: Begin with simple, low-risk refactoring (renaming variables, extracting constants) before tackling architectural changes

- **One refactoring at a time**: Don't mix multiple refactoring types in one session (e.g., don't reorganize files AND change logic patterns simultaneously)

- **Preserve git history**: Commit pure refactoring separately from feature changes so git blame remains useful

- **Use plan mode**: For complex refactoring, explicitly ask Claude to plan with read-only operations before writing code

- **Iterative approach**: Refactor incrementally over multiple sessions rather than attempting massive rewrites

- **Verify frequently**: Run tests after each logical refactoring step, not just at the end

- **Keep communication open**: If Claude's refactoring approach doesn't feel right, ask it to explain the reasoning or try a different approach

- **Document architectural decisions**: Update comments and documentation to explain why code is structured the way it is

- **Know when to stop**: Refactoring can be endless—focus on improvements that provide concrete value

- **Review diffs carefully**: Before committing, review every changed line to ensure the refactoring is safe

- **Consider backward compatibility**: If refactoring a library or API, ensure you're not breaking external consumers

- **Use feature flags**: For risky refactoring in production code, hide changes behind feature flags until thoroughly tested

## Related Articles
- KB-007: How to describe tasks effectively for Claude to understand
- KB-021: How to provide additional context to Claude during a task
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-024: How to ask Claude to revise its approach to a problem
- KB-043: How to configure project-specific settings in .claude/settings.json

## Sources
- Claude Code Best Practices - Anthropic - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Common workflows - Claude Code Docs - https://docs.anthropic.com/en/docs/claude-code/common-workflows (Accessed: November 16, 2025)
- Using Claude Code and Aider to Refactor Large Projects - CodeNotary - https://codenotary.com/blog/using-claude-code-and-aider-to-refactor-large-projects-enhancing-maintainability-and-scalability (Accessed: November 16, 2025)
- How to Use Claude Code for Refactoring & Clean Code - Arsturn - https://www.arsturn.com/blog/how-to-use-claude-code-to-refactor-and-clean-up-your-codebase (Accessed: November 16, 2025)
- Claude Code: A Guide With Practical Examples - DataCamp - https://www.datacamp.com/tutorial/claude-code (Accessed: November 16, 2025)
- Google Search: "Claude Code Web refactoring code maintainability 2025 best practices"

---
*This article is part of the Claude Code Web knowledge base*
