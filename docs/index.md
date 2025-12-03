# What is Inheritance?

Inheritance is an Object-Oriented Programming (OOP) feature that allows a child class to acquire the properties and behaviors of a parent class.

## Key Benefits
- Promotes **code reusability**
- Supports **hierarchical relationships**
- Allows **method overriding** for specific behaviors in derived classes
- Reduces code duplication and improves maintainability

## Real-Time Usage (My Project Example)
I implemented inheritance to create a **base service class for HTTP requests**, which helped reuse common logic in multiple API service classes.

For example:

```csharp
// Base Class
public class ApiService
{
    public virtual void MakeRequest()
    {
        Console.WriteLine("Making API request");
    }
}

// Derived Class
public class UserService : ApiService
{
    public override void MakeRequest()
    {
        Console.WriteLine("Making User API request");
    }
}
