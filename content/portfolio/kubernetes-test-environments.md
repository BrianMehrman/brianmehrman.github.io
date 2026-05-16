---
title: "On-Demand Kubernetes Test Environments"
date: 2023-04-01
featured: true
impact: "Reduced integration test environment setup from days-to-weeks to 3-4 hours; scaled to 50-60 concurrent environments for a 30-engineer org."
tech: ["Kubernetes", "AWS EKS", "Helm", "Docker", "Python"]
tier: "work"
---

## The Problem

Basis Technologies ran shared long-lived integration environments. Engineers queued up, stepped on each other's changes, and environment contention was a constant source of delays. Setting up a fresh environment could take days — sometimes weeks if infrastructure issues arose.

## The Solution

I designed and led the build of an on-demand full-stack environment system on AWS EKS. Each environment runs the full platform stack: application services, data warehouse, event streaming, and persistent storage.

Key design decisions:
- **Namespace-per-environment** — complete isolation at the Kubernetes layer, no cross-contamination
- **Pipeline-triggered provisioning** — engineers request an environment through the CI/CD pipeline, not through manual ops work
- **Automated teardown** — environments are destroyed after use, keeping costs predictable

## Results

- Setup time: **days-to-weeks → 3-4 hours**
- Concurrent environments at peak: **50-60**
- Shared long-lived environments: **fully retired**
- Environment contention incidents: **eliminated**

## Scope

This was a multi-quarter initiative. I handled architecture, implementation, documentation, and rollout — including two company-wide bootcamp sessions (Apr 2023, Dec 2024) to onboard the 30-engineer organization.
