# Three-week Jira backlog

Review draft · 20 October 2026

150 planned focused hours (50/week), plus 30 hours available for changes and unexpected fixes across 18 working days at 10 hours/day. Relative weeks begin on the agreed start date. Estimates include explanation, small changes and learner testing.

Each ticket is a vertical slice where applicable. Before coding: explain approach, affected files and request/data flow. After each small change: show verification and pause for learner review. Translation labels belong in each feature. Do not infer completion from older Jira work.

Existing Jira project: HR-System (SCRUM). It contains a larger prior backlog with completed work. The separate learning-plan epic SCRUM-177 and 24 tasks SCRUM-178 through SCRUM-201 have been created. Existing issues are unchanged. See JIRA-LINKS.md for mappings.

## Week 1 — 50 hours

### L01 · Review the SRS and settle first-milestone fields (4h)

Requirements: FR-02, FR-12. Dependencies: None.

Acceptance criteria:

- Agree required employee fields and initial list columns.
- Review proposed defaults; record unresolved leave/payroll decisions without inventing rules.
- Confirm three-week scope and checkpoint workflow.

### L02 · Create the minimal Next.js, NestJS and database setup (5h)

Requirements: Section 4. Dependencies: L01.

Acceptance criteria:

- Frontend and API run locally with one PostgreSQL database.
- Discuss and select one database library; verify one database connection/migration.
- Use frontend/ and backend/ folders; add only required setup and language support.

### L03 · Build employee creation and listing API (6h)

Requirements: FR-02. Dependencies: L02.

Acceptance criteria:

- A migration defines agreed initial employee fields.
- Create and list endpoints persist real records; duplicate number and invalid required fields fail.
- Describe request/validation/database/response flow. Local development checkpoint only until permissions task is done.

### L04 · Build employee list and add-employee form — first checkpoint (6h)

Requirements: FR-02, FR-12. Dependencies: L03.

Acceptance criteria:

- English/Arabic form saves to the API and list updates.
- Refreshing retains data; validation, loading, empty and error states are visible.
- Learner tests and modifies the slice before continuing; no public deployment yet.

### L05 · Add login, logout and the two-role permissions (8h)

Requirements: FR-01, Section 2. Dependencies: L04.

Acceptance criteria:

- Admin/HR and Employee are the only roles; admin creates accounts without public signup.
- Use hashed passwords and expiring authenticated access; deactivate access for inactive users.
- Employee cannot access another employee or admin endpoints; protect existing list/create endpoints.
- Verify login/logout and server-side authorization.

### L06 · Manage job titles and complete employee profiles (6h)

Requirements: FR-02, FR-03. Dependencies: L05.

Acceptance criteria:

- Admin creates/edits job titles; referenced titles cannot be deleted without reassignment.
- Add agreed optional profile/emergency/employment/salary fields and edit/deactivate actions.
- Employee sees only agreed own fields; no employee editing or department structure.

### L07 · Upload and privately download contract PDFs (7h)

Requirements: FR-04. Dependencies: L06.

Acceptance criteria:

- Admin uploads/replaces one current contract with optional start/end dates.
- Validate actual PDF and size; failed replacement keeps existing contract.
- Authorized download works; anonymous and other-employee download fail.
- Choose private persistent file storage before implementation.

### L08 · Review and verify the employee milestone (8h)

Requirements: Section 5, FR-12. Dependencies: L07.

Acceptance criteria:

- Test records survive restart; exercise duplicate fields and inactive accounts.
- Verify private profile/salary/contract access and Arabic RTL/mobile forms.
- Explain actual affected files/data flow and reserve learner time for changes.

## Week 2 — 50 hours

### L09 · Configure the common company work schedule (5h)

Requirements: FR-05. Dependencies: L08.

Acceptance criteria:

- Admin sets company timezone, currency, working weekdays and common daytime hours.
- Confirm daytime-only scope; validate hours/settings.
- Preserve past schedule interpretation when settings change.

### L10 · Implement employee check-in and check-out (7h)

Requirements: FR-06. Dependencies: L09.

Acceptance criteria:

- Explicit buttons use server timestamps; login alone does not punch attendance.
- Reject duplicate/repeated/concurrent punches and check-out without check-in.
- Employee reads own current attendance in English/Arabic.

### L11 · Show attendance and allow explained corrections (6h)

Requirements: FR-06. Dependencies: L10.

Acceptance criteria:

- Admin filters attendance by date/employee.
- Correction preserves original values, reason, actor and time.
- Show lateness, early departure, extra time and missing check-out against applicable schedule.

### L12 · Configure leave options and advance-notice policy (6h)

Requirements: FR-05, FR-07. Dependencies: L09.

Acceptance criteria:

- Admin manages leave types and minimum notice.
- Confirm calendar-day deadline boundary, balances and paid/unpaid behavior with learner.
- Do not add accrual/carry-over or default statutory rules.

### L13 · Submit leave requests with server-side deadline checks (6h)

