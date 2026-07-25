# Git Reset: Soft, Mixed, and Hard

`git reset` is one of Git's most powerful commands. It moves the current branch to another commit and, depending on the selected mode, may also update the staging area and the working directory.

Understanding `git reset` is much easier once you understand Git's three main states.

---

## Git's Three States

Every change in Git exists in one of three places.

```text
Working Directory
        |
        | git add
        v
Staging Area
        |
        | git commit
        v
Repository History (HEAD)
```

### Working Directory

The working directory contains the files currently visible on your computer.

When you edit a file, the change exists only here.

### Staging Area

The staging area (also called the *index*) contains the exact snapshot that Git will use for the next commit.

Running:

```bash
git add <file>
```

copies the current version of a file from the working directory into the staging area.

### Repository History

The repository history contains every commit that has already been created.

`HEAD` normally refers to the commit currently checked out through the active branch.

For example:

```text
A───B───C
        ^
    HEAD, main
```

Both `HEAD` and the branch `main` point to commit `C`.

---

## What Does `git reset` Do?

Suppose the repository contains:

```text
A───B───C
        ^
    HEAD, main
```

If we execute:

```bash
git reset HEAD~1
```

Git moves the current branch back one commit.

```text
A───B
    ^
HEAD, main

Commit C is no longer referenced by the branch.
```

The important question is:

**What happens to the staging area and the working directory?**

The answer depends on the reset mode.

---

## The Three Reset Modes

The only difference between the reset modes is **how many Git states are updated**.

```text
--soft
    ↓
HEAD

--mixed
    ↓
HEAD + Staging Area

--hard
    ↓
HEAD + Staging Area + Working Directory
```

Each mode updates one additional Git state.

---

# Soft Reset

A soft reset moves **only** the current branch (`HEAD`).

The staging area and the working directory remain unchanged.

```bash
git reset --soft HEAD~1
```

Suppose the repository initially contains:

```text
A───B───C
        ^
    HEAD, main
```

After the reset:

```text
A───B
    ^
HEAD, main
```

The branch has moved back to `B`, but both the staging area and the working directory still contain the changes introduced by commit `C`.

Conceptually:

```text
HEAD              → B
Staging Area      → content of C
Working Directory → content of C
```

Git therefore reports:

```text
Changes to be committed
```

because the changes are still staged.

## Typical Use Case

Soft reset is useful when a commit should be recreated without losing its contents.

For example:

```bash
git reset --soft HEAD~1
git commit -m "Better commit message"
```

The new commit contains exactly the same changes as before, but it receives a new commit hash and a different message.

It is also useful when you want to add a few more changes before recreating the commit.

---

# Mixed Reset

A mixed reset moves the current branch **and** resets the staging area.

The working directory is left untouched.

```bash
git reset --mixed HEAD~1
```

Since `--mixed` is the default mode, the following command is equivalent:

```bash
git reset HEAD~1
```

After the reset:

```text
HEAD              → B
Staging Area      → content of B
Working Directory → content of C
```

The files still contain every modification introduced by commit `C`, but those changes are no longer staged.

Git therefore reports:

```text
Changes not staged for commit
```

## Typical Use Case

Mixed reset is useful when a commit was created too early and you want to reorganize its changes.

Suppose commit `C` modified:

```text
main.py
config.py
README.md
```

After:

```bash
git reset HEAD~1
```

all three files remain modified in the working directory.

You can now choose exactly what belongs in the next commit.

For example:

```bash
git add main.py
git commit -m "Update main application logic"
```

Only `main.py` is included in the new commit.

The remaining files continue as unstaged changes until you decide what to do with them.

Mixed reset can therefore be understood as:

> **Undo the commit, keep all the work, and let me choose again what to commit.**

---

# Hard Reset

A hard reset moves the current branch, resets the staging area, **and** updates the working directory.

```bash
git reset --hard HEAD~1
```

After the reset:

```text
HEAD              → B
Staging Area      → content of B
Working Directory → content of B
```

Unlike the previous reset modes, Git also replaces the files on your computer with the versions stored in the target commit.

