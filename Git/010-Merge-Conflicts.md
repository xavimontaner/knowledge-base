# 010 - Merge Conflicts

## Introduction

One of Git's greatest strengths is its ability to combine changes coming from different branches automatically.

Most of the time, merging happens without requiring any user intervention.

However, there are situations where Git cannot determine how two changes should be combined.

When this happens, Git creates a **merge conflict** and asks the developer to resolve it manually.

The important idea to remember is:

> **A merge conflict does not mean Git failed. It means Git found two incompatible changes and cannot safely choose one over the other.**

---

## How Git Performs a Merge

Suppose two branches were created from the same commit.

```text
        C (main)
       /
A ─── B
       \
        D (feature)
```

When we execute

```bash
git merge feature
```

Git does **not** simply compare commits **C** and **D**.

Instead, it first identifies their **common ancestor**, which is commit **B**.

Then it compares:

- The changes from **B → C**
- The changes from **B → D**

Finally, Git asks:

> "Can I apply both sets of changes at the same time?"

If the answer is yes, the merge is completed automatically.

---

## When Does a Merge Conflict Occur?

A merge conflict occurs when both branches modify the **same region** of a file in incompatible ways.

For example, suppose the original file contains

```c
int age = 18;
```

After some work, the branches become

**main**

```c
int age = 19;
```

**feature**

```c
int age = 21;
```

Both branches modified the same line.

Git has no way of knowing which version should become part of the final project.

Should it keep

```c
int age = 19;
```

or

```c
int age = 21;
```

Or perhaps the developer actually wanted

```c
int age = 20;
```

Git cannot determine the developer's intention.

Instead of making an incorrect decision, Git stops and requests manual intervention.

---

## Automatic Merges

Not every modification to the same file creates a conflict.

Suppose the original file is

```c
int age = 18;
char *name = "Alice";
int score = 100;
```

The branches become

**main**

```c
int age = 19;
char *name = "Alice";
int score = 100;
```

**feature**

```c
int age = 18;
char *name = "Bob";
int score = 100;
```

Although both branches modified the same file, they modified **different lines**.

Since the changes do not overlap, Git combines them automatically.

The result becomes

```c
int age = 19;
char *name = "Bob";
int score = 100;
```

---

## Conflict Markers

When Git cannot resolve a conflict automatically, it modifies the file by inserting **conflict markers**.

For example,

```text
<<<<<<< HEAD
int age = 19;
=======
int age = 21;
>>>>>>> feature
```

These markers indicate the conflicting versions.

- Everything between `<<<<<<< HEAD` and `=======` belongs to the current branch (HEAD).
- Everything between `=======` and `>>>>>>> feature` belongs to the branch being merged.
- The developer must edit the file and leave the desired final version.

For example, the developer might decide to keep

```c
int age = 20;
```

After the conflict has been resolved, the conflict markers must be removed.

---

## Resolving a Merge Conflict

The workflow is straightforward.

First, execute the merge.

```bash
git merge feature
```

If Git detects a conflict, it stops and waits.

The developer then

1. Opens the conflicting file.
2. Removes the conflict markers.
3. Chooses (or combines) the desired code.
4. Saves the file.

After the file has been corrected, Git still needs confirmation that the conflict has been resolved.

```bash
git add <file>
```

In this context, `git add` does more than stage changes.

It tells Git:

> "This file has been resolved."

Finally, create the merge commit.

```bash
git commit
```

---

## Do We Always Need to Commit After a Merge?

It depends on the type of merge.

### Fast-forward

No merge commit is created.

Git simply moves the branch pointer forward.

No additional commit is necessary.

### Merge Without Conflicts

Git creates the merge commit automatically because it can combine all changes safely.

No manual commit is required.

### Merge With Conflicts

Git cannot create the merge commit automatically.

The developer must

1. Resolve the conflicts.
2. Stage the resolved files.
3. Create the merge commit manually.

---

## Summary

- Git performs merges by comparing both branches with their common ancestor.
- Most merges happen automatically.
- A merge conflict occurs when both branches modify the same region of a file in incompatible ways.
- Changes in different regions of the same file can usually be merged automatically.
- Conflict markers identify both versions of the conflicting code.
- The developer is responsible for choosing the final version.
- After resolving a conflict, the file must be staged with `git add`.
- A merge commit is created manually only when Git cannot complete the merge automatically.