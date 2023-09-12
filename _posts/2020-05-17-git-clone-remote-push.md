---
layout: post
title:  "[Git] 저장소 히스토리 이전"
date:   2020-05-17 10:56:00 +0900
categories: git.
tags: git
---

git 주소가 변경 되었을때 이전에 작업 했던 모든 사항들에 대한 히스토리를 이전하고 싶을때 사용

```bash
$git clone --mirror 저장소1의주소

$cd 저장소1의주소.git

$git remote set-url --push origin 저장소2의주소

$git push --mirror
```