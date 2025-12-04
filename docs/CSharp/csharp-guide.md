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
##Singleton vs DI AddSingleton
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

## Explain about Async and Await?

**Async** - Keyword that marks a method to run asynchronously (does not block UI/thread).  
**Await** - Keyword that pauses execution until an asynchronous task completes

