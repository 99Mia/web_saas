# 백엔드 API 와 통신하는 코드를 모아놓는 곳

# 로그인 후 기업별 토큰 처리 
1. 사용자가 로그인 -> 서버에 JWT 발급
{
  "id": 123,
  "username": "hong",
  "roles": ["USER"],
  "company": "A",  // 로그인한 사용자의 회사
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}

2. React에서 이 토큰과 회사 정보를 상태에 저장 
userUserStore.setState({ user: { token: "...", roles: ["USER"], company: "A" } });

3. 로그인 후 라우팅
const currentUser = userUserStore.getState().user;

if(currentUser.company === 'A') {
  navigate('/dashboard-a');
} else if(currentUser.company === 'B') {
  navigate('/dashboard-b');
}

4. API 호출 시
- React에서는 엔드포인트를 회사별로 바꿀 수 있고, 같은 엔드포인트지만 토큰 내부 정보로 필터링 할 수도 있다.

## 방법1: 엔드포인트를 회사별로 나눔
- 회사마다 별도의 URL을 사용
- React에서 사용자가 속한 회사에 맞춰 URL을 선택해서 요청
- URL 만 보고 어떤 회사 데이터인지 알 수 있음
- 서버에서 회사별 엔드포인트를 만들어야 함

axios.get(BASE_API_URL + `/api/company/${currentUser.company}/data`, { headers: authHeader() });

## 방법2: 공통 엔드포인트, 서버에서 토큰의 company로 필터링
- 공통 엔드포인트를 만드는데, 토큰 안에 company = A 또는 B 정보가 들어있음 -> 유지보수 편함
- 서버에서 항상 토큰의 company 확인하고, 다른 회사 데이터는 반환하지 않도록 로직 필요

axios.get(BASE_API_URL + `/api/company/data`, { headers: authHeader() });


# 정리
- 토큰은 하나지만 payload 안에 company 정보가 들어있음
- React 라우터가 회사별 페이지로 이동
- 서버에서는 토큰에 들어있는 company 정보로 API 접근 제어



