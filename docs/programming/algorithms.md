# Advanced Programming Algorithms

## 1. Sliding Window

The sliding window is a technique that involves creating a "window" in your data and then "sliding" it in a certain direction to perform operations on the data within the window.

Here's a Python example of finding the maximum sum of a subarray of size `k` in an array:

```python
def max_sub_array_of_size_k(k, arr):
    max_sum , window_sum = 0, 0
    window_start = 0

    for window_end in range(len(arr)):
        window_sum += arr[window_end]  # add the next element

        # slide the window, we don't need to slide if we've not hit the required window size of 'k'
        if window_end >= k-1:
            max_sum = max(max_sum, window_sum)
            window_sum -= arr[window_start]  # subtract the element going out
            window_start += 1  # slide the window ahead

    return max_sum
```
Real world example: Sliding window algorithms can be used in data stream processing to calculate rolling metrics, such as a moving average.

## 2. Two Pointers

Two Pointers is a pattern where two pointers iterate through the data structure in tandem until one or both of the pointers meet a certain condition.

Python example for reversing a string:

```python
def reverse_string(s):
    left, right = 0, len(s) - 1
    while left < right:
        # Swap s[left] and s[right]
        s[left], s[right] = s[right], s[left]
        left, right = left + 1, right - 1
    return s
```

Real world example: Two pointers can be used in situations where you have to find pairs of elements that meet a certain condition, like in a music playlist to match songs of certain lengths together.

## 3. Binary Search

Binary Search is a divide and conquer algorithm used to find a specific item in a sorted array.

Python example:

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid  # target found
        if arr[mid] < target:
            left = mid + 1  # search in the right half
        else:
            right = mid - 1  # search in the left half
    return -1  # target not found
```
Real world example: Binary search is used in debugging (e.g., git bisect) to find faulty code commit. 


## 4. Fast and Slow Pointers

The Fast and Slow pointer approach, also known as the Hare and Tortoise algorithm, is used to determine if a linked list is a circular linked list.

Python example:

```python
class Node:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next

def has_cycle(head):
    slow, fast = head, head
    while fast is not None and fast.next is not None:
        fast = fast.next.next
        slow = slow.next
        if slow == fast:
            return True  # found the cycle
    return False
```
Real world example: This algorithm can be used to detect cycles in a computer network.

## 5. Merge Intervals

Merge Intervals is a problem where given a collection of intervals, we need to merge all overlapping intervals.

Python example:

```python
def merge(intervals):
    if len(intervals) < 2:
        return intervals

    # sort the intervals on the start time
    intervals.sort(key=lambda x: x[0])

    mergedIntervals = []
    start = intervals[0][0]
    end = intervals[0][1]
    for i in range(1, len(intervals)):
        if intervals[i][0] <= end:  # overlapping intervals
            end = max(end, intervals[i][1])  # adjust the 'end'
        else:  # non-overlapping interval
            mergedIntervals.append([start, end])
            start = intervals[i][0]
            end = intervals[i][1]

    # add the last interval
    mergedIntervals.append([start, end])
    return mergedIntervals
```
Real world example: In calendar systems, to find free or busy time slots, we need to merge all overlapping intervals.

## 6. Top K Elements

To find the top 'K' elements among a given set. This pattern can be easily recognized from questions such as "find the top K numbers" or "find the most frequent K numbers".

Python example:

```python
import heapq

def find_k_largest_numbers(nums, k):
    minHeap = []
    # put first 'K' numbers in the min heap
    for i in range(k):
        heapq.heappush(minHeap, nums[i])

    # go through the remaining numbers of the array, if the number from the array is bigger than the
    # top(smallest) number of the min-heap, remove the top number from heap and add the number from array
    for i in range(k, len(nums)):
        if nums[i] > minHeap[0]:
            heapq.heappop(minHeap)
            heapq.heappush(minHeap, nums[i])

    # the heap has the top 'K' numbers, return them in a list
    return list(minHeap)
```
Real world example: Top K elements can be used in real-time online voting results, showing only top K candidates.

## 7. K-way Merge

K-way merge pattern is an efficient way to merge data from multiple sources. The pattern works by comparing the smallest elements of each source and repeatedly choosing the smallest until there are no more elements left.

Python example (Merging K Sorted Lists):

```python
import heapq
from typing import List

