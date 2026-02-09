# 📋 Specification Package - Quick Reference

## 🎯 What's Been Delivered

**17 Complete Screen Specifications** for the Suprema T&A Management System:

### Employee Portal (6 Screens) ✅

| #   | Screen              | File                                                               | Details                                    |
| --- | ------------------- | ------------------------------------------------------------------ | ------------------------------------------ |
| 1   | Employee Dashboard  | [emp-dashboard.md](emp-dashboard.md)                               | Today's status, quick stats, announcements |
| 2   | Attendance Records  | [emp-attendance-records.md](emp-attendance-records.md)             | View history, submit corrections           |
| 3   | Leave Management    | [emp-leave-management.md](emp-leave-management.md)                 | Balance tracking, usage history            |
| 4   | Leave Request Form  | [emp-leave-request.md](emp-leave-request.md)                       | Submit new requests with validation        |
| 5   | Overtime Management | [emp-overtime-management.md](emp-overtime-management.md)           | Track balance, view requests               |
| 6   | Business Trip Mgmt  | [emp-business-trip-management.md](emp-business-trip-management.md) | Submit and track trips                     |

### HR Admin Portal - Operations (11 Screens) ✅

| #   | Screen                | File                                                             | Details                               |
| --- | --------------------- | ---------------------------------------------------------------- | ------------------------------------- |
| 1   | HR Dashboard          | [hr-dashboard.md](hr-dashboard.md)                               | Overview, pending queue, dept stats   |
| 2   | Attendance Records    | [hr-attendance-records.md](hr-attendance-records.md)             | Org-wide view, corrections, export    |
| 3   | Leave Management      | [leave-management.md](leave-management.md)                       | Approval queue, filters, batch ops    |
| 4   | Leave Request Form    | [hr-leave-request.md](hr-leave-request.md)                       | Submit on behalf of employees         |
| 5   | Overtime Management   | [hr-overtime-management.md](hr-overtime-management.md)           | Approval, tracking, compensation mgmt |
| 6   | Overtime Request Form | [hr-overtime-request.md](hr-overtime-request.md)                 | Submit on behalf of employees         |
| 7   | Business Trip Mgmt    | [hr-business-trip-management.md](hr-business-trip-management.md) | Approvals, budget, expenses           |
| 8   | Business Trip Form    | [hr-business-trip-request.md](hr-business-trip-request.md)       | Submit trips on behalf                |
| 9   | Team Management       | [hr-team-management.md](hr-team-management.md)                   | Employee master data, import/export   |
| 10  | Schedule Management   | [hr-schedule.md](hr-schedule.md)                                 | Shift planning, coverage tracking     |
| 11  | Unified Requests      | [hr-requests-management.md](hr-requests-management.md)           | Central approval queue                |

### Master Registry

- [INDEX.md](INDEX.md) - Master index with navigation map, shared components, color system
- [COMPLETION_SUMMARY.md](COMPLETION_SUMMARY.md) - This delivery summary

---

## 📊 What Each Specification Includes

Every specification document contains:

✅ **1. Screen Overview**

- Purpose and goals
- Access control / Authorization

✅ **2. Screen Layout**

- Main structure with ASCII wireframes
- Visual layout breakdown

✅ **3. Components Specification**

- Column definitions for tables
- Form fields and inputs
- Status badges and indicators
- Modals and overlays

✅ **4. Data Model**

- TypeScript interfaces
- Field types and constraints
- Optional/Required marking

✅ **5. API Requirements**

- Endpoints (GET/POST/PATCH/DELETE)
- Request/Response formats
- Query parameters

✅ **6. Validation Rules**

- Field-level validation
- Business logic validation
- Error messages (in Korean)

✅ **7. User Workflows**

- Step-by-step interactions
- Success and error paths

✅ **8. Edge Cases**

- Boundary conditions
- Error scenarios
- Unusual situations

✅ **9. Responsive Design**

- Desktop layout
- Tablet layout
- Mobile layout

✅ **10. Accessibility**

- ARIA attributes
- Keyboard navigation
- Screen reader support

✅ **11. Toast/Notifications**

- Success messages
- Error messages
- Info/Warning messages

✅ **12. Additional Details**

- Search & filter specifications
- Bulk operations
- Export functionality
- Customization options

---

## 🚀 How to Use These Specifications

### For Frontend Developers

1. **Start Here**: Review [INDEX.md](INDEX.md) for system overview
2. **Review Employee Portal First**: 6 screens for employee-facing features
3. **Then HR Portal**: 11 screens for administrative features
4. **For Each Screen**:
   - Read Layout section (understand structure)
   - Review Components section (implement UI)
   - Check Data Model section (define interfaces)
   - Study Validation section (add error handling)
   - Implement Toast/Notifications

### For Backend Developers

1. **Start Here**: Review [INDEX.md](INDEX.md) for API patterns
2. **For Each Screen**:
   - Extract API endpoints from "API Requirements" section
   - Implement request/response structures matching Data Model
   - Add validation per "Validation Rules" section
   - Return proper HTTP status codes
   - Include error messages matching toast messages

### For QA/Testers

1. **Create Test Cases**:
   - One test per validation rule
   - One test per edge case
   - One test per toast message scenario
2. **Test Across Devices**:
   - Desktop (≥1280px)
   - Tablet (768-1279px)
   - Mobile (<768px)
