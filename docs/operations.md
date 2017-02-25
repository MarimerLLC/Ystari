# Operations

In CSLA, there are a number of operations that developers can implement in a domain object's lifetime:

* `Create`
* `Fetch`
* `Insert`
* `Update`
* `Delete`
* `Execute` (for commands)

These are implemented by adding a method to a class definition with a well-known name. For example, if you wanted a class to handle the `Fetch` operation for a root object, you would create a method called `DataPortal_Fetch`.

There are a couple of issues with this approach:

* It requires the developer to name the method exactly correct.  If you're off by one character, the method will never be found, and you won't find out about it until you execute the code (an analyzer wouldn't help here either).
* CSLA has to use Reflection to find the method and invoke it, which carries some overhead. Granted, CSLA is more efficient than this in that it caches a generated expression when the operation method is found the first time for a given type - subsequent calls to that operation method will avoid the Reflection overhead. However, it still cannot be avoided entirely.

We are currently investigating a couple of approaches to use in Ystari (we'll talk about how dependencies would be injected during the operation in the [dependencies](dependencies.md) section):

* Interface implementations
* Function registration  

With the interface implementation approach, the developer would need to implement a specific operation interface. Here's an example of what that would look like:

```
public interface IHandleRootFetch
{
    Task FetchAsync();
}

public interface IHandleRootFetch<T>
{
    Task FetchAsync(T criteria)
}
```

These are just the interfaces for a domain object to implement a `Fetch`, either with a criteria value or no criteria. Note that `params` would not be used to specify multiple criteria values. A developer would need to provide an object with multiple values. Note that the C#7 tuple is a generic type that can be used to pass over as many values as necessary:

```
public class Customer
    : DomainBase, IHandleRootFetch<(int, string)>
{
    public Task FetchAsync((int, string) criteria) { ... }    
}
```

Another idea is function registration. With this approach, the developer would declare one static method on their class, probably attributed with something like a [RegistrationsAttribute]. It would take an object that would allow the developer to specify which methods would handle Ystari operations:

```
public class Customer
    : DomainBase
{
    [Registrations]
    public static void AddRegistrations<Customer>(IRegistrations registrations)
    {
        registrations.RegisterFetch<(string, int)>(o => o.FetchAsync);
    }    

    private Task FetchAsync((int, string) criteria) { ... }
}
```

In either case, Ystari would not need to rely heavily on Reflection to invoke the right operation. With interfaces, it would know which operation was requested (and criteria value, if any was provided). It would then do an `as` cast on the object to see if it implemented the correct operation interface. If so, it would call the method directly. With registrations, it would know if a method was registered for the requested operation (and criteria value, if any was provided). If it was registered, it would invoke it.

Both have their advantages and disadvantages. At this point, we are leaning towards function registration. We've already done a couple of POCs - here's [one](https://github.com/keithdv/FunctionalMethodInjection2).