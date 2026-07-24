# 012 – Rebasing

## Introduction

As projects grow, the `main` branch continues to evolve while developers work on separate feature branches. By the time a feature is finished, the branch may be based on an outdated version of the project.

Rebasing is Git's way of moving a branch onto a newer base while preserving its changes.

Unlike merging, rebasing does not combine two branches with a merge commit. Instead, it **recreates your commits on top of another branch**, producing a clean and linear history.

---

# What is Rebasing?

Imagine the following history:

```text
      C ─── D   (feature)
     /
A ─ B
     \
      E ─── F   (main)
```

The `feature` branch was created from commit **B**.

Meanwhile, `main` continued to evolve until commit **F**.

If we execute:

```bash
git rebase main
```

Git does **not** merge the branches.

Instead, it changes the base of `feature` from **B** to **F**.

After the rebase, the history becomes:

```text
A ─ B ─ E ─ F ─ C' ─ D'
                     ↑
                  feature
```

Notice that the original commits **C** and **D** have become **C'** and **D'**.

Those are **new commits**.

---

# Understanding the Base of a Branch

Every branch starts from a specific commit.

That commit is called its **base**.

In the previous example:

```text
      C ─── D
     /
A ─ B
```

The base of `feature` is **B** because that is where the branch was created.

Rebasing simply means:

> **Change the base of the branch.**

Instead of pretending that the branch started from **B**, Git makes it appear as if it started from the latest commit of another branch.

---

# How Rebasing Works Internally

One of the biggest misconceptions about rebasing is believing that Git **moves commits**.

It does not.

Git commits are **immutable**.

Once created, a commit cannot be modified.

Since changing the parent of a commit would modify its contents, Git cannot simply "move" it somewhere else.

Instead, Git creates completely new commits.

For example:

Before:

```text
Commit C
Parent = B
```

After rebasing:

```text
Commit C'
Parent = F
```

The changes are identical.

The parent is different.

Therefore, it must be a completely new commit.

> **Rebase does not move commits. It recreates them on top of a new base.**

---

# Replaying Commits

Internally, Git performs a rebase by replaying each commit one at a time.

Conceptually, the algorithm looks like this:

```text
Change the base

↓

Take the first commit

↓

Apply its changes

↓

Create a new commit

↓

Repeat for every remaining commit
```

Suppose our branch contains:

```text
C → Add login page

D → Fix login bug
```

Git first recreates **C**.

Only after that can it recreate **D**.

The order matters because each commit depends on the state left by the previous one.

Creating **D'** before **C'** would often be impossible, since the code modified by **D** may not exist yet.

> **A rebase is essentially a replay of your commits on top of a new base.**

---

# What Happens to the Original Commits?

After the rebase, the branch points to the newly created commits.

```text
feature
   │
   ▼
C' ─ D'
```

The original commits are no longer referenced.

```text
C ─ D
```

They still exist inside Git for some time, but no branch points to them anymore.

Eventually, Git's garbage collector removes them.

This is why rebasing does not immediately destroy commits.

They simply become unreachable.

---

# Handling Rebase Conflicts

Sometimes Git cannot replay one of the commits because the project has changed too much.

For example:

```text
Replay C
✓ Success

Replay D
✗ Conflict
```

The rebase stops immediately.

You resolve the conflict exactly as you would during a merge:

1. Edit the conflicting files.
2. Remove the conflict markers.
3. Keep the desired code.
4. Save the files.

Then continue the rebase:

```bash
git add .
git rebase --continue
```

Git recreates the current commit and continues replaying the remaining ones.

If you decide that the rebase should not continue:

```bash
git rebase --abort
```

This restores the repository to the state before the rebase began.

---

# Merge vs Rebase

Suppose we have:

```text
      C ─ D
     /
A ─ B ─ E ─ F
```

## Merge

A merge preserves the real development history.

```text
      C ─ D
     /     \
A ─ B ─ E ─ F ─ M
```

Advantages:

- Preserves the complete history.
- Safe for shared branches.
- Shows exactly how development happened.

Disadvantages:

- History becomes more complex.
- Many merge commits can clutter the graph.

---

## Rebase

A rebase produces:

```text
A ─ B ─ E ─ F ─ C' ─ D'
```

Advantages:

- Clean linear history.
- Easier to read.
- Simpler `git log`.
- Makes the project history appear as if the feature had been developed on the latest version from the beginning.

Disadvantages:

- Rewrites commit history.
- Changes commit hashes.
- Should not be used on shared commits.

---

# When to Use Rebasing

A common workflow is:

- Use **rebase** to update your local feature branch with the latest changes from `main`.
- Use **merge** when integrating shared work or when preserving the exact development history is important.

As a general rule:

> **Never rebase commits that have already been shared with other people.**

Because rebasing creates new commits, every recreated commit receives a new hash.

If other developers already have the original commits, rewriting history can make synchronization much more difficult.

---

# Common Commands

Rebase onto another branch:

```bash
git rebase main
```

Continue after resolving conflicts:

```bash
git add .
git rebase --continue
```

Abort the rebase:

```bash
git rebase --abort
```

Skip the current commit:

```bash
git rebase --skip
```

---

# Mental Model

Imagine taking your work, placing it on top of the latest version of the project, and replaying each change one by one.

That is exactly what Git does during a rebase.

It does not move commits.

It recreates them in order on top of a new base.

---

# Key Takeaways

- Every branch has a base.
- Rebasing changes that base.
- Git commits are immutable.
- Rebasing does not move commits.
- Git recreates each commit one by one.
- Commit order is preserved during the replay.
- Conflicts pause the rebase until they are resolved.
- Rebasing produces a clean, linear history.
- Merge preserves the real development history.
- **Never rebase commits that have already been shared with other people.**