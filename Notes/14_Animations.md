# Chapter 14: Animations

## Animating state changes

## Adding animation

* 버튼 180도로 회전하는 애니메이션 추가하기
~~~
Image(systemName: "chevron.up.square") 
	.rotationEffect(.degrees(showDetails ? 0 : 180))
	.animation(.default)
~~~
* 각도가 0-360도로 제한되지 않아서 -180도, 540도 등으로도 지정가능. 

## Animation types

### Animating the Details view

* `showDetails`값에 따라서 Details View 위치 조정하는 애니메이션 추가
~~~
FlightDetails(flight: flight)
	.offset(x: showDetails ? 0 : -UIScreen.main.bounds.width)
	.animation(.default)
~~~

* `dafault` 애니메이션은 가장 간단한 애니메이션 타입. 선형의 변화를 제공한다. 원래의 상태에서 마지막 상태까지 상수의 비율로 변화함. 

## Eased animations

* 애니메이션은 실제 움직임의 가속과 감속을 반영한다. 
~~~
// 급히 빨라졌다가 느려짐
.animation(.easeOut)

// 느렸다가 빨라짐
.animation(.easeIn)

// 시작 지점은 가속 끝 지점은 감속
.animation(.easeInOut)
~~~

* 커브 모양을 변경하고 싶다면 `timingCurve(_:_:_:_)` 메서드 사용.

## Spring animations

* 끝 지점에서 바운스 효과
~~~
.animation(.interpolatingSpring(mass: 1.0, 
								stiffness: 100.0,
                                damping: 10,
                                initialVelocity: 0))
~~~

### Why a spring makes a useful animation

* Mass: 무게의 질량. 무거운 무게는 더 길게 바운스된다. 무게가 작을 때보다 중력의 힘을 더 받으니까.
* Spring Resistance: 스프링의 강도. 스트레치 혹은 압박에 얼마나 저항할지
* Damping: 마찰 정도가 어느정도 인가
* Inital Velocity: 스프링을 당기는 것을 시작, 가도록 냅둔다. 만약 무게가 움직임이 없다면 속도는 시스템의 영향을 받음.

### Creating spring animations

* `mass`: 바운스를 얼마나 길게
* `stiffness`: 초기의 움직임의 스피드를 컨트롤
* `damping`: 얼마나 빠르게 느려지거나 멈출건지
* `initialVelocity`: 추가적인 초기동작 주기
~~~
.animation(.interpolatingSpring(mass: 1, 
								stiffness: 100, 
								damping: 10, 
								initialVelocity: 0))
~~~
* mass를 늘리면 애니메이션이 더 오래 지속되고 양쪽 끝에서 더 튀어오름.
* mass가 작을수록 빨리 멈추고 양쪽 끝 지점의 바운스가 더 적다.
* 스프링 강도가 증가하면 끝 지점을 더 멀리 이동한다. 그러나 애니메이션 길이에는 영향을 미치지 않음.
* 마찰을 증가시키면 애니메이션이 더 스무스하고 빨리 끝남.
* initial velocity를 증가시키면 더 바운스 된다. 음수로 지정하면 지연이 발생.

~~~
 .animation(.spring(response: 0.55, 
 					dampingFraction: 0.45, 
					blendDuration: 0))
~~~
* `dampingFraction`: 얼마나 탄성이 있는가? 값이 0이면 절대 멈추지 않음. 0-1 사이의 값.
* `response`: `dampingFraction`이 0 일 때, 1번 진동을 완성하는데 걸리는 시간을 정의한다.

### Removing and combining animations

* 애니메이션 combine, remove
~~~
Image(systemName: "chevron.up.square")
	.scaleEffect(showDetails ? 2 : 1) 
	// 애니메이션 효과를 없애고 싶다면 nil
	//.animation(nil) 
	.animation(.spring()) 
	.rotationEffect(.degrees(showDetails ? 0 : 180)) 
	.animation(.easeInOut)
~~~

### Animating from state changes

* state가 변경되는 지점에 한 번에 애니메이션을 적용할 수 있음.
~~~
Button(action: { 
	withAnimation(.default) {
		self.showDetails.toggle() 
	}
}) {
	HStack {
		Text(showDetails ? "Hide Details" : "Show Details")
		Spacer()
		Image(systemName: "chevron.up.square")
			.scaleEffect(showDetails ? 2 : 1)
			.rotationEffect(.degrees(showDetails ? 0 : 180))
	}
}
~~~

## Adjusting animations

### Delay

* `.delay()`: 애니메이션 시작 전 딜레이
~~~
.animation(Animation.spring().delay(1))
~~~

### Speed

* `.speed()`: 애니메이션 속도 변경하기
~~~
.animation(Animation.spring().speed(2))
~~~

### Repeating animations

* `.repeatCount(repeatCount:,autoreverses:)`: 몇 번 반복할건지.
* `repeatForever(autoreverses:)`
~~~
.animation(Animation.spring().repeatCount(2, autoreverses: false))
~~~

## Extracting animations from the view

* 애니메이션 타입으로 변수 지정해서 사용가능
~~~
var flightDetailAnimation : Animation {
	Animation.easeInOut 
}

/// 사용하는 쪽에서 ...
withAnimation(self.flightDetailAnimation) { ... }
~~~

## Animating view transitions

* Transitions은 뷰를 보이고 숨기는 것을 위한 애니메이션. 예를 들면 페이드인, 페이드 아웃.
* `withAnimation()` 을 사용해야 함.
~~~
Button(action: {
	withAnimation {
		self.showDetails.toggle() 
	}
}) {
	HStack {
		Text(showDetails ? "Hide Details" : "Show Details")
		Spacer()
		Image(systemName: "chevron.up.square")
			.scaleEffect(showDetails ? 2 : 1)
			.rotationEffect(.degrees(showDetails ? 0 : 180))
			.animation(flightDetailAnimation)
	}
}
if showDetails {
	FlightDetails(flight: flight)
		.transition(.slide)
}
~~~

## View transition types

* 슬라이드는 리딩에서 테일로 움직임.
* 뷰를 추가하거나 제거할 때 `.opacity` 지정
* `.move(edge:)`: 추가하거나 제거할 때, 특정 엣지로 이동.
~~~
.transition(.move(edge: .bottom))
~~~

* `.scale()`: 한 지점에서 삽입되거나 제거될 때, 확장 혹은 축소. 초기 뷰 사이즈의 배수로 지정. `anchor` 시작위치.

## Extracting transitions from the view

* 트랜지션 변수로 선언해서 사용가능.
~~~
extension AnyTransition {
	static var flightDetailsTransition: AnyTransition {
		AnyTransition.slide 
	}
}

/// 사용하는 쪽에서 ...
if showDetails {
	FlightDetails(flight: flight)
		.transition(.flightDetailsTransition) 
}
~~~

## Async transitions

* 추가 제거 시 트렌지션 각각 다르게 적용.
~~~
extension AnyTransition {
	static var flightDetailsTransition: AnyTransition {
		let insertion = AnyTransition.move(edge: .trailing) .combined(with: .opacity)
		let removal = AnyTransition.scale(scale: 1.0) .combined(with: .opacity)
		return .asymmetric(insertion: insertion, removal: removal) 
	}
}
~~~
