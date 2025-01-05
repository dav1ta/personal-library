## Full List of RxJS Operators with Examples

### 1. **distinctUntilChanged**
   - **Basic**: Emit only if the current value is different from the previous.
   - **Advanced**: Use it to compare complex objects.

   ```typescript
   import { of } from 'rxjs';
   import { distinctUntilChanged } from 'rxjs/operators';

   // Basic usage
   of(1, 1, 2, 2, 3, 1, 3)
     .pipe(distinctUntilChanged())
     .subscribe(console.log); // Output: 1, 2, 3, 1, 3

   // Advanced usage (comparing object properties)
   of(
     { id: 1, name: 'Alice' },
     { id: 2, name: 'Alice' },
     { id: 2, name: 'Bob' }
   )
   .pipe(distinctUntilChanged((prev, curr) => prev.id === curr.id))
   .subscribe(console.log); // Output: {id: 1, name: 'Alice'}, {id: 2, name: 'Alice'}
   ```

### 2. **map**
   - **Basic**: Transform each value emitted by the source.
   - **Advanced**: Use it to transform API responses.

   ```typescript
   import { of } from 'rxjs';
   import { map } from 'rxjs/operators';

   // Basic usage
   of(1, 2, 3).pipe(map(value => value * 2)).subscribe(console.log); // Output: 2, 4, 6

   // Advanced usage (transforming an API response)
   of({ data: { user: { id: 1, name: 'Alice' } } })
     .pipe(map(response => response.data.user.name))
     .subscribe(console.log); // Output: 'Alice'
   ```

### 3. **switchMap**
   - **Basic**: Map each value to a new observable, canceling previous.
   - **Advanced**: Use in search auto-completion.

   ```typescript
   import { of, interval } from 'rxjs';
   import { switchMap, take } from 'rxjs/operators';

   // Basic usage
   of('first', 'second')
     .pipe(switchMap(val => interval(1000).pipe(take(2))))
     .subscribe(console.log); // Cancels 'first', emits 0, 1 for 'second'

   // Advanced usage (search auto-complete)
   const searchInput$ = of('apple', 'app', 'apple pie');
   searchInput$
     .pipe(
       switchMap(query => fakeApiSearch(query)) // Cancel previous search if a new one occurs
     )
     .subscribe(console.log);

   function fakeApiSearch(query: string) {
     console.log(`Searching for ${query}`);
     return of(`Results for ${query}`);
   }
   ```

### 4. **combineLatest**
   - **Basic**: Combine latest values from multiple observables.
   - **Advanced**: Synchronize multiple fields in a form.

   ```typescript
   import { of, combineLatest } from 'rxjs';

   // Basic usage
   const obs1$ = of(1, 2, 3);
   const obs2$ = of('A', 'B', 'C');
   combineLatest([obs1$, obs2$]).subscribe(console.log); // Output: [3, 'C']

   // Advanced usage (synchronizing form fields)
   const firstName$ = of('John');
   const lastName$ = of('Doe');
   combineLatest([firstName$, lastName$])
     .pipe(map(([first, last]) => `${first} ${last}`))
     .subscribe(console.log); // Output: 'John Doe'
   ```

### 5. **mergeMap**
   - **Basic**: Map each value to an observable and merge results.
   - **Advanced**: Perform parallel requests and combine their results.

   ```typescript
   import { of } from 'rxjs';
   import { mergeMap } from 'rxjs/operators';

   // Basic usage
   of('A', 'B')
     .pipe(mergeMap(val => of(`${val}1`, `${val}2`)))
     .subscribe(console.log); // Output: 'A1', 'A2', 'B1', 'B2'

   // Advanced usage (parallel requests)
   const userIds$ = of(1, 2, 3);
   userIds$
     .pipe(
       mergeMap(id => fetchUser(id)) // Fetches users in parallel
     )
     .subscribe(console.log);

   function fetchUser(id: number) {
     return of(`User ${id}`); // Simulates fetching user by ID
   }
   ```

### 6. **debounceTime**
   - **Basic**: Delay emissions for a set time.
   - **Advanced**: Use in a search field to limit API calls.

   ```typescript
   import { of } from 'rxjs';
   import { debounceTime } from 'rxjs/operators';

   // Basic usage
   of(1, 2, 3)
     .pipe(debounceTime(1000))
     .subscribe(console.log); // Emits only after 1 second delay

   // Advanced usage (debouncing API calls)
   const userInput$ = of('search term');
   userInput$
     .pipe(debounceTime(300)) // Delay API call until typing stops
     .subscribe(query => performSearch(query));

   function performSearch(query: string) {
     console.log(`Searching for: ${query}`);
   }
   ```

