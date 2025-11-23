# Specification: VRC Report Generation

## Goal

Generate comprehensive VRC assessment reports with improvement recommendations, gap analysis, and executive summaries to communicate venture readiness to stakeholders in the Discover phase.

## User Stories

- As a venture creator, I want a VRC report so that I can share my venture readiness with investors and stakeholders
- As an advisor, I want improvement recommendations so that I can guide founders toward stronger validation
- As an investor, I want an executive summary so that I can quickly assess venture quality

## Specific Requirements

**Comprehensive VRC Assessment Report**
- Generate full VRC report including all 20 indicators with scores and evidence
- Include detailed analysis of strengths and weaknesses by dimension
- Document all evidence sources with tier classifications
- Export as professional PDF for stakeholder distribution

**Recommendations for Improvement**
- Generate actionable recommendations for improving VRC score
- Prioritize recommendations by impact (high-impact improvements first)
- Suggest specific validation activities to upgrade evidence tiers
- Estimate effort required for each recommendation

**Gap Analysis**
- Identify gaps between current VRC score and target threshold (76%)
- Highlight indicators with low scores (<60) requiring attention
- Show evidence tier gaps (E0/E1 indicators needing E2+ evidence)
- Calculate points needed to pass VRC gate

**Executive Summary**
- Create 1-page executive summary with key findings
- Include overall VRC score, dimension scores, and go/no-go recommendation
- Highlight top 3 strengths and top 3 risks
- Provide concise next steps for venture progression

**Report Versioning and Tracking**
- Version VRC reports as assessment evolves
- Track score improvements over time
- Compare report versions with diff visualization
- Archive historical reports for audit trail

## Visual Design

No visual assets provided. Design system should follow:
- Executive summary dashboard with VRC score gauge and dimension radar chart
- Indicator breakdown table with scores, evidence tiers, and recommendations
- Gap analysis chart showing distance to threshold
- Improvement roadmap with prioritized recommendations

## Existing Code to Leverage

**VRC Assessment Framework Integration**
- Reference VRC assessment framework for score calculation
- Use same 20-indicator structure and evidence tier logic
- Apply consistent quality gate thresholds

**Evidence Management Integration**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/schemas.py`
- Pull evidence data from Evidence Management System
- Link recommendations to evidence gaps
- Track evidence sources for all VRC claims

**LangGraph Agent Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/agents/discover/agent.py`
- Create VRC Report Generation Agent using StateGraph
- Synthesize recommendations from VRC assessment data
- Use structured output for report sections

## Out of Scope

- Interactive VRC report with live data updates (static report generation)
- Custom report templates beyond standard format
- Report collaboration and commenting features
- Automated report distribution via email/Slack (manual sharing)
- VRC report analytics (views, shares, downloads)
- Multi-language report generation
- Report branding customization (white-labeling for Enterprise tier deferred)
- Presentation mode for VRC report pitches
- VRC report comparison across multiple ventures
- Real-time VRC score tracking dashboard
