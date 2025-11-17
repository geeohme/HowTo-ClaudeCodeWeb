# How to implement test-driven development (TDD) with Claude Code

**Article ID**: KB-092
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 30-45 minutes

## Overview
This guide teaches you how to implement a complete test-driven development (TDD) workflow using Claude Code on the web. TDD with Claude Code is an Anthropic-recommended approach that significantly reduces hallucinations and scope drift by providing clear evaluation targets. You'll learn the official six-phase TDD cycle that Anthropic teams use internally, from writing tests first through validation with independent subagents.

## Prerequisites
- Active Claude Pro or Max subscription with Claude Code access
- Connected GitHub repository via OAuth at claude.ai
- Basic understanding of TDD principles (Red-Green-Refactor cycle)
- Familiarity with your project's testing framework (Jest, pytest, JUnit, etc.)
- Understanding of your programming language's testing conventions
- Recommended: CLAUDE.md file configured in your repository (see KB-061)

## Steps

### Step 1: Establish TDD Context in CLAUDE.md (Highly Recommended)

Create or update your `CLAUDE.md` file in the repository root to establish TDD as your default workflow. This ensures Claude follows TDD principles automatically across all sessions.

```markdown
# Development Workflow

## Test-Driven Development
We follow strict TDD practices for all new features and significant changes:

1. Write tests first based on expected behavior
2. Run tests to verify they fail (Red phase)
3. Write minimal implementation to pass tests (Green phase)
4. Refactor while keeping tests green
5. Never modify tests to make implementation easier

## Testing Standards
- All tests must be deterministic and independent
- Mock all external dependencies (APIs, databases, file systems)
- Use meaningful test names that describe behavior
- Follow Arrange-Act-Assert pattern
- Commit tests separately before implementation

## Test Framework
- Framework: [Jest/pytest/JUnit/etc.]
- Test file naming: [*.test.js / *_test.py / etc.]
- Test location: [__tests__/ / test/ / spec/]
- Coverage target: [80%/90%/etc.]
```

This embedding ensures TDD principles are reinforced in every Claude interaction, as CLAUDE.md is automatically loaded at session start.

### Step 2: Start Your TDD Session with Clear Feature Requirements

Begin by describing the feature or functionality you want to build. Be specific about expected behavior, inputs, outputs, and edge cases.

**Example prompt**:
```
I need to implement a new feature: a rate limiter for our API that allows
a maximum of 100 requests per hour per user ID.

The rate limiter should:
- Track requests by user ID with sliding window algorithm
- Return success (true) if request is allowed
- Return failure (false) with time until next allowed request if limit exceeded
- Handle edge cases like invalid user IDs, concurrent requests, and clock changes
- Reset counters after the time window expires

We're following test-driven development, so we'll write tests first.
DO NOT implement any functionality yet.
```

**Key phrase**: Explicitly state "we're following test-driven development" to set expectations.

### Step 3: Request Comprehensive Test Suite

Ask Claude to write tests based on expected input/output pairs. Be explicit about the TDD approach to prevent Claude from creating mock implementations.

**Recommended prompt structure**:
```
Please write a comprehensive test suite for the rate limiter that covers:

1. Happy path scenarios:
   - First request from new user (should allow)
   - Multiple requests within limit (should allow all)
   - Request exactly at limit boundary

2. Edge cases:
   - Empty or null user ID
   - Concurrent requests from same user
   - Requests spanning multiple time windows
   - Time window rollover behavior

3. Error scenarios:
   - Exceeding rate limit (should reject)
   - Invalid input types
   - System clock changes

Use expected input/output pairs for each test case. Be explicit that
we are doing TDD - do not create any implementation or mock functions.
Only write the tests.
```

**Why this works**: Providing explicit test scenarios helps Claude generate comprehensive coverage while the TDD reminder prevents premature implementation.

### Step 4: Review Generated Tests for TDD Compliance

Claude will generate a test file. Review it carefully to ensure TDD best practices:

