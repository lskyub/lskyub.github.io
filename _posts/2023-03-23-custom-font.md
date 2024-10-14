---
layout: post
title:  "[Android] custom font 적용 방법"
date:   2023-03-23 13:51:00 +0900
categories: android
tags: [android, xml]
---

## 커스텀 폰트 적용 방법
안드로이드에서 커스텀 폰트를 적용하는 방법은 크게 세 가지로 나눌 수 있습니다. 각 방법은 프로젝트의 요구 사항과 안드로이드 버전에 따라 선택할 수 있습니다.

### Assets 폴더를 이용한 폰트 적용
안드로이드의 초기 버전부터 사용 가능하며, assets 폴더에 폰트 파일을 저장한 후 Typeface 클래스를 이용해 적용하는 방식입니다.

- 장점
1. XML에서 직접 폰트 설정 가능.
2. 여러 뷰에 동일한 폰트를 쉽게 적용할 수 있음.
3. 폰트 리소스 관리가 용이.
- 단점
1. Android 8.0 이상에서만 사용 가능 (백워드 호환성 필요).

#### 1.단계별 절차
폰트 파일 준비: .ttf 또는 .otf 형식의 폰트 파일을 준비합니다.
#### 2.Assets 폴더 생성
- app/src/main/assets/fonts/ 디렉토리를 생성합니다.
- fonts 폴더에 준비한 폰트 파일을 복사합니다.


#### 3.Assets 코드에서 Typeface 설정:
```kotlin
val textView: TextView = findViewById(R.id.my_text_view)
val typeface: Typeface = Typeface.createFromAsset(assets, "fonts/MyCustomFont.ttf")
textView.typeface = typeface
```

### Fonts 폴더와 XML을 이용한 폰트 적용 (Android 8.0 이상)
Android 8.0 (API 레벨 26)부터 도입된 Fonts in XML 기능을 사용하면, XML 파일을 통해 폰트를 손쉽게 설정할 수 있습니다.

- 장점
1. APK 크기 감소.
2. Google Fonts의 다양한 폰트를 쉽게 사용 가능.
3. 폰트 업데이트가 용이.
- 단점
1. 인터넷 연결이 필요할 수 있음.
2. 특정 폰트 제공자의 정책에 의존.

#### 1.폰트 파일 준비:
- .ttf 또는 .otf 형식의 폰트 파일을 준비합니다.


#### 3.Fonts 폴더 생성:
- app/src/main/res/font/ 디렉토리를 생성합니다.
- font 폴더에 준비한 폰트 파일을 복사합니다.


```bash
res > font > sample.otf

res > font > font_family.xml
```

#### 3. font_family.xml 작성
```bash
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    tools:ignore="UnusedAttribute">
    <font
        android:font="@font/sample_1"
        android:fontStyle="normal"
        android:fontWeight="400"
        app:font="@font/sample_1"
        app:fontStyle="normal"
        app:fontWeight="400" />

    <font
        android:font="@font/sampel_2"
        android:fontStyle="normal"
        android:fontWeight="400"
        app:font="@font/sampel_2"
        app:fontStyle="normal"
        app:fontWeight="400" />
</font-family>
```

#### 4. TextView font 적용
```bash
<TextView
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/sample_1"
     android:gravity="center"
     android:text="테스트"
     android:textColor="@color/black" />
```