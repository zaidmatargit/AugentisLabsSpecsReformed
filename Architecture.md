# Augentis Labs Architecture

## System Overview

The Augentis Labs MVP is a 5-phase AI-driven product development system that guides ventures from initial concepts through full deployment. Each phase produces artifacts that feed into the next phase through quality gates.

---

## 1. High-Level System Architecture

```mermaid
graph TB
    subgraph "5D Product Development System"
        D1["🔍 DISCOVER<br/>Market Research & Validation"]
        D2["📝 DEFINE<br/>Requirements & PRD"]
        D3["🎨 DESIGN<br/>Architecture & UI/UX"]
        D4["💻 DEVELOP<br/>Code Generation & Testing"]
        D5["🚀 DEPLOY<br/>Production & Monitoring"]
    end

    subgraph "Quality Gates"
        G1["VRC Gate<br/>Validation Complete"]
        G2["VCD Gate<br/>Definition Complete"]
        G3["DSP Gate<br/>Design Specification"]
        G4["MDP Gate<br/>Code Development"]
    end

    subgraph "External Services"
        LLM["Claude API<br/>LangGraph"]
        PG["Supabase<br/>PostgreSQL"]
        VER["Vercel<br/>Deployment"]
    end

    D1 -->|Artifacts| G1
    G1 -->|Approved| D2
    D2 -->|Artifacts| G2
    G2 -->|Approved| D3
    D3 -->|Artifacts| G3
    G3 -->|Approved| D4
    D4 -->|Artifacts| G4
    G4 -->|Approved| D5

    D1 -.->|LLM Calls| LLM
    D2 -.->|LLM Calls| LLM
    D3 -.->|LLM Calls| LLM
    D4 -.->|LLM Calls| LLM
    D5 -.->|Deployment| VER

    G1 -.->|Storage| PG
    G2 -.->|Storage| PG
    G3 -.->|Storage| PG
    G4 -.->|Storage| PG
```

---

## 2. Discover Phase Architecture

```mermaid
graph LR
    subgraph "Discover Phase Agents"
        A1["Market Research<br/>Agent"]
        A2["Competitor Analysis<br/>Agent"]
        A3["Trend Analysis<br/>Agent"]
        A4["Market Sizing<br/>Agent"]
    end

    subgraph "Discover Artifacts"
        AR1["Market Report"]
        AR2["Competitor Matrix"]
        AR3["Trend Analysis"]
        AR4["Market Size"]
    end

    subgraph "Quality Gate"
        GATE["VRC Gate<br/>8 Indicators"]
    end

    A1 --> AR1
    A2 --> AR2
    A3 --> AR3
    A4 --> AR4

    AR1 --> GATE
    AR2 --> GATE
    AR3 --> GATE
    AR4 --> GATE

    GATE -->|Score ≥ PASS| D2["→ DEFINE Phase"]
    GATE -->|Score = CONDITIONAL| REMEDIATE["Remediation Guide"]

    style GATE fill:#ff9800
    style D2 fill:#4caf50
    style REMEDIATE fill:#f44336
```

---

## 3. Define Phase Architecture

```mermaid
graph LR
    subgraph "Input"
        INPUT["Market Research<br/>from Discover"]
    end

    subgraph "Define Phase Agents"
        A1["Interview<br/>Ingestion"]
        A2["Persona<br/>Builder"]
        A3["Requirements<br/>Generator"]
        A4["Business Model<br/>Validator"]
        A5["VCD<br/>Assembler"]
    end

    subgraph "Define Artifacts"
        AR1["Interview Notes"]
        AR2["3-5 Personas"]
        AR3["50-100 User Stories"]
        AR4["Business Model"]
        AR5["PRD v2"]
    end

    subgraph "Quality Gate"
        GATE["VCD Gate<br/>10 Indicators"]
    end

    INPUT --> A1
    A1 --> AR1
    AR1 --> A2
    A2 --> AR2
    AR2 --> A3
    A3 --> AR3
    AR3 --> A4
    A4 --> AR4
    AR4 --> A5
    A5 --> AR5

    AR2 --> GATE
    AR3 --> GATE
    AR4 --> GATE
    AR5 --> GATE

    GATE -->|Score ≥ PASS| D3["→ DESIGN Phase"]

    style GATE fill:#ff9800
    style D3 fill:#4caf50
```

---

## 4. Design Phase Architecture

```mermaid
graph LR
    subgraph "Input"
        INPUT["PRD + Personas<br/>from Define"]
    end

    subgraph "Design Phase Agents"
        A1["UX<br/>Agent"]
        A2["Architecture<br/>Designer"]
        A3["Design System<br/>Builder"]
        A4["Branding<br/>Agent"]
        A5["Journey Mapper<br/>Agent"]
        A6["DSP<br/>Assembler"]
    end

    subgraph "Design Artifacts"
        AR1["Wireframes"]
        AR2["C4 Architecture<br/>Context/Container/Component"]
        AR3["Design System<br/>50+ Components"]
        AR4["Brand Guidelines"]
        AR5["Journey Maps<br/>3-5 per persona"]
        AR6["API Spec<br/>OpenAPI 3.0"]
    end

    subgraph "Quality Gate"
        GATE["DSP Gate<br/>8 Components"]
    end

    INPUT --> A1
    INPUT --> A2
    A1 --> AR1
    A2 --> AR2
    A2 --> AR6
    A3 --> AR3
    A4 --> AR4
    A5 --> AR5

    AR1 --> A6
    AR2 --> A6
    AR3 --> A6
    AR4 --> A6
    AR5 --> A6
    AR6 --> A6

    A6 --> GATE

    GATE -->|Score ≥ PASS| D4["→ DEVELOP Phase"]

    style GATE fill:#ff9800
    style D4 fill:#4caf50
```

