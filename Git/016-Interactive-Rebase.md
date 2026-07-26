# Interactive Rebase

## Why Interactive Rebase Exists

As projects evolve, commit history often becomes messy.

You might realize that:
- A commit message is unclear.
- Two commits should have been one.
- One commit should have been split into several smaller commits.
- A temporary debugging commit should never have been committed.
- A commit was made in the wrong order.

None of these problems affect the current state of the project. They only affect its history.

Interactive Rebase is Git's tool for rewriting commit history before it becomes permanent.

Unlike commands that modify files, Interactive Rebase modifies the sequence of commits themselves.

Because commits are immutable objects, Git cannot edit an existing commit. Instead, it creates new commits that represent the desired history.

This is the most important idea of the entire chapter:

> **Interactive Rebase does not edit commits. It recreates them.**

---

## The Core Idea

Suppose the history looks like this:

```text
A───B───C───D
```

Imagine that commit **B** contains a typo in its commit message.

At first glance, it seems that Git could simply change the message.

It cannot.

A commit's contents include:
- The project snapshot
- The commit message
- The author
- The timestamp
- The parent commit

Changing any of this information changes the commit itself.

Since every commit is identified by its hash, modifying any part of a commit produces a completely new commit.

Instead of changing **B**, Git creates a new commit:

```text
A───B'
```

However, commit **C** originally pointed to **B**.

Now it must point to **B'** instead.

Since its parent changed, **C** must also be recreated.

The same happens with **D**.

The final history becomes:

```text
A───B'───C'───D'
```

Notice that only one commit was intentionally modified, but every descendant had to be recreated as well.

This leads to another useful way of thinking about Interactive Rebase:

> **Interactive Rebase rebuilds history by replaying commits one by one onto a new history.**

---

## Starting an Interactive Rebase

Interactive Rebase is started with:

```bash
git rebase -i HEAD~n
```

where **n** is the number of recent commits to include in the operation.

For example:

```bash
git rebase -i HEAD~4
```

opens an editor containing something similar to:

```text
pick B
pick C
pick D
pick E
```

Many beginners think that `pick` means "select this commit."

It does not.

Git has already selected the commits.

This file is actually a reconstruction plan.

Each line tells Git what to do while rebuilding the new history.

By default, every line is `pick`, which means:

> Recreate this commit exactly as it is.

Changing these actions changes how the new history will be constructed.

---

## Common Interactive Rebase Actions

The most frequently used actions are:

| Action | Purpose |
|---------|----------|
| `pick` | Recreate the commit unchanged. |
| `reword` | Change only the commit message. |
| `edit` | Pause to manually modify the commit. |
| `squash` | Combine this commit with the previous one and edit the final message. |
| `fixup` | Combine this commit with the previous one while discarding its message. |
| `drop` | Remove the commit completely. |

The following sections explain each action individually.

## `reword`

The `reword` action changes only the commit message.

Suppose the history is:

```text
A───B───C───D
```

and commit **C** has an incorrect message.

The rebase plan becomes:

```text
pick B
reword C
pick D
```

When Git reaches **C**, it opens your editor so you can write a new commit message.

Although only the message changes, Git must still create a new commit because commit messages are part of the commit object.

The resulting history becomes:

```text
A───B───C'───D'
```

Notice that **D** is also recreated because its parent changed.

---

## `drop`

The `drop` action removes a commit completely.

Suppose the history is:

```text
A───B───C───D
```

If **C** should never have existed, the rebase plan becomes:

```text
pick B
drop C
pick D
```

The resulting history is:

```text
A───B───D'
```

Commit **D** is recreated because its parent has changed.

---

## `edit`

The `edit` action tells Git to pause while rebuilding history.

This allows you to manually modify a commit before continuing.

For example:

```text
pick B
edit C
pick D
```

Git recreates **B**, then stops before recreating **C**.

At this point you are free to modify the project.

If you simply want to change the contents of the current commit, the typical workflow is:

```bash
git add .
git commit --amend
git rebase --continue
```

`git commit --amend` replaces the current commit with a new version, while `git rebase --continue` tells Git to continue rebuilding the remaining history.

Use `edit` whenever you need to modify the contents of a commit manually. Unlike `reword`, it gives you full control over the commit before the rebase continues.

---

## `squash` and `fixup`

Sometimes several commits represent a single logical change.

Suppose the history is:

```text
A───B───C───D
```

where commits **B**, **C**, and **D** all belong to the same feature.

The rebase plan could be:

```text
pick B
squash C
squash D
```

Git combines the three commits into one:

```text
A───X
```

where **X** contains the changes from **B**, **C**, and **D**.

The difference between `squash` and `fixup` is only how commit messages are handled.

### squash

- Combines the commits.
- Opens an editor so you can write the final commit message.

### fixup

- Combines the commits.
- Automatically discards the message of the merged commit.
- Keeps the previous commit's message.

The project contents are identical in both cases.

Only the final commit message differs.

## Splitting a Commit

Sometimes one commit contains multiple unrelated changes.

For example:

```text
A───B───C───D
```

where commit **C** contains both:

- A login system.
- A registration system.

These should ideally be separate commits.

To split a commit, first mark it with `edit`:

```text
pick B
edit C
pick D
```

When Git pauses, remove the current commit while keeping all of its changes:

```bash
git reset HEAD^
```

This removes commit **C**, but leaves all of its modifications in the Working Directory.

You can now create several smaller commits:

```bash
git add <first-part>
git commit -m "Add login system"

git add <second-part>
git commit -m "Add registration system"
```

Finally, continue the rebase:

```bash
git rebase --continue
```

The history becomes:

```text
A───B───C1───C2───D'
```

Notice that splitting a commit does not use `git commit --amend`.

Instead, the original commit is removed and replaced with multiple new commits.

---

## Never Rewrite Public History

Interactive Rebase is extremely useful while commits are still local.

However, once commits have been pushed and other people may have based work on them, rewriting history becomes dangerous.

Suppose two developers share this history:

```text
A───B───C───D
```

If one developer rewrites history, the result might become:

```text
A───B'───C'───D'
```

Although the project contents may be identical, these are completely different commits with different hashes.

Anyone still working from the original history will now have incompatible commit histories, making future merges much more difficult.

As a general rule:

> **Never rewrite history that other developers may already be using.**

---

## Useful Rebase Commands

During an Interactive Rebase, these commands are commonly used:

| Command | Purpose |
|---------|----------|
| `git rebase --continue` | Continue rebuilding history after resolving a stop or conflict. |
| `git rebase --abort` | Cancel the rebase and restore the repository to its original state. |
| `git rebase --skip` | Skip the current commit and continue with the next one. |

---

## Key Takeaways

- Interactive Rebase rewrites commit history.
- Commits are immutable, so Git recreates commits instead of editing them.
- Changing one commit usually recreates all of its descendants.
- `pick` recreates a commit unchanged.
- `reword` changes only the commit message.
- `edit` pauses so you can manually modify a commit.
- `squash` and `fixup` combine commits.
- `drop` removes a commit.
- Splitting a commit is done by removing it while preserving its changes, then creating several smaller commits.
- Never rewrite public history.