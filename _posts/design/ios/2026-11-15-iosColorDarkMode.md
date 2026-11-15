---
title: "색상과 다크 모드: 의미론적 색 체계"
date: 2026-11-15 00:04:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - iOS
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "iOS의 색은 값이 아니라 역할로 정의된다. 시스템 색, 의미론적 색, tint, 에셋 카탈로그, 그리고 다크 모드와 고대비가 공짜로 따라오는 이유."
---

웹에서 색은 `#1C1C1E` 같은 값이다. iOS에서 색은 **역할**이다. "레이블 색", "배경 색", "구분선 색"처럼 역할로 정의하면, 라이트/다크 모드와 고대비 설정에 맞는 실제 값은 시스템이 고른다.

이 글은 iOS 색 체계의 세 층(시스템 색, 의미론적 색, 앱 색)과, 그 위에서 다크 모드를 설계하는 법을 다룬다.

## HIG가 말하는 것

- **색은 의미를 전달하되, 유일한 수단이 아니어야 한다**: 빨강은 삭제, 파랑은 링크. 하지만 색각 이상 사용자를 위해 아이콘이나 텍스트가 함께 있어야 한다
- **시스템 색을 쓰면 적응은 자동**: 시스템 색은 라이트/다크, 고대비, 투명도 줄이기에 맞는 변형을 모두 갖고 있다
- **앱 강조색(tint)은 하나**: 앱의 상호작용 요소는 한 가지 색으로 통일한다. 사용자는 "이 색이면 누를 수 있다"고 배운다
- **다크 모드는 반전이 아니다**: 라이트 모드의 그림자가 다크 모드에서는 밝기 차이로 바뀐다. 겹쳐 올라갈수록 밝아진다(elevation)
- **콘텐츠의 색은 건드리지 않는다**: 사진, 사용자가 고른 색은 다크 모드에서도 그대로

## 세 층의 색

### 1. 시스템 색 (System Colors)

`systemBlue`, `systemRed`, `systemGreen` 같은 13가지 기본색. 라이트/다크 모드에서 **다른 값**을 가진다. 다크 모드의 systemBlue는 조금 더 밝고 채도가 높다. 어두운 배경에서 같은 인상을 주기 위해서다.

```swift
Color.blue      // SwiftUI의 .blue는 systemBlue. 모드에 따라 값이 바뀐다
Color(.systemBlue)
```

### 2. 의미론적 색 (Semantic Colors)

역할로 이름 붙은 색. UI의 뼈대를 이루며, 대부분의 화면은 이것만으로 충분하다.

| 역할 | 색 | 쓰임 |
|---|---|---|
| 전경 | `label` / `secondaryLabel` / `tertiaryLabel` / `quaternaryLabel` | 텍스트와 아이콘의 4단계 위계 |
| 배경 | `systemBackground` / `secondarySystemBackground` / `tertiarySystemBackground` | 겹쳐 올라가는 레이어 |
| 그룹 배경 | `systemGroupedBackground` / `secondary...` / `tertiary...` | 인셋 그룹 리스트용. 바닥이 회색, 카드가 흰색 |
| 채움 | `systemFill` ~ `quaternarySystemFill` | 입력 필드 배경, 슬라이더 트랙 |
| 구분선 | `separator` / `opaqueSeparator` | 리스트 구분선 |
| 링크 | `link` | 탭 가능한 텍스트 |
| 회색 | `systemGray` ~ `systemGray6` | 6단계 회색. 다크 모드에서는 순서가 뒤집혀 보인다 |

SwiftUI에서는 `.primary`, `.secondary`, `.tertiary`, `.quaternary`가 전경 4단계에 해당하고, 배경은 `Color(.systemBackground)`처럼 UIKit 이름을 쓴다.

### 3. 앱 색 (Custom Colors)

브랜드 색은 에셋 카탈로그에 **색상 세트**로 넣는다. 라이트/다크, 고대비 변형을 각각 지정할 수 있다.

```
Assets.xcassets/
  Brand.colorset/      Any · Dark · High Contrast · Dark High Contrast
  BrandSurface.colorset/
```

Xcode 15부터 `Color.brand`처럼 심볼이 자동 생성된다.

## 다크 모드 설계

### 깊이의 방향이 바뀐다

라이트 모드에서는 카드가 그림자로 떠 보인다. 다크 모드에서는 그림자가 보이지 않으므로 **위로 올라올수록 밝아지는** 방식으로 깊이를 표현한다.

<div class="mermaid">
flowchart LR
    subgraph 라이트
      L1[systemBackground<br/>흰색] --> L2[카드: 흰색 + 그림자]
    end
    subgraph 다크
      D1[systemBackground<br/>검정] --> D2[secondarySystemBackground<br/>어두운 회색 카드] --> D3[tertiary...<br/>더 밝은 회색]
    end
</div>

`secondarySystemBackground`를 카드 배경으로 쓰면 이 전환이 자동이다. 라이트에서는 연회색 카드, 다크에서는 배경보다 밝은 회색 카드가 된다.

### 순수 검정 vs 어두운 회색

