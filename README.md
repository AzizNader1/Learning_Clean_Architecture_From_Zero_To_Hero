# Clean Architecture with .NET 10 and SQL Server — From Zero to Mastery

> The complete, step-by-step, line-by-line guide to building production-grade applications with Clean Architecture, Vertical Slices, CQRS, MediatR, EF Core, and thin API controllers — starting from absolute zero knowledge and ending at mastery.

---

## About This Book

This book teaches you **Clean Architecture** by building a real, working application — one chapter at a time, one line of code at a time, one word at a time.

- **Who it is for:** You can write basic C# (classes, interfaces, `async`/`await`), but everything about software architecture is explained **from absolute zero**. No prior architecture knowledge is assumed. Ever.
- **What you will build:** **ShopSphere**, an e-commerce REST API with products, categories, stock management, and order placement — the classic rich business domain that makes every architectural concept meaningful.
- **How it is written:** Every chapter builds on the previous one. Every line of code is explained. Every technical word is defined in Chapter 4 before it is ever used against you.
- **How to use it:** Read top to bottom. Type the code yourself. Use the table of contents below and your editor's search (`Ctrl+F`) to jump around when you need a reference.

### The Stack You Will Master

| Technology | Version | Role in Our Application |
|---|---|---|
| .NET | 10 (LTS, November 2025) | The platform everything runs on |
| C# | 14 | The language we write |
| ASP.NET Core | 10 | The web framework (the API) |
| Entity Framework Core | 10 | The ORM that talks to SQL Server |
| SQL Server | 2022 | The database |
| MediatR | 12.5.0 (last fully free Apache-2.0 release) | The in-process mediator that carries commands and queries |
| FluentValidation | 12.2.0 | The validation library behind every Validator file |

> **A note on MediatR licensing (important, current as of 2026):** MediatR's creator, Jimmy Bogard, moved MediatR to a commercial dual-license model in July 2025, starting with version 13. Versions 13+ are free for individuals, students, open-source projects, and companies under $5M annual revenue (you register a free key at [mediatr.io](https://mediatr.io)), and paid for larger companies. **Version 12.5.0 — the version this book uses — is permanently free under the Apache-2.0 license** and still fully supported on .NET 10. Everything in this book also applies to MediatR 13/14 if you qualify for the free tier. Chapter 11 explains this whole story in detail.

---

## Table of Contents

**Part I — Foundations (Understanding Before Building)**

1. Chapter 1 — Introduction: What This Book Is and What You Will Build
2. Chapter 2 — The History: How Software Architecture Evolved Into Clean Architecture
3. Chapter 3 — What Clean Architecture Gives You (and What It Costs)
4. Chapter 4 — The Complete Dictionary: Every Word in Clean Architecture Explained
5. Chapter 5 — The Dependency Rule: The Heart of Clean Architecture

**Part II — The Workspace**

6. Chapter 6 — Environment Setup: .NET 10, SQL Server, and Your Toolbox
7. Chapter 7 — Creating the Solution: Four Projects and Their Contracts

**Part III — The Domain Layer**

8. Chapter 8 — The Domain Layer: Where the Business Lives
9. Chapter 9 — Building the ShopSphere Domain Model, Line by Line

**Part IV — The Application Layer**

10. Chapter 10 — The Application Layer Mindset: Use Cases and CQRS
11. Chapter 11 — MediatR From Zero: The Mediator Pattern in Practice
12. Chapter 12 — Vertical Slice Architecture: One Feature, Three Files
13. Chapter 13 — Our First Slice: CreateProduct — Command, Handler, Validator, Line by Line
14. Chapter 14 — Reading Data: The Query Slices
15. Chapter 15 — Completing the CRUD: Update, Delete, Categories, and Placing Orders

**Part V — The Infrastructure Layer**

16. Chapter 16 — EF Core From Zero: The ORM Explained
17. Chapter 17 — Mapping the Domain to SQL Server: Fluent Configurations
18. Chapter 18 — Repositories: The Contract in Application, the Implementation in Infrastructure
19. Chapter 19 — Unit of Work: One Transaction to Rule the Request
20. Chapter 20 — Migrations and Seeding: Bringing the Database to Life

**Part VI — The Presentation Layer**

21. Chapter 21 — The API Project: `Program.cs` From Top to Bottom
22. Chapter 22 — Thin Controllers: HTTP Meets MediatR
23. Chapter 23 — Cross-Cutting Concerns: The Validation Pipeline and Global Error Handling

**Part VII — Mastery**

24. Chapter 24 — Testing the Architecture: Unit and Integration Tests
25. Chapter 25 — Advanced Techniques: Domain Events, Auditing, Soft Delete, Concurrency
26. Chapter 26 — The Mastery Mindset: Best Practices, Common Mistakes, and the Road Ahead

**Appendices**

- Appendix A — The Complete Solution Folder Tree
- Appendix B — Every Command in Order
- Appendix C — Package and Version Cheat Sheet

---

# PART I — FOUNDATIONS (UNDERSTANDING BEFORE BUILDING)

> You cannot "apply" an architecture you cannot explain. Part I gives you the vocabulary, the history, and the one iron rule that makes Clean Architecture what it is. We will not write a single line of application code until every word in the title of this book means something precise to you.

---

## Chapter 1 — Introduction: What This Book Is and What You Will Build

### 1.1 What This Book Is

"Clean Architecture" is a way of organizing code so that the **core of your application — the business rules, the things your software is actually *for*** — depends on nothing else. Not on the database. Not on the web framework. Not on any library. Everything else depends on the core, never the other way around.

That one sentence, fully understood, is 80% of this book. The remaining 20% is the craft of actually building it: which project holds which file, why a command has exactly three files, why a controller should be almost empty, and why that emptiness is a feature.

This book is **complete**: it starts from "what is a layer?" and ends with domain events, concurrency, and the judgment to know when Clean Architecture is overkill. It is **step by step**: you could follow it with a blank folder and end with a running application. And it is **line by line**: every block of code is followed by an explanation of what each line does and why it must exist.

### 1.2 What This Book Is Not

It is not a C# tutorial — we assume you can read a class, an interface, and an `async` method. It is not a SQL Server administration guide — we create exactly one database and never leave it. And it is not a book about microservices — Clean Architecture is most often, and best, learned inside a **single deployable application** (a "monolith" — a perfectly respectable thing to be), and that is what we build.

### 1.3 Who You Are

You can write C#, but the moment a project grows past three files you feel the walls closing in: code that touches the database lives next to code that formats HTTP responses, changing one thing breaks another, and "just add a service class" has stopped working. You have heard words like *layer*, *repository*, *CQRS*, *MediatR*, and *vertical slice* and you want them to stop being noises. This book is written to take you from exactly that point to genuine mastery.

### 1.4 What You Will Build: ShopSphere

ShopSphere is a small but honest e-commerce API. By the final chapter it will support:

- **Products** — with names, SKUs (stock-keeping unit codes — human-readable product identifiers like `TSHIRT-RED-XL`), prices with currency, stock quantities, and categories.
- **Categories** — such as "Electronics" and "Books", to which products belong.
- **Orders** — a customer places an order for several products; the system verifies stock, decreases it, and records the order with its line items — all in one atomic transaction.
- **Full CRUD** — create, read (single and paged lists with search and filtering), update, and delete for products and categories.
- **Production hygiene** — input validation on every request, global error handling with proper HTTP status codes, and automated tests at every layer.

The business rules are deliberately realistic: a product's price must be positive, stock can never go negative, a SKU is unique, an order cannot order zero items. These small rules are where architecture proves itself.

### 1.5 The Destination, At a Glance

By the end of Part VI, our solution will contain four projects, and the direction of dependency between them will form this exact shape:

```
            ┌─────────────────────────────────────────────────┐
            │                                                 │
            │   ShopSphere.Api  (Presentation)                │
            │   Controllers: receive HTTP, send via MediatR   │
            │                                                 │
            │        ┌─────────────────────────────────┐      │
            │        │                                 │      │
            │        │  ShopSphere.Infrastructure      │      │
            │        │  EF Core, Repos, UnitOfWork    │      │
            │        │                                 │      │
            │        │     ┌─────────────────────┐     │      │
            │        │     │                     │     │      │
            │        │     │ ShopSphere.          │     │      │
            │        │     │ Application          │     │      │
            │        │     │ Commands, Queries,  │     │      │
            │        │     │ Handlers, Validators│     │      │
            │        │     │                     │     │      │
            │        │     │  ┌───────────────┐  │     │      │
            │        │     │  │ ShopSphere.   │  │     │      │
            │        │     │  │ Domain        │  │     │      │
            │        │     │  │ Entities      │  │     │      │
            │        │     │  └───────────────┘  │     │      │
            │        │     └─────────────────────┘     │      │
            │        └─────────────────────────────────┘      │
            └─────────────────────────────────────────────────┘

        ARROWS OF DEPENDENCY (who references whom) ALWAYS POINT INWARD.
        Domain references NOTHING. Everything eventually references Domain.
```

Read that diagram carefully, because it is the whole book in one picture: **Domain** sits in the center holding the business entities; **Application** wraps it with use cases (commands and queries); **Infrastructure** implements the technical details behind interfaces the Application defines; **Api** is a thin shell that translates HTTP into MediatR messages. If any line in this paragraph is unclear right now — perfect. That is what Chapters 4 and 5 are for.

### 1.6 The Conventions of This Book

1. **File paths above code blocks.** Every code block starts with a comment naming its exact file, like `// File: src/ShopSphere.Domain/Products/Product.cs`. Type the files where we say, with the names we say.
2. **Line-by-line explanation.** After significant code blocks, we walk through the code. When we say "word by word," we mean it: `public sealed record CreateProductCommand(...) : IRequest<Guid>` will get every one of its six words explained (Chapter 13).
3. **Key Takeaways.** Every chapter ends with a short recap. If you remember only the takeaways, you still remember the chapter.
4. **Progressive honesty.** Early chapters simplify; later chapters return to the same code and deepen it (for example, the tiny `Program.cs` of Chapter 7 grows up in Chapter 21).

### Key Takeaways — Chapter 1

- Clean Architecture = your business core depends on nothing; everything else depends on it.
- We build **ShopSphere**, an e-commerce API with products, categories, and orders.
- Four projects: **Domain** (center) ← **Application** ← **Infrastructure** ← **Api** (shell).
- Every chapter builds on the previous; every line of code is explained; every chapter ends with Key Takeaways.

---

## Chapter 2 — The History: How Software Architecture Evolved Into Clean Architecture

### 2.1 Why History Matters

Architectural patterns are answers to specific, historical pains. If you learn Clean Architecture as a list of rules, you will misapply them. If you learn it as the latest answer in a 50-year conversation, you will know *exactly* which problem you are solving and when the pattern is overkill. So we start at the beginning.

### 2.2 The Era of No Architecture (1950s–1980s)

Early software was small and hardware was the constraint. Code was written, run, and often thrown away. As commercial software grew in the 1970s and 80s, codebases grew into what the industry affectionately calls the **Big Ball of Mud**: SQL queries glued to button-click handlers, business rules duplicated in twenty places, nobody able to say where anything lives. The pain this caused had a name — **spaghetti code** — because pulling on any one strand moves the entire plate.

### 2.3 Layered Architecture (1990s–2000s): The First Widespread Answer

The first mainstream cure was to **separate by technical role**. Split the application into layers, each serving the one above it:

```
┌───────────────────────────────┐
│ Presentation layer            │  UI, screens, later: web pages / API
├───────────────────────────────┤
│ Business layer                │  Rules: "a product needs a price"
├───────────────────────────────┤
│ Data access layer (DAL)       │  SQL, connections, "how to save"
├───────────────────────────────┤
│ Database                      │  SQL Server, Oracle...
└───────────────────────────────┘
```

This was genuinely better — but it carried a fatal flaw that would take the industry a decade to articulate: **the arrows pointed the wrong way**. The business layer *depends on* the data access layer beneath it, because business code calls the DAL to load and save objects. The business rules — the most valuable, most stable, most reason-the-software-exists code — end up shaped by, coupled to, and hostage to the least valuable, most volatile layer: the plumbing. Want to switch databases? Your business layer changes. Want to unit test a rule without a database? Impossible. Layered architecture organized the mess, but left the dependency arrows pointing outward, toward the tools, instead of inward, toward the meaning.

### 2.4 Domain-Driven Design (2003): Rediscovering the Domain

In 2003, Eric Evans published *Domain-Driven Design* (the famous "blue book"). Its core message: the **domain** — the subject area the software serves (banking, shipping, shopping) — and its **model** — the software representation of that domain's concepts and rules — deserve to be the center of a project's attention, expressed in a shared **ubiquitous language** (one vocabulary used by both experts and code: a "Product" in the meeting room is a `Product` class in the source). DDD gave us the tactical vocabulary we still use in the Domain layer today: **Entity**, **Value Object**, **Aggregate**, **Repository** — all defined carefully in Chapter 4. DDD told us *what* should sit at the center; it did not yet give the masses a *physical project structure* that forced it to stay there.

### 2.5 Hexagonal Architecture (2005): Turning the Arrows Around

Alistair Cockburn coined **Hexagonal Architecture**, also called **Ports and Adapters**, around 2005. Its insight is the single most important sentence in this chapter: **the application core should define interfaces (ports) for what it needs, and the outside world should implement them (adapters)**. The database is not a foundation you build *on*; it is a peripheral device you *plug in*. A "port" might be `IProductRepository` ("I need someone who can store and fetch products"); an "adapter" is the EF Core implementation of that interface. The core never imports adapter technology; adapters implement core interfaces. With that single move, the dependency arrows reversed — they now pointed **inward**. Hexagonal architecture gave the world the reversal trick, but its hexagon diagrams were abstract and it stayed a niche idea for a decade.

### 2.6 Onion Architecture (2008): Making It a Project Structure

Jeffrey Palermo's **Onion Architecture** (2008) translated the reversal into a concrete .NET solution layout: concentric rings, with a **Domain Model** (entities, no dependencies at all) at the center, a **Domain Services** ring around it, then application services, and — in the outermost ring only — infrastructure: databases, UI, web services. The rule was explicit: **all dependency is inward**; the outer rings may reference inner rings, never the reverse; the outer rings *implement interfaces declared in inner rings*. If you squint at the description, you have already seen this book's destination diagram. Onion architecture made the idea practical, but the name "onion" and the ring diagrams left many developers uncertain exactly which code goes on which ring.

### 2.7 Clean Architecture (2012): The Populist Unification

In August 2012, Robert C. Martin ("Uncle Bob") published the blog post *The Clean Architecture*, later expanded into his 2017 book *Clean Architecture: A Craftsman's Guide to Software Structure and Design*. His observation was disarmingly simple: Hexagonal, Onion, and several other schemes (including Ivar Jacobson's earlier work) all "scream" the same single idea. He drew them all as **concentric circles**, named the rings in plain English, and crowned the idea with one law — the **Dependency Rule**:

> *The Dependency Rule: source code dependencies must point only inward, toward higher-level policies. Nothing in an inner circle can know anything at all about something in an outer circle.*

The circles, from inside out: **Entities** (enterprise business rules), **Use Cases** (application business rules), **Interface Adapters** (controllers, presenters, gateways), **Frameworks & Drivers** (web framework, database, UI). Clean Architecture is not new science; it is the friendly, named, teachable packaging of the dependency inversion that Cockburn and Palermo had already engineered — and it is the packaging this whole industry adopted. That is the lineage we build in this book, with .NET 10.

### 2.8 Vertical Slice Architecture (2015–): Organizing by Feature

One more step completes our story. Clean Architecture's circles tell you how dependencies flow, but they do not tell you *where to put the files for "update a product."* Early Clean Architecture projects answered with horizontal folders inside the Application layer (`Services/`, `Validators/`, `Mappings/`) — and large projects found that a single feature's code was smeared across a dozen folders.

**Vertical Slice Architecture**, popularized by Jimmy Bogard (the same developer who created MediatR, among many things, from the mid-2010s onward), answered: organize by **feature**, not by kind. Every feature ("create a product") gets its own folder containing everything that feature needs — in our case, exactly three files: its **Command** (the input message), its **Handler** (the use case logic), and its **Validator** (the input rules). This is *not* a replacement for Clean Architecture — it is the modern way to organize Clean Architecture's Application layer. Our book uses Clean Architecture for the solution's dependency flow **and** Vertical Slices for the Application layer's folder structure. They are best friends.

### 2.9 Where We Stand Today (2026)

The family tree, in one glance:

```
Big Ball of Mud
   └─ Layered Architecture (1990s)   — separate by technical role; arrows point OUTWARD (the flaw)
        └─ Domain-Driven Design (2003, Evans) — the DOMAIN deserves the center
        └─ Hexagonal / Ports & Adapters (2005, Cockburn) — reverse the arrows: core defines ports
             └─ Onion Architecture (2008, Palermo) — rings as a real .NET solution
                  └─ Clean Architecture (2012, Martin) — the circles + the Dependency Rule
                       └─ + Vertical Slice Architecture (Bogard) — organize the middle by FEATURE
                            └─ = THIS BOOK: .NET 10 + SQL Server + MediatR + EF Core
```

Every one of these is "the same idea" — protect the core, invert the dependencies — expressed with increasing practicality. When you finish this book, you will not just "use" Clean Architecture; you will know exactly which historical pain each folder in your solution is the cure for.

### Key Takeaways — Chapter 2

- Layered architecture (1990s) separated concerns but pointed dependencies outward, toward the database — the flaw everything after it fixed.
- DDD (2003, Evans) put the domain and its model at the center and gave us Entity, Value Object, Aggregate, Repository.
- Hexagonal (2005, Cockburn) reversed the arrows: the core defines interfaces (ports); the outside implements them (adapters).
- Onion (2008, Palermo) turned the reversal into a concrete .NET project structure with rings.
- Clean Architecture (2012, Martin) unified them: concentric circles + the single Dependency Rule (Chapters 5 and beyond).
- Vertical Slices (Bogard) organize the Application layer by feature: three files — Command, Handler, Validator — per use case.

---

## Chapter 3 — What Clean Architecture Gives You (and What It Costs)

### 3.1 The Honest Sales Pitch

Before you invest 26 chapters of your life, you deserve an honest contract. Clean Architecture is not free. It buys specific, valuable properties — and it charges for them in complexity. A professional knows both columns of that ledger.

### 3.2 What You Gain

**1. Independence from frameworks and libraries.** Your business rules never import Entity Framework, ASP.NET, or any NuGet package. When a library dies (and they do — see the MediatR licensing story in Chapter 11), or when your company migrates from SQL Server to PostgreSQL, the folders that change are the *outer* ones. The center — the code that encodes why the software exists — is untouched.

**2. Testability without ceremony.** Because the Application layer's handlers depend on **interfaces** (`IProductRepository`, `IUnitOfWork`), you can unit test "create a product" by handing the handler a fake repository — no database, no web server, milliseconds per test. Business rules in the Domain layer are pure C#: testing them is testing a function.

**3. A codebase that scales with your team.** Vertical slices mean a new feature is a new folder — not a merge conflict in a 3,000-line `ProductService` that five developers are editing at once. Two features touch each other only through explicit, reviewable contracts.

**4. Change that stays local.** The architecture physically enforces the Dependency Rule: the compiler itself refuses to let a Domain entity call EF Core. You cannot accidentally break the rules; the project references are the guardrails.

**5. A shared mental map.** "Where does this code go?" has exactly one answer per piece of code. Onboarding a new developer becomes: "Business rule? Domain. Use case? Application slice. SQL? Infrastructure. HTTP? Api." That predictability compounds for years.

**6. UI and delivery flexibility.** The same Application layer can serve an HTTP API today, a gRPC service or a background worker tomorrow. The use cases don't know or care who called them.

### 3.3 What It Costs

**1. More files, more ceremony.** A "simple" create operation becomes a Command record, a Handler, a Validator, a repository interface, a repository implementation, a controller action, and a configuration class. For a 200-line weekend tool, this is absurd — and Chapter 26 will tell you plainly: don't do it there.

**2. A learning curve for your team.** Mapping layers, indirection through MediatR, interfaces with implementations elsewhere — a developer who has only known fat controllers needs weeks to become fluent, which is exactly why this book exists.

**3. Occasional "mapping boilerplate."** Data crosses boundaries as simple shapes (DTOs), so you write explicit mapping code between entities and response records. Chapter 26 shows why that's usually a *feature*, but it is real typing.

**4. Discipline forever.** The architecture only stays clean while every new feature follows the rules. One lazy Monday ("I'll just query the DbContext from the controller, just this once") starts the rot.

### 3.4 When to Use It — and When Not To

| Use Clean Architecture when… | Skip it when… |
|---|---|
| The business logic is real (rules, invariants, workflows) | The app is mostly CRUD over simple tables |
| The application will live and grow for years | It's a prototype, a demo, or a throwaway script |
| A team (not one person) maintains it | A single-day tool where ceremony exceeds value |
| You will unit test the core | Tests aren't planned and the app is small |
| The database/framework may change or the app may grow new front ends | Every requirement is frozen and tiny |

ShopSphere sits squarely in the left column, which is why it is a fair training ground.

### Key Takeaways — Chapter 3

- Gains: framework independence, cheap testing, team-scale folder isolation, compiler-enforced rules, one obvious home for every piece of code, UI/delivery flexibility.
- Costs: more files and ceremony, a real learning curve, explicit mapping code, and permanent discipline.
- It is a tool for long-lived, rule-rich, team-built software — not for every project. Knowing the difference *is* mastery.

---

## Chapter 4 — The Complete Dictionary: Every Word in Clean Architecture Explained

You asked for every word explained. Here it is: the full vocabulary of this book, organized in six families. Read it once now; return to it forever. Every term below is used repeatedly in Parts II–VII, and every one of them gets concrete code in later chapters.

### 4.1 The Words of Architecture Itself

**Architecture** — the decisions about how a system is *structured*: what parts exist, what each part is allowed to know, and which way the dependencies point. Architecture is not "which folder do files go in"; it is "who is allowed to depend on whom."

**Clean Architecture** — a specific architecture (Chapter 2) in which business rules sit at the center with zero outward dependencies, and all technical details depend inward on them, honoring one law — the Dependency Rule.

**Layer** — a grouping of code with one responsibility, which is *only allowed* to depend on inner layers. Our four: Domain, Application, Infrastructure, Presentation.

**Tier** — often confused with layer. A tier is a *physical* boundary: code running on a separate machine (a database server, a web server). Layers are logical, tiers are physical. Our four layers all deploy as one application; only SQL Server is a separate tier.

**Separation of Concerns (SoC)** — the principle that each part of a program should worry about exactly one thing: this class validates, this one decides, this one saves. Concerns that are mixed together (validation + SQL + HTTP in one method) are what make code unchangeable.

**Coupling** — how much one piece of code *needs* another to function. Tight coupling (your business rule calls raw SQL) means changing one breaks the other. Clean Architecture is a campaign for *loose* coupling at the layer boundaries.

**Cohesion** — how strongly the code *inside* one unit belongs together. A `CreateProduct` folder holding that feature's command, handler, and validator is maximally cohesive. High cohesion + low coupling = healthy code.

**Dependency** — when code A references or needs code B, A depends on B. In C#, a project reference (`ProjectReference`) or a `using` of another namespace is a dependency. The *direction* of dependencies is the entire subject of Chapter 5.

**Dependency Inversion Principle (DIP)** — the "D" of SOLID: high-level policy must not depend on low-level detail; *both* depend on abstractions. Concretely: the Application layer declares `IProductRepository` and uses it; the Infrastructure layer implements it. The arrow "Application → EF Core" is inverted into "EF Core → Application's interface." This one principle is the mechanism that makes every circle diagram real.

**Abstraction** — a simplified, minimal description of a capability, from which details are removed: "something that can save products," with no mention of SQL. In C#, abstractions are usually **interfaces**.

**Interface** — a C# contract: a named set of members with no implementation (`public interface IProductRepository { Task AddAsync(Product product, CancellationToken ct); }`). Any class can promise to fulfill it (`class ProductRepository : IProductRepository`). The consumer depends on the contract; the implementation can be swapped freely — which is how the Application layer "uses a database" without knowing EF Core exists.

