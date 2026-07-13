# ☕ Java In-Depth — Senior Interview Roadmap

> Goal: master **Java internals** and be ready for **senior Java engineer interviews**.
> Every topic has its own `.md` study guide. Check ✔ when you can answer its interview questions out loud, without notes.

**Legend:** 🟢 Fundamental (must-know) · 🟡 Deep-dive (differentiates seniors) · 🔴 Expert (impressive bonus)

---

## 1. JVM Architecture & Memory

- [ ] 🟢 [JVM Runtime Data Areas](01-jvm-architecture-and-memory/01-jvm-runtime-data-areas.md)
- [ ] 🟢 [Object Layout in Memory](01-jvm-architecture-and-memory/02-object-layout-in-memory.md)
- [ ] 🟢 [Garbage Collection Fundamentals](01-jvm-architecture-and-memory/03-garbage-collection-fundamentals.md)
- [ ] 🟡 [Modern Garbage Collectors (G1, ZGC, Shenandoah)](01-jvm-architecture-and-memory/04-modern-garbage-collectors.md)
- [ ] 🟡 [GC Logs, Heap Dumps & Leak Diagnosis](01-jvm-architecture-and-memory/05-gc-logs-heap-dumps-leak-diagnosis.md)
- [ ] 🔴 [Reference Types & Cleaner](01-jvm-architecture-and-memory/06-reference-types-and-cleaner.md)

## 2. Class Loading & Bytecode

- [ ] 🟢 [ClassLoaders](02-classloading-and-bytecode/01-classloaders.md)
- [ ] 🟡 [Bytecode & the Class File Format](02-classloading-and-bytecode/02-bytecode-and-classfile-format.md)
- [ ] 🔴 [invokedynamic & Lambdas](02-classloading-and-bytecode/03-invokedynamic-and-lambdas.md)
- [ ] 🔴 [Reflection, Proxies & Agents](02-classloading-and-bytecode/04-reflection-proxies-agents.md)

## 3. JIT & Performance

- [ ] 🟡 [JIT & Tiered Compilation](03-jit-and-performance/01-jit-tiered-compilation.md)
- [ ] 🟡 [JIT Optimizations](03-jit-and-performance/02-jit-optimizations.md)
- [ ] 🟡 [Benchmarking with JMH](03-jit-and-performance/03-benchmarking-with-jmh.md)
- [ ] 🔴 [Profiling: JFR & async-profiler](03-jit-and-performance/04-profiling-jfr-async-profiler.md)

## 4. Concurrency & the Java Memory Model

- [ ] 🟢 [Threads & Lock Internals](04-concurrency-and-jmm/01-threads-and-lock-internals.md)
- [ ] 🟢 [Java Memory Model](04-concurrency-and-jmm/02-java-memory-model.md)
- [ ] 🟢 [java.util.concurrent](04-concurrency-and-jmm/03-java-util-concurrent.md)
- [ ] 🟡 [Concurrent Collections Internals](04-concurrency-and-jmm/04-concurrent-collections-internals.md)
- [ ] 🟡 [ForkJoinPool & Parallel Streams](04-concurrency-and-jmm/05-forkjoin-and-parallel-streams.md)
- [ ] 🟢 [Virtual Threads (Project Loom)](04-concurrency-and-jmm/06-virtual-threads-project-loom.md)

## 5. Language & Collections Internals

- [ ] 🟢 [HashMap Internals](05-language-and-collections-internals/01-hashmap-internals.md)
- [ ] 🟢 [Collections Framework Internals](05-language-and-collections-internals/02-collections-framework-internals.md)
- [ ] 🟢 [String Internals](05-language-and-collections-internals/03-string-internals.md)
- [ ] 🟢 [Generics Deep-Dive](05-language-and-collections-internals/04-generics-deep-dive.md)
- [ ] 🟡 [Streams & Lambdas Internals](05-language-and-collections-internals/05-streams-and-lambdas-internals.md)
- [ ] 🟡 [Modern Java Features](05-language-and-collections-internals/06-modern-java-features.md)
- [ ] 🔴 [Project Valhalla](05-language-and-collections-internals/07-project-valhalla.md)

## 6. Ecosystem & Cross-Cutting

