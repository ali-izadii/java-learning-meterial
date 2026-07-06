# Streams & Lambdas Internals: Spliterator, Laziness, Capture

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Pipeline anatomy**: source (**Spliterator**) → stateless ops (map/filter) → stateful ops (sorted/distinct/limit) → terminal op; nothing runs until the terminal op (**laziness**); ops fuse into a single pass (no intermediate collections!) — the `peek` print-order demo proves it.
- **Spliterator**: `tryAdvance`, `trySplit`, characteristics (SIZED, ORDERED, DISTINCT...) — how sources describe themselves so the pipeline can optimize/parallelize; why ArrayList splits perfectly and iterators don't.
- Streams are **single-use**; short-circuiting (`findFirst`, `anyMatch`, `limit`); encounter order vs `unordered()` performance implications.
- **Primitive streams** (IntStream...) — avoiding boxing storms; `mapToInt` before reduction.
- **Collectors**: supplier/accumulator/combiner contract; `groupingBy` + downstream collectors; `toMap` merge-function trap (throws on duplicate keys); mutable reduction (`collect`) vs `reduce` (immutable fold) — when misusing reduce is O(n²) or broken in parallel.
- **Lambda capture**: effectively final rule — *why* (variables are *copied* into the lambda object/method args; no closure over mutable stack); capturing lambdas allocate, non-capturing are cached singletons (ties to invokedynamic topic 2.03).
- Checked exceptions in lambdas (the friction and standard workarounds); when a plain loop is simply better.
- `Optional` semantics: return-type only, not fields/params (Stuart Marks' rules).

## 📚 Resources

### Books / chapters
- **Modern Java in Action** — Urma, Fusco, Mycroft — Ch. 4–7 (streams end-to-end, internals + parallel).
- **Effective Java** — Items 42–48 (lambdas & streams chapter — when to use, when not).

### Videos & conference talks
- 🎥 **"Stream API deep dives"** — José Paumard (Devoxx, e.g., *"Collectors in the Wild"* / JEP Café stream episodes) — the API's best teacher.
- 🎥 **"Optional: The Mother of All Bikesheds"** — Stuart Marks (Devoxx) — Optional semantics from the source; interview-quotable rules.
- 🎥 **"Lambdas: A Peek Under the Hood"** — Brian Goetz — capture & translation mechanics (shared with topic 2.03).

### Articles & official docs
- **`java.util.stream` package javadoc** — the package summary is a precise mini-book on stream semantics (side-effects, ordering, reduction): https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/stream/package-summary.html — genuinely read this.
- **Baeldung – "The Java 8 Stream API Tutorial"**: https://www.baeldung.com/java-8-streams · **"Introduction to Spliterator"**: https://www.baeldung.com/java-spliterator · **"Java 8 Collectors"**: https://www.baeldung.com/java-8-collectors
- **Brian Goetz — "State of the Lambda: Libraries Edition"** (design rationale): search "State of the Lambda libraries".

## ❓ Senior interview questions

1. Prove streams are lazy and fused: what does `peek` print for `list.stream().peek(A).filter(f).peek(B).findFirst()`?
2. What is a Spliterator? What do its characteristics enable (SIZED → exact-size arrays, SORTED → skip sorting)?
3. Why must captured local variables be effectively final? What does the lambda actually capture (value vs variable)?
4. `reduce` vs `collect` — contracts, and why building a String/List via `reduce` is wrong.
5. The `Collectors.toMap` duplicate-key trap — what happens and how do you fix it?
6. When does a stream pipeline allocate boxed objects, and how do primitive streams avoid it?
7. Stateless vs stateful intermediate ops — why do `sorted`/`distinct` change the game (buffering, parallel cost)?
8. Where does Optional belong and not belong in an API?

## 🗺️ Study path

1. Read the `java.util.stream` package javadoc summary (seriously — 45 minutes, interview-precise language).
2. Run the laziness/fusion `peek` experiment; then a `trySplit` experiment on ArrayList vs LinkedList spliterators.
3. Read Modern Java in Action Ch. 5–6; Effective Java Items 42–48.
4. Drill Collectors: groupingBy with downstream counting/mapping, toMap with merge fn, partitioningBy.
5. Watch Marks' Optional talk; write your own "stream vs loop" decision rules in My Notes.

## 📝 My notes

_(your own notes while studying)_
