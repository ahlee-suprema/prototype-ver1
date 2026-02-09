# HR 스케줄 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-schedule`  
> Route: `/hr/schedule`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `schedule.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers view and manage team member schedules, shifts, and work assignments
- **Core Functions**:
  - View team member schedules in calendar format
  - Manage work shifts and assignments
  - Plan coverage and staffing
  - Track shift swaps and changes
  - View schedule conflicts
  - Generate shift reports

### 1.2 Access Control

- **HR Admin**: Full organization view
- **Department Manager**: Team view only
- **Employee**: View own schedule and request changes

---

## 2. Screen Layout & Components

### 2.1 Main Screen Structure

```
┌──────────────────────────────────────────────────────┐
│ 스케줄 관리                                          │
│ 팀 멤버의 근무 일정을 관리합니다                     │
├──────────────────────────────────────────────────────┤
│ [이전 월] [2026년 2월] [다음 월]                    │
│ [주간 보기] [월간 보기] [목록 보기]                 │
├──────────────────────────────────────────────────────┤
│ 부서 필터: [전체 ▼]  직원 필터: [검색...]           │
├──────────────────────────────────────────────────────┤
│ 캘린더 그리드 (월간 보기)                           │
│ ┌─────┬─────┬─────┬─────┬─────┬─────┬─────┐       │
│ │ 월  │ 화  │ 수  │ 목  │ 금  │ 토  │ 일  │       │
│ ├─────┼─────┼─────┼─────┼─────┼─────┼─────┤       │
│ │  3  │  4  │  5  │  6  │  7  │  8  │  9  │       │
│ │[출근]│[야근]│[휴가]│[출근]│[출근]│[휴무]│[휴무]│       │
│ │ 김철수│이영희│박민석│조미영│...  │     │     │       │
│ └─────┴─────┴─────┴─────┴─────┴─────┴─────┘       │
│                                                    │
└──────────────────────────────────────────────────────┘
```

---

## 3. Calendar View Options

### 3.1 View Toggle Buttons

| View      | Display                 | Use Case                           |
| --------- | ----------------------- | ---------------------------------- |
| 월간 보기 | Month grid              | Overview of all schedules          |
| 주간 보기 | Week rows, hourly slots | Detailed shift timing              |
| 목록 보기 | Table list              | Search/filter specific assignments |

---

## 4. Month Calendar Grid

### 4.1 Cell Content

Each day cell shows:

- Date number
- Assigned schedule type (출근, 야근, 휴가, 휴무, etc.)
- Employee name(s) if viewing single person
- Number of employees if viewing team

**Color Coding**:

- 출근 (Regular): Blue
- 야근 (Late shift): Amber
- 야간근무 (Night shift): Purple
- 휴가 (Leave): Green
- 휴무 (Off day): Gray
- 공휴일 (Holiday): Red

### 4.2 Cell Click Handler

Click cell → Show detailed schedule for that day

```
┌──────────────────────────────┐
│ 2026년 2월 9일 (월)          │
├──────────────────────────────┤
│                              │
│ 김철수 (개발팀)              │
│ [09:00 ~ 18:00] (출근)      │
│ [편집] [결과물 보기]         │
│                              │
│ 이영희 (영업팀)              │
│ [09:00 ~ 18:00] (출근)      │
│ [편집] [결과물 보기]         │
│                              │
│ [닫기] [+ 일정 추가]        │
│                              │
└──────────────────────────────┘
```

---

## 5. Week View

### 5.1 Week Grid Layout

```
직원 / 시간 │ Mon │ Tue │ Wed │ Thu │ Fri │ Sat │ Sun
────────────┼─────┼─────┼─────┼─────┼─────┼─────┼─────
김철수      │ 09  │ 09  │ 09  │ 09  │ 09  │ OFF │ OFF
            │ 18  │ 18  │ 18  │ 18  │ 18  │     │
────────────┼─────┼─────┼─────┼─────┼─────┼─────┼─────
이영희      │ 14  │ 14  │ 14  │ OFF │ 09  │ 09  │ OFF
            │ 23  │ 23  │ 23  │     │ 18  │ 18  │
