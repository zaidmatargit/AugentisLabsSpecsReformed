## Database model best practices

### General Principles
- **Clear Naming**: Use PascalCase for Prisma models (e.g., `Venture`, `QualityGate`), snake_case for database tables
- **Timestamps**: Include `createdAt` and `updatedAt` on all models for auditing
- **Data Integrity**: Use Prisma schema constraints (`@unique`, foreign keys, required fields)
- **Appropriate Data Types**: Use `String @db.Uuid` for IDs, `DateTime` for timestamps, enums for fixed sets
- **Indexes**: Index foreign keys, frequently queried fields, and multi-tenant filters (`@@index([tenantId])`)
- **Validation**: Implement validation at Prisma schema level (field constraints) and NestJS DTO level
- **Relationship Clarity**: Define relationships with `@relation` and appropriate cascade behaviors
- **Soft Deletes**: Use `deletedAt DateTime?` for soft deletes instead of hard deletes

### AugentisLabs-Specific Model Patterns

#### Multi-Tenant Row-Level Security (RLS)
Every model must include `tenantId` for multi-tenant isolation:

```prisma
model Venture {
  id          String   @id @default(uuid()) @db.Uuid
  tenantId    String   @db.Uuid
  name        String   @db.VarChar(100)
  description String   @db.VarChar(500)
  phase       Phase    @default(DISCOVER)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  deletedAt   DateTime?

  tenant      Tenant   @relation(fields: [tenantId], references: [id], onDelete: Cascade)

  @@index([tenantId])
  @@unique([tenantId, name])
  @@map("ventures")
}
```

**RLS Rules**:
- Every model (except `Tenant`, `User`) must have `tenantId` field
- Index `tenantId` for query performance: `@@index([tenantId])`
- Use composite unique constraints to prevent duplicates per tenant: `@@unique([tenantId, name])`
- All Prisma queries must filter by tenant: `where: { tenantId: user.tenantId }`

#### Core Models Overview

**Tenant & Users**:
- `Tenant` - Multi-tenant organization
- `User` - User accounts (linked to Supabase Auth)
- `UserRole` - Role assignments (Admin, Member, Viewer)

**Venture Workflow**:
- `Venture` - Top-level venture/project
- `VenturePhase` - Phase progression tracking (Discover, Define, Design, Develop, Deploy)
- `QualityGate` - Gate scores (VRC, VCD, DSP, MDP)
- `UserStory` - User stories with priority (P1, P2, P3)
- `Persona` - User personas from Define phase

**Agent Orchestration**:
- `Agent` - 26 specialized agents across 5D phases
- `AgentExecution` - Agent execution history with state persistence
- `AgentCheckpoint` - LangGraph state checkpoints for error recovery

**Artifacts**:
- `Artifact` - Generated artifacts (PRD, wireframes, schemas, code)
- `ArtifactVersion` - Version history with diffs

**Telemetry & Governance**:
- `Telemetry` - Production telemetry data
- `DivergenceAlert` - Spec divergence detection
- `AuditLog` - Audit trail for governance
- `CostTracking` - LLM cost tracking per tenant

#### Prisma Enums
Define fixed sets as Prisma enums:

```prisma
enum Phase {
  DISCOVER
  DEFINE
  DESIGN
  DEVELOP
  DEPLOY
}

enum GateType {
  VRC  // Venture Readiness Check (≥76%)
  VCD  // Venture Concept Definition (≥75%)
  DSP  // Design Specification Passing (≥80%)
  MDP  // Minimum Deliverable Product (≥85%)
}

enum EvidenceTier {
  E0  // Assumption
  E1  // Anecdotal
  E2  // Researched
  E3  // Field-Tested
  E4  // Production-Proven
}

enum Priority {
  P1  // Critical (must-have)
  P2  // Important (should-have)
  P3  // Nice-to-have (could-have)
}

enum ArtifactType {
  PRD
  PERSONA
  WIREFRAME
  ARCHITECTURE
  DATA_MODEL
  API_CONTRACT
  TEST_SUITE
  CODE
  DEPLOYMENT_CONFIG
}

enum AgentPhase {
  DISCOVER
  DEFINE
  DESIGN
  DEVELOP
  DEPLOY
}

enum ExecutionStatus {
  PENDING
  RUNNING
  COMPLETED
  FAILED
  CANCELLED
}
```

