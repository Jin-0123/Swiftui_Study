# Chapter 3: Understanding SwiftU

## Why SwiftUI?
인터페이스 빌더와 스토리보드는 개발을 빠르게 할 수 있도록 많은 도움을 주지만 일부 많은 개발자들은 코드로 뷰르 만드는 것을 더 선호함. </br>
코드로 작성했을 때, UI의 재사용과 편집에 더 효율적이기 때문. </br>
IBOutlet, IBAction의 이름이 바뀌거나 제거 되었을 때 인터페이스 빌더에서는 코드르 보지 못하기 때문에 크래시가 남. </br>
세그나 테이블뷰의 identifier의 스트링 오타때문에 짜증이 난 적이 있을 것이다. 엑스코드는 그것을 체크해 줄 수 없다. </br>

SwiftUI는 코드와 바로 옆에 프리뷰를 보면서 작업가능. </br>
변화가 생겼을 때 바로 업데이트를 해줌. 어떤 identifier 스트링이 잘못되는 경우도 없음. </br>
코드량도 작성한 것보다 훨씬 더 코드가 적고, 이해햐기 쉽다. </br>


SwiftUI는 UIKit을 대체하는 것이 아니라 Swift와 ObjectiveC와 같이 둘다 사용가능하다. </br>
또한 SwiftUI API는 어떤 플랫폼이든 일관적이어서, 멀티 플랫폼을 구현하기 쉽다. </br>

### Declarative app development
SwiftUI는 너가 **declarative** 앱 개발이 가능하도록 한다. </br>
다르게 생각하기를 익힌다면 더 빠르게 좋은 앱을 개발할 수 있을 것이다. </br>
Declartive앱 개발의 의미는 너가 원하는 뷰는 어떻게 생겼고, 그들이 의존하는 데이터는 어떤 것인지 선언하는 것을 말한다. </br>
SwiftUI 프레임워크는 그들이 의존하고 있는 데이터가 변경됨에 따라 뷰를 나타내고 업데이트 해야할 때 뷰를 만든다. </br>

너는 뷰의 상태가 어떻게 노출되는지, 뷰의 데이터 의존상태에서 SWiftUI가 어떻게 리액트 해야하는지를 선언한다. </br>

- View: </br>
코드와 동기화되는 Declartive UI. </br>
다른 플랫폼에도 API가 일관적이어서 한번 배우면 어디든지 적용할 수 있다. </br>
Controls는 그들의 역할을 서술한다. 그들의 외형이아니라. 그래서 같은 컨트롤이 해당 플랫폼에 따라 적절하게 보이도록 한다. </br>

- Data: </br>
Declretice 데이터 의존성은 데이터가 변화할 때 뷰를 업데이트한다. </br>
뷰의 가능한 상태르 선언하고 각 상태에 따라 뷰가 어떻게 변하는지 선언한다. </br>

- Navigation: </br>
조건적인 서브뷰를 네비게이션과 대체할 수 있음

- Integration: </br>
SwiftUI와 UIKit의 통합

## Getting started

### SWiftUI vs. UIKit
UIKit 앱으로 만들기 위해서는 스토리 보드에 몇몇의 라벨과 버튼, 세개의 슬라이드가 있고 outlet과 action으로 뷰 컨트롤러와 연결되어있다. 그 다음 메서드를 작성해 슬라이드가 움직일 때마다 값을 색, 라벨, 라벨의 컬러르 변경한다. 어떤 것을 할지 정확하 순서로 생각해야한단다. 이 단계는 쉽게 잊게 된다. </br>

SwiftUI로 만들기 위해서는 Color, Text, Button, Slider 서브뷰르 나타나기 원하는 순서대로 목록화한다. (오토레이아웃 셋탕하는 것보다 훨씬 쉬움) </br>
그 다음 각각 데이터에 따라 어떻게 변화하는지 선언한다. 뷰의 상태에 따라서 뷰가 일관성을 유지하 수 있도록 데이터 의존관계를 관리한다. </br>

올바른 순서가 무엇인지 걱정할 필요 없고 UI 객체 업데이트를 잊을 걱정하 필요도 없다. 캔버스 프리뷰는 스토리보드가 필요없다는 것을 의미한다. 서브뷰는 스스로 업데이트가 유지되며 뷰 컨트롤러 또한 그렇다. 그리고 라이브 프리뷰는 너가 거의 시뮬레이터를 실행하지 않아도 된다는 것을 뜻한다. </br>

## Declaring views
SwiftUI 뷰는 너의 UI의 한 부분이다: 작은 뷰를 결합해서 큰 뷰로 빌드한다. Text, Color와 같이 작은 단위의 뷰들이 있고, 그것을 커스텀뷰의 기초 블럭으로 사용하 수 있다.

