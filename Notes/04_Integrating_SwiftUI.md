# Chapter 4: Integrating SwiftUI
* UIKit 프로젝트 안에서 SwiftUI 뷰 호스트하기
* SwiftUI 프로젝트 안에서 뷰 컨트롤러 호스트하기
* SwiftUI 프로젝트 안에서 UIKit의 뷰와 데이터를 호스트하기

## Getting started

## Hosting a SwiftUI view in a UIKit project
* UIHostingController는 SwiftUI View를 컨텐츠로 가진 UIViewController이다.
* ContentView를 로드할 때, SceneDelegate에서 본 적이 있다.
~~~
window.rootViewController = UIHostingController(rootView: ContentView(...))
~~~

* ViewController.swift 코드에 control Drag 로 세그 연결.
* `import SwiftUI` 해주고, 아래 코드 리턴
~~~
UIHostingController(coder: coder, rootView:
  ContentView(rGuess: 0.5, gGuess: 0.5, bGuess: 0.5))
~~~

## Hosting a view controller in a SwiftUI project
1. Add ViewController.swift and Main.storyboard to RGBullsEye.
2. In the storyboard’s identity inspector, set the Storyboard ID for ViewController.
3. Create a representation struct for ViewController.
4. Add a NavigationLink to ContentView.

### Conforming to UIViewControllerRepresentable
* make `makeUIViewController(context:)`
메인 스토리보드에서 뷰 컨트롤러를 instatiate
스토리보드를 사용하지 않는 뷰거나 빈 뷰 컨트롤러를 원할 때는 기본 생성자를 이용할 수 있음.
* update `updateUIViewController(_:context:)`
SwiftUI view에 의존하지 않기 때문에 비워둠.

### Navigating to the view controller
* 최상위 레벨 NavigationView
~~~
var body: some View {
  NavigationView {
    VStack { ... }
  }
}
~~~

### Previewing UIKit views
~~~
struct ViewControllerPreviews: PreviewProvider {
  static var previews: some View {
    ViewControllerRepresentation()
  }
}
~~~

## Hosting a UIKit view with data dependencies
1. Create a SwiftUI view that conforms to UIViewRepresentable.
2. Implement the **make method** to instantiate the UIKit view.
3. Implement the **update method** to update the UIKit view from the SwiftUI view.
4. Create a Coordinator and implement its valueChanged method to update the SwiftUI view from the UIKit view.

### Conforming to UIViewRepresentable
* UIViewControllerRepresentable이 아닌 UIViewRepresentable이지만 make/update 메서드는 요구된다. 

### Updating the UIView from SwiftUI
### Coordinating data between UIView and SwiftUI view
### Making it all happen!

