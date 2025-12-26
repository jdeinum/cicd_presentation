Title: CI/CD Managing Change

0: Who am I

1: Intro

CI/CD are a set of practices that help streamline application deployment

Continuous integration: Pushing changes into the main branch

Continuous delivery: Production software artifacts are build continuously

Continuous deployment: Production software arficats are deployed to your runtime
continuously

2: Why do we need it ?

Deploying code that doesn't change is easy

Deploying code that changes is hard:

    - maintaining correctness
    - maintaining non functional requirements
    - maintaining code quality
    - SLAs / SLOs
    - ensuring deployability
    - etc

3: Taking a simple rust app to production

I learn best with examples, and I think a lot of people are in the same boat. So
I made a simple Rust HTTP server that we can use for our examples.

Goals:

    - ensure correctness
    - ensure non functional goals
    - ensure proper code quality
    - ensure developer agility

Non Goals:

    - Setting up all of the infrastructure

4: Env setup

- simple rust http application
- master branch and short lived feature branches
- k3d
- argocd

<!---------------------------------------------------------------------------->
<!-- Continuous Integration -->

5: Pushing changes frequently into main frequently

We gotta have something to deploy ¯\\_(ツ)_/¯

Golden Rule: Always have a working version of the application in main. This
requires some specfiic settings in git repos, namely that pull requests require
status checks to pass and the branch be up to date with main.

Whatever your branch setup, we want to merge changes with main frequently
through our PR flows. One way this can look like it having a trunk branch spun
off of it, making your changes, commiting, pushing, and merging a PR.

Something a lot of people skip over (that has massive downstream impact) is
semantic versioning. Convential commits and the tooling surrounding it help a
ton with this, and it helps prevent breaking downstream consumers.

1/6 in the top 1000 crates broke semver! That's a big deal!
https://predr.ag/blog/semver-in-rust-tooling-breakage-and-edge-cases/

Tools of interest:

- git
- jj
- release-plz
- cargo-semver-checks

Presentation: Simple example of creating a branch, making a change, and
commiting.

6: Tests

Everyone knew this was coming. Tests are one of the cornerstones of building
reliable software. There are many forms of testing, each with their own
purpose. I really like the way Alex Matklad thinks about tests, which is that
each testing strategy lies somewhere on the scope and purity grid.

Types of tests:

- Unit tests
- Invariant testing
- Snapshot testing
- Property testing
- Contract based testing
- Integration tests

While the concept of testing code might seem easy, in reality it really isn't.
Consider the following simple example:

```rust
fn is_even(number: u32) -> bool {
    // ...
}

#[test]
fn test_is_even() {
    assert(is_even(2))
}
```

We never actually checked an odd number. Also are we sure it handles 0
correctly? For all we know, the function signature code be:

```rust
fn is_even(number: u32) -> bool {
    return true
}
```

For code that returns errors, we HAVE to check that the error actually occurs
when we expect it to. For rust users, theres a really cool tool called
cargo-mutents, that uses a form of logic chaos engineering to ensure your test
quality.

For simple functions like that, property based testing is good, or is the input
space is small enough, something like Kani that checks all inputs is good.

Tools of interest:

- cargo-mutants
- quickcheck
- proptest
- kani
- insta
- wiremock
- nexttest

Examples:
Apple goto error in 2014  
Knight Capital 2012

NIST Study (2002):

- Software bugs cost the U.S. economy $59.5 billion/year
- Over 1/3 of this cost (~$22B) could be avoided with better testing
- Over half of bugs are found "downstream" rather than during development
- ~80% of development costs go to identifying and correcting defects
- Updated 2020 estimate: $2.08 trillion in costs from poor software quality
- https://www.nist.gov/document/report02-3pdf

7: Benchmarks

If you thought testing was hard, a new challenger approaches. Benchmarking is
challenging not just because it require you understand what you're trying to
measure, but because measuring that may itself be extremely difficult.

Consier the following snippet:

```rust
let start = Instant::now();

isEven(2);

let time_in_seconds = Instant::now() - start;

println!("isEven took {time_in_seconds} seconds");
```

or how about

```rust
// Instant is now a monotomic clock, garanteeing we move forward
// and we'll ignore clock slewing for now
let start = Instant::now();

isEven(2);

let time_in_seconds = Instant::now() - start;

println!("isEven took {time_in_seconds} seconds");
```

