# Git Tags

## Introduction

Imagine you have been working on a project for several months.

During that time, you have created hundreds of commits, fixed bugs, implemented new features, and refactored large parts of the codebase.

One day, someone asks you:

> "Can you show me exactly the version that was released as Version 2.0?"

The commit history already contains that version, but unless you remember the exact commit hash, finding it can be difficult.

Git solves this problem with **tags**.

A tag is a permanent name attached to a specific commit. Unlike branches, which move forward as new commits are created, tags remain fixed forever unless you explicitly change or delete them.

For this reason, tags are commonly used to mark important milestones in a project's history, such as software releases, major versions, or significant achievements.

---

## What Is a Tag?

A **tag** is a reference that points to a specific commit.

Just like branches, tags store a reference to a commit object.

However, unlike branches, **they never move automatically**.

Consider the following repository:

```text
A --- B --- C --- D
                  ↑
                main
```

Suppose commit `D` corresponds to the first public release of your application.

Instead of remembering its commit hash, you can create a tag:

```text
                 v1.0
                  ↓
A --- B --- C --- D
                  ↑
                main
```

Now the commit has two references pointing to it:

- `main`
- `v1.0`

Although both point to the same commit, they have completely different purposes.

- The branch (`main`) represents the current line of development.
- The tag (`v1.0`) permanently identifies an important moment in the project's history.

This distinction is fundamental.

Branches are designed to evolve.

Tags are designed to preserve history.

---

## Branches vs Tags

At first glance, branches and tags look almost identical.

Both are references pointing to commits.

The difference appears when new commits are created.

### Branch

Suppose the repository looks like this:

```text
A --- B --- C
            ↑
          main
```

After creating another commit:

```text
A --- B --- C --- D
                 ↑
               main
```

The branch automatically moved to the new commit.

---

### Tag

Now imagine the same repository, but with a tag:

```text
           v1.0
             ↓
A --- B --- C
            ↑
          main
```

After creating another commit on `main`:

```text
           v1.0
             ↓
A --- B --- C --- D
                 ↑
               main
```

Notice what happened.

The branch moved.

The tag stayed exactly where it was.

This is why tags are perfect for identifying releases.

Once a release has been published, its tag should always continue pointing to that exact commit.

---

| Branch | Tag |
|---------|-----|
| Moves when new commits are created | Never moves automatically |
| Represents ongoing development | Represents a fixed point in history |
| Used for active work | Used for releases and milestones |
| Usually changes every day | Usually remains permanent |

---

## Types of Tags

Git provides two different kinds of tags.

- Lightweight tags
- Annotated tags

Although they look similar from the outside, internally they are stored differently.

### Lightweight Tags

A lightweight tag is simply another reference to a commit.

```text
          v1.0
            ↓
A --- B --- C
```

Internally, Git stores nothing except the name and the commit it points to.

There is no additional information attached to it.

Lightweight tags are useful for temporary personal markers or quick references during development.

---

### Annotated Tags

An annotated tag is a separate Git object.

Instead of pointing directly to the commit, the reference points to a **tag object**, which then points to the commit.

```text
v1.0
  │
  ▼
+----------------------+
| Tag Object           |
|----------------------|
| Name: v1.0           |
| Author               |
| Date                 |
| Message              |
+----------------------+
          │
          ▼
A --- B --- C
```

Because the tag object stores metadata, annotated tags can contain:

- Author
- Creation date
- Description
- Cryptographic signature (optional)

For official releases, annotated tags are almost always preferred because they preserve additional information about the release itself.

---

## Creating Tags

The simplest way to create a tag is:

```bash
git tag v1.0
```

Git creates a tag named `v1.0` pointing to the commit currently referenced by `HEAD`.

```text
Before

A --- B --- C
            ↑
          HEAD

After

          v1.0
            ↓
A --- B --- C
            ↑
          HEAD
```

You can also create a tag for an older commit.

```bash
git tag v1.0 <commit-hash>
```

For example:

```text
Before

A --- B --- C --- D
                  ↑
                HEAD
```

Running:

```bash
git tag v1.0 B
```

produces:

```text
          v1.0
            ↓
A --- B --- C --- D
                  ↑
                HEAD
```

Notice that creating a tag **does not modify the repository history**.

It only creates another reference.

---

## Listing Tags

As a project grows, it is common to accumulate multiple tags corresponding to different releases or milestones.

To display every tag in the repository, use:

```bash
git tag
```

Example output:

```text
v1.0
v1.1
v2.0
v2.1
v3.0
```

Unlike `git log`, this command only lists the names of the tags.

If you need more information about a specific tag, Git provides other commands that we will see later in this chapter.

---

## Deleting Tags

Tags are references.

Deleting a tag simply removes that reference.

The commit itself is **not** deleted.

To delete a tag:

```bash
git tag -d v1.0
```

Suppose the repository looks like this:

```text
          v1.0
            ↓
A --- B --- C --- D
                  ↑
                main
```

After deleting the tag:

```text
A --- B --- C --- D
                  ↑
                main
```

Notice that commit `D` still exists.

Only the name `v1.0` disappeared.

This follows one of Git's most important principles:

> References can be created and deleted without affecting the commits they point to.

As long as another reference (such as a branch) still reaches that commit, the commit remains part of the repository.

