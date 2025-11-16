# How to update dependencies safely

**Article ID**: KB-058
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 15-30 minutes

## Overview
Updating project dependencies in Claude Code Web requires careful planning to ensure compatibility and maintain project stability. This guide covers best practices for checking, updating, and validating dependencies across npm and Python ecosystems, including automated setup with SessionStart hooks and proper documentation in CLAUDE.md files.

## Prerequisites
- Active Claude Code Web session with a project repository
- Basic understanding of package managers (npm or pip)
- Existing package.json (Node.js) or requirements.txt (Python) file
- Project tests or validation procedures in place
- Understanding of semantic versioning (MAJOR.MINOR.PATCH)

## Steps

### Step 1: Document Current Dependencies in CLAUDE.md

Create or update your CLAUDE.md file to document your dependency management approach. This file is automatically read by Claude Code and should be committed to source control.

```markdown
# Project Dependencies

## Installation Commands

### Node.js/npm
- `npm install` - Install all dependencies from package.json
- `npm ci` - Clean install using exact versions from package-lock.json (CI/CD)
- `npm run build` - Build the project
- `npm test` - Run test suite

### Python/pip
- `python -m venv venv` - Create virtual environment
- `source venv/bin/activate` - Activate environment (Unix/Mac)
- `pip install -r requirements.txt` - Install dependencies
- `pytest` - Run tests

## Dependency Update Policy
- PATCH updates: Applied regularly for bug fixes
- MINOR updates: Review changelog, test thoroughly
- MAJOR updates: Requires careful evaluation and migration plan
```

**Best Practice**: Keep CLAUDE.md concise as its contents are prepended to prompts and consume your token budget with every interaction.

### Step 2: Configure SessionStart Hooks for Automatic Installation

Set up automatic dependency installation when sessions start by creating `.claude/settings.json`:

```json
{
  "hooks": {
    "SessionStart": [
      {
        "matcher": "startup",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/scripts/install_deps.sh"
          }
        ]
      }
    ]
  }
}
```

Create the corresponding script at `scripts/install_deps.sh`:

```bash
#!/bin/bash
set -e

echo "Installing project dependencies..."

# Check if this is a Node.js project
if [ -f "package.json" ]; then
    echo "Found package.json - installing npm dependencies..."
    npm ci || npm install
fi

# Check if this is a Python project
if [ -f "requirements.txt" ]; then
    echo "Found requirements.txt - installing Python dependencies..."

    # Create virtual environment if it doesn't exist
    if [ ! -d "venv" ]; then
        python -m venv venv
    fi

    # Activate and install
    source venv/bin/activate
    pip install -r requirements.txt
fi

echo "Dependency installation complete!"
exit 0
```

Make the script executable:
```bash
chmod +x scripts/install_deps.sh
```

**Note**: To run hooks only in web environments, check the `CLAUDE_CODE_REMOTE` environment variable in your script.

### Step 3: Check for Outdated Dependencies

Before updating, identify which packages have newer versions available.

#### For npm projects:

```bash
# Check for outdated packages
npm outdated

# For more detailed analysis
npx npm-check-updates
```

The output shows:
- **Current**: Currently installed version
- **Wanted**: Maximum version satisfying package.json constraints
- **Latest**: Latest published version

#### For Python projects:

```bash
# List all outdated packages
pip list --outdated

# Preview updates without installing
pip install --upgrade -r requirements.txt --dry-run --report -
```

**Alternative Tools**:
- npm: `npm-check-updates` (ncu) for advanced checking
- Python: `pur`, `piprot`, or `pip-tools` for requirements management

### Step 4: Update Dependencies Incrementally

**Critical Best Practice**: Never update all dependencies simultaneously. Update one or a small group at a time to isolate potential breaking changes.

#### Strategy for Safe Updates:

1. **PATCH updates** (1.2.3 → 1.2.4): Usually safe, bug fixes only
2. **MINOR updates** (1.2.3 → 1.3.0): Review changelog, backward compatible
3. **MAJOR updates** (1.2.3 → 2.0.0): Breaking changes, requires careful evaluation

#### For npm projects:

```bash
# Update a specific package to latest compatible version
npm update package-name

# Update to a specific version
npm install package-name@2.3.4

# Update and save to package.json
npm install package-name@latest --save

# For interactive selection
npx npm-check-updates -i
```