### 7. **takeUntil**
   - **Basic**: Take values until another observable emits.
   - **Advanced**: Auto-unsubscribe on component destruction.

   ```typescript
   import { interval, Subject } from 'rxjs';
   import { takeUntil } from 'rxjs/operators';

   // Basic usage
   const source$ = interval(1000);
   const stop$ = new Subject();
   source$.pipe(takeUntil(stop$)).subscribe(console.log);

   setTimeout(() => stop$.next(), 5000); // Stops after 5 seconds

   // Advanced usage (auto-unsubscribe in Angular component)
   class MyComponent {
     private destroy$ = new Subject();

     ngOnInit() {
       interval(1000)
         .pipe(takeUntil(this.destroy$))
         .subscribe(console.log);
     }

     ngOnDestroy() {
       this.destroy$.next();
       this.destroy$.complete();
     }
   }
   ```

### 8. **catchError**
   - **Basic**: Handle errors in an observable.
   - **Advanced**: Retry on failure, then handle error if retries fail.

   ```typescript
   import { of, throwError } from 'rxjs';
   import { catchError, retry } from 'rxjs/operators';

   // Basic usage
   throwError('Error!')
     .pipe(catchError(err => of(`Caught: ${err}`)))
     .subscribe(console.log); // Output: 'Caught: Error!'

   // Advanced usage (retry and then handle error)
   of('Request')
     .pipe(
       mergeMap(_ => throwError('Network error')), // Simulate network error
       retry(3), // Retry up to 3 times
       catchError(err => of(`Failed after retries: ${err}`))
     )
     .subscribe(console.log); // Output after retries fail: 'Failed after retries: Network error'
   ```

### 9. **concatMap**
   - **Basic**: Map each value to an observable, preserving order.
   - **Advanced**: Queue API calls to avoid rate limits.

   ```typescript
   import { of } from 'rxjs';
   import { concatMap, delay } from 'rxjs/operators';

   // Basic usage
   of('A', 'B', 'C')
     .pipe(concatMap(val => of(`${val}1`).pipe(delay(1000))))
     .subscribe(console.log); // Output: 'A1', 'B1', 'C1' with 1 second delay between

   // Advanced usage (rate-limiting API requests)
   const requestQueue$ = of('Request 1', 'Request 2', 'Request 3');
   requestQueue$
     .pipe(
       concatMap(request => fakeApiRequest(request)) // Processes each request sequentially
     )
     .subscribe(console.log);

   function fakeApiRequest(req: string) {
     console.log(`Processing ${req}`);
     return of(`${req} processed`).pipe(delay(1000)); // Simulates 1-second API response
   }
   ```

### 10. **exhaustMap**
   - **Basic**: Ignore new values if a previous observable is still active.
   - **Advanced**: Prevent multiple clicks on a button from causing repeated requests.

   ```typescript
   import { of, interval } from 'rxjs';
   import { exhaustMap, take } from 'rxjs/operators';

   // Basic usage
   of('A', 'B')
     .pipe(exhaustMap(val => interval(1000).pipe(take(2))))
     .subscribe(console.log); // Ignores 'B' since 'A' is not complete

   // Advanced usage (button click prevention)
   const buttonClick$ = of('click');
   buttonClick$
     .pipe(
       exhaustMap(() => fakeLongRequest()) // Ignores further clicks until request completes
     )
     .subscribe(console.log);

   function fakeLongRequest() {
     return of('Request complete').pipe(delay(2000)); // Simulates long request
   }
   ```

### 11. **tap**
   - **Basic**: Perform side effects without altering emitted values.
   - **Advanced**: Log values for debugging and analytics.

   ```typescript
   import { of } from 'rxjs';
   import { tap, map } from 'rxjs/operators';

   // Basic usage
   of(1, 2, 3)
     .pipe(
       tap(val => console.log(`Before map: ${val}`)),
       map(val => val * 10),
       tap(val => console.log(`After map: ${val}`))
     )
     .subscribe(console.log);

   // Advanced usage (logging analytics)
   of('page_view', 'button_click')
     .pipe(
       tap(event => logToAnalytics(event)) // Send event to analytics before further processing
     )
     .subscribe();

   function logToAnalytics(event: string) {
     console.log(`Logging event: ${event}`);
   }
   ```

