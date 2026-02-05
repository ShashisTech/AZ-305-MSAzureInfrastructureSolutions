**The Solution: Azure App Service (Web Apps)**

Azure App Service is a fully managed Platform-as-a-Service (PaaS) offering that is specifically optimized for hosting web applications like Tailwind Traders' .NET Core-based front end.

**Why I Decided on This Solution**

My decision is based on addressing the specific pain points identified in the case study:

**1\. Solving the "Idle Server" vs. "Performance Limit" Problem (Elasticity)**

*   **The Issue:** Tailwind Traders currently has idle servers during off-hours but hits performance ceilings during peak holiday sales.
*   **The Solution:** App Service provides **Autoscale** capabilities. We can configure the front end to automatically scale out (add more instances) based on CPU or memory usage during peak periods and scale in (remove instances) during off-hours. This ensures performance for those 1,750 hourly customers while eliminating the cost of unused resources.

**2\. Seamless Modernization for .NET Core**

*   **The Issue:** The team is moving from on-premises IIS servers and wants to modernize.
*   **The Solution:** Azure App Service has native, first-class support for **.NET Core**. Since the application already runs on IIS, it can be migrated to App Service with minimal code changes using the Azure App Service Migration Assistant, reducing migration risk and time-to-market.

**3\. High Availability in a Single Region**

*   **The Issue:** Legal requirements mandate staying in a single region, but high availability remains a concern.
*   **The Solution:** By using the **Premium or Standard tier** of App Service, Tailwind Traders can leverage **Zone Redundancy**. This spreads the app instances across different Availability Zones within that single region, ensuring the front end stays online even if one data center experiences a failure.

**4\. Operational Excellence (PaaS vs. IaaS)**

*   **The Issue:** The IT team is currently managing physical/virtual IIS servers, which involves patching, OS updates, and middleware maintenance.
*   **The Solution:** App Service is a **PaaS** offering. Microsoft handles the underlying VM management, OS patching, and IIS configuration. This allows the Tailwind Traders IT team to focus on the application and the product catalog rather than "server plumbing."

**Summary of Benefits for Tailwind Traders:**

*   **Cost Efficiency:** Only pay for the compute you need via autoscaling.
*   **Reliability:** Built-in health monitoring and zone-redundant deployment options.
*   **Scalability:** Handles the 1,750+ customers per hour without manual intervention.

By moving to Azure App Service, Tailwind Traders transforms a rigid, under-performing front end into a modern, elastic, and resilient web presence.