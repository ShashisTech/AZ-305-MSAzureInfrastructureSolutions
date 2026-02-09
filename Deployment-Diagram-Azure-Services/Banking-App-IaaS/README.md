
# iNB – Three‑tier IaaS on Azure with Load Balancer

This README describes the **production‑ready IaaS deployment** for the Indian Net Bank (iNB) online banking case study using a classic **Web / App / Data** three‑tier architecture on **Microsoft Azure**. It focuses on high availability, security, and operational excellence while keeping a close mapping to the functional requirements of the iNB system.

> **Scope**: This file covers only the **Three‑tier IaaS on Azure with Load Balancer** option.

---

## 1. High‑level Overview

- **Web tier**: Public‑facing ASP.NET Core web servers (multiple VMs in a **Virtual Machine Scale Set**, VMSS), fronted by a **WAF** and an **Azure Load Balancer** (or App Gateway + internal load balancer).
- **App tier**: Internal application servers (VMSS) that host domain/business services and background endpoints.
- **Data tier**: SQL Server on Azure VMs with **Always On Availability Groups** (or Azure SQL Managed Instance as an alternative), deployed in a dedicated subnet with no public exposure.
- **Cross‑cutting**: Azure Key Vault (secrets), Azure Cache for Redis (session/cache), Azure Storage (blobs for cheque slips and queues for background jobs), Azure Monitor/Application Insights, Azure Bastion for secure admin access.

---

## 2. Deployment Diagram (Mermaid)

```mermaid
graph TD
  A[Internet Users] --> B[WAF: Azure Front Door or App Gateway]
  B --> C[Azure Load Balancer (Public)]
  C --> W1[Web VMSS - Instance 1]
  C --> W2[Web VMSS - Instance 2]
  W1 --> ILB[Azure Load Balancer (Internal)]
  W2 --> ILB
  ILB --> A1[App VMSS - Instance 1]
  ILB --> A2[App VMSS - Instance 2]
  A1 -->|TDS/1433| D1[(SQL Server AG - DB VM 1)]
  A2 -->|TDS/1433| D2[(SQL Server AG - DB VM 2)]
  A1 --> R[Azure Cache for Redis]
  A2 --> R
  A1 --> S[Azure Storage (Blob/Queue)]
  A2 --> S
  W1 -->|MSI| K[Azure Key Vault]
  W2 -->|MSI| K
  A1 -->|MSI| K
  A2 -->|MSI| K
  subgraph Network
    B
    C
    W1
    W2
    ILB
    A1
    A2
    D1
    D2
    R
    S
    K
  end
```

---

## 3. Virtual Network & Subnets

- **VNet**: `vnet-inb-prod`
- **Subnets**:
  - `snet-web` (DMZ): Web VMSS + Public LB; inbound **80/443** from WAF only, egress via NAT.
  - `snet-app`: App VMSS + Internal LB; inbound only from `snet-web` on app ports (e.g., 5000/HTTPS).
  - `snet-data`: SQL VMs; inbound **1433** from `snet-app` only.
  - `snet-mgmt` (optional): Bastion/Jumpbox for administration.

**NSGs** block east–west traffic except strictly allowed paths. **UDRs** can force outbound via NAT Gateway/Firewall if required.

---

## 4. Components

### 4.1 Web Tier
- **Purpose**: Serve UI/API endpoints, terminate TLS, and forward to the App tier.
- **Compute**: Windows or Linux **VMSS** (2+ instances) across **Availability Zones**.
- **Ingress**: WAF → Public Load Balancer → VMSS.
- **State**: **Stateless**; use **Azure Cache for Redis** for session/state.

### 4.2 App Tier
- **Purpose**: Domain/business services (interest/overdraft calculation, bill scheduling, internal APIs).
- **Compute**: VMSS (2+ instances) across Zones.
- **Ingress**: Internal Load Balancer; traffic only from Web subnet.
- **Background jobs**: Timer/Queue triggered workers hosted here or in **Azure Functions**.

### 4.3 Data Tier
- **SQL Server on VMs** with **Always On AG** (2+ nodes) + File Share Witness or Cloud Witness.
- Alternatively, **Azure SQL Managed Instance** for reduced ops overhead.

### 4.4 Cross‑cutting
- **Key Vault**: Store connection strings, certificates; access via **Managed Identity**.
- **Redis**: Session/cache offload for multi‑instance tiers.
- **Storage**: Blob (cheque slips), Queue (cheque processing, scheduled payments).
- **Monitoring**: Application Insights + Log Analytics Workspaces.
- **Access**: Azure Bastion; block direct RDP/SSH from internet.

