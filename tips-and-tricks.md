# JS tips and tricks

## Named functions
Named JS functions as callback or predicates make code more readable and stack trace more clear:
```js
// I know, this case is too trivial but it reveals the main idea 
ajax((data) => template.bind(data));
// vs
ajax(function bindToTemplate(data) {
  template.bind();
});
```

## Function parameters aggregation
Destructive/spread operators make arguments passing more declarative and do not bring any complexity
```js
function foo({ x, y } = {}) {
  ...
};
function foo([ x, y ] = []) {
  ...
};
function foo(x, y, ...args) {
  ...
};
```

## Named arguments/parameters
In case a function has a lot of arguments or, for example, most of them are optional, making a function unary and passing a single object is an option for such cases.
 ```js
 function foo({ x, y, z }) {
  ...
 };
 
 foo({ y: 'y' });
 ```
 
## Constants
`const` identifier does not make a variable *constant*, it just tells that it can not be reassiggned, but a variable mutation is allowed.
```js
const a = { b: 1 };
a.b = 2;
console.log(a.b); // 2
```
And `Object.freeze(..)` can help you in this approach:
```js
const a = Object.freeze({ b: 1 });
a.b = 2; // Error
```
`Object.freeze(..)` makes an object immutable at its first root level, nested objects should be *freezed* as well in case you need this. This behavior can be achieved with some custom recursion function or with a third party library.

## Purity and mutations
The best choise for developer that wants to create a readable and clean code is immutability. Utilities like `[...arr]` and `Object.assign({}, obj)` may help you do achieve this goal but this approach leads to extra CPU and memory consumption. So that the best choise it using more sophisticated structures like ones from [Immutable.js](https://github.com/facebook/immutable-js/issues). It introduces structures like `Map` and `List` that can help you in real immutability.
