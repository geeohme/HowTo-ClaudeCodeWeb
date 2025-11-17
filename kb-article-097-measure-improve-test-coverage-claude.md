# How to measure and improve test coverage with Claude's help

**Article ID**: KB-097
**Difficulty**: Advanced
**Last Updated**: November 16, 2025
**Estimated Time**: 25-40 minutes

## Overview
This guide shows you how to leverage Claude Code Web to systematically measure and improve test coverage in your projects. You'll learn to configure coverage tools, analyze coverage reports with Claude's help, and use AI-assisted workflows to achieve 80-90% coverage or higher while maintaining test quality.

## Prerequisites
- Active Claude Code Web session with a repository connected
- Basic familiarity with your project's testing framework (Jest, pytest, JUnit, etc.)
- Understanding of what test coverage means and why it's important
- Repository with existing code that needs test coverage

## Steps

### Step 1: Set Up Coverage Tools for Your Stack

First, configure the appropriate coverage measurement tool for your project's language.

**For Python projects (pytest):**
```bash
pip install pytest-cov
```

Add to your `CLAUDE.md` file:
```markdown
## Testing Standards

- Test framework: pytest
- Coverage tool: pytest-cov
- Run tests: `pytest -q --maxfail=1 --disable-warnings --cov`
- Coverage report: `pytest --cov --cov-report=html --cov-report=term-missing`
- Target coverage: 80-90%
```

**For JavaScript/TypeScript projects (Jest):**
```bash
npm install --save-dev jest
```

Add to your `package.json`:
```json
{
  "scripts": {
    "test": "jest",
    "test:coverage": "jest --coverage"
  },
  "jest": {
    "collectCoverageFrom": [
      "src/**/*.{js,jsx,ts,tsx}",
      "!src/**/*.test.{js,jsx,ts,tsx}"
    ],
    "coverageThreshold": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
```

**For PHP projects:**
```bash
composer require --dev phpunit/phpunit
```

Command to run with coverage:
```bash
XDEBUG_MODE=coverage ./vendor/bin/phpunit --coverage-text
```

### Step 2: Generate Initial Coverage Report

Run your coverage tool to establish a baseline.

Ask Claude Code:
```
Please run our test suite with coverage enabled using the command in CLAUDE.md
and show me the current coverage report.
```

For Python:
```bash
pytest --cov --cov-report=term-missing
```

For JavaScript:
```bash
npm test -- --coverage
```

The report will show:
- Overall coverage percentage
- Coverage by file
- Lines/branches not covered
- Coverage by type (statements, branches, functions, lines)

### Step 3: Analyze Coverage Gaps with Claude

Once you have the coverage report, ask Claude to analyze it strategically.

**Effective prompts:**
```
Based on this coverage report, identify the 5 most critical files or modules
that need additional test coverage. Prioritize based on:
1. Business logic importance
2. Complexity of the code
3. Current coverage gaps
4. Risk of bugs
```

Claude will examine your coverage report and provide risk-based prioritization, focusing on critical code paths rather than trying to cover everything at once.

### Step 4: Use Test-Driven Development (TDD) Workflow

For new features or untested code, use Claude's TDD capabilities to ensure coverage from the start.

**TDD Workflow with Claude:**

1. **Write tests first:**
```
I'm doing test-driven development. Write comprehensive tests for the
`calculateDiscount` function that should:
- Apply percentage discounts correctly
- Handle minimum purchase amounts
- Validate discount codes
- Return errors for invalid inputs

Create the test cases but don't implement the function yet.
```

2. **Verify tests fail:**
```
Run the tests and confirm they fail as expected before we implement the function.
```

3. **Implement to pass tests:**
```
Now implement the calculateDiscount function to pass all tests.
Do not modify the test cases.
```

4. **Iterate until all tests pass:**
```
Keep running the tests and fixing any failures until all tests pass.
```

This approach ensures new code starts with 100% coverage.

### Step 5: Systematically Fill Coverage Gaps

For existing code with low coverage, use a structured approach to add tests.

**Ask Claude to add tests for specific uncovered files:**
```
Looking at the coverage report, the file `src/utils/validation.js` has only
45% coverage. Please:
1. Analyze the uncovered lines (lines 23-45, 67-89)
2. Write comprehensive unit tests covering:
   - All uncovered code paths
   - Edge cases and boundary conditions
   - Error scenarios
3. Ensure tests follow our existing test conventions in validation.test.js
4. Run the tests to verify they pass
5. Generate a new coverage report to confirm improvement
```