class ListNode:
    def __init__(self, value, next=None):
        self.value = value
        self.next = next

def merge_lists(lists: List[ListNode]) -> ListNode:
    min_heap = []

    # put the root of each list in the min heap
    for root in lists:
        if root is not None:
            heapq.heappush(min_heap, (root.val, root))

    # take the smallest (top) element from the min-heap and add it to the result
    # if the top element has a next element add it to the heap
    prehead = point = ListNode(-1)
    while min_heap:
        val, node = heapq.heappop(min_heap)
        point.next = ListNode(val)
        point = point.next
        node = node.next
        if node is not None:
            heapq.heappush(min_heap, (node.val, node))

    return prehead.next
```
Real world example: K-way merge is used in big data processing for merging large datasets from multiple sources.

## 8. Breadth-First Search (BFS)

BFS is an algorithm for traversing or searching tree or graph data structures. It starts at the tree root and explores all of the neighbor nodes at the present depth prior to moving on to nodes at the next depth level.

Python example:

```python
from collections import deque

def bfs(graph, root):
    visited = set()
    queue = deque([root])

    while queue:
        vertex = queue.popleft()
        print(str(vertex) + " ", end="")

        # add neighbours to the queue
        for neighbour in graph[vertex]:
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append(neighbour)
```
Real world example: BFS is often used in AI for finding the shortest path in a graph (like Google Maps to find shortest route).

## 9. Depth-First Search (DFS)

DFS is an algorithm for traversing or searching tree or graph data structures. The algorithm starts at the root node and explores as far as possible along each branch before backtracking.

Python example:

```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)

    print(start, end=' ')

    for next in graph[start] - visited:
        dfs(graph, next, visited)
    return visited
```
Real world example: DFS can be used to solve puzzles such as mazes.


## 10. Backtracking

Backtracking is a strategy for finding all (or some) solutions to computational problems, notably constraint satisfaction problems, by incrementally building candidates to the solutions, and abandoning a candidate as soon as it's determined that it cannot be extended to a valid solution.

Python example (Generating all possible permutations of a list):

```python
def generate_permutations(nums):
    def backtrack(start):
        # if we are at the end of the array, we have a complete permutation
        if start == len(nums):
            output.append(nums[:])
            return
        for i in range(start, len(nums)):
            # swap the current index with the start
            nums[start], nums[i] = nums[i], nums[start]
            # continue building the permutation
            backtrack(start + 1)
            # undo the swap
            nums[start], nums[i] = nums[i], nums[start]

    output = []
    backtrack(0)
    return output
```
Real world example: Backtracking is used in many algorithms for searching and constraint satisfaction problems, such as Sudoku.

## 11. Dynamic Programming (DP)

Dynamic Programming is a method for solving a complex problem by breaking it down into simpler subproblems, solving each of those subproblems just once, and storing their solutions to avoid duplicate work.

Python example (Finding the nth Fibonacci number):

```python
def fibonacci(n):
    dp = [0, 1] + [0]*(n-1)
    for i in range(2, n+1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]
```
Real world example: DP is used in many areas of computer science, such as in optimizing the operation of a network or the performance of a computer program.


## 12. Kadane's Algorithm

Kadane's algorithm is a Dynamic Programming approach to solve "the largest contiguous elements in an array" with runtime of O(n).

Python example:

```python
def max_sub_array(nums):
    if not nums:
        return 0

    cur_sum = max_sum = nums[0]

    for num in nums[1:]:
        cur_sum = max(num, cur_sum + num)
        max_sum = max(max_sum, cur_sum)

    return max_sum
