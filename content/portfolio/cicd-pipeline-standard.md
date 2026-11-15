---
title: "Company-Wide CI/CD Pipeline Standard"
featured: true
tech: ["Docker", "Kubernetes", "Harness", "Python", "Ruby"]
tier: "work"
weight: 2
impact: "Authored a shared CI/CD standard and led the migration from the legacy environment pipeline."
---

## Shared delivery workflow

Service teams needed a consistent way to build and version artifacts and deploy them into test environments. I authored the shared CI/CD standard and created a service-version repository that delivery pipelines and end-to-end environment configuration could both use.

## Rollout and flexibility

The standard gave teams a common path for building and versioning services. Service owners maintained their Helm charts and deployment definitions, and engineers could choose the services and versions they needed for a particular environment.

I led the migration while teams balanced the rollout with product work. Service teams adopted the shared workflow incrementally, and the legacy environment pipeline was retired in January 2025.

See how the delivery workflow supported the [on-demand Kubernetes test environments](/portfolio/kubernetes-test-environments/).
