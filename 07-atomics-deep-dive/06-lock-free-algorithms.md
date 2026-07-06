# Building Lock-Free Algorithms: Treiber Stack, Michael-Scott Queue, Progress Guarantees

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Progress guarantees** (define all three precisely):
  - **Wait-free**: every thread completes in bounded steps (e.g., `getAndAdd` via `lock xadd`).
  - **Lock-free**: *some* thread always makes progress (CAS loops — an individual thread can starve, the system can't).
  - **Obstruction-free**: progress only in isolation.
  - Contrast with blocking: a descheduled/crashed lock-holder blocks everyone; a stalled thread in a lock-free structure blocks no one — this is the *availability* argument, not (primarily) a speed argument.
- **Treiber stack** (write it from memory — the standard whiteboard exercise):
  ```java
  void push(T v) {
      Node<T> n = new Node<>(v);
      do { n.next = top.get(); }
      while (!top.compareAndSet(n.next, n));
  }
  ```
  Pop = read top, CAS to top.next; where ABA lurks (topic 03) and why GC saves the Java version.
- **Michael-Scott queue** (the algorithm inside `ConcurrentLinkedQueue`): head/tail pointers, dummy node, two-step enqueue (CAS next, then swing tail) and the key idea — **helping**: any thread finding a lagging tail advances it; invariants survive threads dying mid-operation because every intermediate state is legal.
- **Linearizability**: each operation appears to take effect atomically at one instant (the linearization point — identify it: the successful CAS); this is the correctness criterion for all these structures.
- Design heuristics: single CAS-able "commit point"; immutable nodes where possible; treat every interleaving of *legal states* as reachable; helping over waiting.
- **Honest senior judgment**: you almost never hand-roll these — use j.u.c. Know *when* lock-free matters (latency-critical paths, signal handlers/interrupt contexts, avoiding priority inversion) and the real-world exemplars: **JCTools** (Netty's queues — SPSC/MPSC specializations beat general MPMC), **Disruptor** (single-writer + sequences).
- The **single-writer principle** (Thompson): the fastest concurrency is no concurrency on the write side — often beats clever lock-free MPMC designs.

## 📚 Resources

### Books / chapters
- **The Art of Multiprocessor Programming** — Herlihy & Shavit — Ch. 3 (linearizability), **Ch. 10–11 (queues & stacks — Treiber and Michael-Scott walkthroughs)**. The textbook for this topic.
- **Java Concurrency in Practice** — Ch. 15.4 (nonblocking algorithms — includes both structures in Java form).

### Videos & conference talks
- 🎥 **"Lock-Free Programming (or, Juggling Razor Blades)"** — Herb Sutter (CppCon, parts 1–2) — builds exactly these structures live, including every bug on the way.
- 🎥 **"Adventures with concurrent programming in Java"** — Martin Thompson — queues, single-writer principle, why JCTools exists.
- 🎥 **Nitsan Wakart — JCTools talks** (search *Wakart lock free queues*) — the maintainer on real-world Java lock-free queues.

### Articles & official docs
- **Michael & Scott — "Simple, Fast, and Practical Non-Blocking and Blocking Concurrent Queue Algorithms"** (PODC '96) — the original paper; short and readable: search the title (free PDF).
- **`ConcurrentLinkedQueue` source + class comment** — the JDK's annotated production implementation of Michael-Scott (with Java-specific twists documented in the comment).
- **JCTools**: https://github.com/JCTools/JCTools — read the README + one SPSC queue source.
- **1024cores.net** — Dmitry Vyukov's site on lock-free design (producer-consumer taxonomy): https://www.1024cores.net/
- **Baeldung – "Non-Blocking Algorithms in Java"**: https://www.baeldung.com/java-non-blocking-algorithms

## ❓ Senior interview questions

1. Define wait-free / lock-free / obstruction-free with one Java example each.
2. Whiteboard a Treiber stack push and pop. Where is the linearization point of each?
3. In the Michael-Scott queue, why is enqueue two separate CASes, and what does a helper thread do when it observes the intermediate state?
4. What could go wrong if a thread dies between the two enqueue CASes — and why doesn't it corrupt the queue?
5. Why is "lock-free is faster than locks" wrong as a blanket statement? What is the actual guarantee you buy?
6. Why do SPSC/MPSC specialized queues (JCTools) dramatically outperform general MPMC ones?
7. Explain the single-writer principle and how the Disruptor applies it.
8. Your teammate proposes hand-rolling a lock-free map for a hot path — walk through your review checklist (need proof, jcstress, ABA analysis, memory ordering on ARM, maintenance cost, "did you try LongAdder/CHM first").

## 🗺️ Study path

1. Read JCiP 15.4, then Herlihy & Shavit Ch. 10–11 for the same structures with proofs.
2. Implement a Treiber stack from memory; test it with jcstress; deliberately break it (plain read instead of volatile) and watch jcstress catch it.
3. Read the Michael-Scott paper (≈10 pages), then the `ConcurrentLinkedQueue` class comment to see the deltas.
4. Watch Sutter parts 1–2; then Thompson's talk for the "when NOT to do this" wisdom.
5. Rehearse Q2 (whiteboard-ready) and Q8 (judgment-ready) — together they cover both ways this topic is interviewed.

## 📝 My notes

_(your own notes while studying)_
