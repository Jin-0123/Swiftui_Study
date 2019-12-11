# Chapter 1: Introduction

_Apple:_
> SwiftUI is an innovative, exceptionally simple way to build user interfaces across all Apple platforms with the power of Swift.

SwiftUI는 스위프트의 강력함으로 애플의 모든 플랫폼을 커버하는 유저 인터페이스를 빌드할 수 있는 혁신적이고 이례적으로 간단한 방법이다. 

# Chapter 2: Getting Started
**_이 장에서 배우는 것은:_**
* 코드와 나란히 UI를 만들기 위해 Xcode 캔버스를 어떻게 사용하는지, 어떻게 동기화 하는지(한 부분의 변화가 항사 다른 부분을 업데이트하는 경우)에 대하여 배운다.
* 재사용가능한 뷰를 만든다.
* _@State_ 변수에 대하여 배우고, 상태가 변했을 때마다 UI를 업데이트하기 위해 어떻게 사용하는지 익힌다.
* 알림을 띄운다.

## Getting Started
이 앱은 적색, 녹색, 청색 값이 임의적으로 생성된 특정 색상을 보여준다. 유저는 왼쪽 색상블럭과 오른쪽의 것과 매치하기 위해 슬라이더를 움직일 수 있다.

### Creating a new SwiftUI project 
~~~
window.rootViewController = UIHostingController(rootView: contentView)
~~~
* UIHostingController 
  - SwiftUI 뷰인 ContentView를 위한 뷰 컨트롤러를 생성한다. 
  - 실재하는 앱 안에서 SwiftUI 뷰를 통합할 수 있도록 활성화한다.

### Previewing your ContentView
~~~
struct ContentView_Previews : PreviewProvider {
  static var previews: some View {
    ContentView()
  }
}
~~~
* ContentView_Previews는 ContentView의 인스턴트를 포함한 뷰를 가지고 있다.
* Preview는 어떤 데이터를 명시할지 확인하고, 다른 폰트사이즈와 컬러 스킴들을 비교하는 곳이다.
* Xcode의 **Resume** 버튼을 누르면 확인 가능하다. 버튼이 없다면, Editor Options > Canvas를 누르면 된다.
* Shortcut Key: Option-Command-P

### Previewing in landscape
ContentView_Previews의 previews안에 ContentView()을 다음의 코드로 바꾸면 iPhone-SE사이즈의 landscape로 회전된 프리뷰가 나타난다.
~~~
ContentView().previewLayout(.fixed(width: 568, height: 320))
~~~

## Creating your UI
* SwiftUI앱은 스토리보트나 뷰 컨트롤러를 가지고 있지 않다. ContentView.swift가 그들의 모든 작업을 감당한다. UI를 만들기 위해 코드와 object 라이브러리에서 드래그의 어떤 조합으로도 사용할 수 있다. 그리고 코드에서 직접 스토리보드와 같이 사용할 수 있다. 더 좋은 것은 모든 것은 동기화된다는 것이다.

* SwiftUI는 선언적이다. 너가 원하는 UI는 어떻게 생겼는지 선언하는 것이다. SwiftUI는 너의 선언을 유능한 코드로 변환하여 작업을 완료한다. Apple은 더 읽기 쉬운 코드를 유지할 수 있게 함으로써 많은 뷰를 만들 수 있도록 한다. 재사용가능한 파라미터로 표시되어진 뷰들을 특히 추천한다.

### Some SwiftUI vacabulary
* Canvas and Minimap: 앱의 뷰를 코드 에디터를 옆에 나란히 두고 canvas 안에서 미리볼 수 있다. 또한 minimap을 이용할 수 있다. 
* Container views: 이전에 스택뷰를 사용했다면, HStack과 VStack을 사용하면서 SwiftUI에서 더 쉽게 만들 수 있다는 것을 알게 될 것이다. ZStack과 Group(Chapter9 에서 배우게 될  "Containers")을 포함한 다른 컨테이너 뷰들도 있다. 게다가 Containter뷰에는 너가 알고, 아끼는 UIKit의 많은 객체들을 위한 SwiftUI 뷰들이 있다. 예를 들면, Text, Button, Slider. 툴바에 + 버튼은 SwiftUI 뷰들의 라이브러리를 보여준다.
* Modifiers: UIKit 객체들의 attributes나 properties을 설정하는 대신에 너는 modifiers를 첨부할 수 있다. 배경, 폰트, 패딩, 더 많은 것들을 위해.

### Creating the target color block
* SwiftUI에서는 body의 최상단 레벨에서는 하나 이상의 뷰를 가질 수 없다. 그래서 다른 뷰들은 container view 안에 넣어야 한다. 

1. Embeded VStack
2. Inspect 열어서 Text 변경하기 
3. "+" 버튼 눌러서 VStack에 Color 삽입하기

* IB에서는 뷰에서 여러 개의 객체를 드래그해서 선택하면 스택 뷰안에 embed할 수 있었지만 SwiftUI에서는 Embed를 한 객체 안에서만 수행할 수 있다. 

### Creating the guess color block

### Creating the button and slider

## Updating the UI
값이 변할 때 UI가 업데이트 되어야 한다면, @State variable로서 나타낸다. 
@State variable이 변경될 때, 뷰는 설정값을 무효화 시키고, body를 리컴퓨트한다. 

### Using _@State_ variables
~~~
let rTarget = Double.random(in: 0..<1)
let gTarget = Double.random(in: 0..<1)
let bTarget = Double.random(in: 0..<1)
@State var rGuess: Double
@State var gGuess: Double
@State var bGuess: Double
~~~

### Updating the Color views

## Making Reusable Views

### Making the red slider

### Binding
* "$": 값이 변경되면 업데이트하기 위해서
  - rguess: just read
  - $rguess: read-write binding
  
### Extracting subviews

### Live Preview

### Presenting an Alert

### Key point
* Xcode canvas는 UI를 코드와 나란히 놓고 동기화 시키면서 반영할 수 있다. 한 면이 변하면 다른 면도 업데이트 된다.
* 코드로도 canvas로도 도구의 조합으로도 UI를 만들 수 있다.
* 스토리보드에서 사용했던 것처럼 수직/수평 스택으로 뷰의 객체를 구성할 수 있다.
* Preview는 앱이 어떻게 생겼는지, 다른 셋팅에서 혹은 초기 데이타에서 어떻게 동작하는지 알 수 있게 해준다. 그리고 Live Preview에서는 시뮬레이터 없이 너의 앱과 인터렉션할 수 있다. 
* 재사용 뷰를 만들 수 있다. Extract Subview를 사용하면 매우 쉽다.
* SwiftUI는 @State variable의 값이 변경되면 언제든지 UI를 업데이트 한다. @Binding으로 subview의 참조를 넘기면 @State variable의 read-write 접근을 허용한다. 
* alert을 띄우는 것은 짱 쉽다.
