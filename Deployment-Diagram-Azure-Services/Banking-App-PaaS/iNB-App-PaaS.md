**Azure PaaS Deployment Diagram for iNB**

**Architectural Components & Requirements Mapping**

The deployment is organized into three logical areas: **Identity**, **Compute**, and **Data**, reflecting the banking operations and security needs of the document.

**1\. Identity & Security (The Guard)**

*   **Microsoft Entra ID (Azure AD):** Handles the **Customer Registration** and **Logon** requirements.
*   **Azure Key Vault:** Securely stores secrets like database connection strings, ensuring that sensitive info isn't hardcoded in the .NET application.
*   **Account Lockout Policy:** Configured at the Entra ID level to satisfy the requirement of locking accounts after **three consecutive invalid attempts**.

**2\. Compute Tier (The Engine)**

*   **Azure App Service (Web App):**
    *   Hosts the **ASP.NET** application.
    *   Manages the **Home Page** logic, **Mini/Detailed Statements**, and **Bill Payments**.
    *   Provides the **Unified Look-and-Feel** using shared Master Pages/Layouts to ensure consistent headers, footers, and left-navigation.
*   **WebJobs or Azure Functions:** Handles the **Asynchronous Tasks** like calculating **Overdraft Charges** daily or scheduling future **Bill Payments**.

**3\. Data Tier (The Vault)**

*   **Azure SQL Database (PaaS):** \* Stores all **Customer Data Fields** (First Name, Username, Cell, etc.).
    *   Manages **Account Types** (Savings vs. Current) and their respective logic, such as **Interest Rates** and **Overdraft Facilities**.
    *   Tracks **Cheque Deposit Statuses** (Not received, Sent for Clearance, Cleared, Bounced).

**Key Workflow Implementation**

*   **Customer Registration:** Customer registers $\\rightarrow$ Data is stored in **Azure SQL** with status 'Pending' $\\rightarrow$ Banker approves $\\rightarrow$ **Azure Communication Services** (or similar) sends the confirmation letter/email.
*   **Daily Reconciliation:** A scheduled **Azure Function** runs at the end of the day to calculate interest on savings and overdraft charges on current accounts.
*   **Cheque Processing:** The Banker portal (hosted on the same App Service) updates the cheque status, which is immediately reflected in the customer's online view.

**Why this PaaS Architecture?**

1.  **Reduced Maintenance:** By using **App Service**, you eliminate the need for manual **IIS patching**.
2.  **Scalability:** The banking app can scale out automatically during high-traffic periods (e.g., when many customers are paying bills).
3.  **High Availability:** Azure SQL Database provides a **99.99% SLA**, ensuring customers can perform "day-to-day banking operations" without downtime.