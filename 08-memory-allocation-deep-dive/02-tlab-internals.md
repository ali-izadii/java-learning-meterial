# TLAB Internals: Sizing, Refills, Waste & Tuning

> **Level:** 🟡 Deep-dive
> **Status:** ✅ Filled — curated 2026-07-06

---

## 🎯 What a senior candidate must know

- **Why TLABs exist**: eden is shared; a global CAS per allocation would make every `new` a contention point (cache-line ping-pong — atomics topic 7.02). TLAB = each thread reserves a private chunk once, then bumps a private pointer — contention amortized over hundreds of allocations.
- **Lifecycle**: thread requests TLAB from eden (CAS on eden top) → allocates privately → TLAB exhausted → **retire** (fill leftover with a dummy filler array so the heap stays parseable — nice expert detail) → request a new one. All TLABs die at minor GC (eden is evacuated).
- **Adaptive sizing**: TLAB size is per-thread adaptive (`-XX:TLABSize` initial, `ResizeTLAB` on by default) — targets a waste percentage (`TLABWasteTargetPercent`, default 1% of eden per thread refill-set); busy allocating threads get bigger TLABs, idle threads smaller.
- **The waste tradeoff**: bigger TLABs = fewer refills (less CAS traffic) but more wasted eden at retire time & fewer threads served; smaller = the reverse. **`-XX:TLABWasteIncrement` / refill thresholds** decide "allocate this big object outside TLAB vs retire early".
- **Observing TLABs**: `-Xlog:gc+tlab=trace` (per-thread refills, waste, slow allocs); JFR events `jdk.ObjectAllocationInNewTLAB` / `jdk.ObjectAllocationOutsideTLAB` — the two events that power allocation profilers (async-profiler alloc mode samples exactly these — topic 04).
- **Allocations outside TLAB**: objects bigger than current TLAB free space + above refill-waste threshold → direct eden CAS allocation (slower, contended); consistently large objects → outside-TLAB churn is a visible profile signature.
- Interaction with collectors: TLABs are a young-gen/eden concept in all HotSpot GCs; **PLAB** (promotion-local allocation buffer) is the GC-internal analog for evacuation — knowing the term signals depth.
- Practical stance: **you almost never hand-tune TLAB flags** — you diagnose with the logs/events and fix the *allocation pattern* instead.

## 📚 Resources

### Books / chapters
- **Optimizing Java** — Ch. 6 TLAB sections.
- **JVM Performance Engineering** — Monica Beckwith — allocation/TLAB coverage from a HotSpot GC engineer (recent book, good on G1-era details).

### Videos & conference talks
- 🎥 Shipilëv GC/allocation talks (JVM Anatomy live) — search *Shipilev TLAB talk*.
- 🎥 **Monica Beckwith — G1/JVM performance talks** (JavaOne/Devoxx) — includes TLAB/PLAB mechanics in the G1 context.

### Articles & official docs
- **JVM Anatomy Quark #4: TLAB Allocation**: https://shipilev.net/jvm/anatomy-quarks/4-tlab-allocation/ — re-read with tuning eyes this time.
- **JVM Anatomy Quark #5: TLABs and Heap Parsability**: https://shipilev.net/jvm/anatomy-quarks/5-tlabs-and-heap-parsability/ — the retire/filler-object story.
- **HotSpot `ThreadLocalAllocBuffer` source comment** (`tlab.hpp/cpp`) — the sizing policy is documented right in the source; searchable on GitHub openjdk/jdk.
- **Baeldung-level intro**: https://www.baeldung.com/java-jvm-tlab (quick warm-up if needed).
- **JFR event reference — allocation events**: https://sap.github.io/SapMachine/jfrevents/ (community-maintained, excellent searchable JFR event catalog).

## ❓ Senior interview questions

1. What problem do TLABs solve, precisely? What would per-allocation cost look like without them?
2. Describe the TLAB lifecycle: acquisition, exhaustion, retirement. What is the filler object for (heap parsability)?
3. How does adaptive TLAB sizing work — what does it optimize for, and what's the waste tradeoff?
4. An allocation doesn't fit the current TLAB — enumerate the decision tree (retire-and-refill vs allocate-outside).
5. What do `ObjectAllocationInNewTLAB` / `ObjectAllocationOutsideTLAB` JFR events sample, and why is "outside TLAB" a red flag in a profile?
6. Why do all TLABs become garbage-collectable at every minor GC?
7. What is a PLAB and how does it mirror the TLAB idea inside the collector?
8. A service with 2000 mostly-idle threads shows high eden waste — connect this to TLAB mechanics.

## 🗺️ Study path

1. Re-read quark #4, then #5 (parsability/retirement).
2. Run any allocating app with `-Xlog:gc+tlab=debug` — read refill counts, waste %, slow allocations per thread.
3. Record JFR and filter the two allocation events; find which call sites allocate outside TLAB.
4. Skim the `ThreadLocalAllocBuffer` source comment for the sizing math.
5. Rehearse Q2 + Q5 — the retirement/filler detail and the JFR-event link are strong depth signals.

## 📝 My notes

_(your own notes while studying)_
