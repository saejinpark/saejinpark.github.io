---
title: "접근성: 모두를 위한 기본값"
date: 2026-11-15 00:03:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - iOS
  - 접근성
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "VoiceOver, Dynamic Type, 대비, 모션. iOS 접근성의 네 축과, 시스템 기본값을 따르면 대부분 공짜로 얻는다는 사실."
---

접근성은 "장애가 있는 사용자를 위한 추가 작업"이 아니다. iOS에서 접근성은 **시스템 기본값을 따르면 대부분 자동으로 얻어지고, 기본값을 덮어쓸 때 잃는 것**이다.

이 시리즈의 모든 글에 접근성 항목이 있는 이유가 그것이다. 뉴모피즘은 대비를 잃고, 커스텀 제스처는 VoiceOver를 잃고, 고정 크기 폰트는 Dynamic Type을 잃는다.
이 글은 흩어져 있던 그 항목들을 한곳에 모은다.

## 네 가지 축

iOS 접근성은 설정 > 손쉬운 사용에 있는 수십 가지 기능으로 이뤄지지만, 앱 디자인에 영향을 주는 것은 크게 넷이다.

| 축 | 사용자 설정 | 앱이 할 일 |
|---|---|---|
| **시각: 읽기** | VoiceOver, 화면 읽어주기 | 모든 요소에 레이블, 올바른 순서와 그룹 |
| **시각: 크기와 대비** | 더 큰 텍스트, 대비 높이기, 색상 없이 구별, 투명도 줄이기, 버튼 모양 | Dynamic Type, 의미론적 색, 색 외의 구분 수단 |
| **운동** | 스위치 제어, 음성 제어, AssistiveTouch, 동작 줄이기 | 제스처의 대안, 충분한 터치 영역, 모션 대체 |
| **청각** | 자막, 모노 오디오, 햅틱 | 소리에만 의존하지 않기, 햅틱과 시각 병행 |

## HIG가 말하는 것

HIG의 Accessibility 페이지는 세 문장으로 요약된다.

1. **단순하게 설계하라**: 익숙한 상호작용, 명확한 레이블, 일관된 구조. 접근성이 좋은 디자인은 모두에게 좋은 디자인이다
2. **인식할 수 있게 하라**: 시각, 청각, 촉각 중 하나에만 의존하지 않는다. 색만으로, 소리만으로 정보를 전하지 않는다
3. **시스템 설정을 존중하라**: 사용자가 글자를 키우고 모션을 줄였다면 앱은 그 선택을 따라야 한다

## VoiceOver

VoiceOver는 화면의 요소를 순서대로 읽어 주는 스크린 리더다. 시스템 컴포넌트는 모두 VoiceOver를 지원하므로, 앱이 할 일은 **커스텀 뷰와 이미지에 의미를 붙이는 것**이다.

```swift
// 아이콘만 있는 버튼: 레이블 필수
Button { toggleFavorite() } label: {
    Image(systemName: isFavorite ? "heart.fill" : "heart")
}
.accessibilityLabel(isFavorite ? "즐겨찾기 해제" : "즐겨찾기 추가")

// 의미 있는 이미지: 설명
Image("chart_q3")
    .accessibilityLabel("3분기 매출 차트. 7월 1200만 원에서 9월 1800만 원으로 상승")

// 장식 이미지: 숨김
Image("divider_ornament")
    .accessibilityHidden(true)

// 여러 요소를 하나로: 카드
ArticleCard(article)
    .accessibilityElement(children: .combine)       // 제목+요약+날짜를 한 번에 읽음
    .accessibilityAddTraits(.isButton)

// 값이 있는 컨트롤
Slider(value: $volume)
    .accessibilityLabel("음량")
    .accessibilityValue("\(Int(volume * 100))퍼센트")

// 제스처 대안
SwipeCard()
    .accessibilityAction(named: "다음 카드") { next() }

// 제목 표시: 로터로 제목 사이를 이동할 수 있게
Text("오늘의 할 일").font(.title2)
    .accessibilityAddTraits(.isHeader)
```

