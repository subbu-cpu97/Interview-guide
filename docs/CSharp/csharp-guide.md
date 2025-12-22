# C# – Basics

## * SOLID
SOLID is a set of 5 design principles that make software:  
✔ Maintainable  
✔ Extensible  
✔ Testable  
✔ Scalable  
✔ Clean  

| Principle                 | Meaning                                            |
| ------------------------- | -------------------------------------------------- |
| S – Single Responsibility | One class = One purpose                            |
| O – Open/Closed           | Open for extension, closed for modification        |
| L – Liskov Substitution   | Child should replace parent without breaking logic |
| I – Interface Segregation | No forcing extra methods on class                  |
| D – Dependency Inversion  | Depend on abstractions, not concrete classes       |

#### S stands for Single Responsibility Principle
A class should have only one reason to change.

**Scenario:**
When updating a Ticket:
    - Validate request
    - Update DB
    - Ticket history
    - Notify Team Lead [EMail]
    
If all logic stays inside TicketService → it becomes a God Class ❌  
Difficult to maintain, test, and extend.  
❌ Before (bad design — breaks SOLID)  
```CSharp
public class TicketService
{
    public void UpdateTicket(Ticket ticket)
    {
        // Validate request
        // Update DB
        // Log update
        // Ticket history
        // Notify Team Leader
    }
}
```
***Problem:***
    - SRP violated → one class does everything
    - Hard to extend
    - Unit testing = nightmare  
    
✔ After SOLID Applied (Clean Design)    
```CSharp
public class TicketService
{
    private readonly ITicketRepository _repository;
    private readonly ITicketValidator _validator;
    private readonly ITicketHistory _ticketHistory;
    private readonly ILogger _logger;
    private readonly INotification _notification;

    public TicketService(
        ITicketRepository repository,
        ITicketValidator validator,
        ITicketHistory ticketHistory
        ILogger logger,
        INotificationService notificationService)
    {
        _repository = repository;
        _validator = validator;
        _ticketHistory = ticketHistory;
        _logger = logger;
        _notificationService = notificationService;
    }

    public void UpdateTicket(Ticket ticket)
    {
        if (!_validator.Validate(ticket))
            return;

        _repository.Update(ticket);
        _ticketHistory.Save(tciket);
        _logger.Log("Ticket update successful");
        _notificationService.Notify(ticket);
    }
}
```
### O stands for Open/Closed Principle (OCP)
Software should be open for extension, closed for modification.  
You should be able to add new logic without toushing existing code
```CSharp
public interface ITicketValidator
{
    bool Validate(Ticket ticket);
}

public class SLAValidator : ITicketValidator
{
    public bool Validate(Ticket ticket)
    {
        return ticket.ResponseTime <= ticket.SLA;
    }
}

```

### LSP stands for Liskov Substitution Principle
Subtypes must be substitutable for their base types.  
Whenever INotification used, it should work perfectly with any implementation.  

```CSharp
public interface INotification {
    void Send(string mesage);
}

public class EmailService: INotification {
    public void Send(string message) {
        // Logic to send the email
    }
}

public class SMSService: INotification {
    public void Send(string message) {
        // Logic to send the SMS
    }
}

public class NotificationManager {
    private readonly IENumerable<INotification> _channels;
    public NotificationManager(IENumerable<INotification>  channel) {
        _channel = channel;
    }

    public void SendAll() {
        foreach(var channel in _channel) {
            channel.Send(message);
        }
    }
}
```
### ISP – Interface Segregation Principle
Don’t force classes to implement methods they don’t need
Interface should be focused and small.

***Bad Interface***
```CSharp
public interface INotificaion {
    void SendEmail(string message);
    void SendWhatsappMessage(string message);
    void SendSMS(string message);
}
```
***Good Interface***
```CSharp
public interface INotification {
    void Send(string mesage);
}
```
### DIP – Dependency Inversion Principle
High level modules depend on abstractions, not concrete classes
TicketService should not know how SMS or Email works.

***Why**
This makes the system flexible.  
You can change entire notification mechanism and TicketService won't care.

See TicketService example.


