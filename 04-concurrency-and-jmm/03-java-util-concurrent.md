# java.util.concurrent: Executors, Futures, Locks, Atomics (CAS)

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **ThreadPoolExecutor anatomy** (the #1 senior question): corePoolSize, maximumPoolSize, keepAlive, **workQueue**, ThreadFactory, **RejectedExecutionHandler** — and the exact order: core threads → queue → extra threads up to max → reject. Why an unbounded `LinkedBlockingQueue` means max is never reached; why `Executors.newFixedThreadPool` can OOM (unbounded queue) — Bloch's advice to construct TPE directly.
- Pool sizing: CPU-bound ≈ cores; IO-bound ≈ cores × (1 + wait/compute) — and "or use virtual threads" as the modern answer.
- **CAS (compare-and-swap)**: the primitive under everything; `AtomicInteger.incrementAndGet` loop; **ABA problem** (+ `AtomicStampedReference`); contention → `LongAdder` (striping).
- **AQS (AbstractQueuedSynchronizer)**: the framework under ReentrantLock/Semaphore/CountDownLatch — state + CLH queue of waiting threads; know the concept, not every line.
- **CompletableFuture**: composition (`thenApply/thenCompose/thenCombine`), async variants and *which executor runs the callback* (default ForkJoinPool.commonPool — dangerous for blocking IO), error channels (`exceptionally/handle`), `join` vs `get`.
- Coordination primitives: `CountDownLatch` (one-shot) vs `CyclicBarrier` (reusable, action) vs `Semaphore` (permits) vs `Phaser` (dynamic parties).
- `ReadWriteLock` and `StampedLock` (optimistic reads) — when read-mostly workloads justify them.
- `ThreadLocal` — use cases and the thread-pool leak trap (remove() in finally).

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Ch. 5 (building blocks), 6–8 (task execution & pools), 14–15 (synchronizers, atomics). The interview canon.
- **The Well-Grounded Java Developer (2nd ed.)** — modern concurrency chapter (bridges JCiP to today).

### Videos & conference talks
- 🎥 **"The java.util.concurrent Synchronizer Framework"** — read Doug Lea's AQS paper (below); for video search *AQS AbstractQueuedSynchronizer deep dive*.
- 🎥 **"CompletableFuture: The Promises of Java"** — search Devoxx (José Paumard's async talks are outstanding: *"Asynchronous Systems with CompletionStage"*).
- 🎥 **"From Concurrent to Parallel"** — Brian Goetz — pool sizing & task decomposition rationale.

### Articles & official docs
- **Doug Lea — "The java.util.concurrent Synchronizer Framework" (AQS paper)**: https://gee.cs.oswego.edu/dl/papers/aqs.pdf — short academic paper by the author of j.u.c; skim for architecture.
- **`ThreadPoolExecutor` javadoc** — the class-level doc IS the interview answer sheet; read every paragraph.
- **Baeldung – "Guide to ThreadPoolExecutor"**: https://www.baeldung.com/thread-pool-java-and-guava · **"Guide to CompletableFuture"**: https://www.baeldung.com/java-completablefuture · **"LongAdder vs AtomicLong"**: https://www.baeldung.com/java-longadder-and-longaccumulator
- **Effective Java** — Item 80 (prefer executors), Item 81 (prefer concurrency utilities to wait/notify).

## ❓ Senior interview questions

1. Walk through exactly what happens when you submit a task to a ThreadPoolExecutor with core=4, max=8, queue capacity=100. When is thread #5 created?
2. Why can `Executors.newFixedThreadPool` take down a service? What would you configure instead?
3. How does CAS work? What is the ABA problem and when does it actually matter?
4. `AtomicLong` vs `LongAdder` — how does striping trade read cost for write scalability?
5. `thenApply` vs `thenCompose` vs `thenCombine`? Which thread runs a non-async callback?
6. Why is blocking inside `ForkJoinPool.commonPool()` (default CF executor) dangerous?
7. CountDownLatch vs CyclicBarrier vs Semaphore — give a use case each.
8. Sketch how ReentrantLock works on top of AQS (state, CAS, queue, park/unpark).

## 🗺️ Study path

1. Read the `ThreadPoolExecutor` class javadoc top-to-bottom; draw the submit-decision flowchart from memory.
2. Read JCiP Ch. 6–8.
3. Build: a bounded TPE with custom rejection; a CompletableFuture pipeline with a dedicated executor; a CAS retry loop by hand.
4. Skim the AQS paper; be able to draw state+queue.
5. Rehearse Q1 and Q2 — asked in nearly every senior Java interview.

## 📝 My notes

_(your own notes while studying)_
