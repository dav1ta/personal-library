### Core Programming Concepts (Basics)

**1. What is a variable? How do you declare one in your favorite language?**  
A variable is a named storage that holds data. In JavaScript, you declare one using `let x = 5;` or `const y = "text";`.

**2. What is the difference between == and === in JavaScript?**  
`==` compares values after type coercion, while `===` compares both value and type strictly without coercion.

**3. Explain the concept of immutability.**  
Immutability means once a data structure is created, it cannot be altered. Operations produce new copies rather than modifying the original, leading to predictable behavior and easier debugging.

**4. What is a function? How is it different from a method?**  
A function is a reusable block of code. A method is a function tied to an object or class, operating on the data contained within that object.

**5. What is recursion? Provide an example.**  
Recursion is when a function calls itself.  
Example in JavaScript:  
```js
function factorial(n) {
  if(n <= 1) return 1;
  return n * factorial(n - 1);
}
```

**6. Explain pass-by-value and pass-by-reference.**  
Pass-by-value copies the actual value to the function, so changes don’t affect the original. Pass-by-reference passes the address, so modifications affect the original object.

**7. What are primitive data types in your language of choice?**  
In JavaScript: `Number`, `String`, `Boolean`, `Null`, `Undefined`, `Symbol`, and `BigInt`.

**8. How do you swap two variables without using a temporary variable?**  
Using destructuring in JavaScript:  
```js
[a, b] = [b, a];
```

**9. What are null and undefined?**  
`null` is an intentional assignment for no value. `undefined` means a variable has been declared but not assigned a value.

**10. What is the difference between a stack and a queue?**  
A stack is LIFO (last-in, first-out) while a queue is FIFO (first-in, first-out).

### Object-Oriented Programming (OOP)

**1. What is the difference between a class and an object?**  
A class is a blueprint for creating objects. An object is an instance of a class containing actual data.

**2. Define encapsulation.**  
Encapsulation bundles data and methods operating on that data within a class, restricting direct access to some components to protect integrity.

**3. What is inheritance? Provide an example.**  
Inheritance allows a class to acquire properties and methods from another.  
Example:  
```js
class Animal { }
class Dog extends Animal { }
```

**4. What is polymorphism?**  
Polymorphism lets methods do different things based on the object they are acting on, typically through method overriding.

**5. What are abstract classes?**  
Abstract classes cannot be instantiated and often contain abstract methods that must be implemented by subclasses.

**6. What is the difference between an interface and an abstract class?**  
An interface defines a contract with no implementation, while an abstract class can provide base implementations and state.

**7. How does method overloading differ from method overriding?**  
Overloading: same method name, different parameters in the same class. Overriding: subclass provides a specific implementation of a method defined in a superclass.

**8. What are getters and setters? Why are they useful?**  
Getters and setters are methods to read/write property values, allowing control over how properties are accessed or modified.

**9. Explain the purpose of constructors in classes.**  
Constructors initialize new objects, setting up initial state and resource allocation.

**10. What is multiple inheritance, and why can it be problematic?**  
Multiple inheritance allows a class to inherit from multiple classes. It can cause ambiguity (diamond problem) and increased complexity.

### Data Structures

**1. How does a hash table work?**  
It uses a hash function to map keys to indices in an array, allowing fast insertion, deletion, and lookup.

**2. What are the differences between an array and a linked list?**  
Arrays have fixed size, contiguous memory, fast access by index. Linked lists have dynamic size, non-contiguous memory, slow random access but fast insertions/deletions.

**3. How does a binary search tree differ from a binary tree?**  
A binary search tree (BST) orders nodes: left child < parent < right child, enabling efficient search.

**4. What is a graph? Explain directed vs. undirected graphs.**  
A graph is a set of nodes connected by edges. Directed graphs have one-way edges; undirected graphs have bidirectional edges.

**5. What is a stack, and where is it used?**  
A stack is a LIFO structure used in function call management, expression evaluation, and backtracking algorithms.

**6. What is a circular linked list?**  
A circular linked list’s last node points back to the first, forming a loop.

**7. Explain a heap and its applications.**  
A heap is a tree-based structure satisfying the heap property (max or min). Used in priority queues and heap sort.

**8. What is a trie, and how is it used?**  
A trie is a tree for efficient retrieval of keys, often used for autocomplete and dictionary implementations.

**9. What is a priority queue?**  
A priority queue is an abstract data type where each element has a priority, and elements are dequeued based on priority.

**10. How do you balance a binary search tree?**  
Through algorithms like AVL or Red-Black trees that perform rotations to maintain height balance.

### Algorithms

**1. What is the difference between breadth-first search (BFS) and depth-first search (DFS)?**  
BFS explores neighbors level by level. DFS dives deep along a branch before backtracking.

**2. How do you find the maximum subarray sum?**  
Use Kadane’s algorithm to iterate and track local and global maximum sums.

**3. Explain the divide-and-conquer approach with an example.**  
Divide problem into subproblems, solve recursively, and combine. Example: Merge sort splits arrays, sorts halves, and merges them.

**4. What is the time complexity of binary search?**  
O(log n)

**5. What is dynamic programming? Provide an example.**  
It's solving problems by combining solutions to subproblems, storing results to avoid redundancy. Example: Fibonacci with memoization.

**6. How would you reverse a linked list?**  
Iterate through the list adjusting pointers.  
```js
let prev = null;
while(node) {
  let next = node.next;
  node.next = prev;
  prev = node;
  node = next;
}
```

**7. How do you find duplicates in an array?**  
Use a hash set to track seen elements; duplicates appear when adding fails.

