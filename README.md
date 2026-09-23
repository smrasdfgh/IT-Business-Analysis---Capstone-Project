# IT-Business-Analysis--Capstone-Project

## Please note that this project was developed as part of a diploma defense, and the documents provided are intended solely as examples to demonstrate general knowledge and skills.


# Techleaf

**Your path to better financial choices.**

Techleaf is a digital platform that helps users discover, compare, and apply for the financial products that fit them best — loans, deposits, bank cards, P2P lending, and insurance — using AI-driven matching, real-time data aggregation, and a gamified user experience.

---

## Table of Contents

- [Overview](#overview)
- [Vision & Mission](#vision--mission)
- [Key Features](#key-features)
- [Business Model](#business-model)
- [System Architecture](#system-architecture)
- [Core User Flow](#core-user-flow)
- [Tech Stack](#tech-stack)
- [API Overview](#api-overview)
- [Non-Functional & System Requirements](#non-functional--system-requirements)
- [Database](#database)
- [Infrastructure & DevOps](#infrastructure--devops)
- [Security](#security)
- [Project Documentation](#project-documentation)
- [Roadmap](#roadmap)

---

## Overview

The financial services market in Azerbaijan is growing, but product discovery is still largely manual and fragmented across individual bank websites and platforms like Hangikredi.com and Banks.az. **Techleaf** centralizes this discovery process into a single, data-driven platform where users can:

- Search and filter loans, deposits, cards, P2P offers, and insurance in one place
- Get AI-powered product recommendations tailored to their financial profile
- Compare offers from multiple banks and institutions side-by-side
- Apply directly and track application status in real time
- Save favorite offers and earn rewards through a gamified points system

Techleaf's edge over competitors comes from **AI matching, real-time data scraping, and a gamified UX** — without depending on direct bank API integrations.

## Vision & Mission

| | |
|---|---|
| **Vision** | Shape the future of financial decision-making by connecting 10,000+ users with tailored financial solutions, forming partnerships with 15+ institutions, achieving 80% AI match accuracy, and securing ₼60,000 in early-stage funding. |
| **Mission** | Deliver a personalized, data-driven experience that empowers people to discover, compare, and connect with the most relevant financial products through AI, real-time data, and trusted partnerships. |

**Current stage:** MVP, operating on a lean development model, seeking initial funding and strategic bank partnerships.

## Key Features

- 🔍 **Smart Search & Filtering** — filter financial products by term, amount, currency, interest rate, and category
- 🤖 **AI-Powered Recommendations** — personalized product matching based on user profile and behavior
- ⚖️ **Side-by-Side Comparison** — compare multiple offers on key metrics at once
- ⭐ **Favorites** — save offers to revisit and compare later
- 📝 **End-to-End Application Flow** — apply for a product, get routed to bank specialists, and track status (approved / pending / rejected)
- 🔔 **Real-Time Notifications** — updates on application status and new matching offers
- 🎮 **Gamification** — earn points by watching platform content, redeemable for promo codes, subscriptions, and discounts
- 🏦 **Bank Admin Panel** — dedicated interface for partner institutions to review, respond to, and manage incoming applications
- 🌐 **Web Scraping Engine** — automatically aggregates and normalizes product data from partner and public sources
- 🌍 **Multilingual Support** — Azerbaijani, Russian, and English

## Business Model

| Stream | Model | Rate |
|---|---|---|
| Premium Subscriptions (B2C) | Monthly / Annual | ₼4.99/mo · ₼50.90/yr (save 15%) |
| Referral Fee (B2B) | Per referred user | ₼10 / user |
| Advertising (B2B) | Monthly placement | ₼350 / month |
| Data-Driven Insights (B2B) | Per user data point | ₼1 / user |

Financial projections and monthly expense/revenue breakdowns (development, hosting, marketing, and ROI over the first two years post-MVP) are maintained in the project's ROI model.

## System Architecture

Techleaf is designed as a **microservices architecture** with independently deployable, containerized services:

- **User Interface Service** — web/mobile front end
- **Savings / Product Service** — manages product catalog and filter logic
- **Bank Integration Service** — handles offer aggregation and bank communication
- **Favorites Service** — stores user-saved offers
- **Application Service** — orchestrates the loan/product application lifecycle
- **Admin Panel Service** — internal tool for bank specialists and Techleaf admins
- **Notification Service** — push/email notifications to users

Services communicate over **REST APIs** (with WebSockets for real-time events), are deployed via **Docker + Kubernetes**, sit behind an **API Gateway / NGINX reverse proxy**, and are observed through a **Prometheus + Grafana + ELK/Loki** stack.

## Core User Flow

The primary savings/loan product journey (see `sequence_diagram.jpg`):

1. User opens the products section and requests filter options (term, amount, currency)
2. User submits filters → Product Service fetches matching offers from the Bank Integration Service
3. Matched offers are displayed; the user can save an offer to Favorites or click **"Apply Now"**
4. The Application Service creates the application and forwards it to the Admin Panel
5. A bank specialist reviews the application and responds with an offer/conditions
6. The user is notified and shown the offer details, then either:
   - **Accepts** → the bank is informed, the offer is finalized, and acceptance is acknowledged, or
   - **Rejects** → the system checks other bank responses / forwards the request to additional banks and presents any new offers

Supporting flows (see `diagram_1.bpmn` and `techleaf.drawio`) cover phone/OTP verification, AI-assisted loan qualification, the AI recommendation questionnaire, and the full use-case model (Register, Login, Search, Compare, Apply, Track Status, Manage Users, etc.) across **User**, **Financial Institution**, **Admin**, and **AI** actors.

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3.11+, FastAPI (async, REST-native microservices) |
| Frontend | React 18+, TypeScript, Tailwind CSS |
| Admin Panel | Next.js 13+ with ShadCN UI, SSR |
| Relational DB | PostgreSQL 15+ |
| Document/NoSQL DB | MongoDB Atlas (M10+) |
| Cache / Sessions | Redis 7+ |
| Queue / Background Jobs | Celery + RabbitMQ |
| Web Scraping | Playwright (Python), headless Chromium, proxy rotation |
| AI / ML | scikit-learn, LightGBM (deployed as APIs) |
| Containers / Orchestration | Docker 24+, Kubernetes 1.27+, Helm 3+ |
| API Docs | OpenAPI 3.1, Swagger UI / Redoc |
| Monitoring | Prometheus, Grafana, ELK / Loki |
| CI/CD | GitHub Actions / GitLab CI / Jenkins, GitFlow branching |
| Cloud | AWS or GCP (VPC, auto-scaling, multi-AZ) |

## API Overview

- **Base URL (Production):** `https://api.techleaf.az/v1/`
- **Base URL (Sandbox):** `https://sandbox.techleaf.az/v1/`
- **Auth:** Bearer Token (OAuth 2.0 / JWT), tokens signed with HMAC SHA-256, expiring within 60 minutes
- **Format:** JSON only, `snake_case` keys, standard HTTP status codes and structured error responses (`error_code`, `message`)

Main endpoint groups:

| Group | Examples |
|---|---|
| Authentication | `POST /api/auth/login`, `POST /api/auth/logout` |
| User Management | `GET /api/users`, `DELETE /api/users/{id}` |
| Product Requests | `GET /api/requests`, `GET /api/requests/{id}`, `PUT /api/requests/{id}`, `DELETE /api/requests/{id}` |
| Product Listing & Details | `GET /api/products` |
| Offer Management, Consent, Reviews & Ratings, Insights | see full API documentation |

Rate limits are enforced per role — e.g. 100 req/min for authenticated users, 500 req/min for partner institutions, 10 login attempts per 5 minutes.

## Non-Functional & System Requirements

- **Availability:** 99.9% monthly uptime SLA
- **Cross-platform:** Chrome, Firefox, Safari, Edge; responsive from 320px–2560px; iOS 13+ / Android 9+
- **Accessibility:** WCAG 2.1 AA compliant
- **Deployment:** Microservices, zero-downtime rolling / blue-green releases, feature flags, health/readiness endpoints
- **Compliance:** GDPR, ISO 27001, PCI DSS (for payment data), local Azerbaijan data protection law
- **Localization:** Azerbaijani, Russian, English

## Database

- **PostgreSQL 15+** for structured, transactional data (ACID-compliant, 3NF normalized, foreign-key enforced)
- **MongoDB** for unstructured data (scraped content, logs, AI model artifacts)
- Multi-tenant schema, sharding and partitioning for scale, materialized views for reporting
- **Backups:** automated every 6 hours, 30-day retention, encrypted (AES-256), geographically redundant, point-in-time recovery
- **High availability:** master-slave replication, automatic failover (≤30s), read replicas for reporting

## Infrastructure & DevOps

- Containerized with **Docker**, orchestrated with **Kubernetes** across Dev, Test, and Production environments
- **NGINX / ELB** for load balancing and reverse proxying
- Auto-scaling based on CPU/memory thresholds, multi-AZ high availability
- **CI/CD:** lint → unit/integration tests → security scan → build → deploy, with GitFlow branching and semantic versioning
- **Observability:** Prometheus + Grafana dashboards, centralized ELK logging, alerting on anomalies

## Security

- Bcrypt password hashing (12+ salt rounds), JWT access + HttpOnly refresh token model
- Two-Factor Authentication (TOTP) for users and mandatory for admins
- Role-Based Access Control (RBAC) with Viewer / Editor / Approver / Auditor roles
- CSRF protection, rate-limited login, hardened security headers, HTTPS/TLS 1.2+ everywhere
- Tokenized payment data (PCI-DSS scope reduction) — no raw card data stored
- Immutable, hash-verified audit logs for compliance and forensic traceability

## Project Documentation

This repository's `/docs` (or attached files) includes:

- `Techleaf_Technical_Documentation.docx` — full technical specification (functional/non-functional requirements, API docs, system & database requirements, infrastructure)
- `sequence_diagram.jpg` — savings product application sequence diagram
- `diagram_1.bpmn` — loan application business process flow
- `techleaf.drawio` — system use-case diagram
- `ROI_calculation.xlsx` — expense, revenue, and ROI projections
- `Techleaf.pptx` — pitch deck (vision, personas, business model, go-to-market, risk analysis)

## Roadmap

- Reach 10,000+ active users and integrate with 15+ financial institutions by late 2025
- Expand from web to native mobile apps
- Regional expansion beyond Azerbaijan
- Deeper AI personalization and conversational/chatbot assistance
- Premium tiers with dedicated financial advisory support
