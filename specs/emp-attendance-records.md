# 직원 출퇴근 기록 화면 명세

> Document Version: 0.2  
> Last Updated: 2026-02-09  
> Screen ID: `emp-attendance-records`  
> Route: `/emp/attendance`  
> Parent Layout: `Employee Portal Layout`  
> Prototype File: `emp-attendance-records.html`

---

## 1. Screen Overview

### 1.1 Purpose

- **Primary Goal**: Employees view their historical attendance records and request attendance corrections
- **Core Functions**:
  - View daily attendance history (check-in/out times)
  - Filter attendance records by date range
  - View attendance status (On-time, Late, Early leave, etc.)
  - Submit attendance correction requests
  - Request correction for missing check-in/out records

### 1.2 Access Control

| Role     | Access | Scope            | Notes                                   |
| -------- | ------ | ---------------- | --------------------------------------- |
| Employee | Full   | Own records only | Cannot view other employees' attendance |
| Manager  | View   | Team members     | Can see team attendance for management  |
| HR Admin | Full   | All employees    | Full audit access                       |

### 1.3 Entry Points

- **Sidebar**: Navigation menu → "출퇴근 기록" (Clock icon)
- **Dashboard**: Attendance card or quick link
- **URL Direct**: `/emp/attendance`

### 1.4 Exit Points

- **Sidebar**: Navigate to other menu items
- **Back Button**: Return to dashboard or previous page

---

## 2. Screen Layout

### 2.1 Layout Overview

```
[Header & Title]
[Date Range Filter]
[Summary Stats Cards - Daily breakdown]
[Attendance Records Table with Correction Request modals]
[Pagination]
```

---

## 3. Core Sections

### 3.1 Date Range Filter

- Quick select buttons: Today, This Week, This Month, This Year
- Date picker: From ~ To (custom range)
- Default: Current month

### 3.2 Summary Stats (Grid of Cards)

| Card        | Data                          | Purpose        |
| ----------- | ----------------------------- | -------------- |
| Total Days  | Number of work days in period | Overview       |
| On-time     | Days arrived before/on time   | Performance    |
| Late        | Days arrived after start time | Performance    |
| Early Leave | Days left before end time     | Performance    |
| Absences    | Days not recorded             | Issue tracking |

### 3.3 Attendance Records Table

**Columns**:
| # | Column | Content | Format |
|---|--------|---------|--------|
| 1 | Date | Date of record | YYYY.MM.DD |
| 2 | Day of Week | Day name | Mon, Tue, etc. |
| 3 | Check-in | Arrival time | HH:MM |
| 4 | Check-out | Departure time | HH:MM |
| 5 | Work Hours | Duration | X hours Y mins |
| 6 | Status | On-time/Late/Early | Badge color |
| 7 | Actions | Correct/View details | Buttons |

**Status Badges**:

- **On-time** (Green): Checked in by start time
- **Late** (Red): Checked in after start time
- **Early Leave** (Yellow): Checked out before end time
- **Absent** (Gray): No records
- **Holiday** (Slate): Company holiday/weekend

### 3.4 Correction Request Modal

**Trigger**: Click "Correct" button in action column

**Fields**:

- Issue Type: Missing check-in / Missing check-out / Wrong time
- Explanation: Text field for reason
- Actual Time: (If applicable)
- Attachments: File upload for proof

**Actions**:

- Submit: Send to HR for review
- Cancel: Close modal

---

## 4. Key Features

### 4.1 Daily Detail View

Clicking a row expands to show:

- Detailed timeline of activity
- Multiple check-in attempts (if any)
- Manual time adjustments (if any)
- Notes/comments

### 4.2 Correction Submission

Modal form with:

- Reason dropdown
- Text explanation
- File attachment
- Estimated actual time

### 4.3 Correction Status

Show status of submitted corrections:

- **Pending**: Waiting for HR review
- **Approved**: Accepted, time adjusted
- **Rejected**: Denied, reason provided

---

## 5. Data Model

```typescript
interface AttendanceRecord {
  date: string; // YYYY-MM-DD
  dayOfWeek: string; // Mon, Tue, Wed, etc.
  checkInTime?: string; // HH:MM
  checkOutTime?: string; // HH:MM
  workHours?: number; // Minutes or decimal hours
  status: "on-time" | "late" | "early-leave" | "absent" | "holiday";
  isWorkday: boolean;
  notes?: string;
}

interface CorrectionRequest {
  id: number;
  recordDate: string;
  issueType: "missing-checkin" | "missing-checkout" | "wrong-time";
  reason: string;
  actualTime?: string;
  status: "pending" | "approved" | "rejected";
  submittedAt: string;
  reviewedAt?: string;
  reviewerNotes?: string;
}
```

---

## 6. API Requirements

| #   | Method | Endpoint                                                 | Response                                                   |
| --- | ------ | -------------------------------------------------------- | ---------------------------------------------------------- |
| 1   | GET    | `/api/v1/employees/{empId}/attendance?startDate&endDate` | `{ data: AttendanceRecord[], summary: AttendanceSummary }` |
| 2   | POST   | `/api/v1/employees/{empId}/correction-requests`          | `{ id, status: 'pending' }`                                |
| 3   | GET    | `/api/v1/employees/{empId}/correction-requests`          | `{ data: CorrectionRequest[] }`                            |

---

## 7. Responsive Design

- Desktop: Full table view
- Tablet: Horizontal scroll or card view
- Mobile: Card-based timeline view

---

**Document Version**: 0.2  
**Status**: Specification Complete - Ready for Frontend Development
