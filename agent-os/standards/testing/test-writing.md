## Test coverage best practices

### AugentisLabs TDD-First Discipline (Constitution Principle III)

**CRITICAL: Test-Driven Development is MANDATORY**
- **RED**: Write FAILING tests FIRST before any implementation
- **GREEN**: Implement code to make tests pass
- **REFACTOR**: Improve code while keeping tests green
- **No Implementation Before Tests**: Test Generator agent creates failing tests; developers implement to pass them
- **Quality Gate Requirement**: MDP gate requires ≥85% with all P1 tests passing

### Test-First Workflow

#### Step 1: RED - Write Failing Tests First
```typescript
// tests/unit/services/venture.service.spec.ts
describe('VentureService', () => {
  describe('createVenture', () => {
    it('should create a new venture with valid data', async () => {
      const createDto = {
        name: 'Test Venture',
        description: 'A test venture for e-commerce',
        phase: Phase.DISCOVER
      };

      const result = await service.createVenture(tenantId, createDto);

      expect(result.id).toBeDefined();
      expect(result.name).toBe(createDto.name);
      expect(result.tenantId).toBe(tenantId);
    });
  });
});

// Run test - it should FAIL (RED)
// Output: Error: service.createVenture is not a function
```

#### Step 2: GREEN - Implement to Pass Tests
```typescript
// backend/src/modules/ventures/venture.service.ts
@Injectable()
export class VentureService {
  async createVenture(tenantId: string, dto: CreateVentureDto): Promise<Venture> {
    return this.prisma.venture.create({
      data: {
        ...dto,
        tenantId,
        id: randomUUID()
      }
    });
  }
}

// Run test - it should PASS (GREEN)
```

#### Step 3: REFACTOR - Improve Code
```typescript
// Refactor while keeping tests green
@Injectable()
export class VentureService {
  async createVenture(tenantId: string, dto: CreateVentureDto): Promise<Venture> {
    this.validateTenantAccess(tenantId);

    return this.prisma.venture.create({
      data: {
        ...dto,
        tenantId
      }
    });
  }

  private validateTenantAccess(tenantId: string): void {
    if (!tenantId) throw new UnauthorizedException('Tenant context missing');
  }
}

// Run test - it should still PASS (GREEN)
```

### Test Types & Coverage Requirements

#### Unit Tests (Jest)
Test individual functions, services, and utilities in isolation.

**Coverage Requirements**:
- **Critical Paths**: ≥80% coverage (5D phases, quality gates, multi-tenant isolation)
- **Services**: ≥70% coverage
- **Utilities**: ≥60% coverage
- **Controllers**: ≥50% coverage (focus on integration tests instead)

**Location**: `backend/tests/unit/` or alongside source files

```typescript
// tests/unit/services/quality-gate.service.spec.ts
describe('QualityGateService', () => {
  let service: QualityGateService;
  let prisma: PrismaService;

  beforeEach(() => {
    const module = await Test.createTestingModule({
      providers: [QualityGateService, PrismaService]
    }).compile();

    service = module.get(QualityGateService);
    prisma = module.get(PrismaService);
  });

  describe('calculateVRCGate', () => {
    it('should return score ≥76% when all criteria met', async () => {
      const ventureId = randomUUID();

      // Mock required evidence
      jest.spyOn(prisma.requirement, 'findMany').mockResolvedValue([
        { evidenceTier: EvidenceTier.E2, isCritical: true },
        { evidenceTier: EvidenceTier.E3, isCritical: true }
      ]);

      const result = await service.calculateVRCGate(ventureId);

      expect(result.score).toBeGreaterThanOrEqual(0.76);
      expect(result.passed).toBe(true);
    });

    it('should fail when critical evidence tier < E2', async () => {
      const ventureId = randomUUID();

      jest.spyOn(prisma.requirement, 'findMany').mockResolvedValue([
        { evidenceTier: EvidenceTier.E0, isCritical: true } // Insufficient
      ]);

      const result = await service.calculateVRCGate(ventureId);

      expect(result.passed).toBe(false);
      expect(result.score).toBeLessThan(0.76);
    });
  });
});
```

