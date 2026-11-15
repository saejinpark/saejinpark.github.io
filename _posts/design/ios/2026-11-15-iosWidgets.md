---
title: "위젯과 라이브 액티비티: 한눈에 보는 디자인"
date: 2026-11-15 00:05:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - iOS
  - WidgetKit
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "앱 밖에서 보이는 앱. 홈 화면 위젯, 잠금 화면 위젯, 라이브 액티비티, 다이내믹 아일랜드의 정보 디자인 원칙과 WidgetKit 구현."
---

위젯은 앱을 열지 않고 보는 앱이다. 사용자는 위젯을 **0.5초 보고 지나간다**. 그 짧은 시간에 가장 중요한 정보 하나를 전달하는 것이 위젯 디자인의 전부다.

웹에 대응물이 없는 주제다. 가장 가까운 것은 대시보드의 타일이지만, 대시보드는 사용자가 보러 오는 곳이고 위젯은 사용자의 홈 화면에 얹혀 있다. 그래서 규칙이 훨씬 엄격하다.

## 종류

| 종류 | 어디에 | 크기 | 갱신 | 상호작용 |
|---|---|---|---|---|
| **홈 화면 위젯** | 홈 화면, 투데이 뷰 | 소 / 중 / 대 / 특대(iPad) | 타임라인 (시스템이 예산 관리) | 탭하면 앱 열기, 버튼·토글 (iOS 17+) |
| **잠금 화면 위젯** | 잠금 화면 시계 아래, 위 | 원형 / 직사각형 / 인라인 | 타임라인 | 탭하면 앱 열기 |
| **StandBy** | 가로로 충전 중인 iPhone | 홈 화면 위젯 재사용 | 타임라인 | 동일 |
| **라이브 액티비티** | 잠금 화면, 다이내믹 아일랜드 | 잠금 화면 배너, 아일랜드 소/확장 | 실시간 (푸시 또는 앱) | 버튼 (iOS 17+) |
| **컨트롤** (iOS 18+) | 제어 센터, 잠금 화면, 동작 버튼 | 작은 버튼/토글 | 즉시 | 토글, 실행 |
| **Apple Watch 컴플리케이션** | 시계 페이스 | 페이스별 다양 | 타임라인 | 탭하면 앱 열기 |

## HIG가 말하는 것

- **한눈에(Glanceable)**: 위젯은 읽는 것이 아니라 보는 것이다. 핵심 정보 하나, 보조 정보 둘까지
- **적절한 시점에(Relevant)**: 시간과 상황에 맞는 정보. 아침에는 오늘 일정, 저녁에는 내일 첫 일정
- **개인화(Personalized)**: 사용자가 위젯을 편집해 원하는 것을 고를 수 있게
- **앱의 축소판이 아니다**: 위젯은 앱의 기능을 다 담지 않는다. 정보를 보여 주고, 탭하면 앱의 해당 화면으로 데려간다
- **콘텐츠가 가장자리까지**: 위젯 안에 또 카드를 그리지 않는다. 위젯 자체가 카드다
- **다크 모드, 틴트 모드, 투명 모드**: iOS 18부터 사용자가 홈 화면을 틴트 색으로 물들일 수 있다. 위젯은 단색 렌더링에서도 읽혀야 한다
- **라이브 액티비티는 진행 중인 것에만**: 배달, 운동, 타이머, 경기 점수처럼 시작과 끝이 있는 것. 광고나 정적 정보에 쓰면 안 된다

## 정보 설계

위젯 크기별로 담을 수 있는 정보의 양이 정해져 있다.

| 크기 | 담을 것 | 예: 날씨 |
|---|---|---|
| 인라인 (잠금 화면) | 텍스트 한 줄 | "맑음 12°" |
| 원형 (잠금 화면) | 숫자 하나 또는 게이지 | 기온 게이지 |
| 소 | 핵심 정보 하나 + 맥락 | 현재 기온, 최고/최저, 아이콘 |
| 중 | 핵심 + 시간 축 또는 목록 3~4개 | 시간별 예보 6칸 |
| 대 | 중 + 더 긴 목록 또는 차트 | 주간 예보 |

**숫자가 주인공**이면 크게. 글자 크기 대비를 과감하게 둔다. 소형 위젯에서 기온은 40pt, 설명은 12pt.

## WidgetKit으로 구현하기

**타임라인 모델**

위젯은 뷰가 아니라 **시간표**를 제공한다. "지금은 이것, 10분 뒤에는 저것"을 미리 넘기면 시스템이 때맞춰 그린다.

```swift
struct TimerEntry: TimelineEntry {
    let date: Date
    let remaining: TimeInterval
    let label: String
}

struct TimerProvider: TimelineProvider {
    func placeholder(in context: Context) -> TimerEntry {
        TimerEntry(date: .now, remaining: 25 * 60, label: "집중")   // 위젯 갤러리용
    }
    func getSnapshot(in context: Context, completion: @escaping (TimerEntry) -> Void) {
        completion(loadCurrent())
    }
    func getTimeline(in context: Context, completion: @escaping (Timeline<TimerEntry>) -> Void) {
        let entry = loadCurrent()
        let end = entry.date.addingTimeInterval(entry.remaining)
        completion(Timeline(entries: [entry], policy: .after(end)))   // 끝나면 다시 요청
    }
}
```

**크기별 뷰**

