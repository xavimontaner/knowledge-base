# Git Workflows

## What Is a Git Workflow?

A Git workflow defines how a team uses Git to collaborate on the same project.

Git itself does not force any particular way of working.

Instead, a workflow establishes conventions such as:
- Which branches should exist.
- Where developers should work.
- How changes are integrated.
- When code is merged into the main branch.

Different teams use different workflows depending on the size of the project, the number of developers, and the release strategy.

The goal of every workflow is the same:

> **Allow multiple developers to work simultaneously while keeping the project's history organized and the main branch stable.**

---

## Centralized Workflow

The Centralized Workflow is the simplest way to collaborate using Git.

Every developer works directly on the main branch.

```text
main

A───B───C───D───E───F
 ↑   ↑   ↑   ↑
Alice Bob Alice Bob
```

Each developer pulls the latest changes, creates new commits, and pushes them directly to `main`.

### Advantages

- Very simple to understand.
- Easy to manage.
- Suitable for personal projects and very small teams.

### Disadvantages

- Everyone modifies the same branch.
- Merge conflicts become more frequent.
- It is easy to accidentally break the main branch.
- There is no isolation while developing new features.

---

## Feature Branch Workflow

Instead of working directly on `main`, every new feature is developed in its own branch.

```text
main
 │
 ├── feature/login
 │
 ├── feature/payment
 │
 ├── feature/profile
 │
 └── feature/api
```

Each developer works independently on their own branch.

When the feature is finished:

1. Update the branch with the latest changes from `main`.
2. Resolve any conflicts if necessary.
3. Merge the branch into `main`.

This keeps the main branch stable while allowing multiple developers to work simultaneously.

This is the workflow used by the vast majority of modern software teams because it provides a good balance between simplicity and collaboration.

### Advantages

- Isolates each feature.
- Keeps `main` stable.
- Makes code reviews easier.
- Scales well for medium and large teams.

### Disadvantages

- Requires managing multiple branches.
- Developers must regularly integrate changes from `main`.

---

## GitFlow

GitFlow extends the Feature Branch Workflow by introducing several permanent and temporary branches.

The permanent branches are:

- `main`
- `develop`

Additional branches are created for specific purposes:

- `feature/*`
- `release/*`
- `hotfix/*`

A simplified view looks like this:

```text
main
 │
 ├── hotfix/*
 │
develop
 │
 ├── feature/*
 ├── feature/*
 └── release/*
```

The idea is to separate ongoing development from stable releases.

New features are developed from `develop`.

Release branches prepare new versions before publication.

Hotfix branches allow urgent production bugs to be fixed immediately without waiting for the next release.

### Advantages

- Clear organization for complex projects.
- Excellent support for scheduled releases.
- Allows urgent production fixes independently of ongoing development.

### Disadvantages

- More branches to manage.
- More merge operations.
- More complex than other workflows.
- Can become unnecessary for projects with continuous deployment.

---

## Trunk-Based Development

Trunk-Based Development keeps a single main branch and encourages very short-lived feature branches.

```text
main
 │
 ├── feature/login (1 day)
 │       ↓
 │     merge
 │
 ├── feature/profile (2 days)
 │       ↓
 │     merge
 │
 └── feature/search (same day)
```

Developers integrate small changes frequently instead of keeping long-lived branches.

This approach reduces merge conflicts and allows teams to detect integration problems early.

It is commonly used together with Continuous Integration (CI) and Continuous Deployment (CD).

### Advantages

- Frequent integration.
- Smaller merge conflicts.
- Faster feedback.
- Well suited for modern software development.

### Disadvantages

- Requires disciplined development practices.
- Usually depends on automated testing and CI pipelines.

---

## Which Workflow Should You Use?

There is no single workflow that is best for every project.

The appropriate choice depends on the size of the team, the release strategy, and the project's requirements.

| Situation | Recommended Workflow |
|-----------|----------------------|
| Personal projects | Centralized Workflow |
| Small to medium-sized teams | Feature Branch Workflow |
| Most professional teams | Feature Branch Workflow |
| Teams using Continuous Integration / Continuous Deployment (CI/CD) | Trunk-Based Development |
| Projects with long release cycles and multiple maintained versions | GitFlow |

---

## Key Takeaways

- A Git workflow defines how a team collaborates using Git.
- Different workflows solve different collaboration problems.
- The Centralized Workflow is simple but offers little isolation.
- The Feature Branch Workflow is the most common choice for professional teams.
- GitFlow adds specialized branches for release management.
- Trunk-Based Development focuses on short-lived branches and frequent integration.
- There is no universally "best" workflow. The right choice depends on the project's needs.