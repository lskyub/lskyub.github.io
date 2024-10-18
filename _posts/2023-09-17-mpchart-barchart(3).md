---
layout: post
title: "[MPAndroidChart] BarChart Custom (3)"
date: 2023-09-17 09:00:00 +0900
categories: android
tags: [android, MPAndroidChart, BarChart, custom]
---

"MPAndroidChart"에서 BarChart를 사용하며 일부 사항을 작성<br>
디자인 요구 사항에 따라 작업 중 발생한 이슈 사항을 기록 및 구현

[MPAndroidChart] BarChart Custom (1) : 마커
[MPAndroidChart] BarChart Custom (2) : 차트 바 라운처리

이전 포스트에서는 이슈 사항 중 마커([MPAndroidChart] BarChart Custom (3))에 관련된 내용을 작성 이번 포스트에서는 차트의 XAxis 커스텀 영역에 대해 작성

## 요구사항

- 애니메이션처리 필요
- 바차트 상단 라운드 처리
- 스크롤 기능
- 마커 추가
- X축 점선, X축 포인트 컬러 추가

![img-description](/assets/img/post/barchart.png)<br>
_요구 사항 이미지_

## 구현 순서

- 기본 바차트를 구현 기본적으로 라이브러리에서 지원하는 기능을 검토
- x, y 축을 포함하여 기본기능을 적용
- 커스텀으로 처리 가능한 영을 체크 구현 방법에 대한 검토 진행

## 문제 및 해결

- 차트 X축 텍스트 중 특정 조건에 따라 텍스트의 색상을 변경 해당 기능의 경우 MPAncroidChart에서 따로 지원 하지 않으며 해당 기능을 추가 하기 위해서는 XAxisRenderer를 상속받아 따로 처리 해야 함

1. 아래 함수에서 라벨을 생성

```kotlin
override fun drawLabels(c: Canvas?, pos: Float, anchor: MPPointF?)
```

위 함수 에서 반복문의 통해 라벨의 위치와 데이터를 구하고 해당 위치에 데이터를 노출
해당 프로젝트에서는 날짜에 따라 일요일일 경우 텍스트를 붉은색으로 표현 하는 기능이 필요

날짜 계산의 경우 아래 코드를 통해 요일을 구했으며 해당 코드는 "joda-time"을 활용하여 구현
```java
public static int dayOfWeek(String date){
        DateTimeFormatter dtf = DateTimeFormat.forPattern("MM/dd");
        int dayOfWeek = dtf.parseDateTime(date).getDayOfWeek();
        String dayOfWeekStr = "";
        switch (dayOfWeek) {
            case 1:
                dayOfWeekStr = "월요일";
                break;
            case 2:
                dayOfWeekStr = "화요일";
                break;
            case 3:
                dayOfWeekStr = "수요일";
                break;
            case 4:
                dayOfWeekStr = "목요일";
                break;
            case 5:
                dayOfWeekStr = "금요일";
                break;
            case 6:
                dayOfWeekStr = "토요일";
                break;
            case 7:
                dayOfWeekStr = "일요일";
                break;
        }
        return dayOfWeek;
    }
```

2. YAxis 점선 표현
점선 표현의 경우 MPAncroidChart에서 기본으로 지원하고 있으며 아래 코드를 통해 적용 가능
```kotlin
YAxis.enableGridDashedLine(10f, 10f, 0f)
```

**참고**<br>
[MPAndroidChart issues: 3536](https://github.com/PhilJay/MPAndroidChart/issues/3536)<br>
[joda-time](https://www.joda.org/joda-time/)