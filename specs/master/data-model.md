# Data Model: AugentisLabs MVP Database Schema

**Phase**: 1 (Weeks 3-4)  
**Purpose**: Complete database design with Prisma schema, entity relationships, validation rules, multi-tenant RLS policies  
**Input**: SPECIFICATION.md (6 user stories, 32 requirements), IMPLEMENTATION.md (project structure)  
**Output**: Prisma schema, entity relationships diagram, RLS policy definitions, migration strategy

---

## 1. Entity Relationship Diagram (ERD)

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  Workspace (workspace_id)                                          │
│  ├─ id (PK)                                                    │
│  ├─ name                                                        │
│  ├─ subscription_tier (BASIC/PRO/ENTERPRISE)                  │
│  ├─ created_at                                                 │
│  └─ users (1:M via workspace_members)                               │
│                                                                 │
│  ├─ Projects (project_id)                                      │
│  │  ├─ id (PK)                                                │
│  │  ├─ workspace_id (FK)                                      │
│  │  ├─ name, description                                      │
│  │  ├─ phase (DISCOVER/DEFINE/DESIGN/DEVELOP/DEPLOY)         │
│  │  ├─ vrc_score, vcd_score, dsp_score, mdp_score            │
│  │  ├─ status (IN_PROGRESS/PASSED/CONDITIONAL/FAILED)        │
│  │  ├─ created_at, updated_at                                │
│  │  └─ Artifacts (1:M)                                        │
│  │     ├─ id (PK)                                            │
│  │     ├─ project_id (FK)                                    │
│  │     ├─ type (PRD/PERSONA/WIREFRAME/CODE/TESTS)            │
│  │     ├─ version (v1, v2, v3...)                           │
│  │     ├─ content (JSON), evidence_tier (E0-E4)              │
│  │     ├─ created_at                                          │
│  │     └─ Tags (1:M) - for versioning & search               │
│  │                                                             │
│  │  ├─ GateDecisions (1:M)                                   │
│  │  │  ├─ id (PK)                                           │
│  │  │  ├─ project_id (FK)                                   │
│  │  │  ├─ gate_type (VRC/VCD/DSP/MDP)                       │
│  │  │  ├─ score, decision (PASS/CONDITIONAL/FAIL)           │
│  │  │  ├─ decided_by (user_id), rationale                   │
│  │  │  ├─ created_at                                         │
│  │  │  └─ override_by (user_id), override_rationale         │
│  │  │                                                         │
│  │  ├─ Telemetry (1:M)                                       │
│  │  │  ├─ id (PK)                                           │
│  │  │  ├─ project_id (FK)                                   │
│  │  │  ├─ metric_name (feature_usage, retention, crash)     │
│  │  │  ├─ metric_value, unit                                │
│  │  │  ├─ timestamp                                          │
│  │  │  └─ cohort (user segment)                             │
│  │  │                                                         │
│  │  ├─ DivergenceReports (1:M)                               │
│  │  │  ├─ id (PK)                                           │
│  │  │  ├─ project_id (FK)                                   │
│  │  │  ├─ divergence_type (persona/feature/pricing)          │
│  │  │  ├─ assumed_value, actual_value                        │
│  │  │  ├─ confidence_score, classified_as (MAJOR/MINOR)      │
│  │  │  └─ created_at                                         │
│  │  │                                                         │
│  │  └─ AgentExecutions (1:M)                                 │
│  │     ├─ id (PK)                                           │
│  │     ├─ project_id (FK)                                   │
│  │     ├─ agent_id (FK to Agent master)                     │
│  │     ├─ phase (DISCOVER/DEFINE/DESIGN/DEVELOP/DEPLOY)    │
│  │     ├─ status (QUEUED/RUNNING/SUCCESS/FAILED/RETRIED)   │
│  │     ├─ started_at, completed_at                          │
│  │     ├─ tokens_used, cost_usd, model_used                 │
│  │     ├─ output_artifact_id (FK to Artifact)              │
│  │     ├─ error_message, retry_count                        │
│  │     └─ created_at                                        │
│  │                                                             │
├─ Agents (Master Catalog - 26 agents)                          │
│  ├─ id (PK)                                                 │
│  ├─ name (Problem Validation, Code Generation, etc.)        │
│  ├─ phase (DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY)      │
│  ├─ agent_type (VALIDATOR, ANALYZER, GENERATOR, ASSEMBLER) │
│  ├─ description                                              │
│  ├─ model_preference (gpt-4, claude-3-sonnet, etc.)        │
│  ├─ avg_tokens_input, avg_tokens_output                    │
│  ├─ cost_per_execution                                      │
│  ├─ is_active (enabled/disabled)                            │
│  └─ created_at, updated_at                                  │
│                                                                 │
│  ├─ AuditLogs (1:M)                                           │
│  │  ├─ id (PK)                                              │
│  │  ├─ workspace_id (FK)                                          │
│  │  ├─ action, actor_id, resource_type, resource_id         │
│  │  ├─ changes (JSON diff)                                  │
│  │  ├─ timestamp                                            │
│  │  └─ ip_address, user_agent                              │
│  │                                                             │
│  └─ CostTracking (1:M)                                        │
│     ├─ id (PK)                                              │
│     ├─ project_id (FK)                                      │
│     ├─ agent_name, phase, tokens_used                       │
│     ├─ cost_usd, model_used (gpt-4/claude)                  │
│     ├─ timestamp                                            │
│     └─ status (success/failed/retried)                      │
│                                                                 │
├─ Users (user_id)                                              │
│  ├─ id (PK)                                                 │
│  ├─ email, display_name                                     │
│  ├─ auth_provider (supabase, google, ...)                   │
│  ├─ role (USER/ADMIN/CTO)                                   │
│  ├─ created_at, last_login                                  │
│  └─ workspace_members (1:M) - with role per workspace                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. Prisma Schema

