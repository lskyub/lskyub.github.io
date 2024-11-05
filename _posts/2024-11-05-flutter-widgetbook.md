---
layout: post
title:  "Flutter Widgetbook 적용"
date:   2024-11-05 09:00:00 +0900
categories: flutter
tags: [flutter, vscode, widgetbook]
---

Flutter를 시작하며 TDD, UI TSET와 같이 미리 확인하고 추후 문제에 대해 대응 하기 UI TSET를 위한 Widgetbook을 적용한 과정을 작성 하려고 합니다.

# Widgetbook이란?
Widgetbook은 Flutter 개발자들이 UI 컴포넌트를 독립적으로 구성하고 다양한 상태에서 미리 볼 수 있도록 지원하는 도구입니다. 웹 개발에서 Storybook과 비슷한 역할을 수행하며, UI를 모듈화해 관리하는 데 유용합니다. 이를 통해 컴포넌트 상태, 테마, 반응형 레이아웃 등 다양한 환경에서의 UI 상태를 쉽게 테스트할 수 있습니다.

## Widgetbook의 장점
- 효율적인 UI 테스트: 다양한 상태와 테마에서 UI 컴포넌트를 빠르게 테스트할 수 있어 개발과 유지 보수 효율이 높아집니다.
- 팀 협업 강화: Widgetbook Cloud를 활용하면 팀원이나 클라이언트가 쉽게 미리보기 화면에 접근하여 피드백을 줄 수 있어 협업에 유리합니다.
- 디자인 피드백: 디자이너가 직접 컴포넌트의 변화를 미리보며 디자인 수정과 피드백을 쉽게 제공할 수 있습니다​.

## Widgetbook 패키지 추가
pubspec.yaml 파일에 Widgetbook 패키지를 추가
```yaml
dependencies:
  widgetbook: ^3.10.0  # 최신 버전으로 설정합니다.
  widgetbook_annotations: ^3.2.0

dev_dependencies:
  widgetbook_generator: ^3.9.0
```

## Widgetbook 설정 파일 생성
프로젝트 루트 디렉토리 또는 lib 폴더에 widgetbook.dart 파일을 생성합니다. 이 파일은 Widgetbook의 진입점으로, 구성 요소들의 미리보기 시나리오와 상태를 정의합니다.
```dart
import 'package:widgetbook_annotation/widgetbook_annotation.dart' as widgetbook;

void main() {
  runApp(const WidgetbookApp());
}

@widgetbook.App()
class WidgetbookApp extends StatelessWidget {
  const WidgetbookApp({super.key});

  @override
  Widget build(BuildContext context) {
    return Widgetbook.material(
      directories: directories,
    );
  }
}
```

## Widgetbook directories 추가
Widgetbook에 컴포넌트 및 UseCase를 노출 하기 위해 directories를 아래와 같이 생성 하여 Widgetbook에 추가한다.
```dart
final directories = [
  WidgetbookComponent(
    name: 'CustomButton',
    useCases: [
      WidgetbookUseCase(
        name: 'Enabled',
        builder: (context) => enabledButton(context),
      ),
      WidgetbookUseCase(
        name: 'Disabled',
        builder: (context) => disabledButton(context),
      ),
    ],
  ),
];
```

## 각 위젯에 @UseCase 애노테이션 추가
각 컴포넌트의 다양한 상태를 미리보기 위해 @UseCase 어노테이션을 활용할 수 있습니다.<br>
아래 코드의 경우 CustomButton Widget을 생성하고 Widgetbook에서 미리 보기를 하기 위해
@UseCase를 추가 하고 상태에 따라 구분하여 작성한 내용을 볼 수 있습니다.
```dart
class CustomButton extends StatelessWidget {
  const CustomButton({
    super.key,
    required this.title,
    this.onPressed,
  });
  
  final String title;
  final VoidCallback? onPressed;

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onPressed,
      child: Text(title),
    );
  }
}

@widgetbook.UseCase(
  name: 'Enabled',
  type: CustomButton,
)
CustomButton enabledButton(BuildContext context) {
  return CustomButton(
    title: 'Enabled',
    onPressed: () {},
  );
}

@widgetbook.UseCase(
  name: 'Disabled',
  type: CustomButton,
)
CustomButton disabledButton(BuildContext context) {
  return const CustomButton(
    title: 'Disabled',
  );
}
```

## Widgetbook 옵션(테마, 정렬, 디바이스) 설정
Widgetbook의 addons를 통해 다양한 옵션을 추가 할 수 있습니다.
custom addons 도 지원 하고 있으며 아래 내용은 테마, 정렬, 디바이스 등 기본적인 옵션에 대한 작성을 보여주고 있습니다. 
```dart
Widgetbook.material(
    // Use the generated directories variable
    directories: directories,
    addons: [
    DeviceFrameAddon(
        devices: [
        Devices.ios.iPhone13,
        Devices.ios.iPhone13Mini,
        Devices.android.samsungGalaxyNote20Ultra,
        Devices.android.samsungGalaxyA50,
        Devices.android.samsungGalaxyNote20,
        Devices.android.samsungGalaxyS20
        ],
        initialDevice: Devices.ios.iPhone13,
    ),
    MaterialThemeAddon(
        themes: [
        WidgetbookTheme(
            name: 'Light',
            data: ThemeData.light(),
        ),
        WidgetbookTheme(
            name: 'Dark',
            data: ThemeData.dark(),
        ),
        ],
    ),
    AlignmentAddon(),
    ],
);
```

## 작성된 Widgetbook의 실행
Widgetbook는 아래 명령어를 통해 웹 으로 실헹 할 수 있다.
```bash
flutter run -d chrome -t lib/widgetbook.dart
```

# github widgetbook 연결
github과 [Widgetbook](https://app.widgetbook.io/)을 연결 하고 workflows 적용을 하면 코드를 받지 않아도 현재 적용된 내용에 대해 웹에서 테스트가 가능 합니다.

## Widgetbook 패키지 추가
pubspec.yaml 파일에 widgetbook_cli 패키지를 추가
```yaml
dev_dependencies:
  widgetbook_cli: ^3.4.3
```

## workflows 파일 생성
.github\workflows 폴더를 생성 하고 아래 yml파일 생성 하고 아래 내용과 같이 작성 한다.
아래 파일을 작성할때 `working-directory` `widgetbook.dart`의 위치에 따라 추가 하여 workflow가 실행 될 때의 경로를 잡아준다.
```yml
name: Widgetbook Cloud Hosting
on: push

jobs:
  widgetbook-cloud-hosting:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup flutter
        uses: subosito/flutter-action@v2
        with:
            channel: stable

      - name: Bootstrap App
        run: |
            flutter pub get
            # Add any other steps needed to make your
            # app widgets available for Widgetbook

      - name: Build Widgetbook
        run: |
            flutter pub get
            dart run build_runner build -d
            flutter build web -t lib/widgetbook.dart

      - name: Install Widgetbook CLI
        run: dart pub global activate widgetbook_cli

      - name: Push Widgetbook Build
        run: widgetbook cloud build push --api-key {Widgetbook에서 발급 받은 키}
```

패키지 추가 및 workflow를 작성하여 github으로 push를 진행 하면 get action에서 workflow를 진행하여 Widgetbook에 배포를 하며 웹에서 widget을 확인 할 수 있습니다.

# 결론
Flutter 프로젝트에 Widgetbook을 적용하면 UI 컴포넌트를 체계적으로 관리하고 테스트할 수 있어 생산성과 협업 효율이 크게 향상됩니다.<br>


## 참고
[Widgetbook Doc](https://docs.widgetbook.io/)