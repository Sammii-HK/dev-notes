#  A List of Very Useful Functions



## Objects

##### Filtering Null Properties

A simple function which can be used to iterate over an existing object and create a new object which exists of `key : value` properties which have `non-null` values.

```js
filterNullAttrs() {
	return Object.keys(obj)
	.filter((*k*) => obj[k] != null)
	.reduce((*a*, *k*) => ({ ...a, [k]: obj[k] }), {});
},
```