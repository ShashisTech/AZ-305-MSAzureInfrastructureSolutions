**1) Business Objectives & Success Criteria**

*   **Goals & outcomes**: What are the top 3 outcomes the business expects (e.g., cycle‑time reduction, transparency, auditability)?
*   **KPIs**: How will we measure success (e.g., average appraisal turnaround time, % of on‑time completions, # of unlock requests)?
*   **Scope boundaries**: Are we building for a single business unit first or company‑wide from day one?
*   **Release strategy**: Do we need a pilot (department/region) before global rollout? What’s the expected timeline for v1 vs. subsequent releases?

**2) Users, Roles & Governance**

*   **Role definitions**: Are there any sub‑roles (e.g., HR Admin vs. HR Executive; Line Manager vs. Skip‑Level approver) beyond the three roles in the use case?
*   **Delegation & proxies**: Should managers be able to delegate their appraisal actions (e.g., during leave)? If yes, approval rules?
*   **Edge cases**: How do we handle temporary reporting changes during the cycle (acting managers, matrix reporting)?
*   **Account lifecycle**: Who provisions users and assigns roles? What’s the expected SLA for unlock requests?

**3) Appraisal Process & Workflow**

*   **Due‑date logic**: How are “upcoming appraisals” determined—fixed cycle dates, tenure‑based, or HR‑uploaded schedule?
*   **Re‑open/rework**: After **FINALIZED**, can an appraisal be reopened? Under what authorization?
*   **Parallel cycles**: Can an employee have more than one active appraisal (e.g., mid‑year + annual)?
*   **State transitions**: Are there any exceptions to the linear flow (Manager → Employee → Manager finalize)? For example, can HR intervene mid‑flow?
*   **Notifications**: In‑app only for v1, or do we need email alerts/reminders/escalations (out‑of‑scope in the case study but often requested)?

**4) Data & Information Architecture**

*   **Employee master**: Who is the system of record (HRMS/Payroll/AD)? Will we import initial employee data or start greenfield entry?
*   **Reporting structure**: How often does **Reports To** change, and who owns its accuracy?
*   **Data retention**: How long must we retain appraisal data, comments, and CTC snapshots? Any legal or policy requirements?
*   **CTC sensitivity**: Who can view CTC (HR, Manager); should Employees ever see their new CTC in app (or only via payroll letter)?
*   **Validation rules**: Any format rules for PAN/Passport, constraints for CTC components by grade/band?

**5) HR Module Clarifications**

*   **Mandatory fields**: Of the Employee Information fields in the use case, which are mandatory at creation vs. optional later?
*   **Bulk operations**: Do we need bulk import/update for employees, departments, due dates, or assignments?
*   **Unlock policy**: Should HR receive a dashboard of locked accounts and reasons?

**6) Appraiser Module Clarifications**

*   **Visibility**: Can a manager view historical appraisals of a reportee? Are comparisons with prior year comments required in v1?
*   **Comments editing**: Can Manager edit comments after Employee has submitted feedback (pre‑finalization)?
*   **CTC rules**: Are there guardrails or ranges for Basic/DA/HRA/PF by grade, location, or policy?

**7) Appraisee Module Clarifications**

*   **Self‑service scope**: Beyond personal info, can employees upload evidence (attachments) supporting achievements in v1?
*   **Feedback window**: Is there a deadline or SLA for employee feedback once manager comments are available?
*   **Visibility**: Should employees see the **promotion flag** or **CTC** at any point (usually **No** in v1, but confirm)?

**8) Security, Privacy & Compliance**

*   **Lockout specifics**: The case mandates lock after **3** invalid attempts; confirm lockout duration (indefinite until HR unlock vs. time‑based) and audit detail needed.
*   **PII/CTC protection**: Any requirements for data masking, encryption at rest, or field‑level authorization?
*   **Access reviews**: How often should role reviews/audits occur? Who certifies access?
*   **Regulatory**: Any specific compliance frameworks (e.g., ISO 27001, local labor laws, data residency constraints)?

