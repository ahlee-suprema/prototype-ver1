# HR 외근/출장 신청 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `hr-business-trip-request`  
> Route: `/hr/business-trip-request` or `/hr/business-trip/request`  
> Parent Layout: `HR Admin Portal Layout`  
> Prototype File: `business-trip-request.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: HR Managers/Admins submit business trip requests on behalf of employees
- **Core Functions**:
  - Select employee for trip
  - Enter destination, dates, and purpose
  - Set trip budget
  - Attach supporting documents
  - Submit for approval

### 1.2 Access Control

- **HR Admin**: Submit on own behalf or any employee
- **HR Manager**: Submit on team members only
- **Finance**: Budget oversight

---

## 2. Screen Layout

### 2.1 Main Form Structure

```
┌────────────────────────────────────────────────┐
│ HR 외근/출장 신청                              │
│ HR 담당자가 직원 외근/출장을 신청합니다        │
├────────────────────────────────────────────────┤
│                                                 │
│ 신청 대상 직원 *                               │
│ [직원 선택 또는 검색...]                       │
│                                                 │
│ 외근/출장 유형 *                               │
│ ○ 외근 (당일)  ○ 출장 (숙박)  ○ 회의         │
│                                                 │
│ 목적지 *                                        │
│ [도시명 또는 장소]                             │
│ (검색 및 자동완성)                            │
│                                                 │
│ 기간 *                                          │
│ [시작 날짜] ~ [종료 날짜]                      │
│                                                 │
│ ┌──────────────────────────────────────────┐   │
│ │ 예상 기간: 3일                           │   │
│ │ (2026-02-09 ~ 2026-02-11)                │   │
│ └──────────────────────────────────────────┘   │
│                                                 │
│ 신청 사유 *                                     │
│ [텍스트 입력 필드]                            │
│ (고객 방문, 회의, 협력사 방문, 등)            │
│                                                 │
│ 출장 세부사항 (선택사항)                       │
│ 교통수단: [드롭다운] 항공/기차/자동차/기타   │
│ 숙박 필요: ○ 예  ○ 아니오                    │
│ 숙박명: [텍스트]                              │
│                                                 │
│ 예상 예산 *                                     │
│ [금액 입력]                                    │
│ (기본값: 출장지별 표준 예산)                  │
│                                                 │
│ 첨부 문서 (선택사항)                           │
│ [파일 업로드 또는 드래그 드롭]                │
│ (항공권, 호텔 선택지, 등)                     │
│                                                 │
│ 관리자 메모 (선택사항)                        │
│ [텍스트 입력 필드]                            │
│                                                 │
│ [취소] [신청하기]                             │
│                                                 │
└────────────────────────────────────────────────┘
```

---

## 3. Form Fields

### 3.1 Employee Selector

**Field Name**: `employeeId`  
**Type**: Searchable dropdown/autocomplete  
**Required**: Yes

---

### 3.2 Trip Type

**Field Name**: `tripType`  
**Type**: Radio buttons  
**Required**: Yes

**Options**:
| Value | Label | Description |
|-------|-------|-------------|
| external-work | 외근 (당일) | Same-day external work |
| business-trip | 출장 (숙박) | Multi-day trip with accommodation |
| conference | 회의 | Conference attendance |

---

### 3.3 Destination

**Field Name**: `destination`  
**Type**: Text input with autocomplete  
**Required**: Yes

**Features**:

- Autocomplete from recent destinations
- Suggest common trip locations
- Display city/country format
- Timezone info if international

---

### 3.4 Trip Dates

**Fields**: `startDate`, `endDate`  
**Type**: Date inputs  
**Required**: Yes  
**Format**: YYYY-MM-DD

**Duration Display**:

```
예상 기간: 3일
(2026-02-09 (금) ~ 2026-02-11 (일))
```

**Validation**:

- Both dates required
- End ≥ start
- Max 30 days
- Cannot overlap existing approved trips (warning)

---

### 3.5 Trip Reason

**Field Name**: `reason`  
**Type**: Textarea  
**Required**: Yes  
**Max**: 300 characters

**Placeholder**: "출장 목적을 입력해주세요"

---

### 3.6 Trip Details (Optional Section)

**Transport Type**:

```
교통수단: [드롭다운]
  - 항공기 (비행기)
  - 기차
  - 자동차
  - 버스
  - 기타
