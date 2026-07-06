# JIT Optimizations: Inlining, Escape Analysis, Intrinsics

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Inlining is the gateway optimization** — it enables everything else; limits: bytecode size thresholds (`MaxInlineSize` ~35 bytes hot ~325 `FreqInlineSize`), call-site polymorphism, inlining depth. "Small methods are JIT-friendly" is *engineering* advice, not style advice.
- **Escape analysis**: if an object doesn't escape (thread-local, not stored/returned), C2 can do **scalar replacement** (explode object into registers — no allocation at all) and **lock elision** (remove synchronization on non-escaping objects). NOT "stack allocation" literally — say *scalar replacement* in interviews.
- **Intrinsics**: methods the JIT replaces with hand-written machine code (`System.arraycopy`, `Math.*`, `String.indexOf`, CRC32, AES, `Unsafe.compareAndSetInt`) — why calling the "obvious" JDK method beats hand-rolling.
- Loop optimizations: unrolling, **loop-invariant code motion**, range-check elimination, auto-vectorization (SIMD) — and the explicit **Vector API (JEP 508 family)** as the modern escape hatch.
- **Lock coarsening** and biased-locking history; dead code elimination — why naive microbenchmarks measure nothing (JIT deletes unused results).
- Class hierarchy analysis (CHA) + devirtualization: `final` isn't needed for inlining virtual calls when only one implementor is loaded.

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 10 "Understanding JIT Compilation" (inlining/EA sections).
- **Java Performance (2nd ed.)** — Oaks, Ch. 4 (escape analysis, inlining flags).

### Videos & conference talks
- 🎥 **"JVM Mechanics"** — Doug Hawkins — part 2/3 covers inlining & deopt in depth (same series as previous topic — watch once, credit twice).
- 🎥 **"Escape Analysis in the HotSpot JIT Compiler"** — search YouTube (Ruslan Cheremin's *"Escape Analysis and Scalar Replacement"* talks are excellent if you find them; else the JVMLS material).
- 🎥 **"Java and the modern CPU"** talks — Martin Thompson's *"Mythbusting Modern Hardware to Gain 'Mechanical Sympathy'"* — the hardware context that makes these optimizations make sense.

### Articles & official docs
- **JVM Anatomy Quarks** (Shipilëv): **#18 Scalar Replacement**, **#19 Lock Elision**, **#27 Compiler Blackholes** — https://shipilev.net/jvm/anatomy-quarks/ — precisely on topic, with runnable proofs.
- **JITWatch wiki on inlining** — shows real inlining decisions from your code: https://github.com/AdoptOpenJDK/jitwatch/wiki
- **Baeldung – "Escape Analysis in Java"**: https://www.baeldung.com/java-escape-analysis
- **Vector API JEP (latest incubator/final)**: https://openjdk.org/jeps/508

## ❓ Senior interview questions

1. Why is inlining called the "mother of all optimizations" in the JVM?
2. Explain escape analysis and scalar replacement. Why is "the JVM stack-allocates objects" technically wrong?
3. When can the JVM remove a `synchronized` block entirely? (lock elision via EA; coarsening for adjacent blocks)
4. What is an intrinsic? Give three examples and the practical takeaway.
5. A hot loop over `list.get(i)` on an `ArrayList` — name the optimizations that make this nearly free (inlining, range-check elimination, LICM).
6. Does marking a method `final` make it faster? Discuss CHA and devirtualization honestly.
7. Why do results that aren't consumed make a benchmark meaningless?

## 🗺️ Study path

1. Read Shipilëv quarks #18, #19, #27 (short, with experiments you can run).
2. Run a tiny allocation loop with `-XX:-DoEscapeAnalysis` vs default; measure with `-verbose:gc` — see allocations vanish.
3. Use `-XX:+UnlockDiagnosticVMOptions -XX:+PrintInlining` on a hot method; read the decisions ("too large", "inline (hot)").
4. Watch Thompson's mechanical sympathy talk for the hardware frame.
5. Rehearse Q2 with the correct "scalar replacement" vocabulary.

## 📝 My notes

_(your own notes while studying)_