## Singleton Pattern?
The Singleton pattern ensures that a class has only one instance in the entire application and provides a global point of access to that instance.

***Key characteristics:***  
    - Only one instance is created  
    - That instance is globally accessible  
    - Object creation is controlled inside the class  
    - Often used for shared resources (e.g., configuration, logger, cache)  

```CSharp
public class EposConfig
{
    private static EposConfig _instance;

    public string SlaDefaultHours { get; private set; }
    public string SupportEmail { get; private set; }

    private EposConfig()
    {
        // Ideally load from config or DB
        SlaDefaultHours = "24";
        SupportEmail = "support@epos.com";
    }

    public static EposConfig Instance
    {
        get
        {
            if (_instance == null)
            {
                _instance = new EposConfig();
            }

            return _instance;
        }
    }
}

```
***Usage:***
```Charp
var config = EposConfig.Instance;
Console.WriteLine(config.SlaDefaultHours);
```
⚠ This is NOT thread-safe. In web apps with multiple threads, it can create multiple instances. So this version is only for explanation, not for production.    

***Thread-Safe Singleton (With lock)***  
```CSharp
public class EposConfig
{
    private static EposConfig? _instance;
    private static readonly object _lock = new object();

    public string SlaDefaultHours { get; private set; }
    public string SupportEmail { get; private set; }

    private EposConfig()
    {
        // Load from config/DB once
        SlaDefaultHours = "24";
        SupportEmail = "support@epos.com";
    }

    public static EposConfig Instance
    {
        get
        {
            if (_instance == null)
            {
                lock (_lock)
                {
                    if (_instance == null)
                    {
                        _instance = new EposConfig();
                    }
                }
            }

            return _instance;
        }
    }
}

```
This is the classic double-check locking Singleton.  
***Modern .NET Style — Using Lazy<T>***
```CSharp
public class EposConfig
{
    private static readonly Lazy<EposConfig> _instance =
        new Lazy<EposConfig>(() => new EposConfig());

    public static EposConfig Instance => _instance.Value;

    public string SlaDefaultHours { get; private set; }
    public string SupportEmail { get; private set; }

    private EposConfig()
    {
        // Load once
        SlaDefaultHours = "24";
        SupportEmail = "support@epos.com";
    }
}

```

Lazy<T> ensures: 
    - Thread-safe
    - Only created when first accessed

***EPOS Real Use Case – Singleton Config + TicketService***  
Imagine: You have EPOS SLA configuration and status labels that you want to reuse.   
```CSharp
public class TicketService
{
    public void CheckSla(Ticket ticket)
    {
        var config = EposConfig.Instance;

        // Using shared default SLA
        if (ticket.ResolutionHours > int.Parse(config.SlaDefaultHours))
        {
            // Mark as SLA breached
        }
    }
}

```
## Singleton vs DI AddSingleton
In modern ASP.NET Core, we rarely use the classic Singleton pattern.  
Instead, we use dependency injection with Singleton lifetime:  
```CSharp
builder.Services.AddSingleton<IEposConfig, EposConfig>();

```

***Then:***
```CSharp
public class TicketService
{
    private readonly IEposConfig _config;

    public TicketService(IEposConfig config)
    {
        _config = config;
    }

    public void CheckSla(Ticket ticket)
    {
        if (ticket.ResolutionHours > _config.SlaDefaultHours)
        {
            // ...
        }
    }
}

```
✅ This still gives one instance per app  
✅ But now:  
    - Easier to unit test (can mock IEposConfig)  
    - No hidden static dependencies  
    - Better lifetime management by DI container  
    
👉 Architect answer:  
“In .NET Core, instead of manually coding Singletons, we prefer DI-managed singletons using AddSingleton, which keeps design testable and maintainable.”


***Full Overview***

The Singleton pattern ensures that only one instance of a class exists and provides a global point of access.  

In our EPOS Ticket Maintenance system, we used a Singleton-style configuration component to store SLA defaults, ticket status codes, and notification settings that are used across Call Register, Team Leader assignment, and reporting modules.  

