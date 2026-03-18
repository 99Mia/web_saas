
# Spring Boot Controller Documentation

## 목적
- 사용자 및 프로젝트 관련 요청 처리
- 회사별 프로젝트 접근 관리
- REST API 엔드포인트, 요청/응답, 권한 설명

---

## 1. 회사별 엔드포인트 (Company-specific)

### 전체 프로젝트 조회 (A사)
**GET** `/api/company/A/projects`
- Header: `Authorization: Bearer {token}`
- 서버에서 토큰 검증 후 A사 사용자만 접근 허용
- 응답 예시:
```json
[
  {"projectId": 1, "name": "Project A1", "status": "ACTIVE"},
  {"projectId": 2, "name": "Project A2", "status": "PLANNED"}
]
```

### 전체 프로젝트 조회 (B사)
**GET** `/api/company/B/projects`
- Header: `Authorization: Bearer {token}`
- B사 사용자만 접근 허용
- 응답 예시:
```json
[
  {"projectId": 3, "name": "Project B1", "status": "ACTIVE"},
  {"projectId": 4, "name": "Project B2", "status": "PLANNED"}
]
```

### 특정 프로젝트 상세 조회
**GET** `/api/company/{company}/projects/{id}`
- Path Variable: `company`, `id`
- Header: `Authorization: Bearer {token}`
- 서버에서 회사/권한 검증 후 프로젝트 상세 반환

---

## 2. 공통 엔드포인트 + 토큰 기반 필터링 (Recommended)

### 전체 프로젝트 조회
**GET** `/api/projects`
- Header: `Authorization: Bearer {token}`
- 서버가 JWT 토큰의 `company`를 확인 후 해당 회사 프로젝트만 반환
- 응답 예시 (A사 로그인):
```json
[
  {"projectId": 1, "name": "Project A1", "status": "ACTIVE"},
  {"projectId": 2, "name": "Project A2", "status": "PLANNED"}
]
```

- 응답 예시 (B사 로그인):
```json
[
  {"projectId": 3, "name": "Project B1", "status": "ACTIVE"},
  {"projectId": 4, "name": "Project B2", "status": "PLANNED"}
]
```

### 특정 프로젝트 상세 조회
**GET** `/api/projects/{id}`
- Header: `Authorization: Bearer {token}`
- 서버가 토큰의 `company` 확인 후 해당 회사 프로젝트만 반환
- 응답 예시:
```json
{
  "projectId": 1,
  "name": "Project A1",
  "description": "상세 설명",
  "status": "ACTIVE",
  "members": ["hong", "kim"]
}
```

### 프로젝트 상태 변경
**PUT** `/api/projects/{id}`
- Header: `Authorization: Bearer {token}`
- Body:
```json
{
  "status": "COMPLETED"
}
```
- ADMIN 권한 필요

---

## 접근 권한 관리
- 로그인 후 발급된 JWT 토큰에 `roles`와 `company` 포함
- 서버에서 요청 시 토큰 확인 → 회사/권한 필터링
- React 프론트에서는 토큰을 헤더에 포함하여 API 호출
- React 라우트에서도 `company`와 `roles` 기반 조건부 렌더링 가능

---

## 설명
- 회사별 엔드포인트는 URL만 보고 회사 확인 가능
- 공통 엔드포인트는 유지보수가 쉽고 코드 중복 최소화
- 실제 구현 시 회사 수에 따라 선택
- 프로젝트 관련 API는 회사 필터링 필수
