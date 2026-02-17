# Business Question Answerability Matrix

This matrix states what can be answered from current bronze coverage + proposed dimensional model.

| Business Question | Answerable? | Status | Required Model Components | Notes / Blockers |
|---|---|---|---|---|
| Which sources/vendors drive conversion? | Yes | Now | `dim_source`, `bridge_lead_source`, `dim_lead` | conversion flag from lead status/conversion fields |
| Are we buying same leads repeatedly across vendors? | Yes | Now | `bridge_lead_source`, `fact_lead_duplicate_analysis` | supports duplicate purchase analytics |
| Is same person represented by multiple lead IDs? | Yes | Now | `dim_lead` normalized identity + duplicate fact | identity-rule threshold must be governed |
| What is lifecycle progression Lead -> Application? | Yes | Conditional | `fact_application_lifecycle`, `dim_application_status`, `dim_lead` | application state mapping finalization needed |
| What is lifecycle progression Application -> Admit? | Yes | Conditional | same as above + admit status mapping | multiple admit/status fields require precedence rule |
| What is lifecycle progression Admit -> Enroll? | Yes | Conditional | `fact_enrollment_outcome`, account/contact/application linkage | canonical enrollment event rule required |
| Which leads need immediate recruiter attention? | Yes | Now | `dim_lead`, `fact_lead_activity`, ownership bridge | staleness threshold must be configured |
| How do credentials impact progression? | Yes | Now | `fact_lead_credential`, lifecycle facts | dependency on credential requirement definitions by program |
| How does ISIR readiness impact progression? | Yes | Now | `fact_lead_financial_readiness`, lifecycle facts | readiness-stage business rule needed for standardization |
| Which recruiter/territory performs better? | Yes | Now | `bridge_lead_recruiter_territory`, `dim_recruiter_territory`, outcomes | assignment effective dating required |
| What are best/worst causes of conversion? | Partially | Conditional | all conformed facts/dims | correlation only unless controlled experiments added |
| Can we prove campaign/source causality? | Not fully | Phase-2 | attribution framework + experimentation | causal design not in current baseline |

## Recommended Client Messaging

- Immediate delivery supports operational decisioning and KPI governance.
- Lifecycle is fully modelable, with conditional status on fields requiring final business precedence.
- Causality is a separate advanced analytics phase.