**8. Write pseudocode to sort an array using merge sort.**  
```
function mergeSort(arr):
    if length(arr) <= 1:
        return arr
    mid = length(arr) / 2
    left = mergeSort(arr[0:mid])
    right = mergeSort(arr[mid:])
    return merge(left, right)
```

**9. Explain the concept of memoization.**  
Memoization caches function results by input to avoid repeated calculations for the same inputs.

**10. How would you check if a string is a palindrome?**  
Compare the string to its reverse. If equal, it's a palindrome.

### Database

**1. What is normalization in databases?**  
Process of structuring data to reduce redundancy and improve integrity through normal forms.

**2. What are the differences between primary key and foreign key?**  
Primary key uniquely identifies a record. A foreign key references a primary key in another table establishing relations.

**3. How does indexing work?**  
Indexes create a data structure to quickly locate rows, speeding up queries at the cost of extra storage and slower writes.

**4. What is a stored procedure?**  
A saved set of SQL statements on the database server to perform operations, improving performance and reusability.

**5. Explain ACID properties in a database.**  
Atomicity, Consistency, Isolation, Durability – guarantees reliable transactions.

**6. What are the differences between SQL and NoSQL databases?**  
SQL uses structured schema and relational tables. NoSQL is schema-less, uses various models (document, key-value, etc.), and scales horizontally.

**7. How do you perform a join in SQL?**  
Using JOIN clauses to combine rows from two or more tables based on a related column.  
```sql
SELECT * FROM A INNER JOIN B ON A.id = B.a_id;
```

**8. What is sharding, and why is it used?**  
Sharding partitions data across multiple databases or servers to improve scalability and performance.

**9. What is a deadlock in database transactions?**  
A situation where two or more transactions permanently block each other by holding locks the others need.

**10. How do you optimize a slow SQL query?**  
Use proper indexing, query refactoring, analyzing execution plans, and adjusting schema design.

### System Design

**1. How do you design a URL shortener like Bitly?**  
Use a database for mappings, encode IDs into short strings, handle collisions, and scale with caching and sharding.

**2. What is load balancing, and how does it work?**  
Distributes network or application traffic across servers to ensure reliability and efficiency.

**3. What is a microservices architecture?**  
Breaks an app into loosely coupled, independently deployable services focused on specific functions.

**4. How would you handle rate limiting in an API?**  
Implement counters or token buckets per user/IP with time windows, using middleware or API gateways.

**5. Explain the CAP theorem.**  
In a distributed system, you can only guarantee two of three: Consistency, Availability, Partition tolerance.

**6. What is caching, and how do you implement it?**  
Store frequently accessed data temporarily in a fast storage layer to reduce load times. Use in-memory caches like Redis or browser caches.

**7. How would you design a distributed file storage system?**  
Use replicated storage nodes, metadata servers, consistent hashing, and fault tolerance strategies.

**8. What are the differences between monolithic and distributed systems?**  
Monolithic: single codebase, tightly coupled. Distributed: multiple services, decoupled, scalable independently.

**9. How would you design a messaging queue system?**  
Use producers, consumers, brokers with persistence, ordering guarantees, and scaling via partitioning.

**10. What are some techniques for database scaling?**  
Vertical scaling, horizontal sharding, replication, caching, and load balancing.

### Web Development

**1. What is the difference between frontend and backend development?**  
Frontend deals with UI/UX in the browser; backend handles server logic, databases, and APIs.

**2. Explain the DOM.**  
The Document Object Model is a tree structure representing HTML/XML documents, allowing programmatic access.

**3. What is CORS, and why does it exist?**  
Cross-Origin Resource Sharing defines how browsers and servers share resources across different origins securely.

**4. What is the purpose of cookies, local storage, and session storage?**  
They store client-side data: cookies for small data with HTTP support; localStorage for persistent key-value data; sessionStorage for data lasting a browser session.

**5. What are the differences between HTTP and HTTPS?**  
HTTPS adds encryption (TLS/SSL) to HTTP for secure data transmission.

**6. What are WebSockets?**  
A protocol for full-duplex communication channels over a single TCP connection, enabling real-time data exchange.

**7. How do you prevent XSS attacks?**  
Sanitize user input, encode outputs, use Content Security Policies, and avoid inline scripts.

**8. What is the difference between client-side and server-side rendering?**  
Client-side rendering generates HTML in the browser using JavaScript. Server-side rendering produces complete HTML on the server for faster initial load and SEO.

**9. Explain how a REST API works.**  
Uses HTTP methods (GET, POST, PUT, DELETE) to manipulate resources identified by URLs, statelessly exchanging data typically in JSON.

**10. What is GraphQL, and how does it differ from REST?**  
GraphQL is a query language for APIs allowing clients to request exactly the data they need, reducing over-fetching compared to REST.

### Security

**1. What is SQL injection, and how can it be prevented?**  
A code injection attack using malicious SQL. Prevent by using parameterized queries, ORM frameworks, and input validation.

**2. What is cross-site scripting (XSS)?**  
Injection of malicious scripts into webpages viewed by others. Prevent by escaping outputs and validating inputs.

**3. How do you securely store passwords?**  
Hash passwords with a strong algorithm (bcrypt, Argon2) and salt them to protect against brute force.

**4. What is a man-in-the-middle attack?**  
An attacker intercepts communication between two parties to steal or alter data. Mitigate with encryption and certificate validation.

**5. What is token-based authentication?**  
Use tokens (JWTs, OAuth tokens) to verify identity instead of sessions, enabling stateless authentication.

**6. How does HTTPS secure communication?**  
Uses TLS/SSL to encrypt data between client and server, ensuring confidentiality and integrity.

**7. What is the purpose of OAuth?**  
OAuth authorizes third-party apps to access user data without sharing credentials, using token delegation.