```prisma
// prisma/schema.prisma

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

// ============================================================================
// AUTHENTICATION & MULTI-TENANCY
// ============================================================================

model Workspace {
  id                String      @id @default(cuid())
  name              String      @db.VarChar(255)
  subscription_tier String      @default("BASIC") // BASIC, PRO, ENTERPRISE
  max_projects      Int         @default(10) // Tier limit
  monthly_token_budget Int     @default(100000) // LLM token budget
  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  // Relations
  members           WorkspaceMember[]
  projects          Project[]
  auditLogs         AuditLog[]
  costTracking      CostTracking[]

  @@map("workspaces")
}

model User {
  id                String      @id @default(cuid())
  email             String      @unique @db.VarChar(255)
  display_name      String      @db.VarChar(255)
  auth_provider     String      @default("supabase") // supabase, google, github
  role              String      @default("USER") // USER, ADMIN, CTO
  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt
  last_login        DateTime?

  // Relations
  workspaceMemberships WorkspaceMember[]
  gateDecisions     GateDecision[] @relation("DecidedBy")
  gateOverrides     GateDecision[] @relation("OverriddenBy")
  auditLogs         AuditLog[] @relation("ActorId")

  @@map("users")
}

model OrgMember {
  id                String      @id @default(cuid())
  workspace_id      String
  user_id           String
  role              String      @default("MEMBER") // OWNER, ADMIN, MEMBER, VIEWER
  joined_at         DateTime    @default(now())

  // Relations
  workspace         Workspace @relation(fields: [workspace_id], references: [id], onDelete: Cascade)
  user              User @relation(fields: [user_id], references: [id], onDelete: Cascade)

  @@unique([workspace_id, user_id])
  @@map("workspace_members")
}

// ============================================================================
// AGENT MASTER CATALOG (26 Agents from Product Brief)
// ============================================================================

model Agent {
  id                String      @id @default(cuid())
  name              String      @unique @db.VarChar(100) // Problem Validation, Code Generation, etc.
  phase             String      @db.VarChar(20)  // DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY
  agent_type        String      @db.VarChar(50)  // VALIDATOR, ANALYZER, GENERATOR, ASSEMBLER, SCANNER, OPTIMIZER
  description       String?     @db.Text

  // Model Configuration
  model_preference  String      @db.VarChar(50)  // gpt-4, claude-3-sonnet, gpt-4-turbo, claude-3-opus
  temperature       Decimal     @default(0.7) @db.Decimal(3, 2) // 0.0-1.0
  max_tokens        Int         @default(2000)

  // Cost & Performance Baseline
  avg_tokens_input  Int         @default(1000)   // Historical average
  avg_tokens_output Int         @default(500)    // Historical average
  cost_per_execution Decimal    @db.Decimal(6, 4) // USD cost

  // Status
  is_active         Boolean     @default(true)
  version           Int         @default(1)      // Agent version tracking

  // Timestamps
  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  // Relations
  executions        AgentExecution[]

  @@unique([name, phase])
  @@index([phase])
  @@index([is_active])
  @@map("agents")
}

// ============================================================================
// PROJECTS & ARTIFACTS
// ============================================================================

model Project {
  id                String      @id @default(cuid())
  workspace_id      String
  name              String      @db.VarChar(255)
  description       String?     @db.Text
  phase             String      @default("DISCOVER") // DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY
  status            String      @default("IN_PROGRESS") // IN_PROGRESS, COMPLETED, FAILED, PAUSED

  // Quality Gate Scores (0-100)
  vrc_score         Int?        @default(0) // Venture Readiness Compass
  vrc_decision      String?     // PASS, CONDITIONAL, FAIL
  vcd_score         Int?        @default(0) // Validation & Clarity Degree
  vcd_decision      String?     // PASS, CONDITIONAL, FAIL
  dsp_score         Int?        @default(0) // Design Specification Progress
  dsp_decision      String?     // PASS, CONDITIONAL, FAIL
  mdp_score         Int?        @default(0) // Minimum Deployable Product
  mdp_decision      String?     // PASS, CONDITIONAL, FAIL

  // Timestamps
  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt
  launched_at       DateTime?   // When deployed to production

  // Relations
  workspace         Workspace @relation(fields: [workspace_id], references: [id], onDelete: Cascade)
  artifacts         Artifact[]
  gateDecisions     GateDecision[]
  telemetry         Telemetry[]
  divergenceReports DivergenceReport[]
  agentExecutions   AgentExecution[]
  costTracking      CostTracking[]

  @@index([workspace_id])
  @@index([phase])
  @@index([status])
  @@map("projects")
}

model Artifact {
  id                String      @id @default(cuid())
  project_id        String
  type              String      @db.VarChar(50) // PRD, PERSONA, WIREFRAME, CODE, TESTS, DESIGN_SYSTEM, ARCHITECTURE, TELEMETRY_DASHBOARD
  version           Int         @default(1) // v1, v2, v3...
  title             String      @db.VarChar(255)
  description       String?     @db.Text

  // Content & Classification
  content           Json        // Structured artifact data
  evidence_tier     String      @default("E0") // E0-E4: Unvalidated to Production-Proven
  confidence_score  Int?        // 0-100 confidence percentage

  // Versioning & Storage
  s3_url            String?     // For large files (PDFs, images)
  storage_type      String      @default("JSON") // JSON, FILE, URL

  // Metadata
  created_by        String?     // user_id of creator (agent or human)
  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  // Relations
  project           Project @relation(fields: [project_id], references: [id], onDelete: Cascade)
  tags              ArtifactTag[]
  generatedByAgents AgentExecution[]

  @@unique([project_id, type, version])
  @@index([project_id])
  @@index([type])
  @@index([evidence_tier])
  @@map("artifacts")
}

model ArtifactTag {
  id                String      @id @default(cuid())
  artifact_id       String
  tag               String      @db.VarChar(50) // "latest", "approved", "deprecated", "review_pending"
  created_at        DateTime    @default(now())

  // Relations
  artifact          Artifact @relation(fields: [artifact_id], references: [id], onDelete: Cascade)

  @@unique([artifact_id, tag])
  @@map("artifact_tags")
}

// ============================================================================
// QUALITY GATES & DECISIONS
// ============================================================================

model GateDecision {
  id                String      @id @default(cuid())
  project_id        String
  gate_type         String      @db.VarChar(10) // VRC, VCD, DSP, MDP

  // Score & Decision
  score             Int         // 0-100
  decision          String      // PASS, CONDITIONAL, FAIL
  rationale         String?     @db.Text // Why this decision
  indicators        Json        // Individual indicator scores (20 items per gate)

  // Decision Making
  decided_by        String      // user_id
  decided_at        DateTime    @default(now())

  // Override (if applicable)
  override_by       String?     // user_id of CTO/approver
  override_reason   String?     @db.Text
  override_at       DateTime?

  // Remediation (for CONDITIONAL decisions)
  remediation_items Json?      // List of items to address
  remediation_deadline DateTime?

  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  // Relations
  project           Project @relation(fields: [project_id], references: [id], onDelete: Cascade)
  decidedByUser     User @relation("DecidedBy", fields: [decided_by], references: [id])
  overriddenByUser  User? @relation("OverriddenBy", fields: [override_by], references: [id])

  @@unique([venture_id, gate_type])
  @@index([project_id])
  @@index([gate_type])
  @@index([decision])
  @@map("gate_decisions")
}

// ============================================================================
// TELEMETRY & MONITORING (Deploy Phase)
// ============================================================================

model Telemetry {
  id                String      @id @default(cuid())
  project_id        String
  metric_name       String      @db.VarChar(100) // feature_usage, retention_d1, crash_rate, api_latency_p95
  metric_value      Float       // Numeric value
  unit              String      @db.VarChar(50)  // %, ms, count, users, etc.
  cohort            String?     @db.VarChar(100) // User segment: "trial", "premium", "age_25_35", etc.
  timestamp         DateTime    @default(now())

  // Relations
  project           Project @relation(fields: [project_id], references: [id], onDelete: Cascade)

  @@index([project_id])
  @@index([metric_name])
  @@index([timestamp])
  @@index([cohort])
  @@map("telemetry")
}

model DivergenceReport {
  id                String      @id @default(cuid())
  project_id        String

  // Divergence Detection
  divergence_type   String      @db.VarChar(50) // persona_attribute, feature_priority, pricing_strategy, market_change
  assumed_value     String      @db.VarChar(255)  // From Define phase
  actual_value      String      @db.VarChar(255)  // From Deploy telemetry
  confidence_score  Int         // 0-100, E4 telemetry vs E1 assumption
  classification    String      // MAJOR (>50% delta), MINOR (20-50%), COSMETIC (<20%)

  // Recommendation
  recommendation    String?     @db.Text // Proposed action
  affected_stories  Json?       // User stories affected
  affected_screens  Json?       // Design screens affected
  affected_code     Json?       // Code modules affected
  effort_estimate   Int?        // Hours to fix

  // Status
  status            String      @default("OPEN") // OPEN, REVIEWED, APPROVED, IMPLEMENTED, CLOSED
  reviewed_by       String?     // user_id
  reviewed_at       DateTime?

  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  // Relations
  project           Project @relation(fields: [project_id], references: [id], onDelete: Cascade)

  @@index([project_id])
  @@index([classification])
  @@index([status])
  @@map("divergence_reports")
}

// ============================================================================
// AGENT ORCHESTRATION & EXECUTION TRACKING
// ============================================================================

model AgentExecution {
  id                String      @id @default(cuid())
  project_id        String
  agent_id          String      // FK to Agent master
  phase             String      @db.VarChar(20)  // DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY

  // Execution Status
  status            String      @default("QUEUED") // QUEUED, RUNNING, SUCCESS, FAILED, RETRIED, SKIPPED
  started_at        DateTime?
  completed_at      DateTime?
  duration_seconds  Int?        // Execution time

  // Input & Output
  input_data        Json?       // Input parameters passed to agent
  output_artifact_id String?    // Links to generated Artifact
  error_message     String?     @db.Text // If FAILED, why?
  retry_count       Int         @default(0) // Number of retries
  max_retries       Int         @default(3)

  // Resource Usage & Cost
  tokens_used_input  Int?       // Input tokens to LLM
  tokens_used_output Int?       // Output tokens from LLM
  total_tokens      Int?        // input + output
  model_used        String      @db.VarChar(50)  // gpt-4, claude-3-sonnet, etc.
  cost_usd          Decimal     @db.Decimal(10, 4) // USD cost for this execution
  cost_tokens       Int?        // Token count for cost calculation

  // Quality Metrics
  confidence_score  Int?        // 0-100, how confident is the output?
  quality_checks    Json?       // {check_name: pass/fail/warning}
  evidence_tier     String      @default("E0") // E0-E4: evidence classification

  // Timestamps
  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  // Relations
  project           Project @relation(fields: [project_id], references: [id], onDelete: Cascade)
  agent             Agent @relation(fields: [agent_id], references: [id], onDelete: Restrict)
  outputArtifact    Artifact? @relation(fields: [output_artifact_id], references: [id], onDelete: SetNull)

  @@index([project_id])
  @@index([agent_id])
  @@index([phase])
  @@index([status])
  @@index([started_at])
  @@index([completed_at])
  @@map("agent_executions")
}

// ============================================================================
// AUDIT LOGGING & COMPLIANCE
// ============================================================================

model AuditLog {
  id                String      @id @default(cuid())
  org_id            String

  // Event Details
  action            String      @db.VarChar(50)  // created, updated, deleted, approved, deployed, etc.
  actor_id          String
  resource_type     String      @db.VarChar(50)  // Venture, Artifact, GateDecision, CostTracking
  resource_id       String      @db.VarChar(100)

  // Change Tracking
  changes           Json?       // {old: {...}, new: {...}}

  // Security & Compliance
  timestamp         DateTime    @default(now())
  ip_address        String?     @db.VarChar(45)  // IPv4 or IPv6
  user_agent        String?     @db.VarChar(255)

  created_at        DateTime    @default(now())

  // Relations
  workspace      Workspace @relation(fields: [workspace_id], references: [id], onDelete: Cascade)
  actor             User @relation("ActorId", fields: [actor_id], references: [id])

  @@index([org_id])
  @@index([action])
  @@index([resource_type])
  @@index([timestamp])
  @@map("audit_logs")
}

// ============================================================================
// COST TRACKING & BUDGETING
// ============================================================================

model CostTracking {
  id                String      @id @default(cuid())
  org_id            String
  project_id        String?     // null if org-level

  // LLM Usage
  agent_name        String      @db.VarChar(100) // Problem Validator, Persona Builder, etc.
  phase             String      @db.VarChar(20)  // DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY
  model_used        String      @db.VarChar(50)  // gpt-4-turbo, claude-3-sonnet
  tokens_used       Int         // Input + output tokens
  cost_usd          Decimal     @db.Decimal(10, 4) // USD cost

  // Status
  status            String      @default("SUCCESS") // SUCCESS, FAILED, RETRIED
  retry_count       Int         @default(0)

  // Timestamps
  started_at        DateTime    @default(now())
  completed_at      DateTime?
  timestamp         DateTime    @default(now())

  // Relations
  workspace      Workspace @relation(fields: [workspace_id], references: [id], onDelete: Cascade)
  project           Project? @relation(fields: [project_id], references: [id], onDelete: SetNull)

  @@index([org_id])
  @@index([project_id])
  @@index([phase])
  @@index([timestamp])
  @@map("cost_tracking")
}

// ============================================================================
// FEATURE FLAGS & CONFIGURATION
// ============================================================================

model FeatureFlag {
  id                String      @id @default(cuid())
  name              String      @unique @db.VarChar(100)
  description       String?     @db.Text
  enabled_for_all   Boolean     @default(false)

  // Rollout
  rollout_percentage Int       @default(0) // 0-100%
  enabled_orgs      Json?      // List of org_ids to enable for

  created_at        DateTime    @default(now())
  updated_at        DateTime    @updatedAt

  @@map("feature_flags")
}
```

