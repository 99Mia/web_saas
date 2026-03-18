# JwtProvider Interface Documentation

## 패키지
`main.java_psh_websaas.security.jwt`

## 역할
- JWT 토큰 생성, 인증 정보 조회, 토큰 유효성 검증 기능 제공

## 메서드

### generateToken
```java
String generateToken(UserPrincipal userPrincipal);
```
- 사용자 정보를 기반으로 JWT 토큰 생성

### getAuthentication
```java
Authentication getAuthentication(HttpServletRequest request);
```
- 요청(HttpServletRequest)으로부터 인증 정보(Authentication) 조회

### isTokenValid
```java
boolean isTokenValid(HttpServletRequest request);
```
- 요청(HttpServletRequest)에 포함된 토큰의 유효성 검증