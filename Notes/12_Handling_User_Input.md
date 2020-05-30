# Chapter 12: Handling User Input

## Adding the learn feature
* SwiftUI LearnView 생성
* Learn 탭 추가

## Creating a flash card

* FlashCard 구조체 생성
~~~
import Learning

struct FlashCard {
    var card: WordCard
    let id = UUID()
    var isActive = true
}

extension FlashCard: Identifiable {
    
}

extension FlashCard: Equatable {
    static func == (lhs: FlashCard, rhs: FlashCard) -> Bool {
        return lhs.card.word.original == rhs.card.word.original
            && lhs.card.word.translation == rhs.card.word.translation
        }
}
~~~

## Building a flash deck

* FlashDeck model 생성
~~~
import Learning
import Combine

final internal class FlashDeck {
    @Published var cards: [FlashCard]
    
    init(from words: [WordCard]) {
        self.cards = words.map {
            FlashCard(card: $0)
        }
    }
}

extension FlashDeck: ObservableObject {

}
~~~

## Final state 

* LearningStore top-level state 모델 생성

~~~
import Combine
import Languages
import Learning

final class LearningStore {
    // 1. @Published 어트리뷰드 사용을 위해 컴바인 사용
    @Published var deck: FlashDeck
    // 2. 현재 카드
    @Published var card: FlashCard
    // 3. 현재 스코어
    @Published var score = 0
    
    // 4. deck 빌더
    init() {
        let deck = FlashDeck(from: DeckBuilder.learning.build())
        self.deck = deck
        self.card = FlashCard(
            card: WordCard(
                from: TranslatedWord(
                    from: "",
                    withPronunciation: "",
                    andTranslation: ""
                )
            )
        )
        if let nextCard = self.getNextCard() {
            self.card = nextCard
        }
    }
    
    // 5. 다음 카드 가져오기
    func getNextCard() -> FlashCard? {
        if let nextCard = self.getLastCard() {
            self.card = nextCard
            self.deck.cards.removeLast()
        }
        return self.card
    }

    // 6. 마지막 카드 가져오기
    func getLastCard() -> FlashCard? {
        if let lastCard = deck.cards.last {
            self.card = lastCard
            return self.card
        } else {
            return nil
        }
    }
}

extension LearningStore: ObservableObject {

}
~~~

## And finally... building the UI
* CardView 생성하기
~~~
import SwiftUI

struct CardView: View {
    var body: some View {
        ZStack {
            Rectangle()
                .fill(Color.red)
                .frame(width: 320, height: 210) .cornerRadius(12)
            VStack {
                Spacer()
                Text("Apple")
                    .font(.largeTitle)
                    .foregroundColor(.white)
                Text("Omena")
                    .font(.caption)
                    .foregroundColor(.white)
                Spacer()
            }
        }
        .shadow(radius: 8) .frame(width: 320, height: 210) .animation(.spring())
    }
}

struct CardView_Previews: PreviewProvider {
    static var previews: some View {
        CardView()
    }
}
~~~

* DeckView 생성
~~~
import SwiftUI

struct DeckView: View {
    var body: some View {
        ZStack {
            CardView()
            CardView()
        }
    }
}

struct DeckView_Previews: PreviewProvider {
    static var previews: some View {
        DeckView()
    }
}
~~~

* LearnView 수정
~~~
import SwiftUI

struct LearnView: View {
    var body: some View {
        VStack {
            Spacer()
            Text("Swipe left if you remembered" + "\nSwipe right if you didn’t") 
				.font(.headline)
            DeckView()
            Spacer()
            Text("Remembered 0/0")
        }
    }
}

struct LearnView_Previews: PreviewProvider {
    static var previews: some View {
        LearnView()
    }
}
~~~

### Adding LearningStore to the views
* LearnView.swift
* 프러퍼티 learningStore 추가 
~~~
import SwiftUI

struct LearnView: View {
    @ObservedObject private var learningStore = LearningStore()
    
    var body: some View {
        VStack {
            Spacer()
            Text("Swipe left if you remembered" + "\nSwipe right if you didn’t") 
				.font(.headline)
            DeckView(onMemorized: { self.learningStore.score += 1 }, deck: learningStore.deck)
            Spacer()
			// learnStore에서 스코어, 카드 수 가져오기
            Text("Remembered \(self.learningStore.score)" + "/\(self.learningStore.deck.cards.count)")
        }
    }
}

struct LearnView_Previews: PreviewProvider {
    static var previews: some View {
        LearnView()
    }
}
~~~

