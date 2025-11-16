# How to migrate code between different frameworks or versions

**Article ID**: KB-059
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 30-60 minutes

## Overview
Migrating code between frameworks (like React to Vue, Angular to React) or upgrading to newer versions (Python 2 to 3, Node 14 to 20) is a complex task that requires careful planning and execution. This guide teaches you how to leverage Claude Code Web to systematically migrate codebases—from individual files to thousands of components—while maintaining functionality and minimizing errors. You'll learn proven workflows including exploration-first assessment, checklist-based tracking, headless automation for large-scale migrations, and parallel processing strategies.

## Prerequisites
- Claude Code Web Pro/Max account with an active session
- A connected repository with code that needs migration
- Basic understanding of both source and target frameworks/versions
- Familiarity with describing tasks effectively (KB-007)
- Understanding of how to provide context to Claude (KB-021)
- Access to documentation for the target framework/version

## Understanding Migration with Claude Code Web

### Types of Migrations Claude Can Help With
- **Framework migrations**: React to Vue, Angular to React, Express to Fastify, etc.
- **Version upgrades**: Python 2 to 3, Node 14 to 20, React 16 to 18, etc.
- **Language migrations**: JavaScript to TypeScript, CommonJS to ES Modules
- **Library replacements**: Moment.js to date-fns, Lodash to native methods
- **Architecture migrations**: Class components to functional hooks, callbacks to async/await

### Claude Code Web's Strengths for Migration
- **Pattern recognition**: Understands common migration patterns across frameworks
- **Multi-file operations**: Can update hundreds of files consistently
- **Context awareness**: Maintains architectural understanding throughout migration
- **Documentation integration**: Can reference official migration guides and documentation
- **Error recovery**: Identifies and fixes migration-related issues systematically

## Steps

### Step 1: Assess and Explore the Migration Scope

Before making any changes, have Claude analyze your codebase to understand the full scope of the migration.

**Ask Claude to assess:**
```
Analyze the entire codebase and identify all files that need to be migrated from React to Vue.

For each file, note:
- Current framework patterns used (class components, hooks, etc.)
- Complexity level (simple, medium, complex)
- Dependencies on other files
- Any migration challenges or edge cases

Create a comprehensive list organized by priority.
```

Claude will examine your project structure and provide a detailed migration assessment without modifying anything.

**Example assessment output:**
```markdown
## Migration Assessment: React to Vue

### High Priority (Core Components - 15 files)
- src/components/UserProfile.jsx - Medium complexity, uses hooks
- src/components/Dashboard.jsx - Complex, multiple child components
- src/components/Navigation.jsx - Simple, no state management

### Medium Priority (Feature Components - 23 files)
- src/features/auth/LoginForm.jsx - Medium, form handling
- src/features/cart/ShoppingCart.jsx - Complex, state management
[...]

### Low Priority (Utility Components - 8 files)
- src/components/ui/Button.jsx - Simple, minimal logic
[...]

### Migration Challenges Identified:
1. Redux state management needs Vuex/Pinia equivalent
2. React Router requires Vue Router migration
3. Custom hooks need conversion to Vue composables
4. PropTypes should become Vue props validation
```

**Pro Tip**: The explore-first approach prevents surprises mid-migration and helps estimate time and effort accurately.

### Step 2: Create Migration Documentation in CLAUDE.md

Set up a `.claude/CLAUDE.md` file with migration-specific guidelines. Claude automatically loads this file, ensuring consistent decisions throughout the migration.

**Create the file:**
```bash
mkdir -p .claude
touch .claude/CLAUDE.md
```

