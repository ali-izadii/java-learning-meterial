# Generics Deep-Dive: Erasure, Wildcards, PECS, Bridge Methods

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Erasure**: type parameters exist only at compile time; erased to bound (or Object); compiler inserts `checkcast`; consequences — no `new T()`, no `T.class`, no `instanceof List<String>`, one class for all instantiations, generic array creation forbidden (`new T[]`).
- Why erasure (migration compatibility with pre-generics code) vs reified generics (C#) — the design-tradeoff question.
- **Invariance**: `List<Integer>` is NOT a `List<Number>` — and why arrays being covariant is the broken design (`ArrayStoreException` at runtime vs compile-time safety).
- **Wildcards & PECS**: `? extends T` = Producer (read T, write nothing but null); `? super T` = Consumer (write T, read Object); `Collections.copy(List<? super T> dest, List<? extends T> src)` as the canonical signature; unbounded `?` vs raw types (raw disables ALL generic checking — never use).
- **Bridge methods**: synthetic methods the compiler generates so overriding works after erasure (e.g., `compareTo(Object)` bridging to `compareTo(MyType)`) — visible in `javap`.
- **Type tokens**: `Class<T>` parameters, and super-type tokens (`new TypeReference<List<String>>(){}` — how Jackson/Gson recover generic types via `getGenericSuperclass`); reflection CAN see generic signatures in class metadata (fields, methods, superclass) — "erasure" is not total.
- Generic method type inference, `<T extends Comparable<? super T>>` — read and explain the JDK's own signatures.
- Heap pollution & `@SafeVarargs` (varargs create arrays of erased type).

## 📚 Resources

### Books / chapters
- **Effective Java (3rd ed.)** — **Ch. 5 entire (Items 26–33)** — the single best generics text; PECS is Item 31.
- **Java Generics and Collections (2nd ed.)** — Naftalin & Wadler — for the full theory.

### Videos & conference talks
- 🎥 **"Getting the Most out of Java Generics"**-type talks — search Devoxx; also Kevlin Henney / Venkat Subramaniam generics talks for wildcard intuition.
- 🎥 Brian Goetz on **erasure & Valhalla** (JVMLS "specialization" talks) — why erasure happened and how Valhalla revisits it; great senior framing.

### Articles & official docs
- **Angelika Langer's Java Generics FAQ** — the legendary exhaustive reference: http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html — use as lookup, not linear read.
- **Baeldung – "The Basics of Java Generics"**: https://www.baeldung.com/java-generics · **"Type Erasure"**: https://www.baeldung.com/java-type-erasure · **"Super Type Tokens"**: https://www.baeldung.com/java-super-type-tokens
- **Oracle Generics tutorial (wildcards section)**: https://docs.oracle.com/javase/tutorial/java/generics/
- **Neal Gafter — "Super Type Tokens"** (the original blog post; search "gafter super type tokens").

## ❓ Senior interview questions

1. What does erasure actually erase, and what generic information survives in the class file?
2. Why can't you write `new T()` or `T[] arr = new T[10]`? Show workarounds (Class<T> factory, `Array.newInstance`).
3. Explain PECS with `Collections.copy`'s signature. Why can't you `add` to a `List<? extends Number>`?
4. Why are Java arrays covariant and generics invariant? Which design is safer and why?
5. What is a bridge method? Show when one is generated.
6. How does Jackson deserialize `List<User>` despite erasure? (super type tokens / generic metadata)
7. Explain `<T extends Comparable<? super T>>` in `Collections.sort` — why the `? super`?
8. What is heap pollution? What does `@SafeVarargs` promise?

## 🗺️ Study path

1. Read Effective Java Ch. 5 (Items 26–33) — one item per sitting, code each example.
2. `javap -c` a generic class: find checkcasts, erased signatures, a bridge method.
3. Implement a small type-safe heterogeneous container (EJ Item 33) and a super-type token.
4. Drill wildcard puzzles: write signatures for copy/merge/max functions and verify compilation.
5. Rehearse Q3 and Q7 — wildcard fluency is the visible line between mid and senior.

## 📝 My notes

_(your own notes while studying)_