Requirements: FR-07. Dependencies: L12.

Acceptance criteria:

- Employee submits type, dates and reason; valid request becomes Pending.
- Reject late requests, reversed/past ranges and overlapping pending/approved requests.
- Test exact notice boundary in company timezone; employee sees own requests only.

### L14 · Approve or decline leave and reconcile attendance (5h)

Requirements: FR-07, FR-06. Dependencies: L13, L11.

Acceptance criteria:

- Admin sees pending requests and decides once, recording decision actor/time.
- Employee sees result; approved leave/non-working days do not become absence suggestions.
- Apply only agreed balance and pay rules; finalized payroll conflicts are handled explicitly.

### L15 · Record interviewed candidates and their status (5h)

Requirements: FR-08. Dependencies: L06.

Acceptance criteria:

- Admin adds/lists/edits candidate contact, target title, interview date/interviewer, notes and status.
- Employee access is denied.
- No public job page, incoming applications, CV uploads or automatic employee conversion.

### L16 · Flag attendance adjustments and capture admin decisions (10h)

Requirements: FR-09. Dependencies: L11, L14.

Acceptance criteria:

- Agree thresholds; identify late/early/extra/absence events without duplicate suggestions.
- Admin sees evidence, enters amount/reason and approves or rejects; no automatic salary changes.
- Corrections refresh pending suggestions and flag affected approved ones for review.
- Verify missing check-out requires correction and approved leave suppresses false absence.
- Learner reviews attendance/leave/interview/adjustment flows before payroll starts.

## Week 3 — 50 hours

### L17 · Create monthly payroll drafts and calculate net pay (7h)

Requirements: FR-10. Dependencies: L16.

Acceptance criteria:

- Confirm partial-month, unpaid-leave, negative-net and salary inclusion rules before coding.
- Snapshot salary/allowance and include approved adjustments once per employee/month.
- Calculate decimal net amounts; draft recalculation never duplicates adjustments.

### L18 · Finalize payroll and mark employee payments paid (6h)

Requirements: FR-10. Dependencies: L17.

Acceptance criteria:

- Admin reviews unresolved issues before deliberate finalization.
- Prevent duplicate finalization; preserve finalized values after future salary changes.
- Record payment status/date per employee; no bank transfer integration.

### L19 · Show payroll due and pending-action notifications (5h)

Requirements: FR-11. Dependencies: L14, L16, L18.

Acceptance criteria:

- Confirm month-end/configured payday rule.
- Admin opening app sees due/overdue payroll, pending leave and adjustments without duplicate prompts.
- State resolves when action completes; no scheduler/email/SMS/push infrastructure.

### L20 · Show and print employee payslips (4h)

Requirements: FR-11. Dependencies: L18.

Acceptance criteria:

- Printable English/Arabic page shows month, employee, salary components, net and payment status.
- Employee can read only own finalized payslips, subject to visibility agreement.
- Browser printing works without separate PDF generation.

### L21 · Complete English/Arabic and responsive usability review (6h)

Requirements: FR-12, Section 5. Dependencies: L19, L20, L15.

Acceptance criteria:

- Verify all screens/messages/statuses, RTL layout, language persistence and mobile forms.
- Preserve user-entered names/notes and monetary/date meaning across language switches.
- Check labels, keyboard interaction and Arabic printed payslip.

### L22 · Verify payroll arithmetic and access boundaries (7h)

Requirements: Section 5. Dependencies: L18, L20.

Acceptance criteria:

- Meaningful tests cover decimal amounts, rejected adjustments, duplicate inclusion and finalization.
- Test two employees attempting cross-profile/contract/leave/payslip access.
- Verify late-leave boundary, concurrent punches and salary edits after payroll finalization.

### L23 · Prepare deployment and verify persistence and recovery (6h)

Requirements: Section 5. Dependencies: L22.

Acceptance criteria:

- Discuss host and choose persistent PostgreSQL/private contract storage.
- Keep secrets out of source control and use HTTPS; run migrations.
- Verify application restart preserves records/files and perform a basic backup/restore check.
- Deployment approval, if needed, is separate from preparing the release.

### L24 · Run final bilingual acceptance and learner fixes (9h)

Requirements: Section 5. Dependencies: L21, L22, L23.

Acceptance criteria:

- Run complete employee → attendance → leave → adjustment → payroll → payslip flow in both languages.
- Learner tests each role and fixes reproducible defects; record remaining limitations.
- Stop adding scope; report completed/unfinished tasks honestly against the three-week target.

## Review checkpoints

- Week 1 first checkpoint: employee API plus form/list, tested locally before authentication is complete.
- Week 1 exit: employee records, accounts, job titles and private contract PDFs.
- Week 2 exit: attendance, leave, interviews and admin-reviewed adjustment suggestions.
- Week 3 exit: monthly payroll, in-app due prompts, payslips and bilingual acceptance.

If a week overruns, use the reserved review/fix hours and discuss any change in delivery scope. Do not silently remove agreed requirements or extend the deadline.
