
# userUserStore

## 목적
현재 로그인한 사용자 상태를 저장하고 관리하는 Zustand 스토어입니다.  
로그인 후 `user` 정보를 저장하고, 로그아웃 시 초기화합니다.  
`persist`를 이용해 브라우저 스토리지(localStorage 등)에 상태를 유지합니다.

---

## 사용법

### 1. 스토어 임포트
```js
import userUserStore from "../store/userUserStore";
```

### 2. 현재 사용자 저장
```js
userUserStore.getState().setCurrentUser({
  id: 123,
  username: "hong",
  token: "JWT토큰",
  company: "A",
  roles: ["ADMIN"]
});
```

### 3. 현재 사용자 초기화
```js
userUserStore.getState().clearCurrentUser();
```

---

## 코드

```js
import { create } from "zustand";
import { persist } from "zustand/middleware";

// 액션 타입
const SET_CURRENT_USER = "SET_CURRENT_USER";
const CLEAR_CURRENT_USER = "CLEAR_CURRENT_USER";

// 현재 로그인한 사용자 상태 저장
const userUserStore = create(
  persist(
    (set) => ({
      user: null,
      setCurrentUser: (user) =>
        set({
          user,
          lastAction: SET_CURRENT_USER,
        }),
      clearCurrentUser: () =>
        set({
          user: null,
          lastAction: CLEAR_CURRENT_USER,
        }),
    }),
    { name: "currentUser" } // 브라우저 스토리지 키 이름
  )
);

export default userUserStore;
```

---

## 설명
- `user`: 현재 로그인한 사용자 정보(JSON 형태)
- `setCurrentUser(user)`: 로그인 시 호출하여 `user` 상태 저장
- `clearCurrentUser()`: 로그아웃 시 호출하여 상태 초기화
- `persist`: 브라우저 스토리지에 상태를 유지
