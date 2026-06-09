# GoF Design Patterns (and a Tool-Based View of Design)

**Domain:** 06 - Software Design Patterns and DDD
**Primary sources:** Creational Design Patterns in C# (Vaskaran Sarcar, 2025, building on Gang of Four); Software Design by Example: A Tool-Based Introduction with Python (Greg Wilson, 2024); the Gang of Four catalog (Gamma, Helm, Johnson, Vlissides, 1994)

## Overview
The Gang of Four (GoF) design patterns are named, reusable solutions to recurring object-oriented design problems, grouped into three families: **creational** (how objects are created), **structural** (how objects and classes are composed), and **behavioral** (how objects interact and distribute responsibility). A design pattern is "specific enough to be worth naming, but not so specific that a single best implementation can be provided by a library" (Wilson 2024). Patterns give teams a shared vocabulary, capture proven trade-offs, and steer designs toward loose coupling and extension without modification.

This topic combines two complementary treatments. Sarcar gives a detailed, modern C# treatment of the five **creational** patterns plus Dependency Injection, including the GoF intents and multiple implementation variants. Wilson teaches design by building small versions of the tools programmers use every day (a test runner, an interpreter, a file backup tool, an HTML validator, a build manager, a virtual machine), and along the way shows where real patterns naturally appear (Visitor, Chain of Responsibility, Null Object, Decorator, Iterator, Factory Method, Template Method, Singleton) and how making code testable also makes it reusable. Together they cover the GoF families and ground them in how real systems are actually designed.

## Core concepts

### The three GoF families
- **Creational** - abstract and control object creation so client code does not depend on concrete classes: Abstract Factory, Factory Method, Builder, Prototype, Singleton.
- **Structural** - compose objects/classes into larger structures while keeping them flexible: Adapter, Decorator, Facade, Proxy, Composite, Bridge, Flyweight.
- **Behavioral** - assign responsibilities and define communication between objects: Strategy, Observer, Command, State, Template Method, Iterator, Mediator, Chain of Responsibility, Visitor, Memento.

### Underlying design principles (Wilson 2024; GoF)
- **Program to an interface, not an implementation**; **favor composition over inheritance.**
- **Polymorphism** - many implementations of one interface, called interchangeably; the engine behind Strategy, State, Template Method, and most behavioral patterns.
- **Duck typing** - the methods an object has determine how it can be used, not its class hierarchy (a Python-flavored route to polymorphism).
- **Liskov Substitution Principle** - a derived object must be usable wherever its base is, without breaking the program; enforced by **design by contract** (weaken preconditions, strengthen postconditions).
- **Dependency Inversion Principle (DIP)** - depend on abstractions; high-level policy should not depend on low-level detail. Achieved in practice by **Dependency Injection / Inversion of Control**.

### Objects and classes are dictionaries with behavior (Wilson 2024)
Wilson's foundational lesson: objects and classes can be implemented as dictionaries with stereotyped behavior, and method lookup is a search up the class chain. Understanding this demystifies inheritance, overriding, and why patterns like Decorator and Visitor work the way they do.

## Frameworks & models

### Creational patterns in depth (Sarcar 2025)
- The five creational patterns abstract the `new` operator. Factory Method and Abstract Factory defer or group instantiation; Builder sequences multi-step construction; Prototype clones an existing instance; Singleton restricts to one instance.
- A **simple factory** (a static method/class returning concrete types by parameter) is a common precursor; it centralizes creation but introduces global state, so many prefer factory subclasses.
- **Dependency Injection** is presented as a technique for achieving **Inversion of Control** and the Dependency Inversion Principle, via constructor, property, or method injection, decoupling a class from constructing its collaborators.

