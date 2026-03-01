**Functional Specification — E‑Appraisal System**

**Document Owner:** Solution Architecture – Shashi Shekhar  
**Version:** 1.0  
**Date:** 28‑Feb‑2026  
**Status:** Draft for Review

**1\. Purpose & Background**

Nano Technologies conducts employee appraisals through a manual, file‑based process that causes loss of visibility, delays, and risk of mishandling sensitive information. The **E‑Appraisal System** digitizes the end‑to‑end appraisal process—enabling HR to manage employee data and trigger cycles, Managers (Appraisers) to evaluate and finalize CTC/promotions, and Employees (Appraisees) to review and submit feedback—while providing consistent UX, security, and auditability.

**2\. Scope**

**2.1 In‑Scope**

*   **Secure login** with role‑based access (HR, Appraiser, Appraisee).
*   **Employee master** data management (HR CRUD; employee self‑service with restricted fields).
*   **Appraisal lifecycle**:
    1.  HR: **View Upcoming Appraisals** and **assign** to manager.
    2.  Manager: **Personal Info (read‑only)** → **Manager’s Comments** → **Employee Comments (read‑only)** → **Appraisal Status & CTC** (Promotion flag, CTC breakdown, Next Appraisal Date) → **Finalize**.
    3.  Employee: **My Appraisal** (Personal Info RO → Manager’s Comments RO → **Employee Feedback**).
*   **Status visibility**: Manager’s **Upcoming** and **In‑Process** lists.
*   **Site details**: unified header/footer/left navigation; minimal clicks.
*   **Security policy**: lock account after **3** invalid logins; **manual** reactivation.

**2.2 Out‑of‑Scope (Release 1)**

*   Payroll/ERP integration, SSO/LDAP, mobile apps, multi‑language, external notifications (email/SMS), advanced analytics.

**3\. Stakeholders & Roles**

| Role | Responsibilities in System |
| --- | --- |
| HR | Maintain employee data; view upcoming appraisals; assign appraisees to managers; unlock user accounts. |
| Appraiser (Manager) | View assigned appraisals; enter manager comments; view employee feedback; set promotion & CTC; finalize appraisal; monitor in‑process. |
| Appraisee (Employee) | Update own profile (except Reports To & Department); view manager comments; submit feedback. |
| IT/Support | User provisioning, environment support, backups, and operations monitoring. |

**4\. High‑Level Requirements**

**4.1 Functional Requirements (FR)**

**FR‑AUTH (Authentication & Authorization)**

*   **FR‑AUTH‑01**: Users authenticate with username/password.
*   **FR‑AUTH‑02**: After **3** consecutive invalid attempts, the account is **locked**.
*   **FR‑AUTH‑03**: Only **HR** (or designated admin) can **unlock** accounts.
*   **FR‑AUTH‑04**: Post‑login menu items and pages are visible based on **role**.

**FR‑HR (HR Module)**

*   **FR‑HR‑01**: Create, read, update Employee profiles with fields:  
    Name, Address, City, Personal Phone, Mobile, Email, DOB, Gender, Marital Status, Date of Joining, Passport No, PAN No, Work Experience, Reports To, Department.
*   **FR‑HR‑02**: **View Upcoming Appraisals**: list employees with due appraisals.
*   **FR‑HR‑03**: **Assign** an appraisal to a manager (initiates workflow).
*   **FR‑HR‑04**: Unlock a locked user account.

**FR‑APPR (Appraiser Module)**

*   **FR‑APPR‑01**: **View Upcoming Appraisals** (only direct reports assigned by HR).
*   **FR‑APPR‑02**: **Personal Information** page (read‑only).
*   **FR‑APPR‑03**: **Manager’s Comments** page to enter: Achievements, Things not achieved, Suggestions.
    *   Save must persist data. If Employee has not yet commented, **Next** behaves as **Save** (no navigation to Employee Comments).
*   **FR‑APPR‑04**: **Employee Comments** page is **read‑only** for managers.
    *   A **Next** button is visible only to Managers (navigates to Status & CTC).
*   **FR‑APPR‑05**: **Appraisal Status & CTC** page: set **Promoted (Y/N)**; enter **Basic, DA, HRA, Food Allowance, PF**; set **Next Appraisal Date**; **Finalize** appraisal.
*   **FR‑APPR‑06**: **View Appraisals in Process**: list appraisals with status between initiated and finalized.