---

## 3. Multi-Tenant Isolation: Row-Level Security (RLS) Policies

### PostgreSQL RLS Policies

```sql
-- ============================================================================
-- Enable RLS on all tables
-- ============================================================================

ALTER TABLE workspaces ENABLE ROW LEVEL SECURITY;
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE workspace_members ENABLE ROW LEVEL SECURITY;
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;
ALTER TABLE artifacts ENABLE ROW LEVEL SECURITY;
ALTER TABLE artifact_tags ENABLE ROW LEVEL SECURITY;
ALTER TABLE gate_decisions ENABLE ROW LEVEL SECURITY;
ALTER TABLE telemetry ENABLE ROW LEVEL SECURITY;
ALTER TABLE divergence_reports ENABLE ROW LEVEL SECURITY;
ALTER TABLE audit_logs ENABLE ROW LEVEL SECURITY;
ALTER TABLE cost_tracking ENABLE ROW LEVEL SECURITY;

-- ============================================================================
-- Workspaces: Admins can manage, members can view their own
-- ============================================================================

CREATE POLICY workspace_view ON workspaces
  FOR SELECT
  USING (
    -- User is member of this workspace
    EXISTS (
      SELECT 1 FROM workspace_members om
      WHERE om.workspace_id = workspaces.id
      AND om.user_id = current_user_id()
    )
  );

CREATE POLICY workspace_update ON workspaces
  FOR UPDATE
  USING (
    -- User is OWNER or ADMIN of this workspace
    EXISTS (
      SELECT 1 FROM workspace_members om
      WHERE om.workspace_id = workspaces.id
      AND om.user_id = current_user_id()
      AND om.role IN ('OWNER', 'ADMIN')
    )
  );

-- ============================================================================
-- Projects: Only users in same workspace can access
-- ============================================================================

CREATE POLICY venture_select ON ventures
  FOR SELECT
  USING (
    -- Venture's org_id matches user's org memberships
    org_id IN (
      SELECT org_id FROM org_members
      WHERE user_id = current_user_id()
    )
  );

CREATE POLICY venture_insert ON ventures
  FOR INSERT
  WITH CHECK (
    -- Can only create in org where user is ADMIN or above
    org_id IN (
      SELECT org_id FROM org_members
      WHERE user_id = current_user_id()
      AND role IN ('OWNER', 'ADMIN')
    )
  );

CREATE POLICY venture_update ON ventures
  FOR UPDATE
  USING (
    org_id IN (
      SELECT org_id FROM org_members
      WHERE user_id = current_user_id()
      AND role IN ('OWNER', 'ADMIN')
    )
  );

CREATE POLICY venture_delete ON ventures
  FOR DELETE
  USING (
    org_id IN (
      SELECT org_id FROM org_members
      WHERE user_id = current_user_id()
      AND role IN ('OWNER', 'ADMIN')
    )
  );

-- ============================================================================
-- Artifacts: Inherit venture org_id isolation
-- ============================================================================

CREATE POLICY artifact_select ON artifacts
  FOR SELECT
  USING (
    -- Artifact's venture's org_id matches user's memberships
    project_id IN (
      SELECT id from projects
      WHERE org_id IN (
        SELECT org_id FROM org_members
        WHERE user_id = current_user_id()
      )
    )
  );

CREATE POLICY artifact_insert ON artifacts
  FOR INSERT
  WITH CHECK (
    -- Can only add to venture in user's org
    project_id IN (
      SELECT id from projects
      WHERE org_id IN (
        SELECT org_id FROM org_members
        WHERE user_id = current_user_id()
      )
    )
  );

-- ============================================================================
-- Gate Decisions: Same org isolation, with approval roles
-- ============================================================================

CREATE POLICY gate_decision_select ON gate_decisions
  FOR SELECT
  USING (
    -- User is in venture's org
    project_id IN (
      SELECT id from projects
      WHERE org_id IN (
        SELECT org_id FROM org_members
        WHERE user_id = current_user_id()
      )
    )
  );

CREATE POLICY gate_decision_insert ON gate_decisions
  FOR INSERT
  WITH CHECK (
    -- Can only create for venture in user's org with ADMIN+ role
    project_id IN (
      SELECT id from projects
      WHERE org_id IN (
        SELECT org_id FROM org_members
        WHERE user_id = current_user_id()
        AND role IN ('OWNER', 'ADMIN')
      )
    )
  );

-- ============================================================================
-- Telemetry: Read-only for members, write-only for backend services
-- ============================================================================

CREATE POLICY telemetry_select ON telemetry
  FOR SELECT
  USING (
    project_id IN (
      SELECT id from projects
      WHERE org_id IN (
        SELECT org_id FROM org_members
        WHERE user_id = current_user_id()
      )
    )
  );

-- Telemetry INSERT/UPDATE only allowed from service role (backend)
-- Via Supabase service role RLS bypass

-- ============================================================================
-- Audit Logs: Read-only, only for admins of the org
-- ============================================================================

CREATE POLICY audit_log_select ON audit_logs
  FOR SELECT
  USING (
    -- User is OWNER/ADMIN of this org
    org_id IN (
      SELECT org_id FROM org_members
      WHERE user_id = current_user_id()
      AND role IN ('OWNER', 'ADMIN')
    )
  );

-- ============================================================================
-- Cost Tracking: Visible to org admins, hidden from regular members
-- ============================================================================

CREATE POLICY cost_tracking_select ON cost_tracking
  FOR SELECT
  USING (
    -- User is OWNER/ADMIN of org
    org_id IN (
      SELECT org_id FROM org_members
      WHERE user_id = current_user_id()
      AND role IN ('OWNER', 'ADMIN')
    )
  );
```

