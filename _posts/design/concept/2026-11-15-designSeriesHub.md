---
title: "디자인 시리즈 읽기 안내: 웹 디자인론을 iOS로 옮겨 읽기"
date: 2026-11-15 00:08:00 +0900
categories:
  - 디자인
tags:
  - 개념
  - iOS
  - 시리즈
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "디자인 스타일 9편, 기법 12편, 개념 3편, iOS 전용 7편. 어디서부터 어떻게 읽으면 좋은지 정리한 입구 글."
---

이 블로그의 디자인 글은 한 가지 전제 위에 있다.

> 웹에서 이름 붙은 디자인 이론은 매체가 아니라 사람의 눈과 손에 대한 이론이다. 그래서 iOS 앱에도 그대로 적용된다. 다만 도구가 다르다.

그리드, 여백, 타이포그래피 계층, 마이크로인터랙션 같은 개념은 CSS와 JavaScript로 정리되었지만, 원리는 SwiftUI에서도 같다.
웹이 `@media` 쿼리로 푸는 문제를 iOS는 사이즈 클래스로 풀고, `backdrop-filter`로 만들던 유리는 `Material`로 만든다.
이 시리즈는 **웹 일반론을 유지한 채, 각 글에 iOS·SwiftUI·HIG(Human Interface Guidelines)의 대응을 붙인** 글 모음이다.

## 글의 구조

2024년에 쓴 24편은 모두 같은 틀을 따른다.

1. **웹 일반론**: 특징, 역사적 배경, 주요 요소, 장단점, 적용 사례
2. **iOS에서는**: HIG가 말하는 것 → SwiftUI로 구현하기 → 주의할 점
3. **웹과 iOS 대응표**: CSS/JS 개념과 SwiftUI API의 1:1 매핑
4. **함께 읽기**: 관련 글 링크

2026년에 추가한 iOS 전용 7편은 웹에 대응물이 없는 주제를 다루며, HIG 원칙 → 선택 기준 → SwiftUI 구현 → 자주 하는 실수 → 체크리스트의 틀을 쓴다.

## 읽는 순서

### 처음이라면

1. [디자인 스타일: UI/UX의 시각적 언어]({% post_url design/concept/2024-08-25-designStyle %}) — 스타일이 무엇인지, 어떤 스타일들이 있는지
2. [디자인 기법: UI/UX의 실행 도구]({% post_url design/concept/2024-08-25-disignTechniques %}) — 기법이 무엇인지, 어떤 기법들이 있는지
3. [디자인 시스템: 일관된 사용자 경험의 기반]({% post_url design/concept/2024-09-21-designSystem %}) — 스타일과 기법을 재사용 가능한 자산으로 굳히는 법

### iOS 앱을 만들고 있다면

바로 쓸 수 있는 순서로 골랐다.

1. [비주얼 계층구조]({% post_url design/technique/2024-08-25-visualHierarchy %}) — 텍스트 스타일과 의미론적 색으로 위계 만들기. 모든 화면의 기본
2. [타이포그래피]({% post_url design/technique/2024-08-25-typography %}) — Dynamic Type을 따르는 법
3. [화이트 스페이스]({% post_url design/technique/2024-08-25-whitespaceDesign %}) — 시스템 여백에 맞추기
4. [반응형 디자인]({% post_url design/technique/2024-08-25-responsiveDesign %}) — iPhone과 iPad, 큰 글자 설정에 적응하기
5. [접근성: 모두를 위한 기본값]({% post_url design/ios/2026-11-15-iosAccessibility %}) — 위 네 가지를 왜 지켜야 하는지
6. [마이크로인터랙션]({% post_url design/technique/2024-08-25-microInteraction %}) — 햅틱과 심볼 애니메이션
7. [iOS 내비게이션]({% post_url design/ios/2026-11-15-iosNavigation %}) — 앱의 뼈대 고르기

### 스타일을 고르고 있다면

[디자인 스타일 개념 글]({% post_url design/concept/2024-08-25-designStyle %})의 표를 먼저 보고, 끌리는 것부터 읽는다.
iOS에서 현실적인 선택지는 [플랫]({% post_url design/style/2024-08-25-flatDesign %})을 기본으로 [글라스모피즘]({% post_url design/style/2024-08-25-glassmorphism %})의 재질을 얹는 것이다. 나머지는 그 선택의 이유를 설명한다.

