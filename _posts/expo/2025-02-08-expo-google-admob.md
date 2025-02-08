---
layout: single
title: "[expo] google admob"
categories: expo
---

예전에는 expo 앱에서 expo-ads-admob 라이브러리를 쓸 수 있었지만, Expo SDK 46 부터 지원하지 않는다고 함.

react-native-google-mobile-ads 사용!

#### Step 1. admob 가입

[https://admob.google.com/home/](https://admob.google.com/home/)

- 왼쪽 탭 중 지급에서 지급 정보도 추가 필요

#### Step 2. 앱 만들기 (android, ios 별 진행 필요 - 총 2 회)

admob 사이트에서 왼쪽 탭 중 앱 > 앱추가

- 배포되지 않은 앱 선택 후 이름 입력
  - 나중에 다시 스토어를 추가해줘야 광고 게재가 가능함..

#### Step 3. 광고 단위 추가 (android, ios 별 진행 필요)

- 사이트 탭 중 앱 > 앱 선택 > 앱 개요 > 광고 단위 추가
- 광고 유형 선택
- 광고 단위 이름 입력
- 파트너 입찰 (X)
- 나머지 기본으로 놓고 광고 단위 만들기
- 그러면 ID 두 개가 나옴.
  - 앱 ID : 사이트 탭 중 앱 > 앱 선택 > 앱 설정 > 앱 ID 로 재확인 가능
  - 광고 단위 ID : 사이트 탭 중 앱 > 앱 선택 > 광고 단위 에서 각 광고 단위 별 ID 확인 가능

#### Step 4. react-native-google-mobile-ads 설치

```
npx expo install react-native-google-mobile-ads
```

`app.config.js` 또는 `app.json` 또는 `app.config.ts`에 아래 내용 추가

- androidAppId와 iosAppId에 각각 내용 입력

app.json

```json
// <project-root>/app.json
{
  "expo": {
    "plugins": [
      [
        "react-native-google-mobile-ads",
        {
          "androidAppId": "ca-app-pub-xxxxxxxx~xxxxxxxx",
          "iosAppId": "ca-app-pub-xxxxxxxx~xxxxxxxx"
        }
      ]
    ]
  }
}
```

> 처음에 .env 파일에다가 ID를 넣고, 빌드 했는데, 앱이 실행이 안됨.

빌드할 때, 써야하는 환경 변수는 .env 외에도 eas secret:create에도 등록이 필요

.env

```tsx
ADMOB_ANDROID_APP_ID = "ca-app-pub-xxxxxxxx~xxxxxxxx";
ADMOB_IOS_APP_ID = "ca-app-pub-xxxxxxxx~xxxxxxxx";
```

> eas secret:create를 사용해 비밀 환경 변수를 eas 쪽에 설정하고, app.conifg.js에서는 환경 변수를 가져다 쓸 수 있도록 함.

```tsx
eas secret:create --scope project --name ADMOB_ANDROID_APP_ID --type string --value "ca-app-pub-xxxxxxxx~xxxxxxxx"
eas secret:create --scope project --name ADMOB_IOS_APP_ID --type string --value "ca-app-pub-xxxxxxxx~xxxxxxxx"
```

app.config.js

```tsx
androidAppId: process.env.ADMOB_ANDROID_APP_ID,
iosAppId: process.env.ADMOB_IOS_APP_ID,
```

ios static frameworks (expo의 경우 아마도 `expo-build-properties`) 사용시, 아래 내용 추가 필요!

```json
// <project-root>/app.json
{
  "expo": {
    "plugins": [
      [
        "expo-build-properties",
        {
          "ios": {
            "useFrameworks": "static"
          }
        }
      ]
    ]
  }
}
```

#### Step 5. EAS Build

```
npx eas-cli build --profile development # 이렇게하고 ALL 선택
eas build --profile development --platform android # android만 빌드
eas build --profile development --platform ios # ios 만 빌드
```

#### Step 6. 광고 넣는 코드 작성

> 개발 버전에서는 테스트 아이디를 사용하도록 코드가 구성됨

**AppOpen 광고 : 앱 로드 화면에 띄우는 광고**

- 광고를 표시하기 전에 미리 로드 필요함. 그래야 사용자가 앱을 열 때 광고가 즉시 표시
- best practice
  - 앱을 몇 번 사용한 후에 오픈 광고를 띄우기
  - 앱이 로드 되는 동안(기다리는 시간)에 오픈 광고를 표시
  - 로딩이 다 되면 광고도 종료하고 매끄럽게 전환
- 예제 코드

```tsx
import {
  AppOpenAd,
  TestIds,
  AdEventType,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.APP_OPEN
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

const appOpenAd = AppOpenAd.createForAdRequest(adUnitId, {
  keywords: ["fashion", "clothing"],
});

// Preload an app open ad
appOpenAd.load();

// Show the app open ad when user brings the app to the foreground.
appOpenAd.show();
```

**Interstitial 광고** : 전면 광고

- 광고를 표시하기 전에 미리 로드하여 필요시 광고가 표시될 수 있도록 하기
- 게임 앱에서 레벨이 완료된 후나 게임 오버 시점등에 표시
- 광고 로드 Event나 오류 발생 Event를 리스너 메서드로 처리하여, 광고 상태를 모니터링하고, 적절한 시점에 광고를 표시
- 예제 코드

```tsx
import React, { useEffect, useState } from "react";
import { Button, Platform, StatusBar } from "react-native";
import {
  InterstitialAd,
  AdEventType,
  TestIds,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.INTERSTITIAL
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

const interstitial = InterstitialAd.createForAdRequest(adUnitId, {
  keywords: ["fashion", "clothing"],
});

function App() {
  const [loaded, setLoaded] = useState(false);

  useEffect(() => {
    const unsubscribeLoaded = interstitial.addAdEventListener(
      AdEventType.LOADED,
      () => {
        setLoaded(true);
      }
    );

    const unsubscribeOpened = interstitial.addAdEventListener(
      AdEventType.OPENED,
      () => {
        if (Platform.OS === "ios") {
          // Prevent the close button from being unreachable by hiding the status bar on iOS
          StatusBar.setHidden(true);
        }
      }
    );

    const unsubscribeClosed = interstitial.addAdEventListener(
      AdEventType.CLOSED,
      () => {
        if (Platform.OS === "ios") {
          StatusBar.setHidden(false);
        }
      }
    );

    // Start loading the interstitial straight away
    interstitial.load();

    // Unsubscribe from events on unmount
    return () => {
      unsubscribeLoaded();
      unsubscribeOpened();
      unsubscribeClosed();
    };
  }, []);

  // No advert ready to show yet
  if (!loaded) {
    return null;
  }

  return (
    <Button
      title="Show Interstitial"
      onPress={() => {
        interstitial.show();
      }}
    />
  );
}
```

###### Rewarded 광고 : 보상형 광고

- 예제 코드

```tsx
import React, { useEffect, useState } from "react";
import { Button } from "react-native";
import {
  RewardedAd,
  RewardedAdEventType,
  TestIds,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.REWARDED
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

const rewarded = RewardedAd.createForAdRequest(adUnitId, {
  keywords: ["fashion", "clothing"],
});

function App() {
  const [loaded, setLoaded] = useState(false);

  useEffect(() => {
    const unsubscribeLoaded = rewarded.addAdEventListener(
      RewardedAdEventType.LOADED,
      () => {
        setLoaded(true);
      }
    );
    const unsubscribeEarned = rewarded.addAdEventListener(
      RewardedAdEventType.EARNED_REWARD,
      (reward) => {
        console.log("User earned reward of ", reward);
      }
    );

    // Start loading the rewarded ad straight away
    rewarded.load();

    // Unsubscribe from events on unmount
    return () => {
      unsubscribeLoaded();
      unsubscribeEarned();
    };
  }, []);

  // No advert ready to show yet
  if (!loaded) {
    return null;
  }

  return (
    <Button
      title="Show Rewarded Ad"
      onPress={() => {
        rewarded.show();
      }}
    />
  );
}
```

`serverSideVerificationOptions`를 사용하면, 커스텀 데이터를 넣을 수 있음. (나중에 추가적인 분석을 위해서 쓰이는듯?)

```tsx
const rewardedAd = RewardedAd.createForAdRequest(adUnitId, {
  serverSideVerificationOptions: {
    userId: "9999",
    customData: "my-custom-data",
  },
});
```

> RewardedInterstitialAd 라는게 따로 또있음. RewardedAd랑 무슨 차이인지 확인 필요. 자연스러운 중단이나 전환 중에 광고를 시청한 사용자에게 리워드를 제공하는 방식이라고 함. 사용방법은 문서 참고!

###### 배너 광고 : 제한된 영역 안에서 광고

```tsx
import React, { useState, useRef } from "react";
import { Platform } from "react-native";
import {
  BannerAd,
  BannerAdSize,
  TestIds,
  useForeground,
} from "react-native-google-mobile-ads";

const adUnitId = __DEV__
  ? TestIds.ADAPTIVE_BANNER
  : Platform.OS === "ios"
  ? "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy" // ios ad unit id
  : "ca-app-pub-xxxxxxxxxxxxx/yyyyyyyyyyyyyy"; // android ad unit id

function App() {
  const bannerRef = useRef<BannerAd>(null);

  // IOS의 WKWebView는 일시정지 상태일때, 정지될 수 있고, 그 결과 앱이 foreground로 돌아올 때 배너 광고가 비어있을 수 있음. 그래서 앱이 foreground로 돌아올 때 수동으로 load 해줌.
  useForeground(() => {
    Platform.OS === "ios" && bannerRef.current?.load();
  });

  return (
    <BannerAd
      ref={bannerRef}
      unitId={adUnitId}
      size={BannerAdSize.ANCHORED_ADAPTIVE_BANNER}
    />
  );
}
```

enum BannerAdSize

- BANNER: MMA 배너 광고 크기 (320x50 밀도 독립 픽셀).
- FULL_BANNER: IAB 전체 배너 광고 크기 (468x60 밀도 독립 픽셀).
- LARGE_BANNER: 대형 배너 광고 크기 (320x100 밀도 독립 픽셀).
- LEADERBOARD: IAB 리더보드 배너 광고 크기 (728x90 밀도 독립 픽셀).
- MEDIUM_RECTANGLE: IAB 중간 사각형 광고 크기 (300x250 밀도 독립 픽셀).
- ADAPTIVE_BANNER: 사용 중단됨. 대신 ANCHORED_ADAPTIVE_BANNER를 사용해야 함.
- ANCHORED_ADAPTIVE_BANNER: 다음 세대의 동적으로 크기가 조정되는 배너로, 전체 너비와 자동 높이를 가짐.
- INLINE_ADAPTIVE_BANNER: 고정된 높이의 배너보다 더 크고 높은 배너로, 스크롤 콘텐츠에 배치하기 위해 설계됨.
- WIDE_SKYSCRAPER: IAB 와이드 스카이스크래퍼 광고 크기 (160x600 밀도 독립 픽셀). 현재 Google Mobile Ads 네트워크에서 지원되지 않으며, 중재 광고 네트워크 전용.

###### native 광고 : 뭔가 더 자연스럽게 녹아든 광고를 만들 수 있음?

참고 : https://docs.page/invertase/react-native-google-mobile-ads/native-ads

#### Step 7. 유럽 사용자 동의 (optional)

유럽 사용자에게 광고를 제공하기 전에 동의가 필요하고, 아래는 그 동의 받는 코드임.

```tsx
import {
  getTrackingPermissionsAsync,
  PermissionStatus,
  requestTrackingPermissionsAsync,
} from "expo-tracking-transparency";

const { status } = await getTrackingPermissionsAsync();
if (status === PermissionStatus.UNDETERMINED) {
  await requestTrackingPermissionsAsync();
}

const adapterStatuses = await mobileAds().initialize();

// Now ads can be loaded.
```

#### 참고자료

- https://nonmajor-be-developer.tistory.com/entry/React-Native-Expo%EC%97%90-google-AdMob%EC%9D%84-%EB%84%A3%EC%96%B4%EB%B3%B4%EC%9E%90
- https://www.youtube.com/watch?v=2f0AJ0O_45k
- https://docs.page/invertase/react-native-google-mobile-ads
- https://docs.page/invertase/react-native-google-mobile-ads/displaying-ads
