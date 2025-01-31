---
layout: single
title: "[expo] expo-router 정리"
---

파일 기반 라우터 expo-router 사용하기

#### 시작

```
npx create-expo-app@latest
```

이렇게 프로젝트를 시작하면 자동으로 설치 및 설정이 완료됨.

#### page 만들기

`app` 디렉토리에 파일을 생성하면 자동 route 됨

```
- app
	- index.tsx # '/' 페이지
	- home.tsx # '/home' 페이지
	- [user].tsx # '/{user}' 페이지, 동적 path
	- settings
		- index.tsx # `/settings` 페이지
```

> `expo-router`는 `app` 폴더 내 파일을 기반으로 routing을 하기 때문에, routing 페이지만 들어가야함. 그 외 파일들(예. components, hooks)등은 따로 떼어내서 관리가 필요.

#### 플랫폼 별 layout

> expo router 3.5.x 버전 이상 지원

`.ios.tsx, .web.tsx, .native.tsx` 파일등을 지원
```
- app
	- _layout.tsx
	- _layout.web.tsx
	- index.tsx
	- about.tsx
	- about.web.tsx
```

#### 동적 라우팅
- `app/blog/[slug].tsx` 는 `/blog/{slug}` 경로

router paramter 사용방법
```tsx
import { useLocalSearchParams } from 'expo-router';
import { Text } from 'react-native';

export default function Page() {
  const { slug } = useLocalSearchParams();

  return <Text>Blog post: {slug}</Text>;
}
```

> `screen`, `params`, `key`는 Reserved keywords로 동적 라우트 파라미터로 사용할 수 없음.

#### 페이지 이동

`Link`를 사용하여 페이지 이동 (web의 `a` 태그와 `href` 속성과 유사함.)

페이지 구조
```
- app
	- index.tsx
	- about.tsx
	- user
		- [id].tsx
```

`app/index.tsx`

```tsx
import { View } from 'react-native';
import { Link } from 'expo-router';

export default function Page() {
  return (
    <View>
      <Link href="/about">About</Link>
      {/* ...other links */}
      <Link href="/user/bacon">View user</Link>
    </View>
  );
}
```

버튼 형태로 `Link` 사용하기
- `asChild` prop을 사용하여 자식 컴포넌트를 포워딩함.

```tsx
import { Pressable, Text } from 'react-native';
import { Link } from 'expo-router';

export default function Page() {
  return (
    <Link href="/other" asChild>
      <Pressable>
        <Text>Home</Text>
      </Pressable>
    </Link>
  );
}
```

#### Route

Link 사용
```tsx
<Link push href="/login">Login</Link> // push가 default 임
<Link replace href="/login">Login</Link>

// params 활용
<Link href={{
  pathname: '/user/[id]',
  params: { id: 'bacon' }
}}>View user</Link>
```



router 사용
```tsx
import { router } from 'expo-router';

router.replace('/login');
router.push('/login');
router.back();
```

#### 참고자료
- https://docs.expo.dev/router/create-pages/
