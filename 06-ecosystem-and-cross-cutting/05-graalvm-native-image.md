# GraalVM & Native Image, AOT Compilation

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Two distinct things called "Graal": **Graal JIT** (Java-written JIT, can replace C2) and **Native Image** (AOT compiler → standalone executable) — don't conflate them in interviews.
- **Native Image model**: **closed-world assumption** — points-to analysis from entry points; unreachable code eliminated; **build-time initialization** of classes; heap snapshot baked into the **image heap**; SubstrateVM as the embedded runtime (its own GC — Serial GC default, G1 on Oracle GraalVM).
- **The tradeoff table** (the core interview answer): instant startup (ms), low RSS, predictable perf from first request vs longer builds, no runtime class loading, **peak throughput usually below JIT C2** (no profile-guided speculation — PGO can claw some back), reduced observability (agents).
- **The dynamic-features problem**: reflection/proxies/JNI/resources need **reachability metadata** (config or the shared metadata repository); the tracing agent to generate it; how frameworks answer it — **Spring AOT processing, Quarkus/Micronaut build-time DI** (avoiding runtime reflection by design).
- When to choose it: serverless/CLI/scale-to-zero/high-density microservices; when NOT: long-running throughput-critical monoliths (JIT wins at steady state).
- The middle-ground landscape (great senior nuance): **CRaC** (checkpoint/restore), **Project Leyden AOT cache (JEP 483)** — JVM-native answers to startup without closed-world costs.

## 📚 Resources

### Books / chapters
- **Supercharge Your Applications with GraalVM** — A B Vijay Kumar (Packt) — decent book-length overview if you want one; docs below are primary.

### Videos & conference talks
- 🎥 **Thomas Wuerthinger — "GraalVM Native Image" / "One VM to Rule Them All"** (GraalVM lead; Devoxx/JVMLS) — architecture from the source.
- 🎥 **Oleg Šelajev — GraalVM talks** (many, practical, entertaining) — search *Šelajev GraalVM Devoxx*.
- 🎥 **"Spring Boot 3 Native"** / **Quarkus native** talks — how frameworks made themselves AOT-friendly (Stéphane Nicoll / Sébastien Deleuze for Spring).

### Articles & official docs
- **GraalVM Native Image docs** — especially "Native Image Basics" and "Reachability Metadata": https://www.graalvm.org/latest/reference-manual/native-image/ — the Basics page explains build-time init & image heap precisely.
- **Spring Boot AOT / GraalVM support docs**: https://docs.spring.io/spring-boot/reference/packaging/native-image/index.html
- **JEP 483: AOT Class Loading & Linking (Leyden)**: https://openjdk.org/jeps/483 — the JVM's own answer; comparing them intelligently is expert-level.
- **CRaC project**: https://openjdk.org/projects/crac/
- **Baeldung – "GraalVM Native Image"**: https://www.baeldung.com/graal-native-image

## ❓ Senior interview questions

1. Why does native image start in milliseconds while the JVM takes seconds? (no classloading/verification/JIT warmup + image heap)
2. What is the closed-world assumption and what does it forbid at runtime?
3. Why is peak throughput typically lower than C2-JITed code? What is PGO for native image?
4. How does reflection work in native image? What is reachability metadata and the tracing agent?
5. Why are Quarkus/Micronaut "more native-friendly" than classic Spring was — what moved from runtime to build time?
6. You must cut cold-start for a serverless Java function — compare native image, CRaC, and Leyden AOT cache.
7. What GC runs inside a native image and what does that imply for heap-heavy services?
8. What happens to `static { }` initializers at image build time, and what bug class does build-time init create (captured state, opened sockets)?

## 🗺️ Study path

1. Read the Native Image "Basics" doc page (build-time init + image heap).
2. Build a hello-world native image; measure startup & RSS vs JVM; then add a reflection call and fix it with metadata/agent.
3. Build a Spring Boot 3 (or Quarkus) app natively; skim what Spring AOT generated.
4. Read JEP 483 and the CRaC overview for the alternatives comparison.
5. Prepare Q6 as a decision matrix — that's the question form in senior/staff interviews.

## 📝 My notes

_(your own notes while studying)_
