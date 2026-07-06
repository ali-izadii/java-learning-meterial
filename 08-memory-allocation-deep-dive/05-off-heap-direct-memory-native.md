# Off-Heap Allocation: Direct Buffers, Unsafe, MemorySegment & Native Memory

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06
> **Cross-ref:** FFM API details in [06-ecosystem-and-cross-cutting/04-jni-panama-ffm.md](../06-ecosystem-and-cross-cutting/04-jni-panama-ffm.md); this file focuses on the allocation/memory-management angle.

---

## 🎯 What a senior candidate must know

- **Why go off-heap at all**: escape GC (no marking/copying of big long-lived data), avoid copy on native I/O (zero-copy), >heap-size datasets, memory-mapped files, sharing across processes. Costs: manual lifecycle, no compaction (native fragmentation), serialization at the boundary, harder debugging.
- **Direct ByteBuffers** (`ByteBuffer.allocateDirect`): malloc'ed native memory + small heap shell object; freed only when the shell is GC'd (via a `Cleaner` — topic 1.06 in action!) — the infamous failure mode: **plenty of free heap → no GC → native memory never freed → process RSS grows**; `-XX:MaxDirectMemorySize` cap and the `System.gc()` hack inside the JDK (`Bits.reserveMemory` retry loop — great expert detail). 2 GB limit per buffer (int indexing).
- **Why NIO needs direct buffers**: the OS can't safely DMA into a movable heap array (GC relocates it); heap buffers get copied to a temporary direct buffer on every I/O — the hidden copy senior candidates should know about.
- **`Unsafe.allocateMemory/freeMemory`**: raw malloc surface — historically Netty/Chronicle territory; being removed (**JEP 471**) → migration to FFM.
- **MemorySegment + Arena (FFM, JEP 454)** — the modern answer: deterministic scoped deallocation (confined/shared arenas), 64-bit sizes, bounds + use-after-free (temporal) safety, mapped segments for mmap; how it fixes every direct-ByteBuffer flaw. Slicing without copying; structured layouts.
- **Who allocates off-heap under you anyway** (the "hidden native memory" list): metaspace, code cache, thread stacks, GC side structures, direct buffers from your HTTP/DB clients (Netty pools!), zip/inflater buffers, JNI libraries. Diagnosis tool: **NMT** (`-XX:NativeMemoryTracking=summary` + `jcmd VM.native_memory`) — but NMT doesn't see non-JVM malloc (Netty/JNI) → `pmap`/`jemalloc` profiling as the next level.
- **Netty's PooledByteBufAllocator** as the real-world case study: arena-based pooling (jemalloc-inspired: chunks → pages → subpage bins, per-thread caches) — why pooling *off-heap* memory makes sense even when pooling heap objects doesn't (malloc/free cost + zeroing + fragmentation, not GC).
- Container reality: off-heap counts against the cgroup limit — the classic "`-Xmx` fits but the pod gets OOM-killed" incident (full treatment in topic 06).

## 📚 Resources

### Books / chapters
- **Netty in Action** — Norman Maurer — Ch. on ByteBuf & allocators (the production off-heap pattern).
- **Optimizing Java** — native memory & NIO sections.

### Videos & conference talks
- 🎥 **"Project Panama: Memory Segments"** — Maurizio Cimadamore (JVMLS/Devoxx) — the design rationale vs ByteBuffer/Unsafe, from the lead.
- 🎥 **Norman Maurer — Netty memory/allocator talks** (search *Maurer Netty ByteBuf Devoxx*) — pooled off-heap allocation in anger.
- 🎥 **"Memory footprint of a Java process"** — Andrei Pangin (search; also his QCon/Joker versions) — THE talk for "where did my RSS go": NMT, malloc arenas, all the hidden consumers.

### Articles & official docs
- **JEP 454 (FFM)**: https://openjdk.org/jeps/454 — re-read the MemorySegment/Arena sections with allocation eyes.
- **JEP 471: Deprecate Unsafe memory-access methods**: https://openjdk.org/jeps/471 — includes the official migration table Unsafe→FFM/VarHandle.
- **Netty docs — Reference-counted objects & pooled allocator**: https://netty.io/wiki/reference-counted-objects.html
- **Baeldung – "Guide to ByteBuffer"** / direct buffers: https://www.baeldung.com/java-bytebuffer · **"Native Memory Tracking"**: https://www.baeldung.com/native-memory-tracking-in-jvm
- **`Bits.reserveMemory` source** (openjdk/jdk, `java.nio.Bits`) — read the direct-memory accounting + GC-retry loop; 40 lines that answer three interview questions.

## ❓ Senior interview questions

1. Direct vs heap ByteBuffer — allocation cost, I/O path, deallocation mechanism, and the failure mode of each.
2. Why can a JVM with 4 GB free heap leak native memory through direct buffers? What two mechanisms limit/reclaim it?
3. Why can't the OS write directly into a heap `byte[]` during async I/O? What does NIO do about it?
4. How does an FFM Arena fix the direct-ByteBuffer deallocation problem? Compare confined vs shared arena semantics.
5. RSS is 3 GB, `-Xmx` is 1 GB — enumerate every place the other 2 GB could be, and the tool to attribute each.
6. Why does Netty pool its off-heap buffers when object pooling is generally discouraged on-heap?
7. What is reference counting doing in Netty (`retain/release`) — why not rely on GC?
8. Your team wants to move a 50 GB cache off-heap — walk the design discussion (serialization cost, fragmentation, mmap, vs just using a bigger heap with ZGC, vs Redis).

## 🗺️ Study path

1. Read `Bits.reserveMemory` source + JEP 454's Arena section — the old and new deallocation stories side by side.
2. Demo: allocate direct buffers in a loop with a small `-XX:MaxDirectMemorySize` — observe the OOM and the GC-retry behavior; redo with an FFM confined Arena and watch deterministic free.
3. Run NMT on a real service; account for every category; then compare with `pmap -x` to find what NMT misses.
4. Watch Pangin's memory-footprint talk.
5. Rehearse Q5 — the RSS-attribution question is a staple of senior SRE-flavored interviews.

## 📝 My notes

_(your own notes while studying)_