Claude will:
- Examine the uncovered code
- Write tests for missing branches and lines
- Follow your project's testing patterns
- Verify the tests work
- Show the improved coverage

### Step 6: Focus on Different Coverage Metrics

Understanding the different types of coverage helps you write better tests.

**Coverage metrics explained:**
- **Line coverage**: Percentage of code lines executed
- **Branch coverage**: Percentage of decision branches (if/else, switch) tested
- **Function coverage**: Percentage of functions called
- **Statement coverage**: Percentage of statements executed

**Ask Claude to improve branch coverage:**
```
Our branch coverage is only 65% while line coverage is 82%. Please identify
the conditional branches that aren't being tested and add test cases to cover:
- All if/else paths
- Switch case alternatives
- Ternary operators
- Short-circuit evaluations

Focus on the UserService class first.
```

### Step 7: Set Up Coverage in CLAUDE.md

Document your coverage standards so Claude applies them consistently across sessions.

Add to your `CLAUDE.md`:
```markdown
## Test Coverage Requirements

### Coverage Standards
- Minimum coverage: 80% for all metrics (lines, branches, functions, statements)
- Critical modules (auth, payment, security): 90%+ required
- New code: 100% coverage required before PR approval

### Coverage Commands
- Python: `pytest --cov --cov-report=html --cov-report=term-missing`
- JavaScript: `npm test -- --coverage`
- Generate report: Coverage reports are in `coverage/` directory

### Testing Guidelines
- Write tests before implementing features (TDD preferred)
- Test edge cases, error conditions, and boundary values
- Mock external dependencies appropriately
- Keep tests fast and focused
- Update tests when modifying covered code

### Coverage Workflow
1. Run coverage before starting work to establish baseline
2. Write/update tests for any code changes
3. Run coverage after changes to verify improvement
4. Ensure no decrease in coverage percentage
5. Review coverage report for any new gaps
```

### Step 8: Automate Coverage Checks in CI

Set up continuous integration to enforce coverage standards.

**For GitHub Actions (`.github/workflows/test-coverage.yml`):**
```yaml
name: Test Coverage

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install pytest-cov
      - name: Run tests with coverage
        run: pytest --cov --cov-report=xml --cov-report=term-missing --cov-fail-under=80
      - name: Upload coverage reports
        uses: codecov/codecov-action@v3
```

**Ask Claude to set this up:**
```
Please create a GitHub Actions workflow that:
1. Runs our test suite with coverage on every push and PR
2. Fails the build if coverage drops below 80%
3. Uploads coverage reports to Codecov
4. Comments on PRs with coverage changes
```

### Step 9: Review and Improve Test Quality

High coverage doesn't always mean high-quality tests. Use Claude to review test effectiveness.

**Ask Claude to audit test quality:**
```
Please review the tests in tests/auth.test.js and assess:
1. Are we testing actual behavior or just implementation details?
2. Are there redundant tests that don't add value?
3. Are we properly testing error cases and edge conditions?
4. Do tests make assertions about meaningful outcomes?
5. Are we over-mocking, potentially missing integration issues?

Suggest improvements to make these tests more valuable.
```

Claude can identify:
- Tests that only verify mocks are called (low value)
- Missing assertions on actual outcomes
- Over-reliance on implementation details
- Gaps in error scenario testing
- Opportunities for integration tests

### Step 10: Iteratively Improve Coverage to Target

Use an iterative approach to reach your coverage goals.

**Effective prompt for systematic improvement:**
```
Our current coverage is 67%. I want to reach 90% coverage. Please:

1. Generate the current coverage report
2. Identify the 3 highest-impact files to test next (based on complexity,
   importance, and current coverage)
3. For each file, write comprehensive tests covering all uncovered lines
   and branches
4. Run the tests to ensure they pass
5. Generate a new coverage report showing the improvement
6. Repeat steps 2-5 until we reach 90% coverage

After each iteration, show me the coverage improvement and estimated
remaining work.
```

Claude will work through your codebase methodically, tracking progress and adapting priorities based on results.

## Expected Results

After completing these steps, you should have:

- **Coverage metrics:** 80-90% coverage across all metrics (lines, branches, functions, statements)
- **Coverage reports:** HTML and terminal reports showing detailed coverage by file
- **Automated checks:** CI/CD pipeline enforcing coverage standards on every commit
- **Quality tests:** Comprehensive test suite covering edge cases, errors, and business logic
- **Documentation:** Clear testing standards in CLAUDE.md guiding all development
- **Sustainable workflow:** Repeatable process for maintaining and improving coverage