**Check for**:
- Tests are written for behavior that doesn't exist yet
- No mock implementations or stubs for the main functionality
- Each test has clear Arrange-Act-Assert structure
- Test names describe expected behavior, not implementation details
- All external dependencies (time, storage) are properly mocked
- Tests are independent and can run in any order
- Setup and teardown are properly configured

**Request refinements**:
```
Please add tests for when the time window is exactly 1 hour, and ensure
the time functions are mocked using a controllable clock so tests are
deterministic.
```

### Step 5: Run Tests and Verify They Fail (Red Phase)

This is the critical "Red" phase of TDD. Before any implementation exists, run the tests to confirm they fail appropriately.

**Prompt**:
```
Please run the test suite and confirm all tests fail as expected.
Show me the failure output for each test.
```

Claude will execute the tests in its sandboxed environment and display the failures. This verifies that:
- Tests are actually testing the functionality
- Tests aren't passing by default due to mocking issues
- Error messages are clear and helpful

**Expected output example**:
```
FAIL  src/rateLimiter.test.js
  RateLimiter
    ✕ allows first request from new user (2 ms)
    ✕ allows multiple requests within limit (1 ms)
    ✕ rejects request when limit exceeded (1 ms)

  TypeError: RateLimiter is not defined

All 12 tests failed as expected - no implementation exists yet.
```

**Why this matters**: If tests pass without implementation, they're not validating anything. This step ensures test quality.

### Step 6: Commit Tests Before Implementation

Following Anthropic's recommended workflow, commit the test suite separately before writing any implementation code. This creates a clear history and prevents mixing test changes with implementation.

**Prompt**:
```
Please commit the test suite with a descriptive commit message.
The commit should only include the test files, not any implementation.
```

**Example commit message Claude might generate**:
```
Add comprehensive test suite for rate limiter

Tests cover request allowance within limits, rejection at limit,
sliding window behavior, concurrent requests, and edge cases for
invalid inputs and time window transitions.
```

**Benefits of separate commits**:
- Clear code review: reviewers can see test expectations first
- Easier to verify TDD was followed
- Tests serve as specification documentation
- Simpler to track when tests vs. implementation changed

### Step 7: Request Minimal Implementation (Green Phase)

Now ask Claude to write code to make the tests pass. Emphasize minimal implementation and prohibit test modification.

**Prompt**:
```
Now please implement the RateLimiter class to make all tests pass.

Requirements:
- Write minimal code necessary to satisfy the tests
- DO NOT modify the test suite we just committed
- If tests fail, fix the implementation, not the tests
- Run the test suite after implementation to verify all tests pass

Show me the implementation and test results.
```

**Why "minimal implementation" matters**: Following TDD principles, we only write code that makes tests pass, avoiding over-engineering and scope creep.

### Step 8: Monitor the Test-Fix-Retest Iteration Cycle

Claude will typically need multiple iterations to get all tests passing. This is normal and expected in TDD. The cycle looks like:

1. Write implementation code
2. Run tests
3. Analyze failures
4. Adjust implementation (not tests)
5. Rerun tests
6. Repeat until all pass

**Observe the process**: Claude will show you each iteration. Review the changes and failure messages to ensure Claude is fixing implementation, not modifying tests.

**If Claude tries to modify tests**: Intervene immediately:
```
Stop. Do not modify the tests. The tests define correct behavior.
Please fix the implementation code to match what the tests expect.
The issue is in the sliding window calculation, not the test assertions.
```

**Typical iteration count**: 2-5 iterations for well-specified tests. More than 10 iterations suggests unclear requirements or test issues.

### Step 9: Verify All Tests Pass

Once Claude reports all tests passing, verify the results:

**Prompt**:
```
Please run the complete test suite one more time and show me:
- Number of tests passed
- Test coverage percentage (if available)
- Any warnings or skipped tests
- Total execution time
```

**Expected success output**:
```
PASS  src/rateLimiter.test.js
  RateLimiter
    ✓ allows first request from new user (3 ms)
    ✓ allows multiple requests within limit (5 ms)
    ✓ rejects request when limit exceeded (2 ms)
    ✓ handles concurrent requests correctly (8 ms)
    ✓ resets after time window expires (4 ms)
    ...

Test Suites: 1 passed, 1 total
Tests:       12 passed, 12 total
Coverage:    95.2% of statements, 100% of branches
Time:        2.451s
```

