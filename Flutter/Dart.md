# Dart

### Dart 란
- Google에서 개발한 객체지향 프로그래밍 언어로, 다양한 플랫폼에서 실행될 수 있도록 설계되어 있다
- 두 가지의 컴파일러(JIT, AOT)를 보유하고 있으며 강력한 타입 시스템을 갖추고 있다
  - JIT(Just in Time) compiler
    - 코드 실행 시점에 컴파일을 수행할 수 있는 컴파일러
    - Flutter 개발의 특징인 Hot-Reload / Hot-Restart를 구현할 때 활용
  - AOT(Ahead of Time) compiler
    - 사전에 미리 컴파일을 수행하여 높은 실행속도를 필요로 할 때 활용되는 컴파일러
    - 다른 플랫폼에서 실행 할 수 있도록 컴파일 후 배포 가능한 바이너리 파일을 생성하는 것이 특징 
- Flutter SDK에 기본적으로 Dart가 내장되어 있다
  - FVm(Flutter Version Manager)를 통한 설치로 버전 관리를 할 수 있다


### 변수, 연산자, 클래스
- 변수
  - ```
    void main() {
      bool isTrue = true;
      int num = 100;
      double num2 = 3.14;
      String string = 'Hello World';
      Null thisIsNull = null; //널형은 null값 밖에 줄 수 없다!
      print(isTrue);
    
      var value = 1;
      value = 2; // ok
      value = 'isError'; // 에러
      dynamic value2 = 100;
      value2 = 'Hello'; // ok
    }
    ```
- 연산자
  - 2.12버전부터 Null값에 대한 안정성을 위해 NullSafety라는 개념이 도입
    - Null을 허용하는 타입/변수 뒤에 ```?```를 붙이고(ex, int?), 비허용 뒤에는 ```!```를 붙여(ex, int!) 활용
  - ```
    void main() {
      int num1 = 10;
      int num2 = 20;
      print(num1 != num2); // true
      print(num1 == num2); // false
      String str1 = 'Hello';
      String str2 = str1 + ' World'; //'Hello World'
      String str3 = '$str1 World';   //'Hello World'
      bool bool1 = true;
      bool bool2 = false;
      print(!bool1); // false
      print(bool1 && bool2); // false (and 연산)
      print(bool1 || bool2); // true (or 연산)
    
      int? num1;
      int num2;
      print(num1); // null
      print(num2); // 에러.
      num2 = 10;
      // ?? 연산자: 값이 null이면 해당 값으로 대채한다
      print( (num1 ?? 5) + num2); // 15 
    }
    ```
- 클래스
  - ```
    void main() {
      Point point = Point();
      Point point2 = Point2(1, 2);
      print(point); // Instance of 'Point' 
      print(point2.x); // 1
    }
    class Point {
      double? x;
      double? y;
    }
    class Point2 {
      double x;
      double y;
      Point(this.x, this.y); // 초기값 대입
    }
    ```






















