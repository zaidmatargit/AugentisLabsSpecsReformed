# Tech Stack Update Summary

**Date**: November 23, 2025  
**Updated by**: AI Assistant  
**Status**: ✅ Complete

---

## Overview

Complete tech stack migration across all specification and documentation files from Node.js/NestJS to Python/FastAPI with updated DevOps, monitoring, and infrastructure approach.

---

## Files Updated

### 1. **research.md** ✅

**Changes:**

- Updated Architecture Decision Summary table with new tech stack choices
- Added FastAPI as backend framework (replaced NestJS)
- Added Python 3.11+ with uvicorn (replaced Node.js)
- Added uv package manager (Python package management)
- Added SQLAlchemy + Alembic for ORM/migrations (replaced Prisma)
- Added pytest for testing (replaced Jest)
- Added ruff for Python linting (replaced ESLint)
- Added Langfuse for LLM observability
- Added LGTM stack (Loki/Grafana/Tempo) for monitoring (replaced Datadog)
- Added Resend for email service
- Added Azure App Service for user application hosting
- Added AKS (Azure Kubernetes Service) for container orchestration
- Added Terraform for Infrastructure as Code
- Added RabbitMQ for message broker
- Updated conclusion to reflect all new tech stack components

### 2. **quickstart.md** ✅

**Changes:**

- Updated prerequisites from Node.js 20.x to Python 3.11+
- Replaced npm with uv package manager
- Updated installation instructions for all platforms (macOS, Windows, Linux)
- Changed environment setup from NestJS to FastAPI
- Updated .env.local configuration to include:
  - Langfuse observability keys
  - RabbitMQ connection string
  - Supabase Storage (replaced AWS S3)
  - PYTHONENV and uvicorn settings
  - Removed Node.js specific variables
- Updated database initialization from Prisma to Alembic migrations
- Updated backend server startup from NestJS to uvicorn
- Updated test runner from Jest to pytest
- Updated linting from ESLint/Prettier to ruff
- Updated Docker Compose services section (RabbitMQ management UI added)
- Updated troubleshooting section for Python-specific issues
- Updated deployment instructions to include Azure App Service, AKS, and Terraform
- Updated development resources links to FastAPI, SQLAlchemy, Alembic, pytest, ruff, Langfuse, RabbitMQ docs

### 3. **spec.md** ✅

**Changes:**

- Updated Section 7.1 (Backend) technology stack:
  - Runtime: Python 3.11+ with uvicorn (from Node.js LTS)
  - Framework: FastAPI (from NestJS)
  - Package Manager: uv (added)
  - ORM: SQLAlchemy (from Prisma)
  - Migrations: Alembic (added)
  - Testing: pytest (from Jest/Supertest)
  - Linting: ruff (added)
  - Observability: Langfuse (added)
  - Storage: Supabase Storage (from AWS S3)
  - Message Broker: RabbitMQ (added)
  - Hosting: Azure App Service (from Vercel serverless)
- Updated Section 7.3 (Infrastructure) stack:

  - File Storage: Supabase Storage (from AWS S3)
  - Hosting: Azure App Service (from Vercel)
  - Container Orchestration: AKS (added)
  - Infrastructure as Code: Terraform (added)
  - Message Queue: RabbitMQ (added)
  - Email: Resend (added)
  - Monitoring: LGTM Stack (added)
  - Observability: Langfuse (added)
  - Secrets: Azure Key Vault (from Vercel KV)

- Updated code generation reference to FastAPI + Next.js (from NestJS)
- Updated deployment reference to Azure App Service + AKS with Terraform

### 4. **plan.md** ✅

**Changes:**

- Updated Technical Context section with:
  - Language/Version: Python 3.11+, FastAPI, uvicorn
  - Primary Dependencies: FastAPI (backend), SQLAlchemy + Alembic (ORM)
  - Package Manager: uv
  - Testing: pytest with pytest-asyncio
  - Linting/Formatting: ruff and black
  - Agent Observability: Langfuse
  - Monitoring: LGTM stack
  - Message Broker: RabbitMQ
  - Email Service: Resend
  - Infrastructure: Terraform, AKS
  - Container Hosting: Azure App Service
  - Removed: Node.js, NestJS, Prisma, Jest, ESLint, Prettier, Vercel, Datadog references

