# RequirementsArtifact

Represents the Define-phase PRD: user stories, non-functional requirements, success metrics, and key dependencies. Produced by the Requirements Specialist.

## Pydantic Model

```python
from enum import Enum
from typing import List, Optional
from datetime import datetime
from pydantic import BaseModel


class RequirementPriority(str, Enum):
    MUST = "must"
    SHOULD = "should"
    COULD = "could"
    WONT = "wont"


class UserStory(BaseModel):
    story_id: str
    persona_id: Optional[str] = None
    as_a: str
    i_want: str
    so_that: str
    priority: RequirementPriority
    acceptance_criteria: List[str]


class NonFunctionalRequirement(BaseModel):
    nfr_id: str
    category: str                # e.g. "performance", "security"
    description: str
    acceptance_criteria: List[str]


class RequirementsArtifact(BaseModel):
    artifact_id: str
    version: str = "0.1"
    created_at: datetime
    updated_at: datetime

    problem_artifact_id: str
    persona_artifact_id: str
    journey_artifact_id: str

    product_overview: str        # vision + objectives
    success_metrics: List[str]   # measurable outcomes (e.g. activation, NPS)

    user_stories: List[UserStory]
    non_functional_requirements: List[NonFunctionalRequirement]

    dependencies: List[str]      # technical or business dependencies
    notes: Optional[str] = None
```

## Field Descriptions

### RequirementsArtifact

- **artifact_id, version, created_at, updated_at**: Standard metadata.
- **problem_artifact_id**: ID of the ProblemStatementArtifact grounding these requirements.
- **persona_artifact_id**: ID of the PersonaArtifact that provided persona context.
- **journey_artifact_id**: ID of the JourneyMapArtifact used to derive flows.
- **product_overview**: Short narrative of what the product is and why it exists.
- **success_metrics**: List of high-level outcome metrics that define success (e.g. "Activation rate from X to Y in 6 months").
- **user_stories**: Detailed functional requirements framed as user stories.
- **non_functional_requirements**: Cross-cutting concerns (performance, security, compliance, etc.).
- **dependencies**: List of key dependencies or constraints (e.g. "requires Stripe", "needs SSO provider").
- **notes**: Free-form comments.

### UserStory

- **story_id**: Unique identifier used for tracking/prioritisation.
- **persona_id**: Optional link to the persona this story primarily serves.
- **as_a**: Role part of the story ("As a growth marketer…").
- **i_want**: Capability or action desired.
- **so_that**: Business or user outcome desired.
- **priority**: MoSCoW priority (Must/Should/Could/Won't).
- **acceptance_criteria**: List of testable conditions for completion.

### NonFunctionalRequirement

- **nfr_id**: Unique identifier for the NFR.
- **category**: Area of concern (e.g. "security", "availability", "performance").
- **description**: What is required.
- **acceptance_criteria**: What must be true for this NFR to be met.
