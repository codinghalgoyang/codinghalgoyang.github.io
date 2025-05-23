---
layout: single
title: "[expo] google signin"
categories: expo
---

회원 가입 및 관리의 편의성을 위해 구글 로그인을 지원

## 시행착오

- 처음 생각 : database 활용면에서 firebase보단 supabase가 더 내 프로젝트에 적합하다고 생각해서, firebase를 아예 배제하고 작업하려고 함.
- firebase 없이 구글 클라우드 콘솔(구글 인증 서비스 제공)에 직접 프로젝트를 생성하고, 구글 인증 클라이언트들을 만들어서 설정하는 방법을 시도했으나, ios에서 불안정한 모습을 보임
- 결론 : 구글 클라우드 콘솔을 따로 설정하는 방법이 불안정함. firebase에서 프로젝트를 생성해 구글 클라우드 콘솔 연동되도록 하자.
- firebase를 사용하면 firebase-sdk 도 설치해야한다고 생각했지만, 그렇지는 않음. 그냥 구글 로그인만 firebase 에서 지원해주는 것 뿐임.

## 빌드 준비

- react-native-google-signin은 기본 expo go 앱에 포함되어있지 않아, 새로 빌드가 필요함
- firebase 구글 로그인을 사용할 경우, react-native-google-signin은 google service 파일(안드로이드의 경우, json 파일, ios의 경우 plist 파일)을 필요로 함

#### 안드로이드 SHA-1 키 준비 (firebase 안드로이드 프로젝트에 등록필요)

- 구글 로그인을 위해서는 구글 클라우드 콘솔의 구글 인증 플랫폼(또는 firebase)에 클라이언트를 추가해줘야함.
- 안드로이드의 경우, 클라이언트에 SHA-1키를 함께 등록해줘야함.
- SHA-1키는 eas build 를 통해 생성할 수 있음. (eas credentials 사용시 dev용 패키지명을 잘 인식하지 못하는 문제가 있음.) 빌드가 시작되면, expo사이트에서 빌드취소 가능(빌드횟수를 아끼기 위해서!)

```bash
eas build --platform android --profile development
eas build --platform android --profile production
```

#### firebase 프로젝트 생성 및 설정

- firebase 콘솔 > firebase 프로젝트 만들기 > dev용 하나, production용 하나 총 2개 생성
- 구글 Authentication 활성화
  - firebase 콘솔 > 프로젝트 선택 (dev 또는 production) > 빌드 > Authentication > 시작하기
  - 로그인 방법 > 구글 > 사용설정 > 프로젝트 지원 이메일 선택 후 저장
- ios 앱 생성
  - firebase 콘솔 > 프로젝트 선택 (dev 또는 production) > 프로젝트 설정 > 앱추가 > ios+
  - apple 번들 ID : com.{company}.{appname}.{dev} 또는 com.{company}.{appname}
  - 나머지는 다 다음 다음 다음 하면서 넘겨버리기
- 안드로이드 앱 생성
  - firebase 콘솔 > 프로젝트 선택 (dev 또는 production) > 프로젝트 설정 > 앱추가 > 안드로이드
  - android 패키지 이름 : com.{company}.{appname}.{dev} 또는 com.{company}.{appname}
  - 디버그 서명 인증서 SHA-1 : expo 사이트 > 프로젝트 선택 > Credentials > Android Application identifier 선택 > SHA-1 Fingerprint
  - 나머지는 다 다음 다음 다음 하면서 넘겨버리기

#### google service 파일 다운로드

- firebase 콘솔 > 프로젝트 선택 (dev 또는 production) > 프로젝트 설정 > 앱 (안드로이드 또는 ios) 선택 > google-services.json 또는 GoogleService-Info.plist 다운로드
- 추후 파일 구분을 위해 dev의 경우, 파일명 앞에 dev를 달아둠. (앞쪽에 달아놓으면 이후 과정에서 좋음.)
- development 용 프로젝트
  - android 앱 : dev-google-services.json
  - ios 앱 : dev-GoogleService-Info.plist
- production 용 프로젝트
  - android 앱 : google-services.json
  - ios 앱 : GoogleService-Info.plist

#### google service 파일 환경변수 등록

expo 사이트 > 프로젝트 > Environment varaibles > Add Variables

- GoogleService-Info.plist
  - Name : GOOGLE_SERVICE_INFO_PLIST
  - value : 위 파일 업로드
  - Visibility : Secret
  - Environment : production, preview
- dev-GoogleService-Info.plist
  - Name : GOOGLE_SERVICE_INFO_PLIST
  - value : 위 파일 업로드
  - Visibility : Secret
  - Environment : development
- google-services.json
  - Name : GOOGLE_SERVICES_JSON
  - value : 위 파일 업로드
  - Visibility : Secret
  - Environment : production, preview
- dev-google-services.json
  - Name : GOOGLE_SERVICES_JSON
  - value : 위 파일 업로드
  - Visibility : Secret
  - Environment : development

> dev를 앞에 붙이면 내가 올린 파일이 dev인지 아닌지 보임. 파일명에서 뒷부분은 잘려서 안보임.

## 빌드

#### react-native-google-signin 설치

```bash
npx expo install @react-native-google-signin/google-signin
```

#### app.config.ts 내용추가

```ts
export default {
    expo: {
        ios: {
            googleServicesFile: process.env.GOOGLE_SERVICE_INFO_PLIST,
        }
        android: {
            googleServicesFile: process.env.GOOGLE_SERVICES_JSON,
        }
        plugins: [
	        ["@react-native-google-signin/google-signin"],
        ]
    }
}
```

> 이름 주의 : GOOGLE_SEVICES_JSON에는 service 뒤에 S가 있고, GOOGLE_SERVICE_INFO_PLIST에는 service 뒤에 S가 없음. 처음에 이거 잘못해서 찾는데 힘들었음.
> 또, googleServicesFile에는 모두 s가 붙음

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
  isSuccessResponse,
} from "@react-native-google-signin/google-signin";
import { useEffect } from "react";
import { Text, View } from "react-native";

const signinWithGoogle = async () => {
  try {
    await GoogleSignin.hasPlayServices();
    const response = await GoogleSignin.signIn();
    if (isSuccessResponse(response)) {
      const googleUser = response.data;
      console.log("googleUser : ", googleUser);
    }
  } catch (error) {
    console.error(error);
  }
};

export default function Index() {
  useEffect(() => {
    GoogleSignin.configure();
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
