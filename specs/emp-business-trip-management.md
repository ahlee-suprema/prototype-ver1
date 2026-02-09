# 직원 외근/출장 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `emp-business-trip-management`  
> Route: `/emp/business-trip`  
> Parent Layout: `Employee Portal Layout`  
> Prototype File: `emp-business-trip-management.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: Employees submit business trip requests, view trip history, and track approval status
- **Core Functions**:
  - Submit new business trip/external work requests
  - View trip history with locations and dates
  - Track trip request approval status
  - View trip details and itinerary
  - Cancel or modify pending trip requests
  - View trip budget/expense tracking

### 1.2 Access Control

- **Employee**: Submit and view own business trips
- **Manager**: View team member trips for approval
- **HR Admin**: Full audit and management access

---

## 2. Screen Layout & Components

### 2.1 Main Sections

```
┌──────────────────────────────────────────────┐
│ 외근 및 출장 관리                             │
│ 외근, 출장 신청 및 현황을 관리합니다          │
├──────────────────────────────────────────────┤
│ [+ 외근/출장 신청]                            │
├──────────────────────────────────────────────┤
│ ┌────────────┐ ┌────────────┐ ┌────────────┐ │
│ │ 이번 달    │ │ 올해       │ │ 미승인     │ │
│ │ {count}건  │ │ {count}건  │ │ {count}건  │ │
│ └────────────┘ └────────────┘ └────────────┘ │
├──────────────────────────────────────────────┤
│ [이번주] [이번달] [올해] [직접선택]          │
├──────────────────────────────────────────────┤
│ 외근/출장 신청 현황                          │
│ ┌─────────────────────────────────────────┐  │
│ │ 2026-02-09 | 서울 출장 | 1일 | [대기중] │  │
│ │ 2026-02-03 | 부산 출장 | 2일 | [승인]   │  │
│ │ 2026-01-30 | 외근 (회의) | 반일 | [승인]│  │
│ └─────────────────────────────────────────┘  │
├──────────────────────────────────────────────┤
│ [이전] 1 2 3 [다음]                          │
└──────────────────────────────────────────────┘
```

---

## 3. Quick Stats Cards

### 3.1 Three-Card Grid

| Card | Label   | Data      | Color |
| ---- | ------- | --------- | ----- |
| 1    | 이번 달 | {count}건 | Blue  |
| 2    | 올해    | {count}건 | Amber |
| 3    | 미승인  | {count}건 | Red   |

**Card Content**:

- Total trips this period
- Breakdown by status (if needed)
- Quick actions (View all, Submit new)

---

## 4. Trip Records Table/List

### 4.1 Column Definitions (Table View)

| #   | Column    | Format        | Notes                             |
| --- | --------- | ------------- | --------------------------------- |
| 1   | 신청 날짜 | YYYY.MM.DD    | Request submission date           |
| 2   | 위치      | Location name | Destination city/country          |
| 3   | 기간      | Duration      | e.g., "2 Days", "반일" (Half day) |
| 4   | 사유      | Trip reason   | Purpose (회의, 현장방문, etc.)    |
| 5   | 상태      | Badge         | Pending/Approved/Rejected         |
| 6   | 관리      | Actions       | View detail, Cancel, Edit         |

### 4.2 Card View (Mobile)

```
┌──────────────────────────────────┐
│ 📍 서울 출장                     │
│ 2026-02-09 ~ 2026-02-11 (3일)  │
│ 사유: 고객 방문 및 협력사 미팅   │
│ [대기중]      [수정] [취소]     │
└──────────────────────────────────┘
```

---

## 5. Trip Detail View

### 5.1 Modal/Drawer with Trip Details

**Sections**:

1. **Trip Information**
   - Destination (Location name)
   - Duration (From ~ To dates)
   - Duration (Days/Hours)
   - Purpose/Reason

2. **Travel Details**
   - Transport type (Car, Flight, Train, etc.)
   - Departure time
   - Return time
   - Accommodation (if applicable)

3. **Expense Information**
   - Budget allocated
   - Current spent
   - Receipts/Invoices attached
   - Budget remaining

4. **Approval Status**
   - Current approver
   - Approval timestamp
   - Approver notes
   - Timeline of status changes

5. **Actions**
   - Edit (if pending)
   - Cancel (if pending)
   - Close modal

---

## 6. Date Range Filter

### 6.1 Quick Select Buttons

| Button | Label    | Logic                   |
| ------ | -------- | ----------------------- |
| 1      | 이번주   | Current week            |
| 2      | 이번달   | Current month (default) |
| 3      | 올해     | Current year            |
| 4      | 직접선택 | Custom date range       |

**Button Styling**:

- Inactive: White bg, slate border
- Active: Primary blue bg, white text, shadow

---

## 7. Data Model

```typescript
interface BusinessTrip {
  id: number;
  employeeId: string;
  requestDate: string; // YYYY-MM-DD (when submitted)
  startDate: string; // YYYY-MM-DD (trip start)
  endDate: string; // YYYY-MM-DD (trip end)
  destination: string; // City/Location name
  reason: string; // Purpose of trip
  tripType: "external-work" | "business-trip";
  duration: {
    days: number;
    hours?: number;
  };
  transportType?: "car" | "flight" | "train" | "other";
  accommodation?: string; // Hotel name if applicable
  budget?: number; // Allocated budget
  spent?: number; // Current expenses
  status: "pending" | "approved" | "rejected" | "completed";
  approverName?: string;
  approvedAt?: string;
  notes?: string;
  attachments?: string[]; // Receipt/document URLs
}