---

## 5. Develop Phase Architecture

```mermaid
graph LR
    subgraph "Input"
        INPUT["Architecture + API Spec<br/>from Design"]
    end

    subgraph "Code Generation Agents"
        A1["Backend<br/>Generator"]
        A2["Frontend<br/>Generator"]
        A3["Test<br/>Generator"]
        A4["Schema<br/>Generator"]
        A5["Code Generator<br/>Service"]
    end

    subgraph "Quality Agents"
        Q1["Security<br/>Scanner"]
        Q2["Performance<br/>Profiler"]
    end

    subgraph "Generated Artifacts"
        AR1["NestJS Backend<br/>All Endpoints"]
        AR2["Next.js Frontend<br/>Components"]
        AR3["Test Suite<br/>≥80% Coverage"]
        AR4["Prisma Schema"]
    end

    subgraph "Quality Outputs"
        QO1["Vulnerability Report"]
        QO2["Performance Profile"]
    end

    subgraph "Quality Gate"
        GATE["MDP Gate<br/>6 Components"]
    end

    INPUT --> A1
    INPUT --> A2
    INPUT --> A4
    A1 --> AR1
    A2 --> AR2
    A3 --> AR3
    A4 --> AR4

    AR1 --> Q1
    AR1 --> Q2
    AR3 --> Q1
    Q1 --> QO1
    Q2 --> QO2

    AR1 --> GATE
    AR2 --> GATE
    AR3 --> GATE
    QO1 --> GATE
    QO2 --> GATE

    GATE -->|Score ≥ PASS| D5["→ DEPLOY Phase"]

    style GATE fill:#ff9800
    style D5 fill:#4caf50
```

---

## 6. Deploy Phase Architecture

```mermaid
graph LR
    subgraph "Input"
        INPUT["Generated Code<br/>from Develop"]
    end

    subgraph "Deployment Agents"
        A1["Deployment<br/>Agent"]
        A2["Telemetry<br/>Aggregator"]
        A3["Divergence<br/>Detector"]
    end

    subgraph "Deployment Targets"
        TARGET1["Vercel<br/>Frontend"]
        TARGET2["Supabase<br/>Backend + DB"]
    end

    subgraph "Monitoring & Analytics"
        MON1["Feature Usage<br/>Tracking"]
        MON2["User Cohorts<br/>Day 1/7/30"]
        MON3["Crash Reports"]
        MON4["Assumption vs<br/>Reality"]
    end

    INPUT --> A1
    A1 --> TARGET1
    A1 --> TARGET2

    TARGET1 --> A2
    TARGET2 --> A2
    A2 --> MON1
    A2 --> MON2
    A2 --> MON3
    A3 --> MON4

    style A1 fill:#4caf50
    style TARGET1 fill:#2196f3
    style TARGET2 fill:#2196f3
```

---

## 7. Full Agent Ecosystem (26 Agents)

```mermaid
graph TB
    subgraph "DISCOVER Phase - 4 Agents"
        D1["Market Research"]
        D2["Competitor Analysis"]
        D3["Trend Analysis"]
        D4["Market Sizing"]
    end

    subgraph "DEFINE Phase - 5 Agents"
        DE1["Interview Ingestion"]
        DE2["Persona Builder"]
        DE3["Requirements Generator"]
        DE4["Business Model Validator"]
        DE5["VCD Assembler"]
    end

    subgraph "DESIGN Phase - 6 Agents"
        DES1["UX Agent"]
        DES2["Architecture Designer"]
        DES3["Design System Builder"]
        DES4["Branding Agent"]
        DES5["Journey Mapper"]
        DES6["DSP Assembler"]
    end

    subgraph "DEVELOP Phase - 7 Agents"
        DEV1["Backend Generator"]
        DEV2["Frontend Generator"]
        DEV3["Test Generator"]
        DEV4["Schema Generator"]
        DEV5["Code Generator Service"]
        DEV6["Security Scanner"]
        DEV7["Performance Profiler"]
    end

    subgraph "DEPLOY Phase - 3 Agents"
        DP1["Deployment Agent"]
        DP2["Telemetry Aggregator"]
        DP3["Divergence Detector"]
    end

    subgraph "Cross-Phase - 1 Agent"
        CP1["MDP Assembler"]
    end

    style D1 fill:#e8f5e9
    style D2 fill:#e8f5e9
    style D3 fill:#e8f5e9
    style D4 fill:#e8f5e9

    style DE1 fill:#e3f2fd
    style DE2 fill:#e3f2fd
    style DE3 fill:#e3f2fd
    style DE4 fill:#e3f2fd
    style DE5 fill:#e3f2fd

    style DES1 fill:#f3e5f5
    style DES2 fill:#f3e5f5
    style DES3 fill:#f3e5f5
    style DES4 fill:#f3e5f5
    style DES5 fill:#f3e5f5
    style DES6 fill:#f3e5f5

    style DEV1 fill:#fff3e0
    style DEV2 fill:#fff3e0
    style DEV3 fill:#fff3e0
    style DEV4 fill:#fff3e0
    style DEV5 fill:#fff3e0
    style DEV6 fill:#fff3e0
    style DEV7 fill:#fff3e0

    style DP1 fill:#fce4ec
    style DP2 fill:#fce4ec
    style DP3 fill:#fce4ec

    style CP1 fill:#f1f8e9
```

