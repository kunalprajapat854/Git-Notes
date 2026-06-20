# Git Troubleshooting & Branch Management Guide

## Scenario: `git pull` Fails with Divergent Branches

### Error

```bash
git pull origin testing --force
```

Output:

```text
hint: You have divergent branches and need to specify how to reconcile them.
fatal: Need to specify how to reconcile divergent branches.
```

This happens when:

* Local branch contains commits not present on remote.
* Remote branch contains commits not present locally.
* Git does not know whether to merge, rebase, or discard local changes.

---

# Step 1: Check Repository Status

```bash
git status
git branch
git log --oneline --graph --decorate --all -10
```

Example:

```text
* 0c01483 (origin/testing) security-fixes
* f25716c Revert "security-fixes"
| * b924c9e (HEAD -> testing) Revert "security-fixes"
|/
* 9392926 security-fixes
```

This indicates that local and remote branches have diverged.

---

# Decision Tree

## Case 1: Deployment Server

### Condition

* GitHub branch is the source of truth.
* Local commits are not important.
* Server should exactly match remote branch.

### Solution

```bash
git fetch origin
git reset --hard origin/testing
```

Optional cleanup:

```bash
git clean -fd
```

Verify:

```bash
git status
git log --oneline --graph --decorate -5
```

Expected:

```text
HEAD -> testing
origin/testing
```

Both should point to the same commit.

---

## Case 2: Keep Local Commits

### Condition

* Local commits contain important work.
* Remote branch has new commits.
* Need to combine both histories.

### Solution

Merge:

```bash
git pull --no-rebase origin testing
```

or

```bash
git merge origin/testing
```

Resolve conflicts if prompted.

Commit merge:

```bash
git add .
git commit
```

Push:

```bash
git push origin testing
```

---

## Case 3: Keep Clean History

### Condition

* Local commits should be replayed on top of latest remote commits.

### Solution

```bash
git pull --rebase origin testing
```

Resolve conflicts if required:

```bash
git add .
git rebase --continue
```

Push:

```bash
git push origin testing
```

---

# Check Differences Between Local and Remote

## Commits Only in Local

```bash
git log origin/testing..HEAD --oneline
```

Example:

```text
b924c9e Revert "security-fixes"
```

---

## Commits Only in Remote

```bash
git log HEAD..origin/testing --oneline
```

Example:

```text
0c01483 security-fixes
```

---

# Undo Last Commit

## Keep Changes Staged

```bash
git reset --soft HEAD~1
```

Result:

* Commit removed.
* Files remain staged.

---

## Keep Changes Unstaged

```bash
git reset HEAD~1
```

or

```bash
git reset --mixed HEAD~1
```

Result:

* Commit removed.
* Files remain in working directory.

---

## Delete Commit and Changes

⚠️ Dangerous

```bash
git reset --hard HEAD~1
```

Result:

* Commit removed.
* Changes permanently deleted.

---

# Undo Pushed Commit

Never use reset on shared branches.

Instead:

```bash
git revert <commit-id>
```

Example:

```bash
git revert b1d5f21
```

Creates a new commit that reverses changes.

---

# Restore a Reverted Commit

If you accidentally reverted a commit:

```bash
git revert <revert-commit-id>
```

This restores the original code.

Example:

```bash
git revert b924c9e
```

---

# Unstage Files

## Unstage Single File

```bash
git restore --staged filename
```

Example:

```bash
git restore --staged info.txt
```

---

## Unstage All Files

```bash
git restore --staged .
```

or

```bash
git reset
```

---

# Convert Staged File Back to Untracked

For newly added files:

```bash
git restore --staged newfile.txt
```

Check:

```bash
git status
```

Result:

```text
Untracked files:
  newfile.txt
```

---

# Remove Untracked Files

Preview:

```bash
git clean -n
```

Delete:

```bash
git clean -f
```

Delete directories too:

```bash
git clean -fd
```

---

# Move Selected Files from Main to Testing

## Method 1: Checkout Specific Files

Switch branch:

```bash
git checkout testing
```

Copy files:

```bash
git checkout main -- Dockerfile docker-compose.yml
```

Commit:

```bash
git add Dockerfile docker-compose.yml
git commit -m "Copy selected files from main"
```

---

## Method 2: Restore from Main

```bash
git switch testing
git restore --source main -- Dockerfile docker-compose.yml
```

Commit:

```bash
git add .
git commit -m "Bring files from main"
```

---

## Method 3: Cherry Pick Commit

If files are contained within one commit:

```bash
git checkout testing
git cherry-pick <commit-id>
```

Example:

```bash
git cherry-pick 1f3942b
```

---

# Useful Commands

## Repository Status

```bash
git status
```

---

## Branches

```bash
git branch
git branch -a
```

---

## Commit History

```bash
git log --oneline
```

---

## Graph View

```bash
git log --oneline --graph --decorate --all
```

---

# Recommended Workflow for Deployment Servers

```bash
git fetch origin

git reset --hard origin/testing

git clean -fd

git status
```

This ensures the deployment server always matches GitHub exactly.
