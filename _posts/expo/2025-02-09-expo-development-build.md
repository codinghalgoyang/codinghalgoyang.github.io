---
layout: single
title: "[expo] Development build"
categories: expo
---

> 커스텀화된 expo go를 빌드하는 방법

#### development build 란?
- project의 디버그 버전임 (expo go의 커스텀 버전)
- 빠른 반복 작업을 위해 최적화됨
- 네이티브 디렉토리 내에서 모든 라이브러리를 통합하거나 코드를 변경할 수 있음.
- 여기에는 견고하고 완전한 개발 환경을 제공하는 exp-dev-client 라이브러리가 포함됨

#### expo-dev-client 설치

development build를 사용하기 위해, 가장 처음 할 일은 `expo-dev-client`를 설치하는 일임.

```
npx expo install expo-dev-client
```

그리고 이제 `npx expo start`를 하게되면, 기본적으로 `development build`로 동작하게 됨.

그런데 바로 ios 시뮬레이터등에 동작하지 않음. > 앱을 빌드해야하기 때문 > eas 사용!

#### expo app 만들기
```
npx create-expo-app@latest
```


#### 실행하기
```
npx expo start # 기본적으로 expo go로 기본 실행됨
```

- expo go는 미리 설치된 일반적으로 사용되는 라이브러리가 포함되어 있음.
- expo go의 한계는 expo go에 번들로 제공되는 라이브러리만 사용할 수 있다는 점임
- 사용하려는 라이브러리가 expo go에서 지원하는지 알아보려면, React Native Directory 사이트에서 검색해서 `Expo Go`에 포함되어있는지 확인할 수 있음.
- notification이나 oAuth인증, 분석 등을 사용할 수 없음
- 앱 별 패키지 이름이나 번들 식별자를 사용하지 않기 때문에 프로덕션과 다른 동작을 경험할 수 있음
- 이런 한계에 도달했을 때, development build를 사용하게 됨.

#### `development build`의 의미는? 
- react native app은 2가지 부분으로 이루어짐.
	- 하나, Native app bundle : physical device에 설치되는 bundle
	- 둘, javascript bundle : native app bundle 위에서 돌아가는 bundle
- expo go는 native app bundle 역할을 하는 거임.
- 개발 빌드를 사용하면, native app bundle을 직접 만들 수 있음.
- 다시 얘기하면, 개발 중인 react native 앱을 실행하는데 필요한 네이티브 코드만으로 구성된 커스텀 expo go 임.
- 그래서 development build를 사용하면 
	- 모든 네이티브 라이브러리를 사용하면서
	- expo 프로젝트에서 제공되는 개발자 경험을 유지할 수 있음(xcode나 Android studio를 사용하지 않고도 실제 기기에 설치하여 테스트 가능)

#### development build 방법

2가지 방법이 있음
1. 로컬 실행 (Xcode, Android Studio 필요) 
2. eas (expo application services) 활용 : 클라우드에서 애플리케이션 빌드한 다음 실제 장치에 설치

#### 방법 1. development build: 로컬 빌드
```
npx expo prebuild
```
- 위 명령어를 실행하면, android와 ios의 native code를 생성해줌
- 안드로이드 폴더와 ios 폴더가 생성되고 그 안에 native code가 들어있음
- 이 폴더들은 수동으로 수정하는것보다 `npx expo prebuild`를 통해 자동 생성되도록 하는 것을 권장함.
- 환경설정하는 방법은 해당링크의 댓글에 달아놓았다고 함.
- 실행 : `npx expo run:ios`
- 실행하면 `Using development build`라는 문구와 함께 expo 실행 출력들이 나오는 것을 볼 수 있음.
- 이때부턴는 expo go 가 아닌 우리가 빌드한 네이티브 앱 자체가 생성되고, 그걸 실행시키는 것임.
- 그 앱 자체는 expo go와 같아서, 애플리케이션 재빌드 없이 View에서 바뀐 것들을 반영할 수 있음.

#### 방법 2. eas 활용

> 이 방법을 사용하면 실제 장치에서도 development build를 테스트 할 수 있음.

- eas-cli를 사용

```
npm install -g eas-cli # eas-cli 설치
eas login # eas 로그인
eas whoami # 현재 로그인된 계정 확인

# 현재 프로젝트에 대한 eas 초기화
eas init
> Which account shoud own this project? 내 계정 선택
> Would you like to create a project for ...? Y

# eas.json 생성
eas build:configure
> Which platforms would you like to configure for EAS Build? All

# 빌드
eas build --profile development --platform android
# 이 과정(또는 위의 과정)에서 app.json에 android pacakge 이름도 자동으로 등록됨.
eas build --profile development --platform ios
> Do you want EAS CLI to install expo-dev-client for you? yes
> Do you want to log in to your Apple account? yes # apple의 개발자 계정등이 필요
# Apple developer 등록 : https://developer.apple.com/kr/support/app-account/
# 개발자 등록시 돈을 내야하는 부분이 있고, 신분증으로 인증하는 과정이 필요함
# 이 부분을 마치고나면 Apple BundleID(android의 package)도 자동으로 app.json에 생성됨. 난 중간에 실패했지만, bundleID는 생성되긴 함.
# 끝나면 나오는 QR 코드 또는 링크를 폰으로 접속해 앱을 다운로드 받을 수 있음

# ios 개발자 등록이 되어있다면, 이렇게 한 다음 ALL을 선택해도 됨!
npx eas-cli build --profile development

# 아마도 여기에서 expo-dev-client 설치가 빠진듯?
# npx expo install expo-dev-client # Running expo install expo-dev-client 위 과정에서 자동으로 설치됨

npx expo start # 이제부터는 빌드한 앱에서 expo go 처럼 개발 시작
# 핫스팟 연결 상태에서는 연결 실패함.
# 와이파이 연결 상태에서 vscode와 앱을 종료후 다시시작하고 카메라로 QR코드 찍어 연결하니 연결이 됨. 그러고나서는 QR 코드 안찍어도 연결이되기 시작함. 

```

- eas.json의 "development"/"developmentClient" 는 개발클라이언트를 사용해 애플리케이션을 설치하고 싶다는 뜻이라고 함. 이걸 사용하면 애플리케이션에서 일부 개발자 도구를 볼 수 있음.
- expo 사이트에서 build 과정을 볼 수 있음.

> eas build시 android, ios 폴더가 만들어져있는 상태라면, eas는 뭔가 이 폴더들에 변경사항이 있다고 생각함. 그래서 이를 방지하는 방법엔 gitignore에 android, ios 폴더를 등록해주는 방법이 있음.