#### Foreign Key Relationships
Define relationships with cascade behaviors:

```prisma
model UserStory {
  id          String   @id @default(uuid()) @db.Uuid
  tenantId    String   @db.Uuid
  ventureId   String   @db.Uuid
  title       String   @db.VarChar(200)
  priority    Priority
  evidenceTier EvidenceTier @default(E0)

  venture     Venture  @relation(fields: [ventureId], references: [id], onDelete: Cascade)
  tenant      Tenant   @relation(fields: [tenantId], references: [id], onDelete: Cascade)

  @@index([tenantId])
  @@index([ventureId])
  @@map("user_stories")
}
```

**Cascade Rules**:
- `onDelete: Cascade` - Delete children when parent deleted (e.g., delete user stories when venture deleted)
- `onDelete: Restrict` - Prevent deletion if children exist (e.g., prevent tenant deletion if ventures exist)
- `onDelete: SetNull` - Set foreign key to null when parent deleted (use sparingly)

#### Composite Indexes for Performance
Create composite indexes for common query patterns:

```prisma
model AgentExecution {
  id          String          @id @default(uuid()) @db.Uuid
  tenantId    String          @db.Uuid
  ventureId   String          @db.Uuid
  agentId     String          @db.Uuid
  status      ExecutionStatus
  startedAt   DateTime        @default(now())
  completedAt DateTime?

  @@index([tenantId, ventureId])
  @@index([tenantId, status])
  @@index([agentId, status])
  @@map("agent_executions")
}
```

#### JSON Fields for Flexible Data
Use `Json` type for semi-structured data:

```prisma
model Artifact {
  id          String       @id @default(uuid()) @db.Uuid
  tenantId    String       @db.Uuid
  ventureId   String       @db.Uuid
  type        ArtifactType
  content     Json         // Flexible artifact content
  metadata    Json?        // Optional metadata
  version     Int          @default(1)

  @@index([tenantId, ventureId, type])
  @@map("artifacts")
}
```

**JSON Field Usage**:
- Store flexible/nested data that doesn't need querying
- Use for LLM outputs, configuration objects, metadata
- Avoid for data that needs filtering/sorting (use proper columns instead)

#### Soft Delete Pattern
Implement soft deletes for audit trail:

```prisma
model Venture {
  id          String    @id @default(uuid()) @db.Uuid
  tenantId    String    @db.Uuid
  deletedAt   DateTime? // null = active, timestamp = soft deleted

  @@index([tenantId, deletedAt]) // Query active ventures: where deletedAt is null
}
```

**Soft Delete Queries**:
```typescript
// Find active ventures
const ventures = await prisma.venture.findMany({
  where: { tenantId: user.tenantId, deletedAt: null }
});

// Soft delete
await prisma.venture.update({
  where: { id: ventureId },
  data: { deletedAt: new Date() }
});

// Restore soft deleted
await prisma.venture.update({
  where: { id: ventureId },
  data: { deletedAt: null }
});
```

#### Agent State Persistence
Store LangGraph state for error recovery:

```prisma
model AgentCheckpoint {
  id            String   @id @default(uuid()) @db.Uuid
  executionId   String   @db.Uuid
  checkpointKey String   @db.VarChar(100)
  state         Json     // LangGraph state snapshot
  createdAt     DateTime @default(now())

  execution     AgentExecution @relation(fields: [executionId], references: [id], onDelete: Cascade)

  @@unique([executionId, checkpointKey])
  @@index([executionId])
  @@map("agent_checkpoints")
}
```

#### Evidence Tier Tracking
Track evidence tier for requirements:

```prisma
model Requirement {
  id           String       @id @default(uuid()) @db.Uuid
  tenantId     String       @db.Uuid
  ventureId    String       @db.Uuid
  description  String       @db.Text
  evidenceTier EvidenceTier @default(E0)
  evidenceUrl  String?      @db.VarChar(500) // Link to supporting evidence
  isCritical   Boolean      @default(false)

  @@index([tenantId, ventureId])
  @@index([evidenceTier]) // Query by evidence tier
  @@map("requirements")
}
```

**Evidence Tier Validation**:
- Critical requirements must have `evidenceTier >= E2`
- Validate in service layer before saving
- Update to E4 after production validation