**8. What is CSRF, and how can it be prevented?**  
Cross-Site Request Forgery tricks users into submitting unwanted actions. Prevent with CSRF tokens, same-site cookies, and validation.

**9. Explain public key cryptography.**  
Uses paired keys: public for encryption/verification, private for decryption/signing, enabling secure communication without shared secrets.

**10. What are JWTs (JSON Web Tokens), and how are they used?**  
JWTs are compact, self-contained tokens that transmit information securely between parties, often used for authentication.

### DevOps & Tools

**1. What is CI/CD?**  
Continuous Integration/Continuous Deployment: automated building, testing, and deploying software for rapid delivery.

**2. How does Docker work?**  
Docker packages applications and dependencies into containers using OS-level virtualization for consistency across environments.

**3. What is Kubernetes, and why is it used?**  
An orchestration tool for automating deployment, scaling, and management of containerized applications.

**4. Explain the purpose of a reverse proxy.**  
A reverse proxy forwards client requests to backend servers, adding load balancing, caching, security, and abstraction.

**5. What is the difference between scaling vertically and horizontally?**  
Vertical scaling increases resources of a single machine; horizontal scaling adds more machines to distribute load.

**6. What is infrastructure as code?**  
Managing and provisioning infrastructure using code and automation tools, ensuring reproducibility and version control.

**7. How do you monitor server health?**  
Use metrics (CPU, memory, disk usage), logs, uptime checks, and monitoring tools like Prometheus, Grafana, or New Relic.

**8. What are the benefits of using a cloud provider like AWS?**  
Scalability, reliability, diverse services, pay-as-you-go, and global infrastructure without hardware management.

**9. How would you debug a failing deployment?**  
Check logs, monitor resource usage, verify configurations, use version control to rollback, and isolate issues methodically.

**10. What are environment variables, and why are they important?**  
Configuration values set outside code, allowing separation of configuration from code and secure management of secrets.

### Advanced Topics

**1. What is a race condition?**  
A flaw where outcomes depend on the timing of threads/processes, leading to unpredictable results.

**2. How do you handle deadlocks?**  
Avoid circular wait by ordering resource acquisition, use timeouts, lock hierarchies, or deadlock detection algorithms.

**3. What is event-driven programming?**  
A paradigm where flow is determined by events (user actions, messages), and handlers respond to these asynchronously.

**4. What is garbage collection in programming?**  
Automatic reclamation of memory by identifying and freeing objects no longer in use.

**5. Explain the purpose of design patterns like Singleton or Factory.**  
They provide reusable solutions to common design problems—Singleton ensures a single instance; Factory abstracts object creation.

**6. How do you profile and optimize performance in an application?**  
Use profiling tools to identify bottlenecks, optimize algorithms, refactor code, and use caching or concurrency where needed.

**7. What is multithreading, and how do you manage thread safety?**  
Running multiple threads concurrently. Ensure safety with locks, mutexes, atomic operations, and avoiding shared mutable state.

**8. What is the purpose of a message broker?**  
A mediator that routes messages between services, decoupling systems, improving scalability and reliability.

**9. How does a blockchain work at a high level?**  
A decentralized ledger where transactions are grouped in blocks, linked cryptographically, and validated by consensus.

**10. What is eventual consistency, and where is it used?**  
A consistency model where updates propagate gradually, so all nodes eventually converge. Used in distributed databases for high availability.



### Programming Fundamentals

**1. What are constants, and how are they different from variables?**  
Constants are immutable values that cannot be reassigned after declaration. Variables can change their value over time.

**2. Explain the difference between compilation and interpretation.**  
Compilation translates source code to machine code before execution. Interpretation executes code line by line at runtime without producing a separate machine code file.

**3. How do you define a function in your language of choice?**  
In JavaScript:  
```js
function greet(name) {
  return `Hello, ${name}!`;
}
```

**4. What is the purpose of the return statement in a function?**  
It exits the function and optionally returns a value to the caller.

**5. What are default parameters in a function?**  
Default parameters assign a default value to a function parameter if no argument is provided.  
```js
function greet(name = 'Guest') { ... }
```

**6. Explain the difference between while and for loops.**  
A `while` loop runs until a condition is false, often used when iteration count is unknown. A `for` loop is typically used for a known number of iterations.

**7. What is a ternary operator?**  
A shorthand conditional operator: `condition ? exprIfTrue : exprIfFalse`.

**8. How do you handle type conversion in your preferred language?**  
In JavaScript, use functions like `Number()`, `String()`, or `Boolean()` for explicit conversion.

**9. What are higher-order functions?**  
Functions that take other functions as arguments or return functions as results.

**10. What is a lambda or anonymous function?**  
A function defined without a name, often used as a callback. In JavaScript:  
```js
const add = (a, b) => a + b;
``` 

### Intermediate Programming Concepts

**1. How does a switch statement work?**  
It compares a value against multiple cases and executes corresponding code blocks until a break is encountered.  
```js
switch(value) {
  case 1: doSomething(); break;
  default: doDefault();
}
```

**2. What is a callback function?**  
A function passed as an argument to another function to be executed later.

**3. What are closures in programming?**  
A closure is a function that captures variables from its surrounding scope even after that scope has finished executing.

**4. Explain the difference between deep copy and shallow copy.**  
A shallow copy duplicates only the top-level structure; nested objects are shared. A deep copy replicates all nested structures, creating independent copies.

**5. What is tail recursion?**  
A recursion where the recursive call is the last operation in the function, enabling optimization by reusing stack frames.

**6. What is the difference between synchronous and asynchronous programming?**  
Synchronous code executes sequentially, blocking further execution until complete. Asynchronous code allows other operations to run while waiting for tasks to complete.

