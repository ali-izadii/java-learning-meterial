# JVM Architecture & Runtime Data Areas (Heap, Stacks, Metaspace, PC, Code Cache)

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- The full picture: **class loader subsystem → runtime data areas → execution engine (interpreter + JIT) + native interface**.
- Per-thread areas: **PC register, JVM stack (frames: local variables, operand stack, frame data), native method stack** — and why `StackOverflowError` vs `OutOfMemoryError` happen.
- Shared areas: **heap** (young: eden + survivors / old gen) and **metaspace** (why PermGen was removed in Java 8 — JEP 122; class metadata now in native memory, interned strings & statics moved to heap earlier, in Java 7).
- **Code cache**: where JIT-compiled code lives; what happens when it fills up (compilation stops → performance cliff).
- Non-heap native memory: thread stacks, direct `ByteBuffer`s, metaspace, GC structures — why an app can exceed `-Xmx` in RSS. Use **Native Memory Tracking** (`-XX:NativeMemoryTracking=summary`).
- Key flags: `-Xms/-Xmx`, `-Xss`, `-XX:MaxMetaspaceSize`, `-XX:ReservedCodeCacheSize`.
- What lives where: object instances & arrays → heap; references & primitives in locals → stack frame; `static` fields → part of class representation (heap); string literals → string pool (heap).

## 📚 Resources

### Books / chapters
- **The Java® Virtual Machine Specification, Chapter 2 "The Structure of the JVM"** — the ground truth, short and readable: https://docs.oracle.com/javase/specs/jvms/se21/html/jvms-2.html
- **Inside the Java Virtual Machine** — Bill Venners, Ch. 5 "The Java Virtual Machine" (free online at artima.com). Old, but still the best mental model of frames/operand stack.
- **Optimizing Java** (Evans, Gough, Newland) — Ch. 2 "Overview of the JVM" & Ch. 6 "Understanding Garbage Collection" intro sections.

### Videos & conference talks
- 🎥 **"JVM Anatomy 101" / "A Practical Introduction to the JVM"** — search YouTube for *Devoxx JVM internals introduction* (Rafael Winterhalter's *"The Definitive Guide to Java Agents"* also opens with a great architecture recap).
- 🎥 **"Everything you need to know about the JVM (but were afraid to ask)"** — Ben Evans, Devoxx/GOTO — perfect senior-level overview.
- 🎥 **Inside Java Newscast** (José Paumard / Nicolai Parlog, official *Java* YouTube channel) — episodes on metaspace, memory, and JVM flags.

### Articles & official docs
- **JVM Anatomy Quarks** — Aleksey Shipilëv: https://shipilev.net/jvm/anatomy-quarks/ — short, authoritative posts; read quarks on TLAB allocation (#4), NMT, and stacks.
- **JEP 122: Remove the Permanent Generation** — https://openjdk.org/jeps/122 — why metaspace exists; classic interview question.
- **Baeldung – "JVM Memory Areas" / "Stack Memory and Heap Space in Java"**: https://www.baeldung.com/java-stack-heap
- **Oracle: Native Memory Tracking guide**: https://docs.oracle.com/en/java/javase/21/vm/native-memory-tracking.html

## ❓ Senior interview questions

1. Walk me through what memory areas exist in the JVM and which are per-thread vs shared.
2. Why was PermGen removed and what replaced it? What still goes OOM in metaspace?
3. A container gets OOM-killed but the heap never exceeded `-Xmx`. Explain where the extra memory can come from and how you'd diagnose it.
4. What exactly is in a stack frame? What's the difference between the operand stack and local variables?
5. What is the code cache, and what symptom appears when it's exhausted?
6. Where do string literals live? Where do `static` fields live?
7. What is a TLAB and why does it make allocation nearly free?

## 🗺️ Study path

1. Read JVMS Chapter 2 (1–2 hours) — take notes on each runtime area.
2. Read Shipilëv's quarks on TLABs and native memory.
3. Run a small app with `-XX:NativeMemoryTracking=summary` + `jcmd <pid> VM.native_memory summary` and map the output to theory.
4. Watch the Ben Evans overview talk.
5. Answer the interview questions above out loud.

## 📝 My notes

_(your own notes while studying)_