### Building real tools reveals patterns (Wilson 2024)
- A **pattern matcher** (glob/regex) is built so matchers delegate to other matchers (Chain of Responsibility) and a Null Object eliminates special cases at the end of the chain.
- An **interpreter** evaluates expression trees; integers evaluate to themselves and lists trigger function calls, illustrating the interpreter/composite structure and environments/closures.
- A **file backup tool**, **template expander**, **HTML validator**, and **code linter** are built by walking tree structures with the Visitor pattern, separating traversal from the operation done at each node.
- A **decorator** wraps one function with another (logging, timing), demonstrating the structural Decorator at the language level.
- Factory Methods appear when re-using an app skeleton (e.g., a buffer/editor app) across versions by letting subclasses choose which objects to create.

## Patterns
For each: name, intent, when to use, trade-offs. (Also appended to PATTERN-CATALOG.md.)

### Creational
- **Factory Method** - Intent (GoF): define an interface for creating an object, but let subclasses decide which class to instantiate. When: a class cannot anticipate the concrete type it must create, or wants subclasses to specify it; keep common creation code in one place. Trade-offs: removes `new` coupling and supports open/closed extension; adds a parallel class hierarchy.
- **Abstract Factory** - Intent: provide an interface for creating families of related objects without specifying concrete classes. When: the system must be independent of how products are created and products come in compatible families. Trade-offs: enforces family consistency and swappable product sets; adding a new product type changes every factory.
- **Builder** - Intent (GoF): separate the construction of a complex object from its representation so the same process can create different representations. When: an object has many parts assembled in a configurable sequence (no single-step creation). Trade-offs: readable step-by-step/fluent construction and reusable build process; more classes (Builder, ConcreteBuilder, Director, Product).
- **Prototype** - Intent (GoF): specify the kinds of objects to create using a prototypical instance, and create new objects by copying that prototype. When: object creation is costly or the concrete class is decided at runtime; an existing instance is a good template. Trade-offs: cheap creation by cloning and runtime flexibility; deep-vs-shallow copy hazards.
- **Singleton** - Intent (GoF): ensure a class has only one instance and provide a global point of access. When: exactly one coordinating object is needed (cache, config, thread pool, file system manager) and construction is costly. Trade-offs: controlled single instance; a form of global state that complicates testing and concurrency (needs thread-safe lazy init).
- **Dependency Injection (IoC)** - Intent: supply a class's dependencies from outside rather than having it construct them, inverting control to satisfy DIP. When: decoupling classes from concrete collaborators, enabling testing and configuration. Trade-offs: loose coupling and testability; needs wiring (often a container) and adds indirection. *(Sarcar treats this as a creational concern.)*

### Structural
- **Adapter** - Intent: convert one interface into another that clients expect. When: reusing a class whose interface does not match. Trade-offs: enables reuse without changing the adaptee; one more wrapper.
- **Decorator** - Intent: attach responsibilities to an object dynamically by wrapping it in a compatible object. When: add behaviors (logging, timing, formatting) without subclass explosion. Trade-offs: flexible composition at runtime; many small wrappers and harder identity/debugging. *(Wilson builds this at function level.)*
- **Facade** - Intent: provide a simple unified interface over a complex subsystem. When: hide complexity and decouple clients from internals. Trade-offs: easier use and looser coupling; can become a god-object if it grows.
- **Proxy** - Intent: a surrogate controlling access to another object (lazy, remote, protection, caching). When: defer creation, mediate remote access, or add access control. Trade-offs: transparent indirection and cross-cutting control; extra layer and possible latency.
- **Composite** - Intent: compose objects into tree structures and treat individual and composite objects uniformly. When: part-whole hierarchies (documents, file systems, UI trees). Trade-offs: uniform client code; can over-generalize leaf vs. composite distinctions.
- **Bridge** - Intent: decouple an abstraction from its implementation so both vary independently. When: avoid a combinatorial class explosion across two dimensions. Trade-offs: independent extension; more indirection up front.
- **Flyweight** - Intent: share fine-grained objects to support large numbers efficiently by separating intrinsic from extrinsic state. When: huge numbers of similar objects (glyphs, tiles). Trade-offs: large memory savings; complexity of managing shared/extrinsic state.

