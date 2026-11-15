---
title: "디자인 스타일: UI/UX의 시각적 언어"
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

사용자 인터페이스(UI)와 사용자 경험(UX)을 구성하는 일관된 시각적 접근 방식.
단순한 미적 선호를 넘어서는 종합적인 디자인 철학과 방법론.

이 시리즈에서 다루는 스타일은 대부분 웹에서 이름이 붙었지만, 같은 원리가 iOS 앱에서도 그대로 쓰인다.
웹이 CSS로 표현하는 것을 iOS는 HIG(Human Interface Guidelines)와 SwiftUI로 표현한다는 차이만 있을 뿐이다.

## 구성 요소

### 시각적 요소
- **색상**: 브랜드 아이덴티티를 반영하는 색상 팔레트
- **타이포그래피**: 가독성과 브랜드 개성을 고려한 폰트
- **아이콘**: 일관된 스타일의 그래픽 심볼
- **이미지**: 제품 특성에 맞는 시각적 콘텐츠

### 레이아웃
- **그리드 시스템**: 콘텐츠의 체계적 배치를 위한 구조
- **공간 활용**: 여백(화이트 스페이스)의 전략적 사용

### 상호작용
- **애니메이션**: 사용자 행동에 대한 시각적 피드백
- **전환 효과**: 화면 간 이동 시 적용되는 시각적 변화

### 질감과 깊이
- **평면성**: 2D 디자인을 통한 단순함 강조
- **입체감**: 그림자와 레이어, 재질(material)을 통한 깊이감 표현

## 목적

1. **일관성**: 모든 터치포인트에서의 통일된 사용자 경험
2. **브랜드 강화**: 시각적 요소를 통한 브랜드 가치 전달
3. **감성 전달**: 제품 성격에 맞는 특정 분위기 조성
4. **기능성 향상**: 직관적 인터페이스를 통한 사용성 개선

## 영향 요인

- **기술 트렌드**: 새로운 기술의 디자인 적용 (고해상도 디스플레이, GPU 블러 등)
- **문화적 맥락**: 지역별 선호도와 문화적 특성
- **사용자 선호도**: 사용자 리서치를 통해 파악된 선호 스타일
- **플랫폼 특성**: 각 OS가 제시하는 디자인 언어와 기기 환경
- **기업 철학**: 회사의 미션과 비전의 디자인적 표현

## 진화

시간에 따라 변화하는 디자인 접근 방식. 기술 발전, 사용자 기대 변화, 새로운 디자인 철학 등에 영향을 받음.
흥미로운 점은 큰 전환점마다 Apple의 OS가 기준점이 되었다는 것이다.

<div class="mermaid">
timeline
    title 디자인 스타일의 진화
    2000 : 스큐어모피즘
         : 현실 세계 모방 (iOS 6까지)
    2013 : 플랫 디자인
         : 단순화 (iOS 7)
    2020 : 글라스모피즘 / 뉴모피즘
         : 반투명 재질과 부드러운 입체감 (macOS Big Sur)
    2025 : Liquid Glass
         : 빛을 굴절시키는 동적 재질 (iOS 26)
</div>

## 이 시리즈에서 다루는 스타일

| 스타일 | 한 줄 요약 | iOS에서의 위치 |
|---|---|---|
| [스큐어모피즘]({% post_url design/style/2024-08-25-skeuomorphism %}) | 현실 물체를 모방 | iOS 6 이전의 기본 언어 |
| [플랫 디자인]({% post_url design/style/2024-08-25-flatDesign %}) | 장식을 걷어낸 2D | iOS 7 이후의 기본 언어 |
| [머티리얼 디자인]({% post_url design/style/2024-08-25-materialDesign %}) | 종이와 잉크의 물리 메타포 | Android의 언어, iOS에선 번역이 필요 |
| [미니멀리즘]({% post_url design/style/2024-08-25-minimalism %}) | 본질만 남기기 | HIG의 기본 태도 |
| [뉴모피즘]({% post_url design/style/2024-08-25-neumorphism %}) | 배경에서 솟아오른 부드러운 입체 | 접근성 때문에 제한적 |
| [글라스모피즘]({% post_url design/style/2024-08-25-glassmorphism %}) | 반투명 유리 재질 | 시스템 Material, Liquid Glass |
| [타이포그래피 중심]({% post_url design/style/2024-08-25-typographyCentricDesign %}) | 글자가 곧 디자인 | SF Pro와 Dynamic Type |
| [비주얼 리치]({% post_url design/style/2024-08-25-visuallyRich %}) | 풍부한 이미지와 모션 | 성능과 접근성이 관건 |
| [레트로/빈티지]({% post_url design/style/2024-08-25-retro_vintage %}) | 과거 인터페이스의 재해석 | 브랜딩과 게임에서 |

