# Dependency Injection

TODO: Finish

Here was the content from the original goals page:

* Enable DI where possible
 * Method-level DI in server-side “data portal”
  * Though I wonder if we couldn’t just always provide the DP_XYZ methods with a ‘context’ parameter, and have that object created via DI using ctor or property based injection?
 * Creation of business rule instances
  * Also need some params when Execute is invoked – perhaps use standard DI to create a ‘context’ parameter?
 * Creation of domain graph? Not clear how this could happen.
 * Creation of the “data portal” client-side object.