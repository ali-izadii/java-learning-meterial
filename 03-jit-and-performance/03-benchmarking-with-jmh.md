# Benchmarking Done Right: JMH, Warmup, Common Pitfalls

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Why naive `System.nanoTime()` loops lie: **warmup/mixed-tier measurement, dead-code elimination, constant folding, loop unrolling artifacts, on-stack replacement quirks, coordinated omission**.
- **JMH** structure: `@Benchmark`, `@State` (Scope.Benchmark/Thread), `@Setup/@TearDown`, `@Warmup`/`@Measurement`, forks (why multiple forks matter — run-to-run JIT nondeterminism), modes (Throughput, AverageTime, SampleTime).
- **Blackhole.consume()** and returning values — defeating dead-code elimination; using non-final random state to defeat constant folding.
- Benchmarking is *experiment design*: fix CPU frequency scaling, isolate cores, question every result, compare distributions not single numbers.
- Microbenchmark vs system benchmark — when JMH is the wrong tool; **coordinated omission** in latency measurement (why "average latency" hides disasters; hiccup-style measurement, HdrHistogram).
- Reading JMH output: score ± error (confidence interval), units; `perfasm` profiler for instruction-level truth.

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 5 "Microbenchmarking and Statistics" — exactly this topic, including the statistics.
- **Java Performance (2nd ed.)** — Oaks, Ch. 2 "An Approach to Performance Testing".

### Videos & conference talks
- 🎥 **"Benchmarking: You're Doing It Wrong"** — Aleksey Shipilëv (JMH author) — mandatory; multiple versions exist, watch the longest.
- 🎥 **"The Art of Benchmarking"** / JMH talks — Shipilëv again (Devoxx, JVMLS).
- 🎥 **"How NOT to Measure Latency"** — Gil Tene (creator of HdrHistogram) — THE coordinated-omission talk; senior gold.

### Articles & official docs
- **JMH official samples** — the real documentation; ~40 annotated benchmarks each teaching one pitfall: https://github.com/openjdk/jmh/tree/master/jmh-samples/src/main/java/org/openjdk/jmh/samples — *work through 01–12 minimum*.
- **JMH project page**: https://openjdk.org/projects/code-tools/jmh/
- **Baeldung – "Microbenchmarking with Java (JMH)"**: https://www.baeldung.com/java-microbenchmark-harness
- **Shipilëv — "JMH vs Caliper: reference thread"** and his blog generally for benchmarking methodology: https://shipilev.net/

## ❓ Senior interview questions

1. Why is `long t = System.nanoTime(); for(...){...}; elapsed = ...` an invalid benchmark? Name four distinct reasons.
2. What does JMH's Blackhole do and why is it needed?
3. Why does JMH run multiple forks? What varies between forks?
4. What is coordinated omission and how does it corrupt latency percentiles?
5. Your benchmark shows operation X is "3ns" — faster than a memory access. What probably happened?
6. Median vs p99 vs max latency — which do you report for a user-facing service and why?
7. How would you benchmark two `HashMap` sizes fairly? (state, randomized keys, avoiding constant folding)

## 🗺️ Study path

1. Watch Shipilëv's "Benchmarking: You're Doing It Wrong".
2. Set up JMH via the archetype; write one deliberately broken benchmark (no Blackhole) and watch DCE produce nonsense; fix it.
3. Work through JMH samples 01–12 (then 13–38 over time).
4. Watch Gil Tene's "How NOT to Measure Latency".
5. Rehearse Q1 — listing the failure modes fluently is a strong senior signal.

## 📝 My notes

_(your own notes while studying)_