- [ ] 🟡 [Java Modules (JPMS)](06-ecosystem-and-cross-cutting/01-java-modules-jpms.md)
- [ ] 🟡 [Serialization](06-ecosystem-and-cross-cutting/02-serialization.md)
- [ ] 🟡 [Exceptions Internals](06-ecosystem-and-cross-cutting/03-exceptions-internals.md)
- [ ] 🔴 [Native Interop: JNI → Panama/FFM](06-ecosystem-and-cross-cutting/04-jni-panama-ffm.md)
- [ ] 🔴 [GraalVM & Native Image](06-ecosystem-and-cross-cutting/05-graalvm-native-image.md)

## 7. Atomics Deep-Dive

- [ ] 🟢 [Atomic Classes & CAS Fundamentals](07-atomics-deep-dive/01-atomic-classes-and-cas.md)
- [ ] 🟡 [Hardware Foundations: CPU Atomics, MESI, Barriers](07-atomics-deep-dive/02-hardware-foundations.md)
- [ ] 🟡 [ABA Problem, Field Updaters & Atomic Arrays](07-atomics-deep-dive/03-aba-field-updaters-atomic-arrays.md)
- [ ] 🟡 [LongAdder, LongAccumulator & Striped64](07-atomics-deep-dive/04-longadder-striped64-contention.md)
- [ ] 🔴 [VarHandles & Memory Access Modes](07-atomics-deep-dive/05-varhandles-and-memory-modes.md)
- [ ] 🔴 [Lock-Free Algorithms: Treiber Stack, Michael-Scott Queue](07-atomics-deep-dive/06-lock-free-algorithms.md)

## 8. Memory Allocation Deep-Dive

- [ ] 🟢 [The Object Allocation Path: What `new` Actually Does](08-memory-allocation-deep-dive/01-object-allocation-path.md)
- [ ] 🟡 [TLAB Internals: Sizing, Refills, Waste & Tuning](08-memory-allocation-deep-dive/02-tlab-internals.md)
- [ ] 🟡 [Escape Analysis & Scalar Replacement](08-memory-allocation-deep-dive/03-escape-analysis-scalar-replacement.md)
- [ ] 🟡 [Allocation Profiling: Finding & Fixing GC Pressure](08-memory-allocation-deep-dive/04-allocation-profiling-and-gc-pressure.md)
- [ ] 🔴 [Off-Heap: Direct Buffers, Unsafe, MemorySegment](08-memory-allocation-deep-dive/05-off-heap-direct-memory-native.md)
- [ ] 🟡 [Heap Sizing, Container Awareness & the OOM Taxonomy](08-memory-allocation-deep-dive/06-heap-sizing-containers-oom.md)

## 9. Instrumentation

- [ ] 🟢 [Instrumentation Fundamentals & Observability Signals](09-instrumentation/01-instrumentation-fundamentals.md)
- [ ] 🟢 [Metrics with Micrometer & Prometheus](09-instrumentation/02-metrics-with-micrometer-prometheus.md)
- [ ] 🟡 [Distributed Tracing with OpenTelemetry](09-instrumentation/03-distributed-tracing-opentelemetry.md)
- [ ] 🔴 [Java Agents & Bytecode Instrumentation](09-instrumentation/04-java-agents-bytecode-instrumentation.md)
- [ ] 🟡 [Production Telemetry Pipelines & Safety](09-instrumentation/05-production-telemetry-pipelines-safety.md)

---

## Core bookshelf (referenced across topics)

- **Java Concurrency in Practice** — Brian Goetz. Still *the* concurrency interview book.
- **Effective Java (3rd ed.)** — Joshua Bloch. Senior interviewers quote items from it directly.
- **Optimizing Java** — Evans, Gough, Newland. Best practical JVM performance book.
- **The Well-Grounded Java Developer (2nd ed.)** — Evans & Clark. Modern Java in context.
- **The Java® Virtual Machine Specification** — the ground truth; skim per topic, don't read cover to cover.

## People & sites worth following

- **Aleksey Shipilëv** — JVM performance, JMM, GC (shipilev.net)
- **Brian Goetz** — Java language architect (talks on Valhalla, Loom, Amber)
- **Nicolai Parlog** — modern Java features (nipafx.dev)
- **Inside Java** (inside.java) — official OpenJDK engineering blog, podcast & newscast
- **Baeldung** (baeldung.com) — practical reference articles
