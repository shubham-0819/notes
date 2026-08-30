# Git Worktree — Complete Guide

A practical guide for developers who know Git but haven't used worktrees yet.

---

## What is a Git Worktree?

By default, a Git repo gives you **one working directory** tied to one checked-out branch. `git worktree` lets you **check out multiple branches simultaneously** into separate directories — all sharing the same `.git` history and objects.

```
my-repo/                  ← main worktree (branch: main)
my-repo-feature/          ← linked worktree (branch: feature/auth)
my-repo-hotfix/           ← linked worktree (branch: hotfix/payment)
```

All three directories share the same `.git` database. No duplication of history, no separate clones.

---

## Why Use Worktrees?

### The Problem Without Worktrees

You're deep into a feature branch with unstaged/staged changes and you get a critical hotfix request:

```bash
# Option A: Stash your changes — risky, easy to forget
git stash
git checkout hotfix/payment-bug
# ... fix bug ...
git checkout feature/auth
git stash pop    # Hope nothing conflicts

# Option B: Make a full clone — wastes disk space and re-downloads history
git clone . ../hotfix-clone
```

### With Worktrees

```bash
# Check out the hotfix branch in a separate directory — instantly
git worktree add ../my-repo-hotfix hotfix/payment-bug
cd ../my-repo-hotfix
# ... fix bug, commit, push ...
# Return to feature work — your original directory is untouched
```

### When Worktrees Shine

| Scenario | Why Worktrees Help |
|---|---|
| Hotfix while mid-feature | No stashing, no context loss |
| Code review while working | Open PR branch side-by-side |
| Running two versions simultaneously | Test old vs new without switching |
| Long-running builds | Build one branch while editing another |
| Parallel feature development | Work on multiple branches at once |
| Bisecting a bug | Keep bisect state separate |

---

## Core Concepts

- **Main worktree** — Your original `git clone` directory. Always exists.
- **Linked worktree** — Additional working directories created by `git worktree add`.
- **Bare repository** — A `.git`-only repo with no working directory (advanced pattern, covered later).
- **One branch, one worktree** — A branch can only be checked out in **one** worktree at a time. Git enforces this.

---

## All Commands

### `git worktree add` — Create a New Worktree

```bash
# Basic: create a worktree for an existing branch
git worktree add <path> <branch>

# Example
git worktree add ../my-repo-hotfix hotfix/payment-bug

# Create a new branch at the same time (-b flag)
git worktree add -b feature/new-dashboard ../my-repo-dashboard

# Create from a specific commit or tag
git worktree add ../my-repo-v2 v2.0.0

# Create a detached HEAD worktree (no branch)
git worktree add --detach ../my-repo-inspect HEAD~5

# Create from a remote branch
git worktree add ../my-repo-feat origin/feature/remote-branch
```

### `git worktree list` — View All Worktrees

```bash
git worktree list

# Output:
# /home/user/my-repo          abc1234 [main]
# /home/user/my-repo-hotfix   def5678 [hotfix/payment-bug]
# /home/user/my-repo-dashboard 9ab0123 [feature/new-dashboard]

# Verbose output with extra details
git worktree list --verbose

# Porcelain format (for scripting)
git worktree list --porcelain
```

### `git worktree remove` — Delete a Worktree

```bash
# Remove a linked worktree (directory must be clean)
git worktree remove <path>

# Example
git worktree remove ../my-repo-hotfix

# Force remove even with uncommitted changes
git worktree remove --force ../my-repo-hotfix

# Note: You cannot remove the main worktree with this command
```

### `git worktree move` — Relocate a Worktree

```bash
# Move worktree to a new path
git worktree move <current-path> <new-path>

# Example
git worktree move ../my-repo-hotfix ../hotfixes/payment-bug
```

### `git worktree lock` / `unlock` — Prevent Accidental Deletion

```bash
# Lock a worktree (e.g., on a mounted drive that may disconnect)
git worktree lock <path>
git worktree lock <path> --reason "On external SSD, do not prune"

# Unlock
git worktree unlock <path>
```

### `git worktree prune` — Clean Up Stale Entries

```bash
# Remove worktree metadata for directories that no longer exist
git worktree prune

# Dry run — see what would be pruned
git worktree prune --dry-run

# Verbose output
git worktree prune --verbose

# Expire worktrees older than specified time
git worktree prune --expire 7.days.ago
```

> Use `prune` when you deleted a worktree directory manually (e.g., `rm -rf`) instead of using `git worktree remove`. Git's internal references become stale in that case.

### `git worktree repair` — Fix Broken References

```bash
# Repair worktree administrative files if paths have changed
git worktree repair

# Repair with explicit path
git worktree repair <path>
```

---

## Practical Workflows

### Workflow 1: Emergency Hotfix Without Stashing

```bash
# You're on feature/auth with work in progress
git status
# On branch feature/auth
# Changes not staged for commit: ...

# Create a hotfix worktree without touching your current work
git worktree add ../hotfix hotfix/critical-bug

cd ../hotfix
# ... investigate, fix, test ...
git add .
git commit -m "fix: resolve payment gateway timeout"
git push origin hotfix/critical-bug

# Open a PR, then clean up
cd ../my-repo
git worktree remove ../hotfix

# Your feature/auth work is exactly as you left it
git status   # unchanged
```

### Workflow 2: Reviewing a Pull Request Locally

```bash
# Checkout PR branch in a separate directory — no branch switching
git fetch origin
git worktree add ../review-pr-123 origin/feature/pr-123

cd ../review-pr-123
# Run the app, run tests, inspect changes
npm install && npm test

# Done reviewing — remove
cd ../my-repo
git worktree remove ../review-pr-123
```

