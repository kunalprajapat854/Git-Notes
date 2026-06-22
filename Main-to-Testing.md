# Cherry-Pick a Specific Commit from Main to Testing

## Overview

`git cherry-pick` allows you to copy a specific commit from one branch and apply it to another branch without merging the entire branch.

This is useful when:

* You need only one bug fix from `main`.
* You need only one feature from `main`.
* You don't want to merge all changes from `main` into `testing`.
* You want to selectively move commits between branches.

---

## Step 1: Find the Commit Hash

View commits from the `main` branch:

```bash
git log main --oneline
```

Example Output:

```text
130c4d5 third update
db2b8ff Merge branch 'main' into testing
e073362 full complete
```

Copy the commit hash you want to move.

Example:

```text
130c4d5
```

---

## Step 2: Switch to Testing Branch

Move to the target branch:

```bash
git checkout testing
```

Pull the latest changes:

```bash
git pull origin testing
```

Verify current branch:

```bash
git branch
```

Expected Output:

```text
* testing
  main
```

---

## Step 3: Cherry-Pick the Commit

Apply the selected commit from `main`:

```bash
git cherry-pick <commit-hash>
```

Example:

```bash
git cherry-pick 130c4d5
```

Git will create a new commit on the `testing` branch containing the same changes.

---

## Step 4: Verify the Commit

Check the latest commits:

```bash
git log --oneline -5
```

Example:

```text
abc1234 third update
790d58a Revert "security-fixes"
55213d0 Reapply "security-fixes"
```

The cherry-picked commit should appear at the top.

---

## Step 5: Push Changes

Push the updated `testing` branch:

```bash
git push origin testing
```

---

# Handling Cherry-Pick Conflicts

Sometimes the same files have been modified in both branches.

Example Error:

```text
CONFLICT (content): Merge conflict in src/app.js
error: could not apply 130c4d5
```

---

## Resolve the Conflict

Open the conflicted file:

```text
<<<<<<< HEAD
Current testing code
=======
Code from main branch
>>>>>>> 130c4d5
```

Edit the file and keep the desired code.

---

## Mark Conflict as Resolved

```bash
git add .
```

or

```bash
git add <file-name>
```

---

## Continue Cherry-Pick

```bash
git cherry-pick --continue
```

---

## Abort Cherry-Pick

If you want to cancel the operation:

```bash
git cherry-pick --abort
```

---

# Cherry-Pick Multiple Commits

Apply multiple commits:

```bash
git cherry-pick commit1 commit2 commit3
```

Example:

```bash
git cherry-pick 130c4d5 e073362 db2b8ff
```

---

# Cherry-Pick a Range of Commits

Apply a range:

```bash
git cherry-pick start_commit^..end_commit
```

Example:

```bash
git cherry-pick 94ca89d^..130c4d5
```

---

# Useful Commands

### View Main Branch History

```bash
git log main --oneline
```

### View Testing Branch History

```bash
git log testing --oneline
```

### Check Current Branch

```bash
git branch
```

### Check Repository Status

```bash
git status
```

### View Commit Graph

```bash
git log --oneline --graph --decorate --all
```

---

# Example Workflow

```bash
# Find commit
git log main --oneline

# Switch branch
git checkout testing

# Get latest changes
git pull origin testing

# Apply commit from main
git cherry-pick 130c4d5

# Verify
git log --oneline -5

# Push
git push origin testing
```

---

# When to Use Cherry-Pick

✅ Move a single bug fix from `main` to `testing`

✅ Move a single feature from `main` to `testing`

✅ Apply a hotfix without merging all changes

✅ Selectively move commits between branches

❌ Do not use when you want the entire branch history (use `git merge` instead)

❌ Do not use repeatedly for large groups of commits (consider merging)
