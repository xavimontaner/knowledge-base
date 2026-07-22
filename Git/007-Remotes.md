# 007 - Remotes

## Introduction

So far, every repository we have worked with has been completely local. All commits, branches, and tags existed only on our own computer.

However, software development rarely happens in isolation. We often need to collaborate with other developers, synchronize work between multiple computers, or store our projects online.

To make this possible, Git allows repositories to communicate with one another through **remotes**.

A remote is not another copy of your project—it is simply a named connection to another Git repository.

---

# Why Remotes Exist

Imagine you have a repository on your laptop.

```text
Laptop

Repository
```

Everything works perfectly until another copy of the repository exists somewhere else.

For example:

```text
Laptop Repository

        │
        │
        ▼

GitHub Repository
```

Now there are two independent repositories.

Git needs a way to remember **where the other repository is located** so they can exchange information when necessary.

That is the purpose of a remote.

---

# What Is a Remote?

A **remote** is a named connection to another Git repository.

It stores the information required to locate another repository, typically its URL.

A remote does **not** contain:

- commits
- branches
- tags
- files

Instead, it stores something conceptually similar to:

```text
Name: origin

URL:
https://github.com/user/project.git
```

Think of it like a contact stored in your phone.

The contact is **not** the person.

It simply contains the information required to reach that person.

A remote works exactly the same way.

---

# The Special Case of `origin`

Many beginners believe that `origin` means "the main repository."

It does not.

`origin` is simply the default name that Git uses when a repository is cloned.

It could just as easily be called:

```text
github
server
backup
office
laptop
```

Git does not treat `origin` differently from any other name.

It is simply a convention used by almost every project.

---

# How `git clone` Creates a Remote

Consider these two situations.

### Creating a repository from scratch

```bash
git init
```

Result:

```text
Repository

Remotes
--------
(none)
```

No remote exists because Git has no idea where another repository might be.

### Cloning an existing repository

```bash
git clone https://github.com/user/project.git
```

Besides downloading the repository, Git also configures a remote automatically.

> **Note:** The following is a conceptual simplification to help understand what Git does. Internally, Git performs additional operations.

Conceptually, it is similar to performing:

```bash
git init

git remote add origin https://github.com/user/project.git
```

As a result, immediately after cloning:

```bash
git remote
```

returns

```text
origin
```

even though you never created it yourself.

---

# Listing Remotes

To display every remote configured in the current repository:

```bash
git remote
```

Example:

```text
origin
backup
office
```

Notice that only the **names** are displayed.

The command does **not** show where those remotes point.

---

# Viewing Remote URLs

To display additional information:

```bash
git remote -v
```

Example:

```text
origin  https://github.com/user/project.git (fetch)
origin  https://github.com/user/project.git (push)
```

The `-v` (**verbose**) option displays the URL associated with each remote.

The `(fetch)` and `(push)` labels indicate which URL Git will use when fetching or pushing. We will study these operations in detail in the following chapters.

---

# Adding a Remote

To create a new remote:

```bash
git remote add <name> <url>
```

Example:

```bash
git remote add origin https://github.com/user/project.git
```

This creates a remote named `origin` that points to the specified repository.

Afterwards:

```bash
git remote
```

returns

```text
origin
```

---

# Renaming a Remote

A remote's name can be changed without modifying its URL.

```bash
git remote rename <old-name> <new-name>
```

Example:

```bash
git remote rename origin github
```

Before:

```text
origin → https://github.com/user/project.git
```

After:

```text
github → https://github.com/user/project.git
```

Only the name changes.

The remote still points to exactly the same repository.

---

# Removing a Remote

To remove a configured remote:

```bash
git remote remove <name>
```

Example:

```bash
git remote remove origin
```

This **does not**:

- delete your local repository
- delete your commits
- delete the GitHub repository
- delete any files

It simply removes the stored connection.

Afterwards:

```bash
git remote
```

returns nothing if no remotes remain.

---

# Best Practices

- Use meaningful remote names when working with multiple repositories.
- Keep using `origin` unless you have a good reason to choose another name.
- Remember that removing a remote only removes the connection—it never deletes the remote repository itself.
- Think of a remote as a saved contact rather than as another copy of your project.

---

# Key Takeaways

## Concepts

- A remote is a **named connection** to another Git repository.
- A remote stores a **name** and a **URL**.
- A remote does **not** contain commits, branches, tags, or files.
- `origin` is simply Git's default remote name after `git clone`.
- `git init` creates no remotes.
- `git clone` automatically creates a remote named `origin`.
- Multiple remotes can exist in the same repository.
- Removing a remote only removes the stored connection.

## Commands

| Command | Purpose |
|---------|---------|
| `git remote` | List all configured remotes. |
| `git remote -v` | Display remotes together with their URLs. |
| `git remote add <name> <url>` | Create a new remote. |
| `git remote rename <old> <new>` | Rename an existing remote. |
| `git remote remove <name>` | Remove a configured remote. |