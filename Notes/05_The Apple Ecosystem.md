# Chapter 5: The Apple Ecosystem

## Getting started

## Creating a Swift package
파일이 많지 않을 때는 타겟 멤버십을 이용하지만 기능을 추가해야하는 경우에는 타겟 멤버십을 이용하는 것이 불편함.
이럴 때는 공유될 파일을 패키지로 구성하는 것이 좋다. 애플에 다른 플랫폼과도 더 쉽게 공유할 수 있도록 해준다.

### Customizing your Game package

### Versioning your Game package
버전 관리는 Package.swift 파일에서 아래 코드처럼 작성.

~~~
// Package.swift 

platforms: [.iOS(.v13), .macOS(.v10_15), .watchOS(.v6), .tvOS(.v13)],
~~~

### Linking your Game package library
Package.swift 파일에서 products 아규먼트 정의 후, Build Phases에서 라이브러리 추가.

~~~
// Package.swift 

products: [
  .library(
    name: "Game",
    targets: ["Game"]),
],
~~~

### Importing your Game package module
`import Game`

### Creating a GameView package

## Designing for the strengths of each platform
### watchOS
알림을 빨리 볼 수 있어서 빨리 응답할 수도 있지만 무시할 수도 있음.
화면이 작아서 중요하고 적절한 정보를 보여줘야함.

### macOS/iPadOS
오랜 기간동안 사용하는 경향이 있고, 더 자세한 일을 수행할 때 사용한다. 큰 스크린과 풀 키보드르 가지고 있어서 단축키로 시간을 절약함.
많은 유저들이 멀티 윈도우를 오픈해 놓고 사용한다.

### tvOS
큰 스크린을 이용하지만 유저와의 거리가 떨어져 있음. 한 명 이상의 뷰어가 있을 수 있다.
이미지나 비디오를 이용에 있어서는 베스트지만 글자를 읽고 쓰는 건 좋지 않음.
위치기능이나 로컬기반의 알림은 없어도 된다.

iOS와는 다른 `TabView` 라는 것으로 구성.
가장 탑레벨 

## Improving the watchOS app
애플워치에서 슬라이더 값 변경할 때 디지털 크라운 이용.

~~~
Slider(value: $currentValue, in: 1.0...100.0)
  .digitalCrownRotation($currentValue, from: 1.0, through: 100.0)
~~~

## Extending the Mac Catalyst app
### iOS Settings == macOS Preferences

## Creating a MacOS BullsEye app

## Creating a tvOS BullsEye app
### Using the tvOS simulator


