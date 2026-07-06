# ForkJoinPool, Work Stealing & Parallel Streams

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Fork/join model**: recursive divide-and-conquer; `RecursiveTask`/`RecursiveAction`; fork pushes to the worker's **own deque**, idle workers **steal from the tail** of others' deques (work stealing → load balancing with low contention).
- **ForkJoinPool.commonPool()**: shared JVM-wide, parallelism = cores−1; used by parallel streams and default CompletableFuture async methods — hence **one blocking task degrades every parallel stream in the JVM** (the top interview trap).
- **ManagedBlocker** — how to tell FJP a task will block so it compensates with extra threads.
- **Parallel streams**: when they help — N (elements) × Q (cost per element) large enough, splittable source (ArrayList/arrays split well; LinkedList/iterators don't), associative stateless ops, no shared mutable state, no boxing storms.
- When they hurt: small data, IO-bound work, latency-sensitive request paths (you're sharing the common pool with everyone), ordered/stateful ops (`limit`, `sorted` on unordered demands).
- Running a parallel stream in a custom FJP (submit the stream pipeline as a task) — the workaround and its caveats.
- Doug Lea's guidance: measure; parallel is a *performance optimization*, not a default.

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Ch. 8's task-decomposition groundwork.
- **Modern Java in Action** (Urma, Fusco, Mycroft) — Ch. 7 "Parallel data processing" — best book treatment of parallel-stream pitfalls (boxing, splitting, accumulator traps).

### Videos & conference talks
- 🎥 **"From Concurrent to Parallel"** — Brian Goetz — exactly this topic: when parallelism pays.
- 🎥 **"Thinking in Parallel"** — Stuart Marks & Brian Goetz (Oracle CodeOne) — streams + parallelism mental models.

### Articles & official docs
- **Doug Lea — "When to use parallel streams"** (with Goetz et al.): https://gee.cs.oswego.edu/dl/html/StreamParallelGuidance.html — THE reference; short, memorize the N×Q heuristic.
- **Doug Lea — "A Java Fork/Join Framework"** (the design paper): https://gee.cs.oswego.edu/dl/papers/fj.pdf
- **Baeldung – "Guide to the Fork/Join Framework"**: https://www.baeldung.com/java-fork-join · **"When to Use a Parallel Stream"**: https://www.baeldung.com/java-when-to-use-parallel-stream
- **`ForkJoinPool` javadoc** — including ManagedBlocker section.

## ❓ Senior interview questions

1. Explain work stealing. Why deques, and why steal from the opposite end?
2. Why is calling a blocking HTTP client inside `parallelStream().map(...)` a production incident waiting to happen?
3. How is common-pool parallelism sized? Why cores−1?
4. What source characteristics make a stream split well? Compare ArrayList vs LinkedList as parallel sources.
5. What is ManagedBlocker for?
6. `collect` vs `reduce` with a mutable accumulator in parallel — what goes wrong with `reduce((a,b) -> a.addAll(b))`-style code?
7. Give the N×Q rule of thumb for when parallel streams pay off.
8. How do you run a parallel stream on your own pool, and what would you consider instead (chunked executor tasks, virtual threads for IO)?

## 🗺️ Study path

1. Read Lea's "When to use parallel streams" guidance page.
2. Implement a `RecursiveTask` sum/sort; observe speedup vs sequential; then poison the common pool with a sleeping parallel stream and watch another stream slow down — the demo that makes Q2 visceral.
3. Read Modern Java in Action Ch. 7.
4. Watch "From Concurrent to Parallel".
5. Write your own decision checklist (source splittability, N×Q, CPU vs IO, common-pool sharing) in My Notes.

## 📝 My notes

_(your own notes while studying)_
