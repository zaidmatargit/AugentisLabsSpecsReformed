# DataSchema.md: SQLAlchemy ORM Models & Database Schema

**Created**: November 23, 2025  
**Purpose**: Complete SQLAlchemy 2.x models, field definitions, validation rules, relationships  
**Status**: Ready for code generation (Phase 1, Setup 1.4, tasks S1.4.1-S1.4.8)

---

## Overview

All models use:

- **SQLAlchemy 2.x** with async support
- **Pydantic** for request/response validation
- **UUID primary keys** (gen_random_uuid())
- **Timestamps** (created_at, updated_at with defaults)
- **Multi-tenant isolation** (org_id on every table)
- **PostgreSQL 15** specific features (jsonb, RLS)

---

## Core Models

### 1. Organization Model

```python
from sqlalchemy import Column, String, Integer, Boolean, DateTime, Enum as SQLEnum
from sqlalchemy.dialects.postgresql import UUID
from sqlalchemy.orm import relationship
from datetime import datetime
import uuid

class Organization(Base):
    __tablename__ = "organizations"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    name = Column(String(100), nullable=False, index=True)
    slug = Column(String(50), nullable=False, unique=True, index=True)
    subscription_tier = Column(
        SQLEnum("BASIC", "PRO", "ENTERPRISE", name="subscription_tier"),
        nullable=False,
        default="BASIC"
    )
    max_projects = Column(Integer, nullable=False)  # BASIC: 3, PRO: 20, ENTERPRISE: unlimited
    max_agents_per_project = Column(Integer, nullable=False)  # BASIC: 1, PRO: 5, ENTERPRISE: 20
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow, index=True)
    updated_at = Column(DateTime, nullable=False, default=datetime.utcnow, onupdate=datetime.utcnow)

    # Relationships
    workspaces = relationship("Workspace", back_populates="organization", cascade="all, delete-orphan")
    projects = relationship("Project", back_populates="organization", cascade="all, delete-orphan")
    users = relationship("User", secondary="organization_members", back_populates="organizations")
    audit_logs = relationship("AuditLog", back_populates="organization")

    # Validation
    @validates('name')
    def validate_name(self, key, value):
        if not value or len(value) < 3 or len(value) > 100:
            raise ValueError("Name must be 3-100 characters")
        return value

    @validates('slug')
    def validate_slug(self, key, value):
        import re
        if not re.match(r'^[a-z0-9-]+$', value):
            raise ValueError("Slug must contain only lowercase letters, numbers, and hyphens")
        return value
```

---

### 2. User Model

```python
class User(Base):
    __tablename__ = "users"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    email = Column(String(255), nullable=False, unique=True, index=True)
    full_name = Column(String(100), nullable=False)
    auth_provider = Column(
        SQLEnum("supabase", "google", "github", name="auth_provider"),
        nullable=False
    )
    auth_provider_id = Column(String(255), nullable=False, index=True)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    last_login_at = Column(DateTime, nullable=True)

    # Relationships
    organizations = relationship("Organization", secondary="organization_members", back_populates="users")
    workspaces = relationship("Workspace", back_populates="created_by_user")
    projects = relationship("Project", back_populates="created_by_user")
    audit_logs = relationship("AuditLog", back_populates="actor")

    # Validation
    @validates('email')
    def validate_email(self, key, value):
        import re
        if not re.match(r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$', value):
            raise ValueError("Invalid email format")
        return value
```

**Association Table**:

```python
organization_members = Table(
    'organization_members',
    Base.metadata,
    Column('user_id', UUID(as_uuid=True), ForeignKey('users.id'), primary_key=True),
    Column('org_id', UUID(as_uuid=True), ForeignKey('organizations.id'), primary_key=True),
    Column('role', SQLEnum("ADMIN", "MEMBER", "VIEWER", name="user_role"), default="MEMBER"),
    Column('created_at', DateTime, default=datetime.utcnow),
)
```

---

### 3. Workspace Model

