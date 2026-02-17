# Bronze to Model Mapping (Logical)

Source metadata: `sql/bronze_ddls.txt`  
Scope: base tables only.

## A) Core lifecycle mapping

| Target Model Table | Primary Bronze Sources | Join Keys | Notes |
|---|---|---|---|
| `dim_lead` | `LEAD` | `LEAD.ID` | canonical lead identity and conversion attributes |
| `dim_source` | `SOURCE__C` | derived source hash / source natural tuple | normalize `TYPE__C`, `VENDOR__C`, `VENUE__C` |
| `dim_campaign` | `CAMPAIGN` | `CAMPAIGN.ID` | campaign identity and grouping |
| `dim_activity_type` | `TASK`, `CAMPAIGNMEMBER`, `SOURCE__C`, `CDP_COMMUNICATION_RESULT__C` | standardized code mapping | controlled taxonomy |
| `dim_program_interest` | `PROGRAMS_OF_INTEREST__C` | `ID`, `LEAD__C` | current and historical interest context |
| `dim_recruiter_territory` | `LEAD` | `OWNERID`, `COUNSELOR_TERRITORY__C` | SCD2 assignment dimension |
| `dim_application_status` | `APPLICATION__C`, `APPLICATION_STATUS_HISTORY__C` | status code/value | normalize application/admit statuses |
| `dim_date` | generated date spine | date key | conformed calendar |

## B) Bridge mapping

| Target Bridge | Bronze Source | Relationship |
|---|---|---|
| `bridge_lead_source` | `SOURCE__C` | `SOURCE__C.LEAD__C` -> lead; source tuple -> source dim |
| `bridge_lead_campaign` | `CAMPAIGNMEMBER` | `LEADID` -> lead; `CAMPAIGNID` -> campaign |
| `bridge_lead_program_interest` | `PROGRAMS_OF_INTEREST__C` | `LEAD__C` -> lead; interest row details |
| `bridge_lead_recruiter_territory` | `LEAD` | lead owner + territory over effective periods |

## C) Fact mapping

| Target Fact | Bronze Source(s) | Grain |
|---|---|---|
| `fact_lead_activity` | `TASK`, `CAMPAIGNMEMBER`, `SOURCE__C`, `CDP_COMMUNICATION_RESULT__C` | one activity event |
| `fact_application_lifecycle` | `APPLICATION__C`, `APPLICATION_STATUS_HISTORY__C` | one application event/status transition |
| `fact_enrollment_outcome` | `APPLICATION__C`, `ACCOUNT`, `CONTACT` | one enrollment/admit/enroll outcome event |
| `fact_lead_credential` | `APPLICATION_SUPPORTIVE_DOCUMENTS__C`, optional credential fields from lead/application objects | one credential/readiness event |
| `fact_lead_financial_readiness` | ISIR fields in `LEAD` and/or `APPLICATION__C` where present | one financial readiness event |
| `fact_lead_duplicate_analysis` | `LEAD`, `SOURCE__C` + dedupe logic output | one lead per duplicate run |
| `snapshot_lead_pipeline_weekly` | `WEEKLY_LEAD` + conformed dimensions | one lead-week snapshot |

## D) Specific raw objects enabling Application -> Admit -> Enroll

| Lifecycle Stage | Bronze Objects Found |
|---|---|
| Application | `APPLICATION__C`, `APPLICATION_STATUS_HISTORY__C`, `APPLICATION_SUPPORTIVE_DOCUMENTS__C` |
| Admit | `APPLICATION__C` (`ADMISSION_DECISION__C`, `ADMIT_STATUS_NEW__C`), `ADMISSION_REQUIREMENT__C` |
| Enroll | `ACCOUNT` (`ENROLLED__C`, `CURRENT_STUDENT__C`), related student/account fields; linkage via converted ids and account/contact references |

## E) Mapping caveats (to finalize in ETL spec)

- Decide canonical admit/enroll event precedence when multiple status fields exist.
- Define conformed status mapping for application/admit/enroll states.
- Define enrollment event date logic (first valid enrollment signal precedence).
- Keep strict no-assumption policy: unresolved field precedence should be marked `UNKNOWN` in ETL specs until approved.

