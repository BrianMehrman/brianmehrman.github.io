# Observability Blog Diagrams Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add four PNG diagrams to `content/blog/observability-locally-with-grafana.md` on the `feature/observability-blog-post` branch, illustrating the request flow, observability stack, trace spans, and Fluent Bit pipeline.

**Architecture:** Each diagram lives as a standalone HTML source file under `static/images/observability/src/`. Chrome headless exports each source to a sibling PNG. The blog post references the PNGs with standard markdown image syntax plus an italic caption line.

**Tech Stack:** Hugo static site (PaperMod theme), SVG/HTML for diagram sources, Chrome headless for PNG export, `hugo server` for verification.

---

## File Structure

```
static/
  images/
    observability/
      src/
        request-flow.html          ← diagram A source (create)
        stack-architecture.html    ← diagram B source (create)
        trace-spans.html           ← diagram C source (create)
        fluent-bit-pipeline.html   ← diagram D source (create)
      request-flow.png             ← exported PNG (create)
      stack-architecture.png       ← exported PNG (create)
      trace-spans.png              ← exported PNG (create)
      fluent-bit-pipeline.png      ← exported PNG (create)
content/
  blog/
    observability-locally-with-grafana.md   ← modified (4 image insertions)
```

---

## Task 1: Set up branch and directory structure

**Files:**
- Create: `static/images/observability/src/` (directory)

