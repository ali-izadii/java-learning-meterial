# ABA Problem, AtomicStampedReference, Field Updaters & Atomic Arrays

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **The ABA problem**: CAS checks *value equality*, not *history*. Thread 1 reads A; thread 2 changes A→B→A; thread 1's CAS succeeds though the world changed. Harmless for counters; **catastrophic for pointer-based structures** (classic: lock-free stack pop where node A was freed and reallocated — the canonical whiteboard story to memorize).
- Why Java suffers less than C/C++: GC prevents *memory reuse* while anyone holds a reference — most Java ABA scenarios are benign; but *logical* ABA (same node re-linked, state machines A→B→A) still bites.
- **`AtomicStampedReference<V>`**: CAS on (reference, int stamp) pair — version counter makes history visible; `compareAndSet(expectedRef, newRef, expectedStamp, newStamp)`. **`AtomicMarkableReference<V>`**: (reference, boolean mark) — the "logically deleted" flag used by Harris-style linked lists. Implementation detail: both wrap an immutable `Pair` and CAS the pair reference.
- **Field updaters (`AtomicIntegerFieldUpdater`, `AtomicLongFieldUpdater`, `AtomicReferenceFieldUpdater`)**: reflection-based CAS on a **`volatile` field of an existing class** — zero extra object per instance (vs an AtomicLong *object* per instance: header + indirection). Where they're used in the wild: older JDK internals, Netty, Kafka — memory footprint at scale. Requirements: field must be `volatile`, accessible, non-static. Modern replacement: **VarHandle** (topic 05).
- **Atomic arrays (`AtomicIntegerArray`, `AtomicLongArray`, `AtomicReferenceArray`)**: volatile-semantics element access + per-element CAS (plain arrays have NO volatile elements — `volatile int[] a` makes the *reference* volatile, not the slots — top trap). Cost note: elements are dense → adjacent-index CAS by different threads = false sharing.
- `getAndUpdate`/`accumulateAndGet` exist across the family; the function-must-be-pure rule applies everywhere.

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Ch. 15.4.4 (the ABA discussion) + Ch. 15 generally.
- **The Art of Multiprocessor Programming** — Herlihy & Shavit — Ch. 9–10 (linked lists with AtomicMarkableReference; where these classes earn their keep).

### Videos & conference talks
- 🎥 **"Lock-Free Programming"** — Herb Sutter (CppCon) — part 2 covers ABA with the stack example in unforgettable detail.
- 🎥 Search *"ABA problem lock free"* on YouTube — several good short whiteboard explanations; pick one after reading, to consolidate.

### Articles & official docs
- **Baeldung – "ABA Problem in Concurrency"**: https://www.baeldung.com/cs/aba-concurrency (concept) and **"AtomicStampedReference"**: https://www.baeldung.com/java-atomicstampedreference
- **Baeldung – "AtomicMarkableReference"**: https://www.baeldung.com/java-atomicmarkablereference
- **Baeldung – "Atomic Field Updaters"**: https://www.baeldung.com/java-atomic-field-updaters
- **`AtomicStampedReference` / `AtomicIntegerFieldUpdater` javadoc + source** — the Pair-CAS implementation is 100 lines; read it.
- **Netty source** — search for `AtomicIntegerFieldUpdater` usage (e.g., reference counting in `AbstractReferenceCountedByteBuf`) — real-world footprint-driven usage.

## ❓ Senior interview questions

1. Explain ABA with the lock-free stack pop example. Why does the corrupted-stack version require memory reuse?
2. Why does Java's GC make ABA rarer than in C++ — and give a *logical* ABA example GC doesn't save you from.
3. How does AtomicStampedReference defeat ABA? What does it actually CAS internally?
4. StampedReference vs MarkableReference — different use cases? (versioning vs logical deletion)
5. You have 10 million instances each needing one atomic counter — AtomicLong field vs AtomicLongFieldUpdater vs VarHandle: compare memory and ergonomics.
6. `volatile int[] arr` — what is and isn't volatile here? How do you get volatile array elements?
7. Why might two threads CASing `arr[i]` and `arr[i+1]` of an AtomicLongArray interfere with each other? (false sharing)
8. What preconditions does AtomicIntegerFieldUpdater impose on the target field and why?

## 🗺️ Study path

1. Re-read JCiP 15.4 with ABA focus.
2. Whiteboard the ABA stack-corruption sequence until you can draw it from memory (nodes A, B; pop in flight; reuse).
3. Code: fix a version-counter demo with AtomicStampedReference; port a per-instance AtomicLong to a FieldUpdater and measure per-object size with JOL (ties to topic 1.02).
4. Read the Netty ref-counting source for the production pattern.
5. Rehearse Q1 — the ABA stack story is the single most-asked "prove you understand lock-free" question.

## 📝 My notes

_(your own notes while studying)_
