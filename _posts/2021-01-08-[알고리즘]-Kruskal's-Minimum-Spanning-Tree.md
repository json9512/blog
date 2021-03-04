---
layout: post
title:  "Kruskal's Minimum Spanning Tree"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/)

Kruskal's MST에 대해 알아보자

- 그리디 알고리즘이다
- 시간 복잡도: `O(E log V)`

**방법**
1. edges를 가중치를 기준으로 오름차순 정렬을 한다
2. 가장 작은 가중치를 지닌 edge를 정한 뒤, 현재 MST에 저장된 edge와 연결 했을 시 Cycle이 되는지 확인한다.
여기서 사용하는 알고리즘은 Union-Find 알고리즘이다
3. Cycle이 형셩되지 않으면 선택된 edge를 MST에 더해준다, 반면 Cycle이 형성되면 무시한다.
4. MST에 V-1 edge가 생성될 때까지 반복한다. 


# Algorithm visualization
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/71UQH7Pr9kU"></iframe>
</div>

# Python
```python
# 코드 출처: https://it-garden.tistory.com/411
def kruskal(graph, num_of_nodes):
    
    def find(u, disjointSet):
        # disjointSet 에서 
        # vertex u를 재귀적으로 찾는 함수다
        if u != disjointSet[u]:
            disjointSet[u] = find(disjointSet[u], disjointSet)
        return disjointSet[u]
    
    def union(u, v, disjointSet):
        # vertex u 와 v 를 union시키는 함수다
        root_a = find(u, disjointSet)
        root_b = find(v, disjointSet)
        disjointSet[root_b] = root_a
    
    # 그래프의 형태는 [[from, to, weight]] 형식이라고 가정한다
    # 그래프를 가중치를 기준으로 오름차순 정렬한다
    graph.sort(key= lambda x: x[2])

    # MST를 기록할 list를 만들어준다
    mst = []
    
    # Disjoint Set 도 기록해준다
    disjointSet = [0] # 0은 쓰지 않는 vertex다

    # 각 vertex를 disjointSet에 만들어준다
    for i in range(1, num_of_nodes + 1):
        disjointSet.append(i)
    
    # edges counter
    edges = 0

    # weight counter
    mst_cost = 0 

    # 크루스칼 알고리즘
    # edges가 V-1이 아니면 반복
    while edges != num_of_nodes - 1:
        frm, to, weight = graph.pop(0)

        # vertex u와 v가 다른 집합이면 Cycle이 아니라는 뜻
        if find(frm, disjointSet) != find(to, disjointSet): 
            union(frm, to, disjointSet)
            mst.append((frm, to))
            mst_cost += weight
            edges += 1

    return mst, mst_cost

# 그래프는 Prim 알고리즘 포스트에서 썼던 A,B,C 그래프를 숫자로 치환한 값이다
# A: 1, B: 2, C:3 ----- H:7
graph = [
    (1, 2, 2), # (A, B, 2)
    (1, 3, 3), # (A, C, 3)
    (2, 1, 2),
    (2, 3, 1),
    (2, 4, 1),
    (2, 5, 4),
    (3, 1, 3),
    (3, 2, 1),
    (3, 6, 5),
    (4, 2, 1),
    (4, 5, 1),
    (5, 2, 4),
    (5, 4, 1),
    (5, 6, 1),
    (6, 3, 5),
    (6, 5, 1),
    (6, 7, 1),
    (7, 6, 1) # (G, F, 1)
]

# node의 개수는 1-6
mst, mst_cost = kruskal(graph, 7)

print(mst)
print(mst_cost)
'''
Prim 알고리즘 포스트의 우선 순위 큐 MST와 비교하면 
Kruskal 알고리즘 MST도 잘 구해진 것을 알 수 있다.

mst: 
    1 (A) -> 2 (B) -> 4 (E) -> 5 (F) -> 6 (G) -> 7 (H) 
                |
                v
             3 (C)

mst_cost: 2 + 1 + 1 + 1 + 1 + 1 = 7
'''
```

# Kruskal 알고리즘을 사용할 수 있는 문제
- [백준 9372번 상근이의 여행](acmicpc.net/problem/9372)
- [백준 2887번 행성터널](https://www.acmicpc.net/problem/2887)
- [프로그래머스 지형이동](https://programmers.co.kr/learn/courses/30/lessons/62050)
- [LeetCode 1489번 어려운 문제](https://leetcode.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/)