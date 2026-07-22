# Fetch vs Pull

## Introduction

Once a repository is connected to a remote, the local and remote repositories begin to evolve independently.

Imagine you cloned a repository on Monday. During the week, other developers push new commits to GitHub while you continue working locally.

At some point, your local repository no longer reflects the latest state of the remote repository.

The question becomes:

> **How can you bring those new commits into your local repository?**

Git provides two closely related commands for this:

- `git fetch`
- `git pull`

Although they are often used interchangeably by beginners, they serve different purposes.

Understanding the difference between them is essential for working confidently with remote repositories.

---

# The Problem

Suppose your local repository looks like this:

```text
Local Repository

A ─── B
      ▲
    main
```

Meanwhile, another developer pushes a new commit to GitHub.

The remote repository now looks like:

```text
Remote Repository

A ─── B ─── C
             ▲
           main
```

Your repository does not automatically know that commit **C** exists.

Before you can work with it, Git must first contact the remote repository and download the new information.

---

# Fetch

The purpose of `git fetch` is simple:

> **Download new commits from the remote repository without changing your current branch.**

When you run:

```bash
git fetch
```

Git contacts the remote repository and downloads any commits that your local repository does not yet have.

However, it does **not** move your local branches.

Instead, Git updates a special reference called a **remote-tracking branch**.

For example:

```text
Before fetch

A ─── B ─── C
      ▲     ▲
    main  (remote)
```

After running `git fetch`:

```text
A ─── B ─── C
      ▲     ▲
    main  origin/main
```

Notice what happened:

- The new commits have been downloaded.
- `origin/main` now points to the latest commit.
- Your local branch `main` still points to **B**.
- Your working directory remains exactly the same.

Nothing in your current work has changed.

---

# Remote-Tracking Branches

A remote-tracking branch is Git's local record of a branch that exists in a remote repository.

For example:

```text
origin/main
```

does **not** represent another repository.

It is simply Git remembering:

> "The last time I contacted the remote, its `main` branch pointed here."

Remote-tracking branches are updated automatically by commands such as:

```bash
git fetch
git pull
```

You normally do not modify them yourself.

---

# Why Use Fetch?

Because your working directory remains unchanged, `git fetch` gives you the opportunity to inspect incoming changes before integrating them.

This is one of the main reasons experienced developers often prefer using `fetch` before `pull`.

---

# Inspecting Fetched Changes

After fetching, several commands allow you to inspect the downloaded changes before updating your own branch.

To see which commits are waiting to be integrated:

```bash
git log main..origin/main
```

To compare the code differences:

```bash
git diff main origin/main
```

To visualize the commit history:

```bash
git log --oneline --graph --all
```

Once you have reviewed the incoming changes, you can decide whether or not to integrate them into your current branch.

---

# Pull

While `fetch` only downloads new information, `pull` immediately integrates it into your current branch.

Conceptually, running:

```bash
git pull
```

is equivalent to:

```text
git fetch
+
git merge
```

> **Note:** This is the default behavior. Later, you will learn that `git pull` can also be configured to use **rebase** instead of **merge**.

First, Git downloads any new commits from the remote repository.

Then, it merges those commits into your current branch.

If your branch can simply move forward, Git performs a **fast-forward merge**.

For example:

```text
Before pull

A ─── B ─── C
      ▲     ▲
    main  origin/main
```

After `git pull`:

```text
A ─── B ─── C
             ▲
     main, origin/main
```

Your branch now points to the latest commit, and your working directory has been updated.

---

# What Happens If Both Branches Changed?

Sometimes both your local branch and the remote branch contain new commits.

For example:

```text
        C
       /
A ─── B
       \
        D
```

When you execute:

```bash
git pull
```

Git first downloads the remote commits.

It then attempts to merge the two histories.

Since the histories have diverged, Git creates a **merge commit** if it can combine both sets of changes automatically.

If both versions modify incompatible parts of the same files, Git cannot decide how to combine them.

In that case, Git reports a **merge conflict**, and you must resolve it manually before the merge can be completed.

Merge conflicts will be covered in detail in a later chapter.

---

# Fetch vs Pull

| `git fetch` | `git pull` |
|-------------|------------|
| Downloads new commits | Downloads and integrates new commits |
| Does not modify your current branch | Updates your current branch |
| Leaves the working directory unchanged | May modify the working directory |
| Lets you inspect changes first | Integrates changes immediately |

---

# Key Takeaways

- Local and remote repositories evolve independently.
- `git fetch` downloads new commits without modifying your current branch.
- Remote-tracking branches (such as `origin/main`) store Git's latest knowledge of the remote repository.
- Fetch allows you to inspect incoming changes before integrating them.
- By default, `git pull` performs a fetch followed by a merge.
- Pull immediately updates your current branch and working directory.
- Merge conflicts only occur when Git cannot combine two histories automatically.