# 013 – Cherry-pick

## Introduction

Sometimes you do not want to merge an entire branch.

Sometimes you do not want to rebase it either.

Instead, you only need **one specific commit**.

Perhaps a developer fixed an important bug in another branch, and you want that fix immediately without bringing the rest of the unfinished work.

This is exactly what `git cherry-pick` is designed for.

Cherry-pick allows you to copy the changes introduced by a specific commit and apply them to your current branch.

---

# What is Cherry-pick?

Suppose we have the following history:

```text
A ─ B ─ C                 (main)
     \
      D ─ E ─ F           (feature)
```

Imagine that commit **E** contains an important bug fix.

You are currently on `main`, but you only want that commit.

Instead of merging the whole branch, you execute:

```bash
git cherry-pick <commit-hash>
```

Afterwards, the history becomes:

```text
A ─ B ─ C ─ E'            (main)
     \
      D ─ E ─ F           (feature)
```

Notice that the original commit **E** is still part of `feature`.

Git created a completely new commit **E'** on `main`.

---

# How Cherry-pick Works Internally

Cherry-pick does **not** move commits between branches.

Just like rebasing, Git commits are immutable.

The original commit remains unchanged.

Instead, Git:

1. Reads the changes introduced by the selected commit.
2. Applies those changes to the current branch.
3. Creates a brand new commit.

For example:

Original commit:

```text
Commit E
Parent = D
```

After cherry-picking onto `main`:

```text
Commit E'
Parent = C
```

Since the parent changes, the commit itself changes.

Therefore, Git must create a new commit with a new hash.

> **Cherry-pick recreates a commit on top of the current branch.**

---

# Cherry-pick Copies Changes, Not History

This is one of the most important ideas of this chapter.

Cherry-pick copies **the changes introduced by a commit**.

It does **not** copy:

- the branch,
- previous commits,
- or the history leading to that commit.

Imagine:

```text
feature

D → Create login.c

E → Add authenticate()

F → Fix authentication bug
```

If you execute:

```bash
git cherry-pick E
```

Git does **not** automatically bring commit **D**.

It only tries to apply the changes introduced by **E**.

If those changes depend on files or code created by **D**, Git may not be able to apply them successfully.

This is why cherry-pick works best for **self-contained commits**.

> **Cherry-pick copies the changes of a commit, not its history.**

---

# Handling Cherry-pick Conflicts

Sometimes the selected commit cannot be applied cleanly.

For example:

```text
Applying commit E

✗ Conflict
```

Git pauses the operation.

You resolve the conflict exactly as you would during a merge or a rebase:

1. Edit the conflicting files.
2. Remove the conflict markers.
3. Keep the desired code.
4. Save the files.

Then continue:

```bash
git add .
git cherry-pick --continue
```

If you decide not to continue:

```bash
git cherry-pick --abort
```

Git restores the repository to the state before the cherry-pick began.

---

# When Should You Use Cherry-pick?

Cherry-pick is ideal when you need **one specific change**, not an entire branch.

Typical examples include:

- Applying a bug fix from another branch.
- Copying a small improvement.
- Bringing a single commit into a release branch.
- Recovering a useful commit from another branch.

For example:

```text
A ─ B ─ C                 (main)
     \
      D ─ E ─ F           (feature)
          ↑
     Critical bug fix
```

Instead of merging unfinished work, you simply copy the bug fix:

```bash
git cherry-pick <hash-of-E>
```

---

# Cherry-pick vs Merge

## Merge

A merge integrates the complete history of another branch.

```text
      D ─ E ─ F
     /         \
A ─ B ─ C ───── M
```

Use merge when you want the entire branch.

---

## Cherry-pick

A cherry-pick copies only one commit.

```text
A ─ B ─ C ─ E'
     \
      D ─ E ─ F
```

Use cherry-pick when only one specific commit is needed.

---

# Can Cherry-pick Cause Duplicate History?

Suppose you cherry-pick commit **E** into `main`.

Later, you merge the entire `feature` branch.

Git does **not** consider **E** and **E'** to be the same commit.

They have different hashes because they have different parents.

However, Git compares the contents of both branches during the merge.

If the code introduced by **E** is already present in `main`, Git will often merge successfully without duplicating the code.

Even so, the project history now contains two different commits representing the same logical change.

This can make the history harder to understand and maintain.

For this reason, cherry-pick should be used intentionally, not as a replacement for merging entire branches.

---

# Common Commands

Cherry-pick a commit:

```bash
git cherry-pick <commit-hash>
```

Continue after resolving conflicts:

```bash
git add .
git cherry-pick --continue
```

Abort the operation:

```bash
git cherry-pick --abort
```

---

# Mental Model

Imagine copying one paragraph from another book.

You are not copying the entire chapter.

You are not copying the book's history.

You are only copying that single paragraph and inserting it into your own book.

That is exactly what Git does with a commit during a cherry-pick.

---

# Key Takeaways

- Cherry-pick copies one specific commit.
- The original commit remains in its original branch.
- Git creates a new commit with a new hash.
- Cherry-pick copies changes, not history.
- Previous commits are **not** automatically copied.
- Cherry-pick works best for independent, self-contained commits.
- Conflicts are resolved just like during a merge or rebase.
- Use cherry-pick for isolated changes, not to replace merging an entire branch.
```