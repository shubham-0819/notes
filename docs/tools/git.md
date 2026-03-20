# Git

> **TODO:** This document is a stub. Expand with branching strategies, advanced workflows, and team collaboration tips.

## What is Git?

Git is a distributed version control system for tracking changes in source code during software development.

## Basic Commands

```bash
# Setup
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Repository
git init                    # Initialize a new repo
git clone <url>             # Clone a remote repo

# Staging & Committing
git status                  # Check working tree status
git add <file>              # Stage a file
git add .                   # Stage all changes
git commit -m "message"     # Commit staged changes
git commit --amend          # Amend the last commit

# Branching
git branch                  # List branches
git branch <name>           # Create a branch
git checkout <branch>       # Switch to branch
git checkout -b <branch>    # Create and switch
git merge <branch>          # Merge branch into current
git branch -d <branch>      # Delete a branch

# Remote
git remote add origin <url> # Add remote
git push origin <branch>    # Push to remote
git pull origin <branch>    # Pull from remote
git fetch                   # Fetch without merging

# History
git log                     # View commit history
git log --oneline           # Compact history
git diff                    # Show unstaged changes
git diff --staged           # Show staged changes
```

## Undoing Changes

```bash
git restore <file>          # Discard working dir changes
git restore --staged <file> # Unstage a file
git revert <commit>         # Create a revert commit
git reset --hard <commit>   # Reset to commit (destructive)
```

## Common Workflows

- **Feature Branch Workflow** - Create a branch per feature, merge via PR
- **Gitflow** - `main`, `develop`, `feature/*`, `release/*`, `hotfix/*`
- **Trunk-Based Development** - Short-lived branches, frequent merges to main

## References

- [Git Official Docs](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2)