**7. What are named and optional arguments?**  
Named arguments explicitly specify parameter names during function calls. Optional arguments are parameters with default values, not required in every call.

**8. Explain the purpose of the yield keyword.**  
`yield` pauses a generator function, returning a value and preserving state for resumption.

**9. How do you implement a custom iterator?**  
Define an object with a `next()` method returning `{ value, done }` or implement `[Symbol.iterator]()` returning such an object in JavaScript.

**10. What is a generator function?**  
A function that can pause execution (`yield`) and resume later, producing a sequence of values.  
```js
function* gen() { yield 1; yield 2; }
```

### Data Structures (Different Perspective)

**1. What is a deque, and how does it work?**  
A deque (double-ended queue) allows insertion and removal from both ends.

**2. Explain the difference between singly and doubly linked lists.**  
Singly linked lists have nodes with a pointer to the next node. Doubly linked lists have pointers to both next and previous nodes.

**3. What is an adjacency matrix?**  
A 2D array representing graph edges, where `matrix[i][j]` indicates edge presence between vertices `i` and `j`.

**4. How do you traverse a tree level by level?**  
Use breadth-first search (BFS) with a queue to process nodes level by level.

**5. What is a hash collision, and how do you resolve it?**  
When two keys hash to the same index. Resolutions include chaining (linked lists) or open addressing (probing).

**6. What is a skip list?**  
A probabilistic data structure that allows fast search by maintaining multiple linked lists at different levels to skip through elements.

**7. How does a self-balancing tree like AVL work?**  
AVL trees rebalance after insertions/deletions using rotations, maintaining height differences ≤1 between subtrees.

**8. What is a B-tree, and where is it used?**  
A balanced tree optimized for disk reads/writes, used in databases and file systems to handle large data blocks.

**9. How do you detect a cycle in a linked list?**  
Use Floyd’s Tortoise and Hare algorithm: two pointers moving at different speeds; if they meet, there's a cycle.

**10. What is the difference between immutable and mutable data structures?**  
Immutable structures cannot be altered after creation; mutable ones can change state.

### Algorithms (Variety)

**1. How does bubble sort work?**  
Repeatedly steps through the list, compares adjacent elements, and swaps them if out of order until sorted.

**2. What is quicksort, and why is it efficient?**  
A divide-and-conquer sort that partitions data around a pivot, sorting subarrays recursively. It's efficient due to average-case O(n log n) time.

**3. Explain Dijkstra’s algorithm.**  
An algorithm for finding the shortest path in a weighted graph by iteratively selecting the closest unvisited vertex and updating distances.

**4. How do you find the shortest path in an unweighted graph?**  
Use breadth-first search (BFS), which finds the shortest path in terms of edges.

**5. Write pseudocode for binary search.**  
```
function binarySearch(arr, target):
    low = 0, high = length(arr) - 1
    while low <= high:
        mid = (low + high) // 2
        if arr[mid] == target:
            return mid
        else if arr[mid] < target:
            low = mid + 1
        else:
            high = mid - 1
    return -1
```

**6. What is the purpose of a greedy algorithm?**  
It makes locally optimal choices at each step aiming for a global optimum.

**7. What is the difference between Prim’s and Kruskal’s algorithms?**  
Both find minimum spanning trees: Prim’s grows one tree, picking minimum edges from the tree to new vertices; Kruskal’s sorts edges and adds them if they don’t form a cycle, potentially creating a forest.

**8. How would you find the longest common substring of two strings?**  
Use dynamic programming to build a table of common suffix lengths or suffix tree/trie for optimized search.

**9. What is the Floyd-Warshall algorithm?**  
A dynamic programming algorithm for finding shortest paths between all pairs of vertices in a weighted graph.

**10. Explain backtracking with an example.**  
Backtracking tries possible solutions recursively, undoing choices if they lead to dead ends. Example: solving a maze by exploring paths and backtracking upon hitting walls.

### Object-Oriented Programming (Different Viewpoints)

**1. What is a mixin?**  
A mixin is a class providing methods that can be used by other classes without using inheritance, adding reusable functionality.

**2. What is the purpose of a static method?**  
A static method belongs to the class itself, not instances, used for utility functions related to the class.

**3. How do you implement a singleton pattern?**  
Ensure a class has only one instance and provide a global access point:  
```js
class Singleton {
  constructor() {
    if(Singleton.instance) return Singleton.instance;
    Singleton.instance = this;
  }
}
```

**4. What is a decorator in OOP?**  
A design pattern that adds behavior to objects dynamically without altering their structure.

**5. How does polymorphism improve code flexibility?**  
It allows objects of different classes to be treated as instances of a common superclass, enabling interchangeable use and easier code extension.

**6. What is duck typing, and which languages use it?**  
Duck typing determines an object’s suitability by methods/properties rather than type. Languages like Python and JavaScript use duck typing.

**7. What are virtual functions?**  
In OOP languages like C++, virtual functions allow derived classes to override methods to provide specialized behavior.

**8. Explain the difference between composition and inheritance.**  
Inheritance models “is-a” relationships, extending classes. Composition models “has-a” relationships, building complex types by combining simpler ones.

**9. What is method chaining?**  
A technique where methods return the object itself, allowing multiple method calls in a single statement:  
```js
obj.setA(1).setB(2);
```

**10. How do you handle diamond inheritance issues?**  
Use interfaces or mixins, or languages with virtual inheritance (C++) to avoid duplicate base class instances.

### Web Development (Variety)

**1. How does the internet work?**  
A global network of interconnected devices using standardized protocols (TCP/IP) to send/receive data.

**2. What is a web server?**  
A system that hosts websites and serves web pages to clients via HTTP/HTTPS.

