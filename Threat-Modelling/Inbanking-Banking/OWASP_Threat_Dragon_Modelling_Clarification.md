Complete Detailed Explanation of the Threat Modelling Diagram

**. External Users & Systems (Trust Level: Lowest)**

This includes:

*   **Customer (Web/Mobile)** – Uses login, views balance, transfers funds, deposits cheque slips.
*   **Banker (Back Office)** – Updates cheque status (Cleared/Bounced/Not Received), performs approvals.
*   **Email/SMS Gateway** – Sends OTPs, alerts.
*   **Courier/Postal Service** – Delivers physical cheques for clearance.

**Purpose in Threat Model**

This boundary identifies **all unauthenticated, unverified, or uncontrolled entities** that interact with the system.

**Typical Threats (STRIDE)**

*   **Spoofing** → Fake customer, phishing, OTP interception
*   **Tampering** → Altering cheque slips
*   **Repudiation** → “I didn’t request this payment”
*   **Information Disclosure** → Leak of PII (email, cell, address from spec)
*   **Denial of Service** → Bots attacking login
*   **Elevation of Privilege** → Using compromised credentials to act as a user/banker

**2\. Internet Zone → DMZ (Firewall, WAF, API Gateway)**

Before requests reach the application, they pass through:

**Firewall & DDoS Protection**

*   Blocks brute force, botnets, IP reputation attacks
*   Prevents volumetric DDoS

**WAF (Web Application Firewall)**

*   Blocks SQL Injection, XSS, CSRF, path traversal
*   Detects malicious patterns before reaching API Gateway

**API Gateway**

*   Enforces rate limiting
*   Validates JWT/Auth tokens
*   Acts as a reverse proxy ensuring **zero trust entry** into application layer

**Threats Addressed**

*   Injection attacks
*   API abuse
*   Bot attacks
*   Credential stuffing

**3\. Application Zone**

This zone contains the **core business logic**, separated into microservices:

**a. Auth Service**

Handles login, MFA, lockout after 3 invalid attempts (as per spec).

**Threats**

*   Credential stuffing
*   Session hijacking
*   Weak password policies (spec’s 6–8 char format increases risk)

**b. Customer & Account Services**

Fetch balance, show last 5 transactions, detailed statements, enforce min‑balance rules.

**Threats**

*   IDOR (viewing another customer’s account)
*   Data leakage
*   Statement tampering

**c. Payments & Transfers Service**

Handles bill pay, scheduled payments, intra‑bank transfers.

**Threats**

*   Payee manipulation
*   Fraudulent fund transfers
*   CSRF & replay attacks

**d. Cheque Processing Service**

Handles online cheque slips and updates physical cheque statuses:  
Not Received → Sent for Clearance → Cleared/Bounced.

**Threats**

*   Insider abuse (marking cheques incorrectly)
*   Fraudulent bounce charges
*   Slip modification

**e. Logging & Monitoring (SIEM)**

Collects events, suspicious behaviour, insider anomalies.

**Threats Mitigated**

*   Repudiation (“I didn’t do this transaction”)
*   Insider threats
*   Fraud anomalies

**4\. Data Zone (Highly Sensitive)**

Contains all the databases:

**Customer DB**

Stores PII: Name, Address, Phone, Email, Username, Password Hash.

**Accounts DB**

Balances, overdraft info, transactions (special rules for Savings & Current accounts).

**Cheque DB**

Slip info + status workflow.

**Notification Service**

Stores queued SMS/email OTP messages.

**Risk & Fraud Engine**

Analyses behaviour anomalies (velocity, device risk, failed attempts).

**Threats**

*   Data exfiltration
*   SQL injection
*   Backup theft
*   Insider access
*   Improper encryption

**5\. Core Banking Zone**

The most trusted and regulated zone.

**KMS/HSM (Key Management & Encryption)**

*   Manages database encryption keys.
*   Provides tamper‑proof secure cryptographic operations.

**Core Banking APIs / Service Bus**

*   Processes all real postings (credit/debit/overdraft interest).
*   Validates account state transitions.

**Bank Ops (Back Office)**

*   Approves registrations
*   Updates cheque statuses
*   Initiates manual unlocks (after 3 failed login attempts).

**Threats**

*   Insider manipulation
*   Unauthorized transaction posting
*   Privilege escalation

**6\. Cheque Processing Flow (Hybrid Digital + Physical)**

This is unique to iNB:

1.  Customer fills **online cheque slip**.
2.  Prints it and attaches to physical cheque.
3.  Sends via postal courier to Bank Ops.
4.  Operator marks status in system → affects customer balance.
5.  Bounced cheque applies fine (defined in spec).

**Threats**

*   Tampering in physical courier route
*   Lost or stolen cheques
*   Wrong status updates (fraud or error)
*   Insider setting to “Bounced” → unnecessary fine

**STRIDE Threat Categories in Diagram**

At the bottom of the diagram:

*   **S**poofing
*   **T**ampering
*   **R**epudiation
*   **I**nformation Disclosure
*   **D**enial of Service
*   **E**levation of Privilege

Each zone is architected to mitigate these threats.

**📌 Benefits of This Threat Modelling Architecture**

**1\. Clear Trust Segmentation (Defense‑in‑Depth)**

Hard separation of Internet → DMZ → App → Data → Core Banking drastically reduces blast radius.

**2\. Comprehensive Coverage of iNB Features**

Everything from login → accounts → transfers → cheque processing is captured in one diagram.

**3\. Strong Fraud Mitigation**

*   MFA
*   Rate limiting
*   Risk engine
*   SIEM alerts
*   Maker/Checker in cheque workflow

**4\. Cheque Workflow Risks are Properly Modeled**

This is a uniquely Indian banking behaviour and the architecture captures both digital and physical risks.

**5\. Regulatory Friendly**

*   Clear evidence of encryption
*   Access control boundaries
*   Logging & audit trails
*   Separation of duties (Bank Ops)

**6\. Supports Scalability & Maintainability**

Microservices allow independent scaling (Transfers, Cheques, Accounts).

**📌 Drawbacks / Gaps & Considerations**

**1\. Manual Cheque Process = High Risk**

Physical courier relies on humans → cannot guarantee integrity.  
Specifically vulnerable to tampering, loss, delays.

**2\. Password Requirements in Original Spec Are Weak**

6–8 characters → leads to easy brute force and ATO.  
(You should upgrade the policy.)

**3\. Account Lockout After 3 Attempts Can Be Exploited**

Attackers can easily lock legitimate users → Business DoS.  
Spec requires manual unlock → heavy operational load.

**4\. More Complexity = More Cost**

WAF, SIEM, Fraud Engine, KMS, Microservices = high engineering and maintenance cost.

**5\. Insider Threats Remain Possible**

Even with SIEM, Bank Ops still has elevated powers to alter cheque statuses.

**6\. System Requires Strong Monitoring / Governance**

Because multiple zones + microservices + CBS integration = more operational overhead.

**📌 Final Summary**

The Threat Modelling Diagram gives a **complete, enterprise‑grade security architecture** for Indian Net Banking (iNB), integrating:

*   Modern security patterns
*   All functional modules from the official case study
*   STRIDE threat modelling
*   Physical + digital flow (cheques)
*   Compliance‑ready segmentation

It reveals **visible strengths** (layered security, comprehensive controls) and **true operational risks** (manual cheque flow, weak password policy, lockout DoS, insider risks).