**읽기 순서**: VoiceOver는 왼쪽 위에서 오른쪽 아래로 읽는다. 시각적 배치와 논리적 순서가 다르면 `accessibilitySortPriority`로 조정한다.

**알림**: 화면이 바뀌었을 때 알려 주려면 `AccessibilityNotification.Announcement("저장되었습니다").post()`.

## Dynamic Type

사용자가 글자 크기를 바꾸면 앱의 모든 텍스트가 따라야 한다. 자세한 내용은 [타이포그래피]({% post_url design/technique/2024-08-25-typography %}) 글에 있고, 여기서는 규칙만.

```swift
Text("본문").font(.body)                          // O: 텍스트 스타일
Text("본문").font(.system(size: 17))              // X: 고정 크기

@ScaledMetric(relativeTo: .body) var iconSize = 24  // 비텍스트 크기도 비례

// 접근성 크기에서 레이아웃 전환
@Environment(\.dynamicTypeSize) var typeSize
let layout = typeSize.isAccessibilitySize ? AnyLayout(VStackLayout()) : AnyLayout(HStackLayout())
```

테스트는 설정의 최대 크기(AX5)로 한다. 거기서 잘리거나 겹치는 것이 없으면 중간 크기는 괜찮다.

## 대비와 색

- **대비 기준**: 일반 텍스트 4.5:1, 큰 텍스트(약 18pt 이상 또는 14pt 볼드)와 UI 컴포넌트 3:1
- **의미론적 색을 쓰면 대비 높이기 설정이 자동 적용**된다. `Color.primary`, `.secondary`, `Color(.systemBackground)`는 고대비 변형을 갖고 있다
- **커스텀 색은 에셋 카탈로그**에 넣고 "High Contrast" 변형을 채운다
- **색 외의 수단**: 오류는 빨간 테두리 + 아이콘 + 텍스트. 차트 계열은 색 + 모양

```swift
@Environment(\.colorSchemeContrast) var contrast
@Environment(\.accessibilityDifferentiateWithoutColor) var noColor

// 색상 없이 구별 설정: 상태를 아이콘으로도
HStack {
    if noColor { Image(systemName: status.symbol) }
    Circle().fill(status.color).frame(width: 10)
    Text(status.title)
}
```

## 모션과 투명도

```swift
@Environment(\.accessibilityReduceMotion) var reduceMotion
@Environment(\.accessibilityReduceTransparency) var reduceTransparency

// 동작 줄이기: 큰 이동은 페이드로
withAnimation(reduceMotion ? .easeInOut(duration: 0.15) : .spring) { expand.toggle() }

// 자동 재생, 패럴랙스, 반복 애니메이션은 끈다
.symbolEffect(.pulse, isActive: isLoading && !reduceMotion)

// 투명도 줄이기: 재질 대신 불투명 배경
.background {
    if reduceTransparency { Color(.secondarySystemBackground) }
    else { Rectangle().fill(.regularMaterial) }
}
```

시스템 `Material`은 투명도 줄이기에 자동 대응한다. 직접 만든 반투명만 신경 쓰면 된다.

## 운동과 터치

- **44×44pt**: 모든 탭 가능한 요소의 최소 크기. 아이콘이 작아도 `.padding`과 `.contentShape`로 영역을 확보한다
- **제스처의 대안**: 스와이프, 길게 누르기, 핀치로만 할 수 있는 기능이 없어야 한다. 시스템 컴포넌트(`swipeActions`, `contextMenu`)는 VoiceOver가 자동으로 대안을 제공한다
- **시간 제한**: 자동으로 사라지는 UI는 충분히 길게, 또는 설정에서 조절 가능하게
- **버튼 모양 설정**: 켜면 텍스트 버튼에 밑줄이 생긴다. `Button`을 쓰면 자동, `onTapGesture`를 쓴 텍스트는 아무 일도 없다

