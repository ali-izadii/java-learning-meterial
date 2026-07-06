# Object Layout in Memory (Headers, Compressed Oops, Alignment, JOL)

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Object = **header + fields + padding**. Header = **mark word** (hash code, GC age, lock state) + **class pointer** (klass word); arrays add a length field.
- **Compressed oops** (`-XX:+UseCompressedOops`): 32-bit references on 64-bit JVMs for heaps < ~32 GB; why setting `-Xmx33g` can *reduce* usable memory.
- **8-byte alignment** and field reordering by the JVM; why field declaration order ≠ memory order.
- How to measure: **JOL (Java Object Layout)** tool — `ClassLayout.parseClass(...)`.
- Cost intuition: plain `Object` ≈ 16 bytes, `Integer` ≈ 16 bytes vs 4 bytes for `int` — why boxing and pointer-chasing hurt (cache locality).
- **@Contended / false sharing**: two hot fields on the same cache line written by different threads.
- Modern direction: **JEP 450 Compact Object Headers (Project Lilliput)** — shrinking headers to 8 bytes; and Valhalla value classes (see topic 5.07).

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 12 sections on object layout & memory efficiency.
- **The Well-Grounded Java Developer (2nd ed.)** — JVM internals chapter (object anatomy section).

### Videos & conference talks
- 🎥 **"Java Objects Inside Out"** — Aleksey Shipilëv (Devoxx/Joker) — THE talk for this topic.
- 🎥 **"Compact Object Headers in JDK — Project Lilliput"** — Roman Kennke (JVMLS / Devoxx, on the *Java* channel).

### Articles & official docs
- **"Java Objects Inside Out"** — Shipilëv (article version): https://shipilev.net/jvm/objects-inside-out/ — read this first; everything else is commentary.
- **JVM Anatomy Quark #23: Compressed References**: https://shipilev.net/jvm/anatomy-quarks/23-compressed-references/
- **JOL project** — official OpenJDK code tool: https://openjdk.org/projects/code-tools/jol/ (run the samples!)
- **JEP 450: Compact Object Headers**: https://openjdk.org/jeps/450
- **Baeldung – "Memory Layout of Objects in Java"**: https://www.baeldung.com/java-memory-layout

## ❓ Senior interview questions

1. What's inside an object header? What is the mark word reused for?
2. How much memory does `new Object()` take? An `Integer`? An `int[10]` vs `Integer[10]`?
3. Explain compressed oops. Why can a 34 GB heap perform worse than a 30 GB one?
4. Why might the JVM reorder your fields? What is padding for?
5. What is false sharing and how do you detect/fix it?
6. Where is `hashCode()` cached, and what happens to it when the object's monitor is inflated (identity hash + locking conflict)?

## 🗺️ Study path

1. Read Shipilëv's "Java Objects Inside Out" (long — budget 2–3 hours, it's worth it).
2. Add JOL to a scratch project; print layouts of `Object`, `Integer`, a class with mixed field types, an array.
3. Watch the "Java Objects Inside Out" talk to consolidate.
4. Read JEP 450 to be able to mention where headers are heading (great senior-signal).
5. Answer the interview questions with JOL output as proof.

## 📝 My notes

_(your own notes while studying)_