**Add migration context:**
```markdown
# Migration Guide: React to Vue 3

## Migration Goals
- Migrate from React 18 to Vue 3 with Composition API
- Maintain all existing functionality
- Improve code organization during migration
- Achieve feature parity before removing React code

## Framework Equivalents

### Component Structure
- React functional components → Vue `<script setup>` components
- JSX → Vue template syntax (or JSX if preferred)
- React hooks → Vue Composition API (composables)

### State Management
- useState → ref() and reactive()
- useEffect → watch() and watchEffect()
- useContext → provide/inject
- Redux → Pinia (preferred over Vuex)

### Routing
- React Router → Vue Router 4
- Route parameters: props.match.params → useRoute().params

### Styling
- CSS Modules (current) → CSS Modules (maintain)
- styled-components → `<style scoped>` in Vue SFC

## Migration Patterns

### Class to Composition API
```javascript
// React Hook
const [count, setCount] = useState(0)

// Vue Composition API
const count = ref(0)
```

### Props Validation
```javascript
// React PropTypes
Component.propTypes = { name: PropTypes.string.required }

// Vue Props
defineProps<{ name: string }>()
```

## Testing Requirements
- Maintain or improve test coverage during migration
- Use Vitest (Vue ecosystem) instead of Jest
- Update all component tests to Vue Testing Library
- Run `npm test` after migrating each component

## Files Not to Migrate
- /src/legacy/* - Keep as-is for backward compatibility
- /config/* - Build configuration migrates separately
```

### Step 3: Choose Your Migration Strategy

Based on your project size and requirements, select the appropriate approach.

#### Strategy A: Sequential File-by-File Migration (Small to Medium Projects)

Best for: Projects with <100 files, tight coupling between components

**Request a migration plan:**
```
Create a step-by-step plan to migrate this project from React to Vue:
1. Start with the simplest, most independent components
2. Progress to more complex components with dependencies
3. Migrate shared utilities and services
4. Update state management
5. Migrate routing configuration
6. Update build configuration and dependencies

For each step, list specific files and estimated complexity.
```

**Execute systematically:**
```
Migrate the Button component from React to Vue following our CLAUDE.md guidelines.

Steps:
1. Create src/components/ui/Button.vue
2. Convert the component logic and template
3. Update prop validation
4. Preserve all existing functionality and styling
5. Update the component's unit tests
6. Show me the before/after comparison
```

#### Strategy B: Parallel Migration with Interop Layer (Large Projects)

Best for: Projects with >100 files, need to maintain functionality during migration

**Set up dual framework support:**
```
Set up the project to support both React and Vue components simultaneously:
1. Configure Vite/Webpack to handle both .jsx and .vue files
2. Create an adapter layer that allows Vue components to be used in React (and vice versa)
3. Update the entry point to support both frameworks
4. Document the interop pattern in CLAUDE.md

This allows us to migrate incrementally without breaking the app.
```

**Migrate in phases:**
```
Phase 1: Migrate all leaf components (no children) from React to Vue
Phase 2: Migrate mid-level components, using the interop layer for dependencies
Phase 3: Migrate parent/container components
Phase 4: Migrate routing and state management
Phase 5: Remove React dependencies and interop layer
```

#### Strategy C: Headless Automation for Large-Scale Migration (Very Large Projects)

Best for: Projects with 500+ files, repetitive migration patterns

**Generate a task list:**
```
Generate a complete list of all React components that need migration to Vue.
Output as a JSON array with this structure:
[
  {"file": "src/components/Button.jsx", "priority": "high", "complexity": "low"},
  {"file": "src/components/Form.jsx", "priority": "high", "complexity": "medium"},
  ...
]

Save this to migration-tasks.json
```

**Use headless mode for batch processing:**

Claude Code's headless mode allows you to programmatically execute migration tasks:

```bash
# Example: Migrate files in batch using headless mode
cat migration-tasks.json | jq -r '.[] | .file' | while read file; do
  claude -p "migrate $file from React to Vue. Follow patterns in CLAUDE.md. When done, return OK if successful, FAIL if not" \
    --allowedTools Edit,Read,Bash
done
```

**Pro Tip**: Start with a small batch (5-10 files) to refine your prompt before processing hundreds of files. Iterate on the prompt based on success rate.

