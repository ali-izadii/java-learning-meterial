# JIT Compilation: Tiered Compilation, C1/C2, Deoptimization

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Execution pipeline: **interpreter → C1 (client, fast compile, light optimization + profiling) → C2 (server, slow compile, heavy optimization)**; tiers 0–4 (0 interpreter, 1–3 C1 variants, 4 C2).
- **Profile-guided**: invocation + backedge counters trigger compilation (`-XX:Tier4InvocationThreshold` etc.); **OSR (on-stack replacement)** for hot loops already running.
- **Speculative optimization & deoptimization**: C2 compiles assuming observed profile (e.g., only one receiver type, branch never taken); an **uncommon trap** invalidates code → deopt back to interpreter → possibly recompile. This is why Java can beat AOT-compiled code on steady state.
- **Monomorphic → bimorphic → megamorphic** call sites; inline caches; why interface-heavy megamorphic call sites are slow.
- Warmup: why benchmarks lie before compilation settles; why restarts hurt latency-sensitive services.
- Code cache tiers/segments; `-XX:+PrintCompilation`, `-XX:+UnlockDiagnosticVMOptions -XX:+PrintInlining` — how to observe the JIT.
- Currency signals: **Graal JIT**, and **Project Leyden** (AOT cache, JEP 483 AOT class loading & linking) for startup/warmup.

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 9–10 (JIT + code cache) — best book coverage of exactly this.
- **Java Performance (2nd ed.)** — Scott Oaks, Ch. 4 "Working with the JIT Compiler".

### Videos & conference talks
- 🎥 **"JVM Mechanics"** — Doug Hawkins (SF JUG / Devoxx, ~3 parts) — the legendary deep-dive on tiering, inlining, deopt; the single best video on this topic.
- 🎥 **"A JVM Does That???"** — Cliff Click (creator of C2) — mind-expanding on what the JIT really does.
- 🎥 **"The Black Magic of (Java) Method Dispatch"** — Aleksey Shipilëv — mono/bi/megamorphic dispatch, article + talk.

### Articles & official docs
- **Shipilëv — "The Black Magic of Method Dispatch"**: https://shipilev.net/blog/2015/black-magic-method-dispatch/
- **JVM Anatomy Quarks** — compilation-related quarks (e.g., #2 transparent huge pages aside, look for lock coarsening/loop quarks): https://shipilev.net/jvm/anatomy-quarks/
- **Baeldung – "Tiered Compilation in JVM"**: https://www.baeldung.com/jvm-tiered-compilation
- **JITWatch** (Chris Newland) — GUI over `-XX:+LogCompilation`: https://github.com/AdoptOpenJDK/jitwatch — the hands-on tool.
- **JEP 483: Ahead-of-Time Class Loading & Linking (Leyden)**: https://openjdk.org/jeps/483

## ❓ Senior interview questions

1. Describe the journey of a hot method from first invocation to fully optimized code.
2. What is deoptimization? Give two concrete triggers (new class loaded violating CHA, uncommon branch taken, ...).
3. What's the difference between C1 and C2 and why have both?
4. What is OSR and when does it matter?
5. Mono/bi/megamorphic call sites — what changes in generated code, and what would you refactor if a profiler shows a megamorphic hot path?
6. Why does a JVM service need warmup, and what are current approaches to eliminate it (Leyden AOT cache, CRaC, ReadyNow-style)?
7. What happens when the code cache fills up?

## 🗺️ Study path

1. Watch Doug Hawkins' "JVM Mechanics" (block an evening — it replaces weeks of reading).
2. Run something with `-XX:+PrintCompilation` and identify tier transitions and `made not entrant` (deopt) lines.
3. Read Shipilëv's method-dispatch post.
4. Play with JITWatch on a small benchmark.
5. Read Optimizing Java Ch. 9–10 to consolidate; rehearse Q1 as a narrative.

## 📝 My notes

_(your own notes while studying)_
