# Docker Image Build Optimisation Lab

This mini-project explores practical image build optimisations around four concrete problems: cache-friendly layering, multi-stage builds, deterministic dependency caching, and minimal build contexts.

## Project Plan

- Use a small Python service (can mirror `python-docker-lab`) as the target app.
- For Problem 1, create a naive Dockerfile and an optimised cache-friendly variant; observe rebuild times after small code changes.
- For Problem 2, introduce a multi-stage build that separates build and runtime stages; compare image sizes, pull times, and contents.
- For Problem 3, design a dependency pipeline that caches Python dependencies effectively while keeping builds deterministic; vary `requirements.txt` vs app code and observe cache behaviour.
- For Problem 4, tighten the Docker build context using directory structure and `.dockerignore`; compare build context size and rebuild behaviour when modifying non-essential files.