---

## 8. Quality Gates (4 Gates)

```mermaid
graph LR
    subgraph "VRC Gate - 8 Indicators"
        VRC1["Market Size"]
        VRC2["Trend Momentum"]
        VRC3["Competitor Count"]
        VRC4["Growth Rate"]
        VRC5["TAM"]
        VRC6["SAM"]
        VRC7["SOM"]
        VRC8["Entry Barriers"]
    end

    subgraph "VCD Gate - 10 Indicators"
        VCD1["Market Research Quality"]
        VCD2["Persona Validation"]
        VCD3["Feature Prioritization"]
        VCD4["Business Model"]
        VCD5["Competitive Positioning"]
        VCD6["User Story Completeness"]
        VCD7["PRD Accuracy"]
        VCD8["Requirement Coverage"]
        VCD9["Alignment Score"]
        VCD10["Risk Assessment"]
    end

    subgraph "DSP Gate - 8 Components"
        DSP1["Wireframe Coverage"]
        DSP2["API Spec Completeness"]
        DSP3["Accessibility"]
        DSP4["Security Design"]
        DSP5["Performance Considerations"]
        DSP6["Scalability"]
        DSP7["Compliance"]
        DSP8["Brand Alignment"]
    end

    subgraph "MDP Gate - 6 Components"
        MDP1["Test Coverage ≥80%"]
        MDP2["Security - No Critical"]
        MDP3["Performance <300ms P95"]
        MDP4["Compliance Ready"]
        MDP5["Documentation Complete"]
        MDP6["Deployment Successful"]
    end

    style VRC1 fill:#c8e6c9
    style VCD1 fill:#bbdefb
    style DSP1 fill:#ffe0b2
    style MDP1 fill:#f8bbd0
```

---

## 9. Data Flow & Integration

```mermaid
graph TB
    subgraph "Frontend"
        FE1["Dashboard"]
        FE2["Phase Pages"]
        FE3["Artifact Viewers"]
        FE4["Gate Approvals"]
        FE5["Cost Dashboard"]
    end

    subgraph "Backend API"
        API["NestJS API<br/>31 Endpoints"]
    end

    subgraph "Agent Orchestration"
        ORCH["LangGraph<br/>Workflow Engine"]
    end

    subgraph "AI Models"
        LLM["Claude API<br/>Multi-turn Conversations"]
    end

    subgraph "Data Layer"
        DB["Supabase<br/>PostgreSQL"]
        CACHE["Redis Cache"]
    end

    subgraph "External Services"
        DEPLOY["Vercel API"]
        FILES["File Storage<br/>S3/GCS"]
    end

    FE1 --> API
    FE2 --> API
    FE3 --> API
    FE4 --> API
    FE5 --> API

    API --> ORCH
    ORCH --> LLM
    LLM --> ORCH

    API --> DB
    DB --> API

    API --> CACHE
    CACHE --> API

    ORCH --> DB
    DB --> ORCH

    API --> DEPLOY
    API --> FILES

    style FE1 fill:#e3f2fd
    style API fill:#fff3e0
    style ORCH fill:#f3e5f5
    style LLM fill:#fce4ec
    style DB fill:#c8e6c9
    style CACHE fill:#ffe0b2
```

---

## 10. Technology Stack

```mermaid
graph TB
    subgraph "Frontend Layer"
        FW["Next.js 14"]
        UI["React 18"]
        STATE["Zustand"]
        STYLE["TailwindCSS"]
    end

    subgraph "Backend Layer"
        FRM["NestJS"]
        ORM["Prisma ORM"]
        VAL["Zod Validation"]
        AUTH["JWT Auth"]
    end

    subgraph "AI/Agent Layer"
        LANG["LangGraph"]
        LLM["Claude 3 API"]
        TOOL["Tool Use"]
        MEM["Memory Management"]
    end

    subgraph "Data Layer"
        DB["PostgreSQL<br/>Supabase"]
        CACHE["Redis"]
        FILE["Cloud Storage"]
    end

    subgraph "DevOps & Monitoring"
        CI["GitHub Actions"]
        DEPLOY["Vercel/Docker"]
        LOG["Datadog/CloudWatch"]
        MONITOR["APM"]
    end

    FW --> UI
    UI --> STATE
    STATE --> STYLE

    FRM --> ORM
    ORM --> VAL
    VAL --> AUTH

    LANG --> LLM
    LLM --> TOOL
    TOOL --> MEM

    ORM --> DB
    DB --> CACHE
    CACHE --> FILE

    CI --> DEPLOY
    DEPLOY --> LOG
    LOG --> MONITOR

    style FW fill:#e3f2fd
    style FRM fill:#fff3e0
    style LANG fill:#f3e5f5
    style DB fill:#c8e6c9
    style CI fill:#f8bbd0
```

