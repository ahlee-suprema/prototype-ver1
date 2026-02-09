# HR 요청 관리 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-requests-management`  
> Route: `/hr/requests-management`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `requests-management.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers centrally manage all employee requests (leave, overtime, business trips, corrections) in a unified approval queue
- **Core Functions**:
  - View all pending requests in one place
  - Filter by request type, department, employee, date
  - Bulk approve or reject requests
  - Add notes/comments to requests
  - Track request history and approval timeline
  - Generate request reports

### 1.2 Access Control

- **HR Admin**: Full access to all requests
- **Department Manager**: Team member requests only
- **Finance**: Overtime and business trip requests (budget tracking)

---

## 2. Screen Layout & Components

### 2.1 Main Screen Structure

```
┌──────────────────────────────────────────────────────┐
│ 요청 관리                                            │
│ 조직의 모든 직원 요청을 한곳에서 관리합니다        │
├──────────────────────────────────────────────────────┤
│ ┌───────────┐ ┌───────────┐ ┌───────────┐ ┌────────┐│
│ │ 휴가 요청 │ │ 연근무    │ │ 출장 요청 │ │ 정정   ││
│ │   12건    │ │   8건     │ │   5건     │ │  3건   ││
│ └───────────┘ └───────────┘ └───────────┘ └────────┘│
├──────────────────────────────────────────────────────┤
│ [검색] [요청 유형] [상태] [부서] [기간]             │
├──────────────────────────────────────────────────────┤
│ 요청 목록 (28건)                                     │
│ ┌────────────────────────────────────────────────┐  │
│ │ ☑ 직원명 | 요청 유형 | 내용 | 신청일 | 상태   │  │
│ │          |           |      |        | 작업    │  │
│ │──────────────────────────────────────────────│  │
│ │ ☑ 김철수 | 휴가 신청 | 연차 | 2/9 | [대기중] │  │
│ │ ☑ 이영희 | 연근무    | 2h30m | 2/9 | [대기중] │  │
│ └────────────────────────────────────────────────┘  │
├──────────────────────────────────────────────────────┤
│ [이전] 1 2 3 [다음]  15 items per page              │
└──────────────────────────────────────────────────────┘
```

---

## 3. Summary Statistics (Top Row)

### 3.1 Four-Card Summary

| Card | Type      | Count | Badge Color |
| ---- | --------- | ----- | ----------- |
| 1    | 휴가 요청 | 12건  | Blue        |
| 2    | 연장근무  | 8건   | Amber       |
| 3    | 출장 요청 | 5건   | Green       |
| 4    | 근태 정정 | 3건   | Purple      |

**Click Handler**: Filter table to show this type

---

## 4. Search & Filter Section

### 4.1 Search Bar

**Field**: Employee name or request ID  
**Placeholder**: "직원명 또는 요청 ID로 검색..."

### 4.2 Quick Filters

| Filter    | Type         | Options                                    |
| --------- | ------------ | ------------------------------------------ |
| 요청 유형 | Multi-select | Leave, Overtime, Business Trip, Correction |
| 상태      | Multi-select | Pending, Approved, Rejected                |
| 부서      | Dropdown     | IT, HR, Sales, etc.                        |
| 기간      | Date Range   | From ~ To dates                            |

### 4.3 Advanced Filters (Collapsible)

1. Request Type (multi-select)
2. Status (Pending, Approved, Rejected, Processed)
3. Department (multi-select)
4. Employee (search autocomplete)
5. Date Range (from ~ to)
6. Approver (dropdown)
7. Priority (High, Normal, Low)

---

## 5. Unified Requests Table

### 5.1 Column Definitions

| #   | Column    | Format     | Notes                                       |
| --- | --------- | ---------- | ------------------------------------------- |
| 1   | ☑         | Checkbox   | Multi-select                                |
| 2   | 직원명    | Name       | Link to employee                            |
| 3   | 요청 유형 | Badge      | 휴가/연근무/출장/정정                       |
| 4   | 내용      | Details    | Type-specific (e.g., "연차 3일" or "2h30m") |
| 5   | 신청일    | YYYY.MM.DD | Date submitted                              |
| 6   | 신청 기간 | Dates      | e.g., "2/9~2/11" or "2/9 22:00"             |
| 7   | 상태      | Badge      | Pending/Approved/Rejected                   |
| 8   | 작업      | Actions    | [승인] [반려] [상세]                        |

### 5.2 Row Color Coding

| Status        | Background             | Text   |
| ------------- | ---------------------- | ------ |
| Pending       | Light amber (amber-50) | Normal |
| Approved      | White                  | Normal |
| Rejected      | Light red (rose-50)    | Normal |
| High Priority | Light red (rose-100)   | Bold   |

### 5.3 Request Type Badge Styling

| Type      | Color  | Icon   |
| --------- | ------ | ------ |
| 휴가 신청 | Blue   | Leaf   |
| 연장근무  | Amber  | Clock  |
| 출장 신청 | Green  | MapPin |
| 근태 정정 | Purple | Edit   |

---

## 6. Request Actions

### 6.1 Inline Actions

| Action | Conditions | Modal                     |
| ------ | ---------- | ------------------------- |
| 승인   | Pending    | Confirm or add notes      |
| 반려   | Pending    | Required reason field     |
| 상세   | Any        | Full request detail modal |

### 6.2 Bulk Actions

**When records selected**:

```
☑ [5건 선택됨]
[✓ 일괄 승인] [✗ 일괄 반려] [📝 메모 추가] [✕ 취소]
```

---

## 7. Request Detail Modal

### 7.1 Dynamic Content (Type-Specific)

**Leave Request**:

```
┌─────────────────────────────┐
│ 휴가 신청 상세              │
├─────────────────────────────┤
│ 직원: 김철수               │
│ 휴가 유형: 연차            │
│ 신청 기간: 2026.02.09 ~ 2026.02.11 │
│ 신청 일수: 3일            │
│ 사유: 개인 사유            │
│ 잔여 휴가: 7일            │
├─────────────────────────────┤
│ 상태: [대기중]            │
│ 관리자 메모: [입력]        │
├─────────────────────────────┤
│ [닫기] [승인] [반려]       │
└─────────────────────────────┘
```

**Overtime Request**:

```
직원: 이영희
근무 날짜: 2026.02.08
근무 시간: 2h 30m (22:00~00:30)
사유: 프로젝트 마감
상태: [대기중]
```

**Business Trip Request**:

```
직원: 박민석
목적지: 서울
기간: 2026.02.09 ~ 2026.02.11 (3일)
사유: 고객 미팅
예산: ₩1,500,000
상태: [대기중]
```

---

## 8. Approval Workflow

### 8.1 Approve Button

**Steps**:

1. Click "승인"
2. Optional modal appears for notes
3. Confirm
4. PATCH API to update status
5. Toast: "승인되었습니다."
6. Table refreshes

### 8.2 Reject Button

**Steps**:

1. Click "반려"
2. Required reason modal (max 500 chars)
3. Confirm
4. PATCH API with rejection reason
5. Toast: "반려되었습니다."
6. Table refreshes

### 8.3 Batch Approval

**Steps**:

1. Select multiple requests
2. Click "일괄 승인"
3. Confirmation dialog
4. Batch PATCH API
5. Toast: "X건이 승인되었습니다."
6. Refresh affected rows

---

## 9. Data Model

```typescript
interface PendingRequest {
  id: number;
  employeeId: string;
  employeeName: string;
  department: string;
  requestType: "leave" | "overtime" | "business-trip" | "correction";
  requestId: number; // FK to specific request table
  content: string; // Display-friendly summary
  submittedAt: string; // YYYY-MM-DD
  status: "pending" | "approved" | "rejected";
  details: Record<string, any>; // Type-specific data
  approverNotes?: string;
  approverName?: string;
  approvedAt?: string;
  rejectionReason?: string;
  priority: "high" | "normal" | "low";
}

