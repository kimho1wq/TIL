# [BOJ] [1000. A+B](https://www.acmicpc.net/problem/1000)

### 1. 문제

``` 두 정수 A와 B를 입력받은 다음, A+B를 출력하는 프로그램을 작성하시오 ```

| 시간 제한 | 메모리 제한 |
|:------|:-------| 
| 2 초   | 128 MB |


#### 입력

``` 첫째 줄에 A와 B가 주어진다. (0 < A, B < 10) ```

#### 출력

``` 첫째 줄에 A+B를 출력한다. ```


#### 예제 입력 1

```
1 2
```

#### 예제 출력 1

```
3
```


### 2. 소스 코드

1. python

```python
a, b = map(int, input().split())
print(a+b)
```

2. java

```java
import java.io.IOException;
public class Main {
    public static void main(String[] args) throws IOException {
        int a = System.in.read() - '0';
        System.in.read();
        int b = System.in.read() - '0';

        System.out.println(a+b);
    }
}
```