### Step 10: Validate with Independent Subagents (Critical Code Only)

For production-critical features, ask Claude to verify the implementation using independent subagents. This prevents "overfitting" to specific test cases and catches logical errors tests might miss.

**Prompt**:
```
This rate limiter will protect our production API. Please verify the
implementation using an independent subagent to ensure:

1. The sliding window algorithm is correctly implemented
2. Edge cases around time window boundaries are handled properly
3. Concurrent request handling doesn't have race conditions
4. The implementation matches all business requirements, not just test cases

Provide a validation report with any concerns or issues found.
```

**What this does**: Claude spawns a fresh context to review the code independently, simulating a second pair of eyes catching issues the original implementation might have missed.

**Example validation output**:
```
Independent Validation Report:

✓ Sliding window algorithm correctly tracks requests across time boundaries
✓ Concurrent request handling uses appropriate locking mechanisms
⚠ Potential issue: Memory usage grows unbounded for long-running processes
  Recommendation: Add cleanup for expired user entries

✓ All business requirements satisfied
✓ Implementation is not overfitted to test cases
```

### Step 11: Refactor While Keeping Tests Green (If Needed)

If the validation reveals issues or the code can be improved, now is the time to refactor while ensuring tests continue passing.

**Prompt**:
```
Please add the cleanup mechanism for expired user entries as recommended.
After making changes, rerun the test suite to ensure all tests still pass.
```

This is the "Refactor" phase of Red-Green-Refactor, where you improve code quality without changing behavior.

### Step 12: Commit the Working Implementation

Once all tests pass and validation is complete, commit the implementation.

**Prompt**:
```
Please commit the RateLimiter implementation with a clear commit message
that references the tests we added earlier. Keep this commit separate
from the test commit.
```

**Example commit message**:
```
Implement rate limiter with sliding window algorithm

Implements RateLimiter class to satisfy all test requirements from
previous commit. Uses sliding window approach for accurate rate
limiting across time boundaries. Includes cleanup for expired entries
to prevent memory growth.

All 12 tests passing with 95% coverage.
```

### Step 13: Review the Complete TDD Cycle

Before considering the feature complete, review the entire TDD workflow:

**Prompt**:
```
Please provide a summary of our TDD process including:
- Number of tests written vs. passing
- Test coverage achieved
- Number of iterations needed to pass all tests
- Any refactoring performed
- Link to both commits (tests and implementation)
```

**Verify in commit history**:
1. First commit: Tests only (failing)
2. Second commit: Implementation (tests passing)
3. Optional commits: Refactoring (tests still passing)

This clean history proves TDD was followed and makes code review straightforward.

## Expected Results

After completing a full TDD cycle with Claude Code, you should have:

**Test Suite**:
- Comprehensive test file covering happy paths, edge cases, and errors
- All tests passing when run against implementation
- Deterministic tests with no external dependencies
- Clear, behavior-describing test names
- Proper mocking and stubbing configuration

**Implementation**:
- Minimal code that satisfies all test requirements
- No over-engineered or speculative features
- Clean, maintainable code structure
- Validated by independent review (for critical code)

**Git History**:
- Separate commit for test suite (failing state documented in commit message)
- Separate commit for implementation (all tests passing)
- Optional refactoring commits (tests remain green)
- Clear commit messages documenting TDD process

**Development Confidence**:
- High confidence in correctness due to comprehensive tests
- Protection against regressions with automated test suite
- Clear specification in the form of executable tests
- Reduced hallucinations and scope drift from Claude

**Example complete output**:
```
TDD Cycle Summary:
✓ 12 tests written covering all specified scenarios
✓ All tests failed initially (Red phase verified)
✓ 4 iterations needed to pass all tests (Green phase)
✓ 1 refactoring cycle for memory optimization
✓ Independent validation passed with minor recommendations
✓ Final test run: 12/12 passing, 95% coverage
✓ 2 clean commits: tests then implementation

Commits:
- abc1234: Add comprehensive test suite for rate limiter
- def5678: Implement rate limiter with sliding window algorithm
```

