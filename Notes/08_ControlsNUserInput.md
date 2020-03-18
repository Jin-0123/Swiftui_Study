# Chapter 8: Controls & User Input

## A simple registration form

### A bit of refactoring
### Refactoring the logo image
* 재사용할 뷰를 파일분리하여 컴포넌트화 한다.

## Creating the registration view
* 컴포넌트화 한 뷰로 registerView를 만든다.

## Power to the user: the TextField
* @State로 text를 선언하고, $ 연산자로 바인딩
~~~
@State var name: String = ""

var body: some View {
	VStack {
		WelcomeMessageView()
		TextField("이름을 넣어주세요.", text: $name)
	}
	.background(WelcomeBackgroundImage())
}
~~~

### Styling the TextField
* `.textFieldStyle` modifier 이용하여 스타일 적용.
~~~
TextField("이름을 넣어주세요.", text: $name)
	// 텍스트 필드에 패딩 설정
	.padding(EdgeInsets(top: 8, leading: 16, bottom: 8, trailing: 16))
	// 배경색 흰색으로 설정
	.background(Color.white)
	// 오버레이뷰 생성
	.overlay(
	  RoundedRectangle(cornerRadius: 8)
		.stroke(lineWidth: 2)
		.foregroundColor(.blue)
	)
	// 텍스트필드에 그림자 효과 주기
	.shadow(color: Color.gray.opacity(0.4),
			radius: 3, x: 1, y: 2)
~~~

### Creating a custom modifier
* modifier를 여기저기 적용시킬 수 있도록 커스텀 modifier 만들기
~~~
struct BorderedViewModifier: ViewModifier {
    func body(content: Content) -> some View {
      content
        .padding(EdgeInsets(top: 8, leading: 16, bottom: 8, trailing: 16))
        .background(Color.white)
        .overlay(
          RoundedRectangle(cornerRadius: 8)
            .stroke(lineWidth: 2)
            .foregroundColor(.blue)
        )
        .shadow(color: Color.gray.opacity(0.4),
                radius: 3, x: 1, y: 2)
    }
}
~~~

* 일반적인 modifier처럼 콜하고 싶을 때는?
~~~
extension View {
    func bordered() -> some View {
        ModifiedContent(
            content: self,
            modifier: BorderedViewModifier()
        )
    }
}
~~~
> 이렇게 작성해주면, 아래 코드처럼 일반적인 modifier로 사용가능!

~~~
TextField("이름을 넣어주세요.", text: $name)
	.bordered()
~~~

### A peek at TextField’s initializer
~~~
public init<S>(
	_ title: S,
	text: Binding<String>,
	onEditingChanged: @escaping (Bool) -> Void = { _ in },
	onCommit: @escaping () -> Void = {}
) where S : StringProtocol
~~~
> * TextField localized 이니셜라이즈 파라메터
>	* `onEditingChanged`: 포커스를 얻어 수정할 때(파라메터 true가 넘어옴), 포커스를 잃었을 때 불림(파라메터 false가 넘어옴)
>	* `onCommit`: 사용자가 리턴 키를 누르는 것과 같은 커밋 액션이 있었을 때 불림, 자동적으로 다음 필드로 포커스를 옮길 때 유용함.

~~~
public init<S, T>(
	_ title: S,
	value: Binding<T>,
	formatter: Formatter,
	onEditingChanged: @escaping (Bool) -> Void = { _ in },
	onCommit: @escaping () -> Void = {}
) where S : StringProtocol
~~~
> * TextField non-localized 이니셜라이즈 파라메터
>	* `Formatter`: Foundation의 Formatter 클래스 상속, 스트링 이외의 number, date 타임등을 수정할 때 사용, 커스텀 포맷터 생성가능.
>	* `T`: 제네릭 파라미터로 타입을 결정할 수 있음

### Showing the keyboard
* KeyboardFollower.swift 파일에서 정의되어 있는 키보드 높이를 RegisterView.swift 파일에서 사용하여 View padding을 조정
~~~
@ObservedObject var keyboardHandler: KeyboardFollower

