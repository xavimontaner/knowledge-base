# 009 - Push

## Introduction

In the previous chapter, we learned how Git can communicate with remote repositories through **remotes**.

However, connecting a remote repository is not enough. At some point, we need to **share our local work** with that remote repository so other developers can access it, or simply to back it up online.

This is the purpose of **git push**.

The most important idea to remember is:

> **A commit is private until it is pushed.**

---

## Local and Remote Repositories

Suppose we create a new commit.

```text
Local Repository

A ─── B ─── C
             ↑
           main
```

At this moment, commit **C** exists **only** in the local repository.

The remote repository is still behind.

```text
Remote Repository

A ─── B
       ↑
 origin/main
```

Running

```bash
git push
```

copies the missing commits to the remote repository.

```text
Local Repository

A ─── B ─── C
             ↑
           main
```

```text
Remote Repository

A ─── B ─── C
             ↑
        origin/main
```

Notice that nothing disappears from the local repository.

The commits continue existing locally while Git copies them to the remote repository.

---

## What Does `git push` Actually Send?

Many beginners think that Git uploads project files.

This is not how Git works.

Git transfers **Git objects**, mainly the commits that the remote repository does not already have.

Suppose the local repository contains

```text
A ─── B ─── C ─── D
                 ↑
               main
```

while the remote repository contains

```text
A ─── B
       ↑
 origin/main
```

Running

```bash
git push
```

does **not** upload every file in the project again.

Instead, Git sends only the objects required for the remote repository to obtain commits **C** and **D**.

Because Git never retransmits objects that already exist remotely, pushes are usually very efficient.

---

## Updating the Remote Branch

When a push succeeds, the local history does not change.

Only the remote branch advances.

Before pushing:

```text
Local

A ─── B ─── C
             ↑
           main
```

```text
Remote

A ─── B
       ↑
 origin/main
```

After

```bash
git push
```

the remote branch is updated.

```text
Local

A ─── B ─── C
             ↑
           main
```

```text
Remote

A ─── B ─── C
             ↑
        origin/main
```

---

## Why Can a Push Be Rejected?

Imagine another developer pushes new commits before you do.

Your repository contains

```text
A ─── B ─── C
             ↑
           main
```

while the remote repository now contains

```text
A ─── B ─── D
             ↑
        origin/main
```

If you execute

```bash
git push
```

Git rejects the operation.

Why?

Because accepting your push would overwrite the remote history and remove commit **D**.

Git protects the repository by refusing to overwrite commits that already exist remotely.

To continue, you must first integrate the remote changes (using `git fetch` or `git pull`), and then push again.

Notice that the push itself is **not** creating a merge conflict.

The push is rejected **before** the remote history is modified.

---

## "Everything up-to-date"

Sometimes Git prints

```text
Everything up-to-date
```

This simply means that the remote repository already contains every commit from your current branch.

There is nothing new to send.

---

## The First Push

Suppose you have just created a new repository.

You initialize Git, create some commits and connect a remote repository.

```bash
git init
git remote add origin <repository-url>
```

If you now execute

```bash
git push
```

Git does not know where your local branch should be pushed.

You must specify both the remote repository and the destination branch.

```bash
git push -u origin main
```

This command performs two actions:

1. It pushes the current commits to `origin/main`.
2. It configures the local branch `main` to track `origin/main`.

---

## What Is an Upstream Branch?

An upstream branch is **not** a special type of branch.

It is simply a relationship stored by Git.

```text
main
 │
 │ tracks
 ▼
origin/main
```

This relationship tells Git:

> "Whenever I am on `main`, my default remote branch is `origin/main`."

Once this relationship has been configured, Git already knows where future pushes and pulls should go.

Instead of writing

```bash
git push origin main
```

every time, we can simply write

```bash
git push
```

Likewise,

```bash
git pull
```

automatically retrieves changes from `origin/main`.

The `-u` option only creates this tracking relationship.

It does **not** change the commits being pushed.

---

## Summary

- A commit exists only in the local repository until it is pushed.
- `git push` publishes local commits to a remote repository.
- Git transfers Git objects (commits), not project files.
- Only commits missing from the remote repository are transmitted.
- A successful push advances the remote branch without modifying the local history.
- Git rejects pushes that would overwrite remote commits.
- `"Everything up-to-date"` means there is nothing new to send.
- `git push -u origin main` performs the first push and configures the upstream relationship.
- Once an upstream branch exists, `git push` and `git pull` automatically know their default destination.