---

## Creating Annotated Tags

Earlier in this chapter, we introduced the two types of tags supported by Git: lightweight and annotated.

So far, every tag we created has been a lightweight tag.

To create an annotated tag, use:

```bash
git tag -a v1.0 -m "First stable release"
```

The `-a` option tells Git to create an **annotated tag**, while the `-m` option provides the tag message.

Internally, Git creates a new **tag object** containing metadata such as the author, creation date, and message.

This is fundamentally different from a lightweight tag.

```text
Lightweight Tag

v1.0
  │
  ▼
Commit
```

```text
Annotated Tag

v1.0
  │
  ▼
Tag Object
  │
  ▼
Commit
```

Because annotated tags preserve additional information, they are the standard choice for software releases.

---

## Switching to a Tag

Sometimes you want to inspect the project exactly as it was when a release was created.

Since a tag is simply another reference, Git allows you to switch to it.

```bash
git switch v1.0
```

Suppose the repository looks like this:

```text
          v1.0
            ↓
A --- B --- C --- D
                  ↑
                main
                HEAD
```

After switching:

```text
          HEAD
           │
           ▼
          v1.0
            ↓
A --- B --- C --- D
                  ↑
                main
```

At first glance, everything seems normal.

However, something important has changed.

Previously, `HEAD` pointed to the branch `main`.

Now, `HEAD` points directly to the commit identified by the tag.

Git calls this state **Detached HEAD**.

---

## Detached HEAD

Normally, `HEAD` points to a branch.

```text
HEAD
 │
 ▼
main
 │
 ▼
A --- B --- C
```

When you create a new commit, Git moves the branch forward.

```text
HEAD
 │
 ▼
main
 │
 ▼
A --- B --- C --- D
```

When working from a tag, there is no branch involved.

Instead, `HEAD` points directly to a commit.

```text
HEAD
 │
 ▼
A --- B --- C
      ▲
    v1.0
```

If you now create another commit:

```text
HEAD
 │
 ▼
A --- B --- C --- D

      ▲
    v1.0
```

Notice what happened.

- The tag did not move.
- No branch moved.
- Only `HEAD` points to the new commit.

This is why Git calls it a **detached HEAD**.

The new commit exists, but it does not belong to any branch.

If you later switch back to another branch, that commit will no longer be reachable through a branch.

It is **not deleted immediately**, but if no reference is created for it, Git may eventually remove it during garbage collection.

If you decide that you want to keep working from that commit, simply create a branch before leaving the detached HEAD state.

For example:

```bash
git switch -c experiment
```

This creates a new branch pointing to the current commit and immediately switches to it.

---

## Inspecting Tags

Sometimes you need more information than just the tag's name.

Git can display the object referenced by a tag using:

```bash
git show v1.0
```

For a lightweight tag, Git shows the referenced commit.

```text
commit 7f8a9c...
Author: Alice
Date: ...

Initial public release
```

For an annotated tag, Git first displays the tag object and then the commit.

```text
tag v1.0
Tagger: Alice
Date: ...

First stable release

commit 7f8a9c...
Author: Alice
Date: ...

Initial public release
```

This difference reflects the internal structure of each tag type.

A lightweight tag is only a reference.

An annotated tag is a separate Git object containing metadata that points to the commit.

---

## Best Practices

Although Git allows great flexibility, a few conventions are widely followed.

### Use annotated tags for releases

Annotated tags preserve useful metadata such as the author, creation date, and a descriptive message.

For software releases, they are generally preferred over lightweight tags.

---

### Follow a consistent naming convention

Most projects use version numbers such as:

```text
v1.0.0
v1.1.0
v2.0.0
```

A predictable naming scheme makes releases easier to identify.

---

### Treat published tags as permanent

A tag should represent a specific moment in the project's history.

Once a release has been shared with others, avoid moving or reusing its tag.

If a new release is needed, create a new tag instead.

---

> **Note**
>
> Tags are initially created only in your local repository.
> Sharing tags with remote repositories will be covered in the chapters about remotes and `git push`.

---

## Key Takeaways

### Concepts

- A tag is a permanent reference to a specific commit.
- Unlike branches, tags never move automatically.
- Tags are commonly used to identify releases and important milestones.
- Git supports both lightweight and annotated tags.
- Annotated tags create a separate Git object containing metadata.
- Creating or deleting a tag does not modify the repository history.
- Switching to a tag places Git in a **detached HEAD** state.
- Detached HEAD is not an error—it simply means that `HEAD` points directly to a commit instead of a branch.
- Annotated tags are generally recommended for official releases.

---

### Commands

| Command | Purpose |
|---------|---------|
| `git tag` | List all tags in the repository. |
| `git tag <name>` | Create a lightweight tag on the current `HEAD`. |
| `git tag <name> <commit>` | Create a lightweight tag on a specific commit. |
| `git tag -a <name> -m "<message>"` | Create an annotated tag with metadata. |
| `git tag -d <name>` | Delete a local tag. |
| `git switch <tag>` | Switch to the commit referenced by a tag (Detached HEAD). |
| `git switch -c <branch>` | Create a branch from a detached HEAD to preserve future commits. |
| `git show <tag>` | Display detailed information about a tag and the commit it references. |