---
title: "디자인 시스템: 일관된 사용자 경험의 기반"
date: 2024-09-21
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

재사용 가능한 디자인 구성 요소와 그 사용 규칙을 한데 모은 체계.
디지털 제품을 만드는 모든 사람이 같은 기준으로 일관성 있게 화면을 구축할 수 있도록 돕는다.

[디자인 스타일]({% post_url design/concept/2024-08-25-designStyle %})이 "분위기", [디자인 기법]({% post_url design/concept/2024-08-25-disignTechniques %})이 "방법"이라면, 디자인 시스템은 그 둘을 **재사용 가능한 자산과 규칙**으로 굳힌 것이다.

## 왜 필요한가

- 디자이너마다 조금씩 다른 결과물에 일관성을 부여한다
- 반복해서 쓰는 컴포넌트를 정리해 새 화면을 만들 때의 수고를 줄인다
- 우선순위가 낮은 화면에 들어가는 시간을 줄여 핵심 경험에 집중할 수 있게 한다
- 하나의 결과물이 아닌 결과물의 집합이라서 디자이너와 개발자가 같은 언어로 협업할 수 있다
- 브랜드 규칙을 문서가 아닌 **작동하는 부품**으로 유지한다
- 서비스가 커질수록 디자인 결정의 일관성을 지켜 준다

## 일반적인 구성 요소

- 브랜드의 공유 가치와 목적
- 디자인 원칙
- 브랜드의 정체성과 언어(보이스 앤 톤)
- 스타일 가이드: 색상, 타이포그래피, 간격, 아이콘
- 컴포넌트와 패턴
- 코딩 규칙 같은 개발 요소
- 모범 사례와 이를 관리하는 정책(거버넌스)

<div class="mermaid">
flowchart TB
    A[디자인 원칙] --> B[디자인 토큰<br/>색 · 글꼴 · 간격 · 반경]
    B --> C[컴포넌트<br/>버튼 · 카드 · 입력]
    C --> D[패턴<br/>로딩 · 빈 화면 · 오류]
    D --> E[화면 / 제품]
    F[문서와 거버넌스] -.-> B
    F -.-> C
    F -.-> D
</div>

## 장점

- 일관성 향상
- 시간 절약
- 공통 기반 위에서의 협업
- 리스크 최소화 (한 곳을 고치면 전체에 반영)
- 확장성
- 브랜드 구축

## 대표적 사례

- **Apple Human Interface Guidelines**: 플랫폼 차원의 디자인 시스템. 원칙부터 컴포넌트까지 모두 담고 있다
- **Google Material Design**: 토큰, 컴포넌트, 모션 규칙과 코드 라이브러리를 함께 제공
- **IBM Carbon, Atlassian Design System, Shopify Polaris**: 기업 제품군의 일관성을 위한 시스템
- **Apple Design Resources**: Figma와 Sketch용 공식 UI 키트. HIG를 디자인 도구 안에서 바로 쓸 수 있게 한다

## iOS에서는

### HIG가 말하는 것

iOS 앱을 만든다면 디자인 시스템을 **처음부터 만들지 않는다**. 이미 HIG라는 플랫폼 시스템 위에 서 있기 때문이다.
HIG가 제공하는 것들을 그대로 쓰고, 그 위에 앱 고유의 얇은 층을 얹는 것이 iOS식 디자인 시스템이다.

HIG가 이미 정해 둔 것:

- **색상**: 라이트/다크, 고대비에 자동 대응하는 의미론적 색상 (`label`, `systemBackground` 등)과 시스템 색상 (`systemBlue` 등)
- **타이포그래피**: SF Pro 서체와 11단계 Dynamic Type 텍스트 스타일
- **아이콘**: 6,000개가 넘는 SF Symbols. 글꼴과 함께 굵기와 크기가 맞춰진다
- **레이아웃**: 세이프 에어리어, 레이아웃 마진, 44×44pt 최소 터치 영역
- **컴포넌트**: 내비게이션 바, 탭 바, 리스트, 시트, 알림 등 모든 기본 부품
- **모션**: 화면 전환과 피드백의 기본 타이밍

