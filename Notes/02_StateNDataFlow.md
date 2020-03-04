# Chapter 7: State & Data Flow

With SwiftUI views are a function of state, not a sequence of events, with the app as a constant feedback loop.
- **User** > (interact) > **Action** > (mutate) > **State** > (update) > **View** > (render) > **User**

## What is state?
- what state means: state의 의미가 뭔지
- how SwiftUI’s specific definition applies: SwiftUI의 특정한 정의를 어떻게 적용하는지

- 프러퍼티 랩퍼는 값이 변화하는 것을 자동으로 앱에 반영한다. boilerplate state management 작성하거나 바인딩코드 없음에도.

## Starting with @State
- @State: 로컬 뷰 내에서 자동적으로 persistent 값을 모니터하고 읽을 수 있도록 한다.
- 모든 @State는 프레임 워크가 관리하는 주어진 값을 항상 참으로 유지하는 source. 특정 스코프 내의 심플 타입들과 찰떡임. (e.g. Bool, Int, or String)

~~~
@State var name: String = "
TextField("Enter your name", text: $name).multilineTextAlignment(.center)
~~~
> @State 선언한 값은 $프리픽스르 붙여서 접근 가능, 속성은 변화되는 값이 바인딩될 수 있는 랩퍼를 생성하는 것이 중요.
> name 값이 업데이트 되면, 뷰는 무효화하고 재빌드.

~~~
var name: State<String>
...
name.binding = "Ray
~~~
> @State 나 $ 대신 이렇게도 작성가능, 바인딩은 텍스트 필드와 양방향 관계다.
> 1. 텍스트 필드의 값을 수정할 때, state 랩퍼의 참조를 리테인. 
> 2. 추가적인 코드 필요없이 @State 오브젝트의 값이 바뀐다.

- read-only 와 같은 단방향 참조일 경우 @State 불필요. 신중하게 사용해야함.
- @State는 뷰의 라이프 사이클 동안에만 메모리에 있고, 더 이상 필요하지 않으면 메모리에서 해제.
- @State를 효율적으로 사용하려면, 가능하다면 제한해야함. the derived binding or value를 대신 사용할 수 있다면.

## @State: Learning to share


###
