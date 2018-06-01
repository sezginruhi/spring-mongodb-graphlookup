# Description
This is an initial setup of a mini-app for testing tree/hierarchical data traversal with MongoDB.

# References
- https://docs.mongodb.com/manual/applications/data-models-tree-structures/
- https://www.slideshare.net/mongodb/webinar-working-with-graph-data-in-mongodb
- https://docs.mongodb.com/manual/reference/operator/aggregation/graphLookup/

# Create View
To be executed once-off:
```
db.createView("treeView", "node", [
{
 $graphLookup: {
    from: "node",
    startWith: "$nodeId",
    connectFromField: "nodeId",
    connectToField: "parentId",
    maxDepth: 0,
    as: "children"
 }
}
]);
```

# Schema indexes
In MongoDB, add indexes on fields:
- nodeId
- changesetId
- parentId

# $graphLookup
Get all dimensions and their descendants by changesetId:
```
db.node.aggregate([ 
{ $match: {$and: [ {"parentId": {$eq: []}}, {"changesetId": 2} ]} },
{
 $graphLookup: {
    from: "treeView",
    startWith: "$nodeId",
    connectFromField: "nodeId",
    connectToField: "parentId",
    restrictSearchWithMatch: {"changesetId": 2},
    maxDepth: 0,
    as: "children"
 }
}
]);
```

# Usage
- Check [src/test/resources/application.properties](https://github.com/kmandalas/spring-mongodb-graphlookup/blob/master/src/test/resources/application.properties) 
- Boolean property `populate` leads to always inserting data in db for the number of changesets specified by `changesets`.
- Check and run `com.github.kmandalas.mongodb.GraphLookupTests.shouldRenderCorrectly`
