# RxJS Operators (Practical Shortlist)

Focus on the 20% you use 80% of the time.

## Creation
- `of`, `from` create streams from values or promises/iterables.
- `defer` creates on subscription (useful for lazy work).

```typescript
const once$ = defer(() => api.getUser());
```

## Transform
- `map` transform values.
- `tap` side-effects (logging, metrics); do not mutate state here.
- `scan` accumulate (stream reduce).

## Filter
- `filter` keep what you want.
- `distinctUntilChanged` ignore repeats.
- `take`, `first`, `takeUntil` end subscriptions.

## Flatten (avoid nested subscribe)
- `switchMap` latest only (search/autocomplete).
- `mergeMap` parallel (fan-out calls).
- `concatMap` ordered (queue).
- `exhaustMap` ignore while busy (submit buttons).

## Combine
- `combineLatest` react to latest from multiple streams.
- `withLatestFrom` sample one stream with another.
- `forkJoin` wait for all to complete (like Promise.all).

## Error + Retry
- `catchError` handle errors at the boundary.
- `retry` / `retryWhen` for transient failures (add backoff).

```typescript
stream$.pipe(
  retry({ count: 3, delay: 500 }),
  catchError(() => EMPTY)
);
```

## Time + Rate
- `debounceTime` wait for quiet period (search input).
- `throttleTime` limit rate (scroll).
- `auditTime` sample latest at intervals.
- `timeout` fail slow operations.

## Multicasting / Caching
- `shareReplay({ bufferSize: 1, refCount: true })` cache latest.

```typescript
const user$ = http.get<User>("/api/user").pipe(
  shareReplay({ bufferSize: 1, refCount: true })
);
```

## Common Patterns

### Search Input
```typescript
search$.pipe(
  debounceTime(200),
  distinctUntilChanged(),
  switchMap(q => api.search(q))
);
```

### Cancel Previous Request
```typescript
query$.pipe(
  switchMap(q => api.fetch(q))
);
```

### Ordered Writes
```typescript
save$.pipe(
  concatMap(payload => api.save(payload))
);
```

## Anti-Patterns
- Nested subscribe.
- `shareReplay` without `refCount` for long-lived streams.
- Swallowing errors deep in the chain without surfacing or logging.