**FR‑EMP (Employee Module)**

*   **FR‑EMP‑01**: **My Information**: employee can edit own data except **Reports To** and **Department** (read‑only).
*   **FR‑EMP‑02**: **My Appraisal** sequence (wizard):
    *   **Personal Information** (read‑only),
    *   **Manager’s Comments** (read‑only),
    *   **Employee Comments**: Employee Feedback editable; **only accessible after Manager’s Comments are saved**.

**FR‑WF (Workflow & State)**

*   **FR‑WF‑01**: Appraisal states:  
    NOT\_STARTED → ASSIGNED → MANAGER\_COMMENTS → EMPLOYEE\_COMMENTS → FINALIZED.
*   **FR‑WF‑02**: Guard conditions:
    *   Employee Comments **require** Manager’s Comments.
    *   Status & CTC step **requires** presence of Manager’s and Employee’s Comments.
*   **FR‑WF‑03**: Finalization sets state to **FINALIZED**, persists Promotion and CTC, and records Next Appraisal Date.

**FR‑UI (Navigation & Consistency)**

*   **FR‑UI‑01**: Common **header**, **footer**, and **left navigation** across all pages.
*   **FR‑UI‑02**: Frequent tasks achievable with **minimum clicks** (e.g., ≤2 clicks from section landing pages).

**FR‑AUDIT (Audit & Visibility)**

*   **FR‑AUD‑01**: Record key actions: assignment changes, unlocks, comment submissions, and finalization.
*   **FR‑AUD‑02**: Provide basic admin/audit views (at least by entity & date).

**5\. Non‑Functional Requirements (NFR)**

*   **NFR‑SEC‑01**: Lockout after 3 failed logins; manual unlock only.
*   **NFR‑SEC‑02**: Role‑based authorization for all endpoints and UI elements.
*   **NFR‑SEC‑03**: Protect PII and CTC data (least privilege access, secure at rest/in transit per environment policies).
*   **NFR‑USAB‑01**: Uniform look‑and‑feel with consistent layout.
*   **NFR‑PERF‑01**: Typical page load ≤ 2 seconds on intranet conditions; list views paginated.
*   **NFR‑REL‑01**: Daily database backup with restore validation.
*   **NFR‑OBS‑01**: Server/application logs for authentication and workflow transitions.

**6\. Detailed Page/Screen Specifications**

Each screen specifies **Purpose**, **Fields**, **Actions**, **Validations**, **Rules/Guards**.

**6.1 Login**

*   **Purpose**: Authenticate user; enforce lockout.
*   **Fields**: Username, Password.
*   **Actions**: Sign In.
*   **Validations**: Required fields.
*   **Rules**: On 3rd consecutive failure → set account **Locked**; present “Contact HR” message.

**6.2 HR — Employee Information (Create/Edit)**

*   **Purpose**: Maintain employee master.
*   **Fields**: As listed in FR‑HR‑01.
*   **Actions**: Create, Save, Cancel.
*   **Validations**:
    *   Mandatory: Name, Email, Date of Joining, Reports To, Department.
    *   Email format; PAN format (if required by policy).
*   **Rules**: Manager (Reports To) must be an existing active employee; no self‑reporting.

**6.3 HR — View Upcoming Appraisals**

*   **Purpose**: Identify employees due for appraisal and **assign** to managers.
*   **List Columns**: Employee, Department, Due Date, Current Manager (if any), Status.
*   **Actions**: Assign (picker of eligible managers), View Details (read‑only snapshot).
*   **Rules**: On Assign → Appraisal status moves to ASSIGNED; notify in‑app.

**6.4 Appraiser — View Upcoming Appraisals**

*   **Purpose**: Show assigned appraisals not yet started by manager.
*   **List Columns**: Employee, Due Date, Status.
*   **Actions**: Open → navigates to **Personal Information** (read‑only).

**6.5 Appraiser — Personal Information (RO)**

*   **Purpose**: Provide context before entering comments.
*   **Fields**: Full employee profile (read‑only).
*   **Actions**: **Next** → Manager’s Comments.

**6.6 Appraiser — Manager’s Comments**

*   **Purpose**: Enter assessment.
*   **Fields**: Achievements (required), Things not achieved, Suggestions.
*   **Actions**: Save, **Next**.
*   **Rules/Guards**:
    *   Save always persists; status becomes MANAGER\_COMMENTS on first save.
    *   If employee hasn’t commented yet, **Next** behaves like **Save** only (stays on page).
    *   Once employee feedback exists, **Next** takes manager to **Employee Comments** (RO).

