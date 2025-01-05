# Chapter 1: Introduction to Graph Theory

## What is Graph Theory?

Graph theory is a branch of mathematics that studies the relationships between objects. These relationships are represented as **graphs**, which consist of two main components:  
- **Vertices (nodes):** Represent the objects.  
- **Edges (links):** Represent the relationships between the objects.

In Python, graphs can be modeled using various data structures, such as dictionaries, lists, or specialized libraries like **NetworkX**.

### Example: Simple Graph Representation

Below is an example of how to represent a graph in Python using a dictionary:

```python
# Graph represented as an adjacency list
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Printing the neighbors of each vertex
for vertex, neighbors in graph.items():
    print(f"Vertex {vertex}: {neighbors}")
```

**Output:**
```
Vertex A: ['B', 'C']
Vertex B: ['A', 'D']
Vertex C: ['A', 'D']
Vertex D: ['B', 'C']
```

This is a simple example of an **undirected graph**, where edges have no direction.

---

## Historical Background

The origins of graph theory can be traced back to the famous **Seven Bridges of Königsberg** problem, solved by **Leonhard Euler** in 1736. This work laid the foundation for graph theory as a mathematical discipline.

### The Problem

The city of Königsberg (modern-day Kaliningrad) had seven bridges connecting its landmasses. The challenge was to find a walk that would cross each bridge exactly once. Euler proved that such a walk was impossible, introducing the concept of **Eulerian paths**.

### Eulerian Path Example in Python

```python
# Function to check for an Eulerian path
def is_eulerian_path(graph):
    odd_degree_count = sum(1 for node in graph if len(graph[node]) % 2 != 0)
    return odd_degree_count in [0, 2]

# Example graph (adjacency list)
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'C'],
    'C': ['A', 'B', 'D'],
    'D': ['C']
}

print("Has Eulerian Path:" , is_eulerian_path(graph))  # Output: True
```

---

## Applications of Graph Theory

Graph theory has numerous real-world applications across various fields:

1. **Social Networks:** Representing friendships or connections.
   - Example: Facebook uses graphs where nodes are users, and edges are friendships.
2. **Transportation Networks:** Modeling roads, railways, or airline routes.
3. **Computer Science:** Optimizing tasks such as routing, searching, and scheduling.
4. **Biology:** Analyzing networks like food webs or protein interactions.

These applications demonstrate the versatility and importance of graph theory in solving practical problems.

---

## Key Takeaways

- Graphs are powerful tools for modeling relationships between entities.
- Euler's foundational work introduced the idea of paths and cycles in graphs.
- Python provides flexible tools for graph representation and analysis.

Next, we will delve deeper into the **fundamentals of graphs**, including terminology, types of graphs, and their representations.



# Chapter 2: Fundamentals of Graphs

## Definitions and Terminology

Before diving into graph theory, it's essential to understand the basic terms and concepts:

1. **Graph (G):** A set of vertices \( V \) and edges \( E \), represented as \( G = (V, E) \).
2. **Vertex (Node):** A fundamental unit of a graph, typically denoted as \( V = \{v_1, v_2, \dots, v_n\} \).
3. **Edge:** A connection between two vertices, represented as \( E = \{e_1, e_2, \dots, e_m\} \).
4. **Degree:** The number of edges connected to a vertex.  
   - **In-degree:** Number of incoming edges (in directed graphs).
   - **Out-degree:** Number of outgoing edges (in directed graphs).

### Example: Graph Terminology

```python
# Example graph as an adjacency list
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Function to calculate the degree of each vertex
def calculate_degrees(graph):
    degrees = {vertex: len(neighbors) for vertex, neighbors in graph.items()}
    return degrees

print("Degrees of vertices:", calculate_degrees(graph))
```

**Output:**
```
Degrees of vertices: {'A': 2, 'B': 2, 'C': 2, 'D': 2}
```

---

## Types of Graphs

Graphs can be classified into several categories based on their properties:

### 1. Directed and Undirected Graphs

- **Undirected Graph:** Edges have no direction (e.g., \( A \leftrightarrow B \)).
- **Directed Graph (Digraph):** Edges have a direction (e.g., \( A \rightarrow B \)).

#### Python Example: Directed vs. Undirected Graphs

```python
# Undirected graph
undirected_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Directed graph
directed_graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': [],
    'D': ['C']
}

print("Undirected Graph:", undirected_graph)
print("Directed Graph:", directed_graph)
```

---

### 2. Weighted and Unweighted Graphs

- **Unweighted Graph:** All edges are equal.
- **Weighted Graph:** Edges have weights representing costs, distances, or capacities.

#### Python Example: Weighted Graph

```python
# Weighted graph represented as a dictionary
weighted_graph = {
    'A': {'B': 3, 'C': 5},
    'B': {'A': 3, 'D': 4},
    'C': {'A': 5, 'D': 2},
    'D': {'B': 4, 'C': 2}
}

# Function to print weights of edges
for node, edges in weighted_graph.items():
    for neighbor, weight in edges.items():
        print(f"Edge {node} -> {neighbor}, Weight: {weight}")
```

**Output:**
```
Edge A -> B, Weight: 3
Edge A -> C, Weight: 5
Edge B -> A, Weight: 3
Edge B -> D, Weight: 4
Edge C -> A, Weight: 5
Edge C -> D, Weight: 2
Edge D -> B, Weight: 4
Edge D -> C, Weight: 2
```

---

### 3. Simple Graphs and Multigraphs

- **Simple Graph:** A graph without loops or multiple edges between vertices.
- **Multigraph:** A graph that allows multiple edges between the same pair of vertices.

---

## Representations of Graphs

Graphs can be represented in various ways, depending on the application:

### 1. Adjacency Matrix

An \( n \times n \) matrix where \( A[i][j] = 1 \) if there is an edge from vertex \( i \) to \( j \), otherwise \( 0 \).

#### Python Example: Adjacency Matrix

```python
# Example graph
adj_matrix = [
    [0, 1, 1, 0],
    [1, 0, 0, 1],
    [1, 0, 0, 1],
    [0, 1, 1, 0]
]

# Display the adjacency matrix
for row in adj_matrix:
    print(row)
```

**Output:**
```
[0, 1, 1, 0]
[1, 0, 0, 1]
[1, 0, 0, 1]
[0, 1, 1, 0]
```

---

### 2. Adjacency List

A list where each vertex stores its neighbors. This is space-efficient for sparse graphs.

#### Python Example: Adjacency List

```python
# Graph represented as an adjacency list
adj_list = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Display the adjacency list
for vertex, neighbors in adj_list.items():
    print(f"{vertex}: {neighbors}")
```

**Output:**
```
A: ['B', 'C']
B: ['A', 'D']
C: ['A', 'D']
D: ['B', 'C']
```

---

### 3. Incidence Matrix

An \( n \times m \) matrix where rows represent vertices and columns represent edges. \( M[i][j] = 1 \) if vertex \( i \) is incident to edge \( j \).

---

## Key Takeaways

- Graphs can be directed, undirected, weighted, or unweighted.
- Representations include adjacency matrices, adjacency lists, and incidence matrices.
- Python provides flexible tools for implementing and exploring these concepts.

Next, we will explore **graph properties**, including connectedness, paths, cycles, and subgraphs.



# Chapter 3: Graph Properties

## Degree of a Vertex

The **degree** of a vertex is the number of edges connected to it.

- **Undirected Graph:** The degree is simply the count of edges connected to the vertex.  
- **Directed Graph:**  
  - **In-degree:** Number of edges coming into the vertex.  
  - **Out-degree:** Number of edges going out from the vertex.

### Example: Degree Calculation