- [ ] **Step 1: Check out the feature branch**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
git checkout feature/observability-blog-post
```

Expected: `Switched to branch 'feature/observability-blog-post'`

- [ ] **Step 2: Create the image directories**

```bash
mkdir -p static/images/observability/src
```

- [ ] **Step 3: Verify Hugo can find static assets**

```bash
ls static/
```

Expected: `images/` directory listed alongside any other existing static assets.

- [ ] **Step 4: Commit the plan doc**

```bash
git add docs/superpowers/plans/2026-06-18-observability-diagrams.md
git commit -m "docs: add implementation plan for observability blog diagrams"
```

---

## Task 2: Diagram A — Request → Job → LLM flow

**Files:**
- Create: `static/images/observability/src/request-flow.html`
- Create: `static/images/observability/request-flow.png`

- [ ] **Step 1: Create the source HTML file**

Create `static/images/observability/src/request-flow.html` with this exact content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<style>
  body { margin: 0; background: white; }
  svg text { font-family: 'Segoe UI', system-ui, sans-serif; }
</style>
</head>
<body>
<svg viewBox="0 0 780 360" width="780" height="360" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrow" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#444"/>
    </marker>
    <marker id="arrow-green" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#2a8a4a"/>
    </marker>
    <filter id="rough" x="-2%" y="-2%" width="104%" height="104%">
      <feTurbulence type="fractalNoise" baseFrequency="0.04" numOctaves="3" result="noise"/>
      <feDisplacementMap in="SourceGraphic" in2="noise" scale="1.2" xChannelSelector="R" yChannelSelector="G"/>
    </filter>
  </defs>

  <!-- OTEL trace boundary -->
  <rect x="6" y="14" width="558" height="100" rx="8" ry="8"
        fill="none" stroke="#cca800" stroke-width="2" stroke-dasharray="6,3"/>
  <text x="14" y="10" font-size="10" fill="#9a7a00" font-weight="600">OpenTelemetry trace</text>

  <!-- Browser -->
  <rect x="14" y="28" width="110" height="54" rx="5" ry="5"
        fill="#fffde7" stroke="#888" stroke-width="2" filter="url(#rough)"/>
  <text x="69" y="51" text-anchor="middle" font-size="13" font-weight="600">Browser</text>
  <text x="69" y="68" text-anchor="middle" font-size="10" fill="#666">(user)</text>

  <line x1="125" y1="55" x2="192" y2="55" stroke="#444" stroke-width="2" marker-end="url(#arrow)"/>
  <text x="158" y="48" text-anchor="middle" font-size="10" fill="#555">① POST</text>

  <!-- MessagesController -->
  <rect x="194" y="20" width="148" height="70" rx="5" ry="5"
        fill="#e3f0ff" stroke="#3b6fd4" stroke-width="2" filter="url(#rough)"/>
  <text x="268" y="44" text-anchor="middle" font-size="12" font-weight="600" fill="#1a3a7a">MessagesController</text>
  <text x="268" y="60" text-anchor="middle" font-size="10" fill="#3b6fd4">saves message</text>
  <text x="268" y="74" text-anchor="middle" font-size="10" fill="#3b6fd4">creates pending reply</text>

  <line x1="343" y1="55" x2="416" y2="55" stroke="#444" stroke-width="2" marker-end="url(#arrow)"/>
  <text x="379" y="48" text-anchor="middle" font-size="10" fill="#555">② enqueue</text>

  <!-- SolidQueue -->
  <rect x="418" y="28" width="130" height="54" rx="5" ry="5"
        fill="#f3e8ff" stroke="#7c3aed" stroke-width="2" filter="url(#rough)"/>
  <text x="483" y="51" text-anchor="middle" font-size="12" font-weight="600" fill="#4c1d95">SolidQueue</text>
  <text x="483" y="67" text-anchor="middle" font-size="10" fill="#7c3aed">(Postgres)</text>

  <!-- redirect -->
  <path d="M268 91 Q268 115 69 115 Q69 105 69 85"
        stroke="#999" stroke-width="1.5" fill="none" stroke-dasharray="4,3"
        marker-end="url(#arrow)"/>
  <text x="160" y="128" text-anchor="middle" font-size="10" fill="#999">③ redirect (browser doesn't wait)</text>

  <!-- trace ends divider -->
  <line x1="570" y1="14" x2="570" y2="340" stroke="#e0d0a0" stroke-width="1.5" stroke-dasharray="5,4"/>
  <text x="575" y="26" font-size="10" fill="#bba050" font-style="italic">trace ends</text>

  <!-- queue → job -->
  <line x1="483" y1="83" x2="483" y2="158" stroke="#7c3aed" stroke-width="2"
        stroke-dasharray="5,3" marker-end="url(#arrow)"/>
  <text x="505" y="125" font-size="10" fill="#7c3aed">picks up job</text>

  <!-- LlmResponseJob -->
  <rect x="380" y="160" width="200" height="62" rx="5" ry="5"
        fill="#fff3e0" stroke="#d97706" stroke-width="2" filter="url(#rough)"/>
  <text x="480" y="184" text-anchor="middle" font-size="12" font-weight="600" fill="#92400e">LlmResponseJob</text>
  <text x="480" y="200" text-anchor="middle" font-size="10" fill="#d97706">builds history · calls LlmClient</text>
  <text x="480" y="214" text-anchor="middle" font-size="10" fill="#d97706">marks message "complete"</text>

  <line x1="581" y1="191" x2="648" y2="191" stroke="#d97706" stroke-width="2" marker-end="url(#arrow)"/>
  <text x="614" y="184" text-anchor="middle" font-size="10" fill="#d97706">④ HTTP</text>

  <!-- LLM API -->
  <rect x="650" y="164" width="116" height="54" rx="5" ry="5"
        fill="#e8fdf0" stroke="#2a8a4a" stroke-width="2" filter="url(#rough)"/>
  <text x="708" y="187" text-anchor="middle" font-size="12" font-weight="600" fill="#1a5c32">LLM API</text>
  <text x="708" y="203" text-anchor="middle" font-size="10" fill="#2a8a4a">Ollama / LM Studio</text>

  <line x1="649" y1="206" x2="582" y2="206" stroke="#2a8a4a" stroke-width="2" marker-end="url(#arrow-green)"/>
  <text x="616" y="220" text-anchor="middle" font-size="10" fill="#2a8a4a">⑤ response</text>

  <!-- broadcast -->
  <line x1="430" y1="222" x2="268" y2="268" stroke="#444" stroke-width="2" marker-end="url(#arrow)"/>
  <text x="326" y="258" text-anchor="middle" font-size="10" fill="#555">⑥ broadcast_replace_to</text>

  <!-- Turbo Frame -->
  <rect x="140" y="270" width="256" height="54" rx="5" ry="5"
        fill="#fffde7" stroke="#888" stroke-width="2" filter="url(#rough)"/>
  <text x="268" y="292" text-anchor="middle" font-size="12" font-weight="600">Turbo Frame update</text>
  <text x="268" y="308" text-anchor="middle" font-size="10" fill="#666">AI reply appears — no page reload</text>

  <path d="M140 297 Q80 297 69 260 L69 87"
        stroke="#bbb" stroke-width="1.5" fill="none" stroke-dasharray="4,3"/>

  <text x="598" y="155" text-anchor="middle" font-size="10" fill="#bba050" font-style="italic">not yet traced</text>
</svg>
</body>
</html>
```