---

## 11. Artifact Production Map

```mermaid
graph TB
    subgraph "Discover Artifacts"
        DAR1["Market Report"]
        DAR2["Competitor Matrix"]
        DAR3["Trend Analysis"]
        DAR4["Market Size (TAM/SAM/SOM)"]
    end

    subgraph "Define Artifacts"
        DEAR1["Interview Notes"]
        DEAR2["3-5 Personas"]
        DEAR3["50-100 User Stories"]
        DEAR4["Business Model Canvas"]
        DEAR5["PRD v2"]
    end

    subgraph "Design Artifacts"
        DESAR1["Wireframes (Figma)"]
        DESAR2["C4 Architecture Diagrams"]
        DESAR3["OpenAPI 3.0 Spec"]
        DESAR4["Design System (50+ Components)"]
        DESAR5["Brand Guidelines"]
        DESAR6["Journey Maps"]
    end

    subgraph "Develop Artifacts"
        DEVAR1["NestJS Backend Scaffold"]
        DEVAR2["Next.js Components"]
        DEVAR3["Prisma Schema"]
        DEVAR4["Test Suite"]
        DEVAR5["Security Report"]
        DEVAR6["Performance Report"]
    end

    subgraph "Deploy Artifacts"
        DPAR1["Live Frontend<br/>Vercel"]
        DPAR2["Live Backend<br/>Supabase"]
        DPAR3["Telemetry Dashboard"]
        DPAR4["Analytics Reports"]
    end
```

---

## 12. Request/Response Flow Example (E2E)

```mermaid
sequenceDiagram
    participant User as User<br/>Dashboard
    participant API as NestJS API
    participant Agent as LangGraph<br/>Agent
    participant Claude as Claude API
    participant DB as PostgreSQL

    User->>API: POST /discover/start<br/>Market Research
    API->>Agent: Trigger Discover Workflow
    Agent->>Claude: "Analyze market for [venture]"
    Claude-->>Agent: Market Analysis (1000+ tokens)
    Agent->>Claude: "Extract TAM/SAM/SOM"
    Claude-->>Agent: Market Sizing
    Agent->>DB: Store Artifacts
    DB-->>Agent: Confirm
    Agent-->>API: Workflow Complete
    API-->>User: VRC Score: 8.2/10
    User->>API: POST /gates/vrc/approve
    API->>DB: Update Gate Status
    DB-->>API: Ready for DEFINE
    API-->>User: Next Phase Unlocked
```

---

## 13. Scalability & Performance

```mermaid
graph LR
    subgraph "Load Balancing"
        LB["Load Balancer"]
    end

    subgraph "API Instances"
        API1["NestJS Pod 1"]
        API2["NestJS Pod 2"]
        API3["NestJS Pod 3"]
    end

    subgraph "Caching Layer"
        CACHE1["Redis Cluster<br/>Session + Cache"]
    end

    subgraph "Database"
        DB["PostgreSQL<br/>Read Replicas"]
        BACKUP["Backup Storage"]
    end

    subgraph "Message Queue"
        QUEUE["BullMQ<br/>Job Processing"]
    end

    LB --> API1
    LB --> API2
    LB --> API3

    API1 --> CACHE1
    API2 --> CACHE1
    API3 --> CACHE1

    API1 --> DB
    API2 --> DB
    API3 --> DB

    API1 --> QUEUE
    API2 --> QUEUE
    API3 --> QUEUE

    DB --> BACKUP

    style LB fill:#ffebee
    style API1 fill:#fff3e0
    style API2 fill:#fff3e0
    style API3 fill:#fff3e0
    style CACHE1 fill:#ffe0b2
    style DB fill:#c8e6c9
    style QUEUE fill:#f8bbd0
```

---

## 14. Security Architecture

```mermaid
graph TB
    subgraph "Authentication & Authorization"
        AUTH["JWT Tokens"]
        RBAC["Role-Based Access Control"]
        MFA["Multi-Factor Auth"]
    end

    subgraph "Data Protection"
        ENCRYPT["Field-Level Encryption"]
        RLS["Row Level Security"]
        AUDIT["Audit Logging"]
    end

    subgraph "API Security"
        RATE["Rate Limiting"]
        CORS["CORS Policies"]
        VALIDATE["Input Validation"]
    end

    subgraph "Infrastructure"
        VPC["VPC Isolation"]
        WAF["Web Application Firewall"]
        DLP["Data Loss Prevention"]
    end

    subgraph "Compliance"
        SOC2["SOC 2 Ready"]
        GDPR["GDPR Compliant"]
        HIPAA["HIPAA Compatible"]
    end

    AUTH --> RBAC
    RBAC --> MFA

    ENCRYPT --> RLS
    RLS --> AUDIT

    RATE --> CORS
    CORS --> VALIDATE

    VPC --> WAF
    WAF --> DLP

    style AUTH fill:#ffcdd2
    style RLS fill:#ffcdd2
    style VALIDATE fill:#ffcdd2
    style SOC2 fill:#c8e6c9
```