```python
# Function to calculate degrees in an undirected graph
def calculate_degrees(graph):
    return {vertex: len(neighbors) for vertex, neighbors in graph.items()}

# Example undirected graph
undirected_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

print("Degrees:", calculate_degrees(undirected_graph))

# Function to calculate in-degree and out-degree in a directed graph
def calculate_in_out_degrees(directed_graph):
    in_degrees = {vertex: 0 for vertex in directed_graph}
    out_degrees = {vertex: len(neighbors) for vertex, neighbors in directed_graph.items()}

    for vertex, neighbors in directed_graph.items():
        for neighbor in neighbors:
            in_degrees[neighbor] += 1

    return in_degrees, out_degrees

# Example directed graph
directed_graph = {
    'A': ['B', 'C'],
    'B': ['D'],
    'C': [],
    'D': ['C']
}

in_deg, out_deg = calculate_in_out_degrees(directed_graph)
print("In-degrees:", in_deg)
print("Out-degrees:", out_deg)
```

**Output:**
```
Degrees: {'A': 2, 'B': 2, 'C': 2, 'D': 2}
In-degrees: {'A': 0, 'B': 1, 'C': 2, 'D': 1}
Out-degrees: {'A': 2, 'B': 1, 'C': 0, 'D': 1}
```

---

## Connectedness

A graph is **connected** if there is a path between every pair of vertices.

1. **Connected Graph:** All vertices are reachable from any vertex.
2. **Disconnected Graph:** Contains at least two subsets of vertices with no paths between them.
3. **Strongly Connected (Directed Graphs):** Every vertex is reachable from every other vertex following the edge directions.

### Example: Check Connectedness

```python
# Function to check if a graph is connected
def is_connected(graph):
    visited = set()

    def dfs(node):
        if node not in visited:
            visited.add(node)
            for neighbor in graph[node]:
                dfs(neighbor)

    start_vertex = next(iter(graph))
    dfs(start_vertex)

    return len(visited) == len(graph)

# Example graphs
connected_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

disconnected_graph = {
    'A': ['B'],
    'B': ['A'],
    'C': ['D'],
    'D': ['C']
}

print("Connected Graph:", is_connected(connected_graph))  # True
print("Disconnected Graph:", is_connected(disconnected_graph))  # False
```

---

## Paths and Cycles

- **Path:** A sequence of edges connecting a sequence of vertices.  
  - **Simple Path:** No vertex is repeated.  
- **Cycle:** A path that starts and ends at the same vertex.  
  - **Simple Cycle:** No other vertex is repeated except the starting/ending vertex.

### Example: Find All Paths

```python
# Function to find all paths between two vertices
def find_all_paths(graph, start, end, path=[]):
    path = path + [start]
    if start == end:
        return [path]
    if start not in graph:
        return []

    paths = []
    for neighbor in graph[start]:
        if neighbor not in path:
            new_paths = find_all_paths(graph, neighbor, end, path)
            paths.extend(new_paths)
    return paths

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

print("Paths from A to D:", find_all_paths(graph, 'A', 'D'))
```

**Output:**
```
Paths from A to D: [['A', 'B', 'D'], ['A', 'C', 'D']]
```

---

## Bipartite Graphs

A **bipartite graph** is a graph whose vertices can be divided into two disjoint sets \( U \) and \( V \) such that every edge connects a vertex in \( U \) to one in \( V \).

### Example: Check if a Graph is Bipartite

```python
# Function to check if a graph is bipartite
def is_bipartite(graph):
    color = {}
    queue = []

    start_vertex = next(iter(graph))
    queue.append(start_vertex)
    color[start_vertex] = 0

    while queue:
        vertex = queue.pop(0)
        for neighbor in graph[vertex]:
            if neighbor not in color:
                color[neighbor] = 1 - color[vertex]
                queue.append(neighbor)
            elif color[neighbor] == color[vertex]:
                return False
    return True

# Example graphs
bipartite_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

non_bipartite_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'C'],
    'C': ['A', 'B']
}

print("Is Bipartite:", is_bipartite(bipartite_graph))  # True
print("Is Bipartite:", is_bipartite(non_bipartite_graph))  # False
```

---

## Subgraphs

A **subgraph** is a graph formed from a subset of the vertices and edges of another graph.

### Example: Extract Subgraph

```python
# Function to extract a subgraph
def extract_subgraph(graph, vertices):
    subgraph = {v: [n for n in neighbors if n in vertices] for v, neighbors in graph.items() if v in vertices}
    return subgraph

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Extract subgraph containing vertices A, B, and C
subgraph = extract_subgraph(graph, ['A', 'B', 'C'])
print("Subgraph:", subgraph)
```

**Output:**
```
Subgraph: {'A': ['B', 'C'], 'B': ['A'], 'C': ['A']}
```

---

## Key Takeaways

- The degree of a vertex provides insights into its connectivity.
- Connectedness and cycles help in understanding the overall structure.
- Bipartite graphs and subgraphs are useful in specialized applications.

Next, we will explore **graph traversal algorithms**, including Depth-First Search (DFS) and Breadth-First Search (BFS).




Graph traversal is the process of visiting all the vertices of a graph in a systematic way. It is fundamental for exploring the structure of a graph, solving problems like searching, pathfinding, and connectivity.

## Depth-First Search (DFS)

DFS explores as far as possible along a branch before backtracking. It uses a stack (explicitly or implicitly via recursion).

### Steps of DFS
1. Start at a vertex.
2. Visit the vertex and mark it as visited.
3. Recursively visit all unvisited neighbors.
4. Backtrack when all neighbors are visited.

### Example: DFS Implementation

```python
# Recursive DFS
def dfs_recursive(graph, start, visited=None):
    if visited is None:
        visited = set()
    visited.add(start)
    print(start, end=" ")  # Process the node
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs_recursive(graph, neighbor, visited)

# Iterative DFS
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]

    while stack:
        vertex = stack.pop()
        if vertex not in visited:
            visited.add(vertex)
            print(vertex, end=" ")  # Process the node
            stack.extend(reversed(graph[vertex]))  # Reverse for consistent ordering

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': [],
    'E': ['F'],
    'F': []
}

print("Recursive DFS:")
dfs_recursive(graph, 'A')
print("\nIterative DFS:")
dfs_iterative(graph, 'A')
```

**Output:**
```
Recursive DFS:
A B D E F C
Iterative DFS:
A B D E F C
```

---

## Breadth-First Search (BFS)

BFS explores all neighbors of a vertex before moving to the next level. It uses a queue for tracking vertices.

### Steps of BFS
1. Start at a vertex.
2. Visit the vertex and mark it as visited.
3. Enqueue all unvisited neighbors.
4. Dequeue the next vertex and repeat until the queue is empty.

### Example: BFS Implementation

```python
# BFS using a queue
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])

    while queue:
        vertex = queue.popleft()
        if vertex not in visited:
            visited.add(vertex)
            print(vertex, end=" ")  # Process the node
            queue.extend(graph[vertex])

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': [],
    'E': ['F'],
    'F': []
}

print("BFS:")
bfs(graph, 'A')
```

**Output:**
```
BFS:
A B C D E F
```

---

## Comparison of DFS and BFS

| Aspect                  | DFS                              | BFS                              |
|-------------------------|-----------------------------------|-----------------------------------|
| **Data Structure**      | Stack (or recursion)             | Queue                            |
| **Exploration**         | Explores deeper paths first      | Explores all neighbors first     |
| **Use Cases**           | Pathfinding, Topological Sorting | Shortest Path in Unweighted Graph|
| **Memory Efficiency**   | Efficient for sparse graphs      | May use more memory for dense graphs|

---

## Applications of Traversal Algorithms

1. **Pathfinding:**
   - DFS can find if a path exists between two vertices.
   - BFS can find the shortest path in an unweighted graph.