- [ ] **Step 2: Export to PNG with Chrome headless**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/request-flow.png" \
  --window-size=780,360 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/request-flow.html"
```

If Chrome prints `--headless=new is not supported`, drop `=new`: use `--headless` instead (Chrome < 112).

Expected: `static/images/observability/request-flow.png` created (non-zero size).

- [ ] **Step 3: Verify the PNG**

```bash
file static/images/observability/request-flow.png
```

Expected: output contains `PNG image data`.

---

## Task 3: Diagram B — Observability stack architecture

**Files:**
- Create: `static/images/observability/src/stack-architecture.html`
- Create: `static/images/observability/stack-architecture.png`

- [ ] **Step 1: Create the source HTML file**

Create `static/images/observability/src/stack-architecture.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<style>
  body { margin: 0; background: white; }
  svg text { font-family: 'Segoe UI', system-ui, sans-serif; }
</style>
</head>
<body>
<svg viewBox="0 0 780 430" width="780" height="430" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arr" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#555"/>
    </marker>
    <marker id="arr-orange" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#d97706"/>
    </marker>
    <marker id="arr-green" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#2a8a4a"/>
    </marker>
    <marker id="arr-purple" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#7c3aed"/>
    </marker>
    <filter id="rough" x="-2%" y="-2%" width="104%" height="104%">
      <feTurbulence type="fractalNoise" baseFrequency="0.04" numOctaves="3" result="noise"/>
      <feDisplacementMap in="SourceGraphic" in2="noise" scale="1.2" xChannelSelector="R" yChannelSelector="G"/>
    </filter>
  </defs>

  <!-- Rails App -->
  <rect x="60" y="140" width="180" height="100" rx="6" ry="6"
        fill="#e3f0ff" stroke="#3b6fd4" stroke-width="2.5" filter="url(#rough)"/>
  <text x="150" y="168" text-anchor="middle" font-size="14" font-weight="700" fill="#1a3a7a">Rails App</text>
  <text x="150" y="186" text-anchor="middle" font-size="10" fill="#3b6fd4">Puma + SolidQueue</text>
  <text x="150" y="202" text-anchor="middle" font-size="10" fill="#3b6fd4">OpenTelemetry SDK</text>
  <text x="150" y="218" text-anchor="middle" font-size="10" fill="#3b6fd4">prometheus-client gem</text>

  <!-- Jaeger -->
  <rect x="480" y="30" width="140" height="60" rx="5" ry="5"
        fill="#fff8dc" stroke="#cca800" stroke-width="2" filter="url(#rough)"/>
  <text x="550" y="56" text-anchor="middle" font-size="13" font-weight="600" fill="#7a6000">Jaeger</text>
  <text x="550" y="74" text-anchor="middle" font-size="10" fill="#9a7a00">trace storage + UI</text>

  <path d="M241 160 Q380 80 479 70" stroke="#cca800" stroke-width="2"
        fill="none" marker-end="url(#arr)"/>
  <text x="355" y="95" text-anchor="middle" font-size="10" fill="#9a7a00">traces (OTLP HTTP :4318)</text>

  <!-- Prometheus -->
  <rect x="480" y="160" width="140" height="60" rx="5" ry="5"
        fill="#fff0e8" stroke="#d97706" stroke-width="2" filter="url(#rough)"/>
  <text x="550" y="186" text-anchor="middle" font-size="13" font-weight="600" fill="#92400e">Prometheus</text>
  <text x="550" y="204" text-anchor="middle" font-size="10" fill="#d97706">metrics storage</text>

  <path d="M479 185 Q360 185 242 185" stroke="#d97706" stroke-width="2"
        fill="none" marker-end="url(#arr-orange)" stroke-dasharray="5,3"/>
  <text x="360" y="178" text-anchor="middle" font-size="10" fill="#d97706">scrape /metrics (:9090)</text>

  <!-- Loki -->
  <rect x="480" y="290" width="140" height="60" rx="5" ry="5"
        fill="#e8fdf0" stroke="#2a8a4a" stroke-width="2" filter="url(#rough)"/>
  <text x="550" y="316" text-anchor="middle" font-size="13" font-weight="600" fill="#1a5c32">Loki</text>
  <text x="550" y="334" text-anchor="middle" font-size="10" fill="#2a8a4a">log aggregation</text>

  <!-- Fluent Bit -->
  <rect x="210" y="295" width="150" height="55" rx="5" ry="5"
        fill="#f3e8ff" stroke="#7c3aed" stroke-width="2" filter="url(#rough)"/>
  <text x="285" y="319" text-anchor="middle" font-size="13" font-weight="600" fill="#4c1d95">Fluent Bit</text>
  <text x="285" y="337" text-anchor="middle" font-size="10" fill="#7c3aed">DaemonSet · log collector</text>

  <path d="M150 241 Q150 280 210 315" stroke="#7c3aed" stroke-width="2"
        fill="none" marker-end="url(#arr-purple)"/>
  <text x="148" y="275" text-anchor="end" font-size="10" fill="#7c3aed">stdout →</text>
  <text x="148" y="287" text-anchor="end" font-size="10" fill="#7c3aed">/var/log/containers</text>

  <line x1="361" y1="322" x2="479" y2="322" stroke="#2a8a4a" stroke-width="2"
        marker-end="url(#arr-green)"/>
  <text x="420" y="314" text-anchor="middle" font-size="10" fill="#2a8a4a">forward logs</text>

  <!-- Grafana -->
  <rect x="670" y="155" width="100" height="140" rx="5" ry="5"
        fill="#fce8f0" stroke="#c0366a" stroke-width="2" filter="url(#rough)"/>
  <text x="720" y="215" text-anchor="middle" font-size="13" font-weight="700" fill="#8b1a46">Grafana</text>
  <text x="720" y="233" text-anchor="middle" font-size="10" fill="#c0366a">dashboards</text>
  <text x="720" y="249" text-anchor="middle" font-size="10" fill="#c0366a">:3001</text>

  <path d="M620 70 Q700 70 720 155" stroke="#cca800" stroke-width="1.5"
        fill="none" marker-end="url(#arr)" stroke-dasharray="4,3"/>
  <line x1="621" y1="190" x2="669" y2="210" stroke="#d97706" stroke-width="1.5"
        marker-end="url(#arr-orange)" stroke-dasharray="4,3"/>
  <path d="M620 320 Q700 320 720 296" stroke="#2a8a4a" stroke-width="1.5"
        fill="none" marker-end="url(#arr-green)" stroke-dasharray="4,3"/>

  <!-- Legend -->
  <rect x="60" y="385" width="490" height="34" rx="4"
        fill="#fafafa" stroke="#ddd" stroke-width="1"/>
  <text x="74" y="399" font-size="9" fill="#555" font-weight="600">LEGEND</text>
  <line x1="120" y1="399" x2="150" y2="399" stroke="#cca800" stroke-width="2"/>
  <text x="155" y="403" font-size="9" fill="#555">traces (push)</text>
  <line x1="230" y1="399" x2="260" y2="399" stroke="#d97706" stroke-width="2" stroke-dasharray="4,2"/>
  <text x="265" y="403" font-size="9" fill="#555">metrics (scrape)</text>
  <line x1="350" y1="399" x2="380" y2="399" stroke="#7c3aed" stroke-width="2"/>
  <text x="385" y="403" font-size="9" fill="#555">logs (tail)</text>
  <line x1="430" y1="399" x2="460" y2="399" stroke="#888" stroke-width="1.5" stroke-dasharray="3,2"/>
  <text x="465" y="403" font-size="9" fill="#555">query (Grafana)</text>
  <text x="120" y="414" font-size="9" fill="#888">solid = push · dashed = pull or query</text>