**Typical coverage improvement timeline:**
- Small project (20 files): 2-3 hours to reach 90% from 60%
- Medium project (100 files): 8-12 hours with Claude's assistance
- Large project (500+ files): Incremental improvement over multiple sessions

## Troubleshooting

### Common Issue 1
**Problem**: Coverage report shows 100% but tests are trivial or don't test actual behavior
**Solution**: Ask Claude to audit test quality: "Review these tests and identify any that only verify mocks are called rather than testing actual behavior. Suggest improvements to test real outcomes." Focus on assertion quality, not just code execution.

### Common Issue 2
**Problem**: Coverage percentage drops when adding new code
**Solution**: Implement a pre-commit hook or CI check that requires tests for all new code. Add to CLAUDE.md: "All new functions must have tests written before implementation (TDD). Coverage cannot decrease from the main branch baseline."

### Common Issue 3
**Problem**: Hard-to-test code (static methods, singletons, global state) lowering coverage
**Solution**: Ask Claude: "This code is hard to test due to [static methods/tight coupling/global state]. Please refactor it to be more testable using dependency injection/factory patterns/pure functions, then write comprehensive tests." Claude can both refactor for testability and write the tests.

### Common Issue 4
**Problem**: Coverage tools not detecting test execution properly
**Solution**: Verify your coverage configuration includes all source files. For Jest, check `collectCoverageFrom` in package.json. For pytest-cov, ensure `--cov=src` points to your source directory. Ask Claude: "Our coverage report is empty/incomplete. Please check the coverage tool configuration and fix any path or exclusion issues."

### Common Issue 5
**Problem**: Too many false positives (unreachable code counted as uncovered)
**Solution**: Use coverage pragmas to exclude unreachable code: `# pragma: no cover` for Python or `/* istanbul ignore next */` for JavaScript. Ask Claude: "Identify any unreachable defensive code in error handlers that should be excluded from coverage metrics and add appropriate coverage pragmas."

## Additional Tips

- **Start with business-critical code:** Don't aim for 100% coverage everywhere. Focus first on authentication, payment processing, data validation, and core business logic.

- **Use coverage as a guide, not a goal:** High coverage is valuable, but 90% thoughtful coverage beats 100% superficial coverage. Ask Claude to review whether tests actually verify correct behavior.

- **Leverage Claude's pattern recognition:** When testing similar functions, provide one example then ask: "Write similar tests for the other 15 validation functions following the same pattern."

- **Combine with manual testing:** Coverage shows what code is executed, not whether it's correct. Use exploratory testing and code review alongside coverage metrics.

- **Track coverage trends:** Monitor coverage over time. Ask Claude: "Show me coverage trends for the last 10 commits and identify any modules where coverage is declining."

- **Use coverage diff in PRs:** Focus on coverage changes in pull requests. Set up coverage tools to comment on PRs showing which lines in the diff lack coverage.

- **Optimize for fast tests:** Tests you run frequently get more value. Keep unit tests fast (<1 minute total runtime) so you can run coverage often. Use Claude to identify and optimize slow tests.

- **Document why code isn't tested:** For intentionally untested code (generated files, trivial getters/setters), document the decision in CLAUDE.md so Claude doesn't repeatedly suggest adding tests.

## Related Articles
- KB-073: How to implement test-driven development (TDD) workflow with Claude
- KB-074: How to write comprehensive unit tests with Claude's assistance
- KB-089: How to set up CI/CD pipelines for Claude Code Web projects
- KB-095: How to configure CLAUDE.md for optimal project guidance

## Sources
- Claude Code Best Practices - https://www.anthropic.com/engineering/claude-code-best-practices (Accessed: November 16, 2025)
- Teaching Claude Code About Test Coverage - https://brianreich.dev/uncategorized/claude-code-test-coverage/ (Accessed: November 16, 2025)
- pytest-cov Documentation - https://github.com/pytest-dev/pytest-cov (Accessed: November 16, 2025)
- Coverage.py Documentation - https://coverage.readthedocs.io/en/7.11.3/ (Accessed: November 16, 2025)
- Jest Coverage Documentation - https://jestjs.io/docs/configuration#collectcoverage-boolean (Accessed: November 16, 2025)
- Best Practices for Achieving High Test Coverage with AI-Assisted Testing - https://mammoth-ai.com/best-practices-for-achieving-high-test-coverage-with-ai-assisted-testing/ (Accessed: November 16, 2025)
- Claude Code 2025 Testing Automation Playbook - https://skywork.ai/blog/agent/claude-code-2025-testing-automation-playbook/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
