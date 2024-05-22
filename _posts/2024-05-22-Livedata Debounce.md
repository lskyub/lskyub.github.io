---
layout: post
title: "Livedata Debounce 처리"
date: 2024-05-22 15:00:00 +0900
categories: abdriud.
tags: []
---

flow를 사용하지 않고 livedata를 Observer로 사용할때 

room db를 observer 할 경우 데이터가 연속적으로 들어갔을때 계속 호출이 발생 하는 이슈가 있어 해당 현상을 처리할 필요성이 발생

Lucio Maciel github에서 해당 코드를 찾아 사용

### 참고
[luciofm github](https://gist.github.com/luciofm/3ae1c0869cf9a05cd9a2e9e5baa9c1c9)