---

## 15. Organizational Hierarchy (Workspace → Projects)

```mermaid
graph TB
    subgraph "Workspace Level (Organization)"
        WS["🏢 WORKSPACE<br/>Company/Organization"]
        WSM["👥 WORKSPACE_MEMBERS<br/>All team members"]
        WSR["🔐 WORKSPACE_ROLES<br/>Org-level roles<br/>Admin, Lead, Member"]
        WSS["⚙️ WORKSPACE_SETTINGS<br/>Branding, billing, preferences"]
        WSP["💳 WORKSPACE_SUBSCRIPTIONS<br/>Plan tier, project limits"]
    end

    subgraph "Project Level (Products)"
        P["📊 PROJECT<br/>Individual venture/product"]
        PM["👤 PROJECT_MEMBERS<br/>Project-specific team"]
        PR["🔐 PROJECT_ROLES<br/>Project-level roles<br/>Lead, Developer, Viewer"]
    end

    subgraph "Project Execution"
        PH["🔄 PROJECT_PHASES<br/>5D phases"]
        ART["📄 PHASE_ARTIFACTS<br/>Outputs, designs, code"]
        GG["✅ QUALITY_GATES<br/>VRC, VCD, DSP, MDP"]
    end

    WS --> WSM
    WS --> WSR
    WS --> WSS
    WS --> WSP
    WS -->|creates| P

    P --> PM
    P --> PR
    P --> PH
    P --> GG
    PH --> ART

    WSM -->|member of| P
    PM -->|assigned to| P

    style WS fill:#c8e6c9
    style WSM fill:#a5d6a7
    style WSR fill:#81c784
    style WSS fill:#66bb6a
    style WSP fill:#4caf50
    style P fill:#e3f2fd
    style PM fill:#bbdefb
    style PR fill:#90caf9
    style PH fill:#fff3e0
    style ART fill:#ffe0b2
    style GG fill:#ffcc80
```

---

## 15A. Multi-Tenant Access Control

```mermaid
graph TB
    USER["User Login"]

    USER -->|authenticates| AUTH["Auth Service<br/>Supabase Auth"]
    AUTH -->|returns user_id| SESSION["Session Created"]

    SESSION -->|selects workspace| WS["Load Workspace<br/>workspace_id"]
    WS -->|verify membership| CHECK["Check WORKSPACE_MEMBERS<br/>user_id + workspace_id"]
    CHECK -->|yes| LOAD["Load workspace resources<br/>projects, settings, team"]
    CHECK -->|no| DENIED["❌ Access Denied"]

    LOAD -->|selects project| PROJ["Load Project<br/>project_id"]
    PROJ -->|verify membership| CHECK2["Check PROJECT_MEMBERS<br/>user_id + project_id"]
    CHECK2 -->|yes| ALLOWED["✅ Access Granted<br/>Apply role-based permissions"]
    CHECK2 -->|no| DENIED2["❌ Access Denied"]

    ALLOWED -->|query| RLS["PostgreSQL RLS<br/>Enforce row-level security<br/>WHERE workspace_id = X"]
    RLS --> DATA["✅ Return filtered data"]

    style AUTH fill:#c8e6c9
    style WS fill:#e3f2fd
    style CHECK fill:#fff3e0
    style ALLOWED fill:#c8e6c9
    style DENIED fill:#ffcdd2
    style RLS fill:#ffe0b2
```

---

## 16. Database Schema (Entity Relationships)

