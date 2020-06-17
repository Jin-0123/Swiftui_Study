# Chapter 13: Drawing & Custom Graphics

## Creating shapes

* 사각형그리기
* `.fill`는 `.frame` 호출 전에 불러줘야 함
~~~
Rectangle()
.fill(Color.blue)
.frame(width: 200, height: 200)
~~~

* boder 적용하기
~~~
border(_:width:)
~~~

## Using gradients

* 그라데이션 효과 주기
~~~
.fill(
	LinearGradient(
		gradient: .init(colors: [Color.green, Color.blue]), 
		startPoint: .init(x: 0, y: 1), 
		endPoint: .init(x: 1, y: 0)
	)
)
~~~

## Rotating shapes
* `.rotationEffect(.degrees(Double(i) * 60.0))`: 60도 배수로 기준으로 회전시키기
~~~
ZStack {
	ForEach(0..<3) { i in Rectangle()
		.fill(
			LinearGradient(
			gradient: .init(colors: [Color.green, Color.blue]), 
			startPoint: .init(x: 0, y: 1),
			endPoint: .init(x: 1, y: 0)
		)
	)
	.frame(width: 200, height: 200)
	.rotationEffect(.degrees(Double(i) * 60.0))
	} 
}
~~~

## Adding images

* 이미지 추가, 사이즈 변경, 회전 및 투명도 주기
~~~
Image(systemName: "airplane")
.resizable()
.rotationEffect(.degrees(-90))
.opacity(0.5)
~~~

## Scaling drawings in views

* `GeometryReader {}`: GeometryReader 컨테이너는 그 컨테이너 내부로부터 뷰의 사이즈와 모양을 가져온다. constants에 의존하지 않고 코드를 작성할 수 있도록 해준다. 뷰 사이즈를 기준으로 상대적인 크기 지정 가능
* 비행기 이미지를 뷰 크기의 0.7 정도의 크기로 사이즈 지정
~~~
Image(systemName: "airplane")
.resizable()
.rotationEffect(.degrees(-90))
.opacity(0.5)
.scaleEffect(0.7)
~~~

## Other shapes
* Circle
* Ellipse 타원
* Rounded Rectangle
* Capsule

## Drawing lines with paths

* 도로 모양 그리기
* `.move` 시작 지점 지정하고 `.addLine`으로 선 그리기
~~~
Path { path in
	path.move(to: CGPoint(x: 120, y: 20))
	path.addLine(to: .init(x: 180, y: 180))
	path.addLine(to: .init(x: 20, y: 180))
	path.addLine(to: .init(x: 80, y: 20))
}
~~~

* `GeometryReader` 로 상대적인 크기로 지정하기
* 색 채우기
~~~
GeometryReader { geometry in
	Path { path in
	let size = min(geometry.size.width, geometry.size.height) 
	let nearLine = size * 0.1
	let farLine = size * 0.9
	
	path.move(to: CGPoint(x: size/2 + nearLine, y: nearLine)) 
	path.addLine(to: .init(x: farLine, y: farLine)) 
	path.addLine(to: .init(x: nearLine, y: farLine)) 
	path.addLine(to: .init(x: size/2 - nearLine, y: nearLine))
    }
	.fill(Color.init(red: 0.4, green: 0.4, blue: 0.4))
}
~~~

## Drawing dashed lines

* 대시 선 그려보기
~~~
Path { path in
	let size = min(geometry.size.width, geometry.size.height)
	let nearLine = size * 0.1
	let farLine = size * 0.9
	let middle = size / 2
	path.move(to: .init(x: middle, y: farLine))
	path.addLine(to: .init(x: middle, y: nearLine)) 
}
.stroke(Color.white,
	style: .init(
		lineWidth: 3.0,
		dash: [geometry.size.height / 20, geometry.size.height / 30],
		dashPhase: 0
		)
	)
~~~

* `.offset`으로 차 이미지 원하는 위치에 추가하기
~~~
Image(systemName: "car.fill")
	.resizable()
	.foregroundColor(Color.blue)
	.scaleEffect(0.20)
	.offset(x: -geometry.size.width / 7.25)
~~~

## Drawing arcs and curves

## Drawing quadratic curve 

* `.move` 에서 지정한 시작 지점과 `.addQuadCurve`에서 to까지의 지점까지 곡선으로 연결하는데, control 지점을 중간 지점으로 커브를 생성한다.
* 네잎클로버 모양으로 곡선이 그려짐
~~~
path.move(to: .init(x: mid, y: nearLine)) 
path.addQuadCurve(to: .init(x: farLine, y: mid), control: .init(x: size, y: 0)) 
path.addQuadCurve(to: .init(x: mid, y: farLine),control: .init(x: size, y: size)) 
path.addQuadCurve(to: .init(x: nearLine, y: mid), control: .init(x: 0, y: size)) 
path.addQuadCurve(to: .init(x: mid, y: nearLine), control: .init(x: 0, y: 0))
~~~

* 원형으로 그라데이션 적용하기
~~~
.fill( 
RadialGradient(
	gradient: .init(colors: [Color.white, Color.yellow]), 
	center: .init(x: 0.5, y: 0.5),
	startRadius: geometry.size.width * 0.05,
	endRadius: geometry.size.width * 0.6)
)
~~~

* 곡선 그리기
* 시작할 중심 지점(compass 중심)을 지정하고, 반지름, 시작 각도, 끝 각도 지정 후 시계방향으로 그릴 건지 반시계방향으로 그릴건지 
~~~
Path { path in
	let size = min(geometry.size.width, geometry.size.height)
	let nearLine = size * 0.1
	let farLine = size * 0.9

	path.addArc(
		center: .init(x: nearLine, y: nearLine), 
		radius: size / 2,
		startAngle: .degrees(90), 
		endAngle: .degrees(0), 
		clockwise: true)
	path.addArc(
		center: .init(x: farLine, y: nearLine), 
		radius: size / 2,
		startAngle: .degrees(180), 
		endAngle: .degrees(90), 
		clockwise: true)
	path.addArc(
		center: .init(x: farLine, y: farLine), 
		radius: size / 2,
		startAngle: .degrees(270), endAngle: .degrees(180), 
		clockwise: true)
	path.addArc(
		center: .init(x: nearLine, y: farLine), 
		radius: size / 2,
		startAngle: .degrees(0), endAngle: .degrees(270), 
		clockwise: true)
		path.closeSubpath() 
	}
	.stroke(Color.orange, lineWidth: 2)
~~~

##
