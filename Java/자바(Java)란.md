# Java의 특징
- 자바가상머신(Java Virtual Machine) 덕분에 .java 코드는 운영체제에 독립적이다
- 풍부한 클래스 라이브러리(java API)가 많아 짧은 시간에 프로그램 개발 가능
- 상속, 캡슐화, 다형성이 잘 적용된 객체지향 언어이다
- Garbage Collection가 자동적으로 메모리를 관리해주기 때문에 (프로그래머가 따로 메모리 관리할 필요가 없다)
- multi-thread 지원되고 관련된 API 지원, scheduling을 자바 인터프리터가 담당한다
- 동적 로딩(Dynamic Loading)을 지원하기 때문에 실행 시에 모든 클래스가 로딩되지 않고 필요한 시점에 클래스 로딩
  - 즉, 변경된 클래스만 컴파일하고 전체 애플리케이션은 다시 컴파일하지 않음


## java virtual machine
- java application(.java) <-compile(자바컴파일러,javac.exe)-> JVM(.class, 바이트코드, 컴파일된 자바코드) <-interpret(자바인터프리터,java.exe)-> OS <-> hardware
- 단, JVM이 OS에 종속적
- JDK(java development kit)를 설치하면 JVM과 Java API 등등 설치된다
- public static void main(String[] args)의 main method 선언부는 자바 interpreter(java.exe)에 의해 호출될 수 있도록 미리 약속된 부분이다


## java 기초
- 범위 주석: /* ... */, 한 줄 주석: //
- 출력: System.out.println("");


### 변수
- 변수(variable): 정수(int,long), 실수(float,double), 문자(char, String)
- 상수(constant): final int MAX_VAL;
- 리터럴(literal): 2014, 'a' 등등 (그 자체로 값을 의미하는 것)
  - 논리형(false,true)
  - 정수형(123,0b0101,077,0xFF,100L)
    - 접미사 L은 long type, 그 외는 int, byte와 short타입의 리터럴은 별도 존재하지 않고 int타입 사용
    - JDK1.7부터 정수형 리터럴의 중간에 구분자 가능(long big=100_000_000L);
  - 실수형(3.14,3.0e9,1.4f,0x1.0p-1)
    - 접미사 f는 float type, 접미사d는 double(생략가능)
  - 문자형('a','1')
    - char 문자형 ''안에는 반드시 하나의 문자가 필요
  - 문자열("ABC","A")
    - String 클래스이므로 객체를 생성하는 new연산자가 필요하지만 특별히 생략가능
    - 두 문자열 결합 가능(String str = "str" + "ing";)
- 자료형(data type): 값(data)의 종류(type)에 따라 값이 저장될 공간의 크기와 저장 형식을 정의한 것
  - 기본형(primitive type): 논리형(boolean), 문자형(char), 정수형(byte,short,int,long), 실수형(float,double) 등 계산을 위한 실제 값
    - 2<sup>10</sup> 대략 => 10<sup>3</sup>

| 자료형     | 저장 가능한 값의 범위                                                                                                        | 크기     | 
|---------------------------------------------------------------------------------------------------------------------|--------|-------| 
| boolean | false, true                                                                                                         |  1byte |
| char    | '\u0000' ~ '\uffff' (0 ~ 2<sup>16</sup>-1 => 0 ~ 65535)                                                             | 2byte  |
| byte       | -128 ~ 127 (-2<sup>7</sup> ~ 2<sup>7</sup>-1)                                                                       | 1byte  |
|short | -32,768 ~ 32,767(-2<sup>15</sup> ~ 2<sup>15</sup>-1 => -3x10<sup>4</sup> ~ 3x10<sup>4</sup>, 5자리수)                  | 2byte  |
|int| -2,147... ~ 2,147... (-2<sup>31</sup> ~ 2<sup>31</sup>-1 => -2x10<sup>9</sup> ~ 2x10<sup>9</sup>, 10자리수)            | 4byte  |
|long| -9,223... ~ 9,223... (-2<sup>63</sup> ~ 2<sup>63</sup>-1 => -9x10<sup>18</sup> ~ 9x10<sup>18</sup>, 19자리수)          | 8byte  |
|float| (1.4x10<sup>-45</sup> ~ 3.4x10<sup>38</sup>), (-3.4x10<sup>38</sup> ~ -1.4x10<sup>-45</sup>)       | 4byte  |
|double| (4.9x10<sup>-324</sup> ~ 1.8x10<sup>308</sup>), (-1.8x10<sup>308</sup> ~ -4.9x10<sup>-324</sup>) | 8byte  |

