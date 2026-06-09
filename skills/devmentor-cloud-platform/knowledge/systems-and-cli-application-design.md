# Systems and CLI Application Design

**Domain:** 09 - Cloud, Infrastructure, and Platform
**Primary sources:** Systems Programming with C# and .NET (Vroegop, 2024); Building CLI Applications with C# and .NET (Belmansour, 2025)

## Overview

This topic covers two adjacent but distinct disciplines that sit close to the platform layer: building robust systems-level software, and building well-architected command-line tools. Systems software is the code that does not get in the user's way. It runs as background workers, daemons, services, agents, and device interfaces. It must be fast, memory-disciplined, resilient under failure, and often has to reach below the comfortable managed runtime to call operating-system APIs directly. Command-line tools are the connective tissue of modern infrastructure work: the .NET CLI, git, docker, kubectl, az, terraform, and thousands of internal tools. They must parse arguments predictably, behave well in both human and automated contexts, fail loudly, and distribute cleanly across platforms.

Both disciplines matter to platform and infrastructure engineering because the components built here are the substrate that other software runs on. A leaking background service or a CLI that silently swallows errors degrades every workload that depends on it. The unifying themes are reliability, resource discipline, explicit error handling, cross-platform portability, and respect for the contract a component exposes to its callers (whether that caller is another program or a human at a terminal).

## Core concepts

**Managed vs. unmanaged code.** Managed code runs under the Common Language Runtime (CLR), which provides garbage collection, structured exception handling, and type safety. Unmanaged code (the Win32 API, C/C++ libraries, raw pointers) runs outside that protection. Systems programmers cross this boundary more often than typical application developers and must accept the responsibility that comes with it: manual error checking, manual resource cleanup, and manual memory management. (Vroegop, 2024)

**Low-level vs. high-level APIs.** The "level" of an API is a relative measure of how close it sits to hardware. Low-level APIs (e.g., raw `socket()`/`connect()`/`send()` calls) give granular control at the cost of verbosity and portability; high-level APIs (e.g., `TcpClient`) trade control for productivity and cross-platform reach. Choose low-level only when the high-level abstraction cannot express what you need (an example from the book: switching a socket from TCP to infrared by changing one address-family constant, which the BCL cannot do). (Vroegop, 2024)

**The stack, the heap, and the generational GC.** Value types live on the stack and are reclaimed when scope ends. Reference types live on the heap. The .NET garbage collector is generational: new objects start in generation 0; survivors are promoted to gen 1, then gen 2. Allocation is cheap (move a pointer); collection is expensive (mark, sweep, compact, move). The Large Object Heap (LOH, objects over ~85,000 bytes) is collected rarely and can fragment. Memory discipline in systems code is largely about reducing GC pressure. (Vroegop, 2024)

**Concurrency vs. parallelism vs. threading.** Concurrency is the concept of overlapping execution; parallelism is genuinely simultaneous execution on multiple cores; threading is one mechanism to achieve concurrency. A CPU core does one thing at a time; the OS uses preemptive multitasking to interleave many threads. Single-core devices (IoT, Raspberry Pi) are common in systems work and change the calculus. (Vroegop, 2024)

**CLI application = console application + argument parsing + UX conventions.** At the core of every CLI app is a console application. What makes it a CLI tool is a structured command/subcommand tree, named options instead of positional arguments, an auto-generated help system, version reporting, and consistent, machine-and-human-friendly output. (Belmansour, 2025)

**Packaging vs. distribution vs. deployment.** Packaging bundles the app and its dependencies into a distributable unit. Distribution is the channel that delivers it (NuGet, Docker Hub, WinGet, apt, Homebrew). Deployment is installing and making it operational on the user's machine. These are three separate decisions. (Belmansour, 2025)

## Frameworks & models

**The CLR + BCL mental model (Vroegop, 2024).** Your C# compiles to Intermediate Language (IL), not machine code. The CLR JIT-compiles IL to native code and provides GC, exception handling, and interop. The Base Class Library (BCL) is a vast set of namespaces (`System`, `System.IO`, `System.Net`, `System.Threading`, `System.Diagnostics`, `System.Security`) that wrap the underlying OS. The rule of thumb: use the BCL where you can, drop to low-level APIs only where you must, and mix the two freely.

**P/Invoke six-step model (Vroegop, 2024).** To call a native API: (1) find the API, (2) find the DLL it lives in, (3) load the DLL, (4) declare a stub (`[DllImport]` + `extern` signature), (5) marshal .NET types to native types and back, (6) call it. Always set `SetLastError = true` and retrieve errors via `Marshal.GetLastWin32Error()` / `Marshal.GetPInvokeErrorMessage()` rather than relying on the raw `GetLastError` API. Mind the calling convention (`stdcall` for Win32, `cdecl` for the C runtime).

