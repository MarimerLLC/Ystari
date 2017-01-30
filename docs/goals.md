#Ystari Design Goals

Before we start to list out all of the goals, let's spend a bit of time going through the history of CSLA as it will frame the conversation for the motivation to create Ystari in the first place

##Background

CSLA is a business object framework that was created (originally as source code to accompany Rocky's VB book) in the late 1990s. As .NET came to fruition, the framework was ported to that runtime around 2003. Since that time, CSLA has had numerous features added, bugs fixed, and ended up targeting a number of runtimes and hosts than just WinForms applications written using the full .NET framework. It's also had over 55,000 downloads on NuGet (https://www.nuget.org/packages/CSLA-Core/). 

However, while it is still a viable framework to use in .NET business applications, there are also some limitations to the framework:

* It has no notion of handling dependencies. There are ways to make DI work using `IDataPortalActivator`, but they're somewhat cumbersome, and don't embrace the notion that business objects are mobile. Meaning, if you inject dependencies into a business object and that object moves to a different host, what happens to those dependencies?
* It uses a number of statics which can make unit testing cumbersome. While there is nothing fundamentally wrong with static members (e.g. extension methods are wonderful), if statics are used as a global "context" container (like `ApplicationContext`), it makes isolation in unit tests impossible.
* Given CSLA's long history, it's inevitably picked up some "cruft" over the years. Hosts and frameworks come and go with support (like Remoting, Windows Workflow and Silverlight), but over the years that has required some interesting gyrations in the code base. Sometimes a reboot can provide a fresh start.
* While the fundamental notions of business objects and domain modeling are still solid, the technical landscape has arguably changed a lot between 2003 and 2017. Modern application development techniques should be considered when creating a framework to help developers build their applications.

That's where Ystari comes in. We want to use concepts and implementations that work in CSLA, but approach it with a fresh start. If there are ways to make Ystari better than CSLA, they should be used!

##Design Goals

Note: each of these sections will eventually have links to other documents that will describe the goal in detail

* [Build Targets](build_targets.md)
* [DI/IoC](dependency_injection.md)

##Enhancements to Ystari from CSLA .NET

* Enable DI where possible
 * Method-level DI in server-side “data portal”
  * Though I wonder if we couldn’t just always provide the DP_XYZ methods with a ‘context’ parameter, and have that object created via DI using ctor or property based injection?
 * Creation of business rule instances
  * Also need some params when Execute is invoked – perhaps use standard DI to create a ‘context’ parameter?
 * Creation of domain graph? Not clear how this could happen.
 * Creation of the “data portal” client-side object.
* Enable true async “data portal” behavior
 * Basically send/post semantics
* Eliminate magic strings, in particular in the “data portal” behaviors
 * Either via attribute or interface (preferably interface)
* Allow dev to decide between full and diffgram serialization on update/insert/delete
 * Requires maintaining a unique (to the graph) id field for each object within a graph; probably an Int32
 * Should have the option where the dev can provide their own visitor to each object in the graph, or a way of overriding the behavior of each object in the graph as it is visited, so it is possible for the dev to control which fields are included in the diffgram
* Don’t serialize fields if they contain their default value
* Remove Save/SaveAsync from BusinessBase type base classes and just allow/use extension methods to implement such methods on objects