```
Real world example: Kadane's algorithm can be used in computer vision to detect the largest area of a certain color in an image.

## 13. Knapsack Problem

The knapsack problem is a problem in combinatorial optimization. Given a set of items, each with a weight and a value, the goal is to determine the number of each item to include in a collection so that the total weight is less than or equal to a given limit and the total value is as large as possible.

Python example:

```python
def knapSack(W, wt, val, n):
    K = [[0 for w in range(W + 1)]
            for i in range(n + 1)]
             
    # Build table K[][] in bottom
    # up manner
    for i in range(n + 1):
        for w in range(W + 1):
            if i == 0 or w == 0:
                K[i][w] = 0
            elif wt[i - 1] <= w:
                K[i][w] = max(val[i - 1]
                  + K[i - 1][w - wt[i - 1]],
                               K[i - 1][w])
            else:
                K[i][w] = K[i - 1][w]
 
    return K[n][W]
```
Real world example: The knapsack problem appears in resource allocation in computing. For example, given a set of servers, each with a certain capacity and cost, the goal is to find the least costly way to fulfill a client's resource request.


## 14. Tree Depth-First Search

This is an algorithm for traversing or searching tree data structures. The algorithm starts at the root and explores as far as possible along each branch before backtracking.

Python example:

```python
class Node:
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

def dfs(node):
    if node is None:
        return
    print(node.value, end=' ')
    dfs(node.left)
    dfs(node.right)
```
Real world example: DFS can be used in games like chess where you need to forecast player's moves ahead of time.

## 15. Tree Breadth-First Search

This is an algorithm for traversing or searching tree data structures. It starts at the tree root and explores all of the neighbor nodes at the present depth prior to moving on to nodes at the next depth level.

Python example:

```python
from collections import deque

class Node:
    def __init__(self, value, left=None, right=None):
        self.value = value
        self.left = left
        self.right = right

def bfs(root):
    queue = deque([root])
    while queue:
        node = queue.popleft()
        print(node.value, end=' ')
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
```
Real world example: BFS can be used in social networking sites when suggesting people you may know, as it looks at closest connections first.


## 16. Topological Sort

Topological sort is used to find a linear ordering of elements that have dependencies on each other. For instance, if task 'a' is dependent on task 'b', in the sorted order, 'b' comes before 'a'.

Python example:

```python
from collections import defaultdict, deque

def topological_sort(vertices, edges):
    sorted_order = []
    if vertices <= 0:
        return sorted_order

    # a. Initialize the graph
    in_degree = {i: 0 for i in range(vertices)}  # count of incoming edges
    graph = defaultdict(list)  # adjacency list graph

    # b. Build the graph
    for edge in edges:
        parent, child = edge[0], edge[1]
        graph[parent].append(child)  # put the child into parent's list
        in_degree[child] += 1  # increment child's inDegree

    # c. Find all sources i.e., all vertices with 0 in-degrees
    sources = deque()
    for key in in_degree:
        if in_degree[key] == 0:
            sources.append(key)

    # d. For each source, add it to the sortedOrder and subtract one from all of its children's in-degrees
    # if a child's in-degree becomes zero, add it to the sources queue
    while sources:
        vertex = sources.popleft()
        sorted_order.append(vertex)
        for child in graph[vertex]:  # get the node's children to decrement their in-degrees
            in_degree[child] -= 1
            if in_degree[child] == 0:
                sources.append(child)

    # topological sort is not possible as the graph has a cycle
    if len(sorted_order) != vertices:
        return []

    return sorted_order
```
Real world example: Topological Sort can be used in scheduling tasks, determining the order of courses to take, etc.


## 17. Trie

A Trie, also called digital tree and sometimes radix tree or prefix tree, is a kind of search tree—an ordered tree data structure used to store a dynamic set or associative array where the keys are usually strings.

Python example:

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.endOfString = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert_word(self, word):
        current = self.root
        for char in word:
            node = current.children.get(char)
            if not node:
                node = TrieNode()
                current.children[char] = node
            current = node
        current.endOfString = True

    def search_word(self, word):
        current = self.root
        for char in word:
            node = current.children.get(char)
            if not node:
                return False
            current = node
        return current.endOfString
```
Real world example: Tries are used in search engines for text autocompletion.

## 18. Graph - Bipartite Check

A Bipartite graph is a graph whose vertices can be divided into two independent sets, U and V such that every edge (u, v) either connects a vertex from U to V or a vertex from V to U.

Python example:

```python
def is_bipartite(graph):
    color = {}
    for node in range(len(graph)):
        if node not in color:
            stack = [node]
            color[node] = 0
            while stack:
                node = stack.pop()
                for neighbour in graph[node]:
                    if neighbour not in color:
                        stack.append(neighbour)
                        color[neighbour] = color[node] ^ 1
                    elif color[neighbour] == color[node]:
                        return False
    return True
```
Real world example: Bipartite graphs are used in matching algorithms, such as in job allocation where jobs can be matched to job-seekers.


## 19. Bitwise XOR

XOR is a binary operation that takes two bit patterns of equal length and performs the logical exclusive OR operation on each pair of corresponding bits. The result in each position is 1 if only the first bit is 1 OR only the second bit is 1, but will be 0 if both are 0 or both are 1.

Python example:

```python
def find_single_number(arr):
    num = 0
    for i in arr:
        num ^= i
    return num
```
Real world example: Bitwise XOR can be used in cryptography, error detection and correction algorithms.

## 20. Sliding Window - Optimal

The sliding window pattern is used to perform a required operation on a specific window size of a given large dataset or array. This window could either be a subarray or a subset of data that you are taking from a defined set of data.

Python example (Finding maximum sum of a subarray of size 'k'):

```python
def max_sub_array_of_size_k(k, arr):
    max_sum = 0
    window_sum = 0

    for i in range(len(arr) - k + 1):
        window_sum = sum(arr[i:i+k])
        max_sum = max(max_sum, window_sum)

    return max_sum
```
Real world example: The sliding window concept is used in TCP data transmission for flow control and congestion control.


## 21. Quick Sort

QuickSort is a Divide and Conquer algorithm, which picks an element as pivot and partitions the given array around the picked pivot.

Python example:

```python
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + middle + quick_sort(right)
```

## 22. Merge Sort

Merge Sort is a Divide and Conquer algorithm, which works by dividing the unsorted list into n sublists, each containing one element, and then repeatedly merging sublists to produce new sorted sublists until there is only one sublist remaining.

Python example:

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    def merge(left, right):
        if not left:
            return right
        if not right:
            return left
        if left[0] < right[0]:
            return [left[0]] + merge(left[1:], right)
        return [right[0]] + merge(left, right[1:])

    mid = len(arr) // 2
    return merge(merge_sort(arr[:mid]), merge_sort(arr[mid:]))
```

## 23. Heap Sort

Heap sort is a comparison-based sorting algorithm that uses a binary heap data structure. 

Python example:

```python
import heapq

def heap_sort(arr):
    heapq.heapify(arr)
    return [heapq.heappop(arr) for _ in range(len(arr))]
```

## 24. Insertion Sort

Insertion sort is a simple sorting algorithm that works similar to the way you sort playing cards in your hands. The array is virtually split into a sorted and an unsorted region.

Python example:

```python
def insertion_sort(arr):
    for i in range(1, len(arr)):
        key = arr[i]
        j = i - 1
        while j >= 0 and key < arr[j]:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = key
    return arr
```

## 25. Binary Search

Binary search is an efficient algorithm for finding an item from a sorted list of items. It works by repeatedly dividing in half the portion of the list that could contain the item, until you've narrowed down the possible locations to just one.

Python example:

```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

## 26. Breadth-First Search (Graphs)

Breadth-first search (BFS) is an algorithm for traversing or searching tree or graph data structures. It starts at the tree root and explores all of the neighbor nodes at the present depth prior to moving on to nodes at the next depth level.

Python example (Using adjacency list representation of graph):

```python
from collections import deque

def bfs(graph, root):
    visited = set()
    queue = deque([root])

    while queue:
        vertex = queue.popleft()
        print(vertex, end=" ")

        for neighbour in graph[vertex]:
            if neighbour not in visited:
                visited.add(neighbour)
                queue.append(neighbour)
```

## 27. Depth-First Search (Graphs)

Depth-first search (DFS) is an algorithm for traversing or searching tree or graph data structures. The algorithm starts at the root node and explores as far as possible along each branch before backtracking.

Python example (Using adjacency list representation of graph):

```python
def dfs(graph, root, visited=None):
    if visited is None:
        visited = set()
    visited.add(root)
    print(root, end=" ")

    for neighbour in graph[root]:
        if neighbour not in visited:
            dfs(graph, neighbour, visited)
    return visited
```

