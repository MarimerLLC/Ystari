#Ystari Design Goals

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

