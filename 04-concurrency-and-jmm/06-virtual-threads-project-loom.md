# Virtual Threads & Structured Concurrency (Project Loom)

> **Level:** 🟢 Fundamental (hot topic — expect it in every 2025/2026 interview)
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **What they are**: JVM-managed threads (final in **JEP 444**, JDK 21) multiplexed onto few **carrier** platform threads; continuation-based — on a blocking call the JDK **unmounts** the virtual thread, freeing the carrier; stack chunks live on the heap.
- **What problem they solve**: thread-per-request scalability for **IO-bound** work without async/reactive code contortions. NOT faster CPU work; NOT "lightweight = magic speed."
- **Pinning**: situations where a VT can't unmount — historically `synchronized` blocks around blocking calls (largely FIXED in **JDK 24 via JEP 491** — big currency signal) and native/JNI frames; detect with `-Djdk.tracePinnedThreads` / JFR `jdk.VirtualThreadPinned`.
- **Practice rules**: don't pool virtual threads (create per task — `Executors.newVirtualThreadPerTaskExecutor()`); use **semaphores** for limiting concurrency instead of pool size; careful with `ThreadLocal` (millions of threads × TL = memory) → **Scoped Values (JEP 506)**.
- **Structured concurrency** (`StructuredTaskScope`, JEP 505 family): treat concurrent subtasks as one unit — cancellation propagation, error aggregation, no thread leaks; the "try-with-resources for concurrency" story.
- Scheduler: FJP-based, work stealing; carriers ≈ cores; `Thread.isVirtual()`; virtual threads are always daemon, no meaningful priority.
- vs reactive/async: same scalability class, but debuggable stack traces, familiar code, works with existing blocking libraries (JDBC drivers updated to be Loom-friendly).

## 📚 Resources

### Books / chapters
- **The Well-Grounded Java Developer (2nd ed.)** — future-Java chapter for Loom context (book predates finalization; pair with JEPs below).
- **Modern Java in Action / newer editions of core books** — treat JEPs as the primary source here; the tech is newer than most books.

### Videos & conference talks
- 🎥 **"Virtual Threads: New Foundations for High-Scale Java Applications"** — Ron Pressler (Loom lead; multiple versions on the *Java* channel / Devoxx) — the definitive explanation.
- 🎥 **José Paumard's Loom episodes** — *Java* YouTube channel (JEP Café / Inside Java Newscast on virtual threads, structured concurrency, scoped values) — short and precise.
- 🎥 **"Structured Concurrency in Java"** — Alan Bateman talks (JVMLS/Devoxx).

### Articles & official docs
- **JEP 444: Virtual Threads**: https://openjdk.org/jeps/444 — read it fully; the best-written JEP ever, and interviewers quote it.
- **JEP 491: Synchronize Virtual Threads without Pinning**: https://openjdk.org/jeps/491 — the pinning fix; knowing this marks you as current.
- **JEP 505: Structured Concurrency** & **JEP 506: Scoped Values**: https://openjdk.org/jeps/505 · https://openjdk.org/jeps/506
- **Oracle docs – "Virtual Threads" guide**: https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html
- **Baeldung – "Virtual Threads in Java"**: https://www.baeldung.com/java-virtual-thread-vs-thread

## ❓ Senior interview questions

1. How do virtual threads work under the hood? (continuations, mounting/unmounting, carriers, heap stacks)
2. When do virtual threads help and when are they pointless? (IO-bound yes; CPU-bound no)
3. What is pinning, what caused it, how do you detect it, and what changed in JDK 24?
4. Why should you never pool virtual threads? How do you limit concurrency then?
5. Why are `ThreadLocal`s problematic with VTs and what are Scoped Values?
6. Virtual threads vs reactive programming — how do you argue the migration to your team?
7. A million virtual threads all call a `synchronized` legacy library doing JDBC — what happens on JDK 21 vs JDK 24?
8. What does StructuredTaskScope give you over submitting to an executor and joining futures?

## 🗺️ Study path

1. Read JEP 444 end-to-end (90 min, highest-value read for interviews this year).
2. Build the classic demo: 10,000 tasks sleeping 1s — platform-thread pool vs `newVirtualThreadPerTaskExecutor`; observe.
3. Reproduce pinning on JDK 21 (`synchronized` + sleep + `-Djdk.tracePinnedThreads=full`), then read JEP 491.
4. Try `StructuredTaskScope` (preview flags) for a fan-out/fan-in call.
5. Watch a Pressler talk; rehearse Q1–Q3 fluently — this is 2026's favorite topic.

## 📝 My notes

_(your own notes while studying)_
