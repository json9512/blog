---
layout: post
title:  "[알고리즘] Job sequencing algorithm"
tags: algorithm
comments: true
---

출처: [GeeksForGeeks](https://www.geeksforgeeks.org/job-sequencing-problem/)


Job sequencing, deadline scheduling 알고리즘에 대해 알아보자

- 그리디 알고리즘
- N 개의 `[일 ID, 마감일, 이윤]`가 주어 질 때, 최대 마감일이 되었을 때 최대 이윤을 구하는 알고리즘.
- 마감일은 1 이상이고 마감일 전에 일을 끝내야 이익을 얻을 수 있다.
- 시간은 unit time이다. (eg. 1 = 1일)

**방법**
- 모든 데이터를 `이윤`을 기준으로 내림차순 정렬을 한다
- 데이터를 iterate하면서 다음을 반복한다:
    1. 빈 시간 i를 찾는다. 찾는 기준은 i < deadline이면서 데이터 중에서 i가 가장 커야한다.
    2. i를 빈 자리에 넣고 표시를 한다
    3. 조건에 맞는 i가 없으면 넘어간다

# Python
```python
def jobsequencing(jobs, time):
    # 이윤을 기준으로 내림차순으로 정렬 
    # 데이터는 [job_id, deadline, profit]으로 됐다
    jobs.sort(key=lambda x: x[2], reverse=True)

    # 빈 자리를 표시하는 리스트
    free_slots = [False] * time

    # 결과 값을 저장하는 리스트
    answer = [-1] * time
    print(jobs)
    # 주어진 jobs를 iterate한다
    for i in range(len(jobs)):
        
        # 이 일을 위한 다음 빈 자리를 찾는다
        # 가능한 자리에서부터 시작한다
        for j in range(min(time-1, jobs[i][1]-1), -1, -1):

            # 빈자리 발견 시
            if not free_slots[j]:
                free_slots[j] = True
                answer[j] = jobs[i][0] # job ID를 넣는다
                break

    return answer

jobs = [
    ['a', 2, 100],  # [job, deadline, profit]
    ['b', 1, 19], 
    ['c', 2, 27], 
    ['d', 1, 25], 
    ['e', 3, 15]
]

# lambda function denotes the max function to find
# the maximum value for deadline
job_max_deadline = max(jobs, key=lambda x: x[1])
ans = jobsequencing(jobs, job_max_deadline[1])

print(ans) # ['c', 'a', 'e']
```

# Job sequencing을 사용하는 문제
[Leetcode 1234번 Maximum profit in job scheduling](https://leetcode.com/problems/maximum-profit-in-job-scheduling/)