# Rules

With respect to rules, our expectation is that they would be the same in Ystari as they are in CSLA. That is, we would have authorization and validation rules that are triggered by object usage or property value changes. One change would be that in Ystari, all rules would be registered via a static method attributes with `[Rules]`, similar to the `[Registrations]` attribute used for [operations](operations.md). Note that in CSLA, the `AddAuthorizationRules()` and `AddBusinessRules()` methods are only called once per type, but the former is a static method, while the latter is an instance method that the developer overrides. By having one rules method, it potentially simplifies rule additions, though Ystari would need to handle rules correctly in inheritance scenarios. For example, consider the following case:

```
public class Person
    : DomainBase<Person>
{
    [Rules]
    public static void AddRules(...) { ... }
}

public class Employee
    : Person
{
    [Rules]
    public static void AddMoreRules(...) { ... }
}
```

Ystari would have to walk up the inheritance tree and call the rules methods for any types it hasn't seen yet. For example, let's say an app creates an instance of `Person` first. Ystari finds the `[Rules]` method on `Person`, and adds the rules. Its base class is `DomainBase`, which wouldn't register rules, nor would any of its base classes add rules, so the search terminates. Now, the app creates an `Employee` instance. It finds `AddMoreRules()`, and add those rules. Its base class is `Person`, but we've already done that one, so we stop. If `Employee` was creates first, the rules for `Employee` and then `Person` would be added. If an instance of `Person` was created, Ystari would see that its rules were already added, so no rule registration work is needed.

One idea is to provide a fluent interface to add rules to an object without having to create a type. It may look something like this:

```
// CalculatedB = ValueB * (PercentageB / 100)
// CalculatedB is optional
BusinessRules.AddRule(
    BusinessRuleExtensions.NewBusinessRule(CalculatedBProperty)
    .DependsOn(ValueBProperty, IsRequired: false)
    .DependsOn(PercentageBProperty, IsRequired: false)
    .Calculate((v1, v2) =>
    {

        if (!v1.HasValue || !v2.HasValue)
        {
            return null;
        }
        return v1.Value * (v2.Value / 100);
    }));
```

[Here's](https://github.com/keithdv/FluentBusinessRule) a POC of this idea in CSLA.