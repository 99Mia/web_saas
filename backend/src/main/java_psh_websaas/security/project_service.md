##controller

## 목적
- 로그인한 사용자의 회사 정보를 기반으로 프로젝트 접근 제한
- 다른 회사 프로젝트는 조회 불가
- 서비스 레이어에서 회사 단위 필터링 적용

## currentUser 활용 및 서비스 연동
```java
package com.example.controller;

import com.example.dto.ProjectDTO;
import com.example.security.CurrentUser;
import com.example.security.UserPrincipal;
import com.example.service.ProjectNameService;
import com.example.service.ProjectService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.Map;

@RestController
@RequestMapping("/api/projects")
public class ProjectController {

    private final ProjectService projectService;
    private final ProjectNameService projectNameService;

    public ProjectController(ProjectService projectService, ProjectNameService projectNameService) {
        this.projectService = projectService;
        this.projectNameService = projectNameService;
    }

    ## 전체 프로젝트 조회 (공통 엔드포인트)
    @GetMapping("")
    public ResponseEntity<?> getAllProjects(@CurrentUser UserPrincipal currentUser) {
        return ResponseEntity.ok(projectService.getProjects(currentUser));
    }

    ## 프로젝트 이름별 카드용 DTO 조회
    @GetMapping("/cards")
    public ResponseEntity<Map<String, ProjectDTO>> getProjectsByName(@CurrentUser UserPrincipal currentUser) {
        Map<String, ProjectDTO> projectMap = projectNameService.getProjectsByName(currentUser);
        return ResponseEntity.ok(projectMap);
    }

    ## 특정 프로젝트 상세 조회
    @GetMapping("/{id}")
    public ResponseEntity<ProjectDTO> getProject(@PathVariable Long id, @CurrentUser UserPrincipal currentUser) {
        ProjectDTO projectDTO = projectService.getProject(id, currentUser);
        return ResponseEntity.ok(projectDTO);
    }

    ## 프로젝트 상태 변경 (ADMIN 권한 필요)
    @PutMapping("/{id}")
    public ResponseEntity<?> updateProjectStatus(@PathVariable Long id, @RequestBody ProjectDTO projectDTO, @CurrentUser UserPrincipal currentUser) {
        projectService.updateStatus(id, projectDTO.getStatus(), currentUser);
        return ResponseEntity.ok().build();
    }
}