### 12. **withLatestFrom**
   - **Basic**: Combine values with the latest from another observable only when the source emits.
   - **Advanced**: Combine user input with the latest API data.

   ```typescript
   import { of, interval } from 'rxjs';
   import { withLatestFrom, map } from 'rxjs/operators';

   // Basic usage
   const source$ = interval(1000);
   const latest$ = of(5);
   source$
     .pipe(
       withLatestFrom(latest$),
       map(([source, latest]) => source + latest)
     )
     .subscribe(console.log); // Outputs combined values

   // Advanced usage (combine input with latest API data)
   const userInput$ = of('search term');
   const latestResults$ = of('API result');
   userInput$
     .pipe(
       withLatestFrom(latestResults$), // Combines input with latest API data
       map(([input, results]) => `User searched: ${input}, Results: ${results}`)
     )
     .subscribe(console.log);
   ```

### 13. **startWith**
   - **Basic**: Emit an initial value before actual source values.
   - **Advanced**: Provide a default value for loading state.

   ```typescript
   import { of } from 'rxjs';
   import { startWith } from 'rxjs/operators';

   // Basic usage
   of('A', 'B', 'C')
     .pipe(startWith('Start'))
     .subscribe(console.log); // Output: 'Start', 'A', 'B', 'C'

   // Advanced usage (loading state)
   const data$ = of('Data loaded');
   data$
     .pipe(startWith('Loading...'))
     .subscribe(console.log); // Output: 'Loading...', 'Data loaded'
   ```

### 14. **share**
   - **Basic**: Share the same observable among subscribers.
   - **Advanced**: Avoid multiple HTTP calls by sharing API response.

   ```typescript
   import { of } from 'rxjs';
   import { share, map } from 'rxjs/operators';

   // Basic usage
   const shared$ = of('Shared data').pipe(share());
   shared$.subscribe(console.log); // Output: 'Shared data' (only called once)

   // Advanced usage (shared API response)
   const apiResponse$ = of({ id: 1, name: 'Alice' }).pipe(share());
   apiResponse$.subscribe(console.log); // First subscriber
   apiResponse$.subscribe(console.log); // Second subscriber, no new request
   ```

### 15. **retryWhen**
   - **Basic**: Retry on error based on custom logic.
   - **Advanced**: Exponential backoff on retry.

   ```typescript
   import { of, throwError, timer } from 'rxjs';
   import { retryWhen, delay, mergeMap } from 'rxjs/operators';

   // Basic usage
   throwError('Error!')
     .pipe(
       retryWhen(errors =>
         errors.pipe(delay(1000)) // Retry after 1 second on error
       )
     )
     .subscribe(console.log, console.error);

   // Advanced usage (exponential backoff)
   const source$ = throwError('Network error');
   source$
     .pipe(
       retryWhen(errors =>
         errors.pipe(
           mergeMap((error, i) => {
             const retryAttempt = i + 1;
             if (retryAttempt > 3) {
               return throwError(`Failed after ${retryAttempt} attempts`);
             }
             console.log(`Retrying in ${retryAttempt} second(s)...`);
             return timer(retryAttempt * 1000); // Exponential delay
           })
         )
       )
     )
     .subscribe(console.log, console.error);
   ```

### 16. **zip**
   - **Basic**: Combine values from multiple observables into tuples.
   - **Advanced**: Wait for paired data from different sources.

   ```typescript
   import { of, zip } from 'rxjs';

   // Basic usage
   const obs1$ = of(1, 2, 3);
   const obs2$ = of('A', 'B', 'C');
   zip(obs1$, obs2$).subscribe(console.log); // Output: [1, 'A'], [2, 'B'], [3, 'C']

   // Advanced usage (paired data from different sources)
   const ids$ = of(101, 102, 103);
   const names$ = of('Alice', 'Bob', 'Charlie');
   zip(ids$, names$)
     .pipe(map(([id, name]) => ({ id, name })))
     .subscribe(console.log); // Output: { id: 101, name: 'Alice' }, etc.
   ```

