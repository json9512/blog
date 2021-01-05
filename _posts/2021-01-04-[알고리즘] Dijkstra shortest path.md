---
layout: post
title:  "[알고리즘] Dijkstra's Shortest Path"
tags: python algorithm dijkstra
comments: true
---

출처: [GeeksforGeeks](https://www.geeksforgeeks.org/dijkstras-shortest-path-algorithm-greedy-algo-7/), [Baeldung](https://www.baeldung.com/cs/prim-dijkstra-difference#:~:text=In%20the%20computation%20aspect%2C%20Prim's,only%20works%20on%20undirected%20graphs), [cppsecrets](https://cppsecrets.com/users/1032115979910410511011497115116111103105505149484957575564103109971051084699111109/Python-implementation-of-Dijkastras-Shortest-Path-Algorithm.php)

유명한 다익스트라의 최단 거리 찾기 알고리즘

Prim's Minimum Spanning Tree 알고리즘과 유사하다. 

그리디 알고리즘이다.

다른점:
- 다익스트라 알고리즘은 **최단거리**를, 프림의 알고리즘은 **Minimum Spanning Tree**를 찾는 알고리즘이다
- 다익스트라 알고리즘은 undirected, directed graph 모두 적용이 되지만 프림의 알고리즘은 undirected graph에만 적용된다
- 다익스트라 알고리즘은 edge의 weight가 단 하나라도 음수(negative)면 최단 거리를 찾지 못 할수도 있다

간단한 예를 들자면, 다익스트라 알고리즘은 가장 적은 시간/연료로 A 포인트에서 B 포인트까지 길을 찾는 것이고 프림의 알고리즘은 가장 적은 시간/재료로 여러개의 포인트가 서로 연결 되도록 (사이클 없이) 도로를 까는 것이다.

# 방법
1. 최단 거리 경로를 저장하는 set를 만든다. (empty set)
2. 모든 vertices 간의 거리 값을 지정한다. 시작점을 제외한 모든 vertices 거리 값은 무한대가 된다. 시작점은 0으로 만든다
3. 모든 vertices가 최단 거리 경로를 저장하는 set에 들어올때까지 이하 반복 한다:
    1. set에 포함되지 않는 vertices 중 최단 거리를 가지고 있는 vertex u를 고른다
    2. set에 포함 시킨다
    3. vertex u 와 인접한 모든 vertices의 거리 값을 갱신한다.


# Python
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

# set starting point
initial = 'D'

# keeps track of paths
path = {}
adj_node = {}

# keeps track of nodes yet to visit
queue = []

# create adjacency graph with distance values 
# intialized as infinity 
# except the initial (starting) node 
for node in graph:
    path[node] = float('inf')
    adj_node[node] = None
    queue.append(node)

path[initial] = 0
# while queue
while queue:

    # for visualization
    '''
    print("Graph: \n", graph)
    print("Path: \n", path)
    print("Adj_nodes: \n", adj_node)
    print("Queue (nodes to visit): \n", queue)
    '''

    # find the node with the 
    # minimum distance value
    min_key = queue[0]
    min_val = path[min_key]

    for n in range(1, len(queue)):
        if path[queue[n]] < min_val:
            min_key = queue[n]
            min_val = path[min_key]

    # update the current node as the node with the
    # minimum distance
    current = min_key

    # remove the node from 'to visit' queue
    queue.remove(min_key)

    # iterate through the nodes
    # Update the distance value using the current node
    for i in graph[current]:
        alternate = graph[current][i] + path[current]
        
        if path[i] > alternate:
            path[i] = alternate
            adj_node[i] = current

destination = 'H'
print('Path between {} to {}'.format(initial, destination))
print(destination, end=' <- ')
while True:
    destination = adj_node[destination]

    if destination == None:
        print("")
        break
    
    print(destination, end=' <- ')

# prints:
# H <- J <- E <- B <- A <- D <-
```

# 자세히

위의 코드를 조금 더 자세히 살펴보자

```python
def create_graph():
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

graph = create_graph()
```

이 코드를 실행 하면 아래와 같은 그래프가 형성된다

<img src="{{ site.baseurl}}/images/dijkstra-mp-1.png" class="align-center" alt=""/>

우리의 목적은 'D' 에서 'H' 까지 최단 거리를 찾는 것 이다.

```python
# set starting point
initial = 'D'

# keeps track of paths
path = {}
adj_node = {}

# keeps track of nodes yet to visit
queue = []

# create adjacency graph with distance values 
# intialized as infinity 
# except the initial (starting) node 
for node in graph:
    path[node] = float('inf')
    adj_node[node] = None
    queue.append(node)

path[initial] = 0
```

시작 노드 `initial`을 지정 해주고 추후에 사용할 `path` 와 `adj_node`를 선언해준다

`path`는 시작 노드에서 특정 노드까지 최단 거리 값을 저장 시키기 위한 것이고

`adj_node`는 특정 노드의 최단 거리 값을 계산했을때 사용한 인접한 노드를 저장해 주는 것이다.

`queue`는 현재 최단 거리 값을 계산 해야 할 노드들을 저장한 리스트다.

이후 `for` 문에서 그래프 속 각 노드들의 최단 거리 값을 `inf`로 선언해주고 `adj_node` 값도 초기화 시켜준 뒤 `queue`에 저장한다

`path[initial] = 0` 로 시작 노드를 지정해준다

```python
# while queue
while queue:
    # find the node with the 
    # minimum distance value
    min_key = queue[0]
    min_val = path[min_key]

    for n in range(1, len(queue)):
        if path[queue[n]] < min_val:
            min_key = queue[n]
            min_val = path[min_key]

    # update the current node as the node with the
    # minimum distance
    current = min_key

    # remove the node from 'to visit' queue
    queue.remove(min_key)
```

이후 `queue`에 노드가 없어질때가지 `while`문을 돌린다.

`min_key` - 최단 거리 값을 가지고 있는 노드

`min_val` - 최단 거리 값

`min_key` 와 `min_val`을 `queue`의 첫 번째 노드로 선언해준 뒤 `queue`에 남아 있는 나머지 노드들과 비교를 하며 가장 거리 값이 낮은 노드를 찾는다

이후 찾은 노드를 `current`에 저장해주고 `queue`에서 해당 노드를 지운다

```python
    # iterate through the nodes
    # Update the distance value using the current node
    for i in graph[current]:
        alternate = graph[current][i] + path[current]
        
        if path[i] > alternate:
            path[i] = alternate
            adj_node[i] = current
```

이후 현 노드 (최단 거리 값을 가진)의 인접 노드들을 `for`문을 돌린다

`alternate` - 현재 노드와 인접한 노드의 거리 값 + 현재 노드까지의 최단 거리 

*예를 들자면, `current`가 `A`면 인접 노드는 `B`, `C`, `D` 가 되고, 위에 `for`문이 각각의 인접 노드의 거리 값을 현재 노드 `A`의 최단 거리 값과 더 한다 (`alternate`). 즉 `A`를 이용해서 각 인접 노드로 가는 거리 값이다*

이 `alternate` 값과 현재 인접 노드의 거리값 `path[i]`를 비교해서 `alternate`가 더 작을 경우,

인접노드의 최단 거리 값(`path[i]`)을 `alternate`로 바꾸고 `adj_node`에 현재 노드 (`current`)를 저장해 준다. 

*예를 들면 `A`의 인접노드 `B`를 `A`를 통해서 가게 됐을때 그 값이 현재 `B`의 최단 거리 값 보다 작으면, `B`의 값을 바꿔주고 `A`를 사용했다고 `adj_node`에 명시해 준다*

여기까지만 코드를 실행 시키면 다익스트라 알고리즘을 이용해 시작 노드 `D`에서 나머지 노드들까지 가는 최단 거리가 계산된다.

```python
destination = 'H'
print('Path between {} to {}'.format(initial, destination))
print(destination, end=' <- ')
while True:
    destination = adj_node[destination]

    if destination == None:
        print("")
        break
    
    print(destination, end=' <- ')
```

위의 코드를 실행 시키면 `destination`에 저장된 목적지 노드까지의 경로가 나온다. 

위 코드는 `while` 문만 간단히 설명하자면

`destination = adj_node[destination]` 로 `while`문이 돌때마다 현재 노드(`destination`)를 현재 노드의 최단 거리 값을 계산 할 때 사용했던 인접 노드(`adj_node[destination]`)로 바꿔준다

이후 `if`문으로 `destination` 이 `None`일때 `while`문을 종료시키면 시작 노드 `initial`에서 목적지 노드 `destination`까지의 최단 경로를 알 수 있다.

실제로 확인을 해보면 `D`에서 `A`를 통해 `H`를 가는 경로와 `F`를 통해 `H`로 가는 경로 값을 비교해보면 `A`가는 경로가 최단 경로인 것을 알 수 있다.

`A`를 통할 경우: 10 + 1 + 5 + 7 + 2 = 25

`F`를 통할 경우: 7 + 9 + 4 + 7 + 2 = 29

위 알고리즘의 Time Complexity는 `O(V^2)`이다.

하지만 우선 순위 큐를 사용하면 시간 복잡도를 `O(V + E log V)` 까지 줄일 수 있다. 관련 [포스트]({{site.baseurl}}/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-Dijkstra-shortest-path-2/)

# Dijkstra 최단 경로 알고리즘을 사용한 문제
[백준 다익스트라로 분류된 문제](https://www.acmicpc.net/problemset?sort=ac_desc&algo=22)

[[프로그래머스] 배달](https://programmers.co.kr/learn/courses/30/lessons/12978)

[[프로그래머스] 가장 먼 노드](https://programmers.co.kr/learn/courses/30/lessons/49189)

요런 문제들을 풀려면 `O(V^2)`의 시간 복잡도로는 풀 수가 없다.