### Workflow 3: Side-by-Side Comparison

```bash
# Run old version and new version simultaneously
git worktree add ../app-v1 v1.5.0
git worktree add ../app-v2 v2.0.0-rc

# Terminal 1 (old version)
cd ../app-v1 && PORT=3001 npm start

# Terminal 2 (new version)
cd ../app-v2 && PORT=3002 npm start

# Compare both in browser at localhost:3001 and localhost:3002
```

### Workflow 4: Long-Running Build

```bash
# Start a build on main — this will take 10 minutes
git worktree add ../build-staging main
cd ../build-staging && npm run build:production &

# Meanwhile, keep working on your feature branch without interruption
cd ../my-repo
git checkout feature/new-component
# ... continue development ...
```

### Workflow 5: Bare Repo Pattern (Advanced)

A popular pattern for power users — use a bare repo as the central `.git` and create worktrees for every branch.

```bash
# Clone as bare repo (contains only .git contents, no working directory)
git clone --bare https://github.com/user/repo.git my-repo.git
cd my-repo.git

# Add a worktree for each branch you need
git worktree add ../my-repo/main main
git worktree add ../my-repo/feature feature/auth
git worktree add ../my-repo/hotfix hotfix/bug

# Directory structure:
# my-repo.git/          ← bare repo (.git data lives here)
# my-repo/main/         ← working tree for main
# my-repo/feature/      ← working tree for feature/auth
# my-repo/hotfix/       ← working tree for hotfix/bug
```

This pattern pairs extremely well with `tmux` — one window per branch, all always available.

---

## Worktrees with Common Tools

### VS Code

Each worktree is a separate directory — open them as independent VS Code windows:

```bash
code ../my-repo-hotfix    # opens as its own VS Code window
code ../my-repo           # your main window, unaffected
```

Or use VS Code's "Add Folder to Workspace" to have multiple worktrees in one window.

### npm / Node Projects

Each worktree needs its own `node_modules` — they are **not** shared:

```bash
git worktree add ../my-repo-feat feature/new-api
cd ../my-repo-feat
npm install    # required — node_modules is not in .git
```

Consider symlinking `node_modules` if dependencies are the same:

```bash
ln -s ../my-repo/node_modules ./node_modules
```

### Python / Virtual Environments

```bash
git worktree add ../my-repo-feat feature/ml-update
cd ../my-repo-feat
python -m venv .venv        # create isolated env per worktree
source .venv/bin/activate
pip install -r requirements.txt
```

### Docker

Each worktree can have its own `.env` or docker config — they share source files but can be built independently:

```bash
cd ../my-repo-hotfix
docker build -t myapp:hotfix .
docker run -p 3001:3000 myapp:hotfix
```

---

## Rules & Constraints

| Rule | Detail |
|---|---|
| One branch per worktree | The same branch cannot be checked out in two worktrees simultaneously |
| Main worktree can't be removed | Only linked worktrees can be removed with `git worktree remove` |
| Bare repos have no main worktree | Everything is a linked worktree in the bare pattern |
| Locked worktrees won't be pruned | Use lock when worktree is on removable media |
| `.git` file in linked worktrees | Linked worktrees have a `.git` *file* (not folder) pointing back to the main `.git` |

---

## Useful Aliases

Add to your `~/.gitconfig`:

```ini
[alias]
    wt      = worktree
    wta     = worktree add
    wtl     = worktree list
    wtr     = worktree remove
    wtls    = worktree list --verbose
    # Create worktree with new branch based on current branch
    wtb     = "!f() { git worktree add -b $1 ../$1; }; f"
```

Usage:

```bash
git wtl                        # list all worktrees
git wta ../hotfix hotfix/bug   # add worktree
git wtb feature/new-auth       # create new branch + worktree in ../feature-new-auth
git wtr ../hotfix              # remove worktree
```

---

## Quick Reference Card

```
git worktree add <path> <branch>          Create worktree for existing branch
git worktree add -b <branch> <path>       Create worktree + new branch
git worktree add --detach <path> <commit> Detached HEAD worktree
git worktree list                         List all worktrees
git worktree list --verbose               List with extra info
git worktree remove <path>                Remove linked worktree
git worktree remove --force <path>        Force remove (dirty state)
git worktree move <src> <dst>             Move worktree to new path
git worktree lock <path>                  Lock against accidental prune
git worktree unlock <path>                Unlock
git worktree prune                        Clean stale worktree metadata
git worktree prune --dry-run              Preview what prune would remove
git worktree repair                       Fix broken worktree references
```

---

## Common Mistakes to Avoid

**1. Deleting the directory manually without removing from Git**
```bash
# Wrong
rm -rf ../my-repo-hotfix

# Right
git worktree remove ../my-repo-hotfix
# Or if already deleted manually:
git worktree prune
```

**2. Trying to check out the same branch twice**
```bash
git worktree add ../duplicate main
# error: 'main' is already checked out at '/home/user/my-repo'
```

**3. Forgetting to install dependencies in the new worktree**
```bash
# After creating a worktree for a Node/Python project:
cd ../my-new-worktree
npm install        # Always run this!
```

**4. Committing from the wrong worktree directory**
```bash
# Always check which worktree you're in
git worktree list
pwd
git branch   # Confirm your current branch
```

---

## Resources

- [Official Git Worktree Docs](https://git-scm.com/docs/git-worktree)
- [Pro Git Book — Worktrees](https://git-scm.com/book/en/v2)

---
