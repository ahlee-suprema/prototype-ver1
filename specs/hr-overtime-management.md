# HR 연장근무 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-overtime-management`  
> Route: `/hr/overtime-management`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `overtime-management.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers view, approve, and manage organization-wide overtime requests and track overtime usage
- **Core Functions**:
  - View and approve overtime requests
  - Filter by department, date, employee, status
  - Track overtime balance and usage
  - Identify overtime patterns and trends
  - Generate overtime reports
  - Manage overtime compensation (paid/unpaid)

### 1.2 Access Control

- **HR Admin**: Full organization view with approval rights
- **Department Manager**: Team view with limited approval
- **Executive**: Read-only analytics view

---

## 2. Screen Layout & Components

### 2.1 Main Screen Structure

```
┌──────────────────────────────────────────────────────┐
│ 연장근무 관리                                        │
│ 조직 전체 연장근무 현황을 관리합니다                │
├──────────────────────────────────────────────────────┤
│ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐  │
│ │ 이번 달      │ │ 올해         │ │ 미승인       │  │
│ │ 45시간       │ │ 120시간      │ │ 3건          │  │
│ └──────────────┘ └──────────────┘ └──────────────┘  │
├──────────────────────────────────────────────────────┤
│ [검색] [부서 필터] [기간] [상태 필터] [내보내기]     │
├──────────────────────────────────────────────────────┤
│ 연장근무 신청 현황                                   │
│ ┌────────────────────────────────────────────────┐  │
│ │ ☑ 직원명 | 부서 | 날짜 | 근무시간 | 사유      │  │
│ │          |      |      | 상태 | 작업           │  │
│ │──────────────────────────────────────────────│  │
│ │ ☑ 김철수 | 개발팀 | 2/9 | 2h 30m | [대기중]  │  │
│ │ ☑ 이영희 | 영업팀 | 2/8 | 1h 00m | [승인]    │  │
│ └────────────────────────────────────────────────┘  │
├──────────────────────────────────────────────────────┤
│ [이전] 1 2 3 [다음]  15 items per page              │
└──────────────────────────────────────────────────────┘
```

---

## 3. Summary Statistics (Top Row)

### 3.1 Three-Card Grid

| Card | Metric  | Example |
| ---- | ------- | ------- |
| 1    | 이번 달 | 45시간  |
| 2    | 올해    | 120시간 |
| 3    | 미승인  | 3건     |

**Display**:

- Blue bg with white text
- Large metric number
- Subtext label
- Click to filter table

---

## 4. Search & Filter Section

### 4.1 Search Bar

**Field**: Employee name or ID  
**Placeholder**: "직원 이름 또는 ID로 검색..."

### 4.2 Quick Filters

| Filter     | Type         | Options                           |
| ---------- | ------------ | --------------------------------- |
| Department | Dropdown     | IT, HR, Sales, etc.               |
| Date Range | Date Picker  | From ~ To dates                   |
| Status     | Multi-select | Pending, Approved, Rejected, Paid |

### 4.3 Advanced Filters (Collapsible)

1. Employee ID (text search)
2. Department (multi-select)
3. Date Range (from ~ to)
4. Overtime Hours (min ~ max)
5. Status (Pending, Approved, Rejected, Paid, Unpaid)
6. Compensation Type (Paid, Unpaid, CTO)

---

## 5. Overtime Records Table

### 5.1 Column Definitions

| #   | Column    | Format       | Notes                            |
| --- | --------- | ------------ | -------------------------------- |
| 1   | ☑         | Checkbox     | Multi-select                     |
| 2   | 직원명    | Name         | Link to employee detail          |
| 3   | 부서      | Department   | -                                |
| 4   | 신청 날짜 | YYYY.MM.DD   | Date submitted                   |
| 5   | 근무 날짜 | YYYY.MM.DD   | Date worked                      |
| 6   | 근무 시간 | HH:MM format | Duration of overtime             |
| 7   | 사유      | Reason text  | e.g., "프로젝트 마감"            |
| 8   | 보상 유형 | Badge        | "급여", "휴무일" (CTO), "미지급" |
| 9   | 상태      | Badge        | Pending/Approved/Rejected/Paid   |
| 10  | 작업      | Actions      | [승인] [반려] [상세] [삭제]      |

### 5.2 Status Badge Styling

| Status   | Color               | Display    |
| -------- | ------------------- | ---------- |
| Pending  | Amber (amber-500)   | "대기중"   |
| Approved | Green (green-500)   | "승인"     |
| Rejected | Red (red-500)       | "반려"     |
| Paid     | Blue (blue-500)     | "지급완료" |
| CTO      | Purple (purple-500) | "휴무일"   |
| Unpaid   | Slate (slate-500)   | "미지급"   |

### 5.3 Compensation Type Badge

| Type            | Color  | Display  |
| --------------- | ------ | -------- |
| Paid (급여)     | Green  | "급여"   |
| CTO (보상휴무)  | Purple | "휴무일" |
| Unpaid (미지급) | Slate  | "미지급" |

---

## 6. Row Actions

### 6.1 Inline Action Buttons

| Action | Conditions                   | Modal                       |
| ------ | ---------------------------- | --------------------------- |
| 승인   | Status = pending             | Approve with optional notes |
| 반려   | Status = pending             | Reject with reason required |
| 상세   | Any                          | View detail modal           |
| 삭제   | Status = pending, Admin only | Confirmation required       |

### 6.2 Bulk Actions

**When records selected**:

```
☑ [2건 선택됨]
[✓ 일괄 승인] [✗ 일괄 반려] [💰 보상 유형 변경] [✕ 취소]
```

---

## 7. Overtime Detail Modal

### 7.1 Modal Content

```
┌──────────────────────────────────┐
│ 연장근무 신청 상세               │
├──────────────────────────────────┤
│                                  │
│ 직원: 김철수 (EMP-001)           │
│ 부서: 개발팀                     │
│                                  │
│ 신청 날짜: 2026.02.09           │
│ 근무 날짜: 2026.02.08           │
│ 근무 시간: 2h 30m (22:00~00:30) │
│                                  │
│ 사유: 프로젝트 마감 대응         │
│                                  │
│ 보상 유형: [급여] [휴무일]      │
│ 상태: [대기중] / [승인]          │
│                                  │
│ 관리자 메모:                    │
│ [텍스트 입력]                    │
│                                  │
│ [닫기] [승인] [반려] [저장]     │
│                                  │
└──────────────────────────────────┘
```

### 7.2 Sections

1. **Employee Info**: Name, ID, Department
2. **Date & Time**: Submission date, work date, duration
3. **Request Details**: Reason for overtime
4. **Compensation**: Select type (Paid/CTO/Unpaid)
5. **Status**: Current approval status
6. **Manager Notes**: HR/Manager comments (editable)
7. **Action Buttons**: Approve, Reject, Save, Close

---

## 8. Approval Workflow

### 8.1 Approve Button

**On Click**:

1. Show modal with optional notes field
2. Select compensation type if needed
3. Confirm approval
4. PATCH API to update status
5. Show success toast
6. Refresh table

### 8.2 Reject Button

**On Click**:

1. Show modal with required reason field
2. Reason max 500 characters
3. Confirm rejection
4. PATCH API to update status
5. Show success toast
6. Refresh table

---

## 9. Data Model

```typescript
interface OvertimeRecord {
  id: number;
  employeeId: string;
  employeeName: string;
  department: string;
  submittedAt: string; // YYYY-MM-DD
  overtimeDate: string; // YYYY-MM-DD (when worked)
  startTime: string; // HH:MM
  endTime: string; // HH:MM
  duration: number; // Minutes
  reason: string;
  compensationType: "paid" | "cto" | "unpaid";
  status: "pending" | "approved" | "rejected" | "paid";
  approverName?: string;
  approvedAt?: string;
  managerNotes?: string;
  rejectionReason?: string;
}