### Step 4: Create and Track a Migration Checklist

For visibility and progress tracking, have Claude maintain a comprehensive checklist.

**Request a checklist:**
```
Create a detailed migration checklist for moving from React to Vue.
Include all components, utilities, tests, and configuration files.
Organize by phase and priority.
Track this in MIGRATION_CHECKLIST.md
```

**Claude will create something like:**
```markdown
# React to Vue Migration Checklist

## Phase 1: Foundation (Week 1)
- [ ] Set up Vue 3 in package.json
- [ ] Configure Vite for Vue support
- [ ] Create .claude/CLAUDE.md with migration guidelines
- [ ] Set up Vue Router
- [ ] Configure Pinia for state management
- [ ] Create component adapter for React/Vue interop

## Phase 2: UI Components (Week 2-3)
- [ ] Migrate Button.jsx → Button.vue
- [ ] Migrate Input.jsx → Input.vue
- [ ] Migrate Modal.jsx → Modal.vue
- [ ] Update component tests for Button
- [ ] Update component tests for Input
- [ ] Update component tests for Modal

## Phase 3: Feature Components (Week 4-5)
- [ ] Migrate LoginForm.jsx → LoginForm.vue
- [ ] Migrate Dashboard.jsx → Dashboard.vue
- [ ] Migrate UserProfile.jsx → UserProfile.vue
[...]

## Phase 4: State & Routing (Week 6)
- [ ] Migrate Redux store to Pinia
- [ ] Update all store imports
- [ ] Migrate React Router to Vue Router
- [ ] Update all navigation/routing logic

## Phase 5: Testing & Cleanup (Week 7)
- [ ] Run full test suite
- [ ] Fix all failing tests
- [ ] Remove React dependencies
- [ ] Remove interop layer
- [ ] Update documentation
```

**Track progress:**
```
Update the MIGRATION_CHECKLIST.md by marking all Phase 1 items as complete.
Show me the current progress summary.
```

### Step 5: Migrate with Reference Documentation

Claude can reference official migration guides while working.

**Provide documentation links:**
```
Migrate the ShoppingCart component from React to Vue 3.

Reference the official Vue 3 migration guide: https://v3-migration.vuejs.org/
Specifically follow patterns for:
- Composition API best practices
- State management with ref/reactive
- Event handling

Ensure the migrated component follows Vue 3 idioms, not just a direct translation.
```

**For version upgrades:**
```
Upgrade this Python 2.7 file to Python 3.10.

Reference: https://docs.python.org/3/howto/pyporting.html

Key changes needed:
- print statements → print() function
- dict.iteritems() → dict.items()
- unicode/str unification
- Update exception handling syntax
- Integer division changes

Run the file through 2to3 mentally and apply modern Python 3.10 patterns.
```

### Step 6: Test Each Migration Step

Validate migrations incrementally to catch issues early.

**Request test-driven migration:**
```
Migrate the validateEmail function from the old regex library to the new validator library.

Steps:
1. First, create comprehensive tests for the current validateEmail behavior
2. Ensure tests pass with the current implementation
3. Migrate to the new validator library
4. Run tests to verify behavior is preserved
5. If tests fail, show me what changed and why
```

**For component migrations:**
```
Migrate the UserProfile component from React to Vue.

After migration:
1. Run the component's unit tests (migrated to Vitest)
2. Verify visual rendering in the browser
3. Test all user interactions (click, input, etc.)
4. Confirm props and events work correctly
5. Check for console errors or warnings

Report any issues found during testing.
```

### Step 7: Handle State Management Migration

State management often requires special attention during framework migrations.

**Analyze state usage:**
```
Analyze how Redux is used throughout the application:
1. List all Redux actions, reducers, and selectors
2. Identify components that connect to the store
3. Map Redux patterns to Pinia equivalents
4. Identify any complex middleware that needs special handling
```

