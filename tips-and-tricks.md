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
function foo(x, y, …args) {
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
