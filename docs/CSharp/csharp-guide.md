# C# – Basics

## 1. What is Inheritance in C#?

Inheritance is an OOP feature that allows a **child class** to acquire the properties and behaviors of a **parent class**.

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