**Migrate state management:**
```
Migrate the user authentication Redux store to Pinia.

Current Redux files:
- src/store/auth/actions.js
- src/store/auth/reducers.js
- src/store/auth/selectors.js

Create Pinia equivalent:
- src/stores/auth.js (using setup store pattern)

Maintain all existing functionality:
- Login/logout actions
- User state persistence
- Token management
- Auth status checks
```

### Step 8: Use Git Worktrees for Parallel Migration (Advanced)

For very large codebases, run multiple Claude sessions on different parts of the migration simultaneously.

**Set up parallel workstreams:**
```bash
# Main session migrates UI components
git worktree add ../project-ui-migration feature/migrate-ui-components

# Second session migrates business logic
git worktree add ../project-logic-migration feature/migrate-business-logic

# Third session migrates state management
git worktree add ../project-state-migration feature/migrate-state
```

Connect each worktree to a separate Claude Code Web session and work in parallel.

**Coordinate between sessions:**
```markdown
# MIGRATION_COORDINATION.md

## Session 1: UI Components (@alice)
Status: Migrating Button, Input, Modal components
Progress: 15/47 components complete
Estimated completion: Nov 18

## Session 2: Business Logic (@bob)
Status: Migrating service layer and utilities
Progress: 8/23 files complete
Estimated completion: Nov 19

## Session 3: State Management (@carol)
Status: Migrating Redux to Pinia
Progress: 2/5 stores complete
Estimated completion: Nov 20

## Dependencies:
- Session 1 blocked on Session 3 for AuthButton component
- Session 2 can proceed independently
```

**When to use parallel migration:**
- Codebase has >500 files to migrate
- Different modules are independent
- Tight timeline requires maximum throughput
- Multiple developers available to supervise sessions

### Step 9: Handle Edge Cases and Complex Patterns

Some code patterns require special handling during migration.

**Identify complex patterns:**
```
Identify all uses of higher-order components (HOCs) in the React codebase.
These will need to be migrated to Vue composables or provide/inject.

For each HOC:
1. Explain what it does
2. Propose the Vue 3 equivalent pattern
3. List all components that use it
```

**Migrate complex patterns:**
```
The withAuth HOC wraps components to check authentication.

React HOC:
- File: src/hocs/withAuth.jsx
- Usage: Wraps 23 components

Migrate to Vue 3:
- Create a composable: src/composables/useAuth.js
- Update all 23 components to use the composable
- Remove the old HOC
- Maintain identical authentication logic
```

**Handle framework-specific features:**
```
The React app uses Portals for modal rendering.
Migrate this to Vue's Teleport feature.

Files affected:
- src/components/Modal.jsx
- src/components/Tooltip.jsx
- src/components/Dropdown.jsx

Ensure modals still render to document.body and maintain z-index behavior.
```

### Step 10: Update Build Configuration and Dependencies

After code migration, update the tooling configuration.

**Migrate build tools:**
```
Update the build configuration from Create React App to Vite for Vue 3.

Steps:
1. Remove Create React App dependencies
2. Add Vite and Vue dependencies
3. Create vite.config.js with appropriate plugins
4. Update package.json scripts (start → dev, build, preview)
5. Migrate environment variables (.env files)
6. Update index.html to work with Vite
7. Test the dev server and production build
```

**Update dependencies:**
```
Review and update all dependencies for Vue compatibility:

1. List all current React-specific dependencies
2. Find Vue equivalents or compatible alternatives
3. Update package.json
4. Run npm install
5. Check for any breaking changes in updated packages
6. Update imports throughout the codebase
```

### Step 11: Validate the Complete Migration

Before considering the migration complete, perform comprehensive validation.

