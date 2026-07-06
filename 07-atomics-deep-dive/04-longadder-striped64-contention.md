# LongAdder, LongAccumulator & Striped64: Beating Contention by Striping

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **The problem**: a hot AtomicLong under N writer threads serializes on one cache line — throughput *drops* as threads increase (CAS retry storms + line ping-pong from topic 02).
- **The idea — strip the hot spot**: `LongAdder` = a `base` field + a lazily grown array of **`Cell`s** (padded counters). Uncontended → CAS `base`; on contention → each thread hashes (via its **probe** value) to its own cell and CASes there. Writes scale near-linearly; **`sum()` = base + Σ cells, weakly consistent snapshot** (may miss concurrent updates; NOT a linearizable read).
- **`Striped64`** (the shared parent, worth reading — ~400 lines): cell array grows on observed contention (CAS failure → retry different cell → expand up to ≥ CPU count); cells created lazily under a spinlock (`cellsBusy`); `@Contended` on Cell to prevent false sharing between adjacent cells.
- **The tradeoff**: writes cheap and scalable; reads (`sum`) O(cells) and non-atomic; memory footprint = cells × padded 128+ bytes. Therefore: **write-heavy, read-rarely metrics/counters → LongAdder; read-often or need CAS/exact point-in-time → AtomicLong**.
- **`LongAccumulator`** = generalized: any **associative, commutative, side-effect-free** function + identity (max, min, sum with weights); why non-commutative functions break it (cells applied in arbitrary order).
- `DoubleAdder/DoubleAccumulator` exist; note FP non-associativity caveats.
- Where it's used: `ConcurrentHashMap.size()` counter cells (topic 4.04 link), Micrometer/Dropwizard metrics counters.
- Interview framing: this is **the** case study for "reduce contention by sharding state" — the same idea as striped locks, per-CPU counters in the Linux kernel, and sharded databases.

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Ch. 11 (performance & scalability: contention costs, lock striping — the conceptual frame; LongAdder postdates the book but embodies its Ch. 11 advice).

### Videos & conference talks
- 🎥 **"Adventures with concurrent programming in Java: A quest for predictable latency"** — Martin Thompson (many recordings) — contended-counter benchmarks & queue designs; shows exactly why striping wins.
- 🎥 José Paumard's j.u.c deep dives cover LongAdder — search *Paumard LongAdder*.

### Articles & official docs
- **`LongAdder` javadoc** — precise on the weak-consistency of `sum()`: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/atomic/LongAdder.html
- **`Striped64` source + its class comment** — open in IDE; the comment is a design doc explaining probe hashing, cell growth, and padding. This is *the* primary resource for this topic.
- **Baeldung – "LongAdder and LongAccumulator in Java"**: https://www.baeldung.com/java-longadder-and-longaccumulator
- **Shipilëv — false-sharing & @Contended material** (JVM Anatomy / blog): https://shipilev.net/ — for why Cell padding matters.
- **Concurrency-interest mailing list archives** — Doug Lea's original LongAdder discussions (search "LongAdder concurrency-interest") for design rationale.

## ❓ Senior interview questions

1. Why does AtomicLong throughput *degrade* with more writer threads? Explain at the cache-coherence level.
2. Walk through LongAdder's `add()` path: uncontended, first contention, steady-state contended.
3. Why is `sum()` weakly consistent? Construct a scenario where it returns a value that was never the "true" total at any instant.
4. When is AtomicLong the *right* choice over LongAdder? (compareAndSet needs, exact reads, low write contention, memory)
5. Why is each Cell padded/@Contended? What would happen without it?
6. What constraints must a LongAccumulator function satisfy and why? Give a valid (max) and invalid (subtraction) example.
7. How does ConcurrentHashMap count its size, and why doesn't it use a single AtomicLong?
8. Generalize: you have a hot shared data point in a service — name three striping/sharding strategies inspired by this design.

## 🗺️ Study path

1. Read the `Striped64` class comment + skim its code (1 hour, the core of this topic).
2. JMH benchmark: 1/2/4/8/16 threads incrementing AtomicLong vs LongAdder; plot both curves — the crossing point is your interview exhibit.
3. Read the LongAdder/LongAccumulator javadoc; write a max-tracking LongAccumulator.
4. Revisit your false-sharing demo from topic 02 — connect it to Cell padding.
5. Rehearse Q1–Q2 as one continuous story: "why single-line contention kills, and how striping fixes it."

## 📝 My notes

_(your own notes while studying)_