- 오버플로우(overflow): 2진수로 표현할 수 있는 bit앞에 '1'이 있다고 가정하고 계산한다
  - 부호없는 정수형의 오버플로우 (ex, char)
    - MAX_VALUE(1111<sub>2</sub>) + 1 = MIN_VALUE(0000<sub>2</sub>)
    - MIN_VALUE(0000<sub>2</sub>) - 1 = MAX_VALUE(1111<sub>2</sub>)
  - 부호있는 정수형의 오버플로우 (ex, byte,short,int,long)
    - MAX_VALUE(0111<sub>2</sub>, 7) + 1 = MIN_VALUE(1000<sub>2</sub>, -8)
    - MIN_VALUE(1000<sub>2</sub>, -8) - 1 = MAX_VALUE(0111<sub>2</sub>, 7)
- 참조형(reference type): 8개의 기본형을 제외한 나머지 타입, 객체의 주소(4byte 정수)를 저장
- 타입 간의 변환방법
  - 숫자 -> 문자: (char)(3+'0') -> '3'
  - 숫자 -> 문자열: 3+"" -> "3"
  - 문자 -> 숫자: '3'-'0' -> 3
  - 문자 -> 문자열: '3'+"" -> "3"
  - 문자열 -> 숫자: Integer.parseInt("3") -> 3, Double.parseDouble("3.14") -> 3.14
  - 문자열 -> 문자: "3".charAt(0) -> '3'

- 화면으로부터 입력받기
  - ```java
    import java.util.Scanner;
    public class Main {
      public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("두자리 정수를 입력해주세요");
        String input = scanner.nextLine();
        int num = Integer.parseInt(input);
        System.out.println("num=%d", num);
    ```
    


### 연산자

| 종류     | 연산자(operator)        | 설명              |
|--------|----------------------|-----------------|
| 산술 연산자 | + - * / % << >>      | 사칙 연산과 나머지 연산(%) |
| 비교 연산자 | > < >= <= == !=      | 크고 작음과 같고 다름을 비교 |
| 논리 연산자 | && ㅣㅣ ! & ㅣ ^ ~      | and와 or으로 조건을 연결 |
| 대입 연산자 | =                    | 우변의 값을 좌변에 저장 |
| 기타     | (type) ?: instanceof | 형변환 연산자, 삼항 연산자, instanceof연산자 |

- 연산자의 결합규칙
  1. 산술 -> 비교 -> 논리 -> 대입 순으로 대입이 제일 마지막에 수행된다
  2. 단항(1) -> 이항(2) -> 삼항(3) 순으로 단항 연산자의 우선순위가 높다
  3. 단항 연산자와 대입 연산자를 제외한 모든 연산의 진행방향은 (왼쪽에서 오른쪽)(-->)
- 증감 연산자
  - 전위형(j = ++i): 값이 참조되기 전에 증가
  - 후위형(j = i++): 값이 참조된 후에 증가
  - ```java
    int i=0, j=0;
    System.out.println(i++); //0
    System.out.println(++j); //1
    System.out.println(i,j); //1,1
    ```
