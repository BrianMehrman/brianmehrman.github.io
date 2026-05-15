---
title: "Diagram Builder"
date: 2026-01-01
featured: true
impact: "Open-source code visualization platform that parses repositories into dependency graphs with 2D/3D rendering."
tech: ["TypeScript", "Node.js", "React", "Three.js", "Neo4j"]
link: "https://github.com/brianmehrman"
tier: "open-source"
---

## Overview

Diagram Builder is an open-source code visualization platform built to make large codebases navigable. It parses repositories into a dependency graph, renders them in both 2D and 3D layouts, and exports to multiple formats.

## What It Does

- **Repository parsing** — scans source files and builds a typed dependency graph of modules, classes, and functions
- **2D and 3D layouts** — force-directed and radial-BFS algorithms render the graph at different levels of abstraction
- **Semantic tiered views** — zoom from architecture-level down to individual module dependencies
- **Export** — outputs to JSON, SVG, and image formats for documentation and diagramming tools

## How It Was Built

Built using an agent-driven development workflow with Claude Code and the Anthropic SDK. Most of the core graph logic and Three.js rendering pipeline was developed through iterative agent sessions, with human oversight on architecture decisions and API design.

## Tech Stack

- **TypeScript + Node.js** — parser and graph engine
- **React** — UI and controls
- **Three.js** — 3D graph rendering
- **Neo4j** — graph database for persistent storage and complex queries
