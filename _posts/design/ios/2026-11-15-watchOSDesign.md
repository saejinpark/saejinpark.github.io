---
title: "손목 위의 디자인: watchOS"
date: 2026-11-15 00:07:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - watchOS
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "2초 안에 끝나는 상호작용. 디지털 크라운, 컴플리케이션, 손목 올림, 그리고 iPhone 앱을 축소하면 안 되는 이유."
---

Apple Watch 앱은 작은 iPhone 앱이 아니다. 화면이 작아서가 아니라, **사용 상황**이 다르기 때문이다.
사용자는 손목을 들어 2초 보고 내린다. 걷는 중이고, 한 손은 다른 것을 들고 있고, 화면을 가린 손가락 때문에 보이는 영역은 더 작다.

HIG의 watchOS 섹션은 이 상황을 한 문장으로 요약한다. **"상호작용은 짧고, 정보는 한눈에, 조작은 한 손으로."**

## iPhone과 다른 점

| | iPhone | Apple Watch |
|---|---|---|
| 사용 시간 | 분 단위 | 초 단위 (평균 2~5초) |
| 입력 | 양손, 키보드 | 한 손가락, 디지털 크라운, 음성, 더블 탭 제스처 |
| 화면 | 손이 가리지 않음 | 손가락이 화면의 1/4을 가림 |
| 내비게이션 | 탭 바, 스택 | 세로 페이지, 리스트, 스택 (얕게) |
| 앱 진입 | 홈 화면 아이콘 | 컴플리케이션, 스마트 스택, 알림이 대부분 |
| 배경 | 흰색 / 검정 | 항상 검정 (화면 가장자리가 베젤과 녹아든다) |
| 텍스트 | SF Pro | SF Compact (좁은 폭에서 읽히도록 설계) |

## HIG가 말하는 것

- **한눈에(Glanceable)**: 가장 중요한 정보가 화면 중앙, 가장 크게. 손목을 올렸을 때 첫 0.5초에 보여야 한다
- **실행 가능하게(Actionable)**: 보고 나서 할 수 있는 일이 하나는 있어야 한다. 그 하나는 큰 버튼으로
- **반응적으로(Responsive)**: 로딩 스피너는 손목에서 특히 치명적이다. 마지막 데이터를 먼저 보여 주고 갱신한다
- **디지털 크라운을 활용하라**: 스크롤, 값 조절, 확대는 크라운으로. 손가락이 화면을 가리지 않는다
- **검정 배경**: 화면 가장자리가 보이지 않게. 밝은 배경은 배터리와 눈 모두에 좋지 않다
- **컴플리케이션이 진입점**: 앱 아이콘을 눌러 들어오는 사용자는 적다. 시계 페이스의 컴플리케이션과 스마트 스택이 주 진입점이다
- **알림은 짧게**: 손목에서 긴 알림은 읽지 않는다. 핵심 한 줄 + 동작 버튼 하나

## 내비게이션

watchOS의 내비게이션은 세 가지뿐이고, 깊이는 2~3단계를 넘지 않는다.

- **세로 페이지 (`TabView` + `.verticalPage`)**: watchOS 10부터 기본. 크라운을 돌리면 페이지가 넘어간다. 날씨 앱의 현재 → 시간별 → 주간
- **리스트 (`List`)**: 항목이 여럿일 때. 크라운으로 스크롤
- **스택 (`NavigationStack`)**: 리스트에서 상세로. 왼쪽 가장자리 스와이프로 돌아간다

탭 바(가로 탭)는 watchOS 10부터 권장하지 않는다. 손가락으로 가로 스와이프하면 화면을 가린다.

## SwiftUI로 구현하기

**세로 페이지 구조**

```swift
struct WatchRootView: View {
    var body: some View {
        TabView {
            NowView()          // 지금 가장 중요한 것
            TodayView()        // 오늘
            SettingsView()     // 설정
        }
        .tabViewStyle(.verticalPage)   // 크라운으로 넘김
    }
}
```

**한눈에 보는 화면: 숫자 하나가 주인공**

```swift
struct NowView: View {
    let remaining: Date

    var body: some View {
        VStack(spacing: 4) {
            Text("집중")
                .font(.caption2)
                .foregroundStyle(.secondary)
            Text(remaining, style: .timer)
                .font(.system(size: 48, weight: .light, design: .rounded))
                .monospacedDigit()
                .minimumScaleFactor(0.5)
            Button("일시정지", systemImage: "pause.fill") { }
                .buttonStyle(.borderedProminent)
                .controlSize(.large)                    // 손가락 크기
        }
        .containerBackground(.indigo.gradient, for: .tabView)   // watchOS 10 배경
    }
}
```

**디지털 크라운으로 값 조절**

```swift
struct TimerPicker: View {
    @State private var minutes: Double = 25

    var body: some View {
        VStack {
            Text("\(Int(minutes))분")
                .font(.title)
            Text("크라운을 돌려 조절")
                .font(.caption2).foregroundStyle(.secondary)
        }
        .focusable()
        .digitalCrownRotation($minutes, from: 1, through: 90, by: 1,
                              sensitivity: .medium, isContinuous: false,
                              isHapticFeedbackEnabled: true)     // 한 칸마다 틱
    }
}
```

