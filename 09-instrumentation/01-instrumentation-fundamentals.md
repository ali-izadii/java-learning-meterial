# Instrumentation Fundamentals & Observability Signals

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-13

---

## 🎯 What a senior candidate must know

- **Instrumentation vs monitoring vs profiling**: instrumentation emits telemetry from the application; monitoring stores, queries, alerts, and dashboards it; profiling samples runtime behavior to explain where time/allocations/locks go. A senior answer separates these instead of saying "we use Grafana."
- The three core **observability signals**: **metrics** (cheap aggregate time series), **logs** (discrete events with context), and **traces** (causal path of one request across components). Know when each is the right tool.
- **RED and USE**: for request-serving systems, track Rate, Errors, Duration; for resources, track Utilization, Saturation, Errors. These are practical starting points, not a full observability strategy.
- **Service-level thinking**: instrument user-visible outcomes first: successful requests, failed requests, latency distribution, queue depth, dependency latency, retries, timeouts, circuit-breaker opens, cache hit ratio, and saturation.
- **Good telemetry has dimensions**: method, route template, status code, dependency, outcome, region, version. Bad telemetry uses unbounded labels such as user id, raw URL, order id, exception message, or SQL text.
- **Cardinality is the tax**: every unique metric name + label set is a time series. High cardinality increases memory, storage, network, query latency, and bill size.
- **Instrumentation must be correct under failure**: record errors on all exit paths; close spans in `finally`; avoid throwing from metric/logging code; make exporters asynchronous and bounded.
- **Overhead model**: counters and simple timers are usually cheap; histograms, labels, stack traces, synchronous exports, and per-item inner-loop telemetry are not free. Measure overhead on hot paths.
- **Automatic vs manual instrumentation**: auto-instrumentation gives broad edge coverage quickly; manual instrumentation adds domain meaning: business operation names, outcome reasons, batch sizes, tenant class, retry counts, and semantic attributes.
- **Structured logs and correlation**: logs should carry `trace_id`, `span_id`, request id, service/version, and stable event names. Free-form text is useful for humans but weak for aggregation.

## 📚 Resources

### Books / chapters
- **Observability Engineering** — Charity Majors, Liz Fong-Jones, George Miranda — the modern mental model for high-cardinality events, debugging, and production feedback loops.
- **Site Reliability Engineering** — Google SRE book — monitoring, alerting, SLIs/SLOs, and practical service health thinking.
- **Release It! (2nd ed.)** — Michael Nygard — stability patterns worth instrumenting: timeouts, retries, bulkheads, circuit breakers, pools, and queues.

### Videos & conference talks
- 🎥 **"Monitoring and Observability" talks by Charity Majors** — good for distinguishing dashboard-driven monitoring from exploratory observability.
- 🎥 **Google SRE talks on SLIs/SLOs** — useful for connecting instrumentation to promises users actually care about.
- 🎥 **"The Four Golden Signals" talks** — latency, traffic, errors, saturation; a concise production monitoring vocabulary.

### Articles & official docs
- **OpenTelemetry — Concepts and Signals**: https://opentelemetry.io/docs/concepts/signals/
- **Prometheus — Instrumentation best practices**: https://prometheus.io/docs/practices/instrumentation/
- **Google SRE Book — Monitoring Distributed Systems**: https://sre.google/sre-book/monitoring-distributed-systems/
- **Micrometer concepts**: https://docs.micrometer.io/micrometer/reference/concepts.html

## ❓ Senior interview questions

1. Explain metrics, logs, traces, and profiles. What question does each answer best?
2. What would you instrument first in a new Java HTTP service?
3. Why is `user_id` usually a dangerous metric label but acceptable as a trace/log attribute?
4. RED vs USE — where does each fit?
5. How can instrumentation code create an outage?
6. Your dashboard shows p95 latency is fine but users complain. What telemetry might be missing?
7. What is the difference between observability and a collection of dashboards?

## 🗺️ Study path

1. Pick one service endpoint and define RED metrics, useful trace spans, and log events for the happy path and failure paths.
2. Rewrite one unstructured log line into a structured event with stable fields.
3. List every label you would add to request metrics; remove any field with unbounded cardinality.
4. Create a short incident story: symptom → metric → trace/log → root cause → fix → new instrumentation.
5. Rehearse Q2 as a 90-second production-readiness answer.

## 📝 My notes

_(your own notes while studying)_