```mermaid
erDiagram
    WORKSPACES ||--o{ WORKSPACE_SETTINGS : has
    WORKSPACES ||--o{ WORKSPACE_MEMBERS : contains
    WORKSPACES ||--o{ PROJECTS : owns
    WORKSPACES ||--o{ WORKSPACE_SUBSCRIPTIONS : has

    WORKSPACE_MEMBERS ||--o{ WORKSPACE_ROLES : assigned

    PROJECTS ||--o{ PROJECT_PHASES : has
    PROJECTS ||--o{ QUALITY_GATES : evaluates
    PROJECTS ||--o{ PROJECT_MEMBERS : contains
    PROJECTS ||--o{ TELEMETRY : tracks

    PROJECT_PHASES ||--o{ PHASE_ARTIFACTS : generates
    PHASE_ARTIFACTS ||--o{ ARTIFACT_VERSIONS : versions
    QUALITY_GATES ||--o{ GATE_EVALUATIONS : produces
    QUALITY_GATES ||--o{ GATE_INDICATORS : contains
    PROJECT_MEMBERS ||--o{ USER_APPROVALS : approves

    WORKSPACES {
        int id PK
        string workspace_name
        string slug
        string description
        string status
        datetime created_at
        datetime updated_at
    }

    WORKSPACE_SETTINGS {
        int id PK
        int workspace_id FK
        json branding
        json preferences
        json billing_config
        datetime updated_at
    }

    WORKSPACE_MEMBERS {
        int id PK
        int workspace_id FK
        int user_id FK
        string role
        datetime invited_at
        datetime joined_at
    }

    WORKSPACE_ROLES {
        int id PK
        int member_id FK
        string role_name
        json permissions
        datetime granted_at
    }

    WORKSPACE_SUBSCRIPTIONS {
        int id PK
        int workspace_id FK
        string plan_tier
        int project_limit
        datetime start_date
        datetime renewal_date
    }

    PROJECTS {
        int id PK
        int workspace_id FK
        string project_name
        string description
        string status
        datetime created_at
        datetime updated_at
        string target_market
        float estimated_tam
    }

    PROJECT_PHASES {
        int id PK
        int project_id FK
        string phase_name
        string status
        datetime started_at
        datetime completed_at
    }

    PHASE_ARTIFACTS {
        int id PK
        int phase_id FK
        string artifact_type
        json artifact_data
        string s3_path
        datetime created_at
    }

    ARTIFACT_VERSIONS {
        int id PK
        int artifact_id FK
        int version_number
        json changes
        datetime created_at
    }

    QUALITY_GATES {
        int id PK
        int project_id FK
        string gate_name
        string status
        float score
        datetime evaluated_at
    }

    GATE_INDICATORS {
        int id PK
        int gate_id FK
        string indicator_name
        float indicator_value
        string evidence
    }

    GATE_EVALUATIONS {
        int id PK
        int gate_id FK
        string evaluation_status
        json evaluation_details
        datetime evaluated_at
    }

    PROJECT_MEMBERS {
        int id PK
        int project_id FK
        int user_id FK
        string role
        datetime invited_at
    }

    USER_APPROVALS {
        int id PK
        int user_id FK
        int gate_id FK
        string decision
        string comments
        datetime decided_at
    }

    TELEMETRY {
        int id PK
        int project_id FK
        string metric_name
        datetime recorded_at
    }
```

---

## 16. Database Tables Detail

```mermaid
graph TB
    subgraph "Workspace Tables"
        T1["workspaces<br/>workspace_name, slug, status"]
        T2["workspace_members<br/>user_id, role, joined_at"]
        T3["workspace_settings<br/>branding, preferences, billing_config"]
        T4["workspace_roles<br/>role_name, permissions"]
        T5["workspace_subscriptions<br/>plan_tier, project_limit, renewal"]
    end

    subgraph "Project Tables"
        T6["projects<br/>project_name, status, target_market"]
        T7["project_phases<br/>phase_name, status, started_at"]
        T8["project_members<br/>user_id, role, invited_at"]
    end

    subgraph "Artifacts & Versions"
        T9["phase_artifacts<br/>artifact_type, artifact_data, s3_path"]
        T10["artifact_versions<br/>version_number, changes"]
    end

    subgraph "Quality Gates"
        T11["quality_gates<br/>gate_name, status, score"]
        T12["gate_indicators<br/>indicator_name, value, evidence"]
        T13["gate_evaluations<br/>evaluation_status, details"]
    end

    subgraph "User & Governance"
        T14["user_approvals<br/>decision, comments, decided_at"]
    end

    subgraph "Analytics"
        T15["telemetry<br/>metric_name, recorded_at"]
    end

    T1 --> T2
    T1 --> T3
    T1 --> T4
    T1 --> T5
    T1 --> T6

    T2 --> T4

    T6 --> T7
    T6 --> T8
    T6 --> T11
    T6 --> T15

    T7 --> T9
    T9 --> T10
    T11 --> T12
    T11 --> T13
    T8 --> T14

    style T1 fill:#c8e6c9
    style T2 fill:#c8e6c9
    style T3 fill:#c8e6c9
    style T6 fill:#e3f2fd
    style T7 fill:#e3f2fd
    style T8 fill:#e3f2fd
    style T9 fill:#fff3e0
    style T11 fill:#ffe0b2
    style T15 fill:#f8bbd0

    subgraph "Telemetry & Analytics"
        T13["telemetry<br/>project_id, metric_name, recorded_at"]
        T14["telemetry_events<br/>event_type, event_data, occurred_at"]
        T15["divergence_analysis<br/>assumption_vs_reality, impact"]
    end

    T1 --> T2
    T1 --> T3
    T1 --> T5
    T2 --> T4
    T4 --> T5
    T5 --> T6
    T1 --> T7
    T7 --> T8
    T1 --> T10
    T10 --> T11
    T10 --> T12
    T1 --> T13
    T13 --> T14
    T14 --> T15

    style T1 fill:#c8e6c9
    style T4 fill:#fff3e0
    style T7 fill:#e3f2fd
    style T10 fill:#ffe0b2
    style T13 fill:#f8bbd0

```

---

## 17. Deployment Topology (Production Environment)

