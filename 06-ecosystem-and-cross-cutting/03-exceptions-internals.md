# Exceptions Internals & Best Practices; the Cost of Stack Traces

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Hierarchy precisely: `Throwable` → `Error` (don't catch) / `Exception` → `RuntimeException` (unchecked) vs checked; the **checked-exception debate** — arguments both ways (Bloch: recoverable conditions; critics: forced boilerplate, lambda hostility) — seniors are expected to hold a nuanced position.
- **The cost model**: try blocks are ~free (exception table, zero cost until thrown); the expensive part is **`fillInStackTrace()` at construction** (walking frames), not the throw; `catch` of a cold exception may also deopt-interact. Numbers-level intuition: constructing an exception ≈ hundreds of ns–µs depending on stack depth.
- **JVM optimizations**: JIT can turn hot implicit exceptions (NPE, AIOOBE) into stackless preallocated ones (`-XX:-OmitStackTraceInFastThrow` — the "empty stack trace in production logs" mystery, a superb senior story).
- Escape hatches: override `fillInStackTrace()` / constructor with `writableStackTrace=false` for control-flow-ish exceptions (and why exceptions-as-control-flow is still usually wrong).
- Bytecode level: **exception table** entries (from/to/target/type), why `finally` is duplicated code paths, how try-with-resources compiles (suppressed exceptions, `addSuppressed`).
- **Helpful NullPointerExceptions (JEP 358)** — message tells which subexpression was null.
- Best practices canon: catch specific, don't swallow, wrap with cause (exception chaining/translation at layer boundaries), log-or-rethrow-never-both, cleanup via try-with-resources not finally.

## 📚 Resources

### Books / chapters
- **Effective Java (3rd ed.)** — **Ch. 10 entire (Items 69–77)**: exceptions for exceptional conditions, checked vs unchecked, favor standard exceptions, exception translation, failure atomicity, don't ignore.
- **Java Concurrency in Practice** — Ch. 7 for interruption-exception handling policy.

### Videos & conference talks
- 🎥 **"The Exceptional Performance of Lil' Exception"** — Aleksey Shipilëv — article + talk; THE deep source on exception cost: https://shipilev.net/blog/2014/exceptional-performance/
- 🎥 Kevlin Henney — **"The Error of Our Ways"** / error-handling design talks — the design-thinking layer.

### Articles & official docs
- **Shipilëv — "The Exceptional Performance of Lil' Exception"**: https://shipilev.net/blog/2014/exceptional-performance/ — read fully; every performance claim you'll ever need, benchmarked.
- **JEP 358: Helpful NullPointerExceptions**: https://openjdk.org/jeps/358
- **Baeldung – "Exception Handling in Java"**: https://www.baeldung.com/java-exceptions · **"Performance Effects of Exceptions"**: https://www.baeldung.com/java-exceptions-performance
- **JLS §11 (exceptions) / JVMS §3.12** for the exception-table mechanics.

## ❓ Senior interview questions

1. What's expensive: `try`, `throw`, or `new Exception()`? Break down the cost model.
2. Production logs show `NullPointerException` with no stack trace — explain `OmitStackTraceInFastThrow` and what it tells you about that code path.
3. How does `finally` work at the bytecode level? How does try-with-resources handle an exception in both body and `close()` (suppressed exceptions)?
4. Checked vs unchecked — your policy for a service codebase and its API boundaries?
5. What is exception translation and why do layered architectures need it (with `cause` preserved)?
6. Why is using exceptions for control flow bad — and when is it acceptable (with what constructor tricks)?
7. What is failure atomicity (EJ Item 76)?
8. What should a `catch (InterruptedException)` block do, and why is that different from other exceptions?

## 🗺️ Study path

1. Read Shipilëv's "Lil' Exception" post (the one irreplaceable resource here).
2. Read Effective Java Ch. 10.
3. `javap -c` a try/catch/finally and a try-with-resources; find the exception table and suppressed-exception plumbing.
4. Reproduce fast-throw: hot loop throwing NPE until traces vanish; flip the flag.
5. Write your team-policy answer for Q4 — interviewers want a decisive, justified position.

## 📝 My notes

_(your own notes while studying)_