var body: some View {
	VStack {
		WelcomeMessageView()
		TextField("이름을 넣어주세요.", text: $name)
		.bordered()
	}
	// 키보드 높이에 따라 패딩 값 조정
	.padding(.bottom, keyboardHandler.keyboardHeight)
	.background(WelcomeBackgroundImage())
	.padding()
}
~~~

## Taps and buttons
* SwiftUI 버튼의 기본 컴포넌트는 텍스트, 이미지 뿐만 아니라 훨씬 복잡한 VStact, HStack도 포함할 수 있다.

~~~
init(
	action: @escaping () -> Void,
	@ViewBuilder label: () -> Label
)
~~~
* 첫번째 인자 이름이 탭핸들러가 아닌 action인 이유는 macOS나 watchOS의 경우에는 버튼의 액션이 탭이 아니기 때문.
* 마지막 인자 클로저로 뷰를 반환하게 되어있는데, SwiftUI의 패턴임.

### Submitting the form
~~~
// 앱 전체에서 사용할 것이라서 EnvironmentObject로 선언
@EnvironmentObject var userManager: UserManager
~~~

~~~
// 버튼 뷰에 액션 넘기고, 뷰 정의하기
...
Button(action: self.registerUser) {
	Text("OK")
}
...

// 버튼 액션 시 수행될 코드
func registerUser() {
	userManager.persistProfile()
}
~~~

### Styling the button
~~~
Button(action: tap) {
	HStack {
		// 버튼에 체크마크 이미지 추가
		Image(systemName: "checkmark")
			.resizable()
			.frame(width: 16, height: 16, alignment: .center)
		Text("확인")
			// 버튼 텍스트 모디파이어 적용
			.font(.body)
			.bold()
	}
}.bordered()
~~~

### Reacting to input: validation
* 버튼의 modifier를 통해 validation 체크를 할 수있다. 값에 따라 뷰는 다시 랜딩된다.
~~~
.disabled(!userManager.isUserNameValid())
~~~

### Reacting to input: counting characters
* 글자 수 Text 만들기
~~~
HStack {
	// 오른쪽으로 뷰를 정렬
	Spacer()
	Text("\(userManager.profile.name.count)")
	.font(.caption)
	.foregroundColor(
		userManager.isUserNameValid() ? .green : .red
		)
		.padding(.trailing)
}
.padding(.bottom)
~~~

## Toggle Control
* Toggle의 이니셜라이저, 버튼의 이니셜라이저와 유사
~~~
public init(
	isOn: Binding<Bool>,
	@ViewBuilder label: () -> Label
)
~~~

~~~
HStack {
	Spacer()
	// toggle 값을 셋팅의 리멤버유저 값에 바인딩
	Toggle(isOn: $userManager.settings.rememberUser) {
		Text("Remember me")
			.font(.subheadline)
			.foregroundColor(.gray)
	}
}
~~~

## Other controls

### Slider
~~~
public init<V>(
	value: Binding<V>,
	in bounds: ClosedRange<V>,
	step: V.Stride = 1,
	onEditingChanged: @escaping (Bool) -> Void = { _ in }
) where V : BinaryFloatingPoint, V.Stride : BinaryFloatingPoint
~~~
> * value: 바인딩할 값
> * bounds: 범위
> * step: 얼마만큼 증가 시킬 건지
> * onEditingChanged: 값이 바뀌었을 때, 수행할 액션

### Stepper
* slider와 유사 그러나 두 버튼이 있어서 지정된 인터벌만큼의 증감만 가능
~~~
public init<S, V>(
	_ title: S,
	value: Binding<V>,
	in bounds: ClosedRange<V>,
	step: V.Stride = 1,
	onEditingChanged: @escaping (Bool) -> Void = { _ in }
) where S : StringProtocol, V : Strideable
~~~
> * value: 바인딩할 값
> * bounds: 범위
> * step: 얼마만큼 증가 시킬 건지
> * onEditingChanged: 값이 바뀌었을 때, 수행할 액션

### SecureField
* Textfield와 동일, 민감 정보 다룰 때 사용.
~~~
public init<S>(
	_ title: S,
	text: Binding<String>,
	onCommit: @escaping () -> Void = {}
) where S : StringProtocol
~~~
> * title: 플레이스홀더
> * text: 바인딩할 텍스트
> * onCommit: 커밋될 때 액션