### 17. **reduce**
   - **Basic**: Accumulate values into a single result and emit it on completion.
   - **Advanced**: Calculate totals or summaries from a stream of values.

   ```typescript
   import { of } from 'rxjs';
   import { reduce } from 'rxjs/operators';

   // Basic usage
   of(1, 2, 3, 4)
     .pipe(reduce((acc, value) => acc + value, 0))
     .subscribe(console.log); // Output: 10 (sum of values)

   // Advanced usage (calculate total cost of items)
   const items$ = of({ price: 10 }, { price: 15 }, { price: 20 });
   items$
     .pipe(reduce((acc, item) => acc + item.price, 0))
     .subscribe(console.log); // Output: 45 (total price)
   ```

### 18. **scan**
   - **Basic**: Accumulate values over time and emit each intermediate result.
   - **Advanced**: Track running totals or maintain state over time.

   ```typescript
   import { of } from 'rxjs';
   import { scan } from 'rxjs/operators';

   // Basic usage
   of(1, 2, 3)
     .pipe(scan((acc, value) => acc + value, 0))
     .subscribe(console.log); // Output: 1, 3, 6 (running total)

   // Advanced usage (track user score over time)
   const scoreChanges$ = of(10, -5, 15);
   scoreChanges$
     .pipe(scan((totalScore, change) => totalScore + change, 0))
     .subscribe(console.log); // Output: 10, 5, 20 (score progress)
   ```

### 19. **delayWhen**
   - **Basic**: Delay emissions based on another observable.
   - **Advanced**: Delay API calls based on user actions.

   ```typescript
   import { of, timer } from 'rxjs';
   import { delayWhen } from 'rxjs/operators';

   // Basic usage
   of('A', 'B', 'C')
     .pipe(delayWhen(() => timer(1000))) // Delays each by 1 second
     .subscribe(console.log);

   // Advanced usage (wait until user confirms before making an API call)
   const apiCall$ = of('API call result');
   const userConfirmed$ = timer(3000); // Simulate 3-second delay for user confirmation
   apiCall$
     .pipe(delayWhen(() => userConfirmed$))
     .subscribe(console.log); // Only emits after userConfirmed$ completes
   ```

### 20. **partition**
   - **Basic**: Split values into two groups based on a condition.
   - **Advanced**: Separate even and odd values or filter errors vs. success responses.

   ```typescript
   import { of, partition } from 'rxjs';

   // Basic usage
   const [evens$, odds$] = partition(of(1, 2, 3, 4, 5), value => value % 2 === 0);
   evens$.subscribe(val => console.log('Even:', val)); // Output: 2, 4
   odds$.subscribe(val => console.log('Odd:', val));   // Output: 1, 3, 5

   // Advanced usage (separating success and error responses)
   const responses$ = of({ success: true, data: 'A' }, { success: false, error: 'Error' });
   const [success$, error$] = partition(responses$, res => res.success);

   success$.subscribe(res => console.log('Success:', res.data)); // Output: 'A'
   error$.
   ```

### 21. **groupBy**
   - **Basic**: Group values by a key and process each group individually.
   - **Advanced**: Create group summaries (e.g., total cost by category).

   ```typescript
   import { of } from 'rxjs';
   import { groupBy, mergeMap, reduce } from 'rxjs/operators';

   // Basic usage
   of(
     { id: 1, category: 'A' },
     { id: 2, category: 'B' },
     { id: 3, category: 'A' }
   )
     .pipe(
       groupBy(item => item.category),
       mergeMap(group$ => group$.pipe(reduce((acc, cur) => [...acc, cur], [])))
     )
     .subscribe(console.log); // Output: [{id: 1, ...}, {id: 3, ...}], [{id: 2, ...}]

   // Advanced usage (calculate total by category)
   const items$ = of(
     { category: 'fruit', price: 1 },
     { category: 'vegetable', price: 2 },
     { category: 'fruit', price: 3 }
   );
   items$
     .pipe(
       groupBy(item => item.category),
       mergeMap(group$ =>
         group$.pipe(
           reduce((acc, item) => acc + item.price, 0),
           mergeMap(total => of({ category: group$.key, total }))
         )
       )
     )
     .subscribe(console.log); // Output: {category: 'fruit', total: 4}, {category: 'vegetable', total: 2}
   ```

