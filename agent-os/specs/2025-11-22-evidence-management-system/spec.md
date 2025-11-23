# Specification: Evidence Management System

## Goal

Implement comprehensive evidence tracking and classification system using E0-E4 tiers, automatic evidence extraction from inputs, quality scoring, and evidence dashboard to ensure systematic validation throughout the 5D methodology.

## User Stories

- As a venture creator, I want evidence automatically extracted from my inputs so that I don't manually track supporting data
- As a validator, I want evidence classified by tier (E0-E4) so that I can assess data confidence levels
- As a stakeholder, I want an evidence dashboard so that I can see validation strength across all assumptions

## Specific Requirements

**Evidence Tier Classification (E0-E4)**
- Implement 5-tier evidence classification: E0 (untested ideas), E1 (opinion-based), E2 (qualitative insights), E3 (third-party data), E4 (quantitative validation)
- Apply tier classification to all assumptions and decisions
- Validate evidence tier requirements per phase gate (VRC ≥E2, VCD ≥E2, DSP ≥E2, MDP ≥E3)
- Track evidence tier distribution across venture lifecycle

**Evidence Quality Scoring**
- Calculate evidence quality score from tier distribution and coverage
- Weight evidence by importance (critical assumptions require higher tiers)
- Identify evidence gaps and low-confidence areas
- Generate evidence quality reports per phase

**Automatic Evidence Extraction**
- Extract evidence from user interviews, research reports, and competitive analysis
- Parse evidence from AI agent outputs (research scout, opportunity scorer, etc.)
- Link evidence to specific assumptions and hypotheses
- Tag evidence with sources (URLs, documents, interview notes)

**Evidence Dashboard**
- Visualize evidence tier distribution with donut charts
- Display evidence coverage heatmap by assumption category
- Show evidence quality trends over time
- Highlight critical assumptions with insufficient evidence (E0/E1)

**Evidence Traceability**
- Link evidence to specific requirements, designs, and code
- Track evidence lineage from discovery through deployment
- Enable evidence search and filtering by tier, source, date
- Generate evidence reports for stakeholder reviews

## Visual Design

No visual assets provided. Design system should follow:
- Evidence tier badges (E0-E4) with color coding
- Evidence dashboard with quality score and tier distribution charts
- Evidence list table with tier, assumption, source columns
- Evidence gap alerts highlighting low-confidence areas

## Existing Code to Leverage

**Evidence Schema**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Reuse EvidenceItem model for all evidence tracking
- Extend with additional metadata (importance, category)
- Apply consistent tier classification logic

**LangGraph Agent Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Extract evidence from all agent outputs
- Validate evidence tier consistency
- Aggregate evidence across multiple agents

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create Evidence entity linked to Project
- Store evidence with sources and tier classification
- Apply multi-tenant RLS for evidence isolation

## Out of Scope

- Automated evidence verification and fact-checking (manual tier assignment)
- Evidence version control and change tracking (static evidence records)
- External evidence source integration (CRM, data warehouses)
- Evidence-based decision recommendation engine
- Collaborative evidence review and annotation
- Evidence visualization beyond dashboard
- Automated evidence tier upgrade based on validation
- Evidence expiration and refresh workflows
- Third-party evidence data marketplace
- Evidence export to external validation tools
