# [BOJ] 15650. N과 M (2)

## 1. 문제

자연수 N과 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오.

- 1부터 N까지 자연수 중에서 중복 없이 M개를 고른 수열
- 고른 수열은 오름차순이어야 한다.

| 시간 제한 | 메모리 제한 |
|:------|:-------| 
| 1 초   | 512 MB |


## 입력

첫째 줄에 자연수 N과 M이 주어진다. (1 ≤ M ≤ N ≤ 8)

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

### 예제 입력 1

```
3 1
```

### 예제 출력 1

```
1
2
3
```


### 예제 입력 2

```
4 2
```

### 예제 출력 2

```
1 2
1 3
1 4
2 3
2 4
3 4
```


### 예제 입력 3

```
4 4
```

### 예제 출력 3

```
1 2 3 4
```

## 2. 소스 코드

1. python

```python
def progression(n, m, i, j):
    if i == m:
        print(*[x for x in out])
        return
    for k in range(j, n+1):
        if visited[k] == True:
            continue
        out.append(k)
        visited[k] = True
        progression(n, m, i+1, k+1)
        out.pop()
        visited[k] = False
n, m = map(int, input().split())
visited = [False] * (n+1)
out = []
progression(n, m, 0, 1)

'''
import itertools
n, m = map(int, input().split())
for x in itertools.combinations([i for i in range(1, n+1)], m):
    print(*x)
'''
```

2. java

```java

```


## 3. 참고

```

```



