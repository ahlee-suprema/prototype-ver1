# HR 대시보드 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-dashboard`  
> Route: `/hr/dashboard`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `management.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Manager/Admin gets high-level overview of team status, pending approvals, and key metrics
- **Core Functions**:
  - View department overview statistics
  - Monitor team member status (attendance, leave balance)
  - See pending approval queue
  - Quick-access to frequent tasks
  - View attendance/leave trends
  - Access reports and analytics

### 1.2 Access Control

- **HR Admin**: Full dashboard with all teams
- **Manager**: Filtered view of direct reports only
- **Executives**: Read-only summary view

---

## 2. Screen Layout & Components

### 2.1 Main Dashboard Structure

```
┌─────────────────────────────────────────────────────┐
│ HR 대시보드                                          │
│ 조직의 근태 및 휴가 현황을 한눈에 확인합니다        │
├─────────────────────────────────────────────────────┤
│ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐  │
│ │ 오늘 출근    │ │ 오늘 부재    │ │ 연차 평균    │  │
│ │ 45/50명      │ │ 3명          │ │ 7.2일        │  │
│ └──────────────┘ └──────────────┘ └──────────────┘  │
├─────────────────────────────────────────────────────┤
│ 미승인 요청 (5건)                                   │
│ ┌──────────────────────────────────────────────┐   │
│ │ 김철수 | 연차 신청 (2/9~2/11) | [승인] [반려] │  │
│ │ 이영희 | 출장 신청 (서울) | [승인] [반려]    │  │
│ │ ...                                          │   │
│ └──────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────┤
│ 부서별 현황                                        │
│ ┌─────────┬─────┬──────┬────────┐               │
│ │ 부서    │ 인원 │ 출근 │ 부재   │               │
│ │ 개발팀  │  20 │  18  │  2     │               │
│ │ 영업팀  │  15 │  13  │  2     │               │
│ │ ...     │ ... │ ...  │ ...    │               │
│ └─────────┴─────┴──────┴────────┘               │
├─────────────────────────────────────────────────────┤
│ [빠른 바로가기]                                     │
│ [휴가 관리] [근태 관리] [출장 관리] [보고서]        │
│                                                    │
└─────────────────────────────────────────────────────┘
```

---

## 3. Key Statistics Cards (Top Row)

### 3.1 Four-Card Grid

| #   | Card        | Metric            | Example |
| --- | ----------- | ----------------- | ------- |
| 1   | 오늘 출근   | {present}/{total} | 45/50명 |
| 2   | 오늘 부재   | {absent}          | 3명     |
| 3   | 연차 평균   | {avg} 일          | 7.2일   |
| 4   | 미승인 요청 | {count} 건        | 5건     |

**Card Styling**:

- White bg, rounded-2xl, shadow-sm
- Title: slate-700, small font
- Metric: primary-600, large font (24px)
- Icon in top-right corner
- Clickable: hover state, routes to detail view

### 3.2 Card Click Handlers

| Card        | Route                                | Behavior                    |
| ----------- | ------------------------------------ | --------------------------- |
| 오늘 출근   | `/hr/attendance-today`               | Filter to present employees |
| 오늘 부재   | `/hr/attendance-today?status=absent` | Filter to absent employees  |
| 연차 평균   | `/hr/reports/leave-summary`          | Open leave analytics        |
| 미승인 요청 | `/hr/requests/pending`               | Show approval queue         |

---

## 4. Pending Approval Queue

### 4.1 Mini-Queue Display

**Section Title**: "미승인 요청 ({count}건)"

**Quick List**:

- Show top 5 pending items
- Each item: Employee name | Request type | Date | Quick actions [승인] [반려]
- Inline approve/reject with confirmation
- "모두 확인하기 →" link to full queue

### 4.2 Request Item Display

```
┌──────────────────────────────────────────────┐
│ 김철수 | 연차 신청 | 2026-02-09             │
│ 2026.02.09 ~ 2026.02.11 (3일, 토요일 0.5일) │
│ [승인] [반려]                                │
└──────────────────────────────────────────────┘
```

**Inline Actions**:

- Approve: Direct PATCH to update status
- Reject: Open modal for rejection reason
- View: Navigate to full request detail

---

## 5. Department Overview Table

### 5.1 Column Definitions

| #   | Column    | Format                 | Notes                     |
| --- | --------- | ---------------------- | ------------------------- |
| 1   | 부서      | Department name        | Sortable, clickable       |
| 2   | 인원      | Total count            | Sum of active employees   |
| 3   | 출근      | Present count          | Real-time from attendance |
| 4   | 부재      | Absent count           | Absent reason breakdown   |
| 5   | 평균 휴가 | Average days remaining | Calculated per dept       |

### 5.2 Interactive Features

- **Click Department Name**: Filter attendance/leave by department
- **Hover Row**: Show additional actions (View detail, Export, etc.)
- **Sort**: Click column header to sort

### 5.3 Row Color Coding

| Condition   | Style                   |
| ----------- | ----------------------- |
| All present | White bg                |
| >20% absent | Light red bg (rose-50)  |
| >50% absent | Light red bg (rose-100) |

---

## 6. Quick Access Buttons

### 6.1 Fast Navigation Section

**Title**: "빠른 바로가기" or "주요 업무"

**Button Grid** (4 columns on desktop, 2 on tablet, 1 on mobile):

| Button    | Route                          | Icon     | Badge         |
| --------- | ------------------------------ | -------- | ------------- |
| 휴가 관리 | `/hr/leave-management`         | Leaf     | Pending count |
| 근태 관리 | `/hr/attendance-records`       | Clock    | -             |
| 출장 관리 | `/hr/business-trip-management` | MapPin   | Pending count |
| 보고서    | `/hr/reports`                  | BarChart | -             |

**Button Styling**:

- Rounded-xl, border, hover effect
- Icon on left, text on right
- Badge shows count (red, top-right)

---

## 7. Data Model

```typescript
interface DashboardData {
  summary: {
    todayPresent: number;
    todayAbsent: number;
    averageLeaveBalance: number;
    pendingApprovals: number;
  };
  departmentStats: DepartmentStat[];
  pendingRequests: PendingRequest[];
  attendance: AttendanceStatus[];
  trends: TrendData;
}

