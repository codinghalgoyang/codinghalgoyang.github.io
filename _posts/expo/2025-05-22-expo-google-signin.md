---
layout: single
title: "[expo] google signin"
categories: expo
---

- 구글 로그인을 지원하여 회원가입의 편의성을 높이자.

> google-signin은 firebase를 사용하던 사용하지 않던, 사용하는 경우와 사용하지 않는 경우, 코드와 환경 세팅이 달라짐.
> 크게는 firebase를 사용하는 경우, firebase가 clientID등이 적혀있는 plist와 json 파일을 사용하도록 되어있어서 해당 파일을 app.config.ts 파일에 명시하고 빌드시에 같이 넣어 빌드를 해야한다. 대신, GoogleSignin.configure()시 파라미터를 넣어주지 않아도된다.
> firebase를 사용하지 않는 경우, plist와 json 파일을 따로 넣어주지 않는 GoogleSignin.configure() 안에 webClientID를 넣어줘야한다. ios의 경우 iosUrlScheme을 app.config.ts 파일에 명시해줘야한다.

#### react-native-google-signin 설치

```bash
npx expo install @react-native-google-signin/google-signin
```

#### GoogleSignin

- GoogleSignin.Signin 함수도 있지만, iOS에서는 이 함수를 사용하면 계속 로그인 화면이 나옴.
- GoogleSignin.signInSilently() 함수를 사용하면, 로그인이 되어있는 경우, 자동으로 로그인하게 됨.

```tsx
import { GoogleSignin, User } from "@react-native-google-signin/google-signin";
import { Dispatch, SetStateAction, useEffect, useState } from "react";
import { Text, View } from "react-native";

const signIn = async (setUser: Dispatch<SetStateAction<User | null>>) => {
  try {
    await GoogleSignin.hasPlayServices();
    const googleUser = (await GoogleSignin.signInSilently()).data;
    setUser(null);
  } catch (error) {
    // GoogleSignin.signInSilently error occurs
    console.log(error);
  }
};

export default function Index() {
  const [user, setUser] = useState<User | null>(null);
  useEffect(() => {
    signIn(setUser);
  }, []);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
            <Text> {`user : ${user?.user.id}`}</Text>   {" "}
    </View>
  );
}
```

## 빌드 준비

- react-native-google-signin은 기본 expo go 앱에 포함되어있지 않아, 새로 빌드가 필요함
- firebase를 사용할 땐, plist, json 파일을 다운로드 받아 eas 환경변수에 업로드하는 과정을 거쳤는데, plist와 json 파일은 firebase와 관련된 부분이라, firebase를 사용하지 않는 환경(구글 클라우드 콘솔을 직접 사용하는 환경)에서는 그 부분이 필요하지 않다고 함.

#### 안드로이드 SHA-1 키 준비

- 구글 로그인을 위해서는 구글 클라우드 콘솔의 구글 인증 플랫폼에 클라이언트를 추가해줘야함.
- 안드로이드의 경우, 클라이언트 추가시 패키지명과 함께 SHA-1키를 함께 등록해줘야함.
- SHA-1키는 eas build 를 통해 생성할 수 있음. (eas credentials 사용시 dev용 패키지명을 잘 인식하지 못하는 문제가 있음.)

```bash
eas build --platform android --profile development
eas build --platform android --profile production
```

- 실행시, eas-cli 설치 물으면 Yes
- Generate a new Android Keystore? Yes

> 실제 빌드가 시작되면 expo 사이트 빌드에서 취소! (콘솔에서 ctrl+c 눌러도 실제로는 온라인상에서 빌드중이므로 expo 사이트에 들어가서 취소 필요!)

#### Google Cloud 콘솔 프로젝트 생성 및 인증 클라이언트 생성

- https://console.cloud.google.com/ 접속
- 프로젝트 생성
- 메뉴 > 모든제품보기 > Google 인증 플랫폼 > 클라이언트 > 클라이언트 만들기
  - {App name} Android 클라이언트
  - {App name} (dev) Android 클라이언트
    - 애플리케이션 유형 : Android
    - 이름 : 위의 이름
    - 패키지 이름 : com.{company}.{appname} 또는 com.{company}.{appname}.dev
    - SHA-1 인증서 디지털 지문 : expo > 프로젝트 > Credentials > Android > 해당 패키지 선택 > SHA-1 Fingerprint 복사하여 입력
  - {App name} iOS 클라이언트
  - {App name} (dev) iOS 클라이언트
    - 애플리케이션 유형 : iOS
    - 이름 : 위의 이름
    - 번들 ID : com.{company}.{appname} 또는 com.{company}.{appname}.dev