### Helper Function: current_user_id()

```sql
CREATE OR REPLACE FUNCTION current_user_id() RETURNS text AS $$
  SELECT auth.uid()::text
$$ LANGUAGE SQL STABLE;

CREATE OR REPLACE FUNCTION current_org_id() RETURNS text AS $$
  SELECT nullif(current_setting('app.current_org_id', true), '')
$$ LANGUAGE SQL STABLE;
```

### NestJS Middleware: Set Organization Context

```typescript
// backend/src/middleware/org-context.middleware.ts

import { Injectable, NestMiddleware } from "@nestjs/common";
import { Request, Response, NextFunction } from "express";
import { PrismaService } from "../services/database.service";

@Injectable()
export class OrgContextMiddleware implements NestMiddleware {
  constructor(private prisma: PrismaService) {}

  async use(req: Request, res: Response, next: NextFunction) {
    const userId = req.user?.id;

    if (!userId) {
      return res.status(401).json({ error: "Unauthorized" });
    }

    // Get user's org (assume first org if multiple)
    const orgMember = await this.prisma.orgMember.findFirst({
      where: { user_id: userId },
    });

    if (!orgMember) {
      return res.status(403).json({ error: "User not in any organization" });
    }

    // Set Postgres session variable for RLS
    await this.prisma.$executeRawUnsafe(
      `SELECT set_config('app.current_org_id', $1, false)`,
      [orgMember.org_id]
    );

    // Attach org_id to request for easier access
    req.user.org_id = orgMember.org_id;
    req.user.role = orgMember.role;

    next();
  }
}
```

