To support the technical architecture for **Nano Technologies**, the entities and tables are designed to solve the specific bottlenecks of the current manual system, such as the inability to track where a "file" is located.

The following explains the core tables used in the diagram and how they map to the case study requirements:

### 1. Employees Table

This is the master data repository. It stores all the information the **HR user** enters on the first day an employee joins.

**Fields:** Includes personal data like Name, Address, PAN No, and Work Experience.


**Security Logic:** Contains `LoginAttempts` and `IsLocked` fields to satisfy the requirement that a user's account must be locked after three consecutive invalid logon attempts.


**Hierarchy:** Uses a `ReportsTo` foreign key so the system knows which manager should see which "Upcoming Appraisals".



### 2. Roles Table

This table manages the three distinct sections mentioned in the proposed system: **HR**, **Appraiser (Manager)**, and **Appraisee (Employee)**.

**Purpose:** It ensures that when a user logs on, they only see the relevant options and links for their specific role.


### 3. Appraisals Table (The "File" Tracker)

This table replaces the physical "file" that currently moves between the manager, employee, and HR.

* **Status Field:** This is the most critical addition. It tracks the "total delay" and "where the file is lying" (e.g., "With Manager" or "With Subordinate"), solving the primary problem described in the requirements.


**CurrentOwnerID:** Explicitly identifies which stakeholder currently has the authority to edit the appraisal at any given moment.



### 4. Appraisal_Details Table

This table stores the qualitative data gathered during the process.

**Manager Section:** Stores "Achievements," "Things not achieved," and "Suggestions" entered by the manager.


**Employee Section:** Stores the "Employee Feedback" entered by the appraisee.


**Logic:** It enforces the rule that an employee can only see this page if the manager has already entered their comments.



### 5. CTC_Information Table

This table handles the financial output of the appraisal process.

**Components:** Stores the new Basic Salary, Dearness Allowance, HRA, Food Allowance, and Provident Fund.


**Outcome:** It captures the "Promoted (Yes/No)" decision and the "Next Appraisal Date" determined by the manager.



---

### Why this Design?

**Eliminates Manual Intervention:** By using a centralized `Appraisals` table with status tracking, you remove the need for HR to manually shuttle files back and forth.


**OOAD Principles:** This separation of data (Normalization) allows the system to scale and ensures that historical appraisal data is not overwritten when a new cycle begins.


**Security:** It builds security directly into the schema via the account locking fields, meeting the technical constraints provided.



