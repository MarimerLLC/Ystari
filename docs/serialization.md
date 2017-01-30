# Serialization

TODO: Finish

Here was the content from the original goals page:

* Allow dev to decide between full and diffgram serialization on update/insert/delete
 * Requires maintaining a unique (to the graph) id field for each object within a graph; probably an Int32
 * Should have the option where the dev can provide their own visitor to each object in the graph, or a way of overriding the behavior of each object in the graph as it is visited, so it is possible for the dev to control which fields are included in the diffgram
* Don’t serialize fields if they contain their default value