## 감사 방법

**Xcode Accessibility Inspector**: Xcode > Open Developer Tool > Accessibility Inspector. 시뮬레이터를 가리키고 Audit을 실행하면 레이블 없는 요소, 대비 부족, 작은 터치 영역을 찾아 준다.

**Previews에서 한 번에**

```swift
#Preview("접근성") {
    ContentView()
        .environment(\.dynamicTypeSize, .accessibility5)
        .environment(\.colorSchemeContrast, .increased)
}
```

**실기기 테스트**: 설정 > 손쉬운 사용 > 손쉬운 사용 단축키에 VoiceOver를 등록하고, 측면 버튼 세 번으로 켜고 끈다. 눈을 감고 주요 과제를 끝까지 해 본다.

**Xcode 15+ 접근성 감사 테스트**

```swift
func testAccessibility() throws {
    let app = XCUIApplication()
    app.launch()
    try app.performAccessibilityAudit()   // 자동 감사, 실패하면 테스트 실패
}
```

## 자주 하는 실수

- **심볼 이름이 그대로 읽힘**: `Image(systemName: "gear")`만 있는 버튼은 VoiceOver가 "톱니바퀴"라고 읽는다. "설정"이라고 레이블을 붙인다
- **`onTapGesture`로 만든 버튼**: VoiceOver가 버튼인 줄 모른다. `Button`을 쓰거나 `.accessibilityAddTraits(.isButton)`
- **접근성 레이블에 컨트롤 종류 포함**: "저장 버튼"이 아니라 "저장". 종류는 VoiceOver가 덧붙인다
- **플레이스홀더만 있는 입력 필드**: 입력하면 플레이스홀더가 사라져 무엇을 입력하는지 모른다. `TextField("이메일", ...)`의 첫 인자가 레이블 역할을 하지만, 화면에 보이는 레이블도 두는 것이 좋다
- **`accessibilityHidden`으로 복잡한 것 숨기기**: 어려워서 숨기면 그 기능은 없는 것이다
- **접근성 테스트를 출시 직전에**: 구조 문제(읽기 순서, 그룹)는 나중에 고치기 어렵다. 화면 하나 만들 때마다 VoiceOver로 한 번 훑는다

## 체크리스트

- [ ] 모든 이미지와 아이콘 버튼에 레이블이 있거나 장식으로 숨겨져 있다
- [ ] 모든 텍스트가 Dynamic Type 텍스트 스타일을 쓴다
- [ ] AX5 크기에서 잘림, 겹침, 가로 스크롤이 없다
- [ ] 텍스트 대비 4.5:1, UI 요소 3:1 이상
- [ ] 색만으로 전달하는 정보가 없다
- [ ] 동작 줄이기에서 큰 이동, 자동 재생, 반복 애니메이션이 꺼진다
- [ ] 투명도 줄이기에서 커스텀 반투명이 불투명으로 바뀐다
- [ ] 모든 탭 가능 요소가 44×44pt 이상
- [ ] 제스처로만 할 수 있는 기능이 없다
- [ ] VoiceOver로 주요 과제를 처음부터 끝까지 완료할 수 있다
- [ ] Accessibility Inspector 감사에 경고가 없다

## 함께 읽기

- [타이포그래피: 글자를 통한 효과적인 커뮤니케이션]({% post_url design/technique/2024-08-25-typography %}) — Dynamic Type 자세히
- [색상과 다크 모드: 의미론적 색 체계]({% post_url design/ios/2026-11-15-iosColorDarkMode %}) — 대비와 고대비 변형
- [제스처 기반 인터페이스: 직관적 조작의 혁신]({% post_url design/technique/2024-08-25-gestureBasedInterface %}) — 제스처의 대안
- [애니메이션과 모션: 동적 사용자 경험의 핵심]({% post_url design/technique/2024-08-25-animationMotion %}) — 동작 줄이기 대응