```python
class Workspace(Base):
    __tablename__ = "workspaces"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    name = Column(String(50), nullable=False, index=True)
    description = Column(String(500), nullable=True)
    created_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    updated_at = Column(DateTime, nullable=False, default=datetime.utcnow, onupdate=datetime.utcnow)

    # Relationships
    organization = relationship("Organization", back_populates="workspaces")
    created_by_user = relationship("User", back_populates="workspaces")
    projects = relationship("Project", back_populates="workspace", cascade="all, delete-orphan")

    # Indexes
    __table_args__ = (
        Index('idx_workspace_org_name', 'org_id', 'name'),
    )

    # Validation
    @validates('name')
    def validate_name(self, key, value):
        if not value or len(value) < 3 or len(value) > 50:
            raise ValueError("Name must be 3-50 characters")
        return value
```

**RLS Policy**:

```sql
ALTER TABLE workspaces ENABLE ROW LEVEL SECURITY;
CREATE POLICY workspace_org_isolation ON workspaces
  USING (org_id IN (
    SELECT org_id FROM organization_members
    WHERE user_id = auth.uid()
  ));
```

---

### 4. Project Model

```python
class Project(Base):
    __tablename__ = "projects"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    workspace_id = Column(UUID(as_uuid=True), ForeignKey('workspaces.id'), nullable=False, index=True)
    name = Column(String(100), nullable=False, index=True)
    description = Column(String(1000), nullable=True)
    problem_statement = Column(String(5000), nullable=True)

    phase = Column(
        SQLEnum("DISCOVER", "DEFINE", "DESIGN", "DEVELOP", "DEPLOY", name="project_phase"),
        nullable=False,
        default="DISCOVER",
        index=True
    )
    status = Column(
        SQLEnum("IN_PROGRESS", "PASSED", "CONDITIONAL", "FAILED", name="project_status"),
        nullable=False,
        default="IN_PROGRESS"
    )

    created_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)
    last_updated_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow, index=True)
    updated_at = Column(DateTime, nullable=False, default=datetime.utcnow, onupdate=datetime.utcnow)
    is_archived = Column(Boolean, nullable=False, default=False, index=True)

    # Relationships
    organization = relationship("Organization", back_populates="projects")
    workspace = relationship("Workspace", back_populates="projects")
    created_by_user = relationship("User", foreign_keys=[created_by], back_populates="projects")
    artifacts = relationship("Artifact", back_populates="project", cascade="all, delete-orphan")
    gate_decisions = relationship("PhaseGateDecision", back_populates="project", cascade="all, delete-orphan")
    telemetry = relationship("Telemetry", back_populates="project", cascade="all, delete-orphan")
    divergences = relationship("DivergenceReport", back_populates="project", cascade="all, delete-orphan")
    agent_executions = relationship("AgentExecution", back_populates="project", cascade="all, delete-orphan")
    evidence = relationship("Evidence", back_populates="project", cascade="all, delete-orphan")
    cost_tracking = relationship("CostTracking", back_populates="project", cascade="all, delete-orphan")

    # Indexes
    __table_args__ = (
        Index('idx_project_org_workspace', 'org_id', 'workspace_id'),
        Index('idx_project_org_created', 'org_id', 'created_at'),
        Index('idx_project_phase_status', 'phase', 'status'),
    )

    # Validation
    @validates('name')
    def validate_name(self, key, value):
        if not value or len(value) < 3 or len(value) > 100:
            raise ValueError("Name must be 3-100 characters")
        return value

    @validates('problem_statement')
    def validate_problem_statement(self, key, value):
        if value and (len(value) < 10 or len(value) > 5000):
            raise ValueError("Problem statement must be 10-5000 characters")
        return value
```

**RLS Policy**:

```sql
ALTER TABLE projects ENABLE ROW LEVEL SECURITY;
CREATE POLICY project_org_isolation ON projects
  USING (org_id IN (
    SELECT org_id FROM organization_members
    WHERE user_id = auth.uid()
  ));
```

---

### 5. Artifact Model

