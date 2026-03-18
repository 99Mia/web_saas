# jwtProviderImpl Class Documentation

## 패키지 및 클래스
- 클래스: `jwtProviderImpl`
- 인터페이스 구현: `JwtProvider`

## 역할
- JWT 토큰 생성
- 인증 정보 조회
- 토큰 유효성 검증
- 학습용 기본 코드 포함, 향후 기능 확장 가능

## 기본 코드 예시
```java
public class jwtProviderImpl implements JwtProvider {

    // 토큰 생성
    @Override
    public String generateToken(UserPrincipal userPrincipal) {
        String authorites = userPrincipal.getAuthorities().stream()
                .map(GrantedAuthority::getAuthority)
                .collect(Collectors.joining(","));
        Key key = Keys.hmacShaKeyFor(JWT_SECRET.getBytes(StandardCharsets.UTF_8));

        return Jwts.builder()
                .setSubject(userPrincipal.getUsername())
                .claim("roles", authorites)
                .setExpiration(new Date(System.currentTimeMillis() + JWT_EXPIRATION_IN_MS))
                .signWith(key, SignatureAlgorithm.HS512)
                .compact();
    }

    // 인증 정보 얻기
    public Authentication getAuthentication(HttpServletRequest request) {
        // 학습용 기본 코드, 구현 확장 가능
    } 

    // 토큰 유효성 검증
    public boolean isTokenValid(HttpServletRequest request) {
        // 학습용 기본 코드, 구현 확장 가능
    }
}
```

## 핵심 포인트
- 현재 문서에는 **기본 동작 로직**만 포함
- 인증/권한, 토큰 검증 등은 학습 및 확장 가능
- JWT 생성 로직은 실제 작동 예시 포함
- 길거나 복잡한 로직은 문서에서 생략 