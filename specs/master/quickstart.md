# Quick Start: AugentisLabs MVP Local Development

**Phase**: 1 (Weeks 3-4)  
**Purpose**: Get backend + frontend running locally in 5 minutes  
**Prerequisites**: Docker, Python 3.11+, PostgreSQL 15, Git, uv package manager

---

## 1. Prerequisites

### Required Software

```bash
# Check versions (minimum required)
python --version    # 3.11 or later
uv --version        # Latest
docker --version    # Latest
git --version       # Latest
```

### Install (macOS)

```bash
# Using Homebrew
brew install python@3.11 uv docker git

# Or install uv (standalone installer - recommended)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Or download from official sites:
# - Python: https://www.python.org/downloads/
# - Docker Desktop: https://www.docker.com/products/docker-desktop
# - Git: https://git-scm.com/
```

### Install (Windows)

```powershell
# Using Chocolatey (if installed)
choco install python docker-desktop git

# Or install uv using PowerShell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Or download from official sites
```

### Install (Linux - Ubuntu/Debian)

```bash
sudo apt-get update
sudo apt-get install -y python3.11 python3.11-venv python3-pip docker.io docker-compose git

# Install uv (recommended)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Add user to docker group
sudo usermod -aG docker $USER
```

---

## 2. Clone & Setup Repository

```bash
# Clone the repository
git clone https://github.com/augentislabs/augentislabs.git
cd augentislabs

# Install dependencies using uv
uv sync

# Backend uses uv for environment management
cd backend && uv sync && cd ..
```

---

## 3. Environment Configuration

### Create .env.local (Backend)

```bash
# backend/.env.local

# Database (Supabase PostgreSQL)
DATABASE_URL="postgresql://postgres:your_password@localhost:5432/augentislabs_dev"

# Supabase Auth
SUPABASE_URL="https://your-project.supabase.co"
SUPABASE_ANON_KEY="your-anon-key"
SUPABASE_SERVICE_ROLE_KEY="your-service-key"

# OpenAI API
OPENAI_API_KEY="sk-..."
OPENAI_MODEL="gpt-4-turbo"
OPENAI_MAX_TOKENS=4000

# Anthropic API
ANTHROPIC_API_KEY="sk-ant-..."
ANTHROPIC_MODEL="claude-3-5-sonnet-20241022"

# LangGraph & LLM Configuration
LLM_RETRY_MAX_ATTEMPTS=3
LLM_RETRY_BACKOFF_MS=1000
LLM_TIMEOUT_MS=300000
LLM_TOKEN_BUDGET_PER_VENTURE=5000
LLM_COST_ALERT_THRESHOLD_PERCENT=80

# Langfuse (Agent Observability)
LANGFUSE_PUBLIC_KEY="your-public-key"
LANGFUSE_SECRET_KEY="your-secret-key"
LANGFUSE_HOST="https://cloud.langfuse.com"

# File Storage (Supabase Storage)
SUPABASE_STORAGE_BUCKET="artifacts"

# RabbitMQ Message Broker
RABBITMQ_URL="amqp://guest:guest@localhost:5672/"

# Backend Server
PYTHONENV="development"
PORT=8000
API_URL="http://localhost:8000"
WORKERS=4

# Logging
LOG_LEVEL="debug"
```

### Create .env.local (Frontend)

```bash
# frontend/.env.local

# API Configuration
NEXT_PUBLIC_API_URL="http://localhost:8000/api"
NEXT_PUBLIC_SUPABASE_URL="https://your-project.supabase.co"
NEXT_PUBLIC_SUPABASE_ANON_KEY="your-anon-key"

# Environment
NEXT_PUBLIC_ENVIRONMENT="development"
```

---

## 4. Start PostgreSQL with Docker Compose

```bash
# Start PostgreSQL, Redis, pgAdmin
docker-compose up -d

# Verify services are running
docker ps

# View logs
docker-compose logs -f postgres
```

### Docker Compose Services

```yaml
# docker-compose.yml
version: "3.8"
services:
  postgres:
    image: postgres:15
    environment:
      POSTGRES_DB: augentislabs_dev
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  pgadmin:
    image: dpage/pgadmin4:latest
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@augentislabs.local
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"

volumes:
  postgres_data:
```