```python
class Artifact(Base):
    __tablename__ = "artifacts"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False, index=True)

    type = Column(
        SQLEnum(
            "PROBLEM", "PERSONA", "MARKET_RESEARCH", "PRD", "POSITIONING",
            "PRICING", "GTM", "ARCHITECTURE", "WIREFRAME", "PROTOTYPE",
            "API_SPEC", "DB_SCHEMA", "CODE_BACKEND", "CODE_FRONTEND",
            "TEST_SUITE", "DEPLOYMENT_PLAN",
            name="artifact_type"
        ),
        nullable=False,
        index=True
    )
    version = Column(Integer, nullable=False, default=1, index=True)  # Auto-incremented per artifact_type
    status = Column(
        SQLEnum("DRAFT", "REVIEW", "APPROVED", "DEPLOYED", name="artifact_status"),
        nullable=False,
        default="DRAFT"
    )

    content = Column(JSON, nullable=True)  # Artifact content (text, JSON, or references)
    evidence_tier = Column(
        SQLEnum("E0", "E1", "E2", "E3", "E4", name="evidence_tier"),
        nullable=False,
        default="E0",
        index=True
    )

    created_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)
    approved_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=True)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow, index=True)
    approved_at = Column(DateTime, nullable=True)
    deployed_at = Column(DateTime, nullable=True)

    # Relationships
    project = relationship("Project", back_populates="artifacts")
    tags = relationship("ArtifactTag", back_populates="artifact", cascade="all, delete-orphan")
    evidence = relationship("Evidence", back_populates="artifact")
    gate_decisions = relationship("PhaseGateDecision", back_populates="artifact")

    # Indexes
    __table_args__ = (
        Index('idx_artifact_project_type_version', 'project_id', 'type', 'version'),
        Index('idx_artifact_org_created', 'org_id', 'created_at'),
        Index('idx_artifact_evidence_tier', 'evidence_tier'),
    )
```

**Version Auto-Increment Logic**:

```python
@event.listens_for(Artifact, 'before_insert')
def increment_artifact_version(mapper, connection, target):
    # Get latest version for this artifact type
    latest = connection.execute(
        select(func.max(Artifact.version))
        .where(
            (Artifact.project_id == target.project_id) &
            (Artifact.type == target.type)
        )
    ).scalar()
    target.version = (latest or 0) + 1
```

---

### 6. ArtifactTag Model

```python
class ArtifactTag(Base):
    __tablename__ = "artifact_tags"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    artifact_id = Column(UUID(as_uuid=True), ForeignKey('artifacts.id'), nullable=False, index=True)
    tag_name = Column(String(100), nullable=False)  # e.g., "vrc_gate_submission"
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)

    # Relationships
    artifact = relationship("Artifact", back_populates="tags")

    # Indexes
    __table_args__ = (
        Index('idx_artifact_tag_name', 'artifact_id', 'tag_name'),
    )
```

---

### 7. PhaseGateDecision Model

```python
class PhaseGateDecision(Base):
    __tablename__ = "phase_gate_decisions"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False, index=True)
    artifact_id = Column(UUID(as_uuid=True), ForeignKey('artifacts.id'), nullable=True)

    phase = Column(
        SQLEnum("VRC", "VCD", "DSP", "MDP", name="gate_phase"),
        nullable=False,
        index=True
    )
    score = Column(Integer, nullable=False)  # 0-100
    target_threshold = Column(Integer, nullable=False)  # 76, 75, 80, or 85
    decision = Column(
        SQLEnum("PASS", "CONDITIONAL", "FAIL", name="gate_decision"),
        nullable=False
    )

    indicators = Column(JSON, nullable=False)  # Array of indicator scores
    decided_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)
    rationale = Column(String(2000), nullable=False)

    override_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=True)
    override_reason = Column(String(2000), nullable=True)

    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    decided_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    override_at = Column(DateTime, nullable=True)

    # Relationships
    project = relationship("Project", back_populates="gate_decisions")
    artifact = relationship("Artifact", back_populates="gate_decisions")
    decided_by_user = relationship("User", foreign_keys=[decided_by])

    # Indexes
    __table_args__ = (
        Index('idx_gate_project_phase', 'project_id', 'phase'),
    )
```

---

### 8. Telemetry Model

```python
class Telemetry(Base):
    __tablename__ = "telemetry"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False, index=True)

    metric_name = Column(
        SQLEnum(
            "feature_usage", "retention", "crash_rate", "latency_p50",
            "error_rate", "user_adoption", "churn_rate", "nps_score",
            name="metric_name"
        ),
        nullable=False,
        index=True
    )
    metric_value = Column(Numeric(10, 4), nullable=False)
    unit = Column(String(20), nullable=False)  # %, ms, count, rate, score
    timestamp = Column(DateTime, nullable=False, index=True)  # Hourly aggregations
    cohort = Column(String(100), nullable=True)  # early_adopter, mainstream, enterprise

    # Relationships
    project = relationship("Project", back_populates="telemetry")

    # Indexes
    __table_args__ = (
        Index('idx_telemetry_project_metric', 'project_id', 'metric_name', 'timestamp'),
        Index('idx_telemetry_org_timestamp', 'org_id', 'timestamp'),
    )
```

