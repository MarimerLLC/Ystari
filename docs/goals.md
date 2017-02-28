# Ystari Design Goals

Before we start to list out all of the goals, let's spend a bit of time going through the history of CSLA as it will frame the conversation for the motivation to create Ystari in the first place.

## Background

CSLA is a business object framework that was created (originally as source code to accompany Rocky's VB book) in the late 1990s. As .NET came to fruition, the framework was ported to that runtime around 2003. Since that time, CSLA has had numerous features added, bugs fixed, and ended up targeting a number of runtimes and hosts than just WinForms applications written using the full .NET framework. It's also had over 55,000 downloads on NuGet (https://www.nuget.org/packages/CSLA-Core/). 

However, while it is still a viable framework to use in .NET business applications, there are also some limitations to the framework:

* It has no notion of handling dependencies. There are ways to make DI work using `IDataPortalActivator` (go [here](https://magenic.com/thinking/abstractions-in-csla) for details), but they're somewhat cumbersome, and don't embrace the notion that business objects are mobile. Meaning, if you inject dependencies into a business object and that object moves to a different host, what happens to those dependencies?
* It uses a number of statics which can make unit testing cumbersome. While there is nothing fundamentally wrong with static members (e.g. extension methods are wonderful), if statics are used as a global "context" container (like `ApplicationContext`), it makes isolation in unit tests impossible.
* Given CSLA's long history, it's inevitably picked up some "cruft" over the years. Hosts and frameworks come and go with support (like Remoting, Windows Workflow and Silverlight), but over the years that has required some interesting gyrations in the code base. Sometimes a reboot can provide a fresh start.
* While the fundamental notions of business objects and domain modeling are still solid, the technical landscape has arguably changed a lot between 2003 and 2017. Modern application development techniques should be considered when creating a framework to help developers build their applications.

That's where Ystari comes in. We want to use concepts and implementations that work in CSLA, but approach it with a fresh start. If there are ways to make Ystari better than CSLA, they should be used!

## Design Goals

* [Build Targets](build_targets.md)
* [Stereotypes](stereotypes.md)
* [Asynchronous](asynchronous.md)
* [Operations](operations.md)
* [Serialization](serialization.md)
* [DI/IoC](dependency_injection.md)
* [Properties](properties.md)
* [Rules](rules.md)
* [Pipeline](pipeline.md)