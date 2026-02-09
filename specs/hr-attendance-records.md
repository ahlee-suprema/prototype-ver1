# HR 근태 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-attendance-records`  
> Route: `/hr/attendance-records`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `attendance-records.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers view and manage organization-wide attendance records, corrections, and exceptions
- **Core Functions**:
  - View all employees' attendance history
  - Filter by department, date range, status
  - Approve/reject attendance corrections
  - Export attendance data
  - Generate attendance reports
  - Identify patterns (chronic lateness, frequent absences)

### 1.2 Access Control

- **HR Admin**: Full organization view
- **Department Manager**: Team member view only
- **Executive**: Read-only summary view

---

## 2. Screen Layout & Components

### 2.1 Main Screen Structure

```
┌─────────────────────────────────────────────────────┐
│ 근태 기록                                           │
│ 조직 전체 근태 데이터를 관리합니다                 │
├─────────────────────────────────────────────────────┤
│ [검색] [부서 필터] [기간 선택] [상태 필터] [내보내기] │
├─────────────────────────────────────────────────────┤
│ 오늘의 미확인 현황                                  │
│ ┌────────┬────────┬────────┬────────┐              │
│ │ 총 인원 │ 미출근 │ 지각   │ 조퇴   │              │
│ │  50명  │  2명   │  3명   │  1명   │              │
│ └────────┴────────┴────────┴────────┘              │
├─────────────────────────────────────────────────────┤
│ 근태 현황 목록                                      │
│ ┌──────────────────────────────────────────────┐   │
│ │ ☑ 직원ID | 직원명 | 부서 | 날짜 | 체크인   │   │
│ │          |         |      |      | 체크아웃  │   │
│ │          |         |      |      | 근무시간  │   │
│ │          |         |      |      | 상태      │   │
│ │          |         |      |      | 작업      │   │
│ │────────────────────────────────────────────│   │
│ │ ☑ 김철수 | 개발팀 | ... | 09:15 | 18:32 │   │
│ │ ☑ 이영희 | 영업팀 | ... | 09:05 | 18:10 │   │
│ └──────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────┤
│ [이전] 1 2 3 [다음]  15 items per page             │
└─────────────────────────────────────────────────────┘
```

---

## 3. Search & Filter Section

### 3.1 Search Bar

**Field**: Employee ID or Name  
**Placeholder**: "직원 ID 또는 이름으로 검색..."  
**Icon**: Magnifying glass (search icon)

---

### 3.2 Quick Filters (Top Row)

| Filter             | Type         | Options                            |
| ------------------ | ------------ | ---------------------------------- |
| Department         | Dropdown     | IT, HR, Sales, etc.                |
| Date Range         | Date Picker  | From ~ To dates                    |
| Status             | Multi-select | On-time, Late, Early Leave, Absent |
| Correction Pending | Toggle       | Show only pending corrections      |

---

### 3.3 Advanced Filter Panel (Collapsible)

**Filters**:

1. Department (multi-select dropdown)
2. Date Range (from ~ to)
3. Check-in Time Range (from ~ to)
4. Work Hours Range (min ~ max)
5. Status (checkboxes: On-time, Late, Early Leave, Absent, Holiday, Leave)
6. Correction Status (Pending, Approved, Rejected)

**Appearance**:

- Initially collapsed, show "필터" button
- Click to expand
- "초기화" button to reset all filters

---

## 4. Today's Summary Stats

### 4.1 Four-Card Summary

| Card | Metric  | Value                            |
| ---- | ------- | -------------------------------- |
| 1    | 총 인원 | {total}                          |
| 2    | 미출근  | {absent_count} (Red badge)       |
| 3    | 지각    | {late_count} (Amber badge)       |
| 4    | 조퇴    | {early_leave_count} (Blue badge) |

**Styling**: Inline display, small stats cards with icons  
**Click Handler**: Filter table to show this category

---

## 5. Attendance Records Table

### 5.1 Column Definitions

| #   | Column   | Format         | Notes                               |
| --- | -------- | -------------- | ----------------------------------- |
| 1   | ☑        | Checkbox       | Multi-select for batch actions      |
| 2   | 직원 ID  | EMP-XXXX       | Link to employee detail             |
| 3   | 직원명   | Name           | -                                   |
| 4   | 부서     | Department     | -                                   |
| 5   | 날짜     | YYYY.MM.DD     | -                                   |
| 6   | 요일     | Mon, Tue, etc  | -                                   |
| 7   | 체크인   | HH:MM or "---" | Red if absent                       |
| 8   | 체크아웃 | HH:MM or "---" | -                                   |
| 9   | 근무시간 | HH:MM          | Calculated                          |
| 10  | 상태     | Badge          | On-time/Late/Absent/Holiday         |
| 11  | 정정     | Badge/Button   | Pending/Approved/Rejected or [요청] |
| 12  | 작업     | Actions        | [상세] [정정] [삭제]                |

### 5.2 Status Badge Colors

| Status                         | Color               | Display    |
| ------------------------------ | ------------------- | ---------- |
| On-time (정상)                 | Green (emerald-500) | "정상"     |
| Late (지각)                    | Red (red-500)       | "지각"     |
| Early Leave (조퇴)             | Yellow (amber-500)  | "조퇴"     |
| Absent (결근)                  | Gray (slate-500)    | "결근"     |
| Holiday (휴무)                 | Slate (slate-200)   | "휴무"     |
| On Leave (휴가)                | Blue (blue-500)     | "휴가"     |
| Correction Pending (정정대기)  | Amber (amber-500)   | "정정대기" |
| Correction Approved (정정완료) | Green (green-500)   | "정정완료" |

### 5.3 Row Highlighting

- **Absent/Late/Early Leave**: Light red background (rose-50)
- **Correction Pending**: Light amber background (amber-50)
- **Hover**: Light gray background for row selection context

---

## 6. Actions

### 6.1 Per-Row Actions

| Action | Icon   | Tooltip            | Modal                   |
| ------ | ------ | ------------------ | ----------------------- |
| 상세   | Eye    | View detail        | Attendance detail modal |
| 정정   | Pencil | Request correction | Correction form modal   |
| 삭제   | Trash  | Delete (archived)  | Confirmation dialog     |

### 6.2 Bulk Actions

**Top Action Bar** (visible when items selected):

```
☑ [2건 선택됨]
[✓ 승인] [✗ 반려] [📊 내보내기] [✕ 취소]
```

**Actions**:

- Approve corrections (selected items)
- Reject corrections (opens reason modal)
- Export selected data
- Clear selection

---

## 7. Attendance Detail Modal

### 7.1 Modal Content

```
┌─────────────────────────────────────┐
│ 근태 기록 상세 정보                  │
├─────────────────────────────────────┤
│                                      │
│ 직원명: 김철수                       │
│ 직원 ID: EMP-001                     │
│ 부서: 개발팀                         │
│                                      │
│ 날짜: 2026.02.09 (월요일)            │
│                                      │
│ 체크인: 09:15                        │
│ 체크아웃: 18:32                      │
│ 근무시간: 9h 17m                     │
│ 소요시간: 0m (정시 퇴근)             │
│                                      │
│ 상태: [정상] / [지각] / [조퇴] ...   │
│ 정정 신청: [승인] / [반려] / [대기]  │
│                                      │
│ [닫기] [정정 요청] [내보내기]        │
│                                      │
└─────────────────────────────────────┘
```

### 7.2 Detail Sections

1. **Employee Information**: Name, ID, Department
2. **Date & Time**: Date, Day of week
3. **Clock Times**: Check-in, Check-out
4. **Duration Calculated**: Work hours, Time deviations
5. **Status Badge**: Current status
6. **Correction Status**: If any pending/approved corrections
7. **Notes/Comments**: HR notes field (if applicable)

---

## 8. Correction Request Modal

### 8.1 Form Fields

| Field       | Type        | Required | Notes                                                 |
| ----------- | ----------- | -------- | ----------------------------------------------------- |
| 정정 유형   | Dropdown    | Yes      | Missing check-in, Wrong time, Missing check-out, etc. |
| 수정할 시간 | Time input  | Yes      | New time (HH:MM format)                               |
| 사유        | Textarea    | Yes      | Reason for correction                                 |
| 첨부파일    | File upload | No       | Supporting documentation                              |

### 8.2 Validation

- Correction type selected
- New time provided
- Reason max 500 characters
- File size < 5MB if attached

---

## 9. Data Model

```typescript
interface AttendanceRecord {
  id: number;
  employeeId: string;
  employeeName: string;
  department: string;
  date: string; // YYYY-MM-DD
  checkInTime: string; // HH:MM or null
  checkOutTime: string; // HH:MM or null
  workDuration: number; // Minutes
  status: "on-time" | "late" | "early-leave" | "absent" | "holiday" | "leave";
  correctionStatus?: "pending" | "approved" | "rejected";
  correctionReason?: string;
  notes?: string;
}

