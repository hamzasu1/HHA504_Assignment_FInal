# Reflection: ROI Request Tracker Project

## Areas of High Confidence
I feel most confident about the database design and service selection based on course assignments. The relational schema (Requests, Status_History, DocumentReferences) directly applies SQLAlchemy and pandas concepts from Assignment 4, and the decision to use managed MySQL over VM-hosted follows the clear operational benefits we experienced comparing both approaches. The security architecture using Azure AD for authentication and Key Vault for secrets management builds naturally on credential handling patterns from Assignments 1-4 (.env files, SSH keys, firewall rules). The Flask application structure builds directly on Assignment 2's deployment experience - I understand how ports, networking, and public access work from hands-on practice.

## Areas of Uncertainty
I'm less confident about Azure Functions integration details, particularly around database connection management in serverless environments. While Assignment 3 taught us to deploy HTTP-triggered functions and handle JSON, the production concerns around connection pooling (how do we efficiently connect to MySQL without exhausting connections?) and error handling for timer triggers are unclear.

## Alternative Architecture Considered
I considered a serverless-first architecture using AWS Lambda functions with DynamoDB instead of Flask on App Service with MySQL. I chose not to pursue this because the ROI request data is fundamentally relational (requests link to documents and status history), making MySQL from Assignment 4 a better fit than a NoSQL database. Additionally, maintaining a single Flask application is simpler than managing multiple Lambda functions for a student project.

## Future enhancements
- EHR implementation - Connect to Epic/Cerner systems to retrieve patient records programmatically instead of manual chart pulls. Use Azure API Management to mediate EHR connections. This would reduce processing times.
- AI predictive analysis - Train Azure ML model on historical data to predict turnaround time for new requests based on request type, patient record volume, and assigned specialist workload. Alert supervisors when a request is predicted to go overdue before it happens.