앱이 추가로 정하는 것은 보통 브랜드 색, 특수 컴포넌트, 그리고 "우리 앱은 이럴 때 이렇게 한다"는 패턴 규칙 정도다.

### SwiftUI로 구현하기

SwiftUI에서 디자인 시스템은 네 가지 도구로 코드가 된다.

**1. 토큰은 에셋 카탈로그와 확장으로**

```swift
// Assets.xcassets에 "Brand" 색상 세트를 만들고 라이트/다크/고대비 값을 넣으면
// Xcode 15부터 Color.brand 심볼이 자동 생성된다
extension CGFloat {
    static let spacingS: CGFloat = 8
    static let spacingM: CGFloat = 16
    static let radiusCard: CGFloat = 16
}
```

**2. 컴포넌트는 스타일 프로토콜로**

```swift
struct PrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .font(.headline)
            .padding(.vertical, .spacingS)
            .padding(.horizontal, .spacingM)
            .background(.tint, in: Capsule())
            .foregroundStyle(.white)
            .opacity(configuration.isPressed ? 0.7 : 1)
    }
}

extension ButtonStyle where Self == PrimaryButtonStyle {
    static var primary: PrimaryButtonStyle { .init() }
}

// 사용: Button("저장") { }.buttonStyle(.primary)
```

**3. 전역 설정은 환경(Environment)으로**

```swift
ContentView()
    .tint(.brand)                         // 모든 컨트롤의 강조색
    .fontDesign(.rounded)                 // 앱 전체 서체 느낌
    .environment(\.locale, .current)
```

**4. 패턴은 뷰 수정자로**

```swift
extension View {
    func cardStyle() -> some View {
        self.padding(.spacingM)
            .background(.background, in: RoundedRectangle(cornerRadius: .radiusCard))
            .shadow(color: .black.opacity(0.06), radius: 8, y: 2)
    }
}
```

이 네 층을 하나의 Swift Package로 묶으면, 여러 앱이 같은 시스템을 공유할 수 있다.

### 주의할 점

- 시스템 값을 하드코딩된 값으로 덮어쓰지 않는다. `Color.black` 대신 `.primary`, `size: 17` 대신 `.body`를 쓴다. 그래야 다크 모드와 Dynamic Type이 살아 있다
- 컴포넌트는 **상태**를 모두 가져야 한다. 기본, 눌림, 비활성, 로딩, 오류. 하나라도 빠지면 각 화면이 알아서 만들기 시작하고 시스템이 무너진다
- 문서 없는 디자인 시스템은 라이브러리일 뿐이다. 언제 쓰고 언제 쓰지 않는지가 코드 옆에 있어야 한다
- Xcode Previews를 디자인 시스템의 **살아 있는 카탈로그**로 쓴다. 컴포넌트마다 라이트/다크, 큰 글자, 고대비 프리뷰를 둔다

## 웹과 iOS 대응표

| 웹 | iOS / SwiftUI |
|---|---|
| CSS 커스텀 프로퍼티 (디자인 토큰) | 에셋 카탈로그 색상/이미지 세트, 상수 확장 |
| 컴포넌트 라이브러리 (React 등) | `ButtonStyle`, `LabelStyle`, `ToggleStyle`, 커스텀 뷰 |
| 테마 프로바이더 / Context | `Environment`, `.tint`, `.fontDesign` |
| Storybook | Xcode Previews, `#Preview` 매크로 |
| npm 패키지로 배포 | Swift Package로 배포 |
| 브라우저 기본 스타일 | HIG + 시스템 컨트롤 기본 스타일 |

## 함께 읽기

- [디자인 스타일: UI/UX의 시각적 언어]({% post_url design/concept/2024-08-25-designStyle %})
- [디자인 기법: UI/UX의 실행 도구]({% post_url design/concept/2024-08-25-disignTechniques %})
- [타이포그래피: 글자를 통한 효과적인 커뮤니케이션]({% post_url design/technique/2024-08-25-typography %})
- [아이콘 디자인: 시각적 언어의 핵심 요소]({% post_url design/technique/2024-08-25-iconDesign %})
