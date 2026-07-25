# Git Reflog

Git is designed to prevent data loss whenever possible.

Commands such as `reset`, `rebase`, or `checkout` can move branches and make commits appear to disappear.

In many cases, however, those commits are not actually lost.

Git keeps a record of where important references have been, allowing previously reachable commits to be found again.

This record is called the **Reference Log**, or simply **reflog**.

---

# What Is the Reflog?

The reflog records the recent positions of Git references, especially `HEAD`.

Unlike the commit history, the reflog does **not** describe how the project evolved.

Instead, it records where Git references have pointed over time.

A useful way to think about it is:

```text
Git History
"What happened to the project?"


Reflog
"Where has HEAD been?"
```

This distinction is the key to understanding the reflog.

---

# Git History vs Reflog

Suppose the repository contains:

```text
A───B───C───D
            ^
        HEAD, main
```

If you execute:

```bash
git reset --hard HEAD~2
```

the repository becomes:

```text
A───B
    ^
HEAD, main
```

From the point of view of the current branch, commits `C` and `D` are no longer reachable.

Running:

```bash
git log
```

shows:

```text
A───B
```

because `git log` displays only the commits that are reachable from the current branch.

However, the reflog still remembers that `HEAD` recently pointed to `C` and `D`.

Conceptually:

```text
A
↓
B
↓
C
↓
D
↓
B
```

The reflog therefore records movements of references rather than the visible history of the project.

---

# Viewing the Reflog

The reflog can be displayed with:

```bash
git reflog
```

A typical output looks similar to:

```text
abc1234 HEAD@{0}: reset: moving to HEAD~2
def5678 HEAD@{1}: commit: Finish authentication
9876abc HEAD@{2}: commit: Add login page
4567def HEAD@{3}: clone: from github.com/example/project
```

Each entry represents one movement of `HEAD`.

The most recent movement always appears first.

---

# Understanding Reflog Entries

Consider the following entry:

```text
def5678 HEAD@{1}: commit: Finish authentication
```

It contains three important pieces of information.

### Commit Hash

```text
def5678
```

The commit hash identifies the commit that `HEAD` pointed to after that operation.

---

### Reflog Position

```text
HEAD@{1}
```

This indicates the position within the reflog.

For example:

```text
HEAD@{0}
```

means:

> The current position of `HEAD`.

While:

```text
HEAD@{1}
```

means:

> The previous position of `HEAD`.

Similarly:

```text
HEAD@{2}
```

represents the position before that.

These references can be used directly in Git commands.

For example:

```bash
git reset --hard HEAD@{1}
```

moves the current branch back to the previous reflog entry.

---

### Recorded Action

The final part describes the operation that moved `HEAD`.

Examples include:

```text
commit: Add login page

reset: moving to HEAD~1

checkout: moving from main to feature

rebase finished

merge feature
```

The reflog therefore provides both the destination and the reason why `HEAD` moved.

---

# Recovering Lost Commits

One of the most common uses of the reflog is recovering commits that became unreachable after commands such as `git reset`.

Suppose the repository initially contains:

```text
A───B───C───D
            ^
        HEAD, main
```

After executing:

```bash
git reset --hard HEAD~2
```

the visible history becomes:

```text
A───B
    ^
HEAD, main
```

Although commits `C` and `D` no longer appear in `git log`, they often still appear in the reflog.

For example:

```text
HEAD@{0}  B  reset: moving to HEAD~2
HEAD@{1}  D  commit: Finish authentication
HEAD@{2}  C  commit: Add login page
```

The branch can then be moved back to that commit.

For example:

```bash
git reset --hard HEAD@{1}
```

or:

```bash
git reset --hard <commit-hash>
```

Both commands move the current branch back to the selected commit.

---

# Recovering Commits Safely

Although `git reset` can restore a lost commit, it immediately changes the current branch and working directory.

A safer approach is to create a new branch that points to the recovered commit.

For example:

```bash
git branch recovery HEAD@{1}
```

or, if you are currently on the recovered commit:

```bash
git switch -c recovery
```

The repository now looks like:

```text
           recovery
               │
               ▼
A───B───C───D

    ▲
    │
  main
```

The original branch remains unchanged, while the recovered commit is now protected by a new branch.

This gives you time to inspect the recovered work before deciding whether to merge it back into your main development branch.

In general, creating a temporary branch is safer than immediately performing another `reset`.

---

# HEAD Reflog vs Branch Reflogs

The reflog usually refers to the history of `HEAD`.

Running:

```bash
git reflog
```

is equivalent to:

```bash
git reflog show HEAD
```

This answers the question:

> Where has `HEAD` been?

However, Git also maintains reflogs for branches.

For example:

```bash
git reflog show main
```

shows the movements of the `main` branch.

This distinction becomes important when switching branches.

Suppose the repository contains:

```text
A───B───C
        ▲
      main
```

If you execute:

```bash
git switch feature
```

only `HEAD` moves.

The `main` branch still points to the same commit.

Therefore:

- The **HEAD reflog** records the branch switch.
- The **main reflog** does not change.

A useful mental model is:

```text
HEAD reflog
Where have I been?


Branch reflog
Where has this branch been?
```

Most of the time, the default `git reflog` command is all that you need.

---

# Limitations of the Reflog

Although the reflog is extremely useful, it is important to understand its limitations.

## The Reflog Is Local

Every Git repository has its own reflog.

It is never pushed to remote repositories.

For example:

```text
Your Computer
✓ Reflog exists

GitHub
✗ No reflog

Another Clone
✗ Different reflog
```

This means that another developer cannot recover your local commits using their own reflog.

---

## Reflog Entries Are Temporary

The reflog is not intended to be permanent.

Old entries are eventually removed by Git's garbage collection process.

As a result, the reflog should be viewed as a temporary recovery mechanism rather than a permanent backup.

If a commit is important, it should be protected by creating a branch or pushing it to a remote repository.

---

## Detached HEAD Does Not Protect Commits

Checking out an old commit does not automatically preserve it.

For example:

```bash
git switch --detach <commit>
```

places `HEAD` directly on that commit.

If you later leave detached HEAD without creating a branch, the commit may once again become unreachable.

To keep the commit permanently, create a branch before leaving detached HEAD:

```bash
git switch -c recovery
```

or:

```bash
git branch recovery
```

A branch creates a permanent reference.

Detached HEAD does not.

---

# Key Takeaways

- The reflog records the movements of Git references, especially `HEAD`.
- `git log` shows the current reachable commit history, while `git reflog` shows where references have been.
- Every movement of `HEAD` creates a new reflog entry.
- Reflog entries can be referenced using expressions such as `HEAD@{1}`.
- The reflog makes it possible to recover commits that are no longer reachable after operations such as `git reset`.
- Creating a recovery branch is often safer than immediately resetting the current branch.
- Git maintains reflogs for both `HEAD` and individual branches.
- The reflog is local to each repository and is never shared with remote repositories.
- Reflog entries are temporary and should not be considered permanent backups.