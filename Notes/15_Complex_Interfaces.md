# Chapter 15: Complex Interfaces

## Integrating with other frameworks

~~~
import SwiftUI

struct FlightTimeline: UIViewControllerRepresentable {
    var flights: [FlightInformation]
    
    func makeUIViewController(context: Context) -> UITableViewController {
        UITableViewController()
    }
    
    func updateUIViewController(_ viewController: UITableViewController, context: Context) {
        let timelineTableViewCellNib = UINib(nibName: "TimelineTableViewCell", bundle: Bundle.main)
        viewController.tableView.register(timelineTableViewCellNib, forCellReuseIdentifier: "TimelineTableViewCell")
    }
    
    func updateUIViewController(_ viewController: UITableViewController, context: Context) {
        let timelineTableViewCellNib = UINib(nibName: "TimelineTableViewCell", bundle: Bundle.main)
        viewController.tableView.register(timelineTableViewCellNib, forCellReuseIdentifier: "TimelineTableViewCell")
    }
}
~~~
> FlightTimeline.swift

* `makeUIViewController(context:)` : 뷰가 보일 준비가 되면 불린다. 
* `updateUIViewController(_:context:)` : 뷰 컨이 보이고 있고, 컨피그를 업데이트하기 원할 때 부른다.

## Connecting delegates, data sources and more

~~~
class Coordinator: NSObject {
    var flightData: [FlightInformation]
    
    init(flights: [FlightInformation]) {
        self.flightData = flights
    }
}
~~~

* NSObject에서 상속받는 Coordinator 오브젝트 생성해야한다.
* SwiftUI와 외부 라이브러리의 브릿지 역할

~~~
struct FlightTimeline: UIViewControllerRepresentable {
	...
	
	func makeCoordinator() -> Coordinator {
        Coordinator(flights: flights)
    }
}
~~~

* `makeUIViewController(context:)` 불리기 전에 `makeCoordinator()` 불림.
* SwiftUI가 아닌 컴포넌트를 생성하고 설정하는데 이용.

* UITableViewDelegate와 UITableViewDataSource 구현
~~~
extension Coordinator: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        flightData.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let timeFormatter = DateFormatter()
        timeFormatter.timeStyle = .short
        timeFormatter.dateStyle = .none
        let flight = self.flightData[indexPath.row]
        let scheduledString = timeFormatter.string(from: flight.scheduledTime)
        let currentString = timeFormatter.string(from: flight.currentTime ?? flight.scheduledTime)
        let cell = tableView.dequeueReusableCell( withIdentifier: "TimelineTableViewCell", for: indexPath) as! TimelineTableViewCell
        var flightInfo = "\(flight.airline) \(flight.number) "
        flightInfo = flightInfo + "\(flight.direction == .departure ? "to" : "from")"
        flightInfo = flightInfo + " \(flight.otherAirport)"
        flightInfo = flightInfo + " - \(flight.flightStatus)"
        cell.descriptionLabel.text = flightInfo
        
        if flight.status == .cancelled {
            cell.titleLabel.text = "Cancelled"
        } else if flight.timeDifference != 0 {
            if flight.status == .cancelled {
                cell.titleLabel.text = "Cancelled"
            } else if flight.timeDifference != 0 {
                var title = "\(scheduledString)"
                title = title + " Now: \(currentString)"
                cell.titleLabel.text = title
            } else {
                cell.titleLabel.text = "On Time for \(scheduledString)"
            }
        } else {
            cell.titleLabel.text = "On Time for \(scheduledString)"
        }
        cell.titleLabel.textColor = UIColor.black
        cell.bubbleColor = flight.timelineColor
        return cell
    }
}
~~~

~~~
struct FlightTimeline: UIViewControllerRepresentable {
	// ...
	
	func updateUIViewController(_ viewController: UITableViewController, context: Context) {
        // ...
		// 아래 라인 추가 데이터 소스 연결
        viewController.tableView.dataSource = context.coordinator
    }
	// ...
}

~~~

## Building reusable views

* VStack으로 간단한 그리드뷰 시도
~~~
struct GridView: View {
	var items: [Int]
    
	var body: some View {
        ScrollView {
			VStack {
				ForEach(0..<items.count) { index in
					Text("\(self.items[index])") 
				}
			} 
		}
	}
}

struct GridView_Previews: PreviewProvider {
    static var previews: some View {
        GridView()
    }
}
~~~

~~~
GridView(items: [11, 3, 7, 17, 5, 2])
~~~

## Displaying a grid

~~~
struct GridView: View {
	var items: [Int]
	
	var columns: Int

	var numberRows: Int {
		(items.count - 1) / columns 
	}

	func elementFor(row: Int, column: Int) -> Int? { 
		let index = row * self.columns + column
		return index < items.count ? index : nil 
	}
    
	var body: some View {
		ScrollView {
			// 1
			ForEach(0...numberRows) { row in 
				HStack {
					// 2
					ForEach(0..<self.columns) { column in 
						// 3
						if elementFor(row: row, column: column) != nil {
  							Text("\(items[elementFor(row: row, column: column)!])")
						} else {
							Text("") 
						}	
					}
				} 
			}
		}
    }
}

struct GridView_Previews: PreviewProvider {
    static var previews: some View {
        GridView()
    }
}
~~~

~~~
GridView(columns: 3, items: [11, 3, 7, 17, 5, 2, 1])
~~~

## Using a ViewBuilder

~~~
// 1. 선언부 변경
struct GridView<Content>: View where Content: View {
	var items: [Int]
	
	// 2. content 파라미터 추가 
	let content: (Int) -> Content
	
	var columns: Int

	var numberRows: Int {
		(items.count - 1) / columns 
	}
	
	// 3. 생성자 추가
	init(columns: Int, items: [Int], @ViewBuilder content: @escaping (Int) -> Content) {
		self.columns = columns self.items = items self.content = content
	}

	func elementFor(row: Int, column: Int) -> Int? { 
		let index = row * self.columns + column
		return index < items.count ? index : nil 
	}
    
	var body: some View {
		ScrollView {
			// 4. content 파라미터 사용하도록 ForEach 변경
			ForEach(0...self.numberRows, id: \.self) { row in 
				HStack {
					ForEach(0..<self.columns, id: \.self) { column in 
						Group {
							if elementFor(row: row, column: column) != nil {
								self.content(items[elementFor(row: row, column: column)!])
							} else {
								Spacer()
							}	
						}
						
					}
				} 
			}
		}
    }
}

struct GridView_Previews: PreviewProvider {
    static var previews: some View {
        GridView()
    }
}
~~~

~~~
GridView(columns: 3, items: [11, 3, 7, 17, 5, 2, 1]) { item in
	Text("\(item)")
}
~~~

## Spacing the grid

~~~
GeometryReader { geometry in
	ScrollView {
		// Omitted code
	} 
}
~~~
> GeometryReader로 감싸서 사이즈 얻기

~~~
self.content(self.items[self.elementFor(row: row, column: column)!])
	.frame(width: geometry.size.width / CGFloat(self.columns), height: geometry.size.width / CGFloat(self.columns))
~~~

## Making the grid generic

* 제네릭 사용하도록 선언부, 이닛메서드 변경
~~~
struct GridView<Content, T>: View where Content: View {

var items: [T]

let content: (T) -> Content

init(columns: Int, items: [T], @ViewBuilder content: @escaping (T) -> Content) {
/// ...

}
~~~

## Using the grid
* 만든 그리드 뷰를 사용해보기!
