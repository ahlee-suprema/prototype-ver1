# HR 휴가 신청 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-leave-request`  
> Route: `/hr/leave-request` or `/hr/leave/request`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `leave-request.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers/Admins submit leave requests on behalf of employees or for their own use
- **Core Functions**:
  - Select employee to submit leave for (or self)
  - Choose leave type and dates
  - View balance and deduction calculation
  - Submit request with approval chain
  - Batch submit for multiple employees

### 1.2 Access Control

- **HR Admin**: Submit on own behalf or any employee
- **HR Manager**: Submit on own behalf or team members
- **Employee**: Not typically access (use emp-leave-request)

---

## 2. Screen Layout

### 2.1 Main Form Structure (Same as emp-leave-request.md)

```
┌────────────────────────────────────────────┐
│ HR 휴가 신청 (대신 신청)                    │
│ HR 담당자가 직원 휴가를 신청합니다          │
├────────────────────────────────────────────┤
│                                             │
│ 신청 대상 직원 *                           │
│ [직원 선택 또는 검색...]                   │
│ (기본: 현재 사용자)                        │
│                                             │
│ 휴가 종류 *                                 │
│ [연차 ▼]                                    │
│                                             │
│ 신청 기간 *                                 │
│ [From Date] ~ [To Date]                    │
│                                             │
│ ┌──────────────────────────────────────┐   │
│ │ 잔여 연차: 7일                        │   │
│ │ 신청 기간: 3일                        │   │
│ │ 차감 일수: 2.5일 (토요일 0.5일)      │   │
│ │ 예상 잔여: 4.5일                      │   │
│ └──────────────────────────────────────┘   │
│                                             │
│ 신청 사유 (선택사항)                       │
│ [텍스트 입력 필드]                        │
│                                             │
│ [취소] [신청하기]                         │
│                                             │
└────────────────────────────────────────────┘
```

---

## 3. Key Differences from Employee Form

### 3.1 Employee Selector Field

**Field Name**: `employeeId`  
**Type**: Searchable dropdown/autocomplete  
**Required**: Yes

**Features**:

- Search by employee ID, name, or email
- Default to current HR user (but can be changed)
- Show employee details (department, current balance)
- Only active employees in list

**Autocomplete Logic**:

```
[검색 입력...] →
  Search term matches:
  - Employee ID (EMP-XXX)
  - Full Name (Kim Chul-su)
  - Email (kim@company.com)
  - Department name
  → Show matching employees with department
```

### 3.2 Balance Display

**Shows balance for selected employee** (not just logged-in user):

```
┌──────────────────────────────────────┐
│ 직원: 김철수 (EMP-001) - 개발팀      │
│ ────────────────────────────────────  │
│ 잔여 연차: 7일                        │
│ 신청 기간: 3일                        │
│ 차감 일수: 2.5일 (토요일 0.5일)      │
│ 예상 잔여: 4.5일                      │
│ ────────────────────────────────────  │
│ ℹ️ 주의: 최근 결재 대기 휴가 1건     │
│    (2/20~2/22, 연차, 승인 대기중)   │
└──────────────────────────────────────┘
```

---

## 4. Data Model

```typescript
interface HRLeaveRequest extends LeaveRequest {
  submittedByHRId: string; // HR user who submitted
  submittedForEmployeeId: string; // Employee for whom submitted
  submittedOnBehalfOf: boolean; // HR submitted for employee
  hrNotes?: string; // HR-specific notes
}
```

---

## 5. Form Fields (Identical to emp-leave-request)

Same as [emp-leave-request.md sections 3-6](emp-leave-request.md)

**All field definitions, validation rules, and API integration remain identical except**:

- POST endpoint: `/api/v1/hr/employees/{employeeId}/leave-requests` (with HR context)
- Include `submittedByHRId` in payload

---

## 6. API Requirements

| Method | Endpoint                                               | Request Body                                                 | Response                              |
| ------ | ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------- |
| GET    | `/api/v1/hr/employees?search={text}`                   | N/A                                                          | `{ data: Employee[] }` (for dropdown) |
| GET    | `/api/v1/employees/{empId}/leave-balance`              | N/A                                                          | `{ data: LeaveBalance[] }`            |
| POST   | `/api/v1/employees/{empId}/leave-requests`             | `{ leaveType, startDate, endDate, reason, submittedByHRId }` | `{ id, status: 'pending' }`           |
| PATCH  | `/api/v1/employees/{empId}/leave-requests/{requestId}` | Same as above                                                | `{ success: true }`                   |

---

## 7. Validation Rules

Same as emp-leave-request with additions:

| Rule                       | Error Message                           |
| -------------------------- | --------------------------------------- |
| Employee selected          | "신청 대상 직원을 선택해주세요."        |
| Selected employee active   | "비활성 또는 퇴직한 직원입니다."        |
| Employee has leave balance | "선택한 직원의 잔여 휴가가 부족합니다." |

---

## 8. Workflows

### 8.1 HR Submitting for Employee

1. HR navigates to `/hr/leave-request`
2. Searches and selects employee (e.g., "김철수")
3. System fetches employee's balance and pending requests
4. HR selects leave type, dates, reason
5. Confirms submission
6. Request created with HR context
7. Toast: "휴가가 신청되었습니다. (사용자: 김철수)"

### 8.2 Bulk Submit (Optional Feature)

**Alternative approach**:

- Allow HR to submit for multiple employees in single form
- Table with checkboxes for each employee
- Same leave type/dates for all selected
- Bulk submit creates multiple requests

---

## 9. Accessibility

Same as emp-leave-request with employee selector enhancements:

- Employee search field has aria-autocomplete
- Results announced as user types
- Keyboard navigation through dropdown

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development

**Note**: This form is nearly identical to emp-leave-request, with primary addition being employee selector field. During implementation, consider code reuse or component generalization.