#### Integration Tests (Supertest)
Test complete API workflows with database.

**Coverage Requirements**:
- **All 5D Phase Endpoints**: 100% coverage
- **Quality Gate Endpoints**: 100% coverage
- **Multi-Tenant Isolation**: 100% coverage
- **Agent Execution**: ≥80% coverage

**Location**: `backend/tests/integration/`

```typescript
// tests/integration/ventures.spec.ts
describe('Ventures API (Integration)', () => {
  let app: INestApplication;
  let tenantId: string;
  let authToken: string;

  beforeAll(async () => {
    // Setup test app
    const module = await Test.createTestingModule({
      imports: [AppModule]
    }).compile();

    app = module.createNestApplication();
    await app.init();

    // Create test tenant and user
    const { tenant, user, token } = await setupTestTenant();
    tenantId = tenant.id;
    authToken = token;
  });

  afterAll(async () => {
    await cleanupTestData();
    await app.close();
  });

  describe('POST /api/v1/ventures', () => {
    it('should create venture with valid data', async () => {
      const createDto = {
        name: 'Test Venture',
        description: 'Integration test venture',
        phase: 'DISCOVER'
      };

      const response = await request(app.getHttpServer())
        .post('/api/v1/ventures')
        .set('Authorization', `Bearer ${authToken}`)
        .set('X-Tenant-ID', tenantId)
        .send(createDto)
        .expect(201);

      expect(response.body.data.id).toBeDefined();
      expect(response.body.data.name).toBe(createDto.name);
      expect(response.body.data.tenantId).toBe(tenantId);
    });

    it('should reject cross-tenant access attempt', async () => {
      const otherTenantId = randomUUID();

      await request(app.getHttpServer())
        .post('/api/v1/ventures')
        .set('Authorization', `Bearer ${authToken}`)
        .set('X-Tenant-ID', otherTenantId) // Different tenant
        .send({ name: 'Test', description: 'Test', phase: 'DISCOVER' })
        .expect(403); // Forbidden
    });
  });

  describe('Complete 5D Workflow', () => {
    it('should complete full DISCOVER → DEFINE → DESIGN → DEVELOP → DEPLOY flow', async () => {
      // 1. Create venture
      const venture = await createTestVenture(tenantId, authToken);

      // 2. Start Discover phase
      await request(app.getHttpServer())
        .post(`/api/v1/ventures/${venture.id}/discover/start`)
        .set('Authorization', `Bearer ${authToken}`)
        .expect(200);

      // 3. Execute Discover agents
      // ... (execute all 4 Discover agents)

      // 4. Calculate VRC gate
      const vrcGate = await request(app.getHttpServer())
        .get(`/api/v1/ventures/${venture.id}/discover/vrc-gate`)
        .set('Authorization', `Bearer ${authToken}`)
        .expect(200);

      expect(vrcGate.body.data.score).toBeGreaterThanOrEqual(0.76);

      // 5. Complete Discover phase
      await request(app.getHttpServer())
        .post(`/api/v1/ventures/${venture.id}/discover/complete`)
        .set('Authorization', `Bearer ${authToken}`)
        .expect(200);

      // Continue through remaining phases...
    });
  });
});
```

#### Contract Tests (OpenAPI Compliance)
Validate API requests/responses match OpenAPI 3.0 specifications.

**Coverage Requirements**:
- **All API Endpoints**: 100% compliance with OpenAPI specs

**Location**: `backend/tests/contract/`

