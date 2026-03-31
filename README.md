# 🏕️ Wildmark

> Discover state parks. Find things to do nearby. Share tips with fellow explorers.

Wildmark is a full-stack web application for discovering US state and national parks, exploring nearby activities, and reading community reviews and tips from other campers. Built and owned by a solo developer as a production AWS portfolio project.

**Live:** [wildmark.dev](https://wildmark.dev)

---

## 🗺️ What It Does

- **Search & browse** state and national parks by state or region
- **Park detail pages** — amenities, fees, hours, trails, and nearby activities
- **User accounts** — sign up, log in, save favorite parks
- **Community comments** — leave tips and reviews on parks
- **Nearby explorer** — activities, towns, and points of interest within a configurable radius

---

## 🏛️ Architecture

Wildmark is built on AWS using a security-first, three-tier architecture. All application and database tiers are private — only the load balancer is publicly exposed.

```
User → Route 53 → CloudFront → WAF → ALB (public subnet)
                                         ↓
                               ECS Fargate (private subnet)
                                         ↓
                               RDS PostgreSQL (DB subnet — private)
```

![Architecture Diagram](docs/architecture/system-overview.png)

### Infrastructure Highlights

| Layer | Service | Notes |
|---|---|---|
| DNS | Route 53 | Hosted zone for wildmark.dev |
| CDN | CloudFront + S3 | Static React frontend, global edge delivery |
| Security | WAF + Shield | OWASP Top 10 rules, DDoS protection |
| Load Balancing | ALB | HTTPS only, HTTP → HTTPS redirect |
| Compute | ECS Fargate | Serverless containers, no EC2 to manage |
| Database | RDS PostgreSQL | Multi-AZ, private subnet, never publicly accessible |
| Auth | Cognito | Managed user pools, no rolling your own auth |
| Secrets | SSM Parameter Store | Zero hardcoded credentials anywhere |
| Encryption | KMS | Data encrypted at rest and in transit |
| Monitoring | CloudWatch + CloudTrail | Full audit logging from day one |
| IaC | Terraform | 100% infrastructure as code, modular |

### VPC Design

- **CIDR:** `10.0.0.0/16` across 2 Availability Zones
- **Public subnets:** ALB only
- **Private subnets:** ECS Fargate tasks
- **DB subnets:** RDS — no internet route, isolated tier
- **NAT Gateway:** Outbound-only internet access for private subnets
- **VPC Flow Logs:** Enabled — all traffic logged to CloudWatch

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React |
| Backend | Python / FastAPI |
| Database | PostgreSQL (RDS) |
| Infrastructure | Terraform |
| Containers | Docker / ECS Fargate |
| CI/CD | GitHub Actions |
| Cloud | AWS (us-east-1) |

---

## 🔒 Security Design

Security is a first-class citizen in Wildmark — not an afterthought. This project is a portfolio piece for a Cloud Security Architect path, so every design decision documents the reasoning.

- **Zero public exposure** of app or database tiers — ALB is the only public entry point
- **IAM least privilege** — every service has its own role with minimum required permissions, no wildcards
- **No hardcoded credentials** — all secrets in SSM Parameter Store, injected at runtime
- **Encryption everywhere** — KMS for RDS and S3 at rest, ACM/TLS for all in-transit data
- **WAF rules** — OWASP Top 10 coverage on all inbound traffic
- **CloudTrail enabled from day one** — full management and data event audit log
- **Cognito for auth** — no custom auth implementation, MFA supported
- **VPC Flow Logs** — network traffic logged for anomaly detection

---

## 📁 Project Structure

```
wildmark/
├── frontend/          # React app
├── backend/           # FastAPI (Python) app
├── infrastructure/    # Terraform — all AWS resources
│   └── modules/       # vpc, ecs, rds, alb, cloudfront, waf, iam, cognito, monitoring
├── docs/
│   ├── architecture/  # Diagrams
│   ├── adr/           # Architecture Decision Records
│   └── writeup.md     # Portfolio case study
├── .github/workflows/ # CI/CD — deploy frontend, backend, terraform plan
├── docker-compose.yml # Local dev environment
└── README.md
```

---

## 🚀 Running Locally

### Prerequisites
- Docker + Docker Compose
- Node.js 18+
- Python 3.11+
- AWS CLI (configured)

### Setup

```bash
git clone https://github.com/[your-handle]/wildmark.git
cd wildmark
cp .env.example .env         # Fill in local dev values
docker-compose up --build    # Starts frontend, backend, and local Postgres
```

- Frontend: http://localhost:3000
- Backend API: http://localhost:8000
- API Docs: http://localhost:8000/docs

---

## ⚙️ Deployment

Infrastructure is fully managed with Terraform. CI/CD runs via GitHub Actions on push to `main`.

```bash
cd infrastructure/environments/prod
terraform init
terraform plan
terraform apply
```

GitHub Actions workflows:
- `frontend-deploy.yml` — Build React → upload to S3 → invalidate CloudFront cache
- `backend-deploy.yml` — Build Docker → push to ECR → update ECS service
- `terraform-plan.yml` — Run `terraform plan` on all PRs

---

## 📊 Data Sources

- **National Park Service API** — [nps.gov/developer](https://www.nps.gov/subjects/developer/api-documentation.htm) — free, no auth required, covers all US national parks
- Community data (comments, favorites) stored in RDS PostgreSQL

---

## 🗺️ Roadmap

- [x] Domain registered (wildmark.dev)
- [x] Architecture designed
- [x] Repo scaffolded
- [ ] Phase 1 — VPC, RDS, ECS, ALB, CloudFront foundation
- [ ] Phase 2 — Core product (park search, auth, comments)
- [ ] Phase 3 — Security hardening (WAF, KMS, CloudTrail alarms)
- [ ] Phase 4 — Terraform IaC, architecture writeup, portfolio polish

---

## 👤 About

Built by Jordan Brong — DevOps Engineer with 7–10 years of experience, currently pursuing a Cloud Security Architect path.

- Certs: AWS Cloud Practitioner ✅ | SAA-C03 (in progress) | SCS-C02 (queued)
- [LinkedIn](https://linkedin.com/in/[your-handle])

---

## 📄 License

MIT — see [LICENSE](LICENSE)