---

## 4. Entity Relationships & Constraints

### Foreign Key Relationships

| From             | To        | Constraint              | Behavior               |
| ---------------- | --------- | ----------------------- | ---------------------- |
| Project          | Workspace | workspace_id → id       | CASCADE on delete      |
| Artifact         | Project   | project_id → id         | CASCADE on delete      |
| GateDecision     | Project   | project_id → id         | CASCADE on delete      |
| GateDecision     | User      | decided_by → id         | SET NULL on delete     |
| Telemetry        | Project   | project_id → id         | CASCADE on delete      |
| DivergenceReport | Project   | project_id → id         | CASCADE on delete      |
| AgentExecution   | Project   | project_id → id         | CASCADE on delete      |
| AgentExecution   | Agent     | agent_id → id           | RESTRICT (master data) |
| AgentExecution   | Artifact  | output_artifact_id → id | SET NULL on delete     |
| AuditLog         | Workspace | workspace_id → id       | CASCADE on delete      |
| AuditLog         | User      | actor_id → id           | SET NULL on delete     |
| CostTracking     | Workspace | workspace_id → id       | CASCADE on delete      |
| CostTracking     | Project   | project_id → id         | SET NULL on delete     |

### Unique Constraints

| Table           | Unique Fields               | Purpose                                   |
| --------------- | --------------------------- | ----------------------------------------- |
| User            | email                       | No duplicate accounts                     |
| WorkspaceMember | (workspace_id, user_id)     | One membership per user per workspace     |
| Artifact        | (project_id, type, version) | One artifact per type+version per project |
| ArtifactTag     | (artifact_id, tag)          | One tag per artifact per tag name         |
| GateDecision    | (venture_id, gate_type)     | One decision per gate per venture         |

