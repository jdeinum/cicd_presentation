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

<style>
.slidev-layout pre code,
.slidev-layout .shiki,
.shiki code,
pre.shiki-magic-move,
div[class*="language-"] pre,
div[class*="language-"] code {
  line-height: 1.1 !important;
  padding: 0.5rem !important;
}

.shiki .line {
  line-height: 1.1 !important;
}
</style>

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

<br>

<v-click>

**1 in 6** of the top 1000 crates has violated semver at least once! <span class="citation"><a href="/sources">[1]</a></span>

</v-click>

<br>

<v-click>

[Conventional commits](https://www.conventionalcommits.org/) help:

- Automate version bumping
- Generate changelogs
- Prevent accidental breaking changes

</v-click>

---

# Pushing Changes to Main

Tools of Interest:

<v-click>

- git / [jj](https://github.com/martinvonz/jj) - Version control

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Initiating the Pipeline

How do we trigger a release?

<v-click>

- Manual git tag
- Release PR
- Push to master

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Initiating the Pipeline

Tools of Interest:

<v-click>

- [release-plz](https://github.com/release-plz/release-plz) - Automated releases
- [cargo-semver-checks](https://github.com/obi1kenobi/cargo-semver-checks) - Semver linting

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Tests

Each testing strategy lies somewhere on a **scope** and **purity** grid.

<v-click>

- Unit tests
- Invariant testing
- Snapshot testing
- Property testing
- Contract based testing
- Integration tests

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Tests

Why testing might be worthwhile.

**NIST (2002):** <span class="citation"><a href="/sources">[2]</a></span>

<v-click>

- Software bugs cost the U.S. economy **$59.5 billion/year**
- Over **1/3** of this cost (~$22B) could be avoided with better testing
- ~80% of development costs go to identifying and correcting defects

</v-click>

**CISQ (2020):** <span class="citation"><a href="/sources">[3]</a></span>

<v-click>

- Updated estimate: **$2.08 trillion** in costs from poor software quality (US only)

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Tests

## [Apple goto fail](https://dwheeler.com/essays/apple-goto-fail.html) (2014) <span class="citation"><a href="/sources">[4]</a></span>

Duplicate line broke SSL verification for 18 months. A single unit test would have caught it.

<!-- Image placeholder -->

## [Knight Capital](https://www.henricodolfing.com/2019/06/project-failure-case-study-knight-capital.html) (2012) <span class="citation"><a href="/sources">[5]</a></span>

Lost **$440 million in 45 minutes**. Old test code accidentally deployed to production.

<!-- Image placeholder -->

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bol
```

# Tests

Testing is non trivial.

```rust
#[test]
fn test_is_even() {
    assert!(is_even(2))
}
```

<br>
<br>
<v-click>

It appears correct, although it could just be:

```rust
fn is_even(number: u32) -> bool {
    return true
}
```

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bol
```

# Tests

Some lessons:

<v-click>

- Ensure you have atleast 1 test per group in your input (even/odd in our case)
- Always check error conditions
- Use code coverage tools

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Tests

Tools of Interest:

<v-click>

- [cargo-mutants](https://github.com/sourcefrog/cargo-mutants) - Mutation testing
- [quickcheck](https://github.com/BurntSushi/quickcheck) / [proptest](https://github.com/proptest-rs/proptest) - Property testing
- [kani](https://github.com/model-checking/kani) - Exhaustive verification
- [insta](https://github.com/mitsuhiko/insta) - Snapshot testing
- [wiremock](https://github.com/LukeMathWalker/wiremock-rs) - HTTP mocking
- [nextest](https://github.com/nextest-rs/nextest) - Fast test runner
- [codecov](https://github.com/codecov/codecov-action) - Code coverage

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Benchmarks

If you thought testing was hard, I have some bad news for you.

<v-click>

<img src="/bench.jpeg" class="h-80 mx-auto" />

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Benchmarks

```rust
use std::time::SystemTime;

let start = SystemTime::now();

FunctionThatNormallyTakes100SecondsToRun();

let time_in_seconds = start.elapsed();

println!("Function took {} seconds", elapsed.as_secs());
```

Seems pretty reasonable...

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Benchmarks

<div class="grid grid-cols-2 gap-4">
  <v-click>
    <img src="/res.jpeg" />
  </v-click>
  <v-click>
    <div>
      <img src="/pre_instant.jpg" class="w-3/4 mt-8 ml-20" />
      <div class="absolute bottom-10 left-10 text-2xl font-bold">
        You've done it!
      </div>
    </div>
  </v-click>
</div>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Benchmarks

```rust {all|1}
use std::time::SystemTime;

let start = SystemTime::now();

FunctionThatNormallyTakes100SecondsToRun();

let time_in_seconds = start.elapsed();

println!("Function took {} seconds", elapsed.as_secs());
```

<v-click>

Not **monotonic**, NTP can step your clock back!

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Benchmarks

````md magic-move
```rust
use std::time::SystemTime;

let start = SystemTime::now();

FunctionThatNormallyTakes100SecondsToRun();

let time_in_seconds = start.elapsed();

println!("Function took {} seconds", elapsed.as_secs());
```

```rust
use std::time::Instant; // instant is monotonic, phew!

let start = Instant::now();

FunctionThatNormallyTakes100SecondsToRun();

let time_in_seconds = start.elapsed();

println!("Function took {} seconds", elapsed.as_secs());
```
````

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# Benchmarks

```rust
use std::time::Instant;

let start = Instant::now();

FunctionThatNormallyTakes100SecondsToRun();

let time_in_seconds = start.elapsed();

println!("Function took {} seconds", elapsed.as_secs());
```

Now we get consistent benchmark times right?

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
```

# How do we know:

<v-click>

- We get the same CPU share time
- The system didn't pause to reap memory
- Other programs flood the TLB ?

</v-click>

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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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
    classDef current fill:#3b82f6,stroke:#333,stroke-width:2px,font-weight:bold
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

---

## routeAlias: sources

# Sources

[1] [SemVer in Rust: Breakage, Tooling, and Edge Cases](https://predr.ag/blog/semver-in-rust-tooling-breakage-and-edge-cases/) - FOSDEM 2024

[2] [The Economic Impacts of Inadequate Infrastructure for Software Testing](https://www.nist.gov/document/report02-3pdf) - NIST 2002

[3] [The Cost of Poor Software Quality in the US: A 2020 Report](https://www.it-cisq.org/the-cost-of-poor-software-quality-in-the-us-a-2020-report/) - CISQ 2020

[4] [The Apple goto fail vulnerability: lessons learned](https://dwheeler.com/essays/apple-goto-fail.html) - David Wheeler

[5] [Project Failure Case Study: Knight Capital](https://www.henricodolfing.com/2019/06/project-failure-case-study-knight-capital.html) - Henrico Dolfing
