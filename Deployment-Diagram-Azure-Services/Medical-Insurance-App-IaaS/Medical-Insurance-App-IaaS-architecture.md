# 1) Architecture Goals & Principles

## 1.1 Goals

*   **Secure by design**: minimize public exposure, WAF protection, segmentation, least privilege
*   **Highly available (within single region)**: no single point of failure across tiers
*   **Scalable** despite unknown user load: VM Scale Sets + autoscaling policies
*   **Auditable & compliant**: centralized logging, immutable-ish audit patterns, traceable actions
*   **Operationally manageable**: controlled admin access, patching strategy, backup, monitoring

## 1.2 Non-functional drivers (insurance context)

*   Confidential health and personal data (PII/PHI)
*   Clear separation of duties: Agent submits, Company approves/rejects
*   Strong audit trail for policies, claims, document access, approvals

***

# 2) Target Deployment: Logical View

### Workloads

*   **Public Portals (Internet)**
    *   Policy Holder Portal (read-only views)
    *   Agent Portal (policy creation, claims submission, uploads)

*   **Private Intranet (Corporate network only)**
    *   Company Portal (approve/reject policies and claims, cheque details)

### Core services (IaaS)

*   **Web Tier**: VM Scale Set (VMSS)
*   **App/API Tier**: VMSS
*   **DB Tier**: SQL Server Always On Availability Group on VMs
*   **Document Tier**: File Server Cluster on VMs (SMB/NFS)
*   **Security Tier**: Azure Firewall + NSGs + WAF
*   **Management**: Bastion or hardened Jump VM (no public RDP/SSH)

***

# 3) Azure Landing Zone (Networking) — “Best Practice” Hub-Spoke

Even for a single region, **Hub-Spoke** gives the cleanest governance.

## 3.1 Address Space (example)

Choose a private range that won’t overlap with on-prem networks.

*   **Hub VNet**: `10.10.0.0/16`
*   **App Spoke VNet**: `10.20.0.0/16`
*   (Optional future) **Shared Services VNet**: `10.30.0.0/16`

***

# 4) Detailed Networking Design (CIDR + Subnets)

## 4.1 Hub VNet (`10.10.0.0/16`)

| Subnet                  |           CIDR | Purpose                           |
| ----------------------- | -------------: | --------------------------------- |
| `AzureFirewallSubnet`   | `10.10.0.0/26` | Azure Firewall must live here     |
| `GatewaySubnet`         | `10.10.1.0/27` | VPN Gateway / ExpressRoute        |
| `Mgmt-Subnet`           | `10.10.2.0/24` | Bastion or Jump VM, tooling       |
| `Dns-Subnet` (optional) | `10.10.3.0/24` | DNS forwarders on VMs (if needed) |

### Hub resources

*   **Azure Firewall** (central egress + governance)
*   **VPN Gateway** (for Company staff intranet access)
*   **Bastion** *(or Jump VM if you want strict IaaS-only admin stack)*
*   (Optional) DNS forwarder VMs

***

## 4.2 App Spoke VNet (`10.20.0.0/16`)

| Subnet                    |            CIDR | Type        | Purpose                           |
| ------------------------- | --------------: | ----------- | --------------------------------- |
| `AppGW-DMZ`               |  `10.20.0.0/24` | Public edge | Application Gateway WAF v2        |
| `Web-Subnet`              | `10.20.10.0/24` | Private     | Web portal VMSS                   |
| `App-Subnet`              | `10.20.20.0/24` | Private     | API/Services VMSS                 |
| `Intranet-Subnet`         | `10.20.30.0/24` | Private     | Company Portal behind ILB         |
| `DB-Subnet`               | `10.20.40.0/24` | Private     | SQL Always On nodes               |
| `Files-Subnet`            | `10.20.50.0/24` | Private     | File Server cluster               |
| `Batch-Subnet` (optional) | `10.20.60.0/24` | Private     | Document scanning, reporting jobs |
| `Private-Mgmt`            | `10.20.70.0/24` | Private     | Monitoring agents, patch mgmt     |

