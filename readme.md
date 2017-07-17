# Nests

Nesting in D3 refers to creating hierarchical objects from arrays containing objects with matching property names. The nest method creates key-value pairs by grouping elements in an array by a given property, returning an array of hierarchical objects. By supplying multiple properties, it is possible to create tree structures with many levels of keys.

An initialized nest method is called a nest operator, because it can be reused. The nest operator does not retain any reference to the data it has nested. The only information stored in the nest operator are the keys and other methods used to define how objects should be nested.

Objects are commonly nested so that the new hierarchy can be iterated through, such as in drawing methods. You can also create nest operators to sort the data by keys or leaves, and to calculate summary data using the rollup method.

**Example**

Take the following data in a JavaScript array:

```javascript
var fruits = [
	{name: "apple", color: "red", type: "accessory"},
	{name: "banana", color: "yellow", type: "berry"},
	{name: "blackberry", color: "black", type: "aggregate"},
	{name: "blueberry", color: "blue", type: "berry"},
	{name: "cranberry", color: "red", type: "berry"},
	{name: "grape", color: "green", type: "berry"},
	{name: "grapefruit", color: "orange", type: "hesperidium"},
	{name: "lemon", color: "yellow", type: "hesperidium"},
	{name: "lime", color: "green", type: "hesperidium"},
	{name: "orange", color: "orange", type: "multiple"},
	{name: "pear", color: "green", type: "simple"},
	{name: "pineapple", color: "yellow", type: "multiple"},
	{name: "raspberry", color: "red", type: "aggregate"},
	{name: "strawberry", color: "red", type: "accessory"}
];
```

A nest operator can be created and set up with a key in a chained method:

```javascript
var nested = d3.nest()
	.key(function(d) { return d.type; });
```

Then the nest operator can be used to return a hierarchical object from the fruits array:

```javascript
var entries = nested.entries(fruits);
```

Which contains the nested data:

```javascript
[
	{key: "accessory", values: [{name: "apple", color: "red", type: "accessory"}, {name: "strawberry", color: "red", type: "accessory"}]},
	{key: "berry", values: [{name: "banana", color: "yellow", type: "berry"}, {name: "blueberry", color: "blue", type: "berry"}, {name: "cranberry", color: "red", type: "berry"}, {name: "grape", color: "green", type: "berry"}]},
	{key: "aggregate", values: [{name: "blackberry", color: "black", type: "aggregate"}, {name: "raspberry", color: "red", type: "aggregate"}]},
	{key: "hesperidium", values: [{name: "grapefruit", color: "orange", type: "hesperidium"}, {name: "lemon", color: "yellow", type: "hesperidium"}, {name: "lime", color: "green", type: "hesperidium"}]},
	{key: "multiple", values: [{name: "orange", color: "orange", type: "multiple"}, {name: "pineapple", color: "yellow", type: "multiple"}]},
	{key: "simple", values: [{name: "pear", color: "green", type: "simple"}]}
]
```

## d3.nest()

**Description**

Creates a new nest operator with the following methods:

- entries
- key
- map
- object
- rollup
- sortKeys
- sortValues

The set of keys is initially empty, and you must assign them with the key method to nest data.

**Example**

```javascript
var nest = d3.nest();
```

Initializes a new nest operator and assigns it to the variable *nest*.


## nest.key(key)

**Parameters**

*key*: a callback function that returns a string used to identify groups.

**Description**

Registers a new key function in the nest operator. When the nest operator is used to create nested data with the entries method, the key function is invoked for each element in the input array. The key function uses a string identifier to organize the elements into groups. Most often, the function is an object property accessor returned by the key callback function.

It is possible to register multiple keys in a nest operator. Each time a key is registered, it is pushed onto the end of the internal array of keys. The order of keys is in reverse of how they are registered; the last key registered is at the top of the hierarchy.

*Note*: if the string identifier does not match any property in an element of the array, that object will be appended to an "undefined" key group.

**Example**

```javascript
nest.key(function(d) { return d.type; })
	.key(function(d) { return d.color; });
```

Here two keys are registered with a nest operator. The *color* key will be first in the hierarchy, then *type*.

