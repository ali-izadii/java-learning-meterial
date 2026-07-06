# Allocation Profiling: Finding & Fixing GC Pressure

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **The metric**: allocation rate (MB/s or GB/min). Modern HotSpot handles ~1 GB/s comfortably; the symptom of excess is *minor-GC frequency* (and CPU burned in GC), not usually footprint. Formula to carry: `time between minor GCs ≈ eden size / allocation rate`.
- **How allocation profilers work** (interview differentiator): they do NOT trace every `new` (that would be instrumentation, ~10× slowdown, and EA-distorting). They **sample TLAB events** — a new-TLAB refill or outside-TLAB allocation triggers a stack capture (JFR `ObjectAllocationInNewTLAB/OutsideTLAB`, async-profiler `-e alloc` on the same hooks) — statistically weighted by *bytes*, cheap enough for production, and it doesn't disable escape analysis.
- Reading an alloc flame graph: width = bytes allocated (not time!); the usual suspects on top — `byte[]`/`char[]` (strings! serialization! buffers!), `Object[]` (collections resizing), boxing (`Long.valueOf`), iterators, streams plumbing, `BigDecimal`.
- **The fix catalog** (be ready with 8+): pre-size collections/StringBuilder (resize = allocate + copy); primitive streams / avoid boxing on hot paths; reuse buffers (careful: pooling vs GC — modern GC often beats naive pools); move formatting/logging off hot paths (`log.debug("{}", x)` args still allocate — use `isDebugEnabled` or suppliers for expensive args); prefer `ArrayList` over `LinkedList` (node-per-element); avoid defensive copies in loops; cache common immutables; watch hidden allocators (varargs, autoboxed map keys, `String.split`, regex, date formatting).
- **JMH `-prof gc`**: `gc.alloc.rate.norm` (bytes/op) — the ground truth for "did my fix work" at micro level; JFR/async-profiler for the macro level.
- **Live-set vs garbage distinction**: high allocation of *short-lived* objects = cheap-ish (die in eden — "generational hypothesis working as intended"); the real GC killers are **medium-lived objects** (survive eden → copied through survivors → promoted → die in old = the most expensive lifecycle). Premature promotion diagnosis: `-Xlog:gc+age` tenuring distribution.
- Judgment: don't "optimize allocations" blindly — clean code first, then profile, then surgically fix the top-3 byte producers. Quote: allocation is cheap, but "no allocation is cheaper than any allocation" only *where it matters*.

## 📚 Resources

### Books / chapters
- **Java Performance (2nd ed.)** — Oaks — Ch. 5–6 (GC + allocation efficiency) and the "reduce allocation" guidance scattered through.
- **Optimizing Java** — Ch. 8 (logging/monitoring) + Ch. 6.

### Videos & conference talks
- 🎥 **"The Trouble with Memory"** — Kirk Pepperdine (Devoxx) — THE talk for this exact topic: allocation-rate-driven diagnosis, memory-efficiency patterns.
- 🎥 **Andrei Pangin — async-profiler talks** — the alloc-mode sections (how TLAB sampling works, from the author).
- 🎥 **"Java Memory Efficiency"-type talks** — search *Devoxx memory efficient Java* (Chris Newland's and Pepperdine's sessions).

### Articles & official docs
- **async-profiler README — allocation profiling section**: https://github.com/async-profiler/async-profiler#allocation-profiling — explains the TLAB-hook sampling precisely.
- **JVM Anatomy Quark #4** (again — the TLAB events foundation): https://shipilev.net/jvm/anatomy-quarks/4-tlab-allocation/
- **JEP 331: Low-Overhead Heap Profiling** (`ThreadHeapSampler`, JVMTI sampled allocation — the standardized mechanism): https://openjdk.org/jeps/331
- **Baeldung – "Monitoring Java Application Allocation"**: https://www.baeldung.com/java-flight-recorder-monitoring (JFR angle).
- **JMH `-prof gc` docs/samples** — JMH sample 38 & profiler docs in the repo: https://github.com/openjdk/jmh

## ❓ Senior interview questions

1. How does an allocation profiler achieve <2% overhead in production? Why doesn't it distort escape analysis the way instrumentation does?
2. Your service spends 15% CPU in GC with sub-second minor GCs. Walk the diagnosis: what do you measure, what tool, what do you look for in the flame graph?
3. Width in an allocation flame graph means what, exactly? Top three `byte[]` producers in a typical web service?
4. Why are medium-lived objects the worst case for a generational collector? How do you *see* premature promotion?
5. Give six concrete allocation-reduction techniques and one situation where each backfires.
6. Object pooling in 2026 — when does it still win, and why does it usually lose to just allocating (GC theory answer)?
7. What does `gc.alloc.rate.norm = 0 B/op` in JMH prove, and at which compilation tier is it true?
8. Doubling eden halves minor-GC frequency — what's the catch? (pause length vs frequency, promotion timing, TLAB waste)

## 🗺️ Study path

1. Watch "The Trouble with Memory".
2. Profile a real app: `asprof -e alloc -d 60 -f alloc.html <pid>` — identify top 3 byte producers; fix one; re-profile. Do the full loop once — it's the story you'll tell in interviews.
3. Run JFR with allocation events; compare with async-profiler output.
4. In JMH, measure alloc.rate.norm for: string concat in loop vs StringBuilder pre-sized; boxed vs primitive stream sum; ArrayList with/without initial capacity.
5. Rehearse Q2 end-to-end — the production-diagnosis narrative is the most valuable artifact of this whole topic.

## 📝 My notes

_(your own notes while studying)_
