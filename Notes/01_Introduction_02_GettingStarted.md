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
_UIHostingController_는 SwiftUI 뷰인 ContentView를 위한 뷰 컨트롤러를 생성한다. 
_UIHostingController_는 실재하는 앱 안에서 SwiftUI 뷰를 통합할 수 있도록 활성화한다.

### Previewing your ContentView
~~~
struct ContentView_Previews : PreviewProvider {
  static var previews: some View {
    ContentView()
  }
}
~~~
ContentView_Previews는 ContentView의 인스턴트를 포함한 뷰를 가지고 있다.
Preview는 어떤 데이터를 명시할지 확인하고, 다른 폰트사이즈와 컬러 스킴들을 비교하는 곳이다.
Xcode의 **Resume** 버튼을 누르면 확인 가능하다. 버튼이 없다면, Editor Options > Canvas를 누르면 된다.
Shortcut Key: Option-Command-P

### Previewing in landscape