***

# 5) Traffic Flow & Routing (UDR / Firewall Strategy)

## 5.1 Routing Approach (recommended)

*   **All outbound traffic from spoke subnets goes via Azure Firewall**
*   Achieved by **UDRs** (User Defined Routes)

### Example UDR (applied to Web/App/DB/Files/Intranet subnets)

*   `0.0.0.0/0` → next hop: **Azure Firewall private IP**
*   Exception routes (optional) for Azure platform endpoints if required

## 5.2 Inbound Rules (Public Internet)

Only one public entry:

✅ Internet → **Application Gateway WAF v2** (Public IP) on 443  
❌ No public IPs on VMs  
❌ No inbound directly to VMSS, SQL, file servers

## 5.3 Corporate access (Company Portal)

*   Corporate network → **VPN Gateway** → Hub VNet → Peering → Spoke → Intranet subnet
*   Company Portal sits behind **Internal Load Balancer**

***

# 6) Security Segmentation: NSG & Micro-Segmentation

## 6.1 Recommended NSG model

*   One NSG per subnet
*   Use **Application Security Groups (ASGs)** for clarity:
    *   `ASG-Web`
    *   `ASG-App`
    *   `ASG-SQL`
    *   `ASG-Files`
    *   `ASG-CompanyPortal`

## 6.2 Key NSG Rules (Essential)

### DMZ / AppGW subnet

*   Allow inbound: `Internet → AppGW : 443`
*   Allow outbound: `AppGW → Web VMSS : 443`
*   Deny all else

### Web subnet

*   Allow inbound: `AppGW → Web : 443`
*   Allow outbound: `Web → App : 443`
*   Deny outbound to DB directly

### App subnet

*   Allow inbound: `Web → App : 443`
*   Allow outbound: `App → SQL : 1433`
*   Allow outbound: `App → Files : 445 (SMB)` or `2049 (NFS)`
*   Deny all else (especially internet egress except via firewall)

### DB subnet

*   Allow inbound: `App → SQL : 1433`
*   Allow SQL replication ports (Always On): e.g., `5022` (configurable)
*   Deny all else

### Files subnet

*   Allow inbound: `App → Files : 445 (SMB)` and/or `2049 (NFS)`
*   Allow inbound: `Company Portal → Files : 445` (if staff download docs)
*   Deny all else

### Intranet subnet

*   Allow inbound: `VPN ranges → ILB : 443`
*   Allow outbound: `Company Portal → SQL : 1433`
*   Allow outbound: `Company Portal → Files : 445`
*   Deny internet inbound/outbound except via firewall

***

# 7) Compute Architecture (IaaS)

## 7.1 Web Tier (VMSS)

*   **VM Scale Set** with minimum 2 instances (HA)
*   Hosts:
    *   Policy Holder Portal (internet-facing via WAF)
    *   Agent Portal (internet-facing via WAF)

**Why VMSS?**

*   Handles unknown scale using autoscale policies
*   Allows rolling updates and health probes

**Autoscale baseline**

*   Scale out if CPU > 60% for 10 mins OR requests > threshold
*   Scale in if CPU < 30% for 20 mins
*   Start: `min=2`, `default=2`, `max=10` (adjust later)

## 7.2 App Tier (VMSS)

*   Business APIs / service layer
*   Separation from web tier improves security and maintainability

**Why separate App tier?**

*   Reduces attack surface (web tier cannot directly reach DB/files unless allowed)
*   Enables independent scaling of API logic vs UI

***

# 8) Database Tier (IaaS)

## 8.1 SQL Server Always On Availability Group on VMs

*   2 SQL nodes in DB subnet
*   1 small witness VM (or witness role) for quorum
*   Internal Load Balancer for listener endpoint

**Why SQL on VMs?**

*   Full control (IaaS requirement)
*   Strong relational fit for policies/claims/members
*   HA within region via Always On

**Recommended**

