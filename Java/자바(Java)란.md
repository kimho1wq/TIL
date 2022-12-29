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
  - ```javascript
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
- 재사용성, 유지보수, 중복된 코드의 제거
- 객체의 구성요소
  - 클래스(TV 설계도) <-> 객체 (TV 제품)
    - 클래스의 인스턴스화(instantiate): 클래스로부터 객체를 만드는 과정
    - 객체: 어떠한 클래스의 인스턴스(instance)
  - 멤버: 변수와 메소드
    - 변수(variable) : 크기, 길이, 높이 등의 속성(property)
    - 메소드(method) : 끄기, 켜기 등의 기능(function)
- 클래스파일(.class)는 컴파일 시 클래스마다 하나씩 만들어짐
  - 하나의 소스파일에 둘 이상의 public class가 존재하면 안됨
  - 소스파일의 이름은 public class의 이름과 일치해야함
- 변수의 종류
  - 클래스 변수(class variable): 클래스가 메모리에 올라갈 때 생성(static변수, 공유변수)
  - 인스턴스 변수(instance variable): 객체(isntance)가 생성될 때 생성
  - 지역변수(local variable): 변수 선언문이 수행되었을 때 생성
  - ```java
    class Card {
      //class variables(공통 속성)
      static int width=100; //폭
      static int height=250; //높이
    
      //instance variables(: )개별 속성)
      String kind; //무늬
      int num; //숫자
    }
    ```
- 호출스택(call stack)
  - 메소드의 작업에 필요한 메모리 공간을 할당받음
  - JVM에 의해서 main메소드가 호출됨으로써 프로그램이 시작
- static 메소드와 변수
  - instance 멤버가 존재하는 시점에 class 멤버는 항상 존재
  - class 멤버가 존재하는 시점에 instance 멤버가 존재하지 않을 수 있다
  - static 을 붙여야 하는 경우
    1. 멤버변수 중 모든 instance에 공통으로 사용하는 것
    2. instance를 생성하지 않고 사용하지 않는 것
- 오버로딩(overloading)
  - 한 클래스 내에 같은 이름의 메소드를 여러 개 정의하는 것
    1. 메소드 이름이 같아야 함
    2. 매개변수의 개수 또는 타입이 달라야 함
    3. 변환 타입은 관계 없음
  - 조건을 만족 못하면 중복 정의로 컴파일 에러 발생
- 생성자(constructor)
  - instance가 생성될 때 호출되는 초기화 메소드
    1. 생성자의 이름은 클래스의 이름과 같야하 함
    2. 생성자는 리턴 값이 없음
    3. 오버로딩 가능
  - new 연산자가 instance를 생성하고, constructor는 단순히 변수들의 초기화에 사용됨
  - 클래스 내에 **생성자가 하나도 없다면** 컴파일러가 기본 생성자(default constructor)를 정의해 줌
  - this: 객체(instance) 자신을 가리키는 참조변수
    - static 메소드에서는 사용 불가
    - 생성자의 이름으로 클래스이름 대신 this를 사용해야 함
    - 한 생성자에서 다른 생성자를 호출할 때는 반드시 첫 줄에서 만 호출 가능
      - ```java
        Class Car {
          String color;
          
          Car(String color) {
            this.color = color; // 첫 번째 줄
            Car(color, "auto"); // 에러. --> 첫 번째 줄에 this(color, "auto"); 로 호출해야 함
        ```
- 멤버변수의 초기화
  - 클래스 변수, 인스턴스 변수와 배열의 초기화는 자동으로 됨
  - 초기화 블럭(initialization block)
    - 클래스(static) 블럭 -> 인스턴스(instance) 블럭 -> 생성자(constructor) 순으로 실행 됨
      - ```java
        class Car {
          static {
            System.out.println("static");
          }
          {
            System.out.println("instance");
          }
          Car() {
            System.out.println("constructor");
          }
        ```
- 상속
  - 기존의 클래스를 재사용하여 새로운 클래스를 작성하는 것
  - ```java
    class Parent {}
    class Child extends Parent {}
    ```
  - 자손 클래스는 조상 클래스의 모든 멤버를 상속 받는다
  - 생성자와 초기화 블럭은 상속되지 않는다
  - 자손 클래스의 객체를 생성하면 조상 클래스의 멤버도 함께 생성된다
  - 상속관계: is-a(~은 ~이다)
    - **(x)** 원(Circle)은 점(Point)이다 
  - 포함관계: has-a(~은 ~을 가지고있다)
    - **(o)** 원(circle)은 점(Point)을 가지고 있다 
  - Object 클래스는 모든 클래스 상속계층도의 최상위에 있는 조상 클래스
    - toString(), equals(Object o) 등의 메소드를 사용 가능
  - super: 조상 클래스로부터 상속받은 멤버를 참조하는데 사용되는 참조변수
    - 모든 instance 메소드는 this와 super가 지역변수로 존재함
- 오버라이딩(overriding)
  - 조상 클래스로부터 상속받은 메소드를 변경하는 것
    1. 메소드의 선언부(이름, 매개변수, 반환타입)은 완전히 일치해야 함
    2. 접근 제어자(access modifier)는 조상 클래스의 메소드보다 좁은 범위로 변경할 수 없다
    3. 예외(exception)는 조상 클래스의 메소드보다 많이 선언할 수 없다
- 패키지(package)
  - 클래스의 묶음으로 클래스들을 효율적으로 관리하기 위한 디렉토리
  - ```java
    package 패키지명;
    import 패키지명.클래스명;
    또는 import 패키지명.*;
    ```    
  - 모든 클래스는 반드시 하나의 패키지에 포함되어야 한다
  - 루트 디렉토리를 클래스패스(classpath)에 포함시켜야 실행 시 JVM이 찾을 수 있다
  - import static문은 static 멤버를 호출할 때 클래스 이름을 생략할 수 있다
    - ```java
      import static java.lang.Integer.*;  //Integer클래스의 모든 static메소드
      import static java.lang.System.out; //System.out을 out만으로 참조가능
      ```
- 제어자(modifier)
  - 접근 제어자: public, protected(같은 패키지 + 다른 패키지의 자손), default(같은 패키지), private
    - 클래스의 내부에 선언된 데이터를 보호하기 위해 사용: 캡슐화(encapsulation), 데이터 감추기(data hiding)
  - static: 멤버변수, 메소드, 초기화 블럭에 사용 가능
  - final: 클래스, 메소드, 멤버변수, 지역변수에 사용 가능
    - final class: 변경될 수 없는 클래스, 조상(상속)이 될 수 없다
    - final method: 변경될 수 없는 메소드, 오버라이딩(상속)을 통해 재정의 될 수 없다 
  - abstract: 클래스, 메소드에 사용 가능
    - 추상 클래스의 인스턴스는 생성불가
  - 그 외: native, transient, synchronized, volatile, strictfp
- 다형성(polymorphism)
  - 여러 가지 형태를 가질수 있는 능력
  - 자바에선 조상클래스 타입의 참조변수로 자손클래스의 인스턴스를 참조할 수 있음
    - 단, 조상타입의 참조변수로는 자손타입의 모든 멤버를 사용할 순 없음
      - ```java
        class SmartTv extends Tv {...}
        SmartTv st = new SmartTv();
        Tv t = new SmartTv();
        SmartTv st = new Tv(); //에러.                                                                                      
        ```
  - instanceof 연산자: 참조변수가 참조하고 있는 인스턴스의 실제 타입을 알아보기 위해 주로 사용
    - ```java
      Tv t = new SmartTv();
      if (t instance of SmartTv) {
        SmartTv st = (SmartTv)t;
      }
      ```
- 추상 클래스(abstract class)
  - 추상 메소드를 포함하고 있는 클래스, 인스턴스 생성 불가
  - ```java
    abstract class Unit {
      int x, y;
      abstract void move(int x, int y);
    }
    class Marine extends Unit {
      void move(int x, int y){...}
    }
    class Tank extends Unit {
      void move(int x, int y){...}
    }
    ```
- 인터페이스(interface)
  - 추상메소드와 상수만을 멤버로 가질 수 있다
    - 모든 멤버변수는 public static final 이어야 함 (생략 가능)
    - 모든 메소드는 public abstract 이어야 함 (생략 가능)
    - 단, static메소드와 default메소드는 예외 (JDK1.8 부터)
  - 구현하는 인터페이스의 메소드 중 일부만 구현한다면, abstract를 붙여야 함
  - 리턴타입이 인터페이스라는 것은 메소드가 해당 인터페이스를 구현한 클래스의 인스턴스란 의미
    - ```java
      abstract class Marine implements Unit {...}
      class Fighter implements Fightable {...}
      Fightable m() {
        return new Fighter();
      }
      ```
  - 디폴트 메소드(default method)
    - 추상 메소드가 아니기 때문에, 새로 추가되어도 해당 인터페이스를 구현한 클래스를 변경하지 않아도 됨
    - 일반 메소드처럼 몸통 {}이 있어야 함
    - 접근 제어자는 public이며 생략 가능
- 내부 클래스(inner class)
  - 외부 클래스(outer class)를 제외하고 다른 클래스에서 잘 사용되지 않을 때 사용
  - 변수의 선언위치에 따라 내부 클래스의 특징이 바뀜
    - 익명 클래스(anonymous class): 클래스 선언과 객체의 생성을 동시에 하는 일회용
    - 스태틱 클래스(static class): 외부 클래스의 static 메소드에서 사용될 목적
      - static멤버를 선언 가능, 다른 클래스에서는 불가능
- 익명 클래스(anonymous class)
  - 클래스 선언과 객체의 생성을 동시에 하는 일회용
  - 생성자 없음, 상속 가능, 하나의 인터페이스만 구현 가능
  - ```java
    class Main {
      Object i = new Object(){ void method(){} }; //익명 클래스
      static Object c = new Object(){ void method(){} }; //익명 클래스
    
      public static void main(String[] args) {
        Button b = new Button("Start");
        b.addActionListener(new ActionListener() {
          public void actionPerformed(ActionEvent e) {
            System.out.println("occuredd!");
          }
        }); //익명 클래스의 끝
      }
    }
    ```

### 예외 처리
- 프로그램 오류
  - 컴파일 에러: 컴파일 시에 발생
  - 런타임 에러: 실행 시에 발생
  - 논리적 에러: 실행은 되지만 의도와 다르게 동작
- 모든 예외의 최고 조상은 Exception 클래스
  - ![1](https://user-images.githubusercontent.com/15611500/209649486-9bea7dc1-46d8-40cb-9d0f-40d11994d399.jpg)
  - ![2](https://user-images.githubusercontent.com/15611500/209649491-6b3c173e-1d3d-4036-bccf-e3617b221ffe.jpg)
  - checked 예외
    - Exception클래스와 그 자손들
    - 사용자의 실수와 같은 외부의 영향으로 발생할 수 있는 예외
    - 예외처리를 해주지 않으면 컴파일이 안됨
  - unchecked 예외
    - RuntimeException클래스와 그 자손들
    - 주로 프로그래머의 실수로 발생하는 예외
    - 예외처리를 강제하지 않음
- 예외처리(exception handling)
  - 소스코드(.java) 컴파일(compile) 후, 클래스 파일(.class) 실행 시(runtime) 발생할 수 있는 예기치 못한 오류를 대비하는 것
  - 발생한 예외를 처리하지 못하면 프로그램은 비정상적으로 종료됨
    - JVM의 예외처리기(UncaughtExceptionHandler)가 예외의 원인을 화면에 출력
  - ```java
    try {
      ...
    } catch (Exception e) {
      e.printStrackTrace(); //호출스택(call stack)에 있었던 메소드 정보와 메시지를 출력
      System.out.println(e.getMessage()); //발생한 예외클래스의 인스턴스에 저장된 메시지를 얻음
    } finally {
      ...
    }
    ```
  - jDK1.7부터 '|'기호를 사용해 catch블럭을 합칠 수 있음
    - ```catch(ExceptionA | ExceptionB) { e.printStrackTrace(); }```
    - 단, 예외 클래스가 조상과 자손 관계에 있다면 컴파일 에러 발생
    - 공통 분모인 조상 예외 클래스에 선언된 멤버만 사용 가능
  - 고의로 예외 발생: throw
    - ```throw new Exception("e");```
  - 메소드 내에서 발생할 수 있는 예외 선언: throws
    - ```void method() throws Exception1,...,ExceptionN {...} ```
  - 사용자 정의 예외
    - ```java
      class MyException extends Exception {
        private final int ERR_CODE;
        
        MyException(String msg) {
          this(msg, 100);
        }
        MyException(String msg, int errCode) {
          super(msg);
          ERR_CODE = errCode;
        }
        public int getErrCode() {
          return ERR_CODE;
        }
      }
      ```
  - 원인 예외(cause exception): 예외 A가 예외 B를 발생시켰다면, A를 B의 원인 예외 라고 함
    - 여러가지 예외를 하나의 큰 분류의 예외로 묶어서 다루기 위해 사용
    - initCause() 또는 생성자 사용
      - ```java
        try {...}
        catch (SpaceException e) {
          InstallException ie = new InstallException("설치중 예외발생");
          ie.initCause(e);
          throw ie;
        }
      
        static void startInstall() throws SpaceException {
          if(!enoughSpace())
            throw new SpaceException("공간 부족");
          if(!enoughMemory())
            throw new RuntimeException(new MemoryException("메모리 부족"));
        }
        ```

### java.lang패키지와 유용한 클래스
- java.lang 패키지
  - 자바프로그래밍에 가장 기본이 되는 클래스들을 포함
  - import문 없이 관련 클래스들을 사용할 수 있음 
- Object 클래스
  - ![image](https://user-images.githubusercontent.com/15611500/209749562-13596844-9552-4f69-9a14-36e67d759b8c.png)
  - 모든 클래스의 최고 조상
  - 멤버변수는 없고 오직 11개의 메소드만 존재
  - equals()
    - 두 개의 참조변수가 같은 객체를 참조하고 있는지를 판단
    - ```java
      public booleand equals(Object obj) {
        return (this==obj);
      }
      
      Value v1 = new Value(10); //0x1234
      Valus v2 = new Value(10); //0x2345
      System.out.println(v1.equals(v2)); // --> false
      ```
  - hashCode()
    - 해시 함수(hash function)을 구현한 것, 객체의 주소값을 이용해서 해시코드를 만들어 반환
    - 64bit JVM에서는 주소가 64bit이므로 해시코드(32bit)로 변환하면 중복된 값이 나올 수 있음
  - toString()
    - ```java
      public String toString() {
        return getClass().getName()+"@"+Integer.toHexString(hashCode()); //클래스이름 @ 16진수의해시코드
      ```
- String 클래스
  - ![image](https://user-images.githubusercontent.com/15611500/209753880-79fe4c4a-335b-4bba-a475-7f4607e68217.png)
  - ![image](https://user-images.githubusercontent.com/15611500/209753941-b3c43da7-48f8-4288-ad43-a17ad6c846f3.png)
  - ![image](https://user-images.githubusercontent.com/15611500/209753966-c3125563-9945-48d9-837e-ca78463fa134.png)
  - 문자열을 저장하기 위해 char[] value를 인스턴스 변수로 정의
  - 문자열 만드는 방법
    1. String str1 = "abc"; //문자열 리터럴 "abc"의 주소가 저장
       - 문자열 리터럴은 컴파일 시 클래스 파일에 저장, 메모리에 올라갈 대 JVM내에 있는 constant pool에 저장
    2. String str2 = new String("abc"); //새로운 String 인스턴스 생성(메모리 할당)
  - join()
    - 여러 문자열 사이에 구분자를 넣어서 결합
    - ```java
      String animals = "dog,cat,bear";
      String arr = animals.split(",");   //[dog,cat,bear]
      string str = String.join("-",arr); //dog-cat-bear
      ```
  - 문자열과 기본형 간의 변환
    - 기본형 -> 문자열
      - ```java
        int i = 100;
        String str1 = i + "";
        String str2 = String.valueOf(i);
        ```
    - 문자열 -> 기본형
      - ```java
        int i1 = Integer.parseInt("100");
        int i1 = Integer.parseInt(" 100 ".trim()); //문자열 양끝의 공백 제거
        int i2 = Integer.valueOf("100"); //내부에서 그저 parseInt(String s)를 호출
        ```
      - valueOf()
        - 원래 반환 타입은 Integer
        - 오토박싱(auto-boxing)으로 인해 int로 자동변환
        - ![image](https://user-images.githubusercontent.com/15611500/209755571-07926296-1d02-426f-9bfa-460eecfa3393.png)
- StringBuffer 클래스
  - ![image](https://user-images.githubusercontent.com/15611500/209755586-644c29cf-7fa8-40ca-a0cf-fcd56ae9b78f.png)
  - ![image](https://user-images.githubusercontent.com/15611500/209755646-1f63dd26-f39b-4672-baeb-bb889fee1af4.png)
  - 내부적으로 문자열 편집을 위한 버퍼(buffer)를 가지고 있어 문자열 변경 가능
  - 인스턴스를 생성할 때 buffer 크기를 지정 가능
    - 지정하지 않으면 16개의 문자를 저장할 수 있는 크기의 buffer 생성
    - buffer의 크기가 문자열의 길이보다 작을 때는 내부적으로 buffer의 크기를 증가시키는 작업 수행
      - ```java
        //1. 새로운 길이(newCapacity)의 배열 생성
        char newValue[] = new char[newCapacity];
        
        //2. 배열 value의 내용을 배열 newValue로 복사
        System.arraycopy(value, 0, newValue, 0, count); //count는 문자열의 길이
        
        //3. 새로 생성된 배열의 주소를 참조변수 value에 저장
        value = newValue;
        ```
  - StringBuffer는 멀티쓰레드에 안전(thread safe)하도록 동기화(synchronized)되어 있음
  - StringBuilder는 StringBuffer와 같은 기능이지만 쓰레드 동기화가 없음
- Math 클래스
  - ![image](https://user-images.githubusercontent.com/15611500/209755661-e7448f38-676c-4779-bd26-83d7e6e4edd0.png)
  - 클래스 내에 인스턴스변수가 하나도 없음
  - 생성자 접근 제어자가 private
  - 모든 메소드가 static 
- 래퍼(wrapper) 클래스
  - ![image](https://user-images.githubusercontent.com/15611500/209755686-b1671db4-cca5-48a3-8b4d-ef389413ec6f.png)
  - 기본형(primitive type)변수를 객체로 다뤄야 하는 경우 사용
  - equals()가 오버라이딩되어 있어 주소값이 아닌 객체가 가지고 있는 값(value)을 비교
  - 비교연산자를 사용할 순 없고 compareTo()를 제공
  - MAX_VALUE, SIZE, BYTES, TYPE등의 static 상수를 가지고있음
  - 래퍼 클래스는 모두 Number클래스의 자손
    - BigInterger, BigDecimal등의 클래스로 큰 범위의 정수 또는 실수를 다룰 수 있음
- 오토박싱(autoboxing)과 언박싱(unboxing)
  - 기본형과 참조형 간의 연산을 위해서 컴파일러가 자동으로 변환하는 코드를 넣어줌
    - ```java
      int i = 5;
      Integer Iobj = new Interger(7);
      int sum = i + iObj          //컴파일 후 -> iObj.intValue();
      Interger intg = (Integer)i; //컴파일 후 -> Integer.valueOf(i);
      Long l = 100L;              //컴파일 후 -> new Long(100L);
      ```
      
### 날짜와 시간, 형식화
- Calendar
  - ```java
    Calender date = Calendar.getInstance();
  
    date.get(Calender.YEAR): 2015 //(2015년)
    date.get(Calender.MONTH): 0 //(1월) 0~11
    date.get(Calender.DATE): 23 //(23일)
    date.get(Calender.DAY_OF_WEEK): 7 //(토요일) 1~7 
    date.get(Calender.HOUR): 0 //(1시) 0~11
    date.get(Calender.MINUTE): 11 //(11분) 0~59
    date.get(Calender.SECONDE): 22 //(22초) 0~59
  
    date.set(2019, 2, 20); //2019년 3월 20일로 날짜를 설정
    date.add(Calendar.DATE, 1); //(1일 후) 2019년 3월 21일
    date.add(Calendar.DATE, 31); //(31일 후) 2019년 4월 21일
    date.add(Calendar.MONTH, 1); //(1달 후) 2019년 5월 21일
    ```
  - Date와 Calendar간의 변환 (Date는 대부분 deprecated 됨)
    1. Date -> Calendar
      - ```java
        Date d = new Date();
          ...
        Calendar c = Calendar.getInstance();
        c.setTime(d);        
        ```
    2. Calendar -> Date
      - ```java
        Calendar c = Calendar.getInstance();
          ...
        Date d = new Date(cal.getTimeInMillis()); // Date(long date)
        ```
- 형식화 클래스
  - 데이터를 일정한 형식에 맞게 표한할 수 있는 방법, java.text패키지에 포함된 클래스
  - DecimalFormat: 숫자를 형식화 하는데 사용됨
    - ![image](https://user-images.githubusercontent.com/15611500/209780123-faed41cd-b273-4265-9bb4-d74517346e68.png)
    - ```java
      DecimalFormat df = new DecimalFormat("#,###.##");
      DecimalFormat df2 = new DecimalFormat("#.###E0");
      
      Number num = df.parse("1,234,567.89");
      System.out.println(num.doubleValue()); //1234567.89
      System.out.println(df2.format(num)); //1.2E6
      ```
  - SimpleDateFormat: 날짜를 형식화 하는데 사용됨
    - ![image](https://user-images.githubusercontent.com/15611500/209780159-d736963a-16e5-4d04-a920-87d8860a70fb.png)
    - ```java
      Date today = new Date();
      SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss a");
      System.out.println(df.format(today)) //2019-08-07 09:16:15 오후
      
      DateFormat df1 = new SimpleDateFormat("yyyy년 MM월 dd일");
      DateFormat df2 = new SimpleDateFormat("yyyy/MM/dd");
      Date d = df1.parse("2019월 08월 23일");
      System.out.println(df2.format(d)); //2019/08/23
      ```

### 컬렉션 프레임워크
- 컬렉션 프레임워크의 핵심 인터페이스
  - ![image](https://user-images.githubusercontent.com/15611500/209905433-8739ddb3-b652-4602-abd4-fb7f68c1a4e8.png)
  - ![image](https://user-images.githubusercontent.com/15611500/209905441-db830c5f-cec4-4d61-a2bc-8e3524e2e7be.png)
  - Collection 인터페이스
    - List와 Set의 조상 인터페이스
    - 반환 타입이 boolean 메소드들은 작업의 성공여부를 반환한다
  - List 인터페이스
    - 순서가 있는 데이터의 집합, 데이터의 중복을 허용
    - ArrayList, LinkedList, Stack, Vector 등
  - Set 인터페이스
    - 순서를 유지하지 않는 데이터의 집합, 데이터의 중복을 허용하지 않음
    - HashSet, TreeSet 등
  - Map 인터페이스
    - 키(key), 값(value)의 쌍(pair)로 이루어진 데이터의 집합
    - 순서는 유지하지 않고, 키 중복 허용하지 않고, 값은 중복 허용
      - 중복된 키 값을 저장하면 기존의 값은 없어지고 마지막에 저장된 값이 남게됨
    - HashMap, TreeMap, Hashtable, Properties 등
- ArrayList
  - 데이터를 읽어 오는데 걸리는 시간(access time)이 빠르다
    - 배열은 각 요소들이 연속적인 메모리상에 존재
    - index가 n인 데이터 주소 = 배열의 주소 + (n * data type의 크기)
  - 크기를 변경할 수 없어서 새로운 배열을 생성해서 데이터를 복사해야 함
    - 기존에 큰 크기의 배열을 생성해야 하므로 메모리가 낭비됨
  - 값을 순차적으로 저장하거나 삭제할 때 유용
  - 배열의 중간에 값을 변경하면 데이터가 많을수록 작업시간이 길어짐
  - ```java
    ArrayList list = new ArrayList(10);
    list.add(new Integer(1));
    list.add(new Integer(2));
    list.add(new Integer(0));
    
    Collections.sort(list); //[0, 1, 2]
    list.add(3); //[0, 1, 2, 3]
    list.add(1, "A"); //[0, A, 1, 2, 3]
    list.set(2, "B"); //[0, A, B, 2, 3]
    list.remove(3);  //[0, A, B, 3]
    ```
- LinkedList
  - 데이터를 추가하기 삭제가 빠름, 데이터의 변경이 자주일어날 때 유용
  - 데이터를 읽어오는데 순차적으로 읽어야 해서 느림
  - ```java
    class Node {
      Node next;
      Object obj;
    }
    ```
- Stack
  - ![image](https://user-images.githubusercontent.com/15611500/209905370-9d820119-029a-4cd5-b243-6cdbbcb35cb1.png)
  - LIFO(Last In First Out)
  - ArrayList와 같은 배열 기반의 구현이 적합
    - 수식계산, 괄호검사, 웹브라우저의 뒤로/앞으로 등
  - ```java
    Stack st = new Stack();
    st.push("0"); st.push("1"); st.push("2");
    while(!st.empty())
      System.out.println(st.pop()); // 2 1 0 
    ```
- Queue
  - ![image](https://user-images.githubusercontent.com/15611500/209905383-40d9b3ea-812f-4eca-8aa8-0c6e85021eaa.png)
  - FIFO(First In First Out)
  - 추가/삭제가 더 효율적인 LinkedList의 구현이 적합
    - 최근사용문서, 인쇄작업 대기목록, 버퍼(buffer) 등
  - ```java
    Queue q = new LinkedList();
    q.offer("0"); q.offer("1"); q.offer("2"); 
    while(!q.isEmpty())
      System.out.println(q.poll()); // 0 1 2 
    ```
- Iterator
  - 컬렌션에 저장된 요소들을 읽어오는 방법을 표준화 한 인터페이스
    - ```java
      public interface Iterator {
        boolean hasNext();
        Object next();
        void remove();
      }
      ```
    - ```java
      List list = new ArrayList();
      Iterator it = list.iterator();
      
      while(it.hasNext())
        System.out.println(it.next());
      ```
  - ListIterator: Iterator에 양방향 조회기능 추가(List를 구현한 경우만 사용가능)
  - Enumeration: Iterator의 구버전
  - Map과 Iterator
    - KeySet()이나 entrySet()를 이용해서 Set의 iterator를 호출해야 함
    - ```java
      Map map = new HashMap();
      Set s = map.entrySet();
      Iterator it = s.iterator();
      ```
- Arrays의 메소드
  - copyOf(), copyOfRange(): 배열의 일부를 복사해서 새로운 배열을 만들어 반환
  - fill(): 배열의 모든 요소를 지정된 값으로 채움
  - setAll(): 배열을 채우는데 사용할 함수형 인터페이스를 매개변수로 받음
  - sort(): 배열 정렬
  - binarySearch(): 지정된 값의 인덱스를 찾음 (정렬된 상태여야 함)
  - asList(): 배열을 List에 담아서 반환 (list의 크기를 변경할 수 없음)
  - parallelXXX(): 쓰레드가 작업을 나누어 처리하도록 함
  - spliterator(): 여러 쓰레드가 처리할 수 있게 하나의 작업을 나누는 iterator
  - stream(): 컬렌션을 스트림으로 변환
- Comparator, Comparable
  - 컬렉션을 정렬하는데 필요한 메소드를 정의하고 있는 인터페이스
  - Comparable: 기본 정렬기준을 구현하는데 사용 (java.lang 패키지)
    - Interger, String, Date 등 
    - ```java
      public interface Comparable {
        int compareTo(Object o);
      }
      ```
  - Comparator: 기본 정렬기준 외에 다른 기준으로 정렬하고자 할 때 사용 (java.util 패키지)
    - ```java
      public interface Comparator {
        int compare(Object o1, Object o2);
      }
      ```
  - 비교하는 값보다 작으면 음수(-), 같으면 0, 크면 양수(+)를 반환하도록 구현해야 함
    - ```java
      return (this.value < anotherVal ? -1 : (this.value == anotherVal ? 0 : 1));
      ```
- HashSet
  - ![image](https://user-images.githubusercontent.com/15611500/209905394-bfce2558-2e65-4f17-a531-026ce7b8cb1f.png)
  - Set인터페이스를 구현한 대표적인 컬렉션
  - add를 이용해서 중복된 요소를 저정할 경우 false값을 반환
  - 저장순서를 유지하려면 LinkedHashSet을 사용
    - ```java
      Object[] objArr = {"1", new Interger(1), "2", "2", "3", "4"};
      Set set = new HashSet();
      for(int i=0; i<objArr.length; i++)
        set.add(objArr[i]);
    
      //하나는 String 인스턴스, 하나는 Interger 인스턴스
      System.out.println(set); //[1, 1, 2, 3, 4]
      ```
- TreeSet
  - ![image](https://user-images.githubusercontent.com/15611500/209905407-3d62c593-db72-4626-a7ab-a7bf111ecdb3.png)
  - 이진 탐색 트리(binary search tree)의 성능을 향상 시킨 레드-블랙 트리(Red-Black tree)로 데이터를 저장하는 컬렉션 클래스
    - 부모노드 보다 작은 값은 왼쪽에 큰 값을 오른쪽에 저장
    - ```java
      class TreeNode {
        TreeNode left;
        Object element;
        TreeNode right;
      }
      ```
  - 단일 값 검색과 범위검색(range search)가 빠름
  - 데이터 추가/삭제 시간은 더 걸림
  - 중복된 값을 저장하지 못함
  - ```java
    String from = "b";
    String to = "d";
    
    //대문자가 소문자보다 정렬 우선시됨
    System.out.println(set); //Car, abc, alien, bat, car, dZZZZ, dance, disc, dzzzz, elephant
    System.out.println(set.subSet(from, to)); //[bat, cat] (b~c)
    System.out.println(set.subSet(from, to + "zzz"));[bat, car, dZZZZ, dance, disc] (b~d)
    ```
- HashMap
  - ![image](https://user-images.githubusercontent.com/15611500/209905419-c4e3952a-86d9-425f-8d20-b0eb7ef6083f.png)
  - 기존의 Hashtable(deprecated)과 같음
  - Entry라는 내부 클래스를 정의하여 배열로 관리
    - ```java
      Entry[] table;
      static class Entry implements Map.Entry {
        final Object key;
        Object value;
      }
      ```
  - 한정되지 않은 범위의 비순차적인 값들의 빈도수는 HashMap을 이용해서 구현 가능
    - ```java
      HashMap map = new HashMap();
      map.put("MyId", "1234");
      map.put("MyId", "0012"); //OK. 이미 존재하는 기존 값은 없어짐
      System.out.println(map.containKey("MyId")); //true
      System.out.println(map.get("1234")); //0012
      
      String[] data = {"A", "A", "A", "D", "K", "D", "A"};
      HashMap map = new HashMap();
      for(int i=0; i<data.length; i++) {
        if(map.containsKey(data[i]) {
          map.put(data[i], (int)map.get(data[i]) + 1); //기존에 있으면 1을 더해서 저장
        else
          map.put(data[i], 1); //기존에 없는 키는 값을 1로 저장
      ```
- Collections의 메소드
  - 동기화(synchronizetion) 메소드
    - ```java
      List list = Collections.synchronizedList(new ArrayList(...));
      ```
  - 변경불가(읽기전용) 컬렉션 만들기
    - ```java
      List list = Collections.unmodifiable(new ArrayList(...));
      ```
  - 싱글톤 컬렉션 만들기
    - ```java
      List list = Collections.singletonList(new ArrayList(...));
      ```
  - 한 종류의 객체만 저장하는 컬렉션 만들기
    - ```java
      List list = Collections.checkedList(new ArrayList(...), String.class);
      ```

### 제네릭스, 열거형, 애너테이션
- 제네릭스(Generics)
  - 컴파일 시 타입 체크(compile-time type check)를 해주는 기능
  - 컴파일 후에는 제네릭 타입이 제거되고 원시 타입으로 바뀜
  - 저장된 객체를 꺼낼 때 형변환 필요가 없음
    - ```java
      ArrayList<Tv> tvList = new ArrayList<Tv>();
      tvList.add(new Audio()); //컴파일 에러. Tv 외에 다른 타입 저장 불가
      ```
  - 타입 변수(type variable)
    - '<>'안에 있는 변수
    - Object타입 대신 임의의 타입을 의미하는 타입 변수(T)를 사용하면 됨
    - ```java
      class Box<T> {}
      Box<String> box = new Box<String>();
      ```
      - Box<T>: 제네릭 클래스, 'T Box'라고 읽음
      - T: 타입 변수 또는 타입 매개변수 (T는 타입문자)
      - Box: 원시 타입(raw type)
      - Box<String> : 제네릭 타입 호출 (타입 매개변수에 타입을 지정하는 것)
      - String: 대입된 타입 또는 매개변수화된 타입(parameterized type)
  - 제네릭 타입과 다형성
    - ```java
      ArrayList<Tv> list = new ArrayList<Tv>(); // ok.
      ArrayList<Product> list = new ArrayList<Tv>(); // 에러. 상속관계에 있어도 안됨
      List<Tv> list = new ArrayList<Tv>(); // ok. 클래스의 타입간 다형성은 가능
      
      ArrayList<Product> list = new ArrayList<Product>();
      list.add(new Product());
      list.add(new Tv()); // ok. 단, 객체를 꺼낼 때 형변환이 필요 
      ```
  - Iterator<E>
    - ```java
      ArrayList<Student> list = new ArrayList<Student>();
      list.add(new Student("홍길동", 1, 2));
      
      Iteractor<Student> it = list.iterator();
      while(it.hasNext())
        Student s = it.next(); //제네릭을 사용하지 않으면 형변환 필요: (Student)it.next()
      ```
  - 제한된 제네릭 클래스
    - 'extends'를 사용하면 특정 타입의 자손들만 대입할 수 있음
    - 인터페이스도 extends를 사용, '&'기호로 연결 가능
      - ```java
        class FruitBox<T extends Fruit & Eatable> {...}
        ```
  - 제네릭스의 제약
    - static멤버에 타입 변수 T를 사용할 수 없음
      - T는 인스턴스 변수로 간주
    - 배열 타입의 참조변수 선언은 가능하지만 배열 생성은 안됨
      - 컴파일 시점에 타입 T를 정확히 알아야 하는데, 연산자 new instanceof 등은 알수 없음
      - ```java
        class Box<T> {
          T[] itemArr; //ok.
          T[] tmpArr = new T[10]; //에러.
        }
        ```
  - 와일드 카드
    - '?'기호를 사용하여 제네릭 타입에 다형성 적용
      - ```java
        ArrayList<? extends Product> list = new ArrayList<Tv>(); // ok.
        ArrayList<? extends Product> list = new ArrayList<Audio>(); // ok.
        ```
        - \<? extend T>: T와 그 자손들만 가능
        - \<? super T>: T와 그 조상들만 가능
        - \<?>: 제한 없음. 모든 타입 가능
      - ```java
        static Juice makeJuice(FruitBox<? extends Fruit> box) {
          String tmp = "";
          for(Fruit f : box.getList())
            tmp += f + " ";
          return new Juice(tmp);
        }
        Juicer.makeJuice(new FruitBox<Fruit>()); // ok.  
        Juicer.makeJuice(new FruitBox<Apple>()); // ok.
        ```
  - 제네릭 메소드
    - 메소드 선언부(반환 타입 앞)에 제네릭 타입이 있는 것
    - 지역 변수를 선언한 것과 비슷, 메소드가 static이어도 상관 없다
      - ```java
        class FruitBox<T> {
          ...
          static <E> void sort(List<T> list, Comparator<? super T> c) {...}
        }
        ```
    - 제네릭 메소드를 호출할 때 대입된 타입이 있을 경우, 참조변수나 클래스 이름을 생략할 수 없다
      - ```java
        static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {
          ...
        }
        <Fruit>makeJuice(new FruitBox<Fruit>());  //에러. 클래스 이름 생략 불가
        this.<Fruit>makeJuice(new FruitBox<Fruit>());    // ok.
        Juicer.<Fruit>makeJuice(new FruitBox<Fruit>());  // ok.
        ```
  - 제네릭 타입의 형변환
    - ```java
      // 원시 타입 <-> 제네릭 타입
      Box box = (Box)new Box<Object>(); // ok.
      Box<Object> objBox = (Box<Object>)new Box(); // ok.
      
      // 제네릭 타입 <-> 제네릭 타입
      Box<Object> objBox = (Box<Object>)new Box<String>(); //에러. 형변환 불가
      Box<? extends Object> objBox = new Box<String>(); //ok.
      ```
  - 제네릭 타입의 제거
    0. 컴파일 전(.java)
      - ```java
        class Box<T extends Fruit> {
          void add(T t) {...}
          T get(int i) {
            return list.get(i);
          }
        }
        ```
    1. 제네릭 타입의 경계(bound)를 제거
      - ```java
        class Box {
          void add(Fruit t) {...}
          Fruit get(int i) {
            return list.get(i);
          }
        }
        ```
        - \<T extends Fruit>라면 T는 Fruit로 치환
        - \<T>인 경우 Object로 치환
    2. 제네릭 타입을 제거한 후에 타입이 일치하지 않으면 형변환 추가
      - ```java
        class Box {
          void add(Fruit t) {...}
          Fruit get(int i) {
            return (Fruit)list.get(i);
          }
        }
        ```
        - List의 get()은 Object타입을 반환하므로 형변환 필요
- 열거형(enum)
  - ![image](https://user-images.githubusercontent.com/15611500/209934114-b69e60b7-c2de-43c8-9661-4ac21a10a013.png)
  - 여러 상수를 선언해야 할 때 사용
  - 자동적으로 0부터 시작하는 정수값이 할당됨
    - ```java
      enum Direction { EAST, SOUTH, WEST, NORTH } // 0, 1, 2, 3

      Direction dir;      
      if(dir == Direction.EAST) // ok.
      if(dir > Direction.WEST) // 에러. 열거형 상수에 비교연산자 사용 불가
      if(dir.compareTo(Direction.WEST) > 0) // ok.
      ```
  - 모든 열거형의 조상은 java.lang.Enum
    - ```java
      Direction[] dArr = Direction.values();
      for(Directiond d : dArr)
        System.out.println(d.name(), d.ordinal()); //EAST, 0 ...
      ```
  - 열거형에 멤버 추가하기
    - 열거형 생성자는 제어자가 묵시적으로 private이기 때문에 외부에서 호출불가
      - ```java
        enum Direction {
          EAST(1), SOUTH(5), WEST(-1), NORTH(10)  // 1, 5, -1, 10
        
          private final int value; // 정수를 저장할 필드
          Direction(int value) { this.value = value; } 
          public int getValue() { return value; }
        }
    
        Direction d = new Direction(1); // 에러.
        ```
- 애너테이션(annotation)
  - ![image](https://user-images.githubusercontent.com/15611500/209934137-9d97475f-8ec6-48c9-9691-16d301bf2466.png)
  - 소스코드 안에 다른 프로그램을 위한 정보를 약속된 형식으로 포함시킴
  - 주석(/** */) 안에 정보를 저장하고, javadoc.exe이 정보를 읽어서 HTML문서를 작성하는데 사용
  - JDK의 표준 애너테이션은 컴파일러에게 유용한 정보를 제공
  - 애너테이션 정의하기
    - 애너테이션의 요소는 반환값이 있고, 매개변수는 없는 추상 메소드의 형태를 가짐
    - 애너테이션을 적용할 때 모든 요소들의 값을 지정해야 함
      - ```java
        @interface 애너테이션이름 {
          타입 요소이름();
          ...
        }
        ```
      - ```java
        @interface TestInfo {
          int count() default 1;
          String testedBy();
          String[] testTools();
          TestType testType();  //enum 가능
          DateTime testDate();  //다른 애너테이션을 포함시킬 수 있음
        }
        @interface DateTime {
          String yymmdd();
          String hhmmss() default "235959";
        }
        
        @TestInfo(
          count=3, testedBy="Kim", testTools={"JUnit", "AutoTester"},
          testType=TestType.FIRST, testDate=@DateTime(yymmdd="190101")
        )
        public class NewClass { ... }
        ```
    - 요소 정의 규칙
      1. 요소의 타입은 기본형, String, enum, 애너테이션, Class만 허용
      2. () 안에 매개변수를 선언할 수 없음
      3. 예외를 선언할 수 없음
      4. 요소를 타입 매개변수로 정의할 수 없음
      - ```java
        @interface AnnoTest {
          String major(int i, int j); // 에러. 2.
          String minor() throws Exceptions; //에러. 3.
          ArrayList<T> list(); // 에러. 4.
        }
        ```


### 쓰레드

- 프로세스(process): 실행 중인 프로그램(program)
  - 프로그램을 실행하면 OS로부터 실행에 필요한 자원(메모리)을 할당받아 프로세스가 됨
- 쓰레드(thread): 프로세스의 자원을 이용해서 실제로 작업을 수행하는 것
  - 쓰레드의 구현
    - Thread 클래스를 상속받아 구현
      - ```java
        class MyThread extends Thread {
          public void run() { /* 작업내용 */ }
        }
        ```
    - Runnable 인터페이스를 구현
      - ```java
        class MyThread implements Runnable {
          public void run() { /* 작업내용 */ }
        }
        ```
  - 쓰레드의 실행
    1. main 메소드에서 쓰레드의 start()를 호출
    2. start()는 새로운 쓰레드를 생성하고, 쓰레드가 사용할 호출스택 생성
    3. 새로 생성된 호출새택에 run()이 호출돼 쓰레드가 독립된 공간에서 작업 수행
    4. 스케줄러가 정한 순서에 의해서 2개의 호출스택이 번걸아 가면서 실행됨
  - 싱글쓰레드와 멀티쓰레드
    - ![image](https://user-images.githubusercontent.com/15611500/209964510-029fda32-df9c-46e8-b9ec-07acbf80abb6.png)
    - ![image](https://user-images.githubusercontent.com/15611500/209964537-4f9aada0-ddc2-4e18-8a42-7ca90e3d4de1.png)
    - 멀티쓰레딩의 장점
      - CPU의 사용률을 향상시킴
      - 자원을 효율적으로 사용
      - 사용자에 대한 응답성 향상
      - 작업이 분리되어 코드가 간결해짐
    - 멀티 쓰레드 시 작업 전환(context switching) 시간이 더 소요됨
      - 다음에 실행해야 할 위치(PC, program counter)등의 정보를 읽어야 함
    - 두 쓰레드가 서로 다른 자원을 사용하는 경우 싱글쓰레드 보다 멀티쓰레드 프로세스가 훨씬 효율적
    - 싱글 코어인 경우 멀티쓰레드라도 하나의 코어가 번갈아가면서 작업을 수행하기 때문에 작업 시간이 겹치지 않음
    - 멀티 코어는 두 쓰레드가 동시에 수행될 수 있음, 싱글 코어보다 효율이 훨씬 좋음
    - JVM이 쓰레드를 얼마나 실행할 것인지 스케줄 함
    - OS가 프로레스를 얼마나 실행할 것인지 스케줄 함
      - 결국, 멀티쓰레드 작성 자바 프로그램은 OS에 종속적임 
  - 쓰레드의 I/O블락킹(blocking)
    - ![image](https://user-images.githubusercontent.com/15611500/209964683-51cda16b-099f-46a2-9a78-62c734c923c9.png)
    - 데이터를 입력받는 작업, 네트워크로 파일을 주고 받는 작업, 외부기기와의 입출력이 필요한 경우 등
  - 쓰레드의 우선순위(priority)
    - 우선순위의 값에 따라 쓰레드가 얻는 실행시간이 달라짐
    - main 메소드 쓰레드의 우선순위가 5
    - 쓰레드를 실행하기 전에만 우선순위를 변경할 수 있음
      - ```java
        void setPriority(int newPriority)
        int getPriority()
        
        public static final int MAX_PRIORITY  = 10 // 최대우선순위
        public static final int MIN_PRIORITY  = 1  // 최소우선순위
        public static final int NORM_PRIORITY = 5  // 보통우선순위
        ```
    - 단, 우선순위에 차등을 두어 쓰레드를 실행하려면, 특정 OS의 스케줄링 정책과 JVM의 구형을 직접 확인해야 함 (정확히 알 수 없음?)
  - 쓰레드 그룹(thread group)
    - ![image](https://user-images.githubusercontent.com/15611500/209965264-58fa3c52-4460-437e-8f9e-894736933bcc.png)
    - 보안상의 이유로 도입된 개념으로, 자신 또는 하위 쓰레드 그룹은 변경할 수 있지만 다른 그룹은 변경 불가능
    - 쓰레드를 쓰레드 그룹에 포함시키려면 Thread의 생성자를 이용
      - ```java
        Thread(ThreadGroup group, String name)
        Thread(ThreadGroup group, Runnable targer)
        ```
    - 모든 쓰레드는 반드시 쓰레드 그룹에 포함되어 있어야 함
    - 기본적으로 자신을 생성한 쓰레드와 같은 그룹에 속함
    - JVM은 main과 system이라는 쓰레드 그룹을 만들고, 운영에 필요한 쓰레드들을 생성함
      - Garbage collection을 수행하는 Finalizer쓰레드는 system그룹에 속함
      - 쓰레드 그룹을 지정하지 않으면 자동적으로 main그룹에 속함
  - 데몬 쓰레드(daemon thread)
    - 다른 쓰레드의 작업을 돕는 보조적인 역할을 수행
      - 가비지 컬렉터, 워드프로레서의 자동저장, 화면자동갱신 등
    - 무한루프와 조건문을 이용해서 실행 후 대기하고 있다가 특정 조건이 만족되면 작업을 수행하고 다시 대기하는 형식
      - ```java
        Thread t = new Thread(new Ex13());
        t.setDaemon(true); //true로 지정하면 데몬 쓰레드가 된다
        t.start();
        ```
  - 쓰레드의 상태
    - ![image](https://user-images.githubusercontent.com/15611500/209964578-b4b1a77c-e16e-439c-8983-458ddeb7f5c5.png)
    - NEW: 쓰레드가 생성되고 아직 start()가 호출되지 않은 상태
    - RUNNABLE: 실행 중 또는 실행 가능한 상태
    - BLOCKED: 동기화블럭에 의해서 일시정지된 상태(lock이 풀릴 때까지 기다리는 상태)
    - WAITING, TIMED_WATING: 쓰레드 작업이 종료되지는 않았지만 일시정지 상태
    - TERMINATED: 쓰레드의 작업이 종료된 상태
  - 쓰레드의 실행제어
    - ![image](https://user-images.githubusercontent.com/15611500/209964646-a680533a-f3ff-4cd4-921b-5c3268977845.png)
    - interrupt
      - interrupt()
        - 쓰레드의 interrupted상태(인스턴스 변수)를 false에서 true로 변경
      - isInterrupted()
        - 쓰레드의 interrupted상태(인스턴스 변수)를 반환
      - interrupted()
        - 현재 쓰레드의 interrupted상태(인스턴스 변수)를 반환 후, false로 변경
      - ```java
        class Main {
          public static void main(String[] args) throws Exception {
            MyThread th = new MyThread();
            th.start();
            
            String input = JOptionPane.showInputDialog("카운트 다운을 멈추려면 아무 값이나 입력하시오");
            th.interrupt();
            System.out.println(th.isInterrupted()); // true
          }
        }
        class MyThread extends Thread {
          public void run() {
            int i=10;
            while(i!=0 && !isInterrupted()) {
              System.out.println(i--);       // 카운트 다운
              for(long x=0;x<250000000L;x++) // 시간 지연 용도
            }
          System.out.println("카운트 다운 종료");
        }
        ```
    - suspend(), resume(), stop()
      - 쓰레드를 멈추게하고, 다시 실행대기 상태가 되게 하며, 쓰레드를 즉시 종료시킴
      - 하지만, 교착상태(deadlock)를 일으키기 쉽기때문에 권장되지 않음(deprecated)
    - sleep()
      - 지정된 시간동안 쓰레드를 멈추게한다
      - 항상 현재 실행 중인 쓰레드에 대해 작동
      - 시간이 다 되거나 interrupt()가 호출되면, InterruptedException이 발생해서 항상 예외 처리가 필수
        - ```java
          try {
            Thread.sleep(1, 500000); // 쓰레드를 0.0015s 멈추게 한다
          } catch(InterruptedException e) {}
          ```
    - join()
      - 작업 중에 다른 쓰레드의 작업이 먼저 수행되어야 할 때 사용
      - 자신이 하던 작업을 멈추고, 다른 쓰레드가 지정된 시간동안 작업을 수행하도록 함
        - 시간을 지정하지 않으면 해당 쓰레드가 작업을 모두 마칠 때까지 기다림
      - sleep()처럼 interrupt()에 의해 대기 상태에서 벗어날 수 있음
        - ```java
          try {
            th.join()  // 현재 실행중인 쓰레드가, 쓰레드 th의 작업이 끝날때까지 기다림
          } catch(InterruptedException e) {}
          ```
    - yield()
      - 쓰레드 자신에게 주어진 실행시간을 다음 차례 쓰레드에게 양보(yield)한다
        - 1초의 실행시간을 할당받은 쓰레드가 0.5초 후 yield()를 호출하면 0.5는 포기하고 실행대기상태가 됨
  - 쓰레드의 동기화(synchronization)
    - 한 쓰레드가 진행 중인 작업을 다른 쓰레드가 간섭하지 못하게 막는 것
      - 공유 데이터를 사용하는 코드 영역을 임계 영역(critical section)으로 지정
      - 공유 데이터(객체)가 가지고 있는 락(lock)을 획득한 하나의 쓰레드만 이 영역 내의 코드를 수행할 수 있게 함
      - 해당 쓰레드가 lock을 반납해야만 다른 쓰레드가 수행 가능
      - 모든 객체는 lock을 하나씩 가지고 있음
    - synchronized를 이용한 동기화
      1. 메소드 전체를 임계 영역으로 지정
        - ```java
          public synchronized void sum() { ... }
          ```
      2. 특정한 영역을 임계 영역으로 지정
        - ```java
          synchronized(객체의 참조변수) { ... }
          ```
    - 쓰레드가 락을 보유한 채로 오랜시간 지체되는 것을 방지하기 위한 메소드( 동기화 블록 내에서만 사용 가능)
      - wait()
        - 쓰레드가 락을 반납하고 기다리게 함
        - 해당 객체 대기실(waiting pool)에서 통지를 기다림
      - notify()
        - 해당 객체 대기실(waiting pool)에 있는 쓰레드 중에서 임의의 쓰레드에게 통지 해줌
      - notifyAll()
        - 해당 객체 대기실(waiting pool)에 있는 모든 쓰레드에게 통지해줌
        - 그래도 lock을 얻는 쓰레드는 하나
      - ```java
        import java.util.ArrayList;
      
        class Customer implements Runnable {
          private Table table;
          private String food;
          Customer(Table table, String food) {
            this.table = table;
            this.food = food;
          }
          
          public void run() {
            while(true) {
              try { Thread.sleep(100); } catch(InterruptedException e) {}
              String name = Thread.currentThread().getName();
              table.remove(food);
              System.out.println(name + " ate a " + food);
            }
          }
        }
        
        class Cook implements Runnable {
          private Table table;
          Cook(Table table) { this.table = table; }
          
          public void run() {
            while(true) {
              int idx = (int)(Math.random() * table.dishNum());
              table.add(table.dishNames[idx]);
              try { Thread.sleep(10); } catch(InterruptedException e) {}
            }
          }
        }
        
        class Table { 
          String[] dishNames = { "donut", "donut", "burger" };
          final int MAX_FOOD = 6;
          private ArrayList<String> dishes = new ArrayList<>();
        
          public synchronized void add(String dish) {
            while(dishes.size >= MAX_FOOD) {
              String name = Thread.currentThread().getName();
              System.out.println(name+" is waiting.");
              try {
                wait(); // COOK쓰레드를 기다리게 함
                Thread.sleep(500);
              } catch(InterruptedException e) {}
            }
            dishes.add(dish);
            notify();
            System.out.println("Dishes:" + dishes.toString());
          }
        
          public void remove(String dishName) {
            synchronized(this) {
              String name = Thread.currentThread().getName();
              while(dishes.size()==0) {
                System.out.println(name +" is waiting.");
                try {
                  wait(); // CUSTOMER 쓰레드를 기다리게 함
                  Thread.sleep(500);
                } catch(InterruptedException e) {}
              }
        
              while(true) {
                for (int i=0; i<dishes.size(); i++) {
                  if(dishName.equals(dishes.get(i)) {
                    dishes.remove(i);
                    notify(); // 잠자고 있는 COOK을 깨우기 위함
                    return;
                  }
                }
                try {
                  System.out.println(name + "is waiting.");
                  wait(); // 원하는 음식이 없는 CUSTOMER 쓰레드를 기다리게 함
                  Thread.sleep(500);
                } catch(InterruptedException e) {}
              }
            }
          }
          public int dishNum() {
            return dishNames.length;
          }
        }
        
        class Main {
          public static void main(String[] args) throws Exception {
            Table table = new Table();
        
            new Thread(new Cook(table), "COOK").start();
            new Thread(new Customer(table, "donut"), "CUSTOMER1").start();
            new Thread(new Customer(table, "burger"), "CUSTOMER2").start();
            Thread.sleep(2000);
            System.exit(0);
          }
        }                 
        ```
      - ```
        ...
        Dishes: [donut, donut, donut, donut, donut, donut]
        COOK is waiting.
        CUST2 is waiting.
        CUST1 ate a donut
        Dishes: [donut, donut, donut, donut, donut, donut]
        CUST2 is waiting. <-- 원하는 음식이 없어서 기다림
        COOK is waiting.  <-- 테이블이 가득차서 기다림
        CUST1 ate a donut <-- 테이블의 음식이 소비되어서 notify()가 호출됨
        CUST2 is waiting. <-- 요리사가 아닌 손님이 통지받고, 원하는 음식이 없어서 다시 기다림
        CUST1 ate a donut <-- 테이블의 음식이 소비되어서 notify()가 호출됨
        Dishes: [donut, donut, donut, donut, donut] <-- 요리사가 통지받고 음식 추가
        CUST2 is waiting. <-- 음식추가 통지를 받았으나 원하는 음식이 없어서 기다림
        Dishes: [donut, donut, donut, donut, donut, burger] <-- 요리사가 음식추가(활동 중)
        CUST1 ate a donut
        CUST2 ate a burger <-- 음식추가 통지를 받고, 원하는 음식을 소비
        Dishes: [donut, donut, donut, donut, donut] <-- 요리사가 음식추가(활동 중)
        ...
        ```
        - 다만, notify()는 그저 waiting pool에서 대기 중인 쓰레드 중 하나를 임의로 선택해서 통지할 뿐, 요리사 쓰레드를 선택해서 통지할 수 없다
        - 손님 쓰레드가 통지받으면 lock을 얻어도 여전히 자신이 원하는 음식이 없어, 다시 waiting pool에 들어가게 된다

### 람다와 스트림








