## Troubleshooting

### Common Issue 1: Claude Implements Functionality While Writing Tests
**Problem**: Despite instructions to only write tests, Claude creates implementation code or mock implementations alongside the test suite.

**Solution**: Be more explicit and use stronger language:
```
STOP - Do not write any implementation code whatsoever.

We are in the RED phase of TDD. I only want test files that will FAIL
because the RateLimiter class doesn't exist yet. Do not create even a
stub or skeleton implementation. Only write the tests.

After you write tests, I will ask you to run them and verify they fail.
```

If Claude still adds implementation, explicitly reject it:
```
Please delete the rateLimiter.js file you created. We only want tests
at this stage. Commit only the test file.
```

### Common Issue 2: Tests Pass Immediately Without Implementation
**Problem**: When running tests in Step 5, they pass even though no implementation exists, indicating the tests aren't actually testing anything.

**Solution**: Investigate the cause and fix the tests:
```
These tests passed without any implementation, which means they're not
validating the actual functionality. Please review the tests and identify
why they're passing.

Common causes:
- Tests are checking mocks instead of real behavior
- Test assertions are too permissive (e.g., expecting any truthy value)
- Setup code is implementing functionality inside the test file

Please fix the tests so they fail when no RateLimiter implementation exists.
```

### Common Issue 3: Claude Modifies Tests to Fix Failures
**Problem**: During the iteration cycle (Step 8), Claude changes test assertions instead of fixing the implementation to match what tests expect.

**Solution**: Immediately redirect Claude:
```
Do not modify the tests. The test suite we committed defines the correct
behavior and is our contract.

The test expects isAllowed() to return false after 100 requests. Your
implementation is returning true. Fix the implementation logic, not the
test assertion.

Remember: In TDD, tests are the specification. Implementation must conform
to tests, not the other way around.
```

Add to your CLAUDE.md:
```markdown
## TDD Rule: Tests Are Sacred
Once tests are committed, they define correct behavior and should NEVER
be modified to accommodate implementation difficulties. If a test is
legitimately wrong, discuss with human reviewer before changing.
```

### Common Issue 4: Too Many Iterations Without Progress
**Problem**: After 10+ iterations, tests are still failing with no clear progress toward passing.

**Solution**: This indicates unclear requirements or architectural issues. Pause and diagnose:
```
Let's pause the implementation. Please analyze:

1. Which specific tests are still failing?
2. What are the failure messages?
3. Is the root cause a misunderstanding of requirements or an
   implementation bug?
4. Do we need to clarify the test expectations?

Provide a diagnostic report before continuing.
```

Based on the diagnosis, either:
- Clarify requirements and restart from Step 3
- Fix a specific architectural issue Claude identified
- Simplify the problem by breaking into smaller TDD cycles

### Common Issue 5: Tests Are Too Specific (Brittle Tests)
**Problem**: Tests check implementation details rather than behavior, causing failures when refactoring even though behavior is correct.

**Solution**: Refactor tests to focus on behavior:
```
These tests are too tightly coupled to implementation details. Please
refactor to test behavior, not implementation:

BAD:  expect(rateLimiter.requestCounts['user123'].length).toBe(5)
GOOD: expect(rateLimiter.isAllowed('user123')).toBe(true)

Focus on inputs and outputs (public API), not internal data structures.
Tests should pass as long as external behavior is correct, even if
internal implementation changes.
```

### Common Issue 6: Missing Coverage for Important Edge Cases
**Problem**: After implementation, you realize important edge cases weren't tested.

**Solution**: Add tests following TDD process (yes, even after implementation):
```
I've identified an edge case we didn't test: what happens when the
system clock moves backward (e.g., NTP adjustment)?

Following TDD:
1. First write a test for clock-moves-backward scenario
2. Run tests and verify this new test fails
3. Update implementation to handle this case
4. Verify all tests (including new one) pass
5. Commit the test and implementation separately (or together if minor)
```

This maintains TDD discipline even when adding to existing code.

## Additional Tips