**3. Explain the difference between a GET and POST request.**  
GET requests retrieve data, typically idempotent; POST submits data to be processed, causing state changes.

**4. What is a status code in HTTP? Provide examples.**  
Numeric codes indicating request outcome. Examples:  
- 200 OK (success)  
- 404 Not Found (resource missing)  
- 500 Internal Server Error (server fault)

**5. How does a browser render a webpage?**  
It downloads HTML, CSS, JS, constructs the DOM and CSSOM, applies styles, executes scripts, and paints pixels on screen.

**6. What are HTML, CSS, and JavaScript?**  
HTML structures content, CSS styles it, and JavaScript adds interactivity.

**7. What is the difference between inline, internal, and external CSS?**  
Inline CSS applies styles directly in an element. Internal CSS is defined in a `<style>` block in HTML. External CSS resides in separate files linked to HTML.

**8. What is the box model in CSS?**  
A layout model consisting of content, padding, border, and margin defining element size and spacing.

**9. How does a browser’s event loop work?**  
It continuously checks the call stack and callback queue, executing tasks sequentially to handle asynchronous events without blocking.

**10. What is DOM manipulation?**  
Changing the Document Object Model via scripts to alter webpage content, structure, or style dynamically.

### Advanced Topics

**1. What are design principles like SOLID?**  
SOLID principles (Single Responsibility, Open-Closed, Liskov Substitution, Interface Segregation, Dependency Inversion) guide maintainable, scalable OOP design.

**2. What is the observer pattern?**  
A design pattern where subjects maintain a list of observers notified of state changes.

**3. How does reactive programming work?**  
It builds systems reacting to data streams and propagation of change, often using observables and event streams.

**4. What is the difference between a monad and a functor?**  
A functor applies a function over wrapped values. A monad extends a functor with the ability to chain operations that return wrapped values.

**5. What is the purpose of memoization in functional programming?**  
To cache function results for given inputs, avoiding repeated calculations and improving performance.

**6. What are pure functions?**  
Functions that return the same output for the same inputs and cause no side effects.

**7. How do you implement event sourcing?**  
Capture all changes as events, storing them sequentially, and reconstruct state by replaying events.

**8. What is eventual consistency, and why is it important?**  
A model where updates propagate over time, ensuring all nodes converge eventually—crucial for availability in distributed systems.

**9. Explain the concept of immutability in functional programming.**  
Data is never modified after creation; instead, new data structures are created for changes, ensuring predictability.

**10. What is lazy evaluation?**  
Deferring computation until its result is needed, potentially improving performance by avoiding unnecessary calculations.

### Database (Advanced)

**1. What is a materialized view?**  
A stored query result that can be refreshed, providing faster read performance at the cost of storage.

**2. How does indexing improve query performance?**  
Indexes allow quick lookup of rows matching criteria, reducing full table scans.

**3. What is the difference between a clustered and non-clustered index?**  
A clustered index sorts and stores data rows in the table based on key values. A non-clustered index stores pointers to the data rows.

**4. How do you enforce constraints in a database?**  
Using SQL constraints like PRIMARY KEY, FOREIGN KEY, UNIQUE, CHECK, and NOT NULL to enforce rules on data.

**5. What are triggers in SQL?**  
Special procedures automatically executed in response to certain events on a table or view.

**6. What is the difference between row-level and column-level locking?**  
Row-level locks restrict individual rows; column-level locking restricts specific columns, though less common.

**7. How do you perform database migrations?**  
Use migration tools/scripts to version and apply schema changes incrementally without data loss.

**8. What is database replication?**  
Copying and maintaining database objects in multiple databases for redundancy and high availability.

**9. Explain the difference between vertical and horizontal partitioning.**  
Vertical partitioning splits a table by columns. Horizontal partitioning divides rows across tables or databases.

**10. What is the purpose of database transactions?**  
To group multiple operations into a single, atomic unit of work, ensuring ACID properties.

### System Design (Advanced)

**1. What is a content delivery network (CDN)?**  
A distributed network of servers delivering cached web content to users based on geographic proximity, improving speed and reliability.

**2. How do you design a rate limiter?**  
Implement counters or token buckets per user/IP with time windows to limit request rates, using in-memory stores or distributed caches.

**3. What is a distributed hash table?**  
A decentralized system providing a lookup service, mapping keys to values across nodes, enabling scalable peer-to-peer networks.

**4. How would you design a search engine?**  
Crawl web pages, index content, rank results, and handle queries efficiently using distributed systems and algorithms.

**5. What are API gateways?**  
Service that sits between clients and microservices, handling request routing, composition, authentication, rate limiting, and protocol translation.

**6. How do you handle failover in distributed systems?**  
Implement redundancy, health checks, automatic switching to backup systems, and data replication to ensure continuity.

**7. What is data consistency, and how do you achieve it?**  
Ensuring all users see the same data across systems. Achieve with transactions, consensus algorithms, or consistency models like strong or eventual consistency.

**8. How do you implement fault tolerance?**  
Design systems to continue operation despite failures using redundancy, graceful degradation, retries, and fallback mechanisms.

**9. What is quorum in distributed systems?**  
A minimum number of nodes required to agree on an operation, ensuring consistency and reliability in distributed consensus.

**10. How do you design a logging system?**  
Centralize logs from services, use structured formats, aggregate with collectors, store in scalable storage, and provide search/analysis tools.

### DevOps & Tools (Different)

**1. What is a build pipeline?**  
An automated sequence of steps to compile, test, and package code, ensuring reliable software builds.

**2. How do you containerize an application?**  
Use Docker to create an image with the app and dependencies, defining behavior in a Dockerfile, then run containers from that image.

