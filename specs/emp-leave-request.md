# 직원 휴가 신청 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `emp-leave-request`  
> Route: `/emp/leave/request`  
> Parent Layout: `Employee Portal Layout`  
> Prototype File: `emp-leave-request.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: Employees submit new leave requests with detailed information and receive real-time balance feedback
- **Core Functions**:
  - Select leave type
  - Choose leave dates
  - View remaining balance
  - Calculate deducted days
  - Provide reason/notes
  - Submit for approval
  - View submission confirmation

### 1.2 Access Control

- **Employee**: Submit and edit own requests (if pending)
- **Manager**: Submit on behalf of team members (optional)

---

## 2. Screen Layout & Components

### 2.1 Main Form Structure

```
┌────────────────────────────────────────────┐
│ 휴가 신청                                   │
│ 신규 휴가를 신청합니다                      │
├────────────────────────────────────────────┤
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

## 3. Form Fields

### 3.1 Leave Type Selector

**Field Name**: `leaveType`  
**Type**: Dropdown/Select  
**Required**: Yes

**Options**:
| Code | Label | Color | Annual Limit |
|------|-------|-------|--------------|
| annual | 연차 | Blue | 15 days |
| sick | 병가 | Amber | 3 days |
| family | 가정 사유 | Rose | 5 days |
| maternity | 산모 | Purple | 90 days |
| unpaid | 무급 휴가 | Slate | Unlimited |
| official | 공식 휴일 | Green | Unlimited |

**Styling**:

- Default: White bg, slate border
- Selected: Blue bg (primary-500), white text
- Dropdown has icon: arrow-down

---

### 3.2 Leave Period Selector

**Field Name**: `startDate`, `endDate`  
**Type**: Date Input  
**Required**: Yes  
**Format**: YYYY-MM-DD (input), YYYY.MM.DD (display)

**Validation Rules**:

- Both dates required
- Start date ≥ today
- End date ≥ start date
- Max duration: 30 consecutive days
- Cannot overlap existing approvals
- Must be business days (M-F) or include weekends
- Cross-month selections allowed

**Date Picker Features**:

- Calendar view for both dates
- Highlight selected range
- Show weekends in different color (light gray)
- Show holidays if available
- Keyboard shortcuts (arrow keys for navigation)

---

### 3.3 Leave Period Summary Card

**Display**: After dates are selected

```
┌─────────────────────────────────────┐
│ 잔여 연차: 7일                       │
│ ────────────────────────────        │
│ 신청 기간: 3일 (Fri ~ Mon)         │
│ 토요일: 0.5일                       │
│ 일요일: 1일 (미반영)                │
│ ─────────────────────────────────   │
│ 차감 일수: 2.5일                     │
│ ─────────────────────────────────   │
│ 예상 잔여: 4.5일                     │
│ (신청 후)                           │
└─────────────────────────────────────┘
```

**Breakdown Calculation**:

- Total requested days
- Weekend deduction (0 or 0.5 per day)
- Holiday deduction
- Net deduction (차감 일수)
- Remaining balance projection

---

### 3.4 Reason/Notes Field

**Field Name**: `reason`  
**Type**: Textarea  
**Required**: No (optional)  
**Placeholder**: "신청 사유를 입력해주세요 (최대 500자)"

**Constraints**:

- Max 500 characters
- No special characters
- Character counter: "150/500"

---

## 4. Form Actions

### 4.1 Button States

| State      | Cancel Button    | Submit Button         |
| ---------- | ---------------- | --------------------- |
| Empty      | Enabled, outline | Disabled, gray        |
| Partial    | Enabled, outline | Disabled, gray        |
| Valid      | Enabled, outline | Enabled, primary-blue |
| Submitting | Disabled         | Disabled + spinner    |
| Submitted  | Hidden           | Hidden                |

### 4.2 Form Reset/Cancel

**Cancel Button**:

- Clears all fields
- Closes modal (if in modal)
- Redirects to previous page (if not in modal)
- Shows confirmation: "작성 중인 내용이 사라집니다."

---

## 5. Data Model

```typescript
interface LeaveRequest {
  id?: number;
  employeeId: string;
  leaveType: "annual" | "sick" | "family" | "maternity" | "unpaid" | "official";
  startDate: string; // YYYY-MM-DD
  endDate: string; // YYYY-MM-DD
  reason?: string; // Optional notes
  totalDays: number; // Calendar days
  deductedDays: number; // Actual leave deduction
  status: "pending" | "approved" | "rejected";
  submittedAt: string; // Timestamp
  approverName?: string;
  approvedAt?: string;
  rejectionReason?: string;
}

interface LeaveBalance {
  leaveType: string;
  totalAllowance: number;
  used: number;
  pending: number;
  remaining: number;
  year: number;
}

interface LeaveRequestForm {
  leaveType: string;
  startDate: string;
  endDate: string;
  reason?: string;
  // Calculated fields (not in form)
  totalDays?: number;
  deductedDays?: number;
  currentBalance?: number;
  projectedBalance?: number;
}
```

---

## 6. API Requirements

