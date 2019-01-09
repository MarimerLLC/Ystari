# Serialization

TODO: Finish

Here was the content from the original goals page:

* Allow dev to decide between full and diffgram serialization on update/insert/delete
 * Requires maintaining a unique (to the graph) id field for each object within a graph; probably an Int32 (**RDL:** this unique id exists in CSLA starting in 4.7 to support merging the result of save back into the original object graph)
 * Should have the option where the dev can provide their own visitor to each object in the graph, or a way of overriding the behavior of each object in the graph as it is visited, so it is possible for the dev to control which fields are included in the diffgram
* Don’t serialize fields if they contain their default value
* Remove Save/SaveAsync from BusinessBase type base classes and just allow/use extension methods to implement such methods on objects
