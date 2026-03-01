### UI/UX Design Specification: E-Appraisal

#### 1\. Design Philosophy & Principles

The E-APPRAISAL application handles sensitive, high-stakes data. The user experience must be designed to build trust and reduce friction during the stressful review period.

- **Clarity over Complexity:** Performance data can be overwhelming. We present summary information first, with deep dives accessible via drill-downs.
- **Progressive Disclosure:** Information is revealed only when necessary. For example, the detailed Appraisal form sections are collapsed until needed.
- **Contextual Actions:** Buttons and actions change based on the user's role (Appraisee vs. Appraiser) and the current state of the Appraisal Cycle.
- **Visual Hierarchy:** Key performance indicators (ratings, deadlines) are visually prominent.
- **Accessibility (WCAG 2.1 AA):** The design ensures high contrast, clear focus states, and keyboard navigation for all interactive elements.

#### 2\. Visual Identity & Design Tokens

- **Colour Palette:** Professional, calming, and action-oriented.
  - _Primary:_ Deep Indigo (#3F51B5) - for headers, primary actions, and branding.
  - _Secondary:_ Teal (#009688) - for success states, completed tasks, and progress indicators.
  - _Accent:_ Amber (#FFC107) - for warnings, pending items, and focus states.
  - _Neutral Grays:_ (Slate gray #708090 to Light Gray #F5F5F5) - for text, backgrounds, and borders.
- **Typography:** Professional, clean sans-serif.
  - _Font Family:_ Inter or Roboto.
  - _Scale:_ Large headers (24px+), Sub-headers (18px), Body (16px), Captions (12px).
- **Iconography:** Minimalist, clear line icons (e.g., Material Design).
- **Layout:** A standard responsive grid (12-column), prioritizing a clean, spacious interface. A fixed left sidebar handles main navigation, while the main content area utilizes cards and modules.

#### 3\. Key User Flows (Wireframes/Mock ups)

We will focus on three main screens representing critical interactions within the system.

##### Scenario A: The Appraisee (Employee) Dashboard

This screen represents the landing page for an average employee (UserAccount). It aggregates their personal information, their position within the Department, and, most importantly, the status of their current review.

- _Entities Represented:_ UserAccount, PersonalInfo (VO), Department, Appraisal (Status), Assignment.
<img width="640" height="349" alt="image" src="https://github.com/user-attachments/assets/5743569d-5a21-4b2d-b9eb-eab363739ee7" />

**Image 1: Appraisee Dashboard Mockup**

The design uses clean white cards on a light gray background. We establish a clear information hierarchy:

- **The Left Sidebar:** Contains navigation icons for home, profile, and appraisals.
- **Contextual Greeting:** "Welcome, Amrita!" with profile picture.
- **Card 1 (My Profile):** Displays PersonalInfo(VO) data (Name, Address) and the associated Department. This solidifies the "1--1 PersonalInfo composition in UserAccount."
- **Card 2 (Performance Snapshot):** The center card is critical. It visualizes the current Appraisal status (e.g., 'Draft') using a progress bar and lists pending Assignments (like self-assessments) with deadlines.
- **Card 3 (Audit Trail):** On the right, a simplified AuditTrail widget shows recent activity ("You updated your self-assessment").

##### Scenario B: The Appraisal Review Form (Manager/Appraiser View)

This mockup focuses on the "runtime" of the appraisal process. It is the form where the complex relationships-the comments, the rating, and the outcome decisions-are captured. The design must handle significant text input and data relationship mapping.

- _Entities Represented:_ Appraisal, ManagerComments, EmployeeComments, UserAccount (Appraisee), SlaMetrics (VO), CTC, PromotionDecision.
<img width="640" height="349" alt="image" src="https://github.com/user-attachments/assets/4c7aae66-3a76-4c20-8980-2d58b5a501f8" />

**Image 2: Appraisal Review Mockup (Manager View)**

The design transitions to a deep indigo and white layout, emphasizing focus and data entry. The structure handles the 1--1 and 1--\* relationships:

- **Header Section:** Explicitly names the UserAccount(appraisee) being reviewed ("Amrita Singh") and the current Appraisal.status ("Evaluation").
- **Card 1 (Employee Comments):** This is a critical read-only section. It reads from the EmployeeComments.text (e.g., accomplishments) submitted by the appraisee.
- **Card 2 (Manager Evaluation):** The main input area for ManagerComments.text (Strengths, Improvements) and the selection of the numerical rating.
- **Card 3 (Outcomes):** The most complex relationships are visualized here. It contains interactive fields to determine the final CTC.amount (e.g., a bonus) and the PromotionDecision (Approved or Declined), reflecting the "1--1 association" defined in the diagram. A "Submit" button finalizes the review.

##### Scenario C: The HR Administrator Dashboard (System & Cycle Oversight)

This final screen is designed for the HR administrator role. It provides a macro-view of the system, focusing on managing the process, tracking efficiency, and viewing organizational structure. The data presentation shifts from individual records to aggregated, time-sensitive information.

- _Entities Represented:_ UserAccount, AppraisalCycle, AppraisalPeriod (VO), SlaMetrics (VO), AuditTrail, UserAccount/Department counts.

<img width="640" height="349" alt="image" src="https://github.com/user-attachments/assets/12b8a272-6c48-4d8b-abf2-ec7374e5791e" />

**Image 3: HR Admin Dashboard Mockup (Macro View)**

The design uses a professional indigo and white palette. In this screen, we aggregate system-wide data:

- **Header Bar:** Displays system-wide counts of Active Users and Total Departments.
- **Card 1 (Cycle Information):** Explicitly handles the 1--\* relationship from AppraisalCycle. It visualizes the current cycle ("Q4 Performance Review 2024") and uses a calendar widget to show the AppraisalPeriod (VO) (startDate, endDate) composition.
- **Card 2 (SLA Metrics):** The central card visualizes SlaMetrics (VO) (metricValue), but crucially, it handles the cross-cutting nature: It shows aggregated Cycle Completion Rate and Average Task Time, pulling data from _all_ appraisals in the current cycle to track process efficiency.
- **Card 3 (Audit Trail):** On the right, a expanded AuditTrail widget allows the HR admin to view a detailed, scrollable log of recent actions (action and performedBy attributes) to audit compliance and system usage.