**Contract** — the informal word for what an interface (or a DTO's shape) promises. "The contract of `IUnitOfWork` is: you can save all pending changes with one call."

**Dependency Injection (DI)** — the technique of giving a class its dependencies from outside instead of creating them inside. Instead of `new ProductRepository()` buried in a handler, the handler's constructor *receives* `IProductRepository`. Whoever builds the handler (the DI container) decides which implementation to hand over.

**IoC Container (Inversion of Control container)** — the framework library that owns object creation: you register implementations against interfaces once (`services.AddScoped<IProductRepository, ProductRepository>()`), and thereafter the container constructs every object graph, injecting dependencies automatically. .NET's built-in container lives in `Microsoft.Extensions.DependencyInjection`.

**Composition Root** — the single place in an application where all DI registrations happen: our `Program.cs` plus the `AddApplication()`/`AddInfrastructure()` extension methods it calls. There is exactly one composition root in a clean solution.

**SOLID** — five class-design principles: **S**ingle Responsibility (one reason to change per class), **O**pen/Closed (extend by adding, not editing), **L**iskov Substitution (an implementation must honor its interface's contract), **I**nterface Segregation (small, focused interfaces beat one giant one), **D**ependency Inversion (defined above). Clean Architecture is SOLID applied at the scale of a whole solution.

**DRY (Don't Repeat Yourself)** — every piece of knowledge should have one authoritative home. Caution: two similar code blocks are not automatically a DRY violation — forcing shared abstraction between two features that only *coincidentally* resemble each other couples them forever (a trap we avoid in vertical slices).

**KISS / YAGNI** — Keep It Simple; You Aren't Gonna Need It. Build for today's requirements. Chapter 26 returns to these as architectural virtues.

### 4.2 The Words of the Domain (the Business)

**Domain** — the subject area your software serves: for ShopSphere, "selling products online." The word comes from DDD.

**Domain Model** — the software representation of the domain: the set of entities, value objects, and rules that encode how the business works. Our `ShopSphere.Domain` project *is* the domain model.

**Ubiquitous Language** — one vocabulary shared by domain experts and the codebase: the expert says "SKU," the class says `Sku`. Mismatches between the two are bug factories.

**Business Logic / Business Rule** — a statement the business cares about and enforces: "stock can never be negative," "a price must be positive and have a currency." Uncle Bob distinguishes **entity business rules** (true regardless of application — enforced inside entities) from **use case business rules** (specific to an application's operation — enforced in handlers). Chapter 8 implements both kinds.

**Invariant** — a condition that must *always* be true for an object to be valid (a `Product` can never have a negative `StockQuantity`). Entities guard their own invariants; that duty is what makes them "smart" rather than bags of properties.

**Entity** — a domain object with an **identity** that persists over time and whose properties may change. Two `Product` objects with the same Id are the *same* product even if a property changed. Identity, not property equality, defines entity equality. Entities live only in the Domain layer.

**Value Object** — an immutable object defined entirely by its values, with no identity: `Money(25.00m, "USD")`. Two `Money` amounts with the same values are interchangeable. Value objects are the best place to encode tiny rules ("currency is always three uppercase letters").

**Aggregate** — a cluster of entities and value objects that change together as one unit, with a single **aggregate root** — the only member outsiders may hold and modify. An `Order` (root) with its `OrderItems` is one aggregate: you never modify an `OrderItem` directly; you go through the `Order`.

**Aggregate Root** — the entry gate of an aggregate (above). `Order` is the root; `OrderItem` is reachable only via `Order.Items`.

**POCO (Plain Old CLR Object)** — a class with no framework baggage: no base class from a library, no required attributes, no knowledge of EF Core. Domain entities in Clean Architecture are POCOs — that is precisely why they can never be "infected" by the ORM.

**Anemic Domain Model** — the famous anti-pattern: entities that are public property bags with no behavior (`public string Name { get; set; }` everywhere), with all logic living elsewhere. It looks convenient and quietly kills the domain's protective power. Chapter 8 shows the alternative.

**Domain Exception** — an exception type thrown by a domain entity when an invariant is violated (`throw new DomainException("Stock cannot be negative.")`), allowing the boundary to translate rule violations into friendly errors (Chapter 23).

### 4.3 The Words of Data and Persistence

**Persistence** — storing data beyond the process lifetime — saving to a database. "Persistence ignorance" means the Domain neither knows nor cares how (or whether) it gets saved.

**ORM (Object-Relational Mapper)** — a library that translates between C# objects and relational tables. You say "save this `Product` object"; the ORM generates the `INSERT` SQL. Our ORM is **Entity Framework Core (EF Core)**.

**EF Core, DbContext, DbSet** — EF Core is the ORM; a `DbContext` is one database session: it tracks loaded objects, detects their changes, and writes them to the database when asked; a `DbSet<T>` is the typed doorway to one table's rows (`DbSet<Product>` ↔ the `Products` table). All of Chapter 16.

**Change Tracking** — the DbContext's core trick: it remembers the original state of every loaded entity, so when you call `SaveChanges`, it diffs current vs. original and emits exactly the right `INSERT`/`UPDATE`/`DELETE`.

**Migration** — a C# class that describes a database schema change (create table, add column). Applying migrations is how an empty SQL Server database becomes the ShopSphere schema — without anyone writing SQL by hand. Chapter 20.

**Seeding** — inserting initial/seed data (a few categories to start with) into the database. Chapter 20.

**Repository (pattern)** — an in-memory-looking collection interface over persistence: `AddAsync`, `GetByIdAsync`, `Remove`. The pattern (from Martin Fowler's *Patterns of Enterprise Application Architecture*, 2002) gives the Application layer a persistence vocabulary that hides *where* the objects live. The interface is declared in Application; the EF Core implementation lives in Infrastructure (Chapter 18).

**Unit of Work (pattern)** — also from Fowler's book: one object that collects all changes made during a business transaction and commits them together, atomically. Our `IUnitOfWork.SaveChangesAsync()` commits every repository's pending changes in a single database transaction — all-or-nothing. Chapter 19 makes "placing an order decreases stock AND inserts the order together or not at all" true.

**Transaction** — the database's all-or-nothing guarantee: every statement in a transaction succeeds together or rolls back together. The Unit of Work pattern exists to give your C# code that guarantee.

**DTO (Data Transfer Object)** — a simple object whose only job is to carry data across a boundary, shaped for the *consumer's* convenience: our API's `ProductResponse` is a DTO. DTOs exist so that entities — with their invariants and private setters — never have to serve two masters (database and HTTP).

**Connection String** — the text configuration that says which server, database, and credentials to use: `Server=localhost,1433;Database=ShopSphereDb;...`. Chapter 6 and 21.

**SQL Server essentials** — a **database** holds **tables**; a table has **columns** (typed, with constraints) and **rows** (one per record); a **primary key (PK)** uniquely identifies a row (our `Id` GUIDs); a **foreign key (FK)** references another table's PK (product → category); an **index** accelerates lookups; a **unique constraint** (we put one on `Sku`) makes the database itself reject duplicates. All created for us by EF Core migrations.

### 4.4 The Words of the Application Layer (CQRS, MediatR, Slices)

**Use Case** — one meaningful operation the application performs: "create a product," "list products," "place an order." The Application layer is a catalog of use cases; each vertical slice is one.

**CQRS (Command Query Responsibility Segregation)** — Greg Young's 2010 pattern: split operations into **Commands** (change state, return little or nothing) and **Queries** (read state, change nothing). Two separate models, each optimized: writes go through rich domain entities; reads skip straight to shaped DTOs. Chapter 10.

**Command** — a message *requesting a state change*: `CreateProductCommand(Name, Sku, Price, ...)`. A command is a named intention, not a method call.

**Query** — a message *requesting data* without side effects: `GetProductsQuery(SearchTerm, Page)`.

**Handler** — the class that knows how to execute one command or query: `CreateProductCommandHandler`. It receives the message, orchestrates the domain and repositories, and returns the result. One handler per message — always.

**Mediator pattern** — a design pattern that decouples callers from doers: objects talk to a **mediator** object instead of to each other. In our stack: controllers talk only to `ISender` ("send this message to whoever handles it"). Chapter 11.

**MediatR** — Jimmy Bogard's library implementing the mediator pattern for .NET: define `IRequest<T>` messages, implement `IRequestHandler<TRequest, TResponse>`, and send through `ISender`. MediatR matches message type to handler type at runtime.

**Pipeline Behavior** — MediatR's middleware: a class (`IPipelineBehavior<TRequest, TResponse>`) that wraps *every* message execution, running code before and after the handler. Our `ValidationBehavior` runs all validators before any handler (Chapter 23) — cross-cutting concerns without touching a single handler.

**Validator** — a FluentValidation class (`AbstractValidator<T>`) declaring the input rules for a message: "`Name` must not be empty and at most 200 characters." The third file of every slice.

**FluentValidation** — the dominant .NET validation library: rule-by-rule, lambda-based, testable, DI-friendly.

**Vertical Slice Architecture** — Chapter 2's final idea: organize the Application layer by feature (one folder per use case) instead of by kind. **Slice** = one feature folder = Command/Query + Handler + Validator.

**Domain Event** — "something meaningful happened in the domain" (`ProductCreated`), raised by entities and dispatched to any number of listeners — a way to grow a system without editing existing classes. Chapter 25.

### 4.5 The Words of the Presentation Layer

**API (Application Programming Interface)** — a program's callable surface. We build an **HTTP API**: clients call URLs, get JSON.

**REST** — a style of HTTP API design: resources at URLs (`/api/products`), standard verbs, status codes, stateless calls.

**HTTP verbs** — the operation words of HTTP: `GET` (read), `POST` (create), `PUT` (replace/update), `PATCH` (partial update), `DELETE` (remove). One verb per intent.

**Controller / Action** — an ASP.NET Core class grouping related endpoints (`ProductsController`), whose methods (**actions**) each handle one route. In Clean Architecture, an action's only job is: bind the request, hand it to MediatR, map the result to a status code. "Thin controllers."

**Routing** — URL → action matching: `[Route("api/[controller]")]` makes `ProductsController` serve `/api/products`.

**Middleware** — components every HTTP request passes through, in order: exception handling → HTTPS redirection → routing → endpoint. `Program.cs` is where we line them up (Chapter 21).

**Status codes** — three-digit HTTP results: `200 OK` (success), `201 Created` (resource created), `204 No Content` (success, empty body), `400 Bad Request` (invalid input), `404 Not Found`, `409 Conflict` (e.g., duplicate SKU), `500 Internal Server Error` (our fault). Our error middleware speaks this language (Chapter 23).

**ProblemDetails** — a standard JSON error format (RFC 9457, successor of RFC 7807: `title`, `status`, `detail`, plus extensions) that machines can parse. Our error responses use it.

**OpenAPI / Swagger** — a machine-readable description of an API's endpoints, auto-generated from the code (`AddOpenApi()` in .NET 10), powering interactive documentation tools (such as Scalar or Swagger UI) where you can click "Try it out."

### 4.6 The Words of the Ecosystem

**Solution (.sln) / Project (.csproj)** — a *project* is one output (a DLL or EXE) with its own code, packages, and references; a *solution* is a container that groups projects so the IDE/CLI can build them together.

**Project reference** — a declared compile-time dependency from one project to another (`Api → Application`). Project references are how we *physically enforce* the Dependency Rule: Domain has none; Application references only Domain; Infrastructure references Application; Api references both.

**NuGet / package** — NuGet is .NET's package manager; a *package* is a versioned library (`MediatR 12.5.0`) added with `dotnet add package`.

**Target Framework (`net10.0`)** — the API set a project compiles against. Every project here targets `net10.0`.

**Unit test / Integration test** — a *unit test* exercises one small piece (an entity, a handler) with all dependencies faked; an *integration test* runs the real stack — API + real database — to prove the pieces work *together* (Chapter 24).

**Fake / Mock** — a hand-written or library-generated stand-in for an interface, used in tests (`FakeProductRepository` keeps entities in a list). Fakes are why interfaces matter: no interface, no swap, no fast tests.

**WebApplicationFactory / Testcontainers** — the two tools of Chapter 24: the former boots the in-memory test server around our Api; the latter spins up a real SQL Server in Docker per test run.

### Key Takeaways — Chapter 4

- **Dependency direction** is the load-bearing idea; *Dependency Inversion* (depend on interfaces *you* declare) is the mechanism that enforces it.
- **Entities** have identity and guard invariants; **value objects** are defined by their values; **aggregates** change as one unit behind a root.
- **Repository** abstracts persistence; **Unit of Work** commits a whole business transaction atomically; **DTOs** cross boundaries.
- **CQRS** splits writes (commands through the domain) from reads (queries to shaped DTOs); **MediatR** delivers messages to handlers behind a thin `ISender` door; **pipeline behaviors** add cross-cutting rules like validation.
- **Thin controllers** translate HTTP to messages and nothing more; **status codes + ProblemDetails** are the API's error language.

---

## Chapter 5 — The Dependency Rule: The Heart of Clean Architecture

### 5.1 The One Law

Everything in this book — four projects, thirty folders, every interface — is an application of a single sentence written by Robert C. Martin in 2012:

> **The Dependency Rule: source code dependencies must point only inward, toward higher-level policies. Nothing in an inner circle can know anything at all about something in an outer circle.**

"Inward" needs a picture. Here is Clean Architecture's original diagram, redrawn with the names we will actually use:

```
                      ┌───────────────────────────────────────────────┐
                      │  FRAMEWORKS & DRIVERS                          │
                      │  → our ShopSphere.Api project                  │
                      │    (web framework, HTTP, JSON, DI wiring)      │
                      │  ┌───────────────────────────────────────────┐ │
                      │  │  INTERFACE ADAPTERS                         │ │
                      │  │  → our ShopSphere.Infrastructure project    │ │
                      │  │    (EF Core, repositories, Unit of Work,    │ │
                      │  │     translations between domain & SQL)      │ │
                      │  │  ┌───────────────────────────────────────┐ │ │
                      │  │  │  USE CASES                             │ │ │
                      │  │  │  → our ShopSphere.Application project  │ │ │
                      │  │  │    (commands, queries, handlers,       │ │ │
                      │  │  │     validators, ports like             │ │ │
                      │  │  │     IProductRepository, IUnitOfWork)   │ │ │
                      │  │  │  ┌─────────────────────────────────┐   │ │ │
                      │  │  │  │  ENTITIES                        │   │ │ │
                      │  │  │  │  → our ShopSphere.Domain project │   │ │ │
                      │  │  │  │    (Product, Category, Order,    │   │ │ │
                      │  │  │  │     Money, invariants)           │   │ │ │
                      │  │  │  │     ── ZERO dependencies ──      │   │ │ │
                      │  │  │  └─────────────────────────────────┘   │ │ │
                      │  │  └───────────────────────────────────────┘ │ │
                      │  └───────────────────────────────────────────┘ │
                      └───────────────────────────────────────────────┘

              ALL DEPENDENCIES POINT INWARD.  NO EXCEPTIONS. EVER.
```

### 5.2 What Each Circle Means — in *Our* Terms

**Entities (Domain).** The most general, most stable business rules: what a product *is*, what makes an order *valid*. This code would be needed by *any* application in this company (web shop, warehouse scanner, reporting job). It references **nothing** — no packages, no projects, no frameworks. It is pure C#.

**Use Cases (Application).** The operations of *this* application: "create a product," "place an order," "list products page 2." A use case orchestrates entities and declares what it *needs* from the outside world as interfaces (`IProductRepository`, `IUnitOfWork`) — its **ports**. It knows no EF Core, no HTTP, no SQL Server.

**Interface Adapters (Infrastructure).** The translators. This circle converts the use cases' interface wishes into concrete technology: EF Core queries, SQL Server connections, change tracking. It *implements* the ports declared in Application. Notice the inversion made physical: the code that *mentions* SQL Server depends on the code that doesn't.

**Frameworks & Drivers (Api).** The outermost shell: ASP.NET Core, controllers, routing, JSON serialization, `Program.cs`, `appsettings.json`. It is deliberately thin — a set of adapters converting HTTP requests into messages handed to the use-case circle via MediatR.

### 5.3 How Dependencies "Cross" Boundaries

Here is the subtlety that confuses everyone at first. At **runtime**, control flows outward: a handler (inner) *calls* a repository (outer) — inner code invoking outer code! How is that not an outward dependency?

Because the **source-code dependency** points inward even while control flows outward. The handler references only `IProductRepository` — a type declared *inside* the Application project. The concrete `ProductRepository` (outer) references the interface (inner). This is the **Dependency Inversion Principle** operating at project scale:

```
Runtime call flow:        Handler ──────────────────────▶ ProductRepository ──▶ SQL Server
                              (control flows OUTWARD)

Source dependencies:      Handler ──▶ IProductRepository ◀── ProductRepository
                              (declared in Application)      (declared in Infrastructure)
                              (source depends INWARD only)
```

The interface is the **boundary**. Everything inside it is your application. Everything outside it is a pluggable detail.

### 5.4 What Crosses the Boundary (and What Must Not)

Only **data** crosses — and it crosses in simple, dependency-free shapes:

- **Inward to handlers:** commands and queries (plain records), and domain entities returned by repositories (Domain is innermost, so Infrastructure *may* reference Domain types — inward direction, legal).
- **Outward from the API:** DTOs/response records (plain data, no behavior) — never domain entities leaking raw to the internet, never `IQueryable` leaking out of Infrastructure (Chapter 26 explains why `IQueryable` leakage is a boundary crime).

**What must never cross inward:** `DbContext`, connection strings, HTTP types (`HttpContext`, `IActionResult`), EF attributes, JSON attributes. If you ever write `using Microsoft.EntityFrameworkCore;` anywhere except Infrastructure or Api, a Clean Architecture alarm should sound.

### 5.5 The Compiler as Your Architect

The beautiful property of Clean Architecture in .NET is that the rule is **mechanically enforced by project references**:

- `ShopSphere.Domain` — references nothing. Try to use EF Core in an entity: *compile error, there is no such package.*
- `ShopSphere.Application` — references only `Domain`. Try to touch `DbContext`: *compile error.*
- `ShopSphere.Infrastructure` — references `Application` (and thereby `Domain`). This is where SQL lives.
- `ShopSphere.Api` — references `Application` and `Infrastructure` (the shell must wire implementations to interfaces).

No code review, no discipline, no vigilance required: the dependency rule is a build outcome. This is why we use four **projects** rather than four folders — folders can't refuse you.

### 5.6 The Screaming Test

Uncle Bob offers a diagnostic: *when you look at the top-level structure of your solution, does it "scream" its architecture or its frameworks?* A solution whose projects are named `ShopSphere.Web`, `ShopSphere.Data`, `ShopSphere.Business` screams *technical role*. A solution named `ShopSphere.Domain` screams *"the business is the point."* Ours will scream properly.

### Key Takeaways — Chapter 5

- One law: source dependencies point **inward only**; inner circles know nothing of outer ones.
- Circles → our projects: Entities = `Domain`, Use Cases = `Application`, Interface Adapters = `Infrastructure`, Frameworks & Drivers = `Api`.
- Control can flow outward at runtime because interfaces declared *inward* are implemented *outward* — that's Dependency Inversion in action.
- Only plain data crosses boundaries — DTOs in, entities/DTOs out — never frameworks, never `IQueryable`.
- Project references make the rule compiler-enforced, not willpower-enforced.

---

# PART II — THE WORKSPACE

> Theory is loaded. Part II turns it into folders on your disk: we install and verify every tool, then create the four-project solution skeleton that all remaining chapters build inside.

---

## Chapter 6 — Environment Setup: .NET 10, SQL Server, and Your Toolbox

### 6.1 What You Will Install

Three things: the **.NET 10 SDK** (compiles and runs everything), a **SQL Server 2022** instance (our database), and the **dotnet-ef tool** (runs EF Core migrations from the command line). Plus any editor you like.

### 6.2 The .NET 10 SDK

**What the SDK is:** the compiler (`dotnet build`), runner (`dotnet run`), project scaffolder (`dotnet new`), and package manager (`dotnet add package`) in one install. **.NET 10 is the Long-Term Support (LTS) release** published in November 2025 — supported with patches until November 2028, which is exactly why an architecture book builds on it.

Install it from the official source:

- **Windows:** download "SDK 10.0.x" from <https://dotnet.microsoft.com/download/dotnet/10.0> (the `x86`/`x64`/`arm64` installer matching your machine), run it, done.
- **macOS / Linux:** same page, or Homebrew (`brew install --cask dotnet-sdk`) / your package manager.

**Verify** — open a terminal (PowerShell, bash, whatever) and run:

```bash
dotnet --version
```

Expected output starts with `10.` (for example `10.0.1xx`). If you see an older number, an older SDK is first on your PATH — fix or reinstall. For the full picture:

```bash
dotnet --info
```

This prints every SDK installed, the runtime versions, and your OS. Skim it once; you will rarely need it again, but when something "can't find a framework," this is the diagnostic.

### 6.3 Your Editor

Any of these; the code in this book depends on none of them:

- **Visual Studio 2022/2026 (Windows)** — the full IDE; pick the ".NET desktop development" + "ASP.NET and web development" workloads.
- **JetBrains Rider (any OS)** — excellent for solution-level navigation.
- **VS Code (any OS) + the "C# Dev Kit" extension** — lightweight; the book's terminal-first workflow fits it perfectly.

This book uses **the `dotnet` command line for every step** so it works identically everywhere.

### 6.4 SQL Server 2022 — Pick Your Path

We need one reachable SQL Server. Three good options; pick one:

**Option A — Docker (recommended; identical on every OS).** Install Docker Desktop (or Docker Engine), then run:

```bash
docker run \
  -e "ACCEPT_EULA=Y" \
  -e "MSSQL_SA_PASSWORD=YourStrong@Passw0rd" \
  -p 1433:1433 \
  --name shopsphere-sql \
  -d mcr.microsoft.com/mssql/server:2022-latest
```

Line by line:

- `docker run` — create and start a new container.
- `-e "ACCEPT_EULA=Y"` — Microsoft requires an explicit yes to the End-User License Agreement; without it the container refuses to start.
- `-e "MSSQL_SA_PASSWORD=YourStrong@Passw0rd"` — sets the password of the `sa` (system administrator) account. SQL Server enforces complexity: at least 8 characters mixing uppercase, lowercase, digits, and symbols. `YourStrong@Passw0rd` qualifies. **Change it in real life; keep it for following along.**
- `-p 1433:1433` — publishes the container's port 1433 (SQL Server's default) to your machine's port 1433, so `localhost,1433` reaches the database.
- `--name shopsphere-sql` — a friendly name for `docker start/stop/logs`.
- `-d` — detached: run in the background.
- `mcr.microsoft.com/mssql/server:2022-latest` — Microsoft's official SQL Server 2022 image (Ubuntu-based).

Verify it is alive:

```bash
docker ps --filter name=shopsphere-sql
docker logs shopsphere-sql --tail 5
```

When the log ends with *"SQL Server is now ready for client connections"*, you have a database server. (Stop/start later with `docker stop shopsphere-sql` / `docker start shopsphere-sql` — data persists in the container until you delete it.)

**Option B — Windows with LocalDB or full SQL Server.** Visual Studio typically installs **SQL Server Express LocalDB** — a zero-config, developer-only SQL Server. Your connection string in later chapters becomes `Server=(localdb)\\MSSQLLocalDB;Database=ShopSphereDb;Trusted_Connection=True;`. Or install SQL Server 2022 Developer Edition from Microsoft and use `Server=localhost;Database=ShopSphereDb;Trusted_Connection=True;TrustServerCertificate=True;`.

**Option C — Azure SQL Database.** A cloud SQL Server — create one in the free tier and copy its connection string. Fine for everything in this book, overkill for learning alone.

### 6.5 A Window Into the Database (Optional but Nice)

You will *not* need a database GUI in this book — EF Core migrations do the schema work, and we verify data through the API and tests. But if you like to *see* tables, install one of: **Azure Data Studio** (free, cross-platform), **SQL Server Management Studio (SSMS)** on Windows, or the **SQL Server extension for VS Code**. Connect to `localhost,1433` with user `sa` and your password.

### 6.6 The EF Core Command-Line Tool

Migrations (Chapter 20) are driven by `dotnet-ef`, a global .NET tool. Install it pinned to the 10.x line (so it matches EF Core 10):

```bash
dotnet tool install --global dotnet-ef --version 10.0.0
```

- `dotnet tool install --global` — installs a CLI extension machine-wide (into `~/.dotnet/tools`, already on your PATH).
- `--version 10.0.0` — installs the 10.0.0 release specifically; omitting it grabs the newest, which may target a newer .NET than yours.

Verify:

```bash
dotnet ef --version
```

Expected: `Entity Framework Core .NET Command-line Tools version 10.0.0` (or later 10.x).

### 6.7 Create Your Workspace Folder

Choose a home for the code and create it:

```bash
mkdir shopsphere && cd shopsphere
```

Every command in the rest of the book assumes you are standing in this folder (or its subfolders, as instructed). Your toolbox is now complete: SDK ✔, database ✔, migration tool ✔, workspace ✔.

### Key Takeaways — Chapter 6

- `.NET 10` is the current LTS — verify with `dotnet --version` showing `10.`.
- SQL Server via one `docker run` command — remember the port (`1433`), the user (`sa`), and the password (yours).
- `dotnet-ef` 10.x is the migrations engine; install it globally once.
- Everything in this book is doable from the plain `dotnet` CLI in any editor on any OS.

---

## Chapter 7 — Creating the Solution: Four Projects and Their Contracts

### 7.1 What We Are About to Build

By the end of this chapter your `shopsphere/` folder will contain a solution with four projects, wired with exactly the project references that Chapter 5 promised, plus every NuGet package the whole book will need — installed once, in one place, so later chapters are pure code. We will finish by building and running the skeleton to prove the pipeline is alive.

The commands below are plain `dotnet` CLI — run them from your `shopsphere/` folder. (Windows PowerShell users: replace the trailing `\` line-continuations with backticks or write commands on one line.)

### 7.2 Step 1 — The Solution and the Four Projects

```bash
dotnet new sln -n ShopSphere
```

- `dotnet new sln` — creates a solution file. `-n ShopSphere` names it `ShopSphere.sln`. A solution holds no code; it is the binder that lets one command build all projects together.

```bash
dotnet new classlib -n ShopSphere.Domain -o src/ShopSphere.Domain
dotnet new classlib -n ShopSphere.Application -o src/ShopSphere.Application
dotnet new classlib -n ShopSphere.Infrastructure -o src/ShopSphere.Infrastructure
dotnet new web -n ShopSphere.Api -o src/ShopSphere.Api
```

Line by line:

- `dotnet new classlib` — creates a **class library** project: a project that compiles to a DLL, not a runnable program. Exactly right for Domain, Application, and Infrastructure.
- `-n ShopSphere.Domain` — the project name (which names the `.csproj` file and the default root namespace).
- `-o src/ShopSphere.Domain` — the output folder. The `src/` convention separates source from the `tests/` folder we add in Chapter 24.
- `dotnet new web` — creates a **minimal ASP.NET Core web project**: runnable, with a `Program.cs`, but almost empty — no controllers, no Swagger, no demo code. Perfect: we want to write every line ourselves.

Each `classlib` template comes with a junk `Class1.cs`. Delete all three:

```bash
rm src/ShopSphere.Domain/Class1.cs src/ShopSphere.Application/Class1.cs src/ShopSphere.Infrastructure/Class1.cs
```

### 7.3 Step 2 — Register the Projects in the Solution

```bash
dotnet sln add src/ShopSphere.Domain/ShopSphere.Domain.csproj
dotnet sln add src/ShopSphere.Application/ShopSphere.Application.csproj
dotnet sln add src/ShopSphere.Infrastructure/ShopSphere.Infrastructure.csproj
dotnet sln add src/ShopSphere.Api/ShopSphere.Api.csproj
```

`dotnet sln add` records a project path inside `ShopSphere.sln`. From now on, `dotnet build` at the root builds all four, in dependency order.

### 7.4 Step 3 — The References (This Is the Architecture!)

Now the most important commands in the entire book — the four arrows of Chapter 5's diagram, made physical:

```bash
dotnet add src/ShopSphere.Application/ShopSphere.Application.csproj reference src/ShopSphere.Domain/ShopSphere.Domain.csproj

dotnet add src/ShopSphere.Infrastructure/ShopSphere.Infrastructure.csproj reference src/ShopSphere.Application/ShopSphere.Application.csproj

dotnet add src/ShopSphere.Api/ShopSphere.Api.csproj reference src/ShopSphere.Application/ShopSphere.Application.csproj
dotnet add src/ShopSphere.Api/ShopSphere.Api.csproj reference src/ShopSphere.Infrastructure/ShopSphere.Infrastructure.csproj
```

Read the pattern: **to the left of `reference` is the depender; to the right, the depended-upon.**

- Application → Domain ✔ (use cases may use entities)
- Infrastructure → Application ✔ (EF Core implements the ports)
- Api → Application ✔ (controllers send messages)
- Api → Infrastructure ✔ (the composition root must wire implementations in)
- Domain → *(nothing)* ✔ — the center holds.

Notice what is deliberately absent: Infrastructure does **not** reference Domain directly (it reaches it *through* Application, which is fine and automatic), and Application does **not** reference Infrastructure — which is the whole point: you cannot "accidentally" call EF Core from a handler because the compiler will not find it.

### 7.5 Step 4 — Reading a `.csproj` (Every Line)

Open `src/ShopSphere.Domain/ShopSphere.Domain.csproj`. The classlib template gives you exactly this:

```xml
<!-- File: src/ShopSphere.Domain/ShopSphere.Domain.csproj -->
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>

</Project>
```

Word by word, line by line:

- `<Project Sdk="Microsoft.NET.Sdk">` — this is an **SDK-style project**: Microsoft's build SDK ("Microsoft.NET.Sdk" for libraries) brings in all the default compile rules, so the file can stay tiny.
- `<TargetFramework>net10.0</TargetFramework>` — compiles against the .NET 10 API surface. All four projects will share this line.
- `<ImplicitUsings>enable</ImplicitUsings>` — the compiler silently adds common `using` directives (`System`, `System.Collections.Generic`, `System.Linq`, `System.Threading.Tasks`, ...) to every file. That is why our code files can be short.
- `<Nullable>enable</Nullable>` — turns on nullable reference types: the compiler warns you when a `string` might be `null` and makes you say so (`string?`). Our entities and DTOs will be honest about nullability thanks to this line.

When we added references and (soon) packages, `<ItemGroup>` sections appeared in these files. That is the whole format: properties on top, items (references, packages, source files) below.

### 7.6 Step 5 — The Dependency Budget: Every Package, Once

Now we spend our entire NuGet budget in one place. This table is the *complete* list of packages the finished application needs — and each one goes in exactly one project:

| Project | Package | Version | Why it lives THERE |
|---|---|---|---|
| ShopSphere.Domain | — *(none, ever)* | — | The innermost circle must stay pure |
| ShopSphere.Application | `MediatR` | 12.5.0 | Handlers use `IRequest`, `ISender`, `IPipelineBehavior` |
| ShopSphere.Application | `FluentValidation` | 12.2.0 | Validators derive from `AbstractValidator<T>` |
| ShopSphere.Application | `FluentValidation.DependencyInjectionExtensions` | 12.2.0 | Registers all validators with the DI container |
| ShopSphere.Infrastructure | `Microsoft.EntityFrameworkCore.SqlServer` | 10.0.0 | The SQL Server provider: `DbContext`, `UseSqlServer` |
| ShopSphere.Api | `Microsoft.EntityFrameworkCore.Design` | 10.0.0 | Lets `dotnet ef` design-time tools see our DbContext |
| ShopSphere.Api | `Microsoft.AspNetCore.OpenApi` | 10.0.0 | `AddOpenApi()`/`MapOpenApi()` endpoint documentation |

Run them (the CLI short form works from inside a project folder, which we'll use from here on):

```bash
cd src/ShopSphere.Application
dotnet add package MediatR --version 12.5.0
dotnet add package FluentValidation --version 12.2.0
dotnet add package FluentValidation.DependencyInjectionExtensions --version 12.2.0

cd ../ShopSphere.Infrastructure
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 10.0.0

cd ../ShopSphere.Api
dotnet add package Microsoft.EntityFrameworkCore.Design --version 10.0.0
dotnet add package Microsoft.AspNetCore.OpenApi --version 10.0.0

cd ../..
```

- `dotnet add package NAME --version X` — adds (or updates) a `<PackageReference>` in that project's `.csproj` and **restores** it (downloads it to your global NuGet cache and resolves its own dependencies).
- Notice the version of `MediatR`: we deliberately pin **12.5.0**, the final Apache-2.0-licensed release (see Chapter 1's licensing note and Chapter 11's full story). Its API is identical for everything in this book.

Two details worth burning into memory:

1. **FluentValidation.DependencyInjectionExtensions is a separate package** from FluentValidation — the base package has the validation engine; the DI package adds `AddValidatorsFromAssembly...()` which we meet in Chapter 21.
2. **`Microsoft.EntityFrameworkCore.Design` goes in Api** because EF's migration tools require it in the *startup* project — the project they launch to discover how the app is composed. It is a build-time-only dependency (`PrivateAssets` — the template sets this automatically when it detects it; otherwise add `Version` with `--prerelease` off and keep reading, Chapter 20 covers the details).

### 7.7 Step 6 — The Skeleton's Heartbeat

`dotnet new web` gave Api a tiny `Program.cs`. Open it — the modern template is just:

```csharp
// File: src/ShopSphere.Api/Program.cs (template version — we will replace it in Chapter 21)
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");

app.Run();
```

Four lines, four ideas: `CreateBuilder` assembles configuration + services; `Build()` produces the running app; `MapGet("/", ...)` registers that the URL `/` answers `GET`; `Run()` starts listening. We will rewrite this file completely in Chapter 21 — but first, prove the skeleton is alive:

```bash
dotnet run --project src/ShopSphere.Api
```

- `dotnet run --project PATH` — builds (if needed) then executes that project.

Watch the console for `Now listening on: http://localhost:5xxx`, then open that URL (or `curl` it) and greet your architecture. Stop it with `Ctrl+C`. The final `cd ../..` earlier returned you to the solution root, where every future `dotnet build` will succeed with zero warnings.

### 7.8 Where We Go Next (and Why Nothing Runs Yet)

The skeleton has projects, references, and packages — but no business code. From here we build **bottom-up, one layer at a time**: the Domain (Part III) compiles alone; the Application (Part IV) compiles on top of it; Infrastructure (Part V) implements the ports; and only in Part VI does the full application light up end-to-end. Construction looks like a building site until the roof goes on — that is normal and worth it.

### Key Takeaways — Chapter 7

- One solution, four projects: three class libraries (`Domain`, `Application`, `Infrastructure`) and one web app (`Api`), all under `src/`.
- Project references encode the Dependency Rule: Application→Domain, Infrastructure→Application, Api→both; Domain references nothing.
- The whole dependency budget is three packages in Application, one in Infrastructure, two in Api — and **zero** in Domain, forever.
- `dotnet run` proved the skeleton boots; every later chapter adds code to one specific circle.

---

# PART III — THE DOMAIN LAYER

> The innermost circle at last. Part III builds the heart of ShopSphere: pure C# classes that encode what "product", "category", and "order" mean, what can never be true about them, and what they can *do* — with zero references to databases, web servers, or any library at all.

---

## Chapter 8 — The Domain Layer: Where the Business Lives

### 8.1 The Domain Layer in One Rule

The Domain layer is the **Domain project, and nothing else**. Its rule is radical simplicity:

> **The Domain project references nothing. Nothing framework-shaped ever appears in it. It is plain C#, understandable by any C# developer with no other knowledge.**

If Chapter 5's diagram is the skeleton, the Domain is the heart: every other layer exists to feed it input and carry its decisions outward. In our solution it contains: entities (`Product`, `Category`, `Order`, `OrderItem`), the value object (`Money`), one enum (`OrderStatus`), and exactly two tiny support types (`BaseEntity`, `DomainException`). That's all. If you ever feel the urge to add a service, a repository implementation, or — heaven forbid — a NuGet package here, stop: that code belongs in an outer circle.

### 8.2 Entity Anatomy: Identity, Invariants, Behavior

Chapter 4 defined an entity as "an object with identity that persists over time." Now we make it concrete. A well-built domain entity has three organs:

**1. Identity.** Every entity carries an `Id` that never changes and is unique across all instances of its kind. Two `Product` objects are the same product if and only if their ids match — even if every other property differs. Identity is what the database primary key will store (Chapter 17).

**2. Invariants.** The conditions that must *always* hold (Chapter 4): stock never negative, price always positive, name never empty. Crucially, **the entity itself enforces them** — not a validator in another layer, not the UI, not the database. If the only way to set stock is through `DecreaseStock(...)`, and `DecreaseStock` refuses to go below zero, then stock can *never* be negative, no matter which caller, which layer, which year of the project's life tries it. That certainty is the entire value of a rich domain.

**3. Behavior.** The operations the business recognizes, expressed as methods with business names: `Product.DecreaseStock(...)`, `Order.Place(...)`, `Order.MarkPaid()`. When behavior lives on the entity, the entity *is* the rule — there is one place to look, one place to change, one place to test.

### 8.3 The Anemic Alternative (and Why It's a Trap)

The opposite style — the **anemic domain model** — looks like this:

```csharp
// ❌ The anemic anti-pattern — do NOT write this in this book
public class Product
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public int StockQuantity { get; set; }   // anyone, anywhere, can set -5
}
```

Every property is publicly writable, so *every* caller must remember every rule ("check stock before decrementing..."). One forgotten check in one controller in year three, and your warehouse ships products you don't own. The rules, smeared across the app, are unwritten laws — and unwritten laws get broken. Our entities will instead be **closed boxes with deliberate openings**: you cannot do something invalid because there is no door for it.

### 8.4 The Encapsulation Toolkit

To build closed boxes, C# gives us five tools, all of which Chapter 9 uses:

1. **`private set`** — the property's value can be read by anyone but changed only from inside the class.
2. **Private constructors** — nobody outside (including our own Application layer) can `new Product(...)` into an invalid state; creation flows through factories.
3. **Factory methods** (`static Product Create(...)`) — the *only* public door to birth an entity; they validate before constructing, so an invalid instance never exists, even for a nanosecond.
4. **Read-only collections** — an aggregate exposes `IReadOnlyCollection<OrderItem>` backed by a private `List<OrderItem>`, so outsiders cannot `.Add()` to it.
5. **`DomainException`** — the entity's way of shouting "rule violated!" upward, to be translated by the outer circles into a friendly error (Chapter 23).

### 8.5 Value Objects: Small, Strict, Immutable

A **value object** (Chapter 4) is defined by its values, has no identity, and never changes after creation. `Money` is the canonical example: `Money(25.00m, "USD")` is *equal* to any other `Money(25.00m, "USD")`, and a price of "negative five dollars" should simply never be constructible. C# `record` types give us value-based equality for free, making them the perfect vehicle. Value objects also carry their own rules — "currency is always exactly three letters" — so those rules live in exactly one place too.

### 8.6 Aggregates: Clusters That Change Together

An **aggregate** is a cluster of objects that the business treats as one consistent unit, with one public gate: the **aggregate root**. ShopSphere's aggregate is `Order` + its `OrderItems`:

- You may hold a reference to an `Order` and read `order.Items`.
- You may never hold an `OrderItem` and modify it directly — there is no public door.
- To add an item you call `Order.Place(...)` (at creation), and the *Order* decides what a valid item is.

This is not bureaucracy; it is consistency. An order's items and the order's totals must change *together*, under one set of rules, committed in one transaction (Chapter 19 will make "together" literal).

### 8.7 Two Kinds of Validation (This Confuses Everyone)

Read carefully, because this distinction shapes the whole solution:

| | **Input validation** (Application layer) | **Domain invariants** (Domain layer) |
|---|---|---|
| Question it answers | "Is this request well-formed?" | "Can this object exist/behave this way?" |
| Example | "`Name` missing ⇒ 400 Bad Request" | "Stock cannot go negative, ever" |
| Enforced by | FluentValidation Validators (Ch. 13) | Entity factories & methods (Ch. 9) |
| Failure result | HTTP 400 for the client | `DomainException` ⇒ 400 for the client (Ch. 23) |

A request can pass input validation and still violate a domain rule (`DecreaseStock(5)` on 3 items: input was well-formed; the rule is contextual). So we validate *twice, deliberately*: cheap, declarative input checks at the boundary; deep, contextual rules in the entities. This is not duplication — it is two different questions.

### 8.8 What Is Forbidden in the Domain Project

Print this list and tape it to your monitor:

- ❌ Any `using Microsoft.EntityFrameworkCore...` (or any other package's namespace)
- ❌ Attributes like `[Required]`, `[Table]`, `[JsonPropertyName]` — framework hints on business classes
- ❌ Repositories, DbContexts, HTTP calls, file system, `DateTime.Now` (we use `DateTime.UtcNow`; Chapter 19 discusses why "now" is set by infrastructure)
- ❌ Static mutable state, singletons, logging frameworks
- ✅ Allowed: plain C#, `System.*` namespaces, our own `DomainException`

The compiler enforces most of this for free — the Domain project has no packages to `using` — which is precisely why we made it a separate project in Chapter 7.

### Key Takeaways — Chapter 8

- Domain = entities + value objects + enum + `BaseEntity` + `DomainException`; references nothing, forever.
- Entities = identity + self-enforced invariants + behavior; anemic property bags are the anti-pattern.
- Encapsulation toolkit: `private set`, private constructors, factory methods, read-only collections, `DomainException`.
- Value objects are immutable and value-equal (`record`); aggregates change as one unit behind their root (`Order` → `OrderItems`).
- Validate twice on purpose: input validation at the boundary (Validators), invariants in the entities — different questions, different layers.

---

## Chapter 9 — Building the ShopSphere Domain Model, Line by Line

Every file in this chapter goes inside `src/ShopSphere.Domain/`. After the last file we build the solution and the heart of ShopSphere will be beating. Create each file exactly where its header comment says.

### 9.1 The File Map (What We Are About to Type)

```
src/ShopSphere.Domain/
├── Common/
│   ├── BaseEntity.cs          the shared identity + audit columns
│   └── DomainException.cs     the entity's alarm bell
├── Products/
│   ├── Money.cs               value object: amount + currency
│   ├── Category.cs            entity: a product family
│   └── Product.cs             entity: the star of the show
└── Orders/
    ├── OrderStatus.cs         enum: where an order is in its life
    ├── OrderItem.cs           the lines of an order (inside the aggregate)
    └── Order.cs               the aggregate root
```

We create the folders with the files. From the solution root:

```bash
mkdir -p src/ShopSphere.Domain/Common src/ShopSphere.Domain/Products src/ShopSphere.Domain/Orders
```

### 9.2 `DomainException` — The Alarm Bell

```csharp
// File: src/ShopSphere.Domain/Common/DomainException.cs
namespace ShopSphere.Domain.Common;

public sealed class DomainException(string message) : Exception(message);
```

Three lines, three words to learn:

- **`namespace ShopSphere.Domain.Common;`** — the file-scoped namespace (the trailing `;` form): everything declared here lives under `ShopSphere.Domain.Common`, so consumers write `using ShopSphere.Domain.Common;`. The `Common` sub-namespace marks "shared across the whole domain" as opposed to `Products` or `Orders`.
- **`sealed`** — no class may inherit from `DomainException`. Inheritance of exception types invites a zoo of hyper-specific exceptions that callers can't keep up with; one clear type is enough.
- **`(string message) : Exception(message)`** — a **C# primary constructor**: the parentheses after the class name declare a constructor parameter, and `: Exception(message)` chains it to `Exception`'s constructor. This single line replaces the traditional five-line constructor boilerplate. It is exactly equivalent to the old `public DomainException(string message) : base(message) { }`.

Why a *domain-specific* exception at all? Because Chapter 23's error middleware needs to answer "was this failure a broken business rule (client's fault, HTTP 400) or a real error (our fault, HTTP 500)?" A distinct type makes that distinction machine-readable.

### 9.3 `BaseEntity` — Shared Identity and Audit Columns

```csharp
// File: src/ShopSphere.Domain/Common/BaseEntity.cs
namespace ShopSphere.Domain.Common;

public abstract class BaseEntity
{
    public Guid Id { get; protected set; } = Guid.CreateVersion7();

    public DateTime CreatedAtUtc { get; protected set; }

    public DateTime? UpdatedAtUtc { get; protected set; }
}
```

Line by line:

- **`public abstract class BaseEntity`** — `abstract` means "never instantiated directly": only real entities (`Product`, `Category`, `Order`) extend it. It exists to say *every entity has an identity and audit columns* once, instead of three times.
- **`public Guid Id { get; protected set; } = Guid.CreateVersion7();`** — unpacking all five pieces:
  - `Guid` — a 128-bit globally unique identifier, e.g. `3f2b8a4e-...`. Unlike auto-increment integers, GUIDs are unique across every table, server, and machine on earth, which means we can generate the id *in our domain* — no round-trip to the database to learn "who am I?" 
  - `{ get; protected set; }` — public to *read*, but only the entity itself (or a subclass, which is what `protected` means) may *write* it. The id is assigned once at birth and never edited.
  - `= Guid.CreateVersion7();` — the **field initializer**: runs at construction. `CreateVersion7()` (added to .NET in version 9, standard equipment in 10) generates a **time-ordered GUID (UUIDv7)**: it embeds the creation timestamp, so ids sort chronologically — database indexes stay compact and page-friendly, unlike fully random v4 GUIDs which scatter. This one initializer line is a modern .NET best practice worth knowing.
- **`public DateTime CreatedAtUtc { get; protected set; }`** — when the row was inserted. **UTC, always**: local times are ambiguous across servers and time zones; UTC is one truth. It stays empty here — Chapter 19's `SaveChanges` override will stamp it, because "now" is an infrastructure concern (clocks are devices; the domain stays pure).
- **`public DateTime? UpdatedAtUtc { get; protected set; }`** — `DateTime?` (nullable) since a never-edited row has no update time yet.

### 9.4 `Money` — Our Value Object

```csharp
// File: src/ShopSphere.Domain/Products/Money.cs
namespace ShopSphere.Domain.Products;

public sealed record Money(decimal Amount, string Currency)
{
    public static Money Create(decimal amount, string currency)
    {
        if (amount <= 0)
        {
            throw new DomainException("Price must be greater than zero.");
        }

        if (string.IsNullOrWhiteSpace(currency) || currency.Length != 3)
        {
            throw new DomainException("Currency must be a 3-letter ISO 4217 code (e.g. USD, EGP, EUR).");
        }

        return new Money(amount, currency.ToUpperInvariant());
    }
}
```

Line by line:

- **`public sealed record Money(decimal Amount, string Currency)`** — a **positional record**. The compiler generates: two public init-only properties (`Amount`, `Currency`), a constructor, **value equality** (`new Money(5, "USD") == new Money(5, "USD")` is `true` — impossible with classes), and a nice `ToString()`. Value equality is the defining trait of a value object (Chapter 4), which is why we choose `record` and not `class`.
- **Why a private-ish constructor is NOT needed here but a factory is** — the positional constructor itself is public, so nothing *technically* stops `new Money(-5, "USD")`. That is why `Create` exists as the door we actually use in handlers and entities: it refuses invalid money. (`decimal` is C#'s exact base-10 type — the correct choice for money; `double`/`float` are binary approximations and accumulate rounding errors.)
- **`string.IsNullOrWhiteSpace(currency) || currency.Length != 3`** — rejects `null`, `""`, `"  "`, and anything not exactly 3 characters, enforcing the ISO 4217 shape (USD, EGP, EUR, SAR...).
- **`currency.ToUpperInvariant()`** — normalizes `"usd"` → `"USD"`. `ToUpperInvariant()` uses the culture-neutral rules, so a Turkish locale's dotted-I quirks can never corrupt "USD".
- **ISO 4217** — the international standard for currency codes, cited so you know "3 letters" is a real-world rule, not our invention.

### 9.5 `Category` — A Gentle First Entity

```csharp
// File: src/ShopSphere.Domain/Products/Category.cs
using ShopSphere.Domain.Common;

namespace ShopSphere.Domain.Products;

public sealed class Category : BaseEntity
{
    private Category() { }

    private Category(string name, string? description)
    {
        Name = name;
        Description = description;
    }

    public string Name { get; private set; } = null!;

    public string? Description { get; private set; }

    public static Category Create(string name, string? description)
    {
        ValidateName(name);
        return new Category(name, description);
    }

    public void Rename(string name)
    {
        ValidateName(name);
        Name = name;
    }

    private static void ValidateName(string name)
    {
        if (string.IsNullOrWhiteSpace(name))
        {
            throw new DomainException("Category name is required.");
        }

        if (name.Length > 100)
        {
            throw new DomainException("Category name cannot exceed 100 characters.");
        }
    }
}
```

Line by line, with the new concepts bolded in the prose:

- **`public sealed class Category : BaseEntity`** — a `class`, not a record: entities have *identity* (inherited `Id`), so value-equality is wrong for them. `sealed` again prevents accidental inheritance trees.
- **`private Category() { }`** — the **parameterless private constructor, and it is not decoration**: EF Core needs a way to build `Category` objects when it reads rows from SQL Server (Chapter 16). With this door, *only* EF (and this class itself) can materialize an empty shell and then fill it — nobody in the Application layer can bypass `Create` with `new Category()`.
- **`private Category(string name, string? description)`** — the **real constructor, also private**, callable only from inside the class (i.e., from `Create`), which has already validated. Invalid categories are not just discouraged — they are unconstructible.
- **`public string Name { get; private set; } = null!;`** — read publicly, written privately, and **`= null!`** needs explaining honestly: with `Nullable` enabled (Chapter 7), a non-nullable `string` property that nobody sets in the visible constructor would normally raise warning CS8618 ("possible null"). But EF Core *does* set it, from database values, via the private constructor path the compiler cannot see. `null!` is the nullable-reference-type escape hatch that says "trust me, someone will populate this." In our code, that someone is EF at materialization time or `Create` at birth.
- **`public string? Description { get; private set; }`** — `string?`: description is genuinely optional; the type system now *documents* that.
- **`public static Category Create(string name, string? description)`** — the **factory method**: validate first (`ValidateName`), construct last. A `Category` that exists is a `Category` that is valid — there was never a moment in between.
- **`public void Rename(string name)`** — **behavior with a business name**: "rename a category," not "set the Name property." Same validation, because the invariant ("names are 1–100 non-blank characters") applies at every moment of life, not just birth.
- **`private static void ValidateName(...)`** — the rules, written once, called by both doors (birth and rename). One rule, one home — DDD's real-world payoff.

### 9.6 `Product` — The Entity That Earns Its Keep

```csharp
// File: src/ShopSphere.Domain/Products/Product.cs
using ShopSphere.Domain.Common;

namespace ShopSphere.Domain.Products;

public sealed class Product : BaseEntity
{
    private Product() { }

    private Product(string name, string sku, Money price, int stockQuantity, Category? category)
    {
        Name = name;
        Sku = sku;
        Price = price;
        StockQuantity = stockQuantity;
        Category = category;
    }

    public string Name { get; private set; } = null!;
    public string Sku { get; private set; } = null!;
    public Money Price { get; private set; } = null!;
    public int StockQuantity { get; private set; }
    public Guid? CategoryId { get; private set; }
    public Category? Category { get; private set; }

    public static Product Create(string name, string sku, Money price, int stockQuantity, Category? category)
    {
        ValidateName(name);
        ValidateSku(sku);

        if (stockQuantity < 0)
        {
            throw new DomainException("Stock quantity cannot be negative.");
        }

        return new Product(name, sku, price, stockQuantity, category);
    }

    public void UpdateDetails(string name, Money price, Category? category)
    {
        ValidateName(name);
        Name = name;
        Price = price;
        Category = category;
    }

    public void IncreaseStock(int quantity)
    {
        if (quantity <= 0)
        {
            throw new DomainException("Stock changes must use a positive quantity.");
        }

        StockQuantity += quantity;
    }

    public void DecreaseStock(int quantity)
    {
        if (quantity <= 0)
        {
            throw new DomainException("Stock changes must use a positive quantity.");
        }

        if (quantity > StockQuantity)
        {
            throw new DomainException(
                $"Cannot decrease stock by {quantity}; only {StockQuantity} remain for SKU '{Sku}'.");
        }

        StockQuantity -= quantity;
    }

    private static void ValidateName(string name)
    {
        if (string.IsNullOrWhiteSpace(name))
        {
            throw new DomainException("Product name is required.");
        }

        if (name.Length > 200)
        {
            throw new DomainException("Product name cannot exceed 200 characters.");
        }
    }

    private static void ValidateSku(string sku)
    {
        if (string.IsNullOrWhiteSpace(sku))
        {
            throw new DomainException("SKU is required.");
        }

        if (sku.Length is < 3 or > 20)
        {
            throw new DomainException("SKU must be between 3 and 20 characters.");
        }
    }
}
```

Only the lines *new* relative to `Category` need fresh explanation:

- **`public Money Price { get; private set; } = null!;`** — a domain type as a property: the price is not a raw `decimal` but a `Money`, so "which currency?" and "is it positive?" are answered by the value object, everywhere, forever. (A property of a `record` type, all default `null!`-style warnings apply; same EF-populates logic as before.)
- **`public Guid? CategoryId { get; private set; }`** — the **foreign key**: the id of the category this product belongs to, or `null` for "uncategorized." We keep both `CategoryId` and `Category` — id for the database relationship, navigation for object code; EF links them in Chapter 17.
- **`sku.Length is < 3 or > 20`** — the **`is` pattern with relational patterns** (C# 9+): a modern, readable range test. `sku.Length is < 3 or > 20` reads as English: "if the length is below 3 or above 20."
- **`UpdateDetails`, `IncreaseStock`, `DecreaseStock`** — three more **behaviors**, each guarding its own invariant. `DecreaseStock` is the one to study: the second `if` is the entire "never oversell" policy of ShopSphere, in four lines, in the one place stock changes. Note the error message uses **string interpolation** (`$"..."`) to tell the caller exactly what went wrong — helpful errors are part of good domain design.
- **Why `Create` doesn't take a `categoryId`** — it takes the `Category?` *object*. The rule "the category must be valid and really exist" is then guaranteed by construction: you can only pass a real `Category` you loaded (Chapter 13 will show the handler loading it and translating "not found" into HTTP 404).

### 9.7 The Order Aggregate — `OrderStatus`, `OrderItem`, `Order`

**The enum first** — a `enum` is a closed set of named constants, perfect for states:

```csharp
// File: src/ShopSphere.Domain/Orders/OrderStatus.cs
namespace ShopSphere.Domain.Orders;

public enum OrderStatus
{
    Pending = 1,
    Paid = 2,
    Shipped = 3,
    Cancelled = 4
}
```

Explicit numbers (`= 1` ...) are deliberate: enum values get stored in the database as integers, and implicit numbering silently breaks if someone later inserts a value in the middle. Pinned numbers are a stability contract.

**The line item** — a member of the aggregate with no independent life:

```csharp
// File: src/ShopSphere.Domain/Orders/OrderItem.cs
using ShopSphere.Domain.Products;

namespace ShopSphere.Domain.Orders;

public sealed class OrderItem
{
    private OrderItem() { }

    private OrderItem(Product product, int quantity)
    {
        Product = product;
        ProductId = product.Id;
        Quantity = quantity;
        UnitPrice = product.Price;
    }

    public Guid ProductId { get; private set; }
    public Product Product { get; private set; } = null!;
    public int Quantity { get; private set; }
    public Money UnitPrice { get; private set; } = null!;
}
```

- **No `BaseEntity`** — `OrderItem` has no independent identity in the business: a line means nothing without its order. Its "identity" will be composite (order id + product id) in the database (Chapter 17). Note this also means no public `Create` factory — the *only* constructor caller is `Order` itself, which is the aggregate root enforcing its rules. The constructor does validate its inputs by construction: `quantity` is a positive `int` because `Order.Place` checked, and `product` is a real `Product` object, not an id string from the internet.
- **`UnitPrice = product.Price`** — a deliberate business decision called a **snapshot**: the order records the price *at the moment of sale*. If the shop changes a price tomorrow, historical orders still show what the customer actually paid. Small line, big domain lesson.

**The aggregate root** — where the use case "place an order" actually lives:

```csharp
// File: src/ShopSphere.Domain/Orders/Order.cs
using ShopSphere.Domain.Common;
using ShopSphere.Domain.Products;

namespace ShopSphere.Domain.Orders;

public sealed class Order : BaseEntity
{
    private readonly List<OrderItem> _items = [];

    private Order() { }

    private Order(string customerName, string shippingAddress)
    {
        CustomerName = customerName;
        ShippingAddress = shippingAddress;
        Status = OrderStatus.Pending;
        OrderDateUtc = DateTime.UtcNow;
    }

    public string CustomerName { get; private set; } = null!;
    public string ShippingAddress { get; private set; } = null!;
    public DateTime OrderDateUtc { get; private set; }
    public OrderStatus Status { get; private set; }

    public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();

    public static Order Place(
        string customerName,
        string shippingAddress,
        IReadOnlyList<OrderLine> lines)
    {
        if (string.IsNullOrWhiteSpace(customerName))
        {
            throw new DomainException("Customer name is required.");
        }

        if (string.IsNullOrWhiteSpace(shippingAddress))
        {
            throw new DomainException("Shipping address is required.");
        }

        if (lines is null || lines.Count == 0)
        {
            throw new DomainException("An order requires at least one item.");
        }

        var order = new Order(customerName, shippingAddress);

        foreach (var line in lines)
        {
            if (line.Quantity <= 0)
            {
                throw new DomainException("Order item quantity must be positive.");
            }

            line.Product.DecreaseStock(line.Quantity);
            order._items.Add(new OrderItem(line.Product, line.Quantity));
        }

        return order;
    }

    public void MarkPaid()
    {
        if (Status != OrderStatus.Pending)
        {
            throw new DomainException($"Cannot pay an order that is already {Status}.");
        }

        Status = OrderStatus.Paid;
    }

    public sealed record OrderLine(Product Product, int Quantity);
}
```

The aggregate-specific lines, in order:

- **`private readonly List<OrderItem> _items = [];`** — the **backing field**: the real, mutable list, private to the root. `[]` is the C# **collection expression** for "new empty list." `_` prefix is the convention for private fields.
- **`public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();`** — an **expression-bodied property** (`=>` instead of a body) that hands out a *read-only wrapper* over the private list. Outsiders can `foreach` the items; they cannot `.Add()`, `.Remove()`, or otherwise touch the collection. **This line is the aggregate boundary made physical.**
- **`Status = OrderStatus.Pending;` / `OrderDateUtc = DateTime.UtcNow;`** — both set in the private constructor, so *every* order is born pending and stamped — there is no door that lets an order exist without them.
- **`public static Order Place(...)`** — the factory is named for the *use case*, not the type: business says "place an order," so the method is `Place`. (Naming factories after verbs of the domain is the ubiquitous language in action.)
- **`IReadOnlyList<OrderLine>`** — the caller (a handler, Chapter 15) supplies loaded `Product` objects plus quantities. The domain does not load anything (no repository calls here!) — it receives what it needs and rules over it. `Order` never does I/O; it only decides.
- **`line.Product.DecreaseStock(line.Quantity);`** — look closely, this is the deepest line in the chapter: **one aggregate root commands another entity**. "Placing an order consumes stock" is implemented as the order asking each product to decrement itself — through the *guarded door*, so if stock is insufficient, `DecreaseStock` throws and the whole `Place` aborts. No order, no item, no negative stock. One rule, one home, one moment of truth. (What if two customers race for the last item? Chapter 25's concurrency section answers.)
- **`public void MarkPaid()`** — a **state machine step**: the guard (`Status != Pending`) makes "paying a shipped order" impossible. The status enum plus guarded transitions equals an auditable order lifecycle.
- **`public sealed record OrderLine(Product Product, int Quantity);`** — a **nested type**: `OrderLine` exists only to describe `Order`'s input, so it lives *inside* `Order` — discoverable, unnameable-conflicted, and invisible to the rest of the domain. A tiny value object in exactly one place.

### 9.8 Build the Heart

From the solution root:

```bash
dotnet build
```

Expected: `Build succeeded` with zero warnings. (Three projects built before `Api` — the build order itself recapitulates the dependency diagram.) The Domain now exists, pure and reference-free; nothing can run yet, because a heart needs a body. Part IV grows the use-case layer that will make the heart beat.

### Key Takeaways — Chapter 9

- `DomainException` = sealed, primary-constructed, one clear type for broken business rules.
- `BaseEntity` = `Guid` identity via time-ordered `Guid.CreateVersion7()`, `protected set`, UTC audit columns to be stamped by infrastructure.
- `Money` = a `record` value object with a validating `Create` factory; `Category`/`Product` = entities with private constructors, `null!`-documented EF-populated properties, and behavior (`Rename`, `UpdateDetails`, `DecreaseStock`) guarding invariants.
- `OrderItem` snapshots `UnitPrice`; `Order` is the aggregate root: private `_items`, public `IReadOnlyCollection`, use-case-named factory `Place`, and cross-entity command (`DecreaseStock`) that keeps "no overselling" true in one auditable place.
- `dotnet build` succeeded — the innermost circle is real, and it knows nothing about databases or the web.

---

# PART IV — THE APPLICATION LAYER

> The use-case circle. Part IV is where ShopSphere learns to *do* things: we adopt CQRS, wire MediatR as the messenger, organize everything as vertical slices, and then build every use case of the system — each one exactly three files: a Command (or Query), a Handler, and a Validator.

---

## Chapter 10 — The Application Layer Mindset: Use Cases and CQRS

### 10.1 What This Layer Is

If the Domain layer answers "*what* is a product?", the Application layer answers "*what can you do* with one?" It is the catalog of every operation the application offers — its **use cases**:

| Use case (business wording) | Message type | Slice name |
|---|---|---|
| "Add a product to the catalog" | Command | `CreateProduct` |
| "Change a product's details" | Command | `UpdateProduct` |
| "Remove a product" | Command | `DeleteProduct` |
| "Look up one product" | Query | `GetProductById` |
| "Browse products with search/paging" | Query | `GetProducts` |
| "Manage categories" | Command/Query | `CreateCategory`, `GetCategories`... |
| "Place an order" | Command | `PlaceOrder` |

What is **allowed** in this project: commands and queries (records), their handlers, their validators, their response DTOs, cross-cutting behaviors (Chapter 23), and the **ports** — interfaces like `IProductRepository` and `IUnitOfWork` that declare what use cases need from the world (Chapter 5's boundary types).

What is **forbidden**: EF Core (no `DbContext`, no `IQueryable`), SQL, HTTP types, business rules that belong inside entities (Chapter 8's table), and UI concerns. A handler is an *orchestrator*, not a rule-maker and not a database client.

### 10.2 CQRS From Zero

**CQRS — Command Query Responsibility Segregation.** In 2010, Greg Young crystallized an observation Fowler had been writing about: in most systems, *reading* and *writing* have fundamentally different shapes:

- A **write** ("create product") is a careful ceremony: validate the input, load related data, invoke guarded domain behavior, commit a transaction. Maybe 20 database rows written. Result: one GUID.
- A **read** ("show me page 3 of red t-shirts, cheapest first") is a bulk haul: filter thousands of rows, join, sort, project to exactly the columns a screen needs. No domain rules involved at all. Result: 20 fully-shaped DTOs.

Traditional "CRUD services" (`IProductService` with `Get/Create/Update/Delete`) force **one model and one code path** to serve both shapes — so every method compromises: entity graphs loaded for screens that needed three columns, domain rules dragged into read paths, god-services that grow to thousands of lines. CQRS's move is to **segregate** them into two first-class kinds of message:

| | **Command** | **Query** |
|---|---|---|
| Purpose | Change state | Return data |
| Naming | Imperative: `CreateProductCommand` | Question: `GetProductsQuery` |
| Touches domain entities? | Yes — writes flow through entities' guarded doors | Ideally no — read straight to DTOs |
| May it mutate anything? | Yes (via domain + Unit of Work) | **Never** |
| Return value | Minimal: an id, or nothing at all | Rich: DTOs, paged lists |
| Failure modes | Validation 400 / NotFound 404 / Conflict 409 | Mostly 404 |

Two clarifications that will save you from the internet's favorite CQRS myths:

1. **CQRS ≠ two databases.** Massive systems sometimes split read/write *stores* (a full CQRS topology), but that is an option, not the pattern. Our CQRS is **at the code level**: one SQL Server, two cleanly separated message pipelines. The discipline, not the topology.
2. **CQRS ≠ event sourcing.** Event sourcing (storing every change as an immutable event) is a separate, heavyweight pattern that some CQRS systems use. We do not use it; Chapter 26 points you to it for later study.

### 10.3 Why Commands Are Better Than "Service Methods"

Compare the traditional and the CQRS/vertical-slice shape of the same feature:

```
Traditional:                                    This book:
ProductService.cs (1 class, 12 methods,         CreateProduct/
  3 developers editing it, 900 lines)             ├── CreateProductCommand.cs       (input)
                                                  ├── CreateProductCommandHandler.cs (the use case)
                                                  └── CreateProductCommandValidator.cs (input rules)

UpdateProduct/  GetProductById/  GetProducts/   ← each feature: same 3-file shape, zero shared edits
```

Every use case becomes a small, closed unit. Want to know what "create product" does? One file, ~40 lines, top to bottom. Want to add a rule to ordering? Open `PlaceOrder`'s folder — no other folder changes. That locality is what "vertical" means: slices cut *across* the technical layers of old, one slice per feature, instead of scattering features across them.

### 10.4 How a Request Will Flow (Preview of the Whole Book)

Fix this picture in your mind — it is the runtime story of every chapter from 13 to 23:

```
HTTP POST /api/products
  │
  ▼
ProductsController.Create(...)            (Chapter 22 — binds JSON, hands message to MediatR)
  │  sender.Send(createProductCommand)
  ▼
MediatR pipeline                          (Chapter 11)
  │  1. ValidationBehavior runs ALL validators   (Chapter 23 — invalid ⇒ 400, stop)
  │  2. CreateProductCommandHandler.Handle(...)  (Chapter 13 — the use case)
  │        │
  │        ├─ loads Category via ICategoryRepository   (port, Chapter 13)
  │        ├─ Product.Create(...)  ← guarded domain   (Chapter 9)
  │        └─ IProductRepository.AddAsync + IUnitOfWork.SaveChangesAsync
  │                                             (implemented by EF Core, Chapter 18-19)
  │  3. returns new product id
  ▼
Controller maps result ⇒ 201 Created + Location header
  │
  ▼
SQL Server                                (schema by migrations, Chapter 20)
```

The Application layer owns step 2. The rest of this part builds it piece by piece: MediatR (Chapter 11), the folder convention (Chapter 12), and then every slice of ShopSphere (Chapters 13–15).

### Key Takeaways — Chapter 10

- Application = the catalog of use cases; allowed: messages, handlers, validators, DTOs, behaviors, ports (interfaces); forbidden: EF Core, HTTP, and domain rules.
- CQRS separates writes (commands through guarded entities) from reads (queries straight to DTOs) — at the code level, with one database.
- Commands are imperative, minimal-return, transactional; queries are questions that never mutate.
- One feature = one folder = three files: message, handler, validator.

---

## Chapter 11 — MediatR From Zero: The Mediator Pattern in Practice

### 11.1 The Problem MediatR Solves

Imagine Chapter 22's controller *without* a mediator. To create a product it must: construct a validator, run it, load the category, build the product, save it... so it injects `CreateProductHandler`, `UpdateProductHandler`, `GetProductsHandler`, ... — one dependency per use case, growing forever. Or you hide them behind a `IProductService` mega-facade, which is the god-class CQRS just killed. Either way, the **caller and the doer are welded together**: the controller knows concrete handler types, handler signatures ripple outward when they change, and adding cross-cutting steps (validation! logging!) means editing every caller or every handler.

### 11.2 The Mediator Pattern

The **mediator pattern** (one of the classic "Gang of Four" behavioral patterns) inserts a middleman: *callers talk only to the mediator; the mediator knows who the right doer is.* Everyone is decoupled from everyone; only the mediator routes. An airport control tower is the classic analogy — planes don't talk to each other; they talk to the tower.

### 11.3 MediatR's Vocabulary — Three Interfaces, That's All

**MediatR** (Jimmy Bogard's library, the de-facto standard since 2014, over a billion downloads) implements the pattern with in-process messages. Its entire public surface for us:

**1. `IRequest<TResponse>` — the message envelope.** A command or query *record* implements this to declare "when I am sent, I produce a `TResponse`":

```csharp
public sealed record CreateProductCommand(...) : IRequest<Guid>;  // returns the new id
public sealed record DeleteProductCommand(Guid Id) : IRequest;   // returns nothing
```

- `IRequest` (no type argument) means "no response expected" — right for deletes.
- A record implementing a marker interface like this is what MediatR calls an **in-process message**: it is not serialized anywhere, it lives and dies inside the same process; nothing external ever sees it.

**2. `IRequestHandler<TRequest, TResponse>` — the doer.** A class that promises to handle one message type and produce the response:

```csharp
public sealed class CreateProductCommandHandler
    : IRequestHandler<CreateProductCommand, Guid>
{
    public async Task<Guid> Handle(CreateProductCommand request, CancellationToken cancellationToken)
    { ... the entire use case lives here ... }
}
```

One handler per message. Always. Not "a handler with a switch." The handler is registered with DI as `transient` — created per request, dependencies injected, discarded.

**3. `ISender` — the door.** The single dependency any caller ever needs:

```csharp
Guid id = await sender.Send(command, cancellationToken);
```

`Send` is MediatR's whole routing brain. At startup, MediatR scans the assembly (we'll tell it which one in Chapter 21), finds every `IRequest`/`IRequestHandler` pair, and builds a dictionary: *message type → factory that constructs its handler*. At `Send` time it looks up the runtime type of your message, gets the handler, awaits `Handle`. The caller (`ProductsController`) knows nothing but `ISender`. Handler changes, new features, deleted features — the controller doesn't move.

### 11.4 The Licensing Story (Current, Complete, Calm)

You will read alarming headlines about this, so here is the verified timeline and the decision logic:

- **January 2025** — Jimmy Bogard announces that MediatR (and AutoMapper) must move to a commercial model to remain sustainable; his company is **Lucky Penny Software**.
- **July 2, 2025** — the commercial editions launch. **MediatR 13.0** and later ship under a dual license: a commercial license for for-profit companies above the revenue threshold, and a **free Community edition** for individuals, students, open-source projects, non-profits, and companies under **$5M gross annual revenue** (register a free key at [mediatr.io](https://mediatr.io)).
- **Versions 12.x and earlier — including 12.5.0 — remain permanently free under Apache-2.0**, and work perfectly on .NET 10. Their APIs (`IRequest`, `IRequestHandler`, `ISender`, `IPipelineBehavior`) are identical to the commercial line for everything this book does.
- As of late 2026 the commercial line is at **14.2.0**; the ecosystem (templates, samples, Stack Overflow answers) still overwhelmingly uses the same three interfaces you are learning here.

**This book pins `MediatR` 12.5.0** (Chapter 7's `dotnet add package MediatR --version 12.5.0`): zero licensing friction for every reader in every context. If your company exceeds the threshold and you prefer a fully open-source alternative: **`Mediator`** (by Martin Othamar, MIT license) is a source-generated, AOT-friendly drop-in with nearly the same API shape; **Brighter** is a larger FOSS alternative; and hand-rolling a dispatcher over a `Dictionary<Type, Func<object, Task<object>>>` is ~50 lines if you ever want zero dependencies. Everything in this book transfers: the *pattern* is the knowledge; MediatR is just its most famous implementation.

### 11.5 Pipeline Behaviors (Preview)

MediatR has one more trick we exploit in Chapter 23: **`IPipelineBehavior<TRequest, TResponse>`** — a wrapper that runs *around every* `Send`, like middleware around a request. Our `ValidationBehavior` will "run every registered validator, reject invalid messages with a `ValidationException`, otherwise let the handler run" — written once, applied to all current and future use cases automatically. This is how Clean Architecture does cross-cutting concerns without repeating code.

### 11.6 Notifications (One Paragraph Now, Used in Chapter 25)

Besides request/response, MediatR supports **fire-and-forget broadcast**: `INotification` messages can be handled by *any number* of `INotificationHandler<T>` classes. Where `Send` finds *one* handler, `Publish` calls *all* of them. Chapter 25 uses this to dispatch **domain events** ("product created") to listeners — remember this paragraph when you get there.

### Key Takeaways — Chapter 11

- Mediator pattern = callers talk to a middleman; MediatR = that middleman as three interfaces: `IRequest<T>` (message), `IRequestHandler<TReq,TResp>` (one doer per message), `ISender` (the door).
- `Send` routes by scanning-built dictionary at runtime; callers depend on one interface forever.
- Licensing, verified: 13+ (2025) is dual-licensed with a free tier under $5M revenue; **12.5.0 is permanently Apache-2.0** and is what we use; open-source alternatives exist with the same shape.
- `IPipelineBehavior` = middleware for messages (our validation step, Chapter 23); `INotification`/`Publish` = one-to-many broadcast (our domain events, Chapter 25).

---

## Chapter 12 — Vertical Slice Architecture: One Feature, Three Files

### 12.1 The Problem With "Folders by Kind"

The Application layer could be organized like most old tutorials organize it:

```
ShopSphere.Application/          ❌ the "folder by kind" layout
├── Services/
│   ├── ProductService.cs        (create + update + delete + get + list…)
│   └── CategoryService.cs
├── Validators/
│   ├── CreateProductValidator.cs
│   ├── UpdateProductValidator.cs
│   └── ...
├── Mapping/
└── Interfaces/
```

Nice and tidy — for a table of contents. Now watch a real team work: to implement "create product" a developer opens `Services/ProductService.cs` (contested by everyone), a validator three folders away, a mapping profile, an interface file... The *feature* is smeared horizontally across the project. To review it you need five files open; to delete it you must surgically extract code from four of them. Services grow into 1,000-line museums where each new developer adds a wing.

### 12.2 The Vertical Answer

**Vertical Slice Architecture** inverts the axis: instead of grouping code *by kind* (all services, all validators), group it **by feature** — by use case. A **slice** is everything one use case needs, in one folder, named after the use case, containing exactly three files:

```
ShopSphere.Application/          ✅ the "folder by feature" layout
└── Features/
    ├── Products/
    │   ├── CreateProduct/
    │   │   ├── CreateProductCommand.cs              the message (input)
    │   │   ├── CreateProductCommandHandler.cs       the use case (orchestration)
    │   │   └── CreateProductCommandValidator.cs     the input rules
    │   ├── UpdateProduct/         (same three files)
    │   ├── DeleteProduct/         (same three files)
    │   ├── GetProductById/
    │   │   ├── GetProductByIdQuery.cs
    │   │   ├── GetProductByIdQueryHandler.cs
    │   │   └── GetProductByIdQueryValidator.cs?      (often unneeded — see 12.3)
    │   └── GetProducts/
    │       ├── GetProductsQuery.cs
    │       ├── GetProductsQueryHandler.cs
    │       ├── GetProductsQueryValidator.cs          (paging bounds ARE rules)
    │       └── ProductResponse.cs                    (the slice's output DTO)
    └── Categories/
        └── ...
```

Vertical because each folder *cuts vertically* through the old horizontal categories: message + logic + rules in one place. When a new developer joins and asks "how does creating a product work?", you open **one folder** and hand them three files in reading order. When a feature dies, you delete one folder. No merge conflicts with unrelated features, no museums.

### 12.3 The Three Files, Precisely Defined

**File 1 — the Command / Query (the message).** A `sealed record` implementing `IRequest<T>` (or `IRequest`), holding *only* the input data the use case needs — nothing more, nothing the handler could look up itself. Naming law: `{Verb}{Noun}Command` for writes, `{Verb}{Noun}Query` for reads. It is a dumb, immutable envelope — created from an HTTP request, sent through `ISender`, never containing logic. Even its name is documentation: `CreateProductCommand` needs no comment.

*Why a record?* Immutability (no one edits the message mid-flight), value semantics, and constructor brevity. *Why only its own data?* If `CreateProductCommand` carried `StockQuantityRemaining`, the caller would need to know internals — the handler fetches what it needs (Chapter 13).

**File 2 — the Handler (the use case).** A `sealed class` implementing `IRequestHandler<TMessage, TResponse>`, whose single `Handle` method is the entire use case, in this order, always:

1. Load what the domain needs (through repository ports),
2. Invoke domain behavior (factories, entity methods — Chapter 9's guarded doors),
3. Persist through the Unit of Work port,
4. Return a small result (an id, a DTO).

Handlers are **thin by law**: if you find business rules in a handler that *could* live in an entity (checking `stockQuantity < 0`? — `Product.Create` already does that), move them inward; if you find SQL, move them outward to Infrastructure. The handler's constructor declares its ports as interfaces — repositories, `IUnitOfWork`, nothing concrete. `sealed` because nobody should ever subclass a use case; new behavior = new slice.

**File 3 — the Validator (the input rules).** A `sealed class` inheriting FluentValidation's `AbstractValidator<TMessage>` — one per message — declaring *well-formedness* rules in a fluent chain: `RuleFor(x => x.Name).NotEmpty().MaximumLength(200);`. It answers only Chapter 8's first question ("is this request well-formed?"), never contextual rules (that's the domain's job). Two honest notes:

- **Do queries get validators?** When the input has constraints, yes: `GetProductsQuery` needs `PageNumber >= 1`, `PageSize` between 1 and 100 — real rules, so it gets a validator. `GetProductByIdQuery(Guid Id)` has no interesting input rules (a malformed GUID never even reaches us — the route won't bind), so it gets none. Decide per slice; the pattern allows both.
- **When does the validator run?** Not manually! Chapter 23's `ValidationBehavior` runs every matching validator automatically before every handler. One line of registration, every slice protected, forever.

### 12.4 The Naming Law (Memorize It)

| Kind | Message file | Handler file | Validator file |
|---|---|---|---|
| Command | `{Name}Command.cs` | `{Name}CommandHandler.cs` | `{Name}CommandValidator.cs` |
| Query | `{Name}Query.cs` | `{Name}QueryHandler.cs` | `{Name}QueryValidator.cs` |

And nothing else in the slice folder, with exactly one exception: the slice's **response DTO record** (`ProductResponse.cs`) may live beside its query — a read slice owns its own output shape.

### 12.5 What Slices May Share (and What They May Not)

Slices are independent by design, but not isolated islands — three shared categories are legitimate, all under `Application/Common/`:

1. **Ports** (`Common/Interfaces/`): `IProductRepository`, `IUnitOfWork`, ... — the boundary interfaces of Chapter 5. Every slice needs the same doors; defining each door once is the Dependency Inversion Principle, not duplication.
2. **Small result shapes** (`Common/Models/`): `PagedResult<T>` (Chapter 14) — a generic pagination envelope that would be absurd to re-declare per slice.
3. **Cross-cutting machinery** (`Common/Behaviors/`, `Common/Exceptions/`): the validation pipeline, exception types — Chapter 23's machinery.

What slices must **not** share: base handler classes, shared "manager" services, or a shared validator for "all product commands" (each use case's input rules differ; pretending they don't is how subtle bugs are born). If two slices feel duplicative, first ask whether the duplication is real (same *knowledge*) or accidental (same *typing*) — only the first is worth extracting (recall DRY's trap from Chapter 4).

### 12.6 One Slice = One Use Case (Not One Entity)

A final calibration, because beginners overshoot: a slice is one **use case**, not one **entity**. "Products" is not a slice; "create product", "get products page", "place order" are. If a future feature is "restock a product by SKU", it earns its own `RestockProduct/` folder with its own three files — even though it touches the same `Product` entity as five other slices. Entities are shared inward (Domain); use cases are not shared at all.

### Key Takeaways — Chapter 12

- Organize by feature, not by kind: one folder per use case, three files per folder — message, handler, validator — plus (queries only) a response DTO.
- Message = sealed record implementing `IRequest<T>`, input only. Handler = sealed class, thin orchestrator in four steps: load → domain → persist → return. Validator = `AbstractValidator<TMessage>`, well-formedness only.
- Naming law: `{Name}Command/Query`, `{Name}Command/QueryHandler`, `{Name}Command/QueryValidator`.
- Share only ports, generic result shapes, and cross-cutting machinery — never base handlers or "shared logic" between slices.
- Slices are cut per use case, not per entity.

---

## Chapter 13 — Our First Slice: CreateProduct — Command, Handler, Validator, Line by Line

This is the chapter the whole book has been climbing toward. We build one complete use case — "create a product" — as three files, and on the way we discover why the Application layer must declare **ports** (the interfaces of Chapter 5) and two boundary exceptions. Read it slowly; every line gets its explanation.

Create the folders first, from the solution root:

```bash
mkdir -p src/ShopSphere.Application/Common/Interfaces \
         src/ShopSphere.Application/Common/Exceptions \
         src/ShopSphere.Application/Features/Products/CreateProduct
```

### 13.1 The Port Problem (or: Where Did My Database Go?)

Pretend we already had a `CreateProductCommand` and wanted to handle it. The handler must: check the SKU is free, load the category if given, save the product. But Chapter 7's rulebook says: *the Application project may not reference EF Core*. So how does a handler save anything?

This is the exact moment Dependency Inversion (Chapter 4) stops being theory. The Application layer does not ask "what database library can I use?" — it asks "what do I *need*?" and *writes that need down as an interface*. Infrastructure (Part V) will implement it. The interface is a **port** (Chapter 2's hexagonal vocabulary), and it belongs to *us*, the inner circle.

### 13.2 The Ports — `IProductRepository`, `IUnitOfWork`, `ICategoryRepository`

```csharp
// File: src/ShopSphere.Application/Common/Interfaces/IProductRepository.cs
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Common.Interfaces;

public interface IProductRepository
{
    Task AddAsync(Product product, CancellationToken cancellationToken = default);

    Task<Product?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default);

    Task<bool> SkuExistsAsync(string sku, CancellationToken cancellationToken = default);
}
```

Line by line:

- **`public interface IProductRepository`** — a pure contract: method signatures with no bodies. No implementation exists yet anywhere; that is the point. Note the namespace `...Common.Interfaces` — "shared doors of the application layer" (Chapter 12's sharing rules).
- **`using ShopSphere.Domain.Products;`** — the interface speaks the *domain's* types (`Product`). Application may reference Domain (inward dependency — legal), so ports and handlers exchange entities across that boundary.
- **`Task AddAsync(Product product, CancellationToken cancellationToken = default)`** — "you can hand me a product to store." `Task` (no `<T>`) = an asynchronous operation returning nothing. The `= default` makes the token optional for callers that don't care (we will always pass it — but the option documents intent). "Async" suffix is the .NET convention for awaitable methods.
- **`Task<Product?> GetByIdAsync(Guid id, ...)`** — "fetch a product by id, or *nothing*." `Product?` is the nullable reference type from Chapter 7 doing real design work: absence is now *typed*. The caller (you'll see in a moment) is forced by the compiler to handle the null case.
- **`Task<bool> SkuExistsAsync(string sku, ...)`** — "does any product already use this SKU?" — the uniqueness pre-check our handler needs. (The *database* will also enforce uniqueness — Chapter 17 — because two requests can race; the pre-check is for a friendly error, the unique index is the true guarantee. Defense in depth.)

```csharp
// File: src/ShopSphere.Application/Common/Interfaces/ICategoryRepository.cs
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Common.Interfaces;

public interface ICategoryRepository
{
    Task<Category?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default);

    Task AddAsync(Category category, CancellationToken cancellationToken = default);
}
```

Same shape, for categories — grown later (Chapter 15) as more use cases demand more doors. Interfaces evolving with real needs beats speculative kitchen-sink interfaces: that is the Interface Segregation Principle in daily life.

```csharp
// File: src/ShopSphere.Application/Common/Interfaces/IUnitOfWork.cs
namespace ShopSphere.Application.Common.Interfaces;

public interface IUnitOfWork
{
    Task<int> SaveChangesAsync(CancellationToken cancellationToken = default);
}
```

The Unit of Work port, four words of concept: **"commit everything this request changed, atomically."** A handler may touch several repositories; when it calls `SaveChangesAsync` once, all pending changes — in every repository — are written in a single database transaction (all-or-nothing, Chapter 4). It returns `Task<int>`: the number of affected rows, which we ignore but which is the conventional signature (it mirrors EF Core's own `SaveChangesAsync` — no accident; Chapter 19 implements it with exactly that call). Deliberately *not* `IDisposable`: lifetimes are owned by the DI container (Chapter 21), and interface bloat avoided.

### 13.3 The Boundary Exceptions

The handler will need to say "that category doesn't exist" and "that SKU is taken" in a way Chapter 23's middleware can translate into precise HTTP status codes (404, 409). Distinct exception *types* are that language:

```csharp
// File: src/ShopSphere.Application/Common/Exceptions/NotFoundException.cs
namespace ShopSphere.Application.Common.Exceptions;

public sealed class NotFoundException(string name, object key)
    : Exception($"{name} with id '{key}' was not found.");
```

- **`(string name, object key)`** — a primary constructor (Chapter 9's trick) capturing what was sought: `new NotFoundException(nameof(Category), request.CategoryId.Value)`. `nameof(Category)` compiles to the string `"Category"` — refactoring-safe typing.
- **`: Exception($"{name} with id '{key}' was not found.")`** — the message is built with interpolation and passed to the base `Exception` constructor. One line, whole class.
- These live in the **Application** layer (not Domain — "not found" is a *use case* outcome, not a business rule; the Domain says what a valid order is, the Application says whether a requested one exists).

```csharp
// File: src/ShopSphere.Application/Common/Exceptions/ConflictException.cs
namespace ShopSphere.Application.Common.Exceptions;

public sealed class ConflictException(string message) : Exception(message);
```

For "the request conflicts with current state" — a duplicate SKU, the classic HTTP **409 Conflict** case.

### 13.4 File 1 — `CreateProductCommand` (Word by Word, As Promised)

```csharp
// File: src/ShopSphere.Application/Features/Products/CreateProduct/CreateProductCommand.cs
using MediatR;

namespace ShopSphere.Application.Features.Products.CreateProduct;

public sealed record CreateProductCommand(
    string Name,
    string Sku,
    decimal PriceAmount,
    string Currency,
    int StockQuantity,
    Guid? CategoryId) : IRequest<Guid>;
```

Every single word, as promised:

- **`public`** — visible to any referencing project; Api's controller (Chapter 22) will construct it.
- **`sealed`** — no derived `SpecialCreateProductCommand`; the message is final.
- **`record`** — the message gets a constructor, init-only properties, value equality, and `ToString` for free; immutable by shape — nobody can mutate a message mid-flight.
- **`CreateProductCommand(`** — the naming law of Chapter 12: verb + noun + `Command`.
- **`string Name`** — required input. Not `Name?`: a missing name is a *validation* failure (the Validator below), and FluentValidation works best with non-nullable, honest strings.
- **`string Sku`** — same reasoning.
- **`decimal PriceAmount`** and **`string Currency`** — note what is *absent*: a `Money Price` property! The command carries **input data**, not domain objects: the client sends JSON (`{ "priceAmount": 25.00, "currency": "USD" }`), and the *handler* will call `Money.Create(...)` to turn that raw pair into a guarded domain value. Boundary shapes stay primitive; domain types are constructed inside. (This is the DTO-vs-entity discipline of Chapter 5 in miniature.)
- **`int StockQuantity`** — starting stock.
- **`Guid? CategoryId`** — nullable: "the id of the category, if any." Note the command carries the *id*, not a `Category` — the client cannot hand us a counterfeit category; the handler loads the real one through the port (13.5).
- **`) : IRequest<Guid>;`** — implements MediatR's marker interface: "sending me yields a `Guid`" — the new product's id. That single type parameter is the whole contract between controller and use case.

### 13.5 File 2 — `CreateProductCommandHandler` (Line by Line)

```csharp
// File: src/ShopSphere.Application/Features/Products/CreateProduct/CreateProductCommandHandler.cs
using MediatR;
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Products.CreateProduct;

public sealed class CreateProductCommandHandler(
    IProductRepository productRepository,
    ICategoryRepository categoryRepository,
    IUnitOfWork unitOfWork)
    : IRequestHandler<CreateProductCommand, Guid>
{
    public async Task<Guid> Handle(CreateProductCommand request, CancellationToken cancellationToken)
    {
        if (await productRepository.SkuExistsAsync(request.Sku, cancellationToken))
        {
            throw new ConflictException($"A product with SKU '{request.Sku}' already exists.");
        }

        Category? category = null;
        if (request.CategoryId is not null)
        {
            category = await categoryRepository.GetByIdAsync(request.CategoryId.Value, cancellationToken)
                ?? throw new NotFoundException(nameof(Category), request.CategoryId.Value);
        }

        var price = Money.Create(request.PriceAmount, request.Currency);

        var product = Product.Create(
            request.Name,
            request.Sku,
            price,
            request.StockQuantity,
            category);

        await productRepository.AddAsync(product, cancellationToken);
        await unitOfWork.SaveChangesAsync(cancellationToken);

        return product.Id;
    }
}
```

The declaration, piece by piece:

- **`public sealed class CreateProductCommandHandler(` … **)** — a **primary constructor for dependency injection**: the three parameters become constructor-injected, readonly-ish fields available throughout the class (C# 12+). This replaces the old five-line boilerplate (`private readonly IProductRepository _repo; public Handler(IProductRepository repo) { _repo = repo; }`) with three lines of pure intent. The handler *declares* what it needs; someone else (Chapter 21) decides what it gets.
- **`: IRequestHandler<CreateProductCommand, Guid>`** — the MediatR contract: "I handle `CreateProductCommand` and return a `Guid`." The type pairing is how `Send` finds this class without anyone registering the pair manually.
- **`public async Task<Guid> Handle(CreateProductCommand request, CancellationToken cancellationToken)`** — the one method MediatR calls. `async`/`Task<Guid>` because handlers await I/O; the `CancellationToken` flows from the HTTP request itself — if the client disconnects, cancellation propagates down to the SQL query. Never drop that token.

The body, step by step (Chapter 10's four-step choreography):

1. **Uniqueness pre-check.** `await productRepository.SkuExistsAsync(...)` — ask the port. If taken, `throw new ConflictException(...)` → Chapter 23 turns it into HTTP 409. Failing *early*, before any work, is cheapest for everyone.
2. **Resolve the category.** `Category? category = null;` then `if (request.CategoryId is not null)` — `is not null` is the modern, pattern-based null test. Inside: **`request.CategoryId.Value`** unwraps the `Guid?` (safe: we just proved it has a value). The expression **`?? throw new NotFoundException(...)`** is the **null-coalescing throw**: "if `GetByIdAsync` returned null, throw; otherwise keep the value." It turns a nullable `Category?` into a definite `Category` in one breath — the compiler now *knows* `category` is real. (This is the 404 path: the client asked for a category that does not exist — their mistake, a precise status code, a helpful message.)
3. **Build domain values.** `Money.Create(...)` validates and normalizes price+currency (Chapter 9); `Product.Create(...)` runs the entity's invariants. Look at what we *don't* check here: name length, SKU shape, positive stock — **already guaranteed** by the Validator (below) and the entity. The handler orchestrates; it does not re-legislate.
4. **Persist atomically.** `productRepository.AddAsync(product, ...)` registers the product as *pending insertion* — nothing has touched SQL Server yet! Then `unitOfWork.SaveChangesAsync(...)` commits. Why two calls? Between them there could have been *more* changes (in Chapter 15's `PlaceOrder`, many entities pile up); `AddAsync` announces intent, `SaveChangesAsync` performs the single atomic write of everything (Chapter 19 shows the machinery). Also notice: we never open/close a connection, never begin/commit a transaction — the Unit of Work hides all of it.
5. **Return.** `product.Id` — generated in the domain (Chapter 9's `Guid.CreateVersion7()`), present *before* saving. The handler returns it; MediatR hands it to `sender.Send`'s caller; the controller (Chapter 22) answers `201 Created` with the id.

### 13.6 File 3 — `CreateProductCommandValidator` (Line by Line)

```csharp
// File: src/ShopSphere.Application/Features/Products/CreateProduct/CreateProductCommandValidator.cs
using FluentValidation;

namespace ShopSphere.Application.Features.Products.CreateProduct;

public sealed class CreateProductCommandValidator : AbstractValidator<CreateProductCommand>
{
    public CreateProductCommandValidator()
    {
        RuleFor(x => x.Name)
            .NotEmpty().WithMessage("Product name is required.")
            .MaximumLength(200);

        RuleFor(x => x.Sku)
            .NotEmpty()
            .Matches("^[A-Z0-9-]{3,20}$")
            .WithMessage("SKU must be 3-20 characters: uppercase letters, digits or hyphens.");

        RuleFor(x => x.PriceAmount)
            .GreaterThan(0).WithMessage("Price must be greater than zero.");

        RuleFor(x => x.Currency)
            .NotEmpty()
            .Matches("^[A-Z]{3}$").WithMessage("Currency must be a 3-letter ISO 4217 code (e.g. USD, EGP).");

        RuleFor(x => x.StockQuantity)
            .GreaterThanOrEqualTo(0).WithMessage("Stock quantity cannot be negative.");

        RuleFor(x => x.CategoryId)
            .NotEqual(Guid.Empty)
            .When(x => x.CategoryId.HasValue)
            .WithMessage("CategoryId must be a real id when provided.");
    }
}
```

- **`: AbstractValidator<CreateProductCommand>`** — FluentValidation's base class, bound to our exact message type. It knows how to *run* itself; we only declare rules.
- **`RuleFor(x => x.Name)`** — starts a rule chain for one property; the lambda (`x => x.Name`) is compiled so both the rule and its error messages reference the real property name (`"Name"`), typed safely — rename the property and the rule follows.
- **`.NotEmpty()`** — rejects `null`, `""`, and whitespace. **`.MaximumLength(200)`** — the boundary's copy of the domain's name rule (Chapter 9) — deliberately: the validator gives the client an immediate, field-level 400; the entity remains the deep guarantee (Chapter 8's "two questions" table).
- **`.Matches("^[A-Z0-9-]{3,20}$")`** — a **regular expression**: start (`^`), 3–20 characters from the classes `[A-Z 0-9 -]`, end (`$`). Input *shape* rules are exactly what regexes are for, and exactly what belongs in the Validator (not the entity — the domain checks "3-20 characters", the *format* is an application-level convention for ShopSphere's SKU style).
- **`.WithMessage(...)`** — overrides FluentValidation's default English message for that single check; without it you get sensible defaults, with it you get your app's voice.
- **`.GreaterThan(0)` / `.GreaterThanOrEqualTo(0)`** — numeric bounds; note these mirror `Money.Create` and `Product.Create` — again the deliberate double check, each at its own layer, for its own consumer.
- **`.NotEqual(Guid.Empty)` + `.When(x => x.CategoryId.HasValue)`** — the **conditional rule**: `CategoryId` is optional, *but* if present it must not be `Guid.Empty` (a GUID of all zeros — what `new Guid()` gives you; never a real id). `.When(...)` is FluentValidation's guard: the rule only fires for requests that actually carry a category.

Nothing calls this class manually. In Chapter 23, a MediatR pipeline behavior will discover every `AbstractValidator<CreateProductCommand>` in DI and run all of them before the handler — one registration, every current and future slice validated.

### 13.7 Build and Reflect

```bash
dotnet build
```

Zero warnings — and notice what just compiled: an entire use case that mentions no database, no HTTP, no `DbContext`; only its own message, its domain, and three interfaces *it owns*. The Application layer now *expects* persistence but cannot name its provider. Part V has a promise to keep.

### Key Takeaways — Chapter 13

- Ports are needs written as interfaces: `IProductRepository`, `ICategoryRepository` (per-aggregate doors) and `IUnitOfWork` (one atomic commit for all of them) — declared in Application, implemented in Infrastructure.
- `NotFoundException`/`ConflictException` are the Application layer's boundary vocabulary for 404/409 (Chapter 23 translates).
- `CreateProductCommand`: sealed record, primitive input shapes (no `Money`!), `IRequest<Guid>` — the id is the contract.
- The handler: primary-constructor DI, five steps (pre-check → resolve → domain → persist-atomically → return), `?? throw` for null translation, thin by law.
- The validator: fluent chains per property, regex for shape rules, `.When` for conditionals, messages in your app's voice — and it runs automatically via Chapter 23's behavior.

---

## Chapter 14 — Reading Data: The Query Slices

Commands are done; now the read side. In this chapter we build two query slices — `GetProductById` and `GetProducts` — and in doing so meet DTO mapping, pagination, and the CQRS asymmetry that lets queries skip the domain entirely.

New folders, from the solution root:

```bash
mkdir -p src/ShopSphere.Application/Features/Products/GetProductById \
         src/ShopSphere.Application/Features/Products/GetProducts \
         src/ShopSphere.Application/Common/Models
```

### 14.1 The CQRS Asymmetry (Why Queries Are Different)

Re-read Chapter 10's table with today's eyes: commands *must* flow through guarded domain objects (writes have invariants to honor); queries have no invariants to honor — they answer a question. Dragging full `Product` entities out through HTTP would force every read to materialize tracked, validated domain objects and then expose their guts to the internet. Instead, queries select **exactly the shape the caller needs** into a plain DTO record — faster (no change-tracking, only the needed columns — Chapter 18 implements this with `AsNoTracking`), safer (the Domain's API stays private), and free to evolve with the UI. That is the asymmetry: **writes honor the domain; reads honor the consumer.**

### 14.2 `PagedResult<T>` — The Shared Envelope (Shared Model #1)

Every list endpoint in every real application eventually needs "page 2 of 50-per-page, plus the totals for the pager UI." That envelope is a generic shape with no per-feature knowledge — a legitimate shared model (Chapter 12's rule 2):

```csharp
// File: src/ShopSphere.Application/Common/Models/PagedResult.cs
namespace ShopSphere.Application.Common.Models;

public sealed record PagedResult<T>(
    IReadOnlyList<T> Items,
    int Page,
    int PageSize,
    int TotalCount)
{
    public int TotalPages => PageSize == 0 ? 0 : (int)Math.Ceiling(TotalCount / (double)PageSize);
    public bool HasNextPage => Page < TotalPages;
    public bool HasPreviousPage => Page > 1;
}
```

Line by line:

- **`PagedResult<T>(` … **)** — a **generic record**: `T` is a placeholder for whatever item shape a slice returns (`PagedResult<ProductResponse>`, `PagedResult<CategoryResponse>`). One envelope, every list.
- **`IReadOnlyList<T> Items`** — the page's rows, read-only so no caller mutates the result.
- **`int Page, int PageSize, int TotalCount`** — where we are, how big pages are, how many rows exist in total.
- **`public int TotalPages => ...`** — a computed property (expression-bodied, Chapter 9): total pages = rows ÷ size, rounded **up** — 101 products in pages of 50 need 3 pages, hence `Math.Ceiling` with a cast to `int`. The `PageSize == 0` guard prevents division by zero from a malformed direct call.
- **`HasNextPage` / `HasPreviousPage`** — conveniences for UI pagers, each one expression. The record carries data *and* small derived facts — fine for a DTO (it has no invariants to protect; it *is* pure data).

### 14.3 Slice 1 — `GetProductById`: Message, DTO, Handler

```csharp
// File: src/ShopSphere.Application/Features/Products/GetProductById/GetProductByIdQuery.cs
using MediatR;

namespace ShopSphere.Application.Features.Products.GetProductById;

public sealed record GetProductByIdQuery(Guid Id) : IRequest<ProductResponse>;
```

- Same record shape as a command; `Query` in the name (Chapter 12's law); `IRequest<ProductResponse>` — the response *is* the point of a query. Note `Guid Id` (not nullable): "give me the product with this id"; a malformed GUID in the URL never even binds into the message (Chapter 22).

The slice's DTO — allowed to live in the slice folder (Chapter 12's exception):

```csharp
// File: src/ShopSphere.Application/Features/Products/GetProductById/ProductResponse.cs
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Products.GetProductById;

public sealed record ProductResponse(
    Guid Id,
    string Name,
    string Sku,
    decimal PriceAmount,
    string Currency,
    int StockQuantity,
    Guid? CategoryId,
    string? CategoryName)
{
    public static ProductResponse From(Product product) => new(
        product.Id,
        product.Name,
        product.Sku,
        product.Price.Amount,
        product.Price.Currency,
        product.StockQuantity,
        product.CategoryId,
        product.Category?.Name);
}
```

- **Flat, primitive, consumer-shaped** — the exact opposite of `Product` (Chapter 9): `Price` is flattened to `PriceAmount` + `Currency`, and the navigation `Category` is flattened to `CategoryName`. JSON clients get a simple document; the Domain's design stays internal.
- **`public static ProductResponse From(Product product) => new(...)`** — the **mapping method**, written by hand, visible, debuggable, ~10 lines. This is our answer to mapping libraries: for a handful of fields, explicit mapping is *cheaper to read than a mapping profile is to configure* (Chapter 26 revisits AutoMapper-style tools honestly).
- **`product.Category?.Name`** — null-conditional: if `Category` is null (uncategorized product), `CategoryName` is null — not a crash. Nullable types make the optional-ness flow through the mapping.

```csharp
// File: src/ShopSphere.Application/Features/Products/GetProductById/GetProductByIdQueryHandler.cs
using MediatR;
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Application.Common.Interfaces;

namespace ShopSphere.Application.Features.Products.GetProductById;

public sealed class GetProductByIdQueryHandler(
    IProductRepository productRepository)
    : IRequestHandler<GetProductByIdQuery, ProductResponse>
{
    public async Task<ProductResponse> Handle(
        GetProductByIdQuery request,
        CancellationToken cancellationToken)
    {
        var product = await productRepository.GetByIdAsync(request.Id, cancellationToken)
            ?? throw new NotFoundException(nameof(Product), request.Id);

        return ProductResponse.From(product);
    }
}
```

Three lines of body: **load-or-404** (the `?? throw` idiom from Chapter 13, now second nature), **map**, **return**. Note what is absent: no `SaveChangesAsync` (queries never mutate — and never need the Unit of Work), no try/catch (Chapter 23 owns error translation), no branching. This is what a read use case looks like when nothing fights it.

### 14.4 Slice 2 — `GetProducts`: Searching, Filtering, Paging

The browse endpoint takes optional filters and page parameters:

```csharp
// File: src/ShopSphere.Application/Features/Products/GetProducts/GetProductsQuery.cs
using MediatR;
using ShopSphere.Application.Common.Models;

namespace ShopSphere.Application.Features.Products.GetProducts;

public sealed record GetProductsQuery(
    string? SearchTerm,
    Guid? CategoryId,
    int PageNumber = 1,
    int PageSize = 10) : IRequest<PagedResult<ProductResponse>>;
```

- **`string? SearchTerm` / `Guid? CategoryId`** — optional filters; null means "no filter." Note `SearchTerm`, not `Search` — unabbreviated words are the C# naming law.
- **`int PageNumber = 1, int PageSize = 10)`** — **parameter default values**: a caller sending no paging gets page 1 of 10. Sensible defaults at the boundary protect every consumer.

This slice's response reuses the DTO shape — but Chapter 12 said each slice owns its DTO! `GetProducts` returns a *list* of the same per-product shape, and `ProductResponse` lives in the `GetProductById` folder. Pragmatism: we *reference* it from `GetProducts` (`using ShopSphere.Application.Features.Products.GetProductById;`) rather than duplicate a record field-for-field. When the two shapes eventually diverge (lists usually slimmer — no `CategoryName`, no `Description`...), each slice gets its own DTO and the coupling dies. Knowing when that moment comes is Chapter 26 material.

The query needs a new door in the product port — back to the interface, add one method (the port grows as use cases demand, exactly as promised in 13.2):

```csharp
// Append inside IProductRepository (src/ShopSphere.Application/Common/Interfaces/IProductRepository.cs):
    Task<PagedResult<Product>> GetPagedAsync(
        string? searchTerm,
        Guid? categoryId,
        int pageNumber,
        int pageSize,
        CancellationToken cancellationToken = default);
```

- Returns **domain entities** inside the envelope (`PagedResult<Product>`), not DTOs — a deliberate simplification so the port stays persistence-agnostic and the handler owns the mapping. (In Chapter 26 you'll meet the optimization — repositories projecting straight to DTOs — and when it's worth it.)

```csharp
// File: src/ShopSphere.Application/Features/Products/GetProducts/GetProductsQueryHandler.cs
using MediatR;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Application.Common.Models;
using ShopSphere.Application.Features.Products.GetProductById;

namespace ShopSphere.Application.Features.Products.GetProducts;

public sealed class GetProductsQueryHandler(
    IProductRepository productRepository)
    : IRequestHandler<GetProductsQuery, PagedResult<ProductResponse>>
{
    public async Task<PagedResult<ProductResponse>> Handle(
        GetProductsQuery request,
        CancellationToken cancellationToken)
    {
        var page = await productRepository.GetPagedAsync(
            request.SearchTerm,
            request.CategoryId,
            request.PageNumber,
            request.PageSize,
            cancellationToken);

        var items = page.Items
            .Select(product => ProductResponse.From(product))
            .ToList();

        return new PagedResult<ProductResponse>(
            items,
            page.Page,
            page.PageSize,
            page.TotalCount);
    }
}
```

- **`page.Items.Select(...).ToList()`** — LINQ's `Select` maps each `Product` through the same `ProductResponse.From` (one mapper, used by both read slices), and `ToList()` materializes the sequence so it satisfies `IReadOnlyList<T>`.
- **`return new PagedResult<ProductResponse>(...)`** — the envelope is *rebuilt* around mapped items rather than mapped wholesale: the counts are ints, the items are new objects; explicit beats clever.
- Where is the search/filter/pagination *logic*? Pushed down into the port. The handler stays a pure orchestrator; Infrastructure (Chapter 18) will translate `searchTerm` into a SQL `LIKE` and paging into `OFFSET/FETCH` — with `AsNoTracking`, because reads don't need change tracking (Chapter 16 explains what that saves).

### 14.5 The Query Validator (Yes, Queries Get One)

```csharp
// File: src/ShopSphere.Application/Features/Products/GetProducts/GetProductsQueryValidator.cs
using FluentValidation;

namespace ShopSphere.Application.Features.Products.GetProducts;

public sealed class GetProductsQueryValidator : AbstractValidator<GetProductsQuery>
{
    public GetProductsQueryValidator()
    {
        RuleFor(x => x.PageNumber)
            .GreaterThanOrEqualTo(1).WithMessage("PageNumber must be 1 or greater.");

        RuleFor(x => x.PageSize)
            .InclusiveBetween(1, 100).WithMessage("PageSize must be between 1 and 100.");
    }
}
```

- **Paging bounds are real input rules**: `PageNumber = 0` or `PageSize = 10000` are malformed requests (one breaks the math, the other invites abuse), so the query earns a validator — exactly Chapter 12's "decide per slice." `InclusiveBetween(1, 100)` allows both ends.
- Note the constructor is *not* needed to be parameterless — FluentValidation activates it via DI (Chapter 21); an empty body constructor is fine as written.

### 14.6 Where We Stand

```bash
dotnet build
```

Two query slices compile — and the Application layer still knows nothing of SQL, LIKE, or OFFSET. The port now *demands* five capabilities of persistence; Infrastructure will pay every one of them in Part V.

### Key Takeaways — Chapter 14

- Queries bypass domain behavior by design: load → map → return, no `SaveChangesAsync`, no Unit of Work.
- `ProductResponse` = flat, primitive, consumer-shaped DTO with a hand-written `From(Product)` mapper; `PagedResult<T>` = the one shared generic envelope (items + page + totals + derived pager facts).
- `?? throw new NotFoundException` is the load-or-404 idiom; LINQ `Select` reuses one mapper across slices.
- Filter/paging logic lives behind the port (`GetPagedAsync`); the query validator bounds the paging inputs — queries get validators when inputs have rules.

---

## Chapter 15 — Completing the CRUD: Update, Delete, Categories, and Placing Orders

Four more slices and Part IV is complete. `UpdateProduct` and `DeleteProduct` finish the product story; `CreateCategory`/`GetCategories` prove the recipe generalizes; and `PlaceOrder` — the finale — shows why Unit of Work exists. Along the way the ports grow to their final shape. From the solution root:

```bash
mkdir -p src/ShopSphere.Application/Features/Products/UpdateProduct \
         src/ShopSphere.Application/Features/Products/DeleteProduct \
         src/ShopSphere.Application/Features/Categories/CreateCategory \
         src/ShopSphere.Application/Features/Categories/GetCategories \
         src/ShopSphere.Application/Features/Orders/PlaceOrder
```

### 15.1 `UpdateProduct` — Where Change Tracking Becomes Magic

```csharp
// File: src/ShopSphere.Application/Features/Products/UpdateProduct/UpdateProductCommand.cs
using MediatR;

namespace ShopSphere.Application.Features.Products.UpdateProduct;

public sealed record UpdateProductCommand(
    Guid Id,
    string Name,
    decimal PriceAmount,
    string Currency,
    Guid? CategoryId) : IRequest;
```

One difference from `CreateProductCommand`, one letter of type-level meaning: **`: IRequest;`** (no `<T>`) — the update returns **nothing** (HTTP 204 No Content, Chapter 22). The `Guid Id` first parameter identifies *which* product; the rest is the new state.

```csharp
// File: src/ShopSphere.Application/Features/Products/UpdateProduct/UpdateProductCommandHandler.cs
using MediatR;
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Products.UpdateProduct;

public sealed class UpdateProductCommandHandler(
    IProductRepository productRepository,
    ICategoryRepository categoryRepository,
    IUnitOfWork unitOfWork)
    : IRequestHandler<UpdateProductCommand>
{
    public async Task Handle(UpdateProductCommand request, CancellationToken cancellationToken)
    {
        var product = await productRepository.GetByIdAsync(request.Id, cancellationToken)
            ?? throw new NotFoundException(nameof(Product), request.Id);

        Category? category = null;
        if (request.CategoryId is not null)
        {
            category = await categoryRepository.GetByIdAsync(request.CategoryId.Value, cancellationToken)
                ?? throw new NotFoundException(nameof(Category), request.CategoryId.Value);
        }

        var price = Money.Create(request.PriceAmount, request.Currency);

        product.UpdateDetails(request.Name, price, category);

        await unitOfWork.SaveChangesAsync(cancellationToken);
    }
}
```

New lines, and one *absent* line that matters most:

- **`: IRequestHandler<UpdateProductCommand>`** — the responseless pairing: `Handle` returns plain `Task`, matching `IRequest`.
- **`product.UpdateDetails(...)`** — Chapter 9's guarded behavior does the update: name re-validated, price replaced with a fresh `Money`, category swapped. The entity keeps its own rules during edits.
- **The absent line: there is no `productRepository.Update(product)` call.** Stop and absorb this, because it is the single most surprising fact about working with change-tracking ORMs: when `GetByIdAsync` loaded the product, the infrastructure (Chapter 18) attached it to a *tracked* session; when `UpdateDetails` mutated its properties, the tracker noticed; when `SaveChangesAsync` ran, it diffed original-vs-current and emitted exactly one `UPDATE`. The repository pattern's "attach-and-save" is not needed for *edits* — only *insertions* (explicit `AddAsync`) and *deletions* (next slice) announce themselves. (Repositories that force `Update(entity)` on every edit are re-telling EF what it already knows; Chapter 26 returns to this as a design smell.)
- Everything else is Chapter 13's choreography, replayed.

The validator is Chapter 13's plus an identity rule:

```csharp
// File: src/ShopSphere.Application/Features/Products/UpdateProduct/UpdateProductCommandValidator.cs
using FluentValidation;

namespace ShopSphere.Application.Features.Products.UpdateProduct;

public sealed class UpdateProductCommandValidator : AbstractValidator<UpdateProductCommand>
{
    public UpdateProductCommandValidator()
    {
        RuleFor(x => x.Id).NotEmpty();

        RuleFor(x => x.Name)
            .NotEmpty().WithMessage("Product name is required.")
            .MaximumLength(200);

        RuleFor(x => x.PriceAmount)
            .GreaterThan(0).WithMessage("Price must be greater than zero.");

        RuleFor(x => x.Currency)
            .NotEmpty()
            .Matches("^[A-Z]{3}$").WithMessage("Currency must be a 3-letter ISO 4217 code.");

        RuleFor(x => x.CategoryId)
            .NotEqual(Guid.Empty)
            .When(x => x.CategoryId.HasValue)
            .WithMessage("CategoryId must be a real id when provided.");
    }
}
```

(`RuleFor(x => x.Id).NotEmpty()` rejects `Guid.Empty` — the "no id at all" GUID — before it becomes a pointless database lookup.)

### 15.2 `DeleteProduct` — The Shortest Slice

```csharp
// File: src/ShopSphere.Application/Features/Products/DeleteProduct/DeleteProductCommand.cs
using MediatR;

namespace ShopSphere.Application.Features.Products.DeleteProduct;

public sealed record DeleteProductCommand(Guid Id) : IRequest;
```

```csharp
// File: src/ShopSphere.Application/Features/Products/DeleteProduct/DeleteProductCommandHandler.cs
using MediatR;
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Products.DeleteProduct;

public sealed class DeleteProductCommandHandler(
    IProductRepository productRepository,
    IUnitOfWork unitOfWork)
    : IRequestHandler<DeleteProductCommand>
{
    public async Task Handle(DeleteProductCommand request, CancellationToken cancellationToken)
    {
        var product = await productRepository.GetByIdAsync(request.Id, cancellationToken)
            ?? throw new NotFoundException(nameof(Product), request.Id);

        productRepository.Remove(product);

        await unitOfWork.SaveChangesAsync(cancellationToken);
    }
}
```

- **`productRepository.Remove(product)`** — unlike edits, deletions *must* be announced (a tracker cannot guess a missing row), so the port needs the method. Add it to `IProductRepository` now:

```csharp
// Append inside IProductRepository:
    void Remove(Product product);
```

(A synchronous `void` — removing is a pure in-memory operation: "mark this tracked object for deletion"; the database is only touched at `SaveChangesAsync`. Making it `Task`-returning would falsely advertise I/O.)
- **No validator for this slice** — deliberate: the only input is a route-bound `Guid` (a malformed one never binds, Chapter 22), and `Guid.Empty` simply finds nothing ⇒ 404 from the handler. Chapter 12 said "decide per slice"; here the honest answer is "no rules worth a file." (Add one if you prefer strictness — both are defensible.)

### 15.3 Categories — The Recipe Repeats Verbatim

```csharp
// File: src/ShopSphere.Application/Features/Categories/CreateCategory/CreateCategoryCommand.cs
using MediatR;

namespace ShopSphere.Application.Features.Categories.CreateCategory;

public sealed record CreateCategoryCommand(
    string Name,
    string? Description) : IRequest<Guid>;
```

```csharp
// File: src/ShopSphere.Application/Features/Categories/CreateCategory/CreateCategoryCommandHandler.cs
using MediatR;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Categories.CreateCategory;

public sealed class CreateCategoryCommandHandler(
    ICategoryRepository categoryRepository,
    IUnitOfWork unitOfWork)
    : IRequestHandler<CreateCategoryCommand, Guid>
{
    public async Task<Guid> Handle(
        CreateCategoryCommand request,
        CancellationToken cancellationToken)
    {
        var category = Category.Create(request.Name, request.Description);

        await categoryRepository.AddAsync(category, cancellationToken);
        await unitOfWork.SaveChangesAsync(cancellationToken);

        return category.Id;
    }
}
```

```csharp
// File: src/ShopSphere.Application/Features/Categories/CreateCategory/CreateCategoryCommandValidator.cs
using FluentValidation;

namespace ShopSphere.Application.Features.Categories.CreateCategory;

public sealed class CreateCategoryCommandValidator : AbstractValidator<CreateCategoryCommand>
{
    public CreateCategoryCommandValidator()
    {
        RuleFor(x => x.Name)
            .NotEmpty().WithMessage("Category name is required.")
            .MaximumLength(100);

        RuleFor(x => x.Description)
            .MaximumLength(500);
    }
}
```

Notice: no new concepts — a command, a handler that calls the domain factory, a validator. You could write `UpdateCategory`/`DeleteCategory` yourself right now by pattern-matching against 15.1/15.2 (change the types and the factory call). That flattening of the learning curve is the vertical slice dividend: **slice #7 takes minutes, not days.**

Reading categories needs one more door and a DTO:

```csharp
// Append inside ICategoryRepository:
    Task<List<Category>> GetAllAsync(CancellationToken cancellationToken = default);
```

```csharp
// File: src/ShopSphere.Application/Features/Categories/GetCategories/CategoryResponse.cs
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Categories.GetCategories;

public sealed record CategoryResponse(Guid Id, string Name, string? Description)
{
    public static CategoryResponse From(Category category) =>
        new(category.Id, category.Name, category.Description);
}
```

```csharp
// File: src/ShopSphere.Application/Features/Categories/GetCategories/GetCategoriesQuery.cs
using MediatR;

namespace ShopSphere.Application.Features.Categories.GetCategories;

public sealed record GetCategoriesQuery() : IRequest<List<CategoryResponse>>;
```

```csharp
// File: src/ShopSphere.Application/Features/Categories/GetCategories/GetCategoriesQueryHandler.cs
using MediatR;
using ShopSphere.Application.Common.Interfaces;

namespace ShopSphere.Application.Features.Categories.GetCategories;

public sealed class GetCategoriesQueryHandler(
    ICategoryRepository categoryRepository)
    : IRequestHandler<GetCategoriesQuery, List<CategoryResponse>>
{
    public async Task<List<CategoryResponse>> Handle(
        GetCategoriesQuery request,
        CancellationToken cancellationToken)
    {
        var categories = await categoryRepository.GetAllAsync(cancellationToken);

        return categories
            .Select(CategoryResponse.From)
            .ToList();
    }
}
```

- **`GetCategoriesQuery()`** — a parameterless record: "all categories, no inputs." (`()` is required in record declarations with no parameters.)
- **`.Select(CategoryResponse.From)`** — a **method group**: passing the method itself instead of `c => CategoryResponse.From(c)`; the compiler converts it to a delegate. Shorter, and it *is* the same LINQ map you already know.
- A short category list needs no paging envelope — `List<CategoryResponse>` is honest and sufficient. Add `PagedResult` the day the catalog grows.

### 15.4 `PlaceOrder` — Why Unit of Work Exists

Everything so far touched one entity per use case. Orders touch *many*: every ordered product's stock changes, an order is created, its line items are created — and the business demands **all of it happens or none of it does**. This is the slice the Unit of Work port was born for.

Two new doors first — a brand-new port for orders, and a batch loader for products:

```csharp
// File: src/ShopSphere.Application/Common/Interfaces/IOrderRepository.cs
using ShopSphere.Domain.Orders;

namespace ShopSphere.Application.Common.Interfaces;

public interface IOrderRepository
{
    Task AddAsync(Order order, CancellationToken cancellationToken = default);

    Task<Order?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default);
}
```

```csharp
// Append inside IProductRepository:
    Task<IReadOnlyList<Product>> GetByIdsAsync(
        IReadOnlyList<Guid> ids,
        CancellationToken cancellationToken = default);
```

(`GetByIdsAsync` loads *many* products in **one** SQL `WHERE ... IN (...)` query — one round-trip instead of one per line, a habit that matters the day your orders have twenty lines. `GetByIdAsync` on `IOrderRepository` is the read door for the future "get order details" slice — and Chapter 24's tests.)

The message, with its first **nested** DTO:

```csharp
// File: src/ShopSphere.Application/Features/Orders/PlaceOrder/PlaceOrderCommand.cs
using MediatR;

namespace ShopSphere.Application.Features.Orders.PlaceOrder;

public sealed record PlaceOrderCommand(
    string CustomerName,
    string ShippingAddress,
    IReadOnlyList<OrderLineRequest> Lines) : IRequest<Guid>;

public sealed record OrderLineRequest(Guid ProductId, int Quantity);
```

- **`IReadOnlyList<OrderLineRequest> Lines`** — a collection inside a message; the JSON is `{"customerName": "...", "shippingAddress": "...", "lines": [{"productId": "...", "quantity": 2}, ...]}`. The record list is read-only *as a list* (no `.Add` after creation) — immutable enough for a message.
- **`OrderLineRequest`** — a second record in the same file: a tiny input shape used only by this message, so it lives beside it rather than in `Common/`. Nested-by-proximity, exactly like `Order.OrderLine` nested by ownership in Chapter 9.

The validator introduces **collection rules**:

```csharp
// File: src/ShopSphere.Application/Features/Orders/PlaceOrder/PlaceOrderCommandValidator.cs
using FluentValidation;

namespace ShopSphere.Application.Features.Orders.PlaceOrder;

public sealed class PlaceOrderCommandValidator : AbstractValidator<PlaceOrderCommand>
{
    public PlaceOrderCommandValidator()
    {
        RuleFor(x => x.CustomerName)
            .NotEmpty().WithMessage("Customer name is required.")
            .MaximumLength(200);

        RuleFor(x => x.ShippingAddress)
            .NotEmpty().WithMessage("Shipping address is required.")
            .MaximumLength(500);

        RuleFor(x => x.Lines)
            .NotEmpty().WithMessage("An order requires at least one line.");

        RuleForEach(x => x.Lines).ChildRules(line =>
        {
            line.RuleFor(l => l.ProductId)
                .NotEqual(Guid.Empty).WithMessage("Each line needs a valid ProductId.");

            line.RuleFor(l => l.Quantity)
                .GreaterThan(0).WithMessage("Quantity must be at least 1.");
        });
    }
}
```

- **`RuleFor(x => x.Lines).NotEmpty()`** — validates the *collection itself*: present and non-empty. An order of zero lines dies here, with a clear 400 — *before* Chapter 9's `Order.Place` would refuse it (which remains the deep guarantee).
- **`RuleForEach(x => x.Lines).ChildRules(line => { ... })`** — FluentValidation's door *into* each element: inside the braces, `line` is a mini-validator-builder for one `OrderLineRequest`, declaring the same per-line rules twice more at the boundary (shape) and in the domain (context — Chapter 8's two questions, now with collections).

And the handler where everything meets:

```csharp
// File: src/ShopSphere.Application/Features/Orders/PlaceOrder/PlaceOrderCommandHandler.cs
using MediatR;
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Orders;
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.Features.Orders.PlaceOrder;

public sealed class PlaceOrderCommandHandler(
    IProductRepository productRepository,
    IOrderRepository orderRepository,
    IUnitOfWork unitOfWork)
    : IRequestHandler<PlaceOrderCommand, Guid>
{
    public async Task<Guid> Handle(PlaceOrderCommand request, CancellationToken cancellationToken)
    {
        var productIds = request.Lines
            .Select(line => line.ProductId)
            .Distinct()
            .ToList();

        var products = await productRepository.GetByIdsAsync(productIds, cancellationToken);
        var productsById = products.ToDictionary(product => product.Id);

        var orderLines = new List<Order.OrderLine>(request.Lines.Count);
        foreach (var line in request.Lines)
        {
            var product = productsById.GetValueOrDefault(line.ProductId)
                ?? throw new NotFoundException(nameof(Product), line.ProductId);

            orderLines.Add(new Order.OrderLine(product, line.Quantity));
        }

        var order = Order.Place(request.CustomerName, request.ShippingAddress, orderLines);

        await orderRepository.AddAsync(order, cancellationToken);

        await unitOfWork.SaveChangesAsync(cancellationToken);

        return order.Id;
    }
}
```

Line by line — this is the climax of Part IV:

- **`request.Lines.Select(...).Distinct().ToList()`** — gather the product ids, **deduplicate** (someone ordering the same t-shirt as two lines would otherwise load it twice), materialize. `.ToList()` because LINQ is lazy: without it, nothing has run yet (Chapter 26 revisits laziness).
- **`products.ToDictionary(product => product.Id)`** — build an id → product lookup: O(1) access by key instead of a `.First(...)` scan per line. Small line, real algorithm choice.
- **`productsById.GetValueOrDefault(line.ProductId) ?? throw new NotFoundException(...)`** — `GetValueOrDefault` returns `null` for a missing key (no exception like the indexer would throw); the `?? throw` translates "id not in the shop" into the 404 vocabulary. Every ordered product is now *known to exist*.
- **`new Order.OrderLine(product, line.Quantity)`** — input DTOs become domain lines (the nested record from Chapter 9). Note the direction of translation: **outside-in** (request → domain), always at the handler, never inside entities.
- **`Order.Place(...)`** — Chapter 9's aggregate factory runs the whole business ceremony: names validated, `DecreaseStock` called on *each* product through the guarded door (insufficient stock ⇒ `DomainException` ⇒ nothing further happens), items snapshotted with prices. If line 3 of 3 fails, lines 1–2 have *already mutated their products in memory* — and it doesn't matter, because…
- **`orderRepository.AddAsync(order, ...)` registers the order; `unitOfWork.SaveChangesAsync(...)` commits everything.** Here is the payoff: the *same single call* writes the `UPDATE` for every product's new stock, the `INSERT` for the order, and the `INSERT`s for all its items — **in one database transaction**. Either the whole order exists and every stock is correct, or none of it happened. No partial orders, no negative stock, no compensation logic — that is the Unit of Work pattern doing exactly what Chapter 4 promised, and (Chapter 19 will prove) it is not even hard to implement.
- **`return order.Id;`** — one GUID summarizes a dozen rows of work.

### 15.5 The Full Slice Inventory

```bash
dotnet build
```

The Application layer is now complete:

```
Features/
├── Products/   CreateProduct   UpdateProduct   DeleteProduct   GetProductById   GetProducts
├── Categories/ CreateCategory  GetCategories
└── Orders/     PlaceOrder
```

Eight use cases, eight folders, twenty-two files, every one of them explained. The ports have grown to their final form — `IProductRepository` (add, get-by-id, get-by-ids, paged, sku-exists, remove), `ICategoryRepository` (get-by-id, get-all, add), `IOrderRepository` (add, get-by-id), `IUnitOfWork` (save). Part V now exists to keep every promise those four interfaces make.

### Key Takeaways — Chapter 15

- Responseless messages: `IRequest` + `IRequestHandler<T>` — right for update/delete (HTTP 204).
- Updates need **no `Update()` call**: loaded entities are tracked; `SaveChangesAsync` diffs and writes. Deletes *do* announce themselves (`Remove`), inserts too (`AddAsync`).
- Collection input: nested DTO records + `RuleFor(...).NotEmpty()` + `RuleForEach(...).ChildRules(...)` for per-element rules.
- `PlaceOrder` is the Unit of Work thesis: many aggregates mutated, **one** `SaveChangesAsync`, one transaction, zero partial states.
- The recipe is now muscle memory: message → handler (load → domain → persist → return) → validator. Repetition is the point.

---

# PART V — THE INFRASTRUCTURE LAYER

> Time to pay the port bills. Part V builds everything the Application layer demanded in Chapters 13–15: a real `DbContext`, fluent mappings for every entity, the repository implementations, the Unit of Work that commits atomically — and then, with migrations and seeding, an actual SQL Server database comes into existence.

---

## Chapter 16 — EF Core From Zero: The ORM Explained

### 16.1 What an ORM Actually Does

**Entity Framework Core** is an **Object-Relational Mapper**: a translator standing between C# objects and SQL Server tables. Four jobs, all visible in this chapter and the next two:

1. **Query translation** — you write LINQ (`Where(p => p.Sku == sku)`); EF compiles it to SQL (`SELECT ... WHERE [Sku] = @sku`), sends it, and brings rows back.
2. **Materialization** — rows become C# objects: EF calls our `private` parameterless constructors (Chapter 9's mysterious doors!) and fills the properties — which is why we wrote them.
3. **Change tracking** — every loaded entity is remembered with its original values; at save time EF diffs original vs. current and emits only the necessary `INSERT`/`UPDATE`/`DELETE` statements. This is the machinery behind Chapter 15's "no `Update()` call" trick.
4. **Schema management** — migrations (Chapter 20) turn your entity model into `CREATE TABLE` scripts.

What EF is *not*: a magic layer that makes the database disappear. It *is* SQL Server under there, and Part VII's mastery chapters teach you to remember it (N+1 queries, tracking overhead, index awareness).

### 16.2 `AppDbContext` — Our Database Session

```bash
mkdir -p src/ShopSphere.Infrastructure/Persistence/Configurations \
         src/ShopSphere.Infrastructure/Repositories
```

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/AppDbContext.cs
using Microsoft.EntityFrameworkCore;
using ShopSphere.Domain.Orders;
using ShopSphere.Domain.Products;

namespace ShopSphere.Infrastructure.Persistence;

public sealed class AppDbContext(DbContextOptions<AppDbContext> options) : DbContext(options)
{
    public DbSet<Product> Products => Set<Product>();

    public DbSet<Category> Categories => Set<Category>();

    public DbSet<Order> Orders => Set<Order>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);
    }
}
```

Line by line:

- **`public sealed class AppDbContext(DbContextOptions<AppDbContext> options) : DbContext(options)`** — a primary constructor (Chapter 13's DI pattern, here used by EF's own factory) passing **`DbContextOptions<AppDbContext>`** to the base `DbContext`. The options object carries the provider (SQL Server) and connection string — injected from outside (Chapter 21 wires this). This is EF's version of Dependency Inversion: `AppDbContext` never reads a config file; it *receives* configuration.
- **`public DbSet<Product> Products => Set<Product>();`** — a **`DbSet<T>`** is the typed doorway to one table: queryable (`Products.Where(...)`, `Products.FindAsync(...)`) and the target of `Add`/`Remove`. The expression body calls `Set<T>()`, a base-class helper that resolves the set for that entity type. Three `DbSet`s for three roots — and notice what is *deliberately missing*: **no `DbSet<OrderItem>`**. Order items live inside the `Order` aggregate (Chapter 9); advertising them as a top-level collection would invite someone to query and mutate them directly, breaking the aggregate boundary. `OrderItem` still gets *mapped* (Chapter 17, via the order's configuration) — it's just not given a public door.
- **`protected override void OnModelCreating(ModelBuilder modelBuilder)`** — the model-building hook: EF calls it once (first use) to learn "what tables exist, what columns, what keys." Everything inside describes the mapping from entities to schema — our Chapter 17 file lives for this method.
- **`modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly)`** — the one-line loader: *scan this assembly for every class implementing `IEntityTypeConfiguration<T>` and apply them all*. This is why configuration files "just work" without registration lists. `typeof(AppDbContext).Assembly` is the idiomatic "this assembly" (`Assembly.GetExecutingAssembly()` behaves identically; the `typeof` form is the modern idiom).
- **`base.OnModelCreating(modelBuilder);`** — politeness first: run EF's own defaults before our customizations layer on top.

### 16.3 The Model: Where Entities Meet Tables

After `OnModelCreating` runs, EF holds an in-memory **model**: every entity type → its table, every property → its column, plus keys, relationships, indexes. This model is what migrations read (Chapter 20) and what query translation consults at runtime. Our policy, following Clean Architecture: **the Domain declares *what* the data means; Infrastructure declares *how it is stored*** — which is why Chapter 17's configuration classes live *here*, not as attributes on the entities (data annotations like `[MaxLength]` would drag an EF package reference into Domain — forbidden by Chapter 8's list).

### 16.4 A DbContext's Life (and Why It's Scoped)

A `DbContext` is a **unit of work in embryo**: cheap to create, holds a connection, tracks loaded entities, meant to be **short-lived and single-threaded**. In ASP.NET Core, DI gives each HTTP request **one** `AppDbContext` (a *scoped* lifetime — Chapter 21 registers it), so every repository a request touches shares the *same* instance — which is exactly what will make "one `SaveChangesAsync` commits all changes from all repositories" true (Chapter 19). Never share a DbContext across requests, never use it from parallel tasks; it is not thread-safe, by design.

### 16.5 How a Query Flows (30-Second Tour)

When Chapter 18's repository runs `Products.SingleOrDefaultAsync(p => p.Sku == sku, ct)`:

1. EF parses the expression tree `p => p.Sku == sku` (LINQ is code-as-data),
2. translates it against the model → `SELECT TOP(2) ... FROM [Products] WHERE [Sku] = @__sku_0`,
3. opens (or reuses) the SQL Server connection, executes,
4. materializes each returned row by invoking `Product`'s **private** constructor and setting properties — even `private set` ones — through reflection-compiled accessors,
5. stores a snapshot of original values in the **change tracker**, hands you the object.

Every future mutation you make through the guarded domain methods is noticed by step 5's tracker; `SaveChangesAsync` walks the tracker's four states — `Added` (from `Add`), `Unchanged`, `Modified` (diff detected), `Deleted` (from `Remove`) — and batches exactly the right SQL.

### Key Takeaways — Chapter 16

- EF Core: LINQ→SQL translation, materialization via private constructors, change tracking (the diff engine behind "no `Update()` call"), and migrations.
- `AppDbContext` receives `DbContextOptions` from outside (DI), exposes `DbSet`s for **aggregate roots only** (no `OrderItem` door), and auto-loads all `IEntityTypeConfiguration<T>` classes from the assembly.
- One scoped `DbContext` per request, shared by all repositories — the seed of the Unit of Work.
- Mapping knowledge lives in Infrastructure (fluent configuration), never as attributes in Domain.

---

## Chapter 17 — Mapping the Domain to SQL Server: Fluent Configurations

### 17.1 Why "Fluent" Configuration

Each entity gets a configuration class implementing **`IEntityTypeConfiguration<T>`** with one `Configure(EntityTypeBuilder<T> builder)` method, chaining calls that read like sentences: `builder.Property(p => p.Name).HasMaxLength(200).IsRequired();`. Three wins: zero pollution of Domain, complete control of every column/index/relationship, and per-entity files that review like the slices they mirror. Create the two simple files first:

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/Configurations/CategoryConfiguration.cs
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using ShopSphere.Domain.Products;

namespace ShopSphere.Infrastructure.Persistence.Configurations;

public sealed class CategoryConfiguration : IEntityTypeConfiguration<Category>
{
    public void Configure(EntityTypeBuilder<Category> builder)
    {
        builder.ToTable("Categories");

        builder.HasKey(category => category.Id);

        builder.Property(category => category.Name)
            .HasMaxLength(100)
            .IsRequired();

        builder.Property(category => category.Description)
            .HasMaxLength(500);

        builder.Property(category => category.CreatedAtUtc);
        builder.Property(category => category.UpdatedAtUtc);
    }
}
```

- **`: IEntityTypeConfiguration<Category>`** — the contract `ApplyConfigurationsFromAssembly` hunts for. Generic on the entity, so the builder inside is already typed: `category => category.Name` lambdas are compile-checked.
- **`builder.ToTable("Categories")`** — the table name; explicit plural (the default would also be `Categories`, but we say it out loud — mapping files are the documentation of the database).
- **`builder.HasKey(category => category.Id)`** — primary key on `Id` (a `uniqueidentifier` column in SQL Server; Chapter 20's migration will show it).
- **`.HasMaxLength(100).IsRequired()`** — column shape: `nvarchar(100)` + `NOT NULL`. These mirror the Domain's rules *and* back them: even a bug elsewhere cannot store a 300-character name. (The DB as the last line of defense — Chapter 13 promised defense in depth.)
- **Audit columns mapped with plain `Property(...)`** — no length rules needed; `DateTime`/`DateTime?` map to `datetime2`/`NULL datetime2`.

### 17.2 `ProductConfiguration` — Owned Money, Unique SKU, Check Constraint

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/Configurations/ProductConfiguration.cs
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using ShopSphere.Domain.Products;

namespace ShopSphere.Infrastructure.Persistence.Configurations;

public sealed class ProductConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.ToTable("Products", table =>
        {
            table.HasCheckConstraint("CK_Products_StockQuantity", "[StockQuantity] >= 0");
        });

        builder.HasKey(product => product.Id);

        builder.Property(product => product.Name)
            .HasMaxLength(200)
            .IsRequired();

        builder.Property(product => product.Sku)
            .HasMaxLength(20)
            .IsRequired();

        builder.HasIndex(product => product.Sku)
            .IsUnique()
            .HasDatabaseName("IX_Products_Sku");

        builder.Property(product => product.StockQuantity);

        builder.OwnsOne(product => product.Price, moneyBuilder =>
        {
            moneyBuilder.Property(money => money.Amount)
                .HasPrecision(18, 2)
                .HasColumnName("PriceAmount");

            moneyBuilder.Property(money => money.Currency)
                .IsUnicode(false)
                .HasMaxLength(3)
                .HasColumnName("PriceCurrency");
        });

        builder.HasOne(product => product.Category)
            .WithMany()
            .HasForeignKey(product => product.CategoryId)
            .OnDelete(DeleteBehavior.Restrict);

        builder.Property(product => product.CreatedAtUtc);
        builder.Property(product => product.UpdatedAtUtc);
    }
}
```

New vocabulary, in file order:

- **`builder.ToTable("Products", table => { table.HasCheckConstraint(...); })`** — the second argument customizes the table itself; **`HasCheckConstraint`** adds a raw SQL rule the database enforces on every write: `[StockQuantity] >= 0`. The third wall of defense (validator → entity → database): even a bug that bypassed our code could not create negative stock. (Check constraints are raw SQL strings — one of the few places; keep them rare and named, as here, `CK_Products_StockQuantity`.)
- **`builder.HasIndex(product => product.Sku).IsUnique().HasDatabaseName("IX_Products_Sku")`** — a **unique index**: the database itself rejects duplicate SKUs. This is the *real* guarantee behind Chapter 13's `SkuExistsAsync` pre-check — remember, two concurrent requests can both pass a pre-check and race; only the index catches the loser. Indexes also speed equality lookups — this one is a two-fer.
- **`builder.OwnsOne(product => product.Price, moneyBuilder => { ... })`** — the star of the file: an **owned entity type**. `Money` has no table of its own and no identity of its own (it's a value object!); it is *owned* by `Product` and stored as **columns flattened into the owner's table** — here `PriceAmount decimal(18,2)` and `PriceCurrency char(3)`. The lambda configures the owned columns: `HasPrecision(18, 2)` = 18 total digits, 2 after the decimal point (the standard commercial-money shape); `IsUnicode(false)` stores ASCII `char(3)` instead of `nvarchar` (cheaper, exact for currency codes); `HasColumnName(...)` names the columns explicitly.
- **`builder.HasOne(product => product.Category).WithMany().HasForeignKey(product => product.CategoryId).OnDelete(DeleteBehavior.Restrict)`** — the relationship, read as English: "a product **has one** category; the category side **has many** products; the foreign key is `CategoryId`; and deleting a category that still has products is **forbidden** (`Restrict` = SQL Server raises an error rather than cascading)." Two deliberate decisions hide here: (1) `WithMany()` is **parameterless on purpose** — our `Category` class has *no* `Products` collection (Chapter 9 kept it blissfully unaware of products), so we tell EF "the other side exists but exposes no navigation back" — a one-directional relationship, which is good encapsulation; (2) `Restrict` is a data-safety choice: no accidental mass-deletion of products because someone removed a category.

### 17.3 `OrderConfiguration` — Aggregates, Encapsulated Collections, Shadows

The most advanced file in the book — read it twice:

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/Configurations/OrderConfiguration.cs
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using ShopSphere.Domain.Orders;

namespace ShopSphere.Infrastructure.Persistence.Configurations;

public sealed class OrderConfiguration : IEntityTypeConfiguration<Order>
{
    public void Configure(EntityTypeBuilder<Order> builder)
    {
        builder.ToTable("Orders");

        builder.HasKey(order => order.Id);

        builder.Property(order => order.CustomerName)
            .HasMaxLength(200)
            .IsRequired();

        builder.Property(order => order.ShippingAddress)
            .HasMaxLength(500)
            .IsRequired();

        builder.Property(order => order.OrderDateUtc);

        builder.Property(order => order.Status)
            .HasConversion<int>();

        builder.Navigation(order => order.Items)
            .UsePropertyAccessMode(PropertyAccessMode.Field);

        builder.OwnsMany(order => order.Items, itemsBuilder =>
        {
            itemsBuilder.ToTable("OrderItems");

            itemsBuilder.WithOwner()
                .HasForeignKey("OrderId");

            itemsBuilder.HasKey("OrderId", "ProductId");

            itemsBuilder.Property("OrderId");

            itemsBuilder.Property(item => item.Quantity);

            itemsBuilder.OwnsOne(item => item.UnitPrice, moneyBuilder =>
            {
                moneyBuilder.Property(money => money.Amount)
                    .HasPrecision(18, 2)
                    .HasColumnName("UnitPriceAmount");

                moneyBuilder.Property(money => money.Currency)
                    .IsUnicode(false)
                    .HasMaxLength(3)
                    .HasColumnName("UnitPriceCurrency");
            });

            itemsBuilder.HasOne(item => item.Product)
                .WithMany()
                .HasForeignKey(item => item.ProductId)
                .OnDelete(DeleteBehavior.Restrict);
        });

        builder.Property(order => order.CreatedAtUtc);
        builder.Property(order => order.UpdatedAtUtc);
    }
}
```

Line by line, the new ideas:

- **`builder.Property(order => order.Status).HasConversion<int>();`** — the `OrderStatus` enum is stored as its integer (`Pending` = 1 per Chapter 9's pinned numbers). EF can do this by convention; stating it documents the contract explicitly (the database column is `int`, and the numbers are stable).
- **`builder.Navigation(order => order.Items).UsePropertyAccessMode(PropertyAccessMode.Field)`** — the line that *marries EF to our encapsulation*: EF must read and write the items through the **private backing field `_items`** (Chapter 9), not through the public `IReadOnlyCollection` (which has no `Add` EF could use). With field access mode, `SaveChanges` adds rows by writing the list itself — the aggregate stays sealed while still being persisted.
- **`builder.OwnsMany(order => order.Items, itemsBuilder => { ... })`** — the aggregate mapped: `OrderItem`s are **owned** by `Order` (no independent identity — value semantics at the aggregate level). Everything inside the lambda configures the child table:
  - **`itemsBuilder.ToTable("OrderItems")`** — they do get their *own table* (a separate row per line; owning controls identity/lifetime, not necessarily storage layout — owned types are "table splitting" only when single; `OwnsMany` always creates a separate table).
  - **`itemsBuilder.WithOwner().HasForeignKey("OrderId")`** — the **shadow foreign key**: `OrderItem` has no `OrderId` property in the Domain (it doesn't need one — it lives inside its order), so EF creates the column invisibly, by name.
  - **`itemsBuilder.HasKey("OrderId", "ProductId")`** — the **composite primary key**: an item is identified by *which order* + *which product* — exactly what Chapter 9 said identity means for order lines. (No surrogate `Id` column: honest identity, no wasted bytes.)
  - **`itemsBuilder.Property(item => item.Quantity)`** — the line's quantity, mapped by the typed lambda as usual. Contrast it with the `"OrderId"` strings above: the string form (`Property<T>("name")`) exists for **shadow properties** — columns with no C# property behind them; wherever a real property exists, prefer the lambda form, as here.
  - **`itemsBuilder.OwnsOne(item => item.UnitPrice, ...)`** — the same `Money` value object, owned a second time, with different column names (`UnitPriceAmount`, `UnitPriceCurrency`): value objects are *reusable precisely because* ownership is per-relationship. Two mappings, zero coupling.
  - **`itemsBuilder.HasOne(item => item.Product).WithMany().HasForeignKey(...).OnDelete(DeleteBehavior.Restrict)`** — each line references its product (a cross-aggregate reference — always **by id**, and never cascade-deleted: deleting a product must not silently delete historical order lines; `Restrict` forbids it).
- Notice again: **no `DbSet<OrderItem>`**, no public `OrderItemConfiguration` file — the child is entirely configured from within its parent's boundary, enforced by the compiler. Infrastructure respects the aggregate even though EF itself would happily let you cheat.

### 17.4 The Mental Model of the Resulting Schema

Four tables, one per storage unit — run this picture in your head (and see it *literally* in Chapter 20):

```
Categories(Id PK, Name, Description, CreatedAtUtc, UpdatedAtUtc)

Products(Id PK, Name, Sku UNIQUE, PriceAmount, PriceCurrency,
         StockQuantity CHECK >= 0, CategoryId FK → Categories,   -- Restrict on delete
         CreatedAtUtc, UpdatedAtUtc)

Orders(Id PK, CustomerName, ShippingAddress, OrderDateUtc, Status int,
       CreatedAtUtc, UpdatedAtUtc)

OrderItems(OrderId PK/FK → Orders, ProductId PK/FK → Products,   -- composite key
           Quantity, UnitPriceAmount, UnitPriceCurrency)         -- Restrict on delete
```

Every domain rule from Chapter 9 now has a column, key, or constraint mirroring it in SQL Server. `dotnet build` compiles the mapping; Chapter 18 makes it move.

### Key Takeaways — Chapter 17

- One `IEntityTypeConfiguration<T>` per entity, auto-loaded by `ApplyConfigurationsFromAssembly` — mapping in Infrastructure, never attributes in Domain.
- `OwnsOne`/`OwnsMany` map value objects and aggregate children: flattened columns or owned rows, no independent identity, reusable (`Money` mapped twice).
- Guard rails: unique index (SKU), check constraint (stock ≥ 0), `DeleteBehavior.Restrict` (no cascades), composite key for order lines, shadow FK where the domain has no property.
- `Navigation(...).UsePropertyAccessMode(PropertyAccessMode.Field)` lets EF write the private `_items` list — encapsulation and persistence coexist.

---

## Chapter 18 — Repositories: The Contract in Application, the Implementation in Infrastructure

Eight slices have been making promises to four interfaces. Now Infrastructure pays up. Every repository below is the same shape: constructor-received `AppDbContext`, LINQ translated to SQL, entities crossing back over the boundary as `Domain` types. Put the three files in `Repositories/`.

### 18.1 `ProductRepository` — Line by Line

```csharp
// File: src/ShopSphere.Infrastructure/Repositories/ProductRepository.cs
using Microsoft.EntityFrameworkCore;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Application.Common.Models;
using ShopSphere.Domain.Products;
using ShopSphere.Infrastructure.Persistence;

namespace ShopSphere.Infrastructure.Repositories;

public sealed class ProductRepository(AppDbContext context) : IProductRepository
{
    public async Task AddAsync(Product product, CancellationToken cancellationToken = default)
    {
        await context.Products.AddAsync(product, cancellationToken);
    }

    public async Task<Product?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return await context.Products
            .Include(product => product.Category)
            .FirstOrDefaultAsync(product => product.Id == id, cancellationToken);
    }

    public async Task<IReadOnlyList<Product>> GetByIdsAsync(
        IReadOnlyList<Guid> ids,
        CancellationToken cancellationToken = default)
    {
        return await context.Products
            .Include(product => product.Category)
            .Where(product => ids.Contains(product.Id))
            .ToListAsync(cancellationToken);
    }

    public async Task<bool> SkuExistsAsync(string sku, CancellationToken cancellationToken = default)
    {
        return await context.Products
            .AsNoTracking()
            .AnyAsync(product => product.Sku == sku, cancellationToken);
    }

    public async Task<PagedResult<Product>> GetPagedAsync(
        string? searchTerm,
        Guid? categoryId,
        int pageNumber,
        int pageSize,
        CancellationToken cancellationToken = default)
    {
        var query = context.Products
            .AsNoTracking()
            .Include(product => product.Category)
            .AsQueryable();

        if (!string.IsNullOrWhiteSpace(searchTerm))
        {
            query = query.Where(product =>
                product.Name.Contains(searchTerm) || product.Sku.Contains(searchTerm));
        }

        if (categoryId is not null)
        {
            query = query.Where(product => product.CategoryId == categoryId);
        }

        var totalCount = await query.CountAsync(cancellationToken);

        var items = await query
            .OrderBy(product => product.Name)
            .Skip((pageNumber - 1) * pageSize)
            .Take(pageSize)
            .ToListAsync(cancellationToken);

        return new PagedResult<Product>(items, pageNumber, pageSize, totalCount);
    }

    public void Remove(Product product)
    {
        context.Products.Remove(product);
    }
}
```

Line by line — the whole persistence vocabulary of this book is in this one file:

- **`public sealed class ProductRepository(AppDbContext context) : IProductRepository`** — the payoff line of Part V: an **outer-circle class implementing an inner-circle interface** (Chapter 5's inversion, made real). The primary constructor receives `AppDbContext` — the *scoped, per-request* instance (Chapter 16) shared with every other repository and the Unit of Work.
- **`AddAsync` → `await context.Products.AddAsync(product, ...)`** — announces the product to the change tracker as `EntityState.Added`. No SQL yet (Chapter 15's promise: `Add` = intent, not execution). *Why async then?* EF's `AddAsync` supports value generators that may hit I/O (HiLo sequences); with client-generated GUIDs it completes synchronously, but the async signature keeps the pattern uniform.
- **`GetByIdAsync` → `Include(...).FirstOrDefaultAsync(...)`** — two ideas:
  - **`.Include(product => product.Category)`** — **eager loading**: the FK relationship is only *ids* until you ask for the related rows; `Include` adds a SQL `JOIN` (one round-trip) so returned products carry their `Category` — which is why `ProductResponse.From` could read `product.Category?.Name` in Chapter 14. Forgetting `Include` is the classic N+1 bug (or a `NullReferenceException` waiting for a lazy-loading config we don't use); the repository is precisely where this decision lives so handlers never think about it.
  - **`FirstOrDefaultAsync`** — translated to `SELECT TOP(1) ... WHERE [Id] = @id`. Returns `Product?` — `null` means "not found," typed at the boundary (the handler's `?? throw` finishes the translation to 404).
- **`GetByIdsAsync` → `.Where(product => ids.Contains(product.Id))`** — a `Guid` list `Contains` translates to SQL `WHERE [Id] IN (@id1, @id2, ...)`: **one query** for any number of ids (Chapter 15's batch-load promise kept).
- **`SkuExistsAsync` → `.AsNoTracking().AnyAsync(...)`** — two micro-decisions worth stealing: **`AsNoTracking()`** skips the change-tracker snapshot (read-only queries don't need diffing — measurably less memory and CPU); **`AnyAsync`** translates to `SELECT TOP(1) CASE WHEN EXISTS(...)` — it stops at the first match instead of hauling the whole row.
- **`GetPagedAsync`** — the query-builder pattern:
  - Start `AsNoTracking().Include(...).AsQueryable()` and hold the **`IQueryable`** (a *not-yet-executed* query — composable).
  - **Conditional `.Where` chains** apply only when the filter is present — search on `Name` **or** `Sku` (C# `.Contains` on strings translates to SQL `LIKE '%term%'` — note for mastery: a leading-wildcard `LIKE` cannot use a plain index; at real scale you'd add full-text search), and category equality when chosen.
  - **`CountAsync`** first (one `SELECT COUNT(*)` with the same filters — the pager's `TotalCount`).
  - Then **`.OrderBy(...).Skip((pageNumber - 1) * pageSize).Take(pageSize)`** — ordering is *mandatory* before paging (SQL without ORDER BY has no guaranteed row order, so OFFSET would be meaningless); `Skip/Take` translate to `OFFSET n ROWS FETCH NEXT m ROWS ONLY`. `(pageNumber - 1) * pageSize` is page math in one expression: page 1 skips 0.
  - **`ToListAsync`** materializes rows into entities, wrapped in the shared `PagedResult<Product>` envelope.
- **`Remove`** — `Remove(product)` marks the *tracked* object `EntityState.Deleted`; `void` because nothing touches the database (Chapter 15's contract).

### 18.2 `CategoryRepository` and `OrderRepository` — Same Pattern, New Include

```csharp
// File: src/ShopSphere.Infrastructure/Repositories/CategoryRepository.cs
using Microsoft.EntityFrameworkCore;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Products;
using ShopSphere.Infrastructure.Persistence;

namespace ShopSphere.Infrastructure.Repositories;

public sealed class CategoryRepository(AppDbContext context) : ICategoryRepository
{
    public async Task<Category?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return await context.Categories
            .FirstOrDefaultAsync(category => category.Id == id, cancellationToken);
    }

    public async Task AddAsync(Category category, CancellationToken cancellationToken = default)
    {
        await context.Categories.AddAsync(category, cancellationToken);
    }

    public async Task<List<Category>> GetAllAsync(CancellationToken cancellationToken = default)
    {
        return await context.Categories
            .AsNoTracking()
            .OrderBy(category => category.Name)
            .ToListAsync(cancellationToken);
    }
}
```

Nothing new — but notice `GetByIdAsync` here has **no** `Include`: categories have no navigations worth loading (Chapter 9's design echoing into queries). Repositories know their aggregates' shape.

```csharp
// File: src/ShopSphere.Infrastructure/Repositories/OrderRepository.cs
using Microsoft.EntityFrameworkCore;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Orders;
using ShopSphere.Infrastructure.Persistence;

namespace ShopSphere.Infrastructure.Repositories;

public sealed class OrderRepository(AppDbContext context) : IOrderRepository
{
    public async Task AddAsync(Order order, CancellationToken cancellationToken = default)
    {
        await context.Orders.AddAsync(order, cancellationToken);
    }

    public async Task<Order?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return await context.Orders
            .Include(order => order.Items)
                .ThenInclude(item => item.Product)
            .FirstOrDefaultAsync(order => order.Id == id, cancellationToken);
    }
}
```

- **`.Include(order => order.Items).ThenInclude(item => item.Product)`** — the **include chain**: load the order, *then* each of its items, *then each item's* product. One SQL round-trip bringing the whole aggregate (and its cross-references) to life — Chapter 25's "get order details" future and Chapter 24's tests will lean on exactly this method.
- `AddAsync(order, ...)` registers the root, and — because of Chapter 17's `OwnsMany` mapping — the tracker automatically walks `order.Items` and marks every new `OrderItem` `Added` too. Add the root; the aggregate follows. Aggregates in the domain, aggregates in the tracker.

### 18.3 What Deliberately Did NOT Appear

Three absences, each a mastery lesson: **no generic `IRepository<T>`** — a base repository tempting you with `IQueryable` leaks and kitchen-sink interfaces; Chapter 26 dissects the trade-off. **no `SaveChanges` calls inside repositories** — persistence timing belongs to the Unit of Work (next chapter), so a use case touching three repositories still commits once. **no `Update(entity)` methods** — change tracking makes them redundant (Chapter 15), and forcing callers to "remember to call Update" is a bug factory.

### Key Takeaways — Chapter 18

- Repositories = interface implementation + LINQ-to-SQL translation + eager-loading decisions; the handler stays blind to all of it.
- `Include`/`ThenInclude` for relationships; `AsNoTracking` for read-only queries; `AnyAsync` for existence; `IN (...)` via `Contains` on a list; `Skip/Take` after mandatory `OrderBy`.
- `Add`/`Remove` are announcements; SQL happens only at `SaveChangesAsync`.
- No generic repository, no `SaveChanges` inside repositories, no `Update` methods — all deliberate.

---

## Chapter 19 — Unit of Work: One Transaction to Rule the Request

### 19.1 The Idea, One Last Time

`IUnitOfWork.SaveChangesAsync()` is a single door marked "commit everything this request changed." Its power comes from Chapter 16's lifetime decision: **one scoped `AppDbContext` per HTTP request**, shared by every repository — so by the time the Unit of Work is asked to save, *all* pending changes from *all* repositories sit in *one* change tracker. One `SaveChangesAsync` walks that tracker, batches every `INSERT`/`UPDATE`/`DELETE` into one network trip, and — crucially — EF wraps them in an **implicit transaction**: all statements commit together or roll back together. `PlaceOrder`'s "no partial orders" guarantee is not code we wrote; it is this property of `SaveChanges`.

### 19.2 Stamping Audit Columns — `AppDbContext.SaveChanges` Override

Chapter 9 left `CreatedAtAtUtc`/`UpdatedAtUtc` empty and promised infrastructure would fill them. The cleanest hook is overriding `SaveChangesAsync` itself — every save path passes through it, and the entities stay clean of any "IClock" dependency:

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/AppDbContext.cs — add this override INSIDE the class
public override Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
{
    foreach (var entry in ChangeTracker.Entries<BaseEntity>())
    {
        switch (entry.State)
        {
            case EntityState.Added:
                entry.Property(nameof(BaseEntity.CreatedAtUtc)).CurrentValue = DateTime.UtcNow;
                break;
            case EntityState.Modified:
                entry.Property(nameof(BaseEntity.UpdatedAtUtc)).CurrentValue = DateTime.UtcNow;
                break;
        }
    }

    return base.SaveChangesAsync(cancellationToken);
}
```

Line by line:

- **`public override Task<int> SaveChangesAsync(...)`** — `DbContext` defines virtual `SaveChangesAsync`; we intercept every save. (There is a sync `SaveChanges` twin; we never call it, so we don't bother overriding it — strictness pays.)
- **`ChangeTracker.Entries<BaseEntity>()`** — every tracked entity that is a `BaseEntity` descendant (products, categories, orders — order items aren't, and don't need to be), regardless of state.
- **`entry.State`** — the tracker's four-state machine (Chapter 16): `Added` means about-to-insert; `Modified` means diff-detected. We stamp accordingly — creation time at birth, update time at edit.
- **`entry.Property(nameof(BaseEntity.CreatedAtUtc)).CurrentValue = ...`** — the change API can write properties *regardless of accessibility* — that's how we set `protected set` properties from outside the entity, without weakening their encapsulation against ordinary code. `nameof` keeps it refactor-safe.
- **`DateTime.UtcNow`** — and *never* `DateTime.Now`: server-local time is ambiguous across machines and DST transitions; UTC is the one truth for stored timestamps (Chapter 9's rule, now mechanically enforced). At real scale you'd inject an `TimeProvider` (the .NET abstraction for testable clocks — worth a study session when you get there).

### 19.3 The Unit of Work Itself

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/UnitOfWork.cs
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Infrastructure.Persistence;

namespace ShopSphere.Infrastructure.Persistence;

public sealed class UnitOfWork(AppDbContext context) : IUnitOfWork
{
    public Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        return context.SaveChangesAsync(cancellationToken);
    }
}
```

Six lines. "That's *it*?" — yes, and the thinness is the point: the pattern's value is the **port**, not the code. The Application layer could have been coupled to `AppDbContext` directly; instead it names a one-method business concept ("commit the transaction"), which buys three real options for the future: (1) Chapter 25 will grow this class to dispatch domain events after save; (2) if a use case ever needs *multiple* `SaveChanges` in one physical transaction, this is the single place to add an explicit `BeginTransaction/Commit`; (3) swapping EF for something else touches this file, not thirty handlers.

### 19.4 Owning Your Own DI: `AddInfrastructure`

Each layer registering **itself** is the last habit of this part: Infrastructure publishes one extension method, so the composition root (Chapter 21) stays a three-line summary instead of a list of every class in the solution:

```csharp
// File: src/ShopSphere.Infrastructure/DependencyInjection.cs
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Infrastructure.Persistence;
using ShopSphere.Infrastructure.Repositories;

namespace ShopSphere.Infrastructure;

public static class DependencyInjection
{
    public static IServiceCollection AddInfrastructure(
        this IServiceCollection services,
        IConfiguration configuration)
    {
        services.AddDbContext<AppDbContext>(options =>
            options.UseSqlServer(configuration.GetConnectionString("DefaultConnection")));

        services.AddScoped<IProductRepository, ProductRepository>();
        services.AddScoped<ICategoryRepository, CategoryRepository>();
        services.AddScoped<IOrderRepository, OrderRepository>();
        services.AddScoped<IUnitOfWork, UnitOfWork>();

        return services;
    }
}
```

Line by line:

- **`public static class DependencyInjection`** + **`this IServiceCollection services`** — an **extension method** (Chapter 4's vocabulary): it teaches `IServiceCollection` a new verb, so callers write `services.AddInfrastructure(config)` instead of static calls. One `DependencyInjection.cs` per layer is the Clean Architecture convention.
- **`IConfiguration configuration`** — .NET's configuration abstraction: it is *already loaded* from `appsettings.json`, environment variables, and user secrets by the host (Chapter 21). `GetConnectionString("DefaultConnection")` reads the `"ConnectionStrings"` section — the string we add in Chapter 20.
- **`services.AddDbContext<AppDbContext>(options => options.UseSqlServer(...))`** — registers the context with the **SQL Server provider** and connection string, as **scoped**: one instance per HTTP request (Chapter 16's promise, kept by this line). Every `AddScoped` below resolves against that same instance — the "shared tracker" that powers the Unit of Work.
- **`AddScoped<IPort, Implementation>()`** — the inversion registrations, one per boundary: any handler asking for `IProductRepository` receives `ProductRepository`, per request. Scoped (not transient/singleton) matches the DbContext lifetime they depend on — mismatched lifetimes are the classic DI bug; match your data-layer lifetimes.
- **`return services;`** — chained composition: `builder.Services.AddApplication().AddInfrastructure(...)` reads like a sentence.

### Key Takeaways — Chapter 19

- One scoped `DbContext` per request + one `SaveChangesAsync` = one implicit transaction across all repositories — that is the whole Unit of Work trick, and it is what makes `PlaceOrder` atomic.
- The `SaveChangesAsync` override stamps `CreatedAtUtc`/`UpdatedAtUtc` (UTC only) via the change API — audit without polluting entities.
- `UnitOfWork` is deliberately thin: its value is the port and the future extension point (domain events, explicit transactions).
- `AddInfrastructure(this IServiceCollection, IConfiguration)` = each layer owns its registration; scoped lifetimes aligned to the DbContext.

---

## Chapter 20 — Migrations and Seeding: Bringing the Database to Life

### 20.1 What a Migration Is

A **migration** is a C# class describing a *delta* between two model states: "add these tables, these columns, this index." Two magic properties: it's **generated** (EF compares Chapter 17's model against the last known state) and it's **versioned** — SQL Server keeps a `__EFMigrationsHistory` table listing applied migrations, so `dotnet ef database update` applies only what's missing. Schema evolution becomes reviewable code in your pull requests instead of hand-run scripts.

### 20.2 Step 1 — The Connection String

```json
// File: src/ShopSphere.Api/appsettings.json  (merge into what the template gave you)
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost,1433;Database=ShopSphereDb;User Id=sa;Password=YourStrong@Passw0rd;TrustServerCertificate=True;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

The connection string, word by word (`;`-separated `Key=Value` pairs):

- **`Server=localhost,1433`** — host + port of the SQL Server container from Chapter 6. (LocalDB users: `Server=(localdb)\\MSSQLLocalDB`.)
- **`Database=ShopSphereDb`** — the database EF will create/use.
- **`User Id=sa;Password=...`** — credentials (Chapter 6's `sa` + password).
- **`TrustServerCertificate=True`** — modern SQL Server drivers encrypt connections *by default*; a Docker container ships with a self-signed certificate your machine doesn't trust, so handshakes fail without this. Development convenience only — production uses real certificates (Chapter 26).

> **Never commit real credentials.** For development shops use `dotnet user-secrets` (`dotnet user-secrets set "ConnectionStrings:DefaultConnection" "..."` inside the Api project) — secrets live outside the repo per developer; `appsettings.json` keeps a placeholder.

### 20.3 Step 2 — A Migration-Ready `Program.cs`

EF's tools need a runnable app that registers the DbContext (they launch the *startup project* to build the model). Give Api a minimal real one:

```csharp
// File: src/ShopSphere.Api/Program.cs (interim — Chapter 21 completes it)
using ShopSphere.Infrastructure;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddInfrastructure(builder.Configuration);

var app = builder.Build();

app.Run();
```

- **`builder.Services.AddInfrastructure(builder.Configuration)`** — Chapter 19's extension, now invoked: DbContext + all four port implementations registered. `builder.Configuration` is the already-loaded configuration (appsettings, env vars, user secrets).
- Everything else (`app.Run()`) just boots and idles — enough for the tools.

### 20.4 Step 3 — Create and Apply the First Migration

```bash
dotnet ef migrations add InitialCreate \
  --project src/ShopSphere.Infrastructure \
  --startup-project src/ShopSphere.Api \
  --output-dir Persistence/Migrations
```

Flag by flag: **`migrations add InitialCreate`** — "compute the diff between the current model and nothing, name it `InitialCreate`." **`--project`** — where the `DbContext` and migration files *live* (Infrastructure). **`--startup-project`** — the runnable app the tools boot to build the model (Api). **`--output-dir`** — where the generated files go.

What just happened: EF built the solution, launched Api in *design mode* (it runs `CreateBuilder` and stops before `Run`), read the model (Chapters 16–17), and generated two files plus a snapshot:

- **`..._InitialCreate.cs`** — `Up()`: `CreateTable("Categories", ...)`, `CreateTable("Products", ...)` with every column, the unique `IX_Products_Sku` index, the `CK_Products_StockQuantity` check constraint, `CreateTable("OrderItems")` with composite key and FKs — Chapter 17's schema, as code. `Down()`: the reversible `DropTable`s. **Open it and read it — it is the most educational generated code you will ever skim**, and you *may* hand-edit it before applying (e.g., to add a column default) — that's normal and safe *before* the migration is applied anywhere.
- **`AppDbContextModelSnapshot.cs`** — the model's current state, so the *next* migration diffs against this file instead of the live database.

Now make it real:

```bash
dotnet ef database update \
  --project src/ShopSphere.Infrastructure \
  --startup-project src/ShopSphere.Api
```

`database update` reads `__EFMigrationsHistory` in `ShopSphereDb` (creating the database if it doesn't exist), sees zero rows, and applies `InitialCreate`. If your Docker SQL Server is running from Chapter 6, you now have four tables, three indexes/constraints, two FKs. Verify in Azure Data Studio (Chapter 6) — or trust the migration's `Done.` output.

> In a team, prefer `dotnet ef migrations script` to generate reviewable SQL for DBAs, or `context.Database.Migrate()` at startup (shown in Chapter 21's options box) for dev convenience. Never auto-migrate production on boot without a rollback plan.

### 20.5 Step 4 — Seeding Real Data

Empty databases make boring demos. A **seeder** runs at startup and inserts known-good data if (and only if) the tables are empty — using the *domain factories*, so even seed data obeys the rules:

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/DbInitializer.cs
using Microsoft.EntityFrameworkCore;
using ShopSphere.Domain.Orders;
using ShopSphere.Domain.Products;

namespace ShopSphere.Infrastructure.Persistence;

public static class DbInitializer
{
    public static async Task SeedAsync(AppDbContext context)
    {
        if (await context.Categories.AnyAsync())
        {
            return;
        }

        var electronics = Category.Create("Electronics", "Gadgets, devices, and accessories");
        var books = Category.Create("Books", "Printed and digital reading material");

        context.Categories.AddRange(electronics, books);

        context.Products.AddRange(
            Product.Create("Mechanical Keyboard", "KB-87-RED",
                Money.Create(89.99m, "USD"), 25, electronics),
            Product.Create("Wireless Mouse", "MSE-PRO-2",
                Money.Create(39.50m, "USD"), 60, electronics),
            Product.Create("Clean Architecture (Hardcover)", "BOOK-CA-01",
                Money.Create(32.99m, "USD"), 12, books));

        await context.SaveChangesAsync();
    }
}
```

Line by line:

- **`public static class DbInitializer`** — a static helper (no state, no injection): infrastructure's one-shot utility.
- **`if (await context.Categories.AnyAsync()) return;`** — **idempotency**: seed only an empty database. Every restart re-runs `SeedAsync`; this line makes re-runs harmless.
- **`Category.Create(...)` / `Product.Create(...)` / `Money.Create(...)`** — the guarded factories, not `new` — seeding through the same doors as any handler (the `89.99m` literal is a `decimal` — the `m` suffix, Chapter 9).
- **`AddRange(...)`** — `Add` for collections; one call, many tracked entities.
- **`SaveChangesAsync()`** — one atomic write of all five entities, with audit columns stamped by Chapter 19's override.

Wire it into the pipeline (add before `app.Run()` in the interim `Program.cs`):

```csharp
using var scope = app.Services.CreateScope();

var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
await DbInitializer.SeedAsync(dbContext);
```

- **`app.Services.CreateScope()`** — `AppDbContext` is *scoped* (per-request), but at startup there is no request; **`CreateScope` manufactures one** for us. `using var` disposes it (and the DbContext) the moment the block ends.
- **`GetRequiredService<AppDbContext>()`** — resolve-and-throw-if-unregistered; the direct (non-interface) resolution is fine here — we are *inside* Infrastructure's own house.

Run `dotnet run --project src/ShopSphere.Api` once: watch the console for a clean startup, `Ctrl+C`, and `ShopSphereDb` now holds three products in two categories. The body has a skeleton, muscles, and blood — Part VI gives it a voice.

### Key Takeaways — Chapter 20

- Migrations are versioned, reviewable, *generated* schema deltas; `dotnet ef migrations add` diffs model-vs-snapshot, `database update` applies the missing ones (tracked in `__EFMigrationsHistory`).
- The tools need `--project` (DbContext home) + `--startup-project` (runnable composition); read the generated `Up()` — it's Chapter 17 in C# form.
- Connection string anatomy: `Server,Port;Database;User Id;Password;TrustServerCertificate=True` (dev-only); real secrets go to user-secrets.
- `DbInitializer`: idempotent (`AnyAsync` guard), factory-driven, scoped-resolution at startup — seed data obeys the domain like everything else.

---

# PART VI — THE PRESENTATION LAYER

> The shell. Two small chapters of code and the whole application lights up: the composition root wires every layer, controllers shrink to messengers, and cross-cutting concerns (validation pipeline, global error handling) close the loop.

---

## Chapter 21 — The API Project: `Program.cs` From Top to Bottom

### 21.1 The Composition Root (One Place That Knows Everything)

`Program.cs` is the **composition root** (Chapter 4): the single location where interfaces meet implementations. Its virtue is *brevity* — if it grows long, wiring leaked somewhere it shouldn't. Before writing it, Application needs its own self-registration file, mirroring Chapter 19's.

### 21.2 Application's DI Extension (and the Marker Class)

```csharp
// File: src/ShopSphere.Application/ApplicationAssemblyReference.cs
namespace ShopSphere.Application;

/// <summary>
/// A stable, feature-independent "anchor" type used to identify this assembly.
/// </summary>
public sealed class ApplicationAssemblyReference { }
```

- A class with an empty body — a **marker**: it exists only so registration code can say "the assembly containing *this* type" without referencing a feature type (which could be renamed or deleted someday). Empty-body classes are legal and idiomatic for markers; the XML doc comment explains its odd existence to future readers.

```csharp
// File: src/ShopSphere.Application/DependencyInjection.cs
using FluentValidation;
using MediatR;
using Microsoft.Extensions.DependencyInjection;

namespace ShopSphere.Application;

public static class DependencyInjection
{
    public static IServiceCollection AddApplication(this IServiceCollection services)
    {
        services.AddMediatR(cfg =>
        {
            cfg.RegisterServicesFromAssemblyContaining<ApplicationAssemblyReference>();
        });

        services.AddValidatorsFromAssemblyContaining<ApplicationAssemblyReference>();

        return services;
    }
}
```

Line by line:

- **`services.AddMediatR(cfg => { cfg.RegisterServicesFromAssemblyContaining<ApplicationAssemblyReference>(); })`** — the moment MediatR learns your use cases. `RegisterServicesFromAssemblyContaining<T>` scans T's assembly (our Application project) for every `IRequestHandler` and message pair, registers the handlers (transient), and builds the routing table Chapter 11 promised. One line, eight slices discovered — nine, twenty, fifty as the app grows, still one line.
- **`services.AddValidatorsFromAssemblyContaining<ApplicationAssemblyReference>()`** — the FluentValidation DI package (installed back in Chapter 7) registering every `AbstractValidator<T>` in the assembly, each as transient. Validators are now resolvable — but *who runs them?* Not the handlers (they never mention validators), not the controllers. Chapter 23's pipeline behavior, later in this part.
- Note what this method **cannot** do: touch configuration or databases — Application doesn't know they exist. Its registration is pure scanning; the contrast with `AddInfrastructure` (Chapter 19) is the layer separation showing through the wiring code itself.

### 21.3 The Full `Program.cs`

```csharp
// File: src/ShopSphere.Api/Program.cs (the complete, final version)
using ShopSphere.Application;
using ShopSphere.Infrastructure;
using ShopSphere.Infrastructure.Persistence;

var builder = WebApplication.CreateBuilder(args);

// ── Services: the composition root ──────────────────────────────
builder.Services.AddApplication();
builder.Services.AddInfrastructure(builder.Configuration);

builder.Services.AddControllers();

builder.Services.AddOpenApi();

// ── App: the middleware pipeline ────────────────────────────────
var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}

app.UseHttpsRedirection();

app.MapControllers();

using (var scope = app.Services.CreateScope())
{
    var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    await DbInitializer.SeedAsync(dbContext);
}

app.Run();

// Makes Program visible to WebApplicationFactory in Chapter 24's tests.
public partial class Program { }
```

Every line, top to bottom:

- **`var builder = WebApplication.CreateBuilder(args);`** — constructs the builder that assembles two things: `builder.Services` (the DI container being filled) and `builder.Configuration` (appsettings.json + environment variables + user secrets, layered). `args` passes command-line args into both.
- **`builder.Services.AddApplication();`** — MediatR + validators (21.2).
- **`builder.Services.AddInfrastructure(builder.Configuration);`** — DbContext + the four port implementations (Chapter 19), reading the connection string from configuration.
- **`builder.Services.AddControllers();`** — ASP.NET Core's controller subsystem: discovers classes ending in `Controller`, sets up model binding (JSON → parameters), and — because of `[ApiController]` (next chapter) — turns binding failures into automatic 400s. This line is what makes Chapter 22's files into endpoints.
- **`builder.Services.AddOpenApi();`** — registers .NET 10's built-in **OpenAPI document generator**: at request time it can serialize a machine-readable map of every endpoint (paths, verbs, request/response shapes) — the modern replacement for Swashbuckle, wired into the platform. (It produces the JSON document; the "pretty UI" over it is optional — any OpenAPI-aware client or tool can consume it.)
- **`var app = builder.Build();`** — freezes the container into a runnable application. *After this line, registrations are closed.*
- **`if (app.Environment.IsDevelopment())`** — environment-aware branching (the `ASPNETCORE_ENVIRONMENT` variable: `Development` locally, `Production` in the cloud). Development-only features should never ship to production pipelines.
- **`app.MapOpenApi();`** — serves the document at `/openapi/v1.json` — point any OpenAPI client at it and explore the API. (Optional UIs like Scalar/Swagger UI plug onto this endpoint; a nice weekend experiment.)
- **`app.UseHttpsRedirection();`** — HTTP requests get redirected to HTTPS. First middleware in our current chain; Chapter 23 will put the exception handler *ahead* of it and explain why ordering is everything.
- **`app.MapControllers();`** — connects routing to the controller endpoints — without it, every request 404s and controllers sit undiscovered. (`UseRouting` is implied in minimal hosting.)
- **`using (var scope = ...) { await DbInitializer.SeedAsync(dbContext); }`** — Chapter 20's seeding, now living in its permanent home: run once at startup before the server accepts traffic. `using` disposes the scope — and the DbContext — promptly.
- **`app.Run();`** — starts Kestrel (the built-in web server) and blocks, listening on the port shown in the console.
- **`public partial class Program { }`** — top-level statements compile into a generated `Program` class; adding this (file-scoped, after the statements — type declarations must trail top-level code) makes the class `partial` and referenceable, which is exactly what Chapter 24's `WebApplicationFactory<Program>` needs to boot the whole app in-memory for integration tests. Two lines today, an entire test suite unlocked later.

### 21.4 An Option Worth Knowing: Auto-Migrate on Startup

Chapter 20 applied migrations by hand (`dotnet ef database update`). For local/dev convenience you may let the app apply pending migrations at boot — swap the seeding block for:

```csharp
using (var scope = app.Services.CreateScope())
{
    var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    await dbContext.Database.MigrateAsync();
    await DbInitializer.SeedAsync(dbContext);
}
```

`Database.MigrateAsync()` applies pending migrations (same engine as the CLI, in-process). Keep it out of production startups unless you have an explicit strategy — concurrent instances booting and migrating simultaneously is a classic outage recipe.

### 21.5 Status Check

```bash
dotnet build
```

Four projects, every layer wired: handlers can be resolved, ports have implementations, controllers are about to exist, the database is migrated and seeded. The body is complete — it just has no way to *hear* HTTP yet. That is the entire remaining job of Chapter 22.

### Key Takeaways — Chapter 21

- `Program.cs` = composition root + pipeline definition; its quality metric is shortness.
- `AddApplication()` = MediatR scan + validator scan (marker-anchored); `AddInfrastructure()` = DbContext + ports (Chapter 19).
- `AddControllers` binds HTTP to controllers; `AddOpenApi`/`MapOpenApi` publish the API map; `MapControllers` activates routing.
- Seeding (and optionally `MigrateAsync`) runs in a startup scope before `app.Run()`.
- `public partial class Program { }` future-proofs the app for `WebApplicationFactory` tests.

---

## Chapter 22 — Thin Controllers: HTTP Meets MediatR

### 22.1 The Contract of a Thin Controller

A controller action in this architecture is allowed exactly three jobs: **bind** (translate HTTP to a message), **send** (hand it to `ISender`), and **map status** (turn the result into an HTTP response). It may not validate business rules, touch repositories, or make decisions — the day it does, the architecture starts dying. The test: *could you replace the whole controller file with a generated stub and lose nothing but routing?* If yes, it's thin.

### 22.2 `ProductsController` — Line by Line

```bash
mkdir -p src/ShopSphere.Api/Controllers
```

```csharp
// File: src/ShopSphere.Api/Controllers/ProductsController.cs
using MediatR;
using Microsoft.AspNetCore.Mvc;
using ShopSphere.Application.Features.Products.CreateProduct;
using ShopSphere.Application.Features.Products.DeleteProduct;
using ShopSphere.Application.Features.Products.GetProductById;
using ShopSphere.Application.Features.Products.GetProducts;
using ShopSphere.Application.Features.Products.UpdateProduct;

namespace ShopSphere.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public sealed class ProductsController(ISender sender) : ControllerBase
{
    [HttpGet]
    public async Task<IActionResult> GetProducts(
        [FromQuery] GetProductsQuery query,
        CancellationToken cancellationToken)
    {
        var result = await sender.Send(query, cancellationToken);
        return Ok(result);
    }

    [HttpGet("{id:guid}")]
    public async Task<IActionResult> GetProductById(
        Guid id,
        CancellationToken cancellationToken)
    {
        var query = new GetProductByIdQuery(id);
        var result = await sender.Send(query, cancellationToken);
        return Ok(result);
    }

    [HttpPost]
    public async Task<IActionResult> CreateProduct(
        CreateProductCommand command,
        CancellationToken cancellationToken)
    {
        var id = await sender.Send(command, cancellationToken);
        return CreatedAtAction(nameof(GetProductById), new { id }, id);
    }

    [HttpPut("{id:guid}")]
    public async Task<IActionResult> UpdateProduct(
        Guid id,
        UpdateProductCommand command,
        CancellationToken cancellationToken)
    {
        if (id != command.Id)
        {
            return BadRequest(new ProblemDetails
            {
                Title = "Mismatched identifier",
                Detail = "The id in the URL and the id in the request body must match."
            });
        }

        await sender.Send(command, cancellationToken);
        return NoContent();
    }

    [HttpDelete("{id:guid}")]
    public async Task<IActionResult> DeleteProduct(
        Guid id,
        CancellationToken cancellationToken)
    {
        await sender.Send(new DeleteProductCommand(id), cancellationToken);
        return NoContent();
    }
}
```

The declaration, line by line:

- **`[ApiController]`** — the modern-conventions attribute: JSON binding from body, automatic **400 with ProblemDetails when model binding fails** (malformed JSON, wrong types — before our code ever runs), and inferred attribute routing.
- **`[Route("api/[controller]")]`** — the route template: `[controller]` is replaced by the class name minus `Controller` → `api/products` (kebab-cased). So `GET /api/products` lands on `GetProducts`.
- **`public sealed class ProductsController(ISender sender) : ControllerBase`** — the thinnest possible constructor: **one dependency, `ISender`** (Chapter 11's door), for the entire controller. No `IProductRepository`, no `DbContext`, no validators — one interface to rule all eight use cases, present and future. `ControllerBase` (not `Controller`) is the MVC-less base for APIs — no view support, less weight.

The actions, one verb at a time:

- **`GetProducts([FromQuery] GetProductsQuery query, ...)`** — the query record itself is the binding target: `[FromQuery]` tells the binder to construct `GetProductsQuery` from URL parameters — `GET /api/products?searchTerm=key&categoryId=...&pageNumber=2&pageSize=20` becomes a fully-populated message, parameter names matched case-insensitively, defaults (Chapter 14's `= 1`, `= 10`) applied when absent. **This is why records + primitive input shape earn their keep**: the message is simultaneously the API contract and the use-case input. One `Send`, one `Ok(result)` → `200 OK` with the JSON paged result.
- **`GetProductById(Guid id, ...)`** — the route supplies `id` (see the template below); `new GetProductByIdQuery(id)` builds the message by hand because a single route value is simpler than a binder attribute. Note the flow: **if the product doesn't exist, the *handler* throws `NotFoundException`, the *middleware* (Chapter 23) writes 404** — the controller needs no not-found logic at all.
- **`CreateProduct([FromBody is implicit for complex types] CreateProductCommand command, ...)`** — the request body's JSON deserializes directly into the command record: `{"name": "...", "sku": "...", "priceAmount": 25.00, "currency": "USD", "stockQuantity": 10, "categoryId": null}`. Then `sender.Send` returns the new `Guid`, and:
  - **`return CreatedAtAction(nameof(GetProductById), new { id }, id);`** — the perfect REST answer for creation: **`201 Created`**, a **`Location: /api/products/{id}`** header (that's `CreatedAtAction` — "where you can fetch it," addressed by *action name* so refactoring-safe), and the id in the body for convenience. Three semantics in one line.
- **`UpdateProduct(Guid id, UpdateProductCommand command, ...)`** — two input sources (route + body), so one consistency rule belongs at this level: the guard `if (id != command.Id)` rejects `PUT /api/products/A` with body `{"id": "B", ...}` — a request that disagrees with itself. It returns a hand-built `ProblemDetails` (a plain 400; Chapter 23's format is for pipeline errors). Then `Send` + **`NoContent()` → `204`**: success, deliberately empty body — the client already knows the new state; it sent it.
- **`DeleteProduct(Guid id, ...)`** — the shortest full use case in the system: route → message → send → 204. Notice `new DeleteProductCommand(id)` — a message built from one route parameter; PUT is the only action that needed a body.

Route template details: **`"{id:guid}"`** is a **route constraint** — the action only matches when the segment parses as a GUID; `/api/products/abc` never reaches the action (404 from routing itself, which is honest: that is not a product id, and there is no such route).

### 22.3 `CategoriesController` and `OrdersController` — Same Recipe

```csharp
// File: src/ShopSphere.Api/Controllers/CategoriesController.cs
using MediatR;
using Microsoft.AspNetCore.Mvc;
using ShopSphere.Application.Features.Categories.CreateCategory;
using ShopSphere.Application.Features.Categories.GetCategories;

namespace ShopSphere.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public sealed class CategoriesController(ISender sender) : ControllerBase
{
    [HttpGet]
    public async Task<IActionResult> GetCategories(CancellationToken cancellationToken)
    {
        var result = await sender.Send(new GetCategoriesQuery(), cancellationToken);
        return Ok(result);
    }

    [HttpPost]
    public async Task<IActionResult> CreateCategory(
        CreateCategoryCommand command,
        CancellationToken cancellationToken)
    {
        var id = await sender.Send(command, cancellationToken);
        return CreatedAtAction(nameof(GetCategories), new { id }, id);
    }
}
```

```csharp
// File: src/ShopSphere.Api/Controllers/OrdersController.cs
using MediatR;
using Microsoft.AspNetCore.Mvc;
using ShopSphere.Application.Features.Orders.PlaceOrder;

namespace ShopSphere.Api.Controllers;

[ApiController]
[Route("api/[controller]")]
public sealed class OrdersController(ISender sender) : ControllerBase
{
    [HttpPost]
    public async Task<IActionResult> PlaceOrder(
        PlaceOrderCommand command,
        CancellationToken cancellationToken)
    {
        var id = await sender.Send(command, cancellationToken);
        return CreatedAtAction(nameof(PlaceOrder), new { id }, id);
    }
}
```

(`GetCategoriesQuery()` has no parameters to bind — the empty message is constructed inline. For `PlaceOrder`, a real "get order" endpoint would deserve its own slice — Chapter 24's exercises point that way; `CreatedAtAction` referencing its own action keeps the 201+Location contract consistent meanwhile.)

### 22.4 The Moment of Truth — Run It End to End

```bash
dotnet run --project src/ShopSphere.Api
```

Watch for `Now listening on: http://localhost:5xxx` (note the port, then in another terminal, `http://localhost:PORT`):

**Create a product** (the full journey: controller → MediatR → validator → handler → domain → repository → Unit of Work → SQL Server):

```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{
        "name": "Standing Desk",
        "sku": "DESK-STD-01",
        "priceAmount": 349.99,
        "currency": "USD",
        "stockQuantity": 8,
        "categoryId": null
      }'
```

Expected: **`201 Created`**, `Location: http://localhost:5000/api/products/3f2b...`, body = the new id.

**Browse, seeded and created together:**

```bash
curl "http://localhost:5000/api/products?searchTerm=desk&pageSize=10"
```

Expected: `200 OK` with `{"items": [...], "page": 1, "pageSize": 10, "totalCount": 1, "totalPages": 1, "hasNextPage": false, "hasPreviousPage": false}` — the seeded catalog plus your desk, paged and searched, through five layers that each know only their own job.

**Place an order:**

```bash
curl -X POST http://localhost:5000/api/orders \
  -H "Content-Type: application/json" \
  -d '{
        "customerName": "Amir Hassan",
        "shippingAddress": "12 Nile St, Cairo",
        "lines": [ { "productId": "<paste-the-desk-id>", "quantity": 2 } ]
      }'
```

Expected: `201 Created` with an order id — and if you GET the desk again, its `stockQuantity` is now 6: two aggregates, one transaction, zero partial states. **The architecture you read about in Part I is now software.** (If any request fails with a validation or 404 error in *plain* JSON — that's Chapter 23's unfinished business: the error pipeline isn't wired yet. Next chapter.)

### 22.5 Why Thinness Is a Feature (Not Laziness)

Because the controller holds no logic, it has almost nothing to unit test (Chapter 24 tests handlers instead — where logic lives). Because it depends only on `ISender`, it never changes when persistence changes. And because every action is five lines, ten new endpoints cost an afternoon, not a sprint. Fat controllers are not just ugly — they are *where use cases go to hide*; thin ones force every rule into the layers where it can be tested, named, and reused.

### Key Takeaways — Chapter 22

- Thin action = bind → `sender.Send` → status code. One constructor dependency: `ISender`.
- `[ApiController]` (auto-400 on bad binding), `[Route("api/[controller]")]`, `{id:guid}` route constraints, `[FromQuery]` record binding with defaults.
- Status codes as a language: `200` read, `201+Location` create, `204` update/delete, with 400/404/409 owned by the error pipeline (Chapter 23).
- End-to-end verified with `curl`: create, search, order — all layers cooperating, stock decremented atomically.
- Thin controllers are testability, replaceability, and speed of feature delivery in one discipline.

---

## Chapter 23 — Cross-Cutting Concerns: The Validation Pipeline and Global Error Handling

Two promises remain unkept: validators are registered (Chapter 21) but nobody runs them, and handlers throw typed exceptions (`ValidationException`, `NotFoundException`, `ConflictException`, `DomainException`) that currently bubble to ASP.NET's default ugly 500. This chapter builds both cross-cutting concerns — each written **once**, applied to **every** use case automatically, forever.

### 23.1 `ValidationBehavior` — Validators That Run Themselves

```bash
mkdir -p src/ShopSphere.Application/Common/Behaviors src/ShopSphere.Api/Middleware
```

```csharp
// File: src/ShopSphere.Application/Common/Behaviors/ValidationBehavior.cs
using FluentValidation;
using MediatR;

namespace ShopSphere.Application.Common.Behaviors;

public sealed class ValidationBehavior<TRequest, TResponse>(
    IEnumerable<IValidator<TRequest>> validators)
    : IPipelineBehavior<TRequest, TResponse>
    where TRequest : notnull
{
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        CancellationToken cancellationToken)
    {
        if (!validators.Any())
        {
            return await next();
        }

        var context = new ValidationContext<TRequest>(request);

        var failures = (await Task.WhenAll(
                validators.Select(validator => validator.ValidateAsync(context, cancellationToken))))
            .SelectMany(result => result.Errors)
            .Where(failure => failure is not null)
            .ToList();

        if (failures.Count != 0)
        {
            throw new ValidationException(failures);
        }

        return await next();
    }
}
```

Line by line — this is the densest class in the Application layer, and every line is a lesson:

- **`public sealed class ValidationBehavior<TRequest, TResponse>(IEnumerable<IValidator<TRequest>> validators)`** — a **generic class with two type parameters** (the message type and its response type) plus a primary constructor. MediatR instantiates *one closed version per message type* (e.g. `ValidationBehavior<CreateProductCommand, Guid>`) and injects **all validators registered for that message** — `IEnumerable<IValidator<TRequest>>` is "every matching validator in DI" (usually exactly one: the slice's own; the pattern supports more).
- **`: IPipelineBehavior<TRequest, TResponse>`** — MediatR's middleware contract (Chapter 11). Registering a class as `IPipelineBehavior<,>` (open generic — in a moment) plugs it around **every** `Send` in the application.
- **`where TRequest : notnull`** — MediatR's messages may be value types or reference types but never null; the constraint is required by the interface (the compiler enforces MediatR's contract on us).
- **`Handle(TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken cancellationToken)`** — the pipeline shape: receive the message, receive `next` (a delegate meaning "the rest of the pipeline — other behaviors, then the actual handler"), and decide whether to call it.
- **`if (!validators.Any()) { return await next(); }`** — the zero-validator fast path (messages like `GetProductByIdQuery` that defined none): straight through, no allocation ceremony.
- **`new ValidationContext<TRequest>(request)`** — FluentValidation's typed wrapper around the message; validators run against a context, not the bare object (the context enables future extras like rule sets and property selectors).
- **`await Task.WhenAll(validators.Select(validator => validator.ValidateAsync(context, cancellationToken)))`** — run **every** matching validator **in parallel** (one `Task` per validator, awaited together), each returning a `ValidationResult` (a `.IsValid` flag + `.Errors` list).
- **`.SelectMany(result => result.Errors)`** — flatten each result's failures into one sequence (two validators ⇒ one combined list). **`.Where(failure => failure is not null)`** filters nullable slots (FluentValidation's error collection is `List<ValidationFailure?>` in some versions — a defensive line that costs nothing).
- **`throw new ValidationException(failures);`** — the rejection: FluentValidation's own exception type, carrying every failure (property name + message each). **The handler never runs.** The database is never consulted. One throw, translated by the next section into a 400 with a field-by-field error map.
- **`return await next();`** — the happy path: hand the message to the rest of the pipeline (the handler). Note the symmetry: *invalid stops before `next`; valid passes through `next`.*

Register it inside `AddApplication()` (Chapter 21's file, one new line before `return services;`):

```csharp
services.AddTransient(typeof(IPipelineBehavior<,>), typeof(ValidationBehavior<,>));
```

- **`typeof(IPipelineBehavior<,>)` / `typeof(ValidationBehavior<,>)`** — **open generic registration**: "for *every* closed `IPipelineBehavior<X, Y>` anyone ever resolves, provide `ValidationBehavior<X, Y>`." That single line wraps all eight slices — and every future slice — in validation. This is the line that keeps handlers and controllers free of validation code, permanently.

> With this line added, a **flow upgrade** happened silently: `Send` now means *validate, then handle*. Rebuild and try `POST /api/products` with `"priceAmount": -5` — you'll get... a plain-text 500 for now, because `ValidationException` has no translator *yet*. Next section.

### 23.2 `ExceptionHandlingMiddleware` — One Catch-All, One Language

```csharp
// File: src/ShopSphere.Api/Middleware/ExceptionHandlingMiddleware.cs
using System.Text.Json;
using FluentValidation;
using Microsoft.AspNetCore.Mvc;
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Domain.Common;

namespace ShopSphere.Api.Middleware;

public sealed class ExceptionHandlingMiddleware(
    RequestDelegate next,
    ILogger<ExceptionHandlingMiddleware> logger)
{
    private static readonly JsonSerializerOptions JsonOptions = new(JsonSerializerDefaults.Web);

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await next(context);
        }
        catch (Exception exception)
        {
            await HandleExceptionAsync(context, exception);
        }
    }

    private async Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        var (statusCode, title, detail, errors) = exception switch
        {
            ValidationException validationException => (
                StatusCodes.Status400BadRequest,
                "Validation failed",
                "One or more validation errors occurred.",
                validationException.Errors
                    .GroupBy(failure => failure.PropertyName)
                    .ToDictionary(
                        group => group.Key,
                        group => group.Select(failure => failure.ErrorMessage).ToArray())),

            DomainException domainException => (
                StatusCodes.Status400BadRequest,
                "Domain rule violated",
                domainException.Message,
                (Dictionary<string, string[]>?)null),

            NotFoundException notFoundException => (
                StatusCodes.Status404NotFound,
                "Resource not found",
                notFoundException.Message,
                (Dictionary<string, string[]>?)null),

            ConflictException conflictException => (
                StatusCodes.Status409Conflict,
                "Conflict",
                conflictException.Message,
                (Dictionary<string, string[]>?)null),

            _ => (
                StatusCodes.Status500InternalServerError,
                "Server error",
                "An unexpected error occurred.",
                (Dictionary<string, string[]>?)null)
        };

        if (statusCode == StatusCodes.Status500InternalServerError)
        {
            logger.LogError(exception, "Unhandled exception on {Path}", context.Request.Path);
        }

        var problemDetails = new ProblemDetails
        {
            Status = statusCode,
            Title = title,
            Detail = detail,
            Instance = context.Request.Path
        };

        if (errors is not null)
        {
            problemDetails.Extensions["errors"] = errors;
        }

        context.Response.StatusCode = statusCode;
        context.Response.ContentType = "application/problem+json";

        await context.Response.WriteAsync(
            JsonSerializer.Serialize(problemDetails, JsonOptions));
    }
}
```

Section by section:

**The middleware shape (top):**

- **`RequestDelegate next`** — the next link in the HTTP pipeline (Chapter 4's middleware vocabulary). Our `InvokeAsync` **wraps** it in `try/catch`: every exception thrown by *any later stage* — routing, binding, MediatR, handlers, EF — surfaces here.
- **`ILogger<ExceptionHandlingMiddleware> logger`** — injected logging, categorized by class (log filters can target this category by name).
- **`private static readonly JsonSerializerOptions JsonOptions = new(JsonSerializerDefaults.Web)`** — one reusable serializer config: `JsonSerializerDefaults.Web` = camelCase names + case-insensitive parsing — the JSON conventions browsers expect. `static readonly` because options objects are expensive and thread-safe by design.

**The translation table (the switch):**

- **`exception switch { ... }`** — a **switch expression** over the exception's *runtime type* — C# pattern matching doing the type-dispatch that a chain of `if (ex is ...)` used to do. Each arm yields a **tuple** `(statusCode, title, detail, errors)`, decomposed into four locals in one line — the entire error policy of ShopSphere in one readable table.
- **`ValidationException` → 400** — with the payload built by `validationException.Errors.GroupBy(failure => failure.PropertyName).ToDictionary(...)`: all failures **grouped by property** — `{"PriceAmount": ["Price must be greater than zero."]}` — so clients can highlight the exact form field. This dictionary is the `errors` tuple element; for the arms without field errors it's `null`, cast `(Dictionary<string, string[]>?)null` to unify the tuple's type.
- **`DomainException` → 400** — a broken business rule (negative stock, paying a shipped order) is *the client's request* conflicting with *business reality*; 400 with the entity's own message (Chapter 9's helpful error texts, delivered at last).
- **`NotFoundException` → 404**, **`ConflictException` → 409** — Chapter 13's boundary vocabulary, translated as promised.
- **`_ => 500`** — the discard arm: anything unexpected. **Note the deliberate secrecy**: `detail` says "An unexpected error occurred" — *never* leak exception internals to clients (stack traces are attackers' maps); the **real** detail goes to the log:

**The response:**

- **`logger.LogError(exception, "Unhandled exception on {Path}", ...)`** — structured logging (message *template* + named placeholders rather than string concatenation; log systems can query "all errors on path X"). Only 500s log-as-errors; expected 4xx outcomes are normal traffic, not incidents.
- **`new ProblemDetails { Status, Title, Detail, Instance }`** — ASP.NET's built-in type for the RFC 9457 standard error format (Chapter 4): every client in every language knows how to read `title`/`status`/`detail`. `Instance` = which path failed.
- **`problemDetails.Extensions["errors"] = errors`** — the extension mechanism for standard-plus-custom fields: validation adds the field dictionary on top of the standard shape.
- **`ContentType = "application/problem+json"`** — the RFC's official media type; well-behaved clients switch on it.

### 23.3 Wiring It In (Order Is Everything)

Middleware runs in **registration order**. The exception handler must be the **first** thing every request passes through (so it can catch what any later stage throws) — put it at the top of the pipeline in `Program.cs`, before `UseHttpsRedirection`:

```csharp
var app = builder.Build();

app.UseMiddleware<ExceptionHandlingMiddleware>();   // ← first: wraps everything below

if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}

app.UseHttpsRedirection();
app.MapControllers();
// ... seeding, app.Run() as in Chapter 21
```

**`app.UseMiddleware<ExceptionHandlingMiddleware>()`** — the generic registration of a custom middleware class (convention: an `InvokeAsync(HttpContext)` method). One line; the whole application now speaks fluent errors.

### 23.4 Verify Every Error Path

Restart the API and exercise the table — each `curl` should return the matching JSON:

```bash
# 1) ValidationException → 400 with field errors
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{ "name": "", "sku": "x", "priceAmount": -5, "currency": "us", "stockQuantity": -1 }'
```

```json
{
  "status": 400,
  "title": "Validation failed",
  "detail": "One or more validation errors occurred.",
  "instance": "/api/products",
  "errors": {
    "Name": ["Product name is required."],
    "Sku": ["SKU must be 3-20 characters: uppercase letters, digits or hyphens."],
    "PriceAmount": ["Price must be greater than zero."],
    "Currency": ["Currency must be a 3-letter ISO 4217 code (e.g. USD, EGP)."],
    "StockQuantity": ["Stock quantity cannot be negative."]
  }
}
```

Five broken fields, five precise messages, **one** automatic run of `CreateProductCommandValidator` — and the handler never knew. Continue:

```bash
# 2) ConflictException → 409 (duplicate SKU)
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{ "name": "Desk Mk2", "sku": "DESK-STD-01", "priceAmount": 199.00, "currency": "USD", "stockQuantity": 3 }'

# 3) NotFoundException → 404 (category that doesn't exist)
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{ "name": "Desk Mk3", "sku": "DESK-STD-02", "priceAmount": 199.00, "currency": "USD", "stockQuantity": 3, "categoryId": "00000000-0000-0000-0000-000000000001" }'

# 4) DomainException → 400 (order more than stock)
curl -X POST http://localhost:5000/api/orders \
  -H "Content-Type: application/json" \
  -d '{ "customerName": "Tester", "shippingAddress": "Somewhere", "lines": [ { "productId": "<desk-id>", "quantity": 9999 } ] }'
```

Expected: `409 Conflict` ("A product with SKU 'DESK-STD-01' already exists."), `404 Not Found`, and `400` with `"Domain rule violated" / "Cannot decrease stock by 9999; only N remain for SKU 'DESK-STD-01'."` — the Domain's own voice, verbatim.

### 23.5 The Complete Request Lifecycle (Final Edition)

Every piece is now in place — this is the full, final flow of `POST /api/orders`:

```
HTTP request
 └▶ ExceptionHandlingMiddleware        (wraps everything; translates throws → ProblemDetails)
     └▶ HttpsRedirection → Routing → Model binding (JSON → PlaceOrderCommand)
         └▶ ProductsController.PlaceOrder  — bind, sender.Send, map status
             └▶ MediatR pipeline
                 ├▶ ValidationBehavior       (runs PlaceOrderCommandValidator → 400 on failure)
                 └▶ PlaceOrderCommandHandler — loads products (IProductRepository → EF → SQL),
                                               Order.Place (domain rules, stock decrease),
                                               AddAsync + UnitOfWork.SaveChangesAsync
                                               (audit stamp → one atomic transaction)
             └▶ returns order id
 └▶ 201 Created + Location
```

Six collaborators, each replaceable, each testable alone — the promise of Chapter 5, executing.

### Key Takeaways — Chapter 23

- `ValidationBehavior<TRequest, TResponse>`: MediatR middleware running all matching validators before any handler; `next()` = the pipeline delegate; invalid ⇒ `ValidationException`, handler never runs.
- One open-generic registration (`IPipelineBehavior<,>` → `ValidationBehavior<,>`) validates every current and future slice.
- `ExceptionHandlingMiddleware`: try/catch around the pipeline; switch-expression translation table — `ValidationException`→400+field map, `DomainException`→400, `NotFound`→404, `Conflict`→409, else 500+log.
- Responses are RFC 9457 `ProblemDetails` (`application/problem+json`); internals never leak — they go to structured logs.
- Middleware order: exception handler **first**. The request lifecycle diagram is now complete and final.

---

# PART VII — MASTERY

> The final ascent. Part VII hardens what you built (tests that respect the layers), deepens it (domain events, soft delete, concurrency), and then hands you judgment — the anti-pattern catalog, the scaling decisions, and the honest answer to "when is this architecture overkill?" That judgment is what "mastery" actually means.

---

## Chapter 24 — Testing the Architecture: Unit and Integration Tests

### 24.1 The Testing Map of a Clean Architecture

Clean Architecture is a testing machine: each layer's isolation becomes a specific kind of cheap test.

| Test kind | Tests | Needs | Speed |
|---|---|---|---|
| **Domain unit tests** | Entity invariants (`DecreaseStock`, `Order.Place`) | Nothing — pure C# | microseconds |
| **Application unit tests** | Handler orchestration, validators | Hand-written **fakes** for the ports | microseconds |
| **Integration tests** | The whole HTTP → SQL Server round trip | `WebApplicationFactory` + **Testcontainers** | seconds (real Docker SQL Server) |

The proportions: *many* of the first two kinds, a *focused* set of the third. This chapter builds one project of each.

### 24.2 The Test Projects

```bash
dotnet new xunit -n ShopSphere.Domain.UnitTests -o tests/ShopSphere.Domain.UnitTests
dotnet new xunit -n ShopSphere.Application.UnitTests -o tests/ShopSphere.Application.UnitTests
dotnet new xunit -n ShopSphere.Api.IntegrationTests -o tests/ShopSphere.Api.IntegrationTests

dotnet sln add tests/ShopSphere.Domain.UnitTests tests/ShopSphere.Application.UnitTests tests/ShopSphere.Api.IntegrationTests

dotnet add tests/ShopSphere.Domain.UnitTests reference src/ShopSphere.Domain
dotnet add tests/ShopSphere.Application.UnitTests reference src/ShopSphere.Application
dotnet add tests/ShopSphere.Api.IntegrationTests reference src/ShopSphere.Api
```

Plus the integration project's two extra packages (the unit projects need none beyond the `xunit` template's defaults):

```bash
cd tests/ShopSphere.Api.IntegrationTests
dotnet add package Microsoft.AspNetCore.Mvc.Testing
dotnet add package Testcontainers.MsSql
cd ../..
```

- **`xunit`** — the .NET community's standard test framework: `[Fact]` (one test) and `[Theory]` (one test, many inputs).
- **`Microsoft.AspNetCore.Mvc.Testing`** — hosts an ASP.NET Core app in-memory with a replacement-friendly `WebApplicationFactory<Program>` — the reason Chapter 21 added `public partial class Program { }`.
- **`Testcontainers.MsSql`** — spins up a *real* SQL Server in a throwaway Docker container per test run: true integration without a shared, stateful "test database" that rots. (Docker must be running — Chapter 6's investment keeps paying.)

Delete each template's `UnitTest1.cs`.

### 24.3 Domain Unit Tests — Invariants, Pure and Fast

```csharp
// File: tests/ShopSphere.Domain.UnitTests/Products/ProductTests.cs
using ShopSphere.Domain.Common;
using ShopSphere.Domain.Orders;
using ShopSphere.Domain.Products;
using Xunit;

namespace ShopSphere.Domain.UnitTests.Products;

public class ProductTests
{
    private static Product CreateValidProduct(int stock = 10) =>
        Product.Create("Mechanical Keyboard", "KB-87-RED", Money.Create(89.99m, "USD"), stock, null);

    [Fact]
    public void Create_WithValidValues_Succeeds()
    {
        var product = CreateValidProduct();

        Assert.NotEqual(Guid.Empty, product.Id);
        Assert.Equal("Mechanical Keyboard", product.Name);
        Assert.Equal(10, product.StockQuantity);
        Assert.Equal("USD", product.Price.Currency);
    }

    [Theory]
    [InlineData("")]
    [InlineData("   ")]
    [InlineData(null)]
    public void Create_WithBlankName_ThrowsDomainException(string? name)
    {
        Assert.Throws<DomainException>(() =>
            Product.Create(name!, "KB-87-RED", Money.Create(89.99m, "USD"), 5, null));
    }

    [Fact]
    public void Create_WithNegativeStock_ThrowsDomainException()
    {
        Assert.Throws<DomainException>(() =>
            Product.Create("Mouse", "MSE-1", Money.Create(10m, "USD"), -1, null));
    }

    [Fact]
    public void DecreaseStock_WithinAvailable_Succeeds()
    {
        var product = CreateValidProduct(stock: 10);

        product.DecreaseStock(3);

        Assert.Equal(7, product.StockQuantity);
    }

    [Fact]
    public void DecreaseStock_BeyondAvailable_ThrowsDomainException()
    {
        var product = CreateValidProduct(stock: 2);

        var exception = Assert.Throws<DomainException>(() => product.DecreaseStock(3));

        Assert.Contains("only 2 remain", exception.Message);
    }

    [Fact]
    public void PlaceOrder_WithValidLine_DecreasesProductStock()
    {
        var product = CreateValidProduct(stock: 10);
        var lines = new List<Order.OrderLine> { new(product, 4) };

        var order = Order.Place("Sara", "1 Main St", lines);

        Assert.Equal(OrderStatus.Pending, order.Status);
        Assert.Equal(6, product.StockQuantity);
        Assert.Single(order.Items);
    }
}
```

- **`private static Product CreateValidProduct(int stock = 10)`** — the **object mother / builder habit**: one factory for valid objects, parameters only for what the test varies. Every test then reads as *arrange → act → assert* with the arrange compressed to one call.
- **`[Fact]` / `[Theory] + [InlineData]`** — a single test vs. the same test run once per data row (`""`, `"   "`, `null` all must throw — the whitespace cases are where naive `== ""` checks fail).
- **`Assert.Throws<DomainException>(() => ...)`** — asserts *that* it throws; the `DecreaseStock_BeyondAvailable` test goes further and inspects the **message** (`Assert.Contains("only 2 remain")`) — our Chapter 9 investment in helpful error text pays off as testable behavior.
- The `PlaceOrder` test is the entire overselling rule verified in ten lines, no database, no HTTP — **the Domain layer's purity converted directly into test value**.

### 24.4 Application Unit Tests — Handlers With Hand-Written Fakes

Handlers orchestrate ports; to test them we supply **fakes** (Chapter 4's word): in-memory implementations of `IProductRepository`, `ICategoryRepository`, `IUnitOfWork` — about twenty lines each, no mocking library needed.

```csharp
// File: tests/ShopSphere.Application.UnitTests/Fakes/FakeProductRepository.cs
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Application.Common.Models;
using ShopSphere.Domain.Products;

namespace ShopSphere.Application.UnitTests.Fakes;

public sealed class FakeProductRepository : IProductRepository
{
    private readonly List<Product> _products = [];

    public List<Product> Products => _products;

    public Task AddAsync(Product product, CancellationToken cancellationToken = default)
    {
        _products.Add(product);
        return Task.CompletedTask;
    }

    public Task<Product?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return Task.FromResult(_products.FirstOrDefault(product => product.Id == id));
    }

    public Task<IReadOnlyList<Product>> GetByIdsAsync(
        IReadOnlyList<Guid> ids,
        CancellationToken cancellationToken = default)
    {
        return Task.FromResult<IReadOnlyList<Product>>(
            _products.Where(product => ids.Contains(product.Id)).ToList());
    }

    public Task<bool> SkuExistsAsync(string sku, CancellationToken cancellationToken = default)
    {
        return Task.FromResult(_products.Any(product => product.Sku == sku));
    }

    public Task<PagedResult<Product>> GetPagedAsync(
        string? searchTerm,
        Guid? categoryId,
        int pageNumber,
        int pageSize,
        CancellationToken cancellationToken = default)
    {
        IEnumerable<Product> query = _products;

        if (!string.IsNullOrWhiteSpace(searchTerm))
        {
            query = query.Where(product =>
                product.Name.Contains(searchTerm, StringComparison.OrdinalIgnoreCase));
        }

        if (categoryId is not null)
        {
            query = query.Where(product => product.CategoryId == categoryId);
        }

        var items = query
            .OrderBy(product => product.Name)
            .Skip((pageNumber - 1) * pageSize)
            .Take(pageSize)
            .ToList();

        return Task.FromResult(new PagedResult<Product>(items, pageNumber, pageSize, _products.Count));
    }

    public void Remove(Product product)
    {
        _products.Remove(product);
    }
}
```

- **`Task.CompletedTask` / `Task.FromResult(...)`** — how a fake satisfies an async interface with synchronous in-memory logic: wrap the plain result in an already-completed `Task` (the modern equivalent of `Task.Run(() => result)` — no thread, no overhead). LINQ works on `List<T>` exactly as it did on `IQueryable` — with `string.Contains` needing an explicit `StringComparison` against a list (a compiler prompt that in-memory `Contains` ≠ SQL `LIKE` — a genuine difference our fakes honestly surface).
- The same file pattern gives you `FakeCategoryRepository` (identical shape around `List<Category>`) and the two-line Unit of Work:

```csharp
// File: tests/ShopSphere.Application.UnitTests/Fakes/FakeUnitOfWork.cs
using ShopSphere.Application.Common.Interfaces;

namespace ShopSphere.Application.UnitTests.Fakes;

public sealed class FakeUnitOfWork : IUnitOfWork
{
    public int SaveCalls { get; private set; }

    public Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        SaveCalls++;
        return Task.FromResult(0);
    }
}
```

`SaveCalls` — fakes double as **spies**: the test can assert not just outcomes but *that the handler saved*.

Now the handler tests — the three scenarios of Chapter 13:

```csharp
// File: tests/ShopSphere.Application.UnitTests/Products/CreateProductCommandHandlerTests.cs
using ShopSphere.Application.Common.Exceptions;
using ShopSphere.Application.Features.Products.CreateProduct;
using ShopSphere.Application.UnitTests.Fakes;
using ShopSphere.Domain.Products;
using Xunit;

namespace ShopSphere.Application.UnitTests.Products;

public class CreateProductCommandHandlerTests
{
    private readonly FakeProductRepository _products = new();
    private readonly FakeCategoryRepository _categories = new();
    private readonly FakeUnitOfWork _unitOfWork = new();

    private CreateProductCommandHandler CreateHandler() =>
        new(_products, _categories, _unitOfWork);

    [Fact]
    public async Task Handle_ValidCommand_AddsProductAndSaves()
    {
        var command = new CreateProductCommand("Standing Desk", "DESK-STD-01", 349.99m, "USD", 8, null);

        var id = await CreateHandler().Handle(command, CancellationToken.None);

        var product = Assert.Single(_products.Products);
        Assert.Equal("Standing Desk", product.Name);
        Assert.Equal(id, product.Id);
        Assert.Equal(1, _unitOfWork.SaveCalls);
    }

    [Fact]
    public async Task Handle_DuplicateSku_ThrowsConflictException()
    {
        _products.Products.Add(
            Product.Create("Old Desk", "DESK-STD-01", Money.Create(99m, "USD"), 1, null));

        var command = new CreateProductCommand("New Desk", "DESK-STD-01", 149m, "USD", 2, null);

        await Assert.ThrowsAsync<ConflictException>(
            () => CreateHandler().Handle(command, CancellationToken.None));
    }

    [Fact]
    public async Task Handle_MissingCategory_ThrowsNotFoundException()
    {
        var missingId = Guid.CreateVersion7();
        var command = new CreateProductCommand("Desk", "DESK-STD-02", 99m, "USD", 1, missingId);

        await Assert.ThrowsAsync<NotFoundException>(
            () => CreateHandler().Handle(command, CancellationToken.None));
    }
}
```

- The three fields + `CreateHandler()` — xUnit creates a **fresh class instance per test** (you get clean state without resetting anything — a framework behavior worth knowing).
- `Handle(command, CancellationToken.None)` — calling the handler *directly*, no MediatR: it's a plain class; the pipeline is integration-tested separately. (Want to test *with* the `ValidationBehavior`? Resolve a real `ISender` with `services.AddMediatR(...)` + `AddValidatorsFromAssembly...` + the behavior registration in a small test fixture — the same registrations as Chapter 21 in a test container. An excellent exercise.)
- One validator test to complete the picture:

```csharp
// File: tests/ShopSphere.Application.UnitTests/Products/CreateProductCommandValidatorTests.cs
using ShopSphere.Application.Features.Products.CreateProduct;
using Xunit;

namespace ShopSphere.Application.UnitTests.Products;

public class CreateProductCommandValidatorTests
{
    private readonly CreateProductCommandValidator _validator = new();

    [Fact]
    public void Validate_NegativePrice_FailsWithPriceError()
    {
        var command = new CreateProductCommand("Desk", "DESK-STD-01", -1m, "USD", 5, null);

        var result = _validator.Validate(command);

        Assert.False(result.IsValid);
        Assert.Contains(result.Errors,
            error => error.PropertyName == nameof(CreateProductCommand.PriceAmount));
    }
}
```

### 24.5 Integration Tests — The Whole Beast, In-Memory and In-Docker

Unit tests proved the parts; integration tests prove the **assembly**: HTTP → MediatR → validation → handlers → EF Core → *a real SQL Server* → back. `WebApplicationFactory<Program>` boots our actual `Program.cs` in-memory; Testcontainers supplies the database; we splice them together by **replacing the DbContext registration**:

```csharp
// File: tests/ShopSphere.Api.IntegrationTests/ShopSphereApiFactory.cs
using System.Data.Common;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc.Testing;
using Microsoft.AspNetCore.TestHost;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection.Extensions;
using ShopSphere.Infrastructure.Persistence;
using Testcontainers.MsSql;

namespace ShopSphere.Api.IntegrationTests;

public sealed class ShopSphereApiFactory
    : WebApplicationFactory<Program>, IAsyncLifetime
{
    private readonly MsSqlContainer _dbContainer = new MsSqlBuilder()
        .WithImage("mcr.microsoft.com/mssql/server:2022-latest")
        .WithPassword("YourStrong@Passw0rd")
        .Build();

    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.ConfigureTestServices(services =>
        {
            services.RemoveAll<DbContextOptions<AppDbContext>>();

            services.AddDbContext<AppDbContext>(options =>
                options.UseSqlServer(_dbContainer.GetConnectionString()));
        });
    }

    public async Task InitializeAsync()
    {
        await _dbContainer.StartAsync();

        using var scope = Services.CreateScope();
        var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
        await dbContext.Database.MigrateAsync();
    }

    async Task IAsyncLifetime.DisposeAsync()
    {
        await _dbContainer.StopAsync();
    }
}
```

Line by line:

- **`: WebApplicationFactory<Program>`** — boots the real `Program.cs` (that `partial` finally works) on an in-memory server; `factory.CreateClient()` returns an `HttpClient` that talks to it without opening a port.
- **`new MsSqlBuilder()...Build()`** — declares the container: SQL Server 2022 (same image as Chapter 6) with our known password. Testcontainers picks a random free port, waits for the server to be *actually ready* (it probes health, not just "container running" — the flaky-test killer).
- **`ConfigureWebHost` + `ConfigureTestServices`** — the splice point: services registered *here* override the app's own. `services.RemoveAll<DbContextOptions<AppDbContext>>()` drops Chapter 21's registration (which points at `localhost:1433` and your dev password); `AddDbContext` re-adds it pointing at the container's ephemeral connection string (`GetConnectionString()` — host+port+the random mapping).
- **`IAsyncLifetime` (`InitializeAsync` / `DisposeAsync`)** — xUnit's async fixture hooks: start container → **apply migrations** (`Database.MigrateAsync()` — Chapter 21's option, doing its job) before the first test; stop the container after the last. One SQL Server per test *class run* (via `IClassFixture`), torn down completely — no shared mutable state between runs, ever. (`Program.cs`'s `DbInitializer` runs during boot too, inside the overridden DbContext — seeded data available for free.)
- Integration tests must never touch your **development** database — the override is not a convenience, it is a safety feature.

The test itself — the full round trip:

```csharp
// File: tests/ShopSphere.Api.IntegrationTests/Products/ProductsEndpointTests.cs
using System.Net.Http.Json;
using System.Text.Json;
using ShopSphere.Api.IntegrationTests;
using Xunit;

namespace ShopSphere.Api.IntegrationTests.Products;

public class ProductsEndpointTests(ShopSphereApiFactory factory)
    : IClassFixture<ShopSphereApiFactory>
{
    private readonly HttpClient _client = factory.CreateClient();

    [Fact]
    public async Task CreateProduct_ThenGetProductById_ReturnsCreatedProduct()
    {
        var command = new
        {
            name = "Integration Test Desk",
            sku = "DESK-IT-01",
            priceAmount = 199.99m,
            currency = "USD",
            stockQuantity = 4,
            categoryId = (Guid?)null
        };

        var createResponse = await _client.PostAsJsonAsync("/api/products", command);
        createResponse.EnsureSuccessStatusCode();

        var createdId = await createResponse.Content.ReadFromJsonAsync<Guid>();

        var getResponse = await _client.GetAsync($"/api/products/{createdId}");
        getResponse.EnsureSuccessStatusCode();

        var json = await getResponse.Content.ReadFromJsonAsync<JsonElement>();
        Assert.Equal("Integration Test Desk", json.GetProperty("name").GetString());
        Assert.Equal(4, json.GetProperty("stockQuantity").GetInt32());
    }

    [Fact]
    public async Task CreateProduct_WithInvalidBody_Returns400WithProblemDetails()
    {
        var command = new
        {
            name = "",
            sku = "x",
            priceAmount = -5m,
            currency = "US",
            stockQuantity = -1,
            categoryId = (Guid?)null
        };

        var response = await _client.PostAsJsonAsync("/api/products", command);

        Assert.Equal(System.Net.HttpStatusCode.BadRequest, response.StatusCode);
        Assert.Equal("application/problem+json", response.Content.Headers.ContentType?.MediaType);
    }
}
```

- **`IClassFixture<ShopSphereApiFactory>` + primary-constructor injection** — xUnit's modern sharing: the factory (and its container) is built once for the class, injected as a constructor parameter.
- **`PostAsJsonAsync` / `ReadFromJsonAsync<T>`** — `System.Net.Http.Json`'s extensions (built into .NET): serialize/deserialize without manual strings.
- **`JsonElement` + `GetProperty("name")`** — reading JSON by property when you don't want to mirror every DTO in the test project; trade-off: stringly-typed assertions (a local record `ProductApiResult(string Name, ...)` with `ReadFromJsonAsync<ProductApiResult>` is the stronger-typed alternative — both are respectable).
- The second test asserts the **entire Chapter 23 pipeline** as observable behavior: 400 status + the `application/problem+json` media type — validation, behavior, middleware, and ProblemDetails verified in one round trip, exactly as a real client experiences them.

Run everything:

```bash
dotnet test
```

Unit tests flash by in milliseconds; integration tests take longer (first run pulls the SQL Server image if Docker hasn't cached it) — and then you own the full testing pyramid.

### 24.6 What to Test Where (The Judgment Table)

| Layer | Test focus | Don't bother testing |
|---|---|---|
| Domain | Every invariant and factory, state transitions (`MarkPaid`), value objects | Trivial getters |
| Application | Each handler's scenarios (happy, not-found, conflict, domain-rule) + validators | MediatR itself; the pipeline (covered by integration) |
| Infrastructure | (Optional, small) tricky queries — via integration tests | EF Core's own correctness |
| Api | End-to-end round trips incl. error formats; status codes; Location headers | Controller internals (they have none — Chapter 22) |

### Key Takeaways — Chapter 24

- Three test projects map to three layers; purity becomes testability.
- Domain tests: `[Theory]`-driven invariants, asserting exception *messages* as behavior. Handler tests: hand-written fakes (`Task.CompletedTask`/`FromResult`), spies (`SaveCalls`), direct `Handle` calls.
- Integration: `WebApplicationFactory<Program>` + `Testcontainers.MsSql`, `RemoveAll<DbContextOptions>` + re-register, `MigrateAsync` in `InitializeAsync`, one real SQL Server per run, never touching dev data.
- Assert externally visible behavior (status codes, media types, JSON shape) — not implementation.

---

## Chapter 25 — Advanced Techniques: Domain Events, Auditing, Soft Delete, Concurrency

Four techniques that separate "uses Clean Architecture" from "commands it." Each extends what you built without breaking a single earlier rule — that's the proof the architecture works.

### 25.1 Domain Events: Growing Without Editing

**The problem.** When a product is created, marketing wants an email draft, analytics wants a row, search wants indexing. Where does that code go? Not in `Product.Create` (the Domain must not send emails), not in the handler (each new reaction means editing `CreateProductCommandHandler` — a growing pile of secondary duties). **Domain events** flip the direction: the entity *announces* what happened; any number of *reactions* subscribe — and adding a reaction means adding a file, never editing one. Chapter 11's `INotification`/`Publish` broadcast is the delivery mechanism.

**Step 1 — the Domain stays pure.** A marker interface, no packages:

```csharp
// File: src/ShopSphere.Domain/Common/IDomainEvent.cs
namespace ShopSphere.Domain.Common;

public interface IDomainEvent
{
}
```

```csharp
// File: src/ShopSphere.Domain/Products/ProductCreatedEvent.cs
using ShopSphere.Domain.Common;

namespace ShopSphere.Domain.Products;

public sealed record ProductCreatedEvent(Guid ProductId, string Sku) : IDomainEvent;
```

The event is a **fact, in past tense**: immutable data about something that happened — never a command ("do X"), always a record ("X happened").

**Step 2 — `BaseEntity` collects events.** Append to Chapter 9's class:

```csharp
private readonly List<IDomainEvent> _domainEvents = [];

public IReadOnlyCollection<IDomainEvent> DomainEvents => _domainEvents.AsReadOnly();

public void AddDomainEvent(IDomainEvent domainEvent) => _domainEvents.Add(domainEvent);

public void ClearDomainEvents() => _domainEvents.Clear();
```

The same private-list-plus-readonly-wrapper trick as `Order._items` (Chapter 9): outsiders observe, only the entity publishes and (after dispatch) clears. Then raise it at the moment of truth — the last line of `Product.Create`:

```csharp
var product = new Product(name, sku, price, stockQuantity, category);
product.AddDomainEvent(new ProductCreatedEvent(product.Id, sku));
return product;
```

**Step 3 — the bridge to MediatR (in Application).** MediatR dispatches by *notification type*; our events implement our own `IDomainEvent`, not MediatR's `INotification` (the Domain cannot reference MediatR — Chapter 8's rule holds). The bridge is a generic wrapper notification:

```csharp
// File: src/ShopSphere.Application/Common/Events/DomainEventNotification.cs
using MediatR;
using ShopSphere.Domain.Common;

namespace ShopSphere.Application.Common.Events;

public sealed record DomainEventNotification<TDomainEvent>(TDomainEvent DomainEvent)
    : INotification
    where TDomainEvent : IDomainEvent;
```

Publishing a `DomainEventNotification<ProductCreatedEvent>` makes MediatR look for handlers of exactly that closed type — Domain and MediatR never touch, the Application layer shakes hands for them.

**Step 4 — the Unit of Work dispatches *after* commit.** This is where the pattern earns its keep. Upgrade Chapter 19's `UnitOfWork` (its constructor gains `IPublisher` — MediatR's one-to-many sender, resolved through Infrastructure's reference to Application):

```csharp
// File: src/ShopSphere.Infrastructure/Persistence/UnitOfWork.cs (upgraded)
using MediatR;
using Microsoft.EntityFrameworkCore;
using ShopSphere.Application.Common.Events;
using ShopSphere.Application.Common.Interfaces;
using ShopSphere.Domain.Common;

namespace ShopSphere.Infrastructure.Persistence;

public sealed class UnitOfWork(AppDbContext context, IPublisher publisher) : IUnitOfWork
{
    public async Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        var result = await context.SaveChangesAsync(cancellationToken);

        await DispatchDomainEventsAsync(cancellationToken);

        return result;
    }

    private async Task DispatchDomainEventsAsync(CancellationToken cancellationToken)
    {
        var entities = context.ChangeTracker
            .Entries<BaseEntity>()
            .Where(entry => entry.Entity.DomainEvents.Count > 0)
            .Select(entry => entry.Entity)
            .ToList();

        foreach (var entity in entities)
        {
            var events = entity.DomainEvents.ToList();
            entity.ClearDomainEvents();

            foreach (var domainEvent in events)
            {
                await PublishNotificationAsync((dynamic)domainEvent, cancellationToken);
            }
        }
    }

    private Task PublishNotificationAsync<TDomainEvent>(
        TDomainEvent domainEvent,
        CancellationToken cancellationToken)
        where TDomainEvent : IDomainEvent
    {
        return publisher.Publish(
            new DomainEventNotification<TDomainEvent>(domainEvent),
            cancellationToken);
    }
}
```

- **`ChangeTracker.Entries<BaseEntity>()` → `.Entity`** — the same tracker API as the audit stamping, used to find *who is carrying events*: any tracked entity (products, orders) that raised something this request.
- **Collect → clear → publish**: clear *before* publishing so a handler that triggers another save cannot redispatch the same events (infinite-loop protection).
- **`(dynamic)domainEvent`** — the one `dynamic` in the book, earning its keep: at runtime, C# dispatches the call to `PublishNotificationAsync<T>` *with the concrete event type* (`PublishNotificationAsync<ProductCreatedEvent>`), so the notification is `DomainEventNotification<ProductCreatedEvent>` and MediatR routes to the *specific* handler. (Alternatives for the reflection-averse: an explicit `switch` over event types, or the `MediatR.Contracts` mini-package referenced from Domain — a legitimate exception some teams take.)
- **Why dispatch after `SaveChanges`, not before?** Handlers react to *facts*, and it's only a fact once it's committed. The trade-off is honest: if a handler throws after a successful commit, the data is saved but the reaction failed — for critical reactions (emails, payments) the industry answer is the **Outbox pattern** (persist events as rows in the same transaction, dispatch from a background worker — a worthy next study, pointed to in Chapter 26).

**Step 5 — a reaction (handler), in as little as one file:**

```csharp
// File: src/ShopSphere.Infrastructure/Events/ProductCreatedEventHandler.cs
using MediatR;
using Microsoft.Extensions.Logging;
using ShopSphere.Application.Common.Events;
using ShopSphere.Domain.Products;

namespace ShopSphere.Infrastructure.Events;

public sealed class ProductCreatedEventHandler(
    ILogger<ProductCreatedEventHandler> logger)
    : INotificationHandler<DomainEventNotification<ProductCreatedEvent>>
{
    public Task Handle(
        DomainEventNotification<ProductCreatedEvent> notification,
        CancellationToken cancellationToken)
    {
        var domainEvent = notification.DomainEvent;

        logger.LogInformation(
            "Product {ProductId} (SKU {Sku}) entered the catalog.",
            domainEvent.ProductId,
            domainEvent.Sku);

        return Task.CompletedTask;
    }
}
```

- **`: INotificationHandler<DomainEventNotification<ProductCreatedEvent>>`** — subscribes to exactly one event type; MediatR's `AddMediatR` scan registered it automatically (it lives in an assembly the scan covers). Add a second, third, tenth reaction the same way — **`Product.Create` and the command handler never change**. This handler lives in *Infrastructure* because it's a technical reaction (logging); a pure business reaction would live in Application just as happily.
- Rebuild and create a product: watch the log line appear *after* the 201 — the announcement flowed entity → Unit of Work → MediatR → handler, invisible to the slice that started it.

### 25.2 Beyond Timestamps: Who Changed It

`CreatedAtUtc`/`UpdatedAtUtc` answer *when*; production systems also need *who*. The same port trick as everything else: declare the need in Application — `public interface ICurrentUserService { Guid? UserId; }` — implement it in Api (reading the authenticated user from `HttpContext`), register it, and consume it in the `SaveChangesAsync` override's stamping block (Chapter 19) to set a `CreatedByUtc`-style column. Authentication (JWT) would supply real user ids — deliberately out of this book's scope (see the roadmap).

### 25.3 Soft Delete: Deleting Without Deleting

Hard `DELETE` loses history forever. **Soft delete** keeps the row and hides it: add `IsDeleted` to the entity with a `Delete()` method that sets it, map `builder.Property(p => p.IsDeleted)`, and teach EF to hide deleted rows *everywhere* with a **global query filter** in `ProductConfiguration`:

```csharp
builder.HasQueryFilter(product => !product.IsDeleted);
```

From that one line, every query EF translates — `GetByIdAsync`, `GetPagedAsync`, future joins — automatically appends `WHERE [IsDeleted] = 0`. Two mastery caveats: (1) the **unique index must become a *filtered* index** — `.HasFilter("[IsDeleted] = 0")` on `IX_Products_Sku` — or a deleted product's SKU is forever burned; (2) global filters are *invisible*, which is their power *and* their trap — use `IgnoreQueryFilters()` deliberately in the rare admin query that must see deleted rows.

### 25.4 Optimistic Concurrency: The Last Item In Stock

Two customers request the last desk simultaneously (Chapter 9's cliffhanger). Both handlers load `StockQuantity = 1`, both pass `DecreaseStock(1)`, and the last `UPDATE` wins silently — one desk, two orders. Database-level **optimistic concurrency** catches the race: add to `Product` a `public byte[] RowVersion { get; private set; } = [];` and configure `builder.Property(p => p.RowVersion).IsRowVersion();`. SQL Server's `rowversion` type is a database-maintained counter: **every** update to the row bumps it automatically. EF then writes updates as `UPDATE ... WHERE [Id] = @id AND [RowVersion] = @original` — the second request's update matches zero rows and EF throws `DbUpdateConcurrencyException` (catch it where you choose — per-handler for custom messages, or a middleware arm mapping it to `409 Conflict`, extending Chapter 23's table). "Optimistic" because you proceed hoping for no conflict and *detect* it cheaply at write time — versus locking rows pessimistically, which databases scale badly. Migrations (Chapter 20's `migrations add`) add the column; the race that mattered is now impossible.

### 25.5 The Result Pattern: Exceptions Are Not the Only Way

This book's handlers signal failures by *throwing* typed exceptions that middleware translates — simple, stack-unwinding, idiomatic C#. A widely used alternative returns values that *are* the outcome:

```csharp
public sealed record Error(string Code, string Message);

public sealed record Result<T>(bool IsSuccess, T? Value, Error? Error)
{
    public static Result<T> Success(T value) => new(true, value, null);
    public static Result<T> Failure(Error error) => new(false, default, error);
}
```

Handlers return `Result<Guid>`; controllers switch on `IsSuccess` (mapping `Error.Code` to status codes). Trade-offs, honestly: results make failure **part of the signature** (callers can't forget it) and cost no stack unwinding — but they thread `Result` through every signature, and "invalid input" style errors get bulky fast. Exceptions keep the happy path clean and suit *exceptional* conditions. Either works with this architecture (the pipeline/validators/middleware all stay); pick one per codebase and stay consistent.

### Key Takeaways — Chapter 25

- Domain events: marker interface in Domain (zero packages), raised via `AddDomainEvent`, collected on `BaseEntity`, dispatched by the Unit of Work *after* commit through the `DomainEventNotification<T>` bridge — reactions add files, never edit them; `dynamic` dispatch selects the concrete handler; Outbox is the industrial-strength next step.
- Global query filters implement soft delete invisibly; unique indexes must become filtered (`HasFilter`) or deleted rows poison keys.
- `rowversion` + `IsRowVersion()` = optimistic concurrency: the second writer gets `DbUpdateConcurrencyException` → map to 409.
- Result pattern vs exceptions: signature-visible failure vs clean happy path — both compatible with this architecture; choose deliberately.

---

## Chapter 26 — The Mastery Mindset: Best Practices, Common Mistakes, and the Road Ahead

### 26.1 The Anti-Pattern Catalog (Learn the Smells, Not Just the Flowers)

**1. The anemic domain (relapse).** Six months in, deadlines bite, and someone adds `public void SetStock(int value)` "just this once." The entity becomes a property bag; rules migrate to handlers; every caller re-implements validation. *Smell:* entities with public setters and no methods. *Fix:* the door test — if a property can change, there must be exactly one guarded method that changes it.

**2. The fat controller / fat service.** Logic "temporarily" in the controller because "the handler is overkill for this." Controllers are adapters; the moment they decide anything, the architecture has a leak. *Fix:* if it's a use case, it's a slice — even a five-line one.

**3. `IQueryable` escaping Infrastructure.** A repository returning `IQueryable<T>` hands callers an untranslatable SQL bomb (callers compose filters that execute *whenever enumerated* — hidden queries, hidden N+1s, hidden coupling to EF). *Fix:* repositories return materialized data (`List<T>`, `PagedResult<T>`) or DTO projections. The port is a wall, not a window.

**4. Leaking `DbContext`.** Anyone but Infrastructure resolving `AppDbContext` (a handler "optimizing" a query, a controller shortcut). *Fix:* nothing to fix — your project references already make this a compile error. That's Chapter 7's four commands protecting you forever.

**5. Domain entities over the wire.** Returning `Product` from the controller serializes *everything* (lazy navigation traps, internal fields, future sensitive columns) and welds your public API to your domain model. *Fix:* DTOs out, always (Chapter 14's `ProductResponse`).

**6. "Folder by kind" relapse.** A `Services/` folder sprouting inside Application "for shared logic." Shared logic is usually accidentally-duplicated logic (Chapter 12). *Fix:* new use case → new slice folder.

**7. The validator that validates nothing.** Validators that call repositories to check *contextual* rules (does the SKU exist?) — that's handler work (and a DB round trip hidden inside "input validation"). *Fix:* validators check shape; handlers check context.

**8. Over-abstracting.** Interfaces for things with exactly one implementation and no test seam value (`IProductFactory`, `INameValidator`); generic repositories with 30 methods. Each abstraction must buy something (swapping, testing, boundary) — or it only costs.

### 26.2 The Generic Repository Debate (Settled for Practice)

`IRepository<T>` with `Add/Get/Update/Delete` promises DRY but delivers: `IQueryable` temptation, methods most entities never use, and still a specific interface the moment a real query appears ("find by SKU where not deleted and..."). The pragmatic consensus (and this book's position): **specific, minimal repository interfaces per aggregate** — `IProductRepository` with exactly the methods the use cases actually demand — grown incrementally as you watched Chapters 13–15 do. If many repositories end up sharing `GetByIdAsync`, a small shared *base class* (private, in Infrastructure) removes the typing without exposing a public generic contract.

### 26.3 Reads at Scale: Project, Don't Materialize

`GetPagedAsync` loads full tracked entities and maps them — correct, simple, and fine to tens of thousands of rows. When lists get heavy (wide tables, hot endpoints), move the projection *into* the query: define the port to return DTOs (`Task<PagedResult<ProductListItem>>`) and let the repository `Select` straight into it — EF then emits `SELECT` with only those columns, no tracking, no over-fetch. This is CQRS's read-side optimization arriving exactly when the read side needs it — and it changes *one interface and one implementation* (the beauty of the boundary). Beyond that: Dapper for hand-tuned SQL beside EF Core, SQL views, or a separate read store — each a *later* decision the architecture leaves room for.

### 26.4 When NOT to Use Clean Architecture (The Mastery Answer)

Say it with confidence: **a 3-table CRUD admin panel does not need four projects.** Clean Architecture pays when business logic is real and long-lived; it *costs* files, indirection, and onboarding time. The honest decision rule from Chapter 3, now with experience behind it: if you cannot name five business rules of your domain, build the simple layered app and revisit. Knowing when to *not* apply a pattern is mastery; the pattern serves the product, never the reverse.

### 26.5 Scaling From Here (Your Roadmap, In Order)

1. **Harden the current app:** add `UpdateCategory`/`DeleteCategory`/`GetOrderById` slices yourself (you need no book for them anymore); wire JWT authentication (the Api layer concern), then implement `ICurrentUserService` (25.2) for real.
2. **Read the two living references:** [dotnet/eShop](https://github.com/dotnet/eShop) (Microsoft's reference app — same patterns, production scale) and the [ardalis/CleanArchitecture](https://github.com/ardalis/CleanArchitecture) template (compare its choices with yours — Ardalis splits read/write DbContexts, for example).
3. **Operationally:** Dockerize Api + SQL Server (`docker-compose`), then explore **.NET Aspire** — Microsoft's toolkit for orchestrating exactly these multi-project apps locally with service discovery and dashboards.
4. **Architecturally deeper:** the Outbox pattern for reliable events; the Specification pattern for composable queries; **modular monoliths** (slices → modules → bounded contexts) as the stepping stone before microservices; and only then event sourcing / distributed CQRS — topics that *build on* everything in this book, none of which replace it.

### 26.6 What You Now Own

You can explain every word of the title "Clean Architecture with .NET 10 and SQL Server" — not as jargon, but as decisions you have *made with your own hands*: a dependency-proofed domain with guarded entities and aggregates; an application layer of vertical slices (Command/Handler/Validator) delivered by MediatR through a validating pipeline; an infrastructure layer that pays the ports' bills with EF Core, repositories, one atomic Unit of Work, migrations, and seeding; and an API shell thin enough to delete and regenerate. You verified it end to end with `curl`, and you hardened it with a three-tier test suite — real SQL Server included. The circles diagram is no longer a picture you saw once. It's a system you built, line by line, and can defend, extend, and teach. That is mastery — everything else is mileage.

### Key Takeaways — Chapter 26

- Eight smells to police forever: anemic entities, fat controllers, `IQueryable` leaks, `DbContext` leaks (compile-blocked already), entities over the wire, folder-by-kind relapse, context-checking validators, and over-abstraction.
- Specific minimal repositories > generic ones; project to DTOs inside queries when reads get heavy.
- The mastery answer to "when not to use it" is a real answer, not hedging.
- Roadmap: finish the remaining slices → JWT + current user → eShop/Ardalis study → Docker/Aspire → Outbox/Specification/modular monolith.

---

# APPENDICES

## Appendix A — The Complete Solution Folder Tree

```
shopsphere/
├── ShopSphere.sln
├── src/
│   ├── ShopSphere.Domain/                        (references: NOTHING)
│   │   ├── Common/
│   │   │   ├── BaseEntity.cs
│   │   │   ├── DomainException.cs
│   │   │   └── IDomainEvent.cs                   (Chapter 25)
│   │   ├── Orders/
│   │   │   ├── Order.cs
│   │   │   ├── OrderItem.cs
│   │   │   └── OrderStatus.cs
│   │   └── Products/
│   │       ├── Category.cs
│   │       ├── Money.cs
│   │       ├── Product.cs
│   │       └── ProductCreatedEvent.cs            (Chapter 25)
│   ├── ShopSphere.Application/                   (references: Domain)
│   │   ├── ApplicationAssemblyReference.cs
│   │   ├── DependencyInjection.cs
│   │   ├── Common/
│   │   │   ├── Behaviors/ValidationBehavior.cs
│   │   │   ├── Events/DomainEventNotification.cs (Chapter 25)
│   │   │   ├── Exceptions/
│   │   │   │   ├── ConflictException.cs
│   │   │   │   └── NotFoundException.cs
│   │   │   ├── Interfaces/
│   │   │   │   ├── ICategoryRepository.cs
│   │   │   │   ├── IOrderRepository.cs
│   │   │   │   ├── IProductRepository.cs
│   │   │   │   └── IUnitOfWork.cs
│   │   │   └── Models/PagedResult.cs
│   │   └── Features/
│   │       ├── Categories/
│   │       │   ├── CreateCategory/  (Command, Handler, Validator)
│   │       │   └── GetCategories/   (Query, Handler, CategoryResponse)
│   │       ├── Orders/
│   │       │   └── PlaceOrder/      (Command, Handler, Validator, OrderLineRequest)
│   │       └── Products/
│   │           ├── CreateProduct/   (Command, Handler, Validator)
│   │           ├── DeleteProduct/   (Command, Handler)
│   │           ├── GetProductById/  (Query, Handler, ProductResponse)
│   │           ├── GetProducts/     (Query, Handler, Validator)
│   │           └── UpdateProduct/   (Command, Handler, Validator)
│   ├── ShopSphere.Infrastructure/                (references: Application)
│   │   ├── DependencyInjection.cs
│   │   ├── Events/ProductCreatedEventHandler.cs  (Chapter 25)
│   │   ├── Persistence/
│   │   │   ├── AppDbContext.cs
│   │   │   ├── DbInitializer.cs
│   │   │   ├── UnitOfWork.cs
│   │   │   ├── Configurations/
│   │   │   │   ├── CategoryConfiguration.cs
│   │   │   │   ├── OrderConfiguration.cs
│   │   │   │   └── ProductConfiguration.cs
│   │   │   └── Migrations/                        (generated, Chapter 20)
│   │   └── Repositories/
│   │       ├── CategoryRepository.cs
│   │       ├── OrderRepository.cs
│   │       └── ProductRepository.cs
│   └── ShopSphere.Api/                            (references: Application, Infrastructure)
│       ├── Program.cs
│       ├── appsettings.json
│       ├── Controllers/
│       │   ├── CategoriesController.cs
│       │   ├── OrdersController.cs
│       │   └── ProductsController.cs
│       └── Middleware/ExceptionHandlingMiddleware.cs
└── tests/
    ├── ShopSphere.Api.IntegrationTests/           (references: Api)
    │   ├── ShopSphereApiFactory.cs
    │   └── Products/ProductsEndpointTests.cs
    ├── ShopSphere.Application.UnitTests/          (references: Application)
    │   ├── Fakes/ (FakeCategoryRepository, FakeProductRepository, FakeUnitOfWork)
    │   └── Products/ (CreateProductCommandHandlerTests, CreateProductCommandValidatorTests)
    └── ShopSphere.Domain.UnitTests/               (references: Domain)
        └── Products/ProductTests.cs
```

## Appendix B — Every Command in Order

```bash
# ── Chapter 6: environment ─────────────────────────────────────────
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=YourStrong@Passw0rd" \
  -p 1433:1433 --name shopsphere-sql -d mcr.microsoft.com/mssql/server:2022-latest
dotnet tool install --global dotnet-ef --version 10.0.0

# ── Chapter 7: solution skeleton ───────────────────────────────────
mkdir shopsphere && cd shopsphere
dotnet new sln -n ShopSphere
dotnet new classlib -n ShopSphere.Domain -o src/ShopSphere.Domain
dotnet new classlib -n ShopSphere.Application -o src/ShopSphere.Application
dotnet new classlib -n ShopSphere.Infrastructure -o src/ShopSphere.Infrastructure
dotnet new web -n ShopSphere.Api -o src/ShopSphere.Api
rm src/ShopSphere.Domain/Class1.cs src/ShopSphere.Application/Class1.cs src/ShopSphere.Infrastructure/Class1.cs
dotnet sln add src/ShopSphere.Domain/ShopSphere.Domain.csproj \
               src/ShopSphere.Application/ShopSphere.Application.csproj \
               src/ShopSphere.Infrastructure/ShopSphere.Infrastructure.csproj \
               src/ShopSphere.Api/ShopSphere.Api.csproj
dotnet add src/ShopSphere.Application/ShopSphere.Application.csproj reference src/ShopSphere.Domain/ShopSphere.Domain.csproj
dotnet add src/ShopSphere.Infrastructure/ShopSphere.Infrastructure.csproj reference src/ShopSphere.Application/ShopSphere.Application.csproj
dotnet add src/ShopSphere.Api/ShopSphere.Api.csproj reference src/ShopSphere.Application/ShopSphere.Application.csproj
dotnet add src/ShopSphere.Api/ShopSphere.Api.csproj reference src/ShopSphere.Infrastructure/ShopSphere.Infrastructure.csproj
cd src/ShopSphere.Application
dotnet add package MediatR --version 12.5.0
dotnet add package FluentValidation --version 12.2.0
dotnet add package FluentValidation.DependencyInjectionExtensions --version 12.2.0
cd ../ShopSphere.Infrastructure
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 10.0.0
cd ../ShopSphere.Api
dotnet add package Microsoft.EntityFrameworkCore.Design --version 10.0.0
dotnet add package Microsoft.AspNetCore.OpenApi --version 10.0.0
cd ../..

# ── Chapters 9-23: code, folders, builds ──────────────────────────
mkdir -p src/ShopSphere.Domain/Common src/ShopSphere.Domain/Products src/ShopSphere.Domain/Orders
mkdir -p src/ShopSphere.Application/Common/{Interfaces,Exceptions,Models,Behaviors,Events}
mkdir -p src/ShopSphere.Application/Features/Products/{CreateProduct,UpdateProduct,DeleteProduct,GetProductById,GetProducts}
mkdir -p src/ShopSphere.Application/Features/Categories/{CreateCategory,GetCategories}
mkdir -p src/ShopSphere.Application/Features/Orders/PlaceOrder
mkdir -p src/ShopSphere.Infrastructure/Persistence/Configurations src/ShopSphere.Infrastructure/Repositories
mkdir -p src/ShopSphere.Api/Controllers src/ShopSphere.Api/Middleware
dotnet build

# ── Chapter 20: migrations ─────────────────────────────────────────
dotnet ef migrations add InitialCreate \
  --project src/ShopSphere.Infrastructure \
  --startup-project src/ShopSphere.Api \
  --output-dir Persistence/Migrations
dotnet ef database update \
  --project src/ShopSphere.Infrastructure \
  --startup-project src/ShopSphere.Api

# ── Chapter 22: run & try ──────────────────────────────────────────
dotnet run --project src/ShopSphere.Api
curl "http://localhost:5000/api/products?pageSize=10"

# ── Chapter 24: test projects ──────────────────────────────────────
dotnet new xunit -n ShopSphere.Domain.UnitTests -o tests/ShopSphere.Domain.UnitTests
dotnet new xunit -n ShopSphere.Application.UnitTests -o tests/ShopSphere.Application.UnitTests
dotnet new xunit -n ShopSphere.Api.IntegrationTests -o tests/ShopSphere.Api.IntegrationTests
dotnet sln add tests/ShopSphere.Domain.UnitTests tests/ShopSphere.Application.UnitTests tests/ShopSphere.Api.IntegrationTests
dotnet add tests/ShopSphere.Domain.UnitTests reference src/ShopSphere.Domain
dotnet add tests/ShopSphere.Application.UnitTests reference src/ShopSphere.Application
dotnet add tests/ShopSphere.Api.IntegrationTests reference src/ShopSphere.Api
cd tests/ShopSphere.Api.IntegrationTests
dotnet add package Microsoft.AspNetCore.Mvc.Testing
dotnet add package Testcontainers.MsSql
cd ../..
dotnet test
```

## Appendix C — Package and Version Cheat Sheet

| Project | Package | Version | Layer role |
|---|---|---|---|
| ShopSphere.Domain | *(none — ever)* | — | Purity is the rule |
| ShopSphere.Application | `MediatR` | 12.5.0 | Messages, handlers, pipeline (last Apache-2.0 release; see Ch. 11 for 13+/alternatives) |
| ShopSphere.Application | `FluentValidation` | 12.2.0 | `AbstractValidator<T>` |
| ShopSphere.Application | `FluentValidation.DependencyInjectionExtensions` | 12.2.0 | `AddValidatorsFromAssembly...()` |
| ShopSphere.Infrastructure | `Microsoft.EntityFrameworkCore.SqlServer` | 10.0.0 (or latest 10.x) | Provider: `UseSqlServer`, migrations |
| ShopSphere.Api | `Microsoft.EntityFrameworkCore.Design` | 10.0.0 (or latest 10.x) | `dotnet ef` design-time support |
| ShopSphere.Api | `Microsoft.AspNetCore.OpenApi` | 10.0.0 (or latest 10.x) | `AddOpenApi()` / `MapOpenApi()` |
| Api.IntegrationTests | `Microsoft.AspNetCore.Mvc.Testing` | latest 10.x | `WebApplicationFactory<Program>` |
| Api.IntegrationTests | `Testcontainers.MsSql` | latest 4.x | Real SQL Server per test run |

**Global tools:** `dotnet-ef` 10.0.0 · **Platform:** .NET SDK 10.0 (LTS, C# 14) · **Database:** SQL Server 2022 (image `mcr.microsoft.com/mssql/server:2022-latest`) · **Docker image password from Chapter 6:** `YourStrong@Passw0rd` (change it in anything real).
