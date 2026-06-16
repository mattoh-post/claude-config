# 매핑표

## Linear 상태 → 진행 현황 분류

| Linear statusType | 의미 | 분류 |
|---|---|---|
| completed | 완료 | 완료 (completedAt이 기준 윈도우 안일 때만) |
| started | 진행중 | 이번주 마감 |
| unstarted | Todo | 이번주 마감 (dueDate 이번주 이내 또는 진행중 후속일 때) |
| backlog | 백로그 | 기본 제외. 이번주 의존 항목이면 한 줄 표시 |
| canceled | 취소 | 취소·정리 (canceledAt이 기준 윈도우 안일 때) |
| (archivedAt 있음) | 보관 | 취소·정리 (archivedAt이 기준 윈도우 안일 때) |

기준 윈도우: **전일 12:00 ~ 당일 11:30 (KST)**. 직전 스크럼이 끝난 정오부터 당일 스크럼 직전까지. 완료(completedAt)와 취소·보관(canceledAt/archivedAt) 모두 이 윈도우 안에 든 것만 진행 현황에 넣는다. 주말·공휴일로 어제 스크럼이 없으면 시작점을 가장 최근 스크럼 날짜의 12:00으로 당긴다.

## WBS 단계 → Linear 팀

| WBS 단계 | Linear 팀 | 비고 |
|---|---|---|
| 기획 | PM | 기획 파악·문서·스펙 정의 |
| 디자인 | Design (DES) | 시안·디자인 리뷰 |
| 프론트엔드 | WEB-Tech (WEBTECH) | 클라 구현 |
| 백엔드 | BE-API (BEAPI) | 서버·API 구현 |
| 통합·출시 | WEB-Tech + BE-API | host 통합·배포·릴리스 성격 이슈 |

## 단계 rollup 규칙

해당 단계의 Linear 이슈를 모아 판단한다.

- 전부 completed → 완료
- 하나라도 started → 진행중
- 전부 unstarted/backlog → 예정

취소·보관 이슈는 rollup 계산에서 제외한다.

## 매칭 우선순위

1. WBS 행의 `작업` 이름이 특정 Linear 이슈와 명확히 대응 → 그 이슈 상태를 직접 반영
2. 불명확 → 행의 `단계`로 묶어 rollup
3. 둘 다 불가 → 행을 건드리지 않고 "수동 확인 필요"로 보고
