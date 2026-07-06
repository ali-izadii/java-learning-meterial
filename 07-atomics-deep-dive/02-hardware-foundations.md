# Hardware Foundations: CPU Atomics, Cache Coherence (MESI), Memory Barriers

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **What the CPU actually provides**: x86 — `lock`-prefixed instructions (`lock cmpxchg` = CAS, `lock xadd` = fetch-and-add, `xchg`); ARM — **LL/SC** (load-linked/store-conditional: `ldxr/stxr`) and newer LSE atomics (`cas`, `ldadd`). Java's one `compareAndSet` maps to different machinery per architecture.
- **Cache coherence (MESI)**: cores don't share L1/L2; the coherence protocol (Modified/Exclusive/Shared/Invalid line states) keeps caches consistent. An atomic RMW needs the cache line in **Exclusive/Modified** state — under contention, the line **ping-pongs** between cores, and *that* (not the instruction itself) is the cost of contended atomics.
- Modern `lock` prefix ≠ bus lock: it's a **cache lock** on the line (bus locking only for split/unaligned cases). Uncontended atomic ≈ ~20 cycles; contended ≈ cache-miss latency × retry count.
- **Why reordering exists**: store buffers, out-of-order execution, compiler optimizations. x86 = TSO (strong: only store→load reorders); ARM = weak (almost everything reorders). The JMM abstracts over both — and why "it works on my x86 laptop" concurrency bugs explode on ARM/Graviton.
- **Barriers/fences** as the ordering tool: LoadLoad/StoreStore/LoadStore/StoreLoad (the expensive one — full flush of store buffer); how volatile write ≈ store + StoreLoad barrier; `VarHandle.fullFence/acquireFence/releaseFence` as the Java-level API.
- **False sharing**: two independent variables on one 64-byte cache line → coherence traffic as if contended; `@jdk.internal.vm.annotation.Contended` (JVM-internal) / manual padding; this is the bridge to LongAdder (topic 04).
- Vocabulary check: "atomicity", "visibility", "ordering" are three different problems; hardware gives you tools for each.

## 📚 Resources

### Books / chapters
- **A Primer on Memory Consistency and Cache Coherence (2nd ed.)** — Nagarajan, Sorin, Hill, Wood — free PDF from Morgan & Claypool/authors; Ch. 1–2 + MESI chapter. The academic source, surprisingly readable.
- **The Art of Multiprocessor Programming** — Appendix B (hardware) — concurrency-focused hardware summary.

### Videos & conference talks
- 🎥 **"Mythbusting Modern Hardware to Gain 'Mechanical Sympathy'"** — Martin Thompson (GOTO/QCon) — THE talk: caches, MESI, false sharing, store buffers, for JVM developers.
- 🎥 **"CPU Caches and Why You Care"** — Scott Meyers (code::dive) — the classic cache-behavior lecture; language-agnostic.
- 🎥 **"atomic<> Weapons"** — Herb Sutter (C++ and Beyond, 2 parts) — memory ordering + barriers, the deepest accessible treatment; maps 1:1 to VarHandle modes.

### Articles & official docs
- **Martin Thompson — Mechanical Sympathy blog**: https://mechanical-sympathy.blogspot.com/ — read "Write Combining", "False Sharing", "Memory Barriers/Fences".
- **Paul McKenney — "Memory Barriers: a Hardware View for Software Hackers"** — the canonical paper; search the title (free PDF).
- **JVM Anatomy Quarks** (Shipilëv): **#13 Intergenerational Barriers aside**, and his **"On the Fence with Dependencies"** post for fence semantics: https://shipilev.net/
- **Aleksey Shipilëv — "JMM Pragmatics"** (hardware sections): https://shipilev.net/blog/2014/jmm-pragmatics/

## ❓ Senior interview questions

1. What does the x86 `lock` prefix actually do on a modern CPU? (cache-line lock, not bus lock)
2. Explain MESI in 90 seconds. Why does a contended AtomicLong slow down even though every CAS "succeeds eventually"?
3. LL/SC vs CAS — how does ARM implement `compareAndSet`, and what is a spurious SC failure (why `weakCompareAndSet` exists)?
4. What is a store buffer and how does it cause a store→load reorder that surprises programmers even on x86?
5. Which of the four barrier types is the expensive one and why? Where does a volatile write need it?
6. What is false sharing? How would you detect it (perf counters, profiling) and fix it in Java?
7. Why can a concurrency bug appear on ARM (Graviton/Apple Silicon) but never on x86?
8. Uncontended vs contended atomic increment — give order-of-magnitude cost intuition and name what dominates each.

## 🗺️ Study path

1. Watch Thompson's "Mechanical Sympathy" talk (foundation for everything here).
2. Read McKenney's "Memory Barriers" paper §1–4 (skim the rest).
3. Demo: write a false-sharing benchmark (two threads incrementing adjacent `long`s in one array vs padded) with JMH — observe the ~5–10× gap.
4. Watch Sutter's "atomic<> Weapons" part 1 for the ordering model.
5. Rehearse Q2 and Q6 — hardware-aware answers are rare and score disproportionately in senior interviews.

## 📝 My notes

_(your own notes while studying)_
