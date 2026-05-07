# SerraStack

> AI-first Internal Developer Platform.  
> Describe your service in natural language — SerraStack interprets your intent, generates the scaffold, provisions cloud infrastructure and delivers everything ready to use.

---

## The problem

Developers lose hours before writing the first line of business logic.

Setting up Terraform, provisioning SQS queues, RDS databases, API Gateways, S3 buckets, IAM roles, configuring Docker, writing README files, creating OpenAPI specs, setting up CI/CD pipelines — all of this before a single feature is built.

SerraStack solves this.

---

## How it works

```
You describe your intent in natural language
        ↓
SerraStack interprets and builds an Infrastructure Plan
        ↓
You review the plan and the estimated monthly cost
        ↓
You approve — SerraStack provisions the cloud resources via Terraform
        ↓
You receive the project scaffold, README, OpenAPI, ADR and CI/CD pipeline
        ready to use
```

**Nothing is provisioned without your explicit approval.**

---

## What SerraStack generates

### Project scaffold
- **Java 21** — Spring Boot REST API, SQS Consumer, Kafka Producer, BFF, Batch Worker
- **Angular 17+** — Dashboard, Portal, Form Wizard, Data Table
- **React** — SPA, Dashboard, Form with validation
- **Go** — Lambda, Worker, CLI, REST API
- **Python** — Lambda, ETL, CLI, SQS Worker, Script

### Cloud infrastructure (Terraform)
- **AWS** — SQS, SNS, RDS, DynamoDB, S3, API Gateway, Lambda, ECS, IAM, VPC, ElastiCache, MSK
- **Azure** — Service Bus, PostgreSQL, Cosmos DB, Blob Storage, API Management, Functions, Container Apps
- **GCP** — Pub/Sub, Cloud SQL, Firestore, Cloud Storage, API Gateway, Cloud Functions, Cloud Run

### Artifacts delivered per project
- Project scaffold with folder structure and patterns
- `Dockerfile` + `docker-compose.yml` with LocalStack for local development
- `openapi.yml` or `asyncapi.yml`
- `README.md` with setup instructions
- GitHub Actions CI/CD pipeline
- ADR (Architecture Decision Record) for the main decisions
- Observability starter kit (health check, structured logs, correlationId, RED metrics)
- **Estimated monthly cost** before provisioning any resource

---

## Architecture

```
devforge-web (Angular)
        |
devforge-bff (Java — single entry point)
        |
┌───────────────────────────────────────────┐
│               Microservices               │
├──────────────┬──────────────┬─────────────┤
│ ai-           │ scaffold-ms  │ iac-ms      │
│ orchestrator  │              │             │
│               │ Java         │ Terraform   │
│ Interprets    │ Angular      │ AWS         │
│ intent        │ React        │ Azure       │
│ Chooses       │ Go           │ GCP         │
│ template      │ Python       │ Cost est.   │
├──────────────┴──────────────┴─────────────┤
│ catalog-ms              destroy-ms        │
│                                           │
│ Service catalog         Safe destroy      │
│ Maturity score          with approval     │
│ Owners & deps           Cost report       │
└───────────────────────────────────────────┘
        |
┌───────────────────────────────────────────┐
│           Storage & Infra                 │
│ PostgreSQL · DynamoDB · S3 · LLM (Bedrock)│
│ Terraform State (S3 + DynamoDB Lock)      │
└───────────────────────────────────────────┘
```

---

## Observability

Every service ships with:

- Structured JSON logs with `correlationId`
- RED metrics via Micrometer + Datadog (Rate, Errors, Duration)
- `/actuator/health` — liveness and readiness
- SLOs defined and monitored in Datadog

**Dashboards:**
- Platform Overview — requests, errors, LLM latency p99
- FinOps — estimated vs real cost, LLM token usage, idle resources
- Catalog Health — maturity score, services without tests, README or pipeline

---

## FinOps

Before provisioning any resource, SerraStack shows:

```
Infrastructure plan — estimated monthly cost:

  SQS (standard queue, 1M requests)     → $ 0.40 / month
  RDS PostgreSQL (db.t3.micro, 20GB)    → $ 15.00 / month
  API Gateway (1M requests)             → $ 3.50 / month
  S3 (50GB storage + requests)          → $ 1.20 / month
  IAM roles                             → $ 0.00 / month
  ──────────────────────────────────────────────────────
  Estimated total                       → $ 20.10 / month

Proceed? [Approve / Reject]
```

All provisioned resources are tagged for cost tracking per service and squad.

---

## Service Catalog

Every service created by SerraStack is registered in the catalog with:

- Owner and squad
- Provisioned cloud resources with ARNs
- REST endpoints and OpenAPI link
- Queues and topics produced and consumed
- Dependencies on other services
- **Maturity score** — automated evaluation (README, Dockerfile, tests, pipeline, logs, OpenAPI, ADR, SLOs)

---

## Pipeline

Every generated project includes a GitHub Actions pipeline:

```
build → unit tests → security scan (Trivy) → docker build → deploy dev
```

- PRs without passing tests do not merge
- CRITICAL vulnerabilities block the deploy
- Production deploy always requires manual approval
- Terraform plan published as a PR comment before apply

---

## Development philosophy

SerraStack is built with **Spec-Driven Development (SDD)**:

```
Explore → Plan → Spec → Code → Commit
```

- OpenAPI spec is written before any endpoint is implemented
- Every architectural decision has an ADR
- PRs explain the architectural decision, not just what changed
- Nothing is provisioned without human approval in the loop

---

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Angular 17+ (standalone components) |
| Backend | Java 21 + Spring Boot 3.x |
| Scaffold languages | Java · Angular · React · Go · Python |
| Cloud providers | AWS · Azure · GCP |
| IaC | Terraform |
| AI | Amazon Bedrock (Claude) |
| Observability | Datadog |
| Pipeline | GitHub Actions |
| Local dev | Docker + LocalStack |
| Databases | PostgreSQL · DynamoDB · S3 |

---

## Local setup

```bash
# Clone the repository
git clone https://github.com/your-username/serrastack.git
cd serrastack

# Start local infrastructure (LocalStack + PostgreSQL)
docker-compose up -d

# Run a microservice
./mvnw spring-boot:run -pl scaffold-ms

# Run tests
./mvnw test -pl scaffold-ms
```

**Required environment variables:**
```env
AWS_ENDPOINT=http://localhost:4566
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=test
AWS_SECRET_ACCESS_KEY=test
DB_URL=jdbc:postgresql://localhost:5432/serrastack
DB_USER=serrastack
DB_PASSWORD=serrastack
DATADOG_API_KEY=your-key-here
DD_ENV=local
```

---

## Project status

| Week | Focus | Status |
|---|---|---|
| 1 | MVP — main flow (AWS + Java scaffold) | 🔄 In progress |
| 2 | CI/CD pipeline + Datadog observability | ⏳ Planned |
| 3 | FinOps + Service catalog + Destroy | ⏳ Planned |
| 4 | Multilanguage scaffold (Angular, React, Go, Python) | ⏳ Planned |
| 5 | Multi-cloud (Azure, GCP) + final polish | ⏳ Planned |

---

## About

Built by a backend developer from Petrópolis, Brazil — with a background in banking, real estate and a passion for platform engineering, developer experience and AI-first systems.

Inspired by internal developer platforms like Backstage and Fury (Mercado Livre).

---

*SerraStack — From intent to cloud.*