Instead of reloading this configuration for each request, we loaded it once and shared it across the application. That improved performance and gave consistent behavior across modules.  

In classic C#, we can implement Singleton using a private constructor and a static Instance property, but in modern ASP.NET Core I prefer using dependency injection with AddSingleton. That keeps the design testable because we can mock the interface and avoids hard-coded static dependencies.  

At the same time, I am careful not to store per-request or mutable shared state inside a Singleton, because that can cause race conditions and unpredictable behavior. I mainly use Singletons for read-only configuration, logging, or shared factories and always ensure they are thread-safe.  

## Delegates and Events

| Concept      | Meaning                                                             |
| ------------ | ------------------------------------------------------------------- |
| **Delegate** | A type that **references a method** (function pointer in C#)        |
| **Event**    | A notification mechanism built on delegates → **publish/subscribe** |


Delegates → choose which method to call
Events → notify when something happens

**Without delegates:**
    - Methods are tightly coupled
    - We must know exact method at compile time
**With delegates:**
    - We pass method like data
    - Very useful when callback required
    - Helps loose coupling → scalable architecture

**Types of Delegates**
| Type           | Syntax                       | Usage           |
| -------------- | ---------------------------- | --------------- |
| Named Delegate | `delegate void MyDelegate()` | Early design    |
| Func           | `Func<T>`                    | Returns value   |
| Action         | `Action<T>`                  | No return       |
| Predicate      | `Predicate<T>`               | Returns boolean |


```CSharp
using System;

// Named Delegate
public delegate void TicketHandler(string ticketId);

public class TicketNotifier
{
    // Common method used by all delegate examples
    public void Notify(string ticketId)
    {
        Console.WriteLine($"Ticket {ticketId} updated");
    }

    // Method for boolean Check (used by Predicate & Func)
    public bool IsValidTicket(string ticketId)
    {
        return ticketId.StartsWith("TKT");
    }
    
    public static void Main()
    {
        TicketNotifier notifier = new TicketNotifier();

        // ------------------------------------
        // 1️⃣ Named Delegate (Early Design)
        // Points directly to a method with void return
        TicketHandler handler = notifier.Notify;
        handler("TKT1001");
        // ------------------------------------

        // ------------------------------------
        // 2️⃣ Func -> MUST return a value
        // Func<InputType, ReturnType>
        Func<string, bool> funcCheck = notifier.IsValidTicket;
        bool funcResult = funcCheck("TKT1002");
        Console.WriteLine($"Func: Ticket valid? {funcResult}");
        // ------------------------------------

        // ------------------------------------
        // 3️⃣ Action -> ONLY does work (no return)
        Action<string> actionNotify = notifier.Notify;
        actionNotify("TKT1003");
        // ------------------------------------

        // ------------------------------------
        // 4️⃣ Predicate -> ALWAYS returns boolean
        // Predicate<T> == Func<T, bool>
        Predicate<string> predicateCheck = notifier.IsValidTicket;
        bool predicateResult = predicateCheck("ABC1004");
        Console.WriteLine($"Predicate: Ticket valid? {predicateResult}");
        // ------------------------------------
    }
}


```

**Anonymous Method Example**
```CSharp
TicketHandler handler = delegate(string ticketId)
{
    Console.WriteLine($"Anonymous Handler - {ticketId}");
};

handler("TKT1002");

```
**Lambda Expression Delegate**
✔ Shortest & most commonly used syntax today.
```CSharp
TicketHandler handler = ticketId => 
    Console.WriteLine($"Lambda Handler: {ticketId}");
handler("TKT1003");
```
**Multicast Delegates**
✔ One delegate pointing to multiple methods

```CSharp
handler += new TicketNotifier().Notify;
handler += id => Console.WriteLine($"SMS sent for {id}");
handler("TKT2001");

```
***Events — Publisher & Subscriber Pattern***
    - Events = Delegates + Access Control
    - Only publisher can trigger the event

Perfect for EPOS Ticket system — notification when ticket status changes

```CSharp
public class TicketService
{
    public delegate void TicketUpdated(string ticketId);
    public event TicketUpdated? OnTicketUpdated;

    public void UpdateTicket(string ticketId)
    {
        Console.WriteLine("Ticket updated!");
        OnTicketUpdated?.Invoke(ticketId);
    }
}

public class TeamLeaderService
{
    public void Alert(string ticketId)
    {
        Console.WriteLine($"TeamLead Alerted for {ticketId}");
    }
}

```
**Usage:**
```CSharp
var ticketService = new TicketService();
var teamLeader = new TeamLeaderService();

ticketService.OnTicketUpdated += teamLeader.Alert;
ticketService.UpdateTicket("TKT3001");

```

TicketService does not know who is subscribed  
Ultra low coupling → scalable architecture  

Delegates allow passing method references dynamically, and Events build a publish-subscribe model on top of delegates.
In our EPOS Ticketing system, we use them for notifications when a ticket status changes so that inventory, SLA monitoring, customer callback, and reporting modules react independently without tight coupling.
This supports extensibility, maintainability, and real-time architecture requirements.



## Dependency Injection

Dependency Injection is a design pattern where the framework automatically provides required objects (dependencies) to a class instead of the class creating them itself.    

✔ Promotes loose coupling  
✔ Improves testability  
✔ Follows SOLID (especially DIP)  
✔ Centralized configuration for all dependencies  

**Why DI is built-in to ASP.NET Core?**
| Before DI                                             | With DI                                   |
| ----------------------------------------------------- | ----------------------------------------- |
| Classes create dependencies manually → tight coupling | Framework constructs dependencies for you |
| Hard to test                                          | Easy mocking                              |
| Hard to configure                                     | Centralized service container             |
| Painful structure in large apps                       | Scalable architecture                     |

**DI in Action — Constructor Injection**
```CSharp
public class TicketService
{
    private readonly ITicketRepository _repository;

    public TicketService(ITicketRepository repository)
    {
        _repository = repository;
    }

    public void UpdateTicket(Ticket ticket)
    {
        _repository.Update(ticket);
    }
}


// Program.cs

builder.Services.AddScoped<ITicketRepository, TicketRepository>();
builder.Services.AddScoped<ITicketValidator, SLAValidator>();
builder.Services.AddScoped<INotificationService, EmailNotificationService>();


```

**Types of DI Lifetimes**

| Lifetime      | Meaning                             | EPOS Usage Example                      |
| ------------- | ----------------------------------- | --------------------------------------- |
| **Singleton** | One instance for entire application | App configuration, constant lookup data |
| **Scoped**    | One instance per request            | TicketService, validation services      |
| **Transient** | New instance for each usage         | Helper utilities, formatting            |

```CSharp
builder.Services.AddSingleton<ISlaConfig, SlaConfig>();
builder.Services.AddScoped<ITicketService, TicketService>();
builder.Services.AddTransient<IPdfGenerator, PdfGenerator>();
```
**What happens internally?**
ASP.NET Core:  
    - Uses IServiceCollection to register services  
    - Builds a service container  
    - Uses constructor injection by default  
    - Resolves dependencies when creating Controller  
    - No new keyword in business layer → IoC container does the job.  

TicketService does not know implementation of repository → Loose coupling 

Dependency Injection in ASP.NET Core enables loosely coupled architecture where classes depend on interfaces instead of implementations, improving testability and maintainability using built-in IoC container.

**What is IoC (Inversion of Control)?**
IoC is a design principle where the control of object creation and dependency management is inverted from the class itself to an external container or framework.  

## AsNoTracking()

📌 When reading data without updating it

Disables EF change tracking → improves performance
Best for queries returning DTOs or reporting data
  
✔ Recommended for:  
    Search screens    
    Lists  
    Reports  
    Dashboard APIs  
    Read-only lookups (City, Bank, CallTypes)  

❌ Don’t use when updating entities (EF won’t track changes)  

📌 Example:  
```CSharp
var cities = await _db.Cities
    .AsNoTracking()
    .ToListAsync();

```

**Interview line:**
Using AsNoTracking can improve read performance by 30–40% in large result sets.

## Include()
📌 Used for Eager Loading (load related tables)
Example: Terminal → Merchant → City
```CSharp
var terminal = await _db.Terminals
    .Include(t => t.Merchant)
    .ThenInclude(m => m.City)
    .FirstOrDefaultAsync(x => x.Id == id);
```

✔ Use when you really need related objects
❌ Can cause heavy data loads → row explosion issues

**Interview rule:**
Use projection instead of Include for API DTO responses.

**Example projection (Better):**
```CSharp
var result = await _db.Terminals
    .Select(t => new TerminalDto
    {
        TerminalId = t.TerminalId,
        MerchantName = t.Merchant.MerchantName
    })
    .ToListAsync();
```
## AsQueryable()

📌 Converts collection to IQueryable<T> so EF can build dynamic queries
Used when applying filters later

**Example:**
```Csharp
var query = _db.Terminals.AsQueryable();

if(cityId != null)
    query = query.Where(t => t.Merchant.CityId == cityId);

if(bankId != null)
    query = query.Where(t => t.BankId == bankId);

var result = await query.ToListAsync();
```

✔ Very useful in search screens with multiple filters
✔ Building queries dynamically

## What is the difference between readonly and const in C#?

**Const**  
    A const value is a compile-time constant whose value is fixed forever and embedded into the calling code.  
    const values are inlined at compile time, so changing them requires recompiling all dependent assemblies.  
**Readonly**  
    A readonly field is a runtime constant whose value can be assigned only once, usually at object creation time.  

| Aspect                      | `const`                       | `readonly`         |
| --------------------------- | ----------------------------- | ------------------ |
| Time of assignment          | Compile-time                  | Runtime            |
| Where value is stored       | In calling assembly (inlined) | In object instance |
| Can use non-primitive types | ❌ No                          | ✔ Yes              |
| Can depend on runtime value | ❌ No                          | ✔ Yes              |
| Can be set in constructor   | ❌ No                          | ✔ Yes              |
| Can change per environment  | ❌ No                          | ✔ Yes              |
| Versioning safe             | ❌ No                          | ✔ Yes              |


const → slightly faster (inlined)  

readonly → negligible overhead  

const values are compile-time constants and are inlined into consuming assemblies, which can cause versioning issues if the value changes.  
readonly fields are assigned at runtime and are safer for configuration-based or environment-specific values.  
In real-world enterprise applications, we prefer readonly unless the value is truly universal and guaranteed never to change.  

If there is even a 1% chance the value may change in future → use readonly, not const.  


## What is the sealed keyword used for in C#?  

The sealed keyword is used to prevent inheritance or prevent method overriding in C#.  
A sealed class cannot be inherited by any other class.  
It can be applied to:  
    1.Classes  
    2.Methods (only overriding methods)  


**Why would we want this?**  
To:  
✔ Protect business rules  
✔ Prevent misuse or incorrect extension  
✔ Improve security  
✔ Improve performance (minor but real)  
✔ Enforce architectural boundaries        

The sealed keyword is used to prevent inheritance or further method overriding.  
It is commonly used to protect critical business logic, enforce architectural boundaries, and avoid misuse of classes.  
In enterprise systems like EPOS, we seal classes that represent final domain rules, such as SLA or ticket status   calculations, to ensure consistency and correctness.  

## What are all the access modifiers available for types in C#?    

✅ public  
    The type is accessible from anywhere (any project, any assembly).  

    When to use:
        - API contracts
        - Shared libraries
        - Services used across projects
        
    📌 Interview line:
        Public types form the external contract of an assembly.
    
✅ internal  
    The type is accessible only within the same assembly (project).  
    
    When to use:  
        - Internal implementation details  
        - Helper or utility classes  
        - Prevent misuse from other projects  
        
    📌 Interview line:
        Internal types help encapsulate implementation details within the assembly.    

| Modifier             | Meaning                                    |
| -------------------- | ------------------------------------------ |
| `public`             | Accessible everywhere                      |
| `private`            | Accessible only inside the containing type |
| `protected`          | Accessible in derived types                |
| `internal`           | Accessible within same assembly            |
| `protected internal` | Derived types **or** same assembly         |
| `private protected`  | Derived types **and** same assembly        |

**Architect-Level Insight**
Use internal aggressively to reduce your public surface area.  
Only expose types that are part of your intended API contract.  

This:  
    Improves maintainability 
    Prevents accidental usage  
    Makes refactoring safer  

In C#, top-level types can only be public or internal.  
Nested types can use all access modifiers including private, protected, protected internal, and private protected.  
This design helps control visibility and enforce encapsulation at both assembly and class levels.  


## What is the difference between an interface and an abstract class in C#?

Interface 
    An interface defines a contract that a class must implement.  
    It specifies what the class should do, not how.

Abstract Class  
    An abstract class represents an incomplete base class that provides both behavior and contracts.  
    It defines what + partial how.  

| Aspect               | Interface               | Abstract Class        |
| -------------------- | ----------------------- | --------------------- |
| Purpose              | Contract                | Base class            |
| Implementation       | No (only signatures)    | Yes (partial or full) |
| Fields               | ❌ No                    | ✔ Yes                 |
| Constructors         | ❌ No                    | ✔ Yes                 |
| Access modifiers     | Public only (members)   | Any                   |
| Multiple inheritance | ✔ Yes (many interfaces) | ❌ No (only one class) |
| State (fields)       | ❌ No                    | ✔ Yes                 |
| Versioning           | Easier                  | Risky                 |
| When to use          | Capability / role       | Shared behavior       |



An interface defines a contract and supports multiple inheritance, making it ideal for loose coupling and dependency injection.  
An abstract class provides a base with shared behavior and state, and is useful when multiple derived classes share common logic.  

In enterprise systems like EPOS, we use interfaces for services and repositories to keep the system flexible, and abstract classes when we need to share common functionality or enforce base behavior.  


## When is a static constructor called in C#?

A static constructor is used to initialize static data of a class.

Key characteristics:   
     - Has no access modifier  
     - Takes no parameters
     - Runs automatically
     - Runs only once
     - Cannot be called explicitly

A static constructor is called automatically by the CLR, before the first use of the type, and only once per AppDomain.

The static constructor is triggered when any one of these happens for the first time:

```CSharp
// Creating an instance of the class
var service = new TicketService(); // static ctor runs first

 // Accessing a static field or property
var value = TicketService.MaxRetries; // static ctor runs first

Calling a static method
TicketService.Initialize(); // static ctor runs first

```
“The static constructor runs before any static member is accessed or any instance is created.”


Order of Execution  
    If a class has:  
        Static fields  
        Static constructor  
        Instance constructor  
Order:
    1️⃣ Static field initializers  
    2️⃣ Static constructor (once)  
    3️⃣ Instance constructor (every time)  

Static constructors are thread-safe by default.

CLR ensures:  
    Only one thread executes the static constructor    
    Other threads wait until it completes  
    
    📌 Interview line:  
        “Static constructors are implicitly thread-safe.”

What happens if static constructor throws an exception?  
    ⚠ Very important:  
         If a static constructor throws an exception:  
            The type becomes unusable  
            CLR throws TypeInitializationException    
            All future access fails  
    📌 Architect warning:
            “Static constructors must be simple and reliable. Avoid heavy logic or I/O.”

** When should you use a static constructor? **  
    ✔ Initialize static readonly fields  
    ✔ Load constant configuration once  
    ✔ Prepare lookup tables  
    ✔ One-time setup logic  

❌ Not for:    
    DB calls 
    Network calls  
    Long-running logic      


A static constructor is called automatically by the CLR before the first use of the type—either when an instance is created or a static member is accessed. It runs only once per application domain and is thread-safe by default.  

## How do you create an extension method in C#?  

Extension methods let you add new methods to existing types without modifying them. This is a design feature used heavily in LINQ, ASP.NET Core, and enterprise codebases.  

An extension method is a static method that appears as if it were an instance method on another type.  

Key ideas interviewers expect:
    No inheritance
    No modification of original type
    Compiler rewrites the call

📌 Example you already use:
```CSharp
    list.Where(x => x.IsActive);
```

Where() is an extension method on IEnumerable<T>.

***To create an extension method, you must follow all these rules:***
    Method must be static
    It must be inside a static class
    The first parameter must be prefixed with this
    The first parameter defines which type is extended
    The namespace must be imported (using)


At compile time, the compiler converts the extension method call into a static method call

An extension method is a static method defined in a static class that allows adding behavior to existing types without modifying or inheriting from them.  
It’s created by using the this keyword on the first parameter.  
In enterprise applications like EPOS, we use extension methods to keep reusable domain logic clean and expressive, such as SLA checks or filtering logic, without bloating entity classes.  

## Does C# support multiple inheritance?

    No, C# does not support multiple inheritance of classes.
    Yes, C# supports multiple inheritance through interfaces.

What is multiple inheritance?  
    Multiple inheritance means a class inherits from more than one base class.      
```CSharp
    // ❌ Not allowed in C#
    class MyClass : BaseClass1, BaseClass2
    {
    }

```

Why is it not allowed?
    To avoid the Diamond Problem.

Diamond Problem (conceptual)
    Two base classes have the same method
    Derived class doesn’t know which implementation to use
    Causes ambiguity and bugs

📌 Interview line:
    “C# avoids multiple inheritance of classes to prevent ambiguity and complexity like the diamond problem.”  

What C# DOES support instead (Important)
✔ Single inheritance + Multiple interfaces    
```CSharp
    class TicketService : BaseService, ILogging, IAuditable, INotification
    {
    }

```
✔ One base class
✔ Multiple interfaces

**Why interfaces solve the problem**
    Interfaces:
        Do not contain state
        Do not cause ambiguity
        Define only contracts


“C# uses interfaces to support multiple inheritance of behavior without state.”

C# does not support multiple inheritance of classes to avoid ambiguity and complexity such as the diamond problem.
However, it supports multiple inheritance through interfaces, allowing a class to implement multiple behaviors while inheriting from only one base class.  
This design encourages clean architecture and composition over inheritance.  

## Explain boxing and unboxing in C#.

**What is Boxing?**
    Boxing is the process of converting a value type (stored on the stack) into a reference type (stored on the heap).  
    
What actually happens internally?  
    CLR allocates memory on the heap  
    Copies the value into that heap object  
    Returns a reference to it  

📌 Key point:  
    Boxing creates a new object on the heap.  

**What is Unboxing?**
 Unboxing is the process of converting a boxed object back into a value type.  
 
What happens internally?  
    CLR checks the object’s actual type  
    Copies the value from heap to stack  

📌 Important:  
    Unboxing requires explicit casting.  



## What are the heap and stack in C# (or .NET), and how do they work?    

**What is the Stack?**   
    The stack is a region of memory used to store method execution data.  

It stores:   
    Method parameters  
    Local variables (value types)  
    Method call information (call frames)  
    Return addresses 
    
Key characteristics   
    Fast (LIFO – Last In, First Out)
    Automatically managed
    Memory is freed immediately when method exits  
    Thread-specific (each thread has its own stack)  

📌 Interview line:  
“Stack memory is short-lived and automatically cleaned up when the method scope ends.”      

**What is the Heap?**
    The heap is a region of memory used to store objects with longer lifetimes.

It stores:  
    Reference type objects
    Arrays  
    Objects shared across methods  
    Objects with dynamic lifetime  

Key characteristics  
    Slower than stack  
    Managed by Garbage Collector (GC)  
    Shared across threads  
    Lifetime is non-deterministic  

📌 Interview line:  
“Heap memory is managed by the garbage collector and is used for objects whose lifetime is not bound to a single method.”  

Value Types (usually stack) 
    int, double, bool  
    struct  
    enum  
Reference Types (heap)  
    class  
    string  
    array  
    object  


    | Aspect            | Stack              | Heap                  |
| ----------------- | ------------------ | --------------------- |
| Speed             | Very fast          | Slower                |
| Memory management | Automatic          | Garbage Collector     |
| Lifetime          | Short              | Long                  |
| Scope             | Method-level       | Application-level     |
| Thread-safe       | Yes (thread-local) | Needs synchronization |
| Size              | Limited            | Large                 |


The stack is a fast, thread-local memory used for method execution and local variables, with automatic cleanup when the method exits.  
The heap stores reference-type objects with longer lifetimes and is managed by the garbage collector.  
In enterprise applications, we use the stack for short-lived data and the heap for shared domain objects, while being mindful of heap allocations to avoid GC performance issues.  


## What is the difference between string and StringBuilder in C#?

**string**
    string is immutable.  
    Once created, its value cannot be changed.  

Any modification:
    Creates a new object  
    Allocates memory on the heap  
    Old string waits for GC  

**StringBuilder**
    StringBuilder is mutable.  
    It allows you to modify the same object without creating new instances.  

| Aspect            | `string`                  | `StringBuilder`                |
| ----------------- | ------------------------- | ------------------------------ |
| Mutability        | Immutable                 | Mutable                        |
| Memory allocation | New object per change     | Reuses same object             |
| Performance       | Poor for frequent changes | Excellent for frequent changes |
| Thread safety     | Thread-safe               | ❌ Not thread-safe              |
| Best use          | Fixed / small text        | Repeated concatenation         |
| Namespace         | `System`                  | `System.Text`                  |


string in C# is immutable, meaning every modification creates a new object, which can cause performance issues in loops or repeated concatenations.  
StringBuilder is mutable and allows modifications to the same object, making it more efficient for dynamic string construction.  
In enterprise systems like EPOS, we use StringBuilder for logging, reports, and audit trails, and string for fixed or small text values.  

Optimized logging and reporting performance by replacing repeated string concatenation with StringBuilder.

## How do we create a date and time with a specific time zone in C#?

The Core Problem (Why Time Zones Matter)  
    Servers usually run in UTC
    Users operate in local time zones (e.g., Egypt, India)
Storing local time directly causes:
    Wrong reports
    DST issues
    Data inconsistency

📌 Architect rule:
    Always store time in UTC. Convert to local time only for display.

DateTime
    Can represent Local, UTC, or Unspecified
    Does not reliably store time-zone offset
    Easy to misuse ❌

DateTimeOffset ✅ (Recommended)
    Represents a date + time with an offset from UTC    


“For time-zone-aware systems, DateTimeOffset is preferred over DateTime.”

We store all timestamps in UTC and convert them to the user’s local time zone only when displaying or calculating SLA.  

In distributed systems, we always store timestamps in UTC.  
To create or display time in a specific time zone, we convert UTC using TimeZoneInfo or DateTimeOffset.  
DateTimeOffset is preferred because it preserves the UTC offset and avoids ambiguity, especially during daylight saving changes.  
In enterprise applications like EPOS, this ensures consistent SLA calculations and accurate reporting across regions.  

Implemented UTC-based date handling with time-zone conversion using DateTimeOffset to ensure accurate SLA calculations across regions.

## How do we change the current culture in C# / .NET?

**What is Culture in .NET?**
    Culture defines how data is formatted and interpreted, not the data itself.

Culture controls:
    Date formats (dd/MM/yyyy vs MM/dd/yyyy)
    Number formats (1,000.50 vs 1.000,50)
    Currency symbols
    Language-specific rules
    String comparisons

Examples:
    en-US
    en-IN
    ar-EG

.NET uses two main culture settings: 
    CurrentCulture  
    Affects formatting  
    Dates, numbers, currency  
    
CurrentUICulture  
    Affects resources  
    Labels, messages, UI text  

📌 Interview line:

“CurrentCulture controls formatting, while CurrentUICulture controls localization.”  

In .NET, culture controls formatting and localization.  
We can change culture using CultureInfo.CurrentCulture and CurrentUICulture.  
In enterprise applications, especially ASP.NET Core, culture is typically set per request based on user preference or   region, while data is stored independently in a culture-neutral format like UTC.  
This approach ensures correct formatting without affecting data integrity.  



## Explain about Async and Await?  

**Async** - Keyword that marks a method to run asynchronously (does not block UI/thread).  
**Await** - Keyword that pauses execution until an asynchronous task completes



