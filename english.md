---
title : English
permalink: /english/
---

---


# Strategic BI Platform — Power BI Risk & Finance (BNP Paribas ITG-FRESH)

> **Context:** Digital transformation at a major French bank's Risk & Finance division (ITG-FRESH / RDS pole)
> **Role:** Business Analyst Expert Power BI — freelance, on-site — sole owner of 6 strategic dashboards
> **Period:** 2023–2024 | **Status:** Delivered & in production

---

## Business Problem

The Risk & Finance teams at ITG-FRESH relied entirely on **manual Excel reports sent by email** to track resources, budgets, risks, and IT obsolescence across the organization.

**Pain points:**
- No real-time visibility on strategic indicators
- Data inconsistencies between email versions
- Sensitive financial data with no access control
- Manual weekly consolidation — hours of work every week

**Objective:** Replace 6 manual Excel reports with interactive, real-time Power BI dashboards with full enterprise governance (RLS, Dev/Test/Prod, automated refresh).

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     DATA SOURCES                                 │
│  SFTP (files)  │  SharePoint API  │  ServiceNow GRC (REST API)  │
└───────┬─────────────────┬──────────────────┬────────────────────┘
        │                 │                  │
        ▼                 ▼                  ▼
┌─────────────────────────────────────────────────────────────────┐
│              PYTHON AUTOMATION LAYER                             │
│  ServiceNow API connector  │  SharePoint connector              │
│  Automated Power BI Server refresh scheduler                    │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│           POWER BI DESKTOP — DATA MODELING                       │
│  Star schema  │  DAX measures  │  DAX Studio optimization       │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│      POWER BI SERVER — GOVERNANCE (Dev → Test → Prod)           │
│  RLS by user profile  │  Workspaces  │  Scheduled refresh       │
└──────────────┬──────────────────────────┬───────────────────────┘
               ▼                          ▼
       Risk & Finance users         Management teams
```

---

## 6 Strategic Dashboards

| Dashboard | Domain | Key Indicators |
|---|---|---|
| **Location Strategy** | HR / Staffing | Resources by profile, sourcing, relocation strategy, internal hiring |
| **Capacity & Budget** | Finance | Budget roadmap, forecasts, costs by project / team / domain |
| **Agility** | Transformation | Agile maturity indicators by team |
| **Application Risk** | Risk | Risk tracking — criticality, exposure, remediation plans |
| **Obsolescence** | IT Governance | End-of-support: apps, frameworks, languages |
| **SharePoint Migration** | IT Ops | NAS Driver → SharePoint migration tracking |

---

## Repository Contents

> ⚠️ The `.pbix` file included uses **anonymized sample data** — all real business data has been replaced with fictional values. The data model, DAX measures, RLS configuration and visual structure are authentic.

```
├── dashboards/
│   └── risk_finance_platform_anonymized.pbix   ← Full demo file
├── connectors/
│   ├── servicenow_connector.py                 ← ServiceNow GRC API
│   ├── sharepoint_connector.py                 ← SharePoint lists/libs
│   └── sftp_ingestion.py                       ← SFTP file ingestion
├── automation/
│   └── refresh_scheduler.py                    ← Power BI Server refresh
├── docs/
│   ├── technical_specs.md
│   ├── use_cases.md
│   └── user_guide.md
└── README.md
```

---

## Key Technical Elements

### DAX — Examples of complex measures

```dax
-- Budget variance YTD
Budget Variance % =
DIVIDE(
    [Actual Cost YTD] - [Budget YTD],
    [Budget YTD],
    0
)

-- Obsolescence risk count (next 6 months)
Apps At Risk =
CALCULATE(
    COUNTROWS(Applications),
    Applications[end_of_support_date] <= TODAY() + 180,
    Applications[status] = "Active"
)

-- RLS filter
Is Current User =
[UserDomain] = USERPRINCIPALNAME()
```

### Python — ServiceNow GRC connector

```python
import requests

def get_servicenow_risks(instance, auth, table="sn_risk_risk"):
    url = f"https://{instance}.service-now.com/api/now/table/{table}"
    params = {
        "sysparm_fields": "name,risk_score,state,category,assigned_to",
        "sysparm_limit": 1000
    }
    response = requests.get(url, auth=auth, params=params)
    return response.json()["result"]
```

### Power BI Governance

```
Workspace RISK_DEV   → Development & iteration (analyst only)
Workspace RISK_TEST  → Stakeholder validation
Workspace RISK_PROD  → Production — RLS enforced, refresh scheduled

RLS Roles:
  RISK_VIEWER       → Own department data only
  FINANCE_MANAGER   → Full finance scope
  ADMIN             → All data, all dashboards
```

---

##  Results

- ==> **6 manual Excel reports replaced** by real-time interactive dashboards
- ==> **Unified strategic visibility** for Risk & Finance leadership
- ==> **Full governance deployed** — RLS, Dev/Test/Prod, automated refresh
- ==> **End-to-end ownership** — requirements → build → governance → training → support
- ==> Banking-grade compliance: data access control, change management process enforced

---

## Full Stack

| Layer | Technology |
|---|---|
| BI Platform | Power BI Desktop, Power BI Server |
| Modeling & Calc | DAX, DAX Studio, star schema |
| Sources | SFTP, SharePoint API, ServiceNow GRC REST API |
| Automation | Python (requests, office365-rest-python-client) |
| IDE | PyCharm |
| Governance | RLS, workspace promotion, scheduled refresh |
| Method | Agile/Scrum — daily standups, sprint reviews |

---

## Related Projects

- [Consultant-Mission Matching Pipeline — AWS Lambda + NLP](../consultant-matching-pipeline/README.md)
- [dbt Data Warehouse Modeling](#) *(coming soon)*

