# Serialization: Java Native, Its Dangers & Alternatives

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Mechanics: `Serializable` (marker), `serialVersionUID` (what breaks compatibility if omitted/changed), `transient`, object graph traversal, superclass rules (non-serializable parent needs accessible no-arg constructor), customization hooks: `writeObject/readObject`, `writeReplace/readResolve` (singleton preservation), `Externalizable`.
- **Why it's considered a mistake** (Goetz: "a horrible mistake"; Bloch devotes 5 items to avoiding it): constructors bypassed → invariants unenforced; invisible public API (byte format couples to fields); **deserialization RCE gadget chains** (ysoserial, Log4Shell-adjacent lore) — untrusted `readObject` is an attack surface.
- Defenses if you must: **JEP 290 serialization filters** (`ObjectInputFilter`, allowlists), look-ahead deserialization, never deserialize untrusted data.
- **Records serialize safely** — via canonical constructor, invariants enforced (the one bright spot; JEP 395 notes).
- Alternatives & tradeoffs (the actual senior discussion): **JSON** (Jackson — human-readable, schema-loose), **Protobuf** (schema evolution rules, compact), **Avro** (schema-with-data, big data), Kryo (fast JVM-only) — versioning/evolution strategy is the design question behind "which format".
- Effective Java patterns: **serialization proxy pattern** (Item 90); prefer not implementing Serializable in new code (Item 86).
- "Towards Better Serialization" (Goetz) — the long-term replacement direction (marshalling via constructors/deconstructors).

## 📚 Resources

### Books / chapters
- **Effective Java (3rd ed.)** — **Ch. 12 entire (Items 85–90)** — the canonical treatment; interviewers quote Item 85 ("prefer alternatives") and Item 90 (proxy pattern).
- **Java Object Serialization Specification** (official, for mechanics reference): https://docs.oracle.com/en/java/javase/21/docs/specs/serialization/

### Videos & conference talks
- 🎥 **"Marshalling: Data Moving Between Systems"-era Brian Goetz talks** on serialization's future — search *Goetz serialization Devoxx / "Towards Better Serialization"*.
- 🎥 **Deserialization vulnerability talks** — search *"Marshalling Pickles" AppSecCali (Frohoff & Lawrence)* — the talk that exposed gadget chains; essential security context.

### Articles & official docs
- **Brian Goetz — "Towards Better Serialization"**: https://openjdk.org/projects/amber/design-notes/towards-better-serialization — design-note; explains every flaw precisely.
- **JEP 290: Filter Incoming Serialization Data**: https://openjdk.org/jeps/290 + **JEP 415 (context-specific filters)**: https://openjdk.org/jeps/415
- **OWASP — Deserialization Cheat Sheet**: https://cheatsheetseries.owasp.org/cheatsheets/Deserialization_Cheat_Sheet.html
- **Baeldung – "Introduction to Java Serialization"**: https://www.baeldung.com/java-serialization · **Protobuf schema evolution docs**: https://protobuf.dev/programming-guides/proto3/#updating

## ❓ Senior interview questions

1. What happens during `ObjectInputStream.readObject()`? Why is "constructor is bypassed" the root of both the correctness and security problems?
2. What is serialVersionUID; what changes are compatible vs breaking?
3. Explain a deserialization gadget-chain attack at a conceptual level and the JEP 290 mitigation.
4. `readResolve` — how do you keep a serializable singleton a singleton? What does the serialization proxy pattern fix beyond that?
5. Why do records serialize more safely than classes?
6. Design: two services exchange evolving messages for years — JSON vs Protobuf vs Avro; walk through schema-evolution tradeoffs.
7. `transient` — when do you need it, and how do you restore transient state on read?

## 🗺️ Study path

1. Read Effective Java Ch. 12 (Items 85–90).
2. Read Goetz's "Towards Better Serialization" (the *why it's broken* catalog).
3. Implement: version-mismatch demo (change a field, break deserialization), `readResolve` singleton, an `ObjectInputFilter` allowlist.
4. Read the OWASP cheat sheet; skim the "Marshalling Pickles" slides.
5. Prepare the Q6 format-comparison discussion — it's the form this topic takes in system-design-flavored interviews.

## 📝 My notes

_(your own notes while studying)_
