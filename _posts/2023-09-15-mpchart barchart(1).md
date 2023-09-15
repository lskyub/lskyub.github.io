---
layout: post
title: "[MPAndroidChart] BarChart Custom (1)"
date: 2023-09-15 09:00:00 +0900
categories: android
tags: [android, MPAndroidChart, BarChart, custom]
image: /assets/img/post/custom_progress_bar.png
---

"MPAndroidChart"에서 BarChart를 사용하며 일부 사항을 작성<br>
디자인 요구 사항에 따라 작업 중 발생한 이슈 사항을 기록 및 구현

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

- 마커 라운드 처리 및 날짜, 값 을 표현 하기 위해 커스텀 MarkerView를 커스텀 처리 하여 적용이 필요

1. 마커 위치 조절 offset 계산

   > - 바차트 바로 위로 마커를 추가 하기위한 높이 조절<br>
   >   offset = MPPointF(-(width / 2).toFloat(), (-height - 10).toFloat())<br>
   > - 차트 상단에 고정하기위한 높이 조절<br>
   >   offset = MPPointF(-(width / 2).toFloat(), -chartView.height.toFloat())

2. 커스텀 마커 생성시 차트에 미노출 되는 현상
   > 커스텀 마커를 작성 중 ConstraintLayout을 사용시 차트에 노출이 안되는 현상 Root의 경우 "RelativeLayout"을 활용하여 화면을 구성 정상노출을 확인

## 전체코드

```kotlin
class MyMarkerView(context: Context?, private val maxStep: Int, layoutResource: Int) :
    MarkerView(context, layoutResource) {
    private var offset: MPPointF? = null
    private val tvDate: TextView = findViewById<View>(R.id.tv_date) as TextView
    private val tvContent: TextView = findViewById<View>(R.id.tv_content) as TextView

    override fun refreshContent(e: Entry, highlight: Highlight) {
        val model = e.data as ChartModel
        tvDate.visibility = if (chartView.data.entryCount == 24) {
            GONE
        } else {
            tvDate.text = model.date
            VISIBLE
        }
        tvContent.text = "${(maxStep * model.value).toInt()}"
        super.refreshContent(e, highlight)
    }

    override fun getOffset(): MPPointF? {
        if (offset == null) {
            offset = MPPointF(-(width / 2).toFloat(), -chartView.height.toFloat())
        }
        return offset
    }
}
```