**RLS Policy**:

```sql
ALTER TABLE telemetry ENABLE ROW LEVEL SECURITY;
CREATE POLICY telemetry_org_isolation ON telemetry
  USING (org_id IN (
    SELECT org_id FROM organization_members
    WHERE user_id = auth.uid()
  ));
```

---

### 9. DivergenceReport Model

```python
class DivergenceReport(Base):
    __tablename__ = "divergence_reports"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False, index=True)

    divergence_type = Column(
        SQLEnum(
            "PERSONA_MISMATCH", "FEATURE_ADOPTION_MISS",
            "PRICING_ELASTICITY_MISS", "LATENCY_MISS",
            name="divergence_type"
        ),
        nullable=False
    )
    artifact_id = Column(UUID(as_uuid=True), ForeignKey('artifacts.id'), nullable=False)

    assumed_value = Column(String(500), nullable=False)
    actual_value = Column(String(500), nullable=False)
    assumption_evidence_tier = Column(
        SQLEnum("E0", "E1", "E2", "E3", "E4", name="evidence_tier"),
        nullable=False
    )
    telemetry_evidence_tier = Column(
        SQLEnum("E0", "E1", "E2", "E3", "E4", name="evidence_tier"),
        nullable=False
    )
    tier_delta = Column(Integer, nullable=False)  # 0-4
    classification = Column(
        SQLEnum("COSMETIC", "MINOR", "MAJOR", name="divergence_classification"),
        nullable=False
    )

    detected_at = Column(DateTime, nullable=False, default=datetime.utcnow, index=True)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)

    # Relationships
    project = relationship("Project", back_populates="divergences")
    cascade_analysis = relationship("CascadeAnalysis", back_populates="divergence", uselist=False, cascade="all, delete-orphan")

    # Indexes
    __table_args__ = (
        Index('idx_divergence_project_type', 'project_id', 'divergence_type'),
        Index('idx_divergence_classification', 'classification'),
    )
```

**RLS Policy**:

```sql
ALTER TABLE divergence_reports ENABLE ROW LEVEL SECURITY;
CREATE POLICY divergence_org_isolation ON divergence_reports
  USING (org_id IN (
    SELECT org_id FROM organization_members
    WHERE user_id = auth.uid()
  ));
```

---

### 10. CascadeAnalysis Model

```python
class CascadeAnalysis(Base):
    __tablename__ = "cascade_analyses"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    divergence_id = Column(UUID(as_uuid=True), ForeignKey('divergence_reports.id'), nullable=False, unique=True)

    affected_features = Column(JSON, nullable=False)  # ["F12 PRD", "F13 Positioning", ...]
    affected_code_modules = Column(JSON, nullable=False)  # ["pricing_service", "pricing_api", ...]

    reexecution_scope_days = Column(Integer, nullable=False)  # 2-7 days estimated
    full_redesign_days = Column(Integer, nullable=False)  # 14-21 days estimated

    recommendation = Column(
        SQLEnum("SKIP", "REEXECUTE", "FULL_REDESIGN", name="cascade_recommendation"),
        nullable=False
    )

    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)

    # Relationships
    divergence = relationship("DivergenceReport", back_populates="cascade_analysis")
```

---

### 11. AgentExecution Model