| Method | Endpoint                                                  | Request Body                                | Response                         |
| ------ | --------------------------------------------------------- | ------------------------------------------- | -------------------------------- |
| GET    | `/api/v1/employees/{empId}/leave-balance`                 | N/A                                         | `{ data: LeaveBalance[] }`       |
| GET    | `/api/v1/employees/{empId}/leave-requests?status=pending` | N/A                                         | `{ data: LeaveRequest[] }`       |
| POST   | `/api/v1/employees/{empId}/leave-requests`                | `{ leaveType, startDate, endDate, reason }` | `{ id, status: 'pending' }`      |
| GET    | `/api/v1/employees/{empId}/leave-requests/{requestId}`    | N/A                                         | `{ data: LeaveRequest }`         |
| PATCH  | `/api/v1/employees/{empId}/leave-requests/{requestId}`    | `{ startDate?, endDate?, reason? }`         | `{ success: true }` (if pending) |

---

## 7. Calculation Logic

### 7.1 Days Calculation

**Algorithm**:

1. Count calendar days between startDate and endDate (inclusive)
2. Subtract non-working days (Sundays)
3. Apply Saturday rule (0.5 day or 0 based on company policy)
4. Subtract official holidays from calendar
5. Result = "차감 일수"

**Example**:

- Request: Fri Feb 9 ~ Mon Feb 12 (4 calendar days)
- Breakdown: Fri(1) + Sat(0.5) + Sun(-) + Mon(1) = 2.5 days
- Remaining: 7 - 2.5 = 4.5 days

### 7.2 Balance Calculation

**Remaining** = Total Allowance - Used - Pending + New Request

**Pending** includes:

- Leave requests with status = 'pending'
- Leave requests with status = 'approved' but not yet taken

---

## 8. Validation Rules

| Field     | Rule                    | Error Message                                |
| --------- | ----------------------- | -------------------------------------------- |
| leaveType | Required                | "휴가 종류를 선택해주세요."                  |
| startDate | Required, ≥ today       | "시작일을 선택해주세요."                     |
| endDate   | Required, ≥ startDate   | "종료일을 선택해주세요."                     |
| dateRange | startDate ≤ endDate     | "종료일이 시작일 이후여야 합니다."           |
| dateRange | Max 30 days             | "최대 30일까지만 신청 가능합니다."           |
| balance   | balance ≥ deductedDays  | "잔여 휴가가 부족합니다."                    |
| overlap   | No overlapping approved | "겹치는 기간에 이미 승인된 휴가가 있습니다." |
| reason    | Max 500 chars           | "사유는 500자 이내여야 합니다."              |

---

## 9. User Workflows

### 9.1 Happy Path: Submit New Request

1. User navigates to `/emp/leave/request`
2. Selects leave type (연차)
3. Picks dates (Feb 9 ~ Feb 12)
4. System shows balance summary (7 → 4.5)
5. User enters optional reason
6. Clicks "신청하기"
7. Request submitted (POST API)
8. Show success toast: "휴가가 신청되었습니다."
9. Redirect to `/emp/leave-management`

### 9.2 Validation Error Flow

1. User selects Feb 9 ~ Feb 12
2. Balance insufficient (only 4 days remaining)
3. Submit button disabled + error message
4. User adjusts dates or cancels

### 9.3 Edit Pending Request

1. From emp-leave-management, click pending request
2. Open request detail modal with "수정" button
3. Redirect to request form with fields pre-filled
4. User modifies dates/reason
5. Clicks "신청하기" (button says "수정하기" if editing)
6. PATCH API called
7. Success toast: "휴가 신청이 수정되었습니다."

---

## 10. Edge Cases

| Scenario                        | Behavior                                                  |
| ------------------------------- | --------------------------------------------------------- |
| User has no remaining balance   | Disable submit, show "잔여 휴가 부족" message             |
| User selects dates in past      | Show error, disable submit                                |
| User selects weekend-only dates | Show warning, 0 days deduction                            |
| User has pending requests       | Show warning "이미 대기 중인 신청이 있습니다"             |
| Leave type not available        | Hide from dropdown, show reason (e.g., "산모: 신청 불가") |
| Cross-year request              | Show warning, calculate per-year deduction                |
| All-day leave on holiday        | Deduction = 0, show notification                          |

---

## 11. Responsive Design

- **Desktop**: Full form with side-by-side date inputs
- **Tablet**: Stacked form with calendar modal on tap
- **Mobile**: Full-screen modal with vertical date pickers

---

## 12. Accessibility

- Form labels use `<label>` with `for` attribute
- Required fields marked with \* and aria-required
- Error messages associated with fields via aria-describedby
- Keyboard navigation: Tab through fields, Enter to submit
- Screen reader announces balance changes in real-time

---

## 13. Toast Messages

| Event                | Message                        | Duration |
| -------------------- | ------------------------------ | -------- |
| Submit success       | "휴가가 신청되었습니다."       | 3s       |
| Submit error         | "신청 중 오류가 발생했습니다." | 4s       |
| Edit success         | "휴가 신청이 수정되었습니다."  | 3s       |
| Validation error     | "[field] 오류 메시지"          | 4s       |
| Insufficient balance | "잔여 휴가가 부족합니다."      | 4s       |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
