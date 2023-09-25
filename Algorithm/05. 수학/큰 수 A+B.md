# [BOJ] [10757. 큰 수 A+B](https://www.acmicpc.net/problem/10757)

## 1. 문제

두 정수 A와 B를 입력받은 다음, A+B를 출력하는 프로그램을 작성하시오.


| 시간 제한 | 메모리 제한 |
|:------|:-------| 
| 1 초   | 356 MB |


## 입력

첫째 줄에 A와 B가 주어진다. (0 < A,B < 10<sup>10000</sup>)

## 출력

첫째 줄에 A+B를 출력한다.

### 예제 입력 1

```
9223372036854775807 9223372036854775808
```

### 예제 출력 1

```
18446744073709551615
```



## 2. 소스 코드

1. python

```python
A, B = map(int, input().split())
print(A+B)
```

2. java

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;

public class Main {
    public static void main(String[] args) throws Exception {
        String[] numbers = new BufferedReader(new InputStreamReader(System.in)).readLine().split(" ");
        // 두 개의 수 중 가장 긴 자리수 길이를 구해둠
        int maxLength = Math.max(numbers[0].length(), numbers[1].length());

        int[] A = new int[maxLength + 1];	// 마지막 자리수 올림이 있을 수 있으므로 +1
        int[] B = new int[maxLength + 1];	// 마지막 자리수 올림이 있을 수 있으므로 +1

        // A 초기화
        for(int i = numbers[0].length() - 1, idx = 0; i >= 0; i--, idx++) {
            A[idx] = numbers[0].charAt(i) - '0';	// 맨 뒤 문자부터 역순으로 하나씩 저장
        }

        // B 초기화
        for(int i = numbers[1].length() - 1, idx = 0; i >= 0; i--, idx++) {
            B[idx] = numbers[1].charAt(i) - '0';	// 맨 뒤 문자부터 역순으로 하나씩 저장
        }

        for(int i = 0; i < maxLength; i++) {
            int value = A[i] + B[i];
            A[i] = value % 10;		// 더한 값의 10으로 나눈 나머지가 자리값이 됨
            A[i + 1] += (value / 10);	// 더한 값의 10으로 나눈 몫이 올림값이 됨
        }

        // A배열 역순 출력
        // 가장 높은 자리수가 0일 수도 있기 때문에 0이 아닐 경우에만 출력
        StringBuilder sb = new StringBuilder();
        if(A[maxLength] != 0) {
            sb.append(A[maxLength]);
        }

        for(int i = maxLength - 1; i >= 0; i--) {
            sb.append(A[i]);
        }
        System.out.println(sb);
    }
}

```


## 3. 참고

```
Java 기본 자료형의 크기
int	-2,147... ~ 2,147... (-2^31 ~ 2^31-1 => -2x10^9 ~ 2x10^9, 10자리수)    4byte
long	-9,223... ~ 9,223... (-2^63 ~ 2^63-1 => -9x10^18 ~ 9x10^18, 19자리수)  8byte
```



