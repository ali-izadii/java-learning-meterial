# The Object Allocation Path: What `new` Actually Does

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- The full journey of `new Foo()`: bytecode `new` (allocate + default-init) → `invokespecial <init>` (constructor) — two separate steps at the JVM level (and why the JIT can't reorder the publish before init for `final` fields — ties to JMM topic 4.02).
- **The fast path — TLAB bump-the-pointer**: each thread owns a Thread-Local Allocation Buffer inside eden; allocation = "pointer += size, check limit" — **~10 ns, no synchronization, no lock**. This is the answer to "is allocation expensive in Java?" — *no, allocation is nearly free; collection/promotion is what costs*.
- **The slow-path cascade** when TLAB is exhausted: retire TLAB → request new TLAB from eden (CAS on shared eden pointer) → eden full? → trigger **minor GC** → still can't satisfy? → allocate directly in old gen → full GC → `OutOfMemoryError`.
- What happens *at* allocation: zeroing the memory (default field values — and why zeroing, not construction, is part of allocation cost), header write (mark word + klass pointer — topic 1.02), then constructor runs.
- **Large-object bypass**: allocations bigger than the TLAB (or `-XX:PretenureSizeThreshold` with some collectors; **humongous** ≥ half-region in G1) skip the TLAB / go straight to old gen or humongous regions — why big arrays are special citizens.
- **Allocation rate** as the health metric: MB/s allocated drives minor-GC frequency (eden size / alloc rate = time between minor GCs); "GC problem" is very often an "allocation problem".
- Compiler assists that remove allocation entirely: **escape analysis → scalar replacement** (topic 03 here / 3.02) — why microbenchmarks sometimes show "zero allocation" for code that clearly says `new`.

## 📚 Resources

### Books / chapters
- **Optimizing Java** (Evans, Gough, Newland) — Ch. 6 sections on allocation & TLABs — best book treatment of the allocation path.
- **Java Performance (2nd ed.)** — Scott Oaks — Ch. 5 (GC intro: allocation, eden, promotion mechanics).

### Videos & conference talks
- 🎥 **"Allocation — Mechanics, Profiling and Optimization"** — search YouTube for Shipilëv's allocation talks (*"Shenandoah/allocation"* sessions include the path); his **JVM Anatomy** material in talk form.
- 🎥 **"The Trouble with Memory"** — Kirk Pepperdine (Devoxx) — why allocation rate, not footprint, is the usual killer; memory-efficiency mindset.
- 🎥 **"A JVM Does That???"** — Cliff Click — includes brilliant allocation/GC economics sections.

### Articles & official docs
- **JVM Anatomy Quark #4: TLAB Allocation** — Aleksey Shipilëv: https://shipilev.net/jvm/anatomy-quarks/4-tlab-allocation/ — THE core read: shows bump-the-pointer with disassembly.
- **JVM Anatomy Quark #7: Initialization Costs** (zeroing): https://shipilev.net/jvm/anatomy-quarks/7-initialization-costs/
- **JVM Anatomy Quark #6: New Object Stages**: https://shipilev.net/jvm/anatomy-quarks/6-new-object-stages/ — allocation vs initialization split, precisely.
- **Baeldung – "Memory Allocation in Java"**: https://www.baeldung.com/java-memory-allocation (fast overview before the quarks).
- **HotSpot source pointer**: `MemAllocator::allocate` / interpreter fast-path — for the brave; Shipilëv's quarks quote the relevant parts.

## ❓ Senior interview questions

1. Walk through `new Foo()` end-to-end: bytecode, TLAB fast path, slow paths, constructor. Where can a GC be triggered?
2. "Object allocation in Java is expensive" — attack this statement with mechanism-level arguments. What *is* expensive?
3. Why does allocation require zeroing memory, and when can the JVM skip or defer it?
4. Why is the TLAB fast path lock-free even though eden is shared by all threads? Where does synchronization reappear?
5. What happens when an allocation is larger than the remaining TLAB space? Three possible outcomes.
6. How does allocation rate relate to minor-GC frequency? Give the back-of-envelope formula.
7. `new` and `<init>` are separate bytecode steps — what concurrency guarantee bridges them for final fields?
8. When does `new` compile to *no allocation at all*?

## 🗺️ Study path

1. Read Shipilëv quarks #4, #6, #7 (≈1.5 hours total, runnable experiments included).
2. `javap -c` a simple `new Foo()` call site — see `new` + `dup` + `invokespecial`.
3. Run a JMH allocation benchmark with `-prof gc` — read "gc.alloc.rate" and "gc.alloc.rate.norm" (bytes/op); confirm ~16 bytes for `new Object()`.
4. Watch Pepperdine's "The Trouble with Memory".
5. Rehearse Q1 as a whiteboard flow diagram — this is the anchor question for the whole folder.

## 📝 My notes

_(your own notes while studying)_
