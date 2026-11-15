---
title: "디자인 기법: UI/UX의 실행 도구"
date: 2024-08-25
last_modified_at: 2026-11-15
categories:
  - 디자인
tags:
  - 개념
  - iOS
toc: true
toc_sticky: true
toc_label: "목차"
---

사용자 인터페이스(UI)와 사용자 경험(UX)을 효과적으로 구현하기 위한 구체적인 방법론과 테크닉.
[디자인 스타일]({% post_url design/concept/2024-08-25-designStyle %})이 "어떤 분위기인가"라면, 디자인 기법은 "어떻게 만드는가"다.

기법 역시 웹에서 이름이 붙었지만 매체에 종속되지 않는다.
그리드, 여백, 타이포 계층, 마이크로인터랙션은 CSS로도 SwiftUI로도 구현되며, 원리는 같고 도구만 다르다.

## 주요 기법

<div class="mermaid">
mindmap
  root((디자인 기법))
    시각적 기법
      스켈레톤 UI
      카드 기반 디자인
      비주얼 계층구조
    상호작용 기법
      마이크로인터랙션
      제스처 기반 인터페이스
      애니메이션과 모션
    레이아웃 기법
      그리드 시스템
      화이트스페이스 활용
      반응형 디자인
    콘텐츠 기법
      타이포그래피 계층
      아이콘 디자인
      데이터 시각화
</div>

## 목적
1. **사용성 향상**: 직관적이고 효율적인 인터페이스 구현
2. **시각적 매력**: 심미적 만족도 증대
3. **정보 전달**: 효과적인 콘텐츠 구조화 및 표현
4. **브랜드 강화**: 일관된 사용자 경험을 통한 브랜드 가치 전달

## 적용 시 고려사항
- **사용자 중심**: 타겟 사용자의 니즈와 행동 패턴 고려
- **일관성**: 전체 제품에 걸쳐 통일된 적용
- **접근성**: 다양한 사용자를 위한 포용적 디자인
- **성능**: 기기 및 네트워크 환경에 따른 최적화
- **플랫폼 관례**: 사용자가 이미 몸에 익힌 OS의 패턴 존중

## 발전 과정

<div class="mermaid">
timeline
 title 디자인 기법의 발전
 2000 : 정적 웹 디자인
      : 기본적인 레이아웃과 상호작용
 2010 : 반응형 웹 디자인
      : 다양한 기기에 대응
 2014 : 선언형 UI의 태동
      : React, 컴포넌트 단위 사고
 2019 : SwiftUI 발표
      : 선언형 UI가 네이티브로
 2020 : 인터랙티브 UX
      : 고도화된 상호작용과 개인화
</div>

## 이 시리즈에서 다루는 기법

| 분류 | 기법 | iOS 쪽 핵심 도구 |
|---|---|---|
| 시각 | [스켈레톤 UI]({% post_url design/technique/2024-08-25-skeletonUI %}) | `.redacted(reason: .placeholder)` |
| 시각 | [카드 기반 디자인]({% post_url design/technique/2024-08-25-cardBasedDesign %}) | `GroupBox`, inset grouped `List` |
| 시각 | [비주얼 계층구조]({% post_url design/technique/2024-08-25-visualHierarchy %}) | 텍스트 스타일, `.primary/.secondary` |
| 상호작용 | [마이크로인터랙션]({% post_url design/technique/2024-08-25-microInteraction %}) | `sensoryFeedback`, `symbolEffect` |
| 상호작용 | [제스처 기반 인터페이스]({% post_url design/technique/2024-08-25-gestureBasedInterface %}) | `DragGesture`, `MagnifyGesture` |
| 상호작용 | [애니메이션과 모션]({% post_url design/technique/2024-08-25-animationMotion %}) | `withAnimation`, `matchedGeometryEffect` |
| 레이아웃 | [그리드 시스템]({% post_url design/technique/2024-08-25-gridSystem %}) | `LazyVGrid`, `Grid` |
| 레이아웃 | [화이트 스페이스]({% post_url design/technique/2024-08-25-whitespaceDesign %}) | `padding`, `Spacer`, safe area |
| 레이아웃 | [반응형 디자인]({% post_url design/technique/2024-08-25-responsiveDesign %}) | 사이즈 클래스, `ViewThatFits` |
| 콘텐츠 | [타이포그래피]({% post_url design/technique/2024-08-25-typography %}) | Dynamic Type, `Font.custom(_:relativeTo:)` |
| 콘텐츠 | [아이콘 디자인]({% post_url design/technique/2024-08-25-iconDesign %}) | SF Symbols |
| 콘텐츠 | [데이터 시각화]({% post_url design/technique/2024-08-25-dataVisualization %}) | Swift Charts |

