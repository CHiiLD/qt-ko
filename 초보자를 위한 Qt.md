# 초보자를 위한 Qt
## Qt 모듈 소개

+ `QtCore` 는 컨테이너, 스레드 관리, 이벤트 관리 등을 제공하는 기본 라이브러리입니다.
+ `QtGui` 및 `QtWidgets`는 데스크탑 용 GUI 툴킷으로, 많은 그래픽 구성 요소를 사용하여 응용 프로그램을 설계합니다.
+ `QtNetwork`는 네트워크 통신을 다루는 유용한 클래스 세트를 제공합니다.
+ `QtWebkit` 응용 프로그램에서 웹 페이지 및 웹 응용 프로그램을 사용할 수있게 해주는 웹킷 엔진
+ `QtSQL` 은 자체 드라이버로 확장 가능한 완전한 기능을 갖춘 SQL RDBM 추상화 계층으로, ODBC, SQLITE, MySQL 및 PostgreSQL을 지원합니다.
+ `QtXML` 간단한 XML 구문 분석 (SAX) 및 DOM 지원
+ `QtXmlPatterns` XSLT, XPath, XQuery 및 스키마 유효성 검사 지원

## 프로젝트 파일 구성
### qmake
makefile을 생성하기위해 프로젝트 파일을 분석하는 명령줄 도구

### .pro file
```
TEMPLATE = app
TARGET = name_of_the_app

QT = core gui

greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

HEADERS += first_file.h second_file.h
SOURCES += first_file.cpp second_file.cpp
```
+ `TEMPLATE` - 빌드 할 유형을 설명합니다. 응용 프로그램, 라이브러리 또는 단순히 하위 디렉토리 일 수 있습니다.
+ `TARGET` - 앱 또는 라이브러리의 이름입니다.
+ `QT` - 이 프로젝트에서 사용중인 라이브러리 (Qt 모듈)를 나타내는 데 사용됩니다. 첫 번째 앱은 작은 GUI이므로 `QtCore`와 `QtGui`가 필요합니다.

### 소스 코드
```
#include <QApplication>

int main(int argc, char **argv)
{
	QApplication app (argc, argv);
	return app.exec();
}
```
`QApplication` 은 매우 중요한 클래스입니다. 특히, 이벤트 루프를 처리 합니다. 이벤트 루프는 GUI 응용 프로그램에서 사용자 입력을 대기하는 루프입니다. __app.exec()__ 를 호출 하면 이벤트 루프가 시작됩니다.

## Qt 계층 구조
`QObject`는 Qt에서 최상위 클래스이며, 아래와 같은 기능을 가지고 있음
+ object name
+ parenting system
+ signals and slots
+ event management

QWidget은 모든 위젯 모듈이 상속하는 부모 클래스이며, 창을 구성하는 대부분의 속성 또는 위치, 크기, 마우스커서, 툴팁 같은 정보를 포함하고, 거의 모든 그래픽 요소를 상속받는다.

### parenting system
Qt의 객체를 부모-자식 관계로 계층 구조 트리를 구성하는 시스템
+ 객체가 파괴되면 소속된 자식도 파괴됨
+ `QObject`의 하위 객체를 검색하는데 사용 할 수 있다. `findChild` `findChildren`
+ 자식 위젯은 자동적으로 부모 위젯 내부에 추가된다.

버튼 안에 버튼
```
#include <QApplication>
#include <QPushButton>

int main(int argc, char **argv)
{
    QApplication app (argc, argv);
    QPushButton button1 ("test");
    QPushButton button2 ("other", &button1);
    button1.show();
    return app.exec();
}
```

## 위젯 코드와 main의 분리
Qt Creator로 QWidget를 상속받는 클래스를 생성하면 헤더파일에 아래의 요소를 볼 수 있다.
+ The `Q_OBJECT` macro.
+ A new category of methods : `signals`
+ A new category of methods : `public slots`

QWidget을 상속받은 Window클래스를 호출한다.
```
#include <QApplication>
#include "window.h"

int main(int argc, char **argv)
{
 QApplication app (argc, argv);

 Window window;
 window.show();

 return app.exec();
}
```

## Observer 패턴
Observer pattern은 __관찰 가능한 객체가 다른 관찰자 객체에 상태 변경 을 알리려고 할 때 사용됩니다.__ 다음은 몇 가지 구체적인 예입니다.

+ 사용자가 버튼을 클릭하면 메뉴가 표시됩니다.
+ 웹 페이지가 방금로드가 끝났으며 프로세스는로드 된 페이지에서 일부 정보를 추출해야합니다.
+ 사용자가 항목 목록 (예 : 앱 스토어)을 스크롤하여 끝에 도달하면 다른 항목을로드해야합니다.

## 신호와 슬롯
+ `signals` - 상태가 변화할 때, 메시지를 객체에 전송
+ `slots` - 시그널을 수신하고 응답 처리

QPushButton의 시그널
```
clicked
pressed
released
```

다른 클래스들의 슬롯
```
QApplication::quit
QWidget::setEnabled
QPushButton::setText
```

### 연결 방법
신호에 응답하려면 슬롯 을 신호에 연결 해야합니다 . Qt는 `QObject::connect` 메소드를 제공합니다. 이 방법은 두 개의 매크로 `SIGNAL` 및 `SLOT`을 사용합니다.
```
FooObjectA *fooA = new FooObjectA();
FooObjectB *fooB = new FooObjectB();

QObject::connect(fooA, SIGNAL (bared()), fooB, SLOT (baz()));
```

### 특징
+ 하나의 신호는 여러 슬롯에 연결할 수 있습니다.
+ 많은 신호를 하나의 슬롯에 연결할 수 있습니다.
+ 신호는 신호에 연결될 수 있습니다 : 신호 중계입니다. 첫 번째 신호가 전송되면 두 번째 신호가 전송됩니다.

## Meta Object
순수 C++로는 구현 불가능한 패러다임(런타임에 타입을 검사할 수 있는 기능, 비동기 함수 호출)을 구현한다.
[moc를 사용하는 이유](http://doc.qt.io/qt-5/why-moc.html)

## Q_OBJECT 매크로
signal-slot 등의 문법은 일반 C++ 컴파일러로 해석 불가능하다. 따라서 moc는 `connect`, `signals`, `slots` 같은 Qt 문법을 일반 C++문법으로 변환하기 위해서 제공된다. `Q_OBJECT` 매크로는 이러한 작업을 하기 위해 해더 클래스에 추가한다.
```
class MyWidget : public QWidget
{
    Q_OBJECT
public:
    MyWidget(QWidget *parent = 0);
}
```

moc에 의한 다른 매크로는 아래와 같다.
+ `signals`
+ public /projected / private `slots`

## 시그널과 슬롯 구현하기
1. `Q_OBJECT` 매크로 추가
2. `signals` 섹션 추가
3. `slots` 섹션과 슬롯 함수 선언
4. 슬롯 함수 정의
5. `connect` 함수로 연결

### 신호 전파하기
`emit` 키워드를 사용하여 시그널을 전파할 수 있다.
```
emit mySignal();
emit mySignal(firstParameter, secondParameter …);
```

순서  
signals def > slots fn def > connect > signal emit > raise slots

## 트러블슈팅
`Q_OBJECT` 매크로를 추가하고 프로그램을 컴파일하면 컴파일 에러가 발생한다. 이는 메타 오브젝트 컴파일러가 메타 오브젝트를 가진 클래스를 실행할 수 없기 때문이다. 따라서 Build > Run qmake를 실행하여 qmake를 재실행한다.
