---
title: "iOS 내비게이션: 탭, 스택, 스플릿 뷰"
date: 2026-11-15 00:01:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - iOS
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "앱의 뼈대를 고르는 기준. 탭 바, 내비게이션 스택, 스플릿 뷰가 각각 어떤 질문에 답하는지와 SwiftUI 구현."
---

내비게이션은 사용자가 "지금 어디에 있고, 어디로 갈 수 있고, 어떻게 돌아가는지"를 아는 방법이다.
웹에서는 URL과 뒤로 가기 버튼이 이 역할을 하지만, iOS에는 URL 바가 없다. 그래서 내비게이션 구조 자체가 그 역할을 해야 한다.

HIG는 내비게이션을 **콘텐츠를 담는 그릇**으로 보고, 그릇이 눈에 띄지 않을수록 좋다고 말한다. 사용자가 "어떻게 이동하지?"를 생각하는 순간 내비게이션은 실패한 것이다.

## 세 가지 기본 구조

iOS 앱의 내비게이션은 대부분 세 가지 구조의 조합이다.

<div class="mermaid">
flowchart LR
    A[앱 구조 고르기] --> B{최상위 영역이<br/>서로 독립적인가?}
    B -- 예, 2~5개 --> C[탭 바<br/>TabView]
    B -- 아니오 --> D{콘텐츠가<br/>계층적인가?}
    D -- 예 --> E[내비게이션 스택<br/>NavigationStack]
    D -- 아니오, 평면적 --> F[페이지 / 단일 화면]
    C --> G{iPad도<br/>지원하나?}
    E --> G
    G -- 예 --> H[스플릿 뷰<br/>NavigationSplitView<br/>사이드바 적응형 탭]
</div>

### 탭 바 (Tab Bar)

- **답하는 질문**: "이 앱에는 어떤 영역들이 있는가?"
- **언제**: 서로 독립적인 최상위 영역이 2~5개일 때. 시계 앱(세계 시계, 알람, 스톱워치, 타이머)이 교과서
- **규칙**: 탭은 항상 보이고, 탭을 전환해도 각 탭의 상태(스크롤 위치, 들어간 깊이)는 유지된다. 탭은 모드 전환이지 동작 버튼이 아니다. "작성" 같은 동작을 탭에 넣지 않는다
- **iOS 26**: 탭 바가 Liquid Glass 재질로 콘텐츠 위에 떠 있고, 스크롤하면 작아진다. 탭 바 옆에 검색 탭을 따로 둘 수 있다

### 내비게이션 스택 (Navigation Stack)

- **답하는 질문**: "이 항목 안에는 무엇이 있는가?"
- **언제**: 콘텐츠가 계층적일 때. 설정 → 일반 → 정보처럼 깊이 들어갔다가 돌아온다
- **규칙**: 내비게이션 바 왼쪽에 "< 이전 화면 제목"이 자동으로 생기고, 왼쪽 가장자리 스와이프로 돌아간다. 이 둘은 절대 막지 않는다
- **큰 제목**: 최상위 화면은 큰 제목(Large Title), 들어갈수록 인라인 제목. 깊이를 제목 크기로도 전달한다

### 스플릿 뷰 (Split View)

- **답하는 질문**: "넓은 화면에서 목록과 상세를 어떻게 함께 보여 줄 것인가?"
- **언제**: iPad, Mac, 가로 모드 iPhone Pro Max. 메일 앱의 사이드바-메일함-메일 세 컬럼
- **규칙**: 화면이 좁아지면(iPhone, Split View) 자동으로 스택으로 접힌다. 한 코드로 두 구조를 얻는다

## 선택 기준

| 상황 | 구조 |
|---|---|
| 독립적인 영역 3개, 각각 깊이 있음 | 탭 바 + 각 탭 안에 스택 |
| 하나의 긴 목록과 상세 | 스택 (iPhone) / 스플릿 뷰 (iPad) |
| 설정처럼 깊은 계층만 있음 | 스택 |
| 온보딩, 튜토리얼 | 페이지 (가로 스와이프) |
| 지도, 카메라처럼 한 화면이 전부 | 단일 화면 + 시트로 보조 UI |
| 영역 6개 이상 | 탭 5개 + "더 보기"는 피한다. 영역을 합치거나 사이드바(iPad)로 |

**피해야 할 것**

- 햄버거 메뉴(내비게이션 드로어): iOS 관례가 아니다. 숨겨진 메뉴는 발견되지 않는다
- 탭 바 + 하단 시트가 겹치는 구조: 엄지 영역이 충돌한다
- 뒤로 가기를 커스텀 버튼으로 대체: 시스템 뒤로 가기와 스와이프를 잃는다
- 깊이 5단계 이상: 사용자는 어디 있는지 잊는다. 구조를 평평하게 만들거나 시트로 분리한다

## SwiftUI로 구현하기

**탭 + 스택 조합**

