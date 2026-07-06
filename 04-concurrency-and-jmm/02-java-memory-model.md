# Java Memory Model: Happens-Before, volatile, Final Fields, Data Races

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Why a memory model exists: compilers and CPUs **reorder**; caches make writes invisible; the JMM defines the *minimum guarantees* across all hardware.
- **Happens-before (HB)** — the core relation. The canonical edges: program order within a thread; **monitor unlock → subsequent lock**; **volatile write → subsequent volatile read**; `Thread.start()` → run; run end → `join()` returns; default (final-field) freezes. HB is transitive.
- **Data race** = two conflicting accesses (at least one write) not ordered by HB → undefined-ish behavior (word tearing aside, you may see stale values). "Correctly synchronized programs are sequentially consistent."
- **volatile**: visibility + ordering (NOT atomicity — `volatile int i; i++` is still broken); how it forbids caching in registers and reordering (memory barriers/fences as the implementation intuition).
- **Safe publication**: the double-checked locking story — broken without volatile, why (writes to fields of the object can be reordered past the reference publish); alternatives (holder idiom, enum, eager init).
- **final field semantics**: properly constructed objects (no `this` escape during construction) → all threads see final fields correctly *without* synchronization — the guarantee immutability rests on.
- `long`/`double` word-tearing history; `Atomic*` and `VarHandle` memory modes (acquire/release/opaque/plain) as the modern nuanced API.

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Ch. 3 (visibility/publication) + Ch. 16 (the JMM chapter — read it *twice*).
- **JLS §17.4 "Memory Model"**: https://docs.oracle.com/javase/specs/jls/se21/html/jls-17.html — skim after JCiP; interviewers respect JLS-grounded answers.

### Videos & conference talks
- 🎥 **"Java Memory Model Pragmatics"** — Aleksey Shipilëv — THE talk; also exists as a full transcript article: https://shipilev.net/blog/2014/jmm-pragmatics/
- 🎥 **"Close Encounters of the JMM Kind"** — Shipilëv (article with embedded talk content): https://shipilev.net/blog/2016/close-encounters-of-jmm-kind/ — a catalog of JMM myths debunked; superb interview prep.

### Articles & official docs
- **JSR-133 FAQ** — Jeremy Manson & Brian Goetz: https://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html — old but still the clearest Q&A-format explanation ever written.
- **Shipilëv — "Safe Publication and Safe Initialization in Java"**: https://shipilev.net/blog/2014/safe-public-construction/ — the double-checked-locking reference.
- **Baeldung – "Guide to the Volatile Keyword"**: https://www.baeldung.com/java-volatile
- **jcstress** — OpenJDK concurrency stress-test tool (see real reorderings happen!): https://openjdk.org/projects/code-tools/jcstress/

## ❓ Senior interview questions

1. Define happens-before. List five edges from memory.
2. Thread A sets `flag = true` (plain field); thread B loops on `flag` — why can B loop forever? Name every mechanism that could cause it (register caching, JIT hoisting, CPU cache).
3. Why exactly is double-checked locking broken without `volatile`? What can the second thread observe?
4. Is `volatile` enough for a counter? Why not, and what do you use instead?
5. Explain final-field semantics: what guarantee do you get, and what construction rule must you obey (`this` must not escape)?
6. What does "correctly synchronized programs have sequentially consistent behavior" mean?
7. What orderings do `getAcquire`/`setRelease` provide vs `volatile`? (VarHandle modes — expert differentiator)

## 🗺️ Study path

1. Read the JSR-133 FAQ (1 hour, highest value-per-page on this list).
2. Read JCiP Ch. 3, then Ch. 16.
3. Read/watch Shipilëv's "JMM Pragmatics"; skim "Close Encounters" myth catalog.
4. Run a couple of jcstress samples to *see* reorderings on your own machine — unforgettable.
5. Rehearse Q2 and Q3 — they are the two most-asked JMM interview questions in existence.

## 📝 My notes

_(your own notes while studying)_