## 28. Dijkstra's Algorithm

Dijkstra’s algorithm is a shortest path algorithm that works on a weighted graph. The shortest path in this case is based on the weight of the edges.

Python example:

```python
import heapq

def dijkstras(graph, start):
    distances = {node: float('infinity') for node in graph}
    distances[start] = 0
    pq = [(0, start)]

    while pq:
        current_distance, current_node = heapq.heappop(pq)

        if current_distance > distances[current_node]:
            continue

        for neighbour, weight in graph[current_node].items():
            distance = current_distance + weight

            if distance < distances[neighbour]:
                distances[neighbour] = distance
                heapq.heappush(pq, (distance, neighbour))

    return distances
```

## 29. A* Search Algorithm

A* is a graph traversal and path search algorithm, which is often used in many fields of computer science due to its completeness, optimality, and optimal efficiency.

Python example (Implementing A* to solve a 2D grid-based pathfinding problem would be too large to fit here due to the need for a suitable heuristic function and priority queue data structure.)

## 30. Floyd-Warshall Algorithm

The Floyd-Warshall algorithm is a shortest path algorithm for graphs. It's used to find the shortest paths between all pairs of vertices in a graph, which may represent, for example, road networks.

Python example:

```python
def floyd_warshall(graph):
    distance = dict()

    for vertex in graph:
        distance[vertex] = dict()
        for neighbour in graph:
            distance[vertex][neighbour] = graph[vertex][neighbour]

    for intermediate_vertex in graph:
        for vertex in graph:
            for neighbour in graph:
                if distance[vertex][intermediate_vertex] + distance[intermediate_vertex][neighbour] < distance[vertex][neighbour]:
                    distance[vertex][neighbour] = distance[vertex][intermediate_vertex] + distance[intermediate_vertex][neighbour]

    return distance
```


## 31. Knapsack Problem

The knapsack problem is a problem in combinatorial optimization: Given a set of items, each with a weight and a value, determine the number of each item to include in a collection so that the total weight is less than or equal to a given limit and the total value is as large as possible.

Python example (0/1 Knapsack Problem):

```python
def knapSack(W, wt, val, n):
    K = [[0 for w in range(W+1)] for i in range(n+1)]
 
    for i in range(n+1):
        for w in range(W+1):
            if i == 0 or w == 0:
                K[i][w] = 0
            elif wt[i-1] <= w:
                K[i][w] = max(val[i-1] + K[i-1][w-wt[i-1]], K[i-1][w])
            else:
                K[i][w] = K[i-1][w]
 
    return K[n][W]
```

## 32. Travelling Salesman Problem

The travelling salesman problem (TSP) asks the following question: "Given a list of cities and the distances between each pair of cities, what is the shortest possible route that visits each city exactly once and returns to the origin city?"

Python example (Simple approach for TSP):

```python
from itertools import permutations
 
def travellingSalesmanProblem(graph, s):
    vertex = []
    for i in range(len(graph)):
        if i != s:
            vertex.append(i)

    min_path = float('inf')
    next_permutation=permutations(vertex)
    for i in next_permutation:
        current_pathweight = 0

        k = s
        for j in i:
            current_pathweight += graph[k][j]
            k = j
        current_pathweight += graph[k][s]

        min_path = min(min_path, current_pathweight)
         
    return min_path
```

## 33. Kruskal’s Algorithm

Kruskal's algorithm finds a minimum spanning forest of an undirected edge-weighted graph. If the graph is connected, it finds a minimum spanning tree.

Python example (too long to fit due to the need for a disjoint set data structure)

## 34. Prim's Algorithm

Prim's algorithm is a greedy algorithm that finds a minimum spanning tree for a weighted undirected graph.

Python example (too long to fit due to the need for a priority queue data structure)

## 35. Bellman-Ford Algorithm

The Bellman–Ford algorithm is an algorithm that computes shortest paths from a single source vertex to all of the other vertices in a weighted digraph.

Python example:

```python
def bellman_ford(graph, source_vertex):
    distance, predecessor = dict(), dict()

    for node in graph:
        distance[node], predecessor[node] = float('inf'), None
    distance[source_vertex] = 0

    for _ in range(len(graph) - 1):
        for node in graph:
            for neighbour in graph[node]:
                if distance[neighbour] > distance[node] + graph[node][neighbour]:
                    distance[neighbour], predecessor[neighbour] = distance[node] + graph[node][neighbour], node

    for node in graph:
        for neighbour in graph[node]:
            assert distance[neighbour] <= distance[node] + graph[node][neighbour], "Negative Cycle"
            
    return distance, predecessor
```


## 36. Z-algorithm (Pattern Searching)

Z algorithm is a linear time string matching algorithm which runs in O(n) complexity. It is used to find all occurrences of a pattern in a string, which is common string searching problem.

Python example:

```python
def getZarr(string, z):
    n = len(string)
    l, r, k = 0, 0, 0

    for i in range(1, n):
        if i > r:
            l, r = i, i
            while r < n and string[r - l] == string[r]:
                r += 1
            z[i] = r - l
            r -= 1
        else:
            k = i - l
            if z[k] < r - i + 1:
                z[i] = z[k]
            else:
                l = i
                while r < n and string[r - l] == string[r]:
                    r += 1
                z[i] = r - l
                r -= 1
```

## 37. KMP (Knuth Morris Pratt) Pattern Searching

The KMP matching algorithm uses degenerating property (pattern having same sub-patterns appearing more than once in the pattern) of the pattern and improves the worst-case complexity to O(n).

Python example:

```python
def KMPSearch(pat, txt):
    M = len(pat)
    N = len(txt)
    lps = [0]*M
    j = 0
    computeLPSArray(pat, M, lps)
    i = 0
    while i < N:
        if pat[j] == txt[i]:
            i += 1
            j += 1
        if j == M:
            print("Found pattern at index " + str(i-j))
            j = lps[j-1]
        elif i < N and pat[j] != txt[i]:
            if j != 0:
                j = lps[j-1]
            else:
                i += 1
```

## 38. Rabin-Karp Algorithm for Pattern Searching

Rabin-Karp is a pattern searching algorithm to find the pattern in a more efficient way. It checks the pattern by moving window one by one, but without checking all characters for all cases, it finds the hash value. When the hash value is matched, then only it tries to check each character.

Python example:

```python
def search(pattern, txt, q):
    M = len(pattern)
    N = len(txt)
    i = 0
    j = 0
    p = 0
    t = 0
    h = 1
    d = 256
    for i in range(M-1):
        h = (h*d)%q
    for i in range(M):
        p = (d*p + ord(pattern[i]))%q
        t = (d*t + ord(txt[i]))%q
    for i in range(N-M+1):
        if p==t:
            for j in range(M):
                if txt[i+j] != pattern[j]:
                    break
            j+=1
            if j==M:
                print("Pattern found at index " + str(i))
        if i < N-M:
            t = (d*(t-ord(txt[i])*h) + ord(txt[i+M]))%q
            if t < 0:
                t = t+q
```

## 39. Kosaraju's algorithm to find strongly connected components in a graph

Kosaraju's algorithm performs two passes over a graph to identify its strongly connected components. It's used in graph theory to identify clusters or related groups within the graph.

Python example (due to its complexity, the full implementation is not provided here).

## 40. Boyer Moore Algorithm for Pattern Searching

Boyer Moore is a combination of following two approaches.
1) Bad Character Heuristic
2) Good Suffix Heuristic

Python example:

```python
NO_OF_CHARS = 256
def badCharHeuristic(string, size):
    badChar = [-1]*NO_OF_CHARS
    for i in range(size):
        badChar[ord(string[i])] = i;
    return badChar

def search(txt, pat):
    m = len(pat)
    n = len(txt)
    badChar = badCharHeuristic(pat, m)
    s = 0
    while(s <= n-m):
        j = m-1
        while j>=0 and pat[j] == txt[s+j]:
            j -= 1
        if j<0:
            print("Pattern occur at shift = {}".format(s))
            s += (m-badChar[ord(txt[s+m])] if s+m<n else 1)
        else:
            s += max(1, j-badChar[ord(txt[s+j])])
```