#### For Python projects:

```bash
# Activate virtual environment first
source venv/bin/activate

# Update a specific package
pip install --upgrade package-name

# Update and regenerate requirements
pip install --upgrade package-name
pip freeze > requirements.txt

# Update all packages (use with caution)
pip install --upgrade --upgrade-strategy eager -r requirements.txt
```

**Recommended Workflow**:
1. Create a new branch: `git checkout -b update-dependencies`
2. Update one package or related group
3. Update lock files
4. Test thoroughly
5. Commit if successful
6. Repeat for next package

### Step 5: Update Lock Files

Lock files ensure reproducible installs and should always be committed.

#### For npm:

```bash
# package-lock.json updates automatically with npm install
npm install

# For CI/CD, use npm ci for exact versions from lock file
npm ci
```

**Important**: Always commit `package-lock.json` to source control. It guarantees that teammates and CI/CD install identical dependency trees.

#### For Python:

Python doesn't have built-in lock files, but you can use `pip-tools`:

```bash
# Install pip-tools
pip install pip-tools

# Create requirements.in with your direct dependencies
# Then compile to requirements.txt with locked versions
pip-compile requirements.in

# Install from compiled requirements
pip-sync requirements.txt
```

Alternatively, use `pip freeze` for simple projects:
```bash
pip freeze > requirements.txt
```

### Step 6: Run Security Audits

Check for known vulnerabilities in your dependencies.

#### For npm:

```bash
# Run security audit
npm audit

# Attempt automatic fixes (PATCH/MINOR updates)
npm audit fix

# Include breaking changes (use with caution)
npm audit fix --force
```

#### For Python:

```bash
# Install safety
pip install safety

# Check for known security vulnerabilities
safety check

# Check specific requirements file
safety check -r requirements.txt
```

### Step 7: Test Thoroughly

Before finalizing updates, ensure everything works correctly.

```bash
# For Node.js projects
npm run build
npm test
npm run lint  # if configured

# For Python projects
pytest
python -m mypy .  # if using type checking
flake8  # if using linting
```

**Testing Checklist**:
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Build succeeds without warnings
- [ ] Linting passes
- [ ] Application runs without errors
- [ ] Critical user flows work as expected

### Step 8: Commit and Document Changes

Once testing is successful, commit your changes with clear documentation.

```bash
# Stage the updated files
git add package.json package-lock.json  # for npm
# or
git add requirements.txt  # for Python

# Commit with descriptive message
git commit -m "Update dependencies: package-name from 1.2.3 to 1.4.0

- Fixes security vulnerability CVE-XXXX-XXXX
- Adds support for new feature XYZ
- All tests passing"
```

Update your CLAUDE.md or changelog if major updates were made:
```markdown
## Dependency Updates - 2025-11-16
- Updated express from 4.18.0 to 4.19.2 (security patch)
- Updated pytest from 7.4.0 to 8.0.0 (new features)
```

## Expected Results

After successfully updating dependencies, you should see:

1. **Updated dependency files**:
   - `package.json` and `package-lock.json` (npm)
   - `requirements.txt` (Python)

2. **Successful installation**: Dependencies install without errors in fresh environments

3. **Passing tests**: All test suites run successfully

4. **No security warnings**: `npm audit` or `safety check` shows no critical vulnerabilities

5. **Functional application**: Application runs and critical features work as expected

6. **Automated setup**: SessionStart hooks install dependencies automatically in new Claude Code Web sessions

## Troubleshooting

### Common Issue 1: Dependency Conflicts

**Problem**: After updating, you see peer dependency warnings or version conflicts.

**Solution**:
```bash
# For npm - review peer dependency warnings
npm install --legacy-peer-deps  # temporary workaround

# Or use --force (last resort, may cause issues)
npm install --force

# Better: Identify conflict and update related packages together
npm ls package-name  # see dependency tree
```

For Python:
```bash
# Check which package requires the conflicting version
pip show package-name

# Use pip-tools to resolve dependencies
pip-compile --upgrade-package=specific-package
```

### Common Issue 2: Breaking Changes After Update

**Problem**: Application fails or behaves differently after MAJOR version update.

**Solution**:
1. Read the package's migration guide or changelog
2. Search for breaking changes: `package-name BREAKING CHANGES vX.0.0`
3. Rollback if needed: `git checkout HEAD~1 -- package.json package-lock.json`
4. Update code to accommodate breaking changes
5. Consider using version ranges to stay on older MAJOR: `"package-name": "^1.2.3"` stays on v1.x

