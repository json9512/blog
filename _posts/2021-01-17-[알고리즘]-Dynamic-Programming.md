---
layout: post
title:  "Dynamic Programming"
tags: algorithm
comments: true
---
출처: [GeeksforGeeks](https://www.geeksforgeeks.org/overlapping-subproblems-property-in-dynamic-programming-dp-1/)

다이나믹 프로그래밍에 대해 알아보자

다이나믹 프로그래밍이란 복잡한 문제를 더 작은 문제로 쪼개서 최적의 해를 구하는 방법이다. 작은 문제들의 답을 저장하여 재계산을 하지 않는 것이 중요하다.

#### 그리디 알고리즘과 다이나믹 프로그래밍의 다른 점?
그리디 알고리즘은 *작은 문제*에서 최적의 답안을 찾아서 *복잡한 문제*를 다 풀 때까지 반복하는 것이다. 그래서 작은 문제에서 최적의 답을 찾았다면 다른 선택사항은 고려하지 않는다. 복잡한 문제를 전체적으로 봤을 때 최적의 답이 안 나올 수 있다.

반면 다이나믹 프로그래밍은 *작은 문제*를 해결 할 때 이전에 풀었던 *작은 문제*의 답을 불러올 수 있기 때문에 다른 선택사항들도 고려한다. 즉 다이나믹 프로그래밍으로 문제를 풀게 되면 *복잡한 문제*를 전체적으로 보았을 때 가장 최적의 답안이 나온다.

예를 들면, A에서 B로가는 가장 최단 거리를 계산하는 문제라고 가정해보자.

그래프는 다음과 같은 형식이다
```python
# A --2- C ---
# |           1
# 1           |
# |           v
# D --- 5 --- B
```

여기서 그리디 알고리즘을 사용하면, 현재 위치에서 가장 작은 거리 값을 반복해서 고르기 때문에 최단 거리는 `A->D (1) + D->B (5) = 6`이 된다.

하지만 다이나믹 프로그래밍을 사용하면 `A->C (2) + C->B (1) = 3`이 되면서 원했던 최단 거리를 구할 수 있게 된다.

# 언제 다이나믹 프로그래밍을 쓸 수 있나?
주어진 문제에 아래의 3가지 특징이 있다면 다이나믹 프로그래밍으로 풀 수 있다.
1. Overlapping sub-problems: 동일한 작은 문제를 지속적으로 풀어야 하는 상황이 있을 때
2. Optimal substructure: 문제의 최적의 해결법에 작은 문제의 최적의 해결법이 포함이 될 때
3. Simple sub problems: 주어진 복잡한 문제를 같은 형태의 더 작은 문제로 나눌 수 있어야 한다

# Memoization 과 Tabulation
Memoization을 사용하면 Top-down 방식으로 다이내믹 프로그래밍을 구현할 수 있다.

방법은 다음과 같다.

1. 작은 문제들의 해결안을 담을 lookup table을 만든다
2. 작은 문제들을 해결 할 때 마다, 
    - 현재 작은 문제가 lookup table에 없으면
        - 작은 문제를 해결한 뒤 lookup table에 저장한다
    - 있으면 해결 값을 불러와서 사용한다

Fibonacci 수열을 Memoization을 활용해서 사용하면 다음과 같다

```python
def fibo(n, lookup):
    if n == 0 or n == 1:
        lookup[n] = n
    
    # lookup table에 계산된 값이 없으면, 현재 문제를 
    # 계산해서 넣어 준다
    if lookup[n] is None:
        lookup[n] = fibo(n-1, lookup) + fibo(n-2, lookup)
    
    return lookup[n]

if __name__ == "__main__":
    n = 34

    # lookup table 선언
    lookup = [None] * 100
    print(fibo(n, lookup))
```

Tabulation 방식을 사용하면 Bottom-Up 방식으로 다이내믹 프로그래밍을 구현 할 수 있다.

Memoization과 다른 점은, Tabulation 방식은 답에 도달 할 때까지 계속해서 sub-problem을 구하는 것이다.
답에 도달 했다면, table에 마지막 값이 답이 되는 것이다.

같은 Fibonacci 수열 문제를 Tabulation으로 구현하면 다음과 같다.
```python
def fibo(n):
    # table 선언
    f = [0] * (n+1)

    # 피보나치 수열의 base case 작성
    f[1] = 1

    # 답에 도달 할 때까지 계속해서 문제를 푼다
    for i in range(2, n+1):
        f[i] = f[i-1] + f[i-2]
        
    return f[n]

if __name__ == "__main__":
    n = 34
    print(fibo(n))
```

Memoization과 Tabulation중 어떤 것을 써야하나? 

- Memoization은 재귀적 호출을 기반으로 한다
- Tabulation은 Iterative하게 문제를 푼다
- Memoization은 굳이 모든 sub problem의 해결안을 구하지 않는다. 필요한 부분만 구하기 떄문이다
- Tabulation은 모든 sub problem의 해결안을 구한다
- Memoization의 재귀 호출은 컴퓨터가 실제로 스택내에 새로운 메모리 위치에 함수와 인자 값을 저장하기 때문에 같은 조건의 iterative 한 방식보다 더 느릴 수 있다

알아서 상황에 맞게 쓰도록 하자.