### 22. **bufferTime**
   - **Basic**: Collect values within a time window and emit as an array.
   - **Advanced**: Group multiple quick user inputs into a batch for processing.

   ```typescript
   import { interval } from 'rxjs';
   import { bufferTime } from 'rxjs/operators';

   // Basic usage
   interval(500)
     .pipe(bufferTime(2000))
     .subscribe(console.log); // Output: [0,1,2,...] every 2 seconds

   // Advanced usage (batching user inputs)
   const userInput$ = interval(500).pipe(bufferTime(2000));
   userInput$.subscribe(batch => {
     console.log(`Processing batch:`, batch);
     // Process inputs as a single batch
   });
   ```

### 23. **windowCount**
   - **Basic**: Emit windows of values based on count, outputting observables.
   - **Advanced**: Apply cumulative calculations to batches of values.

   ```typescript
   import { interval } from 'rxjs';
   import { windowCount, mergeMap, reduce } from 'rxjs/operators';

   // Basic usage
   interval(500)
     .pipe(
       windowCount(3),
       mergeMap(window$ => window$.pipe(reduce((acc, val) => [...acc, val], [])))
     )
     .subscribe(console.log); // Output: [0,1,2], [3,4,5], etc.

   // Advanced usage (calculate batch totals)
   interval(500)
     .pipe(
       windowCount(5),
       mergeMap(window$ =>
         window$.pipe(reduce((acc, value) => acc + value, 0)) // Sum of each 5-value window
       )
     )
     .subscribe(console.log); // Output: 10, 35, 60, etc. (sum of each window)
   ```

### 24. **pairwise**
   - **Basic**: Emit pairs of consecutive values.
   - **Advanced**: Detect value changes and apply transformation based on previous value.

   ```typescript
   import { of } from 'rxjs';
   import { pairwise } from 'rxjs/operators';

   // Basic usage
   of(1, 2, 3, 4)
     .pipe(pairwise())
     .subscribe(console.log); // Output: [1, 2], [2, 3], [3, 4]

   // Advanced usage (detect upward/downward trends)
   of(100, 101, 105, 102, 108)
     .pipe(
       pairwise(),
       map(([prev, curr]) => (curr > prev ? 'up' : 'down'))
     )
     .subscribe(console.log); // Output: 'up', 'up', 'down', 'up'
   ```

### 25. **throttleTime**
   - **Basic**: Limit the rate of emitted values within a time frame.
   - **Advanced**: Use in scroll events to reduce API calls.

   ```typescript
   import { fromEvent } from 'rxjs';
   import { throttleTime } from 'rxjs/operators';

   // Basic usage
   const clicks$ = fromEvent(document, 'click');
   clicks$.pipe(throttleTime(1000)).subscribe(console.log); // Emits clicks every 1 second

   // Advanced usage (scroll events for infinite loading)
   const scroll$ = fromEvent(window, 'scroll');
   scroll$
     .pipe(throttleTime(500))
     .subscribe(() => {
       // Load more content if scrolled near bottom
       if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
         console.log('Load more content');
       }
     });
   ```



---

