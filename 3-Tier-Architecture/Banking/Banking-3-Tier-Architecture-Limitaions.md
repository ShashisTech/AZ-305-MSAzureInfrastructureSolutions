**Limitations of the 3‑Tier Architecture (Local / On‑Prem Banking Deployment)**

**1\. Limited Scalability**

A 3‑tier model—especially in local/on‑prem deployment—scales **vertically**, not horizontally.

*   When customer traffic grows (registrations, statements, cheque updates, bill‑pay jobs), the **App Tier becomes the bottleneck**.
*   Scaling the DB tier is even harder; SQL Server vertical scaling has hard limits on CPU/RAM.

**Result:**  
Not suitable for high‑volume banking workloads or future expansion.

**2\. Single Point of Failure (SPOF)**

In local/on‑prem setups:

*   **One IIS server** hosting UI + business logic.
*   **One SQL Server** instance handling all data.

If any of these fail:

*   Entire banking portal becomes unavailable (registration, login, cheque status, bill pay, transfers).

**Result:**  
Poor fault‑tolerance and low resilience.

**3\. Tight Coupling Between Layers**

All business logic sits inside a single application tier.

*   Changes in cheque processing, overdraft rules, interest calculation, bill‑pay scheduler, etc., require **redeploying the whole application**.
*   Difficult to evolve or modernize services independently.

**Result:**  
Slower development, high regression risk, reduced agility.

**4\. Background Jobs Burden the Application Tier**

iNB has many scheduled operations:

*   Daily overdraft interest
*   Savings interest
*   Cheque clearing workflow
*   Bounce fee updates
*   Bill‑pay scheduling

In a 3‑tier system:

*   These jobs run **inside the same server/process** as customer traffic.
*   High loads cause **resource contention** (CPU spikes, long response times).

**Result:**  
Performance degradation during peak operations.

**5\. Limited Support for High Availability & Disaster Recovery**

Local/on‑prem 3‑tier architecture lacks:

*   Distributed failover
*   Geo‑redundancy
*   Automated backup/restore
*   Active‑active nodes

Banking requires high uptime, but 3‑tier local setups struggle to meet that.

**Result:**  
Not suitable for production‑grade banking reliability.

**6\. No Elasticity During Peak Loads**

Banking traffic spikes occur during:

*   Salary days
*   Bill payment cycles
*   End‑of‑day (EOD) job runs

3‑tier local deployments **cannot autoscale**, unlike cloud/Microservices/SOA.

**Result:**  
Slowdowns, timeouts, or manual intervention required.

**7\. Harder to Enforce Modern Security Practices**

Although basic security exists (lockout after 3 failed logins), local 3‑tier architectures lack:

*   Zero‑trust network boundaries
*   API gateways
*   Centralized identity providers
*   Runtime segmentation

**Result:**  
Higher security risks and operational overhead.

**8\. Monolithic Deployment Model**

All modules—Registration, Accounts, Cheques, Transfers, Bill Pay—live inside one deployable unit.

This causes:

*   Long deployment cycles
*   Higher risk of outage during updates
*   Slower QA and release processes

**Result:**  
Not suitable for continuous delivery or rapid changes.

**Summary (Short Version)**

| Limitation | Impact |
| --- | --- |
| Limited scalability | Cannot handle banking growth |
| Single point of failure | Outages affect entire system |
| Tight coupling | Hard to change/extend features |
| Shared resources | Jobs slow down customer traffic |
| Weak high availability | Not enterprise‑grade reliability |
| No elasticity | Poor performance during spikes |
| Harder security | Local infra lacks modern controls |
| Monolithic deployment | Slow releases; high risk |