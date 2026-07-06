# GC Logs, Heap Dumps & Memory-Leak Diagnosis

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Unified logging** (JDK 9+, JEP 158/271): `-Xlog:gc*`, `-Xlog:gc+heap=debug`, log tags/levels/decorators; there is no `-XX:+PrintGCDetails` anymore.
- Reading a GC log: pause cause, generation sizes before→after, promotion, pause time; spotting **leak signature** (old-gen sawtooth floor rising after every full GC).
- **Heap dumps**: `jcmd <pid> GC.heap_dump`, `-XX:+HeapDumpOnOutOfMemoryError`; analyzing with **Eclipse MAT** — dominator tree, retained vs shallow size, "Leak Suspects" report, path to GC roots.
- Common leak patterns: static collections, unbounded caches, `ThreadLocal` in thread pools, listener registration without removal, classloader leaks on redeploy.
- Live diagnosis tools: `jcmd`, `jmap -histo:live`, `jstat -gcutil`, **JFR** (`jcmd JFR.start`) + JDK Mission Control, async-profiler alloc mode.
- **OOM taxonomy**: `Java heap space` vs `GC overhead limit exceeded` vs `Metaspace` vs `unable to create native thread` vs container OOM-kill — each has a different cause and fix.
- Distinguish **leak vs high allocation rate vs undersized heap** — the diagnostic decision tree interviewers want to hear.

## 📚 Resources

### Books / chapters
- **Java Performance (2nd ed.)** — Scott Oaks, Ch. 5–7 (GC tuning + heap analysis).
- **Optimizing Java** — Ch. 8 "GC Logging, Monitoring, Tuning" — directly on-topic.

### Videos & conference talks
- 🎥 **"The Diabolical Developer's Guide to Performance Tuning"** — Martijn Verburg / Ben Evans (Devoxx) — methodology over flags.
- 🎥 **"Eclipse MAT: memory analysis"** — search YouTube for *Eclipse Memory Analyzer dominator tree tutorial*; also **"Troubleshooting Memory Problems"** sessions from Oracle CodeOne (Poonam Parhar's talks are excellent).
- 🎥 **JDK Mission Control / JFR intro** — Marcus Hirt's JMC talks (he wrote much of it).

### Articles & official docs
- **JEP 271: Unified GC Logging**: https://openjdk.org/jeps/271
- **Eclipse MAT docs — Dominator Tree & Leak Suspects**: https://eclipse.dev/mat/documentation/
- **GCeasy** (universal GC log analyzer, free tier): https://gceasy.io — upload a real log and study the report.
- **Baeldung – "Understanding Memory Leaks in Java"**: https://www.baeldung.com/java-memory-leaks
- **Poonam Parhar's blog (HotSpot memory troubleshooting)**: search "Poonam Parhar troubleshooting OutOfMemoryError" — the canonical OOM taxonomy write-ups.

## ❓ Senior interview questions

1. Production service OOMs every ~3 days. Walk me through your diagnosis end-to-end.
2. In MAT, what is the difference between shallow and retained size? What does the dominator tree show?
3. How do `ThreadLocal`s leak in application servers / thread pools?
4. What does `GC overhead limit exceeded` actually mean and how does it differ from plain heap-space OOM?
5. How would you capture a heap dump with minimal production impact? What are the risks of `jmap` on a live JVM?
6. What GC-log pattern distinguishes a memory leak from an undersized heap?
7. What is a classloader leak and why do hot-redeploys cause metaspace OOMs?

## 🗺️ Study path

1. Write a deliberately leaky program (static `List` + `ThreadLocal` in a pool); run with `-Xlog:gc*` and `-XX:+HeapDumpOnOutOfMemoryError`.
2. Analyze the dump in Eclipse MAT: find the leak via dominator tree, then via Leak Suspects.
3. Upload the GC log to GCeasy and interpret every section.
4. Record a 2-minute JFR flight and open it in JDK Mission Control.
5. Practice narrating question 1 as a story — it is *the* most common senior ops question.

## 📝 My notes

_(your own notes while studying)_
