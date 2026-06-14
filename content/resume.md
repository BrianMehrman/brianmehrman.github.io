---
title: "Resume"
layout: "resume"
url: "/resume/"
summary: "Resume — Brian Mehrman, Principal Software Engineer"
---

<div class="resume-header">
<p><a href="mailto:bcmehrman@gmail.com">bcmehrman@gmail.com</a> · <a href="https://github.com/brianmehrman">GitHub</a> · <a href="https://linkedin.com/in/brianmehrman-208a1a1a">LinkedIn</a> · Greater Chicago Area</p>
</div>

---

## Summary

Principal Software Engineer focused on platform infrastructure — I build the systems that let engineering teams ship fast. 15+ years across platform engineering, backend architecture, and developer tooling, with active practice in AI/LLM agent workflows. Recent focus: on-demand Kubernetes environments and company-wide CI/CD standards.

---

## Experience

### Basis Technologies (formerly Centro) — Chicago, IL

**Principal Software Engineer** | Apr 2023–Present

Architect and own platform infrastructure for a ~30-engineer organization; Developer Liaison across product and platform teams.

- Built on-demand integration environments on AWS EKS — cut setup from days/weeks to 3–4 hrs, scaled to 50–60 concurrent
- Authored the company-wide CI/CD standard — 14-stage pipeline, multi-stage Docker builds, semantic versioning, three deployment modes
- Built a long-lived Sales demo environment on Kubernetes — daily synthetic data, weekly automated rebuilds, on-demand org/user provisioning
- Led the transition from the legacy pipeline to the E2E pipeline suite (legacy retired Jan 2025); designed namespace-per-service K8s architecture for multi-app environments
- Ran two company-wide Kubernetes bootcamps (Apr 2023, Dec 2024)

*AWS EKS, Kubernetes, Helm, Docker, Kafka, Snowflake, Datadog, Python, Ruby on Rails*

**Staff Software Engineer** | Oct 2021–Apr 2023

- Migrated all data pipeline workloads to Kubernetes across 5+ workload types (ingestion, batch, streaming, Hadoop)
- Drove company-wide CI/CD adoption — built the first standardized pipelines for core API and analytics services
- Built a shared integration environment system (cloud data warehouse, event streaming, persistent storage)
- Completed a Hadoop NameNode HA migration on Kubernetes; added M1 developer-machine support

*AWS EKS, Kubernetes, Helm, Docker, Hadoop, Kafka, Snowflake, Python, Ruby*

**Staff Software Engineer** | Oct 2020–Oct 2021

- Migrated the analytics data backend from monolith to a dedicated microservice
- Built Kubernetes local-dev tooling adopted across engineering; set up the initial K8s environment for the core platform app
- Built a Dockerized synthetic data generation service for integration testing

**Lead Software Engineer** | May 2019–Oct 2020

- Led a campaign-performance dashboard initiative — replaced fragmented tables with actionable charts (pacing, KPI, agency margin), cutting time to spot at-risk campaigns
- Designed a real-time campaign collaboration system (WebSocket vs. polling evaluation)
- Led product-analytics GDPR compliance integration; owned the engineering interview process for QA and frontend roles

**Senior / Software Engineer** | Jul 2015–May 2019

- Built the backend for a queued bulk-edit system — async Sidekiq Batch processing, live progress polling, concurrency-limited execution
- Unified two overlapping data models; implemented rollback for deadlock scenarios under optimistic locking
- Refactored the monolithic Campaign Overview endpoint into parallel view-specific endpoints for progressive rendering; mentored engineers on Ruby/Rails design

---

### Tukaiz, LLC — Franklin Park, IL

**Software Development Manager / Application Development Specialist** | Dec 2011–Jul 2015

- Built a multi-tenant LocomotiveCMS application with email-marketing integration; configured AWS DOS defenses
- Designed a CDN-delivered document storage system for large uploads; established documentation practices that shortened onboarding

---

### Volition / THQ — Champaign, IL

**Technical Artist** | Jun 2007–Jan 2011

Pipeline and tooling for AAA games (*Saints Row 2*, *Red Faction: Guerrilla*) — Python/MaxScript editors with live in-game feedback and a camera-based parallax shader solution.

---

## Projects

**Diagram Builder** | 2026 | TypeScript, Node, React, Three.js, Neo4j

Open-source code visualization platform — parses repos into a dependency graph, renders 2D/3D layouts (force-directed, radial-BFS), and exports to multiple formats. Built with agent-driven workflows using Claude Code and the Anthropic SDK.

---

## Skills

| Area               | Technologies                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------ |
| **Languages**      | Ruby, JavaScript, TypeScript, Python, Java, SQL                                                        |
| **Frameworks**     | Ruby on Rails, Node.js, React, Sinatra                                                                 |
| **Infrastructure** | AWS EKS, Kubernetes, Helm, Docker, Kafka, Snowflake, Redis, PostgreSQL, MySQL, Datadog                 |
| **CI/CD**          | Pipeline design and standards, Docker multi-stage builds, semantic versioning, Harness                 |
| **AI/LLM**         | Claude Agent SDK, Anthropic API, Claude Code, agent workflow design                                    |
| **Practices**      | Platform architecture, distributed systems, developer tooling, DX, observability, technical mentorship |

---

## Education

**Savannah College of Art and Design** — Savannah, GA
Bachelor of Fine Arts, Interactive Design and Game Development | 2002–2006

**Code Academy** (now the Starter League) — Chicago, IL
Ruby on Rails Development | 2011
