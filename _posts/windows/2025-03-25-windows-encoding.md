---
layout: single
title: "[windows] 문자열 인코딩과 ASCII, ANSI, UNICODE 관련 내용 정리"
categories: windows
---

#### 문자열 인코딩이란?

컴퓨터에서 문자를 디지털 형식으로 표현하기 위해 특정한 규칙에 따라 문자를 비트로 바꾸는 과정

#### ANSI와 UNICODE는 특정 인코딩 방식이 아니다.

- ASCII는 영어, 숫자, 특수문자 정도를 지원하기 위한 인코딩 방식. (7bits, 128개 문자 표현)
- ANSI는 ASCII가 지원하는 영어 외 다른 문자도 지원하기 위한 표준임. (특정 인코딩방식이 아니라 여러 문자 인코딩을 지칭하는 용어)
  - ANSI 계열 인코딩 방식에는 Windows-1252(서유럽 언어 지원을 위한 인코딩), ISO-8895-1(라틴 알파벳을 사용하는 서유럽 언어를 위한 인코딩), CP1250(중앙 유럽 언어를 위한 인코딩) 등이 존재함.
- UNICODE는 전 셰게의 문자를 표현하기 위한 통합 표준임. (특정 인코딩 방식이 아니라, 전 세계 모든 문자를 표현하기 위한 여러 문자 인코딩들을 지칭하는 용어)
  - UTF-8 : 유니코드 계열 인코딩 방식, 1~4 바이트까지 가변 길이를 가짐, 전 세계의 모든 문자를 표현 가능
  - UTF-16 : 유니코드 계열 인코딩 방식, 주로 아시아 언어를 포함한 더 많은 문자를 표현할 수 있는 인코딩
