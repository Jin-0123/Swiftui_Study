# Chapter 7: State & Data Flow

With SwiftUI views are a function of state, not a sequence of events, with the app as a constant feedback loop.
- **User** > (interact) > **Action** > (mutate) > **State** > (update) > **View** > (render) > **User**

## What is state?
- what state means: state의 의미가 뭔지
- how SwiftUI’s specific definition applies: SwiftUI의 특정한 정의를 어떻게 적용하는지

- 프러퍼티 랩퍼는 값이 변화하는 것을 자동으로 앱에 반영한다. boilerplate state management 작성하거나 바인딩코드 없음에도.

## Starting with @State
- `@State`: 로컬 뷰 내에서 자동적으로 persistent 값을 모니터하고 읽을 수 있도록 한다.
- 모든 `@State`는 프레임 워크가 관리하는 주어진 값을 항상 참으로 유지하는 source. 특정 스코프 내의 심플 타입들과 찰떡임. (e.g. Bool, Int, or String)

~~~
@State var name: String = "
TextField("Enter your name", text: $name).multilineTextAlignment(.center)
~~~
> `@State` 선언한 값은 `$` 프리픽스르 붙여서 접근 가능, 변화되는 값이 바인딩될 수 있는 랩퍼를 생성한다는 것이 중요.
> name 값이 업데이트 되면, 뷰는 무효화하고 재빌드.

~~~
var name: State<String>
...
name.binding = "Ray
~~~
> `@State` 나 `$` 대신 이렇게도 작성가능, 바인딩은 텍스트 필드와 양방향 관계다.
> 1. 텍스트 필드의 값을 수정할 때, state 랩퍼의 참조를 리테인. 
> 2. 추가적인 코드 필요없이 `@State` 오브젝트의 값이 바뀐다.

- read-only 와 같은 단방향 참조일 경우 `@State` 불필요. 신중하게 사용해야함.
- @State는 뷰의 라이프 사이클 동안에만 메모리에 있고, 더 이상 필요하지 않으면 메모리에서 해제.
- @State를 효율적으로 사용하려면, 가능하다면 제한해야함. the derived binding or value를 대신 사용할 수 있다면.

## @State: Learning to share
- `@Binding`: bound 값(변경에 허가가 필요한 값)의 매니저 오브젝트로 감싼다.
- 그 값이 변경되지 않는데, 이니셜라이즈에서 넘겨진 @Binding 오브젝트는 허가를 받아야만함. 변경이 일어났을 때 @State 오브젝트에 알리기 위해서.
- 만약에 상수로 @Binding 오브젝트를 만들기를 시도했다면 다음과 같은 오류를 볼 수 있다.
  1. one at the declaration (“Property wrapper can only be applied to a var”)
  2. when you try and set it during initialization ("Value of type ScoreView has no member answered")
  
- @Binding은 데이터에 first-class reference 이고, 재사용성이 아주 좋다. $ 프리픽스를 사용했던 @State에서와 같은 방법으로 바인딩한 값에 접근할 수 있음.
- 이니셜라이저에서 셋팅할 때, 최선의 방법은 언더스코어를 변수에 프리픽스로 붙이는 것이다. 
  - `answered`: state warpper > Int를 셋하려고 하면 에러, Binding<Int> 타입임.
  - `_answered`: state value
- 상수변수는 빌드타입에 셋, 바인딩변수는 이니셜라이저에서 셋. 양방향 업데이트가 필요하지 않은데 왜 바인드 해야하는가? 재빌드를 해야지만 갱신이 되기 때문.
- `@State`를 사용하므로써 강제적으로 재빌드 시킴.

- 뷰간의 메모리 공유는 메모리 측면에서는 효율적이지만, 뷰의 변경을 위해 각 값을 변경해야할 때는 혼동을 줄 수 있다. 
- 데이터는 상태이고, 상태는 곧 디펜던시. 그 디펜던시에는 책임이 따름.
- SwiftUI에서 언제, 어디서 디펜던시가 생겼는지 주의해야함.

