# Design: Diagrams for "Observability Locally With Grafana"

**Date:** 2026-06-07
**Post:** `content/blog/observability-locally-with-grafana.md`
**Status:** Approved — ready for implementation

---

## Overview

Add four PNG diagrams to the observability blog post to make the architecture and
data flow concrete before readers open a terminal. All diagrams use a hand-drawn
(Excalidraw) visual style to match the approachable tone of the post.

---

## Format and delivery

| Decision | Choice |
|---|---|
| File format | PNG (screenshot from Excalidraw or browser) |
| Visual style | Hand-drawn / Excalidraw aesthetic |
| Storage location | `static/images/observability/` |
| Markdown reference | `![alt text](/images/observability/<filename>.png)` |

Each diagram is designed as an SVG mockup in the visual companion session at
`.superpowers/brainstorm/82129-1780882377/content/`. The user screenshots or
recreates these in Excalidraw to produce the final PNGs.

---

## Diagrams

### A — Request → Job → LLM flow

**File:** `static/images/observability/request-flow.png`
**Placement:** After the bullet list in the "What we're building" section
**Caption:** *The async request flow. The OTEL trace covers the HTTP request
through the SolidQueue enqueue. The job execution and LLM call run outside
the current trace boundary.*

**Content:**

Six numbered steps arranged left-to-right then wrapping to a second row:

1. Browser → `POST /chats/:id/messages` → MessagesController
2. MessagesController → enqueue → SolidQueue
3. MessagesController → redirect (dashed, returns to browser without waiting)
4. SolidQueue (dashed, async) → LlmResponseJob → HTTP → LLM API (Ollama / LM Studio)
5. LLM API → response → LlmResponseJob
6. LlmResponseJob → `broadcast_replace_to` → Turbo Frame update → Browser

**OTEL boundary:** A dashed gold box wraps steps 1–2 (Browser → MessagesController
→ SolidQueue) and is labelled "OpenTelemetry trace". A vertical dashed divider
marks where the trace ends. The job and LLM side carries a small italic label
"not yet traced".

---

### B — Observability stack architecture

**File:** `static/images/observability/stack-architecture.png`
**Placement:** After the five-bullet tool list in "The stack" section
**Caption:** *The five observability services and how they connect to the Rails
app. Traces push to Jaeger; Prometheus scrapes metrics; logs flow via Fluent Bit
to Loki. Grafana queries all three.*

**Content:**

Five service boxes arranged around a central Rails App box:

- **Rails App** (centre-left) — labelled: Puma + SolidQueue, OpenTelemetry SDK, prometheus-client gem
- **Jaeger** (top-right) — "trace storage + UI"
- **Prometheus** (middle-right) — "metrics storage"
- **Loki** (bottom-right) — "log aggregation"
- **Fluent Bit** (bottom-centre) — "DaemonSet · log collector"
- **Grafana** (far-right, tall box spanning all three) — "dashboards · :3001"

**Connections:**
- Rails → Jaeger: solid gold arrow, "traces (OTLP HTTP :4318)"
- Prometheus → Rails: dashed orange arrow (pull direction), "scrape /metrics (:9090)"
- Rails → Fluent Bit: purple arrow via stdout / `/var/log/containers`
- Fluent Bit → Loki: green arrow, "forward logs"
- Jaeger, Prometheus, Loki → Grafana: dashed query arrows

**Legend** (below all nodes): traces (push) · metrics (scrape) · logs (tail) · query (Grafana).
Solid lines = push; dashed = pull or query.

---

### C — Distributed trace / span tree

**File:** `static/images/observability/trace-spans.png`
**Placement:** After the `bin/dev` code block in "Instrumenting the Rails app",
before the "Send a chat message" paragraph
**Caption:** *A single OTEL trace for one chat message. The three spans inside
the boundary show the controller, two DB inserts, and the SolidQueue enqueue.
The job and LLM call would appear in a future trace once SolidQueue propagates
context.*

**Content:**

Jaeger-style waterfall table with columns SPAN (left) and DURATION (right,
timeline from 0ms to ~155ms):

| Span | Bar | Duration |
|---|---|---|
| `POST /chats/:id/messages` (root) | Full-width blue bar | ~155ms |
| ↳ `INSERT INTO messages` | Short green bar at 0ms | ~4ms |
| ↳ `INSERT INTO messages (pending)` | Short green bar at ~5ms | ~3ms |
| ↳ SolidQueue enqueue | Short purple bar at ~10ms | ~5ms |

A dashed horizontal divider below the four rows, labelled "trace boundary —
job runs asynchronously, outside this trace."

Below the divider: a greyed-out faint row — "LlmResponseJob (not yet traced)"
with a note "would appear here as a separate trace once SolidQueue propagates
context" and a faint ~8000ms bar.

---

### D — Fluent Bit log pipeline

**File:** `static/images/observability/fluent-bit-pipeline.png`
**Placement:** After the opening two paragraphs of "Fluent Bit and log
collection", before the YAML code block
**Caption:** *The log pipeline. Rails writes to stdout; the container runtime
writes that to /var/log/containers; Fluent Bit tails, filters, and forwards to
Loki; Grafana queries with LogQL. The app never knows Loki exists.*

**Content:**

Left-to-right pipeline with a downward branch at Loki:

1. **Rails Pod** (blue box) — "writes to stdout (Rails.logger)" → stdout arrow →
2. **`/var/log/containers/`** (dashed grey box, infrastructure) — tail arrow →
3. **Fluent Bit DaemonSet** (purple box) with three internal stage boxes:
   - INPUT: tail
   - FILTER: kubernetes
   - OUTPUT: loki
   → forward arrow →
4. **Loki** (green box) — "log store · :3100" → dashed query arrow downward →
5. **Grafana** (pink box) — "LogQL queries · :3001"

**Callout** (bottom-left, yellow dashed box): "Your app stays clean — No Loki
SDK. No log shipper in the app. Just stdout."

---

## Blog post insertion points

| Diagram | Section | Insert after |
|---|---|---|
| A — Request flow | "What we're building" | The four-bullet "By the end of this post" list |
| B — Stack architecture | "The stack" | The five-bullet tool descriptions (Jaeger … Fluent Bit) |
| C — Trace spans | "Instrumenting the Rails app" | The `bin/dev` code block |
| D — Fluent Bit pipeline | "Fluent Bit and log collection" | The second paragraph ("Fluent Bit is already included…") |

---

## Implementation tasks

1. Create `static/images/observability/` directory
2. Produce four PNGs from the approved mockups (Excalidraw or browser screenshot)
3. Add each image to the blog post at the insertion points above
4. Add a figure caption below each image using Hugo's `{{< figure >}}` shortcode
   or plain markdown `*caption text*`
5. Verify images render correctly with `hugo server`
