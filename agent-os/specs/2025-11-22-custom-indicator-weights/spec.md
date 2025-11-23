# Specification: Custom Indicator Weights

## Goal

Enable customization of VRC assessment indicator weights to support industry-specific validation profiles, weight optimization based on venture type, and expert panel configuration for tailored venture readiness evaluation (Enterprise tier feature).

## User Stories

- As an enterprise customer, I want to customize VRC indicator weights so that assessments align with my industry's success factors
- As a venture studio, I want industry-specific assessment profiles so that I can apply consistent evaluation criteria across my portfolio
- As an expert advisor, I want to configure custom weights based on my domain expertise so that assessments reflect real-world success patterns

## Specific Requirements

**Customizable VRC Indicator Weights**
- Allow modification of weights for all 20 VRC indicators
- Ensure weights sum to 100% across all indicators
- Validate weight ranges (min 1%, max 15% per indicator to prevent overemphasis)
- Support saving and loading custom weight profiles

**Industry-Specific Assessment Profiles**
- Provide pre-configured weight profiles for common industries (SaaS, FinTech, Healthcare, E-commerce, etc.)
- Allow creation of new custom profiles from scratch
- Enable profile inheritance and modification (start from SaaS profile, adjust for vertical SaaS)
- Document rationale and evidence for profile weight recommendations

**Weight Optimization Based on Venture Type**
- Recommend weight adjustments based on venture characteristics (B2B vs. B2C, marketplace vs. SaaS, etc.)
- Analyze historical venture data to suggest optimal weights (if available)
- Provide weight sensitivity analysis showing impact on VRC score
- Validate weight choices against evidence tier requirements

**Expert Panel Configuration**
- Allow designation of expert advisors who can configure custom weights
- Implement approval workflow for custom weight profiles
- Track weight profile authorship and change history
- Enable expert commentary and notes on weight rationale

**Weight Profile Management**
- Create, read, update, delete custom weight profiles
- Associate profiles with ventures or organizations
- Set default profile per organization (Enterprise tier)
- Export/import weight profiles for sharing across organizations

## Visual Design

No visual assets provided. Design system should follow:
- Weight adjustment sliders showing % allocation per indicator
- Total weight sum indicator (must equal 100%)
- Industry profile selector dropdown
- Weight comparison table showing default vs. custom weights

## Existing Code to Leverage

**VRC Assessment Framework**
- Reference VRC Assessment Framework for 20 indicator definitions
- Integrate custom weights into existing VRC scoring logic
- Maintain compatibility with phase gate decision system

**Project Model Pattern**
- Location: `/home/augentisai_admin/projects/AugentisLabs/apps/core/models.py`
- Create WeightProfile entity with org_id for tenant isolation
- Link weight profiles to Project or Organization entities
- Track version history for weight profile changes

**Multi-Tenant Architecture**
- Apply RLS policies to weight profiles (Enterprise tier only)
- Enforce tier-based access control (Enterprise feature)
- Isolate custom profiles per organization

## Out of Scope

- Machine learning-based weight optimization (manual configuration only)
- A/B testing of weight profiles with venture outcomes
- Real-time weight recommendation based on venture performance data
- Integration with external expert networks for weight validation
- Collaborative weight configuration with multi-user editing
- Weight profile marketplace or sharing across organizations
- Automated weight adjustment based on market conditions
- Predictive modeling of VRC score impact from weight changes
- Historical analysis of weight profile effectiveness
- Mobile app for weight profile management
