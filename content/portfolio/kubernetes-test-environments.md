---
title: "On-Demand Kubernetes Test Environments"
date: 2023-04-01
featured: true
tech: ["Kubernetes", "AWS EKS", "Helm", "Docker", "Python"]
tier: "work"
weight: 1
impact: "Teams can bring up the application and Snowflake-backed analytics in about four hours. Peak usage reached 50–60 concurrent environments."
---

## Problem

Teams relied on shared staging for full-system validation. QA data derived from production changed over time, making campaign and analytics regressions hard to reproduce.

## First iteration as Staff

I led the cross-team design and contributed changes in service repositories. We used Kustomize, scripts, and Python-generated manifests to create environments on AWS EKS on demand and tear them down automatically. The first iteration grouped each environment into a namespace.

## Second iteration as Principal

I led the move to Helm and Harness Continuous Delivery. Service teams adopted Helm charts and the later rollout used per-service namespaces. Engineers could select services, versions, and synthetic datasets through standard or custom Harness inputs.

The test flow began with synthetic campaigns and delivery scenarios generated through Rails application code. Rails and PostgreSQL held application state, Kafka carried events to Snowflake, and a dedicated analytics service served results back to the Rails UI.

## Data tradeoff

We regenerated data for each environment so teams could reproduce known scenarios without depending on client data. That added startup time. Cross-database snapshots were brittle because changes on a single main branch could break Snowflake loading, and we lacked an effective gate to catch those changes early.

## Outcome

An environment with the application and Snowflake-backed analytics took about four hours to provision. Usage reached 50–60 concurrent environments at peak, giving teams a way to validate changes earlier and in parallel.
