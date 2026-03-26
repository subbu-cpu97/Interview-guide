# C# – Basics



[Abstract Class?](#abstract-class)  
[Interface](#Interface)
[Abstract Class vs Interface](#abstract-class-vs-interface)
[Polymorphism](#polymorphism)  
[Inheritanc?](#inheritanc?)    
[Abstraction?](#abstraction)     
[Encapsulation?](#encapsulation)     
[GC?](#gc)   
[SOLID?](#1-solid)   
[Singleton Pattern?](#singleton-pattern)


## Abstract Class?

An abstract class is a class that cannot be instantiated and is designed to be inherited, providing a common base with both implemented and unimplemented (abstract) members.

A partially implemented blueprint that forces derived classes to complete the missing parts.

**Key Characteristics**.   
❌ Cannot create object (new).    
✅ Can contain:     
Abstract methods (no body).  
Concrete methods (with implementation).    
✅ Supports encapsulation + inheritance.     
✅ Used when classes share common behavior.  

🔹 Basic Example
~~~CSharp
public abstract class Shape
{
    // 🔹 Abstract method (must be implemented by child)
    public abstract double CalculateArea();

    // 🔹 Concrete method (shared behavior)
    public void Display()
    {
        Console.WriteLine("Calculating area...");
    }
}
------------------
public class Circle : Shape
{
    public double Radius { get; set; }

    public override double CalculateArea()
    {
        return Math.PI * Radius * Radius;
    }
}

-----------
Usage
----------
Shape shape = new Circle { Radius = 5 };
shape.Display();
Console.WriteLine(shape.CalculateArea());

~~~~

**Why Use Abstract Class?**.   
✅ 1. Enforce Common Contract + Behavior.   
All derived classes must implement required methods.    
✅ 2. Code Reusability.     
Shared logic stays in base class.     
✅ 3. Partial Implementation.     
Some logic defined, some left to child classes.     
✅ 4. Domain Modeling.   
Represents real-world hierarchy.    

🔹 Real-World Example.  
👉 Payment System.  

~~~CSharp
public abstract class PaymentProcessor
{
    // Common logic
    public void ProcessPayment(decimal amount)
    {
        Validate(amount);
        ExecutePayment(amount);
        Log();
    }

    protected void Validate(decimal amount)
    {
        if (amount <= 0)
            throw new Exception("Invalid amount");
    }

    // Must be implemented by child
    protected abstract void ExecutePayment(decimal amount);

    protected void Log()
    {
        Console.WriteLine("Payment processed");
    }
}

--------------------

public class UpiPaymentProcessor : PaymentProcessor
{
    protected override void ExecutePayment(decimal amount)
    {
        Console.WriteLine("Processing UPI payment");
    }
}


~~~
This is Template Method Pattern.  
✔ Common workflow defined.  
✔ Specific steps customized.    

**🔹 Abstract vs Interface (Important)**


| Feature              | Abstract Class               | Interface                     |
| -------------------- | ---------------------------- | ----------------------------- |
| Methods with body    | ✅ Yes                        | ❌ (mostly no, except default) |
| Fields               | ✅ Yes                        | ❌ No                          |
| Constructors         | ✅ Yes                        | ❌ No                          |
| Multiple inheritance | ❌ No                         | ✅ Yes                         |
| Usage                | Shared behavior + base logic | Contract only                 |


🔥 When to Use Abstract Class.  
Use abstract class when:     
✔ Classes share common behavior.    
✔ Need default implementation + extensibility.    
✔ Want to enforce base workflow.     
🚨 When NOT to Use.   
❌ When only contract is needed → use interface.   
❌ When multiple inheritance required → interface.   
❌ When base class becomes too heavy → bad design.  
🔥 Architect Insight (Very Important).   
👉
Abstract class = “is-a + shared behavior”.   
Interface = “can-do capability”.    

🎯 Final Interview Answer (Strong).   
👉
“An abstract class is a base class that cannot be instantiated and is used to define common behavior while enforcing derived classes to implement specific functionality. It supports both shared logic and extensibility, making it ideal for modeling real-world hierarchies and implementing patterns like the Template Method.”



## Interface
An interface is a contract that defines a set of methods or behaviors that a class must implement, without providing the implementation.

👉 “It tells WHAT to do, not HOW to do it.”

**🔹 Basic Example**
~~~CSharp
// 🔹 Contract
public interface INotification
{
    void Send(); // No implementation
}
// 🔹 Implementation
public class EmailNotification : INotification
{
    public void Send()
    {
        Console.WriteLine("Email sent");
    }
}

##Usage

INotification notification = new EmailNotification();
notification.Send();

~~~

Caller only knows Send()
👉 Doesn’t care about implementation

### 🔥 Why Interfaces are Important (Architect Thinking)
✅ 1. Loose Coupling.   
Code depends on contract, not implementation.  
✅ 2. Extensibility (OCP).  
Add new implementations without changing existing code.  
✅ 3. Testability.  
Easy to mock in unit testing.  
✅ 4. Dependency Injection (DI).  
Core of modern .NET architecture. 

~~~CSharp
public interface ITicketProcessor
{
    void Process();
}
public class HardwareTicketProcessor : ITicketProcessor
{
    public void Process()
    {
        Console.WriteLine("Processing hardware ticket");
    }
}

public class SoftwareTicketProcessor : ITicketProcessor
{
    public void Process()
    {
        Console.WriteLine("Processing software ticket");
    }
}
##Usage 
ITicketProcessor processor = GetProcessor();
processor.Process();

~~~

👉 Plug-and-play design
👉 Add new processors without modifying existing code

***🔹 Key Features of Interface***   
❌ No fields.  
❌ No constructors.  
✅ Only method/property declarations.   
✅ Supports multiple inheritance.  
✅ Public by default.  

### Dependency Injection (Real-world usage)
~~~CSharp
public class OrderService
{
    private readonly INotification _notification;

    public OrderService(INotification notification)
    {
        _notification = notification;
    }

    public void PlaceOrder()
    {
        _notification.Send();
    }
}
~~~

### Interface Segregation Principle (ISP)

Interface Segregation Principle (ISP)

~~~CSharp
❌ Bad:
public interface IWorker
{
    void Work();
    void Eat();
}

👉 Robot doesn’t eat → problem


✔ Good:

public interface IWorkable
{
    void Work();
}

public interface IEatable
{
    void Eat();
}


~~~
### Default Interface Methods (C# 8+)

~~~CSharp
public interface ILogger
{
    void Log(string message);

    void Info(string message)
    {
        Console.WriteLine("INFO: " + message);
    }
}
~~~
👉 Allows default implementation

### Marker Interfaces (Rare but important).  
~~~CSharp
public interface ICacheable { }
~~~

👉 Used for tagging behavior



**👉 Interfaces are backbone of:**    
Clean Architecture.  
Microservices.  
Strategy Pattern.  
Dependency Injection.  
🚨 Common Mistakes.  
❌ Using concrete classes everywhere.   
❌ Not using interfaces for services.   
❌ Creating fat interfaces.  


🎯 Final Interview Answer (Strong).  
👉
“An interface is a contract that defines behavior without implementation, enabling loose coupling, extensibility, and testability. It is widely used in modern architectures with dependency injection to build scalable and maintainable systems.”


## Abstract Class vs Interface



| Aspect                   | Abstract Class                            | Interface                                                    |
| ------------------------ | ----------------------------------------- | ------------------------------------------------------------ |
| **Purpose**              | Provide base class with shared behavior   | Define a contract (capability)                               |
| **Implementation**       | Can have both abstract & concrete methods | Mostly only method signatures (C# 8+ allows default methods) |
| **Fields / State**       | ✅ Can have fields (state)                 | ❌ Cannot have instance fields                                |
| **Constructors**         | ✅ Yes                                     | ❌ No                                                         |
| **Access Modifiers**     | Can use all (private, protected, etc.)    | Members are public by default                                |
| **Multiple Inheritance** | ❌ Not supported                           | ✅ Supported                                                  |
| **Instantiation**        | ❌ Cannot instantiate                      | ❌ Cannot instantiate                                         |
| **Usage Relationship**   | “is-a” relationship                       | “can-do” capability                                          |




🔹 1. State Management.  
👉 Abstract class can store state.  
~~~CSharp
public abstract class Account.  
{
    protected decimal Balance; // ✅ State allowed
}
👉 Interface cannot:
public interface IAccount
{
    // ❌ No fields allowed
}

~~~

✔ Interview Insight:  
“Abstract classes are suitable when shared state is required.”   
🔹 2. Constructor Logic.   
👉 Abstract class can enforce initialization.     
~~~CSharp
public abstract class User
{
    public string Email { get; }

    protected User(string email)
    {
        Email = email;
    }
}
~~~
👉 Interface cannot enforce construction.      
✔ Insight:   
“Abstract class can ensure object validity at creation time.”    
🔹 3. Versioning & Maintainability.   
👉 Interface breaking change risk:   
~~~CSharp
public interface IService
{
    void Run();
}
👉 If you add:
void Stop(); // ❌ breaks all implementations
👉 Abstract class:
public abstract class Service
{
    public abstract void Run();

    public virtual void Stop() { } // ✅ Safe addition
}
~~~
✔ Insight:  
“Abstract classes are more version-friendly than interfaces.”    
🔹 4. Multiple Inheritance Flexibility.   
~~~CSharp
public class Service : ILogger, ICache, IAudit. 
{
}
~~~
👉 Only possible with interfaces.  
✔ Insight:    
“Interfaces enable composition of behaviors.”    
🔹 5. Default Implementation (Modern C#).    
👉 Interfaces (C# 8+):   
~~~CSharp
public interface ILogger
{
    void Log(string msg);

    void Info(string msg)
    {
        Console.WriteLine(msg);
    }
}
~~~
👉 Abstract class always supported this.   
✔ Insight:   
“Interfaces are evolving towards behavior, but still lack state.”    
🔹 6. Dependency Injection (DI Usage).   
👉 
Interfaces are preferred in DI:    
~~~CSharp
services.AddScoped<INotification, EmailNotification>();
~~~
✔ Insight:   
“Interfaces decouple implementation from usage, making them ideal for DI.”   
🔹 7. Testing & Mocking.  
👉 Interfaces are easier to mock:   
~~~CSharp
var mock = new Mock<INotification>();
~~~
👉 Abstract classes require partial mocking.  
✔ Insight:   
“Interfaces improve testability significantly.”   
🔹 8. Performance (Rare but advanced).  
👉 Abstract class:   
Slightly faster (direct inheritance).   
👉 Interface:   
Slight overhead (interface dispatch). 
✔ Insight (advanced):  
“In high-performance systems, abstract classes may be slightly more efficient.”  
🔹 9. Design Intent. 
Use Case	Choose.  
Shared logic + state	Abstract Class.  
Contract / extensibility	Interface.  
🔥 Real-World Example (Architect Level).  
👉 Payment System.  
Interface. 
~~~CSharp
public interface IPayment
{
    void Pay(decimal amount);
}
~~~
👉 Multiple implementations:    
UPI.   
Stripe.  
PayPal.  
Abstract Class.  
~~~CSharp
public abstract class PaymentBase
{
    protected void Validate(decimal amount)
    {
        if (amount <= 0)
            throw new Exception("Invalid amount");
    }

    public abstract void Pay(decimal amount);
}
👉 Combine both:
public class UpiPayment : PaymentBase, IPayment
{
    public override void Pay(decimal amount)
    {
        Validate(amount);
        Console.WriteLine("UPI payment");
    }
}
~~~
🚨 Hidden Interview Traps.   
❌ Saying:   
“Interface is always better” → ❌ Wrong.   
✔ Correct: Depends on use case.   
❌ Ignoring versioning problem.  
❌ Not mentioning DI and testing.  
🎯 Final Interview Answer (Impressive).  

👉
“Interfaces define contracts and are ideal for loose coupling, extensibility, and dependency injection, while abstract classes provide shared behavior and state, making them suitable for base implementations. Interfaces support multiple inheritance and better testability, whereas abstract classes are more version-friendly and allow controlled initialization.”


## Polymorphism?   
Polymorphism allows objects to take multiple forms, enabling the same method or interface to behave differently based on the object’s type.

🧠 Simple Meaning.   
👉 “One interface, multiple implementations.”  

🔹 Types of Polymorphism in C#.    
Type	Description.  
Compile-time	Method overloading.   
Runtime	Method overriding (using inheritance).   

🔹 1. Compile-Time Polymorphism (Method Overloading)
~~~CSharp
public class Calculator
{
    public int Add(int a, int b)
    {
        return a + b;
    }

    public double Add(double a, double b)
    {
        return a + b;
    }
}
~~~
👉 Same method name → different parameters.  
👉 Decided at compile time.  

🔹 2. Runtime Polymorphism (Method Overriding).   
~~~CSharp
public class Payment
{
    public virtual void Pay()
    {
        Console.WriteLine("Base payment");
    }
}
public class UpiPayment : Payment
{
    public override void Pay()
    {
        Console.WriteLine("UPI payment");
    }
}

🔹 Usage 
Payment payment = new UpiPayment();
payment.Pay(); // ✅ Calls derived method
~~~
👉 Decided at runtime.   
🔥 Why Polymorphism is Important (Architect View).  
Enables extensibility (OCP).   
Reduces if-else / switch complexity.   
Supports plug-and-play architecture.   
Enables strategy pattern, dependency injection.   
🔹 Keywords (Very Important).   
🔸 1. virtual.  
👉 Allows a method to be overridden in derived class.  
~~~CSharp
public class Animal
{
    public virtual void Speak()
    {
        Console.WriteLine("Animal sound");
    }
}
~~~
🔸 2. override.  
👉 Used in derived class to change base behavior.  
~~~CSharp
public class Dog : Animal
{
    public override void Speak()
    {
        Console.WriteLine("Bark");
    }
}
~~~
🔸 3. new (Method Hiding).  
👉 Hides base class method (not true polymorphism).   
~~~CSharp
public class BaseClass
{
    public void Show()
    {
        Console.WriteLine("Base");
    }
}
public class DerivedClass : BaseClass
{
    public new void Show()
    {
        Console.WriteLine("Derived");
    }
}
⚠️ Important Behavior
BaseClass obj = new DerivedClass();
obj.Show(); // ❌ Calls Base method (not polymorphism)

👉 Because new = method hiding
👉 NOT runtime polymorphism
~~~


🔸 4. abstract.   
👉 Defines a method without implementation (must override).   
~~~CSharp
public abstract class Shape
{
    public abstract void Draw(); // no implementation
}
public class Circle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("Drawing Circle");
    }
}
~~~

🔸 5. sealed
👉 Prevent further overriding

~~~CSharp
public class Base
{
    public virtual void Run() { }
}

public class Child : Base
{
    public sealed override void Run() { }
}
~~~
👉 No further class can override Run().   
🔸 6. Interface-based Polymorphism (Most Used in Real Projects).   
~~~CSharp
public interface INotification
{
    void Send();
}
public class EmailNotification : INotification
{
    public void Send()
    {
        Console.WriteLine("Email sent");
    }
}
public class SmsNotification : INotification
{
    public void Send()
    {
        Console.WriteLine("SMS sent");
    }
}
Usage
INotification notification = new EmailNotification();
notification.Send();
👉 This is real-world polymorphism (used in DI, microservices)

public interface ITicketProcessor
{
    void Process();
}
public class HardwareTicketProcessor : ITicketProcessor
{
    public void Process()
    {
        Console.WriteLine("Hardware processing");
    }
}
public class SoftwareTicketProcessor : ITicketProcessor
{
    public void Process()
    {
        Console.WriteLine("Software processing");
    }
}
👉 At runtime:
ITicketProcessor processor = GetProcessor();
processor.Process();
~~~
✔ No if-else.  
✔ Easily extensible.  
🚨 Common Interview Mistakes. 
❌ Saying:   
“Polymorphism is just overriding”.  
✔ It includes overloading + overriding + interfaces.  
❌ Confusing new with override.  
🎯 Final Interview Answer (Strong).   

👉
“Polymorphism allows objects to behave differently through a common interface. It is achieved via method overloading at compile time and method overriding or interfaces at runtime. Keywords like virtual, override, and abstract enable runtime polymorphism, while ‘new’ is used for method hiding and does not support true polymorphism.”


## Inheritanc?
Inheritance is a mechanism that allows a class (child/derived) to acquire the properties and behaviors of another class (parent/base), enabling code reuse, extensibility, and polymorphic behavior.   

🧠 Simple Meaning.   
👉 “Write once in base class, reuse and extend in derived classes.”     

🔹 Basic Example (Foundation)
~~~CSharp
public class Vehicle
{
    public void Start()
    {
        Console.WriteLine("Vehicle started");
    }
}
public class Car : Vehicle
{
    public void Drive()
    {
        Console.WriteLine("Car is driving");
    }
}
~~~

👉 Car inherits Start() from Vehicle.   

🔥 Why Inheritance is Used (Architect Thinking).  
   
✅ 1. Code Reusability.   
Common logic in base class.    
✅ 2. Extensibility.     
Add new behavior without modifying existing code.   
✅ 3. Polymorphism Support.   
Treat multiple objects uniformly.    
✅ 4. Domain Modeling.    
Represents “is-a” relationship.   
🔹 Real-World Example (Your Domain – Ticketing System).    
~~~CSharp
public class Ticket
{
    public int Id { get; set; }
    public string Issue { get; set; }

    public void Create()
    {
        Console.WriteLine("Ticket created");
    }
}
public class HardwareTicket : Ticket
{
    public string DeviceType { get; set; }
}
public class SoftwareTicket : Ticket
{
    public string ApplicationName { get; set; }
}
~~~~

👉 Both share common behavior.   
👉 Extend with specific fields.   
🔹 Types of Inheritance in C#.  
Type	Supported in C#.      
| Type                 | Supported in C# |
| -------------------- | --------------- |
| Single               | ✅               |
| Multilevel           | ✅               |
| Hierarchical         | ✅               |
| Multiple (class)     | ❌               |
| Multiple (interface) | ✅               |

🔹 Method Overriding (Runtime Polymorphism).  
~~~CSharp
public class Payment
{
    public virtual void Pay()
    {
        Console.WriteLine("Base payment");
    }
}
public class UpiPayment : Payment
{
    public override void Pay()
    {
        Console.WriteLine("UPI payment");
    }
}

🔹 Usage.  
Payment payment = new UpiPayment();   
payment.Pay(); // ✅ Calls derived implementation
~~~

👉 This is polymorphism using inheritance. 

virtual → can be overridden.  
override → change base behavior.  
sealed → prevent further inheritance.  
base → call parent implementation. 
Example.  
~~~CSharp
public class BaseService
{
    public virtual void Execute()
    {
        Console.WriteLine("Base logic");
    }
}
public class CustomService : BaseService
{
    public override void Execute()
    {
        base.Execute(); // reuse base logic
        Console.WriteLine("Extended logic");
    }
}
~~~

👉 Inheritance can lead to tight coupling.  

~~~CSharp
❌ Bad Design:
public class Bird
{
    public virtual void Fly() { }
}

public class Ostrich : Bird
{
    public override void Fly()
    {
        throw new Exception("Cannot fly"); // ❌ Design problem
    }
}
~~~
👉 Violates LSP (Liskov Substitution Principle)

✅ Better: Composition

~~~CSharp

public interface IFlyBehavior
{
    void Fly();
}
public class NoFly : IFlyBehavior
{
    public void Fly()
    {
        Console.WriteLine("Cannot fly");
    }
}
~~~
👉 More flexible than inheritance.  
🔹 2. Inheritance + SOLID Principles.  
LSP → Derived class should not break base behavior.  
OCP → Extend without modifying base class.  
🔹 3. Template Method Pattern.  

~~~CSharp
public abstract class ReportGenerator.  
{
    public void Generate()
    {
        FetchData();
        ProcessData();
        Export();
    }

    protected abstract void FetchData();
    protected abstract void ProcessData();

    protected void Export()
    {
        Console.WriteLine("Exporting report");
    }
}
~~~

👉 Common structure + customizable steps.  
🔹 4. When NOT to Use Inheritance.  
❌ If relationship is not “is-a”.  
❌ If behavior varies too much.  
❌ If it breaks LSP. 
🔥 Encapsulation + Inheritance Together.  
~~~CSharp
public class Account.   
{
    protected decimal Balance; // accessible in child

    public void Deposit(decimal amount)
    {
        Balance += amount;
    }
}
public class SavingsAccount : Account
{
    public void AddInterest()
    {
        Balance += Balance * 0.05m;
    }
}
~~~

👉 Controlled exposure using protected.  
🎯 Final Architect Answer (Short Version).  
👉
“Inheritance is a mechanism to derive new classes from existing ones to promote reuse, extensibility, and polymorphism. However, in modern architecture, it should be used carefully, favoring composition when flexibility and loose coupling are required.”

## Abstraction?
Abstraction is the process of hiding complex implementation details and exposing only the essential features to the user.     

🧠 Simple Meaning.    
👉 “Show what is necessary, hide how it works.”    
🔹 Real-World Example.  
Think about a car 🚗.   
You press accelerator.   
You don’t know:      
how fuel injects.  
how engine works internally.  
👉 You just use it
✔ That is abstraction

🔹 Example in C#

✅ Using Interface (Best Example)
// 🔹 Abstraction: defines WHAT to do
~~~CSharp
public interface IPaymentService
{
    void Pay(decimal amount);
}
// 🔹 Implementation: defines HOW it works
public class UpiPayment : IPaymentService
{
    public void Pay(decimal amount)
    {
        Console.WriteLine("Paid using UPI");
    }
}
public class CreditCardPayment : IPaymentService
{
    public void Pay(decimal amount)
    {
        Console.WriteLine("Paid using Credit Card");
    }
}
🔹 Usage
IPaymentService payment = new UpiPayment();
payment.Pay(1000);
~~~
🔥 Key Point.  
👉 Caller doesn’t know:    
how UPI works.   
how credit card works.  
👉 Only knows:   
✔ Pay() method.   
🔹 Using Abstract Class.  
~~~CSharp
public abstract class Vehicle
{
    // 🔹 Abstract method (no implementation)
    public abstract void Start();

    // 🔹 Concrete method
    public void Stop()
    {
        Console.WriteLine("Vehicle stopped");
    }
}
public class Car : Vehicle
{
    public override void Start()
    {
        Console.WriteLine("Car started with key");
    }
}
~~~
🔥 Why Do We Need Abstraction?     
✅ 1. Reduce Complexity.   
Hide internal logic.   
✅ 2. Improve Flexibility.   
Change implementation without affecting usage.  
✅ 3. Promote Loose Coupling.  
Depend on interface, not implementation.  
✅ 4. Better Maintainability.   
Easy to extend system.   
🔥 Encapsulation vs Abstraction (Must Know). 
Feature	    Encapsulation	    Abstraction.    
Focus	Data hiding	            Hiding complexity. 
Goal	Protect data	Simplify usage.   
Example	private fields	interfaces.  
Question	“Who can access?”	“What to expose?”    


🎯 Architect-Level Example (Real Project) 
~~~CSharp
public interface ITicketService
{
    void CreateTicket(string issue);
}
👉 In your system:
Different countries → different implementations
public class IndiaTicketService : ITicketService
{
    public void CreateTicket(string issue)
    {
        // India-specific logic
    }
}
public class USATicketService : ITicketService
{
    public void CreateTicket(string issue)
    {
        // USA-specific logic
    }
}
👉 Controller only knows:
_ticketService.CreateTicket("POS issue");
~~~
✔ That’s abstraction in real systems
    

## Encapsulation

Encapsulation is the concept of wrapping data (fields) and behavior (methods) together into a single unit (class) and restricting direct access to the internal state of that object.      

n simple terms:  
👉 “Hide the internal details and expose only what is necessary.”  

Why do we need Encapsulation?

**Data Protection**  
    Prevents invalid or unauthorized changes to data.  
**Control Over Data**  
    You can validate before setting values.   
**Loose Coupling**  
    External code doesn’t depend on internal implementation.  
**Maintainability**
    You can change internal logic without breaking other parts.  

How Encapsulation is Achieved in C#.   
Using:    
 1) private, protected, public access modifiers.    
 2) Properties (get, set).    
 3) Methods.

```CSharp
public class BankAccount
{
    private decimal _balance; // Hidden data

    public decimal Balance
    {
        get { return _balance; }
        private set // Restrict external modification
        {
            if (value < 0)
                throw new ArgumentException("Balance cannot be negative");

            _balance = value;
        }
    }

    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Invalid amount");

        Balance += amount;
    }

    public void Withdraw(decimal amount)
    {
        if (amount > Balance)
            throw new InvalidOperationException("Insufficient funds");

        Balance -= amount;
    }
}
```

**Key Interview Points (Very Important)**.   
Encapsulation ≠ just making fields private.   
👉 It is about controlling access + enforcing rules.   
Properties are not just getters/setters.     
👉 They are validation gates.   
Encapsulation helps enforce business rules inside the domain.  

“Encapsulation is the principle of hiding internal state and exposing controlled access through well-defined interfaces to ensure data integrity and maintainability.”

I’ll go step by step with clear real-world examples + comments in code.    

🔹 1. Data Protection.   
👉 Prevent invalid or unauthorized changes.   
❌ Without Encapsulation (Bad Design).   

```CSharp
public class Employee.  
{
    public int Age; // ❌ Anyone can directly modify
}

// Somewhere else in code
var emp = new Employee();
emp.Age = -10; // ❌ Invalid data allowed
```

✅ With Encapsulation (Good Design).   

```CSharp
public class Employee
{
    private int _age; // 🔒 Hidden field (data protection)

    public int Age
    {
        get { return _age; } // 👁 Read access allowed

        set
        {
            // ✅ Validation logic (prevents invalid data)
            if (value < 18)
                throw new ArgumentException("Age must be >= 18");

            _age = value;
        }
    }
}

// Usage
var emp = new Employee();
emp.Age = 25;  // ✅ Valid
// emp.Age = -10; ❌ Exception thrown
```
🔹 2. Control Over Data.   
👉 You control how data is set.   

```CSharp
public class Product
{
    private decimal _price;

    public decimal Price
    {
        get { return _price; }

        set
        {
            // ✅ Control: applying business rule
            if (value <= 0)
                throw new ArgumentException("Price must be greater than 0");

            // ✅ Additional logic (example: logging, rounding, etc.)
            _price = Math.Round(value, 2);
        }
    }
}
```


👉 Here:   
We control input.   
We modify behavior internally.   

🔹 3. Loose Coupling.   
👉 External code should not depend on internal logic.
```CSharp    
public class Order
{
    private List<string> _items = new List<string>(); // 🔒 Internal structure

    // ✅ Only expose behavior, not internal data
    public void AddItem(string item)
    {
        _items.Add(item);
    }

    public int GetItemCount()
    {
        return _items.Count;
    }
}

// Usage
var order = new Order();
order.AddItem("Laptop");
```
// ❌ External code cannot do this:   
// order._items.Clear();  (Not allowed).  
👉 Benefit:    
Later you can change List<string> → HashSet<string>.  
No external code breaks.  

🔹 4. Maintainability.  
👉 Change internal logic without breaking external code.  
```CSharp
public class DiscountService
{
    private decimal _discount;

    public decimal Discount
    {
        get { return _discount; }

        set
        {
            // Today logic
            _discount = value;
        }
    }
}
👉 Later you change logic:
set
{
    // ✅ Changed internal logic
    if (value > 50)
        throw new ArgumentException("Max discount is 50%");

    _discount = value;
}
👉 External code still:
service.Discount = 30; // No change needed
```

🔹 5. How Encapsulation is Achieved in C#.   

✅ Access Modifiers.   

```CSharp
public class User
{
    private string _password; // 🔒 Only accessible inside class

    protected string Role; // 🔐 Accessible in derived classes

    public string Username; // 🌍 Accessible everywhere
}
✅ Properties
public class Account
{
    private decimal _balance;

    public decimal Balance
    {
        get { return _balance; } // Read

        private set // 🔒 Only class can modify
        {
            _balance = value;
        }
    }
}
✅ Methods (Best Practice)
public class BankAccount
{
    private decimal _balance;

    // ✅ Only way to modify balance
    public void Deposit(decimal amount)
    {
        if (amount <= 0)
            throw new ArgumentException("Invalid amount");

        _balance += amount;
    }

    public decimal GetBalance()
    {
        return _balance;
    }
}
```
👉 This is true encapsulation.  
👉 Not just properties, but behavior control.  
🔥 Architect-Level Insight (Important).  
👉 Encapsulation is not about getters/setters.  
It is about:   
✔ Protecting invariants.  
✔ Enforcing business rules.  
✔ Hiding complexity.  
✔ Exposing only required behavior.  
🚨 Common Mistake (Interview Trap).  
```CSharp
public class Customer
{
    public string Name { get; set; } // ❌ Not true encapsulation
}
```

👉 Why?  
No validation. 
No control.     
✔ Better:   

```CSharp
public string Name
{
    get => _name;
    set
    {
        if (string.IsNullOrWhiteSpace(value))
            throw new Exception("Invalid name");

        _name = value;
    }
}
```




🔹 1. Immutable Objects.  
👉 Definition:   
An object whose state cannot be changed after creation.   
✅ Example.      

```CSharp
public class User
{
    public string Name { get; }
    public int Age { get; }

    // ✅ Values are set only once (constructor)
    public User(string name, int age)
    {
        if (age < 18)
            throw new ArgumentException("Invalid age");

        Name = name;
        Age = age;
    }
}
```
🔥 Why this is powerful.  
Thread-safe (no race conditions).  
Predictable behavior.  
No accidental modification.  
👉 Architect Answer:
“Immutable objects enforce encapsulation by preventing state mutation after initialization.”   

🔹 2. Read-only Properties (init, private set).   
✅ Using private set.    
```CSharp
public class Account
{
    public decimal Balance { get; private set; } // 🔒 Only class can modify

    public void Deposit(decimal amount)
    {
        Balance += amount;
    }
}
```
👉 External code cannot modify Balance directly.    
✅ Using init (C# 9+).   
```CSharp
public class Customer
{
    public string Name { get; init; } // ✅ Set only during object creation
}

// Usage
var customer = new Customer { Name = "Subramanyam" };
```

// ❌ customer.Name = "New Name"; (Not allowed after creation).    
🔥 Key Difference
Feature 	            private set 	init.  
Modify inside class	            ✅	    ❌.   
Modify during creation	        ✅	    ✅.  
Immutable style	                ❌      	✅.    

🔹 3. Encapsulation in Domain-Driven Design (DDD).    
👉 This is very important for architect interviews.  
❌ Anemic Model (Bad).   

```CSharp
public class Order 
{
    public decimal Total { get; set; }
}
```
👉 No rules, no behavior
✅ Rich Domain Model (Encapsulation)
```CSharp
public class Order
{
    private readonly List<string> _items = new();
    public IReadOnlyList<string> Items => _items.AsReadOnly(); // 🔒 Safe exposure

    public decimal Total { get; private set; }

    public void AddItem(string item, decimal price)
    {
        if (string.IsNullOrEmpty(item))
            throw new ArgumentException("Invalid item");

        _items.Add(item);
        Total += price;
    }
}
```
🔥 Architect Insight.   
👉 “In DDD, encapsulation ensures business rules live inside the domain, not outside services.”   
🔹 4. Encapsulation vs Abstraction (🔥 VERY IMPORTANT).   
✅ Encapsulation.  
👉 How data is protected.  
Hides internal state.  
Uses access modifiers.  
Focus: data safety. 
✅ Abstraction.  
👉 What is exposed to the outside.  
Hides complexity.  
Shows only necessary features.  
Focus: design simplicity.  
🔥 Example.    
```CSharp
// Abstraction 
public interface IPaymentService
{
    void Pay(decimal amount);
}

// Encapsulation
public class PaymentService : IPaymentService
{
    private decimal _balance; // 🔒 hidden

    public void Pay(decimal amount)
    {
        if (amount <= 0)
            throw new Exception("Invalid");

        _balance -= amount;
    }
}
```
🎯 Interview One-liner
👉
Encapsulation = hiding data.  
Abstraction = hiding complexity.  
🔹 5. Encapsulation using Interfaces.  
👉 Interfaces help expose only required behavior.  
Example
```CSharp
public interface IBankAccount
{
    void Deposit(decimal amount);
    decimal GetBalance();
}
public class BankAccount : IBankAccount
{
    private decimal _balance; // 🔒 hidden

    public void Deposit(decimal amount)
    {
        _balance += amount;
    }

    public decimal GetBalance()
    {
        return _balance;
    }
}
```
🔥 Benefit
External code cannot access internal fields
Only interacts through contract
🔹 6. Encapsulation in APIs (DTO vs Domain Model)
👉 Very common in real projects (your ticketing system also)
❌ Problem (No Encapsulation)
```CSharp
public class User
{
    public string Password { get; set; } // ❌ exposed
}
✅ Proper Design
🔹 Domain Model (Encapsulated)
public class User
{
    private string _password;

    public string Username { get; private set; }

    public void SetPassword(string password)
    {
        if (password.Length < 6)
            throw new Exception("Weak password");

        _password = password;
    }
}

🔹 DTO (Data Transfer Object)
public class UserDto
{
    public string Username { get; set; }
    public string Password { get; set; }
}
```
🔥 Flow.    
Controller → DTO → Domain Model.  
👉 Domain enforces rules.  
👉 DTO only transfers data. 
🔥 Architect Insight.   
“DTOs expose data, domain models protect invariants using encapsulation.”   
🧠 Final Summary (Interview Ready).   
Encapsulation is foundation of clean architecture.   
Used in:    
Domain models.   
APIs.   
Microservices.   
Prevents:    
Invalid state.   
Tight coupling.  
Security issues.   

### Abstraction vs Encapsulation (Straight Differences)

| Aspect                 | Abstraction                                                    | Encapsulation                                    |
| ---------------------- | -------------------------------------------------------------- | ------------------------------------------------ |
| **Definition**         | Hides implementation details and shows only essential features | Hides internal data and restricts direct access  |
| **Focus**              | *What to expose*                                               | *How to protect data*                            |
| **Goal**               | Reduce complexity for the user                                 | Ensure data integrity and control                |
| **Level**              | Design level (high-level view)                                 | Implementation level (low-level control)         |
| **Achieved Using**     | Interfaces, abstract classes                                   | Access modifiers, properties, methods            |
| **Hides**              | Complexity of logic                                            | Internal state (data)                            |
| **User Perspective**   | User sees only available operations                            | User cannot directly modify internal data        |
| **Example Question**   | “What operations are available?”                               | “Who can access or modify data?”                 |
| **Real-world Example** | Car driving (you use, don’t know engine)                       | Bank account (you can’t directly change balance) |
| **Dependency**         | Promotes loose coupling                                        | Promotes controlled access                       |

Strong Interview One-Liner.   
👉
“Abstraction focuses on hiding complexity by exposing only required behavior, while encapsulation focuses on protecting data by restricting direct access and enforcing rules.”    
 
🔥 Bonus (What impresses interviewer).  
👉
Abstraction = Design decision.  
Encapsulation = Data protection mechanism.  




## 1. GC

Garbage Collector (GC) in C# is an automatic memory management system that:  
    - Allocates memory for objects on the managed heap.   
    - Tracks which objects are still in use.    
    - Automatically reclaims memory from objects that are no longer referenced.   

A runtime service that balances developer productivity, application safety, and system performance—at the cost of non-deterministic execution.

Why GC Exists.   
Without GC:    
    - Developers must manually allocate/free memory   
    - Memory leaks, dangling pointers, crashes are common.    
    - Development speed drops.    
With GC:    
    ✔ Faster development.  
    ✔ Memory safety.  
    ✔ Fewer production crashes.  
    ❌ Less control over when memory is freed.   

➡ Architecture trade-off: safety & productivity vs predictability.   

Object lifecycle:  
    Object created → allocated on Managed Heap.    
    Object used by application.   
    No references remain → object becomes eligible for GC.  
    GC reclaims memory later (not immediately).   

You cannot predict when GC runs


Generational Garbage Collection (Critical Concept).   
.NET GC is Generational, based on this principle:  
        **Most objects die young.**   
Generations:    
Gen 0	Short-lived objects (requests, DTOs, temp objects).   
Gen 1	Medium-lived objects.     
Gen 2	Long-lived objects (singletons, caches).     
LOH	Large objects (> 85 KB).     

Why this matters architecturally:    
    GC focuses mostly on Gen 0 → fast.  
    Gen 2 collections are expensive.    
    Poor object lifetime design causes performance degradation.  

GC vs IDisposable (Very Important).  
GC does NOT manage:   
File handles.  
Database connections.   
Network sockets.   
OS resources         

## 2. SOLID
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

### S stands for Single Responsibility Principle

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

## Strategy Pattern

The Strategy design pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. 
Strategy lets the algorithm vary independently from clients that use it.

**In plain English:**  
    You have multiple ways to do one task  
    You want to choose the behavior dynamically  
    You don’t want if-else or switch blocks  

**Strategy Pattern Structure (Conceptual)**  
3 Main Parts:  
    Strategy Interface – defines the contract  
    Concrete Strategies – actual implementations  
    Context – uses a strategy      


```CSharp
// Step 1: Strategy Interface

   public interface IFeeCalculationStrategy
   {
       decimal CalculateFee(decimal amount);
   }

// Step 2: Concrete Strategies

    public class PremiumMerchantFeeStrategy : IFeeCalculationStrategy
    {
        public decimal CalculateFee(decimal amount)
        {
            return amount * 0.01m;
        }
    }

    public class StandardMerchantFeeStrategy : IFeeCalculationStrategy
    {
        public decimal CalculateFee(decimal amount)
        {
            return amount * 0.02m;
        }
        }
    public class EnterpriseMerchantFeeStrategy : IFeeCalculationStrategy
    {
        public decimal CalculateFee(decimal amount)
        {
            return 0;
        }
    }

// Step 3: Strategy Resolver (Context)
public class FeeCalculator
{
    private readonly IFeeCalculationStrategy _strategy;

    public FeeCalculator(IFeeCalculationStrategy strategy)
    {
        _strategy = strategy;
    }

    public decimal Calculate(decimal amount)
    {
        return _strategy.CalculateFee(amount);
    }
}


// Step 4: Controller Usage (Runtime Selection)
[HttpGet("calculate-fee")]
public IActionResult CalculateFee(string merchantType, decimal amount)
{
    IFeeCalculationStrategy strategy = merchantType switch
    {
        "Premium" => new PremiumMerchantFeeStrategy(),
        "Standard" => new StandardMerchantFeeStrategy(),
        "Enterprise" => new EnterpriseMerchantFeeStrategy(),
        _ => throw new Exception("Invalid merchant type")
    };

    var calculator = new FeeCalculator(strategy);
    var fee = calculator.Calculate(amount);

    return Ok(fee);
}

```
“I use the Strategy pattern when I have multiple implementations of the same business rule.
In EPOS, it helps me isolate merchant-specific logic, remove conditional complexity, and follow the Open–Closed Principle.
It improves maintainability, testability, and extensibility.”


## Factory Design Pattern?
Factory Pattern encapsulates object creation logic and returns objects based on input, without exposing the creation details to the caller.

In simple words:  
    You ask for an object.  
    You don’t care how it is created.   
    The factory decides which concrete object to give you.   
    👉 Focus: object creation, not behavior.  

Problem Without Factory (Common EPOS Mistake)
Imagine EPOS code like this:

```CSharp
if (merchantGroup == "Retailer")
    terminalService = new RetailerTerminalService();
else if (merchantGroup == "Chain")
    terminalService = new ChainTerminalService();
else if (merchantGroup == "Wholesaler")
    terminalService = new WholesalerTerminalService();
```    

Why this is bad:   
    - Creation logic spread everywhere.   
    - Every new merchantGroup = code change in many places.   
    - Violates Single Responsibility Principle.   
    - Hard to test.   

Three Key Parts:  
    - Common Interface.   
    - Concrete Implementations.   
    - Factory Class.   

```CSharp
// Step 1: Common Interface

    public interface ITerminalService
    {
        void CreateTerminal(string transactionId);
    }

// Step 2: Concrete Implementations    

    public class RetailerTerminalService : ITerminalService
    {
        public void CreateTerminal(CreateTerminalDTO model)
        {
            // Retailer-specific logic
        }
    }

    public class ChainTerminalService : ITerminalService
    {
        public void CreateTerminal(CreateTerminalDTO model)
        {
            // Chain-specific logic
        }
    }

    public class WholesalerTerminalService : ITerminalService
    {
        public void CreateTerminal(CreateTerminalDTO model)
        {
            // Wholesaler-specific logic
        }
    }

// Step 3: Factory Class    

    public static class TerminalFactory
    {
        public static ITerminalService Create(string terminalType)
        {
            return terminalType switch
            {
                "Retailer" => new RetailerTerminalService(),
                "Chain"     => new ChainTerminalService(),
                "Wholesaler" => new WholesalerTerminalService(),
                _ => throw new Exception("Unsupported terminal type")
            };
        }
    }

// Step 4: Controller Usage (Clean & Professional)

    [HttpPost("process")]
    public IActionResult Process(CreateTerminalDTO model)
    {
        var type = TerminalFactory.Create(model.terminalType);
        type.CreateTerminal(model);

        return Ok();
    }

```    

✅ Why This Is Senior-Level.   
    - Controller has zero creation logic.   
    - Easy to add new terminal types.     
    - Object creation centralized.  
    - Very testable. 


Factory vs Strategy (INTERVIEW GOLD ⭐)

Factory Pattern.   
    - Creates objects.   
    - Decides which class to instantiate.   
    - Focus: object creation.  


Strategy Pattern   
    - Executes behavior.     
    - Decides which algorithm to run.  
    - Focus: business logic. 

Factory creates the strategy
Strategy executes the logic      


Use Factory when:  
    - Object creation depends on input.   
    - Multiple implementations exist.   
    - You want to hide new keyword usage.   
    - Creation logic is complex or repeated.   
Avoid Factory when:    
    - Only one implementation.   
    - Object creation is trivial.           

## What Is the Abstract Factory Pattern?
Abstract Factory Pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes.

In simple words:  
    - Factory → creates one type of object.   
    - Abstract Factory → creates a group (family) of related objects.  
    - The client does not know which concrete classes are used.   

Factory = one product.   
Abstract Factory = family of products.

Why Do We Need Abstract Factory?  
    Because sometimes one decision affects multiple objects.

If you choose:  
    - Terminal Type = Android.   
    - Then you must also use:  
    - Android printer.   
    - Android receipt formatter.   
    - Android sync service.   
These objects must be consistent with each other.

EPOS Scenario: Terminal Ecosystem.   
Your EPOS system supports:   
    Android Terminal.   
    POS Hardware Terminal.   
    Web Terminal.   

Each terminal type requires a set of related components:
| Component         | Android          | POS          | Web          |
| ----------------- | ---------------- | ------------ | ------------ |
| Receipt Printer   | AndroidPrinter   | PosPrinter   | WebPrinter   |
| Receipt Formatter | AndroidFormatter | PosFormatter | WebFormatter |
| Sync Service      | AndroidSync      | PosSync      | WebSync      |

❌ Problem Without Abstract Factory


```CSharp
var printer = new AndroidPrinter();
var formatter = new PosFormatter();   // ❌ mismatch
var sync = new WebSync();              // ❌ mismatch

```

This creates runtime bugs.    
4️⃣ Abstract Factory Structure.   
4 Core Parts:    
    Abstract Factory Interface.   
    Concrete Factories.  
    Abstract Products.   
    Concrete Products.   

```CSharp
// Step 1️⃣ Abstract Products (Interfaces)
public interface IReceiptPrinter
{
    void Print(string content);
}

public interface IReceiptFormatter
{
    string Format(string data);
}

public interface ISyncService
{
    void Sync();
}
// Step 2️⃣ Abstract Factory Interface
public interface ITerminalFactory
{
    IReceiptPrinter CreatePrinter();
    IReceiptFormatter CreateFormatter();
    ISyncService CreateSyncService();
}
// Step 3️⃣ Concrete Factory – Android Terminal
public class AndroidTerminalFactory : ITerminalFactory
{
    public IReceiptPrinter CreatePrinter()
        => new AndroidReceiptPrinter();

    public IReceiptFormatter CreateFormatter()
        => new AndroidReceiptFormatter();

    public ISyncService CreateSyncService()
        => new AndroidSyncService();
}
// Step 4️⃣ Concrete Factory – POS Terminal
public class PosTerminalFactory : ITerminalFactory
{
    public IReceiptPrinter CreatePrinter()
        => new PosReceiptPrinter();

    public IReceiptFormatter CreateFormatter()
        => new PosReceiptFormatter();

    public ISyncService CreateSyncService()
        => new PosSyncService();
}
// Step 5️⃣ Client Code (Controller / Service)
public class ReceiptService
{
    private readonly IReceiptPrinter _printer;
    private readonly IReceiptFormatter _formatter;
    private readonly ISyncService _sync;

    public ReceiptService(ITerminalFactory factory)
    {
        _printer = factory.CreatePrinter();
        _formatter = factory.CreateFormatter();
        _sync = factory.CreateSyncService();
    }
}
```
✔ Client never knows:  
    Which printer.   
    Which formatter.    
    Which sync service.    

✔ Consistency guaranteed

6️⃣ Factory vs Abstract Factory (Clear Comparison).   
| Pattern            | What it creates           |
| ------------------ | ------------------------- |
| Factory            | One object                |
| Abstract Factory   | Family of related objects |
| Strategy           | Behavior                  |
| Factory + Strategy | Creation + behavior       |
| Abstract Factory   | Creation + consistency    |


7️⃣ When to Use Abstract Factory (Interview Checklist).   
Use it when:    
    - You must create multiple related objects.   
    - Objects must be compatible.   
    - System supports multiple platforms / environments.   
    - You want to switch entire families at runtime
Avoid it when:   
    - Only one object needed.       
    - No dependency between objects.   
    - Simpler Factory is enough     


“Abstract Factory is used when the system needs to create families of related objects.
In EPOS, it helps ensure terminal-specific components like printers, formatters, and sync services are created consistently without leaking implementation details.”
That answer alone = senior-level clarity.

## Builder Pattern
Builder Pattern separates the construction of a complex object from its representation, allowing you to build it step by step.

```CSharp
// Step 1️⃣ Final Object (What We Want at the End)
public class TransactionReport
{
    public string Merchant { get; set; }
    public string Terminal { get; set; }
    public decimal Amount { get; set; }
    public decimal? Gst { get; set; }
    public decimal? Discount { get; set; }
    public bool SettlementIncluded { get; set; }
    public bool SignatureIncluded { get; set; }
}

// This class is simple.
// Builder is NOT here yet.

// Step 2️⃣ Builder (This Is the Key Part)
// Builder does NOT represent the report.
// Builder knows how to construct it.

public class TransactionReportBuilder
{
    private TransactionReport _report = new();

    public TransactionReportBuilder AddMerchant(string merchant)
    {
        _report.Merchant = merchant;
        return this;
    }

    public TransactionReportBuilder AddTerminal(string terminal)
    {
        _report.Terminal = terminal;
        return this;
    }

    public TransactionReportBuilder AddAmount(decimal amount)
    {
        _report.Amount = amount;
        return this;
    }

    public TransactionReportBuilder AddGst(decimal gst)
    {
        _report.Gst = gst;
        return this;
    }

    public TransactionReportBuilder AddDiscount(decimal discount)
    {
        _report.Discount = discount;
        return this;
    }

    public TransactionReportBuilder IncludeSettlement()
    {
        _report.SettlementIncluded = true;
        return this;
    }

    public TransactionReportBuilder IncludeSignature()
    {
        _report.SignatureIncluded = true;
        return this;
    }

    public TransactionReport Build()
    {
        return _report;
    }
}

//Important
//Each method sets ONE thing
//Each method returns this
//Build() gives the final object

// Step 3️⃣ Using Builder

var report = new TransactionReportBuilder()
    .AddMerchant("ABC Store")
    .AddTerminal("T001")
    .AddAmount(1000)
    .Build();

// Case 2: Full Report
var report = new TransactionReportBuilder()
    .AddMerchant("ABC Store")
    .AddTerminal("T001")
    .AddAmount(1000)
    .AddGst(50)
    .AddDiscount(20)
    .IncludeSettlement()
    .IncludeSignature()
    .Build();
// 👉 Same object
// 👉 Different structure
// 👉 No confusion

```
“I use the Builder pattern when creating complex EPOS objects like reports, where many fields are optional.
It improves readability, avoids constructor overloads, and makes object creation safer.”


## Observer Pattern
The core problem:  
One change happens, and many other parts of the system must react to it.  
But:  
They should not be tightly coupled.   
The main object should not know who is listening.   

❌ Bad Design (What Many Systems Do)
```CSharp
UpdateTicket()
{
    UpdateTicketTable();
    UpdateHistory();
    SendEmail();
    UpdateSla();
}
```
Why this is bad:  
    Ticket service knows too much.   
    If tomorrow you add:  
            Push notification
            Audit logging
            You must modify this method again
Violates Open–Closed Principle
Hard to test
This is tight coupling.

Observer Pattern Fix (Correct Design).  
Key Idea:    
Ticket service should only say:  
“Ticket is updated.”    
It should NOT care what happens next.   
Other parts of the system will listen and react.

| Your Scenario  | Observer Pattern Role |
| -------------- | --------------------- |
| Ticket updated | **Event / Subject**   |
| Update history | **Observer**          |
| Send email     | **Observer**          |
| Update SLA     | **Observer**          |

```CSharp
// Step 1️⃣ Observer Interface
public interface ITicketObserver
{
    void OnTicketUpdated(int ticketId);
}


// Step 2️⃣ Observers (Your Exact Requirements)
// 🔹 History Observer
public class TicketHistoryObserver : ITicketObserver
{
    public void OnTicketUpdated(int ticketId)
    {
        // Insert into ticket history table
    }
}
// Email Observer
public class TicketEmailObserver : ITicketObserver
{
    public void OnTicketUpdated(int ticketId)
    {
        // Send email notification
    }
}
//🔹 SLA Observer
public class TicketSlaObserver : ITicketObserver
{
    public void OnTicketUpdated(int ticketId)
    {
        // Recalculate SLA
    }
}

// Step 3️⃣ Subject (Ticket Service)

public class TicketService
{
    private readonly IEnumerable<ITicketObserver> _observers;

    public TicketService(IEnumerable<ITicketObserver> observers)
    {
        _observers = observers;
    }

    public void UpdateTicket(int ticketId)
    {
        // Update ticket data in DB

        NotifyObservers(ticketId);
    }

    private void NotifyObservers(int ticketId)
    {
        foreach (var observer in _observers)
        {
            observer.OnTicketUpdated(ticketId);
        }
    }
}


// program.cs
builder.Services.AddScoped<TicketService>();

builder.Services.AddScoped<ITicketObserver, TicketHistoryObserver>();
builder.Services.AddScoped<ITicketObserver, TicketEmailObserver>();
builder.Services.AddScoped<ITicketObserver, TicketSlaObserver>();

// Controller
[ApiController]
[Route("api/tickets")]
public class TicketController : ControllerBase
{
    private readonly TicketService _ticketService;

    public TicketController(TicketService ticketService)
    {
        _ticketService = ticketService;
    }

    [HttpPut("{ticketId}")]
    public async Task<IActionResult> UpdateTicket(int ticketId)
    {
        await _ticketService.UpdateTicketAsync(ticketId);
        return Ok("Ticket updated successfully");
    }
}




```
Each class:  
Does one job.  
Is independent.  
Can be tested separately. 

“When a ticket is updated, multiple actions like history update, email notification, and SLA recalculation must happen.
I use the Observer pattern so these actions remain decoupled from the ticket update logic and can evolve independently.”

## Explain about Async and Await?  

**Async** - Keyword that marks a method to run asynchronously (does not block UI/thread).  
**Await** - Keyword that pauses execution until an asynchronous task completes



