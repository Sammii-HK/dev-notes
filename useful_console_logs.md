# A List of Very Useful Console Logs

A simple function which maps over an array and counts the instances, which are saved to the object `counts`

```javascript
let counts = {}

arrayToMap.map(item => {
	let value = item.valueToCount;
	counts[value] = counts[value] ? counts[value] + 1 : 1;
})

console.log("🐛🐝🐞 counts", counts);
```

An example of the expected output from the console log is as such:

```
counts {female: 51, male: 50}
```