```typescript
// tests/contract/api-discover.spec.ts
import { validateAgainstSchema } from 'openapi-validator';

describe('Discover Phase API Contract', () => {
  const schema = loadOpenAPISpec('specs/master/contracts/api-discover.yaml');

  it('POST /api/v1/ventures/:id/discover/start matches contract', async () => {
    const response = await request(app.getHttpServer())
      .post(`/api/v1/ventures/${ventureId}/discover/start`)
      .set('Authorization', `Bearer ${authToken}`)
      .expect(200);

    const validation = validateAgainstSchema(
      schema,
      '/ventures/{id}/discover/start',
      'post',
      response.body
    );

    expect(validation.valid).toBe(true);
    expect(validation.errors).toHaveLength(0);
  });
});
```

#### End-to-End Tests (Playwright)
Test complete user workflows in browser.

**Coverage Requirements**:
- **Critical User Journeys**: 100% coverage
- **P1 User Stories**: ≥80% coverage

**Location**: `frontend/tests/e2e/`

```typescript
// tests/e2e/venture-creation.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Venture Creation Flow', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/dashboard');
    await login(page, 'test@augentislabs.com', 'password');
  });

  test('should create new venture and start Discover phase', async ({ page }) => {
    // 1. Navigate to create venture
    await page.click('text=New Venture');

    // 2. Fill form
    await page.fill('[name="name"]', 'E-commerce Platform');
    await page.fill('[name="description"]', 'A modern e-commerce solution');
    await page.selectOption('[name="phase"]', 'DISCOVER');

    // 3. Submit
    await page.click('button:has-text("Create Venture")');

    // 4. Verify creation
    await expect(page).toHaveURL(/\/dashboard\/ventures\/[a-f0-9-]+/);
    await expect(page.locator('h1')).toContainText('E-commerce Platform');

    // 5. Start Discover phase
    await page.click('button:has-text("Start Discover Phase")');

    // 6. Verify phase started
    await expect(page.locator('.phase-indicator')).toContainText('Discover');
  });

  test('should execute Discover agents and pass VRC gate', async ({ page }) => {
    const ventureId = await createTestVenture();
    await page.goto(`/dashboard/ventures/${ventureId}/discover`);

    // Execute all 4 Discover agents
    for (const agent of ['Market Analyzer', 'Competitor Scout', 'Problem Validator', 'Risk Assessor']) {
      await page.click(`button:has-text("Execute ${agent}")`);
      await expect(page.locator('.execution-status')).toContainText('COMPLETED', { timeout: 30000 });
    }

    // Check VRC gate
    await page.click('button:has-text("Calculate VRC Gate")');
    const gateScore = await page.locator('.gate-score').textContent();
    expect(parseInt(gateScore!)).toBeGreaterThanOrEqual(76);

    // Complete Discover phase
    await page.click('button:has-text("Complete Discover Phase")');
    await expect(page.locator('.phase-indicator')).toContainText('Define');
  });
});
```

### Test Organization

#### File Structure
```
backend/tests/
├── unit/                      # Unit tests
│   ├── services/
│   ├── guards/
│   └── utils/
├── integration/               # Integration tests
│   ├── ventures.spec.ts
│   ├── agents.spec.ts
│   ├── gates.spec.ts
│   └── telemetry.spec.ts
└── contract/                  # OpenAPI contract tests
    ├── api-discover.spec.ts
    ├── api-define.spec.ts
    └── ...

frontend/tests/
└── e2e/                       # Playwright E2E tests
    ├── venture-creation.spec.ts
    ├── discover-phase.spec.ts
    ├── define-phase.spec.ts
    └── ...
```

#### Naming Conventions
- **Test files**: `*.spec.ts` (unit, integration, contract) or `*.test.ts` (alternative)
- **E2E files**: `*.spec.ts` in `e2e/` directory
- **Test suites**: `describe('ComponentName', () => { ... })`
- **Test cases**: `it('should do something when condition', async () => { ... })`
- **Setup**: `beforeEach`, `beforeAll`, `afterEach`, `afterAll`

### Mocking Best Practices

