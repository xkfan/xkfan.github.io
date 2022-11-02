# GIT CHEATSHEET (中文速查表)

## branch

```bash
git checkout -b dev     # Switch to a new branch 'dev'
git branch dev          # Create a new branch 'dev'
git checkout dev        # Switch to branch 'dev'
```

## log

```bash
git log                        # Show the whole commit history
git log -n 2                   # Show only 2 commits
git log -p                     # Show the patch for each commit as well as their full diff.
git log commit_id              # Show commit history before commit_id, including commit_id
git log commit_id1 commit_id2  # Show commit history between commit_id1 and commit_id2, including commit_id1 and commit_id2
git log commit_id -p -n 1      # Show the patch for commit_id
```
