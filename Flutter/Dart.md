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
  - FVM(Flutter Version Manager)를 통한 설치로 버전 관리를 할 수 있다


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
    
      print(10 / 2); // 5.0
      print(10 / 3); // 3.33
      print(10 / 3); // 3 (몫)
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

### 조건문, 반복문, 예외 처리
- 조건문
  - ```
    int s = 3;
    if (s < 0) {
      print("s<0");
    } else if (s == 3) {
      print("s==3");
    } else {
      print("else");
    }
    
    int n = 5;
    switch (num) {
      case 1:
        print("switch $n");
      case 2:
        print("switch $n");
      default:
        print("default");
    ```

- 반복문
  - ```
    for (int i=0; i<10; i++) {
      print("index $i");
    }
    
    List<int> indexs = [0, 1, 2, 3, 4];
    for (final index in indexs) {
      print('index $index');
    }
    
    int count = 0;
    while (true) {
      if (count >= 5) {
        break;
      }
      count += 1;
      print("while is running);
    }
    
    do {
      print("do-while is running);
      count += 1;
    } while (count < 10);
    ```

- 예외 처리
  - ```
    int num1 = 10;
    print(10 / 0); // Infinity
    print(10 ~/ 0); // 에러
    try {
      print(10 ~/ 0);
    } on UnsupportedError catch(e, s) {
      print("0으로 나눌 수 없다");
    } on TypeError catch(e, s) {
      print("null 값이 참조 되었다");
    } catch(error, stack) {
      print(error);
      print(stack);
      /// throw Exception("Unknown Error");
      /// rethrow;
    } finally {
      print("finally");
    }
    
    try {
      print(num!);
    } on UnsupportedError catch(e, s) {
      print("0으로 나눌 수 없다");
    } on TypeError catch(e, s) {
      print("null 값이 참조 되었다");
    } catch (e, s) {}
    ```

### 비동기 프로그래밍
- async / await / Future : 1회만 응답을 돌려받는 경우 
  - ```
    void main() {
      todo(3);
      todo(1);
      todo(5);
    
      Future<void> todo (int second) async {
        await Future.delayed(Duration(seconds: second));
        print("TODO Done in $second seconds");
      }
    }
   
    
    ```
- async* / yield / Stream : 지속적으로 응답을 돌려받는 경우 
  - ```
    void main() {
      todo().listen((event) {});
    
      Stream<int> todo() async* {
        int counter = 0;
        while(counter <= 10) {
          counter++;
          await Future.delayed(Duration(seconds: 1));
          print("TODO is Running $counter");
          yield counter;
        }
        print("TODO is Done");
      }
    
    }
    ```

### 가위바위보 프로그램 예시
- ```
  import 'dart:convert';
  import 'dart:io';
  import 'dart:math';
  
  void main() {
    /// 1. 유저에게 어떤 타입을 낼 것인지 물어보는 질의 창
    print('가위 바위 보 중 하나를 입력해 주세요.');
    final String userInput = stdin.readLineSync(encoding: utf8) ?? 'Error';
  
    /// 2. 컴퓨터가 낼 타입이 어떤것인지 결정하는 함수
    const selectList = ['가위', '바위', '보'];
    final cpuInput = selectList[Random().nextInt(3)];
  
    /// 3. 유저의 타입과 컴퓨터 타입에 대한 결과를 계산할 함수
    final result = getResult(userInput, cpuInput);
    print(result);
  }
  
  String getResult(String userInput, String cpuInput) {
    String result;
    const cpuWin = 'CPU가 승리 하였습니다.';
    const playWin = '플레이어가 승리 하였습니다.';
    const draw = '비겼습니다.';
  
    switch(userInput) {
      case '가위':
        if (cpuInput == '바위') {
          result = cpuWin;
        } else if(cpuInput == '보') {
          result = playWin;
        } else {
          result = draw;
        }
      case '바위':
        if (cpuInput == '바위') {
          result = draw;
        } else if(cpuInput == '보') {
          result = cpuWin;
        } else {
          result = playWin;
        }
      case '보':
        if (cpuInput == '바위') {
          result = playWin;
        } else if(cpuInput == '보') {
          result = draw;
        } else {
          result = cpuWin;
        }
      default:
          result = '올바른 값을 입력 해 주세요.';
    }
    return result;
  }
  ```






