2. **Cycle Detection:**
   - DFS can detect cycles by checking for back edges.

3. **Connected Components:**
   - Both DFS and BFS can identify connected components in a graph.

4. **Topological Sorting:**
   - DFS is used for ordering vertices in a Directed Acyclic Graph (DAG).

### Example: Detecting Cycles with DFS

```python
# Function to detect cycles in a graph using DFS
def detect_cycle(graph):
    visited = set()
    stack = set()

    def dfs(vertex):
        if vertex in stack:
            return True  # Cycle detected
        if vertex in visited:
            return False
        visited.add(vertex)
        stack.add(vertex)
        for neighbor in graph[vertex]:
            if dfs(neighbor):
                return True
        stack.remove(vertex)
        return False

    for vertex in graph:
        if dfs(vertex):
            return True
    return False

# Example graph with a cycle
graph_with_cycle = {
    'A': ['B'],
    'B': ['C'],
    'C': ['A']
}

# Example graph without a cycle
acyclic_graph = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F'],
    'D': [],
    'E': ['F'],
    'F': []
}

print("Graph with cycle:", detect_cycle(graph_with_cycle))  # True
print("Acyclic graph:", detect_cycle(acyclic_graph))  # False
```

**Output:**
```
Graph with cycle: True
Acyclic graph: False
```

---

## Key Takeaways

- **DFS** and **BFS** are fundamental traversal algorithms, each suited to specific types of problems.
- DFS is depth-oriented and works well for cycle detection and pathfinding.
- BFS is breadth-oriented and ideal for finding the shortest path in unweighted graphs.

Next, we will explore **trees and spanning trees**, a special class of graphs with unique properties and practical applications.




# Chapter 5: Trees and Spanning Trees

A **tree** is a special type of graph that is connected and acyclic. Trees are fundamental in graph theory and computer science due to their hierarchical structure and efficient algorithms.

---

## Properties of Trees

1. A tree with \( n \) vertices has exactly \( n - 1 \) edges.
2. Any two vertices in a tree are connected by exactly one path.
3. Adding an edge to a tree creates a cycle, and removing any edge disconnects the tree.
4. A tree is a minimally connected graph.

### Example: Tree Representation in Python

```python
# Example tree represented as an adjacency list
tree = {
    'A': ['B', 'C'],
    'B': ['A', 'D', 'E'],
    'C': ['A', 'F'],
    'D': ['B'],
    'E': ['B'],
    'F': ['C']
}

# Function to print the tree structure
def print_tree(tree):
    for vertex, neighbors in tree.items():
        print(f"{vertex}: {neighbors}")

print_tree(tree)
```

**Output:**
```
A: ['B', 'C']
B: ['A', 'D', 'E']
C: ['A', 'F']
D: ['B']
E: ['B']
F: ['C']
```

---

## Binary Trees

A **binary tree** is a tree where each vertex has at most two children, commonly referred to as the **left** and **right** child.

### Types of Binary Trees
1. **Full Binary Tree:** Every node has either 0 or 2 children.
2. **Complete Binary Tree:** All levels are completely filled except possibly the last, which is filled from left to right.
3. **Binary Search Tree (BST):** A binary tree where the left child is smaller than the parent, and the right child is larger.

### Example: Binary Tree Representation

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

# Constructing a binary tree
root = Node('A')
root.left = Node('B')
root.right = Node('C')
root.left.left = Node('D')
root.left.right = Node('E')
root.right.left = Node('F')

# Function to traverse the tree (in-order traversal)
def in_order_traversal(node):
    if node:
        in_order_traversal(node.left)
        print(node.value, end=" ")
        in_order_traversal(node.right)

print("In-order traversal:")
in_order_traversal(root)
```

**Output:**
```
In-order traversal:
D B E A F C
```

---

## Spanning Trees

A **spanning tree** of a graph is a subgraph that includes all vertices and forms a tree. It has the following properties:
- Contains \( n - 1 \) edges for \( n \) vertices.
- There can be multiple spanning trees for a given graph.

### Minimum Spanning Tree (MST)
An MST is a spanning tree with the minimum total edge weight. It is used in applications like network design and clustering.

#### Popular Algorithms for MST:
1. **Kruskal's Algorithm**
2. **Prim's Algorithm**

---

### Kruskal's Algorithm

Kruskal's algorithm finds an MST by sorting edges in ascending order of weight and adding them to the tree if they do not form a cycle.

#### Example: Kruskal's Algorithm Implementation

```python
# Function to find the MST using Kruskal's algorithm
def kruskal(graph):
    parent = {}
    rank = {}

    def find(vertex):
        if parent[vertex] != vertex:
            parent[vertex] = find(parent[vertex])
        return parent[vertex]

    def union(vertex1, vertex2):
        root1 = find(vertex1)
        root2 = find(vertex2)
        if root1 != root2:
            if rank[root1] > rank[root2]:
                parent[root2] = root1
            elif rank[root1] < rank[root2]:
                parent[root1] = root2
            else:
                parent[root2] = root1
                rank[root1] += 1

    # Initialize parent and rank
    for vertex in graph['vertices']:
        parent[vertex] = vertex
        rank[vertex] = 0

    mst = []
    edges = sorted(graph['edges'], key=lambda x: x[2])  # Sort edges by weight

    for edge in edges:
        u, v, weight = edge
        if find(u) != find(v):
            union(u, v)
            mst.append(edge)

    return mst

# Example graph
graph = {
    'vertices': ['A', 'B', 'C', 'D', 'E'],
    'edges': [
        ('A', 'B', 1),
        ('A', 'C', 5),
        ('B', 'C', 4),
        ('B', 'D', 2),
        ('C', 'D', 6),
        ('D', 'E', 3)
    ]
}

mst = kruskal(graph)
print("Minimum Spanning Tree:", mst)
```

**Output:**
```
Minimum Spanning Tree: [('A', 'B', 1), ('B', 'D', 2), ('D', 'E', 3), ('B', 'C', 4)]
```

---

### Prim's Algorithm

Prim's algorithm builds the MST by starting from an arbitrary vertex and repeatedly adding the smallest edge that connects a vertex in the tree to a vertex outside the tree.

#### Example: Prim's Algorithm Implementation

```python
import heapq

def prim(graph, start):
    mst = []
    visited = set()
    min_heap = [(0, start, None)]  # (weight, vertex, parent)

    while min_heap:
        weight, vertex, parent = heapq.heappop(min_heap)
        if vertex not in visited:
            visited.add(vertex)
            if parent:
                mst.append((parent, vertex, weight))
            for neighbor, edge_weight in graph[vertex].items():
                if neighbor not in visited:
                    heapq.heappush(min_heap, (edge_weight, neighbor, vertex))
    return mst

# Example graph as adjacency list with weights
graph = {
    'A': {'B': 1, 'C': 5},
    'B': {'A': 1, 'C': 4, 'D': 2},
    'C': {'A': 5, 'B': 4, 'D': 6},
    'D': {'B': 2, 'C': 6, 'E': 3},
    'E': {'D': 3}
}

