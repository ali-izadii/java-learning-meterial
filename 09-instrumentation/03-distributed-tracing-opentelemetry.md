# Distributed Tracing with OpenTelemetry

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-13

---

## 🎯 What a senior candidate must know

- **Trace model**: a trace represents one logical operation; spans represent timed units of work; parent/child relationships model causality; attributes add searchable dimensions; events mark notable moments inside a span.
- **Context propagation**: trace context must cross HTTP, messaging, async callbacks, thread pools, virtual threads, and reactive pipelines. Missing propagation turns one request into disconnected traces.
- **W3C Trace Context**: `traceparent` and `tracestate` are the standard HTTP propagation headers. Know the shape conceptually: trace id, parent span id, flags.
- **OpenTelemetry pieces**: API (what application/library code calls), SDK (sampling/export/config), instrumentation libraries, Java agent, OTLP exporter, Collector, backend.
- **Auto-instrumentation vs manual spans**: the Java agent captures framework edges such as HTTP, JDBC, messaging, and common clients. Manual spans should add domain operations and attributes that a generic agent cannot infer.
- **Sampling**: head sampling makes the decision early and is cheap; tail sampling decides after seeing the trace and can keep slow/error traces but needs collector/backend support and buffering.
- **Trace-log correlation**: put `trace_id` and `span_id` into MDC/log context so a log line can jump to the request trace and the trace can explain surrounding logs.
- **Span naming and attributes**: names should be stable and low-cardinality (`GET /orders/{id}`, `PaymentService.authorize`), not raw URLs, SQL with literals, or customer data.
- **Async and messaging gotchas**: a message producer span and consumer span are often linked across time rather than a simple synchronous child; record queue name/topic, message id when safe, consumer lag, and retry/dead-letter outcome.
- **Privacy and security**: traces can leak headers, SQL, request bodies, user identifiers, and tokens. Redaction and attribute allowlists are part of instrumentation design.

## 📚 Resources

### Books / chapters
- **Distributed Systems Observability** — Cindy Sridharan — practical tracing, metrics, and debugging in distributed systems.
- **Observability Engineering** — high-cardinality event thinking and tracing tradeoffs.

### Videos & conference talks
- 🎥 **OpenTelemetry Java instrumentation talks** — focus on auto-instrumentation, context propagation, and the Collector.
- 🎥 **Distributed tracing talks by Cindy Sridharan** — strong mental model for where tracing helps and where it does not.
- 🎥 **W3C Trace Context / OpenTelemetry Collector talks** — useful for propagation and pipeline architecture.

### Articles & official docs
- **OpenTelemetry Java docs**: https://opentelemetry.io/docs/languages/java/
- **OpenTelemetry Java agent**: https://opentelemetry.io/docs/zero-code/java/agent/
- **OpenTelemetry sampling**: https://opentelemetry.io/docs/concepts/sampling/
- **W3C Trace Context**: https://www.w3.org/TR/trace-context/
- **OpenTelemetry semantic conventions**: https://opentelemetry.io/docs/specs/semconv/

## ❓ Senior interview questions

1. What is a span? What fields make it useful?
2. Why does tracing often fail around thread pools, messaging, or reactive code?
3. Head sampling vs tail sampling — tradeoffs?
4. What should a good HTTP server span name look like?
5. How does the OpenTelemetry Java agent produce spans without code changes?
6. How do you correlate a production error log with a trace?
7. What sensitive data can accidentally leak into traces?
8. When would you add manual spans even though auto-instrumentation is enabled?

## 🗺️ Study path

1. Run a Java service with the OpenTelemetry Java agent and export traces to a local collector or console exporter.
2. Add one manual span around a business operation and attach safe attributes.
3. Break context propagation through an executor or async callback; observe the disconnected trace; fix it.
4. Add MDC trace/span ids to logs and verify log-to-trace navigation.
5. Rehearse Q3 with a concrete example: high-throughput service with rare slow failures.

## 📝 My notes

_(your own notes while studying)_
