# Nests

Nesting in D3 refers to creating hierarchical objects from arrays containing objects with matching property names. The nest method creates key-value pairs by grouping elements in an array by a given property, returning an array of hierarchical objects. By supplying multiple properties, it is possible to create tree structures with many levels of keys.

An initialized nest method is called a *nest operator* because it performs operations on data, similar to arithmetic operators. A nest operator does not retain any reference to data it has nested and it can be reused with different data inputs. The only information stored in the nest operator are the keys and other methods used to define how objects should be nested.

Nests are an extremely useful feature in D3 for analyzing and manipulating data. Objects are commonly nested so that the new hierarchy can be iterated through, such as in drawing methods. You can also create nest operators to sort the data by keys or leaves, and to calculate summary data using the rollup method.

**Example**

Consider an array with ten elements, each containing an object that has four properties:

![array with ten elements](nesting_color_array.jpg)

Here the properties are shown by different border styles, and their contents as colors. If we were to nest this array by registering the small dotted border as a key, the resulting nested object would look like the following:

![nested object](nesting_color_entries.jpg)

Now the elements are nested by the value of the small dot key. Notice that the elements are still intact (including the small dot property) they have only been reorganized into the new hierarchy.

Here's the same process again, but with Javascript. Start with an array containing ten elements, each containing an object that has four properties:

```javascript
var colors = [
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

> Note: if the string identifier does not match any property in an element of the array, that object will be appended to an "undefined" key group.

**Example**

```javascript
nest.key(function(d) { return d.type; })
	.key(function(d) { return d.color; });
```

Here two keys are registered with a nest operator. The *color* key will be first in the hierarchy, then *type*.


## nest.sortKeys(comparator)

**Parameters**

*comparator*: a callback function that defines the sort order, matching the [compareFunction](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Description) return values. D3 provides two sort order functions, *d3.ascending* and *d3.descending*.

**Description**

Sorts key values for the current registered key



**Example**