### Indexes for Performance

```sql
-- Frequently queried combinations
CREATE INDEX idx_ventures_org_phase ON projects(org_id, phase);
CREATE INDEX idx_ventures_org_status ON projects(org_id, status);
CREATE INDEX idx_artifacts_venture_type ON artifacts(venture_id, type);
CREATE INDEX idx_artifacts_venture_version ON artifacts(venture_id, version DESC);
CREATE INDEX idx_telemetry_venture_metric ON telemetry(venture_id, metric_name);
CREATE INDEX idx_telemetry_timestamp ON telemetry(venture_id, timestamp DESC);
CREATE INDEX idx_audit_logs_org_action ON audit_logs(org_id, action);
CREATE INDEX idx_audit_logs_timestamp ON audit_logs(org_id, timestamp DESC);
CREATE INDEX idx_cost_tracking_org_phase ON cost_tracking(org_id, phase);
CREATE INDEX idx_gate_decisions_venture ON gate_decisions(venture_id, gate_type);
CREATE INDEX idx_divergence_reports_venture ON divergence_reports(venture_id, classification);
CREATE INDEX idx_agent_executions_project_phase ON agent_executions(project_id, phase);
CREATE INDEX idx_agent_executions_project_status ON agent_executions(project_id, status);
CREATE INDEX idx_agent_executions_agent_name ON agent_executions(project_id, agent_name);
CREATE INDEX idx_agent_executions_timestamp ON agent_executions(project_id, started_at DESC);
```

