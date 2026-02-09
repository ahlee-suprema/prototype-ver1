# HR 외근/출장 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-business-trip-management`  
> Route: `/hr/business-trip-management`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `business-trip-management.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers oversee all employee business trips, approve requests, track budgets, and manage expense claims
- **Core Functions**:
  - View and approve business trip/external work requests
  - Track trip locations, durations, and purposes
  - Monitor trip budgets and expenses
  - Manage expense claims and reimbursements
  - Generate trip reports and analytics
  - Send notifications to approvers and employees

### 1.2 Access Control

- **HR Admin**: Full organization view with approval authority
- **Department Manager**: Team trip review (HR final approves)
- **CFO/Finance**: Budget oversight and expense approval
- **Executive**: Read-only summary view

---

## 2. Screen Layout & Components

### 2.1 Main Screen Structure

```
┌──────────────────────────────────────────────────────┐
│ 외근/출장 관리                                       │
│ 조직 전체 외근 및 출장을 관리합니다                 │
├──────────────────────────────────────────────────────┤
│ ┌──────────────┐ ┌──────────────┐ ┌──────────────┐  │
│ │ 이번 달      │ │ 올해         │ │ 미승인       │  │
│ │ 12건         │ │ 45건         │ │ 5건          │  │
│ └──────────────┘ └──────────────┘ └──────────────┘  │
├──────────────────────────────────────────────────────┤
│ [검색] [부서] [기간] [상태 필터] [내보내기]          │
├──────────────────────────────────────────────────────┤
│ 외근/출장 신청 현황                                  │
│ ┌────────────────────────────────────────────────┐  │
│ │ ☑ 직원명 | 부서 | 목적지 | 기간 | 상태 | 작업 │  │
│ │──────────────────────────────────────────────│  │
│ │ ☑ 김철수 | 개발팀 | 서울 | 2d | [대기중]   │  │
│ │ ☑ 이영희 | 영업팀 | 부산 | 1d | [승인]     │  │
│ └────────────────────────────────────────────────┘  │
├──────────────────────────────────────────────────────┤
│ [이전] 1 2 3 [다음]                                  │
└──────────────────────────────────────────────────────┘
```

---

## 3. Summary Statistics (Top Row)

### 3.1 Three-Card Grid

| Card | Metric  | Example |
| ---- | ------- | ------- |
| 1    | 이번 달 | 12건    |
| 2    | 올해    | 45건    |
| 3    | 미승인  | 5건     |

**Color**:

- Card 1: Blue
- Card 2: Amber
- Card 3: Red (attention)

---

## 4. Search & Filter Section

### 4.1 Search Bar

**Field**: Employee name or destination  
**Placeholder**: "직원명 또는 목적지로 검색..."

### 4.2 Quick Filters

| Filter     | Type         | Options                                |
| ---------- | ------------ | -------------------------------------- |
| Department | Dropdown     | IT, HR, Sales, etc.                    |
| Date Range | Date Picker  | From ~ To                              |
| Status     | Multi-select | Pending, Approved, Rejected, Completed |

### 4.3 Advanced Filters (Collapsible)

1. Employee ID
2. Department (multi-select)
3. Destination (autocomplete)
4. Date Range (from ~ to)
5. Duration Range (min ~ max days)
6. Status (Pending, Approved, Rejected, Completed, Cancelled)
7. Trip Type (External work, Business trip, Conference)
8. Budget Range (min ~ max)

---

## 5. Business Trip Records Table

### 5.1 Column Definitions

| #   | Column      | Format      | Notes                          |
| --- | ----------- | ----------- | ------------------------------ |
| 1   | ☑           | Checkbox    | Multi-select                   |
| 2   | 직원명      | Name        | Link to employee detail        |
| 3   | 부서        | Department  | -                              |
| 4   | 목적지      | Destination | City/Country                   |
| 5   | 기간        | Duration    | e.g., "2days", "1/9~1/11"      |
| 6   | 기간 (일수) | Days        | Number of days                 |
| 7   | 사유        | Reason      | Purpose (고객방문, 회의, etc.) |
| 8   | 예산        | Budget      | Amount (KRW)                   |
| 9   | 소비액      | Spent       | Amount (KRW)                   |
| 10  | 상태        | Badge       | Pending/Approved/Completed     |
| 11  | 작업        | Actions     | [승인] [반려] [상세]           |

### 5.2 Row Styling

- **Pending Approval**: Light amber background (amber-50)
- **Approved**: White background
- **Rejected**: Light red background (rose-50)
- **Completed**: Light gray background (slate-50)
- **Budget Exceeded**: Dotted red border on budget cell

---

## 6. Trip Request Actions

### 6.1 Per-Row Actions

| Action | Conditions       | Modal                       |
| ------ | ---------------- | --------------------------- |
| 승인   | Status = pending | Confirm with optional notes |
| 반려   | Status = pending | Required reason field       |
| 상세   | Any              | Full trip detail modal      |

### 6.2 Bulk Actions

**When records selected**:

```
☑ [3건 선택됨]
[✓ 일괄 승인] [✗ 일괄 반려] [📌 상태 변경] [✕ 취소]
```

---

## 7. Trip Detail Modal

### 7.1 Modal Content (Expandable Sections)

```
┌────────────────────────────────────┐
│ 외근/출장 신청 상세               │
├────────────────────────────────────┤
│                                    │
│ [직원 정보]                        │
│  직원: 김철수 (EMP-001)           │
│  부서: 개발팀                      │
│  직급: 대리                        │
│                                    │
│ [출장 정보]                        │
│  목적지: 서울 (강남사옥)          │
│  기간: 2/9 ~ 2/11 (3일)          │
│  사유: 고객 미팅 및 협력사 방문   │
│  출장 유형: Business Trip        │
│                                    │
│ [예산 정보]                        │
│  예산액: ₩1,500,000              │
│  소비액: ₩1,200,000              │
│  잔액: ₩300,000                  │
│                                    │
│ [여행 세부사항]                    │
│  교통수단: 항공기 (KE-123)        │
│  숙박: 신라호텔 (2박)             │
│  출발시간: 2/9 08:00              │
│  귀사시간: 2/11 17:30             │
│                                    │
│ [첨부 문서]                        │
│  - 항공권.pdf (500KB)             │
│  - 영수증.xlsx (200KB)            │
│                                    │
│ [승인 상태]                        │
│  상태: [대기중]                    │
│  현재 승인자: 팀장                │
│  승인 대기 시간: 2일              │
│                                    │
│ [관리자 메모]                      │
│ [텍스트 입력]                      │
│                                    │
│ [닫기] [승인] [반려]              │
│                                    │
└────────────────────────────────────┘
```

### 7.2 Expandable Sections

Each section has collapse/expand icon

1. **Employee Information**: Name, ID, Department, Position
2. **Trip Details**: Destination, Dates, Duration, Reason, Trip type
3. **Budget Information**: Allocated budget, Current spent, Remaining, Budget warnings
4. **Travel Details**: Transport mode, Hotel, Check-in/out times
5. **Attachments**: Documents/receipts uploaded
6. **Approval Status**: Current approval stage, Timeline, Notes
7. **Manager Notes**: HR can add comments/notes

---

## 8. Expense Management

### 8.1 Expense Tracking (Within Detail Modal)

**Sub-section**: 비용 항목

```
┌────────────────────────────────┐
│ 항공료: ₩600,000              │
│ 숙박료: ₩480,000              │
│ 식사: ₩120,000               │
│ 교통비: ₩60,000              │
├────────────────────────────────┤
│ 총 소비: ₩1,260,000          │
│ 예산액: ₩1,500,000           │
│ 초과액: ₩0 (OK)               │
└────────────────────────────────┘
```

### 8.2 Expense Claim Workflow

**If Expenses Exceed Budget**:

1. Show warning in expense summary
2. Allow approval with override reason
3. Require VP/CFO additional sign-off
4. Track budget variance

---

## 9. Data Model

```typescript
interface BusinessTripRequest {
  id: number;
  employeeId: string;
  employeeName: string;
  department: string;
  submittedAt: string; // YYYY-MM-DD
  startDate: string; // YYYY-MM-DD
  endDate: string; // YYYY-MM-DD
  destination: string; // Location
  reason: string; // Purpose
  tripType: "external-work" | "business-trip" | "conference";
  duration: number; // Days
  allocatedBudget: number; // KRW
  currentSpent: number; // KRW
  status: "pending" | "approved" | "rejected" | "completed" | "cancelled";
  transportType?: string; // Flight, Train, Car, etc.
  accommodation?: string; // Hotel name
  checkInTime?: string; // HH:MM
  checkOutTime?: string; // HH:MM
  attachments?: string[]; // Document URLs
  approverName?: string;
  approvedAt?: string;
  rejectionReason?: string;
  managerNotes?: string;
}