#### app.config.ts 수정

> plugins 안쪽에 IS_DEV에 따라 iosUrl Scheme을 설정해줘야하는데, plugins 쪽에서는 process.env를 사용할 수 없음

- app.config.js는 빌드 시점에 실행되어 앱의 설정을 결정함.
- plugin 내용은 런타임에 실행되는 것 같음..

해결 방법: config를 IS_DEV에 따라 다르게 return 하는 함수를 만듦

```ts
// 구글 클라우드 콘솔 > 프로젝트 선택 > 구글 인증 플랫폼 > 클라이언트 > ios 클라이언트 iOS URL 스키마마
// 현재 위치에서 static 값이 아닌 process.env.IOS_URL_SCHEME 을 넣었을땐 동작하지 않음. 이유는 아직 잘 모르겠음
const iosUrlScheme = IS_DEV ? "{iosUrl (dev)}" : "{iosUrl}";

export default {
  expo: {
	  ios: {
	    infoPlist: {
	      ITSAppUsesNonExemptEncryption: false,
	    },
	  }
      plugins: [
      [
        "@react-native-google-signin/google-signin",
        {
          iosUrlScheme: iosUrlScheme,
        },
      ],
    ],
    ...
};
```

- 안드로이드의 경우, 빌드시 사용하는 SHA-1키를 통해 따로 등록이 필요없는듯 하고, ios의 경우 iosUrlScheme 설정을 통해 그 역할을 하고 있는 것 같음.

> 이유는 모르겠으나, plugins 안에 내용을 설정할 때는 위에서 변수를 먼저 선언하고 다른 환경변수가 아닌, IS_DEV로 구분해준 뒤에 설정을 해줘야 동작하는 현상이 있다.
>
> - iosUrlScheme을 직접 설정 (`iosUrlSchem : IS_DEV ? "{}" : "{}"`) 하는 방법 실패
> - iosUrlScheme을 환경변수로 직접 설정 (`iosUrlScheme : process.env.IOS_URL_SCHEME`) 방법 실패
> - iosUrlScheme을 변수로 설정하되 환경변수를 이용하는 방법 (`const iosUrlScheme = process.env.IOS_URL_SCHEME`) 실패
> - 예상하기론 EXPO_PUBLIC 미사용 + plugins 안에서 분기가 안되는 것으로 생각됨.

결론 : plugins 안에서 사용할 내용중 Development에 따라 달라질 내용이 있다면, IS_DEV를 통한 변수를 설정하고 그 변수를 안에 넣어주자!

#### 빌드

```bash
eas build --profile development --platform android
eas build --profile development --platform ios
```

#### 코드

```typescript
import { GoogleSignin, User } from "@react-native-google-signin/google-signin";
import { Dispatch, SetStateAction, useEffect, useState } from "react";
import { Platform, Text, View } from "react-native";

const signIn = async (setUser: Dispatch<SetStateAction<User | null>>) => {
  try {
    await GoogleSignin.hasPlayServices();
    const response = await GoogleSignin.signIn();
    const googleUserInfo = response.data;
    console.log(googleUserInfo);
    setUser(googleUserInfo);
  } catch (error) {
    // GoogleSignin.signInSilently error occurs
    console.log(error);
  }
};

export default function Index() {
  const [user, setUser] = useState<User | null>(null);
  useEffect(() => {
    if (Platform.OS === "android") {
      GoogleSignin.configure();
    } else {
      const iosClientId = __DEV__ ? "{iosClientId (dev)}" : "{iosClientId}";
      GoogleSignin.configure({ iosClientId: iosClientId });
    }

    signIn(setUser);
  }, []);

  return (
    <View
      style={{
        flex: 1,
        justifyContent: "center",
        alignItems: "center",
      }}
    >
      <Text> {`user : ${user?.user.email}`}</Text>
    </View>
  );
}
```

- 이미 로그인 되어있다면, 자동으로 로그인하려면 SigninSilently() 함수를 사용하면됨. (ios에서는 Signin()를 사용하면 매번 로그인이 필요함.)

#### Universal Google sign in (실패)

https://react-native-google-signin.github.io/docs/one-tap

- Google Sign in을 구현하기 위해 구글에서 추천하는 방법임.
- 기존 모듈 `GoogleSignin` 대신 `GoogleOneTapSignIn`을 사용함.
- 이걸 해보려고 했는데 아예 GoogleOneTapSignin이 없다고 나옴.