</svg>
</body>
</html>
```

- [ ] **Step 2: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/stack-architecture.png" \
  --window-size=780,430 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/stack-architecture.html"
```

- [ ] **Step 3: Verify the PNG**

```bash
file static/images/observability/stack-architecture.png
```

Expected: output contains `PNG image data`.

---

## Task 4: Diagram C — Distributed trace / span tree

**Files:**
- Create: `static/images/observability/src/trace-spans.html`
- Create: `static/images/observability/trace-spans.png`

- [ ] **Step 1: Create the source HTML file**

Create `static/images/observability/src/trace-spans.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<style>
  body { margin: 0; background: white; }
  svg text { font-family: 'Segoe UI', system-ui, sans-serif; }
</style>
</head>
<body>
<svg viewBox="0 0 760 290" width="760" height="290" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <filter id="rough" x="-2%" y="-2%" width="104%" height="104%">
      <feTurbulence type="fractalNoise" baseFrequency="0.04" numOctaves="3" result="noise"/>
      <feDisplacementMap in="SourceGraphic" in2="noise" scale="0.8" xChannelSelector="R" yChannelSelector="G"/>
    </filter>
  </defs>

  <!-- Header -->
  <text x="10"  y="18" font-size="10" fill="#999" font-weight="600">SPAN</text>
  <text x="310" y="18" font-size="10" fill="#999" font-weight="600">DURATION</text>
  <line x1="0" y1="24" x2="760" y2="24" stroke="#e0e0e0" stroke-width="1"/>

  <!-- Timeline ticks -->
  <text x="310" y="38" font-size="9" fill="#bbb">0ms</text>
  <text x="432" y="38" font-size="9" fill="#bbb">50ms</text>
  <text x="554" y="38" font-size="9" fill="#bbb">100ms</text>
  <text x="686" y="38" font-size="9" fill="#bbb">150ms</text>
  <line x1="310" y1="40" x2="310" y2="270" stroke="#f0f0f0" stroke-width="1"/>
  <line x1="432" y1="40" x2="432" y2="270" stroke="#f0f0f0" stroke-width="1"/>
  <line x1="554" y1="40" x2="554" y2="270" stroke="#f0f0f0" stroke-width="1"/>
  <line x1="686" y1="40" x2="686" y2="270" stroke="#f0f0f0" stroke-width="1"/>

  <!-- Row 1: Root span -->
  <text x="10" y="72" font-size="11" font-weight="600" fill="#1a3a7a">POST /chats/:id/messages</text>
  <text x="10" y="86" font-size="9" fill="#888">rails.action_controller · MessagesController#create</text>
  <rect x="310" y="60" width="390" height="20" rx="3"
        fill="#3b6fd4" opacity="0.85" filter="url(#rough)"/>
  <text x="706" y="74" font-size="9" fill="#3b6fd4" font-weight="600">~155ms</text>

  <!-- Tree connector -->
  <line x1="30" y1="90" x2="30" y2="190" stroke="#ccc" stroke-width="1.5"/>

  <!-- Row 2: INSERT messages -->
  <line x1="30" y1="115" x2="50" y2="115" stroke="#ccc" stroke-width="1.5"/>
  <text x="54" y="112" font-size="11" fill="#1a5c32">INSERT INTO messages</text>
  <text x="54" y="126" font-size="9" fill="#888">active_record.sql</text>
  <rect x="310" y="103" width="20" height="18" rx="3"
        fill="#2a8a4a" opacity="0.85" filter="url(#rough)"/>
  <text x="334" y="116" font-size="9" fill="#2a8a4a" font-weight="600">~4ms</text>

  <!-- Row 3: INSERT messages (pending) -->
  <line x1="30" y1="148" x2="50" y2="148" stroke="#ccc" stroke-width="1.5"/>
  <text x="54" y="145" font-size="11" fill="#1a5c32">INSERT INTO messages (pending)</text>
  <text x="54" y="159" font-size="9" fill="#888">active_record.sql</text>
  <rect x="333" y="136" width="16" height="18" rx="3"
        fill="#2a8a4a" opacity="0.85" filter="url(#rough)"/>
  <text x="353" y="149" font-size="9" fill="#2a8a4a" font-weight="600">~3ms</text>

  <!-- Row 4: SolidQueue enqueue -->
  <line x1="30" y1="181" x2="50" y2="181" stroke="#ccc" stroke-width="1.5"/>
  <text x="54" y="178" font-size="11" fill="#4c1d95">SolidQueue enqueue</text>
  <text x="54" y="192" font-size="9" fill="#888">INSERT INTO solid_queue_jobs</text>
  <rect x="353" y="169" width="24" height="18" rx="3"
        fill="#7c3aed" opacity="0.85" filter="url(#rough)"/>
  <text x="381" y="182" font-size="9" fill="#7c3aed" font-weight="600">~5ms</text>

  <!-- Trace boundary -->
  <line x1="0" y1="205" x2="760" y2="205" stroke="#e8e0c0" stroke-width="1.5" stroke-dasharray="5,4"/>
  <text x="10" y="218" font-size="9" fill="#bba050" font-style="italic">trace boundary — job runs asynchronously, outside this trace</text>

  <!-- Row 5: not yet traced -->
  <text x="10" y="248" font-size="11" fill="#bbb">LlmResponseJob (not yet traced)</text>
  <text x="10" y="262" font-size="9" fill="#ccc">would appear here as a separate trace once SolidQueue propagates context</text>
  <rect x="310" y="236" width="300" height="18" rx="3"
        fill="#ddd" opacity="0.5" filter="url(#rough)" stroke="#bbb" stroke-width="1"/>
  <text x="616" y="249" font-size="9" fill="#bbb">~8 000ms</text>
</svg>
</body>
</html>
```

