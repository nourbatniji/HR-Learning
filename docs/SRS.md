# HR Learning System — Software Requirements Specification

Version 0.1 · 20 October 2026 · Review draft

## 1. Purpose and delivery scope

Build a small HR website for one company while learning Next.js and NestJS. Deliver incrementally: explain the approach, affected files, request/data flow, and verification before each feature; pause after each small change for the learner to test and modify it.

The first functional milestone is a database-backed employee list and add-employee form. The three-week target includes the scope below. The target is a planning estimate, not a guaranteed completion date.

## 2. Users and permissions

There are exactly two roles: Admin/HR and Employee. Admin and HR are the same role.

| Capability | Admin/HR | Employee |
|---|---|---|
| Company settings and job titles | Manage | No access |
| Employee records and salary | Manage all | Read permitted own fields |
| Contract PDF | Upload/replace/read | Proposed: read own only |
| Attendance | Read all; correct with reason | Check in/out; read own |
| Leave | Configure types and notice; decide requests | Submit and read own requests |
| Interview records | Manage | No access |
| Adjustments and payroll | Review, approve, finalize, mark paid | Proposed: read own finalized payslip |

The server enforces permissions on every request, including file downloads. Hiding UI elements is insufficient. Employee self-editing is not included. Proposed visibility choices must be confirmed before implementation.

## 3. Functional requirements

### FR-01 Accounts

Provide login and logout. Accounts are created by Admin/HR; no public registration. Inactive employee accounts cannot access the application. Passwords must be hashed and authenticated access must expire. Exact account creation/reset flow is decided before that feature; no email service is assumed.

### FR-02 Employee records

Admin/HR can list, create, view, edit and deactivate employees. Deactivation preserves attendance and payroll history. Initial list displays employee number, name, job title, joining date and status. Creating a record saves it in PostgreSQL and makes it visible after refresh.

Proposed fields below are a practical inventory, not a requirement to collect every sensitive value:

| Group | Fields | Proposed required fields |
|---|---|---|
| Identity | Unique employee number, full name; optional date of birth, nationality, national ID/passport number | Employee number, full name |
| Contact | Work/login email, personal email, phone, address | Login email when account is created |
| Emergency | Contact name, relationship, phone | None |
| Employment | Job title, joining date, employment type, active/inactive status, leaving date | Job title, joining date, status |
| Contract | Start date, optional end date, optional private PDF | None initially |
| Pay | Base monthly salary, optional fixed allowance | Salary before payroll inclusion |

Reject duplicate employee numbers and account emails, invalid dates, negative salary and invalid required fields. Store money with decimal precision rather than floating-point arithmetic. Do not expose salary, identification or emergency details in employee directory responses.

### FR-03 Job titles

Admin/HR manages a simple job-title list and assigns a title to employees and candidates. A title in use cannot be deleted without reassignment. No departments, organizational charts or reporting hierarchy.

### FR-04 Contract PDF

Admin/HR uploads, views, replaces and downloads one current contract PDF per employee. Store file metadata and a private file reference; downloads require authorization. Validate actual PDF content as well as extension and apply a size limit (proposed: 10 MB). A failed replacement preserves the current contract. Do not serve contracts from a public assets directory. No electronic signing, OCR, other document types or contract renewal alerts.

### FR-05 Company settings

Admin/HR configures company name, timezone, currency, common working days, start/end time, leave types and minimum leave notice. All employees follow the same work schedule. Settings also include the agreed attendance thresholds and monthly payday rule. Arabic/English display does not change stored amounts or timestamps.

Proposed first-version simplification: one daytime schedule, no rotating or overnight shifts. Confirm this before attendance implementation. Historical attendance uses the schedule applicable on that date; changing settings must not silently relabel past records.

### FR-06 Attendance

Authenticated employees explicitly check in and check out. Website login alone does not record attendance. Use server time and company timezone; clients cannot supply authoritative punch times. Prevent duplicate check-ins/check-outs and check-out before check-in. One attendance record per scheduled workday is proposed for version one.

Admin/HR sees attendance by date/employee and corrects mistakes with a reason. Keep original values and correction actor/time. Identify late arrival, early departure, extra time, absence after a scheduled day ends, and missing check-out. Approved leave and non-working days must not produce false absence suggestions. Missing check-out requires correction, not guessed hours. Device integration is future scope.

### FR-07 Leave

Admin/HR configures leave types and the minimum advance-notice period. An employee submits leave type, start date, end date and reason. Requests start Pending; Admin/HR accepts or declines; the employee sees the result. Record decision actor/time and optional decision note.

The server rejects requests submitted after the configured deadline, invalid date ranges and overlapping pending/approved requests. Proposed notice calculation: calendar days between company-local submission date and leave start date; equality with the minimum is allowed. Confirm this boundary and counting rule before implementation.

Annual balance tracking was not explicitly confirmed. Do not silently implement accrual, carry-over or statutory leave rules. Balance enforcement, paid/unpaid behavior and handling leave overlapping finalized payroll are decisions required before leave/payroll integration.

### FR-08 Interview tracking

Admin/HR records candidates already interviewed: name, phone/email, target job title, interview date, interviewer name, notes and status. Proposed statuses: Pending decision, Accepted, Rejected. Admin/HR can list, view and update records. No public vacancies, application intake, candidate accounts or CV upload. Accepted candidates are not automatically converted to employees.

### FR-09 Attendance adjustment suggestions

Attendance rules identify possible bonuses/deductions and show Admin/HR the employee, date, event and supporting minutes. They are review prompts, not automatic salary changes. Admin/HR approves, changes or rejects a suggestion, recording a reason and amount. Prevent duplicate suggestions and double application to payroll. A corrected attendance record must invalidate or refresh an unapproved suggestion; payroll must flag previously approved adjustments affected by a correction for review.

