# Image Security

- Use minimal base images.
- Pin image versions (avoid `latest` in prod).
- Scan images in CI.
- Sign and verify images if your platform supports it.

## Runtime Controls
- Drop Linux capabilities where possible.
- Run as non-root.