### 5. **tasks.md** ✅

**Changes:**

- Updated Definition of Done (DoD) criteria:
  - Code Quality: ruff formatting (from ESLint/Prettier)
  - Testing: pytest (from Jest)
  - Linting: ruff lint and ruff format (from ESLint/Prettier)
- Updated Sprint 1-2 deliverables:
  - Docker Compose services: RabbitMQ instead of Redis
  - Infrastructure: Added Azure App Service and AKS provisioning with Terraform
- Updated US Setup 1.1 (Backend & Frontend initialization):
  - Backend: FastAPI with Python 3.11+ (from NestJS with TypeScript)
  - Configuration: pyproject.toml with ruff, black, pytest (from ESLint/Prettier/Jest)
  - Environment variables include Langfuse and RabbitMQ
  - Scripts: uv sync, make dev, pytest, ruff check
- Updated US Setup 1.2 (Docker environment):
  - Services: RabbitMQ + PostgreSQL (removed Redis)
  - Added RabbitMQ Management UI reference
  - Database script location: alembic/versions (from prisma/)
- Updated US Setup 1.3 (CI/CD):
  - Backend CI: ruff checks + pytest (from ESLint + Jest)
- Updated US Foundation 2.1 (Database Schema):
  - ORM: SQLAlchemy models (from Prisma schema)
  - Migrations: Alembic (from Prisma)
  - Dependencies: pyproject.toml includes SQLAlchemy, Alembic, psycopg2

---

## Tech Stack Comparison

| Component             | Previous          | New               | Notes                        |
| --------------------- | ----------------- | ----------------- | ---------------------------- |
| **Runtime**           | Node.js 20.x      | Python 3.11+      | Better LLM ecosystem         |
| **Backend Framework** | NestJS 10.x       | FastAPI           | Async-first, type-safe       |
| **Language**          | TypeScript 5.x    | Python 3.11+      | LLM libraries                |
| **Package Manager**   | npm               | uv                | Faster, more reliable        |
| **ORM**               | Prisma 5.x        | SQLAlchemy 2.x    | Declarative, version control |
| **Migrations**        | Prisma migrate    | Alembic           | Fine-grained control         |
| **Database**          | PostgreSQL 15     | PostgreSQL 15     | Same (no change)             |
| **Testing**           | Jest 29.x         | pytest            | Powerful fixtures            |
| **Linting**           | ESLint + Prettier | ruff + black      | Faster, comprehensive        |
| **File Storage**      | AWS S3            | Supabase Storage  | Managed, simpler             |
| **Observability**     | (None)            | Langfuse          | LLM-specific tracing         |
| **Monitoring**        | Datadog           | LGTM Stack        | Open-source, cost-effective  |
| **Message Broker**    | (None)            | RabbitMQ          | Async task processing        |
| **Email Service**     | (None)            | Resend            | Modern email API             |
| **App Hosting**       | Vercel            | Azure App Service | Managed containers           |
| **Container Orch.**   | (None)            | AKS               | Azure Kubernetes             |
| **Infrastructure**    | (None)            | Terraform         | Version-controlled IaC       |

---

## Frontend Changes

**Note**: Frontend stack remains unchanged:

- Framework: Next.js 14.x (no change)
- Language: TypeScript 5.x (no change)
- Styling: Tailwind CSS 3.x (no change)
- Testing: Playwright (no change)

The API endpoint changes from `:3000` (Node) to `:8000` (Python/FastAPI).

---

## Key Technical Updates

### Backend Architecture

