# Git 002 - Commits & History

## What is a commit?
A commit is a snapshot of the project at a specific moment in time. You can think of it as taking a picture of your project so you can always go back to that exact state later.
Each commit becomes part of the project's history.


## What does git add do?
`git add` selects the changes that will be included in the next commit. In other words, it prepares the changes that will appear in the next snapshot.

## What is git diff?
`git diff` shows the differences between your current work and the last commit. It allows you to review your changes before creating a new commit and helps you avoid committing unintended modifications.

## What is git log?
`git log` displays the history of all the commits in the repository. You can think of it as an album of snapshots that lets you see how the project has evolved over time.

## Summary
Git keeps track of a project's evolution by creating snapshots called commits. Before creating a commit, you can review your changes with `git diff` and choose which ones to include using `git add`. The complete history of the project can be explored at any time with `git log`.

### Key Takeaway
A Git repository is a timeline of snapshots. Every commit represents a saved state of the project that you can inspect, compare, and restore whenever needed.