- [ ] **Step 2: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/trace-spans.png" \
  --window-size=760,290 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/trace-spans.html"
```

- [ ] **Step 3: Verify the PNG**

```bash
file static/images/observability/trace-spans.png
```

Expected: output contains `PNG image data`.

---

## Task 5: Diagram D — Fluent Bit log pipeline

**Files:**
- Create: `static/images/observability/src/fluent-bit-pipeline.html`
- Create: `static/images/observability/fluent-bit-pipeline.png`

- [ ] **Step 1: Create the source HTML file**

Create `static/images/observability/src/fluent-bit-pipeline.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<style>
  body { margin: 0; background: white; }
  svg text { font-family: 'Segoe UI', system-ui, sans-serif; }
</style>
</head>
<body>
<svg viewBox="0 0 760 360" width="760" height="360" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arr" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#555"/>
    </marker>
    <marker id="arr-purple" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#7c3aed"/>
    </marker>
    <marker id="arr-green" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#2a8a4a"/>
    </marker>
    <marker id="arr-pink" markerWidth="8" markerHeight="8" refX="6" refY="3" orient="auto">
      <path d="M0,0 L0,6 L8,3 z" fill="#c0366a"/>
    </marker>
    <filter id="rough" x="-2%" y="-2%" width="104%" height="104%">
      <feTurbulence type="fractalNoise" baseFrequency="0.04" numOctaves="3" result="noise"/>
      <feDisplacementMap in="SourceGraphic" in2="noise" scale="1.0" xChannelSelector="R" yChannelSelector="G"/>
    </filter>
  </defs>

  <!-- Rails Pod -->
  <rect x="10" y="90" width="130" height="80" rx="5" ry="5"
        fill="#e3f0ff" stroke="#3b6fd4" stroke-width="2" filter="url(#rough)"/>
  <text x="75" y="118" text-anchor="middle" font-size="12" font-weight="700" fill="#1a3a7a">Rails Pod</text>
  <text x="75" y="135" text-anchor="middle" font-size="10" fill="#3b6fd4">writes to stdout</text>
  <text x="75" y="151" text-anchor="middle" font-size="10" fill="#3b6fd4">(Rails.logger)</text>

  <line x1="141" y1="130" x2="188" y2="130" stroke="#555" stroke-width="2" marker-end="url(#arr)"/>
  <text x="164" y="122" text-anchor="middle" font-size="9" fill="#777">stdout</text>

  <!-- /var/log/containers -->
  <rect x="190" y="100" width="130" height="60" rx="5" ry="5"
        fill="#fafafa" stroke="#999" stroke-width="1.5" filter="url(#rough)" stroke-dasharray="5,3"/>
  <text x="255" y="126" text-anchor="middle" font-size="11" font-weight="600" fill="#555">/var/log/</text>
  <text x="255" y="142" text-anchor="middle" font-size="11" font-weight="600" fill="#555">containers/</text>

  <line x1="321" y1="130" x2="368" y2="130" stroke="#7c3aed" stroke-width="2" marker-end="url(#arr-purple)"/>
  <text x="344" y="122" text-anchor="middle" font-size="9" fill="#7c3aed">tail</text>

  <!-- Fluent Bit -->
  <rect x="370" y="50" width="170" height="165" rx="6" ry="6"
        fill="#f3e8ff" stroke="#7c3aed" stroke-width="2" filter="url(#rough)"/>
  <text x="455" y="74" text-anchor="middle" font-size="13" font-weight="700" fill="#4c1d95">Fluent Bit</text>
  <text x="455" y="90" text-anchor="middle" font-size="9" fill="#7c3aed">DaemonSet (one per node)</text>

  <rect x="388" y="100" width="134" height="24" rx="3" fill="#e8d8ff" stroke="#9c5aff" stroke-width="1"/>
  <text x="455" y="116" text-anchor="middle" font-size="10" fill="#4c1d95">INPUT: tail</text>
  <line x1="455" y1="124" x2="455" y2="134" stroke="#9c5aff" stroke-width="1.5" marker-end="url(#arr-purple)"/>
  <rect x="388" y="134" width="134" height="24" rx="3" fill="#e8d8ff" stroke="#9c5aff" stroke-width="1"/>
  <text x="455" y="150" text-anchor="middle" font-size="10" fill="#4c1d95">FILTER: kubernetes</text>
  <line x1="455" y1="158" x2="455" y2="168" stroke="#9c5aff" stroke-width="1.5" marker-end="url(#arr-purple)"/>
  <rect x="388" y="168" width="134" height="24" rx="3" fill="#e8d8ff" stroke="#9c5aff" stroke-width="1"/>
  <text x="455" y="184" text-anchor="middle" font-size="10" fill="#4c1d95">OUTPUT: loki</text>

  <line x1="541" y1="132" x2="588" y2="132" stroke="#2a8a4a" stroke-width="2" marker-end="url(#arr-green)"/>
  <text x="564" y="124" text-anchor="middle" font-size="9" fill="#2a8a4a">forward</text>

  <!-- Loki -->
  <rect x="590" y="92" width="110" height="80" rx="5" ry="5"
        fill="#e8fdf0" stroke="#2a8a4a" stroke-width="2" filter="url(#rough)"/>
  <text x="645" y="124" text-anchor="middle" font-size="13" font-weight="600" fill="#1a5c32">Loki</text>
  <text x="645" y="142" text-anchor="middle" font-size="10" fill="#2a8a4a">log store</text>
  <text x="645" y="158" text-anchor="middle" font-size="10" fill="#2a8a4a">:3100</text>

  <line x1="645" y1="173" x2="645" y2="238" stroke="#c0366a" stroke-width="2"
        marker-end="url(#arr-pink)" stroke-dasharray="4,3"/>
  <text x="658" y="210" font-size="9" fill="#c0366a">query</text>

  <!-- Grafana -->
  <rect x="580" y="240" width="140" height="60" rx="5" ry="5"
        fill="#fce8f0" stroke="#c0366a" stroke-width="2" filter="url(#rough)"/>
  <text x="650" y="267" text-anchor="middle" font-size="13" font-weight="700" fill="#8b1a46">Grafana</text>
  <text x="650" y="284" text-anchor="middle" font-size="10" fill="#c0366a">LogQL queries · :3001</text>

  <!-- Callout -->
  <rect x="10" y="220" width="200" height="60" rx="4"
        fill="#fffde7" stroke="#cca800" stroke-width="1.5" stroke-dasharray="4,2"/>
  <text x="110" y="240" text-anchor="middle" font-size="10" font-weight="600" fill="#7a6000">Your app stays clean</text>
  <text x="110" y="256" text-anchor="middle" font-size="9" fill="#9a7a00">No Loki SDK. No log shipper</text>
  <text x="110" y="270" text-anchor="middle" font-size="9" fill="#9a7a00">in the app. Just stdout.</text>
