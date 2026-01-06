# Angular + RxJS Tips (Concise)

## RxJS

- Use `AsyncPipe` in templates; use `takeUntilDestroyed` for manual subscriptions.
- Prefer flattening operators over nested subscriptions:
  - `switchMap` for latest-only
  - `concatMap` for ordered
  - `mergeMap` for parallel
  - `exhaustMap` for "ignore while busy"
- Use `shareReplay({ bufferSize: 1, refCount: true })` to cache HTTP results; avoid caching errors.
- Put `catchError` at the boundary and return a safe fallback (`EMPTY`, default value, or rethrow).

```typescript
import { DestroyRef } from '@angular/core';
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

constructor(private destroyRef: DestroyRef) {}

ngOnInit() {
  this.stream$
    .pipe(takeUntilDestroyed(this.destroyRef))
    .subscribe();
}
```

## Angular

- Use `OnPush` + immutable inputs; call `markForCheck()` for external updates.
- Use `trackBy` in `*ngFor` to avoid re-rendering large lists.
- Prefer standalone components for smaller, clearer dependency graphs.
- Use `inject()` in factories/helpers instead of constructor-only DI.
- Signals: use `signal` for writable state, `computed` for derived state, and `effect` for side effects.

```typescript
count = signal(0);
double = computed(() => this.count() * 2);

inc() {
  this.count.update(v => v + 1);
}
```

## Performance Quick Hits

- Avoid heavy work in templates; precompute in signals or component fields.
- Avoid re-creating objects in templates (e.g., `{}` or `[]` per change detection).
- Lazy load feature areas and split routes.
- Use `defer` / deferrable views for expensive UI blocks.

Next: [General](expert.md)
