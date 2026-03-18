## React SPA - 로그인 & 회원가입 구조 (회사별 접근 제어)

### 1️⃣ 로그인 페이지 (공통)
- 회사별로 따로 페이지 만들 필요 없음
- 로그인 시 **회사 선택** 또는 **초대 코드 입력** 방식 사용

```jsx
<form onSubmit={handleLogin}>
  <input type="text" name="username" placeholder="Username" />
  <input type="password" name="password" placeholder="Password" />
  <select name="company">
    <option value="A">Company A</option>
    <option value="B">Company B</option>
  </select>
  <button type="submit">로그인</button>
</form>
```

- 로그인 시 서버에 ID/PW + company 전송
- JWT 발급 후 payload에 `company` 포함
- 클라이언트 `userUserStore`에 저장

---

### 2️⃣ 회원가입 구조 (회사별 제한)
- 일반 사용자가 마음대로 가입하지 못하게 제한
- 접근 방식:
  1. **초대 코드 방식**
     - 회사 관리자가 생성한 inviteCode 필요
     - 서버에서 inviteCode 확인 후 계정 생성
  2. **관리자 승인 방식**
     - 사용자가 가입 신청 → 관리자 승인 → 계정 활성화

```jsx
<form onSubmit={handleSignup}>
  <input name="username" placeholder="Username" />
  <input name="password" type="password" placeholder="Password" />
  <input name="inviteCode" placeholder="Invite Code" />
  <button type="submit">회원가입</button>
</form>
```

- 서버에서 inviteCode로 회사 정보 확인 → JWT payload에 `company` 포함
- 회원가입 완료 시 `userUserStore`에 저장

---

### 3️⃣ 서버 API 처리
**회원가입 API**
1. inviteCode로 회사 확인
2. 기존 사용자 중복 체크
3. 계정 생성 + JWT 발급 (payload에 company 포함)

**로그인 API**
- 기존처럼 username/password + company 확인
- JWT 발급 후 payload에 회사 정보 포함

---

### 4️⃣ 클라이언트 처리
- JWT + company 정보 `userUserStore`에 저장
- CompanyAuthGuard에서 URL :company와 비교하여 접근 제어

```js
const currentUser = userUserStore.getState().user;
if (currentUser.company !== params.company) {
  return <Navigate to="/unauthorized" />;
}
```

---

### ✅ 장점
- 회사별 접근 제어 가능
- 페이지 중복 없이 공통 로그인/회원가입 페이지 사용
- 관리자가 초대 코드 발급으로 회원가입 통제 가능

