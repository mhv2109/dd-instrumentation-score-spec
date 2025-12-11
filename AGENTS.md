# CLAUDE.md

This file provides guidance to AI Agents when working with code in this repository.

## Project Overview

This repository contains the **Instrumentation Score Specification** - a standardized metric (0-100) for assessing Datadog instrumentation quality. The specification is adapted from OpenTelemetry concepts to use Datadog standard attributes and semantic conventions.

## Repository Structure

### Core Specification Files
- `specification.md` - Complete technical specification including scoring formula, rule structure, and calculation methodology
- `README.md` - Public-facing documentation with overview, quick start, and community information
- `CONTRIBUTING.md` - Contribution guidelines and processes
- `GOVERNANCE.md` - Project governance and maintainer information

### Rules Directory (`rules/`)
Contains individual rule definitions that form the basis of instrumentation scoring. Each rule file follows a strict template structure:

- **Rule ID**: Unique identifier with prefix indicating target (RES=Resource, SPA=Span, MET=Metric, LOG=Log, SDK=SDK)
- **Description**: Brief statement of what the rule checks
- **Rationale**: Why this rule matters for instrumentation quality
- **Target**: The telemetry element type (Resource, Span, Metric, Log, etc.)
- **Criteria**: Precise, objective conditions using MUST/SHOULD/MAY per RFC 2119
- **Impact**: One of Critical (weight 40), Important (weight 30), Normal (weight 20), Low (weight 10)

Examples: `RES-005.md` (service attribute presence), `SPA-001.md` (internal span limits), `MET-001.md` through `MET-006.md` (metric rules)

### Rule Template
`rules/_template.md` provides the canonical structure for new rules. Always use this template when creating new rules.

## Working with Rules

### Creating New Rules

1. Copy `rules/_template.md` to create a new rule file
2. Use appropriate ID prefix based on target:
   - `RES-` for Resource attributes
   - `SPA-` for Span/Trace rules
   - `MET-` for Metric rules
   - `LOG-` for Log rules
   - `SDK-` for SDK-specific rules
3. Number sequentially within category (e.g., RES-001, RES-002)
4. Ensure criteria use RFC 2119 keywords (MUST, SHOULD, MAY)
5. Assign appropriate impact level based on observability impact

### Rule Criteria Guidelines

- Criteria must be objective and algorithmically testable
- Use backticks for attribute names (e.g., `service`, `span.kind`)
- Specify exact conditions for triggering
- Include specific thresholds where applicable
- Reference Datadog standard attributes and semantic conventions

### Impact Level Assignment

Choose impact levels based on observability effectiveness:
- **Critical (40)**: Missing fundamental attributes like `service` that break core functionality
- **Important (30)**: Attributes that significantly affect observability quality
- **Normal (20)**: Best practices that improve efficiency and effectiveness
- **Low (10)**: Minor optimizations and edge case handling

## Scoring Formula

The Instrumentation Score is calculated using weighted impact levels:

$$\text{Score} = \frac{\sum_{i=1}^{N} (P_i \times W_i)}{\sum_{i=1}^{N} (T_i \times W_i)} \times 100$$

Where:
- $P_i$ = number of rules passed for impact level $i$
- $T_i$ = total number of rules for impact level $i$
- $W_i$ = weight for impact level $i$ (Critical=40, Important=30, Normal=20, Low=10)

This ensures Critical failures significantly impact the score, providing clear prioritization for remediation.

## Datadog Semantic Conventions

This specification is built on Datadog conventions, not OpenTelemetry. Key differences:

- Uses Datadog's unified service tagging (`service`, `env`, `version`)
- References Datadog standard attributes instead of OTel semantic conventions
- Focuses on Datadog APM trace structure and requirements
- Designed for Datadog Agent and tracing library instrumentation

When writing or reviewing rules, always reference Datadog documentation, not OpenTelemetry specs.

## Implementation Requirements

Per the specification, any tool implementing this standard:
- MUST implement all rules defined in the specification
- MUST use the standardized calculation formula
- MUST provide information if not all rules are implemented
- MUST NOT include additional rules that affect the standard score

## Community and Governance

- Originally adapted from OpenTelemetry instrumentation score spec
- Initiated by OllyGarden with goal of community governance
- Maintainers listed in GOVERNANCE.md and README.md
- Communication via CNCF Slack #instrumentation-score
- Follows conventional commits for PR titles
- Uses RFC 2119 keywords in rule criteria

## Key Constraints

- This is a specification repository, not an implementation
- No build/test commands - documentation-only project
- Changes should maintain vendor neutrality while focusing on Datadog
- Specification must remain implementable by multiple tools
- Score calculation must be deterministic across implementations
