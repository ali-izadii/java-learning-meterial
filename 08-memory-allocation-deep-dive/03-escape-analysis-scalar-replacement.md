# Escape Analysis & Scalar Replacement: When `new` Allocates Nothing

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06
> **Cross-ref:** deep JIT context in [03-jit-and-performance/02-jit-optimizations.md](../03-jit-and-performance/02-jit-optimizations.md) — this file goes deeper on the allocation angle.

---

## 🎯 What a senior candidate must know

- **Escape states**: *NoEscape* (object provably confined to the compiled scope), *ArgEscape* (passed to a callee but doesn't leave the thread), *GlobalEscape* (stored to a field/static, returned, or published to another thread). Only NoEscape unlocks the big prizes.
- **Scalar replacement** (the prize): the object is never materialized — its fields become local variables/registers; no header, no heap write, no GC load. Say "**scalar replacement**", not "stack allocation" — HotSpot does not stack-allocate objects (interviewer-grade precision).
- **What EA also unlocks**: lock elision (synchronized on a non-escaping object → removed — atomics/locks tie-in) and allocation sinking/partial EA variants (Graal is notably stronger here — good currency remark).
- **Why EA is fragile — the senior payload**: EA runs on the *inlined* compilation unit. Anything that blocks inlining blocks EA: too-big methods, polymorphic/megamorphic calls, non-inlined virtual calls. Also: escaping into arrays/collections, exceptions capturing the object, debugger/JVMTI, deoptimization boundaries. Consequence — **you cannot rely on EA; measure per call site**.
- Typical wins in real code: iterator objects in for-each over `ArrayList`, small value-ish temporaries (`Point`, `Optional` in hot paths *sometimes*), boxing in arithmetic (`Integer` temporaries), varargs arrays, StringBuilder in simple concat chains.
- **How to verify** (know all three): JMH `-prof gc` → `gc.alloc.rate.norm` = 0 bytes/op; `-XX:+UnlockDiagnosticVMOptions -XX:+PrintEscapeAnalysis -XX:+PrintEliminateAllocations` (C2 debug output); allocation profiler shows the site vanish at tier 4 (vs interpreter/C1 where it still allocates!).
- Big picture: EA is HotSpot's partial answer to value types — and **Valhalla value classes** (topic 5.07) are the real fix (guaranteed flattening vs opportunistic optimization).

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 10 escape-analysis section.
- **Java Performance (2nd ed.)** — Oaks — Ch. 4 (EA flags & behavior).

### Videos & conference talks
- 🎥 **"Escape Analysis and Scalar Replacement"** — Ruslan Cheremin (Joker/JPoint; look for English slides) — the deepest dedicated talk; his blog series (below) is the written version.
- 🎥 **Doug Hawkins — "JVM Mechanics"** — the EA/inlining segment; explains the fragility brilliantly.
- 🎥 **"Maximizing Performance with GraalVM"** — Thomas Wuerthinger — partial escape analysis, the Graal advantage.

### Articles & official docs
- **JVM Anatomy Quark #18: Scalar Replacement**: https://shipilev.net/jvm/anatomy-quarks/18-scalar-replacement/ — the canonical demo with counters.
- **JVM Anatomy Quark #19: Lock Elision**: https://shipilev.net/jvm/anatomy-quarks/19-lock-elision/
- **Ruslan Cheremin — "Escape Analysis in HotSpot" blog series** (cheremin.info / dev.cheremin.info; search "cheremin escape analysis") — call-site-by-call-site empirical study of what does/doesn't get scalar-replaced.
- **Baeldung – "Escape Analysis in Java"**: https://www.baeldung.com/java-escape-analysis
- **HotSpot escape.hpp/escape.cpp comments** — the Connection Graph algorithm (Choi et al. paper reference inside).

## ❓ Senior interview questions

1. Define the three escape states and which optimizations each permits.
2. Why is "the JVM allocates objects on the stack" wrong, and what does HotSpot actually do?
3. Why does escape analysis depend on inlining? Give two concrete ways a call site loses EA.
4. The same `new Point()` allocates in one caller and not in another — how is that possible, and how do you prove which is which?
5. Interpreter, C1, C2 — where does the object allocate at each tier? What does that mean for warmup-phase allocation rate?
6. How does EA remove a `synchronized` block? Construct an example.
7. What is partial escape analysis and which compiler is known for it?
8. How does Valhalla change this story — opportunistic vs guaranteed?

## 🗺️ Study path

1. Read quark #18, run its experiment locally (JMH `-prof gc`, watch alloc.rate.norm hit 0, then flip `-XX:-DoEscapeAnalysis`).
2. Break it deliberately: store the object into a field, grow the method past inline limits, make the call polymorphic — watch allocation return each time. Keep this as your demo repo.
3. Read Cheremin's series for the catalog of surprising cases.
4. Read quark #19 (lock elision) to connect to concurrency.
5. Rehearse Q3–Q4 — the "EA is fragile, verify per site" narrative is exactly what distinguishes senior answers from blog-level ones.

## 📝 My notes

_(your own notes while studying)_
