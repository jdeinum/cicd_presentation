Title: CI/CD Managing Change

0: Who am I

1: Intro

Technical Definition

Simple Definition

2: Why do we need it ?

Deploying code that doesn't change is easy

Deploying code that changes is hard:

    - maintaining correctness
    - maintaining non functional requirements
    - maintaining code quality
    - SLAs / SLOs
    - etc

CI/CD is meant to help us manage change in a consistent way, a form of anti
entropy if you will.

3: Taking a simple rust app to production

Goals:

    - ensure correctness
    - ensure non functional goals
    - ensure proper code quality
    - ensure developer agility

Non Goals:

    - Setting up all of the infrastructure

4: Env setup

- simple http application
- 3 branches, dev, master, image_updates
- k3d
- argocd

6: Initiating the pipeline

    - git tag
    - push to master
    - release PR
    - ??

7: Tests

    - Test isolation
    - Types of tests, scope + purity
    - How are we sure we are writing good tests?

8: Benchmarks

    - Benchmarking is really really hard!

9: Code quality checks

    - Standard formatting
    - Best practices

10: Documentation

    - Forcing documentation for all functions
    - Doc tests

11: Correctness Checks

    - Proving correctness through whatever mechanism needed

12: Audits

    - Keeping track of vulnerabilities
    - Finding unmaintained code

13: Building

    - Where does our software run?
    - What does our software need access to?
    - How can we attach important context to our blobs (sboms + other provenance data)
    - Image scans?

14: Pushing

    - Where are we storing our artifats?
    - How do we ensure they are never overwritten?

15: Deploying 1. Noticing the new version

    - ArgoCD notices new version of your software

16: Deploying 2. Applying the changes to your runtime

    - Let ArgoCD change the cluster directly for dev
    - Require manual verification before updating production manifests
    - ArgoCD sees updated manifest and updates

17: Precursor: Conventional commits

    - In argoCD , you define what a new version is! How should upgrade our software versions? Convential commits!
    - Uses in release PRs
    - Helps prevent breaking changes

18: Thanks!

19: Questions?
