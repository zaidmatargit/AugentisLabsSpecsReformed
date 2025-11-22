# Quick Start: AugentisLabs MVP Local Development

**Phase**: 1 (Weeks 3-4)  
**Purpose**: Get backend + frontend running locally in 5 minutes  
**Prerequisites**: Docker, Node.js 20.x, PostgreSQL 15, Git

---

## 1. Prerequisites

### Required Software

```bash
# Check versions (minimum required)
node --version    # v20.x or later
npm --version     # v10.x or later
docker --version  # Latest
git --version     # Latest
```

### Install (macOS)

```bash
# Using Homebrew
brew install node docker git

# Or download from official sites:
# - Node.js: https://nodejs.org/
# - Docker Desktop: https://www.docker.com/products/docker-desktop
# - Git: https://git-scm.com/
```

### Install (Windows)

```powershell
# Using Chocolatey (if installed)
choco install nodejs docker-desktop git

# Or download from official sites
```

### Install (Linux - Ubuntu/Debian)

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs docker.io docker-compose git
sudo usermod -aG docker $USER  # Add user to docker group
```

---

## 2. Clone & Setup Repository

```bash
# Clone the repository
git clone https://github.com/augentislabs/augentislabs.git
cd augentislabs

# Install dependencies
npm install

# Install backend dependencies
cd backend && npm install && cd ..

# Install frontend dependencies
cd frontend && npm install && cd ..
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

# AWS S3 (for artifact storage)
AWS_REGION="us-east-1"
AWS_ACCESS_KEY_ID="your-key"
AWS_SECRET_ACCESS_KEY="your-secret"
AWS_S3_BUCKET="augentislabs-dev-artifacts"

# Backend Server
NODE_ENV="development"
PORT=3000
API_URL="http://localhost:3000"

# Logging
LOG_LEVEL="debug"
```

### Create .env.local (Frontend)

```bash
# frontend/.env.local

# API Configuration
NEXT_PUBLIC_API_URL="http://localhost:3000/api"
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

# Generate Prisma Client
npx prisma generate

# Run migrations (create tables)
npx prisma migrate dev --name init

# Seed test data (optional)
npx prisma db seed

# View database in Prisma Studio
npx prisma studio
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
npm install

# Start development server (watches for changes)
npm run dev

# Server should be running on http://localhost:3000
# Check: curl http://localhost:3000/health
```

### Backend Startup Output

```
[Nest] 12345  - 11/22/2025, 10:30:15 AM     LOG [NestFactory] Starting Nest application...
[Nest] 12345  - 11/22/2025, 10:30:15 AM     LOG [InstanceLoader] ConfigModule dependencies initialized +25ms
[Nest] 12345  - 11/22/2025, 10:30:15 AM     LOG [InstanceLoader] PrismaService dependencies initialized +1ms
[Nest] 12345  - 11/22/2025, 10:30:15 AM     LOG [InstanceLoader] AuthModule dependencies initialized +2ms
[Nest] 12345  - 11/22/2025, 10:30:16 AM     LOG [InstanceLoader] VenturesModule dependencies initialized +1ms
[Nest] 12345  - 11/22/2025, 10:30:16 AM     LOG [InstanceLoader] GatesModule dependencies initialized +1ms
[Nest] 12345  - 11/22/2025, 10:30:16 AM     LOG [InstanceLoader] RoutingModule dependencies initialized +0ms
[Nest] 12345  - 11/22/2025, 10:30:16 AM     LOG [NestApplication] Nest application successfully started +15ms
API Server running on http://localhost:3000
```

### Test Backend is Running

```bash
# In a new terminal
curl -s http://localhost:3000/health | jq

# Response:
# {
#   "status": "ok",
#   "timestamp": "2025-11-22T10:30:16.000Z"
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
npm run test:watch

# Run specific test file
npm run test src/modules/gates/gates.service.spec.ts

# Run with coverage
npm test:cov
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
curl -X POST http://localhost:3000/api/ventures \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "name": "AI Time Tracking",
    "description": "AI-powered time tracking for remote teams"
  }'

# Start Discover phase
curl -X POST http://localhost:3000/api/ventures/{ventureId}/discover \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "idea_description": "AI tool that automatically tracks developer time spent on tasks"
  }'

# Get VRC score
curl -X GET http://localhost:3000/api/ventures/{ventureId}/vrc \
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
POST http://localhost:3000/api/ventures
Content-Type: application/json
Authorization: Bearer {{jwt_token}}

{
  "name": "AI Time Tracking",
  "description": "AI-powered time tracking"
}

### Start Discover Phase
POST http://localhost:3000/api/ventures/{{venture_id}}/discover
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

# Development server
npm run dev

# Build for production
npm run build

# Run tests
npm test
npm test:watch
npm test:cov

# Lint code
npm run lint
npm run lint:fix

# Database operations
npx prisma migrate dev --name migration_name
npx prisma db seed
npx prisma studio

# View database schema
npx prisma generate
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

- **Prisma Docs**: https://www.prisma.io/docs/
- **NestJS Docs**: https://docs.nestjs.com/
- **Next.js Docs**: https://nextjs.org/docs
- **LangGraph Docs**: https://langchain-ai.github.io/langgraph/
- **OpenAPI Docs**: https://swagger.io/specification/
- **PostgreSQL Docs**: https://www.postgresql.org/docs/

---

## 13. Production Deployment (Weeks 18-19)

When ready to deploy:

1. **Backend**: Deploy to Vercel serverless or AWS Lambda
2. **Frontend**: Deploy to Vercel (next-gen platform)
3. **Database**: Provision Supabase PostgreSQL instance
4. **Environment Variables**: Set in production dashboard
5. **CI/CD**: Configure GitHub Actions for automated deployment

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
