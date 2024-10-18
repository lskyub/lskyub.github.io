---
layout: post
title:  "VSCode에서 Flutter 디버그 설정하기(Windows)"
date:   2024-10-18 09:00:00 +0900
categories: flutter, vscode, windows
tags: [flutter]
---

Flutter 프로젝트를 디버깅할 때, 올바른 디버그 설정을 통해 더 효율적으로 개발할 수 있습니다. 이번 글에서는 VSCode에서 Flutter 디버그 설정을 단계별로 설명하겠습니다.

# 1. launch.json 파일 생성하기
Flutter 프로젝트의 디버그 설정은 launch.json 파일에 저장됩니다. 이 파일을 통해 어떤 파일을 실행할지, 어떤 디버그 모드로 실행할지 등을 지정할 수 있습니다.

## launch.json 파일 생성 방법
1. VSCode에서 RUN AND DEBUG를 엽니다. Ctrl + Shift + D (macOS의 경우 Cmd + Shift + D)를 눌러 RUN AND DEBUG 를 열 수 있습니다.<br>
![RUN AND DEBUG](/assets/img/1018-1-1.png)<br>

2. `create a launch.json file`를 클릭 명령어 창을 실행 하고 Dart & Flutter를 선택 하여 기본 설정 파일을 생성 합니다.<br>
![RUN AND DEBUG](/assets/img/1018-1-2.png)<br>

3. 파일을 생성하면 `.vscode/launch.json` 이 생성 되며 기본 json은 아래와 같이 생성 됩니다.<br>
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "flutter_application_1",
            "request": "launch",
            "type": "dart"
        },
        {
            "name": "flutter_application_1 (profile mode)",
            "request": "launch",
            "type": "dart",
            "flutterMode": "profile"
        },
        {
            "name": "flutter_application_1 (release mode)",
            "request": "launch",
            "type": "dart",
            "flutterMode": "release"
        },
    ]
}
```

4. 실제 디바이스 또는 에뮬레이터에서 실행하기 위해서는 `launch.json` 파일에 아래 내용을 참고 하여 작성 합니다.<br>
```json

        {
            "name": "Android Device",
            "request": "launch",
            "type": "dart",
            "args" : ["-d", "connected devices"] // 연결된 디바이스 정보
        },
        {
            "name": "Android 9 (API 28) (Pixel 2)",
            "request": "launch",
            "type": "dart",
            "args" : ["-d", "emulator-5554"]
        },
```
현재 연결된 디바이스 정보를 확인하고 싶다면 아래 명령어를 통해 확인 할 수 있습니다.<br>
```bash
flutter devices
```

5. 위 설정까 모두 적용 되었다면 RUN AND DEBUG 에 `launch.json` 에 작성된 name이 노출됩니다.<br>
![RUN AND DEBUG](/assets/img/1018-1-3.png)<br>

## 디버그 실행하기
-  디버그 모드 실행: 디버그 모드를 시작하려면 F5 키를 누르거나, 왼쪽 사이드바의 디버그 아이콘을 클릭한 후 "RUN AND DEBUG"을 선택하면 됩니다.
-  디바이스 선택: Flutter 디버깅은 실제 디바이스 또는 에뮬레이터에서 실행할 수 있습니다. 하단의 상태바에서 디바이스를 선택하고 디버그 모드를 시작할 수 있습니다.

## 최종 `launch.json`
```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "flutter_application_1",
            "request": "launch",
            "type": "dart"
        },
        {
            "name": "flutter_application_1 (profile mode)",
            "request": "launch",
            "type": "dart",
            "flutterMode": "profile"
        },
        {
            "name": "flutter_application_1 (release mode)",
            "request": "launch",
            "type": "dart",
            "flutterMode": "release"
        },
                {
            "name": "Android Device",
            "request": "launch",
            "type": "dart",
            "args" : ["-d", "connected devices"] // 연결된 디바이스 정보
        },
        {
            "name": "Android 9 (API 28) (Pixel 2)",
            "request": "launch",
            "type": "dart",
            "args" : ["-d", "emulator-5554"]
        },
    ]
}
```

## 참고
[{개남DEV}](https://sudarlife.tistory.com/entry/flutter-web-%ED%94%8C%EB%9F%AC%ED%84%B0-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A5%BC-%EC%9B%B9%EA%B3%BC-%EC%95%A0%EB%AE%AC%EB%A0%88%EC%9D%B4%ED%84%B0%EB%A1%9C-%EB%8F%8C%EB%A0%A4%EB%B3%B4%EC%9E%90-vscode-%EC%84%B8%ED%8C%85%EB%B2%95)