**6.7 Appraiser — Employee Comments (RO)**

*   **Purpose**: Manager reviews employee feedback.
*   **Fields**: Employee Feedback (read‑only).
*   **Actions**: **Next** (visible to manager only) → Appraisal Status & CTC.

**6.8 Appraiser — Appraisal Status & CTC**

*   **Purpose**: Finalize promotion and compensation.
*   **Fields**: Promoted (Y/N), Basic, DA, HRA, Food Allowance, PF, Next Appraisal Date.
*   **Actions**: Save, **Finalize**.
*   **Validations**: CTC values non‑negative; Next Appraisal Date ≥ current date.
*   **Rules/Guards**:
    *   Page is accessible only after Employee Comments exist.
    *   **Finalize** sets status to FINALIZED and freezes editable fields.

**6.9 Appraiser — View Appraisals in Process**

*   **Purpose**: Track ongoing appraisals.
*   **Columns**: Employee, Status, Last Updated.
*   **Actions**: Open (starts at Personal Info).

**6.10 Appraisee — My Information**

*   **Purpose**: Employee self‑service.
*   **Editable Fields**: All personal profile fields **except** Reports To & Department (read‑only).
*   **Actions**: Save.
*   **Rules**: Validation similar to HR page for shared fields.

**6.11 Appraisee — My Appraisal**

*   **Purpose**: Provide read‑only view of context and manager’s assessment, then capture employee feedback.
*   **Sequence**: Personal Info (RO) → Manager’s Comments (RO) → **Employee Comments** (editable).
*   **Rules/Guards**: Employee Comments page **visible only after** Manager’s Comments exist.

**7\. Workflow & State Model**

**7.1 States**

*   **NOT\_STARTED**: Appraisal record exists but not yet assigned.
*   **ASSIGNED**: HR assigned to a manager.
*   **MANAGER\_COMMENTS**: Manager saved comments.
*   **EMPLOYEE\_COMMENTS**: Employee submitted feedback.
*   **FINALIZED**: Manager finalized with promotion & CTC details and next appraisal date.

**7.2 Transitions & Preconditions**

*   NOT\_STARTED → ASSIGNED: HR assigns manager.
*   ASSIGNED → MANAGER\_COMMENTS: Manager saves comments.
*   MANAGER\_COMMENTS → EMPLOYEE\_COMMENTS: Employee submits feedback (manager comments present).
*   EMPLOYEE\_COMMENTS → FINALIZED: Manager saves CTC + promotion and finalizes (both comments present).

**8\. Data Model (Functional View)**

Logical entities and key attributes; physical design covered in DB spec.

*   **User**(UserId, Username, PasswordHash, FailedAttempts, IsLocked, LockedAt, Roles\[\])
*   **Department**(DepartmentId, Name)
*   **Employee**(EmployeeId, UserId, Name, Address, City, PersonalPhone, Mobile, Email, DOB, Gender, MaritalStatus, DateOfJoining, PassportNo, PANNo, WorkExperience, ReportsTo→Employee, DepartmentId→Department)
*   **Appraisal**(AppraisalId, EmployeeId→Employee, ManagerId→Employee, DueDate, Status, Promoted, NextAppraisalDate)
*   **ManagerComments**(AppraisalId→Appraisal, Achievements, ThingsNotAchieved, Suggestions, CreatedBy, CreatedAt)
*   **EmployeeComments**(AppraisalId→Appraisal, Feedback, CreatedBy, CreatedAt)
*   **CTC**(AppraisalId→Appraisal, Basic, DA, HRA, FoodAllowance, PF)
*   **AppraisalAssignment**(AppraisalId, AssignedToManagerId, AssignedByUserId, AssignedAt)
*   **AuditLog**(AuditId, EntityType, EntityId, Action, Details, ActorUserId, CreatedAt)

**9\. Business Rules & Validations**

*   **BR‑AUTH‑01**: Lock after 3 consecutive invalid logins; HR unlock only.
*   **BR‑EMP‑01**: Employee cannot set **Reports To** or **Department**.
*   **BR‑WF‑01**: Employee Comments require Manager’s Comments.
*   **BR‑WF‑02**: Status & CTC step accessible only after Employee Comments exist.
*   **BR‑WF‑03**: Finalization requires both comments and full CTC.
*   **BR‑DATA‑01**: No self‑reporting (ReportsTo ≠ EmployeeId).
*   **BR‑CTC‑01**: All CTC components ≥ 0.
*   **BR‑UI‑01**: Uniform shell (header/footer/nav) on all pages; minimize clicks.
*   **BR‑AUD‑01**: Record assign/unlock/submit/finalize actions.

