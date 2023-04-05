# [BOJ]

## 1. 문제

LCS(Longest Common Subsequence, 최장 공통 부분 수열)문제는 두 수열이 주어졌을 때, 모두의 부분 수열이 되는 수열 중 가장 긴 것을 찾는 문제이다.

예를 들어, ACAYKP와 CAPCAK의 LCS는 ACAK가 된다.


| 시간 제한 | 메모리 제한 |
|:------|:-------| 
| 0.1 초 | 256 MB |


## 입력

첫째 줄과 둘째 줄에 두 문자열이 주어진다. 문자열은 알파벳 대문자로만 이루어져 있으며, 최대 1000글자로 이루어져 있다.


## 출력

첫째 줄에 입력으로 주어진 두 문자열의 LCS의 길이를 출력한다.


### 예제 입력 1

```
ACAYKP
CAPCAK
```

### 예제 출력 1

```
4
```




## 2. 소스 코드

1. python

```python
str1 = input()
str2 = input()
len_str1 = len(str1)
len_str2 = len(str2)
dp = [[0]*(len_str2+1) for _ in range(len_str1+1)]
for i in range(len_str1):
    for j in range(len_str2):
        if str1[i] == str2[j]:
            dp[i+1][j+1] = dp[i][j] + 1
        else:
            dp[i+1][j+1] = max(dp[i][j+1], dp[i+1][j])
print(dp[len_str1][len_str2])
```

2. java

```java

```


## 3. 참고

```

```