```swift
struct TimerWidgetView: View {
    @Environment(\.widgetFamily) var family
    @Environment(\.widgetRenderingMode) var renderingMode   // 풀컬러 / 악센트 / 비브런트
    let entry: TimerEntry

    var body: some View {
        switch family {
        case .accessoryInline:
            Text("\(entry.label) \(entry.remaining.formatted(.time(pattern: .minuteSecond)))")
        case .accessoryCircular:
            Gauge(value: entry.remaining, in: 0...(25 * 60)) {
                Image(systemName: "timer")
            }
            .gaugeStyle(.accessoryCircularCapacity)
        case .systemSmall:
            VStack(alignment: .leading) {
                Label(entry.label, systemImage: "timer")
                    .font(.caption).foregroundStyle(.secondary)
                Spacer()
                Text(entry.date.addingTimeInterval(entry.remaining), style: .timer)   // 시스템이 초마다 갱신
                    .font(.system(size: 40, weight: .light, design: .rounded))
                    .monospacedDigit()
                    .minimumScaleFactor(0.6)
            }
            .containerBackground(.fill.tertiary, for: .widget)   // iOS 17+ 필수
        default:
            MediumTimerView(entry: entry)
                .containerBackground(.fill.tertiary, for: .widget)
        }
    }
}
```

`Text(_:style: .timer)`는 위젯이 **갱신 없이** 초를 센다. 타이머, 카운트다운, 경과 시간은 반드시 이것으로 한다.

**상호작용 (iOS 17+)**

```swift
Button(intent: ToggleTimerIntent()) {              // App Intent로 앱을 열지 않고 실행
    Image(systemName: entry.isRunning ? "pause.fill" : "play.fill")
}
.buttonStyle(.plain)
```

**딥 링크**

```swift
.widgetURL(URL(string: "mytimer://session/\(entry.id)"))   // 탭하면 앱의 해당 화면으로
```

**라이브 액티비티**

```swift
struct TimerAttributes: ActivityAttributes {
    struct ContentState: Codable, Hashable { var endDate: Date; var label: String }
    var sessionID: String
}

// 위젯 확장에서
ActivityConfiguration(for: TimerAttributes.self) { context in
    // 잠금 화면 배너
    HStack {
        Label(context.state.label, systemImage: "timer")
        Spacer()
        Text(context.state.endDate, style: .timer).monospacedDigit().font(.title2)
    }
    .padding()
} dynamicIsland: { context in
    DynamicIsland {
        DynamicIslandExpandedRegion(.leading) { Label(context.state.label, systemImage: "timer") }
        DynamicIslandExpandedRegion(.trailing) { Text(context.state.endDate, style: .timer) }
        DynamicIslandExpandedRegion(.bottom) { Button(intent: StopTimerIntent()) { Text("중지") } }
    } compactLeading: {
        Image(systemName: "timer")
    } compactTrailing: {
        Text(context.state.endDate, style: .timer).monospacedDigit().frame(width: 44)
    } minimal: {
        Image(systemName: "timer")
    }
}
```

다이내믹 아일랜드의 compact 영역은 **아이콘 하나 + 숫자 하나**다. 그 이상은 들어가지 않는다.

## 자주 하는 실수

- **위젯을 자주 갱신하려 함**: 시스템은 하루 40~70회 정도만 갱신을 허용한다. 초 단위 정보는 `Text(style: .timer)`나 `.relative`로, 나머지는 타임라인을 미리 넘긴다
- **위젯 안에 카드 그리기**: 위젯이 이미 카드다. 배경은 `containerBackground`로
- **작은 위젯에 정보 다섯 개**: 하나만. 나머지는 중형으로
- **틴트 모드 무시**: iOS 18 틴트 홈 화면에서 위젯이 단색으로 렌더링된다. `widgetRenderingMode`를 읽어 사진은 숨기고 심볼과 텍스트만 남긴다. `.widgetAccentable()`로 강조할 요소를 표시한다
- **플레이스홀더 미제공**: 위젯 갤러리와 첫 로딩에 빈 화면이 보인다. `placeholder(in:)`에 그럴듯한 가짜 데이터를
- **라이브 액티비티를 끝내지 않음**: 작업이 끝났는데 아일랜드에 남아 있으면 사용자가 짜증 낸다. 끝나면 즉시 `end(dismissalPolicy:)`
- **위젯에서 네트워크 직접 호출**: 느리고 예산을 먹는다. 앱이 미리 저장한 데이터를 App Group으로 읽는다
- **접근성 생략**: 위젯도 VoiceOver가 읽는다. 게이지와 아이콘에 레이블을

## 체크리스트

- [ ] 각 크기에서 핵심 정보가 0.5초 안에 읽힌다
- [ ] 시간 정보는 `Text(style:)`로, 갱신 없이 흐른다
- [ ] `placeholder`, `snapshot`, `timeline` 셋 다 의미 있는 데이터를 준다
- [ ] 라이트 / 다크 / 틴트 모드에서 모두 읽힌다
- [ ] 탭하면 앱의 **해당 화면**으로 간다 (앱 첫 화면이 아니라)
- [ ] 잠금 화면 위젯은 단색으로도 의미가 통한다
- [ ] 라이브 액티비티는 시작과 끝이 있고, 끝나면 사라진다
- [ ] 위젯이 앱 없이도 마지막 데이터를 보여 준다 (App Group)
- [ ] VoiceOver로 위젯 내용이 읽힌다

## 함께 읽기

- [데이터 시각화: 정보의 시각적 표현과 해석]({% post_url design/technique/2024-08-25-dataVisualization %}) — 위젯용 요약 차트
- [비주얼 계층구조: 시각적 중요도의 체계화]({% post_url design/technique/2024-08-25-visualHierarchy %}) — 작은 공간의 위계
- [손목 위의 디자인: watchOS]({% post_url design/ios/2026-11-15-watchOSDesign %}) — 컴플리케이션은 위젯의 사촌
- [카드 기반 디자인: 정보의 모듈화]({% post_url design/technique/2024-08-25-cardBasedDesign %})
