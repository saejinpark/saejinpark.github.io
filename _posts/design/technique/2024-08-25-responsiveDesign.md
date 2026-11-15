---
title: "반응형 디자인: 모든 화면에 최적화된 경험"
date: 2024-08-25
last_modified_at: 2026-11-15
categories:
  - 디자인
tags:
  - 디자인 기법
  - 레이아웃
  - iOS
toc: true
toc_sticky: true
toc_label: "목차"
---

다양한 기기와 화면 크기에 자동으로 적응하는 디자인 접근 방식.
단일 코드베이스로 모든 디바이스에 최적화된 사용자 경험을 제공한다.

![다양한 기기에서의 반응형 디자인 예시](/assets/images/design/technique/responsive.png)

웹에서는 "반응형(responsive)", Apple 플랫폼에서는 "적응형(adaptive)"이라고 부르지만 목표는 같다. 화면이 달라도 콘텐츠는 제자리를 찾아야 한다.

# 특징

- 유동적 그리드 레이아웃 사용
- 유연한 이미지와 미디어 요소
- 화면 조건에 따른 스타일 조정 (웹의 미디어 쿼리)
- 콘텐츠 우선 접근 방식
- 디바이스 독립적 디자인 철학

# 주요 구성 요소

### 유동적 그리드
픽셀 대신 상대적 단위(%, em, rem 등)를 사용한 레이아웃

### 유연한 이미지
컨테이너 크기에 맞춰 자동으로 조절되는 이미지

### 미디어 쿼리
화면 크기에 따라 다른 스타일을 적용하는 규칙

### 브레이크포인트
레이아웃이 변경되는 화면 폭의 기준점

### 콘텐츠 재배치
화면 크기에 따라 콘텐츠 순서와 배치 변경

# 장점

### 일관된 사용자 경험
모든 기기에서 최적화된 인터페이스 제공

### 유지보수 용이성
단일 코드베이스로 여러 버전 관리 불필요

### SEO 최적화
검색 엔진이 선호하는 모바일 친화적 디자인 (웹)

### 비용 효율성
별도의 기기별 버전 개발 비용 절감

### 미래 대비
새로운 기기 출시에도 유연하게 대응 가능

# 적용 사례

- 기업 웹사이트, 뉴스 및 미디어 포털
- 이커머스 플랫폼
- 블로그 및 콘텐츠 중심 웹사이트
- iPhone과 iPad를 모두 지원하는 유니버설 앱
- Mac Catalyst, visionOS로 확장되는 앱

# 구현 시 고려사항

### 모바일 퍼스트
가장 제약이 큰 환경을 우선 고려한 디자인 접근

### 성능 최적화
다양한 네트워크 환경을 고려한 최적화

### 터치 인터페이스
모바일 사용자를 위한 충분한 터치 영역 확보

### 콘텐츠 우선순위
화면 크기에 따른 콘텐츠 우선순위 결정

### 테스트
다양한 기기와 환경에서의 철저한 테스트

### 접근성
모든 사용자를 위한 접근성 고려

### 유연한 타이포그래피
가독성을 유지하는 유동적 폰트 크기 설정

### 네비게이션 설계
다양한 화면 크기에 적합한 네비게이션 구조 설계

# iOS에서는

## HIG가 말하는 것

웹의 반응형이 **화면 폭**에 반응한다면, iOS의 적응형은 네 가지에 반응해야 한다.

1. **사이즈 클래스(Size Class)**: 화면 폭을 Compact / Regular 두 단계로만 추상화한다. iPhone 세로는 Compact, iPad와 iPhone Pro Max 가로는 Regular. 픽셀 브레이크포인트가 없다
2. **Dynamic Type**: 사용자가 글자 크기를 바꾸면 레이아웃도 바뀌어야 한다. 가로로 나열한 것이 세로로 쌓여야 할 수 있다
3. **세이프 에어리어**: 기기마다 노치, 홈 인디케이터, 바의 위치가 다르다
4. **멀티태스킹**: iPad에서 Split View, Slide Over, Stage Manager로 앱 폭이 실행 중에 바뀐다

HIG의 기본 태도는 "iPhone 레이아웃을 iPad에서 늘리지 말라"다. 넓은 화면에서는 **구조가 바뀌어야** 한다. 탭 바는 사이드바가 되고, 목록-상세는 한 화면에 나란히 놓인다.

## SwiftUI로 구현하기

SwiftUI의 레이아웃은 기본적으로 적응형이다. `HStack`과 `VStack`은 가용 공간에 맞춰 자식을 배치하고, 고정 크기를 주지 않으면 내용에 맞춰 늘어난다.

