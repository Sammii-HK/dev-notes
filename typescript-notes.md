# Typescript Notes

The goal of TypeScript is to be a static typechecker for JavaScript programs - in other words, a tool that runs before your code runs (static) and ensures that the types of the program are correct (typechecked).

## Basics

There are two syntaxes for building types: Interfaces and Types. You should prefer interface. Use type when you need specific features.

### Composing Types

With TypeScript, you can create complex types by combining simple ones. There are two popular ways to do so: with unions, and with generics.

#### Unions

With a union, you can declare that a type could be one of many types. For example, you can describe a boolean type as being either true or false:

```typescript
type MyBool = true | false;
```
A popular use-case for union types is to describe the set of string or number literals that a value is allowed to be:

```typescript
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type PositiveOddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```

Unions provide a way to handle different types too. For example, you may have a function that takes an array or a string:

```typescript
function getLength(obj: string | string[]) {
  return obj.length;
}
```

#### Generics

Generics provide variables to types. A common example is an array. An array without generics could contain anything. An array with generics can describe the values that the array contains.

```typescript
type StringArray = Array<string>;
type NumberArray = Array<number>;
type ObjectWithNameArray = Array<{ name: string }>;
```

You can declare your own types that use generics:

```typescript
interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}
 
// This line is a shortcut to tell TypeScript there is a
// constant called `backpack`, and to not worry about where it came from.
declare const backpack: Backpack<string>;
 
// object is a string, because we declared it above as the variable part of Backpack.
const object = backpack.get();
 
// Since the backpack variable is a string, you can't pass a number to the add function.
backpack.add(23);
Argument of type 'number' is not assignable to parameter of type 'string'.
```

### Structural Type System

One of TypeScript’s core principles is that type checking focuses on the shape that values have. This is sometimes called “duck typing” or “structural typing”.

In a structural type system, if two objects have the same shape, they are considered to be of the same type.

```typescript
interface Point {
  x: number;
  y: number;
}
 
function logPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}
 
// logs "12, 26"
const point = { x: 12, y: 26 };
logPoint(point);
```

The point variable is never declared to be a Point type. However, TypeScript compares the shape of point to the shape of Point in the type-check. They have the same shape, so the code passes.

The shape-matching only requires a subset of the object’s fields to match.

```typescript
const point3 = { x: 12, y: 26, z: 89 };
logPoint(point3); // logs "12, 26"
 
const rect = { x: 33, y: 3, width: 30, height: 80 };
logPoint(rect); // logs "33, 3"
 
const color = { hex: "#187ABF" };
logPoint(color);
Argument of type '{ hex: string; }' is not assignable to parameter of type 'Point'.
  Type '{ hex: string; }' is missing the following properties from type 'Point': x, y
Argument of type '{ hex: string; }' is not assignable to parameter of type 'Point'.
  Type '{ hex: string; }' is missing the following properties from type 'Point': x, y
```

There is no difference between how classes and objects conform to shapes:

```typescript
class VirtualPoint {
  x: number;
  y: number;
 
  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}
 
const newVPoint = new VirtualPoint(13, 56);
logPoint(newVPoint); // logs "13, 56"
```

If the object or class has all the required properties, TypeScript will say they match, regardless of the implementation details.

### Type Checking

We can observe by reading the code that this function will only work if given an object with a callable flip property, but JavaScript doesn't surface this information in a way that we can check while the code is running.

Seen in this way, a type is the concept of describing which values can be passed to fn and which will crash. JavaScript only truly provides dynamic typing - running the code to see what happens.

#### Static Type Checking

Ideally, we could have a tool that helps us find these bugs before our code runs. That's what a static type-checker like TypeScript does. Static types systems describe the shapes and behaviors of what our values will be when we run our programs. A type-checker like TypeScript uses that information and tells us when things might be going off the rails.

```typescript
const message = "hello!";
 
message();
// This expression is not callable.
  // Type 'String' has no call signatures.
```

Running that last sample with TypeScript will give us an error message before we run the code in the first place.

Non-exception Failures
So far we've been discussing certain things like runtime errors - cases where the JavaScript runtime tells us that it thinks something is nonsensical. Those cases come up because the ECMAScript specification has explicit instructions on how the language should behave when it runs into something unexpected.

For example, the specification says that trying to call something that isn't callable should throw an error. Maybe that sounds like "obvious behavior", but you could imagine that accessing a property that doesn't exist on an object should throw an error too. Instead, JavaScript gives us different behavior and returns the value undefined:

```typescript
const user = {
  name: "Daniel",
  age: 26,
};

// “This expression is not callable.
// Type 'String' has no call signatures.
```

Running that last sample with TypeScript will give us an error message before we run the code in the first place.

### Non-exception Failures

For example, the specification says that trying to call something that isn't callable should throw an error. Instead, JavaScript gives us different behavior and returns the value undefined:

```typescript
const user = {
  name: "Daniel",
  age: 26,
};
user.location; // returns undefined
```

Ultimately, a static type system has to make the call over what code should be flagged as an error in its system, even if it's "valid" JavaScript that won't immediately throw an error. In TypeScript, the following code produces an error about location not being defined:

```typescript
const user = {
  name: "Daniel",
  age: 26,
};
 
user.location;
// Property 'location' does not exist on type '{ name: string; age: number; }'.
```

While sometimes that implies a trade-off in what you can express, the intent is to catch legitimate bugs in our programs. And TypeScript catches a lot of legitimate bugs.

eg:

* typos
* uncalled functions
* basic logic errors

```typescript
function flipCoin() {
  // Meant to be Math.random()
  return Math.random < 0.5;
// Operator '<' cannot be applied to types '() => number' and 'number'.
}
```

or basic logic errors.

```typescript
const value = Math.random() < 0.5 ? "a" : "b";
if (value !== "a") {
  // ...
} else if (value === "b") {
// This condition will always return 'false' since the types '"a"' and '"b"' have no overlap.
  // Oops, unreachable
}
```

### Types for Tooling

The type-checker has information to check things like whether we're accessing the right properties on variables and other properties. Once it has that information, it can also start suggesting which properties you might want to use.

That means TypeScript can be leveraged for editing code too, and the core type-checker can provide error messages and code completion as you type in the editor. That's part of what people often refer to when they talk about tooling in TypeScript.

TypeScript takes tooling seriously, and that goes beyond completions and errors as you type. An editor that supports TypeScript can deliver "quick fixes" to automatically fix errors, refactorings to easily re-organize code, and useful navigation features for jumping to definitions of a variable, or finding all references to a given variable.

And now let's type-check it by running the command tsc which was installed for us by the typescript package.

```cli
tsc hello.ts
```

We got some file output instead. If we look in our current directory, we'll see a hello.js file next to hello.ts. That's the output from our hello.ts file after tsc compiles or transforms it into a plain JavaScript file. And if we check the contents, we'll see what TypeScript spits out after it processes a .ts file:

```typescript
// Greets the world.
console.log("Hello world!");
```

In this case, there was very little for TypeScript to transform, so it looks identical to what we wrote. The compiler tries to emit clean readable code that looks like something a person would write. While that's not always so easy, TypeScript indents consistently, is mindful of when our code spans across different lines of code, and tries to keep comments around.

There are scenarios where those checks get in the way. For example, imagine yourself migrating JavaScript code over to TypeScript and introducing type-checking errors.

So TypeScript doesn't get in your way. Of course, over time, you may want to be a bit more defensive against mistakes, and make TypeScript act a bit more strictly. In that case, you can use the noEmitOnError compiler option. Try changing your hello.ts file and running tsc with that flag:

```typescript
tsc --noEmitOnError hello.ts
```

You'll notice that hello.js never gets updated.

### Explicit Types

“
Let's edit the code to tell TypeScript that person is a string, and that date should be a Date object. We'll also use the toDateString() method on date.

```typescript
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}
```

*What we did was add type annotations on person and date to describe what types of values greet can be called with.* You can read that signature as "greet takes a person of type string, and a date of type Date".

In many cases, TypeScript can even just infer (or "figure out") the types for us even if we omit them.

```Typescript
let msg = "hello there!";
    
let msg: string
```

Even though we didn't tell TypeScript that msg had the type string it was able to figure that out. **That's a feature, and it's best not to add annotations when the type system would end up inferring the same type anyway.**

## Everyday Types

### The primitives: string, number, and boolean

  **‼️ The type names String, Number, and Boolean (starting with capital letters) are legal, but refer to some special built-in types that will very rarely appear in your code. Always use string, number, or boolean for types.**

### Arrays

To specify the type of an array like [1, 2, 3], you can use the syntax `number[];` this syntax works for any type (e.g. `string[]` is an array of strings, and so on). You may also see this written as `Array<number>`, which means the same thing. We'll learn more about the syntax `T<U>` when we cover generics.

Note that `[number]` is a different thing; refer to the section on *Tuples*.

### `any`

TypeScript also has a special type, any, that you can use whenever you don't want a particular value to cause typechecking errors.

### `noImplicitAny`

When you don't specify a type, and TypeScript can't infer it from context, the compiler will typically default to any.

You usually want to avoid this, though, because any isn't type-checked. Use the compiler flag noImplicitAny to flag any implicit any as an error.

### Type Annotations on Variables

When you declare a variable using `const`, `var`, or `let`, you can optionally add a type annotation to explicitly specify the type of the variable:

```typescript
let myName: string = "Alice";
```

In most cases, though, this isn't needed. Wherever possible, TypeScript tries to automatically infer the types in your code. For example, the type of a variable is inferred based on the type of its initializer:

```cli
// No type annotation needed -- 'myName' inferred as type 'string'
let myName = "Alice";
```




```typescript
```
