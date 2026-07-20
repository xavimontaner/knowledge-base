# Git 005 - Stashing

While working on a project, it is common to interrupt one task to work on another.

You may need to fix an urgent bug, switch to another branch, or simply postpone your current work until later.

However, your changes may not be ready to become part of the project's history.

Git provides a feature called **Stash** to temporarily save your work without creating a commit.

---

# Why Stash Exists

Imagine you are implementing a new feature.

After modifying several files, a critical bug is reported and must be fixed immediately.

You need to switch to another branch.

Unfortunately, Git may refuse to switch branches because doing so could overwrite your uncommitted changes.

Creating a commit is not always the right solution.

If your work is incomplete, committing it would add unnecessary or misleading commits to the project's history.

Git Stash solves this problem.

It allows you to temporarily save your current work, return the working directory to a clean state, and continue exactly where you left off later.

---

# What Is a Stash?

A stash is a temporary storage area managed by Git.

When you create a stash, Git saves your current uncommitted changes outside the commit history.

Afterwards, your working directory is restored to the state of the latest commit.

For example:

```text
Working Directory
        │
        │ git stash
        ▼
+------------------+
|      Stash       |
|------------------|
| stash@{0}        |
+------------------+
```

Unlike commits, stashes are **not part of the project's history**.

They are intended only for temporary interruptions.

---

# Stashes and Branches

A stash belongs to the repository, not to a branch.

Although Git remembers the branch from which the stash was created, the stash itself can be applied from any branch.

For example, a stash created on `feature` can later be applied while working on `main`.

If the changes are compatible, Git applies them automatically.

Otherwise, merge conflicts may occur and must be resolved manually.

---

# Creating a Stash

The simplest way to save your current work is:

```bash
git stash
```

This command:

- saves your tracked modifications,
- saves staged changes,
- restores the working directory to the latest commit.

It does **not** include untracked files.

If you also want to save newly created files, use:

```bash
git stash -u
```

or equivalently:

```bash
git stash --include-untracked
```

After creating the stash, the project appears exactly as it did at the latest commit.

---

# Naming Stashes

By default, Git automatically generates a description for every stash.

However, when several stashes exist, these automatic names can become difficult to identify.

A custom message can be provided:

```bash
git stash push -m "Implement login page"
```

This creates a stash with a descriptive name, making it easier to locate later.

---

# Listing Stashes

Every stored stash can be displayed with:

```bash
git stash list
```

Example:

```text
stash@{0}: On main: Fix navbar
stash@{1}: On feature: Update README
```

The newest stash is always `stash@{0}`.

Older stashes receive increasing indices.

+------------------------------+
|           Stash              |
|------------------------------|
| stash@{0}  Fix navbar        |
| stash@{1}  Login page        |
| stash@{2}  README update     |
+------------------------------+
          ▲
          │
 Most recently created stash

New stashes are always added to the top of the stack. Existing stashes move down one position.

---

# Restoring a Stash

To restore the most recent stash while keeping it stored:

```bash
git stash apply
```

A specific stash can also be restored:

```bash
git stash apply stash@{1}
```

If the current project state is compatible with the stored changes, Git restores them automatically.

Otherwise, merge conflicts may need to be resolved manually.

---

# Restoring and Removing a Stash

In many situations, a stash is only needed once.

Instead of restoring it and deleting it separately, Git provides:

```bash
git stash pop
```

This command:

1. Applies the stash.
2. Removes it from the stash list if the operation succeeds.

Before:

stash@{0}
stash@{1}
stash@{2}

git stash pop

After:

stash@{0}   ← previously stash@{1}
stash@{1}   ← previously stash@{2}

However, if Git cannot safely apply the stash, it keeps it to prevent accidental data loss.

---

# Deleting Stashes

To delete a single stash:

```bash
git stash drop stash@{1}
```

To remove every stored stash:

```bash
git stash clear
```

Since stashes may contain valuable unfinished work, `git stash clear` should be used carefully.

---

# When Should You Use Stash?

A stash is designed for **temporary interruptions**.

Typical situations include:

- Switching to another branch.
- Fixing an urgent bug.
- Pulling remote changes before continuing.
- Temporarily saving unfinished work.

If your work already represents a meaningful unit of progress, creating a commit is usually a better choice.

A useful rule of thumb is:

> **Stash is for interruptions. Commits are for progress.**

---

# Key Takeaways

- A stash temporarily stores uncommitted changes.
- Stashes are not part of the commit history.
- A stash belongs to the repository, not to a branch.
- `git stash` saves tracked changes.
- `git stash -u` also saves untracked files.
- `git stash list` displays every stored stash.
- `git stash apply` restores a stash without removing it.
- `git stash pop` restores and removes a stash.
- `git stash drop` deletes a specific stash.
- `git stash clear` removes every stored stash.
- Stashes are intended for temporary interruptions, not long-term progress.