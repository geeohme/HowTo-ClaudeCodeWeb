# How to view pre-installed toolchains and language ecosystems

**Article ID**: KB-040
**Difficulty**: Intermediate
**Last Updated**: November 16, 2025
**Estimated Time**: 5-10 minutes

## Overview
Claude Code Web runs tasks in a standardized Ubuntu-based container with a comprehensive universal image that includes common toolchains and language ecosystems pre-installed. This guide shows you how to view and verify which versions of languages, databases, and build tools are available in your Claude Code Web environment, helping you understand your development environment without needing to install additional dependencies.

## Prerequisites
- Active Claude Code Web session (Pro or Max subscription)
- Basic familiarity with command-line version checking
- Understanding of how to interact with Claude Code Web (see KB-001)

## Steps

### Step 1: Check Python Installation and Version

Ask Claude to check the Python installation by running version and path commands:

```bash
python3 --version
which python3
pip --version
poetry --version
```

**What to expect**: You should see Python 3.x installed with pip and poetry package managers available. The universal image includes common scientific libraries pre-configured.

**Additional tip**: You can also ask Claude to list installed Python packages with `pip list` or `pip freeze` to see what's available out of the box.

### Step 2: Verify Node.js and JavaScript Package Managers

Check the Node.js installation and available package managers:

```bash
node --version
npm --version
yarn --version
pnpm --version
bun --version
which node
```

**What to expect**: The universal image includes the latest LTS versions of Node.js with multiple package managers (npm, yarn, pnpm, and bun) pre-installed, giving you flexibility in dependency management.

### Step 3: Check Ruby Versions and Tools

Verify Ruby installations using rbenv (Ruby version manager):

```bash
ruby --version
rbenv versions
gem --version
bundler --version
which ruby
```

**What to expect**: You'll see Ruby versions 3.1.6, 3.2.6, and 3.3.6 available, with 3.3.6 set as the default. The gem package manager and bundler are pre-configured for dependency management.

**Additional tip**: You can switch Ruby versions if needed using `rbenv global <version>` or `rbenv local <version>`.

### Step 4: Verify PHP Installation

Check the PHP version and configuration:

```bash
php --version
which php
php -m
```

**What to expect**: PHP 8.4.14 is pre-installed in the universal image. The `php -m` command shows all loaded PHP modules.

### Step 5: Check Java Development Kit

Verify the Java installation and build tools:

```bash
java --version
javac --version
mvn --version
gradle --version
which java
```

**What to expect**: OpenJDK is installed with both Maven and Gradle build tools available for Java project management.

### Step 6: Verify Go Installation

Check the Go programming language installation:

```bash
go version
which go
go env
```

**What to expect**: The latest stable version of Go with module support is pre-installed. The `go env` command displays all Go environment variables.

### Step 7: Check Rust Toolchain

Verify Rust and its package manager:

```bash
rustc --version
cargo --version
which rustc
rustup show
```

**What to expect**: The Rust toolchain with cargo (package manager and build tool) is available in the universal image.

### Step 8: Verify C++ Compilers

Check for C and C++ compilation tools:

```bash
gcc --version
g++ --version
clang --version
which gcc
```

**What to expect**: Both GCC and Clang compilers are pre-installed for C and C++ development.

### Step 9: Check Database Installations

Verify PostgreSQL and Redis installations:

```bash
psql --version
redis-server --version
which psql
which redis-server
```

**What to expect**: PostgreSQL version 16 and Redis version 7.0 are pre-installed and available in the universal image.

### Step 10: Create a Comprehensive Environment Report

Ask Claude to generate a complete report of all installed toolchains:

You can request: "Please create a comprehensive report of all pre-installed language versions, databases, and build tools in this environment."

Claude can execute a script that checks all major toolchains and outputs a formatted report showing:
- Language versions (Python, Node.js, Ruby, PHP, Java, Go, Rust, C++)
- Package managers (pip, npm, gem, cargo, etc.)
- Build tools (Maven, Gradle, bundler, etc.)
- Databases (PostgreSQL, Redis)
- System information (Ubuntu version, architecture)

