# C# – Basics

## SOLID
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

## Explain about Async and Await?

**Async** - Keyword that marks a method to run asynchronously (does not block UI/thread).  
**Await** - Keyword that pauses execution until an asynchronous task completes

