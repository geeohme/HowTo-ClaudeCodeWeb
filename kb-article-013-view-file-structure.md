# How to View the File Structure of Your Connected Repository

**Article ID**: KB-013
**Difficulty**: Beginner
**Last Updated**: November 16, 2025
**Estimated Time**: 3 minutes

## Overview
Understanding your repository's file structure helps you communicate with Claude about specific files and folders. This guide shows you how to browse and explore your repository's organization within Claude Code Web.

## Prerequisites
- Active Claude Pro or Max subscription
- At least one repository connected to Claude Code Web
- Basic understanding of file and folder hierarchies

## Steps

### Step 1: Select Your Repository

1. Navigate to `https://claude.com/code`
2. Select the repository you want to explore
3. Access the repository's main view

### Step 2: Locate the File Browser

Look for these interface elements:

**Common Locations:**
- Left sidebar with file tree
- "Files" or "Explorer" tab/panel
- File browser icon (usually folder icon)
- Repository overview section

**Common Names:**
- "File Explorer"
- "Project Files"
- "File Tree"
- "Repository Structure"
- "Files & Folders"

### Step 3: Navigate the File Structure

**Expanding Folders:**
- Click the arrow/chevron (▸) next to folder names
- Click the folder name itself in some interfaces
- Folders expand to show their contents

**Collapsing Folders:**
- Click the down arrow/chevron (▾) to collapse
- Click the folder name again in some interfaces

**Viewing Files:**
- File names are usually shown with file type icons
- Common extensions visible (.js, .py, .md, etc.)
- Indentation shows hierarchy depth

### Step 4: Understand the Structure Display

**Typical File Tree Appearance:**
```
▾ repository-name
  ▸ .github
  ▸ node_modules
  ▸ src
    ▸ components
      - Header.js
      - Footer.js
    ▸ styles
    - index.js
  ▾ tests
    - app.test.js
  - .gitignore
  - package.json
  - README.md
```

**Visual Indicators:**
- ▸ or ▶ = Collapsed folder
- ▾ or ▼ = Expanded folder
- 📁 = Folder icon
- 📄 = File icon (varies by type)

### Step 5: Interact with Files and Folders

**Clicking on Files:**
- Some interfaces allow viewing file contents
- Preview pane may open
- File details may be displayed

**Right-Click Options (if available):**
- Copy file path
- Open in new tab
- See file details

**Search Functionality:**
- Look for a search box in the file browser
- Filter files by name
- Some interfaces support regex search

## What You Can See

### Repository Root Information

**Top-Level Files:**
- README.md
- Configuration files (.gitignore, package.json, etc.)
- License file
- Documentation files

**Top-Level Folders:**
- Source code directories (src, lib, app, etc.)
- Test directories
- Build/dist folders
- Node_modules or other dependencies

### Hidden Files and Folders

**Commonly Hidden:**
- .git folder (usually not shown)
- .env files (may be hidden for security)
- OS-specific files (.DS_Store on Mac)

**Visibility Settings:**
- Some interfaces have "Show Hidden Files" option
- Look in settings or view options

### File Metadata

Some interfaces show:
- File sizes
- Last modified dates
- File counts in folders
- Git status indicators (modified, added, etc.)

## Expected Results

After accessing the file structure viewer, you should:
- See a hierarchical tree of your repository's files and folders
- Be able to expand and collapse folders to explore
- Identify where specific files are located
- Understand the organization of your project
- Locate key files like README, package.json, main source files
- Navigate quickly to any part of your codebase

## Troubleshooting

### Common Issue 1
**Problem**: Can't find the file browser or file tree
**Solution**:
- Look for a sidebar toggle button (often three horizontal lines ≡)
- Check for a "Files" or "Explorer" tab in the interface
- Try different views or layout options
- Some interfaces may not show files until you create a task
- Repository may be empty (contains no files)

### Common Issue 2
**Problem**: Folder won't expand
**Solution**:
- Ensure you're clicking the arrow/chevron, not just the folder name
- Folder might be empty
- Try refreshing the page
- Check internet connection
- Large folders may take a moment to load

### Common Issue 3
**Problem**: Can't see certain files you know exist
**Solution**:
- Check if hidden files are being filtered
- Look for "Show Hidden Files" or filter settings
- .git folder is typically never shown
- Very large repositories may have loading limits
- Verify you're looking in the correct branch

### Common Issue 4
**Problem**: File structure looks different than on GitHub
**Solution**:
- Verify you're viewing the correct branch
- Check if certain folders are hidden by default
- Some CI/CD or build artifacts may not appear
- Node_modules or large dependency folders may be collapsed by default

## Using File Structure Information

### When Describing Tasks to Claude

**Instead of:**
```
Add validation to the header file
```

**More specific:**
```
Add email validation to src/components/Header.js
```

**Even better:**
```
In src/components/UserForm/Header.js, add email validation
to the handleSubmit function
```

### Finding Related Files

**Common Patterns:**
- Tests usually in `/tests` or `/__tests__` or next to source files
- Styles often in `/styles`, `/css`, or `/components` folders
- Configuration at root level
- Source code in `/src`, `/lib`, or `/app`

### Understanding Project Structure

**Monorepo indicators:**
- Multiple package.json files
- /packages or /apps folders
- Lerna.json or nx.json files

**Framework-specific structures:**
- React: /src/components, /public
- Next.js: /pages, /app
- Python: /src, /tests, requirements.txt
- Rails: /app, /config, /db

## Additional Tips

### Quick Navigation
- Use search to find files quickly
- Remember common file locations for faster task descriptions
- Bookmark or note key files you work with frequently

### Before Creating Tasks
- Browse the structure to understand the project
- Locate the files you want Claude to modify
- Verify file paths so you can reference them accurately

### Learning Project Organization
- Studying the file structure helps understand the codebase
- Note patterns in folder naming and organization
- Identify where new files should be created

### File Path Format
When referencing files in task descriptions:
- Use relative paths from repository root
- Example: `src/components/Header.js`
- Not: `/full/system/path/to/src/components/Header.js`
- Not: Just `Header.js` (unless root level)

## Related Articles
- KB-011: How to switch between different connected repositories
- KB-014: How to understand which branch Claude Code is working on
- KB-007: How to describe a task effectively for Claude to understand
- KB-006: How to create your first coding task in Claude Code Web

## Sources
1. File Navigation in Web IDEs - Search: "file explorer navigation cloud IDE" (Accessed: November 16, 2025)
2. Repository Structure Best Practices - Search: "understanding git repository file structure" (Accessed: November 16, 2025)
3. Claude Code Web Interface Guide - Search: "claude code web file browser view files" (Accessed: November 16, 2025)

---
*This article is part of the claudecodeweb knowledge base*