Grace periods, extra-time thresholds and amount formulas are not yet agreed. Until agreed, display measured attendance facts and let Admin/HR enter the proposed adjustment amount. No AI, legal-rule engine, automatic penalties or invented rates.

### FR-10 Monthly payroll

Admin/HR creates a monthly payroll draft. Each employee line snapshots base salary, allowance, approved bonuses and approved deductions for that month.

Net pay = base monthly salary + fixed allowances + approved bonuses − approved deductions.

Use one company currency. Recalculating a draft must not duplicate adjustments. Show unresolved attendance/adjustment issues before finalization. Finalization requires deliberate confirmation and stores a stable snapshot; future salary edits cannot alter finalized months. Prevent duplicate finalized payroll for the same employee/month. Admin/HR marks each payment Paid and records its date; the system does not transfer money.

Proposed first-version policy: no automatic proration or leave deductions. Admin/HR must review joiners/leavers and enter an explicit explained adjustment before finalizing a partial month. Confirm this policy and the negative-net-pay behavior before implementation. No tax or insurance calculations, multiple salary structures, accounting entries or bank integration.

### FR-11 Notifications and payslips

Provide a small in-app pending-action list: leave requests awaiting decisions, attendance adjustment suggestions and monthly payroll due. Proposed implementation computes due state when Admin/HR opens the application, including overdue months; it does not require background workers, email, SMS or push delivery. Confirm payday (month end versus a configured day) before implementation.

Provide a printable payslip page showing company, employee, month, salary components, net pay and payment status. Proposed employee access: own finalized payslips only. Browser printing is sufficient; generated PDF files are not required.

### FR-12 English and Arabic

Provide a language switch, English left-to-right and Arabic right-to-left layouts. Translate navigation, labels, validation messages, statuses and payslip labels. Preserve user-entered names and notes without automatic translation. Both languages use the same data and workflows. Persist the language preference locally for version one.

## 4. Data and request flow

Use one Next.js frontend, one NestJS backend and one PostgreSQL database in one repository. NestJS owns validation, permissions, business rules and database writes. Choose one database library before adding persistence; no extra repository abstraction is required. Keep contract files in private persistent storage suitable for the chosen host.

Examples:

- Employee create: Next.js form → authenticated NestJS endpoint → validate fields → database insert → response → refreshed list.
- Leave: employee form → server checks ownership/settings/deadline → Pending record → admin decision → employee request status.
- Payroll: attendance facts → suggestions → admin decisions → draft calculation → finalized snapshot → payment status/payslip.

Expected records: user, employee, job title, company settings, contract metadata, attendance, leave type/request, interview record, adjustment and payroll/monthly employee lines. These are a data inventory, not instructions to generate every table upfront.

## 5. Quality and acceptance

- Validate inputs on the server and show useful errors in the selected language.
- Prevent one employee reading another employee's profile, salary, attendance, leave or contract by changing an ID.
- Keep secrets outside source control; use HTTPS on deployment and persistent database/file storage.
- Use transactions/constraints where needed for duplicate punches, payroll finalization and adjustments.
- Verify payroll arithmetic, leave deadline boundaries and authorization with meaningful tests.
- Check English/Arabic layouts at desktop and mobile widths; forms need labels and keyboard access.
- Before release, prove data survives an application restart and perform a basic backup/restore check for database and contracts.

Release scenario: Admin/HR adds an employee, assigns a title, saves a salary and contract; employee logs in, views permitted own data, punches attendance and requests eligible leave; admin decides leave and an attendance adjustment, finalizes payroll and marks payment; employee reads the own payslip. Unauthorized cross-employee requests fail. Repeat key flows in Arabic.

## 6. Open decisions and proposed defaults

Resolve each before its implementation; they do not block the employee-list milestone.

| Decision | Proposed first-version default |
|---|---|
| Sensitive profile fields / employee visibility | Optional sensitive fields; own salary, contract and finalized payslip visible |
| Working schedule | Same daytime hours and working weekdays for everyone |
| Leave notice | Whole calendar days; minimum boundary accepted |
| Leave balances and paid/unpaid rules | Needs user decision; no hidden automatic balance/payroll rules |
| Adjustment thresholds and amounts | Configured thresholds; admin enters amount until formula agreed |
| Partial-month salary | Explicit admin-reviewed adjustment; no automatic proration |
| Payroll due date / negative net | Month-end due prompt proposed; negative-net handling needs decision |
| File size / database library / hosting | 10 MB PDF proposed; choose library and host when needed |

## 7. Three-week plan

Three relative weeks from the agreed start date; no calendar start date assumed. Budget 150 planned focused hours, plus up to 30 hours for review/fixes within 18 working days × 10 hours, leaving one rest day per week. Learner testing and explanations are included in task estimates. This is an estimate, not a promise.

- Week 1 (50 hours): requirements decisions, minimal setup, first employee milestone, account permissions, complete profile, job titles and contracts.
- Week 2 (50 hours): common schedule, attendance, leave, interview tracking and adjustment review.
- Week 3 (50 hours): monthly payroll, pending-action notifications, printable payslips, bilingual verification and release testing.

Deliver vertical slices that include UI/API/database behavior. English/Arabic labels are included as features are built; week three checks completeness. No code is implemented as part of preparing this specification.

## 8. Explicitly outside this release

Multiple companies/tenants, separate HR permissions, departments, org charts, rotating/night shifts (proposed simplification), barcode/fingerprint devices, recruitment portals, resume uploads, automatic hiring, automatic contract reminders, accounting integration, bank transfers, tax/insurance calculation, AI/compliance tools, elaborate dashboards, microservices, queues and extensive infrastructure. New needs must be discussed before expanding scope.
