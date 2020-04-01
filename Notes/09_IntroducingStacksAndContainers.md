# Chapter 9: Introducing Stacks and Containers

## Layout and priorities
* 기존에는 부모는 제약조건을 추가함으로써 자식의 사이즈를 결정한다. 정적인 크기를 갖는게 아니라면.
* SwiftUI에서는 자식이 자신의 사이즈를 선택하고, 부모로부터 제안된 사이즈를 응답한다.

### Layout for views with a single child
* 모든 뷰는 부모 뷰의 중앙에 디폴트로 위치한다.
* SwiftUI에서 부모뷰와 자식뷰의 규칙
	1. 부모뷰는 배치 안에서 가능한 프레임을 결정한다.
	2. 부모뷰는 자식뷰에 사이즈를 제의한다.
	3. 부모로부터 받은 제안에 기초로 자식 뷰는 사이즈를 선택한다.
	4. 부모 뷰는 자식의 뷰를 포함해 자신의 사이즈를 정한다.
* 이 과정이 뷰 계층에 따라 반복된다.

* frame modifier를 통해 프레임을 변경할 수 있다.
~~~
Text("안녕 나는 텍스트를 길게 써보려고 해.")
	.background(Color.red)
	.frame(width: 300, height: 100, alignment: .center)
	.minimumScaleFactor(0.5)
	.background(Color.yellow)
~~~
> * 프레임은 고정된 사이즈는 갖는다.
> * 프레임 뷰는 텍스트 뷰에 사이즈를 제안한다.
> * 텍스트 뷰는 해당 사이즈 안에서 텍스트를 보여줄 방법을 찾는다. 가능하면 텍스트가 잘리지 않는 최솟값을 사용하면서.


~~~
.minimumScaleFactor(0.5)
~~~
> * 크기에 따라 텍스트가 짤리게 된다면, 위 코드를 사용해서 최소 스케일(0에서 1사이)을 정해줄 수 있다. 


* 컨포넌트는 항상 부모로부터 제안된 사이즈 내에서 컨텐츠를 맞추기를 시도한다.
* 만약 공간 부족으로 맞추지 못한다면, 컴포넌트 타입으로부터 의존하는 적절한 룰이 적용될 것이다. 
* 부모뷰가 자식뷰의 사이즈를 강제할 수 있는 방법은 없다.
*`.padding` modifier는 고정적인 사이즈를 갖지 않는다. 자식의 사이즈를 받아 각 엣지에 패딩을 추가한다.

## Stack views

### Layout for container views
* 한 뷰에 여러 개의 뷰가 포함되어 있을 때, 자식의 사이즈를 결정하는 룰
	1. 컨데이너 뷰는 가용할 수 있는 프레임을 결정한다. 부모로부터 제안된 사이즈 내에서.
	2. 컨테이너뷰는 자식뷰를 고른다. 가장 제한적인 제약조건을 가졌거나, 동등한 제약조건을 가졌거나 가장 작은 사이즈인.. 
	3. 컨테이너뷰는 자식뷰에게 사이즈를 제안한다. 제안된 사이즈는 자식뷰의 수에 따라 동등하게 나누어진 사이즈이다.
	4. 자식뷰는 제안된 사이즈에 따라 사이즈를 고른다.
	5. 컨테이너뷰는 모든 뷰가 잘 동작될 때까지 2번에서 고른 자식뷰의 사이즈를 가용범위로부터 줄인다.
	
~~~
HStack {
	Text("A great and warm welcome to Kuchi")
		.background(Color.red)
	Text("A great and warm welcome to Kuchi")
		.background(Color.red)
}
.background(Color.yellow)
~~~
> * 스택은 부모로부터 제안된 사이즈를 받고, 두 개를 동등한 파트로 나눈다.
> * 스택은 자식뷰 중에 첫번째 사이즈를 제안한다. 그들은 동등하고, 제안된 첫번째 뷰를 왼쪽으로 보낸다.
> * 텍스트뷰는 찾는다 제안된 사이즈보다 더 작게 필요한지, 때문에 텍스트가 두 줄로 보일 수 있고, 
> * 스택은 첫번째 텍스트 사이즈를 줄이고 최종적인 사이즈를 두번째 텍스트에 제안한다.
> * 텍스트는 제안된 사이즈를 모두 사용하도록 결정했다.

### Layout priority
* 뷰의 조정 우선순위를 변경시킬 수 있는 방법
	1. Alter the view behavior via a modifier
	2. Alter the view’s layout priority

#### modifier
* Image `resizable`
* Text `lineLimit`

#### Priority
~~~
HStack {
	Text("A great and warm welcome to Kuchi")
    	.layoutPriority(-1)
	    .background(Color.red)
	
	Text("A great and warm welcome to Kuchi")
    	.layoutPriority(1)
	    .background(Color.red)
	
	Text("A great and warm welcome to Kuchi")
	    .background(Color.red)
~~~
* `.layoutPriority()` : 뷰의 순서를 바꿀 뿐 아니라 제안된 사이즈도 바꾼다.
* 가장 낮은 우선순위에 있는 뷰를 줄이고, 높은 우선순위에 있는 뷰의 사이즈를 제안한다.

