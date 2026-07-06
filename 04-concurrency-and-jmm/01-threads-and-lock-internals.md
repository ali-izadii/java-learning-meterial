# Threads, Synchronization, Monitors & Lock Internals (Biased → Thin → Fat)

> **Level:** 🟢 Fundamental
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- Platform threads map 1:1 to OS threads; thread states (`NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED`) — and precisely when each occurs (`BLOCKED` = waiting for monitor; `WAITING` = `wait()`/`join()`/`park()`).
- **Every object is a monitor**: `synchronized` → `monitorenter/monitorexit`; `wait/notify/notifyAll` require holding the monitor; **spurious wakeups → always `wait()` in a loop**.
- **Lock inflation states in the mark word**: unlocked → *thin/lightweight lock* (CAS stack pointer) → *fat/inflated monitor* (OS mutex + wait queues) on contention. **Biased locking existed and was removed (JEP 374, JDK 15)** — know the one-liner why (cost of revocation, uncontended CAS got cheap, maintenance).
- `synchronized` vs `ReentrantLock`: fairness, tryLock/timeout, interruptible acquisition, multiple `Condition`s vs single wait-set — and that both have similar raw performance today.
- Deadlock: 4 Coffman conditions; detection (`jstack` finds monitor deadlocks); prevention (lock ordering, tryLock with timeout). Livelock and starvation definitions.
- Contention costs: context switches, cache-line ping-pong; strategies — reduce critical section, striping, CAS, immutability.
- `Thread.interrupt()` semantics: sets flag; blocking methods throw `InterruptedException` *and clear the flag* — restore it or propagate (classic code-review question).

## 📚 Resources

### Books / chapters
- **Java Concurrency in Practice** — Goetz — Ch. 1–5 + Ch. 13 (explicit locks). Non-negotiable reading for interviews despite its age.
- **Optimizing Java** — Ch. 12 concurrent performance sections (lock inflation details).

### Videos & conference talks
- 🎥 **"From Concurrent to Parallel"** — Brian Goetz (Devoxx) — the mental model upgrade.
- 🎥 **"The Java Synchronizers"**-style j.u.c. talks — search *Devoxx Java locks concurrency deep dive*; also Nicolai Parlog's concurrency episodes on the *Java* channel.

### Articles & official docs
- **JVM Anatomy Quarks** (Shipilëv) — lock-related quarks (e.g., lock elision/coarsening) + his **"Safe Publication and Safe Initialization"**: https://shipilev.net/blog/2014/safe-public-construction/
- **JEP 374: Deprecate and Disable Biased Locking**: https://openjdk.org/jeps/374 — read Motivation; it's a ready-made interview answer.
- **Baeldung – "Guide to the Synchronized Keyword"**: https://www.baeldung.com/java-synchronized · **"wait and notify"**: https://www.baeldung.com/java-wait-notify
- **`Thread` & `Object#wait` javadoc** — precise semantics, spurious wakeups note.

## ❓ Senior interview questions

1. What happens, at the JVM level, when two threads hit the same `synchronized` block? Walk through thin-lock CAS → inflation.
2. Why was biased locking removed?
3. `BLOCKED` vs `WAITING` thread states — exact difference?
4. Why must `wait()` be called in a loop checking the condition?
5. `synchronized` vs `ReentrantLock` — when do you actually need the latter?
6. You get a thread dump of a frozen service — how do you find a deadlock? What if it's a `Lock`-based deadlock (jstack + ownable synchronizers)?
7. What's wrong with `catch (InterruptedException e) {}` (swallowing)? What should you do?
8. `notify()` vs `notifyAll()` — when is `notify()` safe?

## 🗺️ Study path

1. Read JCiP Ch. 2–3 carefully (thread safety, sharing objects).
2. Read Shipilëv's "Safe Publication and Safe Initialization".
3. Write and diagnose a deliberate deadlock with `jstack`; write a producer/consumer with wait/notify, then with `Condition`.
4. Read JEP 374 motivation.
5. Rehearse Q1 with mark-word vocabulary from the object-layout topic.

## 📝 My notes

_(your own notes while studying)_
