# Collections Framework Internals: ArrayList, LinkedList, TreeMap, ArrayDeque

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **ArrayList**: backing array, growth ×1.5 (`oldCapacity + (oldCapacity >> 1)`), amortized O(1) append; `remove(i)` = arraycopy shift O(n); `ensureCapacity` for bulk loads; `subList` is a *view* (mutations + modCount traps).
- **LinkedList**: doubly-linked, O(1) add/remove *at a known node*, O(n) index access — and the senior truth: **almost always loses to ArrayList in practice** (cache locality, allocation per node); legitimate uses are rare (ListIterator mid-iteration removal).
- **ArrayDeque**: circular array, head/tail indices, the right stack/queue (not `Stack`, not LinkedList); no nulls (null = empty sentinel).
- **TreeMap/TreeSet**: red-black tree, O(log n), Comparator vs Comparable, **comparator must be consistent with equals** (else Set/Map contract weirdness); NavigableMap API (`floorKey`, `ceilingKey`, range views) — interview favorite for "find nearest" problems.
- **PriorityQueue**: binary heap in array, O(log n) offer/poll, O(n) contains; iteration order is NOT sorted (top trap).
- Iterators: fail-fast (modCount) vs weakly consistent; `Iterator.remove()` is the only safe structural removal mid-iteration (or removeIf).
- `Arrays.asList` (fixed-size view) vs `List.of` (fully immutable, null-hostile) vs `Collections.unmodifiableList` (view over mutable) — the three "immutable list" traps.
- Choosing structures by operation profile — the actual senior skill; know your Big-O table cold.

## 📚 Resources

### Books / chapters
- **Java Generics and Collections (2nd ed.)** — Naftalin & Wadler — the collections framework book.
- **Effective Java** — Items 25–28 area (arrays vs lists), Item 47 (Collection over Stream as return), Item 54 (empty collections, not nulls).

### Videos & conference talks
- 🎥 **"Collections Refueled"** — Stuart Marks (Devoxx/JavaOne) — evolution + best practices from the collections owner; excellent senior material.
- 🎥 Defog Tech / similar **"ArrayList vs LinkedList internals"** source-reading videos for visual walkthroughs.

### Articles & official docs
- **Collections Framework Overview** (official): https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/doc-files/coll-index.html
- **Baeldung – "Guide to ArrayList"**: https://www.baeldung.com/java-arraylist · **"ArrayDeque"**: https://www.baeldung.com/java-array-deque · **"TreeMap"**: https://www.baeldung.com/java-treemap
- **`AbstractList`/`ArrayList` source** — read `grow()`, `add()`, `remove()`; TreeMap's `fixAfterInsertion` if you want RB-tree details.
- **Baeldung – "Arrays.asList vs List.of"**: https://www.baeldung.com/java-arrays-aslist-vs-list-of

## ❓ Senior interview questions

1. How does ArrayList grow? Why 1.5× and what does amortized O(1) mean?
2. "LinkedList is better for frequent insertions" — attack or defend with mechanical-sympathy arguments.
3. Why is ArrayDeque preferred over Stack and LinkedList? What's wrong with `java.util.Stack`?
4. TreeMap: what balances it, what are the operation costs, and what does "comparator inconsistent with equals" break?
5. Why doesn't PriorityQueue iterate in sorted order?
6. `ConcurrentModificationException` — mechanism, and three correct ways to remove while iterating.
7. Difference between `Arrays.asList(arr)`, `List.of(...)`, `Collections.unmodifiableList(list)` — what mutations fail where?
8. Design: track top-K items from a stream of millions — which structure and why? (heap)

## 🗺️ Study path

1. Make/print a Big-O table for ArrayList, LinkedList, ArrayDeque, HashMap, TreeMap, PriorityQueue — from memory, verify against docs.
2. Read `ArrayList.grow()` and `ArrayDeque`'s circular logic in the source.
3. Watch "Collections Refueled".
4. Write the trap demos: PQ iteration order, subList modification, asList/of mutation failures.
5. Rehearse Q2 — the LinkedList myth-busting answer is a classic senior differentiator.

## 📝 My notes

_(your own notes while studying)_
