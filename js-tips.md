# JS tips

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
function({ x, y } = {}) {
  …
};
function([ x, y ] = []) {
  …
};
function(x, y, …args) {
  …
} 
```
