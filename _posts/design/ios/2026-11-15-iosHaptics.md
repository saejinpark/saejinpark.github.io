---
title: "햅틱: 손끝으로 전하는 피드백"
date: 2026-11-15 00:06:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - iOS
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "진동이 아니라 촉감. 성공·경고·실패·선택·충격 다섯 패턴의 문법과 sensoryFeedback 구현, 그리고 절제의 규칙."
---

햅틱은 진동으로 전하는 피드백이다. iPhone의 Taptic Engine은 "윙" 하는 진동이 아니라 **똑, 톡, 두둑** 같은 짧고 정확한 촉감을 만든다.
웹에는 대응물이 없다. `navigator.vibrate()`가 있지만 iOS Safari는 지원하지 않고, 지원하는 기기에서도 정밀하지 않다.

햅틱은 iOS 앱이 "진짜처럼" 느껴지는 이유의 절반이다. 토글이 넘어갈 때, 피커가 한 칸 굴러갈 때, 당겨서 새로 고침이 걸릴 때 손끝에 오는 작은 신호가 화면 속 물건에 무게를 준다.

## HIG가 말하는 것

- **시스템 패턴을 쓴다**: Apple이 정의한 패턴(성공, 경고, 실패, 선택, 충격)은 OS 전체에서 같은 의미로 쓰인다. 사용자는 이미 이 언어를 안다
- **시각·청각과 동기화한다**: 햅틱은 눈에 보이는 변화와 같은 순간에 와야 한다. 0.1초만 어긋나도 이상하다
- **의미 있을 때만**: 모든 탭에 햅틱을 넣으면 아무 햅틱도 의미가 없다. 결과가 있는 순간에만
- **일관되게**: 같은 동작에는 항상 같은 햅틱. 저장이 `.success`면 모든 저장이 `.success`
- **없어도 되게**: 햅틱이 꺼진 기기(대부분의 iPad), 무음 모드, 설정에서 끈 사용자가 있다. 햅틱 없이도 앱은 완전해야 한다
- **시스템 컨트롤은 이미 갖고 있다**: `Toggle`, `Picker`, `Slider`, 스와이프 동작, 당겨서 새로 고침, 컨텍스트 메뉴는 햅틱이 내장되어 있다

## 햅틱의 문법

| 종류 | 느낌 | 쓰는 때 | 예 |
|---|---|---|---|
| `.success` | 두-둑 (가볍게 두 번) | 과제 완료 | 저장됨, 결제 완료, 체크 표시 |
| `.warning` | 둑-두 (무겁게 두 번) | 주의 필요 | 입력 오류, 한도 접근 |
| `.error` | 두두둑 (세 번) | 실패 | 로그인 실패, 잘못된 비밀번호 |
| `.selection` | 틱 (아주 가볍게) | 선택이 바뀜 | 피커 굴러감, 세그먼트 전환, 슬라이더 눈금 |
| `.impact(.light)` | 톡 | 가벼운 물체 접촉 | 카드 스냅, 토글 |
| `.impact(.medium)` | 툭 | 보통 접촉 | 드래그 끝, 버튼 눌림 |
| `.impact(.heavy)` | 쿵 | 무거운 물체 접촉 | 큰 전환, 임계점 통과 |
| `.impact(.soft)` / `.rigid` | 부드럽게 / 딱딱하게 | 재질 표현 | 고무 / 유리 |
| `.levelChange` | 단계 전환 | 단계가 바뀜 | 볼륨 단계, 확대 레벨 |
| `.alignment` | 맞춤 | 정렬 가이드에 붙음 | 그리기 앱에서 선이 맞음 |

**세 가지 질문으로 고른다**

1. 결과를 알리는가? → `.success` / `.warning` / `.error`
2. 선택이 바뀌는가? → `.selection`
3. 물건이 부딪히는가? → `.impact` (무게는 물건의 무게)

## SwiftUI로 구현하기

**기본: `sensoryFeedback` (iOS 17+)**

값이 바뀌면 햅틱이 울린다. 상태 기반이라 SwiftUI와 잘 맞는다.

```swift
struct SaveButton: View {
    @State private var saveCount = 0

    var body: some View {
        Button("저장") {
            save()
            saveCount += 1
        }
        .sensoryFeedback(.success, trigger: saveCount)
    }
}
```

**조건부: 특정 전이에서만**

```swift
.sensoryFeedback(.success, trigger: isValid) { old, new in new && !old }   // 유효해지는 순간만
.sensoryFeedback(.error, trigger: attempts) { _, _ in lastResult == .failed }
```

