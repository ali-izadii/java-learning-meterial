# HashMap Internals: Hashing, Treeification, Resize; equals/hashCode Contract

> **Level:** 🟢 Fundamental (the single most-asked Java interview topic)
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Structure: **array of bins (Node[])**; index = `(n-1) & hash` (power-of-two capacity makes this a mask); **hash spreading** `h ^ (h >>> 16)` — why high bits are mixed in.
- Collision handling: linked list → **treeify to red-black tree at 8 nodes** (if table ≥ 64, else resize), untreeify at 6 — why 8 (Poisson argument in the source comment: probability ~1e-8 under good hashing; trees defend against bad/adversarial hashCodes).
- **Resize**: load factor 0.75, threshold = capacity × LF, doubling; JDK 8 trick — elements split into lo/hi lists by one bit (`hash & oldCap`), no rehash recomputation; why resizing is O(n) and pre-sizing matters (`HashMap(expected/0.75f + 1)`).
- **equals/hashCode contract**: equal objects ⇒ equal hashCodes; consistent; why violating it loses entries; **mutating a key after insertion** loses the entry (classic trap).
- Iteration order unspecified; **fail-fast** iterators via modCount (best-effort — not a guarantee).
- Why HashMap is not thread-safe — JDK 7's resize could create infinite loops (historical); JDK 8+ can silently lose updates → use CHM.
- Family: LinkedHashMap (insertion/access order → LRU via `removeEldestEntry`), TreeMap (red-black tree, comparator, O(log n), NavigableMap), EnumMap, IdentityHashMap.
- Null handling: one null key (bin 0), null values fine.

## 📚 Resources

### Books / chapters
- **Effective Java (3rd ed.)** — **Item 10 (equals) & Item 11 (hashCode)** — verbatim interview material.
- **Java Generics and Collections (2nd ed.)** — Naftalin & Wadler — map chapters for the framework view.

### Videos & conference talks
- 🎥 Search YouTube: **"HashMap internals Java 8 treeification"** — many solid walkthroughs; prefer ones that read the actual JDK source on screen (e.g., Defog Tech's collection internals series is popular and accurate).
- 🎥 **Stuart Marks' collections talks** (Oracle/Devoxx, e.g., on collection implementation changes) — from the JDK collections maintainer.

### Articles & official docs
- **The `HashMap.java` source itself** — the implementation-notes comment at the top explains treeification thresholds & the Poisson distribution. Open it in your IDE; being able to say "the source comment says…" wins interviews.
- **Baeldung – "A Guide to Java HashMap"**: https://www.baeldung.com/java-hashmap · **"Internals of HashMap"**: https://www.baeldung.com/java-hashmap-advanced
- **`Map`/`HashMap` javadoc** — load factor & iteration-order paragraphs.
- **Baeldung – "LinkedHashMap as LRU"**: https://www.baeldung.com/java-linked-hashmap

## ❓ Senior interview questions

1. Walk through `put(k, v)` end-to-end on JDK 8+: hash spreading, index, collision, treeify, resize.
2. Why must capacity be a power of two? What does `h ^ (h >>> 16)` fix?
3. Why treeify at exactly 8? What attack/pathology do trees mitigate?
4. Explain the resize algorithm and the lo/hi split trick. Why is rehashing not recomputed?
5. You override `equals` but not `hashCode` — demonstrate the exact failure in a HashMap.
6. What happens if you mutate a key after putting it? How do you design safe keys?
7. How would you implement an LRU cache with JDK classes only?
8. HashMap vs Hashtable vs ConcurrentHashMap vs Collections.synchronizedMap — one minute.
9. What is fail-fast iteration, and why is it "best effort"?

## 🗺️ Study path

1. Read `HashMap.java` source: class comment, `hash()`, `putVal()`, `resize()` — with this guide's bullets as your map (2 hours).
2. Read Effective Java Items 10–11.
3. Write the failure demos: bad hashCode, mutated key, concurrent modification.
4. Implement LRU via LinkedHashMap in 15 lines.
5. Rehearse Q1 as a whiteboard-ready 3-minute walkthrough — this exact question decides many interviews.

## 📝 My notes

_(your own notes while studying)_
