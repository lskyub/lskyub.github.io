---
layout: post
title:  "Android Studio logcat에 filter를 적용"
date:   2023-03-23 10:56:00 +0900
categories: android
tags: [android studio]
---

안드로이드 스튜디오 버전이 업데이트 되며 변경된 logcat에 filter를 적용 하는 방법<br>

로컬 앱 프로젝트의 PID: package:mine<br>
특정 값:<br>
package:<package-ID><br>
tag:<tag><br>
level:[VERBOSE | INFO | ASSERT |DEBUG | WARN | ERROR ]<br>
키 앞에 -를 추가하여 특정 값을 제외합니다.<br>
-tag:<exclude-tag><br>
키 뒤에 ~를 배치하여 주어진 키로 정규 표현식을 사용합니다.<br>
tag~:<regular-expression-tag><br>
exclude 태그와 결합: -tag~:<exclude-regular-expression-tag>

```bash
package:mine level:error  tag:activity
```