### Connect pgAdmin

1. Open http://localhost:5050
2. Login: admin@augentislabs.local / admin
3. Add Server:
   - Host: postgres
   - Port: 5432
   - Username: postgres
   - Password: postgres

---

## 5. Initialize Database Schema

```bash
# Navigate to backend
cd backend

# Create and apply Alembic migrations
alembic upgrade head

# Seed test data (optional)
python scripts/seed.py

# View database with pgAdmin
# Open: http://localhost:5050
```

### What Gets Created

- **organizations** table (test org)
- **users** table (test user)
- **ventures** table (test ventures)
- **artifacts** table
- **gate_decisions** table
- **telemetry** table
- **divergence_reports** table
- **audit_logs** table
- **cost_tracking** table
- All indexes and RLS policies

---

## 6. Start Backend Server

```bash
# Terminal 1: Backend
cd backend

# Install dependencies (if not done)
uv sync

# Start development server (with auto-reload)
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Server should be running on http://localhost:8000
# Check: curl http://localhost:8000/health
```

### Backend Startup Output

```
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     Started server process [12345]
INFO:     Waiting for application startup.
INFO:     Application startup complete
```

### Test Backend is Running

```bash
# In a new terminal
curl -s http://localhost:8000/health | jq

# Response:
# {
#   "status": "ok",
#   "timestamp": "2025-11-22T10:30:16Z"
# }
```

---

## 7. Start Frontend Server

```bash
# Terminal 2: Frontend
cd frontend

# Install dependencies (if not done)
npm install

# Start development server
npm run dev

# Server should be running on http://localhost:3001
# Open: http://localhost:3001
```

### Frontend Startup Output

```
> next dev

  ▲ Next.js 14.1.0
  - Local:        http://localhost:3001
  - Environments: .env.local

  ✓ Ready in 2.5s
```

### Access the App

1. Open browser: http://localhost:3001
2. Sign up or login
3. Create a new venture
4. Start Discover phase

---

## 8. Run Tests

### Backend Tests

```bash
# Terminal 3: Backend tests
cd backend

# Run all tests (watch mode)
uv run pytest --watch

# Run specific test file
uv run pytest tests/test_gates.py

# Run with coverage
uv run pytest --cov=app

# Run specific test with verbose output
uv run pytest tests/test_gates.py -v
```

### Frontend Tests

```bash
# Terminal 4: Frontend tests
cd frontend

# Run all tests (watch mode)
npm run test

# Run E2E tests with Playwright
npm run test:e2e

# Open Playwright Inspector
npx playwright test --debug
```

---

## 9. Testing the API

### Using cURL

```bash
# Create a venture
curl -X POST http://localhost:8000/api/ventures \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "name": "AI Time Tracking",
    "description": "AI-powered time tracking for remote teams"
  }'

# Start Discover phase
curl -X POST http://localhost:8000/api/ventures/{ventureId}/discover \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "idea_description": "AI tool that automatically tracks developer time spent on tasks"
  }'

# Get VRC score
curl -X GET http://localhost:8000/api/ventures/{ventureId}/vrc \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

### Using Postman

1. Download Postman: https://www.postman.com/downloads/
2. Import collection from `backend/docs/postman-collection.json`
3. Set environment variables (API_URL, JWT_TOKEN)
4. Test endpoints

### Using REST Client (VS Code Extension)

```http
### Create Venture
POST http://localhost:8000/api/ventures
Content-Type: application/json
Authorization: Bearer {{jwt_token}}

{
  "name": "AI Time Tracking",
  "description": "AI-powered time tracking"
}

### Start Discover Phase
POST http://localhost:8000/api/ventures/{{venture_id}}/discover
Content-Type: application/json
Authorization: Bearer {{jwt_token}}

{
  "idea_description": "AI tool for tracking developer time"
}
```

---

## 10. Common Commands

### Backend Commands

```bash
cd backend

# Development server with auto-reload
uv run uvicorn app.main:app --reload

# Run tests
uv run pytest
uv run pytest --watch
uv run pytest --cov=app

# Lint code with ruff
uv run ruff check .
uv run ruff format .

# Database operations
alembic upgrade head          # Apply migrations
alembic downgrade -1          # Rollback last migration
alembic revision --autogenerate -m "message"  # Create migration