mst = prim(graph, 'A')
print("Minimum Spanning Tree:", mst)
```

**Output:**
```
Minimum Spanning Tree: [('A', 'B', 1), ('B', 'D', 2), ('D', 'E', 3), ('B', 'C', 4)]
```

---

## Key Takeaways

- Trees are connected and acyclic structures with diverse applications.
- Binary trees are specialized trees used in searching, sorting, and hierarchical data storage.
- Spanning trees, especially MSTs, are critical in optimizing networks.

Next, we will explore **graph coloring**, including vertex coloring and its applications.


# Chapter 6: Graph Coloring

Graph coloring is the process of assigning colors to the vertices or edges of a graph such that certain constraints are satisfied. It is widely used in problems involving scheduling, resource allocation, and partitioning.

---

## Vertex Coloring

In **vertex coloring**, colors are assigned to vertices such that no two adjacent vertices share the same color. The minimum number of colors required to color a graph is called the **chromatic number** of the graph.

### Example: Vertex Coloring

```python
# Function to perform greedy vertex coloring
def greedy_coloring(graph):
    color_assignment = {}
    for vertex in graph:
        # Find the set of colors already used by neighbors
        neighbor_colors = {color_assignment[neighbor] for neighbor in graph[vertex] if neighbor in color_assignment}
        # Assign the smallest available color
        color = 0
        while color in neighbor_colors:
            color += 1
        color_assignment[vertex] = color
    return color_assignment

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'C', 'D'],
    'C': ['A', 'B', 'D'],
    'D': ['B', 'C']
}

coloring = greedy_coloring(graph)
print("Vertex Coloring:", coloring)
```

**Output:**
```
Vertex Coloring: {'A': 0, 'B': 1, 'C': 2, 'D': 0}
```

---

## Chromatic Number

The **chromatic number** of a graph is the smallest number of colors needed to color its vertices.

### Example: Finding Chromatic Number

```python
def chromatic_number(graph):
    coloring = greedy_coloring(graph)
    return max(coloring.values()) + 1  # Chromatic number is max color + 1

# Using the same graph as above
print("Chromatic Number:", chromatic_number(graph))
```

**Output:**
```
Chromatic Number: 3
```

---

## Applications of Graph Coloring

1. **Scheduling Problems:**
   - Example: Assign time slots to exams such that no two exams with a common student are at the same time.

2. **Map Coloring:**
   - Example: Assign colors to regions on a map such that no two adjacent regions share the same color.

3. **Register Allocation:**
   - Assign registers to variables in a program to minimize conflicts.

---

### Example: Scheduling Problem

Consider the following scenario:
- Exams need to be scheduled.
- \( A \), \( B \), \( C \), \( D \) are exams.
- \( A \) conflicts with \( B \) and \( C \), and so on.

#### Conflict Graph Representation:
```python
schedule_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Solve using greedy coloring
exam_schedule = greedy_coloring(schedule_graph)
print("Exam Schedule:", exam_schedule)
```

**Output:**
```
Exam Schedule: {'A': 0, 'B': 1, 'C': 1, 'D': 0}
```

Interpretation:  
- \( A \) and \( D \) can be scheduled at the same time (Color 0).  
- \( B \) and \( C \) must have separate slots (Color 1).

---

## Edge Coloring

In **edge coloring**, colors are assigned to edges such that no two edges sharing the same vertex have the same color. The minimum number of colors needed is called the **chromatic index**.

---

### Example: Edge Coloring

```python
def edge_coloring(graph):
    edge_colors = {}
    for u in graph:
        for v in graph[u]:
            if (u, v) not in edge_colors and (v, u) not in edge_colors:
                used_colors = {edge_colors.get((u, w), -1) for w in graph[u]}
                used_colors |= {edge_colors.get((v, w), -1) for w in graph[v]}
                color = 0
                while color in used_colors:
                    color += 1
                edge_colors[(u, v)] = color
    return edge_colors

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

edge_coloring_result = edge_coloring(graph)
print("Edge Coloring:", edge_coloring_result)
```

**Output:**
```
Edge Coloring: {('A', 'B'): 0, ('A', 'C'): 1, ('B', 'D'): 0, ('C', 'D'): 1}
```

---

## Key Takeaways

- **Vertex coloring** ensures adjacent vertices are differently colored and is useful for scheduling and resource allocation.
- The **chromatic number** is a critical property that defines the minimum number of colors required.
- **Edge coloring** minimizes conflicts between edges at the same vertex.

Next, we will explore **planar graphs**, their properties, and algorithms like Euler's formula and Kuratowski’s theorem.



# Chapter 6: Graph Coloring

Graph coloring is the process of assigning colors to the vertices or edges of a graph such that certain constraints are satisfied. It is widely used in problems involving scheduling, resource allocation, and partitioning.

---

## Vertex Coloring

In **vertex coloring**, colors are assigned to vertices such that no two adjacent vertices share the same color. The minimum number of colors required to color a graph is called the **chromatic number** of the graph.

### Example: Vertex Coloring

```python
# Function to perform greedy vertex coloring
def greedy_coloring(graph):
    color_assignment = {}
    for vertex in graph:
        # Find the set of colors already used by neighbors
        neighbor_colors = {color_assignment[neighbor] for neighbor in graph[vertex] if neighbor in color_assignment}
        # Assign the smallest available color
        color = 0
        while color in neighbor_colors:
            color += 1
        color_assignment[vertex] = color
    return color_assignment

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'C', 'D'],
    'C': ['A', 'B', 'D'],
    'D': ['B', 'C']
}

coloring = greedy_coloring(graph)
print("Vertex Coloring:", coloring)
```

**Output:**
```
Vertex Coloring: {'A': 0, 'B': 1, 'C': 2, 'D': 0}
```

---

## Chromatic Number

The **chromatic number** of a graph is the smallest number of colors needed to color its vertices.

### Example: Finding Chromatic Number

```python
def chromatic_number(graph):
    coloring = greedy_coloring(graph)
    return max(coloring.values()) + 1  # Chromatic number is max color + 1

# Using the same graph as above
print("Chromatic Number:", chromatic_number(graph))
```

**Output:**
```
Chromatic Number: 3
```

---

## Applications of Graph Coloring

1. **Scheduling Problems:**
   - Example: Assign time slots to exams such that no two exams with a common student are at the same time.

2. **Map Coloring:**
   - Example: Assign colors to regions on a map such that no two adjacent regions share the same color.

3. **Register Allocation:**
   - Assign registers to variables in a program to minimize conflicts.

---

### Example: Scheduling Problem

Consider the following scenario:
- Exams need to be scheduled.
- \( A \), \( B \), \( C \), \( D \) are exams.
- \( A \) conflicts with \( B \) and \( C \), and so on.

#### Conflict Graph Representation:
```python
schedule_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

# Solve using greedy coloring
exam_schedule = greedy_coloring(schedule_graph)
print("Exam Schedule:", exam_schedule)
```

**Output:**
```
Exam Schedule: {'A': 0, 'B': 1, 'C': 1, 'D': 0}
```

Interpretation:  
- \( A \) and \( D \) can be scheduled at the same time (Color 0).  
- \( B \) and \( C \) must have separate slots (Color 1).

---

## Edge Coloring

In **edge coloring**, colors are assigned to edges such that no two edges sharing the same vertex have the same color. The minimum number of colors needed is called the **chromatic index**.

---

### Example: Edge Coloring

```python
def edge_coloring(graph):
    edge_colors = {}
    for u in graph:
        for v in graph[u]:
            if (u, v) not in edge_colors and (v, u) not in edge_colors:
                used_colors = {edge_colors.get((u, w), -1) for w in graph[u]}
                used_colors |= {edge_colors.get((v, w), -1) for w in graph[v]}
                color = 0
                while color in used_colors:
                    color += 1
                edge_colors[(u, v)] = color
    return edge_colors

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

