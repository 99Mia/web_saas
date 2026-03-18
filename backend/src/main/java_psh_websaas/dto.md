# 목적
- DTO(Data Transfer Object) 문서화
- API 요청/응답에서 사용되는 데이터 구조 정의
- 설명: 필드 의미, 타입, 필수 여부
- API 요청/응답 구조

# DTO Documentation

## UserDTO
- 설명: 사용자 정보 전달용
- 필드:
  - id (Long)
  - username (String)
  - roles (List<String>)
  - company (String)

## ProjectDTO
- 설명: 프로젝트 데이터 전달용
- 필드:
  - id (Long)
  - name (String)
  - description (String)
  - status (String) — 진행 상태 (예: 진행중, 완료, 대기)
  - stage (String) — 프로젝트 단계 (예: 기획, 개발, 테스트, 배포)
  - plannedStartDate (LocalDate) — 예정 시작일
  - plannedEndDate (LocalDate) — 예정 완료일
  - actualStartDate (LocalDate) — 실제 시작일
  - actualEndDate (LocalDate) — 실제 완료일
  - duration (Integer) — 총 기간(일)
  - members (List<String>) — 참여 멤버 이름