### Behavioral
- **Strategy** - Intent: define a family of interchangeable algorithms and select one at runtime. When: multiple variants of a behavior (e.g., revenue recognition, sorting). Trade-offs: swap algorithms and avoid conditionals; clients must know the strategies.
- **Observer** - Intent: define a one-to-many dependency so dependents are notified of state changes. When: event/notification, model-view updates. Trade-offs: loose coupling between subject and observers; update order and cascade can be hard to follow.
- **Command** - Intent: encapsulate a request as an object, enabling queuing, logging, and undo. When: parameterize actions, support undo/redo, or decouple invoker from receiver. Trade-offs: first-class operations and undo support; many small command classes.
- **State** - Intent: let an object alter its behavior when its internal state changes, as if it changed class. When: behavior depends on a state machine. Trade-offs: removes large conditionals; one class per state.
- **Template Method** - Intent (GoF/Wilson): a parent class defines the skeleton of an algorithm by calling abstract steps that subclasses implement. When: invariant overall flow with varying steps. Trade-offs: reuse of structure with customizable parts; relies on inheritance and the parent owning the flow.
- **Iterator** - Intent (Wilson): hide the differences between data structures so everything can be processed with uniform loops. When: traverse collections without exposing representation. Trade-offs: uniform traversal; an extra abstraction per structure.
- **Mediator** - Intent: encapsulate how a set of objects interact, so they refer to a mediator instead of each other. When: many-to-many object interactions become tangled. Trade-offs: reduces direct coupling; the mediator can become complex.
- **Chain of Responsibility** - Intent (Wilson): each object either handles a request or passes it to the next. When: more than one handler may process a request and the handler is decided at runtime (e.g., a pattern matcher). Trade-offs: decoupled, extensible handling; no guarantee a request is handled (pair with Null Object).
- **Visitor** - Intent (Wilson): take the operation to each element of a structure in turn, keeping traversal separate from the per-node operation. When: many distinct operations over a stable tree (HTML validator, linter, template expander); the more complex the structure, the more it pays off. Trade-offs: add operations without touching the node classes; adding new node types touches every visitor.
- **Memento** - Intent: capture and externalize an object's internal state so it can be restored later, without violating encapsulation. When: undo/checkpoints. Trade-offs: clean state capture; memory cost of stored states.
- **Null Object** - Intent (Wilson): a placeholder object that has the expected methods but does nothing, used instead of `None`/null. When: eliminate repeated null checks (e.g., terminating a Chain of Responsibility). Trade-offs: removes special-case code; can hide genuinely missing values if overused.

## Principles & best practices
- Program to interfaces and favor composition over inheritance; reach for a pattern only when a real, recurring problem justifies it. (GoF; Wilson 2024)
- Centralize object creation behind factories/builders so client code does not depend on concrete classes or the `new` operator. (Sarcar 2025)
- Use Dependency Injection to satisfy the Dependency Inversion Principle and to make units testable in isolation. (Sarcar 2025)
- Separate traversal from operation with Visitor; the harder the data structure, the greater the benefit. (Wilson 2024)
- Replace recurring null checks with the Null Object pattern; terminate handler chains cleanly. (Wilson 2024)
- Make code testable; testable code tends to be more loosely coupled and more reusable. (Wilson 2024)
- Respect the Liskov Substitution Principle via design by contract so polymorphic substitution stays safe. (Wilson 2024)

## Anti-patterns & pitfalls
- **Pattern overuse / cargo-culting** - applying patterns without a problem that needs them, adding ceremony and indirection.
- **Singleton as global state** - hidden coupling, hard-to-test code, and concurrency bugs from unsynchronized lazy initialization.
- **God Facade** - a facade that accretes responsibilities until it becomes the very complexity it was meant to hide.
- **Shallow-vs-deep copy bugs** in Prototype, where clones unintentionally share mutable substructure.
- **Visitor brittleness** - adding a new node type forces edits to every visitor; choose Visitor only when operations vary more than node types.
- **Subclass explosion** avoided by Decorator/Strategy/Bridge - using inheritance where composition fits better.
- **Static simple factories** promoting global state instead of injectable factories.

