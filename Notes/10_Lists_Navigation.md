# Chapter 10: Lists & Navigation

## Getting started

## Navigating through a SwiftUI app

* SwiftUI으로 TabView라는 것을 사용해서 플랫한 계층으로 구현할 것임.
* 계층 네비게이션을 구현할 때는 NavigationView를 사용해서 구현.

## Creating navigation views

* 탭뷰 셋업 실습
~~~
TabView {
	// 탭 화면 정의	
	FlightBoard(boardName: "Arrivals")
		// 탭아이템 정의
		.tabItem({
			Image(systemName: "icloud.and.arrow.down").resizable()
			Text("Arrivals")
		})
	FlightBoard(boardName: "Departures")
		.tabItem({
			Image(systemName: "icloud.and.arrow.up").resizable()
			Text("Departures")
	})
}
~~~

## Using navigation views
* 네비게이션 뷰는 스택뷰를 통해 위치함.
* 네비게이션 뷰 셋업 실습

~~~
NavigationView {
	ZStack {
		Image(systemName: "airplane").resizable() .aspectRatio(contentMode: .fit)
			.frame(width: 250, height: 250, alignment: .center)
			.opacity(0.1).rotationEffect(.degrees(-90))
		VStack(alignment: .leading, spacing: 5) {
			// 스택 안에서 화면 이동할 화면과 텍스트 지정
			NavigationLink(destination: FlightBoard(boardName:"Arrivals")) {
				Text("Arrivals")
			}
			NavigationLink(destination: FlightBoard(boardName:"Departures")) {
				Text("Departures")
			}
			Spacer()
		}.font(.title).padding(20)
		Spacer()
	}.navigationBarTitle(Text("Mountain Airport"))
}
~~~

## Displaying a list of data

* 화면에 데이터 넘겨서 띄우는 실습

## Making your data more compatible with iteration
~~~
// id 파라미터는 해시어블 프로토콜을 구현한 타입
ForEach(flightData, id: \.id) { fl in
	///
}

// filghtData 클래스에서 Identifiable 프로토콜을 따르게 한다면 id 파라미터 생략하고 사용가능
ForEach(flightData) { fl in
	///
}
~~~

## Showing scrolling data

~~~
// 스크롤뷰 추가, 방향은 아래 배열 파람으로 지정할 수 있음
ScrollView([.horizontal, .vertical]) {
	ForEach(flightData) { fl in
		VStack {
			Text("\(fl.airline) \(fl.number)")
			Text("\(fl.flightStatus) at \(fl.currentTimeString)")
			Text("At gate \(fl.gate)")
		}
	}
}
~~~

## Creating lists

~~~
// 리스트로 만들기
List(flightData) { fl in
	Text("\(fl.airline) \(fl.number)")
}
~~~

~~~
// 네비게이션 뷰로 감싸기
NavigationView {
	List(flightData) { fl in
		Text("\(fl.airline) \(fl.number)")
	}
}.navigationBarTitle(boardName)
~~~

~~~
// FilghtRow 뷰 만들어서 데이터 더 예쁘게 보여주기
struct FlightRow: View {
    var flight: FlightInformation
    
    var body: some View {
        HStack {
            Text("\(self.flight.airline) \(self.flight.number)")
                .frame(width: 120, alignment: .leading)
            Text(self.flight.otherAirport).frame(alignment: .leading)
            Spacer()
            Text(self.flight.flightStatus).frame(alignment: .trailing)
        }
    }
}
~~~

## Adding navigation links

~~~
// 비행정보 상세화면 만들기
struct FlightBoardInformation: View {
    var flight: FlightInformation
    
    var body: some View {
        VStack(alignment: .leading) {
            HStack {
                Text("\(flight.airline) Flight \(flight.number)")
                    .font(.largeTitle)
                Spacer()
            }
            Text("\(flight.direction == .arrival ? "From: " : "To: ") \(flight.otherAirport)")
            Text(flight.flightStatus) .foregroundColor(Color(flight.timelineColor))
            Spacer()
        }.font(.headline).padding(10)
    }
}
~~~

