# Problem 1: Naive Dockerfile Causing Slow CI Builds

**Real-World Scenario:** CI pipelines take several minutes to build the same image repeatedly, even when most of the application code hasn’t changed.

**System Design Pattern:** Cache-Friendly Layering & Immutable Build Steps

**Solution Logic:** Structure the Dockerfile so that the most stable layers (OS, system packages, Python runtime, dependencies) appear first and change rarely, while volatile layers (app source, configs) appear last. This maximizes Docker’s layer cache hits, dramatically reducing rebuild times for small changes. The architectural focus is on ordering, immutability of build steps, and minimizing unnecessary cache invalidation by reducing `COPY` scope and `RUN` layer churn.


# Problem 2: Bloated Images Slowing Deployment and Wasting Resources

**Real-World Scenario:** Production images are >1 GB, contain build tools and caches, and take too long to pull to servers or developer laptops.

**System Design Pattern:** Multi-Stage Builds & Minimal Runtime Images

**Solution Logic:** Split the image into a “builder” stage (with compilers, build tools, and dev dependencies) and a “runtime” stage that only contains the app artifacts and minimal runtime. Copy only the built artifacts from builder to runtime, discarding weighty build-time layers. The architectural goal is strict separation of build-time and run-time concerns, reducing attack surface, image size, and pull time without sacrificing reproducibility.


# Problem 3: Flaky Builds and Inconsistent Dev/Prod Images

**Real-World Scenario:** The same Dockerfile behaves differently on developer machines and CI; sometimes builds are slow due to re-downloading dependencies, or images differ subtly across environments.

**System Design Pattern:** Deterministic Build Context & Dependency Caching

**Solution Logic:** Define a tight build context and a deterministic dependency pipeline using pinned dependency files and explicit cache boundaries. Use explicit `COPY` of only the dependency manifests (e.g., `requirements.txt`) into early layers and install dependencies there, then copy the rest of the source later. This allows caching of expensive dependency installs while ensuring that builds are reproducible across environments, and that dev/prod images are produced from the exact same Dockerfile and dependency definitions.


# Problem 4: Inefficient Rebuilds Due to Over-Broad Build Context

**Real-World Scenario:** Adding or modifying unrelated files (docs, local scripts) forces Docker to re-upload large build contexts and invalidate layers, slowing every build.

**System Design Pattern:** Minimal Build Context & Context Isolation

**Solution Logic:** Restructure the project so that the Docker build context includes only what is required to build and run the app, excluding logs, docs, and local tooling via directory layout and `.dockerignore`. This reduces context size, speeds up context transfer to the daemon/remote builder, and avoids spurious cache invalidations caused by files that should not affect the image. Architecturally, you treat the build context as an API boundary: small, explicit, and stable.

