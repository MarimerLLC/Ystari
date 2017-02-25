# Dependency Injection

Dependency injection, or DI, is an approach to software development where code doesn't instantiate certain objects; rather, they are provided, or injected, into the code. In CSLA, there are ways to do a form of DI via property injection (click [here](https://magenic.com/thinking/abstractions-in-csla) for details), but, without getting into technical details here, this approach doesn't handle a couple of edge cases. Arguably, the real issue is that CSLA doesn't really support DI/IoC as a core concept. We will change this with Ystari.

## Operations and Method Injection

The most common case of dependency usage in CSLA is to obtain a connection to a database or use a REST API when an object is fetched or saved. This happens on the "server" side of the domain object. In Ystari, we plan to allow developers to define their [operation method](operations.md) with a second parameter that specifies all the dependencies they need. In C#7, this can be stated with a tuple.

Let's give a specific example:

```
public class Customer
    : DomainBase
{
    [Registrations]
    public static void AddRegistrations<Customer>(IRegistrations registrations)
    {
        registrations.RegisterFetch<(string, int), IDbConnection>(o => o.FetchAsync);
    }    

    private Task FetchAsync((int, string) criteria, IDbConnection connection) { ... }
}
```

`IRegistrations` is an object that Ystari will provide to the method marked with `[RegistrationsAttribute]`. It will call this method only once for the lifetime of the application, and it will be invoked the first time an instance needs to handle a Ystari operation.

If the developer didn't need any dependencies in `FetchAsync()`, they'd call `RegisterFetch<C>()`, which would just specify the criteria. By calling `RegisterFetch<C, D>()`, Ystari is now responsible for passing in whatever dependencies are needed. If it's just one type, like `IDbConnection`, then Ystari would default to calling the IoC container in use to resolve it. If it's a tuple type, like this:

```
registrations.RegisterFetch<(string, int), (IDbConnection, IService)>(o => o.FetchAsync);

// ...

private Task FetchAsync((int, string) criteria, (IDbConnection, IService) dependencies) { ... }
```

then Ystari will resolve each dependency that the tuple specifies, and then will pass that tuple into the operation.

## Client and Mobile Dependencies

While the majority of DI needs happens on the server, there are cases where a domain object will only need a dependency on the client side only. For example, if you have a parent-child relationship where you could have multiple children (think `Order` with `OrderItems` that contains `Order` objects), you need to add child objects in the client. To do this, you'll need a reference to something like `IDomainFactory<Order>` to call `CreateAsync()` in the client.

Also, there are also cases where a dependency is mobile. In the previous case, we said that to create child objects you needed a client dependency. But it's also possible that the ability to add child objects needs to happen on the server as well. There are other cases where you need to reference the identity both on the client and the server. Typically, DI is location bound - that is, the dependency is never moved past the "host" in which it was resolved (which is typically an executable) and the object receiving the dependency is never serialized.

We think there are a couple of ways to handle this scenario:

* If the dependency itself is serializable and that version of the dependency can live on both the client and server, then the dependency can be injected either on the server or client, set to a field reference, and then passed back and forth via normal serialization machinery.
* If the dependency is not serializable but needs to exist on both the client and server, then the server-side version can be used in the operation methods, and the client-side version can be set after deserialization occurs, or the server-side operation is done (which would be the case if there were only logical client and server tiers in play). We're thinking of having some kind of "landing" and "takeoff" methods that the object could override or define (similar to the functional registration approached discussed in the [operations document](operations.md)). This would allow the developer to re-establish dependencies on both client and server sides where the dependency implementation is different. For example, it would look something like this:

```
public class Customer
    : DomainBase
{
    [Registrations]
    public static void AddRegistrations<Customer>(IRegistrations registrations)
    {
        registrations.RegisterLanding<IDomainFactory<IAddress>(o => o.HandleLanding);
    }    

    private void HandleLanding(IDomainFactory<IAddress> address) { ... }
}
```

The "landing" method would be called whenever the object is deserialized but before the object is given to the caller. Multiple dependencies can be requested with a tuple type.