# [BOJ] 9663. N-Queen

## 1. 문제

N-Queen 문제는 크기가 N × N인 체스판 위에 퀸 N개를 서로 공격할 수 없게 놓는 문제이다.

N이 주어졌을 때, 퀸을 놓는 방법의 수를 구하는 프로그램을 작성하시오.

| 시간 제한 | 메모리 제한 |
|:------|:-------| 
| 10 초  | 128 MB |


## 입력

첫째 줄에 N이 주어진다. (1 ≤ N < 15)

## 출력

첫째 줄에 퀸 N개를 서로 공격할 수 없게 놓는 경우의 수를 출력한다.

### 예제 입력 1

```
8
```

### 예제 출력 1

```
92
```




## 2. 소스 코드

1. python
00 01 02  y-x    -2 -1 0 1 2 +n
10 11 12  x+y 
20 21 22
```python
'''
# PyPy3로 해야 통과
answer = [0, 1, 0, 0, 2, 10, 4, 40, 92, 352, 724, 2680, 14200, 73712, 365596]
print(answer[int(input())])
'''
def progression(n, i):
    if i == n:
        out.append([x for x in queen])
        return
    for y in range(n):
        if column[y] != 0 or right_diagonal[i + y] != 0 or left_diagonal[n + y - i - 1] != 0:
            continue
        queen.append((i, y))
        row[i] += 1
        column[y] += 1
        right_diagonal[i + y] += 1
        left_diagonal[n + y - i - 1] += 1
        progression(n, i + 1)
        queen.pop()
        row[i] -= 1
        column[y] -= 1
        right_diagonal[i + y] -= 1
        left_diagonal[n + y - i - 1] -= 1


n = int(input())
queen = []
out = []
row = [0] * n
column = [0] * n
right_diagonal = [0] * (2 * n - 1)  # /
left_diagonal = [0] * (2 * n - 1)  # \
progression(n, 0)
print(len(out))
```

2. java

```java

```


## 3. 참고

```

```