```python
class AgentExecution(Base):
    __tablename__ = "agent_executions"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False, index=True)
    agent_id = Column(Integer, ForeignKey('agents.id'), nullable=False)

    phase = Column(
        SQLEnum("DISCOVER", "DEFINE", "DESIGN", "DEVELOP", "DEPLOY", name="project_phase"),
        nullable=False
    )
    status = Column(
        SQLEnum("QUEUED", "RUNNING", "SUCCESS", "FAILED", "RETRIED", name="agent_status"),
        nullable=False,
        index=True
    )

    started_at = Column(DateTime, nullable=True)
    completed_at = Column(DateTime, nullable=True)

    tokens_input = Column(Integer, nullable=True)
    tokens_output = Column(Integer, nullable=True)
    cost_usd = Column(Numeric(10, 4), nullable=True)
    model_used = Column(String(50), nullable=True)

    output_artifact_id = Column(UUID(as_uuid=True), ForeignKey('artifacts.id'), nullable=True)
    error_message = Column(String(2000), nullable=True)

    retry_count = Column(Integer, nullable=False, default=0)
    max_retries = Column(Integer, nullable=False, default=3)

    created_at = Column(DateTime, nullable=False, default=datetime.utcnow, index=True)

    # Relationships
    project = relationship("Project", back_populates="agent_executions")
    agent = relationship("Agent", back_populates="executions")

    # Indexes
    __table_args__ = (
        Index('idx_agent_exec_project_status', 'project_id', 'status'),
        Index('idx_agent_exec_org_created', 'org_id', 'created_at'),
    )
```

**RLS Policy**:

```sql
ALTER TABLE agent_executions ENABLE ROW LEVEL SECURITY;
CREATE POLICY agent_exec_org_isolation ON agent_executions
  USING (org_id IN (
    SELECT org_id FROM organization_members
    WHERE user_id = auth.uid()
  ));
```

---

### 12. Evidence Model

```python
class Evidence(Base):
    __tablename__ = "evidence"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False, index=True)
    artifact_id = Column(UUID(as_uuid=True), ForeignKey('artifacts.id'), nullable=False)

    evidence_tier = Column(
        SQLEnum("E0", "E1", "E2", "E3", "E4", name="evidence_tier"),
        nullable=False,
        index=True
    )
    source = Column(
        SQLEnum(
            "interview", "survey", "market_research", "telemetry",
            "analyst_report", name="evidence_source"
        ),
        nullable=False
    )
    confidence_score = Column(Integer, nullable=False)  # 0-100
    evidence_count = Column(Integer, nullable=False)  # Number of data points
    url_or_reference = Column(String(500), nullable=True)

    created_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)
    reviewed_by = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=True)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    reviewed_at = Column(DateTime, nullable=True)

    escalation_status = Column(
        SQLEnum("NONE", "IN_REVIEW", "ESCALATED", name="escalation_status"),
        nullable=False,
        default="NONE"
    )

    # Relationships
    project = relationship("Project", back_populates="evidence")
    artifact = relationship("Artifact", back_populates="evidence")
```

---

### 13. AuditLog Model

```python
class AuditLog(Base):
    __tablename__ = "audit_logs"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    actor_id = Column(UUID(as_uuid=True), ForeignKey('users.id'), nullable=False)

    action = Column(
        SQLEnum(
            "CREATE", "UPDATE", "DELETE", "APPROVE",
            "OVERRIDE", "ESCALATE", name="audit_action"
        ),
        nullable=False
    )
    resource_type = Column(String(100), nullable=False)  # Project, Artifact, etc.
    resource_id = Column(UUID(as_uuid=True), nullable=False)

    changes_before = Column(JSON, nullable=True)
    changes_after = Column(JSON, nullable=True)

    timestamp = Column(DateTime, nullable=False, default=datetime.utcnow, index=True)
    ip_address = Column(String(45), nullable=True)  # IPv4 or IPv6
    user_agent = Column(String(500), nullable=True)
    rationale = Column(String(1000), nullable=False)

    # Relationships
    organization = relationship("Organization", back_populates="audit_logs")
    actor = relationship("User", back_populates="audit_logs")

    # Indexes
    __table_args__ = (
        Index('idx_audit_org_timestamp', 'org_id', 'timestamp'),
        Index('idx_audit_resource', 'resource_type', 'resource_id'),
    )
```

**RLS Policy**:

```sql
ALTER TABLE audit_logs ENABLE ROW LEVEL SECURITY;
CREATE POLICY audit_log_org_isolation ON audit_logs
  USING (org_id IN (
    SELECT org_id FROM organization_members
    WHERE user_id = auth.uid()
  ));
```

---

### 14. CostTracking Model

