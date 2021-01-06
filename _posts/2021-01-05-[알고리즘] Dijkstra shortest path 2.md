---
layout: post
title:  "[알고리즘] Dijkstra's Shortest Path 2"
tags: algorithm
comments: true
---

출처: [greenhelix 푸푸릇님의 post](https://greenhelix.tistory.com/130), [HackerEarth](https://www.hackerearth.com/practice/algorithms/graphs/shortest-path-algorithms/tutorial/)

전 [포스트]({{site.baseurl}}/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-Dijkstra-shortest-path/)에서 시간 복잡도 `O(V^2)`의 알고리즘을 살펴보았다. 

이번 포스트에서는 시간 복잡도 `O(V + E log V)`를 가진 다익스트라 최단 거리 알고리즘을 살펴보자

# 방법
1. 모든 거리 값을 `INF`로 지정 해주고 시작 노드의 거리 값만 `0`으로 지정해준다.
2. `(거리, 노드)` 형식으로 노드들을 min-우선 순위 큐에 저장한다. 우선 순위 큐는 `거리`를 기준으로 한다
3. 가장 적은 `거리` 값을 가진 노드를 우선 순위 큐에서 꺼낸다.
4. 꺼낸 노드와 연결 된 노드들의 거리 값을 계산 한다. 계산 방식은 `현재 노드까지 거리값 + 현재 노드에서 다음 노드까지 거리값 < 다음 노드의 현재 거리 값`을 사용한다. 거리 값이 갱신된 새로운 노드를 우선 순위 큐에 저장한다
5. 만약 꺼낸 노드가 이전에 사용한 노드라면, 무시하고 다음 노드를 꺼낸다
6. 우선 순위 큐가 비워질때가지 반복한다

# Python
부분적으로 먼저 살펴 본 후, 마지막에 전체 코드를 보도록 하자

```python
def createGraph():
    return {
        'A': {'B':1, 'C':4, 'D':2},
        'B': {'A':9, 'E':5},
        'C': {'A':4, 'F':15},
        'D': {'A':10, 'F':7},
        'E': {'B':3, 'J':7},
        'F': {'C':11, 'D':14, 'K':3, 'G':9},
        'G': {'F':12, 'I':4},
        'H': {'J':13},
        'I': {'G':6, 'J':7},
        'J': {'H':2, 'I':4},
        'K': {'F':6}
    }

graph = createGraph()
```
전과 같이 이걸 실행하면 아래와 같은 그래프가 `graph`에 저장된다.

<img src="{{ site.baseurl}}/images/dijkstra-mp-1.png" class="align-center" alt=""/>

```python
def dijkstra(graph, start):
    # set adj_node 
    adj_node = {}

    for node in graph:
        adj_node[node] = None
    
    # set all distance to INF
    distance_matrix = {node: float('inf') for node in graph}

    # starting position is 0
    distance_matrix[start] = 0

    # create min-priority-queue
    heap = []

    # add the starting point to priority queue
    heapq.heappush(heap, [distance_matrix[start], start])
```

위의 코드는 다익스트라 알고리즘에 쓸 변수들을 만들고 초기화 시켜주는 코드다

`adj_node` - 노드들이 최단 거리 값을 계산 할 때 사용했던 인접 노드 정보를 담고 있다

`distance_matrix` - 노드의 최단 거리 값을 가지고 있다

`heap` - 최단 거리 값을 기준으로 만들어진 우선 순위 큐

초기화 작업 시 `adj_node`는 다 `None`으로 해주고 `distance_matrix`도 모든 노드들이 `inf` 값을 가지게 선언 해준다.

시작 노드만 `distance_matrix`에서 거리값을 0으로 선언하고 시작 노드를 `heap`에 `[거리값, 노드]` 형식으로 넣어준다

이후 `while`문으로 `heap`에 노드들이 다 없어질때까지 아래의 작업을 반복한다. 

1. 우선순위 큐에서 노드를 꺼낸 뒤 거리값을 `distance`, 노드 이름을 `node`에 저장한다
```python
while heap:
    # Take out an item from the priority queue
    distance, node = heapq.heappop(heap)
```
2. `distance_matrix`에서 현 `node`의 거리 값이 `distance`보다 낮으면, 다음 iteration으로 넘어간다
```python
    # If this node was used before and has lesser
    # distance value than the new distance
    # skip this node
    if distance_matrix[node] < distance:
        continue 
```
3. 현 `node`와 연결된 모든 노드들을 `for` 문을 돌린다 
```python
    # Iterate through all the next nodes connected to 
    # the current node
    for next_node, next_node_distance in graph[node].items():
```
4. `for` 문에서 이하 반복한다
    1. 현재 노드의 최단 거리 값이랑 다음 노드로 가는 거리 값을 더한다
    ```python
        # Calculate the new distance using current node
        # to get to the next node
        new_distance = distance + next_node_distance
    ```
    2. `next_node`를 위해 갱신된 거리 값 `new_distance`가 `distance_matrix`에 저장 된 거리 값 보다 적으면, `distance_matrix` 값을 갱신해준다.
    <br>
    <br>
    이후 `[갱신된 거리값, 다음 노드]` 를 우선 순위 큐 `heap`에 넣어준다
    <br>
    `adj_node`에도 현재 `node`를 사용 했다고 저장해준다
    ```python
        # change the distance if new_distance is lesser
        if new_distance < distance_matrix[next_node]:
            distance_matrix[next_node] = new_distance

            # add the updated node to the priority queue
            heapq.heappush(heap, [new_distance, next_node])
            
            # update adj_node that next_node used current node
            adj_node[next_node] = node
    ```

## 전체 코드
```python
import heapq

def createGraph():
    return {
        'A': {'B':1, 'C':4, 'D':2},
        'B': {'A':9, 'E':5},
        'C': {'A':4, 'F':15},
        'D': {'A':10, 'F':7},
        'E': {'B':3, 'J':7},
        'F': {'C':11, 'D':14, 'K':3, 'G':9},
        'G': {'F':12, 'I':4},
        'H': {'J':13},
        'I': {'G':6, 'J':7},
        'J': {'H':2, 'I':4},
        'K': {'F':6}
    }

def dijkstra(graph, start):
    # set adj_node 
    adj_node = {}

    for node in graph:
        adj_node[node] = None
    
    # set all distance to INF
    distance_matrix = {node: float('inf') for node in graph}

    # starting position is 0
    distance_matrix[start] = 0

    # create min-priority-queue
    heap = []

    # add the starting point to priority queue
    heapq.heappush(heap, [distance_matrix[start], start])

    # if there are nodes in heap
    while heap:
        # Take out an item from the priority queue
        distance, node = heapq.heappop(heap)

        # If this node was used before and has lesser
        # distance value than the new distance
        # skip this node
        if distance_matrix[node] < distance:
            continue
        
        # Iterate through all the next nodes connected to 
        # the current node
        for next_node, next_node_distance in graph[node].items():
            
            # Calculate the new distance using current node
            # to get to the next node
            new_distance = distance + next_node_distance

            # change the distance if new_distance is lesser
            if new_distance < distance_matrix[next_node]:
                distance_matrix[next_node] = new_distance

                # add the updated node to the priority queue
                heapq.heappush(heap, [new_distance, next_node])
                
                # update adj_node that next_node used current node
                adj_node[next_node] = node

    return distance_matrix, adj_node

graph = createGraph()

initial = 'D'

distance_matrix, adj_node = dijkstra(graph, initial)

print(distance_matrix)
print(adj_node)

# Show shortest path from 'D' to 'H'
destination = 'H'
print('Path between {} to {}'.format(initial, destination))
print(destination, end=' <- ')
while True:
    destination = adj_node[destination]

    if destination == None:
        print("")
        break
    
    print(destination, end=' <- ')

# prints: H <- J <- E <- B <- A <- D <-
```

`dijkstra` 함수는 거리 값 `distance_matrix`랑 사용한 인접 노드 정보 `adj_node`만 주고 실질적으로 최단거리를 계산하려면 `adj_node` 사용해야 한다

코딩 테스트에서는 대부분 위와 같이 우선 순위 큐로 구현하지 않으면 효율성 테스트에서 실패 할 수도 있다