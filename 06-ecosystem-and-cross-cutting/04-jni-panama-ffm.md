# Native Interop: JNI → Panama / Foreign Function & Memory API

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **JNI classic model**: `native` methods, `System.loadLibrary`, generated headers, `JNIEnv*`; costs — transition overhead, no JIT visibility across the boundary, GC can't move pinned things / critical regions, error-prone manual memory & reference management (local/global refs); why JNI code is a crash/leak magnet.
- **FFM API (JEP 454, final in JDK 22)** — the replacement: **`MemorySegment`** (bounded, lifetime-scoped off-heap memory), **`Arena`** (confined/shared/auto lifetimes — deterministic deallocation vs `Unsafe`/ByteBuffer's GC-dependence), **`Linker`** + **method handles** to call C functions, **`jextract`** to generate bindings from headers; structured layouts (`MemoryLayout`, VarHandles into structs).
- Why FFM > JNI: pure-Java (no C glue), safer (bounds + temporal checks), often faster (JIT-optimizable downcalls), and > `sun.misc.Unsafe` (which is being **degraded/removed — JEP 471** deprecates its memory-access methods; strong currency signal).
- Off-heap memory landscape: direct ByteBuffers (2GB limit, GC-tied cleanup) vs MemorySegment (64-bit, arena-scoped) — why Netty/Kafka-style systems care.
- **Restricted methods** & `--enable-native-access` (integrity-by-default arc, JEP 472).
- When native interop is the right call at all: existing native libs (crypto, ML runtimes, GPUs), memory-mapped IO, not "for speed" by default.

## 📚 Resources

### Books / chapters
- Book coverage is thin/dated for FFM — primary sources below are the material. (For JNI history: **Java Native Interface** by Sheng Liang, free spec-book.)

### Videos & conference talks
- 🎥 **"Project Panama: Foreign Function & Memory API"** — Maurizio Cimadamore (Panama lead; JVMLS/Devoxx, multiple years) — the definitive talks.
- 🎥 **José Paumard — JEP Café on FFM** (official *Java* channel) — practical code-along level.
- 🎥 **"The Panama Dojo"** — Per Minborg (Devoxx) — hands-on patterns.

### Articles & official docs
- **JEP 454: Foreign Function & Memory API**: https://openjdk.org/jeps/454 — read fully; excellent explanatory JEP with code.
- **JEP 471: Deprecate Unsafe Memory-Access Methods**: https://openjdk.org/jeps/471 · **JEP 472: Prepare to Restrict JNI**: https://openjdk.org/jeps/472
- **Oracle FFM guide**: https://docs.oracle.com/en/java/javase/22/core/foreign-function-and-memory-api.html
- **jextract**: https://github.com/openjdk/jextract
- **Baeldung – "Foreign Memory Access API"** / FFM guides: https://www.baeldung.com/java-foreign-memory-access
- **JNI spec** (reference): https://docs.oracle.com/en/java/javase/21/docs/specs/jni/

## ❓ Senior interview questions

1. What's wrong with JNI? Name four distinct problems (glue code, safety, performance opacity, lifecycle).
2. Explain Arena lifetimes (confined/shared/auto) — what problem does deterministic scope-based deallocation solve vs direct ByteBuffers?
3. How does the FFM Linker call a C function without any C glue code? What role do method handles play?
4. MemorySegment vs `Unsafe` vs direct ByteBuffer — safety and capability comparison; why is Unsafe being removed now?
5. How do you model and access a C struct from Java with MemoryLayout + VarHandles?
6. What is a restricted method and why does `--enable-native-access` exist?
7. When would you *choose* native interop in a system design, and when is it premature?

## 🗺️ Study path

1. Read JEP 454 end-to-end.
2. Hands-on: allocate a MemorySegment in a confined Arena, write/read a struct via layout VarHandles; then call `strlen` via Linker (the canonical hello-world).
3. Try jextract on a small header.
4. Watch one Cimadamore talk.
5. Read JEP 471/472 summaries for the "where this is going" narrative — that arc (Unsafe → FFM, JNI → restricted) is the expert-level story to tell.

## 📝 My notes

_(your own notes while studying)_
