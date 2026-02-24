**Limitations of the 3‑Tier Architecture (for IMIC Medical Insurance System)**

The classic **Presentation → Application Logic → Data** architecture works for simple, local deployments—but it becomes restrictive as the system grows. Here are the key limitations:

**1\. Limited Scalability**

All components (Policy Management, Claims Handling, Approvals, Document Uploads) run within a single application tier.

*   As simultaneous users increase (Policy Holders, Agents, Company Staff), the system struggles because it cannot scale individual components independently.
*   Heavy workflows like document uploads and claims validation can slow down the entire system.

**2\. Single Point of Failure**

In a local/on‑prem deployment:

*   One IIS server hosts all portals.
*   One SQL Server instance stores all policy, claims, user, and document metadata.

If either server fails:

*   Policy purchase, claim submission, approvals, and logins **all stop**.

**3\. Tight Coupling of Features**

Policy, Claims, Approvals, Login Security, Document Processing are all implemented inside one application.

*   A small change (e.g., new policy rules) may require redeploying the entire system.
*   Harder to isolate bugs or expand features.

**4\. Poor Performance During High‑Load Operations**

Certain IMIC processes are heavier:

*   Uploading medical reports, diagnostic PDFs, age proofs
*   Claim documents submitted by agents
*   Company approval workflows

In 3‑Tier systems, these **block the same app tier** used for all other user requests, creating bottlenecks.

**5\. No Elasticity / Auto‑Scaling**

Traffic varies:

*   Many customers may check policies during renewal season.
*   Agents may submit multiple claims during peak times.

3‑Tier architecture **cannot auto‑scale**, especially in local/on‑prem setups.

**6\. Limited Support for Complex Workflows**

Processes like:

*   “All‑or‑nothing” policy approval
*   Claim validation with document verification
*   Multiple dependent records
*   Company‑side decision queues

These workflows benefit from microservices or workflow engines, which 3‑Tier does not naturally support.

**7\. Limited Security Extensibility**

The system only enforces:

*   Username/password
*   Lock account after 3 failed attempts

Adding modern security:

*   MFA
*   API gateways
*   Zero‑trust network segmentation
*   Risk‑based authentication

…is difficult in monolithic 3‑tier systems.

**8\. Monolithic Deployment Model**

All modules (Policy Holder Portal, Agent Portal, Company Portal, Policy Management, Claims Management, Document Services) are packaged and deployed together.

*   Increases deployment risk
*   Slows release cycles
*   Hard to modernize incrementally

**9\. Local Document Storage Limits**

Medical insurance requires handling:

*   Medical reports
*   Hospital bills
*   Age proofs
*   Claim documentation

Storing these locally (file share/DB) leads to:

*   Storage constraints
*   Backup/restore complexity
*   Higher risk of data loss

**10\. Lack of High Availability & Disaster Recovery**

3‑Tier on local machines:

*   No automatic failover
*   No geo‑redundancy
*   No load balancing across portals

Not suitable for enterprise‑grade insurance systems.

**Summary (Quick View)**

| Limitation | Impact |
| --- | --- |
| Limited scalability | Cannot expand with customers/agents |
| Single point of failure | Entire insurance workflow stops |
| Tight coupling | Slow changes & deployments |
| Resource contention | Document-heavy tasks slow down UI |
| No auto‑scaling | Bad performance during peak loads |
| Hard to support workflows | Manual & batch-heavy approval logic |
| Weak security extensibility | No modern security integrations |
| Monolithic deployment | Risky updates, slower releases |
| Storage limitations | Document growth becomes a problem |
| No DR/HA | System unsuitable for enterprise-grade operations |