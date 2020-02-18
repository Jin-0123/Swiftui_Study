# Chapter 6: Intro to controls: Text & Image

## Getting started

### Changing the root view
* swiftUIView 파일로 WelcomeView 생성 후, SceneDelegate에 RootView 지정

### Welcome, View!
* WelcomeView body 프러퍼티에 Text 컨포넌트를 가진 struct
* 프리뷰 프로바이더의 이름은 WelcomeView_previews

## Text
* ex) UILabel

### Modifiers
* Text 인스턴스의 사이즈, 폰트 등을 변경하려면 modifiers 사용.
~~~
Text("Welcome to Kuchi")
	.font(.system(size: 60))
	.bold()
	.foregroundColor(.red)
	.lineLimit(2)
	.multilineTextAlignment(.leading)
~~~

* 프리뷰에서 Text를 command 클릭하여 Text의 설정을 변경할 수도 있음.
* View 프로토콜은 많은 modifiers 구현되어져 있음.
* Xcode의 Modifiers Library 드래그로 끌어와 사용가능. ( + > 2번째 탭 )

### Are modifiers efficient?
 * modifier 포함한 새 뷰를 포함하고 있는데, 낭비처럼 보일 수 있으나 SwiftUI에서는 효율적인 데이터 구조로 스택을 플랫하게 가지고 있음.
 * 많은 modifier 쓰는 것에 대해 효율적인지 고민할 필요없다.
 
 ### Order of modifiers
 * modifier에는 순서가 있음.
 ~~~
 // 패딩이 들어간 후에 배경색을 입히자
 Text("Welcome To Kuchi")
            .padding()
            .background(Color.red)
			
// 배경색을 넣은 후에 패딩을 먹이자		
 Text("Welcome To Kuchi")
            .background(Color.red)
            .padding()
			
// 두번째 배경색을 넣으면 더 확실히 알 수 있다.
// 배경색 넣고, 패딩을 넣고 그 다음에 배경색을 또 입힌다. 텍스트는 레드로 패딩 부분은 노랑이로.
 Text("Welcome To Kuchi")
            .background(Color.red)
            .padding()
			.background(Color.yellow)
 ~~~

## Image

### Changing the image size
* resizable modifier를 적용하지 않으면 원래 사이즈로 노출.
* frame은 이미지 자체의 사이즈가 바뀌는 것이 아니라 활성화된 뷰(modifer가 적용된)의 사이즈를 바꾸는 것이기 때문.

~~~
Image(systemName: "table")
            .resizable()
            .frame(width: 60, height: 60)
            .cornerRadius(60 / 2)
            .border(Color.gray, width: 1)
            .background(Color(white: 0.9))
            .clipShape(Circle())
            .foregroundColor(.red)
~~~

## Brief overview of stack views
* HStack 안에 Image 와 Text 넣기

## More on Image
~~~
Image("welcome-background", bundle: nil)
	// 2. 사이즈 변경 가능하도록 
	.resizable()
	// 3. 오리지널 비율로 부모뷰에 가득차게
	.scaledToFit()
	// 4. 1:1 비율로 부모뷰에 가득차게
	.aspectRatio(1 / 1, contentMode: .fill)
	// 5. SafeArea 무시
	.edgesIgnoringSafeArea(.all)
	// 6. 채도
	.saturation(0.5)
	// 7. 블러
	.blur(radius: 5)
	// 8. 투명도
	.opacity(0.08)
~~~
 
## Splitting Text
~~~
VStack {
	Text("Welcome to")
		.font(.headline)
		.bold()
	Text("Kuchi")
		.font(.largeTitle)
		.bold()
	}
	// Text가 같은 modifier를 가지면 이렇게도 할 수 있음.
	.foregroundColor(.red)
	.lineLimit(2)
	.multilineTextAlignment(.leading)
	.padding(.horizontal)
~~~
* font, bold의 경우에는 Text modifier이기 때문에 한꺼번에 적용못하고 Text에만 적용가능.

## Accessibility with fonts
* 미리 정해진 폰트 __title__, __headline__, __body__ 등이 있음. 
* 절대적인 사이즈보다는 사이즈클래스를 사용하는 것을 추천.
