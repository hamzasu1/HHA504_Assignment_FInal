# Use Case : Release of Information Request Intake and Status Tracker

## A Concrete Healthcare scenario

## The Challenge

Hospitals receive Release of Information (ROI) requests from multiple
channels including fax, email, patient portals and walk-ins. Facilities track these requests utilizing manual methods such as paper logs, spreadsheets or disconnected systems.

Problems addressed:
- Lost requests
- difficulty tracking turnaround times
- Inability to provide a status for requestors.
- lack of metrics for process improvement
- compliance risks when requests go over a certain timeframe.

## Solution proposed

A cloud based ROI request intake and status tracking system that centralizes request intake, automates status tracking and stores documents in a secure location.  The system provides real time visibility info workflows. The primary users would be the ROI specialists who would receive and process requests and their supervisors. They are tasked to log requests, provide status updates in-person/phone calls, upload records and documents required, and view workload. The ROI supervisors are tasked to oversee the ROI specialists and workflow. They are tasked to monitor requests that have exceeded a timeframe, track metrics ,identify bottlenecks and ensure compliance throughout the team. The final primary user would be the requestors. This would include patients , attorneys, insurance companies, 3rd party companies. They would need to submit their requests online, check for statuses and receive notifications.

## Data sources 

1. Request metadata (structured data)
- Requestors information (name, contact, organization)
- Patients information (name, Date of Birth, MRN)
- Request details (when the request was received, what records are being requested, and the purpose of the request)
- Status information (current status, which staff is working on the request, due date of request)
- Timestamp (created, updated, complete)

2. The supporting documentation (unstructured file)
- Authorization forms (scanned documents, image scans, PDF)
- Identification documentation (drivers licence, passport, insurance card)
- Correspondence (email attachments, cover sheets)
- Release packages (the final package that will be sent to the requestor)


## Basic workflow

Step 1: Request intake
- Receiving a request through phone, fax, email, or walk in
- Specialist logs into the Flask web application
- Fills out the intake form with requestor and patients details
- Uploads the authorization form and all other supporting documents
- Generates a request ID and sets status

Step 2: Data Storage
- Request metadata saved to azure sql database
- Uploaded documents saved to Azure Blob Storage with unique file paths
- File references linked to request record in database
- Timestamp recorded for SLA tracking

Step 3:  Assignment & processing the requests
- Supervisor reviews all new requests in a dashboard
- Each request is assigned to a specific ROI specialist
- Status is changed to in progress
- Prepares the final package and uploads the final pdf

Step 4: Quality assurance/ Quality check
- Quality assurance reviewer checks release package and authorization
- Updates status to "Complete" or notes "Requires correction"
- System timestamps completion
- Notification is then sent to the requester

Step 5: Reporting/Monitoring
- Azure function runs every weekday at 6AM
- Generates "aging report"
- Provides a total requests by status
- List of overdue/priority requests
- Turnaround times for each request
- Report is stored in blob storage and sent to the supervisors

Step 6: Dashboard and Metrics
- Supervisors have access to the dashboards showing:
- Requests by status
- List of priority requests that need to be completed as soon as possible
- Aging requests (0-7 days, 8-15 days, 16-30 days, 31+ days)
- Recent activity of ROI users
- Ability to filter based on dates, specialist tasks and status


This will provide benefits like centralized tracking, improved turnaround times to release requests, compliance alerts, audit trails, accessibility (web based- can possibly expand to other hospitals), security and can be more cost effective. This use case demonstrates a practical healthcare IT problem that benefits significantly from cloud services, combining storage, compute, database, and serverless capabilities to modernize a critical operational workflow.

This use case leverages multiple cloud services learned throughout the course:
- Assignment 1: Understanding VM infrastructure and cloud fundamentals
- Assignment 2: Flask web application for request intake and dashboard
- Assignment 3: Azure Functions for scheduled aging reports (timer-triggered)
- Assignment 4: Azure SQL Database for structured request metadata
- Assignment 5: Optional NoSQL (Cosmos DB) for flexible metadata schemas