## Expected Results

After following these steps, you should have a clear understanding of:

- **Python 3.x** with pip, poetry, and scientific libraries
- **Node.js LTS** with npm, yarn, pnpm, and bun
- **Ruby 3.1.6, 3.2.6, 3.3.6** (default: 3.3.6) with gem, bundler, and rbenv
- **PHP 8.4.14**
- **OpenJDK** with Maven and Gradle
- **Go** (latest stable) with module support
- **Rust** with cargo
- **GCC and Clang** compilers for C/C++
- **PostgreSQL 16**
- **Redis 7.0**

All tools should return version information without errors, confirming they're properly installed and accessible in your PATH.

## Troubleshooting

### Common Issue 1: Command Not Found
**Problem**: A version check command returns "command not found" error.
**Solution**: The tool might not be in the default PATH. Try using the full path or ask Claude to locate it with `find /usr -name <tool-name> 2>/dev/null` or `which -a <tool-name>`.

### Common Issue 2: Different Version Than Expected
**Problem**: The version shown differs from what you expected based on this article.
**Solution**: The universal image is periodically updated. The versions listed here were accurate as of November 2025, but Anthropic may update the container image with newer versions. Document the actual versions in your CLAUDE.md file for your project.

### Common Issue 3: Need a Specific Version Not Installed
**Problem**: Your project requires a specific language version that's not the default.
**Solution**: For languages with version managers (like rbenv for Ruby), you can switch versions. For others, you can install specific versions using SessionStart hooks or request Claude to install them during the session. Note that changes made during a session are not persistent across sessions.

### Common Issue 4: Want to Check Additional Tools
**Problem**: You need to verify tools not covered in this guide.
**Solution**: Use standard version checking commands: `<tool> --version`, `<tool> -v`, or `which <tool>`. You can also ask Claude to check the Ubuntu package manager with `dpkg -l` or `apt list --installed`.

## Additional Tips

- **Document in CLAUDE.md**: It's recommended to specify required runtime versions explicitly in your project's CLAUDE.md file to avoid environment confusion.
- **Use which for Paths**: The `which` command helps verify not just that a tool is installed, but also which version is being used when multiple versions exist.
- **Check Environment Variables**: Use `env` or `printenv` to see all environment variables, which can affect tool behavior.
- **Leverage Version Managers**: For Ruby (rbenv), Node.js (nvm if installed via hooks), and other languages with version managers, you can switch between pre-installed versions.
- **SessionStart Hooks**: For tools not in the universal image, create SessionStart hooks to automatically install them at the beginning of each session.
- **Standardized Environment**: Unlike the CLI version which adapts to your local configuration, the web version uses standardized Ubuntu containers offering consistency across sessions.
- **Network Access**: The container has network access limited to approved package repositories (npm, PyPI, etc.) by default, which you can configure based on your needs.

## Related Articles
- KB-001: Getting started with Claude Code Web
- KB-037: Using .env files for environment configuration (if available)
- Session Start Hooks: Automating dependency installation (if available)

## Sources
- Claude Code on the web - Claude Code Docs - https://docs.claude.com/en/docs/claude-code/claude-code-on-the-web (Accessed: November 16, 2025)
- Claude Code on the web - Official Announcement - https://www.anthropic.com/news/claude-code-on-the-web (Accessed: November 16, 2025)
- Claude Code on the web - Support Center - https://support.claude.com/en/articles/12618689-claude-code-on-the-web (Accessed: November 16, 2025)
- Simon Willison's Weblog - Claude Code for web - https://simonwillison.net/2025/Oct/20/claude-code-for-web/ (Accessed: November 16, 2025)
- Claude Code on the Web Complete Guide for Developers (2025) - https://www.cursor-ide.com/blog/claude-code-on-the-web (Accessed: November 16, 2025)
- Google Search Suggestion: "Claude Code Web universal image toolchains 2025"
- Google Search Suggestion: "Claude Code Web environment pre-installed versions"

---
*This article is part of the Claude Code Web knowledge base*
