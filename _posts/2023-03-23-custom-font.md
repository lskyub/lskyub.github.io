---
layout: post
title:  "[Android] custom font 적용 방법"
date:   2023-03-23 13:51:00 +0900
categories: android
tags: android, xml
---

TextView 에 fontFamily를 통한 font 적용 파일 저장 위치 res 밑에 font 폴더를 생성 후 적용하고 싶은 폰트파일을 해당위치 저장 및 font_family.xml 을 생성

```bash
res > font > sample.otf

res > font > font_family.xml
```

### font_family.xml 작성
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

### TextView font 적용
```bash
<TextView
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/sample_1"
     android:gravity="center"
     android:text="테스트"
     android:textColor="@color/black" />
```