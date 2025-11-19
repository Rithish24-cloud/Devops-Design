# DevOps Design – System Architecture (UI + API + Database)

This document explains how to design, secure, and scale a UI, API, and Database system using AWS cloud services.

---

# 📌 Architecture Diagram
(Exported from draw.io — included in the repository)

---

# 🧩 1. UI (Frontend) Architecture

## 🏗️ Hosting
- UI static files (React/Angular/Vue) are hosted in **Amazon S3**.
- **Amazon CloudFront CDN** delivers the UI globally with improved performance.

## 🔀 Routing
User → CloudFront → S3

## 📈 Scaling
- S3 and CloudFront automatically scale based on traffic.
- UI is fully stateless → no servers required.

## 🔐 Security
- HTTPS enabled on CloudFront.
- S3 bucket policy allows access only through CloudFront.
- AWS WAF can be added for DDOS and OWASP protection.

---

# 🧩 2. API (Backend) Architecture

## 🖥️ Compute Layer
- Backend API runs on **Amazon ECS (Fargate)** or **EC2 Auto Scaling Group**.
- **Application Load Balancer (ALB)** distributes traffic to backend tasks.

## 🔀 Traffic Flow
User → CloudFront → API → ALB → ECS Tasks

## 📈 Scaling
- ECS Service Auto Scaling triggers based on CPU, memory, or request count.

## 🔐 Secrets Management
- Secrets stored securely in **AWS Secrets Manager**.
- No hardcoded credentials.
- IAM roles ensure secure service-level access.

## 🔗 Database Communication
- API tasks run in private subnets.
- Security Groups allow only API → database communication.

---

# 🧩 3. Database Layer (MySQL/RDS)

## 🗄️ High Availability
- **Amazon RDS (MySQL)** with Multi-AZ enabled.
- Supports automatic failover.

## 📚 Scaling
- Read Replicas to handle read-heavy workloads.
- Vertical scaling by upgrading instance sizes.

## 🛡️ Security
- Data encrypted using AWS KMS.
- Database runs in a private subnet.
- No public internet access.

## 📦 Backup Strategy
- Automated daily snapshots.
- Point-in-Time Recovery (PITR) enabled.
- Retention period: 7–30 days.

## 🔄 Migration Strategy
- Database schema versioning with **Flyway/Liquibase**.
- Zero-downtime deployments using rolling migrations.

---

# 🔁 4. CI/CD Pipeline (Design Only)

## ⚙️ Pipeline Tool
**GitHub Actions** or Jenkins.

## 🛠️ CI/CD Flow
```
Code Push → Build → Test → Security Scan → Deploy → Health Check
```

### ✔️ Triggers
- Push to `main` branch.
- Pull Request merge.

### ✔️ Steps
1. Install dependencies.
2. Run unit and integration tests.
3. Build and package application.
4. Upload artifacts to S3 or ECR.
5. Deploy updated stack to ECS.
6. Run post-deployment health checks via ALB.

### ✔️ Promotion Strategy
- Environments: **dev → staging → production**.
- Manual or automated approval gates.

---

# 📁 Repository Structure
```
repo/
 ├── README.md
 ├── architecture.pdf
 ├── diagrams/
 │    └── system-architecture.drawio
```

---

# ✅ Summary
This design ensures that the UI, API, and database layers are fully secure, scalable, and cost-efficient. The system leverages managed AWS services for high availability, auto-scaling, and automated deployment workflows.
