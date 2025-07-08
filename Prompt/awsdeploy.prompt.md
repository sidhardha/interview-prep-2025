Design a High-Availability AWS Architecture for a Next.js Frontend + Spring Boot Backend Application

Requirements:

1. Environments
Development:
- Single-AZ standalone deployment
- Cost-optimized infrastructure
- Minimal redundancy

Production:
- High-availability configuration
- Single-region deployment
- Full redundancy for critical components

2. Application Components
Frontend:
- Next.js application
- Containerized with Docker
- Served via NGINX reverse proxy

Backend:
- Spring Boot API
- Containerized with Docker
- JDK runtime included in container

3. Infrastructure Components

Development Environment:
- EC2 instances (t3.xlarge)
  * 4 vCPU, 16GB RAM
  * Ubuntu 20.04 LTS
  * 20GB EBS storage
- Application Load Balancer
- Security Groups for ports 22, 80, 443, 8080
- Private VPC with public subnet
- Route 53 for DNS management

Production Environment:
- EC2 instances (m5.2xlarge)
  * 8 vCPU, 32GB RAM
  * Ubuntu 20.04 LTS
  * 20GB EBS storage with snapshots
- Auto Scaling Group (min 2, max 4 instances)
- Application Load Balancer with SSL termination
- Security Groups for ports 22, 80, 443, 8080
- Private VPC with public and private subnets
- Route 53 for DNS management with health checks
- AWS Certificate Manager for SSL/TLS

4. CI/CD Requirements
- Bitbucket Pipelines integration
- SSH key pairs for secure deployment
- Environment variables management
- Automated Docker image builds
- Separate deployment workflows for dev/prod

5. Security Requirements
- VPC security groups
- Network ACLs
- SSH key-based authentication
- SSL/TLS encryption
- Private subnet isolation for backend services

Please provide architecture diagrams and implementation details based on these specifications.