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