**3. What is Helm in Kubernetes?**  
A package manager for Kubernetes, using charts to define, install, and upgrade complex Kubernetes applications.

**4. How does a service mesh work?**  
A dedicated infrastructure layer managing service-to-service communication, handling routing, security, and observability without modifying application code.

**5. What are the benefits of serverless computing?**  
Abstract server management, automatic scaling, pay-per-use billing, and focus on code rather than infrastructure.

**6. How do you implement blue-green deployment?**  
Maintain two identical environments (blue & green), switch traffic from old to new version seamlessly after deployment, reducing downtime.

**7. What is a monitoring stack, and what tools are commonly used?**  
A set of tools to collect, visualize, and alert on system metrics/logs. Common tools: Prometheus, Grafana, ELK stack (Elasticsearch, Logstash, Kibana).

**8. How does Terraform differ from Ansible?**  
Terraform focuses on provisioning and managing infrastructure as code. Ansible is used for configuration management and orchestration tasks.

**9. How do you manage secrets in a DevOps pipeline?**  
Use vault services (HashiCorp Vault, AWS Secrets Manager), environment variables secured in CI/CD, and encrypted secret storage.

**10. What is the 12-factor app methodology?**  
A set of principles for building scalable, maintainable web apps, emphasizing codebase, dependencies, config, backing services, build/release/run, processes, port binding, concurrency, disposability, dev/prod parity, logs, and admin processes.



### Programming Fundamentals

**1. What are constants in programming, and why are they used?**  
Constants are fixed values that cannot be changed during program execution. They improve code readability, maintainability, and prevent accidental modifications.

**2. How does integer overflow occur?**  
When an arithmetic operation exceeds the maximum representable value of an integer type, the value wraps around, leading to incorrect results.

**3. What is the purpose of a type system?**  
A type system enforces constraints on the kinds of data that can be used, catching errors early, improving code reliability, and aiding documentation and optimization.

**4. What is the difference between a strongly typed and weakly typed language?**  
Strongly typed languages enforce strict type rules, reducing unexpected behavior. Weakly typed languages allow more implicit type conversions, which can lead to subtle bugs.

**5. Explain the difference between compile-time and runtime errors.**  
Compile-time errors are detected during code compilation (syntax errors, type mismatches). Runtime errors occur during program execution (null reference, division by zero).

**6. What is an enum, and where is it useful?**  
An enum defines a set of named constants, making code more readable and less error-prone when dealing with a fixed set of related values.

**7. How do you check if a number is even or odd programmatically?**  
Use modulo operator:  
```js
if(number % 2 === 0) { /* even */ } else { /* odd */ }
```

**8. What are global variables, and why are they discouraged?**  
Global variables are accessible throughout the program. They are discouraged due to potential naming conflicts, unintended side effects, and maintenance difficulties.

**9. What is a type alias?**  
A type alias gives a new name to an existing type, improving code readability and abstraction without creating new types.

**10. How does short-circuit evaluation work?**  
Logical operators (`&&`, `||`) evaluate operands left-to-right and stop as soon as the outcome is determined, skipping unnecessary evaluations.

### Intermediate Programming Concepts

**1. What is the difference between a thread and a process?**  
A process is an independent execution unit with its own memory space; a thread is a lightweight unit of execution within a process sharing its memory.

**2. How do you reverse a string in your preferred language?**  
In JavaScript:  
```js
let reversed = str.split('').reverse().join('');
```

**3. What is tail-call optimization?**  
An optimization that reuses stack frames for tail-recursive calls, preventing stack overflow and reducing memory usage.

**4. How do you implement a stack using an array?**  
Use array methods: `push()` to add elements (push) and `pop()` to remove elements (pop), following LIFO order.

**5. What is the difference between mutable and immutable objects?**  
Mutable objects can change after creation; immutable objects cannot, ensuring state consistency.

**6. What is the purpose of the finally block in exception handling?**  
The `finally` block executes code regardless of exceptions, used for cleanup tasks like closing files or releasing resources.

**7. How do you implement a circular queue?**  
Use an array with pointers for head and tail that wrap around when reaching the end, managing enqueue/dequeue operations accordingly.

**8. What are bitwise operators, and where are they used?**  
Operators that perform bit-level manipulation (`&, |, ^, ~, <<, >>`). They are used in low-level programming, optimizations, and bit flags.

**9. How do you check for memory leaks in an application?**  
Use profiling tools, monitor memory usage over time, check for unreleased resources, and analyze heap snapshots to identify leaks.

**10. What is the difference between synchronous and blocking calls?**  
Synchronous calls wait for completion before proceeding. Blocking calls halt execution until they finish, which may occur in synchronous or asynchronous contexts.

### Object-Oriented Programming

**1. What is a destructor, and when is it called?**  
A destructor is a method invoked when an object is about to be destroyed, used to release resources.

**2. How do you create an abstract method in your language of choice?**  
In Java, declare an abstract method in an abstract class:  
```java
abstract class Shape { abstract void draw(); }
```

**3. What are access modifiers, and why are they important?**  
Access modifiers (public, private, protected) control visibility of class members, enforcing encapsulation and protecting data integrity.

**4. What is dependency injection?**  
A design pattern where dependencies are provided to a class rather than created inside it, enhancing testability and flexibility.

**5. How do you achieve method overriding in your language?**  
Subclass defines a method with the same signature as in the parent class, optionally using annotations (e.g., `@Override` in Java).

**6. Explain the concept of "composition over inheritance."**  
Favoring building classes by composing objects with desired behaviors over inheriting from parent classes, promoting flexibility and modularity.

**7. What is a virtual destructor, and why is it needed?**  
A virtual destructor ensures proper resource cleanup when deleting an object through a base class pointer, crucial in polymorphic classes.