# Type checking
uv run mypy app/
```

### Frontend Commands

```bash
cd frontend

# Development server
npm run dev

# Build for production
npm run build

# Start production build
npm start

# Run tests
npm test
npm run test:e2e

# Lint code
npm run lint
npm run lint:fix

# Type checking
npm run type-check
```

### Docker Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f postgres

# Access PostgreSQL CLI
docker-compose exec postgres psql -U postgres -d augentislabs_dev

# Rebuild images
docker-compose up -d --build
```

### RabbitMQ Management

```bash
# Access RabbitMQ Management UI
open http://localhost:15672

# Default credentials: guest / guest
# Monitor queues, connections, and message rates
```

---

## 11. Troubleshooting

### Port Already in Use

```bash
# Find process on port 3000 (backend)
lsof -i :3000
kill -9 <PID>

# Or find process on port 3001 (frontend)
lsof -i :3001
kill -9 <PID>

# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F
```

### Database Connection Failed

```bash
# Check PostgreSQL is running
docker-compose ps

# View PostgreSQL logs
docker-compose logs postgres

# Restart PostgreSQL
docker-compose restart postgres

# Reset database (WARNING: deletes all data)
docker-compose down -v
docker-compose up -d
```

### Node Modules Issues

```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install

# Or use npm ci for exact versions
npm ci
```

### Environment Variables Not Loading

```bash
# Check .env.local exists
ls -la backend/.env.local
ls -la frontend/.env.local

# Verify values are correct
cat backend/.env.local | grep DATABASE_URL

# Restart dev server after changing .env.local
# (Ctrl+C and npm run dev again)
```

### Prisma Migration Issues

```bash
# Reset migrations (WARNING: deletes data)
npx prisma migrate reset

# Create a new migration manually
npx prisma migrate dev --name describe_your_change

# View migration status
npx prisma migrate status
```

---

## 12. Next Steps

After getting local development running:

1. **Read the Architecture**: See `ARCHITECTURE.md` for system design
2. **Review API Contracts**: Check `specs/master/contracts/api-*.yaml`
3. **Understand Data Model**: See `DATA-MODEL.md` for schema details
4. **Run Tests**: Ensure all tests pass before making changes
5. **Create a Venture**: Test the full Discover→Develop flow

### Useful Development Resources

- **FastAPI Docs**: https://fastapi.tiangolo.com/
- **SQLAlchemy Docs**: https://docs.sqlalchemy.org/
- **Alembic Docs**: https://alembic.sqlalchemy.org/
- **pytest Docs**: https://docs.pytest.org/
- **ruff Docs**: https://docs.astral.sh/ruff/
- **LangGraph Docs**: https://langchain-ai.github.io/langgraph/
- **Langfuse Docs**: https://langfuse.com/docs/
- **OpenAPI Docs**: https://swagger.io/specification/
- **PostgreSQL Docs**: https://www.postgresql.org/docs/
- **RabbitMQ Docs**: https://www.rabbitmq.com/documentation.html

---

## 13. Production Deployment (Weeks 18-19)

When ready to deploy:

1. **Backend**: Deploy to Azure App Service with Docker container
2. **Kubernetes**: Deploy to AKS (Azure Kubernetes Service) for scalability
3. **Infrastructure**: Provision with Terraform for reproducible deployments
4. **Database**: Provision Supabase PostgreSQL instance
5. **Message Queue**: Deploy RabbitMQ for async task processing
6. **Monitoring**: Set up LGTM stack (Loki/Grafana/Tempo) for observability
7. **Email**: Configure Resend for production email sending
8. **Environment Variables**: Set in Azure Key Vault
9. **CI/CD**: Configure GitHub Actions for automated deployment

See `IMPLEMENTATION.md` Phase 6 for production deployment details.

---

## Done! 🎉

You now have AugentisLabs running locally. Start testing the Discover phase!

```bash
# Open the app
open http://localhost:3001

# Or on Linux/Windows
start http://localhost:3001
xdg-open http://localhost:3001  # Linux
```

For issues or questions, check:

- `.specify/memory/constitution.md` - Governance framework
- `specs/master/research.md` - Architecture decisions
- `specs/master/data-model.md` - Database schema
