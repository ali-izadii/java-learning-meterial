# Modern Collectors: G1, ZGC, Shenandoah — Tradeoffs & Tuning

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **G1 (default)**: region-based, collects a *set* of regions per cycle chosen by garbage-first heuristic; pause-time goal (`-XX:MaxGCPauseMillis`, default 200 ms); young + mixed collections; humongous objects (> 50% region); remembered sets; evacuation failure ("to-space exhausted").
- **ZGC**: concurrent, **colored pointers + load barriers**, sub-millisecond pauses independent of heap size; **generational ZGC** (JEP 439, default generational since JDK 23) — know why generational mattered (allocation stall fix, lower CPU).
- **Shenandoah**: concurrent compaction via **Brooks pointers (historically) / load-reference barriers**; similar goals to ZGC, different lineage (Red Hat).
- **Tradeoff triangle: throughput vs latency vs footprint** — Parallel GC still wins pure throughput/batch; ZGC/Shenandoah trade CPU + memory overhead for latency; G1 is the balanced default.
- When to pick which — the *actual* senior question: batch/ETL → Parallel; typical service → G1; latency-critical, large heap → (generational) ZGC.
- Tuning philosophy: set heap size + pause goal, **stop** — most G1 micro-tuning is counterproductive. Know the 2–3 flags per collector, not 50.

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 7 (G1, Shenandoah, C4 comparisons).
- **The Garbage Collection Handbook (2nd ed.)** — chapters on concurrent & region-based GC for theory depth.

### Videos & conference talks
- 🎥 **"G1: To Infinity and Beyond"** / any G1 deep-dive by **Thomas Schatzl** (G1 lead, JVMLS/FOSDEM) — authoritative G1 internals.
- 🎥 **"The Z Garbage Collector"** — Per Lidén / Stefan Karlsson (Jfokus/Devoxx) and **"Generational ZGC"** — Erik Österlund (JVMLS, on the *Java* channel).
- 🎥 **"Shenandoah GC: The Next Generation"** — Roman Kennke / Aleksey Shipilëv (Devoxx/FOSDEM).
- 🎥 **"Choosing the Right Garbage Collector"** — search *Java* channel / Inside Java for the comparison episodes.

### Articles & official docs
- **Oracle GC Tuning Guide — G1 + ZGC chapters**: https://docs.oracle.com/en/java/javase/21/gctuning/
- **JEP 439: Generational ZGC**: https://openjdk.org/jeps/439 — read the "motivation" section carefully; it's a perfect interview answer.
- **Shenandoah wiki**: https://wiki.openjdk.org/display/shenandoah/Main — maintained by the GC team, surprisingly readable.
- **Inside Java GC posts**: https://inside.java/tag/gc
- **Baeldung – "Introduction to ZGC" / "G1 Garbage Collector"**: https://www.baeldung.com/jvm-zgc-garbage-collector

## ❓ Senior interview questions

1. How does G1 differ fundamentally from the parallel collector? What does "garbage-first" mean?
2. What are humongous objects in G1 and why are they a problem?
3. How does ZGC achieve <1 ms pauses on a 1 TB heap? Explain colored pointers and load barriers at a high level.
4. Why did ZGC become generational? What problem did single-generation ZGC hit?
5. You have a batch job maximizing throughput — which collector and why?
6. What is "to-space exhausted" in G1 logs and how do you respond?
7. G1's `MaxGCPauseMillis` is a goal, not a guarantee — how does G1 try to meet it?
8. Compare ZGC and Shenandoah in one minute.

## 🗺️ Study path

1. Read the Oracle tuning guide's G1 chapter, then ZGC chapter.
2. Read JEP 439 (generational ZGC).
3. Run the same load test with `-XX:+UseParallelGC`, `-XX:+UseG1GC`, `-XX:+UseZGC` and compare `-Xlog:gc` output — this hands-on comparison is unforgettable in interviews.
4. Watch the Schatzl G1 talk and the Österlund generational ZGC talk.
5. Write your own one-page "which collector when" cheat sheet in My Notes.

## 📝 My notes

_(your own notes while studying)_
