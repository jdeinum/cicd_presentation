---
title: "CI/CD: Managing Change"
author: ""
<!-- end_slide -->

# CI/CD: Managing Change

<!-- end_slide -->

# Who am I?

I'm Jacob! Some people call me dino.

Some things I do:

<!-- list_item_newlines: 4 -->

- Senior Software Engineer at CarbinX Technologies
- CTO at Annona
- Part of a systems research group at UofA

<!-- end_slide -->

# Where can you find me?

<!-- list_item_newlines: 4 -->

- On github as jdeinum
- On discord as deinum#4841
- On nullspaces.io, little project of mine

<!-- end_slide -->

# What is CI/CD?

--- 

## I've seen it defined as:


**Continuous Integration (CI):** The practice of frequently integrating code changes into a shared repository, verified by automated builds and tests.

**Continuous Delivery/Deployment (CD):** The practice of automatically preparing and potentially releasing code changes to production.

<!-- end_slide -->

# What is CI/CD?

## My preferred definition:

An anti entropy system that takes code from your laptop and deploys it in
production.

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

- Ensure correctness
- Ensure non-functional requirements
- Ensure proper code quality
- Ensure developer agility

<!-- end_slide -->

# Taking a Simple App to Production

## Non-Goals

- Setting up all of the infrastructure

We'll focus on the **pipeline**, not the platform.

<!-- end_slide -->

# Environment Setup

- Simple API in Rust
- 3 branches: `dev`, `master`, `image_updates`
- **k3d** for local Kubernetes
- **ArgoCD** for GitOps deployments

<!-- end_slide -->

# Initiating the Pipeline

How do we trigger a release?

- Git tag
- Push to master
- Release PR

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

<!-- end_slide -->

# Tests

## Test Isolation

Tests should be **independent** and **reproducible**.

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

<!-- end_slide -->

# Tests

## Types of Tests

Consider both:
- **Scope:** Unit → Integration → E2E
- **Purity:** Pure (deterministic) vs Impure (side effects)

<!-- end_slide -->

# Tests

## How Do We Know Our Tests Are Good?

- Code coverage?
- Mutation testing?
- Property-based testing?

<!-- end_slide -->

# Benchmarks

## Benchmarking is Really, Really Hard!

- Noisy environments
- Micro vs macro benchmarks
- Statistical significance
- Reproducibility

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

<!-- end_slide -->

# Code Quality Checks

- **Standard formatting** (rustfmt)
- **Best practices** (clippy)

Consistency across the codebase.

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

<!-- end_slide -->

# Documentation

## Forcing Documentation

- Require docs for all public functions
- `#![deny(missing_docs)]`

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

<!-- end_slide -->

# Documentation

## Doc Tests

```rust
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
```

Your examples are your tests!

<!-- end_slide -->

# Correctness Checks

Proving correctness through:

- Type system
- Static analysis
- Formal verification (where applicable)
- Contract testing

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

<!-- end_slide -->

# Audits

## Vulnerability Tracking

- `cargo audit` for known vulnerabilities
- Dependency scanning in CI

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

<!-- end_slide -->

# Audits

## Unmaintained Dependencies

- Finding abandoned crates
- Planning migration paths

<!-- end_slide -->

# Building

## Key Questions

- Where does our software run?
- What does our software need access to?
- How do we attach context? (SBOMs, provenance data)
- Image scanning?

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

<!-- end_slide -->

# Pushing

## Artifact Storage

- Where are we storing our artifacts?
- How do we ensure they are **never overwritten**?

Immutable artifacts = reproducible deployments.

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

<!-- end_slide -->

# Deploying: Noticing New Versions

ArgoCD watches for new versions of your software.

GitOps: **Git is the source of truth.**

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

<!-- end_slide -->

# Deploying: Applying Changes

- **Dev:** ArgoCD changes the cluster directly
- **Production:** Manual verification before updating manifests
- ArgoCD sees updated manifest and applies changes

<!-- end_slide -->

# Precursor: Conventional Commits

In ArgoCD, **you** define what a "new version" is!

## Conventional Commits Help Us:

- Automate version bumping
- Generate release notes
- Create release PRs
- Prevent accidental breaking changes

<!-- end_slide -->

# Thanks!

<!-- end_slide -->

# Questions?