**The async/await state-machine model (Vroegop, 2024).** `async`/`await` is built on the Task Parallel Library. The compiler rewrites an async method into a state machine that suspends at each `await`, frees the thread, and resumes (often on a different pooled thread) when the awaited work completes. Use it for any operation slower than memory access (I/O is 100,000–1,000,000x slower). The governing rule is "async all the way to the top": every method in the call chain must be async and return `Task`/`Task<T>`.

**The IDisposable pattern (Vroegop, 2024).** A formal recipe for deterministic cleanup of unmanaged resources: implement `IDisposable`, expose a public `Dispose()` that calls a private `Dispose(bool disposing)` and then `GC.SuppressFinalize(this)`; provide a finalizer (`~Class`) that calls `Dispose(false)` as a safety net; in `Dispose(bool)`, clean managed members only when `disposing == true` (the finalizer path cannot trust other managed objects to still exist), and always clean unmanaged resources. Wrap usage in `using` so the compiler emits the try/finally for you.

**System.CommandLine command tree (Belmansour, 2025).** The model is a tree rooted at a `RootCommand`. Every command has a parent (another command or the root). Commands carry `Option<T>` definitions and a handler delegate set via `SetHandler`. The builder pattern (`CommandLineBuilder(...).UseDefaults().Build()`) wires parsing, help, version, and tab completion. The tree literally defines the CLI's grammar: `bookmarkr link add --name ... --url ...` is valid only because `add` is a child of `link` which is a child of root.

**The testing pyramid for CLIs (Belmansour, 2025).** Many unit tests (methods in isolation, dependencies mocked), fewer integration tests (component interactions), fewer system/end-to-end tests, fewest acceptance tests. Non-functional layers cut across: security, performance, usability, and (critically for cross-platform CLIs) compatibility testing across Windows/Linux/macOS and across interactive vs. non-interactive terminals. Cover both the happy path and the unhappy path (bad input, cancellation, network failure).

## Patterns

**IDisposable / using pattern** - Deterministic release of unmanaged or expensive resources. *When to use:* a class owns file handles, native memory (`Marshal.AllocHGlobal`), sockets, native thread handles, or other resources the GC will not reclaim. *Trade-offs:* adds boilerplate and a finalizer; finalizers cost an extra GC cycle, so always call `GC.SuppressFinalize` on the happy path. Forgetting `using`/`Dispose` reintroduces leaks. (Vroegop, 2024)

**Span<T> / Memory<T> windowing** - Operate on a slice of an existing buffer without copying. *When to use:* parsing, splitting, or processing sub-ranges of arrays/buffers in hot paths where allocations hurt. *Trade-offs:* `Span<T>` is stack-only (cannot be a field or used across awaits); use `Memory<T>` for heap/async scenarios. A span over a collected array points to invalid memory. (Vroegop, 2024)

**Object pooling** - Reuse a pool of pre-allocated instances instead of allocating and collecting repeatedly. *When to use:* high-frequency allocation of similar objects where GC pressure dominates. *Trade-offs:* raises baseline memory, adds return-to-pool discipline, and risks state bleed if objects are not reset. (Vroegop, 2024)

**Thread pool / Task abstraction** - Let the runtime manage a reusable set of threads rather than creating threads per unit of work. *When to use:* virtually all concurrent work in modern .NET; prefer `Task`/`Task.Run` and the TPL over manual `Thread` creation. *Trade-offs:* less control over scheduling and stack size than raw `Thread`/Win32 `CreateThread`; for those rare needs (e.g., `STACK_SIZE_PARAM_IS_A_RESERVATION`), drop down deliberately. (Vroegop, 2024)

**Privilege impersonation (least privilege at runtime)** - Run normally as a low-privilege user and elevate to admin only for the specific operation that requires it, then revert. *When to use:* systems software that occasionally needs admin rights (registry HKLM, protected directories, service control, network config) but should not run as admin all the time. *Trade-offs:* requires a dedicated admin credential and careful handle cleanup; mishandling leaves an elevated token open. (Vroegop, 2024)

**Root command + subcommand tree** - Model the CLI as a hierarchy where each command does one thing and only leaf commands that perform work have handlers. *When to use:* any non-trivial CLI with grouped functionality (e.g., `link add`, `link remove`, `link list`). *Trade-offs:* deep trees can confuse users; keep the hierarchy shallow and discoverable through help. (Belmansour, 2025)