## iOS에서는

### HIG가 말하는 것

HIG는 특정 "스타일"을 강요하지 않는다. 대신 플랫폼이 공유하는 원칙을 제시한다.
iOS 7과 함께 제시된 세 가지 주제가 지금도 iOS 디자인 스타일의 뼈대다.

- **명료성(Clarity)**: 텍스트는 어떤 크기에서도 읽히고, 아이콘은 정확하며, 장식은 기능을 가리지 않는다
- **존중(Deference)**: UI는 콘텐츠를 돋보이게 하는 배경이 된다. 반투명 재질과 블러가 콘텐츠를 비춘다
- **깊이(Depth)**: 레이어와 모션이 계층을 전달하고, 화면 사이의 관계를 이해하게 한다

여기에 2025년 iOS 26의 Liquid Glass가 "재질"이라는 네 번째 축을 더했다.
컨트롤이 아래 콘텐츠의 빛과 색을 굴절시키며, 스타일이 정적인 그림이 아니라 동적인 재질로 바뀐 셈이다.

### SwiftUI로 구현하기

SwiftUI에서 스타일은 개별 뷰의 색을 덧바르는 것이 아니라, 시스템이 제공하는 의미론적(semantic) 값을 쓰는 일에서 시작한다.

```swift
VStack(alignment: .leading, spacing: 8) {
    Text("오늘의 할 일")
        .font(.title2.weight(.semibold))   // Dynamic Type 텍스트 스타일
        .foregroundStyle(.primary)          // 라이트/다크 자동 대응
    Text("3개 남음")
        .font(.subheadline)
        .foregroundStyle(.secondary)
}
.padding()
.background(.regularMaterial,               // 시스템 반투명 재질
            in: RoundedRectangle(cornerRadius: 16))
.tint(.indigo)                               // 브랜드 색 한 곳에서 지정
```

브랜드 스타일은 `tint`, 커스텀 `ButtonStyle`, 에셋 카탈로그의 색상 세트로 얹는다.
시스템 값 위에 브랜드를 얹으면 다크 모드, 고대비, Dynamic Type을 공짜로 얻는다.

### 주의할 점

- 웹에서 유행하는 스타일을 그대로 옮기기 전에, 그 스타일이 **시스템 컨트롤의 기대 동작**을 깨뜨리지 않는지 먼저 확인한다
- 스타일이 강해질수록 **접근성 설정**(투명도 줄이기, 대비 높이기, 동작 줄이기)에 대응하는 코드가 따라와야 한다
- App Store 심사와 사용자 모두 "iOS 앱처럼 느껴지는가"를 본다. 차별화는 색, 타이포, 모션 톤에서 하고, 내비게이션 구조는 플랫폼을 따른다

## 웹과 iOS 대응표

| 웹 | iOS / SwiftUI |
|---|---|
| CSS 변수로 정의한 디자인 토큰 | 에셋 카탈로그 색상 세트, `Font` 텍스트 스타일 |
| `prefers-color-scheme` | `colorScheme` 환경값, 의미론적 색상 |
| `backdrop-filter: blur()` | `Material` (`.regularMaterial` 등), `glassEffect` |
| 브랜드 가이드 문서 | HIG + 앱 자체 디자인 시스템 |
| 프레임워크 테마(Bootstrap 등) | 시스템 컨트롤 스타일 + `ButtonStyle`, `LabelStyle` |

## 함께 읽기

- [디자인 기법: UI/UX의 실행 도구]({% post_url design/concept/2024-08-25-disignTechniques %})
- [디자인 시스템: 일관된 사용자 경험의 기반]({% post_url design/concept/2024-09-21-designSystem %})