```mermaid
graph TB
    subgraph "CDN & DNS"
        CDN["CloudFront CDN<br/>Global Distribution"]
        DNS["Route 53<br/>DNS Management"]
    end

    subgraph "Frontend Deployment - Vercel"
        FE1["Next.js Production<br/>Vercel Edge Functions"]
        FE2["Next.js Preview<br/>Auto-Deployments"]
        FE_CACHE["Vercel Edge Cache<br/>ISR, Incremental"]
    end

    subgraph "Backend Deployment - Supabase/Docker"
        DOCKER["Docker Container<br/>NestJS Application"]
        K8S["Kubernetes Cluster<br/>Auto-scaling 2-10 pods"]
        LB["Internal Load Balancer<br/>Round-robin distribution"]
    end

    subgraph "Message Queue & Jobs"
        BULL["BullMQ<br/>Redis-backed Job Queue"]
        WORKER["Worker Processes<br/>Long-running Tasks"]
    end

    subgraph "Caching Layer"
        REDIS["Redis Cluster<br/>Session + Cache"]
    end

    subgraph "Database Layer"
        DB_PRIMARY["PostgreSQL Primary<br/>Main Database"]
        DB_READ["PostgreSQL Read Replica<br/>Analytics Queries"]
        BACKUP["Automated Backups<br/>Daily + Point-in-time"]
    end

    subgraph "Object Storage"
        S3["S3/GCS Bucket<br/>Artifacts, Wireframes, Docs"]
        CF["CloudFront Distribution<br/>Fast Downloads"]
    end

    subgraph "Monitoring & Logging"
        LOG["Datadog/CloudWatch<br/>Centralized Logging"]
        MONITOR["APM & Metrics<br/>Performance Tracking"]
        ALERT["Alert Manager<br/>Incident Response"]
    end

    subgraph "External Services"
        CLAUDE["Claude API<br/>via LangGraph"]
        GITHUB["GitHub<br/>Version Control"]
    end

    DNS --> CDN
    CDN --> FE1
    CDN --> S3
    S3 --> CF

    FE1 --> FE_CACHE
    FE_CACHE --> LB

    FE2 --> GITHUB
    GITHUB --> FE1

    LB --> K8S
    K8S --> DOCKER
    DOCKER --> BULL
    BULL --> WORKER
    WORKER --> CLAUDE

    DOCKER --> REDIS
    REDIS --> DOCKER

    DOCKER --> DB_PRIMARY
    DB_PRIMARY --> DB_READ
    DB_PRIMARY --> BACKUP

    DOCKER --> S3
    S3 --> BACKUP

    K8S --> LOG
    DOCKER --> MONITOR
    MONITOR --> ALERT

    style FE1 fill:#e3f2fd
    style K8S fill:#fff3e0
    style REDIS fill:#ffe0b2
    style DB_PRIMARY fill:#c8e6c9
    style BULL fill:#f8bbd0
    style LOG fill:#ffcdd2
```

---

## 18. Deployment Pipeline (CI/CD)

```mermaid
graph LR
    subgraph "Version Control"
        PUSH["Git Push<br/>Feature/Main"]
    end

    subgraph "GitHub Actions"
        TRIGGER["Trigger Workflow<br/>on Push"]
        TEST["Run Tests<br/>Jest/Vitest"]
        LINT["Lint & Format<br/>ESLint/Prettier"]
        BUILD["Build Docker Image<br/>Push to Registry"]
        SCAN["Security Scan<br/>Trivy/Snyk"]
    end

    subgraph "Staging Environment"
        STAGE_BUILD["Build Artifacts"]
        STAGE_DEPLOY["Deploy to Staging<br/>Supabase + Vercel Preview"]
        STAGE_TEST["E2E Tests<br/>Playwright"]
        SMOKE["Smoke Tests<br/>Health Checks"]
    end

    subgraph "Production Deployment"
        APPROVAL["Manual Approval<br/>Required"]
        PROD_DEPLOY["Deploy to Production<br/>Canary Release"]
        HEALTH["Health Checks<br/>Endpoint Verification"]
        ROLLBACK["Auto Rollback<br/>on Failure"]
    end

    subgraph "Monitoring Post-Deploy"
        METRICS["Monitor Metrics<br/>CPU, Memory, Latency"]
        ERRORS["Error Tracking<br/>Sentry/Datadog"]
        ALERT["Alert on Issues<br/>Slack/PagerDuty"]
    end

    PUSH --> TRIGGER
    TRIGGER --> TEST
    TRIGGER --> LINT
    TEST --> BUILD
    LINT --> BUILD
    BUILD --> SCAN
    SCAN --> STAGE_BUILD

    STAGE_BUILD --> STAGE_DEPLOY
    STAGE_DEPLOY --> STAGE_TEST
    STAGE_TEST --> SMOKE
    SMOKE --> APPROVAL

    APPROVAL --> PROD_DEPLOY
    PROD_DEPLOY --> HEALTH
    HEALTH --> ROLLBACK

    ROLLBACK --> METRICS
    METRICS --> ERRORS
    ERRORS --> ALERT

    style TEST fill:#c8e6c9
    style LINT fill:#c8e6c9
    style SCAN fill:#ffcdd2
    style APPROVAL fill:#fff3e0
    style PROD_DEPLOY fill:#ffe0b2
    style METRICS fill:#e3f2fd
```

---

## 19. Infrastructure Components & Scaling

