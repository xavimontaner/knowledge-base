# Git 004 - Branches & Merging

Branches are one of Git's most powerful features.

They allow developers to work on independent lines of development without affecting the stable version of a project.

Understanding branches is essential because almost every collaborative Git workflow relies on them.

---

# Why Branches Exist

Imagine that the current version of a project works perfectly.

You now want to implement a new feature that may take several days.

During development, the project may temporarily stop working.

If you develop directly on `main`, the stable version will also become unstable.

A branch solves this problem.

Instead of modifying the stable version, Git allows you to create an independent line of development where you can experiment freely.

Meanwhile, the stable branch remains unchanged.

---

# Understanding Branches

At first, it is natural to imagine a branch as a complete copy of a project.

However, Git does not work this way.

Suppose the project currently has this history:

```text
A --- B --- C
            ^
           main
```

If a new branch is created, Git does **not** duplicate every file.

Instead, both branches initially point to the same commit.

```text
A --- B --- C
            ^
      main  feature
```

At this moment:

- there is only one history,
- the commits are shared,
- nothing has been copied.

The project still occupies practically the same amount of storage.

---

# Why Doesn't Git Copy the Entire Project?

Imagine a repository that occupies 20 GB.

If every branch were a complete copy, creating ten branches would require around 200 GB of storage.

Creating branches would become increasingly slow, consume enormous amounts of memory, and synchronizing different copies would be extremely difficult.

Git avoids this problem completely.

Instead of copying the project, Git stores a single history of commits.

Branches are simply references to commits inside that history.

This design makes branches extremely lightweight.

Creating a branch usually takes only a fraction of a second regardless of the size of the repository.

---

# What Is a Branch?

A branch is a movable pointer to the latest commit of a line of development.

For example:

```text
A --- B --- C
            ^
           main
```

Here, `main` points to commit `C`.

Creating another branch simply creates another pointer.

```text
A --- B --- C
            ^
      main  feature
```

Notice that no commits have changed.

No files have been duplicated.

Only a new pointer has been created.

---

# Independent Lines of Development

Suppose we start working on `feature`.

A new commit is created.

```text
A --- B --- C
            ^
           main
             \
              D
              ^
           feature
```

Only `feature` moves.

`main` continues pointing to commit `C`.

If another commit is created:

```text
A --- B --- C
            ^
           main
             \
              D --- E
                    ^
                 feature
```

Again, only `feature` moves.

The existing commits never move.

The branch pointer moves to the newest commit.

This is why a branch is described as a movable pointer.

---

# HEAD

Git always needs to know which branch is currently active.

This information is stored in `HEAD`.

For example:

```text
HEAD
 |
 v
feature
 |
 v
E
```

This means that any new commit will be added to the `feature` branch.

If another commit is created:

```text
HEAD
 |
 v
feature
 |
 v
F
```

`feature` moves.

`main` remains exactly where it was.

---

# Creating Branches

A new branch is created with:

```bash
git branch feature
```

This command only creates the branch.

It does **not** switch to it.

The available branches can be listed with:

```bash
git branch
```

Example:

```text
* main
  feature
```

The asterisk indicates the current branch.

---

# Switching Branches

To start working on another branch:

```bash
git switch feature
```

This changes the current branch.

However, something even more interesting happens.

Git automatically updates the working directory.

Suppose the file `branch-test.txt` only exists in `feature`.

```text
A --- B --- C
            ^
           main
             \
              D
              ^
           feature
```

If we execute:

```bash
git switch main
```

the file disappears from the working directory because it does not exist in commit `C`.

Switching back:

```bash
git switch feature
```

makes the file appear again.

The file was never deleted.

Git simply reconstructed the working directory so that it matches the selected commit.

Changing branches is therefore similar to moving between different versions of the project.

---

# Visualising Branches

A useful command for understanding branch history is:

```bash
git log --oneline --graph --decorate --all
```

Example:

```text
* a953009 (HEAD -> feature) Add branch test
* d93b99a (main) Add Git 003 notes
```

This tells us that:

- `HEAD` points to `feature`.
- `feature` points to commit `a953009`.
- `main` still points to the previous commit.

Both branches still share the earlier history.

---

# Merging

Eventually, the work done on a feature branch should become part of the stable project.

This process is called merging.

A merge incorporates the history of one branch into another.

The command is:

```bash
git merge feature
```

An important rule is:

> The current branch always receives the changes.

For example:

```bash
git switch main
git merge feature
```

means:

> Merge the work from `feature` into `main`.

The opposite would require switching to `feature` first.

---

# Fast-Forward Merge

Suppose the history is:

```text
A --- B --- C
            ^
           main
             \
              D
              ^
           feature
```

Since `main` has not changed, Git can simply move the `main` pointer forward.

```text
A --- B --- C --- D
                  ^
           main  feature
```

This is called a fast-forward merge.

No new commit is created.

Git simply moves the branch pointer.

---

# True Merge

Sometimes both branches continue developing independently.

```text
          D --- E
         /
A --- B --- C
         \
          F --- G
```

Now neither branch can simply move forward because both contain unique commits.

Git creates a new commit that combines both histories.

```text
          D --- E
         /       \
A --- B --- C     H
         \       /
          F --- G
```

This is called a merge commit.

Unlike a normal commit, it has two parents.

One parent belongs to the previous state of `main`.

The other belongs to the latest commit of `feature`.

This allows Git to reconstruct how both lines of development were combined.

---

# Multiple Branches

A project may contain many branches at the same time.

```text
                 D --- E  <- feature-chat
                /
A --- B --- C  <- main
                \
                 F --- G  <- feature-dark-mode
```

Each branch develops independently.

When one feature is completed, it can be merged into `main` without affecting the remaining branches.

This model allows multiple developers to work simultaneously on different features.

---

# Merge Conflicts

Git can automatically merge changes when they modify different parts of the project.

However, if two branches modify the same lines differently, Git cannot determine which version is correct.

The merge stops and asks the developer to resolve the conflict manually.

A merge conflict is not a Git error.

It simply means that only the developer knows which final version is correct.

Merge conflicts will be studied in the next lesson.

---

# Key Takeaways

- Branches allow independent lines of development.
- A branch is a movable pointer to the latest commit of a line of development.
- Branches are not copies of a project.
- Multiple branches share the same commit history until they diverge.
- `HEAD` identifies the current branch.
- `git branch` creates a new branch.
- `git switch` changes the active branch and updates the working directory.
- `git merge` incorporates one branch into another.
- The current branch always receives the changes.
- A fast-forward merge only moves a branch pointer.
- A true merge creates a merge commit with two parents.
- Merge conflicts occur when Git cannot automatically combine incompatible changes.