- iOS 기본 `systemBackground`의 다크 값은 **순수 검정(#000)**이다. OLED에서 전력을 아끼고 대비가 선명하다
- 많은 앱이 어두운 회색(#1C1C1E 등)을 선호한다. 눈이 덜 피로하고 레이어 표현이 쉽다
- 어느 쪽이든 **그룹 배경 체계**를 따르면 된다. `systemGroupedBackground`는 다크에서 검정, 그 위의 `secondarySystemGroupedBackground`는 #1C1C1E다

### 채도를 낮춘다

라이트 모드의 선명한 브랜드 색은 검정 위에서 눈을 찌른다. 다크 변형은 보통 **밝기를 올리고 채도를 약간 낮춘다**. 시스템 색이 그렇게 되어 있으니 참고한다. 예: systemBlue 라이트 `#007AFF` → 다크 `#0A84FF`.

### 흰 텍스트는 완전한 흰색이 아니다

다크 모드의 `label`은 흰색이지만 `secondaryLabel`은 흰색의 60% 불투명도다. 불투명도로 위계를 표현하면 어떤 배경 위에서도 자연스럽다.

## SwiftUI로 구현하기

**의미론적 색만으로 만든 화면**

```swift
struct ProfileCard: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text("박세진").font(.headline)                       // label
            Text("iOS 개발자").font(.subheadline)
                .foregroundStyle(.secondary)                      // secondaryLabel
            Divider()                                             // separator
            HStack {
                Label("서울", systemImage: "mappin")
                Spacer()
                Text("가입 2024년").foregroundStyle(.tertiary)   // tertiaryLabel
            }
            .font(.caption)
        }
        .padding()
        .background(Color(.secondarySystemGroupedBackground),    // 카드
                    in: RoundedRectangle(cornerRadius: 16))
    }
}
// 바깥: .background(Color(.systemGroupedBackground))
```

색 값을 하나도 쓰지 않았다. 라이트/다크/고대비에서 모두 올바르게 보인다.

**앱 강조색**

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            RootView().tint(.brand)    // 버튼, 링크, 토글, 탭 바 선택 등 모든 상호작용 색
        }
    }
}
```

`tint` 하나로 앱 전체의 "누를 수 있는 것"의 색이 정해진다. 개별 뷰에서 `.foregroundStyle(.blue)`를 쓰지 않는다.

**현재 모드 읽기와 고정**

```swift
@Environment(\.colorScheme) var scheme

Image(scheme == .dark ? "logo_dark" : "logo_light")   // 이미지는 에셋 카탈로그 Dark 변형이 더 낫다

// 특정 화면만 다크로 (카메라, 미디어 플레이어)
CameraView().preferredColorScheme(.dark)
```

**고대비 대응이 필요한 커스텀 색**

에셋 카탈로그에서 High Contrast 변형을 채우면 끝. 코드로 분기해야 한다면:

```swift
@Environment(\.colorSchemeContrast) var contrast
let border = contrast == .increased ? Color.primary : Color(.separator)
```

**Previews로 네 가지 조합 한 번에**

```swift
#Preview("라이트") { ProfileCard() }
#Preview("다크") { ProfileCard().preferredColorScheme(.dark) }
#Preview("다크 고대비") {
    ProfileCard().preferredColorScheme(.dark)
        .environment(\.colorSchemeContrast, .increased)
}
```

## 자주 하는 실수

- **`Color.black` / `Color.white`**: 다크 모드에서 반전되지 않는다. 텍스트는 `.primary`, 배경은 `Color(.systemBackground)`
- **`Color(red:green:blue:)` 하드코딩**: 에셋 카탈로그로 옮긴다. 다크 변형과 고대비 변형이 생기고, 디자이너와 같은 이름으로 소통할 수 있다
- **다크 모드에서 그림자에 의존**: 보이지 않는다. 배경 밝기 차이로 레이어를 표현한다
- **`.opacity(0.5)`로 보조 텍스트**: `.secondary`를 쓴다. 불투명도는 배경에 따라 대비가 들쭉날쭉하다
- **이미지에 글자나 UI 포함**: 다크 모드에서 어색하다. 이미지는 콘텐츠만, 글자는 `Text`
- **강조색 남발**: 파란 글자가 세 개면 어느 것이 버튼인지 모른다. `tint`는 상호작용 요소에만
- **다크 모드를 나중에**: 처음부터 의미론적 색으로 만들면 다크 모드는 추가 작업이 거의 없다. 나중에 하면 모든 색을 다시 본다

## 체크리스트

- [ ] `Color.black`, `Color.white`, RGB 하드코딩이 없다
- [ ] 텍스트는 `.primary` / `.secondary` / `.tertiary`, 배경은 시스템 배경 색 체계
- [ ] 브랜드 색은 에셋 카탈로그에 라이트/다크/고대비 변형과 함께 있다
- [ ] `tint`는 앱 루트에서 한 번 지정, 개별 뷰에서 상호작용 색을 따로 정하지 않는다
- [ ] 다크 모드에서 카드와 배경이 구분된다 (그림자 아닌 밝기 차이)
- [ ] 색만으로 전달하는 상태가 없다
- [ ] 라이트 / 다크 / 다크 고대비 프리뷰를 모두 확인했다
- [ ] 사진과 사용자 콘텐츠의 색은 모드에 따라 바뀌지 않는다

## 함께 읽기

- [접근성: 모두를 위한 기본값]({% post_url design/ios/2026-11-15-iosAccessibility %}) — 대비 기준과 색상 없이 구별
- [비주얼 계층구조: 시각적 중요도의 체계화]({% post_url design/technique/2024-08-25-visualHierarchy %}) — 색 4단계로 만드는 위계
- [디자인 시스템: 일관된 사용자 경험의 기반]({% post_url design/concept/2024-09-21-designSystem %}) — 색 토큰을 시스템으로
- [글라스모피즘: 투명함의 미학]({% post_url design/style/2024-08-25-glassmorphism %}) — 재질과 색의 관계
