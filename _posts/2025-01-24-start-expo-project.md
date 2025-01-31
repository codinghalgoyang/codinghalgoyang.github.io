---
layout: single
title: "expo 프로젝트 시작하기"
---

#### expo-cli 설치

```
npm install --global expo-cli
```

#### 프로젝트 생성

`expo init 프로젝트명`으로 시작하는 방법도 있지만, 아래 명령어를 사용하면 expo-router, 기본 폴더 구조등이 잘 잡혀있음.

```
npx create-expo-app@latest # expo-router, 폴더 구조등 기본 설정이 되어있음
```

#### 프로젝트 실행

```
npm run start

# 또는

npx expo start # 그냥 expo start를 하면, 버전 확인하라는 문구가 발생하는 듯. (npx expo 랑 그냥 expo와 버전차이가 있는 듯함.)
```
