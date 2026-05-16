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

Principal Software Engineer focused on platform infrastructure. I design and ship the systems that let engineering teams move fast. Most recently, I built an on-demand Kubernetes test environment system that cut setup from days or weeks down to 3-4 hours and scaled to 50-60 concurrent environments for a 30-engineer organization, replacing shared long-lived integration environments. I also authored the company-wide CI/CD pipeline standard adopted across engineering. 15+ years across platform engineering, backend architecture, and developer tooling — with active practice in AI/LLM agent workflows.

---

## Experience

### Basis Technologies — Chicago, IL

*Formerly Centro; rebranded October 2021.*

**Principal Software Engineer** | Apr 2023–Present

Architect and own platform infrastructure for the engineering organization (~30 engineers across product and platform teams). Serve as Developer Liaison coordinating technical dependencies between product and platform groups.

- Designed and led an on-demand full-stack integration environment system on AWS EKS — reduced test environment setup from days or weeks to 3-4 hours; scaled to 50-60 concurrent environments, replacing shared long-lived integration environments
- Authored the company-wide CI/CD pipeline standard: 14-stage pipeline, Docker multi-stage builds, semantic versioning, three deployment modes (locked standard, configurable, fully custom)
- Built long-lived Sales demo environment on Kubernetes with daily synthetic delivery data generation, weekly automated rebuild from snapshot, and on-demand org/user provisioning via pipeline
- Managed the full transition from the legacy environment pipeline to the new E2E pipeline suite — legacy pipeline retired Jan 2025
- Designed namespace-per-service Kubernetes architecture for multi-application environments
- Led two company-wide bootcamp sessions on Kubernetes environment usage (Apr 2023, Dec 2024)

*Technologies: AWS EKS, Kubernetes, Helm, Docker, Kafka, Snowflake, Datadog, Python, Ruby on Rails*

**Staff Software Engineer** | Oct 2021–Apr 2023

- Led migration of all data pipeline workloads to Kubernetes (5+ pipeline types: data ingestion, dimensional modeling, batch aggregation, real-time event processing, Hadoop jobs)
- Drove company-wide CI/CD platform adoption — built first standardized pipelines for core API and analytics services
- Designed and built shared integration environment system with cloud data warehouse, event streaming, and persistent storage setup
- Completed Hadoop NameNode High Availability migration on Kubernetes
- Added M1 developer machine support

*Technologies: AWS EKS, Kubernetes, Helm, Docker, Hadoop, Kafka, Snowflake, Python, Ruby*

---

### Centro — Chicago, IL

**Staff Software Engineer** | Oct 2020–Oct 2021

- Migrated analytics data backend from monolith to dedicated analytics microservice
- Built Kubernetes-based local development environment tooling adopted across engineering
- Set up the initial Kubernetes environment for the core platform application
- Built Dockerized synthetic delivery data generation service for integration testing
- Authored technical evaluation for real-time campaign collaboration: WebSocket vs. polling analysis

*Technologies: AWS, Kubernetes, Docker, Python, Ruby on Rails, Kafka, PostgreSQL, Redis*

**Lead Software Engineer** | May 2019–Oct 2020

- Led a three-milestone campaign performance dashboard initiative — replaced fragmented tables with actionable charts (pacing %, KPI %, agency margin %); reduced time to identify at-risk campaigns
- Designed real-time campaign collaboration system with WebSocket vs. polling evaluation
- Led product analytics GDPR compliance integration
- Owned engineering interview process and take-home exercises for QA and frontend roles

*Technologies: AWS, Ruby on Rails, Redis, PostgreSQL, JavaScript, React, Docker*

**Senior Software Engineer** | Aug 2017–May 2019

- Designed and implemented backend for a queued bulk-edit system — async Sidekiq Batch processing with live progress polling, per-item receipts, and concurrency-limited job execution
- Refactored and migrated two overlapping data models into one unified model
- Implemented data rollback for database deadlock scenarios in an optimistic locking application
- Mentored engineers on Ruby and Rails architecture and design patterns

*Technologies: AWS, Ruby on Rails, Redis, PostgreSQL, React, Node.js, Docker, Java*

**Software Engineer** | Jul 2015–Aug 2017

- Refactored monolithic Campaign Overview endpoint into four parallel view-specific endpoints, enabling progressive page rendering
- Built a data generation utility for manual testing and a manual upload feature that ingests spreadsheet data into the interactive dashboard

*Technologies: AWS, Ruby on Rails, Redis, PostgreSQL, React, Node.js, Docker*

---

### Tukaiz, LLC — Franklin Park, IL

**Software Development Manager** | May 2014–Jul 2015

- Developed multi-tenant application using LocomotiveCMS with back-end services and third-party email marketing integration
- Configured AWS infrastructure defenses against DOS attacks
- Established documentation culture that shortened developer onboarding

*Technologies: AWS, Ruby on Rails, PHP, MySQL, Redis, MongoDB, PostgreSQL*

**Application Development Specialist** | Dec 2011–May 2014

- Designed and developed a document storage system for large file uploads delivered via CDN
- Created signage placement guide generator (SVG-to-interactive-guide)

*Technologies: Ruby on Rails, PHP, MySQL, PostgreSQL, JavaScript*

---

### Volition / THQ — Champaign, IL

**Technical Artist** | Jun 2007–Jan 2011

Pipeline and tooling work for AAA game development. Game credits: *Saints Row 2*, *Red Faction: Guerrilla*.

- Built Python-based XML and light editors with live in-game feedback
- Designed camera-based parallax shader solution for in-game windows
- Developed workflow for rapid city layout prototyping using grammar-based generation software

*Technologies: Python, MaxScript, C++, C#, HLSL*

---

## Projects

**Diagram Builder** | 2026 | TypeScript, Node, React, Three.js, Neo4j

Open-source code visualization platform — parses repositories into a dependency graph, renders 2D and 3D layouts (force-directed, radial-BFS), supports semantic tiered views, and exports to multiple formats. Built with agent-driven development workflows using Claude Code and the Anthropic SDK.

---

## Skills

| Area | Technologies |
|---|---|
| **Languages** | Ruby, JavaScript, TypeScript, Python, Java, SQL |
| **Frameworks** | Ruby on Rails, Node.js, React, Sinatra |
| **Infrastructure** | AWS EKS, Kubernetes, Helm, Docker, Kafka, Snowflake, Redis, PostgreSQL, MySQL, Datadog |
| **CI/CD** | Pipeline design and standards, Docker multi-stage builds, semantic versioning, Harness |
| **AI/LLM** | Claude Agent SDK, Anthropic API, Claude Code, agent workflow design |
| **Practices** | Platform architecture, distributed systems, developer tooling, DX, observability, technical mentorship |

---

## Education

**Savannah College of Art and Design** — Savannah, GA
Bachelor of Fine Arts, Interactive Design and Game Development | 2002–2006

**Code Academy** (now the Starter League) — Chicago, IL
Ruby on Rails Development | 2011
