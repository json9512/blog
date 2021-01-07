---
layout: post
title:  "[알고리즘] Prim's Minimum Spanning Tree"
tags: algorithm
comments: true
---

출처: [GeeksforGeeks](https://www.geeksforgeeks.org/prims-minimum-spanning-tree-mst-greedy-algo-5/), [cppsecrets](https://cppsecrets.com/users/1032115979910410511011497115116111103105505149484957575564103109971051084699111109/Python-Implementation-of-Prims-Minimum-Spanning-Tree.php), [bradfieldcs](https://bradfieldcs.com/algos/graphs/prims-spanning-tree-algorithm/)

Prim의 Minimum Spanning Tree 알고리즘에 대해 알아보자

- 그리디 알고리즘이다.
- 가장 작은 weight로 MST를 구현하는 것
- Naive하게 구현 하면 Time complexity `O(|V|^2)` 이나 다익스트라와 같이 priority queue를 사용하면 `O(E log V)`로 만들 수 있다

# 방법
1. MST에 포함된 vertex를 저장할 set를 만든다 (empty set)
2. 모든 vertices 간의 weight 값을 지정한다. 시작점을 제외한 모든 vertices weight 값은 무한대가 된다. 시작점은 0으로 만든다
3. 모든 vertices가 MST에 포함된 vertex를 저장하는 set에 들어올때까지 이하 반복 한다:
    1. set에 포함되지 않는 vertices 중 최단 거리를 가지고 있는 vertex u를 고른다
    2. set에 포함 시킨다
    3. vertex u 와 인접한 모든 vertices의 거리 값을 갱신한다.


# Python
```python
def createAdjMatrix(V, G):
    # Create empty adjacent matrix
    adjMatrix = [[0 for row in range(V)] for col in range(V)]

    # populate adjacency matrix with correct weights
    for i in range(0, len(G)):
        # G[i][0] is index of one vertex (node)
        # G[i][1] is index of the other vertex
        adjMatrix[G[i][0]][G[i][1]] = G[i][2]
        adjMatrix[G[i][1]][G[i][0]] = G[i][2]
    
    return adjMatrix

def primsMST(V, G):
    # create adjacency matrix from graph
    adjMatrix = createAdjMatrix(V, G)

    # choose initial vertex from graph
    vertex = 0

    # initialize empty edges array and empty MST
    MST = []
    edges = []
    visited = []
    # keep track of min Edge
    # [from, to, weight]
    minEdge = [None, None, float('inf')]

    # run prim's algorithm until the MST
    # has all the vertices
    while len(MST) != V - 1:
        

        # mark the vertex as visited
        visited.append(vertex)

        # add each edge to the list of potential edges
        for i in range(0, V):
            if adjMatrix[vertex][i] != 0:
                edges.append([vertex, i, adjMatrix[vertex][i]])
        
        # find the edge with the smallest weight to a vertex
        # that has not yet visited
        for edge in range(0, len(edges)):
            if edges[edge][2] < minEdge[2] and edges[edge][1] not in visited:
                minEdge = edges[edge]
        
        # remove min weighted edge from the list of edges
        edges.remove(minEdge)

        # push min edge to MST
        MST.append(minEdge)

        # update vertex and rest minEdge
        vertex = minEdge[1]
        minEdge = [None, None, float('inf')]
    
    return MST

if __name__ == "__main__":
    # create graph nodes (vertices)
    a, b, c, d, e, f = 0, 1, 2, 3, 4, 5

    # create graph edges with weights
    graph = [[a, b, 2],
        [a, c, 3],
        [b, d, 3],
        [b, c, 5],
        [b, e, 4],
        [c, e, 4],
        [d, e, 2],
        [d, f, 3],
        [e, f, 5]]

    print(primsMST(6, graph))

    # prints [[0, 1, 2], [0, 2, 3], [1, 3, 3], [3, 4, 2], [3, 5, 3]]
    # Which is a MST of:
    #     1(b) - 3(d) - 4(e)
    #     |      |
    #     0(a)   5(f)
    #     |
    #     2(c)
```

# 우선 순위 큐를 사용한 Prim's MST
```python
import heapq
from collections import defaultdict

def createSpanningTree(graph, start):
    mst = defaultdict(set)
    visited = set([start])
    edges = [
        (cost, start, to) for to, cost in graph[start].items()
    ]

    heapq.heapify(edges)

    # Find the mst
    while edges:
        cost, frm, to = heapq.heappop(edges)

        if to not in visited:
            visited.add(to)
            mst[frm].add(to)

            for to_next, cost in graph[to].items():
                if to_next not in visited:
                    heapq.heappush(edges, (cost, to, to_next))
    
    return mst

example = {
    'A': {'B': 2, 'C': 3},
    'B': {'A': 2, 'C': 1, 'D': 1, 'E': 4},
    'C': {'A': 3, 'B': 1, 'F': 5},
    'D': {'B': 1, 'E': 1},
    'E': {'B': 4, 'D': 1, 'F': 1},
    'F': {'C': 5, 'E': 1, 'G': 1},
    'G': {'F': 1},
}

mst = dict(createSpanningTree(example, 'A'))

print(mst)
# {'A': {'B'}, 'B': {'D', 'C'}, 'D': {'E'}, 'E': {'F'}, 'F': {'G'}}
#
#  Original
#     
#       2 -- A ---- 3
#       |           |
#       B -- 1 ---- C
#       | \         |
#       1   4       5
#       |    \      |
#       D -1- E -1- F -1- G
#
#  MST:
#       2 -- A 
#       |           
#       B -- 1 ---- C
#       |           
#       1           
#       |           
#       D --1-- E --1-- F --1-- G
```


# Prim's MST 알고리즘을 사용한 문제
[Leetcode 1584 Min cost to connect all points](https://leetcode.com/problems/min-cost-to-connect-all-points/)

[[프로그래머스] 섬 연결하기](https://programmers.co.kr/learn/courses/30/lessons/42861)

[백준 1197번 최소 스패닝 트리](https://www.acmicpc.net/problem/1197)