*   Place SQL nodes across **Availability Zones** (still single region) if enabled  
    *(This is the best possible for uptime within one region.)*

***

# 9) Document Storage (100% IaaS)

## 9.1 File Server Cluster on VMs (SMB/NFS)

Two strong options:

### Option A (Common enterprise): Windows File Server Cluster

*   2 nodes (Files subnet)
*   SMB shares for document uploads/downloads
*   ACLs integrated with AD groups (Agent upload, Company review, etc.)
*   Use Storage Spaces Direct / clustered disks on managed disks

### Option B (Linux): NFS cluster on VMs

*   Useful if your app stack is Linux-heavy

**Best possible add-ons**

*   **Anti-malware scan pipeline** on upload (dedicated scanning VM)
*   Store a **document hash** in SQL to detect tampering
*   Enable file-level auditing

***

# 10) Identity & Secrets (IaaS-friendly)

Because you’re IaaS-focused:

## 10.1 Identity

*   **Active Directory Domain Services on VMs** (2 DCs)
    *   Used for VM domain join, group policies, file server ACLs
*   Application authentication choices:
    *   **Option 1**: App-managed users (SQL table + hashed passwords) for trainees
    *   **Option 2**: AD/LDAP integration for staff portal

## 10.2 Secrets Management (without Key Vault)

*   **HashiCorp Vault on VMs** (HA) or an enterprise secret manager on VMs
*   Certificates via AD CS (Certificate Services) on VMs

*(In many real deployments, Key Vault is preferred, but you asked best possible IaaS—so we keep secrets on hardened vault VMs.)*

***

# 11) Perimeter Security

## 11.1 WAF

*   **Application Gateway WAF v2** protects public portals (OWASP rules, bot protection tuning)

## 11.2 Firewall

*   **Azure Firewall** for outbound control:
    *   only allow outbound to:
        *   OS update repositories
        *   CRL/OCSP endpoints (cert checks)
        *   email/SMS gateways (if any)
    *   block everything else by default

## 11.3 DDoS

*   **DDoS Network Protection** on VNet (recommended for internet portals)

***

# 12) Observability, Audit, Monitoring (Operational Governance)

## 12.1 Monitoring

*   Azure Monitor agent on all VMs
*   Central log collection (Log Analytics)

## 12.2 Auditing (must-have for insurance)

*   Application audit logs:
    *   policy created/updated/submitted
    *   claim submitted/updated
    *   approvals with reasons
    *   document access events
*   SQL audit:
    *   critical table changes and privileged access
*   File audit:
    *   file read/write/delete with user identity

***

# 13) Backup & Recovery (Single Region)

Even single region needs strong backup.

## 13.1 Backup strategy

*   VM backups (daily, retain per policy)
*   SQL backups:
    *   Full daily + Diff every 6 hours + Log every 15 mins (example)
*   File server backups:
    *   Snapshot-based backups + periodic full

## 13.2 RPO/RTO (typical targets)

*   RPO: 15–60 minutes (depends on log backups)
*   RTO: 2–6 hours (depends on rebuild automation)

***

# 14) Reference Port Matrix (Key Ports)

*   Internet → App Gateway: **443**
*   App Gateway → Web VMSS: **443**
*   Web VMSS → App VMSS: **443**
*   App VMSS → SQL Listener: **1433**
*   SQL replication (Always On): **5022** (typical)
*   App/Company Portal → File servers:
    *   SMB: **445**
    *   NFS: **2049** (if Linux)
*   Admin (internal only via Bastion/Jump):
    *   RDP: **3389**
    *   SSH: **22**

***

# 15) Blueprint Summary

*   Only **one public endpoint** (WAF)
*   Strong **network segmentation** and deny-by-default NSGs
*   Central **egress control** with Firewall + UDR
*   **Autoscaling** compute using VMSS
*   **HA database** (Always On) + internal listener
*   **IaaS document storage** with clustered file servers + auditing
*   Corporate-only intranet via VPN + ILB
*   Built for compliance: logs + audit trails + separation of duties

***

