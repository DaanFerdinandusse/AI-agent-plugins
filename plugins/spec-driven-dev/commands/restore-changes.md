---
description: Restore uncommitted changes using git, with option to restore all or specific files
---

# Restore Changes

You help the user restore uncommitted changes in their working directory back to their last committed state.

## Process

### 1. Analyze Current Changes

Run these commands to understand the current state, there have been changes made to the files and new files added:

```bash
git status
git diff --stat
```

### 2. Present Changes to User

Show the user a summary of what would be restored:

```
## Uncommitted Changes

The following files have uncommitted changes:

**Modified files:**
- {file1} ({lines added/removed})
- {file2} ({lines added/removed})

**Untracked files:**
- {file3}
- {file4}

Would you like to restore these files to their last committed state?
```

Use AskUserQuestion with options:
- "Restore all changes" - Restore everything
- "Select specific files" - Let user specify which files/patterns
- "Cancel" - Abort the operation

### 3. Execute Restoration

**If user chooses "Restore all changes":**

```bash
# Restore all modified/deleted files
git checkout -- .

# Optionally remove untracked files (ask first)
```

If there are untracked files, ask:
- "Also delete untracked files?" (Yes/No)

If yes: `git clean -fd`

**If user chooses "Select specific files":**

Ask the user which files or patterns to restore. They might say:
- Specific files: "restore src/components/Button.tsx"
- Patterns: "restore all files in src/api/"
- Features: "restore the authentication changes"

For specific files/patterns:
```bash
git checkout -- {file-or-pattern}
```

For feature-based requests:
1. Look at the git diff to identify which files relate to that feature
2. Present the list to the user for confirmation
3. Restore only those files

### 4. Confirm Result

After restoration, run `git status` and confirm to the user what was restored:

```
## Restoration Complete

The following files have been restored:
- {file1}
- {file2}

Current status: {clean/remaining changes}
```

## Important Guidelines

1. **Always confirm before restoring** - This is a destructive operation
2. **Show what will be lost** - Make sure user understands the changes being discarded
3. **Handle untracked files separately** - They require `git clean`, not `git checkout`
4. **Be careful with patterns** - Confirm file list before bulk operations
5. **No force operations** - Never use `git clean -f` without explicit user approval for each category of files
