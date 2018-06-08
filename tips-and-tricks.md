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

## Recursion
Everybody knows about recursion and its side effects that touch call stack size and memory consumption. But there are a couple of techniques on the field that can elimitate them and make your code more readable and declarative:
- [Proper Tail Calls (PTC)](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch8.md/#proper-tail-calls-ptc)
These are not PTC:
```js
foo();
return;
// or
var x = foo( .. );
return x;
// or
return 1 + foo( .. );
```
However, this is PTC:
```js
return x ? foo( .. ) : bar( .. );
```
- [Continuation Passing Style (CPS)](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch8.md/#continuation-passing-style-cps)
```js
"use strict";

function fib(n,cont = identity) {
    if (n <= 1) return cont( n );
    return fib(
        n - 2,
        n2 => fib(
            n - 1,
            n1 => cont( n2 + n1 )
        )
    );
}
```
- [Trampolines](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch8.md/#trampolines)
```js
function trampoline(fn) {
    return function trampolined(...args) {
        var result = fn( ...args );

        while (typeof result == "function") {
            result = result();
        }

        return result;
    };
}

// and then
var sum = trampoline(
    function sum(num1,num2,...nums) {
        num1 = num1 + num2;
        if (nums.length == 0) return num1;
        return () => sum( num1, ...nums );
    }
);

var xs = [];
for (let i=0; i<20000; i++) {
    xs.push( i );
}
```

## [True functional `filter()`](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch9.md/#filtering-confusion)
The most interesting question about `filter()` is how _predicate_ function should be named? For example:
```js
[1,2,3,4,5].filter( isOdd );
// and
[1,2,3,4,5].filter( isEven );
```
It is not clear what exactly we want to do with our input: _filter out_ or _filter in_, do we want to _GET_ or _SKIP_ some array elements. Functional programming patterns and most libraries promote next implementation:
```js
filterIn( isOdd, [1,2,3,4,5] );         // [1,3,5]
filterOut( isEven, [1,2,3,4,5] );       // [1,3,5]
```
> To clear up all this confusion, let's define a filterOut(..) that actually filters out values by internally negating the predicate check. While we're at it, we'll alias filterIn(..) to the existing filter(..):

## [Fusion](https://github.com/getify/Functional-Light-JS/blob/master/manuscript/ch9.md/#fusion)
Imagine we have some array `fiter/map` chain like this:
```js
someList
  .filter(..)
  .filter(..)
  .map(..)
  .map(..)
  .map(..)
  .reduce(..);
```
It looks pretty nice but its performance is not hte best - we do one extra `filter` `forEach` and twu extra `map` `forEach`. To avoid this percormance suffering we can combine these `filter` and `map` predicate calls into one function per operator. For example:
```js
var removeInvalidChars = str => str.replace( /[^\w]*/g, "" );

var upper = str => str.toUpperCase();

var elide = str =>
    str.length > 10 ?
        str.substr( 0, 7 ) + "..." :
        str;

var words = "Mr. Jones isn't responsible for this disaster!"
    .split( /\s/ );

words;
// ["Mr.","Jones","isn't","responsible","for","this","disaster!"]

words
.map( removeInvalidChars )
.map( upper )
.map( elide );
// ["MR","JONES","ISNT","RESPONS...","FOR","THIS","DISASTER"]
```

Every array item goes through next chain:
```js
elide( upper( removeInvalidChars( "Mr." ) ) );
// "MR"

elide( upper( removeInvalidChars( "responsible" ) ) );
// "RESPONS..."
```
You may already catched the point that we can `pipe` all this `map()` predicates with `pipe`, do not like `compose` personally, so let's do it with `pipe`:
```js
words
.map(
    pipe( removeInvalidChars, upper, elide )
);
// ["MR","JONES","ISNT","RESPONS...","FOR","THIS","DISASTER"]
```
We got the same result by more efficient, declarative and performance-wise way.