edge_coloring_result = edge_coloring(graph)
print("Edge Coloring:", edge_coloring_result)
```

**Output:**
```
Edge Coloring: {('A', 'B'): 0, ('A', 'C'): 1, ('B', 'D'): 0, ('C', 'D'): 1}
```

---

## Key Takeaways

- **Vertex coloring** ensures adjacent vertices are differently colored and is useful for scheduling and resource allocation.
- The **chromatic number** is a critical property that defines the minimum number of colors required.
- **Edge coloring** minimizes conflicts between edges at the same vertex.

Next, we will explore **planar graphs**, their properties, and algorithms like Euler's formula and Kuratowski’s theorem.
# Chapter 7: Planar Graphs

A **planar graph** is a graph that can be drawn on a plane without any edges crossing each other. Planar graphs have unique properties and are associated with powerful theorems and algorithms.

---

## Properties of Planar Graphs

1. A graph is planar if it can be embedded in the plane without edge crossings.
2. A complete graph \( K_4 \) is planar, but \( K_5 \) is not.
3. A complete bipartite graph \( K_{3,3} \) is not planar.

---

## Euler’s Formula

Euler’s formula relates the number of vertices (\( V \)), edges (\( E \)), and faces (\( F \)) of a connected planar graph:

\[
V - E + F = 2
\]

### Example: Verifying Euler’s Formula

Consider the following planar graph:

```python
# Example planar graph
planar_graph = {
    'A': ['B', 'C'],
    'B': ['A', 'C', 'D'],
    'C': ['A', 'B', 'D'],
    'D': ['B', 'C', 'E'],
    'E': ['D']
}

# Count vertices, edges, and faces
vertices = len(planar_graph)
edges = sum(len(neighbors) for neighbors in planar_graph.values()) // 2
faces = edges - vertices + 2

print(f"Vertices (V): {vertices}")
print(f"Edges (E): {edges}")
print(f"Faces (F): {faces}")
```

**Output:**
```
Vertices (V): 5
Edges (E): 6
Faces (F): 3
```

Euler's formula holds as \( V - E + F = 2 \).

---

## Kuratowski’s Theorem

Kuratowski’s theorem states that a graph is non-planar if and only if it contains a subgraph that is a subdivision of \( K_5 \) (complete graph of 5 vertices) or \( K_{3,3} \) (complete bipartite graph of 3 vertices in each set).

### Example: Checking for Non-Planarity

```python
def is_planar(graph):
    # Simplistic approach for demonstration
    if len(graph) > 4 and sum(len(neighbors) for neighbors in graph.values()) // 2 > 9:
        return False  # Likely contains K5 or K3,3 subgraph
    return True

# Example graphs
graph1 = {  # Likely planar
    'A': ['B', 'C'],
    'B': ['A', 'C', 'D'],
    'C': ['A', 'B', 'D'],
    'D': ['B', 'C']
}

graph2 = {  # Likely non-planar (close to K3,3)
    'A': ['B', 'C', 'D'],
    'B': ['A', 'E', 'F'],
    'C': ['A', 'E', 'F'],
    'D': ['A', 'E', 'F'],
    'E': ['B', 'C', 'D'],
    'F': ['B', 'C', 'D']
}

print("Graph 1 Planar:", is_planar(graph1))  # True
print("Graph 2 Planar:", is_planar(graph2))  # False
```

**Output:**
```
Graph 1 Planar: True
Graph 2 Planar: False
```

---

## Graph Embedding

**Graph embedding** refers to representing a planar graph in the plane such that no edges intersect.

### Example: Visualizing a Planar Graph

Although visual embeddings are better done using graph libraries like `matplotlib` or `networkx`, here's a textual representation example:

```python
import networkx as nx
import matplotlib.pyplot as plt

# Create a planar graph
G = nx.Graph()
G.add_edges_from([('A', 'B'), ('A', 'C'), ('B', 'C'), ('B', 'D'), ('C', 'D'), ('D', 'E')])

# Check if the graph is planar
is_planar, embedding = nx.check_planarity(G)
print("Is the graph planar?", is_planar)

# Draw the planar graph
pos = nx.planar_layout(G)
nx.draw(G, pos, with_labels=True, node_color="lightblue", edge_color="gray", node_size=500, font_size=10)
plt.show()
```

**Output:**
- The graph is planar, and its layout is visualized without any edge crossings.

---

## Applications of Planar Graphs

1. **Geographic Mapping:**
   - Used to model countries, cities, or regions with non-overlapping boundaries.
2. **Circuit Design:**
   - Planar graphs are used to design circuits to minimize crossings.
3. **Network Visualization:**
   - Used for visual clarity in representing networks.

---

## Key Takeaways

- Planar graphs can be drawn without edge crossings and are governed by Euler’s formula.
- Kuratowski’s theorem helps determine graph planarity based on \( K_5 \) and \( K_{3,3} \) subgraphs.
- Graph embedding is critical for applications like mapping and circuit design.

Next, we will explore **shortest path algorithms**, including Dijkstra’s, Bellman-Ford, and Floyd-Warshall algorithms.
# Chapter 8: Shortest Path Algorithms

Shortest path algorithms are used to find the minimum distance or cost between vertices in a graph. These algorithms have widespread applications in network routing, navigation, and optimization problems.

---

## Dijkstra’s Algorithm

Dijkstra’s algorithm finds the shortest path from a source vertex to all other vertices in a graph with **non-negative edge weights**.

### Steps of Dijkstra's Algorithm
1. Initialize the distance to all vertices as infinity (\( \infty \)), except the source vertex, which is set to 0.
2. Use a priority queue to repeatedly extract the vertex with the smallest known distance.
3. Update the distances to the neighbors of the extracted vertex.
4. Repeat until all vertices have been processed.

### Example: Dijkstra’s Algorithm Implementation

```python
import heapq

def dijkstra(graph, source):
    distances = {vertex: float('inf') for vertex in graph}
    distances[source] = 0
    priority_queue = [(0, source)]  # (distance, vertex)

    while priority_queue:
        current_distance, current_vertex = heapq.heappop(priority_queue)

        if current_distance > distances[current_vertex]:
            continue

        for neighbor, weight in graph[current_vertex].items():
            distance = current_distance + weight

            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(priority_queue, (distance, neighbor))

    return distances

# Example weighted graph
graph = {
    'A': {'B': 1, 'C': 4},
    'B': {'A': 1, 'C': 2, 'D': 6},
    'C': {'A': 4, 'B': 2, 'D': 3},
    'D': {'B': 6, 'C': 3}
}

shortest_paths = dijkstra(graph, 'A')
print("Shortest Paths from A:", shortest_paths)
```

**Output:**
```
Shortest Paths from A: {'A': 0, 'B': 1, 'C': 3, 'D': 6}
```

---

## Bellman-Ford Algorithm

The Bellman-Ford algorithm computes shortest paths from a source vertex to all other vertices, even in graphs with **negative edge weights**. However, it does not work with negative weight cycles.

### Steps of Bellman-Ford Algorithm
1. Initialize the distance to all vertices as infinity (\( \infty \)), except the source vertex, which is set to 0.
2. Relax all edges \( |V| - 1 \) times (where \( |V| \) is the number of vertices).
3. Check for negative weight cycles by iterating through the edges once more.

### Example: Bellman-Ford Algorithm Implementation

```python
def bellman_ford(graph, source):
    distances = {vertex: float('inf') for vertex in graph}
    distances[source] = 0

    for _ in range(len(graph) - 1):
        for vertex, neighbors in graph.items():
            for neighbor, weight in neighbors.items():
                if distances[vertex] + weight < distances[neighbor]:
                    distances[neighbor] = distances[vertex] + weight

    # Check for negative weight cycles
    for vertex, neighbors in graph.items():
        for neighbor, weight in neighbors.items():
            if distances[vertex] + weight < distances[neighbor]:
                raise ValueError("Graph contains a negative weight cycle")

    return distances

# Example weighted graph with negative weights
graph = {
    'A': {'B': 1, 'C': 4},
    'B': {'C': -3, 'D': 2},
    'C': {},
    'D': {'C': 1}
}

