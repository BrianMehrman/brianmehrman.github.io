---
date: '2026-05-16T14:48:41-05:00'
draft: true
title: 'Observability Locally With Grafana'
tags: [observability, rails, grafana, otel, docker]
---

Most of the time, `puts` is fine. You add a line, reproduce the bug, and move on.
But at some point your app gets complicated enough that you need to see what's
actually happening — not guess. That's where observability comes in.

Observability means your system tells you what it's doing: how long each operation
takes, how often things fail, and what happened right before something went wrong.
And it turns out the same signals that help you debug production are the ones that
let you wire up AI-driven automation later — but that's a story for another post.
Today, let's get the stack running locally.

## What we're building

We'll be working with a Rails 8 chatbot app backed by a local LLM. The user sends
a message, Rails enqueues a `LlmResponseJob` via SolidQueue, and the job calls
an OpenAI-compatible API (Ollama, LM Studio, or any provider). This gives us
something worth observing: HTTP requests, background jobs, and external API calls
all in one flow.

By the end of this post, you'll have:
- Distributed traces in Jaeger showing the full request → job → LLM span chain
- Prometheus metrics scraped from the Rails app
- Logs collected by Fluent Bit and queryable in Loki
- All of it visible in a single Grafana dashboard

The companion code is at [BrianMehrman/postgres-rails](https://github.com/BrianMehrman/postgres-rails).

## The stack

Four tools, each with one job:

- **Jaeger** answers "where did the time go?" — it stores distributed traces so you
  can see exactly which database query or LLM call ate your latency.
- **Prometheus** answers "how often and how fast?" — it scrapes numeric metrics
  (request rates, durations, error counts) on a schedule.
- **Loki** answers "what happened?" — it aggregates structured log lines so you
  can filter and search across your app's output.
- **Grafana** is the single pane of glass. It connects to all three and lets you
  build dashboards that show traces, metrics, and logs side by side.

## Spinning up the stack

All four services live in `docker-compose.observability.yml` alongside your
existing Postgres and Redis services. Start them with:

```bash
docker compose -f docker-compose.observability.yml up -d
```

Here's the key parts of the compose file:

```yaml
services:
  jaeger:
    image: jaegertracing/jaeger:2.1.0
    ports:
      - "4318:4318"   # OTLP HTTP — your app sends traces here
      - "16686:16686" # Jaeger UI
    environment:
      - COLLECTOR_OTLP_ENABLED=true

  prometheus:
    image: prom/prometheus:v3.1.0
    ports:
      - "9090:9090"
    volumes:
      - ./config/observability/prometheus.yml:/etc/prometheus/prometheus.yml:ro

  loki:
    image: grafana/loki:3.3.2
    ports:
      - "3100:3100"

  grafana:
    image: grafana/grafana:11.4.0
    ports:
      - "3001:3000"
    environment:
      - GF_AUTH_ANONYMOUS_ENABLED=true
      - GF_AUTH_ANONYMOUS_ORG_ROLE=Admin
    depends_on: [prometheus, loki, jaeger]
```

Prometheus needs to know where to scrape. `config/observability/prometheus.yml`:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: postgres-rails
    static_configs:
      - targets: ["web:3000"]
    metrics_path: /metrics
```

Open Grafana at `http://localhost:3001`. No login needed — anonymous admin is
enabled for local use. Add Jaeger, Prometheus, and Loki as datasources.

## Instrumenting the Rails app

Add the OTEL gems to your Gemfile:

```ruby
gem "opentelemetry-sdk"
gem "opentelemetry-exporter-otlp"
gem "opentelemetry-instrumentation-rails"
gem "opentelemetry-instrumentation-active_record"
gem "opentelemetry-instrumentation-http"
gem "prometheus-client"
```

Create `config/initializers/opentelemetry.rb`:

```ruby
if ENV["OTEL_ENABLED"] == "true"
  require "opentelemetry/sdk"
  require "opentelemetry/exporter/otlp"
  require "opentelemetry/instrumentation/rails"
  require "opentelemetry/instrumentation/active_record"
  require "opentelemetry/instrumentation/http"

  OpenTelemetry::SDK.configure do |c|
    c.service_name = ENV.fetch("OTEL_SERVICE_NAME", "postgres-rails")
    c.use "OpenTelemetry::Instrumentation::Rails"
    c.use "OpenTelemetry::Instrumentation::ActiveRecord"
    c.use "OpenTelemetry::Instrumentation::Http"
  end
end
```

The `OTEL_ENABLED` guard means the app runs normally in tests and CI without
touching the observability stack. The OTLP exporter picks up
`OTEL_EXPORTER_OTLP_ENDPOINT` from the environment (default: `http://localhost:4318`).

For the LLM client, wrap the HTTP call in a manual span so the trace shows the
LLM as a child of the background job:

```ruby
tracer = OpenTelemetry.tracer_provider.tracer("llm_client")

tracer.in_span("llm.chat", attributes: { "llm.model" => @model }) do |span|
  response = make_request(messages)
  span.set_attribute("llm.response_length", response.to_s.length)
  response
end
```

Start the app with OTEL enabled:

```bash
OTEL_ENABLED=true OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318 bin/rails server
```

Send a chat message, then open the Jaeger UI at `http://localhost:16686`. Select
the `postgres-rails` service and click "Find Traces." You'll see the HTTP POST
trace with three child spans: the ActiveRecord insert, the SolidQueue job enqueue,
and — once the job runs — the `llm.chat` span showing exactly how long the LLM
took to respond.
