# Define Phase – Agent Specification Document

## Overview

The Define phase translates validated problems and market insights from Discover into a concrete solution blueprint: personas, journeys, requirements, feasibility, and a Venture Concept Document (VCD).

## Agents in Define

- Market Research Specialist
- Persona Builder
- Requirements Specialist
- Feasibility Analyzer
- VCD Assembler

All agents work progressively (v0.1, v0.2, …) and reuse Discover artifacts instead of duplicating work.

---

## Agent 1: Market Research Specialist

**Purpose:**  
Deepen market and competitive intelligence to support concrete product definition and refine earlier Discover outputs (competition, segments, pricing, TAM/SAM/SOM).

**Skills & Capabilities**

- Multi-source competitive research (web, reports, communities)
- Detailed feature and pricing extraction for 10–15 competitors
- Segment-level market sizing and refinement of TAM/SAM/SOM
- White-space and opportunity identification at segment level

**Inputs**

- ProblemStatementArtifact (latest version)
- CompetitiveLandscapeArtifact (from Discover)
- MarketOpportunityArtifact (from Discover)
- Any additional links/evidence the user provides

**Tools & Integrations**

- Web research on competitors and market data
- Feature/pricing matrix builder
- Segment sizing helper (top-down/bottom-up logic, similar to Discover but more granular)

**Outputs (Progressive Artifact)**

- Primary artifact: DefineMarketResearchArtifact v0.x
  - Refined market segments with sizes and evidence tiers
  - Detailed pricing bands and packaging patterns
  - Updated view of competitive white-space and go-to-market implications

**Behavior**

- v0.1: Produces an initial deep-dive based on Discover artifacts and new data
- v0.2+: Updates segments, pricing, and whitespace as more evidence is added (e.g., new competitors, reports)

---

## Agent 2: Persona Builder

**Purpose:**  
Convert validated problem and segment insights into concrete personas and journeys that drive solution design and requirements.

**Skills & Capabilities**

- Persona creation using jobs-to-be-done and behavior patterns
- Mapping pains, goals, motivations, and channels
- Current/future state journey mapping for key flows (discovery, evaluation, purchase, usage)

**Inputs**

- ProblemStatementArtifact
- DefineMarketResearchArtifact
- User-provided interview notes, survey summaries, or observational data (if available)

**Tools & Integrations**

- Text clustering on qualitative data to find themes
- Persona template generator
- Journey mapping helper (step-by-step current and future journeys)

**Outputs (Progressive Artifacts)**

- PersonaArtifact v0.x
  - 2–5 personas with goals, pains, motivations, behaviors, channels
- JourneyMapArtifact v0.x
  - 1–3 journeys per key persona (current and future state)

**Behavior**

- v0.1: Creates draft personas and journeys using whatever data exists, clearly marking assumptions
- v0.2+: Refines personas/journeys as more evidence arrives; preserves IDs so downstream links (stories, metrics) remain stable

---

## Agent 3: Requirements Specialist

**Purpose:**  
Translate personas and journeys into a structured PRD: user stories, non-functional requirements, and success metrics.

**Skills & Capabilities**

- Generating user stories from persona goals and journey steps
- Prioritization (e.g., MoSCoW) and release slicing
- Defining non-functional requirements (performance, security, reliability)
- Drafting measurable success metrics and acceptance criteria

**Inputs**

- ProblemStatementArtifact
- PersonaArtifact and JourneyMapArtifact
- DefineMarketResearchArtifact
- Any constraints from the team (e.g., tech stack, regulatory)

**Tools & Integrations**

- Story generator (persona + journey → user stories)
- Requirements structuring and deduplication
- Prioritization helper (labels stories as Must/Should/Could/Won’t)

**Outputs (Progressive Artifact)**

- Primary artifact: RequirementsArtifact v0.x
  - Product overview and objectives
  - Success metrics
  - User story backlog with priorities and acceptance criteria
  - Non-functional requirements and dependencies

**Behavior**

- v0.1: Produces a minimal but end-to-end PRD covering core flows
- v0.2+: Refines priorities, adds/removes stories, and tightens acceptance criteria as personas/journeys evolve

---

## Agent 4: Feasibility Analyzer

**Purpose:**  
Assess whether the proposed solution is feasible technically, operationally, and financially at a high level.

**Skills & Capabilities**

- Mapping requirements to a rough technical architecture
- Estimating complexity and risk areas
- Identifying operational needs (support, onboarding, integrations)
- High-level cost and ROI estimation; feasibility scoring per dimension

**Inputs**

- RequirementsArtifact
- Team and resource info (if available)
- Any architecture constraints or preferences

**Tools & Integrations**

- Architecture pattern templates (SaaS, data-heavy, workflow-heavy, etc.)
- Complexity estimators (per module/feature)
- Risk catalog for technical/operational/financial dimensions

**Outputs (Progressive Artifact)**

- Primary artifact: FeasibilityArtifact v0.x
  - Technical feasibility summary and score
  - Operational feasibility summary and score
  - Financial feasibility summary and score
  - Risk list with likelihood, impact, and mitigations

**Behavior**

- v0.1: Provides initial feasibility assessment and highlights blockers/risks
- v0.2+: Updates scores and risk mitigations as requirements or team/resources change

---

## Agent 5: VCD Assembler

**Purpose:**  
Synthesize Define outputs into a Venture Concept Document (VCD) that can be reviewed and approved as the gate to Design.

**Skills & Capabilities**

- Narrative synthesis across problem, market, personas, product, and feasibility
- Consistency checking (does the product solve the validated problem for the defined personas within feasible constraints?)
- Structuring a reviewable document for stakeholders

**Inputs**

- ProblemStatementArtifact (latest)
- DefineMarketResearchArtifact
- PersonaArtifact and JourneyMapArtifact
- RequirementsArtifact
- FeasibilityArtifact

**Tools & Integrations**

- Document assembler (section templates for Executive Summary, Market, Customer, Product, Feasibility, Plan, Risks)
- Cross-checking rules (e.g., each key persona should have core stories covered)
- Optional export formats (PDF/HTML) in your implementation

**Outputs (Progressive Artifact)**

- Primary artifact: VCDArtifact v0.x
  - Executive summary
  - Market and segment summary
  - Customer (persona and journey) summary
  - Product concept and requirements summary
  - Feasibility summary (tech/ops/financial)
  - Implementation and risk management summaries

**Behavior**

- v0.1: Creates a first VCD draft that covers all sections with current information
- v0.2+: Updates only affected sections as upstream artifacts change (e.g., personas or feasibility), preserving structure for review history
