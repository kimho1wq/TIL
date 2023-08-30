# Flutter

### Flutter 란
- 플러터(Flutter)는 Dart언어를 사용하는 Google에서 개발한 크로스 플랫폼 개발 프레임워크이다
- ios/android 모바일 플랫폼에 지원을 포함해서 Desktop/Web application 또한 제작할 수 있다
- UI는 Widget 단위로 개발하며, 선언형 UI구조로 로직 코드와 UI코드가 하나의 파일에서 작성된다
- UI와 로직 코드 모두 native로 변환하지 않고, 별도의 UI 엔진을 활용하여 구동한다 (SKiA 엔진, Impeller 엔진 등)
- Flutter에서 사용하는 대표적인 2개의 Design Guide로, Google 느낌의 Material Design과 Apple 느낌의 Cupertino Design을 사용한다
- Hot-restart와 Hot-reload
  - 개발시 앱을 재 컴파일 후 빌드하지 않고 코드의 변경사항을 반영할 수 있다
  - 앱의 기초가 되는 네이티브 코드의 수정이나, 라이브러리, Asset의 추가 등의 상황에서는 정상적으로 작동되지 않는다
    - Hot restart
      - 앱을 재시작하는 형태로, 앱의 모든 상태가 초기화되고 코드의 변경 사항을 반영할 수 있다
    - Hot reload
      - 앱을 재실행하지는 않지만, 코드의 변경 사항을 반영하는 기능



### Widget
- Widget은 Flutter의 UI의 가장 기본적인 단위이다
- 간단한 블록부터, 텍스트 입력 창, 화면 내 특정 요소간 등 모두 Widget을 관리된다
- pub.dev를 통해 오픈소스 Widget을 이용할 수 있다
- Widget들 간의 관계를 Widget Tree라고 한다
- 화면을 갱신 할 필요가 없는 정적인 화면은 Stateless Widget을, 특정한 상황에서 화면을 갱신할 필요가 있다면 Stateful Widget을 사용한다
- 간단한 Widget 예시
  - ```
    import 'package:flutter/material.dart';

    void main() {
      runApp(MaterialApp(
        home: Scaffold(
          appBar: AppBar(actions: [
            IconButton(
              icon: Icon(Icons.home),
              onPressed: () {
                print('Tab!');
              },
            ),
            Icon(Icons.play_arrow),
          ], centerTitle: true, title: Text("This is App bar")),
          body: TestWidget(),
          floatingActionButton: FloatingActionButton(
            child: Icon(Icons.bug_report),
            onPressed: () {
              print('Tab! FAB!');
            },
          ),
        ),
      ));
    }

    class TestWidget extends StatelessWidget {
      const TestWidget({super.key});

      @override
      Widget build(BuildContext context) {
        return SafeArea(
          child: Center(
            child: Text(
              'Hello, Flutter',
              style: TextStyle(
                fontSize: 40,
                color: Colors.black,
              ),
            ),
          ),
        );
      }
    }
    ```
- Container, Center Widget
  - ```
    class CustomContainer extends StatelessWidget {
      const CustomContainer({super.key});

      @override
      Widget build(BuildContext context) {
        return Center(
          child: Container(
              width: 300, //double.infinity,
              height: 300,
              // color: Color(0xFF85D07B), //Colors.red.shade200,
              padding: EdgeInsets.fromLTRB(10, 12, 10, 12),
              decoration: BoxDecoration(
                color: Color(0xFF85D07B),
                border: Border.all(color: Colors.red, width:5, style: BorderStyle.solid),
                borderRadius: BorderRadius.circular(50),
                boxShadow: [
                  BoxShadow(color: Colors.black.withOpacity(0.7), offset: Offset(6, 6), blurRadius: 10, spreadRadius: 10),
                  BoxShadow(color: Colors.blue.withOpacity(0.7), offset: Offset(-6, -6), blurRadius: 10, spreadRadius: 10),

                ],
              ),
              margin: EdgeInsets.symmetric(vertical: 30, horizontal: 10),
              child: Center(
                child: Container(
                    color: Colors.yellow,
                    child: Text(
                        'Hello Container Hello Container Hello Container Hello Container Hello Container')),
              )),
        );
      }
    }
    ```
- Widget을 상하(좌우)로 배치하기
  - ```
    class Body extends StatelessWidget {
      const Body({super.key});

      @override
      Widget build(BuildContext context) {
        return Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Row(
              mainAxisSize: MainAxisSize.max,
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                Container(
                  width: 100,
                  height: 100,
                  color: Colors.red,
                  child: Text('Container 1'),
                ),
                Container(
                  width: 100,
                  height: 100,
                  color: Colors.green,
                  child: Text('Container 2'),
                ),
                Container(
                  width: 100,
                  height: 100,
                  color: Colors.blue,
                  child: Text('Container 3'),
                ),
              ],
            ),
            Container(
              width: 300,
              height: 100,
              color: Colors.yellow,
              child: Text('Container 4'),
            ),
          ],
        );
      }
    }
    ```
- Widget을 비율로 배치하기
  - ```
    class Body extends StatelessWidget {
      const Body({super.key});

      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            Container(
              width: double.infinity,
              height: 200,
              color: Colors.red,
            ),
            Expanded(
                child: Container(
                    color: Colors.blue,
                    width: 200,
                    height: 100,
                    child: SingleChildScrollView(
                        child: Column(
                      children: [
                        Container(
                            color: Colors.green,
                            height: 50,
                            width: 50,
                            margin: const EdgeInsets.symmetric(vertical: 10)),
                        Container(
                            color: Colors.green,
                            height: 50,
                            width: 50,
                            margin: const EdgeInsets.symmetric(vertical: 10)),
                        Container(
                            color: Colors.green,
                            height: 50,
                            width: 50,
                            margin: const EdgeInsets.symmetric(vertical: 10)),
                        Container(
                            color: Colors.green,
                            height: 50,
                            width: 50,
                            margin: const EdgeInsets.symmetric(vertical: 10)),
                      ],
                    )))),
            Flexible(
                child: Container(
              color: Colors.red,
              height: 100,
            )),
            //Flexible(flex: 1, child: Container(c olor: Colors.green,)),
            //Flexible(flex: 2, child: Container(color: Colors.blue,)),
          ],
        );
      }
    }
    ```
- Widget을 겹겹히 쌓아 올리기
  - ```
    class Body extends StatelessWidget {
      const Body({super.key});

      @override
      Widget build(BuildContext context) {
        return Stack(
          children: [
            Container(
              width: 400,
              height: 400,
              color: Colors.black,
            ),
            Container(
              width: 300,
              height: 300,
              color: Colors.red,
            ),
            Align(
              alignment: Alignment.topRight,
              child: Container(
                width: 200,
                height: 200,
                color: Colors.blue,
              ),
            ),
            Positioned(
                bottom: 20,
                right: 20,
                child: Container(
                  width: 100,
                  height: 100,
                  color: Colors.green,
                )
            ),
            Text(
              'Count 0',
              style: TextStyle(color: Colors.yellow, fontSize: 32),
            )
          ],
        );
      }
    }
    ```
































