---
title: "모달의 위계: 알림, 시트, 팝오버"
date: 2026-11-15 00:02:00 +0900
categories:
  - 디자인
tags:
  - iOS 디자인
  - HIG
  - iOS
toc: true
toc_sticky: true
toc_label: "목차"
excerpt: "사용자의 흐름에 끼어드는 다섯 가지 방법과 각각을 써야 할 때. Alert, Confirmation Dialog, Sheet, Popover, Full Screen Cover."
---

모달(modal)은 현재 흐름을 잠시 멈추고 다른 과제에 집중하게 하는 화면이다.
웹에서는 대부분 `<dialog>`나 커스텀 오버레이 하나로 처리하지만, iOS에는 **끼어드는 강도에 따라 다섯 가지**가 있고, HIG는 각각을 언제 쓸지 꽤 명확하게 정해 두었다.

HIG의 기본 태도는 **모달을 최소화하라**는 것이다. 모달은 사용자가 하던 일을 끊는다. 인라인으로 해결할 수 있으면 모달을 쓰지 않는다.

## 다섯 가지 모달

강도가 약한 것부터.

| 종류 | 끼어드는 강도 | 쓰는 때 | 닫는 법 |
|---|---|---|---|
| **팝오버 (Popover)** | 약함 | iPad에서 버튼에 붙은 작은 옵션. iPhone에서는 자동으로 시트가 된다 | 바깥 탭 |
| **컨펌 다이얼로그 (Confirmation Dialog)** | 약함~중간 | 방금 누른 동작의 선택지 제시. "삭제하시겠습니까?" 같은 파괴적 동작 확인 | 선택 또는 취소 |
| **시트 (Sheet)** | 중간 | 별도의 과제. 작성, 편집, 필터, 공유. 뒤 화면이 살짝 보인다 | 아래로 끌기, 닫기 버튼 |
| **전체 화면 커버 (Full Screen Cover)** | 강함 | 몰입이 필요한 과제. 카메라, 동영상, 온보딩, 결제 | 명시적 닫기 버튼만 |
| **알림 (Alert)** | 가장 강함 | 반드시 알아야 할 정보, 되돌릴 수 없는 결정. 가장 드물게 | 버튼 선택만 |

## 선택 기준

<div class="mermaid">
flowchart TD
    A[흐름을 끊어야 하나?] -- 아니오 --> B[인라인으로<br/>상태 표시 · 배너 · 토글]
    A -- 예 --> C{사용자가<br/>방금 한 동작의<br/>선택지인가?}
    C -- 예 --> D[컨펌 다이얼로그]
    C -- 아니오 --> E{새 과제인가?}
    E -- 예, 몰입 필요 --> F[전체 화면 커버]
    E -- 예, 보통 --> G[시트]
    E -- 아니오, 정보 전달 --> H{되돌릴 수 없거나<br/>지금 꼭 알아야 하나?}
    H -- 예 --> I[알림]
    H -- 아니오 --> B
</div>

**알림을 쓰면 안 되는 경우**

- 성공 확인 ("저장되었습니다"): 햅틱과 인라인 상태로. 알림은 사용자가 또 한 번 탭하게 만든다
- 마케팅, 평가 요청, 업데이트 안내: 알림이 아니라 배너나 전용 화면으로
- 선택지가 3개 이상: 컨펌 다이얼로그나 시트로

**시트의 높이**

iOS 16부터 시트는 여러 높이(detent)를 가질 수 있다. 지도 앱의 검색 시트처럼 절반 높이로 시작해 끌어올릴 수 있다.

- `.medium`: 뒤 화면을 보면서 조작해야 할 때 (지도 위 검색 결과)
- `.large`: 독립적인 과제
- 커스텀: 콘텐츠 높이에 맞출 때 (`.height(280)`, `.fraction(0.4)`)

## SwiftUI로 구현하기

**시트: 작성 화면**

```swift
struct NotesView: View {
    @State private var isComposing = false

    var body: some View {
        List(notes) { NoteRow(note: $0) }
            .toolbar {
                Button("새 메모", systemImage: "square.and.pencil") { isComposing = true }
            }
            .sheet(isPresented: $isComposing) {
                NavigationStack {                              // 시트 안에 자기 내비게이션 바
                    ComposeView()
                        .navigationTitle("새 메모")
                        .navigationBarTitleDisplayMode(.inline)
                        .toolbar {
                            ToolbarItem(placement: .cancellationAction) {
                                Button("취소") { isComposing = false }
                            }
                            ToolbarItem(placement: .confirmationAction) {
                                Button("저장") { save(); isComposing = false }
                            }
                        }
                }
                .interactiveDismissDisabled(hasUnsavedChanges)   // 작성 중이면 끌어서 닫기 방지
            }
    }
}
```