**사이즈 클래스에 따라 구조 바꾸기**

```swift
struct RootView: View {
    @Environment(\.horizontalSizeClass) private var sizeClass

    var body: some View {
        if sizeClass == .regular {
            NavigationSplitView {                       // iPad: 사이드바 + 상세
                Sidebar()
            } detail: {
                DetailView()
            }
        } else {
            TabView {                                   // iPhone: 탭 바
                ListView().tabItem { Label("목록", systemImage: "list.bullet") }
                SettingsView().tabItem { Label("설정", systemImage: "gear") }
            }
        }
    }
}
```

iOS 18부터는 `TabView`에 `.tabViewStyle(.sidebarAdaptable)`을 주면 iPad에서 자동으로 사이드바로 바뀐다.

**공간이 부족하면 쌓기 (iOS 16+)**

```swift
ViewThatFits {
    HStack { StatCard(); StatCard(); StatCard() }   // 들어가면 가로로
    VStack { StatCard(); StatCard(); StatCard() }   // 안 들어가면 세로로
}
```

`ViewThatFits`는 화면 폭뿐 아니라 Dynamic Type으로 글자가 커져 공간이 모자랄 때도 작동한다. 미디어 쿼리 없이 두 경우를 한 번에 처리한다.

**Dynamic Type에 따라 레이아웃 전환**

```swift
struct SettingRow: View {
    @Environment(\.dynamicTypeSize) private var typeSize

    var body: some View {
        let layout = typeSize.isAccessibilitySize
            ? AnyLayout(VStackLayout(alignment: .leading))
            : AnyLayout(HStackLayout())

        layout {
            Label("알림", systemImage: "bell")
            Spacer()
            Text("켜짐").foregroundStyle(.secondary)
        }
    }
}
```

**화면 비례 크기 (iOS 17+)**

```swift
Image("banner")
    .resizable()
    .scaledToFill()
    .containerRelativeFrame(.horizontal) { width, _ in width * 0.9 }   // 폭의 90%
```

`GeometryReader`보다 가볍고 레이아웃을 망가뜨리지 않는다.

## 주의할 점

- **고정 `frame` 금지**: `.frame(width: 350)`은 iPhone SE에서 잘리고 iPad에서 작아 보인다. `maxWidth`, `.adaptive`, `containerRelativeFrame`을 쓴다
- **`GeometryReader` 남용 금지**: 부모 공간을 전부 차지하고 레이아웃을 복잡하게 만든다. 꼭 필요할 때 가장 바깥에서 한 번만 쓴다
- **iPad 가로 모드**: 세로만 테스트하면 가로에서 목록이 너무 넓어진다. 본문 폭은 `frame(maxWidth: 680)` 정도로 제한한다
- **멀티태스킹 테스트**: iPad 시뮬레이터에서 Split View로 앱 폭을 1/3까지 줄여 본다. Compact 사이즈 클래스가 iPad에서도 나온다
- **세이프 에어리어**: 배경은 `.ignoresSafeArea()`, 콘텐츠는 그대로. 하단 버튼은 홈 인디케이터 위에 있어야 한다
- **Xcode Previews**: `#Preview` 여러 개로 iPhone SE, iPhone Pro Max, iPad, 접근성 글자 크기를 한 화면에서 본다

# 웹과 iOS 대응표

| 웹 | iOS / SwiftUI |
|---|---|
| `@media (min-width: 768px)` | `horizontalSizeClass == .regular` |
| 모바일 햄버거 ↔ 데스크톱 사이드바 | `TabView` ↔ `NavigationSplitView`, `.sidebarAdaptable` |
| Flexbox `flex-wrap` | `ViewThatFits`, `AnyLayout` 전환 |
| `vw`, `%` 단위 | `containerRelativeFrame`, `maxWidth: .infinity` |
| `rem`과 사용자 글자 크기 | Dynamic Type, `dynamicTypeSize` |
| `env(safe-area-inset-*)` | 세이프 에어리어 (기본 적용), `.ignoresSafeArea` |
| 브라우저 창 리사이즈 테스트 | iPad Split View / Stage Manager 테스트 |

# 함께 읽기

- [그리드 시스템: 디자인의 골격]({% post_url design/technique/2024-08-25-gridSystem %}) — 적응형 그리드
- [타이포그래피: 글자를 통한 효과적인 커뮤니케이션]({% post_url design/technique/2024-08-25-typography %}) — Dynamic Type
- [화이트 스페이스 활용: 여백의 미학]({% post_url design/technique/2024-08-25-whitespaceDesign %}) — 화면이 커질 때 여백이 하는 일