**Run validation checklist:**
```
Perform complete migration validation:

1. Build the application (npm run build)
   - Should complete without errors
   - Check bundle size compared to React version

2. Run all tests (npm test)
   - Unit tests should pass
   - Integration tests should pass
   - E2E tests should pass

3. Manual testing
   - Test all major user flows
   - Check all routes/pages
   - Verify state persistence
   - Test edge cases and error handling

4. Performance check
   - Compare load times to React version
   - Check for memory leaks
   - Verify no console errors

5. Code quality
   - Run linter (npm run lint)
   - Check for any TODO or FIXME comments
   - Verify all migration checklist items are complete

Report any issues found.
```

### Step 12: Document the Migration and Clean Up

Create comprehensive documentation for the completed migration.

**Request documentation:**
```
Create migration documentation:

1. MIGRATION_SUMMARY.md with:
   - What was migrated (React to Vue 3)
   - Timeline and effort (3 weeks, 300 files)
   - Key decisions made
   - Challenges encountered and solutions
   - Performance comparison before/after
   - Breaking changes (if any)

2. Update main README.md with:
   - New tech stack (Vue 3 + Pinia + Vue Router)
   - Updated setup instructions
   - New development commands
   - Testing approach

3. Update CONTRIBUTING.md with:
   - Vue component patterns to follow
   - State management patterns
   - Testing requirements
```

**Clean up migration artifacts:**
```
Clean up temporary migration files:

1. Remove migration-tasks.json
2. Remove MIGRATION_CHECKLIST.md (archive it first)
3. Remove any dual-framework interop code
4. Remove old React dependencies from package.json
5. Clean up any temporary feature flags
6. Remove migration-specific comments and TODOs

Commit these cleanup changes with a clear message.
```

## Expected Results

After successfully migrating code with Claude Code Web, you should achieve:

### Complete Migration
- **All files migrated**: Every file converted to the target framework/version
- **Full functionality preserved**: Application works identically to the original
- **Tests passing**: Entire test suite validates the migration
- **Clean codebase**: No remnants of the old framework (unless intentionally preserved)
- **Updated documentation**: README and guides reflect the new stack

### Code Quality Improvements
- **Modern patterns**: Code uses current best practices for the target framework
- **Improved organization**: Migration provided opportunity to restructure
- **Better type safety**: TypeScript types added if migrating to TS
- **Enhanced performance**: Newer framework/version may offer performance benefits
- **Reduced technical debt**: Legacy patterns eliminated during migration

### Measurable Success Metrics
- **Build succeeds**: `npm run build` completes without errors
- **All tests pass**: 100% of test suite succeeds
- **No console errors**: Application runs without runtime errors
- **Feature parity**: All original features work in the new version
- **Performance maintained or improved**: Load times comparable or better

## Troubleshooting

### Common Issue 1
**Problem**: Migration breaks functionality in unexpected ways
**Solution**:
- Review the test results to identify what specifically broke
- Ask Claude: "Why did this behavior change during migration? Show me the before/after code."
- Check if the target framework has different behavior for the same concept
- Consult migration guides for known gotchas
- Consider if the original code relied on framework-specific quirks
- Add explicit tests for the broken behavior before fixing

### Common Issue 2
**Problem**: Some framework patterns don't have direct equivalents
**Solution**:
- Ask Claude to explain the pattern: "What does this React HOC accomplish?"
- Request alternatives: "What are 3 ways to achieve this in Vue 3?"
- Choose the most idiomatic approach for the target framework
- Don't try to force direct 1:1 translations—embrace the new framework's patterns
- Document the migration decision in CLAUDE.md for consistency

### Common Issue 3
**Problem**: Large batch migration produces inconsistent results
**Solution**:
- Review the first 5-10 migrated files carefully
- Identify patterns in the inconsistencies
- Refine the CLAUDE.md guidelines with specific examples
- Update the migration prompt with clearer instructions
- Migrate smaller batches (10-20 files) instead of hundreds at once
- Run tests more frequently to catch issues early

### Common Issue 4
**Problem**: Build configuration issues after migration
**Solution**:
- Start with the simplest possible build configuration
- Add complexity incrementally (plugins, optimizations, etc.)
- Reference official starter templates for the target framework
- Ask Claude to compare your config against the recommended setup
- Check for conflicting dependencies or version mismatches
- Verify environment variables are properly loaded

