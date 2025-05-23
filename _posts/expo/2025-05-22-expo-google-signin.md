---
layout: single
title: "[expo] google signin"
categories: expo
---

- 회원 가입 및 관리의 편의성을 위해 구글 로그인을 지원

## firebase vs without firebase

- firebase를 사용하는 경우
  - 빌드시 firebase(또는 구글 클라우드 콘솔)에서 plist와 json 파일을 다운로드 받은 뒤, 빌드시 같이 포함시킴. (firebase sdk가 이 파일들을 사용하여 GoogleSignin을 도움)
  - `GoogleSignin.configure()` 함수 실행시 아무값도 넘겨주지 않아도 됨.
- firebase를 사용하지 않는 경우
  - 빌드시 ios는 iosUrlSchema 정보를 넣어줌
  - `GoogleSignin.configure()` 함수 실행시, `{iosClientId : ""}` 파라미터를 함께 넣어줘야 함.

#### firebase를 사용하지 않을 경우, 인증 과정

- 안드로이드 : SHA-1 키 사용
  - 앱을 빌드할 때 SHA-1키를 넣어줌
  - 구글 클라우드 콘솔 클라이언트 해당 SHA-1키를 등록해 놓음
- ios : iosUrlScheme과 iosClientId를 사용
  - 구글 클라우드 콘솔 클라이언트의 iosUrlScheme과 iosClientId를 아래에서 사용
  - 앱을 빌드할 때, iosUrlScheme을 넣어줌
  - GoogleSignin.configure()시 iosClientId를 넣어줌

## 빌드 준비

- react-native-google-signin은 기본 expo go 앱에 포함되어있지 않아, 새로 빌드가 필요함

#### 안드로이드 SHA-1 키 준비

- 구글 로그인을 위해서는 구글 클라우드 콘솔의 구글 인증 플랫폼(또는 firebase)에 클라이언트를 추가해줘야함.
- firebase는 구글 클라우드 콘솔과 연결되어있지만, 나는 firebase를 사용하지 않을꺼라, 구글 클라우드 콘솔을 직접 설정하여 사용함.
- 안드로이드의 경우, 클라이언트 추가시 패키지명과 함께 SHA-1키를 함께 등록해줘야함.
- SHA-1키는 eas build 를 통해 생성할 수 있음. (eas credentials 사용시 dev용 패키지명을 잘 인식하지 못하는 문제가 있음.) 빌드가 시작되면, expo사이트에서 빌드취소 가능(빌드횟수를 아끼기 위해서!)

```bash
eas build --platform android --profile development
eas build --platform android --profile production
```

#### Google Cloud 콘솔 프로젝트 생성 및 인증 클라이언트 생성

- https://console.cloud.google.com/ 접속
- 프로젝트 생성
- 메뉴 > 모든제품보기 > Google 인증 플랫폼 > 클라이언트 > 클라이언트 만들기
- 애플리케이션 유형 : Android (dev용 하나, production 용 하나 총 2개 생성)
  - {App name} Android 클라이언트
  - {App name} (dev) Android 클라이언트
  - 이름 : 위의 이름
  - 패키지 이름 : com.{company}.{appname} 또는 com.{company}.{appname}.dev
  - SHA-1 인증서 디지털 지문 : expo > 프로젝트 > Credentials > Android > 해당 패키지 선택 > SHA-1 Fingerprint 복사하여 입력
- 애플리케이션 유형 : iOS
  - {App name} iOS 클라이언트
  - {App name} (dev) iOS 클라이언트
  - 이름 : 위의 이름
  - 번들 ID : com.{company}.{appname} 또는 com.{company}.{appname}.dev

## 빌드

#### react-native-google-signin 설치

```bash
npx expo install @react-native-google-signin/google-signin
```

#### app.config.ts 내용추가

```ts
const IS_DEV = process.env.EXPO_PUBLIC_APP_VARIANT === "development";
// iosUrlScheme : 구글 클라우드 콘솔 > 프로젝트 >  모든 제품 보기 > 구글 인증 > 클라이언트 > ios 클라이언트 선택 > ios Url 스키마
const iosUrlScheme = IS_DEV ? "{iosUrlScheme (dev)}" : "{iosUrlScheme}";

export default {
  expo: {
    plugins: [
      [
        "@react-native-google-signin/google-signin",
        {
          iosUrlScheme: iosUrlScheme,
        },
      ],
    ],
  },
};
```

> plugins 안에 내용을 설정할 때는 위에서 변수를 먼저 선언하고 다른 환경변수가 아닌, IS_DEV로 구분해준 뒤에 설정을 해줘야 동작하는 현상이 있다.

- iosUrlScheme을 직접 설정 (`iosUrlSchem : IS_DEV ? "{}" : "{}"`) 하는 방법 실패
- iosUrlScheme을 환경변수로 직접 설정 (`iosUrlScheme : process.env.IOS_URL_SCHEME`) 방법 실패
- iosUrlScheme을 변수로 설정하되 환경변수를 이용하는 방법 (`const iosUrlScheme = process.env.IOS_URL_SCHEME`) 실패
- 예상하기론 EXPO_PUBLIC 미사용 + plugins 안에서 분기가 안되는 것으로 생각됨.

결론 : plugins 안에서 사용할 내용중 Development에 따라 달라질 내용이 있다면, IS_DEV를 통한 변수를 설정하고 그 변수를 안에 넣어주자!

#### 빌드 명령

```bash
eas build --profile development --platform android
eas build --profile development --platform ios
```

## GoogleSignin 코드

- GoogleSignin.Signin 함수도 있지만, iOS에서는 이 함수를 사용하면 계속 로그인 화면이 나옴.
- GoogleSignin.signInSilently() 함수를 사용하면, 로그인이 되어있는 경우, 자동으로 로그인하게 됨.

```tsx
import {
  GoogleSignin,
  GoogleSigninButton,
} from "@react-native-google-signin/google-signin";
import { useEffect } from "react";
import { Platform, Text, View } from "react-native";

const signinWithGoogle = async () => {
  try {
    await GoogleSignin.hasPlayServices();
    const googleUser = (await GoogleSignin.signIn()).data;
    console.log(googleUser);
  } catch (error) {
    console.error(error);
  }
};

export default function Index() {
  useEffect(() => {
    // 구글 클라우드 콘솔 > 프로젝트 > 모든 제품 보기 > 구글 인증 > 클라이언트 > ios 클라이언트 id
    const iosClientId = __DEV__ ? "{iosClientId (dev)}" : "{iosClientId}";
    GoogleSignin.configure({ iosClientId: iosClientId });
  }, []);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
            <GoogleSigninButton onPress={signinWithGoogle} />   {" "}
    </View>
  );
}
```

- 이미 로그인 되어있다면, 자동으로 로그인하려면 SigninSilently() 함수를 사용하면됨. (ios에서는 Signin()를 사용하면 매번 로그인이 필요함.)

#### Universal Google sign in (실패)

https://react-native-google-signin.github.io/docs/one-tap

- Google Sign in을 구현하기 위해 구글에서 추천하는 방법임.
- 기존 모듈 `GoogleSignin` 대신 `GoogleOneTapSignIn`을 사용함.
- 이걸 해보려고 했는데 아예 GoogleOneTapSignin이 없다고 나와서 보류함.