**드래그 임계점**

```swift
struct DismissCard: View {
    @State private var offset: CGFloat = 0
    @State private var passedThreshold = false

    var body: some View {
        CardContent()
            .offset(x: offset)
            .gesture(
                DragGesture()
                    .onChanged { v in
                        offset = v.translation.width
                        passedThreshold = abs(offset) > 120          // 넘는 순간 한 번만
                    }
                    .onEnded { _ in /* ... */ }
            )
            .sensoryFeedback(.impact(weight: .medium), trigger: passedThreshold) { _, new in new }
    }
}
```

**피커처럼 한 칸씩**

```swift
.sensoryFeedback(.selection, trigger: selectedIndex)
```

**iOS 16 이하 또는 세밀한 제어: UIKit 제너레이터**

```swift
let generator = UIImpactFeedbackGenerator(style: .light)
generator.prepare()           // 지연을 줄이려면 미리 준비 (0.1초 전쯤)
generator.impactOccurred(intensity: 0.7)

UINotificationFeedbackGenerator().notificationOccurred(.success)
UISelectionFeedbackGenerator().selectionChanged()
```

**커스텀 패턴: Core Haptics**

게임이나 악기 앱처럼 고유한 촉감이 필요할 때. `CHHapticPattern`으로 강도와 선명도를 시간축에 그린다. 일반 앱에는 거의 필요 없다.

```swift
let engine = try CHHapticEngine()
try engine.start()
let event = CHHapticEvent(eventType: .hapticTransient,
                          parameters: [.init(parameterID: .hapticIntensity, value: 0.8),
                                       .init(parameterID: .hapticSharpness, value: 0.5)],
                          relativeTime: 0)
let pattern = try CHHapticPattern(events: [event], parameters: [])
try engine.makePlayer(with: pattern).start(atTime: 0)
```

## 자주 하는 실수

- **모든 버튼에 햅틱**: 시스템 버튼에는 햅틱이 없다. 버튼에 햅틱을 넣으면 iOS답지 않다. 결과(저장 완료)에 넣는다
- **스크롤마다 햅틱**: 리스트를 스크롤할 때 셀마다 울리면 피로하다. 피커처럼 "칸"이 의미 있을 때만 `.selection`
- **의미 뒤섞기**: 삭제 성공에 `.error`를 쓰거나, 오류에 `.impact`를 쓰면 사용자의 학습이 깨진다
- **시각 피드백 없이 햅틱만**: 햅틱이 꺼진 사용자는 아무것도 모른다. 항상 눈에 보이는 변화와 함께
- **타이밍 어긋남**: 애니메이션 끝에 햅틱을 넣으면 늦다. 상태가 바뀌는 순간(애니메이션 시작)에
- **`prepare()` 없이 UIKit 제너레이터 즉시 호출**: 첫 햅틱이 늦게 온다. 제스처가 시작될 때 미리 준비한다
- **햅틱으로 알림 대체**: 백그라운드에서는 햅틱이 울리지 않는다. 알림은 알림으로
- **iPad 테스트만**: iPad 대부분은 햅틱이 없다. iPhone 실기기에서 확인한다. 시뮬레이터도 햅틱을 재생하지 않는다

## 체크리스트

- [ ] 시스템 컨트롤(토글, 피커, 스와이프)에 햅틱을 중복으로 넣지 않았다
- [ ] 결과 햅틱(`.success` / `.warning` / `.error`)이 의미에 맞게 쓰였다
- [ ] 같은 동작에는 항상 같은 햅틱
- [ ] 모든 햅틱에 동시에 일어나는 시각 변화가 있다
- [ ] 햅틱을 끈 기기에서도 앱 사용에 문제가 없다
- [ ] 연속 제스처에서는 임계점 통과 한 번만 울린다
- [ ] iPhone 실기기에서 타이밍을 확인했다

## 함께 읽기

- [마이크로인터랙션: 작은 디테일의 큰 영향]({% post_url design/technique/2024-08-25-microInteraction %}) — 햅틱이 속한 더 큰 범주
- [제스처 기반 인터페이스: 직관적 조작의 혁신]({% post_url design/technique/2024-08-25-gestureBasedInterface %}) — 제스처와 햅틱의 동기화
- [모달의 위계: 알림, 시트, 팝오버]({% post_url design/ios/2026-11-15-iosModality %}) — 알림 대신 햅틱으로 성공 알리기
- [손목 위의 디자인: watchOS]({% post_url design/ios/2026-11-15-watchOSDesign %}) — 손목에서는 햅틱이 더 중요하다