**Named options over positional arguments** - Accept `--name`/`-n` flags rather than relying on argument order. *When to use:* almost always; positional parsing forces users to memorize order and breaks silently when arguments are swapped. *Trade-offs:* slightly more typing for the user; mitigated with short aliases and sensible defaults. (Belmansour, 2025)

**Command-as-class with dependency injection** - Each command is a class deriving from `Command`, holding its options and handler, with collaborators (services) injected via the constructor against an interface. *When to use:* once a single `Program.cs` grows past readable length, or when commands need to be tested in isolation. *Trade-offs:* DI adds setup, interfaces, and indirection that are overkill for tiny tools; balance modularity against ceremony. (Belmansour, 2025)

**Graceful cancellation via CancellationToken** - Thread a `CancellationToken` from the command context into async handlers so Ctrl+C triggers a clean shutdown (flush files, release resources) instead of an abrupt crash. *When to use:* any long-running CLI operation (export, download, encode). *Trade-offs:* requires async APIs end-to-end and explicit `OperationCanceledException` handling. (Belmansour, 2025)

**Structured logging with sinks (Serilog)** - Emit structured (JSON) log events to one or more configurable sinks (console, file, cloud). *When to use:* any CLI or service that needs diagnosability after the fact. *Trade-offs:* choose format and destination to match the run context (local tools log locally; avoid mandating cloud connectivity for offline tools); guard against logging sensitive data. (Belmansour, 2025)

**Progressive enhancement / graceful degradation of TUI** - Rich interactive output (progress bars, prompts, trees via Spectre.Console) when the terminal is interactive; plain text when it is not (CI/CD pipelines, redirected output). *When to use:* CLIs serving both humans and automation. *Trade-offs:* must detect interactivity and provide `--interactive` / `--non-interactive` flags; double the output paths to test. (Belmansour, 2025)

## Principles & best practices

Systems software (Vroegop, 2024):
- Systems software should stay out of the way: be as fast and memory-efficient as possible, because everything else depends on it.
- Always check the result of every low-level/native API call. The runtime will not throw for you outside managed code; a missed check turns into a crash far from the real fault.
- Trust the GC by default. In 99 of 100 cases it outperforms hand-rolled memory management. Reach for unsafe code, manual marshaling, and pointers only after measuring.
- Reduce memory pressure deliberately: prefer value types for small data, arrays over `List<T>` where flexibility is not needed, `Span<T>`/`Memory<T>` to avoid copies, avoid boxing, use lazy initialization, null out large/LOH objects, and use weak references for reclaimable caches.
- Avoid finalizers unless implementing the dispose pattern; they survive an extra GC generation and hurt throughput.
- "Async all the way to the top," and never make an async method return `void` (except top-level event handlers) - a returned `void` becomes an uncatchable fire-and-forget thread that swallows exceptions.
- Treat security as a continuous mindset, not a final-phase checklist. Validate and sanitize all external input, run at the lowest privilege that works, encrypt secrets (keep them out of source and config in plaintext; use data protection APIs / key vaults / environment variables), and audit regularly.
- Write for cross-platform from the start: use `Path.Combine`, `Path.DirectorySeparatorChar`, `Environment.NewLine`, and `OperatingSystem.IsWindows()/IsLinux()` rather than hardcoding separators, and remember P/Invoke ties you to one OS.

CLI tools (Belmansour, 2025):
- Do not reinvent argument parsing; use a maintained library (the book favors `System.CommandLine` for its declarative builder model, command hierarchies, auto-help, completion, and platform-aware parsing).
- Provide meaningful command and option names plus descriptions; help is then generated for free and doubles as a discoverability and code-mapping tool.
- Report a version (`--version`) sourced from the project file; default to a sane value.
- Let exceptions bubble to the top-level caller; handle them there with specific catch blocks ordered specific-to-general, and use exception filters (`when`) for status-code-style branching. Never swallow exceptions silently.
- Preserve stack traces: rethrow with bare `throw;`, or wrap-and-rethrow with the inner exception passed in; never `throw ex;`.
- Prefer defensive programming (validate inputs, enforce preconditions) to reduce the number of exceptions you must catch at all.
- Always use a `finally` (or `using`) to release resources on every path.
- Decide the interactivity default by audience: human-first tools default to interactive with a `--non-interactive` escape; automation-first tools default to non-interactive with an opt-in `--interactive`.
- Use color and structured output conventionally (green success, yellow warning, red error) and degrade gracefully where terminals lack support (e.g., emoji, ANSI).
- Choose distribution to match the audience: `.NET tool` (NuGet) and Docker images target developers/IT; platform package managers (WinGet, Homebrew, apt) reach everyone; self-contained single-file publish removes the runtime-installed prerequisite at the cost of size.