- 첫 번째 탭에는 레이아웃과 컨트롤을 위한 작은 단위의 뷰들이다. UIKit과 유사함. 몇몇은 SwiftUI에만 있다.
- 두 번째 탭에는 레이아웃, 효과, 텍스트, 이벤트 등 표현, 환경, 접근성과 같은 다른 목적들을 위한 modifiers의 리스트이다.

SwiftUI는 작은 뷰들도 재사용 가능하게 생성한다. 그리고 어디서 사용하든지 해당 문맥에 따라 modifiers를 통해 커스텀할 수 있다. 어떤 타입의 뷰든 modifiers르 적용하 수 있다. 나중에 알게 되겠지만 가끔은 순서가 중요하다.

### Environment values
몇 개의 환경변수는 전체 앱에 영향을 준다. 환경변수의 대부분은 디바이스의 유저 셋팅과 일치한다. (접근성, 위치, 캘린더, 색상테마...) 보토 프리뷰 안에서 셋팅으로부터 발생됬을 지도 모르는 문제를 예상하고 풀기위해서 환경 변수의 값을 테스트한다. 
- 문서 참고: https://developer.apple.com/documentation/swiftui/environmentvalues

다크모드르 시작시점에 적용하기를 원한다면, 앱의 탑 레벨 뷰에서 환경변수를 셋팅해야한다.

### Modifying reusable views
HStack은 Padding()을 modifier로 가진다. 끝나는 지점에 space를 추가할 수 있다. 

### Adding modifiers in the right order
SwiftUI는 추가한 순서대로 modifier를 적용한다. 백그라운트 컬러 다음에 패딩을 추가했다면, 패딩을 추가한 다음 백그라운드 컬러 추가했을 때와 다름 효과를 보일 것이다.

컬러 입히고, cornerRadius 주면 cornerRadius 값이 적용되지만, 그 반대의 경우 cornerRadius 값이 적용되지 않음.
백그라운드 컬러가 전체 사각형에 효과를 주기 때문.

### Showing conditional views
특정 조건이 true일 때만 나타나는 뷰: Alert, showAlert이 true일 때만 나타남.

### Using ZStack
Z방향은 스크린과 수직 방향 

### Debugging
엑스코드 라이브 프리뷰에서 런타임 디버깅을 하려면? Control+click OR Right-click 라이브 프리뷰버틍 > 디버깅 프리뷰 메뉴 선택

## Declaring data dependencies

### Guiding principles
SwiftUI에는 앱을 통해서 어떻게 데이터가 흘러가는지 관리하는 두 가지 원리가 있음.

- Data access = dependency: 뷰의 데이터의 일부를 읽는 것은 의존성을 만든다. 모든 뷰는 데이터 디펜던시(인풋/상태)를 위한 function 이다.
- Single source of truth: 뷰의 모드 데이터 조각은 truth의 source를 가진다. </br>
컬러 슬라이더에 @State를 선언하지 않은 이유는 중복된 source를 가졌기 때문이다. Value와 싱크되어 유지되어야한다. 대신 @Binding 값을 선언했고, 그것은 그 뷰가 또 다른 뷰로부터 @State 변수에 의존하고 있다는 것을 의미한다.

### Tools for data flow
**Property wrappers**: @State, @Binding, @ObservedObject, @EnvironmentObject

- @State: 변수가 뷰에 의해 소유되고 있음. var로 할당됨. 값을 초기화해야함. private으로 사용할 것을 권장.
- @Binding: 다른 뷰에 소유된 @State 변수에 디펜던시를 명시한다. $ prefix 사용하므로 다른 뷰에 변수의 상태를 패스. 받는 뷰의 입장에서는 @Binding 변수는 데이타의 reference. 값을 초기화할 필요는 없음. 이 참조는 뷰를 상태에 따라 수정하게 하며 데이터에 의존적이다.
- @ObservedObject: ObservableObject의 프로토콜을 따르는 참조타입의 디펜던시를 명시한다.
- @EnvironmanetObject: 앱 내에서 모든 뷰에 보이는 공유된 데이터에 디펜던시를 명시한다. 바로 데이터를 패스할 수 있어서 편리. 부모가 자녀에게 또 그의 자녀에게 패스하는 것 대신.

재사용 뷰에서는 @State는 잘 사용하지 않고 대신 @Binding 이나 @ObservedObject르 사용한다. 

### Observing a reference type object
ObservableObject 사용해서 타이머 만들기

- @Published, ObservableObject