```mermaid
graph TB
    subgraph "Auto-Scaling Configuration"
        SCALE1["Frontend: Vercel Auto-scaling<br/>Automatic"]
        SCALE2["Backend: K8s Auto-scaling<br/>Min 2 pods, Max 10 pods<br/>Trigger: CPU >70%, Memory >80%"]
        SCALE3["Database: RDS Aurora Auto-scaling<br/>Compute scaling based on load"]
        SCALE4["Cache: Redis Cluster<br/>Sharded across 3+ nodes"]
    end

    subgraph "Disaster Recovery"
        BACKUP1["Database Backups<br/>Daily full + Hourly incremental"]
        BACKUP2["Application State<br/>Versioned in S3"]
        BACKUP3["Multi-region Standby<br/>Ready in <5 minutes"]
        RTO["RTO: 15 minutes<br/>RPO: 1 hour"]
    end

    subgraph "Performance Optimization"
        OPT1["CDN: Global edge caching<br/>Static assets <100ms"]
        OPT2["Compression: Gzip + Brotli<br/>Images <500KB"]
        OPT3["Database Query Optimization<br/>Indexed, Materialized Views"]
        OPT4["API Response Caching<br/>Redis TTL: 5-60min"]
    end

    subgraph "Capacity Planning"
        CAP1["100 concurrent ventures<br/>≈2-3 K8s pods"]
        CAP2["1000 concurrent API calls/sec<br/>≈5-8 pods"]
        CAP3["Storage: 100 ventures × 500MB<br/>≈50GB + 20% growth buffer"]
    end

    SCALE2 --> CAP1
    BACKUP1 --> RTO
    OPT1 --> OPT2
```

---

## 20. Deployment Architecture - Complete Flow

```mermaid
graph TB
    subgraph "Local Development"
        DEV["Developer Laptop<br/>VS Code + Docker Compose"]
    end

    subgraph "Git Repository"
        REPO["GitHub Repository<br/>Main + Feature Branches"]
    end

    subgraph "Build Pipeline"
        BUILD["Docker Build<br/>Multi-stage"]
        REGISTRY["Container Registry<br/>Docker Hub / ECR"]
    end

    subgraph "Kubernetes Cluster<br/>(AWS EKS or Google GKE)"
        NS["Namespace: augentis-prod"]
        INGRESS["Ingress Controller<br/>Route traffic"]
        SVC1["Service: API<br/>Internal Load Balancer"]
        POD1["Pod Replica Set<br/>NestJS x 2-10"]
        SVC2["Service: Workers<br/>Internal"]
        POD2["Pod Replica Set<br/>BullMQ Workers"]
    end

    subgraph "Data Persistence"
        PVC["Persistent Volume<br/>Redis, Temp Files"]
        DB["PostgreSQL<br/>Supabase Managed"]
        S3["S3 Bucket<br/>Artifacts"]
    end

    subgraph "Frontend Deployment"
        VERCEL["Vercel Edge<br/>Next.js Deployment"]
        CDN["Global CDN<br/>99.99% availability"]
    end

    subgraph "External Integrations"
        CLAUDE_API["Claude API<br/>LLM Processing"]
        GITHUB_API["GitHub API<br/>Deployment Triggers"]
    end

    DEV --> REPO
    REPO -->|Push| BUILD
    BUILD --> REGISTRY

    REGISTRY -->|Deploy| NS
    NS --> INGRESS
    INGRESS --> SVC1
    SVC1 --> POD1
    INGRESS --> SVC2
    SVC2 --> POD2

    POD1 --> PVC
    POD2 --> PVC
    POD1 --> DB
    POD1 --> S3

    REPO -->|Auto-deploy| VERCEL
    VERCEL --> CDN

    POD1 --> CLAUDE_API
    REPO --> GITHUB_API

    style DEV fill:#e8f5e9
    style BUILD fill:#fff3e0
    style NS fill:#e3f2fd
    style POD1 fill:#fff3e0
    style VERCEL fill:#e3f2fd
    style DB fill:#c8e6c9
    style CLAUDE_API fill:#f3e5f5
```

---

## Summary Statistics

| Metric                  | Value                                         |
| ----------------------- | --------------------------------------------- |
| **Total Phases**        | 5 (Discover, Define, Design, Develop, Deploy) |
| **Total Agents**        | 26                                            |
| **Quality Gates**       | 4 (VRC, VCD, DSP, MDP)                        |
| **API Endpoints**       | 31                                            |
| **Frontend Components** | 50+                                           |
| **Test Tasks**          | 36+                                           |
| **Total MVP Tasks**     | 192                                           |
| **Sprints**             | 8 (S1-S8)                                     |
| **Target Time**         | ~3 hours (E2E)                                |
| **Max Concurrency**     | 100 ventures                                  |
| **Database Tables**     | 15+ core tables                               |
| **K8s Pods Range**      | 2-10 (auto-scaling)                           |
| **RTO**                 | 15 minutes                                    |
| **RPO**                 | 1 hour                                        |
| **Frontend Latency**    | <100ms (CDN)                                  |
| **API Response Time**   | <300ms P95                                    |
