# HR 연장근무 신청 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-overtime-request`  
> Route: `/hr/overtime-request` or `/hr/overtime/request`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `overtime-request.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers/Admins submit overtime requests on behalf of employees
- **Core Functions**:
  - Select employee to submit overtime for
  - Enter work date and time
  - Record overtime duration
  - Specify overtime compensation type
  - Submit with approval chain

### 1.2 Access Control

- **HR Admin**: Submit on own behalf or any employee
- **HR Manager**: Submit on team members only
- **Finance**: View and approve budgets

---

## 2. Screen Layout

### 2.1 Main Form Structure

```
┌────────────────────────────────────────────┐
│ HR 연장근무 신청                           │
│ HR 담당자가 직원 연장근무를 신청합니다     │
├────────────────────────────────────────────┤
│                                             │
│ 신청 대상 직원 *                           │
│ [직원 선택 또는 검색...]                   │
│                                             │
│ 근무 날짜 *                                 │
│ [날짜 선택]                                │
│                                             │
│ 근무 시간 *                                 │
│ [시작 시간 HH:MM] ~ [종료 시간 HH:MM]     │
│                                             │
│ ┌──────────────────────────────────────┐   │
│ │ 예상 근무 시간: 2h 30m               │   │
│ │ (22:00 ~ 00:30, 자정 경계 표시)     │   │
│ └──────────────────────────────────────┘   │
│                                             │
│ 보상 유형 *                                 │
│ ○ 급여 지급   ○ 휴무일   ○ 미지급         │
│                                             │
│ 근무 사유 *                                 │
│ [텍스트 입력 필드]                        │
│ (프로젝트 마감, 긴급 대응, 등)            │
│                                             │
│ 관리자 메모 (선택사항)                     │
│ [텍스트 입력 필드]                        │
│                                             │
│ [취소] [신청하기]                         │
│                                             │
└────────────────────────────────────────────┘
```

---

## 3. Form Fields

### 3.1 Employee Selector

**Field Name**: `employeeId`  
**Type**: Searchable dropdown/autocomplete  
**Required**: Yes

**Features**:

- Search by ID, name, or email
- Show employee details (department, position)
- Only active employees
- Default to current user (if applicable)

---

### 3.2 Overtime Date

**Field Name**: `overtimeDate`  
**Type**: Date input  
**Required**: Yes  
**Format**: YYYY-MM-DD

**Validation**:

- Past or current date only
- Not holiday (show warning if holiday)
- Can be any day (weekend included)

---

### 3.3 Overtime Time Range

**Fields**: `startTime`, `endTime`  
**Type**: Time input (HH:MM)  
**Required**: Yes

**Features**:

- 24-hour format
- Can cross midnight (e.g., 22:00 ~ 00:30 next day)
- Show warning if crossing midnight
- Auto-calculate duration

**Validation**:

- Both times required
- End time > start time (or next day if midnight cross)
- Max 12 hours consecutive
- At least 30 minutes

---

### 3.4 Duration Display

**Auto-calculated from start/end times**:

```
예상 근무 시간: 2h 30m
(22:00 ~ 00:30, 자정 경계 표시 ⚠️)
```

**If crossing midnight**: Show tooltip "다음날로 연장됨"

---

### 3.5 Compensation Type

**Field Name**: `compensationType`  
**Type**: Radio buttons  
**Required**: Yes

**Options**:
| Value | Label | Description |
|-------|-------|-------------|
| paid | 급여 지급 | Additional pay in salary |
| cto | 휴무일 (보상휴무) | Compensatory Time Off |
| unpaid | 미지급 | Unpaid overtime (voluntary) |

---

### 3.6 Overtime Reason

**Field Name**: `reason`  
**Type**: Textarea or dropdown  
**Required**: Yes  
**Max**: 200 characters

**Common options**:

- 프로젝트 마감 (Project deadline)
- 긴급 대응 (Emergency response)
- 시스템 장애 (System outage)
- 고객 요청 (Client request)
- 기타 (Other)

---

### 3.7 Manager Notes

**Field Name**: `managerNotes`  
**Type**: Textarea  
**Required**: No  
**Max**: 500 characters

**Usage**: HR adds context or special instructions

---

## 4. Data Model

```typescript
interface HROvertimeRequest extends OvertimeRecord {
  submittedByHRId: string; // HR user who submitted
  submittedForEmployeeId: string; // Employee for whom submitted
  submittedOnBehalfOf: boolean;
  hrNotes?: string; // HR-specific notes
  managerApprovalNotes?: string;
}
```

---

## 5. API Requirements

| Method | Endpoint                             | Request                                                                                       | Response                    |
| ------ | ------------------------------------ | --------------------------------------------------------------------------------------------- | --------------------------- |
| GET    | `/api/v1/hr/employees?search={text}` | N/A                                                                                           | `{ data: Employee[] }`      |
| POST   | `/api/v1/hr/overtime/requests`       | `{ employeeId, overtimeDate, startTime, endTime, compensationType, reason, submittedByHRId }` | `{ id, status: 'pending' }` |

---

## 6. Validation Rules

| Field        | Rule                 | Error Message                                  |
| ------------ | -------------------- | ---------------------------------------------- |
| Employee     | Required, active     | "직원을 선택해주세요."                         |
| Date         | Required, past/today | "근무 날짜를 선택해주세요."                    |
| Start time   | Required             | "시작 시간을 입력해주세요."                    |
| End time     | Required, > start    | "종료 시간을 입력해주세요."                    |
| Duration     | ≥ 30 min, ≤ 12 hr    | "연장근무는 30분 이상 12시간 이하여야 합니다." |
| Compensation | Required             | "보상 유형을 선택해주세요."                    |
| Reason       | Required             | "근무 사유를 입력해주세요."                    |

---

## 7. Workflows

### 7.1 Submit Overtime for Employee

1. HR navigates to `/hr/overtime-request`
2. Searches and selects employee
3. Enters overtime date and time
4. System auto-calculates duration
5. Selects compensation type
6. Enters reason
7. (Optional) Adds manager notes
8. Clicks "신청하기"
9. Request created with HR context
10. Toast: "연장근무가 신청되었습니다. (사용자: 김철수)"
11. Redirect to overtime management or detail view

### 7.2 Batch Submit (Optional)

Alternative: Allow submission for multiple employees with same overtime details (less common)

---

## 8. Edge Cases

| Scenario                       | Behavior                                                   |
| ------------------------------ | ---------------------------------------------------------- |
| Midnight crossing              | Show warning "자정을 넘어 다음날로 연장됩니다"             |
| Weekend overtime               | Allow, show "주말 근무" tag                                |
| Holiday overtime               | Allow, show "공휴일 근무" tag, suggest higher compensation |
| Duplicate submission same time | Warn but allow                                             |
| Overtime exceeds 10h/day       | Warn, require override notes                               |

---

## 9. Responsive Design

- **Desktop**: Full form with side-by-side time inputs
- **Tablet**: Stacked form
- **Mobile**: Full-screen form with vertical layout

---

## 10. Toast Messages

| Event            | Message                        |
| ---------------- | ------------------------------ |
| Submit success   | "연장근무가 신청되었습니다."   |
| Submit error     | "신청 중 오류가 발생했습니다." |
| Validation error | "{field} 입력이 필요합니다."   |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development

**Implementation Note**: Similar to emp-leave-request, this HR form adds employee selector to a base overtime request form. Consider component reuse pattern.