## Anti-patterns & pitfalls

- **Swallowing or silently ignoring errors.** In native code a missed return-code check produces a delayed, hard-to-locate crash; in managed code an empty catch hides failures and corrupts state. Both violate fail-fast. (Vroegop, 2024; Belmansour, 2025)
- **`throw ex;` instead of `throw;`.** Resets the stack trace and destroys the diagnostic trail. (Belmansour, 2025)
- **`async void`.** Creates an unobservable fire-and-forget thread; exceptions raised after the first `await` vanish and the operation cannot be awaited or cancelled. (Vroegop, 2024)
- **Overusing finalizers / forgetting `GC.SuppressFinalize`.** Keeps objects alive an extra GC cycle and adds latency for no benefit. (Vroegop, 2024)
- **Premature manual memory management.** Unsafe code and pointer arithmetic can corrupt memory and crash the process; only worthwhile after benchmarking shows the GC is the bottleneck. (Vroegop, 2024)
- **Running everything as administrator "to be safe."** Maximizes blast radius of any vulnerability; elevate only the specific operation that needs it. (Vroegop, 2024)
- **Hardcoding secrets in source or plaintext config**, and **hardcoding path separators or line endings**, both of which break security and portability respectively. (Vroegop, 2024)
- **Positional-only argument parsing.** Users cannot know the order, and swapped arguments fail silently or wrongly. (Belmansour, 2025)
- **A monolithic `Program.cs`.** Commands, options, handlers, and wiring all in one file becomes untestable and unmaintainable; refactor into command classes with injected services. (Belmansour, 2025)
- **Forcing interactivity in non-interactive contexts.** Progress bars and prompts hang or corrupt output in CI/CD; detect the terminal and degrade. (Belmansour, 2025)
- **Over-mocking in tests.** Tests that mock everything stop representing reality and lose value. (Belmansour, 2025)

## Decision guidance

**Low-level API vs. BCL abstraction.** Default to the BCL. Drop to P/Invoke only when (a) the functionality is unavailable in managed APIs (unusual hardware, specific OS features, performance-critical native calls) or (b) you need control the abstraction hides. Accept that doing so ties you to a specific OS/version, requires manual error handling and marshaling, and complicates debugging. (Vroegop, 2024)

**Threading mechanism selection.** Prefer `Task`/TPL and `async`/`await` for almost everything; use the `ThreadPool` implicitly through them. Reach for raw `Thread` when interfacing with libraries that demand it or when you need explicit stack-size/affinity control; reach for Win32 `CreateThread` only for the narrow cases needing reservation-vs-commit stack semantics. (Vroegop, 2024)

**Memory optimization triggers.** Optimize memory only after profiling. When you do: small frequently-allocated data → value types/structs and `Span<T>`; large datasets you do not always need → unload/reload, compress, or weak-reference; per-request churn → pooling. Never assume; benchmark before and after. (Vroegop, 2024)

**Argument-parsing library choice.** `System.CommandLine` for modern, feature-rich CLIs (hierarchies, auto-help, completion, async, hosting/DI integration); a lighter library like CommandLineParser when you need minimal, attribute-based parsing and few dependencies. (Belmansour, 2025)

**Error handling: exception vs. defensive check.** Validate inputs and enforce preconditions first to avoid throwing on predictable conditions (missing file, empty list). Reserve exceptions for genuinely exceptional cases, catch them specifically at the top level, and weigh the performance cost of throwing in hot paths. (Belmansour, 2025)

**Distribution channel.** Map channel to audience and platform: developer/IT tool, cross-platform → `.NET tool` on NuGet or a Docker image; general end users → native package managers (WinGet/Homebrew/apt) or an MSI (Windows-only). Use self-contained single-file publishing when you cannot assume the runtime is installed, accepting a larger artifact. (Belmansour, 2025)

**Default interactivity.** If the primary caller is a human, default to the rich interactive experience and expose `--non-interactive`. If the primary caller is a pipeline or another program, default to plain output and expose `--interactive`. Always degrade gracefully when the terminal is not capable. (Belmansour, 2025)

## Source attribution

