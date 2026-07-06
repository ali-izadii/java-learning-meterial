# Heap Sizing, Container Awareness & the OOM Taxonomy

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Heap sizing fundamentals**: `-Xms/-Xmx` (and why prod often sets them equal — no resize hiccups, honest capacity), young-gen sizing (`-Xmn`, `NewRatio`, or better: leave it to G1's pause-goal heuristics), `SurvivorRatio`, `MaxTenuringThreshold` — know what each does, and that modern advice is "set Xmx, maybe Xms, stop".
- **Ergonomics**: how the JVM picks defaults — "server class" machine → max heap = 1/4 of RAM (default `MaxRAMPercentage=25`), initial = 1/64; GC chosen by CPU/RAM (G1 default; SerialGC on tiny containers <2 cores/<2GB — a nasty surprise worth knowing).
- **Container awareness** (`UseContainerSupport`, on by default since JDK 10; JDK 8u191+ backport): JVM reads **cgroup** limits (v1 vs v2 — JDK 15+ for proper v2) instead of host RAM; `-XX:MaxRAMPercentage` (e.g., 50–75%) instead of hardcoded Xmx in containers; CPU limits also shape GC/JIT/FJP thread counts via `availableProcessors` — and the cpu-shares vs cpu-quota gotchas.
- **The RSS budget equation** (the senior answer to "how much memory does my pod need"): heap + metaspace + code cache + (threads × stack) + direct buffers + GC overhead (~5–10% for G1, more for ZGC) + malloc/JNI + glibc arena slack. Rule of thumb: heap ≈ 50–75% of container limit, verified with NMT + RSS monitoring.
- **The OOM taxonomy** (rapid-fire fluency expected): `Java heap space` · `GC overhead limit exceeded` (98% GC time, <2% reclaimed) · `Metaspace` · `Direct buffer memory` · `unable to create native thread` (thread/stack exhaustion — often ulimit/cgroup pids, not RAM) · `Requested array size exceeds VM limit` · **cgroup OOM-kill (exit 137 — no Java exception at all!)** — cause and first diagnostic step for each.
- **Swap & paging**: why swapping is death for GC (heap walk touches everything); `AlwaysPreTouch` (pay page-fault cost at startup, not at first GC), transparent huge pages tradeoffs (`-XX:+UseTransparentHugePages` / THP madvise — Shipilëv quark).
- **SoftMaxHeapSize / current elasticity story** (ZGC's `-XX:SoftMaxHeapSize`, G1 uncommit behavior) — heaps *can* give memory back now; know that the old "JVM never returns memory" claim is outdated (JEP 346 for G1).

## 📚 Resources

### Books / chapters
- **Java Performance (2nd ed.)** — Oaks — Ch. 5–6 (heap sizing methodology) + Ch. 8 (native memory).
- **JVM Performance Engineering** — Monica Beckwith — container/GC-era sizing guidance.

### Videos & conference talks
- 🎥 **"Java in Containers"-track talks** — search *Devoxx JVM containers memory* (talks by Bruno Borges / Microsoft Java team are excellent and current — *"Secrets of Performance Tuning Java on Kubernetes"*).
- 🎥 **Andrei Pangin — "Memory Footprint of a Java Process"** — (shared with topic 05; the RSS-budget half applies here).

### Articles & official docs
- **Oracle GC Tuning Guide — "Ergonomics" + sizing chapters**: https://docs.oracle.com/en/java/javase/21/gctuning/
- **JEP 346: Promptly Return Unused Committed Memory from G1**: https://openjdk.org/jeps/346
- **Shipilëv quarks: #2 (Transparent Huge Pages), #12 (Native Memory Tracking-adjacent), and the pretouch discussions**: https://shipilev.net/jvm/anatomy-quarks/
- **Baeldung – "JVM Parameters"**: https://www.baeldung.com/jvm-parameters · **"OutOfMemoryError types"**: https://www.baeldung.com/java-outofmemoryerror
- **Microsoft/Azure "Containerize your Java applications" JVM-defaults guidance** (kept current on MaxRAMPercentage best practice): search "Microsoft containerize Java JVM default heap".
- **Kubernetes + JVM memory posts by Bruno Borges**: search "Bruno Borges JVM container memory" — the modern canonical advice thread.

## ❓ Senior interview questions

1. A pod has a 4 GB limit — walk me through your full memory budget and the Xmx (or MaxRAMPercentage) you'd choose, with the reasoning per line item.
2. Exit code 137, no OutOfMemoryError in logs, heap graphs look healthy — what happened and what are your next three commands?
3. Why set `-Xms == -Xmx` in production? When would you *not*?
4. What does container support actually change in JVM behavior — name three subsystems beyond the heap (GC threads, JIT threads, FJP/`availableProcessors`).
5. Rapid fire: cause + first diagnostic for each of the six `OutOfMemoryError` messages.
6. `GC overhead limit exceeded` — what's the exact trigger condition and what does it usually indicate vs plain heap-space OOM?
7. What do `AlwaysPreTouch` and huge pages buy you, and what do they cost?
8. "The JVM never gives memory back to the OS" — correct this statement with specifics (JEP 346, ZGC uncommit, SoftMaxHeapSize).
9. Same app: 2-core/2GB container silently behaves totally differently than 4-core/4GB — what flipped? (SerialGC ergonomics, C2 threads, common pool size)

## 🗺️ Study path

1. Read the Oracle tuning guide's ergonomics + sizing chapters.
2. Experiment: run `java -XX:+PrintFlagsFinal -version | grep -Ei 'maxheap|maxram|usecontainer'` inside Docker with different `--memory/--cpus` values — watch defaults shift (including the GC choice at 1 CPU).
3. Build the RSS budget for one of your real services: NMT summary + thread count × Xss + direct buffer metrics; compare against the container limit.
4. Trigger each OOM type deliberately in a sandbox (heap, metaspace via classloader loop, direct memory, native threads) — once each, so the taxonomy is experiential.
5. Rehearse Q1 and Q2 — these two *are* the senior interview for JVM-in-Kubernetes.

## 📝 My notes

_(your own notes while studying)_
