# Java Agents & Bytecode Instrumentation

> **Level:** 🔴 Expert
> **Status:** ✅ Filled — curated 2026-07-13

---

## 🎯 What a senior candidate must know

- **Java agents** are JVM extensions that can observe or transform classes. Startup agents use `-javaagent` and `premain`; dynamic agents attach to an already-running JVM and use `agentmain`.
- **`java.lang.instrument.Instrumentation` API**: `addTransformer`, `ClassFileTransformer`, `retransformClasses`, `redefineClasses`, `isRetransformClassesSupported`, object sizing via `getObjectSize`, and loaded-class inspection.
- **Load-time transformation**: the JVM asks transformers for new class bytes before defining a class. This is how APMs, coverage tools, profilers, security agents, and mocking frameworks weave behavior without source-code changes.
- **Retransformation/redefinition limits**: you can often change method bodies, but structural changes are restricted. Loaded-class modification depends on JVM capabilities and manifest flags such as `Can-Redefine-Classes` and `Can-Retransform-Classes`.
- **Bytecode libraries**: ASM is low-level and precise; Byte Buddy is the practical high-level layer for matching types/methods and generating transformations; Javassist is simpler but less common in modern agents.
- **Bootstrap and classloader issues**: instrumenting JDK classes or framework internals requires understanding bootstrap injection, helper classes, shaded dependencies, classloader leaks, and circular class loading.
- **Advice shape matters**: injected code must be fast, allocation-conscious, exception-safe, and recursion-safe. Agent bugs run inside the target process and can break application startup.
- **JPMS and strong encapsulation**: agents and reflective instrumentation may need module opens/exports. Know `--add-opens`, hidden classes, and why JDK internals are increasingly protected.
- **Dynamic attach is tightening**: JEP 451 in JDK 21 introduced warnings for dynamically loaded agents and prepares for dynamic loading to be disallowed by default in a future release. Startup `-javaagent` remains the normal production path.
- **Instrumentation vs sampling**: bytecode instrumentation sees specific operations and can capture rich context, but it adds code to the hot path. Sampling profilers usually have lower distortion for CPU/allocation diagnosis.

## 📚 Resources

### Books / chapters
- **The Well-Grounded Java Developer (2nd ed.)** — chapters on class files, reflection, and modern JVM mechanics.
- **Optimizing Java** — profiling/tooling chapters for the operational side of agents.

### Videos & conference talks
- 🎥 **"The Definitive Guide to Java Agents" — Rafael Winterhalter** — best practical explanation of agents and Byte Buddy.
- 🎥 **Byte Buddy talks by Rafael Winterhalter** — type matching, advice, classloading, and runtime code generation.
- 🎥 **OpenJDK serviceability / integrity-by-default talks** — context for attach restrictions and production safety.

### Articles & official docs
- **`java.lang.instrument` package javadoc**: https://docs.oracle.com/en/java/javase/25/docs/api/java.instrument/java/lang/instrument/package-summary.html
- **Instrumentation API javadoc**: https://docs.oracle.com/en/java/javase/25/docs/api/java.instrument/java/lang/instrument/Instrumentation.html
- **JEP 451: Prepare to Disallow the Dynamic Loading of Agents**: https://openjdk.org/jeps/451
- **Byte Buddy tutorial**: https://bytebuddy.net/#/tutorial
- **ASM project**: https://asm.ow2.io/

## ❓ Senior interview questions

1. `premain` vs `agentmain` — when does each run and how is it enabled?
2. What does a `ClassFileTransformer` receive and return?
3. Why is Byte Buddy usually preferred over raw ASM for application agents?
4. What can go wrong if an agent instruments classes used by the agent itself?
5. How do APM tools instrument JDBC or HTTP clients without source changes?
6. What are the limits of redefining an already-loaded class?
7. Why is dynamic agent loading being restricted?
8. Instrumentation profiler vs sampling profiler — which one would you use for production CPU diagnosis?

## 🗺️ Study path

1. Write a minimal `premain` agent that logs class names as they load.
2. Add a Byte Buddy transformer that times one annotated method and prints duration.
3. Package the agent with the correct manifest entries and run a small app with `-javaagent:path/to/agent.jar`.
4. Try dynamic attach locally and observe the JDK warning behavior on a modern JDK.
5. Rehearse Q4: classloader recursion, helper classes, shaded dependencies, and fail-open behavior.

## 📝 My notes

_(your own notes while studying)_