**8. What are final classes or methods?**  
Classes/methods declared `final` cannot be subclassed/overridden, enforcing immutability of behavior and structure.

**9. How does the concept of "open/closed principle" work in OOP?**  
Software entities should be open for extension but closed for modification, allowing new features without altering existing code.

**10. What are object pooling and its benefits?**  
Object pooling reuses instances instead of creating new ones, improving performance, reducing memory churn, and lowering garbage collection overhead.

### Data Structures

**1. What is a min heap, and how is it implemented?**  
A min heap is a binary tree where the parent is less than or equal to its children. It's implemented using arrays with algorithms for insertion (bubble-up) and removal (bubble-down).

**2. How do you traverse a binary tree in post-order?**  
Visit left subtree, then right subtree, then root.  
```js
function postOrder(node) {
  if(!node) return;
  postOrder(node.left);
  postOrder(node.right);
  process(node);
}
```

**3. What is the difference between adjacency lists and adjacency matrices?**  
Adjacency list: each vertex stores a list of neighbors. Adjacency matrix: 2D array indicating edges between vertex pairs.

**4. How do you detect a cycle in a directed graph?**  
Use depth-first search (DFS) with recursion stack tracking. If a node is revisited in the current path, a cycle exists.

**5. What is a Bloom filter, and how does it work?**  
A space-efficient probabilistic structure to test set membership with possible false positives. It uses multiple hash functions to set/check bits in a bit array.

**6. What is a Fibonacci heap?**  
A heap data structure with better amortized operations, using a collection of trees, supporting decrease-key in O(1) amortized time.

**7. How do you merge two sorted linked lists?**  
Iterate both lists, compare heads, append the smaller to a new list, and continue until all nodes are merged.

**8. What is the difference between a binary heap and a binomial heap?**  
Binary heap: a single tree structure. Binomial heap: a collection of binomial trees that allow faster merge operations.

**9. How do you implement a hash map from scratch?**  
Use an array of buckets, a hash function to map keys to indices, and collision resolution (chaining or open addressing) for key-value storage.

**10. What is the difference between a red-black tree and an AVL tree?**  
Both are self-balancing trees. AVL trees are more strictly balanced, offering faster lookups but slower inserts/deletes. Red-black trees are more relaxed, yielding faster modifications.

### Algorithms

**1. How does counting sort work?**  
It counts occurrences of each value in the input, then calculates positions based on cumulative counts, producing a sorted output in linear time given a limited range.

**2. What is the difference between a stable and an unstable sorting algorithm?**  
Stable sorts preserve the relative order of equal elements; unstable sorts may not.

**3. Explain the traveling salesman problem.**  
A NP-hard problem asking for the shortest route that visits each city exactly once and returns to the origin, optimizing travel cost.

**4. How does the Knuth-Morris-Pratt algorithm work for string searching?**  
KMP preprocesses the pattern to create a prefix table, then uses it to skip re-examining characters on mismatches, achieving efficient search.

**5. What is the Rabin-Karp algorithm?**  
A string search algorithm using hashing to find any set of pattern strings in a text, efficiently checking potential matches via hash comparisons.

**6. How do you calculate the power of a number without using the pow function?**  
Use exponentiation by squaring: recursively square base and reduce exponent, handling even/odd exponents.

**7. How do you find the least common multiple (LCM) of two numbers?**  
Compute LCM(a, b) = |a * b| / GCD(a, b), using the greatest common divisor.

**8. What is the Bellman-Ford algorithm?**  
An algorithm to find shortest paths from a single source in a weighted graph, handling negative weights, relaxing edges repeatedly.

**9. How do you generate permutations of a string?**  
Recursively swap characters and generate permutations for remaining substring, backtracking after each recursion.

**10. Explain the concept of amortized time complexity.**  
Average time per operation over a sequence, smoothing out costly operations with cheaper ones to guarantee overall efficiency.

### Web Development

**1. What is a single-page application (SPA)?**  
A web app that loads a single HTML page and dynamically updates content without full page reloads, offering a smoother user experience.

**2. How do you optimize images for web performance?**  
Compress images, use appropriate formats, resize to needed dimensions, lazy-load, and use responsive images.

**3. What is a service worker?**  
A script running in the background of a browser, enabling offline capabilities, caching, and background sync.

**4. How does lazy loading work in web applications?**  
Defers loading of non-critical resources (images, scripts) until needed, improving initial load time and performance.

**5. What is the difference between async and defer in script tags?**  
`async` downloads the script asynchronously and executes immediately when ready. `defer` downloads asynchronously but executes after HTML parsing.

**6. How does browser caching work?**  
Stores resources locally based on HTTP headers, reducing network requests for subsequent visits.

**7. What is the difference between a relative and an absolute URL?**  
Relative URLs specify a path relative to the current location; absolute URLs include the full path including protocol and domain.

**8. What are webhooks, and how are they used?**  
Webhooks are HTTP callbacks triggered by events, allowing one application to notify another in real-time.

**9. Explain the difference between a session and a token-based authentication.**  
Sessions store user state on the server with session IDs, while token-based authentication uses stateless tokens (e.g., JWT) exchanged with each request.

**10. How do you prevent clickjacking?**  
Use X-Frame-Options or Content Security Policy headers to prevent your pages from being framed by other sites.

### Security

**1. What is a replay attack, and how do you prevent it?**  
A replay attack intercepts and resends valid data transmissions. Prevent with nonces, timestamps, or sequence numbers to ensure uniqueness.

**2. What are salted hashes, and why are they important?**  
Salting adds random data to passwords before hashing, preventing attackers from using precomputed tables to reverse hashes.