────────────┼─────┼─────┼─────┼─────┼─────┼─────┼─────
```

**Cells**:

- Show start and end times
- Color-coded by shift type
- Click to edit
- Drag to swap shifts

---

## 6. List View

### 6.1 Table Columns

| Column    | Format     | Notes                       |
| --------- | ---------- | --------------------------- |
| 직원      | Name       | Link to employee            |
| 부서      | Department | -                           |
| 날짜      | YYYY.MM.DD | -                           |
| 시작 시간 | HH:MM      | -                           |
| 종료 시간 | HH:MM      | -                           |
| 유형      | Badge      | 출근, 야근, 휴가, etc.      |
| 시간      | Duration   | Hours worked                |
| 상태      | Badge      | Confirmed, Pending, Swapped |
| 작업      | Actions    | [편집] [삭제]               |

---

## 7. Schedule Actions

### 7.1 Add/Edit Schedule Form

```
┌─────────────────────────────┐
│ 근무 일정 추가/편집         │
├─────────────────────────────┤
│                             │
│ 직원 *: [직원 선택]        │
│ 날짜 *: [날짜 입력]        │
│ 근무 유형 *: [드롭다운]    │
│   - 출근 (09:00~18:00)    │
│   - 야근 (14:00~23:00)    │
│   - 야간 (23:00~08:00)    │
│   - 휴가                   │
│   - 휴무                   │
│                             │
│ 시작 시간: [시간:분]       │
│ 종료 시간: [시간:분]       │
│                             │
│ 참고사항: [텍스트 에리어]  │
│                             │
│ [취소] [저장]              │
│                             │
└─────────────────────────────┘
```

### 7.2 Shift Swap Request

**If employee requests shift swap**:

1. Show affected employees
2. Mark as "대기중" status
3. Require manager approval
4. Show in approval queue

---

## 8. Shift Types Catalog

| Type        | Code      | Start  | End    | Hours    | Color  |
| ----------- | --------- | ------ | ------ | -------- | ------ |
| Regular     | day       | 09:00  | 18:00  | 9        | Blue   |
| Late Shift  | afternoon | 14:00  | 23:00  | 9        | Amber  |
| Night Shift | night     | 23:00  | 08:00  | 9        | Purple |
| Flexible    | flex      | Custom | Custom | Variable | Green  |
| Off Day     | off       | -      | -      | 0        | Gray   |
| Leave       | leave     | -      | -      | 0        | Blue   |
| Holiday     | holiday   | -      | -      | 0        | Red    |

---

## 9. Data Model

```typescript
interface Schedule {
  id: number;
  employeeId: string;
  date: string; // YYYY-MM-DD
  shiftType:
    | "day"
    | "afternoon"
    | "night"
    | "flex"
    | "off"
    | "leave"
    | "holiday";
  startTime?: string; // HH:MM
  endTime?: string; // HH:MM
  status: "confirmed" | "pending" | "swapped" | "cancelled";
  notes?: string;
  createdAt: string;
  updatedAt: string;
}

interface ShiftSwapRequest {
  id: number;
  employeeId: string;
  scheduleId: number;
  swapWithEmployeeId: string; // Employee to swap with
  status: "pending" | "approved" | "rejected";
  requestedAt: string;
  respondedAt?: string;
  reason?: string;
}

interface ScheduleFilter {
  departmentId?: string;
  employeeId?: string;
  dateFrom?: string;
  dateTo?: string;
  shiftType?: string;
  status?: string[];
}
```

---

## 10. API Requirements

| Method | Endpoint                                          | Response                                 |
| ------ | ------------------------------------------------- | ---------------------------------------- |
| GET    | `/api/v1/hr/schedules?filters`                    | `{ data: Schedule[], meta: Pagination }` |
| GET    | `/api/v1/hr/schedules/{scheduleId}`               | `{ data: Schedule }`                     |
| POST   | `/api/v1/hr/schedules`                            | `{ id: number }`                         |
| PATCH  | `/api/v1/hr/schedules/{scheduleId}`               | `{ success: true }`                      |
| DELETE | `/api/v1/hr/schedules/{scheduleId}`               | `{ success: true }`                      |
| POST   | `/api/v1/hr/schedules/bulk`                       | `{ created: [], errors: [] }`            |
| GET    | `/api/v1/hr/schedules/{empId}/month?date=YYYY-MM` | `{ data: Schedule[] }`                   |
| POST   | `/api/v1/hr/shift-swaps`                          | `{ id: number }` (swap request)          |

---

## 11. Coverage Planning

### 11.1 Shift Coverage Summary

**Per shift, show**:

- Total required staff
- Assigned staff
- Coverage percentage
- Gaps/alerts

---

## 12. Responsive Design

- **Desktop**: Month grid + sidebar filters
- **Tablet**: Month grid or week view
- **Mobile**: Week view or list view

---

## 13. Notifications

| Event          | Message                       |
| -------------- | ----------------------------- |
| Shift added    | "근무 일정이 추가되었습니다." |
| Shift deleted  | "근무 일정이 삭제되었습니다." |
| Swap requested | "교대 요청이 있습니다."       |
| Swap approved  | "교대가 승인되었습니다."      |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