- 값을 재선언하면, 참 값의 두 개의 소스가 생성되고 SwiftUI는 state 모델을 무효화한다.
- 여러 개의 뷰로 구성된 부모뷰의 @State 값을 전달하기를 원한다면, 객체 생성하면서 전달하셈.
~~~
// In RegisterView
if profileViewModel.isRegistered {
  WelcomeView(name: name)
} else {
...
~~~
- 이렇게 전달하다보면 점점 뷰모델이 복잡해짐. 그래서 두번째 옵션은 `ObservableObject protocol`임.
- 복잡한 데이터 타입의 상태 관리를 단순화 시켜줌.
- 구현에는 두 가지 방법이 있고, 먼저 API 변경이 필요없는 `@EnvironmentObject` 부터 시작.

## Environmental state
- 여러 개의 뷰를 넘어 앱 생명주기 전체에서 객체의 지속성을 지원함.
- `@EnvironmentObject` 선언할 때마다 앱의 단일 객체를 메모리에 유지하도록한다. 이러한 동작을 원하는 뷰에서 선언.

### Adding a user profile state
- 뷰 상태 모델 / 일반 데이터 모델 분리

~~~
@Published var isRegistered: Bool = false
~~~
- `@Published` : 상태의 서브스크라이버에게 알림을 보내는 가장 간단한 방법.
- 위 예제 코드에서는 유저가 등록되었음을 서브스크라이버에게 알림

- 단순한 뮤테이트 이벤트를 보낼 때, 디스패치큐 코드를 사용한다고 해서 크게 성능 효과는 없음.

### Initiating the environment state scope
- Global state는 가장 피해야함. 앱 확장할 때 모델 복잡도를 증가시킴.
- `@EnvironmentObject` 스코프가 제한되게 설계되어있음. 상위 뷰 안에 이미 셋업되어있지 않으면 접근이 불가능.
- 호출을 통해 전달되었거나 상위 뷰를 통해 전달된 특정 뷰에서만 사용가능.
- global static class의 경우 다른 핸들링이 필요.

- 계층이없는 뷰에 존재해야하는 모델이라면, 상위 뷰에서 하위 뷰로 모델을 전달하지 않는 경우 > @EnvironmentObject 좋은 선택임.
- 예를 들면 User Preferences.

### Configuring the preview environment

### The power of the environment
- `@EnvironmentObject` 환경상태를 공유하는 곳에 효율적으로 사용될 수 있음.
~~~
@Environment(\.locale) var locale: Locale
~~~
- `@Environment` 를 사용하면 시스템 환경 설정에 접근 가능 (언어, 캘린더, 지역 등)

## The happy middle-ground: @ObservedObject
- ObservableObject 프로토콜과 혼동 X, ObservedObject는 @EnvironmentObject와 비슷한 방식으로 ObservableObject 프로토콜을 구현한 attribute.
- state-managed 객체를 생성하는 것을 제공. 앱의 환경설정 부분이 아닌!
- `@EnvironmentObject` 와 달리 ` @ObservedObject` 는 멀티 뷰에서 자동으로 액세스 할 수 없으며 사용 가능하도록 상위 객체에서 전달되어야함.
- @State는 뷰 내부에서 생성되어서 복잡한 뷰의 경우에는 하위 뷰에서 변경 내역을 알 수 없음. 또한 하나의 값만을 추적, 바인딩하므로 그 외의 변경에 대해서는 서브스크라이빙이나 메시징을 지원하지 않음.
- @ObservedObject는 필수적인 셋업이나 global state 없이, @EnvironmentObject와 동일한 방식으로 Combine을 사용하여 모든 구독자 (또는 바운드 뷰)에 변경 내역을 보냄.
- 개발자의 관리가 필요하고, @State보다 더 강한 외부 참조가 필요할 때 선호.
- SwiftUI는 무효화된 서브뷰들을 체크하고 필요 시, 해당 뷰를 다시 빌드.

### Moving from environment to hierarchical state
### Hierarchical event chaos with state events
- SwiftUI는 사용자 흐름이 아니라 데이터 상태에 관한 것. 그러나 이벤트에 응답을 위해서는 액션과 콜백을 사용.
- 변화 트리거에 따라 상태나 뷰 계층이 재평가됨. 신중하지 않으면 불필요한 재구성이 일어날 수 있음.
- `@ObservedObject` 자동적인 디펜던시 트래킹을 제공한다. 서브 뷰의 계층이 디펜던시를 향하고 있는 동안. 참 값이 어디에 있는지 트래킹하는 것을 최소화 시킴.
- `@EnvironmentObject` 계층을 통해 직접 객체를 전달. 상위 뷰에 그 데이터가 전달되는 것을 허가함.

## Combine and other beasts



