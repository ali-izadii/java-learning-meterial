# Bytecode & the Class File Format; javap; the Stack Machine Model

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Class file anatomy: magic (`0xCAFEBABE`), version, **constant pool**, access flags, fields, methods (each with a `Code` attribute: max_stack, max_locals, bytecode, exception table), attributes.
- The JVM is a **stack machine**: instructions push/pop the **operand stack**; locals live in a numbered **local variable table** (slot 0 = `this` for instance methods).
- Core instruction families: loads/stores (`aload_0`, `istore_1`), arithmetic (`iadd`), object ops (`new`, `getfield`, `putfield`), control flow (`if_icmpge`, `goto`), and the **five invokes**: `invokevirtual`, `invokespecial` (constructors/private/super), `invokestatic`, `invokeinterface`, `invokedynamic`.
- Reading `javap -c -v` output fluently — including the constant pool and `StackMapTable` (what the verifier uses).
- What compiles to what: string concat → `invokedynamic` (JDK 9+, JEP 280); `synchronized` block → `monitorenter/monitorexit`; try-with-resources / finally duplication; autoboxing → `Integer.valueOf`.
- Why generics leave no trace except `Signature` attributes and `checkcast` (see Generics topic).
- Bytecode manipulation ecosystem: **ASM** (what everything is built on), **ByteBuddy** (what you'd actually use), and the new **Class-File API (JEP 484, JDK 24)** in the JDK itself.

## 📚 Resources

### Books / chapters
- **The Java® Virtual Machine Specification, Ch. 4 (class file format) + Ch. 6 (instruction set)**: https://docs.oracle.com/javase/specs/jvms/se21/html/jvms-4.html — use as reference, not cover-to-cover.
- **Inside the Java Virtual Machine** — Venners, Ch. 10+ (instruction set walkthroughs with applets of the operand stack — dated visuals, timeless content).

### Videos & conference talks
- 🎥 **"Java Bytecode for Discriminating Developers"** / **"Mastering Java Bytecode"** — Anton Arhipov (JavaZone/Devoxx) — the standard intro talk, very interview-aligned.
- 🎥 **"The ASM library"** deep-dives — search YouTube *ASM bytecode Devoxx*; Rafael Winterhalter's ByteBuddy talks cover the practical layer.

### Articles & official docs
- **Baeldung – "Introduction to Java Bytecode" / "javap"**: https://www.baeldung.com/java-class-view-bytecode
- **ASM user guide (PDF)** — the best written explanation of the class file model: https://asm.ow2.io/asm4-guide.pdf (Ch. 1–3 only).
- **JEP 280: Indify String Concatenation**: https://openjdk.org/jeps/280
- **JEP 484: Class-File API**: https://openjdk.org/jeps/484 — mentioning this is a strong currency signal.

## ❓ Senior interview questions

1. Sketch the structure of a `.class` file. What's in the constant pool?
2. Explain the operand stack vs local variable table. What is in slot 0 of an instance method?
3. Name the five invoke instructions and when each is emitted.
4. What bytecode does `synchronized(obj) {}` compile to, and why is there an exception-table entry?
5. What does `javap -v` show for `String s = a + b;` on a modern JDK, and why did string concat move to `invokedynamic`?
6. Why is `i++` not atomic? Show it at the bytecode level (iload/iinc vs getfield-iadd-putfield).
7. What is the StackMapTable for? What does the verifier check?

## 🗺️ Study path

1. `javap -c -v` a trivial class; identify constant pool entries, max_stack/max_locals, each instruction.
2. Do the same for: a loop, a `synchronized` block, string concat, autoboxing, try/finally — write down the surprises.
3. Read ASM guide Ch. 1–2 for the formal model.
4. Watch Anton Arhipov's talk.
5. Rehearse Q6 (i++ at bytecode level) — very common concurrency-meets-bytecode question.

## 📝 My notes

_(your own notes while studying)_
