# 목적
- 프로젝트에서 사용하는 DB 엔티티와 필드 구조를 문서화
- 각 엔티티 관계 명시
- 필드 의미, 타입, 제약 조건 설명
- DB 구조 + 엔티티 관계

# Domain (Entity) Documentation

## User
- DB 테이블: users
- 설명: 로그인 사용자 정보
- 필드:
  - id (Long, PK)
  - username (String, unique)
  - password (String)
  - roles (Set<Role>)
  - company (String)

## Project
- DB 테이블: projects
- 설명: 각 회사 프로젝트 정보
- 필드:
  - id (Long, PK)
  - name (String)
  - description (String)
  - status (Enum: PLANNED, ACTIVE, COMPLETED) — 진행 상태
  - stage (String) — 프로젝트 단계 (예: 기획, 개발, 테스트, 배포)
  - plannedStartDate (LocalDate) — 예정 시작일
  - plannedEndDate (LocalDate) — 예정 완료일
  - actualStartDate (LocalDate) — 실제 시작일
  - actualEndDate (LocalDate) — 실제 완료일
  - duration (Integer) — 총 기간(일)
  - company (String)
  - members (List<User>)