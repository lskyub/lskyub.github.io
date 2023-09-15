---
layout: post
title: "[MPAndroidChart] BarChart Custom (2)"
date: 2023-09-16 09:00:00 +0900
categories: android
tags: [android, MPAndroidChart, BarChart, custom]
image: /assets/img/post/custom_progress_bar.png
---

"MPAndroidChart"에서 BarChart를 사용하며 일부 사항을 작성<br>
디자인 요구 사항에 따라 작업 중 발생한 이슈 사항을 기록 및 구현

이전 포스트에서는 이슈 사항 중 마커([MPAndroidChart] BarChart Custom (1))에 관련된 내용을 작성 이번 포스트에서는 바차트의 라운드 처리에 대한 내용을 작성

## 요구사항

- 애니메이션처리 필요
- 바차트 상단 라운드 처리
- 스크롤 기능
- 마커 추가

![img-description](/assets/img/post/barchart.png)<br>
_요구 사항 이미지_

## 구현 순서

- 기본 바차트를 구현 기본적으로 라이브러리에서 지원하는 기능을 검토
- x, y 축을 포함하여 기본기능을 적용
- 커스텀으로 처리 가능한 영을 체크 구현 방법에 대한 검토 진행

## 문제 및 해결

- 바차트의 경우 라운드 처리에 대한 기능을 가지고 있지 않아 "BarChartRenderer"를 상속받아 바상단의 라운드 처리를 진행

1. 아래 함수에서 DataSet을 통해 바의 위치와 바의 형태를 Canvas에 그림

```kotlin
override fun drawDataSet(c: Canvas, dataSet: IBarDataSet, index: Int)
```

위 함수 애서 아래와 같이 Rect를 통해 바를 그리는 형태로 구현 되어 있음

```kotlin
c.drawRect(buffer.buffer[j], buffer.buffer[j + 1], buffer.buffer[j + 2], buffer.buffer[j + 3], mRenderPaint);
```

해당 부분을 상단 곡선 형태로 draw하는 방식으로 코드의 수정이 필요 했음
radius의 경우 `left([j])`, `right([j+2])` 인 값을 `(right - left)/2` 해당 공식을 통해 구함 width를 구하지 않고 임의의 값을 사용할 경우 완전한 곡선이 안나올 수 있어 with를 구한뒤 최적의 radius를 구한다.

```kotlin
val path2 = roundRect(
                RectF(
                    buffer.buffer[j],
                    buffer.buffer[j + 1],
                    buffer.buffer[j + 2],
                    buffer.buffer[j + 3]
                ), radius, radius, tl = true, tr = true, br = false, bl = false
            )
c.drawPath(path2, mRenderPaint)
```

## 일부코드

roundRect 함수의 코드는 아래와 같이 구현되어 있음.

```kotlin
private fun roundRect(
    rect: RectF, rx: Float, ry: Float, tl: Boolean, tr: Boolean, br: Boolean, bl: Boolean
): Path {
    var rx = rx
    var ry = ry
    val top = rect.top
    val left = rect.left
    val right = rect.right
    val bottom = rect.bottom
    val path = Path()
    if (rx < 0) rx = 0f
    if (ry < 0) ry = 0f
    val width = right - left
    val height = bottom - top
    if (rx > width / 2) rx = width / 2
    if (ry > height / 2) ry = height / 2
    val widthMinusCorners = width - 2 * rx
    val heightMinusCorners = height - 2 * ry
    path.moveTo(right, top + ry)
    if (tr) path.rQuadTo(0f, -ry, -rx, -ry) //top-right corner
    else {
        path.rLineTo(0f, -ry)
        path.rLineTo(-rx, 0f)
    }
    path.rLineTo(-widthMinusCorners, 0f)
    if (tl) path.rQuadTo(-rx, 0f, -rx, ry) //top-left corner
    else {
        path.rLineTo(-rx, 0f)
        path.rLineTo(0f, ry)
    }
    path.rLineTo(0f, heightMinusCorners)
    if (bl) path.rQuadTo(0f, ry, rx, ry) //bottom-left corner
    else {
        path.rLineTo(0f, ry)
        path.rLineTo(rx, 0f)
    }
    path.rLineTo(widthMinusCorners, 0f)
    if (br) path.rQuadTo(rx, 0f, rx, -ry) //bottom-right corner
    else {
        path.rLineTo(rx, 0f)
        path.rLineTo(0f, -ry)
    }
    path.rLineTo(0f, -heightMinusCorners)
    path.close() //Given close, last lineto can be removed.
    return path
}
```