interface ExpenseItem {
  id: number;
  tripId: number;
  category: "flight" | "hotel" | "meal" | "transport" | "other";
  amount: number;
  description: string;
  receiptUrl?: string;
  submittedAt: string;
}

interface TripFilter {
  employeeName?: string;
  destination?: string;
  departmentId?: string;
  dateFrom?: string;
  dateTo?: string;
  status?: string[];
  tripType?: string;
  page?: number;
  pageSize?: number;
}
```

---

## 10. API Requirements

| Method | Endpoint                                      | Response                                            |
| ------ | --------------------------------------------- | --------------------------------------------------- |
| GET    | `/api/v1/hr/business-trips/summary`           | `{ thisMonth, thisYear, pending }`                  |
| GET    | `/api/v1/hr/business-trips?filters`           | `{ data: BusinessTripRequest[], meta: Pagination }` |
| GET    | `/api/v1/hr/business-trips/{tripId}`          | `{ data: BusinessTripRequest }`                     |
| PATCH  | `/api/v1/hr/business-trips/{tripId}/approve`  | `{ success: true }`                                 |
| PATCH  | `/api/v1/hr/business-trips/{tripId}/reject`   | `{ success: true }`                                 |
| POST   | `/api/v1/hr/business-trips/{tripId}/expenses` | `{ id: number }`                                    |
| POST   | `/api/v1/hr/business-trips/export`            | `{ fileUrl: string }`                               |
| POST   | `/api/v1/hr/business-trips/batch-approve`     | `{ approved: [], errors: [] }`                      |

---

## 11. Approval Workflow

### 11.1 Approval Process

1. Employee submits trip request
2. Appears in HR Dashboard pending queue
3. HR Manager reviews and clicks "승인"
4. Optional modal for notes
5. PATCH API updates status
6. Employee receives notification (email + in-app)
7. Request marked as "승인"

### 11.2 Rejection Process

1. HR Manager clicks "반려"
2. Modal appears with required reason field
3. Reason max 500 characters
4. PATCH API with rejection reason
5. Employee notified with reason
6. Request marked as "반려"

---

## 12. Export Functionality

**Options**:

- Format: CSV, Excel, PDF
- Scope: All trips, Filtered, Selected
- Columns: Selectable
- Report type: Summary or Detailed

---

## 13. Edge Cases

| Scenario                      | Behavior                                                 |
| ----------------------------- | -------------------------------------------------------- |
| Overlapping approved trips    | Show warning but allow (multi-trip same period possible) |
| Budget exceeded               | Show warning, allow approval with override notes         |
| Expense claim after deadline  | Flag in red, may require VP approval                     |
| Trip cancelled after approval | Change status to "cancelled", refund expense             |
| No receipts provided          | Show warning but allow (honor-based for small amounts)   |
| International trip            | Additional approval steps, visa/passport validation      |

---

## 14. Responsive Design

- **Desktop**: Full table with all columns visible
- **Tablet**: Hide optional columns (manager notes, attachments summary)
- **Mobile**: Card-based view, 1 trip per card

---

## 15. Notifications

| Event          | Recipient | Message                        |
| -------------- | --------- | ------------------------------ |
| Trip submitted | HR        | "새로운 출장 신청이 있습니다." |
| Trip approved  | Employee  | "출장이 승인되었습니다."       |
| Trip rejected  | Employee  | "출장 신청이 반려되었습니다."  |
| Budget alert   | Finance   | "출장 예산이 초과되었습니다."  |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
