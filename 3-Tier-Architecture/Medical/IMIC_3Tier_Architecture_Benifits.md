**Indian Medical Insurance Corporation (IMIC)  
3‑Tier Architecture**

**1\. Overview**

The Indian Medical Insurance Corporation (IMIC) application follows a three‑tier architecture supporting three user groups — Policy Holders, Agents, and Company Staff — with workflows for policy creation, approval, and claims processing. This summary is derived from the uploaded requirement brief.

**2\. Architecture Layers**

**A. Presentation Tier**

• Policy Holder Web UI (read‑only personal, policy, and claim details).

• Agent Web UI (new policy entry, claim submission, status tracking).

• Company Back‑Office Intranet UI (policy and claim approvals).

• Optional Web Server/Reverse Proxy (IIS/Nginx) for SSL termination, routing, and static content.

**B. Application Tier**

• IMIC Core Web App (.NET Core / ASP.NET MVC) implementing policy and claims workflows.

• Policy & Claims Services (Web API / WCF) as the integration and business service layer.

• Background Jobs (Schedulers/Workers) for notifications, validations, and document processing.

**C. Data Tier**

• Relational Database (SQL Server / MySQL / Oracle) storing policies, claims, users, and roles.

• Reporting DB / SSRS / Power BI for analytics and dashboards.

• File storage for medical reports, identity proofs, and hospital bills (linked to DB metadata).

**3\. Key Functional Workflows**

• Policy Creation: Agent submits application and documents → Company verifies → Approve/Reject (all members together).

• Claims Management: Policy holder submits via agent → Company validates → Status: Sent for approval → Accepted/Rejected (with cheque details on acceptance).

**4\. Security & UX**

• Account locked after 3 invalid login attempts; manual unlock by admin.

• Consistent UI shell and navigation across all pages; minimal clicks.

**5\. Deployment (Local/Current)**

• Single IIS/Nginx front end or combined Presentation+App host.

• Single database instance with separate schemas for OLTP and reporting; file share for uploads.

• Logical tier isolation even when hosted on the same server or VM.

**Why did we choose a 3‑tier architecture**

**1\. The system is not large or complex enough**

The IMIC application has tightly related modules (policy, claims, users, documents).

These modules share the same workflows and data models.

**2\. 3‑tier architecture keeps things simple and fast to deliver**

A 3‑tier monolith allows:

*   Faster development
*   Easier debugging
*   Single deployment pipeline
*   Simpler management
*   Lower infrastructure cost

This aligns with IMIC project objectives—**easy to train, easy to deploy, easy to maintain** (as stated in the requirement brief)