shortest_paths = bellman_ford(graph, 'A')
print("Shortest Paths from A:", shortest_paths)
```

**Output:**
```
Shortest Paths from A: {'A': 0, 'B': 1, 'C': -2, 'D': 3}
```

---

## Floyd-Warshall Algorithm

The Floyd-Warshall algorithm computes the shortest paths between all pairs of vertices in a graph. It works with both **positive** and **negative weights** but does not handle negative weight cycles.

### Steps of Floyd-Warshall Algorithm
1. Create a matrix where \( dist[i][j] \) represents the shortest distance from vertex \( i \) to vertex \( j \). Initialize:
   - \( dist[i][i] = 0 \)
   - \( dist[i][j] = weight(i, j) \) (if an edge exists) or \( \infty \) (otherwise).
2. For each intermediate vertex \( k \), update \( dist[i][j] \) using:
   \[
   dist[i][j] = \min(dist[i][j], dist[i][k] + dist[k][j])
   \]
3. Repeat for all pairs of vertices.

### Example: Floyd-Warshall Algorithm Implementation

```python
def floyd_warshall(graph):
    vertices = list(graph.keys())
    dist = {u: {v: float('inf') for v in vertices} for u in vertices}

    for u in graph:
        dist[u][u] = 0
        for v, weight in graph[u].items():
            dist[u][v] = weight

    for k in vertices:
        for i in vertices:
            for j in vertices:
                dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])

    return dist

# Example weighted graph
graph = {
    'A': {'B': 3, 'C': 8, 'D': -4},
    'B': {'D': 1, 'C': 4},
    'C': {'A': 2},
    'D': {'C': 5, 'B': -2}
}

shortest_paths = floyd_warshall(graph)
print("Shortest Path Matrix:")
for u in shortest_paths:
    print(f"{u}: {shortest_paths[u]}")
```

**Output:**
```
Shortest Path Matrix:
A: {'A': 0, 'B': 1, 'C': -3, 'D': -4}
B: {'A': 3, 'B': 0, 'C': -4, 'D': -3}
C: {'A': 2, 'B': 5, 'C': 0, 'D': -2}
D: {'A': 7, 'B': -2, 'C': 1, 'D': 0}
```

---

## Comparison of Shortest Path Algorithms

| Algorithm          | Edge Weights         | Negative Weights | Negative Cycles | Complexity       |
|--------------------|----------------------|------------------|-----------------|------------------|
| **Dijkstra**       | Non-negative         | Not allowed      | Not handled     | \( O((V+E) \log V) \) |
| **Bellman-Ford**   | Positive/Negative    | Allowed          | Not handled     | \( O(VE) \)      |
| **Floyd-Warshall** | Positive/Negative    | Allowed          | Not handled     | \( O(V^3) \)     |

---

## Key Takeaways

- **Dijkstra’s algorithm** is efficient for non-negative edge weights.
- **Bellman-Ford algorithm** handles graphs with negative weights but not negative cycles.
- **Floyd-Warshall algorithm** computes all-pairs shortest paths and is suitable for dense graphs.

Next, we will explore **network flow algorithms**, including the Max-Flow Min-Cut theorem and Ford-Fulkerson algorithm.
# Chapter 9: Network Flow

Network flow algorithms are used to model and analyze the flow of resources through a network. They are widely applied in transportation, telecommunications, and resource allocation problems.

---

## Key Concepts in Network Flow

1. **Flow Network:** A directed graph where:
   - Each edge has a **capacity** (\( c(u, v) \)) representing the maximum allowable flow.
   - A **flow** (\( f(u, v) \)) satisfies \( 0 \leq f(u, v) \leq c(u, v) \).

2. **Source (\( s \)) and Sink (\( t \)):**
   - \( s \): Starting point where resources enter the network.
   - \( t \): Endpoint where resources exit the network.

3. **Flow Conservation:** For every vertex except \( s \) and \( t \), the incoming flow equals the outgoing flow:
   \[
   \sum f(u, v) = \sum f(v, w)
   \]

4. **Maximum Flow:** The maximum amount of flow that can be pushed from \( s \) to \( t \) in the network.

---

## Max-Flow Min-Cut Theorem

The **Max-Flow Min-Cut theorem** states that the maximum flow from \( s \) to \( t \) in a flow network is equal to the total capacity of the minimum cut separating \( s \) from \( t \).

### Example: Understanding Min-Cut

- A **cut** partitions the vertices into two disjoint subsets such that \( s \) and \( t \) are in different subsets.
- The **capacity** of a cut is the sum of the capacities of edges crossing from the \( s \)-side to the \( t \)-side.

---

## Ford-Fulkerson Algorithm

The **Ford-Fulkerson algorithm** computes the maximum flow in a network by repeatedly finding augmenting paths and updating the flow.

### Steps of Ford-Fulkerson Algorithm
1. Initialize all flows to 0.
2. While there exists an augmenting path:
   - Find the path with available capacity.
   - Augment the flow along the path.
3. Repeat until no more augmenting paths exist.

---

### Example: Ford-Fulkerson Algorithm Implementation

```python
from collections import deque

# Function to perform BFS to find an augmenting path
def bfs(capacity, flow, source, sink, parent):
    visited = set()
    queue = deque([source])
    visited.add(source)

    while queue:
        current = queue.popleft()
        for neighbor in capacity[current]:
            residual = capacity[current][neighbor] - flow[current][neighbor]
            if neighbor not in visited and residual > 0:
                parent[neighbor] = current
                visited.add(neighbor)
                queue.append(neighbor)
                if neighbor == sink:
                    return True
    return False

# Ford-Fulkerson implementation
def ford_fulkerson(graph, source, sink):
    # Initialize capacity and flow matrices
    capacity = {u: {v: 0 for v in graph} for u in graph}
    flow = {u: {v: 0 for v in graph} for u in graph}

    for u in graph:
        for v, cap in graph[u].items():
            capacity[u][v] = cap

    parent = {}
    max_flow = 0

    # Augment the flow while there exists an augmenting path
    while bfs(capacity, flow, source, sink, parent):
        # Find the bottleneck capacity
        path_flow = float('inf')
        v = sink
        while v != source:
            u = parent[v]
            path_flow = min(path_flow, capacity[u][v] - flow[u][v])
            v = u

        # Update residual capacities and reverse flows
        v = sink
        while v != source:
            u = parent[v]
            flow[u][v] += path_flow
            flow[v][u] -= path_flow
            v = u

        max_flow += path_flow

    return max_flow

# Example flow network
graph = {
    's': {'a': 10, 'b': 5},
    'a': {'b': 15, 't': 10},
    'b': {'a': 4, 't': 10},
    't': {}
}

max_flow = ford_fulkerson(graph, 's', 't')
print("Maximum Flow:", max_flow)
```

**Output:**
```
Maximum Flow: 15
```

---

## Applications of Network Flow

1. **Transportation Networks:**  
   Optimize traffic flow or logistics routes.

2. **Telecommunications:**  
   Maximize data throughput in communication networks.

3. **Job Assignment:**  
   Match jobs to workers efficiently using bipartite graphs.

4. **Resource Allocation:**  
   Allocate resources like water, electricity, or bandwidth.

---

### Example: Bipartite Matching with Network Flow

Given a bipartite graph, find the maximum matching:

```python
# Example bipartite graph for job assignment
graph = {
    's': {'A1': 1, 'A2': 1},
    'A1': {'J1': 1, 'J2': 1},
    'A2': {'J1': 1},
    'J1': {'t': 1},
    'J2': {'t': 1},
    't': {}
}

