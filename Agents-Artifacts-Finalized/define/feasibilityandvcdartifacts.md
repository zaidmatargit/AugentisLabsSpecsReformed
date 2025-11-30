# FeasibilityArtifact and VCDArtifact

FeasibilityArtifact captures technical, operational, and financial feasibility. VCDArtifact synthesizes all Define outputs into a Venture Concept Document. Both are used as the gate to the Design phase.

## FeasibilityArtifact Model

```python
from typing import List, Optional
from datetime import datetime
from pydantic import BaseModel


class RiskItem(BaseModel):
    risk_id: str
    category: str            # "technical", "operational", "financial"
    description: str
    likelihood: str          # "low", "medium", "high"
    impact: str              # "low", "medium", "high"
    mitigation: Optional[str] = None


class FeasibilityScore(BaseModel):
    dimension: str           # "technical", "operational", "financial"
    score_0_to_100: int
    rationale: Optional[str] = None


class FeasibilityArtifact(BaseModel):
    artifact_id: str
    version: str = "0.1"
    created_at: datetime
    updated_at: datetime

    requirements_artifact_id: str

    technical_summary: str
    operational_summary: str
    financial_summary: str

    scores: List[FeasibilityScore]
    risks: List[RiskItem]

    notes: Optional[str] = None
```

### FeasibilityArtifact Fields

- **artifact_id, version, created_at, updated_at**: Standard metadata.
- **requirements_artifact_id**: ID of the RequirementsArtifact this feasibility assessment is based on.
- **technical_summary**: Narrative summary of technical feasibility (architecture approach, key challenges).
- **operational_summary**: Narrative summary of operational feasibility (support, onboarding, delivery).
- **financial_summary**: Narrative summary of financial feasibility (cost, ROI, runway implications).
- **scores**: List of FeasibilityScore per dimension (technical/operational/financial).
- **risks**: List of RiskItem entries with severity and mitigation.
- **notes**: Free-form comments.

### FeasibilityScore

- **dimension**: Dimension being scored (e.g. "technical").
- **score_0_to_100**: Numeric score for feasibility in this dimension.
- **rationale**: Explanation for the score.

### RiskItem

- **risk_id**: Unique identifier for the risk.
- **category**: Risk category (technical, operational, financial).
- **description**: Description of the risk.
- **likelihood**: Qualitative likelihood (low/medium/high).
- **impact**: Qualitative impact (low/medium/high).
- **mitigation**: Planned mitigation strategy.

## VCDArtifact Model

```python
class VCDArtifact(BaseModel):
    artifact_id: str
    version: str = "0.1"
    created_at: datetime
    updated_at: datetime

    problem_artifact_id: str
    define_market_artifact_id: str
    persona_artifact_id: str
    journey_artifact_id: str
    requirements_artifact_id: str
    feasibility_artifact_id: str

    executive_summary: str
    market_summary: str
    customer_summary: str       # personas + journeys
    product_summary: str        # core solution + key requirements
    feasibility_summary: str    # key feasibility conclusions
    implementation_plan: str
    risk_management_summary: str

    appendices_refs: List[str]  # IDs or links to underlying artifacts
    notes: Optional[str] = None
```

### VCDArtifact Fields

- **artifact_id, version, created_at, updated_at**: Standard metadata.
- **problem_artifact_id**: ID of the ProblemStatementArtifact (linking back to Discover).
- **define_market_artifact_id**: ID of DefineMarketResearchArtifact.
- **persona_artifact_id**: ID of PersonaArtifact.
- **journey_artifact_id**: ID of JourneyMapArtifact.
- **requirements_artifact_id**: ID of RequirementsArtifact.
- **feasibility_artifact_id**: ID of FeasibilityArtifact.
- **executive_summary**: High-level overview of venture concept and key points.
- **market_summary**: Summary of target segments, sizing, and positioning.
- **customer_summary**: Summary of personas and key journeys.
- **product_summary**: Summary of solution concept and critical requirements.
- **feasibility_summary**: Summary of technical/operational/financial feasibility.
- **implementation_plan**: High-level rollout plan (phases, milestones).
- **risk_management_summary**: Summary of key risks and mitigation strategies.
- **appendices_refs**: References/IDs for underlying detailed artifacts.
- **notes**: Extra commentary or reviewer notes.
