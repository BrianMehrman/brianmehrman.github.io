---
title: "Resume"
layout: "resume"
url: "/resume/"
summary: "Resume — Brian Mehrman, Principal Software Engineer"
---
<div class="resume-header">
<p><a href="https://github.com/brianmehrman">GitHub</a> · <a href="https://linkedin.com/in/brian-mehrman-208a1a1a">LinkedIn</a> · Greater Chicago Area · <a href="/documents/Brian_Mehrman_Resume.docx">Resume ⬇</a></p>
</div>

---

## Summary

Platform engineers are invisible when things work. After 15+ years building CI/CD pipelines, Kubernetes infrastructure, and developer tooling, I've learned that the real work isn't the technical design — it's getting +30 engineers to trust a shared standard, and keeping it running when they push it in ways you didn't anticipate. I focus on the systems that let teams ship confidently on a Friday. Recent work: on-demand Kubernetes environments and company-wide CI/CD standards. Currently exploring how AI/LLM agent workflows change what "developer tooling" even means.

---

## Experience

### Basis Technologies (formerly Centro) — Chicago, IL

**Principal Software Engineer** | Apr 2023–Present

Architected and owned platform infrastructure for a +30-engineer organization; Developer Liaison across product and platform teams.

- Built on-demand integration environments on AWS EKS — cut setup from days/weeks to hours, scaled to 50–60 concurrent instances
- Authored the team-wide CI/CD standard — multi-stage pipeline, multi-stage Docker builds, semantic versioning, multiple deployment modes
- Built a long-lived Sales demo environment on Kubernetes — daily synthetic data, weekly automated rebuilds, on-demand org/user provisioning
- Pushed a multi-team CI/CD migration across the finish line despite consistent competition from product work — legacy pipeline retired Jan 2025
- Made enablement part of major pipeline overhauls — running Kubernetes bootcamps to bring engineering teams along each time the platform shifted

*AWS EKS, Kubernetes, Helm, Docker, Kafka, Snowflake, Datadog, Python, Ruby, JavaScript, Java, Go, Bazel *

**Staff Software Engineer** | Oct 2021–Apr 2023

- Migrated all data pipeline workloads to Kubernetes across 5+ workload types (ingestion, batch, streaming, Hadoop)
- Drove engineering-wide CI/CD adoption — built the first standardized pipelines for core API and analytics services
- Built a shared integration environment system (cloud data warehouse, event streaming, persistent storage)
- Completed a Hadoop NameNode HA migration on Kubernetes

*AWS EKS, Kubernetes, Helm, Docker, Hadoop, Kafka, Snowflake, Python, Ruby*

**Staff Software Engineer** | Oct 2020–Oct 2021

- Migrated the analytics data backend from monolith to a dedicated microservice
- Built Kubernetes local-dev tooling adopted across engineering; set up the initial K8s environment for the core platform app
- Built a Dockerized synthetic data generation service for integration testing

**Lead Software Engineer** | May 2019–Oct 2020

- Led a campaign-performance dashboard initiative — replaced fragmented tables with actionable charts (pacing, KPI, agency margin), cutting time to spot at-risk campaigns
- Designed and shipped a real-time campaign collaboration system, enabling teams to create and manage ad campaigns together
- Led product-analytics GDPR compliance integration, including owning the engineering hiring process for QA and frontend roles

**Senior / Software Engineer** | Jul 2015–May 2019

- Built the backend for a bulk-edit system that let users make large-scale changes across campaigns simultaneously
- Resolved a deadlock issue where concurrent campaign edits could leave two users stuck — unified the overlapping data models and added rollback handling
- Refactored the monolithic Campaign Overview endpoint into parallel view-specific endpoints for progressive rendering
- Provided technical mentorship to engineers — code review, design guidance, and teaching Ruby/Rails concepts

---

### Tukaiz, LLC — Franklin Park, IL

**Software Development Manager / Application Development Specialist** | Dec 2011–Jul 2015

- Built a multi-tenant LocomotiveCMS application with email-marketing integration
- Diagnosed and mitigated an active DOS attack on AWS infrastructure while keeping the application running
- Designed a CDN-delivered document storage system for large uploads and documented the client onboarding process around it

---

### Volition / THQ — Champaign, IL

**Technical Artist** | Jun 2007–Jan 2011

Pipeline and tooling for AAA games (*Saints Row 2*, *Red Faction: Guerrilla*) — Python/MaxScript editors with live in-game feedback and a camera-based parallax shader solution.

---

## Projects

**Diagram Builder** | 2026 | TypeScript, Node, React, Three.js, Neo4j

Open-source code visualization platform — parses repos into a dependency graph, renders 2D/3D layouts (force-directed, radial-BFS), and exports to multiple formats. Built with agent-driven workflows using Claude Code and the Anthropic SDK.

**Observability Sandbox** | 2026 | Ruby, SolidQueue, Postgres, Grafana, OpenTelemetry

Sandbox application demoing observability of an LLM Chatbot. Uses Grafana to show logs and traces of application locally for learning to add observability to your application.

---

## Skills

| Area               | Technologies                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------------ |
| **Languages**      | Ruby, Python, JavaScript, TypeScript, Java, GO, Rust, SQL                                              |
| **Frameworks**     | Ruby on Rails, Node.js, React, Spring                                                                  |
| **Infrastructure** | AWS EKS, Kubernetes, Helm, Docker, Kafka, Snowflake, Redis, PostgreSQL, MySQL, Datadog                 |
| **CI/CD**          | Pipeline design and standards, Docker multi-stage builds, semantic versioning, Harness.io, GitHub Actions, Jenkins, OpenTofu                 |
| **AI/LLM**         | Claude Agent SDK, Anthropic API, Claude Code, Copilot, Ollama, agent workflow design                                    |
| **Practices**      | Platform architecture, distributed systems, developer tooling, DX, observability, technical mentorship |

---

## Education

**Savannah College of Art and Design** — Savannah, GA
Bachelor of Fine Arts, Interactive Design and Game Development | 2002–2006

**Code Academy** (now the Starter League) — Chicago, IL
Ruby on Rails Development | 2011