# Profiling: async-profiler, JFR, Flame Graphs

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Sampling vs instrumentation** profilers; **safepoint bias** — why old profilers (JVisualVM sampler, hprof) systematically lie, and how async-profiler (AsyncGetCallTrace + perf_events) avoids it.
- **async-profiler** modes: `cpu` (what's burning CPU), `alloc` (allocation profiling via TLAB sampling — find GC pressure sources), `lock` (contention), `wall` (wall-clock — find *waiting*, crucial for latency); output as **flame graphs**.
- Reading a **flame graph**: width = samples (time), y-axis = stack depth (NOT time order); spotting the wide plateaus; icicle/reversed views for "who calls the hot thing".
- **JFR (JDK Flight Recorder)**: always-on, <1% overhead, event-based (GC, allocation, locks, I/O, exceptions, custom events via `jdk.jfr.Event`); `jcmd <pid> JFR.start/dump`; analyze in **JDK Mission Control (JMC)** or `jfr print`; continuous recording in production + dump-on-incident workflow.
- CPU profile vs wall-clock profile: high CPU → cpu mode; high latency but low CPU → wall/lock mode (thread is *waiting* — DB, lock, I/O).
- Production methodology: USE/RED metrics first → profile second → fix the widest bar, re-measure. Never guess.

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 13 "Profiling" + JFR sections.
- **Systems Performance (2nd ed.)** — Brendan Gregg — Ch. 1–2 + flame-graph chapters (beyond Java, but this is where flame graphs come from).

### Videos & conference talks
- 🎥 **Andrei Pangin's async-profiler talks** — he's the author; search YouTube *async-profiler Pangin* ("Profiling in production", GeeCON/Joker) — the definitive source.
- 🎥 **"Java Flight Recorder & Mission Control"** — Marcus Hirt (JMC lead) — practical JFR/JMC walkthroughs.
- 🎥 **"Visualizing Performance with Flame Graphs"** — Brendan Gregg (USENIX) — the origin talk.
- 🎥 **"Continuous Profiling in Production"** — search Devoxx; modern APM-era practice.

### Articles & official docs
- **async-profiler GitHub (README + wiki are the docs)**: https://github.com/async-profiler/async-profiler
- **JFR event reference & docs**: https://docs.oracle.com/en/java/javase/21/jfapi/ and `jfr` tool docs.
- **Brendan Gregg — Flame Graphs page**: https://www.brendangregg.com/flamegraphs.html
- **Baeldung – "Java Flight Recorder"**: https://www.baeldung.com/java-flight-recorder-monitoring

## ❓ Senior interview questions

1. Sampling vs instrumenting profilers — tradeoffs? What is safepoint bias?
2. Service latency doubled but CPU is at 20% — which profiling mode do you reach for and why?
3. How do you read a flame graph? What does width mean; what does the x-axis NOT mean?
4. How would you find which code path is responsible for GC pressure? (alloc profiling)
5. What makes JFR cheap enough to run always-on in production?
6. Describe your end-to-end workflow for "this endpoint is slow in production."
7. How do you write and record a custom JFR event, and when would you?

## 🗺️ Study path

1. Read the async-profiler README fully; profile any Java app in `cpu` mode and open the flame graph.
2. Profile the same app in `alloc` and `wall` modes; note how different the pictures are.
3. Start a JFR recording with `jcmd`, open in JMC, explore the automated analysis tab.
4. Watch one Pangin talk + Gregg's flame-graph talk.
5. Rehearse Q6 as a confident 90-second methodology story.

## 📝 My notes

_(your own notes while studying)_