interface CorrectionRequest {
  id: number;
  attendanceId: number;
  employeeId: string;
  correctionType:
    | "missing-check-in"
    | "missing-check-out"
    | "wrong-time"
    | "other";
  newTime?: string; // HH:MM
  reason: string;
  attachmentUrl?: string;
  status: "pending" | "approved" | "rejected";
  submittedAt: string;
  reviewedAt?: string;
  reviewerName?: string;
  reviewNotes?: string;
}

interface AttendanceFilter {
  departmentId?: string;
  dateFrom?: string;
  dateTo?: string;
  status?: string[];
  searchText?: string;
  correctionPending?: boolean;
  page?: number;
  pageSize?: number;
}
```

---

## 10. API Requirements

| Method | Endpoint                                      | Response                                         |
| ------ | --------------------------------------------- | ------------------------------------------------ |
| GET    | `/api/v1/hr/attendance/records?filters`       | `{ data: AttendanceRecord[], meta: Pagination }` |
| GET    | `/api/v1/hr/attendance/summary/today`         | `{ total, absent, late, early_leave }`           |
| GET    | `/api/v1/hr/attendance/{recordId}`            | `{ data: AttendanceRecord }`                     |
| POST   | `/api/v1/hr/attendance/{recordId}/correction` | `{ id, status: 'pending' }`                      |
| PATCH  | `/api/v1/hr/corrections/{correctionId}`       | `{ success: true }`                              |
| GET    | `/api/v1/hr/corrections?status=pending`       | `{ data: CorrectionRequest[] }`                  |
| POST   | `/api/v1/hr/attendance/export`                | `{ fileUrl: string }` (CSV/Excel)                |

---

## 11. Export Functionality

### 11.1 Export Options

- **Format**: CSV, Excel (.xlsx), PDF
- **Scope**: All records, Selected records, Filtered results
- **Columns**: Selectable from available columns
- **Date Range**: Included in export name (AttendanceReport_2026-02-01_2026-02-09.xlsx)

---

## 12. Edge Cases

| Scenario                         | Behavior                                            |
| -------------------------------- | --------------------------------------------------- |
| No check-in but check-out exists | Status = "Absent" but show check-out time (anomaly) |
| Weekend/Holiday check-in         | Status = "Holiday" regardless of times              |
| Correction pending               | Show "정정대기" badge, allow approval/rejection     |
| Multiple corrections same day    | Show latest correction status, link to history      |
| Time sync issues                 | Flag in notes, allow manual override                |

---

## 13. Responsive Design

- **Desktop**: Full table with all columns visible
- **Tablet**: Hide optional columns, show card view on tap
- **Mobile**: Card-based layout, 1 record per card

---

## 14. Accessibility

- Table headers with th tags
- Sortable columns announced via aria-sort
- Status badges with text labels (not color alone)
- Keyboard navigation through table
- Screen reader announces filter changes

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
