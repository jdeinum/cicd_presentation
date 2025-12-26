---
title: "CI/CD: Managing Change"
author: ""
---

# Who am I?

I'm Jacob! Some people call me dino.

Some things I do:

<!-- list_item_newlines: 4 -->

- Software Engineer at CarbinX Technologies
- CTO at Annona
- Part of a systems research group at UofA

ADD SPACE HERE

Where you can find me:

<!-- list_item_newlines: 4 -->

- On github as jdeinum
- On discord as deinum#4841
- On nullspaces.io, little project of mine

<!-- end_slide -->

# What is CI/CD?

CI/CD are a set of practices that help streamline application deployment, from
Wiki:

Continuous integration -> Pushing changes into the main branch frequently

Continuous delivery -> Production software artifacts are build continuously

Continuous deployment -> Production software arficats are deployed to your runtime
continuously

<!-- end_slide -->

# Why Do We Need It?

Deploying code that **doesn't change** is easy.

Deploying code that **changes** is hard:

- Maintaining correctness
- Maintaining non-functional requirements
- Maintaining code quality
- Meeting SLAs / SLOs
- ...and more

<!-- end_slide -->

# Taking a Simple App to Production

## Goals

- Ensure code correctness
- Ensure non-functional requirements
- Ensure code quality

## Non-Goals

- Setting up all of the infrastructure

We'll focus on the **pipeline**, not the platform.

<!-- end_slide -->

# Environment Setup

- Simple API in Rust using Axum
- `master` and feature branches
- **k3d** for local Kubernetes
- **ArgoCD** for GitOps deployments

<!-- end_slide -->

# Pushing Changes to Main

We gotta have something to deploy ¯\\_(ツ)_/¯

SPACE HERE

## A Golden Rule

Always have a **working version** of the application in main.

This requires:

- Pull requests require status checks to pass
- Branch must be up to date with main before merging

This means we can fork from main at any point without fear of starting with
soomething broken!

SPACE HERE

## Semantic Versioning

Something often skipped that has **massive downstream impact**.

**1 in 6** of the top 1000 crates has violated semver at least once!

Conventional commits help:

- Automate version bumping
- Generate changelogs
- Prevent accidental breaking changes

SPACE HERE

## Tools of Interest

- **git** / **jj** - Version control
- **release-plz** - Automated release PRs
- **cargo-semver-checks** - Lint for semver violations

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate]:::current --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Initiating the Pipeline

How do we trigger a release?

- Git tag
- Push to master
- Release PR

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests

## Test Isolation

Tests should be **independent** and **reproducible**.

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests

## Types of Tests

Consider both:

- **Scope:** Unit → Integration → E2E
- **Purity:** Pure (deterministic) vs Impure (side effects)

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests

## How Do We Know Our Tests Are Good?

- Code coverage?
- Mutation testing?
- Property-based testing?

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]:::current
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Benchmarks

## Benchmarking is Really, Really Hard!

- Noisy environments
- Micro vs macro benchmarks
- Statistical significance
- Reproducibility

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]:::current
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Code Quality Checks

- **Standard formatting** (rustfmt)
- **Best practices** (clippy)

Consistency across the codebase.

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]:::current
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Documentation

## Forcing Documentation

- Require docs for all public functions
- `#![deny(missing_docs)]`

<!-- end_slide -->

# Documentation

## Doc Tests

````rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// assert_eq!(add(2, 2), 4);
/// ```
fn add(a: i32, b: i32) -> i32 {
    a + b
}
````

Your examples are your tests!

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]:::current
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Correctness Checks

Proving correctness through:

- Type system
- Static analysis
- Formal verification (where applicable)
- Contract testing

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]:::current
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Audits

## Vulnerability Tracking

- `cargo audit` for known vulnerabilities
- Dependency scanning in CI

<!-- end_slide -->

# Audits

## Unmaintained Dependencies

- Finding abandoned crates
- Planning migration paths

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]:::current
    H --> I[Push]
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Building

## Key Questions

- Where does our software run?
- What does our software need access to?
- How do we attach context? (SBOMs, provenance data)
- Image scanning?

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]:::current
    I --> J[Deploy]

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Pushing

## Artifact Storage

- Where are we storing our artifacts?
- How do we ensure they are **never overwritten**?

Immutable artifacts = reproducible deployments.

<!-- end_slide -->

```mermaid +render +width:70%
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Documentation]
    E --> F[Correctness]
    F --> G[Audits]
    G --> H[Build]
    H --> I[Push]
    I --> J[Deploy]:::current

    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Deploying: Noticing New Versions

ArgoCD watches for new versions of your software.

GitOps: **Git is the source of truth.**

<!-- end_slide -->

# Deploying: Applying Changes

- **Dev:** ArgoCD changes the cluster directly
- **Production:** Manual verification before updating manifests
- ArgoCD sees updated manifest and applies changes

<!-- end_slide -->

# Thanks!

<!-- end_slide -->

# Questions