- 형변환(casting) 연산자
  - (char)65 --> 'A'
  - (int)'A' --> 65
  - (float)10 --> 10.0f
  - (int)1.5f --> 1
  - 자동 형변환: 컴파일러가 생략된 형변환을 (기존의 값을 최대한 보존할 수 있는 타입으로) 자동적으로 추가해줌
    - 표현범위가 더 넓은 쪽으로 형변환됨 (ex, int -> float)
      - float f = 123; (123f)
    - byte b = 1000; ==> 단, 변수가 저장할 수 있는 **값의 범위보다 큰경우** 형변환 에러발생
    - byte b = (byte)1000; ==> 명시적으로 적어주면 값 손실이 있더라도 괜찮 (-24가 저장됨)
  - 산술 변환 : 이항 연산자는 두 피연산자(operand)의 타입(type)이 일치해야 연산이 가능하므로 서로 일치시키는 변환
    - operand의 값손실을 최소화하기 위해 표현범위가 넓은 쪽으로 변환됨
      - float f = 10 / 4; //(int / int) -> (2) -> (2.0f)
      - float f = 10 / 4.0f; //(int / float) -> (float / float) -> (2.5f)
    - operand의 타입이 int보다 작은 타입이면 int로 변환됨 (byte + short) -> (int + int)
      - ```java
        byte a = 1;
        byte b = 2;
        byte c = a + b; //컴파일 에러가 발생함. int형을 byte변수에 저장하려면 명시적으로 형변환 해야함
        byte c = (byte)(a + b);
        ```
    - 산술 변환을 활용해야 하는 경우
      - ```java
        int a = 1_000_000;
        int b = 2_000_000;
        long c = a * b; //-1454759936 ==> (int 타입으로 먼저 계산되어 오버플로우 발생)
        long c = (long)a * b; //2,000,000,000,000
        ```
- 비교 연산자
  - 기본형: 경우 변수에 저장된 값의 비교
  - 참조형: 참조변수가 같은 객체(object)의 비교(주소값의 비교)
    - equals(): 객체가 달라도 내용이 같으면 true를 반환
      - ```java
        String str1 = "abc";
        String str2 = new String("abc");
        
        System.out.println("abc"=="abc"); //true
        System.out.println(str1=="abc"); //true
        System.out.println(str2=="abc"); //false
        System.out.println(str2.equals("abc")); //true
        System.out.println(str2.equalsIgnoreCase("ABC")); //true
        ```
- 논리 연산자
  - (i%2==0 || i%3==0) && i%6!=0: i는2의배수 또는 3의배수이나 6의배수는 아니다
  - ('a'<=ch && ch<='z') || ('A'<=ch && ch<='Z'): 문자ch는 대문자 또는 소문자이다
- 조건 연산자
  - 삼항 연산자는 조건 연산자 하나뿐이다
  - result = (x > y) ? x : y: 둘 중 큰수를 저장한다
- 대입 연산자
  - 연산자들 중에서 가장 낮은 우선순위를 가짐, 진행 방향이 오른쪽에서 왼쪽
    - x=y=3 (y=3 -> x=y)
  - [lvalue] = [rvalue] 형태로 되어있고, lvalue는 반드시 변수처럼 값은 변경할 수 있어야 한다
    - ```java
      final int MAX = 3; //상수
      MAX = 10; //에러. 상수에 새로운 값을 저장할 수 없다
      ```
  - 복합 대입 연산자: 다른 연산자와 결합하여 사용될 수 있다
    - i += 3; <--> i = i + 3;
    - i *= 10 + j; <--> i = i*(10 + j);


### 조건문과 반복문
- if-else 문
  - ```java
    import java.util.Scanner;
    public class Main {
      public static void main(String[] args) {
        int score = 0;
        char grade = ' ';
      
        Systme.out.print("점수를 입력하세요");
        Scanner scanner = new Scanner(System.in);
        score = scanner.nextInt();
    
        if (score >= 90) {
          grade = 'A';
        } else if (score >= 80) {
          grade = 'B';
        } else {
          grade = 'C';
        }
        System.out.println("당신의 학점은 " + grade + "입니다.");
      }
    }
    ```