```swift
struct RootView: View {
    var body: some View {
        TabView {
            Tab("목록", systemImage: "list.bullet") {          // iOS 18+ Tab 문법
                NavigationStack {
                    ItemListView()
                        .navigationTitle("목록")
                }
            }
            Tab("검색", systemImage: "magnifyingglass", role: .search) {
                SearchView()
            }
            Tab("설정", systemImage: "gear") {
                NavigationStack { SettingsView().navigationTitle("설정") }
            }
        }
        .tabViewStyle(.sidebarAdaptable)   // iPad에서 사이드바로 자동 전환
    }
}
```

각 탭이 **자기 스택**을 가진다. `TabView` 바깥에 `NavigationStack` 하나를 두면 탭을 바꿀 때 깊이가 섞인다.

**데이터 기반 스택 (iOS 16+)**

```swift
struct ItemListView: View {
    @State private var path: [Item] = []

    var body: some View {
        NavigationStack(path: $path) {
            List(items) { item in
                NavigationLink(item.title, value: item)
            }
            .navigationDestination(for: Item.self) { item in
                ItemDetailView(item: item)
            }
        }
    }
}
```

`path`를 상태로 들고 있으면 딥 링크(`path = [item]`), 루트로 돌아가기(`path.removeAll()`), 상태 복원이 모두 배열 조작이 된다.

**스플릿 뷰**

```swift
NavigationSplitView {
    List(folders, selection: $selectedFolder) { folder in
        Label(folder.name, systemImage: folder.icon)
    }
    .navigationTitle("폴더")
} content: {
    if let selectedFolder {
        NoteList(folder: selectedFolder, selection: $selectedNote)
    }
} detail: {
    if let selectedNote { NoteEditor(note: selectedNote) }
    else { ContentUnavailableView("메모를 선택하세요", systemImage: "note.text") }
}
```

iPhone에서는 자동으로 스택으로 접힌다. 상세가 비었을 때의 화면(`ContentUnavailableView`)을 꼭 둔다. iPad에서는 그 화면이 처음부터 보인다.

**제목과 툴바**

```swift
.navigationTitle("받은 편지함")
.navigationBarTitleDisplayMode(.large)          // 최상위는 큰 제목
.toolbar {
    ToolbarItem(placement: .primaryAction) {    // 오른쪽: 주요 동작
        Button("작성", systemImage: "square.and.pencil") { }
    }
    ToolbarItem(placement: .bottomBar) {        // 하단 툴바: 보조 동작
        Button("필터", systemImage: "line.3.horizontal.decrease.circle") { }
    }
}
```

## 자주 하는 실수

- **`NavigationView` 사용**: iOS 16부터 deprecated. `NavigationStack`과 `NavigationSplitView`로 나뉘었다
- **탭마다 다른 내비게이션 바 색**: 탭을 바꿀 때마다 바 색이 바뀌면 앱이 여러 개처럼 느껴진다. `tint` 하나로 통일한다
- **시트 안에 탭 바**: 시트는 임시 공간이다. 탭 바는 앱의 최상위에만
- **뒤로 가기 버튼 제목을 "뒤로"로 고정**: 시스템 기본은 이전 화면 제목이다. 그래야 어디로 돌아가는지 안다. 제목이 너무 길 때만 `.navigationBarBackButtonHidden`이 아니라 `toolbarRole(.editor)`로 "< 뒤로"를 쓴다
- **선택 상태를 뷰에 저장**: `selectedItem`을 `@State`로 하위 뷰에 두면 iPad에서 사이드바와 상세가 어긋난다. 스플릿 뷰의 선택 상태는 가장 바깥에

## 체크리스트

- [ ] 최상위 영역이 2~5개면 탭 바, 아니면 스택이나 스플릿 뷰
- [ ] 모든 스택 화면에서 왼쪽 가장자리 스와이프로 돌아갈 수 있다
- [ ] 최상위 화면은 큰 제목, 하위 화면은 인라인 제목
- [ ] iPad에서 목록-상세가 나란히 보인다 (스플릿 뷰 또는 `.sidebarAdaptable`)
- [ ] 상세가 비었을 때 보여 줄 화면이 있다
- [ ] 탭을 전환해도 각 탭의 깊이와 스크롤 위치가 유지된다
- [ ] 딥 링크로 특정 화면에 바로 갈 수 있다 (`path` 조작)
- [ ] VoiceOver로 탭 바와 뒤로 가기 버튼이 올바르게 읽힌다 (시스템 컴포넌트면 자동)

## 함께 읽기

- [모달의 위계: 알림, 시트, 팝오버]({% post_url design/ios/2026-11-15-iosModality %}) — 스택 밖으로 잠깐 나가는 방법
- [머티리얼 디자인: 디지털 세계의 물리적 메타포]({% post_url design/style/2024-08-25-materialDesign %}) — Android 내비게이션을 iOS로 번역하는 표
- [반응형 디자인: 모든 화면에 최적화된 경험]({% post_url design/technique/2024-08-25-responsiveDesign %}) — 사이즈 클래스에 따른 구조 전환
