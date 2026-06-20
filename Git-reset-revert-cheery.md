# Git Commands Reference Guide

## 1. Undo the Last Commit

### Keep Changes Staged

Remove the latest commit while keeping all changes staged:

```bash
git reset --soft HEAD~1
```

**Result:**

* Last commit is removed.
* Changes remain staged and ready to commit again.

---

### Keep Changes Unstaged

Remove the latest commit while keeping changes in the working directory:

```bash
git reset HEAD~1
```

or

```bash
git reset --mixed HEAD~1
```

**Result:**

* Last commit is removed.
* Changes remain in the working directory.
* Files are not staged.

---

### Remove Commit and Discard Changes

⚠️ This permanently removes the commit and all associated changes.

```bash
git reset --hard HEAD~1
```

---

## 2. Undo a Pushed Commit

If a commit has already been pushed to a shared repository, avoid using `git reset`.

Instead, create a new commit that reverses the changes:

```bash
git revert <commit-id>
```

Example:

```bash
git revert b1d5f21
```

---

## 3. Unstage Files

### Unstage a Specific File

```bash
git restore --staged filename
```

Example:

```bash
git restore --staged info.txt
```

---

### Unstage All Files

```bash
git restore --staged .
```

or

```bash
git reset
```

---

## 4. Convert a Staged New File Back to Untracked

If a new file was added using `git add`:

```bash
git restore --staged newfile.txt
```

The file will become untracked again.

Check status:

```bash
git status
```

---

## 5. Remove Untracked Files

### Preview Files to be Deleted

```bash
git clean -n
```

### Delete Untracked Files

⚠️ This permanently deletes the files.

```bash
git clean -f
```

---

## 6. Move Selected Files from `main` to `testing`

### Method 1: Checkout Specific Files

Switch to the target branch:

```bash
git checkout testing
```

Copy specific files from `main`:

```bash
git checkout main -- path/to/file1 path/to/file2
```

Example:

```bash
git checkout main -- Dockerfile docker-compose.yml
```

Commit the changes:

```bash
git add Dockerfile docker-compose.yml
git commit -m "Add selected files from main"
```

---

### Method 2: Cherry-Pick a Commit

If all required changes exist in a single commit:

```bash
git checkout testing
git cherry-pick <commit-id>
```

Example:

```bash
git checkout testing
git cherry-pick 1f3942b
```

---

### Method 3: Restore Files from Another Branch

Using modern Git syntax:

```bash
git switch testing
git restore --source main -- path/to/file1 path/to/file2
```

Commit the changes:

```bash
git add .
git commit -m "Bring selected files from main"
```

---

## Useful Verification Commands

### Check Repository Status

```bash
git status
```

### View Commit History

```bash
git log --oneline -5
```

### View Commit Graph

```bash
git log --oneline --graph --all
```

---

## Common Workflow Example

```bash
# Move to testing branch
git checkout testing

# Copy selected files from main
git checkout main -- Dockerfile docker-compose.yml

# Verify changes
git status

# Commit changes
git add Dockerfile docker-compose.yml
git commit -m "Copy Docker files from main"

# Push to remote
git push origin testing
```
