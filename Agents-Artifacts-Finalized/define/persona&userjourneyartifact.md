# PersonaArtifact and JourneyMapArtifact

These artifacts describe who the key users/customers are (personas) and how they move through key journeys today and in the future. They are produced by the Persona Builder.​

## Pydantic models

```python
from typing import List, Optional
from datetime import datetime
from pydantic import BaseModel

class EvidenceTier(str, Enum):
E0 = "E0"
E1 = "E1"
E2 = "E2"
E3 = "E3"
E4 = "E4"

class Persona(BaseModel):
persona_id: str
name: str # e.g. "Growth-Focused SaaS Founder"
role: str # main job role
company_context: Optional[str] = None # e.g. "B2B SaaS, 20–50 people"
goals: List[str]
pains: List[str]
motivations: List[str]
behaviors: List[str]
channels: List[str] # where they discover/evaluate tools
tech_adoption: Optional[str] = None # e.g. "early adopter"
evidence_tier: EvidenceTier = EvidenceTier.E1
notes: Optional[str] = None

class PersonaArtifact(BaseModel):
artifact_id: str
version: str = "0.1"
created_at: datetime
updated_at: datetime

    problem_artifact_id: str
    define_market_artifact_id: Optional[str] = None

    personas: List[Persona]

    notes: Optional[str] = None

class JourneyStep(BaseModel):
step_id: str
name: str
description: Optional[str] = None
touchpoints: List[str] # channels or product surfaces
pains: List[str]
opportunities: List[str]

class JourneyMap(BaseModel):
journey_id: str
persona_id: str # ties back to Persona.persona_id
name: str # e.g. "Evaluate and adopt tool"
current_state_steps: List[JourneyStep]
future_state_steps: List[JourneyStep]

class JourneyMapArtifact(BaseModel):
artifact_id: str
version: str = "0.1"
created_at: datetime
updated_at: datetime

    problem_artifact_id: str
    persona_artifact_id: str

    journeys: List[JourneyMap]

    notes: Optional[str] = None

```

## Field descriptions

### PersonaArtifact

artifact_id, version, created_at, updated_at: Standard artifact metadata.

problem_artifact_id: ID of the ProblemStatementArtifact to which these personas relate.

define_market_artifact_id: ID of the DefineMarketResearchArtifact used to derive/refine these personas.

personas: List of Persona objects.

notes: Free-form comments.

### Persona

persona_id: Stable identifier used to link personas to stories and journeys.

name: Human name for the persona (for communication).

role: Main job role or function.

company_context: Typical context (industry, size, maturity).

goals: Main outcomes this persona wants to achieve.

pains: Pain points relevant to the problem/solution.

motivations: Why they care, what drives them.

behaviors: Patterns in how they work or make decisions.

channels: Where they hang out / find solutions (e.g. LinkedIn, Reddit, conferences).

tech_adoption: Qualitative tech adoption profile (“early adopter”, etc.).

evidence_tier: Strength of evidence behind this persona definition (E0–E4).

notes: Extra nuance or corner cases.

### JourneyMapArtifact

artifact_id, version, created_at, updated_at: Standard metadata.

problem_artifact_id: Related problem definition.

persona_artifact_id: ID of the PersonaArtifact whose personas these journeys reference.

journeys: List of JourneyMap.

notes: Free-form comments.

### JourneyMap

journey_id: Stable ID for this journey.

persona_id: ID of the persona this journey represents.

name: Name of the journey (e.g. “Onboard and see value”).

current_state_steps: Steps the persona takes today (without your product or with current tools).

future_state_steps: Steps the persona would take in the envisioned solution experience.

### JourneyStep

step_id: Unique ID for the step within a journey.

name: Short label for the step.

description: Additional detail about what happens in this step.

touchpoints: Key interactions/channels (product screens, emails, calls, etc.).

pains: Pain points experienced at this step.

opportunities: Opportunities to add value or remove friction.

```

```
