## React SPA - 회사별 접근 제어 (CompanyAuthGuard)

### 1️⃣ useAuth 훅 예시
```jsx
import { useContext } from 'react';
import { AuthContext } from '../contexts/AuthContext';

export const useAuth = () => {
  const { currentUser } = useContext(AuthContext);
  return { currentUser };
};
```
- 로그인 시 JWT 디코딩 후 `currentUser`에 회사 정보를 저장한다고 가정

---

### 2️⃣ CompanyAuthGuard 구현
```jsx
import { Navigate, useParams } from 'react-router-dom';
import { useAuth } from '../hooks/useAuth';

const CompanyAuthGuard = ({ children }) => {
  const { currentUser } = useAuth();
  const { company } = useParams(); // URL에서 :company 파라미터 추출

  // 로그인 여부 체크
  if (!currentUser) {
    return <Navigate to="/login" />;
  }

  // 회사 일치 여부 체크
  if (currentUser.company !== company) {
    return <Navigate to="/unauthorized" />;
  }

  return children;
};

export default CompanyAuthGuard;
```

---

### 3️⃣ 라우팅 예시
```jsx
import CompanyAuthGuard from './guards/CompanyAuthGuard';
import CompanyDashboard from './pages/CompanyDashboard';

<Route path='/company/:company/dashboard' element={
  <CompanyAuthGuard>
    <CompanyDashboard />
  </CompanyAuthGuard>
}/>
```

---

### 4️⃣ 옵션: 회사 + Role 조합
```jsx
if (currentUser.company !== company || currentUser.role !== 'MANAGER') {
  return <Navigate to="/unauthorized" />;
}
```
- 회사별 접근 + 역할별 접근이 동시에 필요할 때 사용 가능

---

### ✅ 장점
- 회사별 접근을 URL과 JWT 기반으