## 전체 지도

### 개념 (3편)

| 글 | 한 줄 |
|---|---|
| [디자인 스타일]({% post_url design/concept/2024-08-25-designStyle %}) | 분위기. 9가지 스타일의 지도 |
| [디자인 기법]({% post_url design/concept/2024-08-25-disignTechniques %}) | 방법. 12가지 기법의 지도 |
| [디자인 시스템]({% post_url design/concept/2024-09-21-designSystem %}) | 자산. HIG 위에 얇은 층을 얹는 법 |

### 디자인 스타일 (9편)

| 글 | iOS에서의 위치 | 핵심 SwiftUI |
|---|---|---|
| [스큐어모피즘]({% post_url design/style/2024-08-25-skeuomorphism %}) | iOS 6 이전. 지금은 "작동을 설명하는 모방"만 | `Gradient`, `.shadow`, 햅틱 |
| [플랫 디자인]({% post_url design/style/2024-08-25-flatDesign %}) | iOS 7 이후의 기본. 깊이는 레이어로 | `.borderedProminent`, 의미론적 색 |
| [머티리얼 디자인]({% post_url design/style/2024-08-25-materialDesign %}) | Android의 언어. iOS로 번역하는 표 | `TabView`, `ToolbarItem` |
| [미니멀리즘]({% post_url design/style/2024-08-25-minimalism %}) | HIG의 기본 태도. 점진적 공개 | `Menu`, `ContentUnavailableView` |
| [뉴모피즘]({% post_url design/style/2024-08-25-neumorphism %}) | 접근성과 충돌. 컨트롤 장식으로만 | `.shadow` 두 겹, `colorSchemeContrast` |
| [글라스모피즘]({% post_url design/style/2024-08-25-glassmorphism %}) | 시스템 Material, iOS 26 Liquid Glass | `.regularMaterial`, `glassEffect` |
| [타이포그래피 중심]({% post_url design/style/2024-08-25-typographyCentricDesign %}) | SF와 Dynamic Type이 곧 이 스타일 | `@ScaledMetric`, `design: .serif` |
| [비주얼 리치]({% post_url design/style/2024-08-25-visuallyRich %}) | 콘텐츠는 풍부하게, UI는 얇게 | `scrollTransition`, Metal 셰이더 |
| [레트로/빈티지]({% post_url design/style/2024-08-25-retro_vintage %}) | 모양은 바꿔도 행동은 그대로 | `.interpolation(.none)`, `.monospaced` |

### 디자인 기법 (12편)

| 분류 | 글 | 핵심 SwiftUI |
|---|---|---|
| 시각 | [스켈레톤 UI]({% post_url design/technique/2024-08-25-skeletonUI %}) | `.redacted(reason: .placeholder)` |
| 시각 | [카드 기반 디자인]({% post_url design/technique/2024-08-25-cardBasedDesign %}) | `.insetGrouped`, `RoundedRectangle(style: .continuous)` |
| 시각 | [비주얼 계층구조]({% post_url design/technique/2024-08-25-visualHierarchy %}) | 텍스트 스타일, `.primary/.secondary/.tertiary` |
| 상호작용 | [마이크로인터랙션]({% post_url design/technique/2024-08-25-microInteraction %}) | `sensoryFeedback`, `symbolEffect` |
| 상호작용 | [제스처 기반 인터페이스]({% post_url design/technique/2024-08-25-gestureBasedInterface %}) | `DragGesture`, `.swipeActions`, `accessibilityAction` |
| 상호작용 | [애니메이션과 모션]({% post_url design/technique/2024-08-25-animationMotion %}) | `withAnimation(.spring)`, `matchedGeometryEffect` |
| 레이아웃 | [그리드 시스템]({% post_url design/technique/2024-08-25-gridSystem %}) | `LazyVGrid(.adaptive)`, `Grid` |
| 레이아웃 | [화이트 스페이스]({% post_url design/technique/2024-08-25-whitespaceDesign %}) | `.scenePadding`, `.contentMargins` |
| 레이아웃 | [반응형 디자인]({% post_url design/technique/2024-08-25-responsiveDesign %}) | 사이즈 클래스, `ViewThatFits` |
| 콘텐츠 | [타이포그래피]({% post_url design/technique/2024-08-25-typography %}) | Dynamic Type, `Font.custom(_:relativeTo:)` |
| 콘텐츠 | [아이콘 디자인]({% post_url design/technique/2024-08-25-iconDesign %}) | SF Symbols, `symbolRenderingMode` |
| 콘텐츠 | [데이터 시각화]({% post_url design/technique/2024-08-25-dataVisualization %}) | Swift Charts, Audio Graph |

