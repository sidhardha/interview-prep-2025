# High-Availability AWS Architecture for a Next.js Frontend + Spring Boot Backend Application (Internal-Only Access)

---

## 1. Environments

### Development
- **Single-AZ standalone deployment**
- Cost-optimized infrastructure
- Minimal redundancy

### Production
- **High-availability configuration**
- Single-region deployment
- Full redundancy for critical components

---

## 2. Application Components

### Frontend
- Next.js application
- Served via NGINX reverse proxy

### Backend
- Spring Boot API

---

## 3. Infrastructure Components

### Development Environment
- **EC2 instances (t3.xlarge)**
  - 4 vCPU, 16GB RAM
  - Ubuntu 20.04 LTS
  - 20GB EBS storage
- Application Load Balancer (internal only)
- Security Groups for ports 22, 80, 443, 8080 (restricted to internal IP ranges or VPN)
- Private VPC 
- Route 53 for internal DNS management

### Production Environment
- **EC2 instances (m5.2xlarge)**
  - 8 vCPU, 32GB RAM
  - Ubuntu 20.04 LTS
  - 20GB EBS storage with snapshots
- Auto Scaling Group (min 2, max 4 instances)
- Application Load Balancer (internal, not internet-facing) with SSL termination
- Security Groups for ports 22, 80, 443, 8080 (restricted to internal networks or VPN)
- Private VPC with public and private subnets (no direct internet access to app)
- Route 53 for internal DNS management with health checks
- AWS Certificate Manager for SSL/TLS

---

## 4. CI/CD Requirements

- Bitbucket Pipelines integration
- SSH key pairs for secure deployment
- Environment variables management
- Automated Docker image builds
- Separate deployment workflows for dev/prod

---

## 5. Security Requirements

- VPC security groups (restrict access to internal/VPN IPs)
- Network ACLs
- SSH key-based authentication
- SSL/TLS encryption
- Private subnet isolation for backend services
- No public endpoints for application access; access only via VPN, Direct Connect, or bastion hosts

---

## 6. Architecture Diagrams

### Development Environment

```
[Internal Users/VPN]
    |
[Route 53 Internal DNS]
    |
[Internal App Load Balancer]
    |
[EC2 (t3.xlarge): NGINX/Next.js + Spring Boot]
    |
[Private Subnet in VPC]
```

### Production Environment

```
[Internal Users/VPN/Direct Connect]
    |
[Route 53 Internal DNS + Health Checks]
    |
[Internal App Load Balancer (SSL Termination)]
    |
[Auto Scaling Group: EC2 (m5.2xlarge)]
    |           |
[Private Subnet] [Private Subnet]
    |                |
[NGINX/Next.js]   [Spring Boot API]
```

- **Frontend** (NGINX/Next.js) and **Backend** (Spring Boot) both reside in private subnets.
- **EBS Snapshots** for backup.
- **Security Groups & NACLs** for network security.
- **AWS Certificate Manager** for SSL/TLS.
- **No public internet access** to application endpoints.

---

## 7. Implementation Details

- Use **Docker Compose** or ECS/EKS for container orchestration.
- Use **User Data scripts** or Ansible for provisioning EC2 instances.
- **Bitbucket Pipelines** automate Docker builds, push images to ECR, and deploy via SSH or SSM.
- **Environment variables** managed via AWS Systems Manager Parameter Store or Secrets Manager.
- **Monitoring & Logging:** Use CloudWatch for logs and metrics.

---

## 8. Best Practices

- Use **Infrastructure as Code** (Terraform, CloudFormation) for repeatable deployments.
- Enable **CloudWatch Alarms** for instance health and scaling.
- Regularly **rotate SSH keys** and secrets.
- Use **EBS snapshots** for data backup and disaster recovery.
- Apply **least privilege** on all IAM roles and policies.
- Ensure all access is via secure internal channels (VPN, Direct Connect, or bastion).

---