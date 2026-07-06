# invokedynamic, Method Handles & How Lambdas Really Work

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **`invokedynamic`**: a call site whose target is resolved *once* at first execution by a **bootstrap method (BSM)**, which returns a **CallSite** holding a **MethodHandle**; afterwards it's as fast as a normal call (JIT can inline through it).
- **MethodHandles** vs reflection: typed, JIT-friendly, checked at lookup time not invocation time; `MethodHandles.Lookup` capability model.
- **How lambdas compile** (the star question): lambda body → private synthetic method; lambda expression → `invokedynamic` to `LambdaMetafactory.metafactory`, which spins a small class implementing the functional interface **at runtime** (hidden class), caching it. NOT anonymous inner classes — no `.class` files at compile time.
- Consequences: non-capturing lambdas are singletons; capturing lambdas allocate; method refs similar; `this` inside a lambda = enclosing instance (unlike anonymous classes).
- Other indy users: **string concatenation** (JEP 280), **records' equals/hashCode/toString** (`ObjectMethods` bootstrap), pattern-matching machinery, Groovy/Kotlin/JRuby dynamic dispatch.
- **Hidden classes (JEP 371)** — what LambdaMetafactory uses instead of `Unsafe.defineAnonymousClass`.
- Why this design: "the last invoke instruction" decouples the *what* (call semantics) from the *how* (JVM-optimizable linkage) — lambdas got faster over time without recompiling code.

## 📚 Resources

### Books / chapters
- **The Well-Grounded Java Developer (2nd ed.)** — method handles & invokedynamic chapter.
- **JVMS §6.5 `invokedynamic`** + **`java.lang.invoke` package javadoc** (the package doc is a genuinely good essay): https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/lang/invoke/package-summary.html

### Videos & conference talks
- 🎥 **"Lambdas: A Peek Under the Hood"** — Brian Goetz (Devoxx/GOTO) — THE talk; explains every design decision above, from the architect himself.
- 🎥 **"invokedynamic for Mere Mortals"**-style talks — search YouTube *invokedynamic Devoxx*; also **"One VM to Rule Them All"** contexts for indy's language-implementation role.

### Articles & official docs
- **"Translation of Lambda Expressions"** — Brian Goetz's canonical design doc: https://cr.openjdk.org/~briangoetz/lambda/lambda-translation.html — read this; it answers every lambda-internals interview question.
- **JEP 371: Hidden Classes**: https://openjdk.org/jeps/371
- **Baeldung – "Method Handles in Java" / "An Introduction to Invoke Dynamic in the JVM"**: https://www.baeldung.com/java-invoke-dynamic
- **`LambdaMetafactory` javadoc** — read the class-level doc top to bottom.

## ❓ Senior interview questions

1. What happens the first time an `invokedynamic` instruction executes? And the second time?
2. Compile-time and runtime story of `Runnable r = () -> foo();` — where does the class come from?
3. Why aren't lambdas implemented as anonymous inner classes? Name three concrete differences.
4. When does a lambda allocate an object and when doesn't it?
5. MethodHandle vs `java.lang.reflect.Method` — performance and semantics differences?
6. `this` inside a lambda vs inside an anonymous class — what's the difference and why?
7. Name three other JDK features built on invokedynamic besides lambdas.

## 🗺️ Study path

1. Watch "Lambdas: A Peek Under the Hood".
2. Read Goetz's "Translation of Lambda Expressions".
3. `javap -c -v` a class with a capturing lambda, non-capturing lambda, and method reference — find the indy instructions and BootstrapMethods attribute.
4. Run with `-Djdk.internal.lambda.dumpProxyClasses=<dir>` (or use `-verbose:class`) to see the spun classes.
5. Write a small MethodHandle example (lookup, invokeExact) to feel the API.

## 📝 My notes

_(your own notes while studying)_
