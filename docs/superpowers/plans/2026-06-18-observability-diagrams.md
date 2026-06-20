# Observability Blog Diagrams Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add four PNG diagrams to `content/blog/observability-locally-with-grafana.md` on the `feature/observability-blog-post` branch.

**Architecture:** Each diagram is a standalone HTML/SVG source file under `static/images/observability/src/`. Chrome headless exports each to a sibling PNG. The blog post references the PNGs with standard markdown image syntax plus an italic caption.

**Tech Stack:** Hugo static site (PaperMod theme), SVG/HTML diagram sources, Chrome headless for PNG export, `hugo server` for verification.

---

## File Structure

```
static/
  images/
    observability/
      src/
        request-flow.html          ← diagram A source (pre-created)
        stack-architecture.html    ← diagram B source (pre-created)
        trace-spans.html           ← diagram C source (pre-created)
        fluent-bit-pipeline.html   ← diagram D source (pre-created)
      request-flow.png             ← export (Task 1)
      stack-architecture.png       ← export (Task 2)
      trace-spans.png              ← export (Task 3)
      fluent-bit-pipeline.png      ← export (Task 4)
content/
  blog/
    observability-locally-with-grafana.md   ← modified (Task 5)
```

---

## Task 1: Export Diagram A — Request flow

**Files:**
- Source: `static/images/observability/src/request-flow.html` (already exists)
- Create: `static/images/observability/request-flow.png`

- [ ] **Step 1: Preview the source in a browser (optional)**

Open `static/images/observability/src/request-flow.html` in Chrome to verify the diagram looks right before exporting.

- [ ] **Step 2: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/request-flow.png" \
  --window-size=780,360 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/request-flow.html"
```

> If Chrome prints `--headless=new is not supported`, drop `=new` and use `--headless` instead (Chrome < 112).

- [ ] **Step 3: Verify**

```bash
file static/images/observability/request-flow.png
```

Expected: output contains `PNG image data`.

---

## Task 2: Export Diagram B — Stack architecture

**Files:**
- Source: `static/images/observability/src/stack-architecture.html` (already exists)
- Create: `static/images/observability/stack-architecture.png`

- [ ] **Step 1: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/stack-architecture.png" \
  --window-size=780,430 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/stack-architecture.html"
```

- [ ] **Step 2: Verify**

```bash
file static/images/observability/stack-architecture.png
```

Expected: output contains `PNG image data`.

---

## Task 3: Export Diagram C — Trace spans

**Files:**
- Source: `static/images/observability/src/trace-spans.html` (already exists)
- Create: `static/images/observability/trace-spans.png`

- [ ] **Step 1: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/trace-spans.png" \
  --window-size=760,290 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/trace-spans.html"
```

- [ ] **Step 2: Verify**

```bash
file static/images/observability/trace-spans.png
```

Expected: output contains `PNG image data`.

---

## Task 4: Export Diagram D — Fluent Bit pipeline

**Files:**
- Source: `static/images/observability/src/fluent-bit-pipeline.html` (already exists)
- Create: `static/images/observability/fluent-bit-pipeline.png`

- [ ] **Step 1: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/fluent-bit-pipeline.png" \
  --window-size=760,360 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/fluent-bit-pipeline.html"
```

- [ ] **Step 2: Verify**

```bash
file static/images/observability/fluent-bit-pipeline.png
```

Expected: output contains `PNG image data`.

---

## Task 5: Insert images into the blog post

**Files:**
- Modify: `content/blog/observability-locally-with-grafana.md`

- [ ] **Step 1: Insert Diagram A after the bullet list in "What we're building"**

Find:
```
- All of it visible in a single Grafana dashboard

The companion code is at
```

Replace with:
```
- All of it visible in a single Grafana dashboard

![The async request flow showing Browser, MessagesController, SolidQueue, LlmResponseJob, LLM API, and Turbo Frame update with the OTEL trace boundary wrapping only the HTTP request through the SolidQueue enqueue](/images/observability/request-flow.png)
*The async request flow. The OTEL trace covers the HTTP request through the SolidQueue enqueue. The job execution and LLM call run outside the current trace boundary.*

The companion code is at
```

- [ ] **Step 2: Insert Diagram B after the tool list in "The stack"**

Find:
```
  a logging SDK.

## Spinning up the stack
```

Replace with:
```
  a logging SDK.

![The five observability services — Jaeger, Prometheus, Loki, Fluent Bit, and Grafana — and the arrows showing how they connect to the Rails app](/images/observability/stack-architecture.png)
*The five observability services and how they connect to the Rails app. Traces push to Jaeger; Prometheus scrapes metrics; logs flow via Fluent Bit to Loki. Grafana queries all three.*

## Spinning up the stack
```

- [ ] **Step 3: Insert Diagram C after the OTEL setup in "Instrumenting the Rails app"**

Find:
```
so traces flow without any manual plumbing.

Send a chat message, then open the Jaeger UI
```

Replace with:
```
so traces flow without any manual plumbing.

![Jaeger-style waterfall showing the POST request as root span with three child spans: two ActiveRecord inserts and a SolidQueue enqueue. A dashed line marks the trace boundary below which LlmResponseJob is greyed out as not yet traced.](/images/observability/trace-spans.png)
*A single OTEL trace for one chat message. The three spans inside the boundary show the controller, two DB inserts, and the SolidQueue enqueue. The job and LLM call would appear in a future trace once SolidQueue propagates context.*

Send a chat message, then open the Jaeger UI
```

- [ ] **Step 4: Insert Diagram D in "Fluent Bit and log collection"**

Find:
```
records to Loki. Your app doesn't know Loki exists. That's the production pattern.

Fluent Bit is already included in `skaffold.deps.yaml`
```

Replace with:
```
records to Loki. Your app doesn't know Loki exists. That's the production pattern.

![Log pipeline diagram showing Rails Pod writing to stdout, which the container runtime writes to /var/log/containers, which Fluent Bit tails and forwards through its INPUT, FILTER, and OUTPUT stages to Loki, which Grafana then queries](/images/observability/fluent-bit-pipeline.png)
*The log pipeline. Rails writes to stdout; the container runtime writes that to /var/log/containers; Fluent Bit tails, filters, and forwards to Loki; Grafana queries with LogQL. The app never knows Loki exists.*

Fluent Bit is already included in `skaffold.deps.yaml`
```

---

## Task 6: Verify and commit

- [ ] **Step 1: Start Hugo server and verify the post**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
hugo server -D --port 1313
```

Open `http://localhost:1313/blog/observability-locally-with-grafana/` and confirm all four images appear at the correct locations with captions. Stop with `Ctrl+C`.

- [ ] **Step 2: Commit**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
git add static/images/observability/ content/blog/observability-locally-with-grafana.md
git commit -m "$(cat <<'EOF'
feat(blog): add four diagrams to observability post

Adds request flow, stack architecture, trace span waterfall, and Fluent
Bit pipeline diagrams. Source SVGs in static/images/observability/src/,
exported PNGs referenced inline in the post with captions.

Co-Authored-By: Claude Sonnet 4.6 <noreply@anthropic.com>
EOF
)"
```

Expected: commit created on `feature/observability-blog-post`.
