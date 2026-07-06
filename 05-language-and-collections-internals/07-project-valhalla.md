# Project Valhalla: Value Types & Where Java Is Heading

> **Level:** 🔴 Expert (bonus points — signals you follow the platform's direction)
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **The problem**: Java's "everything is an object reference" → pointer chasing, headers, poor cache density; `ArrayList<Point>` is an array of pointers to scattered 16+-byte objects vs C#'s inline structs. "Codes like a class, works like an int" is the slogan.
- **Value classes (JEP 401)**: `value class` — identity-free (no `==` identity, no synchronization on them, no identity hashCode); JVM free to **flatten** them into containers/arrays and pass in registers; construction still via constructors, still classes (methods, encapsulation).
- **Null-restricted types & flattening**: why nullability blocks flattening (need a null representation) — the `String!`/null-restriction line of JEPs; heap flattening of small value objects.
- Relationship to **primitive boxing**: goal to heal the int/Integer rift (enhanced primitive boxing direction) and eventually allow generics over values (`ArrayList<int>` — specialized generics, the long game).
- What identity actually costs — ties into object-layout topic (headers, indirection).
- Current status awareness (as of early 2026): JEP 401 in preview stages; nothing final — say "preview" in interviews, and know **JEP 390 value-based classes** warnings (`Integer` caching, "don't synchronize on value-based classes") as the *shipped today* part.
- Why records ≠ value classes (records still have identity; orthogonal features that combine: `value record`).

## 📚 Resources

### Books / chapters
- No book covers this well yet — primary sources below are the material.

### Videos & conference talks
- 🎥 **Brian Goetz — "Valhalla Update" / "The State of Project Valhalla"** (JVMLS, yearly, on the *Java* channel) — watch the most recent one; each year's talk is the best snapshot.
- 🎥 **"Valhalla: Value Types in the JVM"** — Dan Heidinga / John Rose JVMLS sessions for the JVM-level view.
- 🎥 Inside Java Newscast Valhalla episodes (Parlog) — concise status updates.

### Articles & official docs
- **JEP 401: Value Classes and Objects (Preview)**: https://openjdk.org/jeps/401 — the core read.
- **Brian Goetz — "State of Valhalla" documents** (design notes, 3 parts: The Road to Valhalla / The Language Model / The JVM Model): https://openjdk.org/projects/valhalla/design-notes/state-of-valhalla/ — the definitive deep source.
- **JEP 390: Warnings for Value-Based Classes** (shipped): https://openjdk.org/jeps/390
- **Valhalla project page**: https://openjdk.org/projects/valhalla/
- **Inside Java — Valhalla tag**: https://inside.java/tag/valhalla

## ❓ Senior interview questions

1. What performance problem does Valhalla attack? Explain with `ArrayList<Point>` memory layout.
2. What does "giving up identity" mean concretely — which three object capabilities disappear?
3. Why does nullability interfere with flattening a value object into an array?
4. Records vs value classes — different problems? Can they combine?
5. What is a value-based class today (JEP 390)? Why is `synchronized(someInteger)` dangerous *right now*?
6. How does Valhalla relate to generic specialization (`List<int>`)?
7. Why can the JVM already scalar-replace some objects (escape analysis) — and why is that not enough?

## 🗺️ Study path

1. Read "State of Valhalla" part 1 (The Road to Valhalla) — the problem statement.
2. Read JEP 401's goals & description.
3. Watch the latest Goetz JVMLS Valhalla update.
4. Read JEP 390 — the today-relevant piece (value-based class rules).
5. Prepare a 60-second "what Valhalla is and why it matters" elevator answer — as a bonus-round differentiator, that's all you need.

## 📝 My notes

_(your own notes while studying)_