## Decision guidance
- **Need to control creation?** One concrete type chosen by subclass -> Factory Method. Families of related products -> Abstract Factory. Complex multi-step assembly -> Builder. Costly creation or runtime-chosen class with a good template -> Prototype. Exactly one instance -> Singleton (prefer DI-managed single instance to avoid global state). Decouple from constructing collaborators -> Dependency Injection.
- **Need to compose structure?** Mismatched interface -> Adapter. Add behavior dynamically -> Decorator. Simplify a subsystem -> Facade. Control/defer access -> Proxy. Part-whole tree -> Composite. Two independent dimensions -> Bridge. Many similar objects -> Flyweight.
- **Need to distribute behavior?** Interchangeable algorithms -> Strategy. State-dependent behavior -> State. Notifications -> Observer. Undoable/queued actions -> Command. Fixed flow, varying steps -> Template Method. Uniform traversal -> Iterator. Tangled object interactions -> Mediator. One of several handlers -> Chain of Responsibility (+ Null Object). Many operations over a stable tree -> Visitor. Snapshot/restore -> Memento.
- **General rule:** prefer the simplest design; introduce a pattern when the cost of not having it (duplication, rigidity, coupling) is concrete and recurring.

## Source attribution
- Creational patterns (Factory Method, Abstract Factory, Builder, Prototype, Singleton), GoF intents, simple-factory discussion, Dependency Injection / IoC / DIP: Sarcar 2025, ch. 1–5.
- Tool-based design and the appearance of Chain of Responsibility, Null Object, Visitor, Decorator, Iterator, Factory Method, Template Method, Singleton; objects-as-dictionaries, polymorphism, duck typing, Liskov Substitution Principle, design by contract, definition of "design pattern": Wilson 2024, ch. 2, 4, 7–13, 19, 24, and glossary.
- The three-family taxonomy and structural/behavioral pattern intents: the Gang of Four catalog (Gamma et al., 1994) as referenced and applied by both Sarcar and Wilson.

## Recent developments (2024–2026)
- **GoF patterns are being reassessed for AI-native codebases.** A 2026 thesis argues that patterns which solved human cognitive-load problems can add friction for LLM agents, which process code linearly and rely on explicit context; deep inheritance trees and complex polymorphism are flagged as the biggest sources of degraded LLM reasoning because resolving a call means traversing many abstraction layers. The implication is to prefer flatter, more explicit designs (e.g., composition and direct dispatch over deep hierarchies) where AI agents are the primary readers/maintainers. (dasroot.net, "Code for the AI Reader", 2026, https://dasroot.net/posts/2026/05/code-for-ai-reader-redesigning-architecture-llm-era/)
- **Empirical studies test whether LLMs "understand" patterns.** ICSE 2025 / LLM4Code work evaluates code LLMs across pattern recognition, comprehension, and generation and finds they frequently fail to follow a project's required patterns and coding standards, generating code that conflicts with the intended design. (ICSE 2025, "Do Code LLMs Understand Design Patterns?", 2025, https://conf.researchr.org/details/icse-2025/llm4code-2025-papers/30/)
- **Pattern detection from source code is an active ML task.** Recent research uses LLM-based feature engineering plus classifiers (e.g., SVM) to predict GoF patterns in code, aimed at automated architecture recovery and documentation. (IET Software, Wiley, "Design Pattern Prediction From Source Code Using LLM-Based Feature Engineering and SVM Classification", 2026, https://ietresearch.onlinelibrary.wiley.com/doi/10.1049/sfw2/7163249)
- **Patterns endure as shared vocabulary and review criteria.** GenAI refactoring studies still use GoF patterns and SOLID as the "good-practice" yardstick for assessing AI-generated code, and the consensus remains that a developer who understands the patterns and trade-offs builds better systems than someone prompting an LLM blindly. (ScienceDirect, 2025, https://www.sciencedirect.com/science/article/abs/pii/S0164121225001876)
- **The catalog itself is stable.** The three families and individual pattern intents are unchanged in this period; the movement is in how patterns are detected, taught, and weighed against AI-readability, not in the patterns' definitions.