## iOS에서는

### HIG가 말하는 것

HIG는 기법을 "Foundations(기초)", "Patterns(패턴)", "Components(컴포넌트)"의 세 층으로 나눠 설명한다.

- **Foundations**: 색상, 타이포그래피, 레이아웃, 아이콘, 모션, 접근성 등 모든 화면에 깔리는 기초
- **Patterns**: 로딩, 온보딩, 피드백, 드래그 앤 드롭처럼 상황에 따른 행동 방식
- **Components**: 버튼, 리스트, 탭 바, 시트처럼 실제로 가져다 쓰는 부품

웹의 "기법"은 대부분 Foundations와 Patterns에 대응한다.
차이는 iOS에서는 Components가 이미 기법을 내장하고 있다는 점이다.
예를 들어 `List`는 그리드, 여백, 타이포 계층, 스와이프 제스처를 기본으로 갖고 있어서 기법을 "적용"하기보다 "따르는" 쪽에 가깝다.

### SwiftUI로 구현하기

SwiftUI는 기법을 뷰 수정자(modifier)로 조합한다. 아래 한 화면에 시리즈의 기법 여섯 개가 들어 있다.

```swift
struct FeedView: View {
    @State private var items: [Item] = []
    @State private var isLoading = true

    var body: some View {
        ScrollView {
            LazyVGrid(columns: [GridItem(.adaptive(minimum: 160))], // 그리드
                      spacing: 16) {                                 // 여백
                ForEach(items) { item in
                    ItemCard(item: item)                             // 카드
                        .redacted(reason: isLoading ? .placeholder : []) // 스켈레톤
                }
            }
            .scenePadding()                                          // 반응형 여백
        }
        .animation(.default, value: isLoading)                       // 모션
    }
}
```

### 주의할 점

- 기법은 수단이다. "스켈레톤을 넣었다"가 아니라 "체감 대기 시간이 줄었다"로 결과를 측정한다
- SwiftUI 기본값은 HIG를 이미 따른다. 기본값을 덮어쓸 때마다 **왜** 덮어쓰는지 설명할 수 있어야 한다
- 여러 기법이 한 화면에 쌓이면 서로 충돌한다. 비주얼 계층구조를 기준으로 어떤 기법이 우선인지 정한다

## 웹과 iOS 대응표

| 웹 | iOS / SwiftUI |
|---|---|
| CSS Grid / Flexbox | `Grid`, `LazyVGrid`, `HStack`/`VStack` |
| `@media` 쿼리 | 사이즈 클래스, `ViewThatFits`, `GeometryReader` |
| CSS transition / animation | `withAnimation`, `.animation(_:value:)`, `.transition` |
| 터치 이벤트 핸들러 | `Gesture` 프로토콜 (`DragGesture` 등) |
| 아이콘 폰트 / SVG 스프라이트 | SF Symbols |
| D3, Chart.js | Swift Charts |

## 함께 읽기

- [디자인 스타일: UI/UX의 시각적 언어]({% post_url design/concept/2024-08-25-designStyle %})
- [디자인 시스템: 일관된 사용자 경험의 기반]({% post_url design/concept/2024-09-21-designSystem %})
