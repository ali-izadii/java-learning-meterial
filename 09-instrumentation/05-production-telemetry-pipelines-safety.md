# Production Telemetry Pipelines & Safety

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-13

---

## 🎯 What a senior candidate must know

- **Telemetry pipeline shape**: application instrumentation → in-process SDK/agent → exporter → OpenTelemetry Collector or vendor agent → backend storage/query/alerting. Each hop needs buffering, retry, backpressure, and failure behavior.
- **OTLP and Collector**: OTLP is the common OpenTelemetry protocol; the Collector receives, processes, batches, samples, redacts, transforms, and exports telemetry. It can run as sidecar, daemonset/agent, or gateway.
- **Fail-open principle**: telemetry must not take down the service. Exporters should be async, bounded, timeout-protected, and willing to drop telemetry when the backend is unavailable.
- **Backpressure and cost**: high-cardinality metrics, unsampled traces, verbose logs, and large attributes create backend cost and application pressure. Seniors treat telemetry volume as a production capacity concern.
- **Sampling strategy**: keep enough normal traffic for baselines, keep errors and slow requests at higher rates, and document what sampling means for dashboards, debugging, and compliance.
- **PII/secrets hygiene**: redact or drop authorization headers, cookies, tokens, raw request/response bodies, email, phone, addresses, payment data, and user-generated fields unless explicitly approved.
- **Versioning and rollout**: telemetry schemas should be stable. Changes to metric names, labels, span names, or log fields can break dashboards and alerts just like API changes break clients.
- **Self-observability**: instrument the telemetry system itself: dropped spans/logs, export queue size, exporter failures, collector CPU/memory, scrape failures, remote-write lag, and ingestion errors.
- **Operational debugging workflow**: alert on SLO symptoms → inspect dashboards → jump to exemplar/trace/logs → profile when code hotspot is unknown → deploy fix → confirm with the same telemetry.
- **Governance**: define owners for dashboards/alerts, review new high-cardinality labels, require runbooks for paging alerts, and keep telemetry config under version control.

## 📚 Resources

### Books / chapters
- **Site Reliability Engineering** — monitoring, alerting, toil reduction, and SLO-based response.
- **Observability Engineering** — telemetry strategy, high-cardinality data, and production debugging.
- **Release It! (2nd ed.)** — production patterns that should be made visible through telemetry.

### Videos & conference talks
- 🎥 **OpenTelemetry Collector production deployment talks** — sidecar vs agent vs gateway, batching, sampling, and exporter failure modes.
- 🎥 **SLO burn-rate alerting talks** — turning instrumentation into actionable pages.
- 🎥 **Telemetry cost-control talks** — cardinality, retention, sampling, and governance.

### Articles & official docs
- **OpenTelemetry Collector docs**: https://opentelemetry.io/docs/collector/
- **OpenTelemetry Collector deployment patterns**: https://opentelemetry.io/docs/collector/deployment/
- **OpenTelemetry Collector processors**: https://opentelemetry.io/docs/collector/configuration/#processors
- **OpenTelemetry security guidance**: https://opentelemetry.io/docs/security/
- **Prometheus alerting best practices**: https://prometheus.io/docs/practices/alerting/

## ❓ Senior interview questions

1. Sketch a production telemetry pipeline for a Java service.
2. What happens if the telemetry backend is down?
3. Why might adding one metric label double infrastructure cost?
4. Sidecar vs daemonset/agent vs gateway Collector — tradeoffs?
5. How do you prevent secrets from leaking into traces and logs?
6. What telemetry would you add to the telemetry pipeline itself?
7. How do sampling decisions affect incident debugging?
8. Why are metric/span/log schema changes risky?

## 🗺️ Study path

1. Draw your current or ideal telemetry pipeline and mark every queue, retry, timeout, and drop point.
2. Configure a local OpenTelemetry Collector with batch and memory-limiter processors.
3. Simulate backend outage; verify the app stays healthy and telemetry drops are visible.
4. Review one dashboard and one paging alert; identify owner, SLO link, runbook, and stale fields.
5. Rehearse Q2 as an incident-readiness story: fail-open, bounded queues, exporter metrics, and alerting on telemetry loss.

## 📝 My notes

_(your own notes while studying)_
