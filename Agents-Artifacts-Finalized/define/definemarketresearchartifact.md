# DefineMarketResearchArtifact

Extends Discover market research with more detailed segments, pricing patterns, and whitespace analysis to drive personas and requirements. This is produced by the Market Research Specialist in the Define phase.

## Data Model

### EvidenceTier (Enum)

| Value | Description |
| ----- | ----------- |
| E0    | E0          |
| E1    | E1          |
| E2    | E2          |
| E3    | E3          |
| E4    | E4          |

### MarketSegment

| Field           | Type           | Default  | Description                                                           |
| --------------- | -------------- | -------- | --------------------------------------------------------------------- |
| `name`          | string         | Required | Name of the segment (e.g. "Mid-market DTC brands with subscriptions") |
| `description`   | string \| null | None     | Brief description of the segment's characteristics                    |
| `size_estimate` | float \| null  | None     | Numeric estimate (e.g. number of companies or revenue)                |
| `size_units`    | string \| null | None     | Unit of size (e.g. "companies", "USD_annual_revenue")                 |
| `evidence_tier` | EvidenceTier   | E1       | Strength of evidence behind this segment size/definition (E0–E4)      |
| `notes`         | string \| null | None     | Any caveats or assumptions                                            |

### PricingBand

| Field            | Type           | Default  | Description                                              |
| ---------------- | -------------- | -------- | -------------------------------------------------------- |
| `label`          | string         | Required | Label for the band (e.g. "Starter", "Pro", "Enterprise") |
| `min_price`      | float \| null  | None     | Approximate minimum price                                |
| `max_price`      | float \| null  | None     | Approximate maximum price                                |
| `currency`       | string \| null | None     | Currency used (e.g. "USD")                               |
| `billing_period` | string \| null | None     | Typical billing interval (e.g. "monthly", "annual")      |
| `notes`          | string \| null | None     | Extra context (e.g. discounting patterns, add-ons)       |

### DefineMarketResearchArtifact

| Field                              | Type                | Default  | Description                                                               |
| ---------------------------------- | ------------------- | -------- | ------------------------------------------------------------------------- |
| `artifact_id`                      | string              | Required | Unique ID for this Define-phase market research artifact                  |
| `version`                          | string              | "0.1"    | Version label (e.g. "0.1") for progressive updates                        |
| `created_at`                       | datetime            | Required | Timestamp of creation                                                     |
| `updated_at`                       | datetime            | Required | Timestamp of last update                                                  |
| `problem_artifact_id`              | string              | Required | ID of the ProblemStatementArtifact this research builds on                |
| `discover_competitive_artifact_id` | string \| null      | None     | ID of the CompetitiveLandscapeArtifact from Discover (for traceability)   |
| `discover_market_artifact_id`      | string \| null      | None     | ID of the MarketOpportunityArtifact from Discover                         |
| `segments`                         | List[MarketSegment] | Required | List of MarketSegment definitions for target segments you will design for |
| `pricing_bands`                    | List[PricingBand]   | Required | High-level pricing patterns that inform your monetization assumptions     |
| `whitespace_summary`               | string \| null      | None     | Short narrative describing key white-space / opportunity areas            |
| `go_to_market_implications`        | string \| null      | None     | Notes on how findings affect GTM (targets, channels, positioning)         |
| `notes`                            | string \| null      | None     | Free-form comments                                                        |

## Field Descriptions

DefineMarketResearchArtifact
artifact_id: Unique ID for this Define-phase market research artifact.

version: Version label (e.g. "0.1") for progressive updates.

created_at / updated_at: Timestamps for creation and last update.

problem_artifact_id: ID of the ProblemStatementArtifact this research builds on.

discover_competitive_artifact_id: ID of the CompetitiveLandscapeArtifact from Discover (for traceability).​

discover_market_artifact_id: ID of the MarketOpportunityArtifact from Discover.​

segments: List of MarketSegment definitions for target segments you will design for.

pricing_bands: High-level pricing patterns that inform your monetization assumptions.

whitespace_summary: Short narrative describing key white-space / opportunity areas.

go_to_market_implications: Notes on how findings affect GTM (targets, channels, positioning).

notes: Free-form comments.

MarketSegment
name: Name of the segment (e.g. “Mid-market DTC brands with subscriptions”).

description: Brief description of the segment’s characteristics.

size_estimate: Numeric estimate (e.g. number of companies or revenue).

size_units: Unit of size (e.g. "companies", "USD_annual_revenue").

evidence_tier: Strength of evidence behind this segment size/definition (E0–E4).

notes: Any caveats or assumptions.

PricingBand
label: Label for the band (e.g. “Starter”, “Pro”, “Enterprise”).

min_price / max_price: Approximate price range.

currency: Currency used (e.g. "USD").

billing_period: Typical billing interval (e.g. "monthly", "annual").

notes: Extra context (e.g. discounting patterns, add-ons).