**9) Non‑Functional Requirements (NFRs)**

*   **Performance**: Expected concurrency (peak users) during cycle windows? SLAs for page loads and list queries?
*   **Availability**: Required uptime (e.g., 99.5% business hours)? Maintenance windows?
*   **Scalability**: Growth assumptions—employee count now vs. 1–3 years?
*   **Usability**: Accessibility standards (WCAG level?), keyboard navigation, mobile‑responsive requirements (mobile app is out of v1 scope—confirm responsive web expectations).
*   **Observability**: What level of telemetry (audit, application logs, metrics, traces) do stakeholders need? Who will monitor?

**10) Integrations (Current & Future)**

*   **SSO/Directory**: Is SSO (AD/Entra ID) required in v1 or later? If later, should we architect to swap local auth easily?
*   **HRMS/Payroll**: Any outbound/inbound integrations (e.g., push finalized CTC/promotion to payroll)? If not now, what’s the roadmap?
*   **Notifications**: Do we integrate with corporate SMTP or notification service in v1?

**11) Reporting & Analytics**

*   **Operational reports**: Which reports are needed at go‑live (Upcoming Appraisals, In‑Process, Overdue, Locked Accounts, Cycle Summary)?
*   **Exports**: CSV/Excel export formats—any corporate templates?
*   **Audit reports**: Who needs audit views (Security, HR leadership), and at what granularity (user action, timestamp, before/after values)?

**12) UX & Content**

*   **Branding**: Any corporate UI guidelines for the **common header/footer/left navigation** (logos, color palette, typography)?
*   **Navigation shortcuts**: “Minimum clicks” requirement—what are the top tasks we must optimize (e.g., HR assign in ≤2 clicks)?
*   **Localization**: English only for v1 or multi‑language later?

**13) Workflow Exceptions & Escalations**

*   **Escalation rules**: If a manager doesn’t act by due date, who gets notified/escalated?
*   **Transfers**: What happens to in‑flight appraisals when an employee or manager transfers departments mid‑cycle?
*   **Special cases**: Contractors, interns, or employees on long leave—do they follow the same flow?

**14) Data Migration & Cutover**

*   **Historical data**: Do we import prior paper‑based appraisal summaries for reference, or start fresh?
*   **Employee master seeding**: Will HR provide a vetted list (Departments, Managers, Employees) for initial load?
*   **Cutover approach**: Big‑bang vs. phased adoption; blackout windows?

**15) Deployment, Environments & Release Management**

*   **Environments**: Dev, Test, UAT, Prod—any need for a staging/pre‑prod?
*   **Approvals**: Who signs off UAT? What are the acceptance criteria for go‑live?
*   **Install pack**: The use case requires an installation pack—what deployment tooling is standard (Azure DevOps, Octopus, scripts)?

**16) Operations & Support**

*   **Support model**: Who is L1/L2/L3? What are ticket priorities and SLAs?
*   **Runbooks**: Do we need runbooks for unlocks, role changes, environment resets, and data fixes?
*   **Backup/Restore**: RPO/RTO targets; restore drill cadence.

**17) Risk, Compliance & Legal**

*   **Data residency**: Any constraints on where the SQL Server instance must reside?
*   **Legal hold**: If there’s a dispute, should we lock specific appraisals from change/deletion?
*   **Retention & deletion**: Retain appraisals for X years; are there right‑to‑be‑forgotten requests applicable?

**18) Future Roadmap (to keep design extensible)**

*   **Capabilities**: Notifications, SSO, analytics dashboards, calibration/normalization flows, goal setting integration—what’s likely for v2/v3?
*   **Extensibility**: Should we design the appraisal model to support different forms/templates per department/band in later releases?

**19) Constraints & Assumptions to Validate**

*   **Technology constraints**: Mandated tech stack (SQL Server confirmed; any constraints on back‑end framework or hosting)?
*   **Budget & timeline**: Non‑negotiables that could affect scope/NFR trade‑offs.
*   **Change management**: Training needs, user guides, internal comms plan.