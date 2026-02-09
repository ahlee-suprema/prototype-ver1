# 직원 연장근무 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `emp-overtime-management`  
> Route: `/emp/overtime`  
> Parent Layout: `Employee Portal Layout`  
> Prototype File: `emp-overtime-management.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: Employees track overtime hours, submit overtime requests, and view overtime request history
- **Core Functions**:
  - View accumulated overtime hours (this month/year)
  - See overtime request history with status
  - Submit new overtime requests
  - View overtime balance and limits
  - Track approved vs. pending overtime

### 1.2 Access Control

- **Employee**: Full access to own overtime records
- **Manager**: View team's overtime for approval
- **HR Admin**: Full audit access

---

## 2. Screen Layout & Components

### 2.1 Main Sections

1. **Page Title & Description**
   - Title: "연장근무 관리"
   - Description: "연장근무 신청 및 현황을 관리합니다"

2. **Quick Action Button**
   - Button: "+ 연장근무 신청"
   - Action: Navigate to overtime request form

3. **Overtime Balance Cards (Grid)**
   - This Month: {hours}h
   - This Year: {hours}h
   - Pending Approval: {count}건

4. **Overtime Request History**
   - Date range filter (quick select buttons)
   - Table of overtime records with status
   - Columns: Date, Duration, Reason, Status, Actions
   - Pagination for history

5. **Overtime Request Status Section**
   - Pending requests highlighted
   - Approved requests archived
   - Request details on click

---

## 3. Overtime Balance Card Specifications

### 3.1 Three-Card Grid

| Card | Label     | Data      | Color |
| ---- | --------- | --------- | ----- |
| 1    | 이번 달   | {hours}h  | Blue  |
| 2    | 올해      | {hours}h  | Amber |
| 3    | 승인 대기 | {count}건 | Red   |

**Card Styling**:

- Size: 1/3 width responsive
- Background: White with subtle shadow
- Typography: Label (small, slate-500), Value (large, bold)
- Hover: Slight elevation

---

## 4. Overtime Records Table

### 4.1 Column Definitions

| #   | Column    | Format                            | Sortable |
| --- | --------- | --------------------------------- | -------- |
| 1   | 신청 날짜 | YYYY.MM.DD                        | No       |
| 2   | 근무 날짜 | YYYY.MM.DD                        | No       |
| 3   | 근무 시간 | {duration}h                       | No       |
| 4   | 사유      | Text (truncated)                  | No       |
| 5   | 상태      | Badge (Pending/Approved/Rejected) | No       |
| 6   | 관리      | Approve/Reject/View buttons       | No       |

### 4.2 Status Display

- **Pending**: Amber background, "승인 대기 중"
- **Approved**: Green background, "승인 완료"
- **Rejected**: Red background, "반려됨"

---

## 5. Request Status Section

### 5.1 Pending Requests

Highlighted section showing:

- Count of pending requests
- Most recent pending item details
- Quick approve/reject options (if manager role)

### 5.2 Request Timeline

Chronological list of overtime submissions:

- Request date
- Work date
- Hours
- Reason
- Current status
- Timeline of status changes

---

## 6. Data Model

```typescript
interface OvertimeBalance {
  year: number;
  thisMonth: {
    approved: number; // Hours
    pending: number;
  };
  thisYear: {
    approved: number;
    pending: number;
  };
  limit?: number; // Maximum allowed per month
}

interface OvertimeRequest {
  id: number;
  employeeId: string;
  requestDate: string; // YYYY-MM-DD
  workDate: string; // YYYY-MM-DD
  startTime: string; // HH:MM
  endTime: string; // HH:MM
  hours: number; // Calculated
  reason: string;
  status: "pending" | "approved" | "rejected";
  approverName?: string;
  approvedAt?: string;
  notes?: string;
}
```

---

## 7. API Requirements

| Method | Endpoint                                      | Response                               |
| ------ | --------------------------------------------- | -------------------------------------- |
| GET    | `/api/v1/employees/{empId}/overtime-balance`  | `{ thisMonth, thisYear, limit }`       |
| GET    | `/api/v1/employees/{empId}/overtime-requests` | `{ data: OvertimeRequest[], summary }` |
| POST   | `/api/v1/employees/{empId}/overtime-requests` | `{ id, status: 'pending' }`            |

---

## 8. Key Features

- **Smart Balance Calculation**: Auto-calculate approved vs pending
- **Request Submission**: Quick form with date/time pickers
- **Status Tracking**: Real-time update of request status
- **Notification**: Toast alerts for approval/rejection
- **Export Option**: Download overtime history as PDF/Excel

---

**Document Version**: 0.2  
**Status**: Specification Complete