**3. How do you ensure secure data transmission over HTTP?**  
Use HTTPS/TLS to encrypt data in transit, validating certificates and using strong cipher suites.

**4. What is the principle of least privilege?**  
Granting only the minimum necessary permissions to users or processes, reducing risk if compromised.

**5. How does two-factor authentication (2FA) work?**  
Requires two forms of verification (e.g., password plus OTP) to improve account security.

**6. What is the difference between symmetric and asymmetric encryption?**  
Symmetric uses the same key for encryption/decryption; asymmetric uses a public-private key pair for secure communication.

**7. How do you secure a REST API?**  
Use HTTPS, authenticate/authorize requests (OAuth, JWT), validate inputs, rate-limit, and sanitize data.

**8. What is a buffer overflow?**  
A vulnerability where a program writes more data to a buffer than it can hold, potentially overwriting adjacent memory.

**9. Explain the concept of a honeypot in cybersecurity.**  
A decoy system set up to attract attackers, gathering intelligence while diverting them from real assets.

**10. What is DNS poisoning?**  
An attack that corrupts DNS records, redirecting traffic to malicious sites.

### Databases

**1. What is a surrogate key in databases?**  
A system-generated unique identifier for a record, not derived from application data.

**2. How do you implement a full-text search in a database?**  
Use database-specific features (like SQL Server Full-Text Search, PostgreSQL tsvector) or external indexing engines (Elasticsearch) to index and search text.

**3. What is database indexing, and what are its drawbacks?**  
Indexing creates a data structure to speed up queries. Drawbacks include increased storage, slower writes, and maintenance overhead.

**4. Explain eventual consistency in distributed databases.**  
A consistency model where updates propagate gradually, so that all nodes will converge to the same state given time.

**5. What is a composite key?**  
A key composed of multiple columns that uniquely identify a record.

**6. How does a bitmap index work?**  
Uses bitmaps for each distinct value in a column, mapping row presence. Efficient for read-heavy, low-cardinality data.

**7. What are the differences between OLTP and OLAP?**  
OLTP handles transactional processing with many short operations. OLAP supports analytical queries on large datasets with complex operations.

**8. What is a materialized view?**  
A precomputed, stored query result that can be refreshed, speeding up read operations.

**9. How do you perform a database rollback?**  
Use transaction control (e.g., `ROLLBACK`) to undo changes within a transaction if an error occurs.

**10. What is the difference between normalization and denormalization?**  
Normalization reduces redundancy by splitting data. Denormalization combines data for performance, increasing redundancy.

### System Design

**1. How do you design a notification system?**  
Design a scalable publisher-subscriber model with message queues, user preferences, delivery channels (email, SMS, push), and real-time updates.

**2. What is a distributed cache, and how does it work?**  
A cache spread across multiple machines, storing frequently accessed data for fast retrieval and reducing load on databases.

**3. How do you design a high-availability database system?**  
Use replication, clustering, failover mechanisms, data partitioning, and backup strategies to ensure minimal downtime.

**4. What is a circuit breaker pattern?**  
A design that stops making requests to a failing service, allowing it time to recover, and preventing cascading failures.

**5. How do you manage configuration in large-scale systems?**  
Centralized configuration services, environment variables, configuration files, and dynamic updates with versioning.

**6. What is the purpose of an API rate limiter?**  
To restrict the number of requests a client can make in a time period, preventing abuse and ensuring fair resource usage.

**7. How do you design a recommendation engine?**  
Collect user data, use collaborative/filtering algorithms or machine learning models, scale with data processing pipelines, and personalize suggestions.

**8. What is sharding, and how does it differ from partitioning?**  
Sharding splits a database horizontally across servers. Partitioning divides data logically, which may occur on a single server or across systems.

**9. How would you handle write-heavy systems?**  
Optimize for concurrency, use write-optimized databases, queue writes, scale horizontally, employ caching, and batch operations.

**10. How do you design a chat system?**  
Use real-time messaging protocols (WebSockets), message queues, user presence indicators, scalable storage for history, and ensure low latency.

### Advanced Programming Topics

**1. What is speculative execution in CPUs?**  
CPUs predict future instructions and execute them ahead of time, increasing performance but potentially causing security issues like Spectre.

**2. How does garbage collection work in Java?**  
Java’s GC periodically identifies unreachable objects and frees memory, using algorithms like mark-and-sweep or generational collection.

**3. What is a coroutine, and how is it different from a thread?**  
Coroutines are lightweight, cooperative units of execution that yield control explicitly, whereas threads are managed by the OS and preemptively scheduled.

**4. What are memory barriers, and why are they used?**  
Memory barriers enforce ordering of memory operations, critical in multi-threaded programming to prevent unexpected behavior due to reordering.

**5. How does dependency resolution work in package managers?**  
It analyzes required packages, their versions, and dependencies, resolving conflicts to install a consistent set of packages.

**6. What is the difference between transactional and eventual consistency?**  
Transactional consistency provides immediate, atomic consistency. Eventual consistency guarantees that, over time, all nodes converge to the same state.

**7. What is the purpose of zero-copy in networking?**  
Zero-copy techniques avoid extra data copying between buffers to improve performance and reduce CPU usage during data transfer.

**8. What is a software interrupt?**  
A programmatic interrupt triggered by software instructions to invoke OS services, differing from hardware interrupts triggered by devices.

**9. Explain the actor model in distributed systems.**  
A concurrency model where independent “actors” communicate via message passing, encapsulating state and behavior to avoid shared state issues.

**10. How do you implement an LRU (Least Recently Used) cache?**  
Use a combination of a hash map for fast lookups and a doubly linked list to track recency. On access, move item to front; on insertion, remove least recently used from tail if capacity exceeded.