---

## 5. Data Validation Rules

### Venture Entity Validation

```typescript
// Entity-level validation rules
class VentureValidation {
  - name: required, 3-255 characters
  - description: optional, max 5000 characters
  - phase: one of [DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY]
  - status: one of [IN_PROGRESS, COMPLETED, FAILED, PAUSED]
  - vrc_score: 0-100 or null
  - vrc_decision: one of [PASS, CONDITIONAL, FAIL] or null
  - vcd_score: 0-100 or null (only if phase ≥ DEFINE)
  - dsp_score: 0-100 or null (only if phase ≥ DESIGN)
  - mdp_score: 0-100 or null (only if phase ≥ DEVELOP)
  - launched_at: timestamp or null (only if phase = DEPLOY)
}
```

### AgentExecution Validation

```typescript
class AgentExecutionValidation {
  - phase: required, one of [DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY]
  - agent_name: required, one of the 26 agents from product brief
  - status: one of [QUEUED, RUNNING, SUCCESS, FAILED, RETRIED, SKIPPED]
  - started_at: timestamp (set when status changes to RUNNING)
  - completed_at: timestamp (set when status changes to SUCCESS/FAILED/SKIPPED)
  - tokens_used_input: ≥0 (null if not applicable)
  - tokens_used_output: ≥0 (null if not applicable)
  - model_used: one of [gpt-4, gpt-4-turbo, claude-3-sonnet, claude-3-opus]
  - cost_usd: ≥0 (calculated from tokens × model pricing)
  - confidence_score: 0-100 or null
  - evidence_tier: one of [E0, E1, E2, E3, E4]
  - retry_count: 0-max_retries
  - output_artifact_id: null OR valid artifact_id in same project
}
```

### GateDecision Validation

```typescript
class GateDecisionValidation {
  - gate_type: one of [VRC, VCD, DSP, MDP]
  - score: 0-100 (integer)
  - decision: one of [PASS, CONDITIONAL, FAIL]
  - decided_by: valid user_id in same org
  - override_by: null OR valid user_id with CTO/ADMIN role
  - indicators: JSON array of 20 items {name, score (0-100), evidence_tier}

  Constraints:
  - If decision = PASS: score ≥ gate_threshold (VRC 76%, VCD 75%, DSP 80%, MDP 85%)
  - If decision = CONDITIONAL: 50% ≤ score < threshold, must have remediation_items
  - If decision = FAIL: score < 50%
  - override_by requires override_reason (non-null, >50 chars)
  - Only one active gate decision per venture per gate_type
}
```

---

## 6. Database Initialization & Migrations

### Initial Seeding (Test Data)

