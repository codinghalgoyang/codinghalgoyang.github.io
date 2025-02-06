---
layout: single
title: "[expo] splash screen 사용하기"
published: false
categories: expo
---

#### expo-splash-screen 설치

```
npx expo install expo-splash-screen
```

#### splash 이미지(다른 예제를 보니 1024x1024 px 사이즈 사용함)

`assets/splash.png` 경로로 저장

#### app.json 수정

```json
"expo": {
    ...
    "splash": {
      "image": "./assets/splash.png",
      "resizeMode": "contain",
      "backgroundColor": "#ffffff"
    },
}
```

#### App.tsx 내용 수정

```
import * as SplashScreen from "expo-splash-screen";
import { useEffect } from "react";

function sleep(ms: number) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

async function delay_splash() {
  await SplashScreen.preventAutoHideAsync();
  await sleep(3000); // 3초 대기
  await SplashScreen.hideAsync();
}

export default function App() {
  useEffect(() => {
    delay_splash();
  }, []);

  return (
    <View style={styles.container}>
      <Text>Open up App.tsx to start working on your app!</Text>
      <StatusBar style="auto" />
    </View>
  );
}
```

#### 일단 실패

-> 3초는 적용이 되는데, expo go에서 이미지가 변경되지 않는 현상이 발생함.