#### Mock External Dependencies
```typescript
describe('AgentExecutionService', () => {
  let service: AgentExecutionService;
  let llmService: jest.Mocked<LLMService>;

  beforeEach(() => {
    llmService = {
      callLLM: jest.fn()
    } as any;

    service = new AgentExecutionService(llmService, prisma);
  });

  it('should retry LLM call on failure', async () => {
    llmService.callLLM
      .mockRejectedValueOnce(new Error('Rate limit'))
      .mockResolvedValueOnce({ result: 'success' });

    const result = await service.executeAgent(agentId);

    expect(llmService.callLLM).toHaveBeenCalledTimes(2);
    expect(result).toBe('success');
  });
});
```

#### Mock Prisma
```typescript
const mockPrisma = {
  venture: {
    create: jest.fn(),
    findMany: jest.fn(),
    update: jest.fn()
  }
} as unknown as PrismaService;
```

### Test Data Management

#### Test Fixtures
```typescript
// tests/fixtures/ventures.ts
export const mockVenture: Venture = {
  id: '123e4567-e89b-12d3-a456-426614174000',
  tenantId: '987fcdeb-51a2-43d1-9876-ba9876543210',
  name: 'Test Venture',
  description: 'A test venture',
  phase: Phase.DISCOVER,
  createdAt: new Date('2025-01-01'),
  updatedAt: new Date('2025-01-01'),
  deletedAt: null
};
```

#### Database Seeding (Integration Tests)
```typescript
async function setupTestTenant() {
  const tenant = await prisma.tenant.create({
    data: { name: 'Test Tenant' }
  });

  const user = await prisma.user.create({
    data: { email: 'test@test.com', tenantId: tenant.id }
  });

  const token = generateJWT(user);

  return { tenant, user, token };
}

async function cleanupTestData() {
  await prisma.venture.deleteMany();
  await prisma.user.deleteMany();
  await prisma.tenant.deleteMany();
}
```

### Performance Testing
- **Unit Tests**: <50ms per test
- **Integration Tests**: <500ms per test
- **E2E Tests**: <5 seconds per test
- **Full Suite**: <5 minutes total

### Continuous Integration
Run tests in CI/CD pipeline (GitHub Actions):

```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: 20

      - name: Install dependencies
        run: npm ci

      - name: Run unit tests
        run: npm run test:unit

      - name: Run integration tests
        run: npm run test:integration

      - name: Run contract tests
        run: npm run test:contract

      - name: Run E2E tests
        run: npm run test:e2e

      - name: Check coverage
        run: npm run test:coverage
```

### Quality Gate Integration
Tests are integrated into quality gates:

**MDP Gate (≥85% required)**:
- All P1 tests passing: ✅ (mandatory)
- Test coverage ≥80% for critical paths: ✅
- Contract tests passing (OpenAPI compliance): ✅
- No critical security vulnerabilities (SAST): ✅
- E2E tests passing for critical user flows: ✅

### Test Documentation
Document test scenarios for complex features:

```typescript
/**
 * Test Scenario: Multi-Tenant Isolation
 *
 * Given: Two tenants (Tenant A, Tenant B) with separate ventures
 * When: Tenant A user attempts to access Tenant B's venture
 * Then: Request should be rejected with 403 Forbidden
 *
 * Evidence Tier: E3 (Field-Tested via integration tests)
 * Priority: P1 (Critical security requirement)
 */
describe('Multi-Tenant Isolation', () => { ... });
```

### Red-Green-Refactor Discipline Checklist
Before committing code, verify:

- ✅ **RED**: Failing test written first?
- ✅ **GREEN**: Implementation makes test pass?
- ✅ **REFACTOR**: Code improved while tests stay green?
- ✅ **Coverage**: Critical paths have ≥80% coverage?
- ✅ **Fast**: Unit tests run in <50ms each?
- ✅ **Isolated**: Tests don't depend on external services?
- ✅ **Deterministic**: Tests pass consistently (no flaky tests)?