```sql
-- ============================================================================
-- Seed 26 Agent Master Catalog (from Product Brief)
-- ============================================================================

-- DISCOVER Phase (4 Agents)
INSERT INTO agents (name, phase, agent_type, description, model_preference, avg_tokens_input, avg_tokens_output, cost_per_execution) VALUES
('Problem Validation', 'DISCOVER', 'VALIDATOR', 'Validate problem-solution fit and market opportunity', 'gpt-4', 800, 400, 0.03),
('Competitive Analysis', 'DISCOVER', 'ANALYZER', 'Map competitive landscape and identify positioning opportunities', 'gpt-4', 900, 600, 0.04),
('Opportunity Scoring', 'DISCOVER', 'ANALYZER', 'Assess market opportunity size and timing', 'gpt-4', 700, 300, 0.02),
('VRC Assessment', 'DISCOVER', 'ASSEMBLER', 'Comprehensive venture readiness evaluation across 20 indicators', 'gpt-4', 1200, 800, 0.05);

-- DEFINE Phase (5 Agents)
INSERT INTO agents (name, phase, agent_type, description, model_preference, avg_tokens_input, avg_tokens_output, cost_per_execution) VALUES
('Market Research', 'DEFINE', 'ANALYZER', 'Comprehensive competitive analysis and market intelligence', 'gpt-4', 1000, 700, 0.04),
('Persona Building', 'DEFINE', 'GENERATOR', 'Develop detailed user personas and customer journey maps', 'gpt-4-turbo', 800, 600, 0.03),
('Requirements Specification', 'DEFINE', 'GENERATOR', 'Generate comprehensive product requirements and feature specifications', 'gpt-4', 1100, 900, 0.05),
('Feasibility Analysis', 'DEFINE', 'ANALYZER', 'Assess technical, operational, and financial feasibility', 'gpt-4', 900, 500, 0.03),
('VCD Assembly', 'DEFINE', 'ASSEMBLER', 'Synthesize all Define phase outputs into Venture Concept Document', 'gpt-4', 1300, 1000, 0.06);

-- DESIGN Phase (6 Agents)
INSERT INTO agents (name, phase, agent_type, description, model_preference, avg_tokens_input, avg_tokens_output, cost_per_execution) VALUES
('UX/UI Design', 'DESIGN', 'GENERATOR', 'Create intuitive user interface and experience design', 'gpt-4-turbo', 950, 700, 0.04),
('Journey Mapping', 'DESIGN', 'GENERATOR', 'Design optimal user journeys and interaction flows', 'gpt-4', 850, 550, 0.03),
('Branding', 'DESIGN', 'GENERATOR', 'Create brand identity, design system, and visual guidelines', 'gpt-4', 800, 600, 0.03),
('Architecture', 'DESIGN', 'GENERATOR', 'Design system architecture and technology recommendations', 'gpt-4', 1100, 800, 0.05),
('Prototyping', 'DESIGN', 'GENERATOR', 'Generate clickable prototypes from wireframes', 'gpt-4-turbo', 900, 700, 0.04),
('DSP Assembly', 'DESIGN', 'ASSEMBLER', 'Calculate design specification progress and gate decision', 'gpt-4', 1200, 900, 0.06);

-- DEVELOP Phase (7 Agents)
INSERT INTO agents (name, phase, agent_type, description, model_preference, avg_tokens_input, avg_tokens_output, cost_per_execution) VALUES
('Code Generation', 'DEVELOP', 'GENERATOR', 'Generate production-ready backend and frontend code', 'gpt-4', 1500, 1200, 0.08),
('Testing', 'DEVELOP', 'GENERATOR', 'Generate unit, integration, and E2E tests with TDD discipline', 'gpt-4', 1300, 1000, 0.07),
('Documentation', 'DEVELOP', 'GENERATOR', 'Generate API docs, setup guides, architecture decision records', 'gpt-4', 1000, 800, 0.05),
('Security Scanning', 'DEVELOP', 'SCANNER', 'Run SAST, dependency checks, and security validation', 'gpt-4', 800, 400, 0.02),
('Quality Gates', 'DEVELOP', 'VALIDATOR', 'Validate code coverage, security score, and completeness', 'gpt-4', 900, 500, 0.03),
('Performance Optimization', 'DEVELOP', 'OPTIMIZER', 'Optimize code performance, database queries, and resource usage', 'gpt-4-turbo', 1100, 700, 0.05),
('MDP Assembly', 'DEVELOP', 'ASSEMBLER', 'Assemble minimum deployable product and validate readiness', 'gpt-4', 1400, 1100, 0.08);

-- DEPLOY Phase (4 Agents)
INSERT INTO agents (name, phase, agent_type, description, model_preference, avg_tokens_input, avg_tokens_output, cost_per_execution) VALUES
('Infrastructure Provisioning', 'DEPLOY', 'GENERATOR', 'Provision infrastructure on Vercel, Supabase, and AWS', 'gpt-4', 1000, 600, 0.04),
('Monitoring Setup', 'DEPLOY', 'GENERATOR', 'Configure telemetry, dashboards, and monitoring alerts', 'gpt-4', 900, 500, 0.03),
('Optimization', 'DEPLOY', 'OPTIMIZER', 'Optimize deployed application performance and costs', 'gpt-4', 850, 550, 0.03),
('Launch Coordination', 'DEPLOY', 'ASSEMBLER', 'Coordinate go-to-market, landing page, and launch activities', 'gpt-4', 950, 700, 0.04);

-- ============================================================================
-- Seed Test Workspace, User, and Project
-- ============================================================================

-- Create test workspace
INSERT INTO workspaces (id, name, subscription_tier)
VALUES ('workspace_test_1', 'Acme Corp', 'PRO');

-- Create test user
INSERT INTO users (id, email, display_name, auth_provider)
VALUES ('user_test_1', 'founder@acme.com', 'Alice Founder', 'supabase');

-- Add user to workspace
INSERT INTO workspace_members (workspace_id, user_id, role)
VALUES ('workspace_test_1', 'user_test_1', 'OWNER');

-- Create test project
INSERT INTO projects (id, workspace_id, name, description, phase)
VALUES (
  'project_test_1',
  'workspace_test_1',
  'AI Time Tracking',
  'AI-powered time tracking for remote teams',
  'DISCOVER'
);
```

### Migration Strategy

- **Week 3**: Create initial schema (tables, indexes, RLS)
- **Week 4**: Test seeding and performance queries
- **Production**: Schema versioning via Prisma migrations

### Backup & Recovery

- Supabase automated daily backups (30-day retention)
- Point-in-time recovery available
- Monthly export to AWS S3 for long-term retention

---

## Conclusion

**Data Model Features**:
✅ Multi-tenant isolation via PostgreSQL RLS policies  
✅ Complete entity relationships for 5D phases  
✅ Comprehensive audit logging for compliance  
✅ Cost tracking per agent, phase, venture  
✅ Telemetry collection for divergence detection  
✅ Artifact versioning with evidence tier classification  
✅ Performance optimized with strategic indexes

**Ready for Phase 1**: API contract generation and quickstart setup.
