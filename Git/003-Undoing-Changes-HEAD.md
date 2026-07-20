# Git 003 - Undoing Changes & HEAD

## Working Directory

The Working Directory contains the files you are currently editing.

Changes made here are not part of Git's history until they are staged and committed.

---

## Staging Area (Index)

The Staging Area stores the exact version of the files that will be included in the next commit.

Files are added to the Staging Area using:

```bash
git add <file>
```

---

## Repository

The Repository contains every commit in the project's history.

Each commit is a permanent snapshot of the project.

---

## The Three Areas of Git

```text
Repository
     ▲
     │ git commit
     │
Staging Area
     ▲
     │ git add
     │
Working Directory
```

Git commands move information between these three areas.

---

## git restore

Restores the Working Directory to match the current Staging Area.

If a file has not been staged, it is restored from the current commit.

Example:

```bash
git restore main.py
```

Use this command to discard changes that you no longer want.

---

## git restore --staged

Removes a file from the Staging Area without changing the Working Directory.

Example:

```bash
git restore --staged main.py
```

The file remains modified, but it is no longer prepared for the next commit.

---

## HEAD

HEAD is a pointer that represents the current position in the repository.

Normally, HEAD points to the current branch, which in turn points to the latest commit.

Example:

```text
HEAD
 │
 ▼
main
 │
 ▼
A ─── B ─── C
```

When a new commit is created, the current branch moves to the new commit, and HEAD follows it automatically.

---

## Key Takeaways

- Git works with three areas:
  - Working Directory
  - Staging Area
  - Repository
- `git add` copies changes from the Working Directory to the Staging Area.
- `git commit` creates a new snapshot in the Repository.
- `git restore` discards changes in the Working Directory.
- `git restore --staged` removes changes from the Staging Area without deleting your work.
- `git diff --staged` displays what is staged but not yet committed.
- HEAD indicates where you are currently working in the repository.