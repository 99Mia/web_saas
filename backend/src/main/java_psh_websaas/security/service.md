# Service Layer Documentation (Company-wide + Project Admin Expansion)

## 목적
- Controller와 Repository 사이에서 비즈니스 로직 처리
- 회사별 데이터 접근 제어 (같은 회사 로그인한 사람은 모두 접근 가능)
- DTO 변환, 트랜잭션 관리
- 향후 프로젝트별 Admin 역할 확장 가능

---

## UserService

### 기능
- 사용자 정보 조회
- 회사 내 사용자 접근 허용 (권한 구분 없음)
- 향후 프로젝트별 역할 기반 접근 가능

### 예시
```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public UserDTO getUserProfile(Long userId, User currentUser) {
        User user = userRepository.findById(userId)
            .orElseThrow(() -> new NotFoundException("User not found"));

        // 회사 단위 접근 체크만 수행
        if (!user.getCompany().equals(currentUser.getCompany())) {
            throw new AccessDeniedException("권한 없음");
        }

        return UserMapper.toDTO(user);
    }
}
```

---

## ProjectService

### 기능
- 회사별 프로젝트 조회 (같은 회사 로그인 사용자 모두 접근 가능)
- 프로젝트 상세 조회
- 프로젝트 상태 변경 및 멤버 관리
- 향후 프로젝트별 Admin 역할 기반 접근 확장 가능

### 예시
```java
@Service
public class ProjectService {

    private final ProjectRepository projectRepository;

    public ProjectService(ProjectRepository projectRepository) {
        this.projectRepository = projectRepository;
    }

    public List<ProjectDTO> getProjects(User currentUser) {
        // 회사 단위 필터링만 수행
        return projectRepository.findByCompany(currentUser.getCompany())
                .stream()
                .map(ProjectMapper::toDTO)
                .collect(Collectors.toList());
    }

    public ProjectDTO getProject(Long projectId, User currentUser) {
        Project project = projectRepository.findById(projectId)
            .orElseThrow(() -> new NotFoundException("Project not found"));

        // 회사 단위 접근 체크만 수행
        if (!project.getCompany().equals(currentUser.getCompany())) {
            throw new AccessDeniedException("권한 없음");
        }

        return ProjectMapper.toDTO(project);
    }

    @Transactional
    public void completeProject(Long projectId, User currentUser) {
        Project project = projectRepository.findById(projectId)
            .orElseThrow(() -> new NotFoundException("Project not found"));

        // 회사 단위 접근 체크만 수행
        if (!project.getCompany().equals(currentUser.getCompany())) {
            throw new AccessDeniedException("권한 없음");
        }

        // 향후 확장: 프로젝트별 Admin 체크 예시 (commented)
        // boolean isProjectAdmin = project.getMembers().stream()
        //     .anyMatch(pm -> pm.getUser().equals(currentUser) && pm.getRole() == Role.ADMIN);
        // if (!isProjectAdmin) { throw new AccessDeniedException("프로젝트 관리자 권한 없음"); }

        project.setStatus(Status.COMPLETED);
        projectRepository.save(project);
    }
}
```

---

## 핵심 포인트
- Service 레이어는 단순 CRUD를 넘어서 비즈니스 로직 처리
- 회사별 접근 체크만 수행 → 같은 회사 로그인 사용자는 모두 접근 가능
- DTO 변환은 Controller와 통신할 때 사용
- 트랜잭션(@Transactional)으로 데이터 일관성 보장
- 권한 구분(ADMIN 등)은 현재 없음, 향후 프로젝트별 Admin 역할 확장 가능