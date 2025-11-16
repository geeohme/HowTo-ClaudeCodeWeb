# How to write unit tests for existing functionality

**Article ID**: KB-054
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 20-40 minutes

## Overview
This guide explains how to use Claude Code on the web to write comprehensive unit tests for your existing codebase. Claude Code excels at test-driven development, generating tests that cover edge cases, boundary conditions, and error scenarios that developers often overlook. You'll learn the recommended workflow from Anthropic's engineering teams for creating reliable test suites.

## Prerequisites
- Active Claude Pro or Max subscription (Claude Code on the web is available in research preview for these tiers)
- Access to Claude Code on the web at claude.ai
- A GitHub repository with existing code that needs test coverage
- Basic understanding of unit testing concepts in your programming language
- GitHub OAuth connection configured (see KB article on connecting repositories)

## Steps

### Step 1: Connect Your Repository
Navigate to claude.ai and click into the "Code" tab at the top of the interface.

Connect your GitHub repository via OAuth if you haven't already. This grants Claude permission to access your repository without storing credentials long-term. Claude will clone your repository and run any configured Claude hooks for initialization.

**Tip**: Claude uses the default branch on your GitHub repository, so ensure your default branch is set correctly.

### Step 2: Set Up Your CLAUDE.md Configuration (Optional but Recommended)
Create or update a `CLAUDE.md` file in your repository root. This serves as your project's "README for Claude" and is automatically pulled into context at session start.

Include testing expectations in your CLAUDE.md:

```markdown
# Testing Guidelines

- Use deterministic fixtures and avoid real network calls in unit tests
- Stub or mock external dependencies as needed
- Follow test-driven development principles
- Ensure all tests are independent and can run in any order
- Use meaningful test names that describe the behavior being tested
```

This ensures Claude follows your project's testing standards consistently.

### Step 3: Identify the Functionality to Test
Be specific about what functionality you want to test. Review your existing code and identify:

- Functions or methods lacking test coverage
- Complex business logic that needs validation
- Edge cases and boundary conditions
- Error handling scenarios

**Example prompt**:
```
I need to write unit tests for the `calculateShippingCost` function in
src/services/shipping.js. This function takes an order object and returns
the shipping cost based on weight, destination, and shipping method.
```

### Step 4: Request Tests Using Test-Driven Development Approach
Use explicit language to indicate you're following test-driven development. This prevents Claude from creating mock implementations for functionality that doesn't exist yet.

**Recommended prompt structure**:
```
We're using test-driven development. Please write comprehensive unit tests
for [functionality] that cover:
- Happy path scenarios
- Edge cases (empty inputs, null values, boundary conditions)
- Error scenarios
- Expected input/output pairs

DO NOT implement any functionality yet - just write the tests.
```

**Example**:
```
We're using test-driven development. Please write comprehensive unit tests
for the calculateShippingCost function in src/services/shipping.js.

The tests should cover:
- Standard shipping for domestic orders
- Express shipping for international orders
- Free shipping threshold logic
- Invalid inputs (negative weight, missing address)
- Boundary cases (exactly at free shipping threshold)

DO NOT modify the implementation - only write tests.
```

### Step 5: Review and Verify the Generated Tests
Claude will generate a test file (e.g., `shipping.test.js`). Review the tests carefully:

**Check for**:
- Appropriate test framework usage (Jest, Mocha, pytest, etc.)
- Clear test descriptions
- Proper setup and teardown
- Appropriate mocking and stubbing
- Coverage of all specified scenarios
- Tests are independent and deterministic

Ask Claude to make adjustments if needed:
```
Please add tests for the case where the destination country is not in our
shipping zones list, and ensure all external API calls are mocked.
```

### Step 6: Run the Tests and Verify They Fail
Before implementing or modifying any functionality, run the tests to confirm they fail appropriately (the "red" phase of TDD).

Ask Claude:
```
Please run the test suite and confirm the tests fail as expected.
```

Claude will execute the tests in the sandboxed environment and show you the results. This verifies that your tests are actually testing the behavior, not just passing by default.

### Step 7: Commit the Tests
Once satisfied with the test suite, commit them separately before any implementation changes.

```
Please commit these tests with a descriptive message about what
functionality they're testing.
```

**Example commit message**:
```
Add unit tests for shipping cost calculation

Tests cover standard/express shipping, domestic/international
destinations, free shipping thresholds, and error handling.
```

### Step 8: Request Implementation or Fixes (If Needed)
If you're writing tests for existing functionality that should already pass, ask Claude to run the tests and fix any failures:

```
Please run the tests against the existing calculateShippingCost
implementation. If any tests fail, analyze the failures and update
the implementation to make the tests pass. DO NOT modify the tests
themselves unless there's a genuine error in the test logic.
```

If you're writing new functionality:
```
Now please implement the calculateShippingCost function to make all
these tests pass. Write minimal code to satisfy the tests.
```

### Step 9: Iterate Until All Tests Pass
Claude will typically need several iterations to get all tests passing. Let it run this cycle:

1. Implement/fix code
2. Run tests
3. Analyze failures
4. Adjust code
5. Repeat until all tests pass

Monitor the process and provide guidance if Claude gets stuck:
```
The international shipping test is still failing. Please check the
country code lookup logic - it should handle both ISO2 and ISO3 codes.
```

### Step 10: Verify with Independent Review (Optional for Critical Code)
For critical functionality, ask Claude to verify the implementation with independent subagents to ensure the code isn't overfitting to the tests:

```
Please verify this implementation using an independent subagent to
ensure it correctly handles all the business requirements, not just
the specific test cases.
```

### Step 11: Final Commit
Once all tests pass and you're satisfied with the implementation:

```
Please commit the implementation with a clear commit message linking
it to the tests we added earlier.
```

### Step 12: Review the Complete Changeset
Before merging, review all changes:

- Read through the test file for clarity and completeness
- Review the implementation changes
- Ensure tests and code follow project conventions
- Verify test coverage is comprehensive

Ask Claude for a summary:
```
Please provide a summary of the tests we added, what they cover,
and the test coverage percentage if available.
```

## Expected Results
After completing these steps, you should have:

- A comprehensive test suite for your functionality stored in appropriately named test files (e.g., `*.test.js`, `*_test.py`, `*_spec.rb`)
- All tests passing when run against your implementation
- Clear, descriptive test names that document expected behavior
- Proper mocking and stubbing of external dependencies
- Coverage of happy paths, edge cases, and error scenarios
- Two separate commits: one for tests, one for implementation (or fixes)
- Confidence that your functionality works correctly across various scenarios

**Sample output**:
```
✓ All 15 tests passing
✓ Code coverage: 95% of calculateShippingCost function
✓ Tests cover: 8 happy path scenarios, 4 edge cases, 3 error conditions
✓ All external dependencies properly mocked
```

## Troubleshooting

### Common Issue 1: Claude Modifies Tests While Fixing Implementation
**Problem**: When asked to fix failing tests, Claude changes the tests instead of (or in addition to) the implementation.

**Solution**: Be very explicit in your instructions:
```
Run the tests. If they fail, fix ONLY the implementation code, NOT the
tests. The tests define the correct behavior and should not be changed
unless there is an actual error in the test logic itself.
```

### Common Issue 2: Tests Are Too General or Don't Cover Edge Cases
**Problem**: Generated tests only cover basic scenarios and miss important edge cases.

**Solution**: Provide specific examples of edge cases in your initial request:
```
Please include tests for:
- Empty string inputs
- Null and undefined values
- Maximum integer values
- Arrays with 0, 1, and 1000+ elements
- Concurrent access scenarios
```

### Common Issue 3: Tests Have External Dependencies
**Problem**: Tests make real API calls, access databases, or have other external dependencies, making them slow and unreliable.

**Solution**: Specify in your CLAUDE.md file and in your request:
```
All tests must use mocks and stubs for external dependencies:
- Mock all HTTP requests using [your mocking library]
- Use in-memory database or mocked database calls
- Stub file system operations
- Use fixed timestamps instead of Date.now()
```

### Common Issue 4: Tests Pass When They Should Fail
**Problem**: New tests pass immediately without any implementation, indicating they're not actually testing the functionality.

**Solution**: Always verify tests fail first:
```
Before writing any implementation, run these tests and confirm they
fail with appropriate error messages. Show me the failure output.
```

### Common Issue 5: Inconsistent Test Organization
**Problem**: Tests are scattered or don't follow your project's conventions.

**Solution**: Specify the structure in your request:
```
Please organize tests following our project structure:
- Place tests in __tests__/ directory next to the source file
- Use describe() blocks to group related tests
- Follow the Arrange-Act-Assert pattern
- Name test files as [filename].test.js
```

## Additional Tips

- **Start small**: If you have a large file with no tests, break it into smaller chunks and test each function separately rather than trying to test everything at once.

- **Use the "Think Harder" pattern**: For complex testing scenarios, ask Claude to think through the test strategy first:
  ```
  Before writing tests, please analyze the calculateShippingCost function
  and think about what edge cases and error scenarios we need to cover.
  ```

- **Leverage multiple sessions**: You can have one Claude session write tests and another session review them for completeness, providing an independent perspective.

- **Keep iteration logs**: Claude shows you each test run result. Review these carefully to understand what's failing and why.

- **Use custom slash commands**: For repeated testing workflows, create custom commands in `.claude/commands/` folder:
  ```markdown
  <!-- .claude/commands/write-tests.md -->
  Please write comprehensive unit tests for the selected code using TDD:
  1. Analyze the functionality
  2. Write tests covering happy path, edge cases, and errors
  3. Run tests to verify they fail
  4. Show me the results
  ```

- **Specify your test framework**: Always mention which testing framework you're using (Jest, Mocha, pytest, JUnit, RSpec, etc.) to ensure Claude generates idiomatic tests.

- **Request coverage reports**: If your project has coverage tooling configured, ask Claude to run coverage reports:
  ```
  Please run the test coverage report and identify any uncovered branches.
  ```

- **Test the tests**: For critical code, intentionally break the implementation and verify the tests catch it:
  ```
  Please temporarily introduce a bug in the calculateShippingCost function
  to verify our tests would catch it, then revert the change.
  ```

- **Document test data**: Ask Claude to add comments explaining any non-obvious test data or scenarios, making tests easier to maintain.

## Related Articles
- KB-001: How to get started with Claude Code on the web
- KB-015: How to connect a GitHub repository to Claude Code
- KB-030: How to configure CLAUDE.md for your project
- KB-042: How to use custom slash commands
- KB-045: How to review Claude's code changes before committing
- KB-060: How to implement test-driven development with Claude Code
- KB-072: How to configure test runners and CI/CD integration

## Sources
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code on the web - https://www.claude.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
- Create Reliable Unit Tests with Claude Code - https://medium.com/ngconf/create-reliable-unit-tests-with-claude-code-9147d050d557 (Accessed: November 16, 2025)
- A practical guide to the Claude Code GitHub integration - https://www.eesel.ai/blog/claude-code-github-integration (Accessed: November 16, 2025)
- Claude Code 2025 Testing Automation Playbook - https://skywork.ai/blog/agent/claude-code-2025-testing-automation-playbook/ (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code Web test-driven development workflow 2025"

---
*This article is part of the Claude Code Web knowledge base*
