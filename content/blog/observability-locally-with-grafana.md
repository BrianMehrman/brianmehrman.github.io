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

## Patterns worth monitoring

Once you have traces and metrics flowing, here are three things to actually
watch and what they tell you.

**LLM p95 latency** — In Jaeger, filter traces by operation name `llm.chat`
and look at the latency distribution. The p95 (95th percentile) is the number
to care about: it tells you what a bad-but-not-worst-case user experience looks
like. Local LLMs can vary wildly — a cold Ollama model might take 30 seconds;
a warm one might take 2. Set your baseline before you optimize.

**Job queue backlog** — In Prometheus, query:
```
rate(http_server_requests_total{path="/chats/*/messages"}[5m])
```
Compare the rate of incoming messages against the rate of completed jobs. If
messages are arriving faster than `LlmResponseJob` is finishing, your queue
is growing. That's when you add workers.

**`LlmResponseJob` error rate** — In Loki, query:
```
{app="postgres-rails"} |= "LlmResponseJob" |= "error"
```
This surfaces job failures in your log stream. Pair it with the `status="error"`
label on the `llm_request_duration_seconds` Prometheus metric for an alert:
when errors exceed 5% of LLM requests, something is wrong with the LLM provider.

## Adding Fluent Bit

The setup above gets traces to Jaeger and metrics to Prometheus. For logs, the
cleanest approach is to let the app write to stdout (which Rails does by default)
and have a log agent collect and ship them. Fluent Bit is the right tool for this.

Instead of adding a Loki library to your app, Fluent Bit reads directly from
Docker's container log files, parses them, and forwards structured records to Loki.
Your app doesn't know Loki exists. That's the production pattern.

Add Fluent Bit to `docker-compose.observability.yml`:

```yaml
  fluent-bit:
    image: fluent/fluent-bit:3.2
    volumes:
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./config/observability/fluent-bit.conf:/fluent-bit/etc/fluent-bit.conf:ro
      - ./config/observability/parsers.conf:/fluent-bit/etc/parsers.conf:ro
    depends_on:
      - loki
```

`config/observability/fluent-bit.conf`:

```ini
[SERVICE]
    Flush         1
    Parsers_File  /fluent-bit/etc/parsers.conf

[INPUT]
    Name              tail
    Path              /var/lib/docker/containers/*/*-json.log
    Parser            docker
    Tag               docker.*
    Refresh_Interval  5

[FILTER]
    Name    record_modifier
    Match   docker.*
    Record  app postgres-rails

[OUTPUT]
    Name    loki
    Match   docker.*
    Host    loki
    Port    3100
    Labels  job=fluent-bit,app=postgres-rails
```

Restart the stack: `docker compose -f docker-compose.observability.yml up -d`

In Grafana, go to Explore → Loki and run `{app="postgres-rails"}`. Instead of
raw text, you get structured JSON records with timestamp, container name, and
log level as separate fields — which means you can filter with LogQL:

```logql
{app="postgres-rails"} | json | level="ERROR"
```

That's the difference between searching logs and querying them.

## What's next

You now have a full local observability loop: traces in Jaeger, metrics in
Prometheus, logs in Loki, and everything queryable in Grafana. A few directions
to take it further:

- **Grafana dashboards** — pin the three queries above to a dashboard so you see
  them at a glance instead of running them manually in Explore.
- **Alerting** — use Grafana's alert rules to get a notification when LLM error
  rate crosses a threshold.
- **Extend to other services** — the same OTEL initializer pattern works for any
  Ruby process. Add it to your workers, CLIs, or a second service and traces
  will automatically connect across service boundaries.

The stack you built here is the same one you'd run in production — scaled up,
but structurally identical. That's the point: local observability shouldn't be
a toy. It should be the real thing.
