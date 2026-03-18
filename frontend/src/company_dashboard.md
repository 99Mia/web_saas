# Company Dashboard (React) - 프로젝트 이름별 카드 기반 시각화

## 목적
- 각 회사별 로그인 시 해당 회사 진행 중인 프로젝트를 이름 단위로 카드 시각화
- 카드 클릭 시 프로젝트 이름 기반 상세보기 페이지로 이동
- 대시보드에서 전체 프로젝트 현황을 한눈에 확인 가능

---

## 컴포넌트 구조
```
CompanyDashboard
 ├─ Sidebar (회사 선택, AuthGuard로 접근 제어)
 ├─ ProjectCardsByName (프로젝트 이름별 카드)
 └─ ProjectDetailModal / ProjectDetailPage (상세보기)
```

---

## 1️⃣ 프로젝트 이름별 카드 컴포넌트
```jsx
// ProjectCard.jsx
import React from "react";

const ProjectCard = ({ projectName, projectList, onClick }) => {
  return (
    <div className="project-card" onClick={() => onClick(projectName)}>
      <h3>{projectName}</h3>
      <p>진행 중 프로젝트 수: {projectList.length}</p>
      <p>대표 담당자: {projectList[0]?.manager}</p>
      <p>평균 진행률: {Math.round(projectList.reduce((sum, p) => sum + p.progress, 0) / projectList.length)}%</p>
    </div>
  );
};

export default ProjectCard;
```

- 프로젝트 이름 단위로 카드 생성
- 카드 클릭 시 상세보기 페이지 이동

---

## 2️⃣ 전체 대시보드
```jsx
// CompanyDashboard.jsx
import React, { useState, useEffect } from "react";
import { useParams } from "react-router-dom";
import userUserStore from '../store/userUserStore';
import ProjectCard from "./ProjectCard";
import ProjectService from "../services/ProjectNameService";

const CompanyDashboard = () => {
  const { company } = useParams();
  const currentUser = userUserStore.getState().user;

  const [projectsByName, setProjectsByName] = useState({});

  useEffect(() => {
    // 서버에서 회사별 프로젝트 이름 단위로 그룹핑
    ProjectService.getProjectsByCompanyName(currentUser.company)
      .then((response) => setProjectsByName(response.data))
      .catch((err) => console.error(err));
  }, [currentUser]);

  const handleCardClick = (projectName) => {
    window.location.href = `/projects/name/${projectName}`;
  };

  return (
    <div className="company-dashboard">
      <h1>{currentUser.company} 프로젝트 대시보드</h1>
      <div className="project-cards-container">
        {Object.entries(projectsByName).map(([name, projectList]) => (
          <ProjectCard
            key={name}
            projectName={name}
            projectList={projectList}
            onClick={handleCardClick}
          />
        ))}
      </div>
    </div>
  );
};

export default CompanyDashboard;
```

- JWT와 `userUserStore` 기반으로 회사별 프로젝트만 조회
- 카드 단위 = 프로젝트 이름

---

## 3️⃣ 상세보기 페이지
```jsx
// ProjectDetailPage.jsx
import React, { useEffect, useState } from "react";
import { useParams } from "react-router-dom";
import ProjectService from "../services/ProjectService";

const ProjectDetailPage = () => {
  const { name } = useParams();
  const [projectList, setProjectList] = useState([]);

  useEffect(() => {
    ProjectService.getProjectsByNameDetail(name)
      .then((res) => setProjectList(res.data))
      .catch((err) => console.error(err));
  }, [name]);

  if (!projectList.length) return <div>Loading...</div>;

  return (
    <div className="project-detail">
      <h2>{name}</h2>
      {projectList.map((project) => (
        <div key={project.id}>
          <p>상태: {project.status}</p>
          <p>담당자: {project.manager}</p>
          <p>시작일: {project.actualStartDate}</p>
          <p>종료일: {project.actualEndDate || '진행중'}</p>
          <p>진행률: {project.progress}%</p>
          <p>설명: {project.description}</p>
        </div>
      ))}
    </div>
  );
};

export default ProjectDetailPage;
```
- 프로젝트 이름 단위 상세보기
- 회사별 필터링은 서버에서 JWT로 처리

---

## 4️⃣ 스타일링 예시
```css
.company-dashboard {
  padding: 20px;
}

.project-cards-container {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.project-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  width: 200px;
  cursor: pointer;
  transition: box-shadow 0.2s ease;
}

.project-card:hover {
  box-shadow: 0 2px 8px rgba(0,0,0,0.2);
}
```

---

## 핵심 포인트
- 로그인 회사 기준으로 프로젝트 필터링
- 카드 단위 = 프로젝트 이름, 클릭 시 상세보기 이동
- 서버에서 Map 구조로 이름별 그룹핑 후 클라이언트 전달
- 확장 가능: **권한별 버튼, 진행률 차트, 필터링** 등

