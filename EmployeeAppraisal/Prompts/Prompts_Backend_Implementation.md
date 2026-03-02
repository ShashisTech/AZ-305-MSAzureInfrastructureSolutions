1.  There is one use-case in Word Format with name "eAppraisal-Case-Study" inside Docs Folder. Can you explain what it says –
2.  What are the major WEB API we can create with this use case
3.  Can you design the database, tables and their relationship between every required tables for these Web API ? We are using SQL Server for Database and Tables.
4.  I am using Microsoft SQL Server Management Studio. Please create ConnectionString to connect with the database with the following information, IF YOU need additional information to create DB ConnectionString, please let me know: ServerName: (localdb)\\MSSQLLocalDB Authentication: WindowsAuthentication UserName: CTS\\2057793 Database Name: EAppraisalApp
5.  Create Entity Framework .EDMX File in .Net Core using Database-First Approach Method. ServerName: (localdb)\\MSSQLLocalDB Authentication: WindowsAuthentication UserName: CTS\\2057793 Database Name: EAppraisalApp

**dotnet ef dbcontext scaffold Name=DefaultConnection Microsoft.EntityFrameworkCore.SqlServer -o Models**

**OR**

**dotnet ef dbcontext scaffold "Name=DefaultConnection" Microsoft.EntityFrameworkCore.SqlServer**

Prompts for API Development

**1\. Authentication & Authorization**

*   **POST /api/auth/login  
    User login, returns JWT or session token.**

I’ve grouped endpoints by domain and highlighted methods, key fields, role scope, and sample payloads.

With respect to Use-Case EAppraisal, Here is the faithful to the flows, pages, and fields in the document (HR → Appraiser → Appraisee, lockout rule, unified pages, CTC entries, etc.).

Base: /api/v1

Auth: JWT/Bearer (recommended)

Roles: HR, APPRAISER (Manager), APPRAISEE (Employee)

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

1) Authentication & Accounts

POST /auth/login

• Purpose: Authenticate user and return access token; enforce lockout after 3 invalid attempts; locked users receive specific error until HR unlocks.

• Request

JSON

{ "username": "jdoe", "password": "••••••" }

• Response

JSON

{ "token": "eyJhbGci...", "expiresIn": 3600, "role": "APPRAISER" }

Show more lines

• Error cases: 401 invalid\_credentials, 423 account\_locked (after 3 bad tries).

**2\. Employee Management (HR Section)**

**GET /api/employees  
List all employees.**

Generate HttpGet API Method to get List All Employees.

The path should be : GET /api/employees

**POST /api/employees  
Add a new employee.**

Generate HttpPost API Method to add a new employee.

The path should be : **POST /api/employees**

**GET /api/employees/{id}  
Get employee details.**

Generate HttpGet API Method to get employee details.

The path should be : GET /api/employees/{id}

**PUT /api/employees/{id}  
Update employee information.**

Generate HttpPut API Method to update employee information.

The path should be : PUT /api/employees/{id}

*   **DELETE /api/employees/{id}  
    Remove employee (if allowed).**

**\---**

**3\. Appraisal Management**

**GET /api/appraisals/upcoming  
List employees with upcoming appraisals (HR/Manager).**

Generate HttpGet API Method to get list of all employees with Upcoming Appraisals. Their Roles in Database are defined here:

HR : HR

Manager: Manager

Employees: SA

This Rest API be consumed by HR/Manager only. HR and Manager only can get the list of all the employees with Upcoming Appraisals.

The path should be : GET /api/appraisals/upcoming

**POST /api/appraisals/assign  
Assign appraisal to manager (HR).**

Generate HttpPost API Method to Assign Appraisal to Manager. Their Roles in Database are defined here:

For HR : HR

For Manager: Manager

For Employees: SA

This HTTPPOST Rest API will be consumed by HR only. HR will use this HTTPPOST API method to Assign Appraisal to Manager.

While assigning appraisal to manager, EmployeeId, ManagerId, HRId, Status, NextAppraisalDate and CreatedAt fields are required to insert into the database that’s how HR with that Id can assign specific employee with his/her employeeId to Manager with his/her EmployeeId

The path should be : POST /api/appraisals/assign

**GET /api/appraisals/in-process  
List appraisals currently in process.**

Generate HttpGet API Method to List Appraisals currently In-Process. Their Roles in Database are defined here:

For HR : HR

For Manager: Manager

For Employees: SA

This HTTPGET Rest API will be consumed by HR and Manager. HR and Manager will use this HTTPGET API method to List Appraisals currently In-Process.

The path should be : GET /api/appraisals/in-process

**\---**

**4\. Appraisal Workflow**

**GET /api/appraisals/{id}  
Get appraisal details. – HR, Manager, Employee**

Generate HttpGet API Method to Get Appraisal Details, allowed access for HR, Manager and Employee. Their Roles in Database are defined here:

For HR : HR

For Manager: Manager

For Employees: SA

This HTTPGET Rest API will be consumed by HR, Manager and Employee. HR, Manager and Employee will use this HTTPGET API method to Get Appraisal Details.

The path should be : GET /api/appraisals/{id}

HR’s Roles and Responsibilities: View any appraisal.

Manager’s Roles and Responsibilities: View appraisals assigned to them.

Employee’s Roles and Responsibilities: View their own appraisal.

**PUT /api/appraisals/{id}/manager-comments  
Manager adds comments. – Manager**

Generate HttpPut API Method for Manager to Adds his/her comments, allowed access for Manager only. Their Roles in Database are defined here:

For Manager: Manager

This HTTPPUT Rest API will be consumed by Manager. Manager will use this HTTPPUT API method to Add his/her comments.

The path should be : PUT /api/appraisals/{id}/manager-comments

Manager’s Roles and Responsibilities: Add their comments.

**PUT /api/appraisals/{id}/employee-comments  
Employee adds feedback. – Employee**

Generate HttpPut API Method for Employee to Adds his/her comments, allowed access for Emplyee only. Their Roles in Database are defined here:

For Employees: SA

This HTTPPUT Rest API will be consumed by Employee. Employee will use this HTTPPUT API method to Add his/her comments.

The path should be : PUT /api/appraisals/{id}/employee-comments

Employee’s Roles and Responsibilities: Add their comments.

**PUT /api/appraisals/{id}/status  
Manager updates appraisal status, promotion, and CTC. – HR, Manager**

Generate HTTPPUT API Method to update appraisal status, promotion and CTC, allowed access for HR and Manager. Their Roles in Database are defined here:

For HR : HR

For Manager: Manager

This HTTPPUT Rest API will be consumed by HR and Manager. HR and Manager will use this HTTPPUT API method to Update appraisal status, promotion and CTC.

The path should be : PUT /api/appraisals/{id}/status

HR’s Roles and Responsibilities: May trigger or update status via

Manager’s Roles and Responsibilities: Update status, promotion, and CTC.

**5\. User Profile (Employee Section)**

**GET /api/profile  
Get logged-in user's profile.**

Generate HttpGet API Method to Get logged-in user's profile.

The path should be : GET /api/profile

*   **PUT /api/profile  
    Update profile (with restrictions on certain fields).**

**\---**

**6\. Security & Account Management**

*   **POST /api/account/lock  
    Lock user account after failed logins.**
*   **POST /api/account/unlock  
    Manually unlock account (admin/HR).**

**\---**

**7\. Supporting Endpoints**

*   **GET /api/departments  
    List departments.**
*   **GET /api/reports-to  
    List reporting managers.**