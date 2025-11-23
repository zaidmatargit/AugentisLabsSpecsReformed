# Specification: VRC Assessment Framework

## Goal

Implement 20-indicator venture readiness evaluation with evidence tier classification (E0-E4), automated scoring with quality gates, and phase gate decision logic to ensure systematic validation in the Discover phase.

## User Stories

- As a venture creator, I want a VRC assessment so that I can objectively evaluate my venture readiness before investing resources
- As a validator, I want evidence tier classification so that I can assess the strength of supporting data
- As a gatekeeper, I want automated scoring so that I can make consistent go/no-go decisions

## Specific Requirements

**20-Indicator Venture Readiness Evaluation**
- Evaluate all 20 VRC indicators across problem, market, solution, team, traction dimensions
- Score each indicator on 0-100 scale based on evidence quality and completeness
- Weight indicators appropriately (default weights, customizable for Enterprise tier)
- Calculate composite VRC score (weighted average of all indicators)

**Evidence Tier Classification (E0-E4)**
- Classify all supporting evidence using 5-tier system (E0: untested ideas → E4: quantitative validation)
- Require minimum evidence tiers per indicator (critical indicators need E2+)
- Track evidence sources and validation activities
- Flag low-confidence areas (E0/E1) requiring validation

**Automated Scoring with Quality Gates**
- Calculate VRC score automatically from indicator inputs
- Apply quality gate threshold (≥76% to pass Discover phase)
- Generate score breakdown showing strengths and gaps
- Provide actionable recommendations to improve score

**Phase Gate Decision Logic**
- Implement go/no-go decision logic based on VRC score
- Block progression to Define phase if VRC <76%
- Support conditional approval with mitigation requirements
- Generate phase gate report with decision rationale

**VRC Report Generation**
- Generate comprehensive VRC assessment report as PDF
- Include indicator scores, evidence summary, and recommendations
- Visualize VRC score with radar chart showing dimension strengths
- Export evidence tier distribution and gap analysis

## Visual Design

No visual assets provided. Design system should follow:
- VRC score gauge (0-100) with pass/fail threshold indicator
- Radar chart showing 5 dimensions (problem, market, solution, team, traction)
- Indicator list table with scores and evidence tiers
- Evidence tier distribution donut chart

## Existing Code to Leverage

**Evidence Management Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Use EvidenceItem model for all VRC evidence tracking
- Apply consistent evidence tier classification (E0-E4)
- Link indicators to supporting evidence

**Phase Gate Decision System**
- Reference phase gate decision system for gate logic
- Apply consistent quality threshold validation
- Use similar approval workflows

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create VRCAssessment entity linked to Project
- Store indicator scores, evidence, and overall VRC score
- Apply multi-tenant RLS for assessment data isolation

## Out of Scope

- Custom VRC frameworks beyond 20 indicators (standardized assessment)
- Real-time VRC monitoring as evidence changes (periodic assessment)
- Predictive VRC scoring based on historical data
- Automated evidence collection for VRC validation (manual inputs)
- VRC benchmarking across ventures or industries
- VRC gamification and leaderboards
- External expert VRC validation (self-assessment only)
- VRC certification or badging program
- Multi-language VRC assessment
- VRC mobile app for on-the-go assessment