* CardView.swift
~~~
import SwiftUI
import Learning
import Languages

struct CardView: View {
    private let flashCard: FlashCard
    
    init(_ card: FlashCard) {
        self.flashCard = card
    }
    
    var body: some View {
        ZStack {
            Rectangle()
                .fill(Color.red)
                .frame(width: 320, height: 210) .cornerRadius(12)
            VStack {
                Spacer()
                Text(flashCard.card.word.original)
                    .font(.largeTitle)
                    .foregroundColor(.white)
                Text(flashCard.card.word.translation)
                    .font(.caption)
                    .foregroundColor(.white)
                Spacer()
            }
        }
        .shadow(radius: 8)
        .frame(width: 320, height: 210)
        .animation(.spring())
    }
}

struct CardView_Previews: PreviewProvider {
    static var previews: some View {
        let card = FlashCard(
            card: WordCard(
                from: TranslatedWord(
                    from: "Apple",
                    withPronunciation: "Apple",
                    andTranslation: "Omena"
                )
            )
        )
        return CardView(card)
    }
}
~~~

* DeckView.swift 
~~~
import SwiftUI

struct DeckView: View {
    @ObservedObject private var deck: FlashDeck
    private let onMemorized: () -> Void
    
    init(onMemorized: @escaping () -> Void, deck: FlashDeck) {
        self.onMemorized = onMemorized
        self.deck = deck
    }
    
    var body: some View {
        ZStack {
            ForEach(deck.cards.filter { $0.isActive }) { card in
                self.getCardView(for: card)
            }
        }
    }
    
    private func getCardView(for card: FlashCard) -> CardView {
        let activeCards = deck.cards.filter {
            $0.isActive == true
        }
        
        if let lastCard = activeCards.last {
            if lastCard == card {
                return createCardView(for: card)
            }
        }
        
        let view = createCardView(for: card)
        return view
    }
    
    private func createCardView(for card: FlashCard) -> CardView {
        let view = CardView(card)
        return view
    }
}

struct DeckView_Previews: PreviewProvider {
    static var previews: some View {
        DeckView(onMemorized: {}, deck: LearningStore().deck)
    }
}
~~~

## Your first gesture
* 탭 제스쳐 추가해보기
~~~
// CardView.swift

// 제스처 추가
.gesture(TapGesture()
	.onEnded {
		withAnimation(.easeIn, { 
			self.revealed = !self.revealed
		})
	}
)

// revealed 값에 따라 번역 Text 노출
if self.revealed { 
	Text(flashCard.card.word.translation)
		.font(.caption)
		.foregroundColor(.white)
}
~~~

## Custom gestures
* 커스텀 드래그 제스쳐 추가해보기
~~~
internal enum DiscardedDirection {
    case left
    case right
}
~~~

* CardView.swift
~~~
// 드래그 프러퍼티 추가
typealias CardDrag = (_ card: FlashCard, _ direction: DiscardedDirection) -> Void
private let dragged: CardDrag
@State var offset: CGSize = .zero

// 생성자 메서드 수정
init(_ card: FlashCard, onDrag dragged: @escaping CardDrag = {_,_ in }) {
	self.flashCard = card
	self.dragged = dragged
}

// body 시작 부분 변경, drag 제스쳐 정의
let drag = DragGesture()
            .onChanged { self.offset = $0.translation }
            .onEnded {
                if $0.translation.width < -100 {
                    self.offset = .init(width: -1000, height: 0)
                    self.dragged(self.flashCard, .left)
                } else if $0.translation.width > 100 {
                    self.offset = .init(width: 1000, height: 0)
                    self.dragged(self.flashCard, .right)
                } else {
                    self.offset = .zero
                }
            }
        
return ZStack {
/// ... 이하 동일

// body 끝에 gesture 추가 
.offset(self.offset) 
.gesture(drag)
~~~

* DeckView.swift
~~~
func createCardView(for card: FlashCard) -> CardView {
	let view = CardView(card, onDrag: { card, direction in
		if direction == .left {
			self.onMemorized()
		}
	})
	return view
}
~~~

## Combining gestures for more complex interactions
* 롱프레스 제스처 추가 
~~~
// CardView.swift

// longGesture 프러퍼티 추가 
@GestureState var isLongPressed = false

// longGesture 정의
let longPress = LongPressGesture()
	.updating($isLongPressed) { value, state, transition in
		state = value
	}
	.simultaneously(with: drag)
	
// body 끝에 제스쳐 추가
.gesture(longPress)
.scaleEffect(isLongPressed ? 1.1 : 1)
~~~

