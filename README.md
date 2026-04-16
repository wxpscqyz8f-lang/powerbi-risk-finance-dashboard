---
title: "Plateforme BI Stratégique"
permalink: /
---

# powerbi-risk-finance-dashboard
Power BI dashboards for Risk &amp; Finance reporting — BNP Paribas ITG-FRESH

> (i) Inspirée d’un cas réel en entreprise, recréée de manière fictive et simplifiée à des fins pédagogiques. Aucun élément confidentiel n’est présenté.

#  Plateforme BI Stratégique — Power BI Risk & Finance (BNP Paribas ITG-FRESH)
 
> **Contexte :** Transformation digitale de la division Risk & Finance d'une grande banque française (ITG-FRESH / pôle RDS)
> **Rôle :** Business Analyst Expert Power BI — freelance, en régie — seul responsable de 6 dashboards stratégiques
> **Période :** 2023–2024 | **Statut :** Livré et en production
 
---
 
##  Problème métier
 
Les équipes Risk & Finance d'ITG-FRESH s'appuyaient entièrement sur des **rapports Excel manuels envoyés par e-mail** pour suivre les ressources, les budgets, les risques et l'obsolescence IT de l'organisation.
 
**Points de douleur :**
- Aucune visibilité temps réel sur les indicateurs stratégiques
- Incohérences entre les versions de fichiers Excel échangés par e-mail
- Données financières sensibles sans contrôle d'accès
- Consolidation manuelle hebdomadaire — plusieurs heures de travail chaque semaine
 
**Objectif :** Remplacer 6 rapports Excel manuels par des dashboards Power BI interactifs, temps réel, avec une gouvernance complète (RLS, Dev/Test/Prod, actualisation automatisée).
 
---
 
##  Architecture
 
```
┌─────────────────────────────────────────────────────────────────┐
│                     SOURCES DE DONNÉES                           │
│  SFTP (fichiers)  │  SharePoint (API)  │  ServiceNow GRC (API)   │
└───────┬─────────────────┬──────────────────┬────────────────────┘
        │                 │                  │
        ▼                 ▼                  ▼
┌─────────────────────────────────────────────────────────────────┐
│              COUCHE AUTOMATISATION PYTHON                        │
│  Connecteur API ServiceNow  │  Connecteur SharePoint            │
│  Planificateur d'actualisation Power BI Server                  │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│           POWER BI DESKTOP — MODÉLISATION DES DONNÉES            │
│  Schéma en étoile  │  Mesures DAX  │  Optimisation DAX Studio   │
└─────────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────────┐
│      POWER BI SERVER — GOUVERNANCE (Dev → Test → Prod)          │
│  RLS par profil utilisateur  │  Espaces de travail  │  Refresh  │
└──────────────┬──────────────────────────┬───────────────────────┘
               ▼                          ▼
     Utilisateurs métier          Équipes de direction
```
 
---
 
## 6 Dashboards Stratégiques
 
| Dashboard | Domaine | Indicateurs clés |
|---|---|---|
| **Location Stratégie** | RH / Staffing | Ressources par profil, sourcing, stratégie de relocalisation, recrutement interne |
| **Capacité & Budget** | Finance | Roadmap budgétaire, prévisions, coûts par projet / équipe / domaine |
| **Agilité** | Transformation | Indicateurs de maturité agile par équipe |
| **Risque Applicatif** | Risque | Suivi des risques — criticité, exposition, plans de remédiation |
| **Obsolescence** | Gouvernance IT | Fin de support : applications, frameworks, langages |
| **Migration SharePoint** | Ops IT | Suivi de migration NAS Driver → SharePoint |
 
---
 
## Contenu du dépôt
 

 
```
├── dashboards/
│   └── risk_finance_platform_anonymized.pbix   ← Fichier de démo complet
├── connectors/
│   ├── servicenow_connector.py                 ← API ServiceNow GRC
│   ├── sharepoint_connector.py                 ← Listes/bibliothèques SharePoint
│   └── sftp_ingestion.py                       ← Ingestion fichiers SFTP
├── automation/
│   └── refresh_scheduler.py                    ← Planificateur refresh Power BI Server
├── docs/
│   ├── technical_specs.md
│   ├── use_cases.md
│   └── user_guide.md
└── README.md
```
 
---
 
##  Éléments Techniques Clés
 
### DAX — Exemples de mesures complexes
 
```dax
-- Écart budgétaire cumulé (YTD)
Ecart Budget % =
DIVIDE(
    [Coût Réel YTD] - [Budget YTD],
    [Budget YTD],
    0
)
 
-- Comptage des applications à risque d'obsolescence (6 prochains mois)
Apps A Risque =
CALCULATE(
    COUNTROWS(Applications),
    Applications[date_fin_support] <= TODAY() + 180,
    Applications[statut] = "Actif"
)
 
-- Filtre RLS
Est Utilisateur Courant =
[DomaineUtilisateur] = USERPRINCIPALNAME()
```
 
### Python — Connecteur ServiceNow GRC
 
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
 
### Gouvernance Power BI
 
```
Espace de travail RISK_DEV   → Développement & itération (analyste uniquement)
Espace de travail RISK_TEST  → Validation par les parties prenantes
Espace de travail RISK_PROD  → Production — RLS activé, refresh planifié
 
Rôles RLS :
  RISK_VIEWER       → Données de son département uniquement
  FINANCE_MANAGER   → Périmètre finance complet
  ADMIN             → Toutes les données, tous les dashboards
```
 
---
 
##  Résultats
 
- ==> **6 rapports Excel manuels remplacés** par des dashboards interactifs temps réel
- ==> **Visibilité stratégique unifiée** pour la direction Risk & Finance
- ==> **Gouvernance complète déployée** — RLS, Dev/Test/Prod, actualisation automatisée
- ==> **Ownership bout en bout** — recueil des besoins → construction → gouvernance → formation → support
- ==> Conformité niveau bancaire : contrôle d'accès aux données, processus de gestion des changements appliqué
 
---
 
## Stack Complète
 
| Couche | Technologie |
|---|---|
| Plateforme BI | Power BI Desktop, Power BI Server |
| Modélisation & Calcul | DAX, DAX Studio, schéma en étoile |
| Sources | SFTP, SharePoint API, ServiceNow GRC REST API |
| Automatisation | Python (requests, office365-rest-python-client) |
| IDE | PyCharm |
| Gouvernance | RLS, promotion d'espaces de travail, actualisation planifiée |
| Méthode | Agile/Scrum — daily standups, revues de sprint |
 
---
 
## Projets Associés
 
- [Pipeline de matching consultants-missions — AWS Lambda + NLP](https://github.com/wxpscqyz8f-lang/aws-nlp-consultant-matching/)
- [Modélisation Data Warehouse dbt](#) *(à venir)*
