# Java Modules (JPMS) & the Module Path

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- `module-info.java` vocabulary: `requires` (+ `transitive`, `static`), `exports` (+ qualified `exports ... to`), **`opens`** (deep reflection at runtime — the exports/opens distinction is *the* JPMS interview question), `uses`/`provides ... with` (services), `open module`.
- **Why JPMS exists**: strong encapsulation of JDK internals (the `sun.misc.Unsafe`/`--add-opens` saga), reliable configuration (fail at startup, not runtime `NoClassDefFoundError`), jlink-able runtime images. And honestly: **most applications still live happily on the classpath** — knowing *when not to modularize* is senior judgment.
- **Unnamed module** (classpath code) & **automatic modules** (plain JARs on module path, `Automatic-Module-Name`) — the migration bridge; split packages as the classic migration blocker.
- Encapsulation enforcement timeline: JDK 9 warnings → **JDK 16/17 strongly encapsulated by default (JEP 396/403)** — why old libraries suddenly broke and what `--add-opens`/`--add-exports` do.
- **jlink**: custom runtime images (smaller containers); **jdeps** for dependency analysis.
- How this interacts with frameworks: Spring/Hibernate need `opens` for reflection; why many stacks stay classpath-based.
- The "integrity by default" direction (agents, JNI restrictions) as the continuing arc.

## 📚 Resources

### Books / chapters
- **The Java Module System** — **Nicolai Parlog** (Manning) — THE book on JPMS, including migration war stories.
- **Java 9 Modularity** — Mak & Bakker (O'Reilly) — the other standard text; great on services & jlink.

### Videos & conference talks
- 🎥 **Nicolai Parlog — "Java Module System" talks** (Devoxx) — the book in 1 hour.
- 🎥 **Alex Buckley — "Modules and Services" / JPMS design talks** (JavaOne/Devoxx) — from the spec lead.

### Articles & official docs
- **The State of the Module System** — Reinhold: https://openjdk.org/projects/jigsaw/spec/sotms/ — the original design doc, still the clearest overview.
- **JEP 261: Module System**: https://openjdk.org/jeps/261 · **JEP 403: Strongly Encapsulate JDK Internals**: https://openjdk.org/jeps/403
- **nipafx.dev JPMS articles** (Parlog): https://nipafx.dev/tag/j_ms/
- **Baeldung – "A Guide to Java 9 Modularity"**: https://www.baeldung.com/java-9-modularity
- **Oracle jlink/jdeps tool docs**: https://docs.oracle.com/en/java/javase/21/docs/specs/man/jlink.html

## ❓ Senior interview questions

1. `exports` vs `opens` — exactly what does each allow, at what time (compile/runtime), for what kind of access?
2. Why did Java need modules when classpath + build tools "worked"? Name the three official goals.
3. What is an automatic module? What breaks migration (split packages) and how do you find it (jdeps)?
4. Your app throws `InaccessibleObjectException` after a JDK upgrade — explain why and the fix spectrum (--add-opens → library upgrade → proper modules).
5. `requires transitive` — what problem does it solve (API leakage)?
6. How do `uses`/`provides` compare to classic `ServiceLoader` on the classpath?
7. When would you *not* modularize an application? What do you still gain via jlink either way?

## 🗺️ Study path

1. Read "The State of the Module System".
2. Build a 3-module toy project (api / impl via services / app); break encapsulation and read every error message.
3. Run `jdeps` on a real project; produce a jlink image and compare sizes.
4. Read Parlog's migration articles (or book Ch. on migration).
5. Rehearse Q1 and Q4 — the two that actually get asked.

## 📝 My notes

_(your own notes while studying)_