### 26. fromEvent
- **Usage**: Creates an Observable that emits events of a specific type from an Angular component template element.
- **Example**:
    ```typescript
    import { Component, ElementRef, ViewChild, AfterViewInit } from '@angular/core';
    import { fromEvent } from 'rxjs';

    @Component({
      selector: 'app-example',
      template: `<button #myButton>Click Me</button>`
    })
    export class ExampleComponent implements AfterViewInit {
      @ViewChild('myButton', { static: true }) button!: ElementRef;

      ngAfterViewInit(): void {
        fromEvent(this.button.nativeElement, 'click').subscribe(() => {
          console.log('Button clicked!');
        });
      }
    }
    ```

---

### 27. interval
- **Usage**: Creates an Observable that emits sequential numbers every specified interval of time.
- **Example**:
    ```typescript
    import { interval } from 'rxjs';

    interval(1000).subscribe(value => console.log(value)); // Emits a number every second
    ```

---


### 28. startWith
- **Usage**: Emits the specified initial values before any values emitted by the source Observable.
- **Example**:
    ```typescript
    import { of } from 'rxjs';
    import { startWith } from 'rxjs/operators';

    of(2, 3).pipe(
      startWith(1)
    ).subscribe(value => console.log(value)); // Output: 1, 2, 3
    ```

---

### 29. take
- **Usage**: Takes only the first N values from an Observable and then completes.
- **Example**:
    ```typescript
    import { of } from 'rxjs';
    import { take } from 'rxjs/operators';

    of(1, 2, 3, 4, 5).pipe(
      take(3)
    ).subscribe(value => console.log(value)); // Output: 1, 2, 3
    ```



## Additional Angular Features Beyond RxJS Operators

Angular has introduced several innovative features recently to improve reactivity, component lifecycle management, and lazy loading. Here’s a closer look at more of these features:

### 1. **Signals** (Angular 16+)
   - **What it is**: Signals are a reactivity model for managing component state, inspired by reactive programming concepts.
   - **Use case**: Provides a simpler way to manage local state in components, ideal for controlling reactivity without manual change detection.

   ```typescript
   import { signal, computed, effect } from '@angular/core';

   // Define a signal
   const counter = signal(0);

   // Computed signal
   const doubleCounter = computed(() => counter() * 2);

   // Effect on signal
   effect(() => {
     console.log(`Counter value: ${counter()}`);
   });

   // Update the signal
   counter.set(counter() + 1); // increments counter by 1
   ```

### 2. **toSignal**
   - **What it is**: Converts an observable to a signal for seamless integration with Angular’s signal-based reactivity model.
   - **Use case**: Ideal for integrating RxJS streams or service observables with components using signals.

   ```typescript
   import { interval } from 'rxjs';
   import { toSignal } from '@angular/core/rxjs-interop';

   const interval$ = interval(1000); // emits every second
   const intervalSignal = toSignal(interval$);

   effect(() => {
     console.log(`Interval signal value: ${intervalSignal()}`);
   });
   ```

### 3. **defer Directive**
   - **What it is**: A structural directive that defers rendering of components until specific conditions are met, like visibility or data readiness.
   - **Use case**: Ideal for improving performance by lazy-loading content.

   ```html
   <ng-container *defer="isVisible">
     <app-heavy-component></app-heavy-component>
   </ng-container>
   ```

### 4. **destroyRef** (Component/Directive Destruction Event)
   - **What it is**: `DestroyRef` is a new injectable that allows for executing cleanup code when a component or directive is destroyed.
   - **Use case**: Useful for managing resources like WebSockets, non-Angular observables, or event listeners.

   ```typescript
   import { DestroyRef } from '@angular/core';

   constructor(private destroyRef: DestroyRef) {
     const myResource = openResource();

     this.destroyRef.onDestroy(() => {
       myResource.close(); // Clean up when destroyed
     });
   }
   ```

### 5. **Signal Store (Experimental)**
   - **What it is**: Angular’s experimental state management solution with signals.
   - **Use case**: Provides a simple API for state management without needing traditional stores or observables.

   ```typescript
   import { signal } from '@angular/core';

   class CounterStore {
     count = signal(0);

     increment() {
       this.count.set(this.count() + 1);
     }
   }

   const store = new CounterStore();
   effect(() => {
     console.log(`Count: ${store.count()}`);
   });

   store.increment(); // Count updates to 1
   ```

### 6. **inject Function**
   - **What it is**: A utility function for injecting dependencies outside of Angular components.
   - **Use case**: Ideal for dependency injection in utility functions or standalone components.

   ```typescript
   import { inject } from '@angular/core';
   import { MyService } from './my-service';

   const myService = inject(MyService);
   myService.doSomething();
   ```

### 7. **Host Directives**
   - **What it is**: Host Directives allow you to add multiple directive functionalities directly to a component.
   - **Use case**: Useful for applying shared behaviors or styling directly to components without cluttering templates.

   ```typescript
   import { Directive, HostBinding } from '@angular/core';

   @Directive({
     selector: '[highlight]'
   })
   export class HighlightDirective {
     @HostBinding('style.backgroundColor') bgColor = 'yellow';
   }

   @Component({
     selector: 'app-example',
     hostDirectives: [HighlightDirective]
   })
   export class ExampleComponent {}
   ```

### 8. **Standalone Components with Providers**
   - **What it is**: Allows defining providers directly within standalone components.
   - **Use case**: Simplifies dependency injection for components without NgModules.

   ```typescript
   import { Component } from '@angular/core';
   import { MyService } from './my-service';

   @Component({
     selector: 'app-standalone',
     standalone: true,
     providers: [MyService]
   })
   export class StandaloneComponent {
     constructor(private myService: MyService) {
       this.myService.doSomething();
     }
   }
   ```

---

Each of these operators and Angular features gives you powerful tools for managing state, reactivity, and performance. Replace any `...` with additional details as needed.
