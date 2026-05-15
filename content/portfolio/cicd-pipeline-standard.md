---
title: "Company-Wide CI/CD Pipeline Standard"
date: 2022-06-01
featured: true
impact: "Authored the CI/CD pipeline standard adopted across all of Basis Technologies engineering — 14 stages, three deployment modes, used by every service."
tech: ["Docker", "Kubernetes", "Harness", "Python", "Ruby"]
tier: "work"
---

## The Problem

Engineering teams at Basis were building and maintaining their own CI/CD pipelines independently. There was no standard. Pipelines varied in quality, lacked consistent artifact management, and created ongoing maintenance burden for each team.

## The Solution

I designed and authored the company-wide CI/CD pipeline standard — a 14-stage pipeline that became the default for all engineering services.

**The 14-stage pipeline includes:**

1. Source checkout
2. Dependency installation
3. Static analysis / linting
4. Unit tests
5. Integration test trigger
6. Docker multi-stage build
7. Image tagging (semantic versioning)
8. Security scanning
9. Artifact registry push
10. Staging deploy
11. Smoke tests
12. Production deploy gate
13. Production deploy
14. Post-deploy verification

**Three deployment modes:**

- **Locked standard** — teams inherit the full pipeline with no changes; zero maintenance overhead
- **Configurable** — teams can toggle stages on/off via config; still managed centrally
- **Fully custom** — teams own their pipeline; opt-in for services with unusual requirements

## Results

- Adopted across all engineering services
- Eliminated per-team pipeline maintenance for teams on locked standard
- Semantic versioning standardized across all artifacts
- Docker multi-stage builds reduced image sizes and improved security posture
- Legacy pipeline suite fully retired January 2025
