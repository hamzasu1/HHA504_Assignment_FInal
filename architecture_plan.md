# Architecture and Implementation plan

## Service Mapping
 
 | Layer | Service (Cloud) | Role in Solution | Related Assignment/Module |
 |---|---|---|---|
 | Storage | Azure Blob Storage | Store uploaded documents (authorization forms PDF, identification images, release packages, CSV reports) | Assignments 1 & 2 - file storage concepts; integration with web applications and VM networking for public access |
 | Compute - Web application | Azure App Service / Container Apps | Host Flask web application serving request intake forms, status dashboards, and API endpoints | Assignment 2 - Flask app on Cloud VM; networking, firewall rules, and exposing app to internet |
 | Database / SQL | Azure Database for MySQL | Store structured requests, status history, and user tables | Assignment 4 - MySQL on VM vs managed services; deploy MySQL, use SQLAlchemy and pandas |
 | Security | Azure Key Vault | User authentication via SSO and secrets management (keys) | Assignments 1–3 - SSH keys, firewall rules |
 | Analytics | Azure ML notebook | Trend analysis , predictive turnaround times, executive reporting, visual analytics, performance tracking | Assignments 4–5 - data visualizations, utilizing AI |

## Data Flow Narrative
### Step 1 - Request Intake
- ROI specialists access Flask web application deployed on Azure app service 
- User authenticates via Azure AD and fills out intake form with requestor/patient details
- User uploads authorization form PDF and supporting documents via web form

### Step 2 - Document storage and validation
- Flask calls Azure Function to validate the requests data
- Function validates required fields and returns structured JSON
- documents uploaded to Azure Blob Storage in container 'roi-documents'
- Files metadata (blob, size, time) recorded for database storage

### Step 3 - Database
- Request metadata inserted into Azure Database for MySQL utilizing SQLAlchemy
- Tables populated: "Requests", "DocumentReferences" (blobs), 
"Status_History" (audit trail)
- Connection established via connection string from Azure Key Vault

### Step 4 - Status Management and Updates
- Supervisor views dashboard showing all "New" requests queried from MySQL
- Assigns requests to each individual specialist, status update triggers database writes to "requests" and "status history"
- Specialists update status through flask routes - goes from "New" to "In Progress" to "Complete"

### Step 5 - Scheduled Reporting
- Azure Function runs daily at 6AM EST
- Connects to MySQL, queries for overdue requests
- Utilize pandas to make a CSV report, uploads this report to Blob storage container "ROI reports"

### Step 6 - Dashboard
- Flask queries MySQL for statistics (status updates, priority requests and workflow of each specialist)
- Dashboard creates visualizations (piecharts, bar charts, list of priority requests)


## Security, Identity and Governance basics
All sensitive credentials are stored in a azure key vault and accessed via managed identity. (.env) will be utilized to store credentials. and will never be commited via Git. Production Flask app and Azure Functions retrieve secrets at runtime from Key Vault using Default Azure Credential(). Database connection strings, Blob Storage access keys, and Application programming interface (API) tokens are all vault-managed. This eliminates the security risks of hardcoded credentials or accidentally committed secrets.

## Access Controls (RBAC)
Azure Active Directory manages user authentication via SSO. Two AD groups control application access: "ROI-Specialists" (create/update requests, view assigned work only) and "ROI-Supervisors" (view all requests, assign work, access reports). Flask enforces role-based permissions at the route level-supervisors can access "/dashboard" and "/assign-request" while specialists have restricted views. At the database level, two MySQL users are created: "roi_app_user" (read/write for Flask) and "roi_report_user" (read-only for reporting functions), following least-privilege principles from Assignment 4.

## PHI protection 
To avoid exposing real PHI in development, all testing uses synthetic data generated via Python Faker (fake patient names, MRNs, requestor information). Development and staging environments exist in separate Azure subscriptions, completely isolated from any production data. In production, Azure Database for MySQL encrypts data at rest. A Business Associate Agreement (BAA) is required with Microsoft for HIPAA compliance, and all services are deployed in HIPAA-compliant Azure regions.


## Cost and Operational Strategy
The most expensive components are compute (Azure App Service for always-on Flask hosting) and the managed MySQL database. This saves significant operational effort versus self-managed VMs as shown in Assignment 4. Storage costs are negligible for document volumes. Serverless functions are ideal for the daily aging report and validation API (occasional usage, no idle costs), while the Flask web app requires always-on App Service for instant user access without start delays. To maintain a student budget, the strategy leverages Azure for Students credits, uses App Service Free tier during development, keeps MySQL on Basic tier, maximizes free tiers for Functions, Key Vault, and monitoring services. Most critically, applying cost hygiene practices from Assignment 1 (immediately stopping or deleting unused development resources prevents unexpected charges from abandoned VMs or databases.
