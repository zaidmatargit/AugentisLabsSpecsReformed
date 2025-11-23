# Specification: AI-Generated Personas

## Goal

Enable automated generation of 3-5 evidence-based user personas from research data using AI, applying the Jobs-to-be-Done framework to support product definition and user-centered design decisions in the Define phase.

## User Stories

- As a venture creator, I want AI to generate detailed user personas from my research data so that I can understand my target users without manual synthesis
- As a product strategist, I want personas based on the Jobs-to-be-Done framework so that I can focus on user outcomes rather than demographics
- As a validation team member, I want a persona approval workflow so that I can review and validate AI-generated personas before using them in product decisions

## Specific Requirements

**Persona Generation from Research Data**
- Extract persona insights from Evidence Management System data (E2+ evidence tier)
- Generate 3-5 distinct personas per venture based on market research and competitive analysis
- Include demographic data, behavioral patterns, pain points, and goals for each persona
- Map personas to market segments identified in Discover phase

**Jobs-to-be-Done Framework Integration**
- Structure each persona around functional, emotional, and social jobs-to-be-done
- Identify "jobs" users are hiring the product to accomplish
- Document success criteria for each job from the user's perspective
- Connect persona jobs to problem validation evidence from VRC assessment

**Persona Validation Workflow**
- Provide human-in-the-loop approval workflow for generated personas
- Allow editing and refinement of persona details before approval
- Track persona version history and approval status
- Require minimum E2 evidence tier support for each persona characteristic

**Persona Output and Storage**
- Generate structured persona documents in markdown format
- Store personas in PostgreSQL with multi-tenant RLS isolation
- Associate personas with ventures and phase (Define phase gate requirement)
- Export personas as PDF artifacts for stakeholder sharing

**AI Agent Integration**
- Implement as LangGraph agent with state management
- Use OpenAI/Anthropic LLMs with structured output validation
- Target 95%+ agent execution success rate
- Maintain <5K tokens per persona generation

## Visual Design

No visual assets provided. Design system should follow:
- Persona cards with avatar, name, demographic summary
- Jobs-to-be-Done framework sections (functional/emotional/social)
- Evidence tier badges showing data confidence level
- Approval workflow UI with edit/approve/reject actions

## Existing Code to Leverage

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Reuse StateGraph orchestration pattern for persona generation workflow
- Apply structured output validation using Pydantic models
- Implement similar error handling and state management

**Evidence Tier Schema**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Reuse EvidenceItem model for persona evidence tracking
- Apply same E0-E4 classification logic to persona characteristics
- Leverage evidence validation patterns for persona approval

**Multi-Tenant Data Model**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Extend Project model pattern for Persona entity
- Apply same org_id and tenant isolation approach
- Use similar created_at, created_by, version tracking

**API Service Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/api/src/app/services/project_service.py`
- Create PersonaService following same service layer pattern
- Implement CRUD operations with tenant isolation checks
- Apply similar error handling and validation logic

## Out of Scope

- Real-time persona updates based on new evidence (deferred to future iteration)
- Integration with external persona tools (Xtensio, HubSpot)
- A/B testing of persona variations
- Persona analytics dashboard showing persona usage across features
- Automated persona refinement based on user feedback post-launch
- Machine learning-based persona clustering from large datasets
- Video or interactive persona presentations
- Persona journey map integration (separate feature: User Journey Mapping)
- Multi-language persona generation
- Custom persona templates beyond Jobs-to-be-Done framework
