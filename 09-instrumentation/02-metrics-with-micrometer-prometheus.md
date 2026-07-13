# Metrics with Micrometer & Prometheus

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-13

---

## 🎯 What a senior candidate must know

- **Metric types**: counter (monotonic events), gauge (current value), timer (duration + count), distribution summary (non-time sizes), histogram (bucketed distribution), long-task timer (in-flight duration). Pick by semantics, not by dashboard shape.
- **Counters are rates in queries**: raw counters only go up and reset on restart. Use PromQL `rate()`/`increase()` for request rate, error rate, bytes/sec, retries/sec.
- **Gauges can go up and down**: queue size, in-flight requests, pool usage, open connections, cache size. Never use `rate()` on a gauge.
- **Timers and histograms**: averages hide tail latency. For SLOs, prefer histogram buckets and query percentiles with `histogram_quantile`, or use backend-native distributions when available.
- **Micrometer as facade**: common in Spring Boot; instruments to Prometheus, OTLP, Datadog, CloudWatch, and others. Know `MeterRegistry`, `Counter`, `Timer`, `Gauge`, `DistributionSummary`, tags, common tags, and Actuator `/actuator/prometheus`.
- **Prometheus naming**: base units, `_total` for counters, `_seconds` for durations, `_bytes` for byte quantities, stable names, and labels for bounded dimensions.
- **Cardinality discipline**: label values must be bounded and low-ish. Route templates (`/orders/{id}`) are okay; raw paths (`/orders/123`) are not. Exception class is usually okay; exception message is not.
- **Business and technical metrics**: request metrics are table stakes; seniors also instrument domain outcomes: payments authorized, fraud rejects, import rows processed, cache hit/miss, retry exhaustion, queue lag.
- **Alertable metrics**: alerts should map to user pain or imminent saturation. Prefer burn-rate alerts on SLOs over single-threshold dashboards.
- **Testing metrics**: assert that important operations emit expected meter names/tags; smoke-test Prometheus scraping; guard against missing default zero values for known label sets.

## 📚 Resources

### Books / chapters
- **Site Reliability Engineering** — Ch. 6 "Monitoring Distributed Systems" and Ch. 4 "Service Level Objectives".
- **Observability Engineering** — chapters on telemetry data and high-cardinality analysis.

### Videos & conference talks
- 🎥 **"Prometheus: Designing and Instrumenting Applications"** — conference talks by Prometheus maintainers are concise and practical.
- 🎥 **Micrometer and Spring Boot Actuator talks** — useful for Java/Spring production conventions.
- 🎥 **SLO burn-rate alerting talks** — connect metrics to operational response.

### Articles & official docs
- **Micrometer reference — Concepts**: https://docs.micrometer.io/micrometer/reference/concepts.html
- **Prometheus — Instrumentation best practices**: https://prometheus.io/docs/practices/instrumentation/
- **Prometheus — Metric and label naming**: https://prometheus.io/docs/practices/naming/
- **Prometheus — Histograms and summaries**: https://prometheus.io/docs/practices/histograms/
- **Spring Boot Actuator metrics**: https://docs.spring.io/spring-boot/reference/actuator/metrics.html

## ❓ Senior interview questions

1. Counter vs gauge vs timer vs histogram — give a production example of each.
2. Why is average latency usually a bad SLO metric?
3. How would you instrument a queue consumer?
4. What makes a Prometheus label high cardinality? Why does it hurt?
5. What is the difference between `http.server.requests` in Micrometer and a custom business metric?
6. How do you query error rate from counters that reset on restart?
7. What metrics would you add around a database connection pool or thread pool?
8. How do you decide whether an alert belongs on a metric?

## 🗺️ Study path

1. Add a `Counter` and `Timer` to a small Java/Spring endpoint; expose them through Actuator Prometheus output.
2. Write PromQL for request rate, error ratio, p95 latency, and queue depth.
3. Create two bad labels (`user_id`, raw path), then replace them with bounded labels.
4. Instrument a cache with hit/miss counters and latency timer; derive hit ratio.
5. Rehearse Q3 with concrete metrics: consumed count, failure count, lag, processing duration, queue depth, retries, dead-letter count.

## 📝 My notes

_(your own notes while studying)_
