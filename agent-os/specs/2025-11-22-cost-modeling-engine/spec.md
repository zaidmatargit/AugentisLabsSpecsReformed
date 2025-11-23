# Specification: Cost Modeling Engine

## Goal

Automatically generate development cost estimates, resource requirement plans, timeline projections, and ROI calculations to support financial planning and investment decisions in the Define phase.

## User Stories

- As a venture creator, I want development cost estimates so that I can plan my budget and funding needs
- As a CFO, I want resource requirement plans so that I can allocate team capacity appropriately
- As an investor, I want ROI calculations so that I can evaluate financial viability and returns

## Specific Requirements

**Development Cost Estimation**
- Estimate development costs based on feature complexity from PRD
- Break down costs by phase (Design, Develop, Deploy)
- Include labor costs using market rates by role (developer, designer, PM)
- Add infrastructure costs (Vercel, Supabase, third-party APIs)

**Resource Requirement Planning**
- Calculate required person-hours per feature
- Recommend team composition (e.g., 2 full-stack developers, 1 designer, 0.5 PM)
- Estimate calendar time based on team size and parallel work capacity
- Identify resource bottlenecks and critical path dependencies

**Timeline Projection**
- Generate Gantt chart showing development timeline
- Account for dependencies between features and phases
- Include buffer time for iteration and quality assurance
- Project milestone dates (VCD approval, design complete, MVP launch, etc.)

**ROI Calculations**
- Calculate return on investment using revenue projections from opportunity scoring
- Estimate payback period and break-even timeline
- Project 3-year NPV and IRR based on cost and revenue models
- Perform sensitivity analysis on key assumptions (pricing, adoption rate, churn)

**Cost Model Export and Visualization**
- Generate cost breakdown charts (pie chart by category, bar chart by phase)
- Export as spreadsheet (CSV) for further financial modeling
- Create executive summary with total cost and timeline
- Enable scenario comparison (lean MVP vs. full-featured launch)

## Visual Design

No visual assets provided. Design system should follow:
- Cost breakdown pie chart showing labor vs. infrastructure vs. third-party costs
- Timeline Gantt chart with phase milestones
- ROI projection line chart over 3 years
- Resource allocation table showing team composition

## Existing Code to Leverage

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create Cost Modeling Agent using StateGraph orchestration
- Use structured output for cost estimates and timelines
- Implement state management for iterative refinement

**Evidence Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Link cost assumptions to evidence tier classification
- Validate estimates against market data (E2+ evidence)
- Track cost estimate confidence levels

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create CostModel entity associated with Project
- Store cost breakdown, timeline, and ROI projections
- Apply multi-tenant RLS for financial data isolation

## Out of Scope

- Real-time cost tracking against estimates during development
- Integration with accounting software (QuickBooks, Xero)
- Detailed financial modeling beyond development costs (operating expenses, sales/marketing)
- Multi-currency support and exchange rate handling
- Tax implications and financial compliance modeling
- Scenario planning tools with Monte Carlo simulations
- Resource leveling and optimization algorithms
- Contractor vs. employee cost comparison
- Geographic cost variations (SF vs. remote team)
- Post-launch operational cost projections
