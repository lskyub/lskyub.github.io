---
layout: post
title:  "[Kotlin] null 처리"
date:   2017-07-11 14:41:00 +0900
categories: etc.
---

### ?.연산자
- 변수가 null이 아닐 때만 함수가 수행되며 null일경우 null리턴
```bash
//java
String temp;
if (s != null) {
   temp = s.toUpperCase();
} else {
   temp = null;
} 
//kotlin
var temp;
temp = s?.toUpperCase()
```

### ?: 연산자
- 변수가 null 일 때 default 값을 리턴

```bash
//java
String temp;
if (s != null) {
   temp = s;
} else {
   temp = "Default";
} 
//kotlin
var temp = s?:"Default"
```

### !!operator
- nullable로 설정된 property를 강제로 not null로 바꿔줌
- !!를 사용 후  null을 넣으면 NPE가 발생

```bash
var temp: String?
print(temp.toUpperCase()) 
// temp.부분에서 에러발생
// Only safe (?.) or non-null asserted (!!.) calls are allowed on a nullable receiver of type String?
print(temp!!.toUpperCase()) 
// null 일 수도 있는 변수를 강제로 null 아니라고 선언하여 사용
```

### let 함수
- not null인 경우에만 지정된 구문을 실행
- 자신의 receiver 객체를 람다식 내부로 넘겨줌

```bash
//java
if(temp != null){
  temp.toUppercase();
}
//kotlin
temp?.let{ it -> it.toUppercase() }
```

**참고**
- https://tourspace.tistory.com/114