**툴바: 상단 모서리 버튼 (watchOS 10+)**

```swift
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        Button("추가", systemImage: "plus") { }
    }
    ToolbarItemGroup(placement: .bottomBar) {       // 하단 양쪽 모서리
        Button("초기화", systemImage: "arrow.counterclockwise") { }
        Spacer()
        Button("시작", systemImage: "play.fill") { }
    }
}
```

**컴플리케이션 (WidgetKit)**

watchOS 9부터 컴플리케이션은 WidgetKit으로 만든다. iPhone 잠금 화면 위젯과 같은 코드를 공유한다.

```swift
struct TimerComplication: Widget {
    var body: some WidgetConfiguration {
        StaticConfiguration(kind: "timer", provider: TimerProvider()) { entry in
            TimerComplicationView(entry: entry)
                .containerBackground(.clear, for: .widget)
        }
        .supportedFamilies([.accessoryCircular, .accessoryRectangular,
                            .accessoryInline, .accessoryCorner])   // .accessoryCorner는 watchOS 전용
    }
}

struct TimerComplicationView: View {
    @Environment(\.widgetFamily) var family
    let entry: TimerEntry

    var body: some View {
        switch family {
        case .accessoryCorner:
            Text(entry.remaining.formatted(.time(pattern: .minuteSecond)))
                .widgetCurvesContent()                           // 모서리 곡선을 따라 휘어짐
                .widgetLabel { Gauge(value: entry.progress) { } }
        case .accessoryCircular:
            Gauge(value: entry.progress) { Image(systemName: "timer") }
                .gaugeStyle(.accessoryCircular)
        default:
            Label(entry.label, systemImage: "timer")
        }
    }
}
```

**더블 탭 제스처 (Series 9 / Ultra 2 이상)**

엄지와 검지를 두 번 맞대면 화면의 **주 동작**이 실행된다. 시스템이 알아서 고르지만, `.handGestureShortcut(.primaryAction)`으로 어떤 버튼인지 지정할 수 있다.

```swift
Button("시작") { start() }
    .handGestureShortcut(.primaryAction)
```

## 자주 하는 실수

- **iPhone 화면을 축소**: 정보를 줄여야 한다. 화면 하나에 숫자 하나, 버튼 하나
- **가로 스크롤과 가로 탭**: 손가락이 화면을 가린다. 세로 페이지로
- **작은 버튼 여러 개**: 손목에서 정확한 탭은 어렵다. 버튼은 크게, 적게. 한 줄에 둘까지
- **흰 배경**: 베젤 경계가 드러나고 배터리를 먹는다. 검정 또는 어두운 그라데이션
- **로딩 스피너로 시작**: 손목을 내린다. 이전 데이터를 즉시 보여 주고 뒤에서 갱신
- **컴플리케이션 없음**: 진입점이 없는 앱은 열리지 않는다. 최소 원형 하나, 직사각형 하나
- **크라운 무시**: 리스트가 스크롤되는 것 외에 크라운이 하는 일이 없으면 손해다. 값 조절, 페이지 넘김, 확대에 쓴다
- **iPhone 없이 안 되는 앱**: 셀룰러 모델과 iPhone이 멀리 있는 상황을 고려한다. 핵심 기능은 워치 단독으로
- **알림에 긴 문장**: 첫 줄만 읽힌다. 핵심을 앞에
- **햅틱 생략**: 손목에서는 햅틱이 시각보다 먼저 도착한다. 타이머 종료, 목표 달성은 반드시 햅틱과 함께

## 체크리스트

- [ ] 손목을 올렸을 때 0.5초 안에 핵심 정보가 보인다
- [ ] 화면당 주 동작이 하나이고, 버튼이 `.controlSize(.large)`다
- [ ] 내비게이션 깊이가 3단계 이하다
- [ ] 세로 페이지 또는 리스트, 가로 스와이프가 없다
- [ ] 디지털 크라운으로 할 수 있는 일이 스크롤 외에 하나는 있다
- [ ] 컴플리케이션이 원형·직사각형·인라인 중 둘 이상 있다
- [ ] 배경이 검정 또는 어두운 색이다
- [ ] 앱을 열면 로딩 없이 마지막 상태가 바로 보인다
- [ ] 중요한 순간(완료, 목표 달성)에 햅틱이 있다
- [ ] 큰 글자 설정(워치에도 있다)에서 레이아웃이 유지된다

## 함께 읽기

- [위젯과 라이브 액티비티: 한눈에 보는 디자인]({% post_url design/ios/2026-11-15-iosWidgets %}) — 컴플리케이션의 바탕이 되는 WidgetKit
- [햅틱: 손끝으로 전하는 피드백]({% post_url design/ios/2026-11-15-iosHaptics %}) — 손목에서 더 중요한 피드백
- [미니멀리즘 디자인: 적은 것이 더 많은 것이다]({% post_url design/style/2024-08-25-minimalism %}) — 손목이 강제하는 미니멀리즘
- [제스처 기반 인터페이스: 직관적 조작의 혁신]({% post_url design/technique/2024-08-25-gestureBasedInterface %}) — 크라운과 더블 탭
