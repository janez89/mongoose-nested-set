# mongoose-nested-set

A mongoose plugin implementing the nested set pattern for mongoose models

### Usage

```javascript
var mongoose = require('mongoose'),
    NestedSetPlugin = require('mongoose-nested-set'),
    Schema = mongoose.Schema;
    
mongoose.connect('mongodb://localhost/nested_set_test');

var UserSchema = new Schema({
  username: {type: String}
});

// Include plugin
UserSchema.plugin(NestedSetPlugin, {root:'517b04a2b85f33ed79000003'});

var User = mongoose.model('User', UserSchema);
```

### Attributes

The plugin adds the following attributes to the model:

* lft: holds the left value of the node in the tree

* rgt: holds the right value of the node in the tree

* parentId: holds the _id of the parent node

* childs: children elements (only whith tree and getChildren method)

* lvl: nested set level (only whith tree and getChildren method)

### Examples

Examples are based on the following tree:

![The Office](https://github.com/groupdock/mongoose-nested-set/raw/master/docs/test_tree.png "The Office")

```javascript
User.findOne({username: 'michael'}, function(err, michael) {
  User.rebuildTree(michael, 1, function() {
    // at this point, the tree is built and every node has a lft and rgt value.
    michael.descendants(function(err, data) {
      // data contains a list of michael descendants
      console.log(data);
    });
    console.log('Is Michael a leaf node?', michael.isLeaf());
    console.log('Is Michael a child node?', michael.isChild());
  });
});
```

```javascript
// from root 
User.tree(function(err, tree){
  console.log(tree); // { name: "some name", lft: 1, rgt: 3, lvl: 0, childs: [...]} 
});

// from another element
User.tree('517b04a2b85f33ed79000003', function(err, tree){
  console.log(tree); // { name: "some name", lft: 1, rgt: 3, lvl: 0, childs: [...]} 
});

// from another element without parent
User.tree('517b04a2b85f33ed79000003', function(err, tree){
  console.log(tree); // { name: "some name", lft: 1, rgt: 3, lvl: 0, childs: [...]} 
}, true); // <-- this set true

User.findOne({username: 'michael'}, function(err, michael) {
  michael.getChildren(function(err, childrenTree){
    console.log(childrenTree); // [ { name: "some name", lft: 1, rgt: 3, lvl: 0, childs: [...]} , ... ]
  });
});
```

For more examples, see our [test suite] (https://github.com/groupdock/mongoose-nested-set/blob/master/tests/nested_set_test.js).

### API

### Static methods

* Model.rebuildTree(rootNode, leftValueOfRootNode, callback)
* Model.tree(rootNodeId, callback [,withoutParent: false])
* Model.tree(callback [,withoutParent: false])

#### Instance methods that return values:

The following methods return a boolean:

* isLeaf()

* isChild()

* isDescendantOf(otherNode)

* isAncestorOf(otherNode)


#### Instance methods that use a callback function:

The following methods must be used with a callback. The callback method will be called with two arguments: the first argument is the error object (if there was no error, it will be null) and the second argument is the data returned.

* selfAndAncestors(callback)

* ancestors(callback)

* selfAndChildren(callback)

* children(callback)

* getChildren(callback)

* selfAndDescendants(callback)

* descendants(callback)

* level(callback)

* selfAndSiblings(callback)

* siblings(callback)


### Related Links/Resources

* Inspired by [Rails nested_set library] (https://github.com/skyeagle/nested_set)
* [Mongoose Documentation] (http://mongoosejs.com/)
* [Mongoose Plugins] (http://mongoosejs.com/docs/plugins.html)
* [Tree used in test and examples] (https://github.com/groupdock/mongoose-nested-set/raw/master/docs/test_tree.png)
* [Nested Set Model] (http://en.wikipedia.org/wiki/Nested_set_model)
* [Storing Hierarchical Data in a Database Article] (http://www.sitepoint.com/hierarchical-data-database/)
* [Trees in MongoDB] (http://www.mongodb.org/display/DOCS/Trees+in+MongoDB)

### Development

To run the tests:

```
npm test
```


### Changelog

#### May 05, 2013: Version 0.0.5
* added method getChildren
* added static method tree
* added lvl, childs attributes only with tree and getChildren methods. (name childs because name of children is reserved)

#### Oct 26, 2012: Version 0.0.4

* Bug fixes

#### Oct 25, 2012: Version 0.0.3

* Bug fixes

#### Oct 25, 2012: Version 0.0.2

* Better tests
* Code cleanup
* Updated package.json to latest dependencies
* Added mongodb indexes on lft, rgt, and parentId

### Authors

* Luc Castera: [https://github.com/dambalah](https://github.com/dambalah)

### Sponsor

[Intellum] (http://www.intellum.com/)
