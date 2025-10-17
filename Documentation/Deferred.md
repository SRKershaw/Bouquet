# Deferred Decisions and Details in Retirement Portfolio Management System Project

## Overview
This document compiles all deferred details, refinements, and decisions from the refinement of HL Requirements.md and use cases derivation. These items were explicitly marked for later phases (e.g., detailed use cases, design, or implementation) to maintain high-level focus and avoid premature specification. They are restructured into thematic topics for easier cross-phase reference.

Deferred items do not block progression to Step 2 (Use Cases.md) but should be revisited in relevant phases (e.g., Phase 8 for API integrations).

Last Updated: October 07, 2025

## Topic 1: SIPP and Tax Modelling
1. **Means-tested benefits modelling (e.g., Pension Credit impacts from drawdowns)**: Deferred to detailed tax forecasting in later phases (Product Manager Q1, Overview).
2. **PAYE-taxed SIPP drawdown modelling as regular income**: Deferred unless architecture implications (Compliance Officer Q5, Management). (Note: Confirmed no implications; still deferred for later.)
3. **Explicit SIPP withdrawal rule configurability (e.g., to optimise tax in Portfolio forecasts)**: Important but deferred to Analysis phase (Product Manager Q1, Forecasting).
4. **Tax confirmation for tax-free flags on pension credits (e.g., PCLS in crystallisation)**: TBD; user-driven with audit logs sufficient (Compliance Officer Q5, Management).

## Topic 2: Rules, Thresholds, and Guardrails
1. **Specific threshold types for rules engine guardrails (e.g., pipeline length in years)**: Deferred; can be defined later unless lack of detail risks non-extensible design (Engineering Manager Q2, Overview).
2. **Threshold-based triggers for Bucket 1 shortfalls (e.g., months of runway)**: Yes, but will be done later (Project Manager Q4, Forecasting).
3. **Configurable severity levels for alerts (e.g., warning vs. urgent in Bucket 1 shortfalls)**: Deferred to detailed rules (Product Manager Q1, Monitoring).
4. **Inflation-adjusted thresholds for shortfall alerts (e.g., runway in real terms)**: Deferred to Forecasting integration (Product Manager Q1, Items 1–5, Monitoring).
5. **Rules for unsustainable SIPP withdrawal patterns (e.g., high rates)**: Deferred to detailed conditions (Product Manager Q1, Items 6–8, Monitoring).

## Topic 3: Alerts and Monitoring Enhancements
1. **LDI alignment examples (e.g., bond maturity scheduling for Bucket 2)**: Deferred unless it impacts software architecture (Product Manager Q2, Overview).
2. **Dashboard integration for prioritised alert summaries**: Deferred to UX outlining (Product Manager Q1, Items 9–10, Monitoring).
3. **Asset unit selection previews for pension transactions (e.g., "Select 50% ETF for crystallisation")**: Deferred to detailed UI (Product Manager Q1, Management).

## Topic 4: Forecasting and Projections
1. **Simulation methods (e.g., Monte Carlo integration)**: Forecast generality sufficient for now; defer to AI phases (Engineering Manager Q3, Scenarios).
2. **Explicit tie-in of Scenarios to Analysis outputs for what-if baselines**: Noted as potential siloing but not pursued; defer to use-case sequencing if needed (implicit gap, Final Review).

## Topic 5: Scenarios and Simulations
- No standalone deferrals beyond cross-topic items above; all tied to rules/thresholds or SIPP.

## Topic 6: Extensibility and Integrations
1. **Pensions Dashboards API readiness in Operational**: Deferred to Phase 8 increments (Product Manager Q1, Final Review).
2. **Bulk CSV import for Catalogue**: Deferred to later phases (Product Manager Q1, Management).

## Topic 7: User Interface and Navigation
1. **Full UI prompts for new user setup (e.g., auto-suggest for Interest category)**: Deferred to onboarding design (User Representative Q5, Management).
2. **Navigation sub-use case (UC-INT-01: Navigate Pages)**: Deferred to Interface derivation (Project Manager Q4, Interface).
3. **Catalogue enrichment fields (e.g., sector from yfinance)**: Deferred to Securities prototype (Engineering Manager Q3, Management).

## General Notes
- **Rationale**: All deferrals align with the hybrid agile-waterfall process, prioritising core completeness over speculative details. Revisit in:
  - Detailed Use Cases (Step 2): Rules/thresholds.
  - ERD/Architecture (Steps 3–7): Extensibility (e.g., API hooks).
  - Implementation Phases (8–9): Tax/SIPP modelling, simulations.
- **Traceability**: Link back to original questions via actor and number (e.g., Product Manager Q1 in Overview).
- **Next Action**: Review this list during Step 2 (Use Cases) to integrate where relevant.

## Appendix: Deferred Items by Phase
| Deferred Item | Suggested Phase for Resolution |
|---------------|--------------------------------|
| Means-tested benefits | Detailed Tax Forecasting (Phase 9) |
| LDI examples | Detailed Design Docs (Step 14) |
| Specific rules/guardrails | Detailed Requirements (Step 13) |
| SIPP configurability | Analysis Use Cases (Step 2) |
| Threshold triggers | Monitoring Use Cases (Step 2) |
| Severity levels | Rules Engine Design (Step 7) |
| Inflation thresholds | Forecasting Use Cases (Step 2) |
| Unsustainable patterns | SIPP-Specific Use Cases (Step 2) |
| Dashboard integration | UX Outline (Step 5) |
| Simulation methods | AI Enhancements (Phase 9) |
| Pensions Dashboards API | Implementation Increment (Phase 8) |
| Scenarios-Analysis tie-in | Use Cases Mapping (Step 2) |
| Tax confirmation for pension flags | Detailed Compliance (Phase 9) |
| Asset unit previews | UI Design (Step 5) |
| Bulk CSV import for Catalogue | Catalogue Enhancements (Phase 8) |
| Full UI prompts for new user setup | Onboarding Design (Step 5) |
| Navigation sub-use case | Interface Derivation (Step 2) |
| Catalogue enrichment fields | Securities Prototype (Phase 8) |

*Document Version: 1.3 (Updated) | Prepared by: Project Team (via Grok)*