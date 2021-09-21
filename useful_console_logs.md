A simple function which maps over an array and counts the instances, which are saved to the object `counts`

```javascript
let counts = {}

*this*.project.fullResults.map(*result* => {
	let value = result.valueToCount;
	counts[value] = counts[value] ? counts[value] + 1 : 1;
})

console.log("🐛🐝🐞 counts", counts);
```

An example of the expected output from the console log is as such:

```
counts {female: 51, male: 50}
```