</svg>
</body>
</html>
```

- [ ] **Step 2: Export to PNG**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --headless=new \
  --screenshot="$(pwd)/static/images/observability/fluent-bit-pipeline.png" \
  --window-size=760,360 \
  --hide-scrollbars \
  "$(pwd)/static/images/observability/src/fluent-bit-pipeline.html"
```

- [ ] **Step 3: Verify the PNG**

```bash
file static/images/observability/fluent-bit-pipeline.png
```

Expected: output contains `PNG image data`.

---

## Task 6: Insert images into the blog post

**Files:**
- Modify: `content/blog/observability-locally-with-grafana.md`

- [ ] **Step 1: Insert diagram A after the bullet list in "What we're building"**

Find this block (around line 29–33):

```markdown
- All of it visible in a single Grafana dashboard

The companion code is at
```

Replace with:

```markdown
- All of it visible in a single Grafana dashboard

![The async request flow showing Browser, MessagesController, SolidQueue, LlmResponseJob, LLM API, and Turbo Frame update with the OTEL trace boundary wrapping only the HTTP request through the SolidQueue enqueue](/images/observability/request-flow.png)
*The async request flow. The OTEL trace covers the HTTP request through the SolidQueue enqueue. The job execution and LLM call run outside the current trace boundary.*

The companion code is at
```