### iOS 전용 (7편)

웹에 직접 대응하는 개념이 없거나, iOS에서 전혀 다르게 풀리는 주제들.

| 글 | 다루는 것 |
|---|---|
| [iOS 내비게이션: 탭, 스택, 스플릿 뷰]({% post_url design/ios/2026-11-15-iosNavigation %}) | 앱의 뼈대를 고르는 기준, `NavigationStack`과 `NavigationSplitView` |
| [모달의 위계: 알림, 시트, 팝오버]({% post_url design/ios/2026-11-15-iosModality %}) | 언제 무엇으로 끼어들 것인가 |
| [접근성: 모두를 위한 기본값]({% post_url design/ios/2026-11-15-iosAccessibility %}) | VoiceOver, Dynamic Type, 대비, 모션, 감사 방법 |
| [색상과 다크 모드: 의미론적 색 체계]({% post_url design/ios/2026-11-15-iosColorDarkMode %}) | 시스템 색, 에셋 카탈로그, `tint`, 고대비 |
| [위젯과 라이브 액티비티: 한눈에 보는 디자인]({% post_url design/ios/2026-11-15-iosWidgets %}) | 홈 화면과 잠금 화면에서의 정보 디자인 |
| [햅틱: 손끝으로 전하는 피드백]({% post_url design/ios/2026-11-15-iosHaptics %}) | 촉각 언어의 문법과 절제 |
| [손목 위의 디자인: watchOS]({% post_url design/ios/2026-11-15-watchOSDesign %}) | 디지털 크라운, 컴플리케이션, 2초 안에 끝나는 상호작용 |

## 번역의 원칙

24편을 쓰면서 반복해서 나온 "웹 → iOS" 번역 규칙을 모으면 다섯 가지다.

1. **값 대신 이름을 쓴다**: `17px` 대신 `.body`, `#333` 대신 `.primary`, `16px` 여백 대신 `.scenePadding()`. 이름을 쓰면 다크 모드, Dynamic Type, 고대비가 따라온다
2. **브레이크포인트 대신 적응**: 화면 폭을 재지 않는다. `.adaptive`, `ViewThatFits`, 사이즈 클래스가 알아서 맞춘다
3. **시스템 컴포넌트 먼저**: `List`, `Toggle`, `TabView`에는 햅틱·제스처·접근성이 이미 들어 있다. 직접 만들면 그것들을 다시 만들어야 한다
4. **접근성 설정 네 가지를 항상 본다**: 동작 줄이기, 투명도 줄이기, 대비 높이기, 글자 크기. 스타일이 강할수록 이 설정들에 대한 대체 모습이 필요하다
5. **구조는 플랫폼, 개성은 색과 톤**: 내비게이션 구조와 제스처는 iOS 관례를 따르고, 브랜드는 `tint`, 서체 느낌, 모션의 성격으로 표현한다

## 자주 쓰는 대응표 모음

시리즈 전체에서 가장 자주 등장한 대응만 추렸다.

| 웹 | iOS / SwiftUI |
|---|---|
| `@media (min-width)` | `horizontalSizeClass`, `ViewThatFits` |
| `rem` + 브라우저 글자 설정 | Dynamic Type 텍스트 스타일 |
| CSS 변수 (디자인 토큰) | 에셋 카탈로그 색상 세트, 상수 확장 |
| `prefers-color-scheme` | `colorScheme`, 의미론적 색 |
| `prefers-reduced-motion` | `accessibilityReduceMotion` |
| `backdrop-filter: blur()` | `Material`, `glassEffect` |
| CSS transition | `.animation(_:value:)`, `withAnimation` |
| 아이콘 폰트 / SVG | SF Symbols |
| `aria-label` | `accessibilityLabel`, `Label` |
| Chart.js / D3 | Swift Charts |
| Storybook | Xcode Previews |