---

## 5. Ports & NSG Rules (example)

- **Public**
  - Inbound: 80/443 → WAF/App Gateway only.
  - Web VMs: receive only from Public LB health probes + WAF on 80/443.
- **Internal**
  - Web → App: 443 (or custom HTTPS port) via Internal LB.
  - App → DB: 1433 (TDS). Optionally 5022 for AG replicas.
  - Management: 22/3389 only from `snet-mgmt` via Bastion.

---

## 6. High Availability & Scalability

- **Scale**: VMSS autoscale on CPU/requests; maintain **min 2 instances** per tier.
- **Zones**: Distribute instances across **3 Availability Zones** (where available).
- **DB HA**: Always On AG across zones; backups with Recovery Services Vault; geo‑replication/DR region as needed.

---

## 7. Security Controls

- **Perimeter**: WAF (Front Door/App Gateway), optional **DDoS Network Protection**.
- **Network**: Subnet isolation, NSGs, UDRs, no public IPs on App/Data.
- **Secrets**: Key Vault + Managed Identity; no secrets in code or appsettings in prod.
- **Transport**: Enforce TLS 1.2+, HSTS, HTTPS‑only.
- **App**: OWASP hardening, CSRF protection, parameterized queries.
- **Identity**: ASP.NET Core Identity/AAD + lockout after 3 failed attempts, MFA for admins.
- **Audit**: Immutable logs for auth, transactions, cheque state changes.

---

## 8. Background Jobs

- **Daily**: Overdraft interest accrual for negative balances.
- **Monthly**: Savings interest postings.
- **Scheduled**: Bill payments; retries with poison queue handling.
- **Reconciliation**: Cheque status ingestion + reporting.

Implement using **Azure Functions (Timer/Queue)** or a dedicated **Worker VM** in the App tier.

---

## 9. Observability

- **Application Insights**: request telemetry, dependencies, exceptions, availability tests.
- **Log Analytics**: consolidate VM, NSG flow, and SQL logs; alert rules and dashboards.
- **Workbooks**: SLA/SLO tracking, autoscale history, error budgets.

---

## 10. CI/CD

- **Pipeline**: GitHub Actions/Azure DevOps.
- **Stages**: Build → Security scan → Infra (Bicep/Terraform) → Deploy app → Smoke tests.
- **Strategy**: Blue‑green or rolling for VMSS; health probes integrated with load balancers.

---

## 11. Cost & Footprint (indicative)

- Web VMSS (2–3 VMs), App VMSS (2–3 VMs), DB (2 VMs with AG), WAF, LB, Bastion, Redis (P1), Storage, Monitor. Use budgets + alerts; right‑size SKUs; schedule Dev/Test shutdowns.

---

## 12. Mapping to iNB Requirements

- **Mini/Detailed statements** → App APIs + SQL indexes + caching.
- **Savings interest** → Monthly job in App tier/Functions.
- **Overdraft daily charge** → Daily job; compound to ledger.
- **Cheque processing** → Blob (slips), Queue (workflow), worker processing, audit trail.
- **Bill payments (immediate/scheduled)** → APIs + schedule/queue processor + idempotency.
- **Security lockout (3 attempts)** → ASP.NET Core Identity policy; admin unlock portal.

---

## 13. Quick Start (Infra High‑level)

1. Create **Resource Group** and **VNet** with subnets (`snet-web`, `snet-app`, `snet-data`, `snet-mgmt`).
2. Deploy **WAF** (Front Door or App Gateway) and **Public LB** for Web VMSS.
3. Deploy **Web VMSS** (Linux/Windows) with custom script extension for app bootstrap.
4. Deploy **Internal LB** + **App VMSS**; lock down NSGs between subnets.
5. Deploy **SQL VMs** with **AG** (or use **Managed Instance**), private endpoints if applicable.
6. Provision **Key Vault**, **Redis**, **Storage**; assign **Managed Identities** and policies.
7. Configure **App Insights**, log routing, alerts, and dashboards.
8. Set up **Bastion**; disable public RDP/SSH.
9. Wire **CI/CD** to publish builds and run rolling upgrades.

---

## 14. Notes

- Prefer **Managed Instance** when possible to reduce ops overhead for the Data tier.
- Use **private endpoints** for PaaS services (Key Vault, Storage, Redis) to keep traffic in the VNet.
- Keep all tiers **stateless** and use **Redis** for session to ensure seamless scaling.

---

## 15. License

This architecture guide is provided for educational purposes as part of the iNB case study.
