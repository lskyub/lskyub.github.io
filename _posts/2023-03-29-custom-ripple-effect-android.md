---
layout: post
title:  "[Android] ripple effect custom 및 적용"
date:   2020-05-17 10:56:00 +0900
categories: android
---

21이상 버전에서의 ripple effect custom 방법

res - drawable 폴더에 ripple_effect.xml 파일을 아래와 같이 생성

### ripple_effect.xml 작성
```bash
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
    android:color="@color/main_round">
    <item android:id="@android:id/mask">
        <shape android:shape="rectangle">
            <corners
                android:bottomLeftRadius="5dp"
                android:bottomRightRadius="5dp"
                android:topLeftRadius="5dp"
                android:topRightRadius="5dp" />
            <solid android:color="@color/main_round" />
            <corners android:radius="2dip" />
        </shape>
    </item>

    <item android:id="@android:id/background">
        <shape android:shape="rectangle">
            <corners
                android:bottomLeftRadius="5dp"
                android:bottomRightRadius="5dp"
                android:topLeftRadius="5dp"
                android:topRightRadius="5dp" />
            <solid android:color="@color/black" />
        </shape>
    </item>
</ripple>
​
```

### ripple effect 적용
```bash
<TextView
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:gravity="center"
     android:text="테스트"
     android:background="@drawable/ripple_effect"
     android:textColor="@color/black" />
```
