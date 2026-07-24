# FinCrime Compliance Platform

> **Enterprise AML & fraud detection built for compliance teams — not just engineers.**

[![Demo Request](https://img.shields.io/badge/Request%20a%20Demo-%F0%9F%93%85%20Schedule%20Now-blue?style=for-the-badge)](https://github.com/naimlabd/fincrime-aml-platform-showcase/issues/new?template=demo-request.yml)
[![Status](https://img.shields.io/badge/Status-Active%20Development-green?style=flat-square)](.)
[![Stack](https://img.shields.io/badge/Stack-FastAPI%20%7C%20Next.js%20%7C%20PostgreSQL-informational?style=flat-square)](.)
[![Tests](https://img.shields.io/badge/Tests-96%20Passing-brightgreen?style=flat-square)](.)

---

## What is this?

A production-grade **Anti-Money Laundering (AML) compliance platform** that combines deterministic regulatory rule engines with ML-based fraud detection. Built to meet the standards compliance officers, BSA officers, and FinCEN auditors actually require.

This is **not** a wrapper around a generic LLM. Every regulatory decision is deterministic, explainable, and fully auditable — as required by 31 CFR.

---

## Key Capabilities

### Regulatory Rule Engine
- **CTR automation** — 31 CFR 1010.311 (cash transactions ≥ $10,000, correct transaction-type gating)
- **SAR candidate detection** — 31 CFR 1020.320 (composite score ≥ 75 OR any critical-severity rule)
- **Structuring / smurfing detection** — AML001 with configurable lookback window
- **FATF jurisdiction risk** — 3-tier severity (High-Risk → Grey List → Offshore), all four country fields checked
- **Velocity spike detection** — ratio ≥ 5× AND daily count ≥ 8, with new-customer baseline guard
- **Crypto mixing / privacy coin** — Monero, Zcash, Dash, mixer-detected flags
- **Cross-border large value** — AML009, configurable threshold
- **Round-amount suspicion** — AML012 with amount floor gate

### Risk Scoring
- Ensemble model: **AML 35% + Fraud 30% + ML 35%**
- Composite score with diminishing returns formula (sorted descending, 0.7× decay per rule)
- Score-to-decision thresholds: APPROVE < 50 · REVIEW ≥ 50 · ESCALATE ≥ 75

### Compliance Dashboard
- **Transaction detail page** built to compliance-officer standard
  - Priority chip (Critical / High-Priority / Review-Level / Monitor-Level / Cleared)
  - Audit-ready Decision Statement
  - "Why was this flagged?" plain-English narrative (score-tiered, no score/language conflicts)
  - Regulatory Context block (SAR, CTR, Structuring, High-Risk Jurisdiction — each with WHY)
  - Confidence signal with indicator count description
  - "Why not escalated" block with actual amounts, thresholds, and FFIEC citations
  - SLA-stamped Recommended Action (4h / 24h / 72h / Ongoing / Routine)
  - Technical Breakdown (collapsed by default)
- Transaction monitoring list with filtering, flagged-only view, CSV export
- Alert queue, case management, audit log export

### Tested to Compliance-Grade Standards
- **96 passing unit tests** covering every critical edge case
- Wire transfer at $52,000 → does NOT trigger CTR (correct: non-cash type)
- AML004 FATF High-Risk alone → sets `is_sar_candidate` via critical severity
- AML005 CTR alone → does NOT set `is_sar_candidate`
- Structuring range `[9500, 10000)` — $10,000.00 excluded (→ CTR)
- Velocity: BOTH `spike_ratio ≥ 5` AND `day_count ≥ 8` required (AND, not OR)
- Determinism test: same input produces identical composite score across runs

---

## Tech Stack

| Layer | Technology |
|---|---|
| API | FastAPI (Python 3.11), SQLAlchemy async, Alembic |
| Rule Engine | Pure Python, no LLM in the decision path |
| ML / Fraud | Scikit-learn ensemble (Isolation Forest + XGBoost) |
| Frontend | Next.js 14, TypeScript, Tailwind CSS, React Query |
| Database | PostgreSQL 16 |
| Cache / Queue | Redis, Celery |
| Observability | Prometheus + Grafana dashboards |
| Infrastructure | Docker Compose (dev), Kubernetes manifests (prod), Nginx |

---

## Regulatory Coverage

| Regulation | Rule | Status |
|---|---|---|
| 31 CFR 1010.311 | Currency Transaction Report (CTR) | Implemented |
| 31 CFR 1020.320 | Suspicious Activity Report (SAR) | Implemented |
| FinCEN CDD Rule | Customer Due Diligence | Implemented |
| FATF High-Risk | High-risk & Non-Cooperative Jurisdictions | Implemented |
| FFIEC BSA/AML | Velocity & Structuring patterns | Implemented |
| OFAC SDN | Sanctions watchlist matching | Implemented |

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────┐
│                    API Gateway (Nginx)               │
└──────────────────────┬──────────────────────────────┘
                       │
          ┌────────────▼────────────┐
          │     FastAPI Backend      │
          │  ┌───────────────────┐  │
          │  │  AML Rule Engine  │  │  ← Deterministic (no LLM)
          │  │  (10 active rules)│  │
          │  └────────┬──────────┘  │
          │  ┌────────▼──────────┐  │
          │  │  ML Fraud Engine  │  │  ← Ensemble model
          │  └────────┬──────────┘  │
          │  ┌────────▼──────────┐  │
          │  │  Risk Aggregator  │  │  ← Weighted ensemble
          │  └───────────────────┘  │
          └────────────┬────────────┘
                       │
          ┌────────────▼────────────┐
          │     PostgreSQL 16        │
          └─────────────────────────┘
```

---

## Who is this for?

- **Banks and credit unions** that need BSA/AML automation
- **FinTech companies** building compliance into their payment rails
- **MSBs (Money Service Businesses)** subject to FinCEN reporting
- **Compliance consultancies** looking to deploy a branded platform

---

## Request a Demo

**[→ Open a Demo Request](https://github.com/naimlabd/fincrime-aml-platform-showcase/issues/new?template=demo-request.yml)**

Fill in the form above and we'll reach out within 1–2 business days to schedule a live walkthrough.

Prefer email? Reach out directly: **Naim Al Abd** — alabdnaim@gmail.com

---

## Frequently Asked Questions

**Is the source code available?**
The codebase is proprietary and maintained in a private repository. Enterprise licensing and source code escrow arrangements are available on request.

**What deployment options are there?**
Docker Compose for single-server deployments, Kubernetes manifests for cloud-native deployments (AWS EKS, GCP GKE, Azure AKS). Full deployment guide available to licensed customers.

**Is this production-ready?**
The platform is actively used and tested. All regulatory rule outputs are deterministic and auditable. A full test suite (96 tests) covers every critical compliance edge case.

**Can rules be customized per organization?**
Yes. The rule engine supports per-organization configuration — thresholds, disabled rules, and custom rule weights — without code changes.

---

*This showcase repository contains no source code. All intellectual property is maintained privately.*

---

**Built by [Naim Al Abd](https://github.com/naimlabd)**