interface DepartmentStat {
  deptId: string;
  deptName: string;
  totalEmployees: number;
  presentToday: number;
  absentToday: number;
  leaveBalanceAvg: number;
}

interface PendingRequest {
  id: number;
  employeeId: string;
  employeeName: string;
  requestType: "leave" | "business-trip" | "overtime";
  details: string; // 연차 신청 (2/9~2/11)
  submittedAt: string;
  priority: "high" | "normal";
}

interface AttendanceStatus {
  employeeId: string;
  name: string;
  status: "present" | "absent" | "late" | "early-leave";
  checkInTime?: string;
  checkOutTime?: string;
  reason?: string;
}

interface TrendData {
  weeklyAttendance: number[];
  monthlyLeaveUsage: number[];
  departmentComparison: Record<string, number>;
}
```

---

## 8. API Requirements

| Method | Endpoint                                        | Response                                           |
| ------ | ----------------------------------------------- | -------------------------------------------------- |
| GET    | `/api/v1/hr/dashboard/summary`                  | `{ todayPresent, todayAbsent, avgLeave, pending }` |
| GET    | `/api/v1/hr/dashboard/departments`              | `{ data: DepartmentStat[] }`                       |
| GET    | `/api/v1/hr/dashboard/pending-requests?limit=5` | `{ data: PendingRequest[] }`                       |
| GET    | `/api/v1/hr/attendance/today`                   | `{ data: AttendanceStatus[] }`                     |
| PATCH  | `/api/v1/hr/requests/{requestId}/approve`       | `{ success: true }`                                |
| PATCH  | `/api/v1/hr/requests/{requestId}/reject`        | `{ success: true, reason: string }`                |

---

## 9. Real-time Updates

### 9.1 WebSocket Events (Optional)

- Employee check-in/out → Update "오늘 출근" counter
- Request approval → Update "미승인 요청" count
- Leave application → Update pending queue

### 9.2 Polling Alternative

- Refresh summary every 30 seconds
- Refresh pending queue every 60 seconds
- Manual refresh button in header

---

## 10. Responsive Design

- **Desktop (≥1280px)**: 4-column cards, full table
- **Tablet (768-1279px)**: 2-column cards, scrollable table
- **Mobile (<768px)**: 1-column stacked, card-based table

---

## 11. Accessibility

- Summary statistics announced via aria-live
- Department table with proper th/td headers
- Keyboard navigation with Tab through all interactive elements
- Color not sole indicator (use text labels too)

---

## 12. Toast Messages

| Event           | Message                                 |
| --------------- | --------------------------------------- |
| Approve success | "요청이 승인되었습니다."                |
| Reject success  | "요청이 반려되었습니다."                |
| Refresh success | "대시보드가 새로고침되었습니다."        |
| Error           | "대시보드 로드 중 오류가 발생했습니다." |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
