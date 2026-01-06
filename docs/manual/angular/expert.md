# Advanced Angular (Battle-Tested Notes)

Concise, practical guidance for large Angular apps. No fluff.

---

## Architecture
- Keep feature boundaries clear: `feature/`, `shared/`, `core/`, `data/`.
- Prefer standalone components; keep NgModules only where needed.
- Keep `app` thin; push logic into services and feature libs.
- Limit cross-feature imports; use public API barrels.

## State Management
- Use signals for local UI state; keep it near the component.
- Use RxJS for async orchestration, streams, and cancellation.
- For global state, prefer small, domain-scoped stores over one giant store.

## Change Detection
- Default to `OnPush` for feature components.
- Push changes via immutable inputs or signals.
- Call `markForCheck()` only for external events (timers, sockets).

## Performance
- Use `trackBy` on long lists.
- Avoid heavy work in templates; compute in code.
- Use deferrable views for expensive sections.
- Cache HTTP results with `shareReplay({ bufferSize: 1, refCount: true })`.

## Routing
- Lazy-load features by default.
- Keep route guards small; delegate to services.
- Use route data for page-level config.
- Avoid nested route trees unless they solve a real layout problem.

## Forms
- Prefer Reactive Forms for complex forms.
- Use typed forms for safety.
- Keep validators pure and isolated.

## DI and Providers
- Prefer `inject()` in factories and helpers.
- Use `providedIn: 'root'` for singleton services.
- Avoid provider duplication at component level unless necessary.

## Security
- Never bypass sanitization in templates.
- Use HttpInterceptors for auth headers and error handling.
- Keep secrets out of the client; use short-lived tokens.

## Testing
- Use `TestBed` only where integration matters.
- Unit-test services with pure DI and mocked deps.
- For UI, prefer harnesses or testing-library patterns.
- Use `fakeAsync` sparingly; prefer real async with `waitFor`.

## SSR and Hydration
- Keep server-only logic isolated.
- Avoid browser-only globals in shared code.
- Use transfer state to avoid duplicate requests.

## Deployment
- Enforce production builds in CI.
- Fail builds on lint/test errors.
- Keep source maps off in production unless needed for error tracking.

## Scalability Practices
- Measure first: add perf budgets, bundle analysis, and profiling.
- Keep API boundaries stable; refactors should not leak.
- Avoid global event buses; use typed service APIs.

## Red Flags
- Nested subscriptions
- Large mutable objects passed via inputs
- Services with side effects in constructors
- UI logic hidden in templates

---

## Version Notes
See [Versions](versions.md) for v18–v20 highlights and official links.
