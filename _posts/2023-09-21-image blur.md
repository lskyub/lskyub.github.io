---
layout: post
title: "Android Image Blur (이미지 흐리게) 처리 하기"
date: 2023-09-21 09:00:00 +0900
categories: android
tags: [android, ImageView, Blur, toolkit, renderscript]
---

android에서 "image blur effect"를 적용 하는 방법에 대해 작성한다.
blur effect에 대해 google에서 검색을 하다 보면 여러 라이브러리를 발견 할 수 있었다.
처음에는 해당 라이브러리를 적용하려고 검토 중이었으나 developer에서 발견한 내용으로 인해 직접 구현을 시도 했으며 해당 시도를 통해 구현한 내용을 작성한다.

github등에서 발견한 라이브러리들은 RenderScript를 활용 하여 구현되어 으며 developer에서 발견한 내용은 아래와 같다.
> RenderScript API는 Android 12부터 지원 중단됩니다. 당분간은 계속 사용할 수 있지만 기기 및 구성요소 제조업체는 이미 하드웨어 가속 지원을 중단했으며 RenderScript 지원은 향후 출시에서 완전히 삭제될 예정

## 요구사항
- 블러효과를 상황에 따라 생성, 제거 가능
- 블러 색상 조절 기능(흰색, 검정 등)
- 블러 강도 조절 기능

![img-description](/assets/img/post/image_bluer.png)<br>
_요구 사항 이미지_

## 구현 순서
developer를 확인하여 RenderScript 제거후 사용 가능한 기술에 대해 정리
 - Android 12 이하 : Toolkit
 - Android 12 이상 : RenderEffect
위 기술들의 경우 문서에 작성된 내용으로 기존 RenderScript 보다 2배의 성능 향상을 보인다고함.

1. Toolkit
- 저장소 추가
```gradle
repositories {
     maven { url 'https://jitpack.io' }
}
```
```gradle
dependencies {
    implementation 'com.github.android:renderscript-intrinsics-replacement-toolkit:b6363490c3'
}
```
- Toolkit.blur 함수
```kotlin
fun blur(inputBitmap: Bitmap, radius: Int = 5, restriction: Range2d? = null): Bitmap
```
>inputBitmap - blur effect를 적용할 이미지<br>
>radius - blur에 사용되는 픽셀 1에서 25 사이의 값입니다. 기본값은 5.<br>
>restriction - null이 아닌 경우 2D 범위의 픽셀로 작업을 제한

- Toolkit.blur 적용
```kotlin
Toolkit.blur(bitmap, radius)
```

2. RenderEffect
- RenderEffect.createBlurEffect 함수
```java
public static RenderEffect createBlurEffect(float radiusX,float radiusY,@NonNull TileMode edgeTreatment)
```
>radiusX – X축을 따라 blur<br>
>radiusY – Y축을 따라 blur<br>
>edgeTreatment – blur 방법에 대한 정책

- RenderEffect.createBlurEffect 적용
```kotlin
val effect = RenderEffect.createBlurEffect(radius * 1.5f, radius * 1.5f, Shader.TileMode.CLAMP)
setRenderEffect(effect)
```


## 문제 및 해결
1. Toolkit를 사용 했을 때 원하는 만큼의 blur 효과가 적용되지 않았다. 방법을 생각하던 중 blur effect가 적용된 이미지를 다시 blure 처리를 해보자라는 생각이듬
- blur effect 반복을 위한 함수 생성
```kotlin
private fun getBitmap(bitmap: Bitmap): Bitmap {
    return Toolkit.blur(bitmap, radius = radius)
}
```
- blur effect 반복 적용 : downsampleFactor의 경우 blur를 반복할 횟수
```kotlin
var bitmap = BitmapFactory.decodeResource(context.resources, resId)
for (i in 0..downsampleFactor) {
    bitmap = getBitmap(bitmap)
}
setImageBitmap(bitmap)
```

**참고**
<br>
[android developer](https://developer.android.com/guide/topics/renderscript/migrate?hl=ko) : RenderScript에서 이전하기