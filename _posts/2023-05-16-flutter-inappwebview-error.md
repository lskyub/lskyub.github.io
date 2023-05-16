---
layout: post
title:  "[Flutter] inappwebview 사용 이슈 공유 "
date:   2023-05-16 09:20:00 +0900
categories: flutter, error
---

flutter webview library `inappwebview` 를 사용하여 빌드시 ios에서 오류 발생

### 최초 발생 오류 
```bash
Uncategorized (Xcode): Command CompileSwift failed with a nonzero exit code

Swift Compiler Error (Xcode): Cannot call value of non-function type 'String?'
/Users/chandankumar/.pub-cache/hosted/pub.dartlang.org/flutter_inappwebview-5.5.0+2/ios/Classes/MyCookieManager.swift:259:43

Swift Compiler Error (Xcode): Cannot call value of non-function type 'String?'
/Users/chandankumar/.pub-cache/hosted/pub.dartlang.org/flutter_inappwebview-5.5.0+2/ios/Classes/MyCookieManager.swift:293:43

Could not build the application for the simulator.
Error launching application on iPhone 11.
```

위 오류의 경우 xcode 13.0 버전을 사용 중 발생 
-> github issues:1376 이슈 내용 확인 중 xcode 14 버전으로 update가 필요하는 내용을 확인

### xcode 업데이트 후 발생 오류
```bash
Missing file `libarclite_iphoneos.a` in Xcode 14.3 after update flutter and Xcode
```
-> stackoverflow 를 통해 Podfile 편집 및 캐시 초기화를 통해 오류 수정 확인

flutter project/ios/Podfile 수정
```bash
# platform :ios, '14.0'

# CocoaPods analytics sends network stats synchronously affecting flutter build latency.
ENV['COCOAPODS_DISABLE_STATS'] = 'true'

project 'Runner', {
  'Debug' => :debug,
  'Profile' => :release,
  'Release' => :release,
}

def flutter_root
  generated_xcode_build_settings_path = File.expand_path(File.join('..', 'Flutter', 'Generated.xcconfig'), __FILE__)
  unless File.exist?(generated_xcode_build_settings_path)
    raise "#{generated_xcode_build_settings_path} must exist. If you're running pod install manually, make sure flutter pub get is executed first"
  end

  File.foreach(generated_xcode_build_settings_path) do |line|
    matches = line.match(/FLUTTER_ROOT\=(.*)/)
    return matches[1].strip if matches
  end
  raise "FLUTTER_ROOT not found in #{generated_xcode_build_settings_path}. Try deleting Generated.xcconfig, then run flutter pub get"
end

require File.expand_path(File.join('packages', 'flutter_tools', 'bin', 'podhelper'), flutter_root)

flutter_ios_podfile_setup

target 'Runner' do
  use_frameworks!
  use_modular_headers!

  flutter_install_all_ios_pods File.dirname(File.realpath(__FILE__))
  target 'RunnerTests' do
    inherit! :search_paths
  end
end

# 수정위치
post_install do |installer|
  installer.pods_project.targets.each do |target|
    target.build_configurations.each do |config|
      if Gem::Version.new(config.build_settings['IPHONEOS_DEPLOYMENT_TARGET']) < Gem::Version.new('11.0')
        config.build_settings['IPHONEOS_DEPLOYMENT_TARGET'] = '11.0'
      end
    end
    flutter_additional_ios_build_settings(target)
  end
end
```

하위 버전의 flutter를 사용하고 있다면 flutter 업데이트 필요
```bash
flutter upgrade
```

명령어 순차적으로 실행
```bash
flutter clean
flutter pub get
cd ios/
pod deintegrate
pod install
pod repo update
pod install (again)
```

mac cpu 에 따란 분기

mac intell cpu
```bash
sudo gem install cocoapods
```

mac apple m1, m2
```bash
sudo gem uninstall ffi && sudo gem install ffi -- --enable-libffi-alloc
```

위 내용을 참고 하여 해당 오류 수정 완료

### 참고
[stackoverflow](https://stackoverflow.com/questions/75894992/missing-file-libarclite-iphoneos-a-in-xcode-14-3-after-update-flutter-and-xcod)\
[github issues](https://github.com/pichillilorenzo/flutter_inappwebview/issues/1376)


