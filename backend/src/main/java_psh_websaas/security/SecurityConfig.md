# SecurityConfig 

# 역할
Spring Security 설정으로 인증 방식 + 접근 권한 정의
(SaasS 방어 : 여러 고객사가 화면을 동시에 접솔 할때, A사 직원이 B사 프로젝트를 조회하는 사고를 막기 위한 접근 권한 정의)

# 주요 설정

1. Corse 설정
- React 등 다른 도메인에서 API 호출 허용

2. 세션 사용 안함 (JWT 방식)
- 서버에 세션 저장 x (웹 상태를 session에 저장 x)
- 매 요청마다 토큰으로 인증

# 접근 권한 설정
1. 모두에게 허용
.requestMatchers ("/controller 엔드포인트").permitAll()

2. 로그인 필요 - 로그인 한 사용자는 다 볼 수 있는 페이지
.requestMatchers ("/controller 엔드포인트").authenticated()

3. 관리자만 접근 - 각 기업별로 Role을 나눠 접근 권한
.requestMatchers("/controller 엔드포인트").hashRole(Role.A사.name())
.requestMatchers("/controller 엔드포인트").hashRole(Role.B사.name())


# 한 줄 정리
JWT 기반으로 세션 없이 인증하고, URL 별로 접근 권한을 설정하는 보안 설정