- Managed vs. unmanaged code, low-level vs. high-level APIs, CLR/BCL model, IL and JIT: Vroegop (2024), Ch. 1 ("The One with the Low-Level Secrets").
- P/Invoke six-step model, `[DllImport]`, marshaling, calling conventions, `Marshal` error retrieval, debugging native interop: Vroegop (2024), Ch. 1.
- Performance, value vs. reference types, boxing, stack/heap, string handling, unsafe code, compiler optimization: Vroegop (2024), Ch. 2 ("The One Where Speed Matters").
- Generational GC, LOH, finalizers, IDisposable pattern, `Span<T>`/`Memory<T>`, memory-saving techniques, weak references, pooling: Vroegop (2024), Ch. 3 ("The One with the Memory Games").
- Concurrency vs. parallelism, threads (Win32 and .NET), ThreadPool, TPL/`Task`/`Parallel`, async/await state machine, "async all the way to the top": Vroegop (2024), Ch. 4 ("The One with the Thread Tangles").
- Security mindset, vulnerability classes, least privilege, secret/settings protection, privilege impersonation: Vroegop (2024), Ch. 12 ("The One with the Security Safeguards").
- Cross-platform code (`Path`, `OperatingSystem`, `Environment.NewLine`), Linux daemons/services, deployment and Docker: Vroegop (2024), Ch. 13–14 ("Deployment Dramas," "Linux Leaps").
- What a CLI is, why CLIs matter, ubiquity in IT workflows: Belmansour (2025), Ch. 1 ("Introduction to CLI Applications").
- Console application basics, `System.Console`: Belmansour (2025), Ch. 3.
- Argument parsing, positional vs. named options, `System.CommandLine`, root/subcommand tree, options, auto-help, version: Belmansour (2025), Ch. 4 ("Command-Line Parsing").
- Input control/validation, file handling, defaults, allowed values: Belmansour (2025), Ch. 5.
- Exception handling best practices, program termination/CancellationToken, structured logging with Serilog/JSON sinks: Belmansour (2025), Ch. 6 ("Error Handling and Logging").
- Interactive CLIs, Spectre.Console, FIGlet, selection prompts, progress bars, trees, interactivity defaults and graceful degradation: Belmansour (2025), Ch. 7.
- Modular/extensible design, code maps via help, project structure, command classes, dependency inversion/injection, refactoring `Program`: Belmansour (2025), Ch. 8.
- Testing types, the testing pyramid, what/how/when to test, mocking, happy vs. unhappy path, usability/feedback commands: Belmansour (2025), Ch. 10 ("Testing CLI Applications").
- Packaging/distribution/deployment terminology, `.NET tool`/NuGet, Docker, WinGet, self-contained single-file publish, versioning: Belmansour (2025), Ch. 11 ("Packaging and Deployment").

## Recent developments (2024–2026)

- .NET 10 (LTS) and C# 14 shipped in November 2025, supported through November 2028, with significant performance, AI, and developer-experience improvements relevant to systems and CLI software. (Microsoft, 2025, https://devblogs.microsoft.com/dotnet/announcing-dotnet-10/; InfoQ, https://www.infoq.com/news/2025/11/dotnet-10-release/)
- The .NET 10 SDK modernized the CLI itself: a more consistent command surface, native tab-completion script generation for popular shells, CLI introspection via --cli-schema, the new SLNX solution format, and first-party container image creation for console apps. (Microsoft Learn, https://learn.microsoft.com/en-us/dotnet/core/whats-new/dotnet-10/overview)
- Native AOT matured as the default packaging story for fast-starting, dependency-free CLI/systems tools: iOS/Mac Catalyst gained NativeAOT support in .NET 9, and .NET 10's new file-based programs (run a single .cs file) publish in Native AOT mode by default, with typical executables around 1 MB. (Soundar Anbalagan, https://code.soundaranbu.com/state-of-nativeaot-net10; Andrew Lock, https://andrewlock.net/exploring-dotnet-10-preview-features-7-packaging-self-contained-and-native-aot-dotnet-tools-for-nuget/)
- Native AOT remains constrained to same-OS builds (no cross-OS compilation) but supports limited x64/arm64 cross-architecture compilation when the native toolchain is present, a key consideration for cross-platform CLI distribution. (Microsoft Learn, https://learn.microsoft.com/en-us/dotnet/core/deploying/native-aot/cross-compile)
- CLIs increasingly serve agentic AI as well as humans: Docker and others standardized on the Model Context Protocol (MCP) and AGENTS.md conventions in 2025, raising the bar for tools to be predictable, scriptable, and machine-consumable. (Docker, 2025, https://www.docker.com/blog/2025-recap-the-year-software-development-changed-shape/)
