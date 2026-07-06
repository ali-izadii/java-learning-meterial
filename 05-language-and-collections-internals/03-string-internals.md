# String Internals: Pool/Interning, Immutability, Compact Strings, Concatenation

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Immutability**: final class, private final `byte[]` (since JDK 9), no mutators — why: security, safe sharing across threads (final-field semantics), hashCode caching, pool feasibility.
- **String pool**: heap-resident table of canonical strings; **literals are interned automatically at class loading/resolution**; `new String("a")` creates a second object; `intern()` returns the canonical one. `==` vs `equals` — and *why* `"a" + "b"` (constant-folded at compile time) == `"ab"` but `a + b` at runtime is not.
- **Compact Strings (JEP 254, JDK 9)**: `byte[]` + coder (LATIN1/UTF16) instead of `char[]` — halves memory for ASCII-heavy apps; why `char[]`→`byte[]` mattered at scale.
- **Concatenation**: compile-time constant folding; runtime `+` → **invokedynamic to StringConcatFactory (JEP 280, JDK 9+)**, not StringBuilder chains; **the loop rule still holds** — `+=` in a loop is O(n²), use StringBuilder.
- StringBuilder vs StringBuffer (dead) vs StringJoiner/`String.join`/`Collectors.joining`.
- `substring` history: shared-backing-array leak pre-7u6 vs copy today — a classic "do you know the evolution" question.
- Hash caching (`hash` field, computed lazily, 0 sentinel); string **deduplication** in G1 (`-XX:+UseStringDeduplication`) vs interning — different mechanisms (dedup shares byte[], intern shares String).
- Strings and memory: strings are typically the #1 heap consumer in real apps — ties into heap-dump analysis.

## 📚 Resources

### Books / chapters
- **Effective Java** — Item 17 (minimize mutability — String is the case study), Item 63 (beware string concatenation performance).
- **Java Performance (2nd ed.)** — Oaks — string efficiency sections (dedup, interning costs).

### Videos & conference talks
- 🎥 **"java.lang.String Catechism"** — Aleksey Shipilëv (Devoxx/Joker) — THE string-internals talk: pool, compact strings, concat, myths, with benchmarks.

### Articles & official docs
- **JEP 254: Compact Strings**: https://openjdk.org/jeps/254
- **JEP 280: Indify String Concatenation**: https://openjdk.org/jeps/280 — motivation section explains why `+` no longer compiles to StringBuilder.
- **Baeldung – "Guide to the Java String Pool"**: https://www.baeldung.com/java-string-pool · **"Why Strings are Immutable"**: https://www.baeldung.com/java-string-immutable
- **Shipilëv — string-related JVM Anatomy Quarks & "String density" material**: https://shipilev.net/

## ❓ Senior interview questions

1. Why is String immutable? Give the security, concurrency, and caching arguments.
2. Exactly how many String objects does `String s = new String("hello");` involve? Where does each live?
3. `"a"+"b" == "ab"` is true but `a+b == "ab"` is false — explain both, at the compiler and pool level.
4. What changed with Compact Strings and why was it a big deal?
5. What bytecode does `s1 + s2` produce on JDK 8 vs JDK 9+? Why the change (JEP 280's "compile once, optimize forever" argument)?
6. When is `intern()` useful and when is it a footgun? Contrast with G1 string deduplication.
7. Why is `+=` concatenation in a loop O(n²)?
8. Why did `substring` change from view to copy in 7u6?

## 🗺️ Study path

1. Watch Shipilëv's "String Catechism" (or read the slide deck on shipilev.net).
2. `javap -c` a class doing `a + b` — find the invokedynamic + StringConcatFactory; compare with a JDK 8 build if available.
3. Write the pool demos for Q2/Q3 and verify with `==`.
4. Read JEP 254 + 280 motivation sections.
5. Rehearse Q1–Q3 — asked at *every* level, but seniors are expected to answer with pool/bytecode mechanics.

## 📝 My notes

_(your own notes while studying)_
