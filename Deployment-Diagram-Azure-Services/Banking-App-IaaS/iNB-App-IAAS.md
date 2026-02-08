**Azure IaaS Deployment Diagram for iNB**

**Architectural Components (IaaS)**

This architecture gives you full control over the environment but requires you to handle patching and backups manually.

**1\. Compute Layer (Virtual Machines)**

*   **Web Server (IIS):** Instead of an App Service, you deploy **Windows Server Virtual Machines**.
    *   **Role:** You must manually install **IIS** and deploy the ASP.NET application code.
    *   **Scalability:** To handle high traffic (e.g., Bill Payments), you deploy multiple VMs behind an **Azure Load Balancer**.
*   **Database Server (SQL Server):** Instead of Azure SQL Database, you deploy a **Virtual Machine with SQL Server installed**.
    *   **Role:** Stores customer data and runs the database engine. You are responsible for SQL patching and backups.

**2\. Networking Layer (VNet)**

*   **Virtual Network (VNet):** Acts as the private cloud boundary.
*   **Subnets:**
    *   **Web Subnet:** Hosts the IIS VMs. Publicly accessible via the Load Balancer.
    *   **Data Subnet:** Hosts the SQL VM. **Strictly private**; no direct internet access.
*   **Network Security Groups (NSGs):** Acts as a virtual firewall.
    *   **Web NSG:** Allows HTTP/HTTPS traffic from the internet.
    *   **Data NSG:** Only allows traffic on port 1433 (SQL) _specifically_ from the Web Subnet IPs.

**3\. Storage Layer (Disks)**

*   **Managed Disks:** Persistent storage attached to the VMs to hold the OS, IIS logs, and the SQL Database files (.mdf/.ldf).