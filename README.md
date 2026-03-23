# E-Commerce 3-Tier Architecture on AWS (Production-Ready)

## Overview

This project implements a production-grade 3-tier architecture on AWS, designed with a focus on high availability, scalability, security, and maintainability. The system is built using industry best practices and follows a layered approach to infrastructure design.

The architecture separates concerns into distinct layers:

* Presentation layer (Web tier)
* Application layer (Backend/API tier)
* Data layer (Database tier)

Each layer is independently scalable, secured, and deployed across multiple Availability Zones to ensure fault tolerance.

The project was developed incrementally, with each layer implemented and validated before progressing to the next. This approach reflects real-world engineering workflows and ensures reliability at every stage.

---

## Architecture Summary

The system is deployed across a primary AWS region with a disaster recovery design in a secondary region.

### Global Layer

* Amazon Route 53 for DNS resolution and failover routing
* Amazon CloudFront for content delivery and caching
* AWS WAF for application-layer protection

### Primary Region (Active)

* Virtual Private Cloud (VPC) with public and private subnets across multiple Availability Zones
* Internet-facing Application Load Balancer
* Web tier running on EC2 instances in Auto Scaling Groups
* Internal Application Load Balancer for backend routing
* Application tier running on EC2 instances in Auto Scaling Groups
* Amazon RDS for relational database storage

### Secondary Region (Standby - Design)

* Mirrored infrastructure for disaster recovery
* Cross-region database replication
* Route 53 failover configuration

---

## Key AWS Services

* Amazon Route 53 – DNS and failover routing
* Amazon CloudFront – Content delivery network
* AWS WAF – Web application firewall
* Elastic Load Balancing – Public and internal load balancing
* Amazon EC2 – Compute instances for web and application tiers
* Amazon RDS – Managed relational database
* AWS Systems Manager – Secure instance access (no SSH exposure)
* Amazon CloudWatch – Monitoring and alerting

---

## Architecture Characteristics

* Multi-AZ deployment for high availability
* Horizontal scaling using Auto Scaling Groups
* Strict network isolation using private subnets
* Layered security using Security Groups and WAF
* Stateless web and application tiers
* Internal service-to-service communication via private load balancing
* Designed for infrastructure-as-code deployment using Terraform

---

## Project Structure

```bash
.
├── app/                # Application source code (web, API, database schema)
├── architecture/       # Final architecture diagrams
├── diagrams/           # Iterative diagram versions
├── docs/               # Supporting documentation
│   ├── architecture.md
│   ├── deployment-guide.md
│   ├── failover-strategy.md
│   └── traffic-flow.md
├── monitoring/         # Monitoring and alert configurations
├── scripts/            # Deployment and utility scripts
├── security/           # IAM roles, security groups, WAF configuration
├── terraform/          # Infrastructure as Code
│   ├── environments/
│   └── modules/
```

---

## Implementation Approach

The system was implemented in structured phases to ensure correctness and testability.

---

### Phase 1 – Networking (VPC)

A custom Virtual Private Cloud was created with:

* Public subnets for load balancers and NAT Gateways
* Private subnets for application and database tiers
* Internet Gateway for external connectivity
* NAT Gateways for outbound internet access from private subnets

Validation included confirming that:

* Public resources were accessible externally
* Private resources had outbound access but no inbound exposure

---

### Phase 2 – Load Balancing

An internet-facing Application Load Balancer was configured to distribute incoming traffic to the web tier.

Validation included:

* Successful routing of HTTP requests to backend instances
* Health check validation and failover behavior

---

### Phase 3 – Web Tier

The web tier was deployed using EC2 instances managed by an Auto Scaling Group.

* Launch Templates defined instance configuration
* Instances deployed across multiple Availability Zones
* Stateless design to support scaling

Validation included:

* Load distribution across instances
* Instance replacement on failure

---

### Phase 4 – Auto Scaling

Auto Scaling policies were configured based on CPU utilization.

Validation included:

* Simulated load generation
* Automatic scale-out and scale-in behavior
* Monitoring scaling events via CloudWatch

---

### Phase 5 – Application Tier

The backend application tier was introduced:

* Internal Application Load Balancer for service routing
* Separate Auto Scaling Group for application servers
* Node.js-based API service

Validation included:

* Successful communication between web and application tiers
* Internal-only accessibility of backend services

---

### Phase 6 – Database Layer

A managed relational database was deployed using Amazon RDS.

* Private DB subnet group
* Restricted access via Security Groups
* Database schema initialization

Validation included:

* Successful application queries to the database
* Verification of no public database access

---

### Phase 7 – Security Hardening

Security controls were implemented to enforce least privilege:

* Removal of SSH access to EC2 instances
* Use of AWS Systems Manager for access control
* Tight Security Group rules between tiers
* Integration of AWS WAF for application protection

Validation included:

* Direct access to instances blocked
* Only load balancer endpoints accessible

---

### Phase 8 – Performance Optimization

A CDN layer was added using CloudFront.

* Reduced latency via edge caching
* Integration with WAF for centralized security

Validation included:

* Improved response times
* Cache hit verification

---

### Phase 9 – Disaster Recovery Design

A secondary region architecture was designed for failover:

* Cross-region database replication
* Route 53 failover routing policy
* Standby infrastructure for recovery scenarios

---

## Security Model

The system enforces strict isolation between layers:

| Layer         | Access Control                     |
| ------------- | ---------------------------------- |
| Load Balancer | Public access (HTTPS only)         |
| Web Tier      | Accessible only from Load Balancer |
| App Tier      | Accessible only from Web Tier      |
| Database      | Accessible only from App Tier      |

Additional measures:

* No public IPs on application instances
* No SSH exposure
* IAM roles with least privilege access
* Web application firewall for threat protection

---

## Testing Strategy

The project emphasizes functional and operational testing:

* Load testing to validate scaling behavior
* Connectivity testing between tiers
* Security validation (access restrictions)
* Health check verification
* Failover scenario simulation (design level)

---

## Known Constraints

Due to free-tier limitations:

* Single-AZ RDS deployment (Multi-AZ documented but not enabled)
* Limited instance sizes
* Optional HTTPS configuration not fully implemented

---

## Future Enhancements

* Full Multi-AZ database deployment
* CI/CD pipeline integration
* HTTPS with managed certificates
* Containerization and orchestration (EKS)
* Blue/green or canary deployment strategies

---

## Conclusion

This project demonstrates the design and implementation of a scalable, secure, and production-ready cloud architecture on AWS. It reflects real-world engineering practices, including phased development, validation, and adherence to architectural best practices.

---

## How to Deploy

```bash
cd terraform/environments/dev
terraform init
terraform apply
```

---

For questions, feedback, or discussion, feel free to connect.