max_matching = ford_fulkerson(graph, 's', 't')
print("Maximum Bipartite Matching:", max_matching)
```

**Output:**
```
Maximum Bipartite Matching: 2
```

---

## Key Takeaways

- **Ford-Fulkerson algorithm** is a fundamental technique for finding maximum flow in a network.
- Network flow concepts are versatile and solve a wide range of problems like traffic optimization and job assignment.
- The **Max-Flow Min-Cut theorem** bridges the connection between flows and cuts.

Next, we will explore **matching and covering**, including bipartite matching, maximum matching algorithms, and vertex/edge covers.
# Chapter 10: Matching and Covering

Matching and covering are important concepts in graph theory used in optimization problems, resource allocation, and network design.

---

## Matching in Graphs

A **matching** is a subset of edges such that no two edges share a common vertex.

### Types of Matching:
1. **Maximum Matching:** The largest possible matching in a graph.
2. **Perfect Matching:** A matching where every vertex is included in exactly one edge.
3. **Bipartite Matching:** Matching in a bipartite graph where vertices are divided into two disjoint sets.

---

### Example: Maximum Matching

```python
# Example graph for bipartite matching
graph = {
    'A': ['X', 'Y'],
    'B': ['X', 'Z'],
    'C': ['Y', 'Z'],
    'X': ['A', 'B'],
    'Y': ['A', 'C'],
    'Z': ['B', 'C']
}

# DFS-based function for finding a matching
def maximum_matching(graph):
    matching = {}
    def bpm(vertex, visited, match):
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                if neighbor not in match or bpm(match[neighbor], visited, match):
                    match[neighbor] = vertex
                    return True
        return False

    for vertex in graph:
        visited = set()
        bpm(vertex, visited, matching)

    return {v: k for k, v in matching.items()}

matching = maximum_matching(graph)
print("Maximum Matching:", matching)
```

**Output:**
```
Maximum Matching: {'X': 'A', 'Y': 'C', 'Z': 'B'}
```

---

## Vertex and Edge Covers

1. **Vertex Cover:** A set of vertices such that every edge in the graph is incident to at least one vertex in the set.
2. **Edge Cover:** A set of edges such that every vertex in the graph is incident to at least one edge in the set.

---

### Example: Vertex Cover Approximation

```python
# Approximation algorithm for vertex cover in a bipartite graph
def vertex_cover(graph):
    cover = set()
    visited = set()

    for vertex in graph:
        if vertex not in visited:
            for neighbor in graph[vertex]:
                if neighbor not in visited:
                    cover.add(vertex)
                    cover.add(neighbor)
                    visited.add(vertex)
                    visited.add(neighbor)
                    break
    return cover

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

cover = vertex_cover(graph)
print("Vertex Cover:", cover)
```

**Output:**
```
Vertex Cover: {'A', 'B', 'C', 'D'}
```

---

## Algorithms for Bipartite Matching

1. **Hungarian Algorithm:** Finds a maximum matching in a weighted bipartite graph in \( O(n^3) \).
2. **Hopcroft-Karp Algorithm:** Finds a maximum matching in a bipartite graph in \( O(E \sqrt{V}) \).

---

### Example: Hopcroft-Karp Algorithm Implementation

```python
from collections import deque

def hopcroft_karp(graph):
    pair_u = {u: None for u in graph}
    pair_v = {}
    dist = {}

    def bfs():
        queue = deque()
        for u in pair_u:
            if pair_u[u] is None:
                dist[u] = 0
                queue.append(u)
            else:
                dist[u] = float('inf')
        dist[None] = float('inf')

        while queue:
            u = queue.popleft()
            if dist[u] < dist[None]:
                for v in graph[u]:
                    if dist[pair_v.get(v, None)] == float('inf'):
                        dist[pair_v.get(v, None)] = dist[u] + 1
                        queue.append(pair_v.get(v, None))
        return dist[None] != float('inf')

    def dfs(u):
        if u is not None:
            for v in graph[u]:
                if dist[pair_v.get(v, None)] == dist[u] + 1:
                    if dfs(pair_v.get(v, None)):
                        pair_v[v] = u
                        pair_u[u] = v
                        return True
            dist[u] = float('inf')
            return False
        return True

    matching = 0
    while bfs():
        for u in graph:
            if pair_u[u] is None and dfs(u):
                matching += 1
    return matching

# Example bipartite graph
bipartite_graph = {
    'A': ['X', 'Y'],
    'B': ['Y', 'Z'],
    'C': ['X', 'Z'],
    'X': [],
    'Y': [],
    'Z': []
}

max_matching = hopcroft_karp(bipartite_graph)
print("Maximum Bipartite Matching:", max_matching)
```

**Output:**
```
Maximum Bipartite Matching: 3
```

---

## Applications of Matching and Covering

1. **Resource Allocation:** Assign workers to tasks, jobs to machines, or students to classes.
2. **Network Design:** Optimize connections with minimum cost.
3. **Scheduling:** Match tasks to time slots or resources to minimize conflicts.

---

## Key Takeaways

- Matching identifies optimal subsets of edges, with maximum and perfect matching being key goals.
- Vertex and edge covers are complementary concepts, minimizing the set of vertices or edges to cover the graph.
- Efficient algorithms like Hopcroft-Karp and Hungarian algorithms solve complex matching problems in bipartite graphs.

Next, we will explore **advanced topics in graph theory**, including Eulerian and Hamiltonian graphs, graph isomorphism, and spectral graph theory.
# Chapter 11: Advanced Topics in Graph Theory

Graph theory extends into specialized topics that explore complex graph properties and applications. This chapter covers **Eulerian and Hamiltonian graphs**, **graph isomorphism**, **random graphs**, and **spectral graph theory**.

---

## Eulerian Graphs

An **Eulerian graph** is a graph that contains an **Eulerian circuit**, which is a closed trail (starts and ends at the same vertex) that visits every edge exactly once.

### Properties of Eulerian Graphs:
1. **Undirected Graphs:**
   - All vertices have an even degree.
   - The graph is connected (ignoring isolated vertices).

2. **Directed Graphs:**
   - Every vertex has equal in-degree and out-degree.
   - The graph is strongly connected.

---

### Example: Check if a Graph is Eulerian

```python
def is_eulerian(graph):
    # Check degree condition for undirected graph
    for vertex in graph:
        if len(graph[vertex]) % 2 != 0:
            return False
    return True

# Example undirected graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

print("Is Eulerian:", is_eulerian(graph))  # False (not all vertices have even degree)
```

---

## Hamiltonian Graphs

A **Hamiltonian graph** contains a **Hamiltonian cycle**, which is a closed path that visits every vertex exactly once.

### Conditions for Hamiltonian Graphs:
1. **Dirac's Theorem:** If every vertex in a graph with \( n \geq 3 \) vertices has a degree \( \geq n/2 \), the graph is Hamiltonian.
2. There is no general efficient algorithm to determine if a graph is Hamiltonian.

---

### Example: Check for a Hamiltonian Path

```python
from itertools import permutations

def is_hamiltonian(graph):
    vertices = list(graph.keys())
    for perm in permutations(vertices):
        is_cycle = True
        for i in range(len(perm)):
            if perm[i - 1] not in graph[perm[i]]:
                is_cycle = False
                break
        if is_cycle:
            return True
    return False

# Example graph
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}

print("Is Hamiltonian:", is_hamiltonian(graph))  # True
```

---

## Graph Isomorphism

Two graphs \( G_1 \) and \( G_2 \) are **isomorphic** if there is a one-to-one correspondence between their vertex sets that preserves adjacency.

### Applications of Graph Isomorphism:
1. Chemical compound analysis (e.g., comparing molecular structures).
2. Network comparison.
3. Pattern recognition.

---

### Example: Check if Two Graphs are Isomorphic

```python
def are_isomorphic(graph1, graph2):
    if len(graph1) != len(graph2):
        return False
    from itertools import permutations
    for perm in permutations(graph1.keys()):
        mapping = {list(graph1.keys())[i]: perm[i] for i in range(len(perm))}
        is_iso = all(
            set(graph1[v1]) == {mapping[v2] for v2 in graph1[v1]} for v1 in graph1
        )
        if is_iso:
            return True
    return False

