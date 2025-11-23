# DataModel.md: AugentisLabs Entity Relationship Diagram & Core Entities

**Created**: November 23, 2025  
**Purpose**: Complete database design with entity relationships, validation rules, multi-tenant architecture  
**Status**: Foundation for all data operations (Phase 1, Weeks 3-4)

---

## Overview

AugentisLabs uses PostgreSQL 15 with SQLAlchemy 2.x ORM + Alembic migrations. Multi-tenant architecture enforces isolation via:

- **org_id** (organization-level isolation)
- **workspace_id** (sub-organization for enterprise)
- **PostgreSQL Row-Level Security (RLS)** policies on all sensitive tables
- **JWT tokens** containing org_id, validated on every request

---

## Core Entity Relationship Diagram (ERD)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                         MULTI-TENANT ARCHITECTURE                             │
│                                                                                │
│  Organization (org_id)                                                         │
│  ├─ id (PK, UUID)                                                             │
│  ├─ name, slug                                                                │
│  ├─ subscription_tier (BASIC/PRO/ENTERPRISE)                                 │
│  ├─ created_at, updated_at                                                   │
│  └─ Users (1:M) via user_organization                                        │
│                                                                                │
│  ├─ User                                                                       │
│  │  ├─ id (PK, UUID)                                                         │
│  │  ├─ email, full_name                                                      │
│  │  ├─ auth_provider (supabase, google, github)                              │
│  │  ├─ role_in_org (ADMIN/MEMBER/VIEWER)                                    │
│  │  ├─ created_at                                                            │
│  │  └─ → Workspaces (1:M)                                                    │
│  │                                                                            │
│  ├─ Workspace (workspace_id)                                                 │
│  │  ├─ id (PK, UUID)                                                        │
│  │  ├─ org_id (FK) - organization isolation                                 │
│  │  ├─ name, description                                                    │
│  │  ├─ created_by (user_id)                                                │
│  │  ├─ created_at, updated_at                                              │
│  │  └─ → Projects (1:M)                                                    │
│  │                                                                          │
│  │  ├─ Project (project_id)                                                │
│  │  │  ├─ id (PK, UUID)                                                   │
│  │  │  ├─ org_id (FK) - organization isolation                            │
│  │  │  ├─ workspace_id (FK)                                               │
│  │  │  ├─ name, description, problem_statement                            │
│  │  │  ├─ phase (DISCOVER/DEFINE/DESIGN/DEVELOP/DEPLOY)                  │
│  │  │  ├─ created_by, last_updated_by                                     │
│  │  │  ├─ created_at, updated_at                                          │
│  │  │  ├─ is_archived (soft delete)                                       │
│  │  │  └─ Relationships (1:M each):                                       │
│  │  │                                                                      │
│  │  │     A) Artifact (1:M)                                              │
│  │  │        ├─ id (PK, UUID)                                           │
│  │  │        ├─ org_id (FK) - organization isolation                    │
│  │  │        ├─ project_id (FK)                                         │
│  │  │        ├─ type (enum): PROBLEM, PERSONA, MARKET_RESEARCH, PRD,   │
│  │  │        │              POSITIONING, PRICING, GTM, ARCHITECTURE,    │
│  │  │        │              WIREFRAME, PROTOTYPE, API_SPEC, DB_SCHEMA,  │
│  │  │        │              CODE_BACKEND, CODE_FRONTEND, TEST_SUITE,    │
│  │  │        │              DEPLOYMENT_PLAN                            │
│  │  │        ├─ version (v1, v2, v3... - auto-incremented)             │
│  │  │        ├─ status (DRAFT/REVIEW/APPROVED/DEPLOYED)                │
│  │  │        ├─ content (JSON/TEXT)                                    │
│  │  │        ├─ evidence_tier (E0/E1/E2/E3/E4 - from RiskMgmt)         │
│  │  │        ├─ created_by, approved_by                                │
│  │  │        ├─ created_at, approved_at, deployed_at                   │
│  │  │        └─ Tags (1:M) - for versioning & search                   │
│  │  │           ├─ tag_name (e.g., "vrc_gate_submission", "v2_fix")   │
│  │  │           └─ created_at                                          │
│  │  │                                                                   │
│  │  │     B) PhaseGateDecision (1:M)                                   │
│  │  │        ├─ id (PK, UUID)                                         │
│  │  │        ├─ org_id (FK) - organization isolation                  │
│  │  │        ├─ project_id (FK)                                       │
│  │  │        ├─ phase (enum): VRC/VCD/DSP/MDP                        │
│  │  │        ├─ score (0-100)                                        │
│  │  │        ├─ target_threshold (76/75/80/85)                       │
│  │  │        ├─ decision (enum): PASS/CONDITIONAL/FAIL                │
│  │  │        ├─ indicators (JSON array of 20/10/8/6 indicator scores) │
│  │  │        ├─ decided_by (user_id)                                 │
│  │  │        ├─ rationale (text)                                     │
│  │  │        ├─ override_by (user_id, nullable)                      │
│  │  │        ├─ override_reason (nullable)                           │
│  │  │        ├─ created_at, decided_at, override_at                  │
│  │  │        └─ Evidence (references Evidence model)                 │
│  │  │                                                                 │
│  │  │     C) Telemetry (1:M)                                         │
│  │  │        ├─ id (PK, UUID)                                       │
│  │  │        ├─ org_id (FK) - organization isolation                │
│  │  │        ├─ project_id (FK)                                     │
│  │  │        ├─ metric_name (enum): feature_usage, retention,       │
│  │  │        │                       crash_rate, latency_p50,        │
│  │  │        │                       error_rate, user_adoption       │
│  │  │        ├─ metric_value (numeric)                             │
│  │  │        ├─ unit (%, ms, count, rate)                          │
│  │  │        ├─ timestamp (hourly aggregations)                    │
│  │  │        ├─ cohort (user segment: early_adopter, mainstream)  │
│  │  │        └─ created_at                                        │
│  │  │                                                              │
│  │  │     D) DivergenceReport (1:M)                               │
│  │  │        ├─ id (PK, UUID)                                    │
│  │  │        ├─ org_id (FK) - organization isolation             │
│  │  │        ├─ project_id (FK)                                  │
│  │  │        ├─ divergence_type (enum): PERSONA_MISMATCH,       │
│  │  │        │                           FEATURE_ADOPTION_MISS,  │
│  │  │        │                           PRICING_ELASTICITY_MISS,│
│  │  │        │                           LATENCY_MISS            │
│  │  │        ├─ artifact_id (FK - which Define assumption)      │
│  │  │        ├─ assumed_value (what we assumed)                 │
│  │  │        ├─ actual_value (what telemetry shows)             │
│  │  │        ├─ assumption_evidence_tier (E2 at time of Define) │
│  │  │        ├─ telemetry_evidence_tier (E4 from Deploy)        │
│  │  │        ├─ tier_delta (how many tiers off)                 │
│  │  │        ├─ classification (COSMETIC/MINOR/MAJOR)           │
│  │  │        ├─ detected_at, created_at                         │
│  │  │        └─ CascadeAnalysis (1:1)                           │
│  │  │           ├─ affected_features (JSON array)               │
│  │  │           ├─ affected_code_modules (JSON array)           │
│  │  │           ├─ reexecution_scope_days (2-7 days est.)       │
│  │  │           ├─ full_redesign_days (14-21 days est.)         │
│  │  │           └─ recommendation (SKIP/REREXECUTE/FULL_REDESIGN)
│  │  │                                                            │
│  │  │     E) AgentExecution (1:M)                              │
│  │  │        ├─ id (PK, UUID)                                │
│  │  │        ├─ org_id (FK) - organization isolation         │
│  │  │        ├─ project_id (FK)                             │
│  │  │        ├─ agent_id (FK to Agent master catalog)       │
│  │  │        ├─ phase (DISCOVER/DEFINE/DESIGN/DEVELOP)     │
│  │  │        ├─ status (QUEUED/RUNNING/SUCCESS/FAILED)      │
│  │  │        ├─ started_at, completed_at                    │
│  │  │        ├─ tokens_input, tokens_output                 │
│  │  │        ├─ cost_usd (calculated from tokens + model)   │
│  │  │        ├─ model_used (gpt-4, claude-3-sonnet)         │
│  │  │        ├─ output_artifact_id (FK to Artifact)         │
│  │  │        ├─ error_message (if FAILED)                  │
│  │  │        ├─ retry_count, max_retries                   │
│  │  │        └─ created_at                                 │
│  │  │                                                       │
│  │  └─ Permissions (1:M via project_access)               │
│  │     ├─ user_id (FK)                                    │
│  │     ├─ project_id (FK)                                 │
│  │     ├─ role (EDITOR/VIEWER/COMMENTER)                  │
│  │     └─ created_at                                      │
│  │                                                        │
│  ├─ Agent (Master Catalog - 26 total agents)             │
│  │  ├─ id (PK, integer)                                  │
│  │  ├─ name (e.g., "Problem Validator", "PRD Synthesizer")
│  │  ├─ phase (DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY)│
│  │  ├─ agent_type (VALIDATOR, ANALYZER, GENERATOR)       │
│  │  ├─ description, system_prompt                        │
│  │  ├─ model_preference (gpt-4, claude-3-sonnet)         │
│  │  ├─ avg_tokens_input, avg_tokens_output               │
│  │  ├─ cost_per_execution_usd                            │
│  │  ├─ is_active (enable/disable agent)                  │
│  │  ├─ created_at, updated_at                            │
│  │  └─ Tools (1:M) - available to agent                  │
│  │     ├─ tool_name (web_search, file_read, etc.)       │
│  │     └─ tool_config (JSON)                             │
│  │                                                       │
│  ├─ Evidence (1:M)                                      │
│  │  ├─ id (PK, UUID)                                   │
│  │  ├─ org_id (FK) - organization isolation            │
│  │  ├─ project_id (FK)                                │
│  │  ├─ artifact_id (FK - which requirement)           │
│  │  ├─ evidence_tier (E0/E1/E2/E3/E4)                │
│  │  ├─ source (interview, survey, market_research)   │
│  │  ├─ confidence_score (0-100)                      │
│  │  ├─ evidence_count (# of data points)             │
│  │  ├─ url_or_reference                              │
│  │  ├─ created_by, reviewed_by                       │
│  │  ├─ created_at, reviewed_at                       │
│  │  └─ escalation_status (NONE/IN_REVIEW/ESCALATED)  │
│  │                                                    │
│  ├─ AuditLog (1:M)                                   │
│  │  ├─ id (PK, UUID)                                │
│  │  ├─ org_id (FK) - organization isolation         │
│  │  ├─ actor_id (user_id)                          │
│  │  ├─ action (CREATE/UPDATE/DELETE/APPROVE)       │
│  │  ├─ resource_type (Project, Artifact, etc.)     │
│  │  ├─ resource_id                                 │
│  │  ├─ changes_before (JSON)                       │
│  │  ├─ changes_after (JSON)                        │
│  │  ├─ timestamp, ip_address, user_agent           │
│  │  └─ rationale (why the change)                  │
│  │                                                 │
│  └─ CostTracking (1:M)                            │
│     ├─ id (PK, UUID)                             │
│     ├─ org_id (FK) - organization isolation      │
│     ├─ project_id (FK)                           │
│     ├─ agent_id (FK)                             │
│     ├─ phase, tokens_used                        │
│     ├─ cost_usd, model_used                      │
│     ├─ created_at                                │
│     └─ aggregated_at (daily/weekly/monthly)      │
│                                                  │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Entity Definitions

### 1. Organization

**Purpose**: Top-level tenant isolation  
**Fields**:

- `id` (UUID): Primary key
- `name` (string): Organization name (e.g., "Acme Corp")
- `slug` (string): URL slug (e.g., "acme-corp")
- `subscription_tier` (enum): BASIC ($0), PRO ($99/mo), ENTERPRISE (custom)
- `max_projects` (integer): Projects allowed (BASIC: 3, PRO: 20, ENTERPRISE: unlimited)
- `max_agents_per_project` (integer): Concurrent agents (BASIC: 1, PRO: 5, ENTERPRISE: 20)
- `created_at`, `updated_at` (timestamp)

**Validation Rules**:

- `name` length: 3-100 chars
- `slug` format: lowercase alphanumeric + hyphens only
- `slug` uniqueness: global unique constraint

**RLS Policy**:

```sql
-- Users can only see their own organization
CREATE POLICY org_isolation ON organization
  USING (id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 2. User

**Purpose**: Authentication + authorization  
**Fields**:

- `id` (UUID): Primary key
- `email` (string): Unique email
- `full_name` (string): User's full name
- `auth_provider` (enum): supabase, google, github
- `auth_provider_id` (string): External auth ID
- `role_in_org` (enum): ADMIN (full access), MEMBER (can edit), VIEWER (read-only)
- `created_at`, `last_login_at` (timestamp)

**Validation Rules**:

- `email` format: valid email
- `email` uniqueness: global unique constraint
- `full_name` length: 2-100 chars

**JWT Token Claims**:

```json
{
  "sub": "user_id",
  "email": "user@example.com",
  "org_id": "org_id",
  "workspace_id": "workspace_id",
  "role": "ADMIN"
}
```

---

### 3. Workspace

**Purpose**: Sub-organization grouping (for enterprise multi-team)  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization owner (organization isolation)
- `name` (string): Workspace name (e.g., "Product Team")
- `description` (string): Workspace purpose
- `created_by` (UUID FK): Workspace creator
- `created_at`, `updated_at` (timestamp)

**Validation Rules**:

- `name` length: 3-50 chars
- `name` uniqueness: per organization

**RLS Policy**:

```sql
-- Users can only see workspaces in their organization
CREATE POLICY workspace_isolation ON workspace
  USING (org_id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 4. Project

**Purpose**: Individual product/idea being evaluated  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization owner (organization isolation)
- `workspace_id` (UUID FK): Workspace grouping
- `name` (string): Project name
- `description` (string): Project description
- `problem_statement` (text): User's problem statement input
- `phase` (enum): DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY
- `status` (enum): IN_PROGRESS, PASSED, CONDITIONAL, FAILED
- `created_by`, `last_updated_by` (UUID FK): User IDs
- `created_at`, `updated_at` (timestamp)
- `is_archived` (boolean): Soft delete

**Validation Rules**:

- `name` length: 3-100 chars
- `problem_statement` length: 10-5000 chars
- `phase` must be valid enum value
- `status` must be valid enum value

**RLS Policy**:

```sql
-- Users can only see projects in their organization
CREATE POLICY project_isolation ON project
  USING (org_id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 5. Artifact

**Purpose**: Versioned outputs (PRD v1, v2, etc.)  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization owner (organization isolation)
- `project_id` (UUID FK): Project it belongs to
- `type` (enum): PROBLEM, PERSONA, MARKET_RESEARCH, PRD, POSITIONING, PRICING, GTM, WIREFRAME, API_SPEC, etc. (15+ types)
- `version` (integer): Auto-incremented (v1=1, v2=2)
- `status` (enum): DRAFT, REVIEW, APPROVED, DEPLOYED
- `content` (jsonb): Artifact content (text, JSON, or references)
- `evidence_tier` (enum): E0-E4 classification (from RiskManagement)
- `created_by`, `approved_by` (UUID FK): User IDs
- `created_at`, `approved_at`, `deployed_at` (timestamp)

**Validation Rules**:

- `type` must be valid enum
- `version` auto-incremented (not user-editable)
- `content` nullable (some artifacts generated asynchronously)
- `evidence_tier` defaults to E0 (must be escalated to E2+ for gates)

**Indexes**:

- `(project_id, type, version DESC)` - get latest version of artifact type
- `(org_id, created_at DESC)` - audit trail per org
- `(evidence_tier)` - find E0 evidence for escalation

---

### 6. Tag (for Artifacts)

**Purpose**: Version tracking + search  
**Fields**:

- `id` (UUID): Primary key
- `artifact_id` (UUID FK)
- `tag_name` (string): e.g., "vrc_gate_submission", "founder_approved", "v2_fix_pricing"
- `created_at` (timestamp)

**Examples**:

- "vrc_gate_submission" - submitted for VRC gate evaluation
- "vcd_gate_passed" - passed VCD gate
- "divergence_fix_v2" - fixes a discovered divergence

---

### 7. PhaseGateDecision

**Purpose**: Gate evaluation & decision audit trail  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `project_id` (UUID FK): Project being gated
- `phase` (enum): VRC, VCD, DSP, MDP
- `score` (integer, 0-100): Calculated gate score
- `target_threshold` (integer): 76 (VRC), 75 (VCD), 80 (DSP), 85 (MDP)
- `decision` (enum): PASS (≥ threshold), CONDITIONAL (50-threshold-1), FAIL (<50)
- `indicators` (jsonb): Array of indicator scores (20 for VRC, 10 for VCD, 8 for DSP, 6 for MDP)
- `decided_by` (UUID FK): CTO or Product Lead
- `rationale` (text): Why this decision was made
- `override_by` (UUID FK, nullable): Who overrode the gate
- `override_reason` (text, nullable): Why it was overridden
- `created_at`, `decided_at`, `override_at` (timestamp)

**Example Indicators (VRC Gate)**:

```json
{
  "indicators": [
    {"name": "problem_clarity", "score": 85},
    {"name": "market_size_tam", "score": 72},
    {"name": "target_customer_identified", "score": 88},
    ...
    // 20 total indicators
  ]
}
```

---

### 8. Telemetry

**Purpose**: Production metrics for divergence detection  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `project_id` (UUID FK): Project being monitored
- `metric_name` (enum): feature_usage, retention, crash_rate, latency_p50, error_rate, user_adoption
- `metric_value` (numeric): The actual value
- `unit` (string): %, ms, count, rate
- `timestamp` (timestamp): When collected (hourly aggregations)
- `cohort` (string): User segment (early_adopter, mainstream, enterprise)

**Examples**:

- metric_name: "user_adoption", metric_value: 52, unit: "%", cohort: "mainstream"
- metric_name: "latency_p50", metric_value: 480, unit: "ms"
- metric_name: "crash_rate", metric_value: 0.1, unit: "%"

**RLS Policy**:

```sql
CREATE POLICY telemetry_isolation ON telemetry
  USING (org_id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 9. DivergenceReport

**Purpose**: Detected assumption mismatches (Define vs. Deploy)  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `project_id` (UUID FK): Project
- `divergence_type` (enum): PERSONA_MISMATCH, FEATURE_ADOPTION_MISS, PRICING_ELASTICITY_MISS, LATENCY_MISS
- `artifact_id` (UUID FK): Which Define assumption
- `assumed_value` (string): What we assumed
- `actual_value` (string): What telemetry shows
- `assumption_evidence_tier` (enum): E2 (when Define was created)
- `telemetry_evidence_tier` (enum): E4 (from Deploy phase production data)
- `tier_delta` (integer): 0-4 (how many tiers off)
- `classification` (enum): COSMETIC (<20% delta), MINOR (1-2 tier delta), MAJOR (>2 tier delta)
- `detected_at`, `created_at` (timestamp)

**RLS Policy**:

```sql
CREATE POLICY divergence_isolation ON divergence_report
  USING (org_id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 10. CascadeAnalysis (linked to DivergenceReport)

**Purpose**: Scope estimation for divergence re-execution  
**Fields**:

- `id` (UUID): Primary key
- `divergence_id` (UUID FK): Which divergence
- `affected_features` (jsonb array): ["F12 PRD", "F13 Positioning", "F20 Wireframes"]
- `affected_code_modules` (jsonb array): ["pricing_service", "pricing_api", "pricing_frontend"]
- `reexecution_scope_days` (integer): 2-7 days estimated
- `full_redesign_days` (integer): 14-21 days estimated
- `recommendation` (enum): SKIP, REEXECUTE, FULL_REDESIGN
- `created_at` (timestamp)

**Decision Logic**:

```
IF tier_delta > 2 AND affected_modules > 5:
  recommendation = FULL_REDESIGN
ELIF tier_delta > 1 OR affected_modules > 2:
  recommendation = REEXECUTE
ELSE:
  recommendation = SKIP
```

---

### 11. AgentExecution

**Purpose**: Audit trail for all agent runs + cost tracking  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `project_id` (UUID FK): Project
- `agent_id` (integer FK): Agent from master catalog
- `phase` (enum): DISCOVER, DEFINE, DESIGN, DEVELOP
- `status` (enum): QUEUED, RUNNING, SUCCESS, FAILED, RETRIED
- `started_at`, `completed_at` (timestamp)
- `tokens_input`, `tokens_output` (integer): Token usage
- `cost_usd` (decimal): Calculated from tokens + model pricing
- `model_used` (string): "gpt-4", "claude-3-sonnet"
- `output_artifact_id` (UUID FK, nullable): Artifact it created
- `error_message` (text, nullable): If FAILED
- `retry_count`, `max_retries` (integer)
- `created_at` (timestamp)

**Cost Calculation**:

```
cost_usd = (tokens_input * input_price + tokens_output * output_price)
# Example: GPT-4: $0.03/1K input, $0.06/1K output
```

**RLS Policy**:

```sql
CREATE POLICY agent_execution_isolation ON agent_execution
  USING (org_id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 12. Evidence

**Purpose**: Track evidence tier classification + escalations  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `project_id` (UUID FK): Project
- `artifact_id` (UUID FK): Which requirement/assumption
- `evidence_tier` (enum): E0-E4
- `source` (enum): interview, survey, market_research, telemetry, analyst_report
- `confidence_score` (integer, 0-100)
- `evidence_count` (integer): # of data points (10 interviews = 10)
- `url_or_reference` (string): Link to evidence
- `created_by`, `reviewed_by` (UUID FK): User IDs
- `created_at`, `reviewed_at` (timestamp)
- `escalation_status` (enum): NONE, IN_REVIEW, ESCALATED

**Tier Elevation Rules**:

```
E0 → E1: 1+ data point collected
E1 → E2: 10+ customer interviews OR market research paper
E2 → E3: Industry analyst confirmation (Gartner, Forrester)
E3 → E4: 1000+ user production telemetry
```

---

### 13. AuditLog

**Purpose**: Complete audit trail for compliance  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `actor_id` (UUID FK): Which user made the change
- `action` (enum): CREATE, UPDATE, DELETE, APPROVE, OVERRIDE, ESCALATE
- `resource_type` (enum): Project, Artifact, PhaseGateDecision, Evidence, etc.
- `resource_id` (UUID FK): Which resource
- `changes_before` (jsonb): Before state
- `changes_after` (jsonb): After state
- `timestamp` (timestamp): When it happened
- `ip_address`, `user_agent` (strings): Client info
- `rationale` (text): Why the change

**Example**:

```json
{
  "actor_id": "user_123",
  "action": "UPDATE",
  "resource_type": "Artifact",
  "resource_id": "artifact_456",
  "changes_before": { "status": "DRAFT" },
  "changes_after": { "status": "APPROVED" },
  "timestamp": "2025-11-23T14:30:00Z",
  "rationale": "PRD meets all VCD indicators, approved by Product Lead"
}
```

**RLS Policy**:

```sql
CREATE POLICY audit_log_isolation ON audit_log
  USING (org_id IN (SELECT org_id FROM organization_members WHERE user_id = auth.uid()));
```

---

### 14. CostTracking

**Purpose**: Aggregate costs by organization, project, phase, agent  
**Fields**:

- `id` (UUID): Primary key
- `org_id` (UUID FK): Organization isolation
- `project_id` (UUID FK): Project
- `agent_id` (integer FK): Agent
- `phase` (enum): DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY
- `tokens_used` (integer): Total tokens for this aggregation
- `cost_usd` (decimal): Calculated cost
- `model_used` (string): gpt-4, claude-3-sonnet
- `created_at` (timestamp)
- `aggregated_at` (enum): daily, weekly, monthly

**Queries**:

- Total cost per organization (monthly)
- Cost per project
- Cost per phase
- Cost per agent (to see which agents are expensive)

---

### 15. Agent (Master Catalog)

**Purpose**: Reference table for all 26 agents  
**Fields**:

- `id` (integer): Primary key (1-26)
- `name` (string): Agent name
- `phase` (enum): DISCOVER, DEFINE, DESIGN, DEVELOP, DEPLOY
- `agent_type` (enum): VALIDATOR, ANALYZER, GENERATOR, ASSEMBLER
- `description` (text): What the agent does
- `system_prompt` (text): LLM system message
- `model_preference` (string): gpt-4, claude-3-sonnet
- `avg_tokens_input`, `avg_tokens_output` (integer): For cost estimation
- `cost_per_execution_usd` (decimal): Average cost
- `is_active` (boolean): Enable/disable agent
- `created_at`, `updated_at` (timestamp)

**26 Agents**:

```
Discover (4):
1. Problem Validator - validates problem statement clarity
2. Competitive Researcher - analyzes competitor landscape
3. Market Analyzer - calculates TAM/SAM/SOM
4. Evidence Collector - collects market signals

Define (5):
5. Market Research Synthesizer - compiles market research
6. Persona Generator - creates user personas
7. PRD Synthesizer - generates PRD from research
8. Positioning Engine - develops competitive positioning
9. Pricing Calculator - designs pricing model

Design (6):
10. Wireframe Generator - creates UI wireframes
11. Journey Mapper - generates user journey maps
12. API Designer - creates API specifications
13. Architecture Designer - generates system architecture
14. Data Modeler - creates database schema
15. Prototype Builder - generates interactive prototypes

Develop (7):
16. Backend Generator - generates FastAPI code
17. Frontend Generator - generates React code
18. Agent Generator - generates LangGraph agents
19. Test Generator - generates TDD test suites
20. CI/CD Generator - generates GitHub Actions
21. Quality Validator - runs code quality checks
22. Multi-Tenant Enforcer - sets up RLS policies

Deploy (4):
23. Infrastructure Provisioner - generates Terraform
24. Monitoring Setup - configures Grafana dashboards
25. Divergence Detector - monitors for assumption mismatches
26. Launch Coordinator - manages production deployment
```

---

## Multi-Tenant Isolation Strategy

**Layer 1: Application Layer**

- JWT token contains `org_id`
- FastAPI middleware validates `org_id` on every request
- All queries filtered by `org_id`

**Layer 2: Database Layer (RLS)**

```sql
-- Row-Level Security on all sensitive tables
CREATE POLICY org_isolation ON project
  USING (org_id IN (SELECT org_id FROM organization_members
                   WHERE user_id = auth.uid()));

CREATE POLICY org_isolation ON artifact
  USING (org_id IN (SELECT org_id FROM organization_members
                   WHERE user_id = auth.uid()));

-- (similar policies on all sensitive tables)
```

**Layer 3: Schema Design**

- Every table has `org_id` column (non-nullable)
- All foreign keys include organization context
- Composite indexes: (org_id, id) for fast lookups

---

## Version Control

**DataModel Version**: 1.0.0  
**Created**: November 23, 2025  
**Status**: Foundation for all data operations  
**Amendment Process**: See Constitution.md (stack changes require written approval)

---

## References

- **DataSchema.md**: SQLAlchemy ORM models, field types, validation rules
- **MasterTasks.md**: Setup 1.4 - Authentication & Multi-Tenant Foundation (tasks for implementation)
- **RiskManagement.md**: Evidence tier classification (E0-E4)
- **TraceabilityMap.md**: DivergenceReport → CascadeAnalysis → Remediation workflow
