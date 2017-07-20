# Nests

Nesting in D3 refers to creating hierarchical objects from arrays containing objects with matching property names. The nest method creates key-value pairs by grouping elements in an array by a given property, returning an array of hierarchical objects. By supplying multiple properties, it is possible to create tree structures with many levels of keys.

An initialized nest method is called a *nest operator* because it performs operations on data, similar to arithmetic operators. A nest operator does not retain any reference to data it has nested and it can be reused with different data inputs. The only information stored in the nest operator are the keys and other methods used to define how objects should be nested.

Nests are an extremely useful feature in D3 for analyzing and manipulating data. Objects are commonly nested so that the new hierarchy can be iterated through, such as in drawing methods. You can also create nest operators to sort the data by keys or leaves, and to calculate summary data using the rollup method.

**Example**

Consider an array with ten elements, each containing an object that has four properties:

![array with ten elements](nesting_color_array.jpg)

Here the properties are shown by different border styles, and their contents as colors. If we were to nest this array by registering the small dotted border as a key, the resulting nested object would look like the following:

![nested object](nesting_color_entries.jpg)

Now the elements are nested by the value of the small dot key. There are three keys, from the three unique values of the dot property. Each key has an array of elements that contain a dot property with a value mactching the key. Notice that the ten elements from the original array have not changed, only they been reorganized into the new key-value hierarchy.

Here's the same process again, but with Javascript. Start with an array containing ten elements, each containing an object that has four properties:

```javascript
var colors = [
	{dot: "red", block: "green", dash: "blue", wave: "yellow"},
	{dot: "orange", block: "pink", dash: "green", wave: "red"},
	{dot: "orange", block: "green", dash: "blue", wave: "yellow"},
	{dot: "blue", block: "orange", dash: "green", wave: "green"},
	{dot: "orange", block: "pink", dash: "pink", wave: "pink"},
	{dot: "blue", block: "orange", dash: "pink", wave: "green"},
	{dot: "blue", block: "yellow", dash: "blue", wave: "orange"},
	{dot: "red", block: "green", dash: "green", wave: "red"},
	{dot: "red", block: "green", dash: "green", wave: "orange"},
	{dot: "red", block: "pink", dash: "green", wave: "yellow"}
];
```

Then, in nested form using the dot property as a key:

```javascript
[
	{key: "red", values: [{dot: "red", block: "green", dash: "blue", wave: "yellow"}, {dot: "red", block: "green", dash: "green", wave: "red"}, {dot: "red", block: "green", dash: "green", wave: "orange"}, {dot: "red", block: "pink", dash: "green", wave: "yellow"}]},
	{key: "orange", values: [{dot: "orange", block: "pink", dash: "green", wave: "red"}, {dot: "orange", block: "green", dash: "blue", wave: "yellow"}, {dot: "orange", block: "pink", dash: "pink", wave: "pink"}]},
	{key: "blue", values: [{dot: "blue", block: "orange", dash: "green", wave: "green"}, {dot: "blue", block: "orange", dash: "pink", wave: "green"}, {dot: "blue", block: "yellow", dash: "blue", wave: "orange"}]}
]
```

The following sections describe how to use d3.nest() to set up and transform arrays into nested objects.


## d3.nest()

**Description**

Creates a new nest operator. Nest operators must first be set up with constructor methods that take functions which describe how to nest data. Then they can be used with invoking methods that take and return data. For example, to return a nested object with the *entries* method, you must first register keys with the *key* method.

Constructor methods:

- key
- rollup
- sortKeys
- sortValues

Invoking methods:

- entries
- map
- object

There are two patterns for using nest operators. First is set up d3.nest with a chain of constructor methods to create a reusable nest operator. The second is to chain both the constructor and invoking methods together to get an immediate result.

**Example**

Create a nest operator and register a key to nest data with, then return the data with entries:

```javascript
var nest = d3.nest()
	.key(function(d) { return d.dot; });

var entries = nest.entries(colors);
```

Notice that nest.entries could be reused with other data objects, so long as they have the property *dot*.

Create a nest operator, register a key to nest data with, and return data in one chain:

```javascript
var entries = d3.nest()
	.key(function(d) { return d.dot; })
	nest.entries(colors);
```


## nest.key(key)

**Parameters**

*key*: a callback function that returns a string used to identify groups.

**Description**

Registers a new key function in the nest operator. When the nest operator is used to create nested data with the entries method, the key function is invoked for each element in the input array. The key function uses a string identifier to organize the elements into groups. Most often, the function is an object property accessor returned by the key callback function.

It is possible to register multiple keys in a nest operator. Each time a key is registered, it is pushed onto the end of the internal array of keys. The order of keys is in reverse of how they are registered; the last key registered is at the top of the hierarchy.

> Note: if the string identifier does not match any property in an element of the array, that object will be appended to an *undefined* key group.

**Example**

Registering one key with a nest operator:

```javascript
nest.key(function(d) { return d.dot; });
```

Here the key method is passed a callback function that returns the value obtained from the property accessor *dot*.

Registering two keys with a nest operator:

```javascript
nest.key(function(d) { return d.dot; })
	.key(function(d) { return d.block; });
```

Here two key methods are chained together to register the *dot* and *block* property accessors as keys. The *block* key will be first in the hierarchy, then *dot*.


## nest.sortKeys(comparator)

**Parameters**

*comparator*: a callback function that defines the sort order, matching the [compareFunction](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Description) return values. 

**Description**

Sorts key values of the last registered key using a given comparator function when they are returned from the *entries* nest method.

> Note: nest.sortKeys only affects the result of nest.entries; the order of keys returned by nest.map and nest.object is always arbitrary, regardless of comparator.

D3 provides two sort order functions that can be supplied to nest.sortKeys, *d3.ascending* and *d3.descending*. If no comparator function is supplied, the keys will be arbitrarily sorted.

**Example**

Sorting keys using the included D3 sort order functions:

```javascript
nest.key(function(d) { return d.dot; }).sortKeys(d3.ascending)
	.key(function(d) { return d.block; }).sortKeys(d3.descending);
```

## nest.sortValues(comparator)

**Parameters**

*comparator*: a callback function that defines the sort order, matching the [compareFunction](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Description) return values. 

**Description**

Sorts leaf elements using a given comparator function. Applies to nest.entries, nest.map, and nest.object.

A comparator function must be supplied to specify which element properties to sort by, even when using the included *d3.ascending* and *d3.descending* sort order functions. If no comparator function is supplied, the keys will be arbitrarily sorted.

**Example**

Sort elements by the wave property in ascending order:

```javascript
nest.key(function(d) { return d.dot; })
	.sortValues(function(a, b) { return d3.ascending(a.wave, b.wave); });
```


## nest.rollup(function)

**Parameters**

*function*: a callback function that is applied on each array of leaf elements in a nested object.

**Description**

Specifies a rollup function to be applied on each group of leaf elements. The return value of the rollup function will replace the array of leaf values in the either associative array returned by nest.map or nest.object; for nest.entries, it replaces the leaf entry.values with entry.value. If the leaves are sorted with nest.sortValues, the leaf elements are sorted prior to invoking the rollup function.