- [ ] **Step 2: Insert diagram B after the tool list in "The stack"**

Find this block (around line 49–53):

```markdown
  a logging SDK.

## Spinning up the stack
```

Replace with:

```markdown
  a logging SDK.

![The five observability services — Jaeger, Prometheus, Loki, Fluent Bit, and Grafana — and the arrows showing how they connect to the Rails app](/images/observability/stack-architecture.png)
*The five observability services and how they connect to the Rails app. Traces push to Jaeger; Prometheus scrapes metrics; logs flow via Fluent Bit to Loki. Grafana queries all three.*

## Spinning up the stack
```

- [ ] **Step 3: Insert diagram C after the `bin/dev` code block in "Instrumenting the Rails app"**

Find this block (around line 165–173):

```markdown
so traces flow without any manual plumbing.

Send a chat message, then open the Jaeger UI
```

Replace with:

```markdown
so traces flow without any manual plumbing.

![Jaeger-style waterfall showing the POST request as root span with three child spans: two ActiveRecord inserts and a SolidQueue enqueue. A dashed line marks the trace boundary below which LlmResponseJob is greyed out as not yet traced.](/images/observability/trace-spans.png)
*A single OTEL trace for one chat message. The three spans inside the boundary show the controller, two DB inserts, and the SolidQueue enqueue. The job and LLM call would appear in a future trace once SolidQueue propagates context.*

Send a chat message, then open the Jaeger UI
```