As a result, the changes introduced by commit `C` disappear from both the staging area and the working directory.

## Typical Use Case

Hard reset is useful when you want to completely discard local work and return to a previous commit.

For example:

```bash
git reset --hard HEAD~1
```

restores the repository exactly as it was in commit `B`.

Because it modifies the working directory, this command should be used with care.

Any changes that exist only in your working directory and have never been committed may be permanently lost.

A useful way to remember the three modes is:

```text
git reset --soft
Move HEAD
Keep staging
Keep files

git reset --mixed
Move HEAD
Reset staging
Keep files

git reset --hard
Move HEAD
Reset staging
Reset files
```

---

# Comparing the Reset Modes

| Mode | Moves HEAD | Updates Staging Area | Updates Working Directory | Result |
|------|:----------:|:--------------------:|:-------------------------:|--------|
| `--soft` | ✓ | ✗ | ✗ | Changes remain staged |
| `--mixed` | ✓ | ✓ | ✗ | Changes remain unstaged |
| `--hard` | ✓ | ✓ | ✓ | Working directory is restored to the target commit |

The difference between the three modes is simply how many Git states are updated.

---

# Resetting to Older Commits

So far we have used:

```bash
git reset HEAD~1
```

which moves back one commit.

You can also move back multiple commits:

```bash
git reset HEAD~2
```

or reset directly to a specific commit:

```bash
git reset --hard <commit-hash>
```

Regardless of the destination, the selected reset mode behaves exactly the same.

---

# Reset Moves the Branch

A very important detail is that **`git reset` moves the current branch**.

Starting from:

```text
A───B───C
        ^
    HEAD, main
```

After:

```bash
git reset --soft HEAD~1
```

the result becomes:

```text
A───B
    ^
HEAD, main
```

Both `HEAD` and `main` now point to commit `B`.

This is different from checking out an old commit.

For example:

```bash
git switch --detach <commit>
```

moves only `HEAD`.

The branch itself remains where it was.

This creates the **detached HEAD** state, which is covered separately.

---

# Reset and Shared History

Because reset moves the current branch, it rewrites the visible history of that branch.

If the removed commits have already been pushed and other developers are using them, resetting the branch can create conflicts between local and remote history.

For this reason, `git reset` should normally be used only for commits that have not yet been shared.

For published history, creating a new commit that reverses previous changes is usually the safer approach.

---

# Recovering a Reset Commit

Moving a branch away from a commit does **not** usually delete that commit immediately.

Although the branch no longer references it, Git normally records the previous position in the reflog.

For example:

```bash
git reflog
```

may show something similar to:

```text
abc1234 HEAD@{0}: reset: moving to HEAD~1
def5678 HEAD@{1}: commit: Add authentication
```

If the commit still appears in the reflog, it can usually be restored:

```bash
git reset --hard def5678
```

However, reflog can only recover work that Git has already recorded.

Changes that existed only in the working directory and were removed by `git reset --hard` may not be recoverable.

Reset does not immediately delete commits; it only removes the branch reference to them.

---

# Choosing the Correct Reset Mode

Use **`--soft`** when:

```text
The commit should disappear,
but its changes should remain staged.
```

Use **`--mixed`** when:

```text
The commit should disappear,
but its changes should remain in your files.
```

Use **`--hard`** when:

```text
The repository should return completely
to an earlier state.
```

Before running a reset, ask yourself:

```text
Do I want to keep the staged changes?

Do I want to keep the files?

Has this commit already been shared?
```

The answers determine which reset mode is appropriate.

---

# Key Takeaways

- `git reset` moves the current branch to another commit.
- The three reset modes differ only in how many Git states they update.
- `--soft` moves only `HEAD`.
- `--mixed` moves `HEAD` and resets the staging area.
- `--hard` moves `HEAD`, resets the staging area, and restores the working directory.
- `--mixed` is the default reset mode.
- `git reset` moves the current branch, unlike detached `HEAD`.
- Reset should normally be used only on private or unpublished history.
- Commits removed from a branch can often be recovered using the reflog.
- Uncommitted changes removed by `git reset --hard` may be permanently lost.