---
layout: post
title:  "[알고리즘] Minimum number of coins algorithm"
tags: algorithm
comments: true
---

출처: [GeeksForGeeks](https://www.geeksforgeeks.org/greedy-algorithm-to-find-minimum-number-of-coins/)


Minimum number of coins 알고리즘에 대해 알아보자

- 그리디 알고리즘
- 돈 V 가 주어졌을 때, 최소 개수의 동전/지폐로 거슬러 줄 수 있는 알고리즘
- 돈 `V = 70`이고 지폐 `[50, 20, 10]` 이 주어졌을 때, 최소 개수의 지폐는 `[50, 20]`다

**방법**
- 내림차순으로 동전/지폐를 정렬
- 현재 돈 보다 적은 denomination을 찾는다
- 찾은 denomination을 결과값에 더해주고 현재 돈에서 denomination을 빼준다
- 현재 돈이 0이 될 때까지 반복  

# Python
```python
def min_num_coin(value, coins):
    # initialize ans list
    ans = []

    # sort the denominations, coins
    coins.sort()

    # traverse through all denominations (동전/화폐 단위)
    # from the largest -> smallest
    i = len(coins) - 1
    while i >= 0:
        # find denominations
        while value >= coins[i]:
            value -= coins[i]
            ans.append(coins[i])
        
        i -= 1
    
    return ans

# Test
value = 70
coins = [5, 10, 50, 15, 20]
print(min_num_coin(value, coins)) # [50, 20]
```