# Specification: Problem Validation Agent

## Goal

Automate structured customer interview analysis, pain point quantification, problem severity scoring, and market size validation to ensure strong problem-solution fit in the Discover phase.

## User Stories

- As a venture creator, I want structured interview analysis so that I can extract insights from customer conversations systematically
- As a product manager, I want pain point quantification so that I can prioritize which problems to solve first
- As an investor, I want problem severity scoring so that I can assess whether the problem is worth solving

## Specific Requirements

**Structured Customer Interview Analysis**
- Parse customer interview transcripts and extract key themes
- Identify problem statements, pain points, and desired outcomes
- Categorize problems by domain (workflow inefficiency, cost burden, compliance risk, etc.)
- Tag interview quotes with evidence tier classification (E2 for direct customer insights)

**Pain Point Quantification**
- Quantify pain point frequency (how often does this problem occur?)
- Estimate pain point cost (time lost, money spent, opportunity cost)
- Assess pain point severity on 1-10 scale
- Prioritize pain points by frequency × severity score

**Problem Severity Scoring**
- Calculate composite problem severity score (0-100)
- Weight by frequency, cost impact, and workaround difficulty
- Classify problems as critical (>75), important (50-75), or minor (<50)
- Compare problem severity across customer segments

**Market Size Validation**
- Estimate number of potential customers experiencing this problem
- Validate problem market size against TAM/SAM estimates
- Identify problem prevalence trends (growing, stable, declining)
- Cross-reference problem evidence with market research data

**Problem Validation Report**
- Generate comprehensive problem validation report
- Include top 5-10 validated problems with supporting evidence
- Provide problem-solution fit recommendations
- Link to customer interview sources and evidence tiers

## Visual Design

No visual assets provided. Design system should follow:
- Problem severity heatmap showing frequency vs. impact
- Pain point list table with severity scores and evidence tiers
- Customer segment breakdown showing problem distribution
- Problem validation scorecard with confidence indicators

## Existing Code to Leverage

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create Problem Validation Agent using StateGraph
- Use structured output for problem extraction and scoring
- Implement similar state management

**Evidence Schema**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Use EvidenceItem model for interview insights
- Classify all customer quotes as E2 evidence
- Track interview sources and validation plans

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create ProblemValidation entity linked to Project
- Store validated problems with severity scores
- Apply multi-tenant RLS for problem data isolation

## Out of Scope

- Primary customer research execution (survey deployment, interview scheduling)
- Automated interview transcription (assumes transcripts provided)
- Real-time interview analysis during customer calls
- Customer interview recruiting and screening
- Problem-solution brainstorming and ideation (ideation agent handles this)
- Competitive problem validation (comparing problems across competitors)
- Problem evolution tracking over time
- Customer feedback management system
- Problem validation workshop facilitation
- Video interview analysis (text transcripts only)
