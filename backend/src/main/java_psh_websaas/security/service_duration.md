## Service Layer Documentation (Project Duration Calculation)

### 목적
- 프로젝트 기간(duration)을 자동 계산하여 DTO에 포함
- Controller와 Repository 사이에서 비즈니스 로직 처리
- 회사별 데이터 접근 제어 (같은 회사 로그인한 사람은 모두 접근 가능)
- DTO 변환, 트랜잭션 관리

---

## ProjectService (Duration 포함)

### 기능
- 회사별 프로젝트 조회
- 프로젝트 상세 조회
- 프로젝트 상태 변경 및 멤버 관리
- 프로젝트 DTO에 실제 기간(duration) 자동 계산

### 예시
```java
@Service
public class ProjectService {

    private final ProjectRepository projectRepository;

    public ProjectService(ProjectRepository projectRepository) {
        this.projectRepository = projectRepository;
    }

    public List<ProjectDTO> getProjects(User currentUser) {
        return projectRepository.findByCompany(currentUser.getCompany())
                .stream()
                .map(this::mapToDTO)
                .collect(Collectors.toList());
    }

    public ProjectDTO getProject(Long projectId, User currentUser) {
        Project project = projectRepository.findById(projectId)
            .orElseThrow(() -> new NotFoundException("Project not found"));

        if (!project.getCompany().equals(currentUser.getCompany())) {
            throw new AccessDeniedException("권한 없음");
        }

        return mapToDTO(project);
    }

    private ProjectDTO mapToDTO(Project project) {
        ProjectDTO dto = ProjectMapper.toDTO(project);

        // 실제 기간 계산 (actualStartDate 기준)
        if (project.getActualStartDate() != null) {
            LocalDate endDate = project.getActualEndDate() != null 
                                ? project.getActualEndDate() 
                                : LocalDate.now(); // 완료일 없으면 오늘 기준
            dto.setDuration((int) ChronoUnit.DAYS.between(
                project.getActualStartDate(), endDate) + 1);
        }

        return dto;
    }
}
```

---

### 핵심 포인트
- 기간 계산은 Service에서 수행하여 DB에는 날짜만 저장
- DTO 변환 시 자동 계산된 duration 포함 → 클라이언트에서 바로 사용 가능
- 회사 단위 접근 체크 유지
- 향후 프로젝트별 Admin 역할 기반 확장 가능