- **Start with simplest test first**: Don't write all 50 tests upfront. Write 2-3 simple tests, implement, then add more complex cases iteratively. This provides faster feedback and prevents overwhelming Claude.

- **Use TDD for debugging**: When you find a bug, write a failing test that reproduces it first, then fix the implementation. This ensures the bug stays fixed:
  ```
  We have a bug: rate limiter fails when user ID contains special characters.
  Following TDD: write a failing test for user ID "user@domain.com" first,
  then fix the implementation to make it pass.
  ```

- **Create custom TDD slash commands**: Standardize your TDD workflow with custom commands in `.claude/commands/tdd.md`:
  ```markdown
  <!-- .claude/commands/tdd.md -->
  # Test-Driven Development Workflow

  We are following strict TDD. For the functionality described above:

  1. Write comprehensive tests covering happy path, edge cases, and errors
  2. DO NOT implement any functionality yet
  3. Run tests and verify they all fail
  4. Show me the failing test output
  5. Wait for my approval before implementing
  ```

- **Leverage parallel sessions for validation**: Run one Claude session to develop with TDD, and a second session to review the tests independently and suggest missing scenarios.

- **TDD works best for well-defined problems**: Use TDD when requirements are clear. For exploratory coding where requirements are uncertain, use a looser approach, then apply TDD once you understand what you're building.

- **Measure TDD effectiveness**: Track metrics over time:
  - Test coverage percentage
  - Number of production bugs in TDD-built features vs. non-TDD
  - Time to implement with TDD vs. code-first approach
  - Code review time (TDD code is often easier to review)

- **Use TDD to combat Claude hallucinations**: TDD is described by practitioners as "the most effective counter to hallucination and LLM scope drift" because tests provide concrete, verifiable targets rather than open-ended implementation requests.

- **Embed TDD in CLAUDE.md for consistency**: Every session with Claude reinforces TDD practices when they're documented in CLAUDE.md, ensuring consistency across different features and team members.

- **Request test-first code reviews**: When reviewing Claude's pull requests, check commit history to verify tests were committed before implementation. If not, request the PR be redone following TDD.

- **Practice the "Think Harder" pattern for test design**: For complex features, ask Claude to design the test strategy before writing tests:
  ```
  Before writing any tests, please analyze the rate limiter requirements
  and create a test plan showing:
  - Categories of tests needed (happy path, edge cases, errors)
  - Specific scenarios within each category
  - Required mocks and test data

  Once we agree on the plan, write the tests.
  ```

- **Use table-driven tests for similar scenarios**: For features with many similar test cases, ask Claude to use parameterized/table-driven tests:
  ```
  Please use parameterized tests for the different rate limit scenarios:

  | requests | limit | expected | reason               |
  |----------|-------|----------|----------------------|
  | 50       | 100   | true     | Within limit         |
  | 100      | 100   | true     | At limit boundary    |
  | 101      | 100   | false    | Exceeds limit        |
  ```

## Related Articles
- KB-054: How to write unit tests for existing functionality
- KB-056: How to perform code reviews with Claude Code
- KB-061: How to create SessionStart hooks for repository initialization
- KB-062: How to configure hooks to auto-format code after edits
- KB-068: How to create your first custom slash command
- KB-087: How to configure agent behavior and permissions

## Sources
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Claude Code and the Art of Test-Driven Development - https://thenewstack.io/claude-code-and-the-art-of-test-driven-development/ (Accessed: November 16, 2025)
- TDD with Claude Code: Model Context Protocol, FMP and Agents - https://medium.com/@taitcraigd/tdd-with-claude-code-model-context-protocol-fmp-and-agents-740e025f4e4b (Accessed: November 16, 2025)
- Taming GenAI Agents: How Test-Driven Development Transforms Claude Code - https://www.nathanfox.net/p/taming-genai-agents-like-claude-code (Accessed: November 16, 2025)
- GitHub - nizos/tdd-guard: Automated TDD enforcement for Claude Code - https://github.com/nizos/tdd-guard (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code TDD workflow best practices 2025"

---
*This article is part of the Claude Code Web knowledge base*