```python
class CostTracking(Base):
    __tablename__ = "cost_tracking"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    org_id = Column(UUID(as_uuid=True), ForeignKey('organizations.id'), nullable=False, index=True)
    project_id = Column(UUID(as_uuid=True), ForeignKey('projects.id'), nullable=False)
    agent_id = Column(Integer, ForeignKey('agents.id'), nullable=True)

    phase = Column(
        SQLEnum("DISCOVER", "DEFINE", "DESIGN", "DEVELOP", "DEPLOY", name="project_phase"),
        nullable=False
    )

    tokens_used = Column(Integer, nullable=False)
    cost_usd = Column(Numeric(10, 4), nullable=False)
    model_used = Column(String(50), nullable=False)

    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    aggregated_at = Column(
        SQLEnum("daily", "weekly", "monthly", name="aggregation_period"),
        nullable=False
    )

    # Relationships
    project = relationship("Project", back_populates="cost_tracking")

    # Indexes
    __table_args__ = (
        Index('idx_cost_org_period', 'org_id', 'aggregated_at'),
        Index('idx_cost_project_agent', 'project_id', 'agent_id'),
    )
```

---

### 15. Agent Model (Master Catalog)

```python
class Agent(Base):
    __tablename__ = "agents"

    id = Column(Integer, primary_key=True)  # 1-26
    name = Column(String(100), nullable=False, unique=True)
    phase = Column(
        SQLEnum("DISCOVER", "DEFINE", "DESIGN", "DEVELOP", "DEPLOY", name="project_phase"),
        nullable=False
    )
    agent_type = Column(
        SQLEnum("VALIDATOR", "ANALYZER", "GENERATOR", "ASSEMBLER", name="agent_type"),
        nullable=False
    )
    description = Column(String(500), nullable=False)
    system_prompt = Column(String(2000), nullable=False)

    model_preference = Column(String(50), nullable=False)  # gpt-4, claude-3-sonnet
    avg_tokens_input = Column(Integer, nullable=False)
    avg_tokens_output = Column(Integer, nullable=False)
    cost_per_execution_usd = Column(Numeric(8, 4), nullable=False)

    is_active = Column(Boolean, nullable=False, default=True, index=True)
    created_at = Column(DateTime, nullable=False, default=datetime.utcnow)
    updated_at = Column(DateTime, nullable=False, default=datetime.utcnow, onupdate=datetime.utcnow)

    # Relationships
    executions = relationship("AgentExecution", back_populates="agent")
```

---

## Pydantic Schemas (Request/Response)

```python
from pydantic import BaseModel, Field, validator
from typing import Optional, List
from datetime import datetime

class ProjectCreate(BaseModel):
    workspace_id: UUID
    name: str = Field(..., min_length=3, max_length=100)
    description: Optional[str] = Field(None, max_length=1000)
    problem_statement: Optional[str] = Field(None, min_length=10, max_length=5000)

    class Config:
        from_attributes = True

class ProjectResponse(BaseModel):
    id: UUID
    name: str
    phase: str
    status: str
    created_at: datetime
    updated_at: datetime

    class Config:
        from_attributes = True

class ArtifactCreate(BaseModel):
    project_id: UUID
    type: str
    content: Optional[dict] = None
    evidence_tier: str = "E0"

    class Config:
        from_attributes = True

class DivergenceResponse(BaseModel):
    id: UUID
    divergence_type: str
    assumed_value: str
    actual_value: str
    classification: str
    cascade_analysis: Optional[dict] = None

    class Config:
        from_attributes = True
```

---

## Migration Strategy (Alembic)

**Alembic Commands**:

```bash
# Initial migration (creates all tables)
alembic revision --autogenerate -m "initial_schema"

# Apply migrations
alembic upgrade head

# Reset database (dev only)
alembic downgrade base
alembic upgrade head
```

**Initial Migration File** (`alembic/versions/001_initial_schema.py`):

- Creates all 15 tables
- Adds indexes
- Sets up RLS policies
- Adds audit trigger

---

## Version Control

**DataSchema Version**: 1.0.0  
**Created**: November 23, 2025  
**Status**: Ready for implementation  
**Amendment Process**: See Constitution.md (stack changes require written approval)

---

## References

- **DataModel.md**: Entity relationships, ERD diagram
- **MasterTasks.md**: Setup 1.4 tasks (S1.4.1-S1.4.8) for implementation
- **Constitution.md**: Stack constraints (frozen until amendment)
- **RiskManagement.md**: Evidence tier classification (E0-E4 enforcement in code)
