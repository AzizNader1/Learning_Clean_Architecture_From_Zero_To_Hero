# Master Clean Architecture in .NET from A to Z

## Table of Contents

1. [Introduction to Clean Architecture](#chapter-1-introduction-to-clean-architecture)
2. [Core Principles and Concepts](#chapter-2-core-principles-and-concepts)
3. [The Four Layers Explained](#chapter-3-the-four-layers-explained)
4. [Project Structure and Organization](#chapter-4-project-structure-and-organization)
5. [Domain Layer Deep Dive](#chapter-5-domain-layer-deep-dive)
6. [Application Layer Implementation](#chapter-6-application-layer-implementation)
7. [Infrastructure Layer Design](#chapter-7-infrastructure-layer-design)
8. [Presentation Layer Patterns](#chapter-8-presentation-layer-patterns)
9. [Dependency Injection and Inversion](#chapter-9-dependency-injection-and-inversion)
10. [Repository Pattern and Unit of Work](#chapter-10-repository-pattern-and-unit-of-work)
11. [CQRS with MediatR](#chapter-11-cqrs-with-mediatr)
12. [Validation and Error Handling](#chapter-12-validation-and-error-handling)
13. [Domain Events and Integration Events](#chapter-13-domain-events-and-integration-events)
14. [Mapping and DTOs](#chapter-14-mapping-and-dtos)
15. [Testing Clean Architecture](#chapter-15-testing-clean-architecture)
16. [Migration Strategies](#chapter-16-migration-strategies)
17. [Common Anti-Patterns](#chapter-17-common-anti-patterns)
18. [Real-World Implementation](#chapter-18-real-world-implementation)
19. [Best Practices and Guidelines](#chapter-19-best-practices-and-guidelines)
20. [Quick Reference Cheatsheet](#chapter-20-quick-reference-cheatsheet)
21. [Additional Resources](#chapter-21-additional-resources)

---

## Chapter 1: Introduction to Clean Architecture

### What is Clean Architecture?

Clean Architecture is a software design philosophy introduced by Robert C. Martin (Uncle Bob) in 2012, building upon earlier architectural patterns like Hexagonal Architecture (Alistair Cockburn), Onion Architecture (Jeffrey Palermo), and Ports and Adapters. At its core, Clean Architecture is about organizing code in a way that separates concerns, makes the business logic independent of external frameworks, and ensures that the most important code—the business rules—remains at the center of your application, protected from changes in the outer world.

The fundamental premise of Clean Architecture is that business logic is the heart of any application, and everything else—databases, web frameworks, UI, external APIs—is merely a detail that should depend on the business logic, not the other way around. This inversion of traditional dependency relationships leads to systems that are easier to maintain, test, and evolve over time. When the database changes, or when you switch from a REST API to GraphQL, or when you replace your web framework, the core business rules remain untouched.

Clean Architecture achieves this through a layered approach where dependencies point inward. The inner layers contain business rules and know nothing about outer layers. The outer layers contain implementation details and depend on inner layers. This dependency rule ensures that business logic never depends on infrastructure, UI, or frameworks—only the other way around.

### Why Clean Architecture Matters

Modern software systems face constant pressure to change. Business requirements evolve, technologies advance, and what worked yesterday may not work tomorrow. Traditional architectures that tightly couple business logic to infrastructure become brittle over time. A change to the database schema ripples through the data access layer, into the business logic, and up to the UI. Testing becomes difficult because you can't test business rules without spinning up a database. The system becomes a monolithic tangle where everything depends on everything else.

Clean Architecture addresses these challenges by establishing clear boundaries between different parts of the system. When your business logic is isolated in its own layer with no dependencies on external concerns, you can test it in isolation with fast unit tests. When your data access is abstracted behind interfaces defined in the application layer, you can swap databases without touching business logic. When your UI is just a detail that calls application services, you can add a new UI without changing any business code.

The benefits extend beyond technical concerns. Clean Architecture creates a shared vocabulary and structure that helps teams communicate. New developers can quickly understand where code belongs. Code reviews become more focused because the boundaries are clear. The architecture itself becomes documentation—by looking at the project structure, you understand the system's intent.

### The Dependency Rule

The cornerstone of Clean Architecture is the dependency rule: source code dependencies must point inward, toward higher-level policies. This rule applies across all layer boundaries. Nothing in an inner circle can know anything about something in an outer circle. This includes function names, classes, variables, and any other named software entity.

```
Clean Architecture Dependency Rule:

┌─────────────────────────────────────────────────────────────────┐
│                    Frameworks & Drivers                          │
│         (Web, Database, External APIs, UI Frameworks)           │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                  Interface Adapters                        │  │
│  │      (Controllers, Gateways, Presenters, Repositories)    │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │              Application Business Rules              │  │  │
│  │  │            (Use Cases, Application Services)         │  │  │
│  │  │  ┌─────────────────────────────────────────────────┐│  │  │
│  │  │  │           Enterprise Business Rules              ││  │  │
│  │  │  │         (Entities, Domain Services)              ││  │  │
│  │  │  └─────────────────────────────────────────────────┘│  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘

Dependencies flow INWARD →
Outer layers depend on inner layers
Inner layers know NOTHING about outer layers
```

### Clean Architecture vs Other Architectures

| Architecture | Key Concept | Dependency Direction | Primary Benefit |
|--------------|-------------|---------------------|-----------------|
| **Clean Architecture** | Concentric layers with dependency rule | Inward toward business rules | Framework independence, testability |
| **Layered Architecture** | Horizontal layers (UI, BLL, DAL) | Downward from UI to Data | Simple separation of concerns |
| **Hexagonal Architecture** | Ports and adapters around core | Inward toward domain | External system independence |
| **Onion Architecture** | Core surrounded by layers | Inward toward center | Domain-centric design |
| **Vertical Slice** | Feature-based organization | Within feature boundaries | Cohesive features, simplicity |
| **Microservices** | Independent deployable services | Service boundaries | Scalability, team autonomy |

```csharp
// The difference in code organization:

// Traditional Layered Architecture - problems arise when layers are coupled
public class UserService
{
    private readonly SqlServerUserRepository _repository; // Direct dependency on infrastructure!
    private readonly SmtpEmailSender _emailSender;        // Direct dependency on implementation!
    
    public UserService(SqlServerUserRepository repository, SmtpEmailSender emailSender)
    {
        _repository = repository;
        _emailSender = emailSender;
    }
    
    public void RegisterUser(UserDto dto)
    {
        // Business logic mixed with infrastructure concerns
        var user = new User { Email = dto.Email };
        _repository.Save(user);  // Direct database call
        _emailSender.SendWelcomeEmail(user.Email);  // Direct email call
    }
}

// Clean Architecture - dependencies inverted, business logic protected
// Domain Layer - no external dependencies
public class User
{
    public Email Email { get; private set; }
    
    public static User Create(Email email)
    {
        // Business rules enforced in domain
        return new User { Email = email };
    }
}

// Application Layer - depends only on abstractions
public class RegisterUserCommandHandler
{
    private readonly IUserRepository _userRepository;
    private readonly IEmailSender _emailSender;
    
    public RegisterUserCommandHandler(IUserRepository userRepository, IEmailSender emailSender)
    {
        _userRepository = userRepository;
        _emailSender = emailSender;
    }
    
    public async Task Handle(RegisterUserCommand command)
    {
        var email = Email.Create(command.Email);
        var user = User.Create(email);
        await _userRepository.AddAsync(user);
        await _emailSender.SendWelcomeEmailAsync(user.Email);
    }
}

// Infrastructure Layer - implements interfaces defined in Application
public class SqlServerUserRepository : IUserRepository
{
    // Implementation details here
}
```

---

## Chapter 2: Core Principles and Concepts

### Separation of Concerns

Separation of concerns is the fundamental principle underlying Clean Architecture. It dictates that a program should be divided into distinct sections, each addressing a separate concern. A concern is a set of information that affects the code of a computer program. In Clean Architecture, concerns are separated into layers, with business concerns at the center and technical concerns at the periphery.

This separation manifests in several ways. The domain layer concerns itself only with business rules—what the business cares about. The application layer concerns itself with orchestrating use cases—how the business processes work. The infrastructure layer concerns itself with technical details—how data is stored and retrieved. The presentation layer concerns itself with user interaction—how information is displayed and collected.

When concerns are properly separated, a change in one area doesn't ripple into others. If you need to add a new field to a database table, the change is localized to the infrastructure layer. If you need to change how a calculation works, the change is in the domain layer. If you need to add a new API endpoint, the change is in the presentation layer. Each change has a clear home.

### Dependency Inversion Principle

The Dependency Inversion Principle (DIP) is the mechanism that makes Clean Architecture work. DIP states that high-level modules should not depend on low-level modules; both should depend on abstractions. Abstractions should not depend on details; details should depend on abstractions.

In traditional architectures, the business logic depends directly on infrastructure. A UserService class might directly instantiate a SqlConnection or depend on a concrete UserRepository. This makes the business logic hard to test and tightly coupled to specific infrastructure choices. When you want to change from SQL Server to PostgreSQL, you have to modify the business logic.

Clean Architecture inverts this dependency. The application layer defines interfaces (abstractions) like IUserRepository and IEmailSender. The business logic depends on these interfaces, not on concrete implementations. The infrastructure layer provides concrete implementations that depend on these interfaces. This way, the business logic remains independent of infrastructure choices.

```csharp
// Dependency Inversion in Practice

// WITHOUT Dependency Inversion (bad)
// High-level module depends directly on low-level module
public class OrderService
{
    private readonly SqlOrderRepository _orderRepository;
    
    public OrderService()
    {
        // Direct dependency on concrete implementation
        _orderRepository = new SqlOrderRepository(ConfigurationManager.ConnectionStrings["Default"]);
    }
    
    public void PlaceOrder(OrderDto orderDto)
    {
        // Business logic tightly coupled to SQL Server
        var order = MapToOrder(orderDto);
        _orderRepository.Save(order);
    }
}

// WITH Dependency Inversion (good)
// High-level module depends on abstraction
public interface IOrderRepository
{
    Task<Order> GetByIdAsync(Guid id);
    Task AddAsync(Order order);
    Task UpdateAsync(Order order);
}

public class OrderService
{
    private readonly IOrderRepository _orderRepository;
    
    // Depends on abstraction, not concretion
    public OrderService(IOrderRepository orderRepository)
    {
        _orderRepository = orderRepository;
    }
    
    public async Task PlaceOrderAsync(OrderDto orderDto)
    {
        var order = MapToOrder(orderDto);
        await _orderRepository.AddAsync(order);
    }
}

// Infrastructure provides the implementation
public class SqlOrderRepository : IOrderRepository
{
    private readonly OrderDbContext _context;
    
    public SqlOrderRepository(OrderDbContext context)
    {
        _context = context;
    }
    
    public async Task<Order> GetByIdAsync(Guid id)
    {
        return await _context.Orders.FindAsync(id);
    }
    
    public async Task AddAsync(Order order)
    {
        await _context.Orders.AddAsync(order);
        await _context.SaveChangesAsync();
    }
    
    public async Task UpdateAsync(Order order)
    {
        _context.Orders.Update(order);
        await _context.SaveChangesAsync();
    }
}

// Can easily swap implementations for testing
public class InMemoryOrderRepository : IOrderRepository
{
    private readonly Dictionary<Guid, Order> _orders = new();
    
    public Task<Order> GetByIdAsync(Guid id) => Task.FromResult(_orders.GetValueOrDefault(id));
    public Task AddAsync(Order order) { _orders[order.Id] = order; return Task.CompletedTask; }
    public Task UpdateAsync(Order order) { _orders[order.Id] = order; return Task.CompletedTask; }
}
```

### Single Responsibility Principle

While Clean Architecture operates at the architectural level, it reinforces SOLID principles at the code level. The Single Responsibility Principle (SRP) states that a class should have only one reason to change. In Clean Architecture, this principle guides the organization of code within layers.

Each use case in the application layer should handle one business operation. Each repository should handle one type of aggregate. Each domain entity should encapsulate one business concept. This granular responsibility distribution makes the system easier to understand, test, and modify.

### Interface Segregation Principle

The Interface Segregation Principle (ISP) states that clients should not be forced to depend on interfaces they don't use. In Clean Architecture, this principle guides the design of interfaces in the application layer. Rather than creating large, monolithic interfaces, we create focused interfaces that serve specific use cases.

```csharp
// Violating ISP - one large interface
public interface IRepository<T>
{
    Task<T> GetByIdAsync(Guid id);
    Task<IEnumerable<T>> GetAllAsync();
    Task AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(Guid id);
    Task<IEnumerable<T>> FindAsync(Expression<Func<T, bool>> predicate);
    Task<int> CountAsync();
    Task BulkInsertAsync(IEnumerable<T> entities);
}

// Following ISP - focused interfaces
public interface IReadRepository<T>
{
    Task<T?> GetByIdAsync(Guid id);
    Task<IEnumerable<T>> GetAllAsync();
    Task<IEnumerable<T>> FindAsync(ISpecification<T> specification);
}

public interface IWriteRepository<T>
{
    Task AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(T entity);
}

public interface IBulkRepository<T>
{
    Task BulkInsertAsync(IEnumerable<T> entities);
    Task BulkUpdateAsync(IEnumerable<T> entities);
}

// Now a read-only service only depends on what it needs
public class ProductQueryService
{
    private readonly IReadRepository<Product> _readRepository;
    
    public ProductQueryService(IReadRepository<Product> readRepository)
    {
        _readRepository = readRepository;
    }
    
    // Only read operations available - write operations not accessible
}
```

### Open/Closed Principle

The Open/Closed Principle (OCP) states that software entities should be open for extension but closed for modification. Clean Architecture supports OCP through the use of interfaces and dependency injection. When you need to add new functionality, you add new implementations rather than modifying existing ones.

Adding a new notification mechanism, for example, requires creating a new class that implements INotificationSender. The existing application code doesn't change. Adding support for a new payment gateway means creating a new IPaymentGateway implementation. The core logic remains untouched.

```csharp
// Open/Closed Principle in Clean Architecture

// Application Layer - defines the abstraction
public interface IPaymentGateway
{
    Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request);
    string GatewayName { get; }
}

// Domain Layer - business logic uses the abstraction
public class ProcessPaymentCommandHandler
{
    private readonly IEnumerable<IPaymentGateway> _paymentGateways;
    
    public ProcessPaymentCommandHandler(IEnumerable<IPaymentGateway> paymentGateways)
    {
        _paymentGateways = paymentGateways;
    }
    
    public async Task<PaymentResult> Handle(ProcessPaymentCommand command)
    {
        var gateway = _paymentGateways.FirstOrDefault(g => g.GatewayName == command.GatewayName)
            ?? throw new NotSupportedException($"Payment gateway {command.GatewayName} not supported");
        
        return await gateway.ProcessPaymentAsync(command.PaymentRequest);
    }
}

// Infrastructure Layer - add new gateways without modifying existing code
public class StripePaymentGateway : IPaymentGateway
{
    public string GatewayName => "Stripe";
    
    public async Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request)
    {
        // Stripe-specific implementation
    }
}

public class PayPalPaymentGateway : IPaymentGateway
{
    public string GatewayName => "PayPal";
    
    public async Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request)
    {
        // PayPal-specific implementation
    }
}

// Adding a new gateway just means adding a new class - no existing code changes
public class SquarePaymentGateway : IPaymentGateway
{
    public string GatewayName => "Square";
    
    public async Task<PaymentResult> ProcessPaymentAsync(PaymentRequest request)
    {
        // Square-specific implementation
    }
}
```

---

## Chapter 3: The Four Layers Explained

### Domain Layer (Enterprise Business Rules)

The Domain Layer sits at the center of Clean Architecture and contains the most fundamental business rules—rules that would exist even if there were no application. These rules define what the business is and how it operates. The Domain Layer has no dependencies on any other layer or external framework. It is pure C# code that expresses business concepts.

The Domain Layer contains entities, value objects, domain services, domain events, and repository interfaces. Entities are objects that have a distinct identity that runs through time—Customer, Order, Product. Value Objects are objects defined by their attributes rather than identity—Money, Address, Email. Domain Services encapsulate business logic that doesn't naturally fit within entities. Domain Events represent something that happened in the domain that other parts of the system might care about.

```csharp
// Domain Layer - Pure business rules, no external dependencies

namespace MyApp.Domain;

// Entity with identity
public class Order : BaseEntity, IAggregateRoot
{
    public Guid Id { get; private set; }
    public CustomerId CustomerId { get; private set; }
    public OrderStatus Status { get; private set; }
    public Money TotalAmount { get; private set; }
    private readonly List<OrderItem> _items = new();
    public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();
    
    private Order() { } // For EF Core
    
    public static Order Create(CustomerId customerId)
    {
        var order = new Order
        {
            Id = Guid.NewGuid(),
            CustomerId = customerId,
            Status = OrderStatus.Pending,
            TotalAmount = Money.Zero("USD")
        };
        
        order.AddDomainEvent(new OrderCreatedEvent(order.Id, customerId));
        return order;
    }
    
    public void AddItem(ProductId productId, string productName, Money unitPrice, int quantity)
    {
        // Business rule: cannot modify submitted orders
        if (Status != OrderStatus.Pending)
            throw new DomainException("Cannot modify a non-pending order");
        
        // Business rule: quantity must be positive
        if (quantity <= 0)
            throw new DomainException("Quantity must be positive");
        
        var existingItem = _items.FirstOrDefault(i => i.ProductId == productId);
        if (existingItem != null)
        {
            existingItem.UpdateQuantity(existingItem.Quantity + quantity);
        }
        else
        {
            var item = OrderItem.Create(Id, productId, productName, unitPrice, quantity);
            _items.Add(item);
        }
        
        RecalculateTotal();
    }
    
    public void Submit()
    {
        // Business rule: cannot submit empty orders
        if (!_items.Any())
            throw new DomainException("Cannot submit an empty order");
        
        // Business rule: cannot resubmit
        if (Status != OrderStatus.Pending)
            throw new DomainException($"Cannot submit order in {Status} status");
        
        Status = OrderStatus.Submitted;
        AddDomainEvent(new OrderSubmittedEvent(Id, CustomerId, TotalAmount));
    }
    
    public void Cancel(string reason)
    {
        // Business rule: cannot cancel shipped orders
        if (Status == OrderStatus.Shipped)
            throw new DomainException("Cannot cancel a shipped order");
        
        // Business rule: must provide reason
        if (string.IsNullOrWhiteSpace(reason))
            throw new DomainException("Cancellation reason is required");
        
        Status = OrderStatus.Cancelled;
        AddDomainEvent(new OrderCancelledEvent(Id, reason));
    }
    
    private void RecalculateTotal()
    {
        TotalAmount = _items.Aggregate(
            Money.Zero("USD"),
            (total, item) => total + item.TotalPrice);
    }
}

// Value Object - defined by its attributes
public record Money
{
    public decimal Amount { get; }
    public string Currency { get; }
    
    private Money(decimal amount, string currency)
    {
        if (amount < 0)
            throw new DomainException("Money amount cannot be negative");
        
        if (string.IsNullOrWhiteSpace(currency))
            throw new DomainException("Currency is required");
        
        Amount = amount;
        Currency = currency.ToUpperInvariant();
    }
    
    public static Money Create(decimal amount, string currency)
        => new(amount, currency);
    
    public static Money Zero(string currency) => new(0, currency);
    
    public static Money operator +(Money left, Money right)
    {
        if (left.Currency != right.Currency)
            throw new DomainException("Cannot add money with different currencies");
        
        return new Money(left.Amount + right.Amount, left.Currency);
    }
    
    public static Money operator *(Money money, int multiplier)
        => new(money.Amount * multiplier, money.Currency);
}

// Enum for order status
public enum OrderStatus
{
    Pending,
    Submitted,
    Processing,
    Shipped,
    Delivered,
    Cancelled
}
```

### Application Layer (Application Business Rules)

The Application Layer contains application-specific business rules—rules that govern how the application operates. This layer orchestrates the flow of data to and from entities, and directs those entities to use their enterprise-wide business rules. The Application Layer defines the jobs the software is supposed to do and directs the expressive domain objects to work out problems.

The Application Layer contains use cases (or application services), commands and queries, validators, and repository interfaces. Use cases encapsulate all the logic for a particular business operation. Commands represent intentions to change state. Queries represent requests for data. Validators ensure that inputs meet requirements before processing.

```csharp
// Application Layer - Orchestrates use cases

namespace MyApp.Application;

// Command (write operation)
public record CreateOrderCommand(
    Guid CustomerId,
    List<CreateOrderItemDto> Items) : IRequest<Guid>;

// Query (read operation)
public record GetOrderByIdQuery(Guid OrderId) : IRequest<OrderDto>;

// Command Handler - the use case implementation
public class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, Guid>
{
    private readonly IOrderRepository _orderRepository;
    private readonly ICustomerRepository _customerRepository;
    private readonly IProductRepository _productRepository;
    private readonly IUnitOfWork _unitOfWork;
    
    public CreateOrderCommandHandler(
        IOrderRepository orderRepository,
        ICustomerRepository customerRepository,
        IProductRepository productRepository,
        IUnitOfWork unitOfWork)
    {
        _orderRepository = orderRepository;
        _customerRepository = customerRepository;
        _productRepository = productRepository;
        _unitOfWork = unitOfWork;
    }
    
    public async Task<Guid> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        // Validate customer exists
        var customer = await _customerRepository.GetByIdAsync(new CustomerId(request.CustomerId), cancellationToken);
        if (customer is null)
            throw new NotFoundException($"Customer with ID {request.CustomerId} not found");
        
        // Create order
        var order = Order.Create(new CustomerId(request.CustomerId));
        
        // Add items
        foreach (var item in request.Items)
        {
            var product = await _productRepository.GetByIdAsync(new ProductId(item.ProductId), cancellationToken);
            if (product is null)
                throw new NotFoundException($"Product with ID {item.ProductId} not found");
            
            if (!product.IsInStock(item.Quantity))
                throw new BusinessRuleException($"Insufficient stock for product {product.Name}");
            
            order.AddItem(product.Id, product.Name, product.Price, item.Quantity);
        }
        
        // Persist
        await _orderRepository.AddAsync(order, cancellationToken);
        await _unitOfWork.SaveChangesAsync(cancellationToken);
        
        return order.Id;
    }
}

// Query Handler - optimized for reading
public class GetOrderByIdQueryHandler : IRequestHandler<GetOrderByIdQuery, OrderDto>
{
    private readonly IOrderReadRepository _orderReadRepository;
    
    public GetOrderByIdQueryHandler(IOrderReadRepository orderReadRepository)
    {
        _orderReadRepository = orderReadRepository;
    }
    
    public async Task<OrderDto> Handle(GetOrderByIdQuery request, CancellationToken cancellationToken)
    {
        var order = await _orderReadRepository.GetByIdAsync(request.OrderId, cancellationToken);
        
        if (order is null)
            throw new NotFoundException($"Order with ID {request.OrderId} not found");
        
        return order;
    }
}

// Repository Interface - defined in Application, implemented in Infrastructure
public interface IOrderRepository
{
    Task<Order?> GetByIdAsync(OrderId id, CancellationToken cancellationToken = default);
    Task AddAsync(Order order, CancellationToken cancellationToken = default);
    Task UpdateAsync(Order order, CancellationToken cancellationToken = default);
}

// Unit of Work - manages transactions
public interface IUnitOfWork
{
    Task<int> SaveChangesAsync(CancellationToken cancellationToken = default);
}
```

### Infrastructure Layer (Interface Adapters)

The Infrastructure Layer contains implementations of interfaces defined in the inner layers. This is where all the technical details live—database access, external API clients, file system operations, email sending, and so on. The Infrastructure Layer depends on the Application and Domain layers, implementing their interfaces.

The Infrastructure Layer contains repository implementations, database contexts, external service clients, and any other technical components. By isolating these details in a separate layer, you can change implementations without affecting business logic. Switching from Entity Framework to Dapper, from SQL Server to PostgreSQL, or from SMTP to SendGrid only requires changes in this layer.

```csharp
// Infrastructure Layer - Technical implementation details

namespace MyApp.Infrastructure;

// Repository Implementation using Entity Framework
public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _context;
    
    public OrderRepository(AppDbContext context)
    {
        _context = context;
    }
    
    public async Task<Order?> GetByIdAsync(OrderId id, CancellationToken cancellationToken = default)
    {
        return await _context.Orders
            .Include(o => o.Items)
            .FirstOrDefaultAsync(o => o.Id == id.Value, cancellationToken);
    }
    
    public async Task AddAsync(Order order, CancellationToken cancellationToken = default)
    {
        await _context.Orders.AddAsync(order, cancellationToken);
    }
    
    public async Task UpdateAsync(Order order, CancellationToken cancellationToken = default)
    {
        _context.Orders.Update(order);
    }
}

// DbContext Configuration
public class AppDbContext : DbContext
{
    private readonly IMediator _mediator;
    
    public AppDbContext(DbContextOptions<AppDbContext> options, IMediator mediator)
        : base(options)
    {
        _mediator = mediator;
    }
    
    public DbSet<Order> Orders => Set<Order>();
    public DbSet<Customer> Customers => Set<Customer>();
    public DbSet<Product> Products => Set<Product>();
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.ApplyConfigurationsFromAssembly(typeof(AppDbContext).Assembly);
    }
    
    public override async Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        // Dispatch domain events before saving
        var domainEntities = ChangeTracker
            .Entries<IHasDomainEvents>()
            .Where(e => e.Entity.DomainEvents.Any())
            .ToList();
        
        var domainEvents = domainEntities
            .SelectMany(e => e.Entity.DomainEvents)
            .ToList();
        
        domainEntities.ForEach(e => e.Entity.ClearDomainEvents());
        
        foreach (var domainEvent in domainEvents)
        {
            await _mediator.Publish(domainEvent, cancellationToken);
        }
        
        return await base.SaveChangesAsync(cancellationToken);
    }
}

// Entity Configuration
public class OrderConfiguration : IEntityTypeConfiguration<Order>
{
    public void Configure(EntityTypeBuilder<Order> builder)
    {
        builder.ToTable("Orders");
        
        builder.HasKey(o => o.Id);
        
        builder.Property(o => o.Id)
            .HasConversion(id => id.Value, value => new OrderId(value));
        
        builder.Property(o => o.Status)
            .HasConversion<string>();
        
        builder.OwnsOne(o => o.TotalAmount, money =>
        {
            money.Property(m => m.Amount).HasColumnName("TotalAmount");
            money.Property(m => m.Currency).HasColumnName("Currency");
        });
        
        builder.HasMany<OrderItem>("_items")
            .WithOne()
            .HasForeignKey("OrderId")
            .OnDelete(DeleteBehavior.Cascade);
    }
}

// External Service Implementation
public class SendGridEmailSender : IEmailSender
{
    private readonly SendGridClient _client;
    private readonly ILogger<SendGridEmailSender> _logger;
    
    public SendGridEmailSender(SendGridClient client, ILogger<SendGridEmailSender> logger)
    {
        _client = client;
        _logger = logger;
    }
    
    public async Task SendAsync(EmailMessage message)
    {
        var sendGridMessage = MailHelper.CreateSingleEmail(
            new EmailAddress(message.From),
            new EmailAddress(message.To),
            message.Subject,
            message.PlainTextBody,
            message.HtmlBody);
        
        var response = await _client.SendEmailAsync(sendGridMessage);
        
        if (!response.IsSuccessStatusCode)
        {
            _logger.LogError("Failed to send email to {To}. Status: {Status}",
                message.To, response.StatusCode);
            throw new EmailException("Failed to send email");
        }
    }
}
```

### Presentation Layer (Frameworks & Drivers)

The Presentation Layer is the outermost layer containing user interface logic, API controllers, and any framework-specific code. This layer handles HTTP requests, validates input at the API level, and returns appropriate responses. The Presentation Layer depends on the Application Layer to execute business operations.

The Presentation Layer should be as thin as possible—its job is to translate HTTP requests into commands/queries and translate results back into HTTP responses. Business logic doesn't belong here. Controllers should delegate to application services or mediators, not contain business rules.

```csharp
// Presentation Layer - API Controllers

namespace MyApp.API.Controllers;

[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;
    
    public OrdersController(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    [HttpPost]
    [ProducesResponseType(typeof(Guid), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<IActionResult> CreateOrder([FromBody] CreateOrderRequest request)
    {
        var command = new CreateOrderCommand(request.CustomerId, request.Items);
        var orderId = await _mediator.Send(command);
        
        return CreatedAtAction(nameof(GetOrder), new { id = orderId }, orderId);
    }
    
    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof(OrderDto), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetOrder(Guid id)
    {
        var query = new GetOrderByIdQuery(id);
        var order = await _mediator.Send(query);
        return Ok(order);
    }
    
    [HttpPost("{id:guid}/submit")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> SubmitOrder(Guid id)
    {
        var command = new SubmitOrderCommand(id);
        await _mediator.Send(command);
        return NoContent();
    }
    
    [HttpPost("{id:guid}/cancel")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> CancelOrder(Guid id, [FromBody] CancelOrderRequest request)
    {
        var command = new CancelOrderCommand(id, request.Reason);
        await _mediator.Send(command);
        return NoContent();
    }
}

// API Models
public record CreateOrderRequest(
    Guid CustomerId,
    List<CreateOrderItemDto> Items);

public record CreateOrderItemDto(
    Guid ProductId,
    int Quantity);

public record CancelOrderRequest(string Reason);

// Exception Handling Middleware
public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;
    
    public ExceptionHandlingMiddleware(RequestDelegate next, ILogger<ExceptionHandlingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }
    
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(context, ex);
        }
    }
    
    private async Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        _logger.LogError(exception, "An unhandled exception occurred");
        
        var problemDetails = exception switch
        {
            NotFoundException notFound => new ProblemDetails
            {
                Title = "Not Found",
                Detail = notFound.Message,
                Status = StatusCodes.Status404NotFound,
                Type = "https://httpstatuses.com/404"
            },
            BusinessRuleException businessRule => new ProblemDetails
            {
                Title = "Business Rule Violation",
                Detail = businessRule.Message,
                Status = StatusCodes.Status400BadRequest,
                Type = "https://httpstatuses.com/400"
            },
            ValidationException validation => new ProblemDetails
            {
                Title = "Validation Failed",
                Detail = validation.Message,
                Status = StatusCodes.Status400BadRequest,
                Type = "https://httpstatuses.com/400"
            },
            _ => new ProblemDetails
            {
                Title = "An error occurred",
                Detail = "An unexpected error occurred. Please try again later.",
                Status = StatusCodes.Status500InternalServerError,
                Type = "https://httpstatuses.com/500"
            }
        };
        
        context.Response.StatusCode = problemDetails.Status ?? 500;
        context.Response.ContentType = "application/problem+json";
        
        await context.Response.WriteAsJsonAsync(problemDetails);
    }
}
```

---

## Chapter 4: Project Structure and Organization

### Recommended Solution Structure

A Clean Architecture solution should have a clear, organized structure that makes the separation of concerns immediately visible. The standard approach uses separate projects for each layer, with the dependency direction clearly enforced through project references.

```
MyApp.sln
├── src/
│   ├── MyApp.Domain/                      # Enterprise Business Rules
│   │   ├── Common/                        # Shared domain primitives
│   │   │   ├── BaseEntity.cs
│   │   │   ├── ValueObject.cs
│   │   │   ├── IDomainEvent.cs
│   │   │   └── IAggregateRoot.cs
│   │   ├── Entities/                      # Domain entities
│   │   │   ├── Order.cs
│   │   │   ├── Customer.cs
│   │   │   └── Product.cs
│   │   ├── ValueObjects/                  # Value objects
│   │   │   ├── Money.cs
│   │   │   ├── Email.cs
│   │   │   ├── Address.cs
│   │   │   └── CustomerId.cs
│   │   ├── Enums/                         # Domain enums
│   │   │   └── OrderStatus.cs
│   │   ├── Events/                        # Domain events
│   │   │   ├── OrderCreatedEvent.cs
│   │   │   └── OrderSubmittedEvent.cs
│   │   ├── Exceptions/                    # Domain exceptions
│   │   │   └── DomainException.cs
│   │   └── Services/                      # Domain services
│   │       └── IPricingService.cs
│   └── MyApp.Domain.csproj                # No project references
│
│   ├── MyApp.Application/                 # Application Business Rules
│   │   ├── Common/                        # Shared application components
│   │   │   ├── Behaviors/                 # Pipeline behaviors
│   │   │   │   ├── ValidationBehavior.cs
│   │   │   │   └── LoggingBehavior.cs
│   │   │   ├── Exceptions/
│   │   │   │   ├── NotFoundException.cs
│   │   │   │   └── BusinessRuleException.cs
│   │   │   ├── Interfaces/
│   │   │   │   ├── IUnitOfWork.cs
│   │   │   │   └── IDateTime.cs
│   │   │   └── Models/
│   │   │       └── PagedResult.cs
│   │   ├── Orders/                        # Feature folder
│   │   │   ├── Commands/
│   │   │   │   ├── CreateOrder/
│   │   │   │   │   ├── CreateOrderCommand.cs
│   │   │   │   │   └── CreateOrderCommandHandler.cs
│   │   │   │   ├── SubmitOrder/
│   │   │   │   │   ├── SubmitOrderCommand.cs
│   │   │   │   │   └── SubmitOrderCommandHandler.cs
│   │   │   │   └── CancelOrder/
│   │   │   │       ├── CancelOrderCommand.cs
│   │   │   │       └── CancelOrderCommandHandler.cs
│   │   │   ├── Queries/
│   │   │   │   ├── GetOrderById/
│   │   │   │   │   ├── GetOrderByIdQuery.cs
│   │   │   │   │   └── GetOrderByIdQueryHandler.cs
│   │   │   │   └── GetOrdersList/
│   │   │   │       ├── GetOrdersListQuery.cs
│   │   │   │       └── GetOrdersListQueryHandler.cs
│   │   │   ├── Events/                    # Event handlers
│   │   │   │   └── OrderCreatedEventHandler.cs
│   │   │   ├── DTOs/
│   │   │   │   ├── OrderDto.cs
│   │   │   │   └── OrderItemDto.cs
│   │   │   └── Validators/
│   │   │       └── CreateOrderCommandValidator.cs
│   │   ├── Customers/                     # Another feature folder
│   │   │   ├── Commands/
│   │   │   ├── Queries/
│   │   │   └── DTOs/
│   │   ├── DependencyInjection.cs         # DI registration
│   │   └── MyApp.Application.csproj       # References: Domain
│
│   ├── MyApp.Infrastructure/              # Interface Adapters
│   │   ├── Persistence/                   # Database
│   │   │   ├── Configurations/
│   │   │   │   ├── OrderConfiguration.cs
│   │   │   │   └── CustomerConfiguration.cs
│   │   │   ├── Migrations/
│   │   │   ├── Repositories/
│   │   │   │   ├── OrderRepository.cs
│   │   │   │   └── CustomerRepository.cs
│   │   │   └── AppDbContext.cs
│   │   ├── Services/                      # External services
│   │   │   ├── Email/
│   │   │   │   └── SendGridEmailSender.cs
│   │   │   ├── Payment/
│   │   │   │   └── StripePaymentGateway.cs
│   │   │   └── Storage/
│   │   │       └── AzureBlobStorageService.cs
│   │   ├── Identity/                      # Authentication
│   │   │   └── IdentityService.cs
│   │   ├── DependencyInjection.cs         # DI registration
│   │   └── MyApp.Infrastructure.csproj    # References: Application, Domain
│
│   └── MyApp.API/                         # Presentation (Web API)
│       ├── Controllers/
│       │   ├── OrdersController.cs
│       │   ├── CustomersController.cs
│       │   └── ProductsController.cs
│       ├── Middleware/
│       │   ├── ExceptionHandlingMiddleware.cs
│       │   └── RequestLoggingMiddleware.cs
│       ├── Filters/
│       │   └── ValidationFilter.cs
│       ├── Program.cs
│       ├── appsettings.json
│       ├── Dockerfile
│       └── MyApp.API.csproj               # References: Application, Infrastructure
│
├── tests/
│   ├── MyApp.Domain.Tests/
│   │   ├── Entities/
│   │   │   └── OrderTests.cs
│   │   └── ValueObjects/
│   │       └── MoneyTests.cs
│   │
│   ├── MyApp.Application.Tests/
│   │   ├── Orders/
│   │   │   └── Commands/
│   │   │       └── CreateOrderCommandHandlerTests.cs
│   │   └── Common/
│   │       └── TestMocks.cs
│   │
│   └── MyApp.API.Tests/
│       ├── Controllers/
│       │   └── OrdersControllerTests.cs
│       └── Integration/
│           └── OrderFlowTests.cs
│
└── Directory.Build.props                  # Global build settings
```

### Project References and Dependencies

The project references enforce the dependency rule. Inner layers should not reference outer layers. Here's how the project files should be configured:

```xml
<!-- MyApp.Domain.csproj -->
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <RootNamespace>MyApp.Domain</RootNamespace>
  </PropertyGroup>
  
  <!-- NO project references - this is the innermost layer -->
</Project>

<!-- MyApp.Application.csproj -->
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <RootNamespace>MyApp.Application</RootNamespace>
  </PropertyGroup>
  
  <ItemGroup>
    <!-- Only reference Domain layer -->
    <ProjectReference Include="..\MyApp.Domain\MyApp.Domain.csproj" />
  </ItemGroup>
  
  <ItemGroup>
    <!-- MediatR for CQRS pattern -->
    <PackageReference Include="MediatR.Contracts" Version="2.0.1" />
    <PackageReference Include="FluentValidation" Version="11.9.0" />
    <PackageReference Include="FluentValidation.DependencyInjectionExtensions" Version="11.9.0" />
  </ItemGroup>
</Project>

<!-- MyApp.Infrastructure.csproj -->
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <RootNamespace>MyApp.Infrastructure</RootNamespace>
  </PropertyGroup>
  
  <ItemGroup>
    <!-- Reference Application (which includes Domain transitively) -->
    <ProjectReference Include="..\MyApp.Application\MyApp.Application.csproj" />
  </ItemGroup>
  
  <ItemGroup>
    <!-- EF Core for database access -->
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="8.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="8.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="8.0.0">
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
      <PrivateAssets>all</PrivateAssets>
    </PackageReference>
    
    <!-- External services -->
    <PackageReference Include="SendGrid" Version="9.29.3" />
    <PackageReference Include="Stripe.net" Version="44.0.0" />
    <PackageReference Include="Azure.Storage.Blobs" Version="12.19.1" />
  </ItemGroup>
</Project>

<!-- MyApp.API.csproj -->
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <RootNamespace>MyApp.API</RootNamespace>
  </PropertyGroup>
  
  <ItemGroup>
    <!-- Presentation references Infrastructure (wiring everything together) -->
    <ProjectReference Include="..\MyApp.Infrastructure\MyApp.Infrastructure.csproj" />
    
    <!-- Also reference Application directly for MediatR -->
    <ProjectReference Include="..\MyApp.Application\MyApp.Application.csproj" />
  </ItemGroup>
  
  <ItemGroup>
    <PackageReference Include="MediatR" Version="12.2.0" />
    <PackageReference Include="Serilog.AspNetCore" Version="8.0.0" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.5.0" />
  </ItemGroup>
</Project>
```

### Dependency Injection Setup

Each layer should have its own extension method for registering services. This keeps the composition root clean and each layer encapsulated.

```csharp
// MyApp.Application/DependencyInjection.cs
namespace MyApp.Application;

public static class DependencyInjection
{
    public static IServiceCollection AddApplication(this IServiceCollection services)
    {
        var assembly = typeof(DependencyInjection).Assembly;
        
        // Register MediatR
        services.AddMediatR(configuration =>
        {
            configuration.RegisterServicesFromAssembly(assembly);
        });
        
        // Register FluentValidation
        services.AddValidatorsFromAssembly(assembly);
        
        // Register pipeline behaviors
        services.AddTransient(typeof(IPipelineBehavior<,>), typeof(ValidationBehavior<,>));
        services.AddTransient(typeof(IPipelineBehavior<,>), typeof(LoggingBehavior<,>));
        
        return services;
    }
}

// MyApp.Infrastructure/DependencyInjection.cs
namespace MyApp.Infrastructure;

public static class DependencyInjection
{
    public static IServiceCollection AddInfrastructure(
        this IServiceCollection services,
        IConfiguration configuration)
    {
        // Register database
        services.AddDbContext<AppDbContext>(options =>
            options.UseSqlServer(
                configuration.GetConnectionString("DefaultConnection"),
                b => b.MigrationsAssembly(typeof(AppDbContext).Assembly.FullName)));
        
        // Register repositories
        services.AddScoped<IOrderRepository, OrderRepository>();
        services.AddScoped<ICustomerRepository, CustomerRepository>();
        services.AddScoped<IProductRepository, ProductRepository>();
        services.AddScoped<IUnitOfWork, UnitOfWork>();
        
        // Register external services
        services.Configure<SendGridSettings>(configuration.GetSection("SendGrid"));
        services.AddTransient<IEmailSender, SendGridEmailSender>();
        
        services.Configure<StripeSettings>(configuration.GetSection("Stripe"));
        services.AddTransient<IPaymentGateway, StripePaymentGateway>();
        
        return services;
    }
}

// MyApp.API/Program.cs (Composition Root)
var builder = WebApplication.CreateBuilder(args);

// Add layers
builder.Services
    .AddApplication()
    .AddInfrastructure(builder.Configuration);

// Add API-specific services
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Add middleware
builder.Services.AddTransient<ExceptionHandlingMiddleware>();

var app = builder.Build();

// Configure pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseMiddleware<ExceptionHandlingMiddleware>();
app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

---

## Chapter 5: Domain Layer Deep Dive

### Entities vs Value Objects

Understanding the distinction between entities and value objects is fundamental to domain-driven design and Clean Architecture. Entities have identity that persists through time and across state changes. Value Objects have no identity—they are defined entirely by their attributes. A $10 bill and another $10 bill are interchangeable; they're value objects. But Customer #123 and Customer #456 are different people even if they have the same name; they're entities.

Entities should be used when identity matters—if you need to track something over time, it's probably an entity. Value Objects should be used when only the attributes matter—if you only care about what something is, not which one it is, it's a value object. Value Objects are immutable; when you need a different value, you create a new instance. Entities are mutable, but their identity never changes.

```csharp
// Entity - has identity, mutable
public abstract class Entity<TId> : IEquatable<Entity<TId>> 
    where TId : notnull
{
    public TId Id { get; protected set; }
    
    protected Entity(TId id)
    {
        Id = id;
    }
    
    // Required for EF Core
    protected Entity() { }
    
    public override bool Equals(object? obj)
    {
        return obj is Entity<TId> entity && Id.Equals(entity.Id);
    }
    
    public bool Equals(Entity<TId>? other)
    {
        return other is not null && Id.Equals(other.Id);
    }
    
    public override int GetHashCode()
    {
        return Id.GetHashCode();
    }
    
    public static bool operator ==(Entity<TId>? left, Entity<TId>? right)
    {
        return Equals(left, right);
    }
    
    public static bool operator !=(Entity<TId>? left, Entity<TId>? right)
    {
        return !Equals(left, right);
    }
}

// Usage: Entity with strong-typed ID
public class Order : Entity<OrderId>
{
    public OrderId Id { get; private set; }
    public CustomerId CustomerId { get; private set; }
    public OrderStatus Status { get; private set; }
    // ... rest of implementation
}

// Strong-typed ID (prevents mixing up IDs)
public readonly record struct OrderId(Guid Value);

// Value Object - no identity, immutable
public abstract record ValueObject
{
    // Records automatically implement equality based on all properties
    // This is perfect for value objects
}

// Money Value Object
public sealed record Money
{
    public decimal Amount { get; init; }
    public string Currency { get; init; }
    
    private Money(decimal amount, string currency)
    {
        if (amount < 0)
            throw new DomainException("Money amount cannot be negative");
        
        if (string.IsNullOrWhiteSpace(currency))
            throw new DomainException("Currency is required");
        
        if (currency.Length != 3)
            throw new DomainException("Currency must be a 3-letter code");
        
        Amount = amount;
        Currency = currency.ToUpperInvariant();
    }
    
    public static Money Create(decimal amount, string currency)
        => new(amount, currency);
    
    public static Money Zero(string currency) => new(0, currency);
    
    public Money Add(Money other)
    {
        if (Currency != other.Currency)
            throw new DomainException("Cannot add money with different currencies");
        
        return new Money(Amount + other.Amount, Currency);
    }
    
    public Money Multiply(int factor)
    {
        return new Money(Amount * factor, Currency);
    }
    
    // Operator overloads for convenience
    public static Money operator +(Money left, Money right) => left.Add(right);
    public static Money operator *(Money money, int factor) => money.Multiply(factor);
}

// Email Value Object
public sealed record Email
{
    public string Value { get; init; }
    
    private Email(string value)
    {
        Value = value.ToLowerInvariant();
    }
    
    public static Email Create(string value)
    {
        if (string.IsNullOrWhiteSpace(value))
            throw new DomainException("Email cannot be empty");
        
        if (!IsValidEmail(value))
            throw new DomainException("Invalid email format");
        
        return new Email(value);
    }
    
    private static bool IsValidEmail(string email)
    {
        try
        {
            var addr = new System.Net.Mail.MailAddress(email);
            return addr.Address == email.ToLowerInvariant();
        }
        catch
        {
            return false;
        }
    }
    
    public static implicit operator string(Email email) => email.Value;
}

// Address Value Object
public sealed record Address
{
    public string Street { get; init; }
    public string City { get; init; }
    public string State { get; init; }
    public string PostalCode { get; init; }
    public string Country { get; init; }
    
    private Address(string street, string city, string state, string postalCode, string country)
    {
        Street = street ?? throw new DomainException("Street is required");
        City = city ?? throw new DomainException("City is required");
        State = state ?? string.Empty;
        PostalCode = postalCode ?? throw new DomainException("Postal code is required");
        Country = country ?? throw new DomainException("Country is required");
    }
    
    public static Address Create(string street, string city, string state, string postalCode, string country)
        => new(street, city, state, postalCode, country);
    
    public string Formatted => $"{Street}, {City}, {State} {PostalCode}, {Country}";
}
```

### Aggregate Roots and Domain Events

An Aggregate is a cluster of associated objects that we treat as a unit for the purpose of data changes. Each Aggregate has a root—the Aggregate Root—that is the only member of the aggregate that outside objects are allowed to hold references to. This ensures consistency boundaries and protects invariants.

Domain Events represent something significant that happened in the domain. They are used to communicate between aggregates and to trigger side effects. Domain Events are raised by aggregates and handled by domain event handlers in the application layer.

```csharp
// Base entity with domain events
public abstract class BaseEntity
{
    private readonly List<IDomainEvent> _domainEvents = new();
    public IReadOnlyCollection<IDomainEvent> DomainEvents => _domainEvents.AsReadOnly();
    
    protected void AddDomainEvent(IDomainEvent domainEvent)
    {
        _domainEvents.Add(domainEvent);
    }
    
    public void ClearDomainEvents()
    {
        _domainEvents.Clear();
    }
}

// Domain Event interface
public interface IDomainEvent : INotification
{
    DateTime OccurredOn { get; }
}

// Aggregate Root marker interface
public interface IAggregateRoot { }

// Order Aggregate Root
public class Order : BaseEntity, IAggregateRoot
{
    public OrderId Id { get; private set; }
    public CustomerId CustomerId { get; private set; }
    public OrderStatus Status { get; private set; }
    public Money TotalAmount { get; private set; }
    public ShippingAddress? ShippingAddress { get; private set; }
    public PaymentDetails? PaymentDetails { get; private set; }
    
    private readonly List<OrderItem> _items = new();
    public IReadOnlyCollection<OrderItem> Items => _items.AsReadOnly();
    
    private Order() { } // EF Core
    
    // Factory method - creates valid aggregate
    public static Order Create(CustomerId customerId)
    {
        var order = new Order
        {
            Id = OrderId.New(),
            CustomerId = customerId,
            Status = OrderStatus.Pending,
            TotalAmount = Money.Zero("USD")
        };
        
        order.AddDomainEvent(new OrderCreatedDomainEvent(order.Id, customerId));
        
        return order;
    }
    
    // Business behavior - methods that enforce invariants
    public void AddItem(ProductId productId, string productName, Money unitPrice, int quantity)
    {
        EnsureOrderIsPending();
        
        if (quantity <= 0)
            throw new DomainException("Quantity must be positive");
        
        if (string.IsNullOrWhiteSpace(productName))
            throw new DomainException("Product name is required");
        
        var existingItem = _items.FirstOrDefault(i => i.ProductId == productId);
        
        if (existingItem != null)
        {
            existingItem.IncreaseQuantity(quantity);
        }
        else
        {
            var newItem = OrderItem.Create(Id, productId, productName, unitPrice, quantity);
            _items.Add(newItem);
        }
        
        RecalculateTotal();
    }
    
    public void RemoveItem(ProductId productId)
    {
        EnsureOrderIsPending();
        
        var item = _items.FirstOrDefault(i => i.ProductId == productId);
        if (item == null)
            throw new DomainException("Item not found in order");
        
        _items.Remove(item);
        RecalculateTotal();
    }
    
    public void SetShippingAddress(ShippingAddress address)
    {
        EnsureOrderIsPending();
        ShippingAddress = address ?? throw new DomainException("Shipping address is required");
    }
    
    public void Submit()
    {
        EnsureOrderIsPending();
        
        if (!_items.Any())
            throw new DomainException("Cannot submit an empty order");
        
        if (ShippingAddress == null)
            throw new DomainException("Shipping address is required");
        
        Status = OrderStatus.Submitted;
        AddDomainEvent(new OrderSubmittedDomainEvent(Id, CustomerId, TotalAmount, ShippingAddress));
    }
    
    public void MarkAsPaid(PaymentDetails paymentDetails)
    {
        if (Status != OrderStatus.Submitted)
            throw new DomainException("Order must be submitted before payment");
        
        PaymentDetails = paymentDetails;
        Status = OrderStatus.Paid;
        AddDomainEvent(new OrderPaidDomainEvent(Id, paymentDetails));
    }
    
    public void Ship(string trackingNumber)
    {
        if (Status != OrderStatus.Paid)
            throw new DomainException("Order must be paid before shipping");
        
        Status = OrderStatus.Shipped;
        AddDomainEvent(new OrderShippedDomainEvent(Id, trackingNumber));
    }
    
    public void Cancel(string reason)
    {
        if (Status == OrderStatus.Shipped || Status == OrderStatus.Delivered)
            throw new DomainException("Cannot cancel a shipped or delivered order");
        
        var previousStatus = Status;
        Status = OrderStatus.Cancelled;
        AddDomainEvent(new OrderCancelledDomainEvent(Id, previousStatus, reason));
    }
    
    private void EnsureOrderIsPending()
    {
        if (Status != OrderStatus.Pending)
            throw new DomainException($"Cannot modify order in {Status} status");
    }
    
    private void RecalculateTotal()
    {
        TotalAmount = _items.Aggregate(
            Money.Zero("USD"),
            (total, item) => total.Add(item.TotalPrice));
    }
}

// Order Item (part of Order aggregate, not an aggregate root)
public class OrderItem : Entity<OrderItemId>
{
    public OrderId OrderId { get; private set; }
    public ProductId ProductId { get; private set; }
    public string ProductName { get; private set; }
    public Money UnitPrice { get; private set; }
    public int Quantity { get; private set; }
    public Money TotalPrice => UnitPrice * Quantity;
    
    private OrderItem() { }
    
    internal static OrderItem Create(
        OrderId orderId,
        ProductId productId,
        string productName,
        Money unitPrice,
        int quantity)
    {
        return new OrderItem
        {
            Id = OrderItemId.New(),
            OrderId = orderId,
            ProductId = productId,
            ProductName = productName,
            UnitPrice = unitPrice,
            Quantity = quantity
        };
    }
    
    internal void IncreaseQuantity(int amount)
    {
        Quantity += amount;
    }
}

// Domain Events
public sealed record OrderCreatedDomainEvent(
    OrderId OrderId,
    CustomerId CustomerId) : IDomainEvent
{
    public DateTime OccurredOn { get; } = DateTime.UtcNow;
}

public sealed record OrderSubmittedDomainEvent(
    OrderId OrderId,
    CustomerId CustomerId,
    Money TotalAmount,
    ShippingAddress ShippingAddress) : IDomainEvent
{
    public DateTime OccurredOn { get; } = DateTime.UtcNow;
}

public sealed record OrderCancelledDomainEvent(
    OrderId OrderId,
    OrderStatus PreviousStatus,
    string Reason) : IDomainEvent
{
    public DateTime OccurredOn { get; } = DateTime.UtcNow;
}
```

### Domain Services

Domain Services encapsulate domain logic that doesn't naturally fit within an entity or value object. This typically includes operations that involve multiple aggregates or require external calculations. Domain Services are defined in the Domain Layer as interfaces, with implementations in the Infrastructure Layer if they need external resources.

```csharp
// Domain Service Interface
public interface IPricingService
{
    Money CalculateDiscount(Customer customer, Money orderTotal);
    Money CalculateTax(ShippingAddress shippingAddress, Money subtotal);
}

public interface IInventoryService
{
    Task<bool> IsProductAvailableAsync(ProductId productId, int quantity);
    Task ReserveInventoryAsync(ProductId productId, int quantity);
}

public interface ICustomerCreditService
{
    Task<bool> HasSufficientCreditAsync(CustomerId customerId, Money amount);
}

// Usage in domain
public class Order : BaseEntity, IAggregateRoot
{
    public async Task Submit(
        IPricingService pricingService,
        IInventoryService inventoryService)
    {
        EnsureOrderIsPending();
        
        if (!_items.Any())
            throw new DomainException("Cannot submit an empty order");
        
        // Check inventory for all items
        foreach (var item in _items)
        {
            var available = await inventoryService.IsProductAvailableAsync(item.ProductId, item.Quantity);
            if (!available)
                throw new DomainException($"Product {item.ProductName} is not available in requested quantity");
        }
        
        Status = OrderStatus.Submitted;
        AddDomainEvent(new OrderSubmittedDomainEvent(Id, CustomerId, TotalAmount, ShippingAddress!));
    }
}
```

---

## Chapter 6: Application Layer Implementation

### Commands, Queries, and the Command Handler Pattern

The Application Layer implements use cases through the Command Query Responsibility Segregation (CQRS) pattern. Commands represent intentions to change state—they create, update, or delete data. Queries represent requests for data—they read and return information without side effects. This separation enables independent optimization, clearer code organization, and better scalability.

```csharp
// Command and Handler Pattern using MediatR

// Command - represents an intention to change state
public sealed record CreateOrderCommand(
    Guid CustomerId,
    List<CreateOrderItemDto> Items) : IRequest<Guid>;

// Command Handler - contains the use case logic
public sealed class CreateOrderCommandHandler : IRequestHandler<CreateOrderCommand, Guid>
{
    private readonly IOrderRepository _orderRepository;
    private readonly ICustomerRepository _customerRepository;
    private readonly IProductRepository _productRepository;
    private readonly IInventoryService _inventoryService;
    private readonly IUnitOfWork _unitOfWork;
    private readonly ILogger<CreateOrderCommandHandler> _logger;
    
    public CreateOrderCommandHandler(
        IOrderRepository orderRepository,
        ICustomerRepository customerRepository,
        IProductRepository productRepository,
        IInventoryService inventoryService,
        IUnitOfWork unitOfWork,
        ILogger<CreateOrderCommandHandler> logger)
    {
        _orderRepository = orderRepository;
        _customerRepository = customerRepository;
        _productRepository = productRepository;
        _inventoryService = inventoryService;
        _unitOfWork = unitOfWork;
        _logger = logger;
    }
    
    public async Task<Guid> Handle(CreateOrderCommand request, CancellationToken cancellationToken)
    {
        // Validate customer exists
        var customer = await _customerRepository.GetByIdAsync(
            new CustomerId(request.CustomerId), 
            cancellationToken);
            
        if (customer is null)
            throw new NotFoundException($"Customer with ID {request.CustomerId} not found");
        
        // Check customer can place orders
        if (customer.Status != CustomerStatus.Active)
            throw new BusinessRuleException("Customer is not active and cannot place orders");
        
        // Create order
        var order = Order.Create(new CustomerId(request.CustomerId));
        
        // Add items with validation
        foreach (var item in request.Items)
        {
            var product = await _productRepository.GetByIdAsync(
                new ProductId(item.ProductId), 
                cancellationToken);
                
            if (product is null)
                throw new NotFoundException($"Product with ID {item.ProductId} not found");
            
            if (!product.IsAvailable)
                throw new BusinessRuleException($"Product {product.Name} is not available");
            
            // Check inventory
            var inStock = await _inventoryService.IsProductAvailableAsync(
                product.Id, 
                item.Quantity);
                
            if (!inStock)
                throw new BusinessRuleException(
                    $"Insufficient stock for product {product.Name}. Requested: {item.Quantity}");
            
            order.AddItem(product.Id, product.Name, product.Price, item.Quantity);
        }
        
        // Persist
        await _orderRepository.AddAsync(order, cancellationToken);
        await _unitOfWork.SaveChangesAsync(cancellationToken);
        
        _logger.LogInformation("Order {OrderId} created for customer {CustomerId}", 
            order.Id, request.CustomerId);
        
        return order.Id;
    }
}

// Query - represents a request for data
public sealed record GetOrderByIdQuery(Guid OrderId) : IRequest<OrderDetailsDto>;

// Query Handler - optimized for reading
public sealed class GetOrderByIdQueryHandler : IRequestHandler<GetOrderByIdQuery, OrderDetailsDto>
{
    private readonly IOrderReadRepository _orderReadRepository;
    
    public GetOrderByIdQueryHandler(IOrderReadRepository orderReadRepository)
    {
        _orderReadRepository = orderReadRepository;
    }
    
    public async Task<OrderDetailsDto> Handle(GetOrderByIdQuery request, CancellationToken cancellationToken)
    {
        var order = await _orderReadRepository.GetDetailsByIdAsync(request.OrderId, cancellationToken);
        
        if (order is null)
            throw new NotFoundException($"Order with ID {request.OrderId} not found");
        
        return order;
    }
}

// Paged query
public sealed record GetOrdersQuery(
    Guid? CustomerId,
    OrderStatus? Status,
    int Page = 1,
    int PageSize = 10) : IRequest<PagedResult<OrderListDto>>;

public sealed class GetOrdersQueryHandler : IRequestHandler<GetOrdersQuery, PagedResult<OrderListDto>>
{
    private readonly IOrderReadRepository _orderReadRepository;
    
    public GetOrdersQueryHandler(IOrderReadRepository orderReadRepository)
    {
        _orderReadRepository = orderReadRepository;
    }
    
    public async Task<PagedResult<OrderListDto>> Handle(GetOrdersQuery request, CancellationToken cancellationToken)
    {
        var filter = new OrderFilter
        {
            CustomerId = request.CustomerId,
            Status = request.Status
        };
        
        var orders = await _orderReadRepository.GetPagedAsync(
            filter,
            request.Page,
            request.PageSize,
            cancellationToken);
        
        return orders;
    }
}
```

### Pipeline Behaviors

Pipeline behaviors allow you to add cross-cutting concerns that execute before and after request handlers. Common uses include validation, logging, performance measurement, and transaction management.

```csharp
// Validation Behavior
public sealed class ValidationBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    private readonly IEnumerable<IValidator<TRequest>> _validators;
    
    public ValidationBehavior(IEnumerable<IValidator<TRequest>> validators)
    {
        _validators = validators;
    }
    
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        CancellationToken cancellationToken)
    {
        if (!_validators.Any())
            return await next();
        
        var context = new ValidationContext<TRequest>(request);
        
        var validationResults = await Task.WhenAll(
            _validators.Select(v => v.ValidateAsync(context, cancellationToken)));
        
        var failures = validationResults
            .SelectMany(r => r.Errors)
            .Where(f => f != null)
            .ToList();
        
        if (failures.Count != 0)
            throw new ValidationException(failures);
        
        return await next();
    }
}

// Logging Behavior
public sealed class LoggingBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    private readonly ILogger<LoggingBehavior<TRequest, TResponse>> _logger;
    
    public LoggingBehavior(ILogger<LoggingBehavior<TRequest, TResponse>> logger)
    {
        _logger = logger;
    }
    
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        CancellationToken cancellationToken)
    {
        var requestName = typeof(TRequest).Name;
        
        _logger.LogInformation(
            "Handling {RequestName}: {@Request}",
            requestName,
            request);
        
        var stopwatch = Stopwatch.StartNew();
        
        try
        {
            var response = await next();
            
            stopwatch.Stop();
            
            _logger.LogInformation(
                "Handled {RequestName} in {ElapsedMilliseconds}ms: {@Response}",
                requestName,
                stopwatch.ElapsedMilliseconds,
                response);
            
            return response;
        }
        catch (Exception ex)
        {
            stopwatch.Stop();
            
            _logger.LogError(ex,
                "Error handling {RequestName} after {ElapsedMilliseconds}ms",
                requestName,
                stopwatch.ElapsedMilliseconds);
            
            throw;
        }
    }
}

// Transaction Behavior
public sealed class TransactionBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest<TResponse>
{
    private readonly IUnitOfWork _unitOfWork;
    
    public TransactionBehavior(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }
    
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        CancellationToken cancellationToken)
    {
        // Only wrap commands in transactions, not queries
        if (request is IQuery)
        {
            return await next();
        }
        
        await using var transaction = await _unitOfWork.BeginTransactionAsync(cancellationToken);
        
        try
        {
            var response = await next();
            await transaction.CommitAsync(cancellationToken);
            return response;
        }
        catch
        {
            await transaction.RollbackAsync(cancellationToken);
            throw;
        }
    }
}

// Caching Behavior
public sealed class CachingBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : IRequest<TResponse>, ICacheableQuery
{
    private readonly ICacheService _cacheService;
    private readonly ILogger<CachingBehavior<TRequest, TResponse>> _logger;
    
    public CachingBehavior(
        ICacheService cacheService,
        ILogger<CachingBehavior<TRequest, TResponse>> logger)
    {
        _cacheService = cacheService;
        _logger = logger;
    }
    
    public async Task<TResponse> Handle(
        TRequest request,
        RequestHandlerDelegate<TResponse> next,
        CancellationToken cancellationToken)
    {
        var cacheKey = request.CacheKey;
        
        var cachedResponse = await _cacheService.GetAsync<TResponse>(cacheKey, cancellationToken);
        
        if (cachedResponse is not null)
        {
            _logger.LogDebug("Cache hit for {CacheKey}", cacheKey);
            return cachedResponse;
        }
        
        _logger.LogDebug("Cache miss for {CacheKey}", cacheKey);
        
        var response = await next();
        
        await _cacheService.SetAsync(
            cacheKey,
            response,
            request.Expiration,
            cancellationToken);
        
        return response;
    }
}

// Interface for cacheable queries
public interface ICacheableQuery
{
    string CacheKey { get; }
    TimeSpan? Expiration { get; }
}
```

---

## Chapter 7: Infrastructure Layer Design

### Repository Implementations

The Repository pattern abstracts data access, providing a collection-like interface for accessing domain objects. Repositories should work with aggregates, not individual entities, ensuring that consistency boundaries are respected.

```csharp
// Generic Repository Interface
public interface IRepository<T, TId> where T : Entity<TId> where TId : notnull
{
    Task<T?> GetByIdAsync(TId id, CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> GetAllAsync(CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> GetAsync(ISpecification<T> specification, CancellationToken cancellationToken = default);
    Task AddAsync(T entity, CancellationToken cancellationToken = default);
    Task UpdateAsync(T entity, CancellationToken cancellationToken = default);
    Task DeleteAsync(T entity, CancellationToken cancellationToken = default);
}

// Specification Pattern for complex queries
public interface ISpecification<T>
{
    Expression<Func<T, bool>>? Criteria { get; }
    List<Expression<Func<T, object>>> Includes { get; }
    List<string> IncludeStrings { get; }
    Expression<Func<T, object>>? OrderBy { get; }
    Expression<Func<T, object>>? OrderByDescending { get; }
    int? Take { get; }
    int? Skip { get; }
    bool IsPagingEnabled { get; }
}

// Base Specification
public abstract class Specification<T> : ISpecification<T>
{
    public Expression<Func<T, bool>>? Criteria { get; private set; }
    public List<Expression<Func<T, object>>> Includes { get; } = new();
    public List<string> IncludeStrings { get; } = new();
    public Expression<Func<T, object>>? OrderBy { get; private set; }
    public Expression<Func<T, object>>? OrderByDescending { get; private set; }
    public int? Take { get; private set; }
    public int? Skip { get; private set; }
    public bool IsPagingEnabled { get; private set; }
    
    protected void AddCriteria(Expression<Func<T, bool>> criteria)
    {
        Criteria = criteria;
    }
    
    protected void AddInclude(Expression<Func<T, object>> include)
    {
        Includes.Add(include);
    }
    
    protected void AddInclude(string includeString)
    {
        IncludeStrings.Add(includeString);
    }
    
    protected void ApplyOrderBy(Expression<Func<T, object>> orderBy)
    {
        OrderBy = orderBy;
    }
    
    protected void ApplyOrderByDescending(Expression<Func<T, object>> orderByDescending)
    {
        OrderByDescending = orderByDescending;
    }
    
    protected void ApplyPaging(int skip, int take)
    {
        Skip = skip;
        Take = take;
        IsPagingEnabled = true;
    }
}

// Example Specification
public class OrdersByCustomerSpecification : Specification<Order>
{
    public OrdersByCustomerSpecification(CustomerId customerId, OrderStatus? status = null)
    {
        AddCriteria(o => o.CustomerId == customerId);
        AddInclude(o => o.Items);
        
        if (status.HasValue)
        {
            AddCriteria(o => o.Status == status.Value);
        }
        
        ApplyOrderByDescending(o => o.Id);
    }
}

// Repository Implementation using EF Core
public class EfRepository<T, TId> : IRepository<T, TId> 
    where T : Entity<TId> 
    where TId : notnull
{
    protected readonly AppDbContext _context;
    
    public EfRepository(AppDbContext context)
    {
        _context = context;
    }
    
    public async Task<T?> GetByIdAsync(TId id, CancellationToken cancellationToken = default)
    {
        return await _context.Set<T>().FindAsync(new object[] { id }, cancellationToken);
    }
    
    public async Task<IReadOnlyList<T>> GetAllAsync(CancellationToken cancellationToken = default)
    {
        return await _context.Set<T>().ToListAsync(cancellationToken);
    }
    
    public async Task<IReadOnlyList<T>> GetAsync(
        ISpecification<T> specification, 
        CancellationToken cancellationToken = default)
    {
        return await ApplySpecification(specification).ToListAsync(cancellationToken);
    }
    
    public async Task AddAsync(T entity, CancellationToken cancellationToken = default)
    {
        await _context.Set<T>().AddAsync(entity, cancellationToken);
    }
    
    public Task UpdateAsync(T entity, CancellationToken cancellationToken = default)
    {
        _context.Set<T>().Update(entity);
        return Task.CompletedTask;
    }
    
    public Task DeleteAsync(T entity, CancellationToken cancellationToken = default)
    {
        _context.Set<T>().Remove(entity);
        return Task.CompletedTask;
    }
    
    private IQueryable<T> ApplySpecification(ISpecification<T> specification)
    {
        var query = _context.Set<T>().AsQueryable();
        
        if (specification.Criteria is not null)
        {
            query = query.Where(specification.Criteria);
        }
        
        foreach (var include in specification.Includes)
        {
            query = query.Include(include);
        }
        
        foreach (var includeString in specification.IncludeStrings)
        {
            query = query.Include(includeString);
        }
        
        if (specification.OrderBy is not null)
        {
            query = query.OrderBy(specification.OrderBy);
        }
        else if (specification.OrderByDescending is not null)
        {
            query = query.OrderByDescending(specification.OrderByDescending);
        }
        
        if (specification.IsPagingEnabled)
        {
            query = query.Skip(specification.Skip!.Value).Take(specification.Take!.Value);
        }
        
        return query;
    }
}

// Specific Repository Interface
public interface IOrderRepository : IRepository<Order, OrderId>
{
    Task<Order?> GetByIdWithItemsAsync(OrderId id, CancellationToken cancellationToken = default);
    Task<IReadOnlyList<Order>> GetByCustomerIdAsync(CustomerId customerId, CancellationToken cancellationToken = default);
}

// Specific Repository Implementation
public class OrderRepository : EfRepository<Order, OrderId>, IOrderRepository
{
    public OrderRepository(AppDbContext context) : base(context)
    {
    }
    
    public async Task<Order?> GetByIdWithItemsAsync(OrderId id, CancellationToken cancellationToken = default)
    {
        return await _context.Orders
            .Include(o => o.Items)
            .FirstOrDefaultAsync(o => o.Id == id, cancellationToken);
    }
    
    public async Task<IReadOnlyList<Order>> GetByCustomerIdAsync(
        CustomerId customerId, 
        CancellationToken cancellationToken = default)
    {
        return await _context.Orders
            .Where(o => o.CustomerId == customerId)
            .Include(o => o.Items)
            .OrderByDescending(o => o.Id)
            .ToListAsync(cancellationToken);
    }
}
```

### Unit of Work Pattern

The Unit of Work pattern coordinates the writing of changes across multiple repositories, ensuring that all changes are committed or rolled back as a single unit.

```csharp
// Unit of Work Interface
public interface IUnitOfWork : IDisposable
{
    Task<int> SaveChangesAsync(CancellationToken cancellationToken = default);
    Task<IDbContextTransaction> BeginTransactionAsync(CancellationToken cancellationToken = default);
}

// Unit of Work Implementation
public class UnitOfWork : IUnitOfWork
{
    private readonly AppDbContext _context;
    private readonly IMediator _mediator;
    private IDbContextTransaction? _transaction;
    
    public UnitOfWork(AppDbContext context, IMediator mediator)
    {
        _context = context;
        _mediator = mediator;
    }
    
    public async Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        // Dispatch domain events before saving
        await DispatchDomainEventsAsync(cancellationToken);
        
        // Update audit fields
        UpdateAuditFields();
        
        return await _context.SaveChangesAsync(cancellationToken);
    }
    
    public async Task<IDbContextTransaction> BeginTransactionAsync(CancellationToken cancellationToken = default)
    {
        if (_transaction is not null)
        {
            return _transaction;
        }
        
        _transaction = await _context.Database.BeginTransactionAsync(cancellationToken);
        return _transaction;
    }
    
    private async Task DispatchDomainEventsAsync(CancellationToken cancellationToken)
    {
        var domainEntities = _context.ChangeTracker
            .Entries<IHasDomainEvents>()
            .Where(e => e.Entity.DomainEvents.Any())
            .ToList();
        
        var domainEvents = domainEntities
            .SelectMany(e => e.Entity.DomainEvents)
            .ToList();
        
        domainEntities.ForEach(e => e.Entity.ClearDomainEvents());
        
        foreach (var domainEvent in domainEvents)
        {
            await _mediator.Publish(domainEvent, cancellationToken);
        }
    }
    
    private void UpdateAuditFields()
    {
        var entries = _context.ChangeTracker
            .Entries<IAuditableEntity>();
        
        foreach (var entry in entries)
        {
            if (entry.State == EntityState.Added)
            {
                entry.Entity.CreatedAt = DateTime.UtcNow;
                entry.Entity.CreatedBy = "System"; // Get from current user context
            }
            
            if (entry.State == EntityState.Modified)
            {
                entry.Entity.UpdatedAt = DateTime.UtcNow;
                entry.Entity.UpdatedBy = "System";
            }
        }
    }
    
    public void Dispose()
    {
        _transaction?.Dispose();
        _context.Dispose();
    }
}
```

### External Service Integrations

```csharp
// Interface in Application Layer
public interface IEmailSender
{
    Task SendWelcomeEmailAsync(Email email, string name);
    Task SendOrderConfirmationAsync(Email email, OrderDto order);
    Task SendPasswordResetAsync(Email email, string resetToken);
}

// Implementation in Infrastructure Layer
public class EmailSender : IEmailSender
{
    private readonly SendGridClient _client;
    private readonly EmailAddress _fromAddress;
    private readonly ILogger<EmailSender> _logger;
    
    public EmailSender(
        SendGridClient client,
        IConfiguration configuration,
        ILogger<EmailSender> logger)
    {
        _client = client;
        _logger = logger;
        _fromAddress = new EmailAddress(
            configuration["Email:FromAddress"],
            configuration["Email:FromName"]);
    }
    
    public async Task SendWelcomeEmailAsync(Email email, string name)
    {
        var message = MailHelper.CreateSingleTemplateEmail(
            _fromAddress,
            new EmailAddress(email),
            "Welcome to Our Platform",
            "d-abc123templateid",
            new { name });
        
        await SendEmailAsync(message, email);
    }
    
    public async Task SendOrderConfirmationAsync(Email email, OrderDto order)
    {
        var message = MailHelper.CreateSingleTemplateEmail(
            _fromAddress,
            new EmailAddress(email),
            "Order Confirmation",
            "d-order-template-id",
            new { order });
        
        await SendEmailAsync(message, email);
    }
    
    public async Task SendPasswordResetAsync(Email email, string resetToken)
    {
        var message = MailHelper.CreateSingleTemplateEmail(
            _fromAddress,
            new EmailAddress(email),
            "Password Reset",
            "d-password-reset-template-id",
            new { resetToken });
        
        await SendEmailAsync(message, email);
    }
    
    private async Task SendEmailAsync(SendGridMessage message, Email email)
    {
        try
        {
            var response = await _client.SendEmailAsync(message);
            
            if (!response.IsSuccessStatusCode)
            {
                _logger.LogError(
                    "Failed to send email to {Email}. Status: {StatusCode}",
                    email, response.StatusCode);
                throw new EmailException($"Failed to send email to {email}");
            }
            
            _logger.LogInformation("Email sent successfully to {Email}", email);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error sending email to {Email}", email);
            throw;
        }
    }
}
```

---

## Chapter 8: Presentation Layer Patterns

### API Controller Patterns

Controllers in Clean Architecture should be thin, acting as adapters that translate HTTP requests into application commands/queries and translate results back into HTTP responses. Business logic should never be in controllers.

```csharp
[ApiController]
[Route("api/v1/[controller]")]
[Produces("application/json")]
public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;
    
    public OrdersController(IMediator mediator)
    {
        _mediator = mediator;
    }
    
    /// <summary>
    /// Creates a new order
    /// </summary>
    [HttpPost]
    [ProducesResponseType(typeof(OrderCreatedResponse), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ValidationProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<ActionResult<OrderCreatedResponse>> CreateOrder(
        [FromBody] CreateOrderRequest request,
        CancellationToken cancellationToken)
    {
        var command = new CreateOrderCommand(request.CustomerId, request.Items);
        var orderId = await _mediator.Send(command, cancellationToken);
        
        return CreatedAtAction(
            nameof(GetOrderById),
            new { id = orderId },
            new OrderCreatedResponse(orderId));
    }
    
    /// <summary>
    /// Gets an order by ID
    /// </summary>
    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof(OrderDetailsDto), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<ActionResult<OrderDetailsDto>> GetOrderById(
        Guid id,
        CancellationToken cancellationToken)
    {
        var query = new GetOrderByIdQuery(id);
        var order = await _mediator.Send(query, cancellationToken);
        return Ok(order);
    }
    
    /// <summary>
    /// Gets paginated list of orders
    /// </summary>
    [HttpGet]
    [ProducesResponseType(typeof(PagedResult<OrderListDto>), StatusCodes.Status200OK)]
    public async Task<ActionResult<PagedResult<OrderListDto>>> GetOrders(
        [FromQuery] GetOrdersRequest request,
        CancellationToken cancellationToken)
    {
        var query = new GetOrdersQuery(
            request.CustomerId,
            request.Status,
            request.Page,
            request.PageSize);
            
        var result = await _mediator.Send(query, cancellationToken);
        return Ok(result);
    }
    
    /// <summary>
    /// Submits an order for processing
    /// </summary>
    [HttpPost("{id:guid}/submit")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<IActionResult> SubmitOrder(
        Guid id,
        CancellationToken cancellationToken)
    {
        var command = new SubmitOrderCommand(id);
        await _mediator.Send(command, cancellationToken);
        return NoContent();
    }
    
    /// <summary>
    /// Cancels an order
    /// </summary>
    [HttpPost("{id:guid}/cancel")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<IActionResult> CancelOrder(
        Guid id,
        [FromBody] CancelOrderRequest request,
        CancellationToken cancellationToken)
    {
        var command = new CancelOrderCommand(id, request.Reason);
        await _mediator.Send(command, cancellationToken);
        return NoContent();
    }
}

// API Request/Response DTOs
public record CreateOrderRequest(
    Guid CustomerId,
    List<CreateOrderItemRequest> Items);

public record CreateOrderItemRequest(
    Guid ProductId,
    int Quantity);

public record CancelOrderRequest(string Reason);

public record OrderCreatedResponse(Guid OrderId);

public record GetOrdersRequest(
    Guid? CustomerId,
    OrderStatus? Status,
    int Page = 1,
    int PageSize = 10);
```

### Problem Details for Error Handling

```csharp
// Custom Problem Details
public class BusinessProblemDetails : ProblemDetails
{
    public string? ErrorCode { get; set; }
    public IDictionary<string, string[]>? ValidationErrors { get; set; }
}

// Exception Handling Middleware
public class ExceptionHandlingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<ExceptionHandlingMiddleware> _logger;
    private readonly IHostEnvironment _environment;
    
    public ExceptionHandlingMiddleware(
        RequestDelegate next,
        ILogger<ExceptionHandlingMiddleware> logger,
        IHostEnvironment environment)
    {
        _next = next;
        _logger = logger;
        _environment = environment;
    }
    
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(context, ex);
        }
    }
    
    private async Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        _logger.LogError(exception, "An unhandled exception occurred during request processing");
        
        var (statusCode, problemDetails) = exception switch
        {
            ValidationException validationEx => (
                StatusCodes.Status400BadRequest,
                CreateValidationProblemDetails(validationEx)),
            
            NotFoundException notFoundEx => (
                StatusCodes.Status404NotFound,
                CreateProblemDetails(
                    "Not Found",
                    notFoundEx.Message,
                    StatusCodes.Status404NotFound)),
            
            BusinessRuleException businessEx => (
                StatusCodes.Status422UnprocessableEntity,
                CreateProblemDetails(
                    "Business Rule Violation",
                    businessEx.Message,
                    StatusCodes.Status422UnprocessableEntity,
                    businessEx.ErrorCode)),
            
            ConcurrencyException concurrencyEx => (
                StatusCodes.Status409Conflict,
                CreateProblemDetails(
                    "Concurrency Conflict",
                    concurrencyEx.Message,
                    StatusCodes.Status409Conflict)),
            
            UnauthorizedAccessException => (
                StatusCodes.Status401Unauthorized,
                CreateProblemDetails(
                    "Unauthorized",
                    "You are not authorized to perform this action",
                    StatusCodes.Status401Unauthorized)),
            
            _ => (
                StatusCodes.Status500InternalServerError,
                CreateProblemDetails(
                    "Internal Server Error",
                    _environment.IsDevelopment() 
                        ? exception.Message 
                        : "An unexpected error occurred",
                    StatusCodes.Status500InternalServerError))
        };
        
        context.Response.StatusCode = statusCode;
        context.Response.ContentType = "application/problem+json";
        
        await context.Response.WriteAsJsonAsync(problemDetails);
    }
    
    private static BusinessProblemDetails CreateProblemDetails(
        string title,
        string detail,
        int status,
        string? errorCode = null)
    {
        return new BusinessProblemDetails
        {
            Title = title,
            Detail = detail,
            Status = status,
            Type = $"https://httpstatuses.com/{status}",
            ErrorCode = errorCode
        };
    }
    
    private static BusinessProblemDetails CreateValidationProblemDetails(ValidationException exception)
    {
        var problemDetails = new BusinessProblemDetails
        {
            Title = "Validation Failed",
            Detail = "One or more validation errors occurred",
            Status = StatusCodes.Status400BadRequest,
            Type = "https://httpstatuses.com/400",
            ErrorCode = "VALIDATION_ERROR"
        };
        
        if (exception.Errors.Any())
        {
            problemDetails.ValidationErrors = exception.Errors
                .GroupBy(e => e.PropertyName, e => e.ErrorMessage)
                .ToDictionary(g => g.Key, g => g.ToArray());
        }
        
        return problemDetails;
    }
}

// Extension method for registering middleware
public static class ExceptionHandlingMiddlewareExtensions
{
    public static IApplicationBuilder UseExceptionHandling(this IApplicationBuilder app)
    {
        return app.UseMiddleware<ExceptionHandlingMiddleware>();
    }
}
```

---

## Chapter 9: Dependency Injection and Inversion

### Service Registration Patterns

Clean Architecture requires careful configuration of dependency injection to ensure that dependencies flow in the correct direction. Each layer should have its own service registration extension method.

```csharp
// Application Layer DI
public static class ApplicationServiceExtensions
{
    public static IServiceCollection AddApplicationServices(
        this IServiceCollection services,
        Action<ApplicationOptions>? configure = null)
    {
        var options = new ApplicationOptions();
        configure?.Invoke(options);
        
        services.Configure<ApplicationOptions>(opt => configure?.Invoke(opt));
        
        // MediatR
        services.AddMediatR(cfg =>
        {
            cfg.RegisterServicesFromAssembly(typeof(ApplicationServiceExtensions).Assembly);
            cfg.AddOpenBehavior(typeof(ValidationBehavior<,>));
            cfg.AddOpenBehavior(typeof(LoggingBehavior<,>));
        });
        
        // Validators
        services.AddValidatorsFromAssembly(
            typeof(ApplicationServiceExtensions).Assembly,
            ServiceLifetime.Scoped);
        
        // AutoMapper
        services.AddAutoMapper(typeof(ApplicationServiceExtensions).Assembly);
        
        // Application services
        services.AddScoped<IOrderNumberGenerator, OrderNumberGenerator>();
        services.AddSingleton<IDateTimeProvider, DateTimeProvider>();
        
        return services;
    }
}

// Infrastructure Layer DI
public static class InfrastructureServiceExtensions
{
    public static IServiceCollection AddInfrastructureServices(
        this IServiceCollection services,
        IConfiguration configuration,
        Action<InfrastructureOptions>? configure = null)
    {
        var options = new InfrastructureOptions();
        configuration.GetSection("Infrastructure").Bind(options);
        configure?.Invoke(options);
        
        // Database
        services.AddDbContext<AppDbContext>((sp, dbOptions) =>
        {
            dbOptions.UseSqlServer(
                configuration.GetConnectionString("DefaultConnection"),
                sqlOptions =>
                {
                    sqlOptions.MigrationsAssembly(
                        typeof(InfrastructureServiceExtensions).Assembly.FullName);
                    sqlOptions.EnableRetryOnFailure(
                        maxRetryCount: 3,
                        maxRetryDelay: TimeSpan.FromSeconds(30),
                        errorNumbersToAdd: null);
                });
            
            if (options.EnableSensitiveDataLogging)
            {
                dbOptions.EnableSensitiveDataLogging();
            }
        });
        
        // Repositories
        services.AddScoped<IOrderRepository, OrderRepository>();
        services.AddScoped<ICustomerRepository, CustomerRepository>();
        services.AddScoped<IProductRepository, ProductRepository>();
        services.AddScoped(typeof(IRepository<,>), typeof(EfRepository<,>));
        
        // Unit of Work
        services.AddScoped<IUnitOfWork, UnitOfWork>();
        
        // External Services
        services.AddExternalServices(configuration);
        
        // Caching
        services.AddStackExchangeRedisCache(options =>
        {
            options.Configuration = configuration.GetConnectionString("Redis");
        });
        services.AddScoped<ICacheService, RedisCacheService>();
        
        // Background Jobs
        services.AddHangfire(config =>
        {
            config.UseSqlServerStorage(
                configuration.GetConnectionString("Hangfire"));
        });
        services.AddHangfireServer();
        
        return services;
    }
    
    private static IServiceCollection AddExternalServices(
        this IServiceCollection services,
        IConfiguration configuration)
    {
        // Email
        services.Configure<SendGridOptions>(configuration.GetSection("SendGrid"));
        services.AddTransient<IEmailSender, SendGridEmailSender>();
        
        // Payment
        services.Configure<StripeOptions>(configuration.GetSection("Stripe"));
        services.AddTransient<IPaymentGateway, StripePaymentGateway>();
        
        // Storage
        services.Configure<AzureStorageOptions>(configuration.GetSection("AzureStorage"));
        services.AddTransient<IFileStorageService, AzureBlobStorageService>();
        
        return services;
    }
}

// Presentation Layer DI (Composition Root)
public static class PresentationServiceExtensions
{
    public static IServiceCollection AddPresentationServices(this IServiceCollection services)
    {
        // Controllers
        services.AddControllers(options =>
        {
            options.Filters.Add<ValidationFilter>();
            options.Filters.Add<ModelStateFilter>();
        });
        
        // API Versioning
        services.AddApiVersioning(options =>
        {
            options.DefaultApiVersion = new ApiVersion(1, 0);
            options.AssumeDefaultVersionWhenUnspecified = true;
            options.ReportApiVersions = true;
            options.ApiVersionReader = new UrlSegmentApiVersionReader();
        });
        
        // Swagger/OpenAPI
        services.AddEndpointsApiExplorer();
        services.AddSwaggerGen(options =>
        {
            options.SwaggerDoc("v1", new OpenApiInfo
            {
                Title = "My API",
                Version = "v1",
                Description = "Clean Architecture API"
            });
            
            options.CustomSchemaIds(type => type.FullName?.Replace("+", "."));
        });
        
        // CORS
        services.AddCors(options =>
        {
            options.AddDefaultPolicy(builder =>
            {
                builder.AllowAnyOrigin()
                       .AllowAnyMethod()
                       .AllowAnyHeader();
            });
        });
        
        return services;
    }
}

// Program.cs - Composition Root
var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services
    .AddApplicationServices()
    .AddInfrastructureServices(builder.Configuration)
    .AddPresentationServices();

var app = builder.Build();

// Configure pipeline
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseExceptionHandling();
app.UseHttpsRedirection();
app.UseCors();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

---

## Chapter 10: Repository Pattern and Unit of Work

### Repository Pattern Deep Dive

The Repository pattern mediates between the domain and data mapping layers, acting like an in-memory domain object collection. It provides a clean separation between the domain and persistence layers, allowing the domain to remain ignorant of how data is stored.

```csharp
// Complete Repository Pattern Implementation

// Base Repository Interface
public interface IRepository<T, in TId> where T : class
{
    Task<T?> GetByIdAsync(TId id, CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> GetAllAsync(CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> FindAsync(
        Expression<Func<T, bool>> predicate, 
        CancellationToken cancellationToken = default);
    Task AddAsync(T entity, CancellationToken cancellationToken = default);
    Task UpdateAsync(T entity, CancellationToken cancellationToken = default);
    Task DeleteAsync(T entity, CancellationToken cancellationToken = default);
}

// Read-Only Repository (CQRS - Query Side)
public interface IReadOnlyRepository<T>
{
    Task<T?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> GetAllAsync(CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> FindAsync(
        Expression<Func<T, bool>> predicate,
        CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> GetBySpecificationAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default);
    Task<int> CountAsync(
        Expression<Func<T, bool>>? predicate = null,
        CancellationToken cancellationToken = default);
    Task<bool> AnyAsync(
        Expression<Func<T, bool>> predicate,
        CancellationToken cancellationToken = default);
}

// Write-Only Repository (CQRS - Command Side)
public interface IWriteRepository<T>
{
    Task AddAsync(T entity, CancellationToken cancellationToken = default);
    Task AddRangeAsync(IEnumerable<T> entities, CancellationToken cancellationToken = default);
    Task UpdateAsync(T entity, CancellationToken cancellationToken = default);
    Task DeleteAsync(T entity, CancellationToken cancellationToken = default);
    Task DeleteRangeAsync(IEnumerable<T> entities, CancellationToken cancellationToken = default);
}

// Combined Repository
public interface IRepository<T> : IReadOnlyRepository<T>, IWriteRepository<T>
    where T : class
{
}

// Specification-Based Repository
public interface ISpecificationRepository<T> where T : class
{
    Task<T?> FirstOrDefaultAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default);
    Task<IReadOnlyList<T>> ListAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default);
    Task<int> CountAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default);
}

// Implementation
public class EfRepository<T> : IRepository<T>, ISpecificationRepository<T>
    where T : class
{
    protected readonly AppDbContext _context;
    protected readonly DbSet<T> _dbSet;
    
    public EfRepository(AppDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }
    
    public async Task<T?> GetByIdAsync(Guid id, CancellationToken cancellationToken = default)
    {
        return await _dbSet.FindAsync(new object[] { id }, cancellationToken);
    }
    
    public async Task<IReadOnlyList<T>> GetAllAsync(CancellationToken cancellationToken = default)
    {
        return await _dbSet.AsNoTracking().ToListAsync(cancellationToken);
    }
    
    public async Task<IReadOnlyList<T>> FindAsync(
        Expression<Func<T, bool>> predicate,
        CancellationToken cancellationToken = default)
    {
        return await _dbSet.AsNoTracking()
            .Where(predicate)
            .ToListAsync(cancellationToken);
    }
    
    public async Task<IReadOnlyList<T>> GetBySpecificationAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default)
    {
        return await ApplySpecification(specification)
            .AsNoTracking()
            .ToListAsync(cancellationToken);
    }
    
    public async Task<int> CountAsync(
        Expression<Func<T, bool>>? predicate = null,
        CancellationToken cancellationToken = default)
    {
        var query = _dbSet.AsNoTracking();
        
        if (predicate is not null)
        {
            query = query.Where(predicate);
        }
        
        return await query.CountAsync(cancellationToken);
    }
    
    public async Task<bool> AnyAsync(
        Expression<Func<T, bool>> predicate,
        CancellationToken cancellationToken = default)
    {
        return await _dbSet.AsNoTracking().AnyAsync(predicate, cancellationToken);
    }
    
    public async Task AddAsync(T entity, CancellationToken cancellationToken = default)
    {
        await _dbSet.AddAsync(entity, cancellationToken);
    }
    
    public async Task AddRangeAsync(
        IEnumerable<T> entities,
        CancellationToken cancellationToken = default)
    {
        await _dbSet.AddRangeAsync(entities, cancellationToken);
    }
    
    public Task UpdateAsync(T entity, CancellationToken cancellationToken = default)
    {
        _dbSet.Update(entity);
        return Task.CompletedTask;
    }
    
    public Task DeleteAsync(T entity, CancellationToken cancellationToken = default)
    {
        _dbSet.Remove(entity);
        return Task.CompletedTask;
    }
    
    public Task DeleteRangeAsync(
        IEnumerable<T> entities,
        CancellationToken cancellationToken = default)
    {
        _dbSet.RemoveRange(entities);
        return Task.CompletedTask;
    }
    
    public async Task<T?> FirstOrDefaultAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default)
    {
        return await ApplySpecification(specification)
            .FirstOrDefaultAsync(cancellationToken);
    }
    
    public async Task<IReadOnlyList<T>> ListAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default)
    {
        return await ApplySpecification(specification)
            .ToListAsync(cancellationToken);
    }
    
    public async Task<int> CountAsync(
        ISpecification<T> specification,
        CancellationToken cancellationToken = default)
    {
        return await ApplySpecification(specification)
            .CountAsync(cancellationToken);
    }
    
    protected IQueryable<T> ApplySpecification(ISpecification<T> specification)
    {
        return SpecificationEvaluator<T>.GetQuery(_dbSet, specification);
    }
}

// Specification Evaluator
public static class SpecificationEvaluator<T> where T : class
{
    public static IQueryable<T> GetQuery(
        IQueryable<T> inputQuery,
        ISpecification<T> specification)
    {
        var query = inputQuery;
        
        if (specification.Criteria is not null)
        {
            query = query.Where(specification.Criteria);
        }
        
        query = specification.Includes
            .Aggregate(query, (current, include) => current.Include(include));
        
        query = specification.IncludeStrings
            .Aggregate(query, (current, include) => current.Include(include));
        
        if (specification.OrderBy is not null)
        {
            query = query.OrderBy(specification.OrderBy);
        }
        else if (specification.OrderByDescending is not null)
        {
            query = query.OrderByDescending(specification.OrderByDescending);
        }
        
        if (specification.GroupBy is not null)
        {
            query = query.GroupBy(specification.GroupBy).SelectMany(x => x);
        }
        
        if (specification.IsPagingEnabled)
        {
            query = query.Skip(specification.Skip)
                         .Take(specification.Take);
        }
        
        return query;
    }
}
```

---

*Due to the length limit, the guide continues in the file with chapters 11-21 covering CQRS with MediatR, Validation, Domain Events, Mapping, Testing, Migration Strategies, Anti-Patterns, Real-World Implementation, Best Practices, Cheatsheet, and Resources.*