**10\. Role‑Permission Matrix (Summary)**

| Capability | HR | Appraiser | Appraisee |
| --- | --- | --- | --- |
| Login | ✓ | ✓ | ✓ |
| Unlock Users | ✓ | – | – |
| Employee CRUD (full) | ✓ | – | – |
| My Information (self) | – | – | ✓ (except Reports To & Department) |
| View Upcoming Appraisals | ✓ (all due) | ✓ (assigned only) | – |
| Assign Appraisal | ✓ | – | – |
| Manager’s Comments | – | Create/Update | Read‑Only |
| Employee Comments | – | Read‑Only | Create/Update (self) |
| Appraisal Status & CTC | – | Create/Update/Finalize | – |
| In‑Process List | – | ✓ | – |
| Audit Logs (basic view) | ✓ | – | – |

**11\. Error Handling & Messages (Examples)**

*   **Invalid credentials**: “Username or password is incorrect.”
*   **Account locked**: “Your account is locked after 3 unsuccessful attempts. Contact HR to unlock.”
*   **Guard violation**:
    *   Employee tries to comment before manager comments: “Manager’s comments are required before submitting employee feedback.”
    *   Manager tries to finalize without prerequisites: “Cannot finalize: enter CTC and ensure both comments are submitted.”

**12\. Reporting & Lists (Release 1)**

*   **HR — Upcoming Appraisals**: CSV export (Employee, Department, Due Date, Assigned Manager, Status).
*   **Manager — In‑Process**: Filter by Status and Due Date.

**13\. Acceptance Criteria (Samples)**

1.  **Lockout**: After three invalid login attempts, user cannot authenticate; HR unlock allows subsequent login.
2.  **Assignment**: HR assigns appraisal; the selected manager sees the employee under **View Upcoming Appraisals** immediately.
3.  **Manager’s Guard**: After saving Manager’s Comments, employee can access Employee Comments; until then, cannot.
4.  **Finalize**: Manager sets Promotion Y/N, enters CTC and Next Appraisal Date; on Finalize, status becomes **FINALIZED**, and edits are disabled.
5.  **UI Consistency**: All pages exhibit identical header/footer/left navigation and achieve main task flows within minimal clicks.

**14\. Assumptions**

*   Due date calculation or provisioning is handled outside scope (provided to HR or seeded).
*   All users are provisioned with correct roles before first login.
*   Browser is a modern, standards‑compliant desktop browser; responsive design is recommended but mobile app is out‑of‑scope.

**15\. Constraints**

*   Must enforce the **3‑attempt lockout** rule with **manual unlock**.
*   Must use consistent site shell across all pages.
*   Sensitive data (CTC & PII) access restricted to designated roles.

**16\. Dependencies**

*   Corporate user provisioning (for initial HR/admin users).
*   Department and reporting hierarchy master data availability (for HR data entry and assignment).

**17\. Open Questions (to confirm with Business/HR)**

1.  Do we need due‑date rules by band/tenure or is it manual per cycle?
2.  Should promotion Y/N trigger any downstream notifications (in‑app only vs. email)?
3.  Are there constraints on CTC component ranges by grade/band?
4.  What is the retention policy for audit logs?
5.  Do we need “reopen” after Finalized (likely **No** for v1)?

**18\. Appendices**

**18.1 API Mapping (Short Form)**

*   **Auth**: POST /auth/login, POST /auth/unlock/{userId} (HR).
*   **Employees**: POST/GET/PATCH /employees, GET /employees/{id}.
*   **Appraisals**: POST /appraisals, GET /appraisals?status=…, POST /appraisals/{id}/assign (HR).
*   **Comments**:
    *   POST /appraisals/{id}/manager-comments (Appraiser)
    *   POST /appraisals/{id}/employee-comments (Appraisee)
*   **CTC & Finalize**: POST /appraisals/{id}/status-ctc (Appraiser).

**18.2 State & Guard Summary**

*   Guards implemented both at API and DB (triggers/constraints) to ensure order of operations and data integrity.