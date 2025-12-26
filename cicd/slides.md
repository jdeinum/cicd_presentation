---
theme: default
title: "CI/CD: Managing Change"
info: |
  CI/CD presentation with retro pixel styling
class: text-center
drawings:
  persist: false
transition: none
mdc: true
---

# CI/CD: Managing Change

---

# Who am I?

I'm Jacob! Some people call me dino.

<br>

**Some things I do:**

<v-click>

- Software Engineer at CarbinX Technologies
- CTO at Annona
- Part of a systems research group at UofA

</v-click>

<br>

**Where you can find me:**

<v-click>

- On github as [jdeinum](https://github.com/jdeinum)
- On discord as deinum#4841
- On [nullspaces.io](https://nullspaces.io/blog)

</v-click>

---

# What is CI/CD?

CI/CD are a set of practices that help streamline application deployment

<br>

<v-click>

**Continuous Integration** → Pushing changes into the main branch frequently

</v-click>

<br>

<v-click>

**Continuous Delivery** → Production software artifacts are built continuously

</v-click>

<br>

<v-click>

**Continuous Deployment** → Production software artifacts are deployed to your runtime continuously

</v-click>

---

# Why Do We Need It?

Deploying code that **doesn't change** is easy.

<br>

Deploying code that **changes** is hard:

<v-click>

- Maintaining correctness
- Maintaining non-functional requirements
- Maintaining code quality
- Meeting SLAs / SLOs
- Ensuring deployability

</v-click>

---

# Taking a Simple App to Production

## Goals

<v-click>

- Ensure correctness
- Ensure non-functional requirements
- Ensure proper code quality

</v-click>

## Non-Goals

<v-click>

- Setting up all of the infrastructure

</v-click>

<br>

<v-click>

We'll focus on the **pipeline**, not the platform.

</v-click>

---

# Environment Setup

## We will be using:

<v-click>

- Axum for our http application
- Git repository for code
- k3d for local Kubernetes
- ArgoCD for GitOps deployments

</v-click>

---

# Pushing Changes to Main

We gotta have something to deploy ¯\\_(ツ)_/¯

<br>

<v-click>

## The Golden Rule

</v-click>

<v-click>

**Always** have a working version of the application in main.

</v-click>

<v-click>

<br>
<br>
<br>

This lets us fork from main without fear!

</v-click>

---

# Pushing Changes to Main

## Semantic Versioning

Something often skipped that has **massive downstream impact**.

**1 in 6** of the top 1000 crates has violated semver at least once!

Conventional commits help:

- Automate version bumping
- Generate changelogs
- Prevent accidental breaking changes

---

# Pushing Changes to Main

## Tools of Interest

- **git** / **jj** - Version control
- **release-plz** - Automated release PRs
- **cargo-semver-checks** - Lint for semver violations

---

```mermaid
flowchart LR
    A[Initiate]:::current --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Initiating the Pipeline

How do we trigger a release?

- Git tag
- Push to master
- Release PR

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests

Tests are one of the cornerstones of building reliable software.

Each testing strategy lies somewhere on a **scope** and **purity** grid.

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests - Types

- Unit tests
- Invariant testing
- Snapshot testing
- Property testing
- Contract based testing
- Integration tests

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests - The Trap

```rust
fn is_even(number: u32) -> bool {
    // ...
}

#[test]
fn test_is_even() {
    assert!(is_even(2))
}
```

We never checked an odd number! For all we know:

```rust
fn is_even(number: u32) -> bool {
    return true
}
```

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests - Why It Matters

**NIST Study (2002):**

- Software bugs cost the U.S. economy **$59.5 billion/year**
- Over 1/3 of this cost (~$22B) could be avoided with better testing
- ~80% of development costs go to identifying and correcting defects
- Updated 2020 estimate: **$2.08 trillion** in costs from poor software quality

**Famous Examples:** Apple goto fail (2014), Knight Capital (2012)

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]:::current
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Tests - Tools of Interest

- **cargo-mutants** - Mutation testing
- **quickcheck** / **proptest** - Property testing
- **kani** - Exhaustive verification
- **insta** - Snapshot testing
- **wiremock** - HTTP mocking
- **nextest** - Fast test runner

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]:::current
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Benchmarks

If you thought testing was hard, a new challenger approaches.

Benchmarking is challenging because measuring is itself extremely difficult.

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]:::current
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Benchmarks - The Problems

- How do we know our program got the same CPU time each time?
- What if the CPU is busy from another process?
- What if the kernel stops everything to reclaim memory?

<br>

See: _"Producing Wrong Data Without Doing Anything Obviously Wrong!"_

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]:::current
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Benchmarks - Goodput

When measuring throughput under load, if we run out of resources the system returns 500s **really quickly**.

Observed throughput ≠ Valuable throughput

This is called **Goodput** - valuable throughput only.

<br>

**Example:** Cloudflare regex outage (2019)

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]:::current
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Benchmarks - Tools of Interest

- **criterion** - Statistical benchmarking
- **hyperfine** - CLI benchmarking
- **divan** - Fast benchmarking
- **iai-callgrind** - Instruction-level benchmarks

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]:::current
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Code Quality Checks

CI/CD as anti-entropy is most evident here.

Everyone has different coding styles. On bigger projects, we use tools to ensure a standard everyone abides by.

- **rustfmt** - Consistent formatting
- **clippy** - Best practices and common pitfalls
- **cargo-machete** - Find unused dependencies

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]:::current
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Correctness Checks

Concurrent systems are hard.

Adding checks in your CI/CD pipeline helps catch undefined behavior before it lands in deployment.

- **Miri** - Detect undefined behavior
- **Shuttle** / **Loom** - Concurrency testing
- **Turmoil** - Network simulation

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]:::current
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Audits

Who wants to deploy unsafe code?

Audit tools help capture:

- CVEs
- Unmaintained repos
- License issues

We don't need to run this on every push - only when dependencies change or weekly.

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]:::current
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Audits - Tools of Interest

- **cargo-deny** - Lint dependencies for security, licenses, sources
- **cargo-vet** - Supply chain security
- **cargo-audit** - Known vulnerability detection

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]:::current
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Building

Now that we have validated source code, we can build.

**Key questions:**

- Where will the code run? (Container, binary, WASM?)
- How do we attach context? (SBOMs, provenance data)
- What about image scanning?

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]:::current
    G --> H[Push]
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Building - Tools of Interest

- **docker buildx** - Multi-platform builds
- **nix** - Reproducible builds
- **cargo-auditable** - Embed dependency info in binary

We can embed dependencies and source code info into the binary itself for future inspection.

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]:::current
    H --> I[Deploy]
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Pushing

Now we need to store our built artifacts somewhere.

<br>

**Recommendations:**

- Upload to both cloud registry and local registry
- Local images let you run scanning tools without cost concerns

<br>

**Make sure your registry entries are immutable!**

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]:::current
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Deploying - Noticing New Versions

Tools like **ArgoCD Image Updater** and **Flux** monitor your container registries.

They either:

- Directly update your runtime (continuous deployment)
- Notify you that a new version is available

---

```mermaid
flowchart LR
    A[Initiate] --> B[Tests]
    B --> C[Benchmarks]
    C --> D[Code Quality]
    D --> E[Correctness]
    E --> F[Audits]
    F --> G[Build]
    G --> H[Push]
    H --> I[Deploy]:::current
    classDef current fill:#f96,stroke:#333,stroke-width:4px
```

# Deploying - Applying Changes

With k3d and ArgoCD, we update the `Application` manifests.

<br>

**GitOps method:**

- Built-in audit log of deployment upgrades
- Git is the source of truth

**Direct modification:**

- Maximum agility
- Can get audit info earlier in pipeline

---

## layout: center

# Thanks!

---

## layout: center

# Questions?
