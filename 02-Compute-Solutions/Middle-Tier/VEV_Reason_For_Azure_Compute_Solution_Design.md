# Designing a Scalable Azure Compute Solution for a 3‑Tier Application

## First, understand the problem (before Azure)

Tailwind Traders has a **traditional 3‑tier application**:

**Traditional 3-tier model**

1.  **Front-end tier**
    *   IIS web servers
    *   .NET Core web app
    *   Handles customers browsing & purchasing
    *   Problem:
        *   During peak (1750 users/hour) → servers slow
        *   During off-hours → servers idle (wasted cost)
2.  **Middle tier**
    *   Business logic
    *   Handles **support requests**
    *   Requests are queued
    *   Problem:
        *   Long wait times
        *   Customers disconnect
        *   75–125 requests/hour
3.  **Back-end tier**
    *   SQL Server database
    *   Currently performing well
    *   No major problem here

**Constraints**

*   ✅ Must stay **in a single Azure region**
*   ✅ Needs **high availability**
*   ✅ Needs **modernization**
*   ✅ Should scale **up and down automatically**

Azure lets us:

*   **Scale automatically** instead of adding fixed servers
*   **Pay only when used**
*   **Separate traffic spikes from processing**
*   **Use managed services** instead of managing servers  
    \-----------------------------------------------------------------------

## Step 1: Azure Front Door -> Entry point for azure application

**What it does:**

*   Accepts all user traffic
*   Routes traffic efficiently
*   Uses **CDN (Content Delivery Network)** to cache static content (images, CSS, JS)

**Why it helps:**

*   Faster page loads
*   Reduces load on backend servers
*   Handles traffic spikes smoothly

✅ **Solves:** slow page loads during peak traffic

## Step 2: Azure WAF (Web Application Firewall)

**What is WAF?**

A security guard in front of your app.

**It protects against:**

*   SQL injection
*   Cross-site scripting (XSS)
*   Malicious bots
*   Common web attacks (OWASP Top 10)

**Why it’s important:**

*   Security is **not optional** in cloud
*   Protects your app without changing code

✅ **Adds security without extra servers**

## Step 3: Front-end Tier → Azure App Service

**This answers Task 1: Front-end tier**

**Why Azure App Service?**

Azure App Service is:

“A fully managed web hosting platform”

You **do NOT manage**:

*   IIS
*   Windows patches
*   OS upgrades

You **ONLY focus on code**.

**Key features used here:**

*   Hosts **.NET Core web app**
*   **Auto-scale**
*   High availability
*   Load balanced automatically

**Why this is perfect for your scenario:**

*   Traffic varies (1750/hour peak, low at night)
*   App Service:
    *   Scales OUT during peak
    *   Scales IN during off-hours
*   No idle servers
*   No performance bottleneck like before

✅ **This directly solves the front-end performance problem**

## Step 4: Orders & Catalog → Azure SQL Database (Managed Instance)

**What is Azure SQL Managed Instance?**

*   Fully managed SQL Server in Azure
*   Almost 100% compatible with on-prem SQL Server

**Why Managed Instance?**

*   Minimal code changes
*   Built-in:
    *   Backups
    *   Patching
    *   High availability

**Why it stays simple:**

*   Backend DB already performs well
*   No need to overcomplicate

✅ **Meets legal requirement: single region**

✅ **Stable and reliable**

## Step 5: Support Requests → Azure Service Bus (Queue)

**This is the MOST important modernization step**

**What is Azure Service Bus Queue?**

Think of it as:

“A waiting line between the front-end and the middle tier”

**Why do we need a queue?**

Previously:

*   User waits until support logic finishes
*   Long waits → customers disconnect

Now:

*   User submits request
*   Request goes into **queue**
*   User gets immediate response
*   Processing happens in background

**Request volume:**

*   75–125/hour
*   Perfect use case for a queue

✅ **Removes long wait times**

✅ **Improves customer experience**

✅ **Decouples front-end and middle tier**

## Step 6: Middle Tier → Azure Functions

**This answers Task 2: Middle tier**

**What are Azure Functions?**

*   Serverless compute
*   Runs code **only when needed**
*   No servers to manage
*   Scales automatically

**Why Azure Functions for middle tier?**

Middle tier:

*   Event-driven (queue messages)
*   Variable load
*   Doesn’t need to run 24/7

Azure Functions:

*   Triggered by Service Bus messages
*   Scales automatically based on queue depth
*   Costs less than always-on servers

✅ **Perfect for 75–125 requests/hour**

✅ **Eliminates idle compute cost**

✅ **Automatically handles spikes**

## Step 7: Supporting Services (Observability & Security)

**Application Insights**

*   Tracks:
    *   Response times
    *   Failures
    *   Dependencies
*   Helps debug performance issues

**Azure Monitor & Log Analytics**

*   Centralized logging
*   Alerts when something goes wrong

**Azure Key Vault**

*   Stores secrets:
    *   DB connection strings
    *   API keys
*   No secrets in code

✅ **Production-grade monitoring**

✅ **Security best practice**

## Final Answer to the Tasks

**✅ Task 1: Front-end tier – Recommended Azure service**

**✅ Azure App Service**

**Why?**

*   Native support for .NET Core
*   Auto-scaling handles peak traffic
*   No server management
*   High availability by default
*   Cost-efficient (no idle servers)

**✅ Task 2: Middle tier – Recommended Azure service**

**✅ Azure Functions + Azure Service Bus**

**Why?**

*   Asynchronous processing
*   Removes user wait times
*   Serverless = pay only when used
*   Scales automatically with demand
*   Ideal for support request workloads

## Azure Well-Architected Framework (WAF)

Now let’s explain **WHY this is a high-quality design**.

**🛡️ Reliability**

*   Auto-scaling App Service
*   Managed SQL with HA
*   Queue-based processing (no lost requests)

✅ App keeps running even under load

**🔐 Security**

*   Azure WAF
*   Azure Key Vault
*   Managed identities
*   Reduced attack surface

✅ Secure by default

**⚡ Performance Efficiency**

*   CDN caching
*   Async processing
*   Auto-scale based on demand

✅ Fast user experience

**💰 Cost Optimization**

*   No idle IIS servers
*   Serverless middle tier
*   Scale-in during off-hours

✅ Pay only for what you use

**🔧 Operational Excellence**

*   Application Insights
*   Centralized logging
*   Easy troubleshooting
*   Managed services reduce ops burden

✅ Easier to operate and maintain

## In one sentence

This Azure architecture modernizes a traditional 3-tier application by using Azure App Service for scalable front-end compute, Azure Service Bus and Azure Functions for asynchronous middle-tier processing, and Azure SQL Managed Instance for reliable data storage, while following the Azure Well-Architected Framework to ensure scalability, security, cost efficiency, and operational excellence.