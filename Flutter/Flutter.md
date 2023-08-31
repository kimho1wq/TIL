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

- Stateless와 Stateful
  - Stateless Widget Lifecycle
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/8c2e45d4-fe02-48e1-b2d0-f1a6b7b2ab5c)
  - Sataeful Widget Lifecycle
    - ![image](https://github.com/kimho1wq/TIL/assets/15611500/6196e080-08f7-418d-9269-c8dd3f5abb4b)
  - ```
    import 'package:flutter/material.dart';
  
    void main() {
      runApp(MaterialApp(
        home: SafeArea(
          child: Scaffold(
            appBar: AppBar(),
            body: Body(),
          ),
        ),
      ));
    }
  
    class Body extends StatelessWidget {
      const Body({super.key});
  
      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            ExampleStateless(),
            ExampleStateful(index: 3),
          ]
        );
      }
    }
  
    class ExampleStateless extends StatelessWidget {
      const ExampleStateless({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Expanded(
          flex: 1,
          child: Container(
            color: Colors.red,
          ),
        );
      }
    }
  
  
    class ExampleStateful extends StatefulWidget {
      final int index;
  
      const ExampleStateful({required this.index, Key? key}) : super(key: key);
  
      @override
      State<ExampleStateful> createState() => _ExampleStatefulState();
    }
  
    class _ExampleStatefulState extends State<ExampleStateful> {
      late int _index;
      late TextEditingController textEditingController;
  
      @override
      void initState() {
        super.initState();
        _index = widget.index;
        textEditingController = TextEditingController();
      }
  
      @override
      void dispose() {
        textEditingController.dispose();
        super.dispose();
      }
  
      @override
      Widget build(BuildContext context) {
        return Expanded(
          flex: 1,
          child: GestureDetector(
            onTap: () {
              setState(() {
                if(_index == 5) {
                  _index = 0;
                  return;
                }
                _index += 1;
              });
            },
            child: Container(
              color: Colors.blue.withOpacity(_index / 5),
              child: Center(child: Text("$_index")),
            ),
          ),
        );
      }
    }
    ```
- 다양한 Flutter의 입력 (박스, 버튼, 메뉴 등)
  - ```
    class Body extends StatelessWidget {
      const Body({super.key});
  
      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            TestCheckBox(),
            TestRadioButton(),
            TestSlider(),
            TestSwitch(),
            TestPopupMenu(),
          ],
        );
      }
    }
    class _TestCheckBoxState extends State<TestCheckBox> {
      List<bool> values = [false, false, false];
  
      @override
      void initState() {
        super.initState();
        values = [false, false, false];
      }
  
      @override
      Widget build(BuildContext context) {
        return Row(
          children: [
            Checkbox(
                value: values[0],
                onChanged: (value) => changeValue(0, value: value)),
            Checkbox(
                value: values[1],
                onChanged: (value) => changeValue(1, value: value)),
            Checkbox(
                value: values[2],
                onChanged: (value) => changeValue(2, value: value)),
          ],
        );
      }
  
      void changeValue(int index, {bool? value = false}) {
        setState(() {
          values[index] = value!;
        });
      }
    }

    enum TestValue {
      test1,
      test2,
      test3;
    }
  
    class _TestRadioButtonState extends State<TestRadioButton> {
      TestValue? selectValue;
  
      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            ListTile(
              leading: Radio<TestValue>(
                  value: TestValue.test1,
                  groupValue: selectValue,
                  onChanged: (value) => setState(() => selectValue = value!)),
              title: Text(TestValue.test1.name),
              onTap: () => setState(() {
                if (selectValue != TestValue.test1) {
                  selectValue = TestValue.test1;
                }
              }),
            ),
            Radio<TestValue>(
                value: TestValue.test2,
                groupValue: selectValue,
                onChanged: (value) => setState(() => selectValue = value!)),
            Radio<TestValue>(
                value: TestValue.test3,
                groupValue: selectValue,
                onChanged: (value) => setState(() => selectValue = value!)),
          ],
        );
      }
    }

    class _TestSliderState extends State<TestSlider> {
      double value = 0;
  
      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            Text('$value'),
            Slider(
              value: value,
              onChanged: (newValue) => setState(() => value = newValue),
              divisions: 100,
              max: 100,
              min: 0,
              label: value.round().toString(),
              activeColor: Colors.green,
            ),
          ],
        );
      }
    }
  
    class _TestSwitchState extends State<TestSwitch> {
      bool value = false;
  
      @override
      Widget build(BuildContext context) {
        return Column(children: [
          Switch(
            value: value,
            onChanged: (newValue) => setState(() => value = newValue),
          ),
          CupertinoSwitch(
            value: value,
            onChanged: (newValue) => setState(() => value = newValue),
          )
        ]);
      }
    }
  
    class _TestPopupMenuState extends State<TestPopupMenu> {
      TestValue selectValue = TestValue.test1;
  
      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            Text(selectValue.name),
            PopupMenuButton(
              itemBuilder: (context) {
                return TestValue.values
                    .map((value) =>
                        PopupMenuItem(value: value, child: Text(value.name)))
                    .toList();
              },
              onSelected: (newValue) => setState(() => selectValue = newValue),
            ),
          ],
        );
      }
    }
    ```
- Flutter의 Callback
  - ``` 
    class _TestWidgetState extends State<TestWidget> {
      int value = 0;

      @override
      Widget build(BuildContext context) {
        return Column(
          children: [
            Text(
              'Count: $value',
              style: const TextStyle(fontSize: 30),
            ),
            TestButton(addCounter, addParamCounter),
          ],
        );
      }

      void addCounter() => setState(() => value += 1);
      void addParamCounter(int addValue) => setState(() => value = addValue + value);
    }

    class TestButton extends StatelessWidget {
      const TestButton(this.voidCallback, this.functionCallback, {Key? key}) : super(key: key);
  
      final VoidCallback voidCallback;
      final Function(int) functionCallback;
  
      @override
      Widget build(BuildContext context) {
        return Container(
          margin: const EdgeInsets.symmetric(vertical: 10),
          width: double.infinity,
          child: InkWell(
            onTap: () => voidCallback.call(),
            onDoubleTap: () => functionCallback.call(2),
            onLongPress: () => functionCallback.call(3),
            child: Center(
              child: Container(
                padding: EdgeInsets.symmetric(vertical: 4, horizontal: 8),
                decoration: BoxDecoration(border: Border.all()),
                child: Text(
                  'Up Counter',
                  style: TextStyle(fontSize: 24),
                ),
              ),
            ),
          ),
        );
      }
    }
    ``` 

- 로컬 이미지 데이터 가져오기
  - ```
    const assetImagePath = 'assets/images';
    const bannerImage = '$assetImagePath/banner.png';
    
    class Body extends StatelessWidget {
      const Body({Key? key}) : super(key: key);

      @override
      Widget build(BuildContext context) {
        return Center(
          child: Image.asset(bannerImage),
        );
      }
    }
    ```

- Flutter에서 화면 이동하기
  - ```
    class _BodyState extends State<Body> {
      late int index;
  
      @override
      void initState() {
        super.initState();
        index = 0;
      }
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          appBar: AppBar(),
          body: homeBody(),
          bottomNavigationBar: BottomNavigationBar(
            items: const [
              BottomNavigationBarItem(icon: Icon(Icons.home), label: 'Home'),
              BottomNavigationBarItem(icon: Icon(Icons.search), label: 'Search'),
              BottomNavigationBarItem(icon: Icon(Icons.person), label: 'User'),
            ],
            currentIndex: index,
            onTap: (newIndex) => setState(() => index = newIndex),
          ),
        );
      }
  
      Widget homeBody() {
        switch(index) {
          case 1:
            return const Center(child: Icon(Icons.search, size:100,),);
          case 2:
            return const Center(child: Icon(Icons.person, size:100,),);
          case 0: default:
            return const Center(child: Icon(Icons.home, size:100,),);
        }
      }
    }
  
  
    class HomeWidget extends StatelessWidget {
      const HomeWidget({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
              title: const Text('Flutter에서 화면 이동하기'),
            ),
            body: Center(
                child: TextButton(
              child: const Text('Go to Page'),
              onPressed: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (context) {
                      return const NewPage();
                    },
                  ),
                );
              },
            )));
      }
    }
  
    class NewPage extends StatelessWidget {
      const NewPage({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
              title: Text('Welcome new Page'),
            ),
            body: Center(
              child: Column(mainAxisAlignment: MainAxisAlignment.center, children: [
                TextButton(
                  child: const Text('Go to Back'),
                  onPressed: () {
                    Navigator.pop(context);
                  },
                ),
                TextButton(
                  child: const Text('Go to New Page2'),
                  onPressed: () {
                    Navigator.push(
                      context,
                      MaterialPageRoute(
                        builder: (context) {
                          return const NewPage2();
                        },
                      ),
                    );
                  },
                ),
              ]),
            ));
      }
    }
  
    class NewPage2 extends StatelessWidget {
      const NewPage2({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
              title: Text('Welcome new Page2'),
            ),
            body: Center(
              child: Column(mainAxisAlignment: MainAxisAlignment.center, children: [
                TextButton(
                  child: const Text('Go to Back'),
                  onPressed: () {
                    Navigator.pop(context);
                  },
                ),
                TextButton(
                  child: const Text('Go to Home'),
                  onPressed: () {
                    Navigator.popUntil(context, (route) => route.isFirst);
                  },
                ),
              ]),
            ));
      }
    }
    
    
    /// pub.dev에서 go_router 외부 라이브러리 import
    dependencies:
      flutter:
        sdk: flutter
      go_router: 10.1.2
    import 'package:go_router/go_router.dart';

    void main() {
      runApp(MaterialApp.router(
        routerConfig: GoRouter(initialLocation: '/', routes: [
          GoRoute(
              path: '/', name: 'home', builder: (context, _) => const HomeWidget()),
          GoRoute(
              path: '/new', name: 'new', builder: (context, _) => const NewPage()),
          GoRoute(
              path: '/new1',
              name: 'new1',
              builder: (context, _) => const NewPage2()),
        ]),
      ));
    }
  
    class HomeWidget extends StatelessWidget {
      const HomeWidget({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
              title: const Text('Flutter에서 화면 이동하기'),
            ),
            body: Center(
                child: TextButton(
              child: const Text('Go to Page'),
              onPressed: () {
                context.pushNamed('new');
              },
            )));
      }
    }
  
    class NewPage extends StatelessWidget {
      const NewPage({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
              title: Text('Welcome new Page'),
            ),
            body: Center(
              child: Column(mainAxisAlignment: MainAxisAlignment.center, children: [
                TextButton(
                  child: const Text('Go to Back'),
                  onPressed: () {
                    context.pop();
                  },
                ),
                TextButton(
                  child: const Text('Go to New Page2'),
                  onPressed: () {
                    context.pushNamed('new1');
                  },
                ),
              ]),
            ));
      }
    }
  
    class NewPage2 extends StatelessWidget {
      const NewPage2({Key? key}) : super(key: key);
  
      @override
      Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
              title: Text('Welcome new Page2'),
            ),
            body: Center(
              child: Column(mainAxisAlignment: MainAxisAlignment.center, children: [
                TextButton(
                  child: const Text('Go to Back'),
                  onPressed: () {
                    context.pop();
                  },
                ),
                TextButton(
                  child: const Text('Go to Home'),
                  onPressed: () {
                    context.goNamed('home');
                  },
                ),
              ]),
            ));
      }
    }
    ```









