# ClassLoaders: Loading, Linking, Initialization & the Delegation Model

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- The class lifecycle: **loading → linking (verification, preparation, resolution) → initialization** — and what happens at each phase (preparation = default values; initialization = `<clinit>`, static initializers).
- **Lazy initialization triggers** (JLS 12.4): first `new`, static field access (non-constant!), static method call, reflection, subclass init, `main`. Why `static final int X = 5` access does *not* trigger init (compile-time constant) — classic trap.
- The three built-in loaders since JDK 9: **bootstrap → platform → application (system)**; the old "extension" loader is gone.
- **Parent delegation**: child asks parent first; why (security, consistency — you can't replace `java.lang.String`); who *breaks* it and why (servlet containers/Tomcat prefer webapp-first, OSGi, SPI via thread context classloader).
- **Class identity = (class name, defining loader)** — the same `.class` loaded by two loaders yields two distinct classes → `ClassCastException` between "identical" types. This underlies plugin systems and classloader leaks.
- Exceptions taxonomy: `ClassNotFoundException` (loading by name failed) vs `NoClassDefFoundError` (class was there at compile time, missing/failed at runtime — often a failed `<clinit>` → `ExceptionInInitializerError` first).
- Custom classloaders: override `findClass`, call `defineClass`; use cases — plugins, isolation, hot reload, encryption.
- The **holder idiom** for lazy singletons works *because* of class-init laziness + JVM-guaranteed thread safety of `<clinit>`.

## 📚 Resources

### Books / chapters
- **The Java® Virtual Machine Specification, Ch. 5 "Loading, Linking, and Initializing"**: https://docs.oracle.com/javase/specs/jvms/se21/html/jvms-5.html
- **Inside the Java Virtual Machine** — Bill Venners, Ch. 7–8 (lifetime of a class) — best narrative explanation ever written.
- **The Well-Grounded Java Developer (2nd ed.)** — class loading chapter (covers the JDK 9+ loader changes).

### Videos & conference talks
- 🎥 **"Do You Really Get Classloaders?"** — Jevgeni Kabanov (ZeroTurnaround/JRebel founder, JavaOne) — the classic talk; find it on YouTube, still 95% accurate.
- 🎥 **"The Definitive Guide to Java Agents"** — Rafael Winterhalter (Devoxx) — second half is applied classloading.

### Articles & official docs
- **Baeldung – "Class Loaders in Java"**: https://www.baeldung.com/java-classloaders
- **JLS §12.4 "Initialization of Classes and Interfaces"**: https://docs.oracle.com/javase/specs/jls/se21/html/jls-12.html#jls-12.4 — memorize the trigger list.
- **"Understanding Extension Class Loading removal / JDK 9 loader changes"** — search Inside Java / JEP 261 (module system) notes on the platform loader.
- **Baeldung – "ClassNotFoundException vs NoClassDefFoundError"**: https://www.baeldung.com/java-classnotfoundexception-and-noclassdeffounderror

## ❓ Senior interview questions

1. Walk through everything that happens between `new Foo()` appearing in code and the constructor running, for a class never loaded before.
2. What is parent delegation and why does Tomcat deliberately invert it for webapps?
3. Two plugins each load `com.example.Model` with their own loader — can you cast one to the other? Why not?
4. `ClassNotFoundException` vs `NoClassDefFoundError` vs `ExceptionInInitializerError` — causes and typical scenarios?
5. Why is the initialization-on-demand holder idiom thread-safe without `synchronized` or `volatile`?
6. Accessing `Constants.MAX` (a `static final int`) doesn't initialize `Constants` — why? When would it?
7. How do memory leaks happen via classloaders on redeploy (who holds the loader alive)?
8. What is the thread context classloader and why does JDBC/SPI need it?

## 🗺️ Study path

1. Read Venners Ch. 7 (or JVMS Ch. 5 if you prefer the spec) — take notes on the 3 linking sub-phases.
2. Read JLS §12.4 trigger list; write tiny programs with `-verbose:class` / static-init print statements to verify each trigger (and the constant non-trigger).
3. Write a custom classloader that loads a class from a byte array; demonstrate the two-loaders → ClassCastException effect.
4. Watch "Do You Really Get Classloaders?".
5. Rehearse Q1 as a fluent 2-minute story — it's a favorite senior screener.

## 📝 My notes

_(your own notes while studying)_
