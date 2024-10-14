---
layout: post
title:  "Android Studio logcat에 filter를 적용"
date:   2024-10-13 10:56:00 +0900
categories: android
tags: [android studio]
---

Android 개발 시 Logcat은 애플리케이션의 로그 메시지를 실시간으로 확인할 수 있는 강력한 도구입니다. 로그 메시지를 효과적으로 필터링하면 디버깅 과정을 더욱 효율적으로 진행할 수 있습니다.

## Logcat의 중요성
Logcat을 효과적으로 활용하면 다음과 같은 이점을 얻을 수 있습니다.

버그 식별 및 수정: 오류 메시지와 스택 트레이스를 통해 문제의 원인을 파악.
애플리케이션 상태 모니터링: 애플리케이션의 실행 상태와 성능을 실시간으로 확인.
사용자 상호작용 추적: 특정 이벤트나 사용자 행동에 따른 로그를 기록하여 분석.

## Logcat의 주요 로그 레벨
- VERBOSE: 가장 상세한 로그. 모든 로그 메시지를 포함.
- DEBUG: 디버깅 목적으로 사용되는 메시지.
- INFO: 일반적인 정보 메시지.
- WARN: 잠재적인 문제를 나타내는 경고 메시지.
- ERROR: 오류 발생 시의 메시지.
- ASSERT: 심각한 오류 상황을 나타내는 메시지.


## Android Studio 로그 쿼리
Android 스튜디오에서는 기본 쿼리 필드에서 바로 키-값 검색을 생성할 수 있습니다. 이 쿼리 시스템은 쿼리하려는 항목의 정확성을 제공하고 키-값을 기반으로 로그를 제외합니다. <br>

## 부정 표현식
부정 표현식을 지원하는 필드는 tag, package, message, line입니다.
부정은 필드 이름 앞에 -를 추가하여 표현합니다. 
>-tag:MyTag

## 논리 연산자 및 괄호
쿼리 언어는 &, |, 괄호로 표현되는 AND 연산자 및 OR 연산자를 지원합니다. 

> (tag:foo | level:ERROR) & package:mine<br>
> 일반적인 연산자 우선순위가 적용되므로, 다음의 경우<br>
> tag:foo | level:ERROR & package:mine<br>
> 다음과 같이 평가됩니다.<br>
> tag:foo | (level:ERROR & package:mine)

## 암시적 논리 연산자
논리 연산자가 적용되지 않으면 쿼리 언어는 OR 연산자와 동일한 키를 가진 다수의 비무효 key-value 필터 용어 및 AND가 있는 다른 모든 용어를 자동으로 평가합니다.
> tag:foo tag:bar package:myapp<br>
> 다음과 같이 평가됩니다.<br>
> (tag:foo | tag:bar) & package:myapp<br>
> 하지만 아래 예는<br>
> tag:foo -tag:bar package:myapp<br>
> 다음과 같이 평가됩니다.<br>
> tag:foo & -tag:bar & package:myapp

여러 개의 검색어를 논리 연산자 없이 공백으로 구분하면 우선순위가 낮은 AND로 처리됩니다. 예를 들어 foo bar tag:bar1 | tag:bar2는 'foo bar' & (tag: bar1 | tag: bar2)와 동일하게 처리됩니다.

## 예제
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

## 참고
[Android Studio Logcat 필터링 가이드](https://developer.android.com/studio/debug/logcat?hl=ko)