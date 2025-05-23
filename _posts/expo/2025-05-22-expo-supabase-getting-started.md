---
layout: single
title: "[expo] supabase 초기 설정"
categories: expo
---

#### supabase 프로젝트 만들기

- https://supabase.com/
- Start your project > New project (dev용 하나, production용 하나)

#### 패키지 설치

```bash
npx expo install @supabase/supabase-js @react-native-async-storage/async-storage react-native-url-polyfill
```

#### supabase.ts 생성

```ts
import AsyncStorage from "@react-native-async-storage/async-storage";
import { createClient } from "@supabase/supabase-js";

// Get from supabase > project > Project Settings > Data API
const supabaseUrl = __DEV__ ? "{supabaseUrl (dev)}" : "{supabaseUrl}";
const supabaseAnonKey = __DEV__ ? "{anonKey (dev)}" : "{anonKey}";

export const supabase = createClient(supabaseUrl, supabaseAnonKey, {
  auth: {
    storage: AsyncStorage,
    autoRefreshToken: true,
    persistSession: true,
    detectSessionInUrl: false,
  },
});
```

#### 참고자료

- https://docs.expo.dev/guides/using-supabase/