### Common Issue 3: SessionStart Hook Fails

**Problem**: Dependencies don't install automatically in Claude Code Web sessions.

**Solution**:
1. Verify `.claude/settings.json` is properly formatted (valid JSON)
2. Check script permissions: `chmod +x scripts/install_deps.sh`
3. Ensure script path is correct: `"$CLAUDE_PROJECT_DIR"/scripts/install_deps.sh`
4. Test script manually: `bash scripts/install_deps.sh`
5. Check Claude Code Web network access if installation requires external packages

### Common Issue 4: Network Access Restrictions

**Problem**: Package installation fails due to network restrictions in Claude Code Web.

**Solution**:
Claude Code Web runs behind an HTTP/HTTPS proxy for security. To enable package installation:

1. Verify allowed domains in network access settings
2. Use HTTPS endpoints over HTTP when possible
3. Follow principle of least privilege - only enable minimum required network access
4. Audit allowed domains regularly

### Common Issue 5: Lock File Conflicts

**Problem**: `package-lock.json` changes unexpectedly or has merge conflicts.

**Solution**:
```bash
# For unexpected changes - ensure consistent npm version
npm --version  # check version
npm ci  # use clean install in CI/CD

# For merge conflicts
git checkout --ours package-lock.json
npm install  # regenerate from package.json
# OR
git checkout --theirs package-lock.json
npm install
```

## Additional Tips

- **Automate dependency checks**: Set up Dependabot, Renovate, or similar tools to create PRs for updates automatically

- **Semantic versioning in package.json**:
  - `^1.2.3` - Allow MINOR and PATCH updates (1.x.x)
  - `~1.2.3` - Allow only PATCH updates (1.2.x)
  - `1.2.3` - Exact version only

- **Use npm ci in CI/CD**: It's faster and more reliable than `npm install` for automated environments

- **Virtual environments for Python**: Always use virtual environments (`venv`, `virtualenv`, or `conda`) to isolate project dependencies

- **Review changelogs**: Before updating, review the package's CHANGELOG.md or release notes on GitHub

- **Keep dependencies minimal**: Fewer dependencies mean fewer security vulnerabilities and easier maintenance

- **Update regularly**: Small, frequent updates are safer than large, infrequent ones

- **Test in staging first**: For production applications, test updates in a staging environment before deploying

- **Document known issues**: If a package version has issues, document it in CLAUDE.md to prevent accidental updates

- **Environment-specific hooks**: Use `CLAUDE_CODE_REMOTE` environment variable to run hooks only in web or only in local environments:
  ```bash
  if [ "$CLAUDE_CODE_REMOTE" = "true" ]; then
    # Web-only installation steps
  fi
  ```

## Related Articles
- KB-001: Getting started with Claude Code Web
- KB-017: Creating and using SessionStart hooks
- KB-025: Understanding CLAUDE.md configuration
- KB-042: Managing project settings and configuration
- KB-055: Setting up CI/CD pipelines with Claude Code Web

## Sources
- Claude Code on the web - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/claude-code-on-the-web (Accessed: November 16, 2025)
- The ultimate guide to Claude Code Hooks - https://www.augmentedswe.com/p/guide-to-claude-code-hooks (Accessed: November 16, 2025)
- What is CLAUDE.md in Claude Code - https://claudelog.com/faqs/what-is-claude-md/ (Accessed: November 16, 2025)
- npm Best Practices - RisingStack Engineering - https://blog.risingstack.com/nodejs-at-scale-npm-best-practices/ (Accessed: November 16, 2025)
- npm-check-updates - npm - https://www.npmjs.com/package/npm-check-updates (Accessed: November 16, 2025)
- package-lock.json - npm Docs - https://docs.npmjs.com/cli/v7/configuring-npm/package-lock-json/ (Accessed: November 16, 2025)
- Keeping Up-to-Date with pip: How to Update Packages Safely in Python - Udacity - https://www.udacity.com/blog/2024/12/keeping-up-to-date-with-pip-how-to-update-packages-safely-in-python.html (Accessed: November 16, 2025)
- pip User Guide - pip documentation - https://pip.pypa.io/en/stable/user_guide/ (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
