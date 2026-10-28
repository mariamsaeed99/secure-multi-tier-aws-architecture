# Secure Multi-Tier Architecture on AWS

**Project:** AWS Certified Solutions Architect – Associate (SAA-C03) Graduation Project  
**Selected idea:** Project 8 — Secure Multi-Tier Architecture with GuardDuty, KMS & Security Hub  
**Status:** Architecture design and documentation proposal. AWS resources have **not** been deployed or tested.

## 1. Project Overview

This project proposes a secure three-tier architecture for a web application hosted on AWS. The design separates the public entry point, application compute layer, and database layer. It applies least-privilege access, encryption, secret management, logging, threat detection, and centralized security findings.

The goal is to reduce direct exposure of application and data resources, protect stored data, and improve visibility into suspicious activity.

## 2. Architecture Diagram

See [`architecture.png`](architecture.png).

### Request flow

1. A user sends an HTTPS request from the internet.
2. AWS WAF evaluates supported web requests for configured rules.
3. An Application Load Balancer in a public subnet routes allowed requests to the application layer.
4. Application resources in private subnets access the database only through explicitly permitted security-group rules.
5. The database is placed in private subnets and encrypted at rest.
6. IAM controls permissions; AWS KMS manages encryption keys; AWS Secrets Manager stores application secrets.
7. AWS CloudTrail records supported account/API activity. Amazon GuardDuty analyzes supported data sources for potential threats. AWS Security Hub can aggregate supported security findings.

**Important:** The diagram is a proposed design, not evidence of a deployed system. Service integration and network rules must be configured and tested before claiming they work.

## 3. Main AWS Services and Their Roles

| Service | Role in the proposed design |
|---|---|
| Amazon VPC | Isolated network boundary for application resources |
| Public and private subnets | Separate internet-facing entry resources from application and data resources |
| Security Groups | Stateful traffic controls between supported resources |
| Application Load Balancer (ALB) | HTTPS entry point and application traffic routing |
| AWS WAF | Filters web requests using configured rules; attach it to a supported resource such as an ALB |
| Amazon EC2 or another compute service | Runs the application in private subnets; final choice depends on scope |
| Amazon RDS | Managed relational database in private subnets |
| AWS IAM | Identity and permission management using least privilege |
| AWS KMS | Manages keys used for supported encryption integrations |
| AWS Secrets Manager | Stores secrets such as database credentials instead of hardcoding them |
| AWS CloudTrail | Records account activity and API events |
| Amazon GuardDuty | Detects potential threats from supported log and event sources |
| AWS Security Hub | Central view of supported security findings and security checks |
| AWS Config | Tracks resource configuration and evaluates supported rules |

## 4. Security Design Decisions

- **Least privilege:** Grant each identity or workload only the permissions it needs.
- **Private application and data layers:** Do not assign public IP addresses to application/database resources unless a justified requirement exists.
- **Restricted database access:** Permit database traffic only from the application security group on the database's required port.
- **Encryption at rest:** Enable supported encryption for database/storage resources and manage keys appropriately.
- **Encryption in transit:** Use HTTPS/TLS for user-facing traffic and protect service connections where supported.
- **Secret management:** Keep credentials out of source code and GitHub; store them in a secrets manager.
- **Logging and detection:** Enable and review CloudTrail, GuardDuty, and Security Hub integrations appropriate to the account and scope.
- **Monitoring and remediation:** Use AWS Config rules and carefully tested remediation procedures where required.

## 5. Example Network Access Policy

These are intended design rules, not deployed settings:

| Source | Destination | Intended access |
|---|---|---|
| Internet | ALB | HTTPS (TCP 443) |
| ALB security group | Application security group | Only the application's required listener port |
| Application security group | Database security group | Only the database engine's required port |
| Internet | Database | Deny direct access |
| Administrator | AWS management plane | Use approved identity, MFA, and least-privilege permissions |

Actual ports must match the selected application and database. Do not open broad inbound access such as `0.0.0.0/0` to database or administration ports.

## 6. High-Level Implementation Plan

1. Plan non-overlapping VPC CIDR ranges and public/private subnets across at least two Availability Zones if high availability is in scope.
2. Create route tables and only the required internet/NAT paths.
3. Configure security groups and verify the intended traffic path.
4. Deploy the load balancer and application resources, if implementation is required.
5. Deploy a private database and enable supported encryption.
6. Configure IAM roles, KMS permissions, and Secrets Manager access.
7. Enable appropriate CloudTrail logging and GuardDuty/Security Hub services.
8. Configure AWS Config rules and any remediation only after testing.
9. Validate connectivity, access restrictions, encryption, and alerts.
10. Document test evidence and estimated costs; remove test resources when finished.

## 7. Validation Checklist

Complete this checklist only after the corresponding test is actually performed.

- [ ] HTTPS access to the application is confirmed.
- [ ] Direct internet access to the database is blocked.
- [ ] Application-to-database access works only through the intended rule.
- [ ] Storage/database encryption settings are verified.
- [ ] IAM permissions have been reviewed for least privilege.
- [ ] Secrets are not committed to the repository.
- [ ] CloudTrail activity can be reviewed.
- [ ] GuardDuty and Security Hub are enabled/configured as required and findings are reviewed.
- [ ] AWS Config rules evaluate the intended resources.
- [ ] Costs are reviewed and temporary resources are removed.

## 8. Cost and Scope Notes

This design may incur charges for resources such as NAT Gateways, load balancers, EC2, RDS, logging, and some security services. Pricing and free-tier eligibility vary by region, account, usage, and date. Review the AWS Pricing Calculator and service pricing before deploying anything. Do not create resources just to produce a diagram.

For a time-limited academic submission, the diagram and documentation can describe the proposed architecture.

## 9. References

- AWS Well-Architected Framework: https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html
- Amazon VPC: https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html
- AWS IAM: https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html
- AWS KMS: https://docs.aws.amazon.com/kms/latest/developerguide/overview.html
- Amazon GuardDuty: https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html
- AWS Security Hub: https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html
- AWS CloudTrail: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html