- switch 문
  - ```java
    public static void main(String[] args) {
      int num, result;
      final in ONE = 1;
      switch(result) {
        case '1':     //문자(정수49와 동일)
        case ONE:     //정수, 상수
        case "YES":   //문자열 가능(JDK 1.7부터)
        case num:     //에러. 변수는 불가
        case 1.0:     //에러. 실수도 불가
          break;
        default:
    ```
- 반복문
  - for(초기화; 조건식; 증감식;){...}
  - while(조건식) {}
  - do {} while(조건식);
  - ```java
    for(int i=1; i<=10; i++) {...}
    
    int i=1;
    while(i<=10) {i++;}
    
    int i=0;
    do {i++;} while (i<=10);
    ```
  - break문: 반복이 진행되는 도중에 만나면, 바로 반복문을 벗어남
  - continue문: 반복이 진행되는 도중에 만나면, 바로 끝으로 이동하여 다음 반복으로 넘어감
  - 이름 붙은 반복문
    - ```java
      Loop1: for(;;) {
        for(int i=0; i<=10; i++) {
          break Loop1;
          continue Loop1;
        }
      }
      ```


### 배열
- 배열(array): 같은 타입의 여러 변수를 하나의 묶음으로 다루는 것, 각 저장공간이 연속적으로 배치됨
  - int[] score = new int[5]; ```type[] name = new type[length]```
  - 생성된 배열의 각 저장 공간을 배열의 요소(element)라고 함
  - JVM이 모든 배열의 길이를 별도로 관리 --> ```name.length```를 통해서 얻음(변경 불가)
    - ```java
      int[] score = new int[]{1, 2, 3}; //배열의 생성과 초기화를 동시에
      int[] score = {1, 2, 3};
      int[] score;
      score = new int[]{1, 2, 3};
      score = {1, 2, 3}; //에러. 동시에 하지 않으면 생략 불가
      
      import java.util.Arrays;
      char[] chArr = {'a','b','c'};
      System.out.println(score); // I@14234bb
      System.out.println(Arrays.toString(score)); // [1, 2, 3]
      System.out.println(chArr); // abc --> char array 경우에만 이렇게 동작
      ```
  - 기본값으로 자동 초기화 됨

| 자료형            | 기본값         |
|----------------|-------------|
| boolean        | false       |
| char           | '\u0000'    |
| byte,short,int | 0           |
| long           | 0L          |
| float          | 0.0f        |
| double         | 0.0d or 0.0 |
| 참조형            | null        |      

- String 클래스: char배열에 다양한 기능(method)를 추가한 클래스
  - ```java

    //String클래스만 예외적으로 new 생성자 생략 가능
    String[] name = new String[2];
    name[0] = "Kim";  //new String("Kim");
    name[1] = "Park"; //new String("Park");

    //String 객체는 read만 가능하고 write는 안됨
    String str = "Java";
    str = str + "8"; //"Java8"이라는 새로운 문자열이 str에 저장됨
    ```
  - 변경 가능한 문자열을 다루려면, StringBuffer클래스를 사용
- 2차원 배열
  - ```java
    int[][] arr = new int[][]{{1,2,3},{4,5,6}}; //new int[][] 생략가능
    ```
- Arrays 클래스
  - ```java
    int[] arr = {0,1,2};
    int[][] arr2D = {{11,12},{21,22}};
    int[][] arr2D2 = {{11,12},{21,22}};
    System.out.println(Arrays.toString(arr)); // [0, 1, 2]
    System.out.println(Arrays.deepToString(arr2D)); // [[11, 12], [21, 22]]
    System.out.println(Arrays.equals(arr2D, arr2D2)); // false
    System.out.println(Arrays.deepEquals(arr2D, arr2D2)); // true
    
    System.out.println(Array.copyOf(arr, arr.length)); // [0, 1, 2]
    System.out.println(Array.copyOfRange(arr, 1, arr.length)); // [1, 2]
    System.out.println(Arrays.toString(Arrays.sort({1, 3, 0, 2}))); // [0, 1, 2, 3]
    ```
    
### 객체지향 프로그래밍

































