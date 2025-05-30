---
layout: single
title: "[expo] 프로젝트 시작하기"
categories: expo
---

## 개발환경

> - 공식적으로 지원이 잘되는 걸 쓰자. (웹 + firebase)
> - Hybrid로 개발한다. native의 기능은 expo를 사용하고, 대부분은 UI는 web으로 구현하자

#### Web vs Hybrid(expo+web) vs Native(expo)

- Web
  - web용 광고인 adsense는 보상형 광고를 제공하지 않음. (앱용 광고인 admob에서만 제공)
  - native 기능을 충분히 활용하지 못함
- Native(expo)
  - 막상 react native를 사용해보니, 버그 및 오류가 많음.
  - web 기술에 기반한 OAuth 구현면에서도 web + app 에 대한 내용이 추가적으로 들어가서 설정에서 어려움을 많이 겪음.(ios에서 google signin accesToken을 통한 supabase google signin 미지원, ios에서 kakao login 실패했고, ios 인증에 대한 부분이 좀 더 깊은 이해가 필요해보임) 이걸 web으로 구현하면 좀 더 구조가 명확해지고 간단해질 것으로 생각함.
- Hybrid(expo web view + web)
  - native 에 비해 웹호스팅을 해야하고, expo 프로젝트와 next.js 프로젝트가 2개 운영되어야함.
  - 대신 ios, android에 따른 컴포넌트가 다르게 동작하거나 하는 부분이 없을 것 같음
  - expo 빌드를 굳이 개발/양산으로 나눌 필요 없을듯. 처음 시작할때만 development로 가다가 production으로 변경하고, 나머지 개발은 웹개발에 집중할 수 있음.

#### supabase vs firebase

- firebase의 NoSQL database의 단점으로 supabase로 전환을 고려함.
- supabse의 버그에 부딪힘. 특정 버전을 사용해야하는 점도 있었음.
- 안정성 및 편의성을 따졌을 때는 firebase가 더 나아보임
- firebase의 NoSQL database의 단점을 극복할 수 있는 방법을 좀 더 찾아보기로 함.