취소는 왼쪽, 확인은 오른쪽. `cancellationAction`과 `confirmationAction` 배치를 쓰면 플랫폼마다 올바른 위치에 놓인다.

**반높이 시트**

```swift
.sheet(isPresented: $showFilters) {
    FilterView()
        .presentationDetents([.medium, .large])
        .presentationDragIndicator(.visible)
        .presentationBackgroundInteraction(.enabled(upThrough: .medium))  // 뒤 지도 조작 가능
}
```

**컨펌 다이얼로그: 파괴적 동작**

```swift
.confirmationDialog("이 메모를 삭제하시겠습니까?",
                    isPresented: $showDeleteConfirm,
                    titleVisibility: .visible) {
    Button("삭제", role: .destructive) { delete() }
    Button("취소", role: .cancel) { }
} message: {
    Text("삭제한 메모는 복구할 수 없습니다.")
}
```

`role: .destructive`는 빨간색으로, `.cancel`은 분리된 아래 버튼으로 자동 표시된다.

**알림: 정말 필요할 때만**

```swift
.alert("저장 공간이 부족합니다", isPresented: $showStorageAlert) {
    Button("설정 열기") { openSettings() }
    Button("나중에", role: .cancel) { }
} message: {
    Text("사진을 저장하려면 최소 50MB의 여유 공간이 필요합니다.")
}
```

알림 제목은 문장으로, 버튼은 동사로. "확인/취소"보다 "설정 열기/나중에"가 무슨 일이 일어날지 말해 준다.

**팝오버 (iPad) / 시트 (iPhone)**

```swift
.popover(isPresented: $showOptions, arrowEdge: .top) {
    OptionsView()
        .presentationCompactAdaptation(.sheet)   // iPhone에서는 시트로 (기본값)
        .frame(minWidth: 320, minHeight: 240)
}
```

**전체 화면 커버**

```swift
.fullScreenCover(isPresented: $showCamera) {
    CameraView(onDismiss: { showCamera = false })   // 닫기 버튼을 직접 제공해야 한다
}
```

## 자주 하는 실수

- **시트 위에 시트**: 한 번에 하나만. 시트 안에서 또 시트를 열어야 한다면 구조를 다시 본다. 시트 안에서 스택으로 들어가는 쪽이 낫다
- **닫기 버튼 없는 시트**: 끌어서 닫을 수 있어도 닫기 버튼(또는 취소/완료)은 있어야 한다. VoiceOver와 스위치 제어 사용자는 끌지 못한다
- **알림으로 성공 알리기**: 사용자를 멈추게 하는 비용이 성공 메시지의 가치보다 크다
- **알림 제목에 "알림", "경고", "오류"**: 제목은 무슨 일인지 말해야 한다. "네트워크에 연결할 수 없습니다"가 "오류"보다 낫다
- **파괴적 동작에 `.destructive` 생략**: 빨간색은 "되돌릴 수 없음"의 시스템 신호다
- **커스텀 모달 직접 그리기**: `ZStack`에 반투명 배경을 깔고 뷰를 띄우면 시트의 제스처, 접근성, 키보드 회피, 뒤 화면 축소 효과를 전부 잃는다
- **시트에 `TabView`**: 시트는 임시 공간이다. 탭은 최상위에만

## 체크리스트

- [ ] 인라인으로 해결할 수 있는 것을 모달로 만들지 않았다
- [ ] 알림은 되돌릴 수 없는 결정이나 꼭 알아야 할 정보에만 썼다
- [ ] 모든 시트에 명시적 닫기(취소/완료) 버튼이 있다
- [ ] 작성 중인 시트는 끌어서 닫을 때 변경 사항 손실을 막는다
- [ ] 파괴적 동작은 `.destructive` 역할과 확인 단계가 있다
- [ ] 버튼 레이블이 동사다 ("삭제", "설정 열기"). "확인"만 있는 알림이 없다
- [ ] iPad에서 팝오버가 적절한 크기로 뜬다
- [ ] VoiceOver로 모달이 열리면 포커스가 모달로 이동한다 (시스템 모달이면 자동)

## 함께 읽기

- [iOS 내비게이션: 탭, 스택, 스플릿 뷰]({% post_url design/ios/2026-11-15-iosNavigation %}) — 모달이 아닌 이동
- [햅틱: 손끝으로 전하는 피드백]({% post_url design/ios/2026-11-15-iosHaptics %}) — 알림 대신 쓸 수 있는 피드백
- [미니멀리즘 디자인: 적은 것이 더 많은 것이다]({% post_url design/style/2024-08-25-minimalism %}) — 점진적 공개와 모달의 절제
