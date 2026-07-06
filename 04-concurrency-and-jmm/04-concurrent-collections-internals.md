# ConcurrentHashMap & Concurrent Collections Internals

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **ConcurrentHashMap (JDK 8+)**: NOT segments anymore (know the history: JDK 7 = 16 lock segments) — now **CAS on empty bins + `synchronized` on the first node of a non-empty bin**; treeification like HashMap; **concurrent resize with transfer assistance** (helping threads); `size()` via `LongAdder`-style counter cells.
- CHM semantics: weakly consistent iterators (no `ConcurrentModificationException`), `get` is lock-free, null keys/values forbidden (why: ambiguity between "absent" and "mapped to null" under concurrency).
- **`computeIfAbsent` is atomic per-bin** — the correct idiom for caches; but its lambda must be short and must not modify the map (deadlock/ISE risk).
- **CopyOnWriteArrayList**: snapshot iterators, write cost O(n) copy — read-heavy listener lists only.
- **ConcurrentLinkedQueue** (Michael-Scott non-blocking algorithm, CAS) vs **LinkedBlockingQueue / ArrayBlockingQueue** (locks + conditions; two-lock vs one-lock, bounded backpressure) vs **SynchronousQueue** (zero capacity, used by cached thread pools).
- **BlockingQueue** as the producer-consumer backbone: `put/take` block, `offer/poll` with timeout — and why bounded queues = backpressure = system stability.
- `Collections.synchronizedMap` vs CHM: coarse lock, iteration must be manually synchronized, no atomic compound ops.
- Failure of compound actions on any concurrent map: check-then-act still races → use atomic methods (`putIfAbsent`, `compute`, `merge`).

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Ch. 5 (concurrent collections & synchronizers).
- **The Art of Multiprocessor Programming** — Herlihy & Shavit — Ch. on concurrent hashing & queues (for the algorithms behind it; optional but deep).

### Videos & conference talks
- 🎥 **"Concurrent Collections"-track talks by José Paumard** — search *Paumard concurrent collections Devoxx*; his API deep-dives are the best available.
- 🎥 Any **Doug Lea JVMLS interview/talk** — hearing the design rationale from the author sticks.

### Articles & official docs
- **`ConcurrentHashMap` javadoc + the implementation notes comment in the source** — open the JDK source; the giant leading comment is the authoritative design doc. (In IntelliJ: navigate to class → view source.)
- **Baeldung – "Guide to ConcurrentHashMap"**: https://www.baeldung.com/java-concurrent-map · **"CopyOnWriteArrayList"**: https://www.baeldung.com/java-copy-on-write-arraylist · **"Guide to BlockingQueue"**: https://www.baeldung.com/java-blocking-queue
- **Michael & Scott queue paper** (for the brave): "Simple, Fast, and Practical Non-Blocking and Blocking Concurrent Queue Algorithms".

## ❓ Senior interview questions

1. How does ConcurrentHashMap achieve thread safety in JDK 8+? Contrast with the JDK 7 segment design.
2. Why does `get()` need no lock? (volatile reads of Node.val + safe publication)
3. Why no null keys/values in CHM but they're fine in HashMap?
4. What does "weakly consistent iterator" mean? What do you trade vs fail-fast?
5. `map.computeIfAbsent(k, heavyFn)` — what guarantees do you get? What must the function not do?
6. How does CHM resize while readers and writers keep working? (forwarding nodes, helping)
7. When would you pick CopyOnWriteArrayList, and when is it catastrophic?
8. Design choice: unbounded ConcurrentLinkedQueue vs bounded ArrayBlockingQueue for a work pipeline — argue for the bounded one.

## 🗺️ Study path

1. Read JCiP Ch. 5.
2. Open `ConcurrentHashMap.java` source and read the implementation-overview comment (it's ~200 lines of gold).
3. Write a mini cache with `computeIfAbsent`; demonstrate the check-then-act race with plain `get`+`put` under threads.
4. Read the Baeldung guides for API breadth (queues, COW).
5. Rehearse Q1 including the historical contrast — interviewers love the "segments are gone" correction.

## 📝 My notes

_(your own notes while studying)_