```

**Accommodation**:

```
숙박 필요: ○ 예  ○ 아니오
숙박 기간: [자동계산]
숙박명 (선택사항): [텍스트]
```

---

### 3.7 Budget

**Field Name**: `allocatedBudget`  
**Type**: Currency input (KRW)  
**Required**: Yes

**Features**:

- Default budget based on destination
- Show budget guidelines for destination
- Allow override with notes
- Warning if exceeds threshold

**Budget Tiers** (example):

- Domestic (서울, 대구, 부산, 등): ₩1,000,000 default
- International (중국, 일본, 미국, 등): Based on country

---

### 3.8 Attachments

**Field Name**: `attachments`  
**Type**: File upload  
**Required**: No  
**Max Size**: 5MB per file

**Allowed Types**: PDF, JPEG, PNG, XLSX, DOC

**Usage**:

- Airline ticket confirmations
- Hotel reservations
- Invitation letters
- Approval documents

---

### 3.9 Manager Notes

**Field Name**: `managerNotes`  
**Type**: Textarea  
**Required**: No  
**Max**: 500 characters

---

## 4. Data Model

```typescript
interface HRBusinessTripRequest extends BusinessTripRequest {
  submittedByHRId: string; // HR user
  submittedForEmployeeId: string; // Employee
  submittedOnBehalfOf: boolean;
  hrNotes?: string;
  managerApprovalNotes?: string;
  budgetAllocated: number; // KRW
  budgetTier?: string; // Reference tier
}
```

---

## 5. API Requirements

| Method | Endpoint                                                      | Request                  | Response                    |
| ------ | ------------------------------------------------------------- | ------------------------ | --------------------------- |
| GET    | `/api/v1/hr/employees?search={text}`                          | N/A                      | `{ data: Employee[] }`      |
| GET    | `/api/v1/business-trips/budget-guidelines?destination={dest}` | N/A                      | `{ defaultBudget, tier }`   |
| POST   | `/api/v1/hr/business-trips`                                   | Form data + file uploads | `{ id, status: 'pending' }` |

---

## 6. Validation Rules

| Field       | Rule                | Error Message                      |
| ----------- | ------------------- | ---------------------------------- |
| Employee    | Required, active    | "직원을 선택해주세요."             |
| Trip type   | Required            | "외근/출장 유형을 선택해주세요."   |
| Destination | Required, not empty | "목적지를 입력해주세요."           |
| Start date  | Required, future    | "시작 날짜를 선택해주세요."        |
| End date    | Required, ≥ start   | "종료 날짜를 선택해주세요."        |
| Date range  | Max 30 days         | "최대 30일까지만 신청 가능합니다." |
| Reason      | Required            | "신청 사유를 입력해주세요."        |
| Budget      | Required, > 0       | "예상 예산을 입력해주세요."        |

---

## 7. Workflows

### 7.1 HR Submitting Trip for Employee

1. HR navigates to `/hr/business-trip-request`
2. Searches and selects employee
3. Selects trip type (출장)
4. Enters destination (with autocomplete)
5. Picks start/end dates
6. System shows duration (3 days)
7. Enters trip reason
8. Selects transport type
9. Confirms accommodation need
10. System suggests default budget (₩1,500,000)
11. Uploads flight ticket and hotel reservation
12. Adds manager notes (optional)
13. Clicks "신청하기"
14. Request created
15. Toast: "출장 신청이 완료되었습니다. (사용자: 김철수)"

### 7.2 Budget Approval Flow

**If budget exceeds threshold**:

1. Show warning: "예산이 가이드라인을 초과합니다."
2. Allow override with reason
3. Flag for Finance approval
4. Send notification to Finance team

---

## 8. Responsive Design

- **Desktop**: Full form with side columns
- **Tablet**: Stacked sections
- **Mobile**: Vertical stacked form

---

## 9. Toast Messages

| Event            | Message                           |
| ---------------- | --------------------------------- |
| Submit success   | "출장 신청이 완료되었습니다."     |
| Submit error     | "신청 중 오류가 발생했습니다."    |
| Budget warning   | "예산이 가이드라인을 초과합니다." |
| File upload fail | "파일 업로드에 실패했습니다."     |

---

## 10. Accessibility

- Form labels with proper association
- Required fields marked with asterisk and aria-required
- Error messages linked via aria-describedby
- Keyboard navigation through all controls
- Screen reader announces duration calculation

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development

**Implementation Note**: This form can reuse business trip logic from emp-business-trip-management with addition of employee selector. Consider abstraction for both employee and HR versions.
