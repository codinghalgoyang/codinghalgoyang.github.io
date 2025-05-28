---
layout: single
title: "[expo] splash screen 사용하기"
published: false
categories: expo
---

문서 : https://docs.expo.dev/versions/latest/sdk/splash-screen/

#### expo-splash-screen 설치

```
# 이미 설치되어 있었음
# 별도 (추가)빌드 필요없었음
npx expo install expo-splash-screen
```

#### splash 이미지(다른 예제를 보니 1024x1024 px 사이즈 사용함)

`assets/images/splash-icon.png` 덮어쓰기

#### app.json 확인 (plugins/expo-splash-screen에서 물고 들어감)

```json
{
  "expo": {
    "plugins": [
      [
        "expo-splash-screen",
        {
          "image": "./assets/images/splah-icon.png",
          "imageWidth": 200,
          "resizeMode": "contain",
          "backgroundColor": "#ffffff"
        }
      ]
    ]
  }
}
```

#### App.tsx 내용 수정

> 아래 내용을 사용해 user session 확인 후, 원하는 screen으로 라우팅

```
import * as SplashScreen from "expo-splash-screen";
import { useEffect } from "react";
// import { router } from "expo-router";

SplashScreen.preventAutoHideAsync();

// Set the animation options. This is optional.
SplashScreen.setOptions({
  duration: 1000,
  fade: true,
});

const doSomething = async () => {};

export default function IndexScreen() {
  useEffect(() => {
    const initialze = async () => {
      doSomething();
      SplashScreen.hide();
      // router.replace("/home");
    };

    initialze();
  }, []);

  return null;
}
```

#### expo go에서 splash screen이 변경 안됨

> development 버전으로 eas build 실행해보니 변경됨!

- 개발시 앱 로딩시 나오는 화면은 splash screen이 아니고, 다른 것 같음. 이거랑 splash screen이랑 헷갈렸음.
- splash screen이 앱이 실행되면서 나오는 화면인데, expo go의 splash screen은 이미 정해져있는 것 같음.

```bash
eas build --profile development --platform android
```
