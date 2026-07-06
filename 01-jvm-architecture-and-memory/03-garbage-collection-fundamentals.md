# Garbage Collection Fundamentals (Reachability, Generational Hypothesis, Safepoints)

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- GC = **reachability**, not reference counting: GC roots (thread stacks, statics, JNI refs) → object graph tracing.
- Core algorithms: **mark-sweep, mark-compact, copying** — and their tradeoffs (fragmentation vs copy cost).
- **Weak generational hypothesis**: most objects die young → young/old generations, eden + survivor spaces, object **age** and **tenuring/promotion**.
- **Minor vs major vs full GC**; premature promotion; **card table / remembered sets** (how old→young references are found without scanning the old gen).
- **Safepoints**: what stop-the-world actually means, time-to-safepoint, why a long-running counted loop can delay everyone.
- **Allocation**: bump-the-pointer in TLABs — allocation is ~10 instructions; "GC pressure" is about allocation *rate*.
- STW vs **concurrent** vs **parallel** collection — the three words interviewers love to see you distinguish.
- Classic collectors as baseline vocabulary: Serial, Parallel (throughput), CMS (deprecated/removed — know *why*: fragmentation + concurrent mode failure).

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 6 "Understanding Garbage Collection" & Ch. 7 "Advanced GC" — best practical treatment.
- **The Garbage Collection Handbook (2nd ed.)** — Jones, Hosking, Moss — the academic bible; skim Ch. 1–5 only.
- **Java Performance (2nd ed.)** — Scott Oaks, Ch. 5–6.

### Videos & conference talks
- 🎥 **"Understanding Low Latency JVM GCs"** — Jean-Philippe Bempel (Devoxx) — great fundamentals-to-modern bridge.
- 🎥 **"GC Fundamentals"** / **"A Young Person's Guide to Garbage Collection"** — search YouTube for *Devoxx garbage collection fundamentals* (Charlie Hunt's GC talks are also gold).
- 🎥 **"Do It Yourself (OpenJDK) Garbage Collector"** — Aleksey Shipilëv — builds Epsilon-based intuition of what a GC really does.

### Articles & official docs
- **Oracle HotSpot GC Tuning Guide — "Introduction" + "Generations"**: https://docs.oracle.com/en/java/javase/21/gctuning/ — free, official, and interview-precise vocabulary.
- **JVM Anatomy Quarks** (Shipilëv): #4 TLAB allocation, #22 safepoint polls: https://shipilev.net/jvm/anatomy-quarks/
- **Baeldung – "JVM Garbage Collectors" overview**: https://www.baeldung.com/jvm-garbage-collectors
- **JEP 318: Epsilon (no-op GC)**: https://openjdk.org/jeps/318 — reading this clarifies what GCs actually provide.

## ❓ Senior interview questions

1. What are GC roots? Why doesn't Java use reference counting (hint: cycles)?
2. Explain the generational hypothesis and how eden/survivor/old spaces exploit it.
3. What is a card table / remembered set and what problem does it solve?
4. What is a safepoint? Give an example where threads take long to reach one.
5. Mark-sweep vs mark-compact vs copying — tradeoffs?
6. Why was CMS removed? What replaced it and what did the replacement fix?
7. Your service has rising minor GC frequency but stable heap after GC — what's happening? (allocation rate ↑)
8. What is premature promotion and how does it show up in behavior?

## 🗺️ Study path

1. Read Optimizing Java Ch. 6 (or the Oracle tuning guide intro chapters).
2. Read Shipilëv quarks #4 and #22.
3. Run any app with `-Xlog:gc*` and identify minor/major GC lines, promotion, pause times.
4. Watch the Bempel talk to connect fundamentals to modern collectors.
5. Answer the questions; then move to topic 04 (modern collectors).

## 📝 My notes

_(your own notes while studying)_