# Example graphs
graph1 = {
    'A': ['B', 'C'],
    'B': ['A', 'C'],
    'C': ['A', 'B']
}

graph2 = {
    'X': ['Y', 'Z'],
    'Y': ['X', 'Z'],
    'Z': ['X', 'Y']
}

print("Are Isomorphic:", are_isomorphic(graph1, graph2))  # True
```

---

## Random Graphs

**Random graphs** are generated by connecting vertices randomly. They are used to study the properties of real-world networks like social networks and the internet.

### Erdős–Rényi Model (\( G(n, p) \)):
- \( n \): Number of vertices.
- \( p \): Probability of connecting any pair of vertices.

---

### Example: Generate a Random Graph

```python
import random

def generate_random_graph(n, p):
    graph = {i: [] for i in range(n)}
    for i in range(n):
        for j in range(i + 1, n):
            if random.random() < p:
                graph[i].append(j)
                graph[j].append(i)
    return graph

# Generate a random graph with 5 vertices and 0.4 connection probability
random_graph = generate_random_graph(5, 0.4)
print("Random Graph:", random_graph)
```

---

## Spectral Graph Theory

**Spectral graph theory** studies the properties of graphs through the eigenvalues and eigenvectors of their adjacency or Laplacian matrices.

### Applications:
1. Clustering and community detection in networks.
2. Analyzing connectivity and robustness.
3. Solving optimization problems.

---

### Example: Eigenvalues of a Graph

```python
import numpy as np

def adjacency_matrix(graph):
    n = len(graph)
    matrix = np.zeros((n, n))
    for i, neighbors in graph.items():
        for j in neighbors:
            matrix[i][j] = 1
    return matrix

# Example graph
graph = {
    0: [1, 2],
    1: [0, 2],
    2: [0, 1, 3],
    3: [2]
}

matrix = adjacency_matrix(graph)
eigenvalues = np.linalg.eigvals(matrix)
print("Eigenvalues:", eigenvalues)
```

**Output:**
```
Eigenvalues: [ 2.732..., -1.0, -1.0, 0.268...]
```

---

## Key Takeaways

- **Eulerian and Hamiltonian graphs** describe special traversals in graphs, with practical applications in routing and scheduling.
- **Graph isomorphism** tests structural equivalence, useful in pattern recognition and chemistry.
- **Random graphs** model complex networks like social and communication systems.
- **Spectral graph theory** uses matrix-based approaches to analyze graph properties.

Next, we will explore **real-world applications of graph theory** in diverse domains.
# Chapter 12: Applications of Graph Theory

Graph theory provides the foundation for solving complex problems in various domains. Its versatility allows it to model relationships, optimize systems, and analyze networks in the real world.

---

## 1. Social Networks

Graphs are used to represent social relationships, where:
- **Vertices:** Represent individuals or entities.
- **Edges:** Represent relationships, such as friendships, follows, or collaborations.

### Applications:
1. **Community Detection:** Identify clusters or groups of tightly connected individuals.
2. **Influence Maximization:** Identify key influencers in a network.
3. **Recommendation Systems:** Suggest connections or products based on graph-based similarity measures.

### Example: Friend Recommendation System

```python
def recommend_friends(graph, person):
    friends = set(graph[person])
    recommendations = {}

    for friend in friends:
        for potential_friend in graph[friend]:
            if potential_friend != person and potential_friend not in friends:
                recommendations[potential_friend] = recommendations.get(potential_friend, 0) + 1

    return sorted(recommendations, key=recommendations.get, reverse=True)

# Example graph (social network)
social_graph = {
    'Alice': ['Bob', 'Charlie'],
    'Bob': ['Alice', 'David'],
    'Charlie': ['Alice', 'David'],
    'David': ['Bob', 'Charlie', 'Eve'],
    'Eve': ['David']
}

print("Friend Recommendations for Alice:", recommend_friends(social_graph, 'Alice'))
```

---

## 2. Computer Networks

Graphs are used to model communication networks, where:
- **Vertices:** Represent devices (routers, switches, computers).
- **Edges:** Represent communication links (wired or wireless).

### Applications:
1. **Routing Protocols:** Find the shortest or most reliable paths.
2. **Bandwidth Optimization:** Allocate resources effectively.
3. **Network Reliability:** Analyze robustness and identify critical points of failure.

### Example: Shortest Path in a Network

```python
network_graph = {
    'Router1': {'Router2': 2, 'Router3': 5},
    'Router2': {'Router1': 2, 'Router3': 1, 'Router4': 3},
    'Router3': {'Router1': 5, 'Router2': 1, 'Router4': 2},
    'Router4': {'Router2': 3, 'Router3': 2}
}

from heapq import heappop, heappush

def shortest_path(graph, start, end):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]

    while pq:
        current_distance, current_node = heappop(pq)
        if current_distance > distances[current_node]:
            continue
        for neighbor, weight in graph[current_node].items():
            distance = current_distance + weight
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heappush(pq, (distance, neighbor))

    return distances[end]

print("Shortest Path from Router1 to Router4:", shortest_path(network_graph, 'Router1', 'Router4'))
```

---

## 3. Biological Networks

Graphs are widely used to model and analyze biological systems, such as:
- **Protein Interaction Networks:** Model interactions between proteins in a cell.
- **Gene Regulatory Networks:** Represent relationships between genes and regulatory elements.
- **Food Webs:** Represent predator-prey relationships.

### Applications:
1. **Drug Discovery:** Identify potential targets by analyzing protein interactions.
2. **Epidemic Modeling:** Simulate the spread of diseases using graph-based models.
3. **Ecology:** Study stability and biodiversity in ecosystems.

### Example: Protein Interaction Network

```python
protein_graph = {
    'Protein1': ['Protein2', 'Protein3'],
    'Protein2': ['Protein1', 'Protein4'],
    'Protein3': ['Protein1'],
    'Protein4': ['Protein2']
}

def find_interacting_partners(graph, protein):
    return graph.get(protein, [])

print("Interacting Partners of Protein1:", find_interacting_partners(protein_graph, 'Protein1'))
```

---

## 4. Scheduling Problems

Graphs are used to represent tasks and constraints, where:
- **Vertices:** Represent tasks.
- **Edges:** Represent dependencies between tasks.

### Applications:
1. **Job Scheduling:** Allocate resources to tasks while respecting constraints.
2. **Exam Scheduling:** Assign exams to time slots to avoid conflicts.
3. **Project Planning:** Use critical path analysis to optimize project timelines.

### Example: Task Scheduling with Topological Sort

```python
from collections import deque

def topological_sort(graph):
    in_degree = {node: 0 for node in graph}
    for node in graph:
        for neighbor in graph[node]:
            in_degree[neighbor] += 1

    queue = deque([node for node in graph if in_degree[node] == 0])
    sorted_order = []

    while queue:
        node = queue.popleft()
        sorted_order.append(node)
        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    return sorted_order

# Example task graph
task_graph = {
    'Task1': ['Task2', 'Task3'],
    'Task2': ['Task4'],
    'Task3': ['Task4'],
    'Task4': []
}

print("Task Scheduling Order:", topological_sort(task_graph))
```

---

## Key Takeaways

- **Social Networks:** Analyze relationships and influence using graph structures.
- **Computer Networks:** Optimize communication and resource allocation.
- **Biological Networks:** Study complex interactions in biological systems.
- **Scheduling Problems:** Solve dependency constraints effectively with graph algorithms.

Next, we will conclude the book with exercises, problems, and references for further exploration.
