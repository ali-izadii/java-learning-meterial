# VarHandles & Memory Access Modes (Plain, Opaque, Acquire/Release, Volatile)

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **What VarHandle is (JEP 193, JDK 9)**: a typed, JIT-inlinable handle to a variable (field, static, array element) supporting *every* access mode — the sanctioned replacement for `Unsafe` field/array access and `Atomic*FieldUpdater`s; obtained via `MethodHandles.lookup().findVarHandle(...)` (+ `privateLookupIn` for other classes' privates).
- **The four access-mode strengths** (weakest → strongest) — the heart of this topic:
  - **Plain** (`get/set`): normal field access; may be reordered, cached, torn (long/double on 32-bit); no inter-thread meaning.
  - **Opaque** (`getOpaque/setOpaque`): per-variable coherence — the write *will* eventually be seen, access happens "as written" for that variable, but **no ordering with other variables**; ~free on mainstream CPUs. Use: progress flags, statistics you poll.
  - **Acquire/Release** (`getAcquire/setRelease`): one-directional fences — release-write = nothing before it moves after; acquire-read = nothing after it moves before. Together: **message passing / safe publication** without the StoreLoad barrier. This is exactly how most "volatile" use cases can be cheapened.
  - **Volatile** (`getVolatile/setVolatile`): full JMM volatile — total order over all volatile ops; the only mode with the expensive StoreLoad barrier; needed for **Dekker-style** patterns (two flags, each thread writes one reads the other).
- Atomic ops in modes: `compareAndSet` (volatile), `weakCompareAndSetPlain/Acquire/Release` (may fail spuriously — LL/SC!), `compareAndExchange[Acquire/Release]` (returns witness value — enables slicker loops), `getAndAdd[Acquire/Release]`, `getAndSet`.
- **Fences** (`VarHandle.fullFence/acquireFence/releaseFence/loadLoadFence/storeStoreFence`) — free-standing ordering when you can't attach it to an access.
- Rules of engagement: **default to volatile mode / AtomicX; downgrade only with a profiler proof and a written argument**; opaque/acquire/release mistakes produce heisenbugs that pass all tests on x86 (TSO gives acquire/release semantics for free — ARM is where wrong-mode code dies). Test with **jcstress**.
- Why interviewers care: this is the JMM-in-practice question — mapping happens-before (topic 4.02) to actual API calls and barrier costs (topic 02 here).

## 📚 Resources

### Books / chapters
- No book covers this well — the two Doug Lea / Shipilëv sources below are the canon.

### Videos & conference talks
- 🎥 **"VarHandles"-era talks by Paul Sandoz** (JEP 193 lead; JVMLS/Devoxx) — search *Sandoz VarHandle*.
- 🎥 **Shipilëv's JMM talks** ("Java Memory Model Pragmatics", "Close Encounters") — the modes are the practical payload of those talks.

### Articles & official docs
- **Doug Lea — "Using JDK 9 Memory Order Modes"**: https://gee.cs.oswego.edu/dl/html/j9mm.html — THE document for this topic; every mode, every idiom (message passing, Dekker, seqlock), by the man himself. Read it twice.
- **`VarHandle` javadoc** — mode definitions are normative here: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/invoke/VarHandle.html
- **JEP 193: Variable Handles**: https://openjdk.org/jeps/193
- **Shipilëv — "On the Fence with Dependencies"** and jcstress samples: https://shipilev.net/ · https://openjdk.org/projects/code-tools/jcstress/
- **Baeldung – "Java 9 Variable Handles Demystified"**: https://www.baeldung.com/java-variable-handles

## ❓ Senior interview questions

1. Rank the four access modes and state, for each, one guarantee it adds over the previous.
2. Implement single-writer single-reader flag+data handoff with the *cheapest correct* modes (setRelease/getAcquire) and explain why plain and opaque are insufficient.
3. Why does the Dekker/StoreLoad pattern require full volatile mode — what goes wrong with acquire/release?
4. What is `weakCompareAndSet` and why can it fail spuriously? On which CPUs does "weak" actually differ?
5. `compareAndExchange` vs `compareAndSet` — what does the returned witness value buy you in a retry loop?
6. Why can wrong-mode code pass every test on x86 and fail on ARM? What does TSO give you for free?
7. VarHandle vs Unsafe vs AtomicFieldUpdater — capabilities, safety, and what the JDK itself migrated to.
8. What is a seqlock and which modes/fences does Lea's version use? (bonus differentiator)

## 🗺️ Study path

1. Read Doug Lea's "Using JDK 9 Memory Order Modes" (the whole thing; take idiom notes).
2. Implement the message-passing idiom with each mode; run under **jcstress** and watch plain/opaque fail on a weak-memory machine (or study the jcstress sample results if you only have x86).
3. Read the VarHandle javadoc mode definitions against your JMM notes (topic 4.02) — map each mode to happens-before edges.
4. Convert one of your AtomicLong demos to VarHandle with acquire/release; benchmark the difference under contention.
5. Rehearse Q1–Q3; being crisp on modes is rare even among seniors — this topic is a differentiator.

## 📝 My notes

_(your own notes while studying)_