### Common Issue 5
**Problem**: Performance degrades after migration
**Solution**:
- Profile both versions to identify specific bottlenecks
- Check for common performance anti-patterns in the new framework
- Ask Claude: "Analyze this component for Vue 3 performance issues"
- Verify you're using production builds for comparison
- Check bundle sizes—unnecessary dependencies may have been added
- Review if migration introduced unnecessary re-renders or computations

### Common Issue 6
**Problem**: Tests are difficult to migrate
**Solution**:
- Migrate test framework first (e.g., Jest to Vitest)
- Ensure test tooling works before migrating individual tests
- Focus on testing behavior, not implementation details
- Ask Claude to migrate test utilities and helpers first
- Some tests may need complete rewrites—that's okay
- Consider this an opportunity to improve test quality

### Common Issue 7
**Problem**: Third-party library dependencies break during migration
**Solution**:
- Identify which libraries are framework-specific vs. framework-agnostic
- Find equivalent libraries for the target framework
- Check if libraries have versions that support both frameworks
- Consider building thin abstraction layers for critical dependencies
- Test each dependency migration independently
- Update one library at a time rather than all at once

## Additional Tips

- **Start with a spike**: Migrate one complete vertical slice (component + state + tests + routing) to validate the approach before scaling

- **Maintain a running app**: Use feature flags or parallel builds to keep the original version running while migrating

- **Document decisions**: Record why you chose specific migration patterns in CLAUDE.md for consistency

- **Set up comparison environments**: Run both old and new versions side-by-side to verify functionality

- **Use official migration guides**: Reference framework maintainers' guides—they know the gotchas

- **Preserve git history**: Use `git mv` when renaming files so git tracks the history

- **Automate validation**: Create scripts that verify common migration patterns are followed

- **Take breaks on large migrations**: Spread work across multiple sessions to maintain focus and quality

- **Celebrate incremental progress**: Large migrations are marathons—acknowledge milestones

- **Learn the target framework**: Don't just translate—embrace the new framework's idioms

- **Consider hybrid approaches**: Not everything needs to migrate at once—critical paths first

- **Budget extra time**: Migrations always take longer than estimated—plan for 1.5-2x the initial estimate

- **Get peer review**: Have another developer review migrated code for quality

- **Monitor production carefully**: After deploying migrated code, watch error rates and performance closely

## Related Articles
- KB-007: How to describe tasks effectively for Claude to understand
- KB-021: How to provide additional context to Claude during a task
- KB-022: How to intervene and guide Claude mid-session when it encounters issues
- KB-053: How to refactor code for better maintainability
- KB-043: How to configure project-specific settings in .claude/settings.json
- KB-054: How to write unit tests for code

## Sources
- Claude Code Best Practices - Anthropic - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Rewriting whole apps with AI - Medium - https://medium.com/@tombeckenham/rewriting-whole-apps-with-ai-69e879ad5f9c (Accessed: November 16, 2025)
- React to Vue Conversion with Claude, Gemini, and ChatGPT - DEV Community - https://dev.to/rimutaka/react-to-vue-conversion-with-claude-gemini-and-chatgpt-2a (Accessed: November 16, 2025)
- Claude Code on the web - Anthropic - https://www.anthropic.com/news/claude-code-on-the-web (Accessed: November 16, 2025)
- How to Use Claude Code Plugin for Safe Refactoring & Migration - Skywork AI - https://skywork.ai/blog/how-to-use-claude-code-plugin-for-refactoring-migration-guide/ (Accessed: November 16, 2025)
- Common workflows - Claude Code Docs - https://docs.anthropic.com/en/docs/claude-code/common-workflows (Accessed: November 16, 2025)
- Migrate to Claude Agent SDK - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/sdk/migration-guide (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
