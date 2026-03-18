# SecurityUtils 

# 역할 
JWT 인증에서 사용하는 유틸 클래스 (도우미)

# 하는 일
1. 권한 변환
- USER -> ROLE_USER 변환
- Spring Security 형식 맞춰줌

2. 토큰 추출
- HTTP 요청 헤더에서 JWT 꺼냄 
(프론트엔드에서 로그인 시 서버 엔드포인트로 json 형태로 사용자 id, password 정보를 토큰과 함께 post. 서버에서는 그 json 정보 중 jwt 토큰만 추출해 검증)

- "Bearer" 제거하고 순수 토큰만 반환 
(Bearer는 리액트에서 토큰을 보낼때 이 값이 인증 토큰이다 라고 서버에 알려주는 표준 방식)

# 한 줄 정리
JWT 인증에서 "토큰 꺼내고 + Spring Security 권한 형식 맞추는 유틸"