---
layout: post
title:  "[Android] ripple effect custom 및 적용"
date:   2023-03-29 10:56:00 +0900
categories: android
tags: [android, xml]
---

Ripple Effect는 Android 5.0 Lollipop (API 레벨 21)에서 도입된 Material Design 가이드라인의 일부로, 사용자 상호작용(예: 탭)에 반응하여 터치 지점에서 방사형 애니메이션을 표시함으로써 시각적 피드백 메커니즘을 제공합니다. 이 효과는 상호작용이 보다 반응적이고 직관적으로 느껴지도록 하여 사용자 경험을 향상시킵니다.

## XML에서 Ripple Effect 구현하기
1. 레이아웃 파일에서 XML 속성을 사용하는 것 으로 background 속성에 ?attr/selectableItemBackground, ?attr/selectableItemBackgroundBorderless를 사용하는 방법이 있습니다.
```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Ripple 버튼"
    android:background="?attr/selectableItemBackground" />
```
```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Ripple 버튼"
    android:background="?attr/selectableItemBackgroundBorderless를" />
```
- ?attr/selectableItemBackground : 기본 선택 가능 배경을 적용하며, API 21 이상에서는 Ripple Effect가 포함됩니다.
- ?attr/selectableItemBackgroundBorderless : 테두리 없는 Ripple을 제공하며, 뷰의 경계에 클리핑되지 않습니다.
2. 커스터마이즈하려면 커스텀 Ripple Drawable을 정의할 수 있습니다.

## 21이하 버전 대체 방안
API 21 미만의 기기에서는 위의 접근 방식이 StateListDrawable을 사용하여 Ripple 대신 유사한(비록 동일하지는 않지만) 피드백 메커니즘을 제공합니다.

## Ripple Effect 사용 유의사항
- 일관성 유지: 상호작용 가능한 요소 전반에 걸쳐 Ripple Effect를 일관되게 사용하여 통일된 사용자 경험을 제공합니다.
- 성능 고려: 특히 리스트나 복잡한 레이아웃에서 성능에 영향을 줄 수 있는 복잡한 Ripple 애니메이션의 과도한 사용을 피합니다.
- 접근성 보장: Ripple Effect가 접근성 기능을 방해하지 않으면서 사용성을 향상시키도록 합니다.
- 커스터마이징: 앱의 테마에 맞게 Ripple 색상을 커스터마이즈하여 시각적 일관성을 유지합니다.


## 참고
- [공식 Android RippleDrawable 문서](https://developer.android.com/reference/android/graphics/drawable/RippleDrawable)
- [Material Design 가이드라인]()
- [AppCompat 라이브러리](https://developer.android.com/jetpack/androidx/releases/appcompat?hl=ko)