3. **Accessibility Testing**:
   - Keyboard navigation (Tab, Enter, Arrow keys)
   - Screen reader (NVDA, JAWS, or browser built-in)
   - Color contrast validation

---

## 🔑 Key Concepts

### Authentication & Authorization

- **Employee**: Can only access own portal and data
- **HR Manager**: Can access team data + submit on behalf
- **HR Admin**: Full access to all organization data
- **Finance**: Can approve business trip budgets

### Date Format Convention

- **UI Display**: YYYY.MM.DD (e.g., 2026.02.09)
- **API Transfer**: YYYY-MM-DD (ISO 8601 format)

### Pagination Standard

- **Default**: 15 items per page
- **Options**: [15, 30, 50] items per page
- **Navigation**: Previous/Next buttons + page numbers (max 5 visible)

### Filter Logic

- **Multiple filters**: AND logic (e.g., Department=IT AND Status=Pending)
- **Multiple values in same filter**: OR logic (e.g., Status IN [Pending, Approved])

### Color Coding

**Leave Types**:

- 연차 (Annual) = Blue
- 병가 (Sick) = Amber
- 가정 (Family) = Rose/Pink
- 산모 (Maternity) = Purple
- 무급 (Unpaid) = Slate/Gray
- 공식 (Official) = Green

**Request Status**:

- 대기중 (Pending) = Amber
- 승인 (Approved) = Green
- 반려 (Rejected) = Red
- 완료/지급 (Completed/Paid) = Blue

---

## 💡 Implementation Tips

### React Component Structure

Suggested component reuse:

```
Shared/
  ├── Sidebar (all HR screens)
  ├── Header (all screens)
  ├── DataTable (all management screens)
  ├── DateRangePicker (filters, forms)
  ├── StatusBadge (all status displays)
  ├── Modal (confirmations, details)
  └── Toast (notifications)

Employee/
  ├── Dashboard
  ├── Attendance
  ├── Leave (management + request)
  ├── Overtime (management + form)
  └── BusinessTrip (management + form)

Admin/
  ├── Dashboard
  ├── Attendance (management + records)
  ├── Leave (management + request)
  ├── Overtime (management + request)
  ├── BusinessTrip (management + request)
  ├── Team Management
  ├── Schedule
  └── Requests (unified queue)
```

### API Endpoint Pattern

All endpoints follow REST conventions:

```
GET    /api/v1/{resource}              # List (with filters)
POST   /api/v1/{resource}              # Create
GET    /api/v1/{resource}/{id}         # Read
PATCH  /api/v1/{resource}/{id}         # Update
DELETE /api/v1/{resource}/{id}         # Delete

Query params for filtering:
  ?page=1&pageSize=15
  ?dateFrom=2026-02-01&dateTo=2026-02-28
  ?status=pending&status=approved
  ?search=kim
```

### Error Handling

Standard error response:

```json
{
  "error": true,
  "message": "Error message in Korean",
  "details": { "field": "error details" },
  "statusCode": 400
}
```

### Toast Message Pattern

All screens include success/error/warning toasts:

- **Success**: Green bg, checkmark icon, 3s duration
- **Error**: Red bg, X icon, 4s duration
- **Warning**: Amber bg, exclamation icon, 4s duration
- **Info**: Blue bg, info icon, 3s duration

---

## 🆘 Troubleshooting

**Q: Which screen should I start with?**  
A: Start with Employee Dashboard (emp-dashboard.md) - it's the landing page and simplest screen.

**Q: How do I handle date conversions?**  
A: UI uses YYYY.MM.DD format. When sending to API, convert to YYYY-MM-DD. When receiving from API, convert to YYYY.MM.DD.

**Q: What about the deferred screens (Reports, Settings)?**  
A: These are not in scope for Phase 1. Focus on the 17 operational screens first. Reports and Settings can be implemented later.

**Q: How do I test mobile responsiveness?**  
A: Use DevTools device simulation or build responsive components that adapt to:

- Mobile: <768px (single column, stacked)
- Tablet: 768-1279px (2 columns)
- Desktop: ≥1280px (full layout)

**Q: Which screens share similar patterns?**  
A:

- All management screens (leave-mgmt, overtime-mgmt, etc.) follow same pattern: filters → table → modals
- All request forms (leave-request, overtime-request, etc.) follow same pattern: fields → validation → submit
- All approval screens follow same pattern: summary → queue → bulk actions

---

## 📞 Quick Links

| Document                                               | Purpose                                            |
| ------------------------------------------------------ | -------------------------------------------------- |
| [INDEX.md](INDEX.md)                                   | System overview, navigation map, shared components |
| [emp-dashboard.md](emp-dashboard.md)                   | Employee landing page                              |
| [hr-dashboard.md](hr-dashboard.md)                     | HR landing page                                    |
| [hr-requests-management.md](hr-requests-management.md) | Central approval queue                             |
| [hr-team-management.md](hr-team-management.md)         | Employee master data                               |
| [COMPLETION_SUMMARY.md](COMPLETION_SUMMARY.md)         | Delivery summary & metrics                         |

---

**Ready to build? Start with emp-dashboard.md! 🚀**

Last Updated: 2026-02-09  
Status: ✅ Ready for Development