~~~
NavigationView {
	List(flightData) { fl in
		// NavigationLink로 연결
		NavigationLink(destination: FlightBoardInformation(flight: fl)) {
			FlightRow(flight: fl)
		}
	}.navigationBarTitle(boardName)
}
~~~

## Adding items to the navigation bar

~~~
@State private var hideCancelled = false
var shownFlights: [FlightInformation] {
	hideCancelled ? flightData.filter { $0.status != .cancelled } : flightData
}
...

...
NavigationView {
	List(shownFlights) { fl in
		NavigationLink(destination: FlightBoardInformation(flight: fl)) {
			FlightRow(flight: fl)
		}
	}
	.navigationBarTitle(boardName)
	// 네비게이션 아이템 추가해보기
	.navigationBarItems(trailing:
		Toggle(isOn: $hideCancelled, label: {
		Text("취소된 비행 숨기기")
	}))
}
...
~~~

## Displaying a modal sheet

~~~
// 비행 상세정보 모달로 띄우기
struct FlightRow: View {
    @State private var isPresented = false
    var flight: FlightInformation
    
    var body: some View {
        Button(action: {
            self.isPresented.toggle() }) {
                HStack {
                    Text("\(self.flight.airline) \(self.flight.number)")
                        .frame(width: 120, alignment: .leading)
                    Text(self.flight.otherAirport).frame(alignment: .leading)
                    Spacer()
                    Text(self.flight.flightStatus).frame(alignment: .trailing)
                }
        }.sheet(isPresented: $isPresented, onDismiss: {
            print("Modal dismissed. State now: \(self.isPresented)")
        }) {
            FlightBoardInformation(flight: self.flight)
        }
    }
}
~~~

## Programmatically dismissing a modal

~~~
// Done 버튼 만들어서 모달 디스미스
@Binding var showModal: Bool
Button("Done", action: {
	self.showModal = false
})

// FlightRow.swift
FlightBoardInformation(flight: self.flight, showModal: self. $isPresented)
~~~

## Using alerts, action sheets and popovers

### Creating an alert

~~~
@State private var rebookAlert: Bool = false

// Rebook Flight 버튼 클릭 시 얼럿 노출하기
if flight.status == .cancelled {
	Button("Rebook Flight", action: {
		self.rebookAlert = true }
	).alert(isPresented: $rebookAlert) {
		Alert(title: Text("Contact Your Airline"),
			  message: Text("We cannot rebook this flight. Please contact the airline to reschedule this flight."))
} }
~~~

## Adding an action sheet

* 액션 시트 띄우기
~~~
struct CheckInInfo : Identifiable {
	let id = UUID()
	let airline: String
	let flight: String
}
@State private var checkInFlight: CheckInInfo?

if flight.direction == .departure && (flight.status == .ontime || flight.status == .delayed) {
	Button("Check In for Flight", action: {
	self.checkInFlight = CheckInInfo(airline: self.flight.airline, flight: self.flight.number)
	}).actionSheet(item: $checkInFlight) { flight in
		ActionSheet(title: Text("Check In"), 
					message: Text("Check in for \(flight.airline) Flight \(flight.flight)"),
					buttons: [
						.cancel(Text("Not Now")),
						.destructive(
							Text("Reschedule"), action: { print("Reschedule flight.") }),
						.default(
							Text("Check In"), action: { print("Check-in for \(flight.airline) \(flight.flight).") })
							]
					)
	} }
~~~

## Showing a popover

* 아이패드나 맥과 같은 큰 화면에서 유용한 팝오버띄우기
~~~
@State private var showFlightHistory = false

Button("On-Time History") {
	self.showFlightHistory.toggle() 
}.popover(isPresented: $showFlightHistory, arrowEdge: .top) { 
	FlightTimeHistory(flight: self.flight)
}
~~~
