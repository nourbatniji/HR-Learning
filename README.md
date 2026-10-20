# HR Learning

A small HR management system for one company, built incrementally to learn Next.js and NestJS.

## Planned stack

- Next.js and TypeScript for the frontend.
- NestJS and TypeScript for the backend API.
- PostgreSQL for the database.
- English and Arabic interfaces, including right-to-left layouts.

## Scope

- Two roles: Admin/HR and Employee.
- Employee records, job titles and private contract PDFs.
- Employee check-in/check-out against company working hours.
- Leave requests with configurable advance notice and admin approval.
- Interviewed candidate records and statuses.
- Monthly payroll with admin-approved bonuses/deductions, in-app reminders and printable payslips.

The first milestone is a database-backed employee list and add-employee form. Payroll and leave rules still awaiting a decision are identified in the SRS.

## Three-week plan

| Week | Focus |
| --- | --- |
| 1 | Employee milestone, login, profiles, job titles and contracts |
| 2 | Attendance, leave, interviews and adjustment review |
| 3 | Payroll, reminders, payslips and bilingual verification |

The estimate is 150 focused hours plus 30 hours reserved for review and fixes. Before each feature, discuss the approach, affected files and data flow. Make small changes and pause for testing and modification before continuing.

## Project documents

- [Software requirements specification](docs/SRS.md)
- [Three-week task backlog](docs/JIRA-BACKLOG.md)
- [Jira task links](docs/JIRA-LINKS.md)
- [Jira epic](https://nouribrahim.atlassian.net/browse/HL-1)

## Current status

Planning documents only. The application has not been scaffolded, so there are no installation or run commands yet. We will add them when the project setup is implemented.
