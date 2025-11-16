# How to understand which Ruby, Node, Python versions are available

**Article ID**: KB-041
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Claude Code Web runs tasks in isolated Ubuntu 22.04 containers with pre-installed language runtimes. Understanding which versions of Ruby, Node.js, and Python are available helps you write compatible code, configure dependencies correctly, and troubleshoot version-specific issues. This guide shows you how to check installed versions and understand the runtime environment.

## Prerequisites
- Access to Claude Code Web
- Basic familiarity with command-line tools
- Understanding of version numbering (semantic versioning)

## Steps

### Step 1: Check Python Version

Python is pre-installed in the Claude Code Web environment. To check which version is available:

```bash
python --version
```

Or use the shorter form:

```bash
python -V
```

You can also check if Python 3 is explicitly available:

```bash
python3 --version
```

**Expected output**: `Python 3.11.x` (based on the Ubuntu 22.04 container)

To see the full Python environment details:

```bash
python -c "import sys; print(sys.version)"
```

### Step 2: Check Node.js Version

Node.js LTS versions are pre-installed. To check the available version:

```bash
node --version
```

Or:

```bash
node -v
```

**Expected output**: `v20.x.x` (LTS version)

To check npm (Node Package Manager) version:

```bash
npm --version
```

To see all available Node.js package managers:

```bash
# Check npm
npm --version

# Check yarn (if available)
yarn --version

# Check pnpm (if available)
pnpm --version

# Check bun (if available)
bun --version
```

### Step 3: Check Ruby Version

Ruby is available with multiple versions managed by rbenv. To check the current default Ruby version:

```bash
ruby --version
```

Or:

```bash
ruby -v
```

**Expected output**: `ruby 3.3.6` (default version)

To see all installed Ruby versions:

```bash
rbenv versions
```

**Expected output**:
```
  3.1.6
  3.2.6
* 3.3.6 (set by /root/.rbenv/version)
```

The asterisk (*) indicates the currently active version.

### Step 4: Check Version Managers

Claude Code Web includes version managers for flexibility:

**For Ruby (rbenv)**:
```bash
rbenv --version
rbenv versions
```

**To check gem (Ruby package manager)**:
```bash
gem --version
```

**To check bundler**:
```bash
bundler --version
```

### Step 5: Create a Version Check Script

For a comprehensive overview, you can create a quick script to check all language versions:

```bash
echo "=== Language Runtime Versions ==="
echo ""
echo "Python:"
python --version
echo ""
echo "Node.js:"
node --version
echo "npm:"
npm --version
echo ""
echo "Ruby:"
ruby --version
echo ""
echo "Available Ruby versions:"
rbenv versions
```

Save this as `check-versions.sh` and run it anytime you need a quick reference.

### Step 6: Verify Package Managers

Check the available package managers for each language:

**Python package managers**:
```bash
pip --version
pip3 --version
poetry --version
```

**Node.js package managers**:
```bash
npm --version
yarn --version
pnpm --version
```

**Ruby package managers**:
```bash
gem --version
bundler --version
```

## Expected Results

After running these commands, you should see:

1. **Python**: Version 3.11.x with pip and poetry available
2. **Node.js**: Version 20.x (LTS) with npm, yarn, and pnpm
3. **Ruby**: Default version 3.3.6 with versions 3.1.6 and 3.2.6 also available via rbenv
4. All associated package managers (pip, npm, gem) properly installed

The environment is consistent across tasks, as each task spawns a fresh Ubuntu 22.04 container with the same base image.

## Troubleshooting

### Common Issue 1
**Problem**: Command not found when checking a version manager
**Solution**: Some version managers may not be in your PATH. Try the full path or check if the tool is actually installed:
```bash
which rbenv
which python
which node
```

### Common Issue 2
**Problem**: Different version than expected
**Solution**: Claude Code Web uses a universal image with specific versions. If you need a different version:
- For Ruby: Use `rbenv` to switch between available versions (3.1.6, 3.2.6, 3.3.6)
- For Python/Node: The versions are fixed in the container. Use version-specific dependency declarations in your `requirements.txt`, `package.json`, or `Gemfile` to ensure compatibility

### Common Issue 3
**Problem**: Need to switch Ruby versions
**Solution**: Use rbenv to change the Ruby version for your session:
```bash
# Switch to Ruby 3.2.6
rbenv global 3.2.6
ruby --version

# Switch back to default 3.3.6
rbenv global 3.3.6
ruby --version
```

## Additional Tips

- **Version Pinning**: Always specify compatible versions in your dependency files (`package.json`, `requirements.txt`, `Gemfile`) to ensure your code works with the available runtimes
- **Fresh Containers**: Each task in Claude Code Web runs in a fresh container, so environment changes don't persist between tasks
- **Ubuntu Base**: The environment is based on Ubuntu 22.04, which influences the default system packages and libraries available
- **Multiple Ruby Versions**: Take advantage of rbenv to test your Ruby code against different versions (3.1.6, 3.2.6, 3.3.6)
- **LTS Preference**: Node.js uses LTS (Long Term Support) versions for stability
- **Check Before Installing**: Always verify what's pre-installed before adding dependencies to avoid unnecessary package downloads

## Related Articles
- KB-001: Getting Started with Claude Code Web (if available)
- KB-025: Working with dependencies and package managers (if available)
- KB-035: Understanding the execution environment (if available)

## Sources
- Claude Code on the web - Official Documentation - https://docs.claude.com/en/docs/claude-code/claude-code-on-the-web (Accessed: November 16, 2025)
- Simon Willison's Analysis - https://simonwillison.net/2025/Oct/20/claude-code-for-web/ (Accessed: November 16, 2025)
- Web Search: "Claude Code Web available Ruby Node Python versions 2025" (Accessed: November 16, 2025)
- Web Search: "Anthropic Claude Code Web language runtime versions documentation" (Accessed: November 16, 2025)

---
*This article is part of the Claude Code Web knowledge base*
