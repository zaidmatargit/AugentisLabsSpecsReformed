# Specification: VCD Assembly & Approval

## Goal

Generate Venture Concept Definition (VCD) documents consolidating problem validation, market research, personas, requirements, and feasibility analysis with stakeholder approval workflow to gate progression from Define to Design phase.

## User Stories

- As a product owner, I want a comprehensive VCD document so that all definition work is consolidated before design starts
- As a stakeholder, I want an approval workflow so that I can review and sign off on the venture concept
- As a program manager, I want VCD versioning so that I can track concept evolution and approvals

## Specific Requirements

**VCD Document Generation**
- Consolidate all Define phase outputs (market research, personas, PRD, feasibility, cost model)
- Include executive summary with venture concept overview
- Embed evidence tier indicators for all key assumptions
- Generate comprehensive VCD as PDF and markdown

**Stakeholder Approval Workflow**
- Implement multi-stakeholder approval (product owner, tech lead, finance, marketing)
- Support parallel or sequential approval workflows
- Enable conditional approvals with documented concerns
- Track approval timestamps, approvers, and justifications

**VCD Versioning**
- Version VCD documents with semantic versioning (v1.0, v1.1, etc.)
- Track changes between VCD versions with diff visualization
- Archive previous VCD versions for audit trail
- Link VCD versions to VRC and DSP for traceability

**Sign-Off Tracking**
- Record all stakeholder sign-offs with timestamps
- Document approval rationale or concerns
- Require re-approval if major changes occur after initial sign-off
- Generate sign-off summary for audit purposes

**Phase Gate Integration**
- Block progression to Design phase until VCD approval complete
- Require minimum VCD quality score (≥75%)
- Validate all Define phase artifacts present
- Generate phase gate report showing concept readiness

## Visual Design

No visual assets provided. Design system should follow:
- VCD document viewer with section navigation
- Approval workflow panel showing stakeholder status
- Version comparison view with highlighted changes
- Evidence tier distribution chart

## Existing Code to Leverage

**Phase Gate Decision System**
- Reference phase gate logic for VCD approval workflow
- Apply similar quality score calculation
- Use consistent gate blocking mechanisms

**Evidence Management Integration**
- Reference evidence management system for tier validation
- Aggregate evidence across all VCD sections
- Validate minimum E2+ evidence tier for critical assumptions

**Project Model Extension**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create VCD entity with version tracking and approval records
- Link to Project with phase progression logic
- Apply multi-tenant RLS for VCD data isolation

## Out of Scope

- Real-time collaborative VCD editing (static document approval)
- Integration with external document management systems
- Custom VCD templates by industry
- Automated VCD updates based on new evidence (manual revision)
- VCD presentation mode for stakeholder pitches
- External stakeholder access without platform accounts
- VCD analytics showing review time and bottlenecks
- Multi-language VCD generation
- VCD export to business planning tools
- Video walkthrough embedding in VCD
