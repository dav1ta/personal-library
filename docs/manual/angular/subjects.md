# RxJS Subjects (Concise)

Subjects are both an `Observable` and an `Observer`. Use them to multicast values.

```typescript
import { Subject, BehaviorSubject, ReplaySubject, AsyncSubject } from 'rxjs';
```

## Subject
- No initial value.
- New subscribers only see future emissions.

```typescript
const subject = new Subject<number>();
subject.next(1); // no subscribers yet
subject.subscribe(v => console.log(v));
subject.next(2); // prints 2
```

## BehaviorSubject
- Requires an initial value.
- New subscribers immediately receive the latest value.

```typescript
const state$ = new BehaviorSubject<number>(0);
state$.subscribe(v => console.log(v)); // prints 0
state$.next(1);
```

## ReplaySubject
- Replays the last N values (or time window) to new subscribers.
- Useful for caching; watch memory usage.

```typescript
const replay$ = new ReplaySubject<number>(2);
replay$.next(1);
replay$.next(2);
replay$.next(3);
replay$.subscribe(v => console.log(v)); // prints 2, 3
```

## AsyncSubject
- Emits only the last value, and only after completion.

```typescript
const done$ = new AsyncSubject<number>();
done$.next(1);
done$.next(2);
done$.complete();
done$.subscribe(v => console.log(v)); // prints 2
```

## Quick Comparison

| Type              | Initial Value | Replays | Typical Use |
|-------------------|---------------|---------|-------------|
| Subject           | No            | No      | Events |
| BehaviorSubject   | Yes           | Latest  | State |
| ReplaySubject     | No            | N / time| Cache |
| AsyncSubject      | No            | Last on complete | Final result |

## Best Practices
- Keep subjects private; expose `asObservable()`.
- Prefer plain observables or signals unless you need multicasting.
- Avoid using subjects as global state without clear ownership.

Next: [Gitlab](../setups/install.md)
