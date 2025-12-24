---
title: "CI/CD: Managing Change"
author: ""
---

# CI/CD: Managing Change

---

# Who am I?

<!-- Add your introduction here -->

---

# What is CI/CD?

## Technical Definition

**Continuous Integration (CI):** The practice of frequently integrating code changes into a shared repository, verified by automated builds and tests.

**Continuous Delivery/Deployment (CD):** The practice of automatically preparing and potentially releasing code changes to production.

---

# What is CI/CD?

## Simple Definition

A system that helps us **safely and consistently** get code from our laptops to production.

---

# Why Do We Need It?

Deploying code that **doesn't change** is easy.

Deploying code that **changes** is hard:

- Maintaining correctness
- Maintaining non-functional requirements
- Maintaining code quality
- Meeting SLAs / SLOs
- ...and more

---

# CI/CD as Anti-Entropy

CI/CD helps us **manage change** in a consistent way.

Think of it as a form of **anti-entropy**  fighting the natural tendency of systems to become disordered over time.

---

# Taking a Simple Rust App to Production

## Goals

- Ensure correctness
- Ensure non-functional requirements
- Ensure proper code quality
- Ensure developer agility

---

# Taking a Simple Rust App to Production

## Non-Goals

- Setting up all of the infrastructure

We'll focus on the **pipeline**, not the platform.

---

# Environment Setup

- Simple HTTP application (Rust)
- 3 branches: `dev`, `master`, `image_updates`
- **k3d** for local Kubernetes
- **ArgoCD** for GitOps deployments

---

# Initiating the Pipeline

How do we trigger a release?

- Git tag
- Push to master
- Release PR
- ???

---

# Tests

## Test Isolation

Tests should be **independent** and **reproducible**.

---

# Tests

## Types of Tests

Consider both:
- **Scope:** Unit ’ Integration ’ E2E
- **Purity:** Pure (deterministic) vs Impure (side effects)

---

# Tests

## How Do We Know Our Tests Are Good?

- Code coverage?
- Mutation testing?
- Property-based testing?

---

# Benchmarks

## Benchmarking is Really, Really Hard!

- Noisy environments
- Micro vs macro benchmarks
- Statistical significance
- Reproducibility

---

# Code Quality Checks

- **Standard formatting** (rustfmt)
- **Best practices** (clippy)

Consistency across the codebase.

---

# Documentation

## Forcing Documentation

- Require docs for all public functions
- `#![deny(missing_docs)]`

---

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

---

# Correctness Checks

Proving correctness through:

- Type system
- Static analysis
- Formal verification (where applicable)
- Contract testing

---

# Audits

## Vulnerability Tracking

- `cargo audit` for known vulnerabilities
- Dependency scanning in CI

---

# Audits

## Unmaintained Dependencies

- Finding abandoned crates
- Planning migration paths

---

# Building

## Key Questions

- Where does our software run?
- What does our software need access to?
- How do we attach context? (SBOMs, provenance data)
- Image scanning?

---

# Pushing

## Artifact Storage

- Where are we storing our artifacts?
- How do we ensure they are **never overwritten**?

Immutable artifacts = reproducible deployments.

---

# Deploying: Noticing New Versions

ArgoCD watches for new versions of your software.

GitOps: **Git is the source of truth.**

---

# Deploying: Applying Changes

- **Dev:** ArgoCD changes the cluster directly
- **Production:** Manual verification before updating manifests
- ArgoCD sees updated manifest and applies changes

---

# Precursor: Conventional Commits

In ArgoCD, **you** define what a "new version" is!

## Conventional Commits Help Us:

- Automate version bumping
- Generate release notes
- Create release PRs
- Prevent accidental breaking changes

---

# Thanks!

---

# Questions?

