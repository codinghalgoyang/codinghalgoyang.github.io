---
layout: single
title: "[expo] App 아이콘(로고)"
categories: expo
---

## 파일 포맷과 사이즈는 어떤걸 사용하는게 좋은가?
- 포맷 : png (투명 배경 지원)
- 사이즈 : 512 x 512 (앱스토어에서 요구하는 최소 사이즈가 512 x 512 임)

## 적용 방법

app.json 수정 후 재빌드

```json
{
  "expo": {
    "icon": "./assets/icon.png"
  }
}
```

> expo 프로젝트에서는 바로 안보일 수 있어 release 버전의 빌드가 필요할 수 있음.