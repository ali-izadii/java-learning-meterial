# Atomic Classes & CAS Fundamentals (AtomicInteger, AtomicLong, AtomicReference)

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **What "atomic" means here**: an indivisible read-modify-write — no other thread can observe or interleave in the middle. `volatile` gives visibility/ordering but NOT atomicity (`volatile int i; i++` = read + add + write = race); atomics give **both** (all reads/writes have volatile semantics + atomic RMW ops).
- **CAS (compare-and-swap/compareAndSet)**: `cas(expected, newValue)` — succeeds only if the current value equals expected; returns success/failure instead of blocking. The universal primitive of non-blocking concurrency.
- **The CAS retry loop** — the pattern behind every atomic method; be able to write it cold:
  ```java
  int prev, next;
  do {
      prev = atomic.get();
      next = compute(prev);
  } while (!atomic.compareAndSet(prev, next));
  ```
- The family: `AtomicInteger/AtomicLong` (`incrementAndGet`, `getAndAdd`, `getAndSet`), `AtomicBoolean` (one-shot latches: `compareAndSet(false, true)`), `AtomicReference<V>` (`updateAndGet/accumulateAndGet` with pure lambdas — they may retry, so **functions must be side-effect-free**).
- **Implementation stack**: `AtomicInteger.compareAndSet` → `Unsafe.compareAndSetInt` (historically) / `VarHandle` (modern JDK) → **JIT intrinsic** → single CPU instruction (`lock cmpxchg` on x86). It's not magic — it's hardware.
- **Optimistic vs pessimistic**: locks assume conflict (block others), CAS assumes no conflict (retry on failure). Consequences: no deadlock, no priority inversion, no context switch on contention — but **livelock-ish retry storms under heavy contention** and only single-variable scope.
- When CAS loses to locks: high contention (retry waste + cache-line ping-pong), multi-variable invariants (you can't CAS two things atomically — that's what locks/STM are for).
- `getAndIncrement` on x86 compiles to `lock xadd` (fetch-and-add, wait-free!) not a CAS loop — a nice expert nugget.

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — **Ch. 15 "Atomic Variables and Nonblocking Synchronization"** — THE chapter for this whole folder; read it first.
- **The Art of Multiprocessor Programming** — Herlihy & Shavit — Ch. 5 (the relative power of synchronization primitives; consensus numbers — why CAS is "universal").

### Videos & conference talks
- 🎥 **"The java.util.concurrent Package"-level talks by José Paumard** — search *Paumard atomic CAS Devoxx*; his API walkthroughs cover atomics precisely.
- 🎥 **"Lock-Free Programming (or, Juggling Razor Blades)"** — Herb Sutter (CppCon, 2 parts) — C++ but the best CAS-mental-model talk ever recorded; everything transfers.

### Articles & official docs
- **`java.util.concurrent.atomic` package javadoc** — the package summary defines the exact memory semantics of every method (weakCompareAndSet caveats included): https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/concurrent/atomic/package-summary.html
- **Baeldung – "An Introduction to Atomic Variables in Java"**: https://www.baeldung.com/java-atomic-variables
- **Baeldung – "Compare and Swap in Java"**: https://www.baeldung.com/java-compare-and-swap
- **`AtomicInteger` / `AtomicReference` source** — open in IDE; see the VarHandle plumbing and how thin the classes really are.
- **Maged Michael Pragmatic Implementation Non-Blocking Linked Lists: https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://timharris.uk/papers/2001-disc.pdf&ved=2ahUKEwj6qvLnir6VAxUkVkEAHVZoDvcQFnoECCEQAQ&usg=AOvVaw24RK5STHAVw9cw0vdkHbWV

## ❓ Senior interview questions

1. Why is `volatile int counter; counter++` broken, and what exactly does `AtomicInteger.incrementAndGet()` add on top of volatile?
2. Write the CAS retry loop for "multiply the value by 2 but cap at 100" on an AtomicInteger — no locks.
3. What does `compareAndSet` return and why is *returning failure* (instead of blocking) the whole point of non-blocking algorithms?
4. What is compareAndSet compiled down to on x86? What layers sit between the Java call and the instruction?
5. Why must the lambda passed to `updateAndGet` be side-effect-free?
6. When would you deliberately choose a `synchronized` counter over an AtomicLong? (multi-variable invariants, extreme contention → see LongAdder)
7. CAS "spins" on failure — why is that usually cheaper than blocking, and when does it become worse?
8. What can you atomically update with a single CAS — and what fundamentally can't you (two variables)?

## 🗺️ Study path

1. Read JCiP Ch. 15 (≈25 pages — the foundation for topics 02–06 too).
2. Read the `java.util.concurrent.atomic` package javadoc summary.
3. Code kata: implement `AtomicCappedCounter` and a one-shot `AtomicBoolean` latch; race 10 threads on a plain int vs volatile int vs AtomicInteger and print the three results — keep this demo.
4. Open `AtomicInteger` source and trace `incrementAndGet` down to the VarHandle call.
5. Rehearse Q1 and Q2 — the two most common atomics screeners.

## 📝 My notes

_(your own notes while studying)_