interface RequestFilter {
  requestType?: string[];
  status?: string[];
  departmentId?: string;
  employeeName?: string;
  dateFrom?: string;
  dateTo?: string;
  approverId?: string;
  page?: number;
  pageSize?: number;
}
```

---

## 10. API Requirements

| Method | Endpoint                                          | Response                                       |
| ------ | ------------------------------------------------- | ---------------------------------------------- |
| GET    | `/api/v1/hr/pending-requests?filters`             | `{ data: PendingRequest[], meta: Pagination }` |
| GET    | `/api/v1/hr/pending-requests/summary`             | `{ leave, overtime, trip, correction }`        |
| GET    | `/api/v1/hr/pending-requests/{requestId}`         | `{ data: PendingRequest }`                     |
| PATCH  | `/api/v1/hr/pending-requests/{requestId}/approve` | `{ success: true }`                            |
| PATCH  | `/api/v1/hr/pending-requests/{requestId}/reject`  | `{ success: true }`                            |
| POST   | `/api/v1/hr/pending-requests/batch-approve`       | `{ approved: [], errors: [] }`                 |
| POST   | `/api/v1/hr/pending-requests/export`              | `{ fileUrl: string }`                          |

---

## 11. Request History

### 11.1 Timeline View (Optional)

**Show approval timeline**:

- Submission time
- Assignment to approver
- Approver action (approve/reject)
- Any reassignments/escalations

---

## 12. Responsive Design

- **Desktop**: Full table with all columns
- **Tablet**: Hide optional columns
- **Mobile**: Card-based list view

---

## 13. Notifications

| Event                 | Message                          |
| --------------------- | -------------------------------- |
| New request submitted | "새로운 요청이 있습니다: {type}" |
| Batch processed       | "X건의 요청이 처리되었습니다."   |
| Request approved      | "요청이 승인되었습니다."         |
| Request rejected      | "요청이 반려되었습니다."         |

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
