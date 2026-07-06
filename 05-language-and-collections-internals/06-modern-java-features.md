# Records, Sealed Types, Pattern Matching (Modern Java Features)

> **Level:** 🟡 Deep-dive (expected knowledge in 2026 — shows you're not stuck on Java 8)
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Records (JEP 395, JDK 16)**: nominal tuples — canonical constructor, **compact constructor** (validation/normalization), accessors, equals/hashCode/toString **generated via invokedynamic (ObjectMethods bootstrap)**, shallowly immutable (fields final, contents aren't), can implement interfaces, no inheritance, local records; serialization is special-cased (canonical constructor — safer than classic).
- **Sealed types (JEP 409, JDK 17)**: `sealed ... permits` / `non-sealed` / `final`; closed hierarchies → compiler-checked **exhaustive switch without default** — the killer combo with pattern matching (algebraic data types in Java).
- **Pattern matching**: `instanceof` patterns (JEP 394), **switch patterns (JEP 441, JDK 21)** — type patterns, guarded patterns (`when`), null handling in switch (`case null`), **record deconstruction patterns (JEP 440)** and nesting; exhaustiveness rules.
- **Switch expressions (JEP 361, JDK 14)**: arrow form, `yield`, no fall-through.
- Text blocks (JEP 378); `var` (JEP 286) — type inference rules and style discipline.
- The design narrative for interviews: **data-oriented programming** — model data as records+sealed hierarchies, behavior in switch patterns; when it beats visitor pattern / class hierarchies (Goetz's "Data-Oriented Programming" essay).
- LTS storyline: 8 → 11 → 17 → 21 → 25 and what landed at each (a one-liner per LTS is a great interview asset).

## 📚 Resources

### Books / chapters
- **The Well-Grounded Java Developer (2nd ed.)** — modern language features chapters.
- **Effective Java** — still applies: Item 17 immutability arguments map directly onto records.

### Videos & conference talks
- 🎥 **"Java Language Futures"** / **"Data Oriented Programming in Java"** — Brian Goetz (Devoxx/JVMLS) — the architect explaining the *why* behind all of it; top senior prep.
- 🎥 **Nicolai Parlog — Inside Java Newscast** & **José Paumard — JEP Café** (official *Java* channel) — one crisp episode per feature; the fastest way to cover ground.
- 🎥 **Venkat Subramaniam — "Modern Java"-tour talks** — great for fluency and idioms.

### Articles & official docs
- **Brian Goetz — "Data Oriented Programming in Java"** (InfoQ article): https://www.infoq.com/articles/data-oriented-programming-java/ — read this; it's the essay that ties records+sealed+patterns together.
- **JEP 395 (Records)**: https://openjdk.org/jeps/395 · **JEP 409 (Sealed)**: https://openjdk.org/jeps/409 · **JEP 441 (Pattern switch)**: https://openjdk.org/jeps/441 · **JEP 440 (Record patterns)**: https://openjdk.org/jeps/440
- **nipafx.dev** (Parlog's site) — deep feature guides: https://nipafx.dev
- **Baeldung – "Java Record Keyword"**: https://www.baeldung.com/java-record-keyword · **"Sealed Classes and Interfaces"**: https://www.baeldung.com/java-sealed-classes-interfaces

## ❓ Senior interview questions

1. What exactly does a record generate, and how are its equals/hashCode implemented under the hood (invokedynamic)?
2. Compact constructor vs canonical constructor — show a validation example. Are records deeply immutable?
3. Why do sealed interfaces enable switch without `default`, and why is omitting `default` *desirable*? (new subtype → compile error, not silent fallthrough)
4. Model a payment domain (Card/Cash/Transfer) with sealed + records + pattern switch vs the visitor pattern — compare maintainability.
5. What can a guarded pattern (`case Circle c when c.radius() > 10`) do? How does `case null` change switch semantics?
6. Records and frameworks: what changes for Jackson/JPA (no no-arg constructor, no setters)?
7. What landed in each LTS: 11, 17, 21? One highlight each.
8. When is `var` appropriate? Rules and readability tradeoffs.

## 🗺️ Study path

1. Read Goetz's "Data Oriented Programming" article.
2. Build a small sealed + records + pattern-switch domain model (expression evaluator or payment types); then delete `default` branches and add a new subtype to feel exhaustiveness checking.
3. Read the four JEPs' motivation sections (395/409/440/441).
4. Binge the relevant JEP Café / Inside Java Newscast episodes.
5. Rehearse Q4 — the DOP-vs-visitor comparison is prime senior-interview material.

## 📝 My notes

_(your own notes while studying)_
