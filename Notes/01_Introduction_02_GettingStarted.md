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
SwiftUI앱은 스토리보트나 뷰 컨트롤러를 가지고 있지 않다.-ContentView.swift가 그들의 모든 작업을 감당한다. UI를 만들기 위해 코드와 object 라이브러리에서 드래그의 어떤 조합으로도 사용할 수 있다. 그리고 코드에서 직접 스토리보드와 같이 사용할 수 있다. 더 좋은 것은 모든 것은 동기화된다는 것이다.

SwiftUI는 선언적이다. 너가 원하는 UI는 어떻게 생겼는지 선언하는 것이다. SwiftUI는 너의 선언을 유능한 코드로 변환하여 작업을 완료한다. Apple은 더 읽기 쉬운 코드를 유지할 수 있게 하므로써 많은 뷰를 만들 수 있도록 한다. 재사용가능한 파라미터로 표시되어진 뷰들을 특히 추천한다.

### Some SwiftUI vacabulary
* Canvas and Minimap
앱의 뷰를 코드 에디터를 옆에 나란히 두고 canvas 안에서 미리볼 수 있다. 또한 minimap을 이용할 수 있다. 
* Container views
이전에 스택뷰를 사용했다면, HStack과 VStack을 사용하면서 SwiftUI에서 더 쉽게 만들 수 있다는 것을 알게 될 것이다. ZStack과 Group(Chapter9 에서 배우게 될  Containers)을 포함한 다른 컨테이너 뷰들도 있다.
