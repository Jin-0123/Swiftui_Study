# Chapter 11: Testing and Debugging

## Different types of tests

- 기본 테스트, 통합 테스트, 유저 인터페이스 테스트
- 모든 테스트의 기초는 유닛 테스트, 주어진 인풋에 예상한 아웃풋이 도출되었는가 확인. 실행이 빠름.
- 통합 테스트는 앱의 다른 파트와 함께 잘 동작하는지 테스트, 유닛 테스트보다 조금 더 복잡하고 오랜 시간이 걸린다. 
- 가장 복잡한 테스트는 UI 테스트, 앱과의 인터렉션을 시뮬레이팅. 

## Debugging SwiftUI apps
## Setting breakpoints
## Exploring breakpoint control
## Adding UI tests

- 테스트 클래스의 각 메서드가 실행되기 전에 불리는 setUp()
- 모든 메서드가 끝난 이 후에 불리는 tearDown()

~~~
continueAfterFailure = false
~~~
> 테스트 실패하면 멈출 것인지? 잘못된 정보로 계속 테스트를 진행하는 것보다는 멈추는 것이 더 나을지도

- test라는 이름으로 시작해야함.

## Creating a UI Test
## Accessing UI elements
## Reading the user interface
~~~
.accessibility(identifier: "display")
~~~
> identifier를 통해서 앱에서 엘리먼트를 찾는다. 

## Adding more complex tests
~~~
func testAddingTwoDigits() {
        let app = XCUIApplication()
        app.launch()
        let threeButton = app.buttons["3"]
        threeButton.tap()
        let addButton = app.buttons["+"]
        addButton.tap()
        let fiveButton = app.buttons["5"]
        fiveButton.tap()
        let equalButton = app.buttons["="]
        equalButton.tap()
        let display = app.staticTexts["display"]
        let displayText = display.label
        //XCTAssert(displayText == "8")
        XCTAssert(displayText == "8.0")
}
~~~

## Simulating user interaction
- 제스처 추가 > 스와이프 액션 이후에 memoryDisplay.exists 값 테스트
~~~
func testSwipeToClearMemory() {
        let app = XCUIApplication()
        app.launch()
        let threeButton = app.buttons["3"]
        threeButton.tap()
        let fiveButton = app.buttons["5"]
        fiveButton.tap()
        let memoryButton = app.buttons["M+"]
        memoryButton.tap()
        let memoryDisplay = app.staticTexts["memoryDisplay"]
        // 1
        XCTAssert(memoryDisplay.exists)
        // 2
        memoryDisplay.swipeLeft()
        // 3
        XCTAssertFalse(memoryDisplay.exists)
}
~~~

* .isHittable: 현재 위치에서 클릭, 탭 할 수 있는 엘리먼트인가? 스크린이 꺼져있다면 not hittable
* .typeText(): 텍스트 타이핑하는 것처럼 동작
* .press(forDuration:): 특정 시간만큼 싱글 터치 수행
* .press(forDuration:thenDragTo:): 드래그 액션 수행
* .waitForExistence(): 화면에 즉시 나타나지 않는 엘리먼트일 경우, 유용하게 사용되는 일시정지 메서드
* 참고 문서: https://developer.apple.com/documentation/xctest/xcuielemen

## Testing multiple platforms
* 테스트를 원하는 환경에서만 하게 하려면?
~~~
 #if !targetEnvironment(macCatalyst)
  // Test to exclude
#endif

 #if !os(watchOS)
  // Your XCTest code
#endif

~~~