How do we know that our program got the same CPU time each time? What happens if
the CPU is super busy from another process? Or we run out of memory and mid way
through running the program the kernel stops everything to reclaim memory
synchonrously? There is a great paper called "Producing Wrong Data Without Doing
Anything Obviously Wrong!" where they explore how changing seamingly unrelated
parameters greatly affects benchmarks.

As another example, consider measuring response times acorss a varying amount of
requests per second. If we reach a point where our system runs out of resources,
it will start returning 500s or some other HTTP code, and it can do that REALLY
QUICKLY. Meaning our observed throughput is not actually valuable throughput.
There is a term coined for this called "Goodput", pretty much just valuable
throughput.

On the note of measuring throughput, are you modelling your real world message
patterns correctly? There is a great paper called open or closed that talks
about how simple assumptions about request patterns can greatly.

Benchmarking is hard! But if you care about something (SLAs / SLOs), you HAVE to
be measuring it to determine regressions.

Tools of interest:

- criterion
- hyprfine
- tang-bench
- iai callgrind

Sources:

- Producing Wrong Data Without Doing Anything Obviously Wrong!
- Open or closed, a cautionary tail

Examples:
Cloudflare - 2019 https://blog.cloudflare.com/details-of-the-cloudflare-outage-on-july-2-2019/

8: Code quality checks

At the start I talked a bit about CI/CD being a form of anti entropy, and I
think its most evident in the code quality checks. Everyone has different coding
styles and sylistic preferences. On bigger projects, we use tools to
ensure there is a standard that everyone abides by.

Code formatters like rustfmt are important for ensuring consistent formatting

Linters like clippy help ensure you follow best practices, and avoid common
pitfalls. They also help make sure you are not missing documention.

Tools of interst:

- cargo-machete
- cargo
- clippy
- rustfmt

9: Correctness Checks

Concurrent systems are hard. Adding checks in your CI/CD pipeline help catch
undefined behavior before it lands in deployment.

Tools of interest:

- Miri
- Shuttle / Loom
- Turmoil

10: Audits

Who wants to deploy unsafe code? Audit tools help capture things like CVEs,
unmaintained repos, and license issues.

We don't need to run this on every push, only if one of our dependencies changes
and maybe once a week just to find any new issues unrelated to updating
dependencies.

Tools of interest:

- cargo-deny
- cargo-vet

<!---------------------------------------------------------------------------->
<!-- Continuous Delivery -->

11: Building

Now that we have our validated source code, we can look to build the code
itself. When designing the CI/CD pipeline, it's important we ask ourself where
the code is likely to be run. Is it in a container runtime of some kind,
directly as a binary on some target system, or a wasm runtime?

Another thing we have to often think about is what piece of software is running
where and when. We can actually choose to embedd our dependencies and source
code info into the binary itself as part of the build so we can inspect it at a
future date. Alternatively, we can have docker generate SBOMs and provenance
info for our images.

Tools of interest:

- docker buildx
- nix
- cargo-audittable

12: Pushing

Now that we have our built software artifacts, we need to put it somewhere. Your
choice of registry / artifact hub is mostly a personal preference. I prefer
uploading both to a cloud registry of your choice as well as a local registry.
Having images local let's me run new code / image scanning tools without needing
to worry about costs.

Make sure your registry entries are immutable!

13: Deploying 1. Noticing the new version

Once we have our software artifacts stored somewhere, we need to initialize the
process of deploying our software. Software tools like ArgoCD Image updater and
Flux monitor your container registries for new software artifacts, and either
directly update your software runtime (continuous deployment) to run the new
version or notify you that a new version is available and allows you to deploy
when you want.

14: Deploying 2. Applying the changes to your runtime

Assuming you didn't automatically deploy your new software, you need to
manually update your software runtime yourself. In our case with k3d and argocd,
this means we need to update the argocd `Application` manifests. I have tried
both allowing direct modification of the runtime and the gitops method, and I
recommend considering your position to choose between the two. Do you need an
audit log of deployment upgrades? Git has that builtin, although we can also
potentially get this info earlier in the pipeline. Maximum agility? Directly
modify.

15: Thanks!

16: Questions?