- [ ] **Step 4: Insert diagram D after the second paragraph in "Fluent Bit and log collection"**

Find this block (around line 213–216):

```markdown
records to Loki. Your app doesn't know Loki exists. That's the production pattern.

Fluent Bit is already included in `skaffold.deps.yaml`
```

Replace with:

```markdown
records to Loki. Your app doesn't know Loki exists. That's the production pattern.

![Log pipeline diagram showing Rails Pod writing to stdout, which the container runtime writes to /var/log/containers, which Fluent Bit tails and forwards through its INPUT, FILTER, and OUTPUT stages to Loki, which Grafana then queries](/images/observability/fluent-bit-pipeline.png)
*The log pipeline. Rails writes to stdout; the container runtime writes that to /var/log/containers; Fluent Bit tails, filters, and forwards to Loki; Grafana queries with LogQL. The app never knows Loki exists.*

Fluent Bit is already included in `skaffold.deps.yaml`
```

---

## Task 7: Verify and commit

**Files:**
- No new files — verification and git only

- [ ] **Step 1: Start Hugo server and check the post**

```bash
cd /Users/brianmehrman/projects/brianmehrman.github.io
hugo server -D --port 1313
```

Open `http://localhost:1313/blog/observability-locally-with-grafana/` in a browser.
Verify all four images appear at the correct locations in the post with captions beneath each.

Stop the server with `Ctrl+C` when done.

- [ ] **Step 2: Check git status**

```bash
git status
```

Expected: four new files under `static/images/observability/src/`, four new PNGs under `static/images/observability/`, and `content/blog/observability-locally-with-grafana.md` modified.

- [ ] **Step 3: Stage and commit**

```bash
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
