# Repository Documentation

## 목적
- Spring Data JPA Repository 인터페이스 문서화
- 각 Repository가 어떤 엔티티를 다루는지, 기본 CRUD 메서드와 커스텀 쿼리 설명

---

## UserRepository
- 인터페이스: `UserRepository extends JpaRepository<User, Long>`
- 설명: 사용자 정보 DB 접근
- 제공 메서드:
  - findByUsername(String username): User
  - existsByUsername(String username): boolean
  - save(User user)
  - findAll()
  - delete(User user)

---

## ProjectRepository
- 인터페이스: `ProjectRepository extends JpaRepository<Project, Long>`
- 설명: 프로젝트 정보 DB 접근
- 제공 메서드:
  - findByCompany(String company): List<Project>
  - findByIdAndCompany(Long id, String company): Optional<Project>
  - save(Project project)
  - findAll()
  - delete(Project project)

---

## 커스텀 쿼리 예시
- JPQL/Query Annotation 사용 예시
```java
@Query("SELECT p FROM Project p WHERE p.company = :company AND p.status = :status")
List<Project> findByCompanyAndStatus(@Param("company") String company, @Param("status") Status status);


--- 

## 설명
- Repository는 DB 접근 전용 계층
- 엔티티 테이블 별 Repository를 만들어 CRUD 및 조회 기능 제공
