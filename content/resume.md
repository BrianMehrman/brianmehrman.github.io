---
title: "Resume"
layout: "resume"
url: "/resume/"
summary: "Resume — Brian Mehrman, Principal Software Engineer"
---
<div class="resume-header">
<p><a href="https://github.com/brianmehrman">GitHub</a> · <a href="https://linkedin.com/in/brian-mehrman-208a1a1a">LinkedIn</a> · Greater Chicago Area · <a href="/documents/Brian_Mehrman_Resume.docx">Download Word resume</a></p>
</div>

---

## Summary

Principal Software Engineer with 15+ years across backend product development, data modeling, platform architecture, and developer tooling. Hands-on experience with an advertising product catalog and the campaign workflows it supports, followed by cross-team leadership on synthetic data, on-demand Kubernetes environments, CI/CD standards, and observability. Set technical direction for release validation across a 30-engineer organization and use AI tools in day-to-day implementation, investigation, code review, and documentation.

## Selected skills

**Architecture and data:** Service integration, API design, relational data modeling, distributed data flows, PostgreSQL, Kafka, Snowflake, Ruby on Rails

**Platform and delivery:** AWS EKS, Kubernetes, Helm, Docker, self-service test environments, CI/CD standards, Harness, Datadog

**Languages and AI:** Ruby, Python, JavaScript, TypeScript, SQL; Claude Code, AI-assisted engineering workflows

## Experience

### Basis Technologies (formerly Centro) — Chicago, IL

**Principal Software Engineer | Apr 2023–Present**

- Set technical direction for release validation and platform tooling across an approximately 30-engineer organization, working with product and service teams to make full-system testing available before release.
- Led the second iteration of on-demand EKS environments, replacing Kustomize and Python-generated manifests with Helm-based deployment through a self-service continuous delivery workflow. Teams can choose service versions, add services, and select synthetic datasets.
- Enabled test environments with the application, supporting services, and Snowflake-backed analytics; environments are ready in about four hours and supported 50–60 concurrent instances at peak.
- Authored a shared CI/CD pipeline standard and created a service-version repository used by delivery pipelines and end-to-end environment configuration. Led migration from the legacy environment pipeline, retired in January 2025.
- Lead observability, incident response, postmortems, and follow-up fixes. Use AI tools to investigate failures, review code, plan changes, and maintain documentation; created Datadog and deployment-tool investigation skills that colleagues use.

**Staff Software Engineer | Oct 2020–Apr 2023**

- Led the on-demand end-to-end test environment initiative across teams, defining how services, synthetic data, and analytics fit together and contributing integration changes in service-owning repositories.
- Drove the first EKS environment iteration with the team, using Kubernetes, Kustomize, scripts, and Python-generated manifests; on-demand creation and automated teardown were available by the end of the Staff role.
- Helped build a repeatable synthetic-data generator using application code paths and configurable JSON templates to create campaign, entity, and delivery scenarios without depending on client data.
- Worked with the team to extract the analytics backend from the Rails monolith into a dedicated service.

**Lead Software Engineer | May 2019–Oct 2020**

- Led team implementation of the Campaign Performance Dashboard, concentrating personally on backend data and testing. The dashboard remains in use and has grown with campaign and line-item views.
- Built reusable Rails aggregation logic to surface delivery metrics at campaign, proposal, line-item, and optional flight or group/tactic levels; identified the need for repeatable test data as regression tests struggled with changing production-derived records.

**Senior Software Engineer / Software Engineer | Jul 2015–May 2019**

- Worked on the ad inventory product catalog's vendor, property, placement, and contact relationships. Primarily contributed backend/database design and data access logic for property contacts, with API-to-UI integration and cross-stack debugging.
- Built backend support for bulk campaign editing and refactored the Campaign Overview endpoint into view-specific APIs for progressive rendering.

### Tukaiz, LLC — Franklin Park, IL

**Software Development Manager / Application Development Specialist | Dec 2011–Jul 2015**

- Built backend applications and developer workflows, including a multi-tenant CMS, a document delivery system, and onboarding documentation.

### Volition / THQ — Champaign, IL

**Technical Artist | Jun 2007–Jan 2011**

- Built Python-based tools for game content creation and rapid iteration.

## Selected projects

**Diagram Builder | 2026** — Open-source repository visualization platform using TypeScript, React, Three.js, and Neo4j, built with agent-assisted development workflows.

## Education

**Savannah College of Art and Design** — BFA, Interactive Design and Game Development | 2002–2006

**Code Academy (now the Starter League)** — Ruby on Rails Development | 2011