interface TripSummary {
  thisMonth: number; // Count of trips
  thisYear: number;
  pendingApproval: number;
  approvedCount: number;
}
```

---

## 8. API Requirements

| Method | Endpoint                                                     | Response                                     |
| ------ | ------------------------------------------------------------ | -------------------------------------------- |
| GET    | `/api/v1/employees/{empId}/business-trips/summary`           | `{ thisMonth, thisYear, pending }`           |
| GET    | `/api/v1/employees/{empId}/business-trips?date-from&date-to` | `{ data: BusinessTrip[], meta: Pagination }` |
| POST   | `/api/v1/employees/{empId}/business-trips`                   | `{ id, status: 'pending' }`                  |
| PATCH  | `/api/v1/employees/{empId}/business-trips/{tripId}`          | `{ success: true }`                          |
| DELETE | `/api/v1/employees/{empId}/business-trips/{tripId}`          | `{ success: true }` (if pending)             |
| GET    | `/api/v1/employees/{empId}/business-trips/{tripId}`          | `{ data: BusinessTrip (full detail) }`       |

---

## 9. Key Features

### 9.1 Trip Submission

- Simple form with date picker
- Location autocomplete (popular destinations)
- Reason dropdown with common options
- Transport type selector
- Budget suggestion based on destination

### 9.2 Trip Tracking

- Real-time status updates
- Approval timeline visualization
- Approver comments/notes
- Status notification on changes

### 9.3 Expense Management

- Upload receipts/invoices
- Expense amount tracking
- Budget vs. actual comparison
- Expense claim workflow

### 9.4 Integration

- Calendar integration (show trips on calendar)
- Outlook/Google Calendar sync
- Auto-add to team calendar when approved
- Notification to manager/HR on approval

---

## 10. Edge Cases

| Scenario                        | Behavior                                       |
| ------------------------------- | ---------------------------------------------- |
| User cancels pending trip       | Status changes to cancelled, notification sent |
| User modifies approved trip     | Request new approval for changes               |
| Trip dates overlap              | Show warning, suggest alternative dates        |
| Duplicate trip requests         | Warn user of similar recent request            |
| Budget exceeded                 | Show warning, allow override with notes        |
| Multi-day trip spanning weekend | Calculate only business days if needed         |

---

## 11. Responsive Design

- **Desktop**: Full table with all columns
- **Tablet**: Card layout with horizontal scroll
- **Mobile**: Compact card view with tap-to-expand detail

---

## 12. Notifications & Alerts

| Event             | Message                           | Type    |
| ----------------- | --------------------------------- | ------- |
| Trip approved     | "출장이 승인되었습니다."          | Success |
| Trip rejected     | "출장 신청이 반려되었습니다."     | Error   |
| Trip cancelled    | "출장이 취소되었습니다."          | Info    |
| Budget warning    | "예산이 초과되었습니다."          | Warning |
| Approval reminder | "결재 대기 중인 출장이 있습니다." | Info    |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
