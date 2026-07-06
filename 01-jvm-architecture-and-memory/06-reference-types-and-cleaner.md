# References: Soft/Weak/Phantom, Finalization, Cleaner

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Reachability strength ladder: **strong → soft → weak → phantom → unreachable**; an object's reachability is the *strongest* path to it.
- **SoftReference**: cleared before OOM, LRU-ish heuristic (`-XX:SoftRefLRUPolicyMSPerMB`) — why soft-reference caches are usually a bad idea vs Caffeine.
- **WeakReference**: cleared eagerly at GC; **WeakHashMap** (weak *keys*, common interview trap: values holding keys strongly → no collection); canonical use: canonicalizing mappings, listener maps.
- **PhantomReference**: `get()` always returns null; used with a **ReferenceQueue** for post-mortem cleanup — the mechanism under `Cleaner`.
- **ReferenceQueue** mechanics: GC enqueues cleared references; your thread polls and acts.
- **Finalization is dead**: unpredictable, resurrects objects, at least 2 GC cycles to reclaim, security issues → deprecated for removal (**JEP 421**). Replacement: `java.lang.ref.Cleaner` + `AutoCloseable`/try-with-resources as the primary mechanism.
- `Reference.reachabilityFence()` — why the JIT can consider `this` dead while a method is still running.

## 📚 Resources

### Books / chapters
- **Effective Java (3rd ed.)** — Item 7 (eliminate obsolete references), **Item 8 (avoid finalizers and cleaners)** — Bloch's exact arguments are quoted in interviews.
- **Java Performance (2nd ed.)** — Scott Oaks — section on soft/weak references and heap impact.

### Videos & conference talks
- 🎥 **"The Trouble with Memory"** — Kirk Pepperdine (Devoxx) — reference-object impact on GC.
- 🎥 Inside Java Newscast episode on **finalization deprecation / Cleaner** (search *Inside Java finalization JEP 421*).

### Articles & official docs
- **`java.lang.ref` package javadoc** — short and canonical: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/ref/package-summary.html
- **JEP 421: Deprecate Finalization for Removal**: https://openjdk.org/jeps/421 — the motivation section is a complete interview answer.
- **Baeldung – "Soft References" / "Weak References" / "Phantom References in Java"**: https://www.baeldung.com/java-phantom-reference
- **`Cleaner` javadoc with its usage example**: https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/ref/Cleaner.html — the code sample there (static nested State, no reference to the outer object!) is exactly the pattern to memorize.

## ❓ Senior interview questions

1. Rank the reference strengths and state precisely when each is cleared.
2. Why is `WeakHashMap` not a cache? What silently prevents entries from being collected?
3. What is a `ReferenceQueue` and how does `Cleaner` use phantom references internally?
4. Why was finalization deprecated? Name three concrete problems.
5. In a `Cleaner` cleanup action, why must the runnable not capture a reference to the object being cleaned?
6. What does `reachabilityFence` do and when can an object be collected while one of its methods is executing?
7. Design a cache: why choose Caffeine over `SoftReference`-based caching?

## 🗺️ Study path

1. Read the `java.lang.ref` package javadoc + Cleaner javadoc example.
2. Read Effective Java Items 7–8.
3. Write a demo: create soft/weak/phantom refs, force GC (`-Xmx16m` + allocation), watch clearing order and queue delivery.
4. Read JEP 421.
5. Answer the questions; the WeakHashMap trap (Q2) is worth rehearsing verbatim.

## 📝 My notes

_(your own notes while studying)_