#### Artifact Versioning
Track artifact version history with diffs:

```prisma
model ArtifactVersion {
  id          String   @id @default(uuid()) @db.Uuid
  artifactId  String   @db.Uuid
  version     Int
  content     Json
  diff        Json?    // Diff from previous version
  createdBy   String   @db.Uuid
  createdAt   DateTime @default(now())

  artifact    Artifact @relation(fields: [artifactId], references: [id], onDelete: Cascade)
  user        User     @relation(fields: [createdBy], references: [id])

  @@unique([artifactId, version])
  @@index([artifactId])
  @@map("artifact_versions")
}
```

#### Quality Gate Scores
Store quality gate calculations:

```prisma
model QualityGate {
  id          String   @id @default(uuid()) @db.Uuid
  tenantId    String   @db.Uuid
  ventureId   String   @db.Uuid
  gateType    GateType
  score       Float    // 0.0 to 1.0 (0% to 100%)
  threshold   Float    // Required threshold (0.76, 0.75, 0.80, 0.85)
  passed      Boolean
  criteria    Json     // Detailed criteria breakdown
  calculatedAt DateTime @default(now())

  @@index([tenantId, ventureId, gateType])
  @@map("quality_gates")
}
```

#### Telemetry & Divergence Detection
Track production telemetry:

```prisma
model DivergenceAlert {
  id          String   @id @default(uuid()) @db.Uuid
  tenantId    String   @db.Uuid
  ventureId   String   @db.Uuid
  component   String   @db.VarChar(100)
  expected    Json     // Expected behavior from spec
  actual      Json     // Actual behavior from telemetry
  severity    String   @db.VarChar(20) // LOW, MEDIUM, HIGH, CRITICAL
  detectedAt  DateTime @default(now())
  resolvedAt  DateTime?

  @@index([tenantId, ventureId])
  @@index([severity, resolvedAt])
  @@map("divergence_alerts")
}
```

#### Audit Logging
Comprehensive audit trail:

```prisma
model AuditLog {
  id          String   @id @default(uuid()) @db.Uuid
  tenantId    String   @db.Uuid
  userId      String   @db.Uuid
  action      String   @db.VarChar(50) // CREATE, UPDATE, DELETE, EXECUTE
  resource    String   @db.VarChar(50) // venture, agent, artifact, etc.
  resourceId  String   @db.Uuid
  changes     Json?    // Before/after snapshot
  ipAddress   String?  @db.VarChar(45)
  userAgent   String?  @db.VarChar(500)
  createdAt   DateTime @default(now())

  @@index([tenantId, createdAt])
  @@index([userId, createdAt])
  @@index([resource, resourceId])
  @@map("audit_logs")
}
```

#### Migration Best Practices
- **Sequential Versioning**: Name migrations with timestamps (e.g., `20251122134600_add_evidence_tier`)
- **Backwards Compatible**: Ensure migrations don't break existing code (use multi-step migrations if needed)
- **Data Migration**: Use Prisma Client in migration scripts for data transformations
- **Rollback Plan**: Document rollback steps in migration comments
- **Test Migrations**: Run migrations on staging before production
- **Index Creation**: Create indexes with `CONCURRENTLY` option (requires raw SQL) to avoid locking tables

#### Prisma Client Usage
```typescript
// NestJS service with Prisma
@Injectable()
export class VentureService {
  constructor(private prisma: PrismaService) {}

  async findAllByTenant(tenantId: string): Promise<Venture[]> {
    return this.prisma.venture.findMany({
      where: {
        tenantId,
        deletedAt: null // Only active ventures
      },
      include: {
        qualityGates: true,
        userStories: { where: { priority: Priority.P1 } }
      },
      orderBy: { createdAt: 'desc' }
    });
  }
}
```

#### Performance Optimization
- **Select Only Needed Fields**: Use `select` to fetch specific fields instead of entire models
- **Batch Operations**: Use `createMany`, `updateMany`, `deleteMany` for bulk operations
- **Connection Pooling**: Configure Prisma connection pool size based on load
- **Query Optimization**: Use `explain` on slow queries; add indexes as needed
- **Avoid N+1**: Use `include` or `select` with nested relations instead of multiple queries
- **Pagination**: Always paginate large result sets (use `skip` and `take`)