interface OvertimeSummary {
  thisMonth: number; // Total hours
  thisYear: number; // Total hours
  pendingApprovals: number; // Count
  departmentBreakdown: Record<string, number>;
}

interface OvertimeFilter {
  employeeName?: string;
  departmentId?: string;
  dateFrom?: string;
  dateTo?: string;
  status?: string[];
  compensationType?: string;
  page?: number;
  pageSize?: number;
}
```

---

## 10. API Requirements

| Method | Endpoint                                      | Response                                       |
| ------ | --------------------------------------------- | ---------------------------------------------- |
| GET    | `/api/v1/hr/overtime/summary`                 | `{ thisMonth, thisYear, pending }`             |
| GET    | `/api/v1/hr/overtime/records?filters`         | `{ data: OvertimeRecord[], meta: Pagination }` |
| GET    | `/api/v1/hr/overtime/{recordId}`              | `{ data: OvertimeRecord }`                     |
| PATCH  | `/api/v1/hr/overtime/{recordId}/approve`      | `{ success: true }`                            |
| PATCH  | `/api/v1/hr/overtime/{recordId}/reject`       | `{ success: true }`                            |
| PATCH  | `/api/v1/hr/overtime/{recordId}/compensation` | `{ success: true }` (change type)              |
| POST   | `/api/v1/hr/overtime/export`                  | `{ fileUrl: string }`                          |
| POST   | `/api/v1/hr/overtime/batch-approve`           | `{ approved: [], errors: [] }`                 |

---

## 11. Edge Cases

| Scenario                             | Behavior                                                   |
| ------------------------------------ | ---------------------------------------------------------- |
| Duplicate overtime same date         | Show warning, allow if different times                     |
| Overtime exceeds weekly max          | Show warning (50h/week typical), allow override with notes |
| Pending compensation type            | Show "미결정" badge, require selection before approval     |
| Overtime extends to next day (24:30) | Split display or show "다음날" suffix                      |
| Weekend overtime                     | Show "주말" tag, potentially higher comp rate              |
| Multiple rejections                  | Show rejection history in detail view                      |

---

## 12. Responsive Design

- **Desktop**: Full table with all columns
- **Tablet**: Hide optional columns, card view available
- **Mobile**: Card-based layout, 1 item per card

---

## 13. Export Functionality

**Options**:

- Format: CSV, Excel, PDF
- Scope: All, Filtered, Selected
- Columns: Customizable
- Date range in filename

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
