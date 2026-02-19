**Threat Dragon Diagram Structure**

**📌 External Entities**

*   **Policy Holder**: Submits policy applications and claims.
*   **Agent**: Enters customer and claim data.
*   **Insurance Company Staff**: Validates documents and approves/rejects policies and claims.

**🔄 Processes**

*   **Policy Submission**: Agent enters policy details.
*   **Claim Submission**: Agent enters claim details.
*   **Authentication**: Validates user credentials.
*   **Document Verification**: Company checks uploaded documents.

**🗄️ Data Stores**

*   **Policy Database**: Stores policy details and status.
*   **Claims Database**: Stores claim details and status.
*   **Document Repository**: Stores medical reports, age proofs, and bills.

**➡️ Data Flows**

*   Agent → Policy Submission → Policy DB
*   Policy Holder → Claim Submission → Claims DB
*   Authentication → All Portals
*   Company → Document Repository → Verification Process

**Entities**

*   **Policy Holder** → Submits policy applications and claims.
*   **Agent** → Enters customer and claim data.
*   **Insurance Company Staff** → Validates documents and approves/rejects policies and claims.

**Processes**

*   **Policy Submission** → Agent enters policy details.
*   **Claim Submission** → Agent enters claim details.
*   **Authentication** → Validates user credentials.
*   **Document Verification** → Company checks uploaded documents.

**Data Stores**

*   **Policy Database** → Stores policy details and status.
*   **Claims Database** → Stores claim details and status.
*   **Document Repository** → Stores medical reports, age proofs, and bills.

**Trust Boundaries**

*   **External → Portal**: Policy Holder and Agent accessing via internet.
*   **Portal → Backend**: Internal company systems.
*   **Backend → Data Stores**: Sensitive storage and retrieval.

| STRIDE Category | Threat Example | Affected Component | Mitigation |
| --- | --- | --- | --- |
| Spoofing | Fake login attempts | Authentication | MFA, CAPTCHA, account lockout |
| Tampering | Altered policy/claim data | Submission Processes | Input validation, audit logs |
| Repudiation | Denial of submission | Policy/Claim Submission | Immutable logs, digital signatures |
| Information Disclosure | Leaked medical data | Document Repository | Encryption, RBAC |
| Denial of Service | Flooded login portal | Authentication | Rate limiting, load balancing |
| Elevation of Privilege | Agent accessing company-only functions | Backend Systems | RBAC, privilege audits |