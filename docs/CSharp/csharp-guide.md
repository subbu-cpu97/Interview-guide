# C# – Basics

## 1. Explain about Async and Await?

**Async** - Keyword that marks a method to run asynchronously (does not block UI/thread).  
**Await** - Keyword that pauses execution until an asynchronous task completes

**Key benefits:**

- Code reusability  
- Shared base behavior across multiple derived classes  
- Ability to override base methods in derived classes  

**Example – Base and Derived Class**

```csharp
public class ApiService
{
    public virtual void MakeRequest()
    {
        Console.WriteLine("Making API request");
    }
}

public class UserService : ApiService
{
    public override void MakeRequest()
    {
        Console.WriteLine("Making User API request");
    }
}
