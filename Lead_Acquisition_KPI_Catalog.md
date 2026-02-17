# Lead Acquisition KPI Catalog

Lifecycle coverage: `Lead -> Application -> Admit -> Enroll`

## KPI Definitions

| KPI | Formula (concept) | Grain | Primary Tables | Confidence |
|---|---|---|---|---|
| Total Leads | distinct leads | day/week/month | `dim_lead` | Now |
| Attributed Lead Coverage % | leads with source mapping / total leads | week/month | `bridge_lead_source`, `dim_lead` | Now |
| Source Conversion Rate | converted leads by source / leads by source | source/vendor x period | `bridge_lead_source`, `dim_source`, `dim_lead` | Now |
| Multi-vendor Overlap Rate | leads linked to >1 vendor / total sourced leads | month | `bridge_lead_source`, `dim_source` | Now |
| Identity Duplicate Rate | normalized identity groups with >1 lead id / total identities | month | `dim_lead`, `fact_lead_duplicate_analysis` | Now |
| Activities per Lead | total relevant activities / distinct leads | week/month | `fact_lead_activity`, `dim_activity_type` | Now |
| Need-Attention Leads | active non-converted leads with stale activity threshold | daily/weekly | `dim_lead`, `fact_lead_activity` | Now |
| Application Start Rate | leads with application started / total leads | cycle/month | `fact_application_lifecycle`, `dim_lead` | Conditional |
| Application Completion Rate | completed applications / started applications | cycle/month | `fact_application_lifecycle` | Conditional |
| Admit Rate | admitted applications / completed applications | cycle/month | `fact_application_lifecycle`, `dim_application_status` | Conditional |
| Enroll Yield Rate | enrolled / admitted | cycle/term | `fact_enrollment_outcome`, `fact_application_lifecycle` | Conditional |
| Credential Readiness Rate | leads with required credentials / active leads | cycle/month | `fact_lead_credential`, `dim_lead` | Now |
| ISIR Readiness Rate | leads with ISIR receipt / active leads | cycle/month | `fact_lead_financial_readiness`, `dim_lead` | Now |
| Recruiter Conversion Rate | converted leads / assigned leads by recruiter | recruiter x cycle | `bridge_lead_recruiter_territory`, `dim_recruiter_territory`, `dim_lead` | Now |
| Territory Conversion Rate | converted leads / assigned leads by territory | territory x cycle | same as above | Now |
| Cost per Converted Net-New Lead | spend / converted leads classified net-new | vendor/campaign x period | `dim_source`, `dim_campaign`, finance inputs | Phase-2 |
| Best/Worst Driver Ranking | ranked correlation impact across source/campaign/activity/readiness factors | cycle/segment | conformed dims + facts | Conditional |

## Confidence Tier Meaning

- **Now**: can be computed from current bronze coverage + proposed model.
- **Conditional**: requires final business rule choices (status mapping, precedence, relevance filters).
- **Phase-2**: requires additional cost/mart integration or causal framework.

## KPI Guardrails

- Driver rankings are correlation-based in phase-1, not causal proof.
- Duplicate analysis must separate:
  - strict entity duplicates (same record identity)
  - identity duplicates (same normalized person attributes across multiple IDs)