- **Async Support**: FastAPI provides native async/await support (vs NestJS which requires middleware)
- **Type Safety**: Python type hints + Pydantic models (vs TypeScript)
- **Dependencies**: LangGraph, LangChain, OpenAI libraries better supported in Python ecosystem
- **Performance**: uvicorn ASGI server provides sub-100ms latency targets

### Database Management

- **SQLAlchemy**: Declarative ORM with better relationship mapping
- **Alembic**: Version-controlled migrations with rollback support
- **No Prisma Client**: Removed Node.js specific ORM, simpler dependency tree

### Development Workflow

- **uv**: ~10-100x faster dependency resolution than pip
- **ruff**: Single linter + formatter (replaces ESLint + Prettier)
- **pytest**: Rich plugin ecosystem, better async support, faster test discovery

### Infrastructure

- **Azure Cloud**: App Service for managed containers, AKS for production scale
- **Terraform**: Infrastructure as Code for reproducible deployments
- **RabbitMQ**: Message broker for async task processing (e.g., agent jobs)
- **Open-Source Monitoring**: LGTM stack reduces Datadog costs

### Observability

- **Langfuse**: LLM-specific tracing, agent debugging, cost tracking
- **LGTM Stack**: Loki (logs), Grafana (dashboards), Tempo (distributed tracing)
- **Better Debugging**: Agent execution traces, LLM token usage, latency analysis

---

## Migration Path

When implementing:

1. **Backend Setup** (Week 1)

   - Initialize FastAPI project with uv
   - Configure SQLAlchemy models and Alembic migrations
   - Set up pytest configuration

2. **Database** (Week 1-2)

   - Create Alembic migration scripts
   - Run migrations on PostgreSQL
   - Verify RLS policies work with SQLAlchemy

3. **API Endpoints** (Week 2-4)

   - Implement FastAPI routes with dependency injection
   - Configure Langfuse instrumentation
   - Add pytest integration tests

4. **Infrastructure** (Week 2-3)

   - Write Terraform configurations for Azure
   - Deploy test environment to Azure App Service
   - Set up Docker image builds

5. **Async Processing** (Week 4-5)

   - Configure RabbitMQ connection
   - Implement Celery or similar task queue
   - Add background job processing for agent workflows

6. **Monitoring** (Week 5-6)
   - Deploy LGTM stack (self-hosted or managed)
   - Configure Langfuse agent tracing
   - Set up Grafana dashboards

---

## Testing Strategy

### Backend (pytest)

```bash
uv run pytest tests/ -v --cov=app
```

### Frontend (unchanged)

```bash
npm run test:e2e
```

### Integration (E2E across both)

```bash
npm run test:e2e  # Tests FastAPI backend + Next.js frontend
```

---

## Deployment Commands (Updated)

```bash
# Local development
make dev  # Starts both FastAPI and Next.js

# Docker
docker-compose up -d

# Terraform deployment to Azure
cd terraform/
terraform init
terraform plan
terraform apply
```

---

## Next Steps

1. ✅ **Specifications Updated** - All documentation reflects new tech stack
2. 🔲 **Environment Setup** - Install Python 3.11+, uv, Docker
3. 🔲 **Backend Initialization** - Create FastAPI project structure
4. 🔲 **Database Schema** - Implement SQLAlchemy models
5. 🔲 **Testing Framework** - Configure pytest and fixtures
6. 🔲 **Infrastructure** - Write Terraform modules for Azure
7. 🔲 **CI/CD** - Update GitHub Actions for Python/ruff/pytest
8. 🔲 **Deployment** - Test Azure App Service deployment

---

## Questions & Support

For questions about the tech stack update:

- FastAPI: https://fastapi.tiangolo.com/docs
- SQLAlchemy: https://docs.sqlalchemy.org/
- Alembic: https://alembic.sqlalchemy.org/
- pytest: https://docs.pytest.org/
- ruff: https://docs.astral.sh/ruff/
- Azure: https://docs.microsoft.com/azure/
- Terraform: https://www.terraform.io/docs

---

**Update Complete** ✅  
All specification files have been successfully updated with the new Python/FastAPI tech stack.
